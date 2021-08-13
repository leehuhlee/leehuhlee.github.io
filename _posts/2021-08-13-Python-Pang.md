---
layout: post
title: "[Python] Funny Poop"
date: 2021-08-12
excerpt: "Python Game"
tags: [Python, Game, Logic]
comments: false
---

<center>Reference by <a href="https://edu.goorm.io/learn/lecture/21001/%EB%AC%B4%EB%A3%8C-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%ED%99%9C%EC%9A%A9%ED%8E%B81-%EC%B6%94%EC%96%B5%EC%9D%98-%EC%98%A4%EB%9D%BD%EC%8B%A4-%EA%B2%8C%EC%9E%84-%EB%A7%8C%EB%93%A4%EA%B8%B0">무료 파이썬 활용편 1 - 추억의 게임 만들기</a></center>

# Demo

<iframe width="560" height="315" src="/assets/video/posts/python_pang/Python-Pang-Complete.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/python_pang/Python-Pang-GameOver.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/python_pang/Python-Pang-TimeOver.mp4" frameborder="0"> </iframe>

# Code

{% highlight py %}
import pygame
import os

################################################################################
pygame.init() # initialization

# Screen size setting
screen_width = 1280 # width size
screen_height = 980 # height size
screen = pygame.display.set_mode((screen_width, screen_height))

# Screen title setting 
pygame.display.set_caption("Python Pang") # Game Name

# FPS
clock = pygame.time.Clock()
################################################################################

# 1. Initial User Game(Background, Game Image, Position, Speed, Font, etc)

# Create background
current_path = os.path.dirname(__file__) # return current file path
image_path = os.path.join(current_path, "images") # return images folder path
background = pygame.image.load(os.path.join(image_path, "background.png"))

# Create stage
stage = pygame.image.load(os.path.join(image_path, "stage.png"))
stage_size = stage.get_rect().size
stage_height = stage_size[1] # To put character on the stage

# Create character
character = pygame.image.load(os.path.join(image_path, "character.png"))
character_size = character.get_rect().size # Get image size
character_width = character_size[0] # character width size
character_height = character_size[1] # character height size
character_x_pos = (screen_width - character_width) / 2  # locate screen width / 2
character_y_pos = screen_height - stage_height - character_height # locate screen bottom

# Character move position
character_to_x = 0

# Character move speed
character_speed = 5

# Create weapon
weapon = pygame.image.load(os.path.join(image_path, "weapon.png"))
weapon_size = weapon.get_rect().size
weapon_width = weapon_size[0]

# Weapon can be shoot several times at once
weapons = []

# Weapon speed
weapon_speed = 10

# Create ball
ball_images = [
    pygame.image.load(os.path.join(image_path, "balloon1.png")),
    pygame.image.load(os.path.join(image_path, "balloon2.png")),
    pygame.image.load(os.path.join(image_path, "balloon3.png")),
    pygame.image.load(os.path.join(image_path, "balloon4.png"))]

# ball speed
ball_speed_y = [-18, -15, -12, -9] # value of index 0, 1, 2, 3

# balls
balls = []

balls.append({
    "pos_x" : 50, # x pos of ball
    "pos_y" : 50, # y pos of ball
    "img_idx" : 0, # image index of ball
    "to_x" : 3, # move direction of x axis 
    "to_y" : -6, # move direction of y axis
    "init_spd_y" : ball_speed_y[0]}) # first y speed

# weapon and ball to remove
weapon_to_remove = -1
ball_to_remove = -1

# Define font
game_font = pygame.font.Font(None, 40)
total_time = 10
start_ticks = pygame.time.get_ticks() # define start time

# game over message
game_result = "Game Over"

################################################################################

# Event loop
running = True # Is the game running?
while running:
    dt = clock.tick(60) # set second frame count of game screen

    # 2. Event (Keyboard, Mouse, etc)
    for event in pygame.event.get(): # which event is occured?
        if event.type == pygame.QUIT: # is Window closing event occured?
            running = False # game is not running

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT: # Move charater to left
                character_to_x -= character_speed
            elif event.key == pygame.K_RIGHT: # Move character to right
                character_to_x += character_speed
            elif event.key == pygame.K_SPACE: # shoot
                weapon_x_pos = character_x_pos + (character_width - weapon_width) / 2
                weapon_y_pos = character_y_pos
                weapons.append([weapon_x_pos, weapon_y_pos])

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                character_to_x = 0

################################################################################

    # 3. Move Game Character
    character_x_pos += character_to_x

    if character_x_pos <= 0:
        character_x_pos = 0
    elif character_x_pos > screen_width - character_width:
        character_x_pos = screen_width - character_width

    # Set weapon location
    weapons = [[w[0], w[1] - weapon_speed] for w in weapons] # up weapon location
    
    # Remove weapon
    weapons = [[w[0], w[1]] for w in weapons if w[1] > 0]

    # Define ball location
    for ball_idx, ball_val in enumerate(balls):
        ball_pos_x = ball_val["pos_x"]
        ball_pos_y = ball_val["pos_y"]
        ball_img_idx = ball_val["img_idx"]

        ball_size = ball_images[ball_img_idx].get_rect().size
        ball_width = ball_size[0]
        ball_height = ball_size[1]

        # move ball position when ball touch horizental wall
        if ball_pos_x < 0 or ball_pos_x > screen_width - ball_width:
            ball_val["to_x"] = ball_val["to_x"] * -1

        # move ball position when ball touch vertical wall
        if ball_pos_y >= screen_height - stage_height - ball_height:
            ball_val["to_y"] = ball_val["init_spd_y"]
        else:
            ball_val["to_y"] += 0.5

        ball_val["pos_x"] += ball_val["to_x"]
        ball_val["pos_y"] += ball_val["to_y"]
        
################################################################################

    # 4. Collision

    # Character rect information
    character_rect = character.get_rect()
    character_rect.left = character_x_pos
    character_rect.top = character_y_pos

    for ball_idx, ball_val in enumerate(balls):
        ball_pos_x = ball_val["pos_x"]
        ball_pos_y = ball_val["pos_y"]
        ball_img_idx = ball_val["img_idx"]

        # Ball rect information
        ball_rect = ball_images[ball_img_idx].get_rect()
        ball_rect.left = ball_pos_x
        ball_rect.top = ball_pos_y

        # Collision 
        if character_rect.colliderect(ball_rect):
            running = False
            break

        # Collision ball and weapon
        for weapon_idx, weapon_val in enumerate(weapons):
            weapon_pos_x = weapon_val[0]
            weapon_pos_y = weapon_val[1]

            # weapon rect information
            weapon_rect = weapon.get_rect()
            weapon_rect.left = weapon_pos_x
            weapon_rect.top = weapon_pos_y

            # Check collision
            if weapon_rect.colliderect(ball_rect):
                weapon_to_remove = weapon_idx # value to remove weapon
                ball_to_remove = ball_idx # value to remove ball

                # the ball is not a smallest ball, then divide it to next ball
                if ball_img_idx < 3:
                    # current ball size
                    ball_width = ball_rect.size[0]
                    ball_height = ball_rect.size[1]

                    # divided ball information
                    small_ball_rect = ball_images[ball_img_idx + 1].get_rect()
                    small_ball_width = small_ball_rect.size[0]
                    small_ball_height = small_ball_rect.size[1]

                    # ball to bounce left
                    balls.append({
                        "pos_x" : ball_pos_x + (ball_width - small_ball_width) / 2, # x pos of ball
                        "pos_y" : ball_pos_y + (ball_height - small_ball_height) / 2, # y pos of ball
                        "img_idx" : ball_img_idx + 1, # image index of ball
                        "to_x" : -3, # move direction of x axis 
                        "to_y" : -6, # move direction of y axis
                        "init_spd_y" : ball_speed_y[ball_img_idx + 1]}) # first y speed

                    # ball to bounce right
                    balls.append({
                        "pos_x" : ball_pos_x + (ball_width - small_ball_width) / 2, # x pos of ball
                        "pos_y" : ball_pos_y + (ball_height - small_ball_height) / 2, # y pos of ball 
                        "img_idx" : ball_img_idx + 1, # image index of ball
                        "to_x" : 3, # move direction of x axis 
                        "to_y" : -6, # move direction of y axis
                        "init_spd_y" : ball_speed_y[ball_img_idx + 1]}) # first y speed
                break
        else:
            continue
        break

    # Remove weapon and ball which is collied
    if ball_to_remove > -1:
        del balls[ball_to_remove]
        ball_to_remove = -1

    if weapon_to_remove > -1:
        del weapons[weapon_to_remove]
        weapon_to_remove = -1

    if len(balls) == 0:
        game_result = "Mission Complete"
        running = False
         
################################################################################

    # 5. Print Screen

    screen.blit(background, (0, 0)) # Paint background 

    for weapon_x_pos, weapon_y_pos in weapons:
        screen.blit(weapon, (weapon_x_pos, weapon_y_pos))

    for idx, val in enumerate(balls):
        ball_pos_x = val["pos_x"]
        ball_pos_y = val["pos_y"]
        ball_img_idx = val["img_idx"]
        screen.blit(ball_images[ball_img_idx], (ball_pos_x, ball_pos_y))

    screen.blit(stage, (0, screen_height - stage_height))
    screen.blit(character, (character_x_pos, character_y_pos)) # Print character

    elapsed_time = (pygame.time.get_ticks() - start_ticks) / 1000 # ms -> s
    timer = game_font.render("Time : {}".format(int(total_time - elapsed_time)), True, (255, 255, 255))
    screen.blit(timer, (10, 10))

    # Time Over
    if total_time - elapsed_time <= 0:
        game_result = "Time Over"
        running = False

    pygame.display.update() # Repaint game screen

# Game over message
msg = game_font.render(game_result, True, (255, 255, 0))
msg_rect = msg.get_rect(center=(int(screen_width / 2), int(screen_height / 2)))
screen.blit(msg, msg_rect)
pygame.display.update() # Repaint game screen

# wait
pygame.time.delay(2000)

# pygame quit
pygame.quit()

################################################################################
{% endhighlight %}

[Download](https://github.com/leehuhlee/Python){: .btn}
