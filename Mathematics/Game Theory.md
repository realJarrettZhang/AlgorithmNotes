<center><b><font size=6>Game Theory</font></b></center>



### 1. Basic Definitions

___

​	Game theory studies the predicted and actual behaviors of agents engaged in competitive or adversarial interactions under specified rules, and investigates their optimal strategies. This article focuses on **Impartial Combinatorial Games (ICG)**, which exhibit the following characteristics:

+ Two players move alternately, and both possess complete information about the game.
+ At any point in the game, the set of legal moves depends solely on the current position, not on the player.
+ The position space is finite, and no previously visited position will reappear.
+ A player who has no legal move on their turn loses.



### 2. Impartial Combinatorial Game Theory

___

​	Any impartial combinatorial game can be modeled as a directed acyclic graph $G=(V,E)$, where vertices represent game positions and directed edges correspond to legal moves. A complete position graph contains every reachable position and all possible transitions from the initial position.

​	Define the **Sprague–Grundy Function** $f(x)$ as:
$$
f(x)=\text{mex}\{f(y)\mid (x,y)\in E\}
$$

​	Here, $\text{mex}(S)$ denotes the smallest non-negative integer not contained in the set $S$:
$$
\text{mex}(S)=\min\{x\mid x\in \mathbb{N}-S\}
$$

​	A position $x$ is losing for the player to move if and only if it has no successor positions, or all of its successors are winning positions. In such cases, clearly $f(x)=0$. Conversely, a position is winning if and only if it has at least one losing successor, in which case $f(x)>0$. As the game graph is acyclic, all game positions can be computed in reverse topological order.

​	Some games can be decomposed as **the Sum of Multiple Impartial Combinatorial Games**. Under this rule set, a move consists of choosing one ICG component and making a single legal move within it. The Sprague–Grundy value of the sum is equal to **the Bitwise XOR of the Sprague–Grundy Values** of the initial positions of all component games.
