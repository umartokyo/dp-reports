## Research Question
To what extent does a a chess engine trained with self-play and deep reinforcement learning compare to traditional heuristic-based search engine in performance and win rate?
## Introduction
Not long ago, Large Language Models (LLM's) such as ChatGPT emerged, demonstrating the capabilities of Artificial Intelligence (AI) across a wide range of language-based tasks (11). However, as major AI labs have been increasing the training data and size of these models, the performance gains appear to be plateauing (10). One of the core reasons is thought to be the reliance of these models on human-generated data: they learn to generalise from patterns of our behaviour, thus they are limited to human performance (12). Interestingly, this pattern of stagnation is not new; a similar bottleneck was once met in another domain: computer chess. (13)

Traditional chess engines based on the rule-based search algorithms such as heuristic evaluation functions with alpha-beta pruning proved successful in defeating the world champion of the time Garry Kasparov and kept improving ever since (14). Although the strength of these engines exceeded human performance by a large margin, progress was limited by the expertise of human experts who've expressed their strategies through careful manual tuning of parameters (4). Additionally, attempts to introduce a deep learning approach failed as engines built with this paradigm, while successful, struggled to match higher-end classical chess engines, with speculations that this is due to the fact they were trained on human gameplay data. (15)

In 2016, DeepMind introduced AlphaGo - an AI agent which learned to play the game of Go not by mimicking human play, but training entirely through self-play. (16) Go is considered to be a complex game with significantly more possible moves on every turn than atoms in the universe! A major breakthrough of this approach was that AlphaGo has beaten the world champion Lee Sedol in the game which was long believed to require intuition and impossible to brute-force. (16)

After AlphaGo, DeepMind developed AlphaZero, a general-purpose reinforcement learning agent capable of mastering any strategy based on two-player games that can be expressed digitally. (7) When AlphaZero was put against Stockfish, the strongest chess engine of the time, in a closed match with 100 games, AlphaZero decisively defeated Stockfish with 28 wins, 72 ties and not a single loss. (8)

AlphaZero achieved, *tabula rasa*, superhuman performance, discovering a new way machines can learn. Instead of relying on human data, being a self-play agent, it surpassed human intelligence entirely, discovering strategies that were previously unimaginable and sometimes incomprehensible by humans. (8) Potentially, if mathematics of AlphaZero can be generalised beyond games to domains such as reasoning or language to go beyond human-level performance, it would be a major step towards superhuman intelligence in the future.

This paper explores the extent to which self-trained reinforcement learning agents like AlphaZero can outperform traditional heuristic search-based systems such as Stockfish. By analysing underlying algorithms and match outcomes of both engines, this paper will seek the understanding of the strengths and limitations of each approach.
## Background Information
### Chess
Chess is a classical two player, deterministic (no element of chance involved; each move leads to a predictable outcome), full knowledge (both players have complete information of the game at all times) game. (8) It is played on an 8x8 board with each player controlling one of their own 16 pieces of 6 different types (king, queen, rook, bishop, knight, pawn) with each piece having its own way to move and capture. Players alternate moves, with the main objective being to win by checkmating the opponent (king is under attack and no legal moves to escape the attack exist). Draws are possible when either: insufficient material is left in the game, both players repeat moves 3 times, or a player can't move on their turn. (21)
### Stockfish
Based on classical search algorithms, Stockfish represents the pinnacle of human ingenuity. It has consistently dominated at competition, notably the Top Chess Engine Championship (TCEC), where it achieved 16 titles and 9 runner up finishes out of 26 tournaments. Its core architecture relies on a deterministic search with domain-specific optimisation, allowing it to evaluate billions of positions per second with remarkable accuracy.

Static evaluation function is a numeric way of determining which side is in advantage in a given position without further search. It takes the board as input and returns a numerical score: positive values indicate advantage for white, negative for black, and zero represents equality. Parameters for evaluation are based on handcrafted features, tuned by chess experts and automated testing. These include but are not limited to:
- Material Balance: material advantage.
- Piece activity and mobility: number of available moves
- Position: control of the centre and to the opponents king, safety of the king.
Parameters are continuously refined and examples above might not represent the state of stockfish.

> Three example positions with evaluation scores showing (1) material advantage, (2) positional advantage, and (3) equality despite uneven material.

In order to determine the best move, Stockfish constructs a game tree, where each node and edge represent board position and legal move leading to a new position. The turn alternates between players as the tree branches out.
In theory, a complete search of this tree could reveal the optimal move sequence from any position. However, chess has an average branching factor of ~35 and typical game length of over 80 moves resulting in a game tree with more possible terminal positions than the estimated number of atoms in the universe, thus making exhaustive search computationally infeasible. 
Minimax acknowledges these limitations by limiting the depth of the search as follows:
	1. Expand the tree to a fixed depth.
	2. Evaluate the leaf nodes using static evaluation function.
	3. Propagate the scores back to the root. At white's turn, select the move that maximises the score; at black's turn, select the move that minimises it.

> **Illustration**: Game tree diagram showing alternating maximizing and minimizing levels.

> **Illustration**: Minimax propagation example, with scores flowing from leaves to root.

Even with minimax, searching all nodes to a given depth is expensive. Alpha-Beta pruning avoids excess branches that cannot affect the final decision. Alpha represents the best score achievable by the maximiser so far, while Beta represents the best score achievable by the minimiser so far. If at any point the minimiser finds a move that leads to a score lower than Alpha, or the maximiser finds a score higher than Beta, the remaining sibling nodes in that branch will be skipped - they are insignificant. This can be imagined as the engine skipping all the moves that will be worse than the best found so far. 

> **Illustration**: Search tree with pruned branches visually marked (e.g., crossed-out subtrees).

Stockfish uses dozens more optimizations under the hood such as Transposition tables (cashing the board positions and reuse results when searching deeper), Move order heuristics (starts the search from the most promising moves), Quiescence search (automatically extends or shrinks the search depth when necessary).
### AlphaZero
AlphaZero is fundamentally different from classical search engines such as Stockfish. Instead of relying on handcrafted evaluation functions and brute-force search, AlphaZero learns to play optimally from scratch solely through self-play and reinforcement learning, utilising a combination of deep neural network and Monte Carlo Tree Search (MCTS). (8)

Evaluation Network
Similar to the static evaluation function used in Stockfish, AlphaZero's deep neural network evaluates the position by estimating its quality. It is denoted by the function in on Equation $(1)$, with weights $\theta$, mapping a board position $s$ to two outputs: a **policy vector** $\mathbf{p}$, representing probability distribution over legal moves (where each entry $p_i$ represents the estimated likelihood of selecting move $i$ at position $s$), and a **value scalar** $v\in[-1,1]$ representing the expected outcome from $s$ (1 indicating a certain win, -1 for certain loss). (8)
$$(\mathbf{p},v)=f_\theta(s) \tag{1}$$
> **Illustration:** Neural network diagram showing input planes of board state, convolutional layers, and policy/value heads.

Monte Carlo Tree Search is a heuristic search strategy which AlphaZero uses for selecting moves. MCT's operate in four main stages, repeated for hundreds of thousands of times before each move: Selection, Expansion, Simulation, and Backpropagation. (23)

- Selection: From the root node (current position), the algorithm traverses the search tree using the Upper Confidence Bound (UCB) formula. It balances between exploration (searching for potentially better moves) and exploitation (following already-found high-value moves). (22)
$$UCB(s,a)= \underbrace{E[\text{win}[k,p]]}_{\text{exploit}} + \underbrace{C\sqrt{\frac{2\ln(n_{parent(k)})}{n_k}}}_{\text{explore}} \tag{2}$$

- Expansion: Exploitation term of the Equation (2) defines the chance of winning by measuring the times won in the node over the total times played, while exploration term defines how much has algorithm already searched by measuring the simulation the parent node has played through over the nodes played through within this node. $C$ is a parameter controlling exploration-exploitation tradeoff. (23)
- Simulation:  After deciding on a leaf node $s'$ to **expand**, new leaf node of the most probable move decided by the UCB is added to it. In classical MCTS, **simulation** is done by calculating win rate by averaging outcome of random play; however, AlphaZero directly uses the network's (Equation (1)) value $v$ for leaf evaluation. (23)
- Backpropagation: The value $v$ is propagated back up to the tree, updating the statistics for each visited node, which will influence the decisions taken on the next cycle. (23)

Unlike Stockfish, which can run right after its creation, AlphaZero requires training for its network to understand what the actual values of positions are. The training loop consists of AlphaZero using first starting with MCST and network randomly initialised parameters. It plays games with itself, storing all the data in a buffer. Network parameters are then updated using gradient descent by using the data from the buffer, by minimising the difference between the predictions made by the network and the true value stored in the buffer. This iterative cycle gradually refines the network's skill all without any external influence.
(Training sections kept short on purpose to give the reader brief understanding while staying loyal to the original aim and research question. For more information please check: (20), (22) and (24))
## Hypothesis
With the differences in the fundamental architectures between LeelaChessZero and Stockfish, it is expected that their playing styles will diverge noticeably. 

Stockfish's handcrafted heuristic evaluations designed by human experts, supported by high search speed (millions of nodes per second), is likely to produce moves that align with established human principles in chess. This ability to calculate deeply into the game suggests that it will have an advantage in sharp forcing positions where concrete calculation is necessary.

LeelaChessZero's self-trained neural network evaluation function may deviate from established human strategic principles  in chess with higher priority on long-term compensations over immediate gain. Although its raw search speed is far lower (thousands of nodes per second), each node evaluation is richer than Stockfish's. This would potentially allow it to excel in planning ahead long-term by excelling at complex positions.

In this paper, it is anticipated that the primary outcome will be the draw with LeelaChessZero winning the majority of the rest, primarily through superior handling of positional and imbalanced positions, while Stockfish will dominate in direct tactical encounters.
## Methodology
This investigation replicates the core approach of the original AlphaZero paper by running a 100 game match between two engines. (8) However, unlike the original paper, which only reviewed the final scores, this work will also conduct a qualitative analysis.

A custom python script was developed to automate match execution. The script communicates between two engines using Portable Game Notation (PGN) protocol, outputs logs after each game, and stores match data in the sqlite database for analysis. Full code and instructions for replications are provided in Appendix (a).

Recent versions of Stockfish incorporate Efficiency Updatable Neural Network (NNUE) evaluation, adding neural network methods into historically a purely search-driven architecture. (25) To preserve a clear contrast of underlying architectures of both models, Stockfish 11 was used, as it is the last major release without NNUE integration. (26)

Due to the proprietary nature of AlphaZero, which makes it unavailable outside DeepMind, LeelaChessZero was selected as an open-source alternative based on the same architecture. It is based on reinforcement learning with self-play, is actively maintained, offers performance optimisations, and has won the TCEC that put it above other AlphaZero-inspired engines.

Hardware used to run this match is a single ARM-based system with 10 CPU, 32 GPU, and 16 NPU cores supported by 32 GB of RAM and 1 TB SSD. This ensures no computational bottlenecks for either engine, providing enough resources for Stockfish's search and sufficient GPU and NPU for LeelaChessZero's neural network evaluations. The limiting factor for both chess engines was chosen to be time: specifically chosen to be 2.5 seconds per move after which, the engine played the best move it found in this time limit.

Lastly, in contrast to certain engine competitions, where engines begin from predetermined opening positions played by humans, *books*, all games here start from the standard chess starting positions. (27) This is done to avoid bias that could arise from giving one chess engine better position over the other.
## Match Results
## Analysis
1. Results
PGN notation for the first five games is available in the Appendix (3). In the 100 game match, LeelaChessZero demonstrated superior performance over Stockfish with 49 wins, 37 ties and 14 losses, averaging 70 moves per game. This contrasts from the original DeepMind match, where AlphaZero remained undefeated with 28 wins and 72 draws. (28)

The smaller proportion of draws in this experiment could be due to difference in hardware, engine versions, and training data, rather than fundamental algorithmic changes. One highly theoretical explanation for the high number of draws in both cases is the notion that "perfect" chess play would result in a tie: a hypothesis that remains unconfirmed until chess will be fully solved. If both engines approached optimal play, wins/losses would only occur when one side deviates from perfection. The higher loss rate for LeelaChessZero compared to AlphaZero in our match suggests that such deviations were more frequent.

2. Threefold repetition
Source of the draws were unusual compared to human games: 32 from threefold repetition and only 5 from insufficient material. (Threefold repetition occurs when both players repeat moves three times. Insufficient material occurs at the end game, where no player has enough pieces to checkmate.)

In Game 7, despite having extra material, Stockfish forced perpetual check to avoid a loss when facing a threat to mate resulting in threefold repetition. Alternatively, Game 8 featured both engines using rooks to prevent opposing kings from approaching, creating a locked position. Several games such as Game 4 ends with threefold repetition, where both sides decide on a draw by threefold repetition in the middle of what looks like a promising game.

The unusual frequency of threefold repetition in chess engines contrasts to one of human grandmaster play, where most draws occur through insufficient material or stalemate. This behaviour suggests that both engines often converge at positions they evaluate as balanced, where the "optimal" move for both repeats the previous position, leading to draw.

3. LeelaChessZero's evaluation function
There was a common pattern of LeelaChessZero clearly giving away pieces in cases such as Move X Game Y or Move A Game B. This often isn't a part of a strategy such as sacrifice (when a piece is given away for some other form of advantage), but rather just a present. 

This behaviour is persistent in AlphaZero's games of the DeepMind's match. An analysis published by an independent Chess YouTuber notices a similar pattern: AlphaZero just gives out extra material without any reason. (29) 

Despite this, in further analysis giving LeelaChessZero more time and resources to check the correctness of the move, the engine was certain in those specific moves, indicating that this wasn't just a mistake but what LeelaChessZero believed as: the optimal move. A possible explanation for such careless behaviour with material could be that we as humans are too attached to pieces in chess and we value having more pieces. Stockfish's evaluation functions are handcrafted by humans, which prevents it from choosing the moves that lose pieces without justification. However, LeelaChessZero hasn't received any of this information during training through self-play, defining its own evaluation function. There is a possibility that material advantage doesn't matter as much as other parameters of the game do, resulting in such a policy.

4. LeelaChessZero's random moves
Within the games, LeelaChessZero often choose the moves that Stockfish and we perceive as waste of turn as they seem random. 

They seem to occur most commonly during the openings such as in Move X Game Y or right before the major attack such as Move A Game B. The same pattern was observed in AlphaZero's moves, which suggests that this is an emergent behaviour of this paradigm of engines as Stockfish never moves in this way by seeing them as purposeless. (29)

Possible explanation for such moves could be that Leela differs from Stockfish and Humans in a way that she sees the game. While Stockfish tries to search into the future by trying out as many moves as possible and finding one which seems most efficient, LeelaChessZero checks the possible legal moves and predicts the possibility of it leading to the victory. You could imagine it as Stockfish looking into short term benefits with near future positions while LeelaChessZero seeing the whole game as one as the probability of winning and loosing.

## Limitations
## Conclusion
