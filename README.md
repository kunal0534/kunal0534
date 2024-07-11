pip install pygame

import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 600
SCREEN_HEIGHT = 600
GRID_SIZE = 8
TILE_SIZE = SCREEN_WIDTH // GRID_SIZE

# Colors
COLORS = [
    (255, 0, 0),  # Red
    (0, 255, 0),  # Green
    (0, 0, 255),  # Blue
    (255, 255, 0),  # Yellow
    (255, 165, 0),  # Orange
    (0, 255, 255)  # Cyan
]

# Set up display
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Match-3 Game")

# Load tiles
def create_grid():
    grid = []
    for row in range(GRID_SIZE):
        grid.append([])
        for col in range(GRID_SIZE):
            color = random.choice(COLORS)
            grid[row].append(color)
    return grid

# Draw grid
def draw_grid(grid):
    for row in range(GRID_SIZE):
        for col in range(GRID_SIZE):
            color = grid[row][col]
            pygame.draw.rect(screen, color, (col * TILE_SIZE, row * TILE_SIZE, TILE_SIZE, TILE_SIZE))
            pygame.draw.rect(screen, (0, 0, 0), (col * TILE_SIZE, row * TILE_SIZE, TILE_SIZE, TILE_SIZE), 1)

# Swap tiles
def swap(grid, pos1, pos2):
    grid[pos1[0]][pos1[1]], grid[pos2[0]][pos2[1]] = grid[pos2[0]][pos2[1]], grid[pos1[0]][pos1[1]]

# Check for matches
def check_matches(grid):
    matched = set()
    for row in range(GRID_SIZE):
        for col in range(GRID_SIZE - 2):
            if grid[row][col] == grid[row][col + 1] == grid[row][col + 2]:
                matched.add((row, col))
                matched.add((row, col + 1))
                matched.add((row, col + 2))
    for col in range(GRID_SIZE):
        for row in range(GRID_SIZE - 2):
            if grid[row][col] == grid[row + 1][col] == grid[row + 2][col]:
                matched.add((row, col))
                matched.add((row + 1, col))
                matched.add((row + 2, col))
    return matched

# Remove matches and fill empty spaces
def remove_matches(grid, matches):
    for row, col in matches:
        grid[row][col] = None
    for col in range(GRID_SIZE):
        empty_spots = 0
        for row in range(GRID_SIZE - 1, -1, -1):
            if grid[row][col] is None:
                empty_spots += 1
            elif empty_spots > 0:
                grid[row + empty_spots][col] = grid[row][col]
                grid[row][col] = None
        for row in range(empty_spots):
            grid[row][col] = random.choice(COLORS)

def main():
    grid = create_grid()
    selected = None
    clock = pygame.time.Clock()
    running = True

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.MOUSEBUTTONDOWN:
                x, y = event.pos
                row, col = y // TILE_SIZE, x // TILE_SIZE
                if selected:
                    swap(grid, selected, (row, col))
                    matches = check_matches(grid)
                    if matches:
                        remove_matches(grid, matches)
                    else:
                        swap(grid, selected, (row, col))
                    selected = None
                else:
                    selected = (row, col)

        screen.fill((255, 255, 255))
        draw_grid(grid)
        pygame.display.flip()
        clock.tick(30)

    pygame.quit()

if _name_ == "_main_":
    main()
