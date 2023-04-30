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

music = "music.ogg"
goal = 5
score1 = 0
score = 0


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


class Ball(GameSprite):
    speed_x = 4
    speed_y = 4

    def update(self):
        global finish
        ball.rect.y += self.speed_y
        ball.rect.x += self.speed_x

        if ball.rect.y < 0 or ball.rect.y > win_height - 50:
            self.speed_y *= -1

        if sprite.collide_rect(ball, racket1) or sprite.collide_rect(ball, racket2):
            self.speed_x *= -1
            racket_sound.play()

        if ball.rect.x < 0:
            window.blit(lose1, (200, 200))
            finish = True

        if ball.rect.x > win_width - 50:
            window.blit(lose2, (200, 200))
            finish = True

        if ball.rect.x < 0:
            score += 1
            ball.rect.x = 300
            ball.rect.y = 250

        if ball.rect.x > win_width:
            score1 += 1
            ball.rect.x = 300
            ball.rect.y = 250


mixer.init()
racket_sound = mixer.Sound(music)


# Игровая сцена:
back = (200, 255, 255)  # цвет фона (background)
win_width = 600
win_height = 500
window = display.set_mode((win_width, win_height))
window.fill(back)

# создания мяча и ракетки
racket1 = Player('racket.png', 30, 200, 4, 150, 150)  # при создании спрайта добавляется еще два параметра
racket2 = Player('racket.png', 520, 200, 4, 150, 150)
ball = Ball('tenis_ball.png', 200, 200, 4, 150, 50)

font.init()
font1 = font.Font(None, 35)
lose1 = font1.render('PLAYER 1 LOSE!', True, (180, 0, 0))
lose2 = font1.render('PLAYER 2 LOSE!', True, (180, 0, 0))

# флаги отвечающие за состояние игры
game = True
finish = False
clock = time.Clock()
FPS = 60

if score >= goal or score1 >= goal:
    finish = True


while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
    if not finish:
        window.fill(back)

        ball.update()
        racket1.update_l()
        racket2.update_r()

        racket1.reset()
        racket2.reset()
        ball.reset()

    display.update()
    clock.tick(FPS)

