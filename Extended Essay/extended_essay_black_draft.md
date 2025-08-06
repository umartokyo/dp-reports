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