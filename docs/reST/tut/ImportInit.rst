import pygame
import random
import time
pygame.init()
# Defining colors
black = (0, 0, 0)
white = (255, 255, 255)
blue = (0, 0, 255)
green = (0, 255, 0)
red = (255, 0, 0)
yellow = (255, 255, 0)
# Setting up the display
display_width = 800
display_height = 600
game_display = pygame.display.set_mode((display_width, display_height))
pygame.display.set_caption('Eddie vs Aliens')
# Loading images
player_img = pygame.image.load('images/eddie.png')
player_img = pygame.transform.scale(player_img, (50, 100))
player_img.set_colorkey(white)
player_down = pygame.transform.rotate(player_img, 90)
player_down.set_colorkey(white)
player_up = pygame.transform.rotate(player_img, 270)
player_up.set_colorkey(white)
player_left = pygame.transform.flip(player_img, True, False)
player_left.set_colorkey(white)
player_right = player_img
player_right.set_colorkey(white)
alien_img = pygame.image.load('images/alien.png')
alien_img = pygame.transform.scale(alien_img, (50, 50))
alien_img.set_colorkey(white)
ammo_img = pygame.image.load('images/ammo.png')
weapon_img = pygame.image.load('images/weapon.png')
# Setting up game clock
clock = pygame.time.Clock()
# Defining global variables
score = 0
level = 1
player_x = display_width/2
player_y = display_height/2
player_speed = 5
player_direction = 'down'
player_health = 100
ammo_count = 10
weapon = 1
aliens = []
ammo = []
weapons = []
game_over = False
pause = False
# Defining functions
def player(x, y):
    if player_direction == 'up':
        game_display.blit(player_up, (x, y))
    elif player_direction == 'down':
        game_display.blit(player_down, (x, y))
    elif player_direction == 'left':
        game_display.blit(player_left, (x, y))
    elif player_direction == 'right':
        game_display.blit(player_right, (x, y))
def alien(x, y):
    game_display.blit(alien_img, (x, y))
def generate_alien():
    # Generating aliens based on the level
    for i in range(level):
        # Randomly placing the aliens on the screen
        x = random.randrange(0, display_width - 50)
        y = random.randrange(0, display_height - 50)
        aliens.append([x, y])
def generate_ammo():
    # Generating ammo in random locations on the screen
    for i in range(5):
        x = random.randrange(0, display_width - 50)
        y = random.randrange(0, display_height - 50)
        ammo.append([x, y])
def generate_weapon():
    # Generating weapons in random locations on the screen
    for i in range(2):
        x = random.randrange(0, display_width - 50)
        y = random.randrange(0, display_height - 50)
        weapons.append([x, y])
def display_message(text, color, size, x, y):
    # Displaying messages on the screen
    font = pygame.font.SysFont(None, size)
    message = font.render(text, True, color)
    game_display.blit(message, (x, y))
def display_score():
    # Displaying score on the screen
    display_message("Score: " + str(score), black, 30, 10, 10)
def display_level():
    # Displaying level on the screen
    display_message("Level " + str(level), black, 30, display_width - 150, 10)
def display_ammo():
    # Displaying ammo count on the screen
    display_message("Ammo: " + str(ammo_count), black, 30, 10, display_height - 40)
def display_health():
    # Displaying player's health on the screen
    display_message("Health: " + str(player_health), black, 30, display_width - 200, display_height - 40)
def game_over_screen():
    # Displaying game over screen
    game_display.fill(white)
    display_message("GAME OVER", red, 50, display_width/2 - 150, display_height/2 - 50)
    display_message("Final Score: " + str(score), black, 30, display_width/2 - 100, display_height/2)
    pygame.display.update()
    time.sleep(2)
def pause_screen():
    # Displaying pause screen
    game_display.fill(white)
    display_message("PAUSED", black, 50, display_width/2 - 100, display_height/2 - 50)
    pygame.display.update()
# Main game loop
while not game_over:
    # Handling events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_p and not pause:
                pause = True
            elif event.key == pygame.K_p and pause:
                pause = False
        if not pause:
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    player_x -= player_speed
                    player_direction = 'left'
                elif event.key == pygame.K_RIGHT:
                    player_x += player_speed
                    player_direction = 'right'
                elif event.key == pygame.K_UP:
                    player_y -= player_speed
                    player_direction = 'up'
                elif event.key == pygame.K_DOWN:
                    player_y += player_speed
                    player_direction = 'down'
                elif event.key == pygame.K_SPACE and ammo_count > 0 and weapon == 1:
                    ammo_count -= 1
                    for a in aliens:
                        if player_x + 50 >= a[0] and player_x - 50 <= a[0] and player_y + 50 >= a[1] and player_y - 50 <= a[1]:
                            aliens.remove(a)
                            score += 1
                elif event.key == pygame.K_SPACE and ammo_count > 0 and weapon == 2:
                    ammo_count -= 1
                    for a in aliens:
                        if player_x + 50 >= a[0] and player_x - 50 <= a[0] and player_y + 50 >= a[1] and player_y - 50 <= a[1]:
                            aliens.remove(a)
                            score += 1
                    for a in aliens:
                        if player_x + 100 >= a[0] and player_x - 100 <= a[0] and player_y + 100 >= a[1] and player_y - 100 <= a[1]:
                            aliens.remove(a)
                            score += 1
    # Checking for collisions between player and aliens
    for a in aliens:
        if player_x + 50 >= a[0] and player_x - 50 <= a[0] and player_y + 50 >= a[1] and player_y - 50 <= a[1]:
            player_health -= 10
            aliens.remove(a)
    if player_health <= 0:
        game_over = True
    # Updating display
    game_display.fill(white)
    # Generating aliens for the level
    if len(aliens) == 0:
        level += 1
        generate_alien()
        generate_ammo()
        if level == 2:
            generate_weapon()
            weapon = 2
    # Displaying and moving aliens
    for a in aliens:
        alien(a[0], a[1])
        # Moving alien towards player
        if player_x < a[0]:
            a[0] -= 2
        elif player_x > a[0]:
            a[0] += 2
        if player_y < a[1]:
            a[1] -= 2
        elif player_y > a[1]:
            a[1] += 2
    # Displaying player
    player(player_x, player_y)
    # Displaying and picking up ammo
    for a in ammo:
        game_display.blit(ammo_img, (a[0], a[1]))
        if player_x > a[0] and player_x < a[0] + 50 and player_y > a[1] and player_y < a[1] + 50:
            ammo.remove(a)
            ammo_count += 5
    # Displaying and picking up weapon
    for w in weapons:
        game_display.blit(weapon_img, (w[0], w[1]))
        if player_x > w[0] and player_x < w[0] + 50 and player_y > w[1] and player_y < w[1] + 50:
            weapons.remove(w)
            weapon = 2
    # Displaying game information
    display_score()
    display_level()
    display_ammo()
    display_health()
    # Updating display
    pygame.display.update()
    clock.tick(60)
if game_over:
    game_over_screen()
pygame.quit()
quit()
