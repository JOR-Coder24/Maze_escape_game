import pygame
import random
import time

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 600, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))

# Colours
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)

# Ball settings
ball_radius = 10
player_pos = [50, 50]  # Starting position for the red ball
green_pos = [WIDTH - 50, HEIGHT - 50]  # Starting position for the green ball

# Maze settings
cols, rows = 30, 20  # Number of columns and rows in the maze
cell_size = WIDTH // cols
maze_thickness = 2
exit_pos = (cols - 2, rows - 2)  # Exit position in the maze

# Timing settings
time_limit = 90  # Reset after 90 seconds
start_time = time.time()

# Movement speed
player_speed = 5
green_speed = 2  # Slower speed for the green ball

# Clock for controlling FPS
clock = pygame.time.Clock()

# Maze grid setup
maze = []
visited = []

# Directions for DFS: (dx, dy)
directions = [(0, -1), (1, 0), (0, 1), (-1, 0)]


class Cell:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.walls = [True, True, True, True]  # [Top, Right, Bottom, Left]
        self.visited = False

    def draw(self):
        x, y = self.x * cell_size, self.y * cell_size
        if self.walls[0]:  # Top wall
            pygame.draw.line(screen, WHITE, (x, y), (x + cell_size, y), maze_thickness)
        if self.walls[1]:  # Right wall
            pygame.draw.line(screen, WHITE, (x + cell_size, y), (x + cell_size, y + cell_size), maze_thickness)
        if self.walls[2]:  # Bottom wall
            pygame.draw.line(screen, WHITE, (x, y + cell_size), (x + cell_size, y + cell_size), maze_thickness)
        if self.walls[3]:  # Left wall
            pygame.draw.line(screen, WHITE, (x, y), (x, y + cell_size), maze_thickness)


# Function to remove walls between two cells
def remove_walls(current, next):
    dx = current.x - next.x
    dy = current.y - next.y
    if dx == 1:  # Current is to the right of next
        current.walls[3] = False
        next.walls[1] = False
    elif dx == -1:  # Current is to the left of next
        current.walls[1] = False
        next.walls[3] = False
    if dy == 1:  # Current is below next
        current.walls[0] = False
        next.walls[2] = False
    elif dy == -1:  # Current is above next
        current.walls[2] = False
        next.walls[0] = False


# DFS Maze Generation
def dfs_maze_generation(x, y):
    stack = []
    current_cell = maze[y][x]
    current_cell.visited = True
    stack.append(current_cell)

    while stack:
        current_cell = stack[-1]
        neighbours = []
        for direction in directions:
            nx, ny = current_cell.x + direction[0], current_cell.y + direction[1]
            if 0 <= nx < cols and 0 <= ny < rows and not maze[ny][nx].visited:
                neighbours.append(maze[ny][nx])

        if neighbours:
            next_cell = random.choice(neighbours)
            remove_walls(current_cell, next_cell)
            next_cell.visited = True
            stack.append(next_cell)
        else:
            stack.pop()


# Function to initialize the maze
def initialize_maze():
    global maze
    maze = [[Cell(x, y) for x in range(cols)] for y in range(rows)]
    dfs_maze_generation(0, 0)


# Function to draw the maze
def draw_maze():
    for row in maze:
        for cell in row:
            cell.draw()


# Function to draw the red ball (player)
def draw_player():
    pygame.draw.circle(screen, RED, (player_pos[0], player_pos[1]), ball_radius)


# Function to draw the green chaser ball
def draw_green_chaser():
    pygame.draw.circle(screen, GREEN, (green_pos[0], green_pos[1]), ball_radius)


# Function to draw the exit
def draw_exit():
    ex, ey = exit_pos[0] * cell_size + cell_size // 2, exit_pos[1] * cell_size + cell_size // 2
    pygame.draw.rect(screen, BLUE, (ex - 15, ey - 15, 30, 30))


# Function to move the green ball towards the red ball using BFS
def bfs_pathfinding(start, goal):
    queue = [start]
    came_from = {start: None}
    while queue:
        current = queue.pop(0)
        if current == goal:
            break
        cx, cy = current
        for direction in directions:
            nx, ny = cx + direction[0], cy + direction[1]
            if 0 <= nx < cols and 0 <= ny < rows:
                neighbor = (nx, ny)
                if neighbor not in came_from and not maze[ny][nx].walls[
                    directions.index((-direction[0], -direction[1]))]:
                    queue.append(neighbor)
                    came_from[neighbor] = current

    path = []
    step = goal
    while step:
        path.append(step)
        step = came_from[step]
    path.reverse()
    return path


def move_green_towards_player():
    gx, gy = green_pos[0] // cell_size, green_pos[1] // cell_size
    px, py = player_pos[0] // cell_size, player_pos[1] // cell_size

    if (gx, gy) == (px, py):
        return

    path = bfs_pathfinding((gx, gy), (px, py))
    if len(path) > 1:
        next_step = path[1]
        ngx, ngy = next_step
        if green_pos[0] < (ngx + 0.5) * cell_size:
            green_pos[0] += green_speed
        elif green_pos[0] > (ngx + 0.5) * cell_size:
            green_pos[0] -= green_speed
        if green_pos[1] < (ngy + 0.5) * cell_size:
            green_pos[1] += green_speed
        elif green_pos[1] > (ngy + 0.5) * cell_size:
            green_pos[1] -= green_speed


# Function to check if player touches maze walls
def check_collision():
    px, py = player_pos[0] // cell_size, player_pos[1] // cell_size
    cell = maze[py][px]

    if cell.walls[0] and player_pos[1] < (py * cell_size + maze_thickness):
        return True
    if cell.walls[1] and player_pos[0] > (px * cell_size + cell_size - maze_thickness):
        return True
    if cell.walls[2] and player_pos[1] > (py * cell_size + cell_size - maze_thickness):
        return True
    if cell.walls[3] and player_pos[0] < (px * cell_size + maze_thickness):
        return True

    return False


# Function to check if the player has reached the exit
def check_exit():
    px, py = player_pos[0] // cell_size, player_pos[1] // cell_size
    if (px, py) == exit_pos:
        return True
    return False


# Function to draw the timer
def draw_timer(time_left):
    font = pygame.font.SysFont(None, 36)
    timer_text = font.render(f'Time left: {int(time_left)}', True, WHITE)
    screen.blit(timer_text, (10, 10))


# Game Loop
initialize_maze()
running = True
while running:
    screen.fill(BLACK)  # Clear the screen

    # Calculate elapsed time
    elapsed_time = time.time() - start_time

    # Check if the time limit has been reached
    if elapsed_time >= time_limit:
        print("Time's up! Resetting game...")
        player_pos = [50, 50]
        green_pos = [WIDTH - 50, HEIGHT - 50]
        start_time = time.time()  # Reset the start time

    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_pos[0] -= player_speed
    if keys[pygame.K_RIGHT]:
        player_pos[0] += player_speed
    if keys[pygame.K_UP]:
        player_pos[1] -= player_speed
    if keys[pygame.K_DOWN]:
        player_pos[1] += player_speed

    # Move green chaser towards the player
    move_green_towards_player()

    # Draw the maze
    draw_maze()

    # Draw the player, the green chaser, the exit, and the timer
    draw_player()
    draw_green_chaser()
    draw_exit()
    draw_timer(time_limit - elapsed_time)

    # Check if the player collides with the walls or is caught by the green ball
    if check_collision() or (abs(player_pos[0] - green_pos[0]) < ball_radius * 2 and abs(
            player_pos[1] - green_pos[1]) < ball_radius * 2):
        print("Game reset!")
        player_pos = [50, 50]
        green_pos = [WIDTH - 50, HEIGHT - 50]
        start_time = time.time()

    # Check if the player reaches the exit
    if check_exit():
        print("You won!")
        running = False

    # Update the screen
    pygame.display.flip()

    # Limit the frame rate
    clock.tick(30)

# Quit the game
pygame.quit()
