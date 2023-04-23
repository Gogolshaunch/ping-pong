from random import randint
from pygame import *


class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed, wight,
                 height):
        super().__init__()

        self.image = transform.scale(image.load(player_image), (wight, height))  # вместе 55,55 - параметры
        self.speed = player_speed

        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))


class Player(GameSprite):
    def update_l(self):
        keys = key.get_pressed()
        if keys[K_w] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_s] and self.rect.y < win_width - 80:
            self.rect.y += self.speed

    def update_r(self):
        keys = key.get_pressed()
        if keys[K_UP] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y < win_width - 80:
            self.rect.y += self.speed


back = (200, 255, 255)  # цвет фона (background)
win_width = 600
win_height = 500
window = display.set_mode((win_width, win_height))
window.fill(back)

racket1 = Player('pong/racket.png', 30, 200, 4, 50, 150)  # при созданни спрайта добавляется еще два параметра
racket2 = Player('pong/racket.png', 520, 200, 4, 50, 150)
ball = GameSprite('pong/tenis_ball.png', 200, 200, 4, 50, 50)

font.init()
font1 = font.Font(None, 80)
font2 = font.Font(None, 36)
lose1 = font1.render('Игрок 1 проиграл', True, (255, 255, 255))
lose2 = font1.render('Игрок 2 проиграл', True, (180, 0, 0))

speed_x = 5
speed_y = 5
# флаги отвечающие за состояние игры
game = True
finish = False
clock = time.Clock()
FPS = 60

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
    if not finish:
        ball.rect.y += speed_y
        ball.rect.x += speed_x

        if ball.rect.y < 0 or ball.rect.y > win_height - 50:
            speed_y *= -1

        if sprite.collide_rect(ball, racket1) or sprite.collide_rect(ball, racket2):
            speed_x *= -1

        if ball.rect.x < 0:
            finish = True
            window.blit(lose1, (200, 200))

        if ball.rect.x > win_width - 50:
            window.blit(lose2, (200, 200))
            finish = True

        window.fill(back)
        racket1.update_l()
        racket2.update_r()

        racket1.reset()
        racket2.reset()
        ball.reset()
    display.update()
    clock.tick(FPS)

    time.delay(50)
