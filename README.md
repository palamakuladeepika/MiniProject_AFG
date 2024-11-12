# MiniProject_AI for Games

## MAN Vs ZOMBIE

### Aim : 

To Create a Shooter game where the player controls a Man to shoot plant zombies, avoid collisions, and track score and time.


### Algotrithm:

```
1.Initialize Pygame and set up screen dimensions, colors, and constants.
2. Load spaceship and alien images and scale them to appropriate sizes.
3. Define the Bullet class for handling bullet movement and properties.
4. Define the zombie class for handling alien movement and properties.
5. Initialize main game loop:
    -> Process user input for player movement (left, right) and firing bullets (spacebar).
    -> Move bullets upwards and remove them if they go off-screen.
    -> Move aliens downwards and remove those that go off-screen.
    -> Spawn new aliens at random intervals.
6. Check for collisions between bullets and aliens, updating the score and removing
    the alien and bullet upon collision.
7. Check for collisions between the player's shooter and zombies, ending the game
    if a collision occurs.
8. Draw all game elements to the screen.
9. Display the game-over screen with score and options to restart or quit.
10. Restart the game or quit based on user input from the game-over screen.

```



### Program:

```python

import pygame
import random
import time

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
PLAYER_IMG_SIZE = 50  # Size of the player spaceship image
ALIEN_IMG_SIZE = 40   # Size of the alien image
BULLET_SPEED = 10     # Speed of bullets (moving right)
PLAYER_SPEED = 5

# Set up the display
win = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Shooter")

# Load images (add your spaceship and alien images here)
player_img = pygame.image.load('manshooter.png')  # Add your spaceship image path here
player_img = pygame.transform.scale(player_img, (PLAYER_IMG_SIZE, PLAYER_IMG_SIZE))

alien_img = pygame.image.load('alienplant.png')  # Add your alien image path here
alien_img = pygame.transform.scale(alien_img, (ALIEN_IMG_SIZE, ALIEN_IMG_SIZE))

# Bullet class
class Bullet:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x, y, 10, 5)  # Set a wider bullet for horizontal shooting

    def move(self):
        self.rect.x += BULLET_SPEED  # Move the bullet to the right

# Alien class
class Alien:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x, y, ALIEN_IMG_SIZE, ALIEN_IMG_SIZE)

    def move(self):
        self.rect.x -= 3  # Move the alien to the left

# Main Game Loop
def main():
    clock = pygame.time.Clock()
    player_pos = [50, HEIGHT // 2 - PLAYER_IMG_SIZE // 2]  # Position player in top-left corner
    bullets, aliens = [], []
    score = 0
    run_game = True

    start_time = time.time()  # Record start time

    # Define the font for displaying text
    font = pygame.font.SysFont(None, 36)

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return

        keys = pygame.key.get_pressed()
        if keys[pygame.K_UP] and player_pos[1] > 0:
            player_pos[1] -= PLAYER_SPEED
        if keys[pygame.K_DOWN] and player_pos[1] < HEIGHT - PLAYER_IMG_SIZE:
            player_pos[1] += PLAYER_SPEED
        if keys[pygame.K_SPACE]:  # If the space bar is pressed
            bullets.append(Bullet(player_pos[0] + PLAYER_IMG_SIZE // 2, player_pos[1] + PLAYER_IMG_SIZE // 2))

        # Move bullets
        for bullet in bullets:
            bullet.move()  # Call the move method for each bullet
            if bullet.rect.x > WIDTH:  # Remove bullets that move off-screen (right side)
                bullets.remove(bullet)

        # Move aliens
        new_aliens = []
        for alien in aliens:
            alien.move()
            if alien.rect.x > 0:  # Keep aliens on the screen until they go off the left side
                new_aliens.append(alien)

        # Spawn new aliens randomly on the right side of the screen
        if random.random() < 0.02:  # Adjust spawn rate
            new_aliens.append(Alien(WIDTH, random.randint(0, HEIGHT - ALIEN_IMG_SIZE)))

        # Check collisions
        for bullet in bullets:
            for alien in new_aliens:
                if bullet.rect.colliderect(alien.rect):
                    bullets.remove(bullet)
                    new_aliens.remove(alien)
                    score += 10  # Increase score for hitting an alien
                    break

        # Check spaceship-alien collisions
        for alien in new_aliens:
            if alien.rect.colliderect(pygame.Rect(player_pos[0], player_pos[1], PLAYER_IMG_SIZE, PLAYER_IMG_SIZE)):
                run_game = False  # Set flag to stop the game

        aliens = new_aliens

        # Draw everything
        win.fill(BLACK)
        win.blit(player_img, (player_pos[0], player_pos[1]))

        for alien in aliens:
            win.blit(alien_img, (alien.rect.x, alien.rect.y))

        for bullet in bullets:
            pygame.draw.rect(win, WHITE, bullet.rect)

        # Display score
        score_text = font.render(f"Score: {score}", True, WHITE)
        win.blit(score_text, (10, 10))

        # Display elapsed time
        elapsed_time = time.time() - start_time  # Calculate elapsed time
        time_text = font.render(f"Time: {elapsed_time:.2f} seconds", True, WHITE)
        win.blit(time_text, (10, 40))

        pygame.display.update()
        clock.tick(30)

        # If the game is over, show the game-over screen
        if not run_game:
            game_over_screen(score)

# Function to display the game over screen
def game_over_screen(score):
    font = pygame.font.SysFont(None, 48)
    while True:
        win.fill(BLACK)
        game_over_text = font.render("Game Over", True, WHITE)
        score_text = font.render(f"Score: {score}", True, WHITE)
        restart_text = font.render("Press R to Restart or Q to Quit", True, WHITE)

        win.blit(game_over_text, (WIDTH // 2 - game_over_text.get_width() // 2, HEIGHT // 2 - 60))
        win.blit(score_text, (WIDTH // 2 - score_text.get_width() // 2, HEIGHT // 2))
        win.blit(restart_text, (WIDTH // 2 - restart_text.get_width() // 2, HEIGHT // 2 + 40))

        pygame.display.update()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:  # Restart the game
                    main()  # Call main again to restart
                if event.key == pygame.K_q:  # Quit the game
                    pygame.quit()
                    return

if __name__ == "__main__":
    main()


```

### Output:

![Screenshot 2024-11-12 114749](https://github.com/user-attachments/assets/d91c39c7-12d2-49b3-b1ba-a17c9a6c3d38)

![Screenshot 2024-11-12 114807](https://github.com/user-attachments/assets/0f1b898f-56e3-4d04-8a1e-e22c0cb426ca)

![Screenshot 2024-11-12 114828](https://github.com/user-attachments/assets/e6fea26d-d6a9-4e43-9e06-50c6a4f7d885)


### Result:

Thus a working Man vs Zombie game with player control, zombie destruction, and game-over functionality is developed Successfully.



