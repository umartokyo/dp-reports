# List of sources
---
1
Which Chess Engine should you use?
Source: https://youtu.be/Y-i5uN6NsaM?si=wmSdoxRawB8Wr7m5
Summary:
- Explains about three chess engines, comparing Stockfish to Leela0 as a strong brute-force approach to intelligent thinking one. Seems like Leela will take more computational resources for analysis.
- There's been a major point of Stockfish being unable to see a move which while being worse technically ending up being way better if given thought (Being blind to sacrificing a knight and imprisoning a rook because that would be -3 points and it can't account for imprisonment of a rook, while Leelo0 sees it right away as it has the intuition necessary).
---
2
Cornell University Game Theory Article
Source: https://blogs.cornell.edu/info2040/2022/09/30/game-theory-how-stockfish-mastered-chess/
Summary:
- Chess is a deterministic game. (However states that it's not fully deterministic, thus there's an element of randomness.)
- Engines take probabilistic approach to maximise the chance of winning. Every position has a state which is either $0.0$ (perfect draw), or mix of $n$ (advantage for white) and $-n$ (advantage for black).
- For each position, stockfish generates a decimal, giving an account of who's winning. Unit for evaluation is **centi-pawns**, and anything more than a magnitude of 1.5 is significant and anything more than a magnitude of 4 is a determined win/loose.
- Stockfish uses a static evaluation function which estimates an evaluation of the position using heuristic (hard-coded by humans) approach.
- Minimax algorithm is used. It builds a game tree with each layer alternating between white and black's moves growing exponentially. If some criteria stops it from growing, it evaluates the positions from the leafs to the original branch. Finally, the optimal path with highest payoff is chosen which estimates both players' best moves. It's important to prune the search tree effectively.
- Alpha-beta pruning stops growing the nodes in scenarios where after own turn, the if opponent can make a move better then it.
- Stockfish utilises a combination of minimax with alpha-beta pruning and a powerful static evaluation function. Developers designed static evaluation functions to consider most aggressive and tactical options first (captures, checks, and deeper tactical ideas) in order to lead to sharper moves. Null-move searching is also involved.
---
3
Shanon's Number
Source: https://youtu.be/Km024eldY1A?si=lJQsn-ZnB1XM0fuo
Summary:
- There are more chess games possible than atoms of the observable universe: Shannon's number $10^{120}$. This is an estimate, and some other mathematicians suggest even larger numbers such as: $10^{10^{50}}$.
---
4
Fascinating Programming of Chess Engine
Source: https://youtu.be/w4FFX_otR-4?si=3yWDA1iN-DFSggJX
Summary:
- All the positions are stored as 64bit integers.
- Position evaluation is hard-coded and the pieces and their positions are taken as arguments for the evaluation function.
- Search happens with alpha-beta pruning.
---
5
How AlphaZero Completely Crushed Stockfish
Source: https://youtu.be/8dT6CR9_6l4?si=HmqGTV-nOPe6LbuC
Summary:
- AlphaZero has beaten stockfish by large margin in the closed tournament. Google has published some of the games. While in original match AlphaZero used the Google's infrastructure while Stockfish was running in quad-core cpu, the re-match was done on fair grounds with both algorithms running on the same hardware.
- "AlphaZero loves material sacrifice, it loves peace activity and it sacrifices material for time, it will sacrifice material for damage, ad it will sacrifice material to take your soul." (2:10) "One thing about AlphaZero is it's incredible ability to just leave material in the hands of the opponent and just go yeah whatever I'll take care of it later. I don't need immediate kind of benefit." (2:20)
- "AlphaZero absolutely loves suppressing movement. It evaluates position not on plus two, not on plus three but on probability of victory and probability of defeat or draws." (4:41) 
- "It's a long term endgame weakness"
-  1000 games; alphazero won 155 to 7 with 839 draws.
- "Its ability to absorb just pawn loss and see the horizon is so incredible."
- "A backwards retrieving move is when you know AlphaZero is about to kill you."
- "alpha zero is absolutely un just unmatched at endgames it waits it waits it waits it finally at some moment will need to move a pawn or will it or will it the grinds and grinds and grinds and grinds"
---
6
Stockfish
Source: https://www.chessprogramming.org/Stockfish
Summary:
- 
---
7
AlphaZero: An Introduction
Source: https://youtu.be/gsbkPpoxGQk?si=berNBnMLrSZ0_f4D
Summary:
- The game is represented as a matrix. Every single possible next move is represented as a branch from the node. All the leaf nodes' values (probabilities of winning) are estimated and passed upwards in the branch. 
- We start with a position, node. Afterwards, evaluating all possible future moves and their potential, we select more promising ones, then after figuring the best choice, taking the leaf as a node and continuing downwards.
- Upper Confidence Bound Score for Trees (UCB) is calculated among candidate nodes, and selects the one with higher score. If scores are similar, it is randomly selected. UCB balances between exploration and exploitation.
- For the evaluation, we represent the board as a matrix and use CNN.
- We build a graph of possible game states using Monte Carlo Tree Search, then we feed data from the graph to CNN and learn to predict probabilities conditioned on taking certain moves. Returning back to generating data on the graph, we repeat this cycle until the engine is trained.
---
8
Mastering Chess and Shogi by Self-Play with a General Reinforcement Learning Algorithm
Source: https://arxiv.org/pdf/1712.01815
Summary:
- Computer chess is old as computer science itself. Babbage, Turing, Shanon, and von Neumann all had ideas on analysing and playing a game of chess digitally.
- Creating programs that can learn by themselves is a long-standing ambition of artificial intelligence. AlphaGo Zero achieves superhuman performance in Go through tabular rasa.
- Computer algorithms beat the humans in chess in 1997 with Deep Blue defeating the world champion Garry Kasparov. 
- "These programs evaluate positions using features hand-crafted by human grandmasters and carefully tuned weights, combined with a high-performance alpha-beta search that expands a vast search tree using a large number of clever heuristics and domain-specific adaptations."
- AlphaZero replaces handcrafted knowledge and domain-specific augmentations of the previously used algorithms with deep neural networks and a tabular rasa (blank slate) reinforcement learning algorithm.
- AlphaZero uses a general purpose Monte-Carlo tree search algorithm. 
- The algorithm is poorly explained so i didn't really get it.
---
9
AlphaZero: DeepMind’s AI Works Smarter, not Harder
Source: https://youtu.be/1gWpFuQlBsg?si=G1uQZwmlWYvMYtgb
Summary:
- "The point of AlphaZero is not to solve chess or any of these games. Its main point is to show that a general AI can be created that can perform on a superhuman level on not one, but several different tasks at the same time."
-  "It was quite inspirational. I was thinking at several points in the game, how would #AlphaZero have approached this?" - Magnus Carlsen
- "I can't disguise my satisfaction that it plays with a very dynamic style, much like my own!" - Garry Kasparov
---
10
Has Generative AI Already Peaked? - Computerphile
Source: https://youtu.be/dDUC-LqVrPU?si=MoqDN5lBZYdMYJqT
Summary:
- Due to nature of generative models, the performance gain from the increase of the model size and training data will be plateauing due to the nature of these algorithms.
---
11
Introducing ChatGPT
Source: https://openai.com/index/chatgpt/

---
12
Easy-to-Hard Generalization: Scalable Alignment Beyond Human Supervision
Source: https://arxiv.org/pdf/2403.09472
Summary:
- Proposition of a method which will allow AI to surpass human intellect.
---
13
When machines take over: professional chess as a model case for the societal impact of superhuman AI
Source: https://link.springer.com/article/10.1007/s00146-025-02465-w?utm_source=chatgpt.com
- F
---
14
Deep Blue
Source: https://www.ibm.com/history/deep-blue
- F
---
15
Giraffe: Using Deep Reinforcement Learning to Play Chess
Source: https://arxiv.org/abs/1509.01549?utm_source=chatgpt.com
- F
---
16
AlphaGo - The Movie | Full award-winning documentary
Source: https://youtu.be/WXuK6gekU1Y?si=cnf8coEDLLG108Iv
- F
---
20
How do Chess Engines work? Looking at Stockfish and AlphaZero | Oliver Zeigermann
Source: https://youtu.be/P0jd8AHwjXw?si=xHHx90crK4cpnbt2
Summary:
- Speaker shows a position from a game between AlphaZero and Stockfish, where AlphaZero clearly seems to be loosing by human metrics but is still very confident in itself winning and ending up as a winner for the match.
Stockfish!
- Stockfish is a classic chess engine. Deep Blue too was of the same family.
- Central Question: What move to play next to maximise chance of winning? (You might ask, can we not just take the best position?) Theoretically, stockfish just does that. It evaluates all possible future positions with handcrafted features. 
- Stockfish's evaluation function depends on: (all of these are handcrafted)
	- Midgame/endgame specific material point values
	- Mobility and trapped pieces
	- Pawn structure
	- King safety
	- More
- Looking ahead 1 position isn't enough, you must look more ahead. The further you look ahead, the better it is. Otherwise, the engine will prioritise the short-term points over actual 
- All the perfect moves are impossible to pre-compute due to Shanon's number being too large. Size of complete Search Tree would be too large. Thus what we're doing is: limiting the depth of the search, limit in breadth. Typically, we view the game as one player maximising the outcome while other minimises.
- Minimax: restricts search breadth. The circle is player 1, maximising. Square is player 2, minimising. ![[screenshot1.png]]
- Alpha Beta Pruning: It prunes the branches that are not promising to the player because the player would never chose that branch anyway (second player has a good move). 
- Stockfish heavily relies on human knowledge put into heuristics by additional cuts using domain knowledge, ordering heuristics: efficiency of alpha-beta search depends critically upon the order in which moves are considered, extends the search depth of promising variants while reducing the search depth of unpromising variants. It has an opening book and table based endgames for 6 (sometimes 7) pieces or less. This was the state of the art. Can run powerfully on weak hardware.
AlphaZero!
- This is not machine learning, it's a reading machine? 
- Go was supposed to be the game machines won't ever play due to it's high branching factor and belief of it being intuitional.
- AlphaZero:
	1. Replaces handcrafted evaluation function with a deep neural network.
		CNN in AlphaZero is a single network, state-of-the-art ResNet styled.
		Input: board position
		Output: Policy head: move probabilities (vector) & Value head: winning probability (scalar).
	2. Monte Carlo Tree Search instead of classical search with alpha-beta pruning
		Probabilities of model's policy head output works as input to MCTS simulation. MCTS explores game trees based on these priors. Heavy simulation play-outs using model probabilities. Terminates after a certain number of iterations. Creates new probabilities based on the outcomes of these simulations.

		Monte Carlo Experiments: Approximates results by repeating random sampling
		Monte Carlo Tree Search:
		Phase 1: Selection, it will consider which of the moves looks more promising.
		Phase 2: Expansion, one random next move is chosen.
		Phase 3: Simulation, it will try doing simulations
		Phase 4: Back-propagation, it will count the probabilities given from the simulation and change the nodes accordingly.
		There is an exploration vs exploitation tradeoff: Choose moves with high average win ration or choose more moves but with few simulations
	3. Use Reinforcement Learning to train the model: AlphaZero learns how to play chess by playing against itself.
---
21
FIDE Chess Rules
Source: https://handbook.fide.com/chapter/E012023

---
22
Monte Carlo Tree Search - Computerphile
Source: https://youtu.be/BEFY7IHs0HM?si=JrIYkq7WaUWx3WYa

---
23
Advanced 4. Monte Carlo Tree Search
Source: https://youtu.be/xmImNoDc9Z4?si=XjXAX5UHdPXcIqLp

---
24
Alpha Zero and Monte Carlo Tree Search
Source: https://youtu.be/62nq4Zsn8vc?si=yB0uQCoFWeIfxFbt

---
25
NNUE
Source: https://github.com/official-stockfish/nnue-pytorch/blob/master/docs/nnue.md#sfnnv8-architecture

---
26
Introducing NNUE evaluations
Source: https://stockfishchess.org/blog/2020/introducing-nnue-evaluation/

---
27
Rules (TCEC)
Source: https://wiki.chessdom.org/Rules

---
28
Google's AlphaZero Destroys Stockfish In 100-Game Match
Source:  https://www.chess.com/news/view/google-s-alphazero-destroys-stockfish-in-100-game-match

---
29
How AlphaZero Completely CRUSHED Stockfish
https://youtu.be/8dT6CR9_6l4?si=tjoCQ7i4YTzvRNpJ

---
30


Other Sources:
- [Leela Chess Zero](https://www.chessprogramming.org/Leela_Chess_Zero)
- [Large collection of MCTS Variants](http://www.incompleteideas.net/609%20dropbox/other%20readings%20and%20resources/MCTS-survey.pdf)
- [EE1](https://c0abe087-b0be-4c74-ad20-4c339141ff55.filesusr.com/ugd/720584_2effd87e251b4e51b74744492085c01e.pdf)
- [EE2](https://c0abe087-b0be-4c74-ad20-4c339141ff55.filesusr.com/ugd/720584_431b7e94bc5e4b4a91067e422400e1a9.pdf)
- 