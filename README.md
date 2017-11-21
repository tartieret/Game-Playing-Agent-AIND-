
# Game-playing Agent for the "Isolation" game

![Example game of isolation](viz.gif)

## Introduction

In this project, we develop an adversarial search agent to play the game "Isolation". Isolation is a deterministic, two-player game of perfect information in which the players alternate turns moving a single piece from one cell to another on a board. Whenever either player occupies a cell, that cell becomes blocked for the remainder of the game. The first player with no remaining legal moves loses, and the opponent is declared the winner. These rules are implemented in the `isolation.Board` class provided in the repository.

This project uses a version of Isolation where each agent is restricted to L-shaped movements (like a knight in chess) on a rectangular grid (like a chess or checkerboard). The agents can move to any open cell on the board that is 2-rows and 1-column or 2-columns and 1-row away from their current position on the board. Movements are blocked at the edges of the board (the board does not wrap around), however, the player can "jump" blocked or occupied spaces (just like a knight in chess).

Additionally, agents have a fixed time limit each turn to search for the best move and respond. If the time limit expires during a player's turn, that player forfeits the match, and the opponent wins.


## Implementation

This project implements a minimax search with iterative deepening and alpha-beta pruning. 

## Heuristics

For this project I implemented several heuristics and compared them with the following game-playing agents in a round-robin tournament:

- Random: An agent that randomly chooses a move each turn.
- MM_Open: MinimaxPlayer agent using the open_move_score heuristic with search depth 3
- MM_Center: MinimaxPlayer agent using the center_score heuristic with search depth 3
- MM_Improved: MinimaxPlayer agent using the improved_score heuristic with search depth 3
- AB_Open: AlphaBetaPlayer using iterative deepening alpha-beta search and the open_move_score heuristic
- AB_Center: AlphaBetaPlayer using iterative deepening alpha-beta search and the center_score heuristic
- AB_Improved: AlphaBetaPlayer using iterative deepening alpha-beta search and the improved_score heuristic

### AB_Custom – Number of possible moves in the current and next turns

For each player, this heuristic calculates the sum of possible moves in the next two turns. The final score is the difference of the results obtained for each player.

Score = [Nb possible moves in next 2 turns for active player] – [Nb possible moves in next 2 turns for opponent player]

The idea behind this heuristic is to prevent the agent from selecting positions that do not allow any “escape”. 

###	AB_Custom2 – Number of blank cells in are around each player

This heuristic calculates the number of blank cells in a 5x5 area around the active player. Using this heuristic the agent will tend to move in areas with the highest number of blank cells. It will also stay away from the corners at the beginning of the game. 

Score = [number of blank cells in 5x5 area around player] - [number of blank cells in 5x5 area around opponent]

###	AB_Custom3 – Ratio

This heuristic combines different metrics seen in the previous heuristics

Score = [number of blank cells in 5x5 area around player] / [number of blank cells in 5x5 area around opponent] * len(current player moves)/ len(opponent player moves)

###	AB_Custom_4 – Weighted difference between player moves

This heuristic is given by:
Score = len(current player moves) – gamma*len(opponent player moves)
Where gamma is a positive coefficient.

The idea is to make the player more or less aggressive by adding some weight on the number of possible moves for the opponent player. If gamma is low, the player will focus on moves that open more possibilities for himself. If gamma is high, the player will tend to select moves that reduce the number of possible moves for the opponent.
I tried different values of gamma and found out that gamma=1.5 gives the best results.

###	AB_Custom_5 – Weighted difference between player moves (gamma=3)

This heuristic is the same than 4) but with gamma=3.

###	AB_Custom_6 – Combination of 2 heuristics

This heuristic combines Custom1 and Custom4: if the number of possible moves is less than 4, then the heuristics is the difference in number of moves in the next two turns (=Custom1). Otherwise, the heuristics uses Custom4. This optimizes the search by increasing the calculation speed and allow to explore the tree at deeper levels.


## Tournament results

|    #    |    Opponent          |    ABImprov.    |        |    Custom    |        |    Custom2    |        |    Custom3    |            |     Custom4      |            |     Custom5      |            |     Custom6      |            |
|---------|----------------------|-----------------|--------------|---------------|---------------|---------------|---------------|---------------|------------|-----------|------------|-----------|------------|-----------|------------|
|         |                      |    Win          |    Lost      |    Win        |    Lost       |    Win        |    Lost       |    Win        |    Lost    |    Win    |    Lost    |    Win    |    Lost    |    Win    |    Lost    |
|    1    |    Random            |    37           |    13        |    39         |    1          |    33         |    7          |    39         |    1       |    38     |    2       |    37     |    2       |    35     |    5       |
|    2    |    MM_Open           |    26           |    14        |    30         |    10         |    19         |    21         |    27         |    13      |    31     |    9       |    29     |    9       |    31     |    9       |
|    3    |    MM_Center         |    33           |    7         |    31         |    9          |    27         |    13         |    32         |    8       |    24     |    16      |    35     |    16      |    37     |    3       |
|    4    |    MM_Improved       |    29           |    11        |    26         |    14         |    26         |    14         |    28         |    12      |    29     |    11      |    25     |    11      |    28     |    12      |
|    5    |    AB_Open           |    21           |    19        |    18         |    22         |    14         |    26         |    23         |    17      |    19     |    21      |    18     |    21      |    23     |    17      |
|    6    |    AB_Center         |    20           |    20        |    23         |    17         |    17         |    23         |    27         |    13      |    25     |    15      |    20     |    15      |    26     |    14      |
|    7    |    AB_Imp.           |    17           |    23        |    20         |    20         |    16         |    24         |    17         |    23      |    22     |    18      |    18     |    18      |    19     |    21      |
|         |    Win Rate          |      65.4%      |         |    66.8%      |          |    54.3%      |          |    68.9%      |            |     67.1%      |            |     64.3%      |            |      71.1%     |            |



