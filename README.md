from pygame import *


class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed, wight, height):
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


class Ball(GameSprite):
    speed_x = 4
    speed_y = 4

    def update(self):
        global finish
        global lost1
        global lost2
        ball.rect.y += self.speed_y
        ball.rect.x += self.speed_x

        if ball.rect.y < 0 or ball.rect.y > win_height - 50:
            self.speed_y *= -1
            reb.play()

        if sprite.collide_rect(ball, racket1) or sprite.collide_rect(ball, racket2):
            self.speed_x *= -1
            reb.play()

        if ball.rect.x < 0:
            lost1 += 1
            self.kill()
            ball.reset()

        if ball.rect.x > win_width - 50:
            lost2 += 1
            self.kill()
            ball.reset()


def update_status():
    text = font1.render("Пропущено: " + str(lost1), True, (180, 0, 0))
    window.blit(text, (10, 20))

    text_lose = font1.render("Пропущено: " + str(lost2), True, (180, 0, 0))
    window.blit(text_lose, (400, 20))


back = (200, 255, 255)
win_width = 600
win_height = 500

window = display.set_mode((win_width, win_height))
window.fill(back)
racket1 = Player('pong/racket.png', 30, 200, 4, 50, 150)
racket2 = Player('pong/racket.png', 520, 200, 4, 50, 150)
ball = Ball('pong/tenis_ball.png', 200, 200, 4, 50, 50)

font.init()
font1 = font.Font(None, 35)
lose1 = font1.render('PLAYER 1 WIN!', True, (180, 0, 0))
lose2 = font1.render('PLAYER 2 WIN!', True, (180, 0, 0))

lost1 = 0
lost2 = 0
max_lost = 3

mixer.init()
mixer.music.load('pong/how.ogg')
mixer.music.play()
reb = mixer.Sound('pong/zvuk.ogg')

game = True
finish = False
clock = time.Clock()
FPS = 60

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
    if not finish:
        window.fill(back)
        if lost1 >= max_lost:
            window.blit(lose2, (200, 200))
            finish = True
        if lost2 >= max_lost:
            window.blit(lose1, (200, 200))
            finish = True

        update_status()
        racket1.update_l()
        racket2.update_r()
        ball.update()

        ball.reset()
        racket1.reset()
        racket2.reset()
    else:
        finish = False
        lost1, lost2 = 0, 0
        time.delay(3000)
    display.update()
    clock.tick(FPS)

