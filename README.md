# Space-Invader-Game
import pygame
import random
import math
from pygame import mixer
pygame.init()
screen = pygame.display.set_mode((800,600)) # width and height of our gaming window.

pygame.display.set_caption("Space Invaders")
icon = pygame.image.load("D:/firework.png") # window icon.
pygame.display.set_icon(icon)

background = pygame.image.load("D:/space-background.jpg")

mixer.music.load("D:/backgroundMusic.mp3") # background music.
mixer.music.play(-1)

playerImg = pygame.image.load("D:/spring-swing-rocket (2).png")
playerX = 370 # the initial position on X axis
playerY = 480 # the initial position on Y axis
playerX_change = 0 # initial value of change position
playerY_change = 0 # initial value of change position
score_value = 0

enemyImg = []
enemyX = []
enemyY = []
enemyX_change = []
enemyY_change = []
nos_of_enemies = 6

for i in range(nos_of_enemies):
    enemyImg.append(pygame.image.load("D:/ufo.png"))
    enemyX.append(random.randint(0,735))
    enemyY.append(random.randint(50,200))
    enemyX_change.append(1)
    enemyY_change.append(40)

bulletImg = pygame.image.load("D:/bullet.png")
bulletX = 0
bulletY = 480
bulletX_change = 0
bulletY_change = 2
bullet_state = "ready"

font = pygame.font.Font('freesansbold.ttf', 70)
textX = 10
textY = 10

over_font = pygame.font.Font('freesansbold.ttf', 70)

def show_score(x, y):
    score = font.render("Score:" + str(score_value), True, (255,255,255))
    screen.blit(score, (x,y))

def game_over_text():
    over_text = over_font.render("GAME OVER",True, (255,255,255))
    screen.blit(over_text, (200, 250))

def player(x,y):
    screen.blit(playerImg, (x, y))

def enemy(x,y, i):
    screen.blit(enemyImg[i], (x, y))

def fire_bullet(x,y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bulletImg, (x + 16,y + 10))

def isCollision(enemyX, enemyY, bulletX, bulletY):
    distance = math.sqrt((math.pow(enemyX - bulletX, 2)) + ((math.pow(enemyY - bulletY , 2))))
    if distance < 27:
        return True
    else:
        return False

running = True
while running:
    screen.fill((0, 0, 0))
    #playerX -= 0.2
    screen.blit(background,(0,0))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False



        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                playerX_change = -5
            if event.key == pygame.K_RIGHT:
                playerX_change = 5
            if event.key == pygame.K_SPACE:
                if bullet_state is "ready":
                    bulletX = playerX
                    fire_bullet(bulletX, bulletY)
                    if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT or event.key == pygame.K_UP or event.key == pygame.K_DOWN:
                playerX_change = 0
                #playerY_change = 0


            if bulletY <= 0:
        bulletY = 480
        bullet_state = "ready"
        bullet_Sound = mixer.Sound("D:/LaserSound.wav")
        bullet_Sound.play() # we didnt use -1 in the bracket bcoz we dont want it to play continuously

    if bullet_state is "fire":
        fire_bullet(bulletX, bulletY)
        bulletY -= bulletY_change



     player(playerX, playerY) # checking for boiundaries of spaceship..
    playerX += playerX_change
    playerY += playerY_change
    if playerX <= 0:
        playerX = 0
    if playerX >= 730:
        playerX = 730
    if playerY >= 536:
        playerY = 536
    if playerY <= 0:
        playerY = 0


    for i in range(nos_of_enemies):
        #Game Over
        if enemyY[i] > 440:
            for j in range(nos_of_enemies):
                enemyY[j] = 2000
            game_over_text()
            break

        enemyX[i] += enemyX_change[i] #enemy movement
        #enemyY += enemyY_change
        if enemyX[i] <= 0:
             enemyX_change[i] = 1

             enemyY[i] += enemyY_change[i]
        if enemyX[i] >= 730:
             enemyX_change[i] =- 1
             enemyY[i] += enemyY_change[i]
            collision = isCollision(bulletX, bulletY, enemyX[i], enemyY[i])
        if collision:
            bulletY = 480
            bullet_state = "ready"
            score_value += 1
            #print(score)
            enemyX[i] = random.randint(0, 735)
            enemyY[i] = random.randint(50, 200)
        enemy(enemyX[i], enemyY[i], i)
    show_score(textX, textY)
    pygame.display.update()
