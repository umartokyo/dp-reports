## Subject
Computer Science
## Topic
Reinforcement Learning
## Title
Undecided
## Research question
To what extent does a deep reinforcement learning agent trained solely through self-play, similar to AlphaZero compare to traditional heuristic-based search algorithm such as Stockfish in their performance, win rate against each other?
## Introduction
Recently, with chatGPT, the boom in ai has happened. However, with companies trying to build smarter and smarter models, with each getting bigger and bigger, we see a plato in it's performance, a human bottleneck of intelligence with the sole reason being the fact that they are learning from our data and generalising our behaviour. However, there've been a similar plato of progress in ai before: specifically, chess ai.

The first algorithms for playing chess were devised earlier than the invention of the computers themselves by bright minds of the past such as ..., ... . The core algorithm - heuristic search with alpha-beta pruning (explained in detailed later) has been iterated on and perfected over the years, however, the human players who were designing the engine and tuning the parameters was the bottleneck. The deep learning approach was also tested, but limited with the human data, it didn't come close to the performance of the classical chess engines of those times.

However, in 201X, Google announced AlphaGo - an artificial intelligence model which learned not by generalising human performance with the data it was fed but rather learning all the patterns and rules solely by itself through self-play, emerging with super-intelligence in the game and beating the world champion in the game of Go, the game which thought to be based on intuition rather than computation. AlphaGo has learned the game, gained the intuition, illustrated the limits of human intelligence. The fascinating part of this approach in comparison to everything else that came before was it's ability to learn by it's own. We were no longer guiding ai to learn as we did, we're letting ai explore new paths, and every time, it broke the limits of what has done before and educated itself to go way beyond humans, achieving super-intelligence in that field. 

Following AlphaGo, researchers at Google generalised it to AlphaZero family, which learned to play any tactical board games including chess. Annihilating the strongest chess engine of it's time Stockfish (which uses traditional search algorithm) in the closed 100 game match with 28 wins, 72 draws, and no losses, AlphaZero have shown a promising field for evolution of ai: machines learning not from us by from themselves. 

If underlying mathematics of AlphaZero could be generalised to logic / language tasks, there's a potential in achieving a general super-intelligence.

In this research, we will cover the underlying mathematics and programming of both approaches, understanding where the 
## Background Information
### Chess
Chess is a classical two player, perfect information game: meaning both players have complete information of the game at all times. It is also deterministic, with no element of chance involved: each move leads to a predictable outcome. () It is played on an 8x8 board with each player controlling one of own 16 pieces of 6 different types (kind, queen, rook, bishop, pawn) with each piece having its own way to move and capture. Players alternate moves, with the main objective being to win by checkmating the opponent (king is under attack and no legal moves to escape the attack exist). Draws are possible when either: no sufficient material is left in the game, both player repeat moves 3 times, or a player can't move on their turn.
### Stockfish
Stockfish is a state of the art chess engine based on classical search algorithms,  representing the pinnacle of human ingenuity. It has consistently dominated at competition, notably the Top Chess Engine Championship (TCEC), where it achieved 16 titles and 9 runner up finishes out of 26 tournaments. It's core architecture relies on a deterministic search with domain-specific optimisation, allow it to evaluate billions of positions per second with remarkable accuracy.

1. Static Evaluation Function
Static evaluation function is numeric way of determining which side is in advantage in a given position without further search. It takes the board as input and returns a numerical score: positive values indicate advantage for white, negative for black, and zero represents equality. Parameters for evaluation are based on handcrafted features, tuned by chess experts and automated testing. These include but are not limited to:
- Material Balance: material advantage.
- Piece activity and mobility: number of available moves
- Position: control of the centre and to the opponents king, safety of the king.
Parameters are continuously refined and examples above might not represent the state of stockfish.

> Three example positions with evaluation scores showing (1) material advantage, (2) positional advantage, and (3) equality despite uneven material.

2. Game Tree Search and Minimax
In order to determine the best move, Stockfish constructs a game tree, where each node and edge represent board position and legal move leading to a new position. The turn alternates between players as the tree branches out.
In theory, a complete search of this tree could reveal the optimal move sequence from any position. However, chess has an average branching factor of ~35 and typical game length of over 80 moves resulting in a game tree with more possible terminal positions than the estimated number of atoms in the universe, thus making exhaustive search computationally infeasible. 
Minimax acknowledges these limitations by limiting the depth of the search as follows:
	1. Expand the tree to a fixed depth.
	2. Evaluate the leaf nodes using static evaluation function.
	3. Propagate the scores back to the root. At white's turn, select the move that maximises the score; at black's turn, select the move that minimises it.

> **Illustration**: Game tree diagram showing alternating maximizing and minimizing levels.

> **Illustration**: Minimax propagation example, with scores flowing from leaves to root.

3. Alpha-Beta Pruning
Even with minimax, searching all nodes to a given depth is expensive. Alpha-Beta pruning avoids excess branches that cannot affect the final decision. Alpha represents the best score achievable by the maximiser so far, while Beta represents the best score achievable by the minimiser so far. If at any point the minimiser finds a move that leads to a score lower than Alpha, or the maximiser finds a score higher than Beta, the remaining sibling nodes in that branch will be skipped - they are insignificant. This can be imagined as the engine skipping all the moves that will be worse than the best found so far. 

> **Illustration**: Search tree with pruned branches visually marked (e.g., crossed-out subtrees).

---
### AlphaZero
AlphaZero is fundamentally different from classical search engines such as Stockfish. AlphaZero learns to play optimally from scratch solely through self-play and reinforcement learning, utilising combination of deep neural networks and Monte Carlo Tree Search (MCTS).

Evaluation Network
Similar to static evaluation function used in Stockfish, AlphaZero's deep neural network evaluates the position. It is written by the function in on Equation $(1)$, parametrised with weights $\theta$, which maps a board position $s$ to two outputs: a **policy vector** $\mathbf{p}$, representing probability distribution over legal moves, where each entry $p_i$ represents the estimated likelihood of selecting move $i$ at position $s$, and a **value scalar** $v\in[-1,1]$ representing expected outcome from position $s$ (1 indicating a certain win, -1 for certain loss). 
$$(\mathbf{p},v)=f_\theta(s) \tag{1}$$
> **Illustration:** Neural network diagram showing input planes of board state, convolutional layers, and policy/value heads.

Monte Carlo Tree Search
Monte Carlo Tree Search is a heuristic search strategy which AlphaZero uses for selecting moves. MCT's operate in four main stages, repeated for hundreds of thousands of simulations before each move: Selection, Expansion, Simulation, and Backpropagation. 

In **selection stage**, algorithms selects path down the search tree using the Upper Confidence Bound (UCB) formula. It balances between exploration (searching for new potentially better moves) and exploitation (going deeper down in the best move found so far.) 
$$UCB(s,a)= \underbrace{E[\text{win}[k,p]]}_{\text{exploit}} + \underbrace{C\sqrt{\frac{2\ln(n_{parent(k)})}{n_k}}}_{\text{explore}} \tag{2}$$
UCB measures the likelihood the move will be chosen through sum of two terms. Exploitation term of the Equation (2) defines the chance of winning by measuring the times won in the node over the total times played. Exploration term defines how much has algorithm already searched by measuring the nodes the parent has played through over the nodes played through within this node. $C$ is used as a weight, which balances between the two terms.

After deciding on a leaf node $s'$ to **expand**, new leaf node of the most probable move decided by the UCB is added to it. In classical MCTS, **simulation** is done by calculating win rate by averaging outcome of random play; however, AlphaZero directly uses the network's (Equation (1)) value $v$ for leaf evaluation. Lastly, $v$ is **backpropagated** up to the tree, updating visit counts and values. 

---
## Methodology
In carrying this investigation, we will replicate the methodology of the original AlphaZero paper by hosting 100 game match between two engines. (8) However, while the original paper has only reviewed the final score, here the games will be additionally analysed qualitatively.

A custom python script was written for flexibility and automation. A single python file communicates between two engines using Portable Game Notation (PGN) protocol. It outputs logs after every game and scores all the data in the sqlite database for future analysis. The code is provided in Appendix (a) with further instructions for replication.

The recent versions of Stockfish heavily relies on Efficiency Updatable Neural Network (NNUE), which adapts and implements deep neural networks, stripping it from its search-based purity. (25) For this reason, the older variant will be used, specifically Stockfish 11, as it was the last version that hasn't relied on NNUE. (26)

AlphaZero is not open-source, which makes it unattainable for non-DeepMind employees. However, LeelaChessZero is an open-source alternative, based on the same architecture as AlphaZero, implementing self-play with reinforcement learning. LeelaChessZero specifically was chosen over other alternatives of AlphaZero due to it being constantly maintained and with optimisations, it's performance being better by a large margin.

Hardware running this match was a singular Apple-Silicon based computer with M1-Max containing 10 CPU, 32 GPU, and 16 Neural Engine Cores with 32 Gb of RAM and 1Tb of storage. This hardware doesn't bottleneck either of chess engines, providing enough CPU cores for Stockfish and memory with GPU cores for LeelaChessZero which it is strongly reliant on.

Unlike some popular chess tournaments, where first several moves are given from the games played by humans, in this paper, engines will be given control over from the beginning. This should eliminate unfair advantage one could get over another if the position is unfair.