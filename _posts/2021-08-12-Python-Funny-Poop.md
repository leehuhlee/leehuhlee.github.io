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

<iframe width="560" height="315" src="/assets/video/posts/python_funny_poop/Python-Funny-Poop.mp4" frameborder="0"> </iframe>

# Install

<figure class="half">
  <a href="/assets/img/posts/python_funny_poop/0.jpg"><img src="/assets/img/posts/python_funny_poop/0.jpg"></a>
  <a href="/assets/img/posts/python_funny_poop/1.jpg"><img src="/assets/img/posts/python_funny_poop/1.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

- install `pygame`
- [File]-[Preferences], search `linting` and uncheck


# Code

{% highlight py %}
import pygame
import random

################################################################################
pygame.init() # initialization

# Screen size setting
screen_width = 480 # width size
screen_height = 640 # height size
screen = pygame.display.set_mode((screen_width, screen_height))

# Screen title setting
pygame.display.set_caption("Python Game") # Game Name

# FPS
clock = pygame.time.Clock()
################################################################################

# 1. Initial User Game(Background, Game Image, Position, Speed, Font, etc)

# Load background image
background = pygame.image.load("C:/Users/leehu/Desktop/Python/pygame_basic/river.png")

# Load character(sprite)
character = pygame.image.load("C:/Users/leehu/Desktop/Python/pygame_basic/rubber-duck.png")
character_size = character.get_rect().size # Get image size
character_width = character_size[0] # character width size
character_height = character_size[1] # character height size
character_x_pos = (screen_width - character_width) / 2  # locate screen width / 2
character_y_pos = screen_height - character_height # locate screen bottom

# position to move
to_x = 0

# speed
character_speed = 0.6
enemy_speed = 10

# Enemy character
enemy = pygame.image.load("C:/Users/leehu/Desktop/Python/pygame_basic/poop.png")
enemy_size = enemy.get_rect().size # Get image size
enemy_width = enemy_size[0] # character width size
enemy_height = enemy_size[1] # character height size
enemy_x_pos = random.randrange(0, screen_width - enemy_width)  # locate screen width / 2
enemy_y_pos = 0 # locate screen bottom

# total time
total_time = 10

# start time
start_ticks = pygame.time.get_ticks() # get start tick

################################################################################

# Event loop
running = True # Is the game running?
while running:
    dt = clock.tick(60) # set second frame count of game screen

    # 2. Event (Keyboard, Mouse, etc)
    for event in pygame.event.get(): # which event is occured?
        if event.type == pygame.QUIT: # is Window closing event occured?
            running = False # game is not running

        if event.type == pygame.KEYDOWN: # Is key pressed?
            if  event.key == pygame.K_LEFT: # Move character to left
                to_x -= character_speed # to_x = to_x -5
            elif  event.key == pygame.K_RIGHT: # Move character to right
                to_x += character_speed
        
        if event.type == pygame.KEYUP: # is key not pressed?
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                to_x = 0

################################################################################

    # 3. Move Game Character
    character_x_pos += to_x * dt
    enemy_y_pos += enemy_speed

    # Max x position
    if character_x_pos < 0:
        character_x_pos = 0
    elif character_x_pos > screen_width - character_width:
        character_x_pos = screen_width - character_width
    
    if enemy_y_pos > screen_height:
        enemy_x_pos = random.randint(0, screen_width - enemy_width)  # locate screen width / 2
        enemy_y_pos = 0 # locate screen bottom

################################################################################

    # 4. Collision

    # Update rect information for Collision
    character_rect = character.get_rect()
    character_rect.left = character_x_pos
    character_rect.top = character_y_pos

    enemy_rect = enemy.get_rect()
    enemy_rect.left = enemy_x_pos
    enemy_rect.top = enemy_y_pos

    # Check Collision
    if character_rect.colliderect(enemy_rect):
        print("Collision!")
        running = False
    
    
################################################################################

    # 5. Print Screen

    screen.blit(background, (0, 0)) # Paint background 
    screen.blit(character, (character_x_pos, character_y_pos)) # Print character
    screen.blit(enemy, (enemy_x_pos, enemy_y_pos)) # Print enemy

    pygame.display.update() # Repaint game screen

# wait
pygame.timer.delay(2000) # wait 2 second

# pygame quit
pygame.quit()

################################################################################
{% endhighlight %}

[Download](https://github.com/leehuhlee/Python){: .btn}
