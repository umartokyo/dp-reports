## Research Question
To what extent does a deep reinforcement learning agent trained solely through self-play, similar to AlphaZero compare to traditional heuristic-based search algorithm such as Stockfish in their performance, win rate against each other?
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