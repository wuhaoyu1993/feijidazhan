# feijidazhan
import random
import time

import pygame
from pygame.locals import *


# 初始化飞机的坐标，图像
class Initial(object):

    def __init__(self, screen, x, y, imagepath):
        self.X = x
        self.Y = y
        self.imagePath = imagepath
        self.image = pygame.image.load(self.imagePath)
        self.screen = screen

#创建飞机的基类
class Baseplane(Initial):

    #初始化飞机的坐标，图像
    def __init__(self,screen, x, y, imagepath):
        super(Baseplane, self).__init__(screen, x, y, imagepath)
        self.bulletList = []

    #初始化飞机的坐标，图像
    def setplant(self):
        self.screen.blit(self.image, (self.X, self.Y))
        tmp = []
        for x in self.bulletList:
            if x.judge():
                tmp.append(x)
            x.bulletMove()
            x.setbullet()
        for i in tmp:
                self.bulletList.remove(i)

#创建子弹基类
class BaseBullet(Initial):

    #设定子弹位置
    def setbullet(self):
        self.screen.blit(self.image,(self.X, self.Y))

class Playerplane(Baseplane):

    #初始化飞机的坐标，图像
    def __init__(self,screen):
        super(Playerplane, self).__init__(screen, 190, 550, './feiji/hero.gif')

    #定义飞机的上下左右移动
    def leftMove(self):
        self.X -= 20

    def rigthMove(self):
        self.X += 20

    def upMove(self):
        self.Y -= 20

    def downMove(self):
        self.Y += 20

    #飞机发射子弹
    def shoot(self):
        bullet = Bullet(self.X,self.Y,self.screen)
        self.bulletList.append(bullet)

class Enemyplane(Baseplane):

    # 初始化飞机的坐标，图像
    def __init__(self, screen):
        super(Enemyplane, self).__init__(screen, 0, 0, './feiji/enemy1.png')

    # 定义敌机的上下左右移动
    def leftMove(self):
        self.X -= 5

    def rigthMove(self):
        self.X += 5

    def move(self):
        if self.X == 0:
            self.fangxiang = 'right'
        elif self.X == 430:
            self.fangxiang = 'left'
        if self.fangxiang == 'right':
            self.rigthMove()
        elif self.fangxiang == 'left':
            self.leftMove()

    # 飞机发射子弹
    def shoot(self):
        a = random.randint(1,20)
        if a == 1:
            bullet = EnemyBullet(self.X, self.Y, self.screen)
            self.bulletList.append(bullet)

#创建子弹类
class Bullet(BaseBullet):

    #初始化子弹
    def __init__(self,x,y,screen):
        super(Bullet, self).__init__(screen, x+40, y-30, './feiji/bullet.png')

    #子弹自动移动
    def bulletMove(self):
        self.Y -= 30

    def __del__(self):
        print('删除子弹')

    def judge(self):
        return self.Y < 0

#创建子弹类
class EnemyBullet(BaseBullet):

    #初始化子弹
    def __init__(self,x,y,screen):
        super(EnemyBullet, self).__init__(screen, x+30, y+90, './feiji/bullet1.png')

    #子弹自动移动
    def bulletMove(self):
        self.Y += 10

    def __del__(self):
        print('删除敌机子弹')

    def judge(self):
        return self.Y > 700

def main():

    #创建游戏窗口
    screen = pygame.display.set_mode((480,700),0,32)

    #创建背景图片
    background = pygame.image.load('./feiji/background.png')

    #创建飞机对象
    playerplane = Playerplane(screen)

    #创建敌人飞机
    enemyplane = Enemyplane(screen)

    while True:

        #将背景图片放入指定位置
        screen.blit(background,(0,0))

        #将飞机放入指定位置
        playerplane.setplant()

        #将敌人飞机放入指定位置
        enemyplane.setplant()

        #控制敌机移动
        enemyplane.move()

        #敌机发射子弹
        enemyplane.shoot()

        for event in pygame.event.get():

            # 判断是否是点击了退出按钮
            if event.type == QUIT:
                print("exit")
                exit()
            # 判断是否是按下了键
            elif event.type == KEYDOWN:
                # 检测按键是否是a或者left
                if event.key == K_a or event.key == K_LEFT:
                    print('left')
                    playerplane.leftMove()
                # 检测按键是否是d或者right
                elif event.key == K_d or event.key == K_RIGHT:
                    print('right')
                    playerplane.rigthMove()
                # 检测按键是否是w或者up
                elif event.key == K_w or event.key == K_UP:
                    print('UP')
                    playerplane.upMove()
                # 检测按键是否是s或者down
                elif event.key == K_s or event.key == K_DOWN:
                    print('DOWN')
                    playerplane.downMove()
                # 检测按键是否是空格键
                elif event.key == K_SPACE:
                    print('space')
                    playerplane.shoot()

        #更新界面，优化计算
        pygame.display.update()
        time.sleep(1/60)


if __name__ == '__main__' :

    main()

