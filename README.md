# Maze Escape Game

This is a Python-based maze game where a player-controlled red ball navigates a maze to reach the exit while being chased by a green ball. The green ball tracks the player's movements and tries to catch them. The game ends when the player touches the maze walls or is caught by the green ball.

## Features
- **Player Control**: Move the red ball using arrow keys (left, right, up, down).
- **Chasing AI**: A green ball follows the player, using BFS pathfinding to move towards the player.
- **Maze Generation**: The maze is dynamically generated using a Depth-First Search (DFS) algorithm.
- **Timer**: The game has a time limit of 90 seconds that resets when it runs out.
- **Exit Objective**: The player must reach the exit to win the game.
- **Collision Detection**: The game checks if the player collides with maze walls or is caught by the green ball.

## Requirements
- Python 3.x
- Pygame library

## Installation
1. Ensure you have Python installed on your system.
2. Install Pygame via pip:
   ```bash
   pip install pygame
   ```

## How to Play
1. Run the game by executing the script:
   ```bash
   python maze_game.py
   ```
2. Use the arrow keys to move the red ball.
3. Avoid getting caught by the green ball.
4. Reach the exit to win the game!

## Controls
- **Arrow Keys**: Move the red ball (player).
- **Green Ball**: Chases the player using AI pathfinding.

## Reset Conditions
- If the player collides with the maze walls or gets caught by the green ball, the game resets.
- The game also resets when the 90-second timer runs out.

## License
This project is open-source and available under the MIT License.
