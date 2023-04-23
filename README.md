from random import randint

from pygame import *


# класс-родитель для спрайтов
class GameSprite(sprite.Sprite):
    # конструктор класса
    # конструктор класса
    def __init__(self, player_image, player_x, player_y, player_speed, wight,
                 height):  # добавить еще два параметра при создании и задавать размер прямоугольгника для картинки самим
        super().__init__()

        # каждый спрайт должен хранить свойство image - изображение
        self.image = transform.scale(image.load(player_image), (wight, height))  # вместе 55,55 - параметры
        self.speed = player_speed

        # каждый спрайт должен хранить свойство rect - прямоугольник, в который он вписан
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))


# класс главного игрока
class Player(GameSprite):
    def update_r(self):
        keys = key.get_pressed()
        if keys[K_UP] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y < win_height - 80:
            self.rect.y += self.speed

    def update_l(self):
        keys = key.get_pressed()
        if keys[K_w] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_s] and self.rect.y < win_height - 80:
            self.rect.y += self.speed


# Игровая сцена:
back = (200, 255, 255)  # цвет фона (background)
win_width = 600
win_height = 500
window = display.set_mode((win_width, win_height))
window.fill(back)
music = "music.ogg"

# создания мяча и ракетки
racket1 = Player('racket.png', 30, 200, 4, 50, 150)  # при создании спрайта добавляется еще два параметра
racket2 = Player('racket.png', 520, 200, 4, 50, 150)
ball = GameSprite('tenis_ball.png', 200, 200, 4, 50, 50)

# флаги отвечающие за состояние игры
game = True
finish = False
clock = time.Clock()
FPS = 60

speed_x = 4
speed_y = 4

goal = 5
score1 = 0
score2 = 0

mixer.init()
racket_sound = mixer.Sound(music)


font.init()
font1 = font.Font(None, 80)
font2 = font.Font(None, 36)
win = font1.render('U WIN', True, (0, 0, 0))
lose = font1.render('U lose', True, (180, 0, 0))

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
    if not finish:
        ball.rect.y += speed_y
        ball.rect.x += speed_x

        if ball.rect.y < 0 or ball.rect.y > win_height - 50:
            speed_y *= -1
        if sprite.collide_rect(ball, racket1) or sprite.collide_rect(ball,racket2):
            speed_x *= -1
            racket_sound.play()

        if ball.rect.x < 0:
            score2 += 1
            ball.rect.x = 300
            ball.rect.y = 250

        if ball.rect.x > win_width:
            score1 += 1
            ball.rect.x = 300
            ball.rect.y = 250

        window.fill(back)
        racket1.update_l()
        racket2.update_r()

        racket1.reset()
        racket2.reset()
        ball.reset()

        if score2 >= goal or score1 >= goal:
            finish = True
    else:
        window.blit(win, (200, 200))
    display.update()
    clock.tick(FPS)
