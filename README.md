''' 젤리먹기게임 Rule
    : 이 게임은 여러 방향으로 움직이는 젤리를 마우스로 클릭하여 먹는 게임이다.
      젤리를 15개를 먹으면 레벨이 오른다. 레벨이 오를 때마다 젤리의 크기는 점점 작아진다.
      제한시간은 60초이고 레벨이 오를때마다 10초를 얻을 수 있다. 젤리를 최대한 많이 먹어보자
      사진 출처: background:pixabay.com, jelly:iconfinder.com
    '''

import pygame
import random
import time

pygame.init() 

WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLACK = (0, 0, 0)
large_font = pygame.font.SysFont(None, 72)
small_font = pygame.font.SysFont(None, 36)
SCREEN_WIDTH = 1000
SCREEN_HEIGHT = 800
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT)) #화면 크기 설정

done = False
clock = pygame.time.Clock() 

def runGame():
    global done
    score = 0 #쌓이는 점수의 변수
    start_time = int(time.time()) # 시작 시간을 입력하는 변수
    remain_time = 0 # 남은 시간을 담는 변수
    game_over = 0 # 게임이 끝날 플레그를 담는 변수
    level=0 # 레벨을 나타내는 코드
    
    jelly_image = pygame.image.load('C:/Users/jelly.png') #젤리이미지를 불러오는 코드
    jellyX=120
    jellyY=150
    jelly_image = pygame.transform.scale(jelly_image, (jellyX, jellyY)) #젤리 크기 조정 코드
    jellys = [] # Rect()로 생성한 것을 담는 리스트를 선언
    for i in range(5): #5개 생성
        jelly = pygame.Rect(jelly_image.get_rect()) #Rect() 함수를 이용하여 이미지를 넣어주는 코드입니다.
        jelly.left = random.randint(0, SCREEN_WIDTH) 
        jelly.top = random.randint(0, SCREEN_HEIGHT) #좌표를 설정해주는 코드 먼저, left라는 변수를 random 값을 넣어 스크린의 width(size[0]) 안에 속하는 랜덤 x 좌표를 설정해주고, top이라는 변수에는 젤리의 시작점을 설정해줍니다.
        dx = random.randint(-10, 10) #속도를 정해주는 dx, dy 변수를 선언해주는데 각 젤리의 이동 속도를 다르게 하기위해 random.randint() 함수를 통해 랜덤값을 넣어줍니다.
        dy = random.randint(-10, 10)
        jellys.append((jelly, dx, dy)) #리스트 안에 삽입하는 코드

    while not done: 
        clock.tick(30)
        
        background_image = pygame.image.load('C:/Users/background.jpg') #배경 사진 출력
        background_image = pygame.transform.scale(background_image, (1000, 800))
        screen.blit(background_image, (0, 0)) #배경화면 지정
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT: # [X] 종료키가 누르면, 게임 종료
                done=True
            elif event.type == pygame.MOUSEBUTTONDOWN and game_over == 0: #마우스를 클릭하였을 때 이벤트 값에 저장하는 코드
                print(event.pos[0], event.pos[1])
                for (jelly, dx, dy) in jellys:
                    if jelly.collidepoint(event.pos):
                        print(jelly)
                        jellys.remove((jelly, dx, dy)) #46부터 49줄까지 젤리를 리스트에서 삭제하는 코드 
                        jelly = pygame.Rect(jelly_image.get_rect())
                        jelly.left = random.randint(0, SCREEN_WIDTH)
                        jelly.top = random.randint(0, SCREEN_HEIGHT)
                        dx = random.randint(-10, 10)
                        dy = random.randint(-10, 10)
                        jellys.append((jelly, dx, dy)) #50부터 55줄까지 젤리를 새로 다시 추가하는 코드
                        score += 1 #score 1점 올라가는 코드
        
            
        if game_over == 0: # gameover 플래그 세우기
            for (jelly, dx, dy) in jellys:
                jelly.left += dx
                jelly.top += dy # 이동하게 해주는 코드
                
            remain_time = 60 - (int(time.time()) - start_time) # 시간 설정 60초

            if remain_time <= 0: 
                game_over = 1 # 0이되면 게임 종료
        

        for (jelly, dx, dy) in jellys:
            screen.blit(jelly_image, jelly) #젤리 이미지 출력 코드

        for (jelly, dx, dy) in jellys:
            if not jelly.colliderect(screen.get_rect()):
                jellys.remove((jelly, dx, dy))
                jelly = pygame.Rect(jelly_image.get_rect())
                jelly.left = random.randint(0, SCREEN_WIDTH)
                jelly.top = random.randint(0, SCREEN_HEIGHT)
                dx = random.randint(-10, 10)
                dy = random.randint(-10, 10)
                jellys.append((jelly, dx, dy)) #위치에서 벗어나면 삭제하고 새로 생성시키는 코드
        
        if score == 15: # 스코어가 15점이 되면
           score = 0
           jellyX-=10
           jellyY-=10
           jelly_image = pygame.transform.scale(jelly_image, (jellyX, jellyY)) #젤리의 크기를 줄여주는 코드
           level =level+1 #레벨이 1이 올라가는 코드
           remain_time += 10 #10초 추가 코드
           
         

          
           
        score_image = small_font.render('Point {}'.format(score), True, BLACK)
        screen.blit(score_image, (10, 10)) #점수 출력 코드
        
        level_image = small_font.render('level {}'.format(level), True, WHITE)
        screen.blit(level_image, (475, 10)) #점수 출력 코드
        
        remain_time_image = small_font.render('Time {}'.format(remain_time), True, BLACK)
        screen.blit(remain_time_image, (SCREEN_WIDTH - 10 - remain_time_image.get_width(), 10)) #남은 시간을 화면에 출력하는 코드

        if game_over == 1: #gameover 스택이 1 쌓이면
            game_over_image = large_font.render('GameOver', True, RED)
            screen.blit(game_over_image, (SCREEN_WIDTH // 2 - game_over_image.get_width() // 2, SCREEN_HEIGHT // 2 - game_over_image.get_height() // 2)) #게임 오버 글씨 출력 코드

        pygame.display.update()

runGame()
pygame.quit()
