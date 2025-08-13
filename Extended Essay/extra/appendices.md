# List of Appendices
---
## a
Python Source Code

```python
# main.py
import chess
import chess.engine
import chess.pgn
import sqlite3
import pathlib
import time
import os
import sys
import json
from tqdm import tqdm

# --- CONFIGURATION ---
STOCKFISH_PATH = "/Users/umarbektokyo/Developer/Projects/Extended-Essay/engines/stockfish-11-mac/Mac/stockfish-11-bmi2"
LC0_PATH = "/opt/homebrew/bin/lc0"
LC0_WEIGHTS_PATH = "/Users/umarbektokyo/Developer/Projects/Extended-Essay/engines/BT4-1740.pb"
DB_PATH = "games.db"
STATE_FILE = "match_state.json"

TOTAL_GAMES = 100
TIME_LIMIT_STOCKFISH = 2.5
TIME_LIMIT_LC0 = 2.5

STOCKFISH_THREADS = 4
STOCKFISH_HASH_MB = 256

LC0_BACKEND = "metal"
LC0_THREADS = 4
LC0_MINIBATCH_SIZE = 256

def setup_database(db_path: pathlib.Path):
    """Initializes the SQLite database."""
    print(f"Setting up database at: {db_path}")
    with sqlite3.connect(db_path) as con:
        cur = con.cursor()
        cur.execute("""
            CREATE TABLE IF NOT EXISTS games (
                id INTEGER PRIMARY KEY,
                white_engine TEXT NOT NULL,
                black_engine TEXT NOT NULL,
                result TEXT NOT NULL,
                termination_reason TEXT NOT NULL,
                game_length_moves INTEGER NOT NULL,
                pgn TEXT NOT NULL,
                timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
            )
        """)
        con.commit()
    print("Database setup complete.")

def save_state(game_number):
    """Saves the number of completed games to the state file."""
    with open(STATE_FILE, 'w') as f:
        json.dump({'games_completed': game_number}, f)

def load_state_and_scores(db_path):
    """
    Loads the last session's progress if it exists.
    Recalculates scores from the database to ensure accuracy.
    """
    scores = {"Stockfish": 0, "Lc0": 0, "Draw": 0}
    start_game = 0

    if os.path.exists(STATE_FILE):
        print("Previous session state found. Resuming...")
        with open(STATE_FILE, 'r') as f:
            state = json.load(f)
            start_game = state.get('games_completed', 0)

        with sqlite3.connect(db_path) as con:
            cur = con.cursor()
            cur.execute("SELECT white_engine, result FROM games")
            for row in cur.fetchall():
                white_player, result = row
                if result == "1-0":
                    scores[white_player] += 1
                elif result == "0-1":
                    black_player = "Lc0" if white_player == "Stockfish" else "Stockfish"
                    scores[black_player] += 1
                elif result == "1/2-1/2":
                    scores["Draw"] += 1
        print(f"Resuming from game {start_game + 1}. Current scores loaded.")

    return start_game, scores

def configure_engines():
    """Configures and launches the Stockfish and Lc0 engines."""
    try:
        stockfish_engine = chess.engine.SimpleEngine.popen_uci(STOCKFISH_PATH)
        stockfish_engine.configure({"Threads": STOCKFISH_THREADS, "Hash": STOCKFISH_HASH_MB, "UCI_AnalyseMode": False})

        lc0_engine = chess.engine.SimpleEngine.popen_uci(
            [LC0_PATH, f"--weights={LC0_WEIGHTS_PATH}", f"--backend={LC0_BACKEND}", f"--threads={LC0_THREADS}", f"--minibatch-size={LC0_MINIBATCH_SIZE}"],
            stderr=open(os.devnull, 'w')
        )
        return stockfish_engine, lc0_engine
    except (chess.engine.EngineTerminatedError, FileNotFoundError) as e:
        print(f"ERROR: An engine failed to start. Please check paths/permissions. Details: {e}")
        return None, None

def play_game(white_engine, black_engine, white_name, black_name):
    """Plays a single game of chess between two configured engines."""
    board = chess.Board()
    game = chess.pgn.Game()
    game.headers.update({"Event": "Engine Match", "White": white_name, "Black": black_name})
    node = game

    try:
        while not board.is_game_over(claim_draw=True):
            engine_to_move = white_engine if board.turn == chess.WHITE else black_engine
            engine_name = white_name if board.turn == chess.WHITE else black_name
            time_limit = TIME_LIMIT_LC0 if engine_name == "Lc0" else TIME_LIMIT_STOCKFISH
            result = engine_to_move.play(board, chess.engine.Limit(time=time_limit))
            board.push(result.move)
            node = node.add_variation(result.move)

        outcome = board.outcome(claim_draw=True)
        game.headers["Result"] = outcome.result()
        game.headers["Termination"] = outcome.termination.name.capitalize()
        return outcome.result(), outcome.termination.name.capitalize(), str(game), board.fullmove_number
    except chess.engine.EngineTerminatedError:
        print("\nERROR: An engine terminated unexpectedly during the game.")
        return "error", "Engine crashed", "", 0

def main():
    """The main function to run the chess engine tournament."""
    print("--- Chess Engine Match Runner ---")
    
    if not all(pathlib.Path(p).exists() for p in [STOCKFISH_PATH, LC0_PATH, LC0_WEIGHTS_PATH]):
        print("Error: Required files not found. Check paths in CONFIGURATION.")
        return

    db_path = pathlib.Path(DB_PATH)
    setup_database(db_path)
    
    start_game, scores = load_state_and_scores(db_path)

    progress_bar = tqdm(total=TOTAL_GAMES, initial=start_game, desc="Starting Match", bar_format="{l_bar}{bar}| {n_fmt}/{total_fmt} [{elapsed}<{remaining}]")

    stockfish_engine, lc0_engine = configure_engines()
    if not stockfish_engine or not lc0_engine:
        print("Halting match due to engine configuration error.")

    for i in range(start_game, TOTAL_GAMES):
        try:
            white_player, black_player = ("Stockfish", "Lc0") if i % 2 == 0 else ("Lc0", "Stockfish")
            white_engine, black_engine = (stockfish_engine, lc0_engine) if i % 2 == 0 else (lc0_engine, stockfish_engine)

            result, termination, pgn, length = play_game(white_engine, black_engine, white_player, black_player)

            if result == "1-0":
                scores[white_player] += 1
            elif result == "0-1":
                scores[black_player] += 1
            elif result == "1/2-1/2":
                scores["Draw"] += 1
            
            total_played = i + 1
            win_rate_sf = (scores['Stockfish'] / total_played) * 100 if total_played > 0 else 0
            win_rate_lc0 = (scores['Lc0'] / total_played) * 100 if total_played > 0 else 0
            progress_bar.set_description(
                f"SF: {scores['Stockfish']} ({win_rate_sf:.1f}%) | "
                f"Lc0: {scores['Lc0']} ({win_rate_lc0:.1f}%) | "
                f"Draws: {scores['Draw']} | Last game: {length} moves"
            )
            
            with sqlite3.connect(db_path) as con:
                cur = con.cursor()
                cur.execute(
                    "INSERT INTO games (white_engine, black_engine, result, termination_reason, game_length_moves, pgn) VALUES (?, ?, ?, ?, ?, ?)",
                    (white_player, black_player, result, termination, length, pgn)
                )
                con.commit()
            
            progress_bar.update(1)
            save_state(i + 1)

        except KeyboardInterrupt:
            print("\n--- Match Paused ---")
            print("Progress has been saved.")
            while True:
                action = input("Press 'c' to continue, or 'q' to quit: ").lower()
                if action == 'q':
                    print("Exiting match. Run the script again to resume.")
                    stockfish_engine.quit()
                    lc0_engine.quit()
                    sys.exit(0)
                elif action == 'c':
                    print("Resuming match...")
                    break
                else:
                    print("Invalid input.")
        
        time.sleep(0.7)

    print("\n--- Match Complete ---")
    print(f"Final Score after {TOTAL_GAMES} games:")
    print(f"Stockfish Wins: {scores['Stockfish']}")
    print(f"Lc0 Wins: {scores['Lc0']}")
    print(f"Draws: {scores['Draw']}")
    print(f"\nAll game data saved to '{DB_PATH}'")

    stockfish_engine.quit()
    lc0_engine.quit()
    
    if os.path.exists(STATE_FILE):
        os.remove(STATE_FILE)

if __name__ == "__main__":
    main()
```

---
## b
