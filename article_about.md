[[https://skillbox.ru/media/code/sozdayem-pervuyu-igru-na-python-i-pygame/]]

[Документация pygame](https://www.pygame.org/)

Учимся программировать через разработку игр. Сегодня напишем знакомую всем «Змейку» — вспомним правила игры и реализуем их на Python.
![[Pygame/img/2.png]]
Pygame — популярная библиотека для создания игр под различные устройства на Windows, macOS, Linux или Android. Она помогает разработчику не только описать геймплей, но и работать с клавиатурой, мышью, акселерометром, звуком и видео.
Первая версия Pygame была представлена Питом Шиннерсом в октябре 2000 года. За 22 года вокруг библиотеки сложилось большое комьюнити, а о работе с ней написано несколько десятков книг. Последняя стабильная версия на июль 2022 года — 2.1.2.
Давайте разберёмся в том, как устроена Pygame, и напишем свою первую игру — классическую «Змейку».

## Устанавливаем Pygame и разбираемся

Pygame — не самостоятельная библиотека. На самом деле это обёртка для [библиотеки SDL](https://www.libsdl.org/), Simple DirectMedia Layer. Именно SDL позволяет задействовать любые внешние устройства — например, мышь или клавиатуру. А Pygame делает работу с ними удобной для Python-разработчика.

Установить Pygame просто. Для этого воспользуемся терминалом или командной строкой и командой pip:
```bash
pip install pygame
```
Если во время установки возникают ошибки, то можно воспользоваться [официальной документацией](https://www.pygame.org/wiki/GettingStarted). В ней описаны особенности установки на различные системы, а также пути решения распространённых проблем.

## Инициализация и модули

Библиотека Pygame состоит из конструкций на языке Python и включает в себя несколько модулей. Модули позволяют получить доступ к определённому устройству и содержат методы для работы с ним. Например, модуль display позволяет работать с экраном, а `joystick` — считывать движения с джойстика.

После того как вы импортировали Pygame, необходимо инициировать библиотеку с помощью команды `pygame.int()`. Это поможет нам использовать любые методы любых функций, включённых в библиотеку модулей. Без инициализации код может потерять кросс-платформенность и не запускаться в другой системе.

## Displays и Surfaces: рисуем игровое поле

Помимо модулей, Pygame включает несколько классов Python, которые работают с концепциями, не зависящими от аппаратного обеспечения. Одна из таких концепций — Surface. Surface, можно сказать, определяет прямоугольную область, на которой можно рисовать. Если переносить на практику, то этот класс позволяет создать игровое поле. Он широко используется при работе с Pygame, и мы тоже поработаем с ним при создании «Змейки».

В Pygame вся информация выводится на игровое поле, которому в коде соответствует класс display. Игровое поле может быть полноэкранным или занимать часть экрана. Display создаётся с помощью функции .set_mode(), которая возвращает Surface, представляющий видимую часть окна. Именно эту область вы будете передавать в функции рисования, такие как pygame.draw.circle(), а содержимое этого Surface будет выводиться на дисплей при вызове pygame.display.flip(). Звучит сложно, но на практике будет проще. Оба класса мы будем использовать при создании «Змейки».

## Изображения и прямоугольники

Работать с изображениями в Pygame можно двумя способами: создавать их с нуля на экране или использовать изображения с диска. И тот и другой тип можно перезаписывать, загружать и сохранять в различных форматах — например, в PNG и JPG.

Изображения — не самостоятельные компоненты: они загружаются в объекты Surface, которые можно изменять и выводить на дисплеи различными способами.

Выше мы писали, что объекты Surface представлены прямоугольниками, подобно многим другим объектам в Pygame, в том числе изображениям и окнам. Прямоугольники используются настолько активно, что существует даже специальный класс Rect, предназначенный для работы только с ними. Объекты Rect используются для различных задач: создания фигур игрока и врагов, управления взаимодействиями между ними и так далее.

## Продумываем и описываем правила игры

Перед тем как начать писать код, необходимо подумать о том, какие правила мы должн учиывать при создании игры. Давайте составим для "Змейки" базовые правила, которые вляют на геймплей:
- цель игры - набрть как можно больше очков за счёт увеличения длинны змейки;
- при поглощении специального объекта на игровом экране длина змейки увеличивается на один блок;
- игрок начинает движение с левой стороны экрана;
- игрок может двигаться влево, вправо или вниз;
- игрок не может двигаться за пределы экрана, при столкновении с границами игра заканчивается;
- игра заканчивается, когда змейка врезается сама в себя;
- еще игра заканчивается, когда пользователь закрывает окно.

# Создаём игру с нуля

Несмотря на то что «Змейка» — простая игра, нам предстоит написать довольно много кода. Мы разбили этапы создания игры на последовательные шаги, каждый из которых реализует отдельную часть геймплея.

## Шаг 0
## Запускаем Python

Писать код на Python лучше всего в специальном редакторе. Есть несколько вариантов:
- Воспользоваться специализированными IDE: [IntelliJ IDEA](https://www.jetbrains.com/ru-ru/idea/) или [Visual Studio Code](https://code.visualstudio.com/). Мы рекомендуем именно этот способ — например, весь код для этой статьи мы писали в Visual Studio Code.
- Использовать терминал на macOS или Linux или воспользоваться командной строкой в Windows. Для этого предварительно потребуется установить Python в систему. Мы подробно писали об этом [в отдельном материале](https://skillbox.ru/media/code/kak_zapustit_python_rukovodstvo_dlya_novichka/).

После установки и запуска Python загружаем библиотеку Pygame:

```bash
pip install pygame
```

Теперь у нас всё готово к работе над игрой. Для дальнейшего написания кода необходимо создать один пустой Python-файл.

## Шаг 1
## Создаем игровое поле

Чтобы создать окно с игрой с помощью Pygame, необходимо использовать функцию `display.set_mode()` и передать в неё желаемый размер окна в пикселях. Также необходимо использовать методы `init()` и `quit()` для инициализации библиотеки в начале кода и её деинициализации в конце кода.

Метод `update()` используется для обновления содержимого экрана. Существует ещё метод `flip()`, который работает аналогично update(). Разница в том, что метод `update()` обновляет только внесённые изменения, а метод `flip()` перерисовывает экран целиком. Но если в метод `update()` не передавать никакие параметры, то также обновится весь экран.
```python
import pygame
 
pygame.init()
dis=pygame.display.set_mode((500,400))  #Задаём размер игрового поля.
 
pygame.display.update()
 
pygame.quit()
quit()
```

Если сейчас запустить этот код, то экран игры сразу же закроется. Это связано с тем, что код сразу переходит к следующей строчке pygame.quit(), отключающей библиотеку и наше игровое поле. Чтобы избежать этого, необходимо воспользоваться циклом while — он не позволит игровому экрану закрыться:
```python
import pygame
 
pygame.init()
 
dis=pygame.display.set_mode((500,400))
pygame.display.update()
pygame.display.set_caption('Змейка от Skillbox') #Добавляем название игры.
 
game_over=False #Создаём переменную, которая поможет нам контролировать статус игры — завершена она или нет. Изначально присваиваем значение False, то есть игра продолжается.
 
while not game_over:
   for event in pygame.event.get():
       print(event)  #Выводить в терминал все произошедшие события.
 
pygame.quit()
quit()
```

Кроме этого, мы добавили в код ещё две сущности: название игры и функцию для отслеживания игровых событий. Чтобы у окна с игрой появилось название, мы используем `pygame.display.set_caption('')` (название пишем в кавычках). А функция `event.get()` возвращает в терминал все события, которые происходят с игрой.

Запустим код и посмотрим, что получилось:
![[Pygame/img/3.jpg]]

Теперь игровое окно не закрывается само по себе. Однако и закрыть его мы тоже не сможем — если нажать на кнопку «Выход», ничего не произойдёт. Исправляем это с помощью кода: добавляем событие QUIT, закрывающее окно.
```python
import pygame
 
pygame.init()
 
dis=pygame.display.set_mode((500, 400))
pygame.display.update()
pygame.display.set_caption('Змейка от Skillbox')
 
game_over=False
while not game_over:
   for event in pygame.event.get():
       if event.type==pygame.QUIT:
           game_over=True
 
pygame.quit()
quit()
```

Теперь кнопка выхода работает как надо. Если нажать на неё, то окно с игрой закроется.

## Шаг 2
## Cоздаем змейку

Для начала инициализируем переменные, которые задают цвет. Мы будем использовать их, чтобы присвоить цвет экрану, самой змейке и еде. В Pygame используется стандартная RGB-схема, то есть любой цвет представляет собой комбинацию красного, зелёного и синего цветов, интенсивность которых мы можем менять.

Наша змейка — прямоугольник, поэтому мы воспользуемся функцией создания прямоугольников draw.rect(). Она позволяет задать размер и цвет прямоугольника.

```python
import pygame
 
pygame.init()

white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)

dis=pygame.display.set_mode((800, 600))
pygame.display.update()
pygame.display.set_caption('Змейка от Skillbox')
 
game_over=False
clock = pygame.time.Clock()

while not game_over:
   for event in pygame.event.get():
       if event.type==pygame.QUIT:
           game_over=True
    dis.fill(white)
    pygame.draw.rect(10, 10)
    pygame.display.update()
    
 
pygame.quit()
quit()
```

Запустим код и посмотрим на результат.

Всё получилось. Ближе к центру экрана появился синий квадрат, который и будет нашей змейкой.

## Шаг 3
## Описываем движения змейки

Управлять перемещением змейки можно с помощью специального класса Pygame KEYDOWN. Класс позволяет использовать четыре стандартных события, получая их с клавиавтуры: K_UP, K_DOWN, K_LEFT и K_RIGHT — они соответствуют движениям змейки вверх, вниз, влево и вправо. Срабатывание любого события из класса KEYDOWN приводит к изменению положения змейки. Зададим шаг этого движения в 10 пикселей.

Кроме того, мы должны создать две переменные для хранения значений координат первой клетки нашей змейки по осям x и y. Назовём их x1_change и y1_change.

```python
import pygame
 
pygame.init()
 
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
 
dis = pygame.display.set_mode((800, 600))
pygame.display.set_caption('Змейка от Skillbox')
 
game_over = False
x1 = 300 #Указываем начальное значение положения змейки по оси х.
y1 = 300 #Указываем начальное значение положения змейки по оси y.
x1_change = 0 #Создаём переменную, которой в цикле while будут
присваиваться значения изменения положения змейки по оси х.
y1_change = 0 #создаём переменную, которой в цикле while будут
присваиваться значения изменения положения змейки по оси y.
clock = pygame.time.Clock()
 
while not game_over:
   for event in pygame.event.get():
       if event.type == pygame.QUIT:
           game_over = True
       if event.type == pygame.KEYDOWN: #Добавляем считывание направления
движений с клавиатуры.
           if event.key == pygame.K_LEFT:
               x1_change = -10 #Указываем шаг изменения положения змейки
в 10 пикселей.
               y1_change = 0
           elif event.key == pygame.K_RIGHT:
               x1_change = 10
               y1_change = 0
           elif event.key == pygame.K_UP:
               y1_change = -10
               x1_change = 0
           elif event.key == pygame.K_DOWN:
               y1_change = 10
               x1_change = 0
   x1 += x1_change #Записываем новое значение положения змейки по оси х.
   y1 += y1_change #Записываем новое значение положения змейки по оси y.
   dis.fill(white)
   
   pygame.draw.rect(dis, black, [x1, y1, 10, 10])
   pygame.display.update()
   clock.tick(30)
 
pygame.quit()
quit()
```

Теперь змейка двигается по игровому полю:
![[4.gif]]

## Шаг 4
## Учитываем препятсвия - границы игрового поля

Если змейка попадает на границу экрана, то игрок терпит поражение, а игра заканчивается. Чтобы закодить это правило, можно воспользоваться оператором if, который определяет координаты x и y для змейки и анализирует, выходят ли они за границы игрового поля. Добавим необходимый код.

```python
import pygame
import time
 
pygame.init()
 
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
 
dis_width = 800 #Зададим размер игрового поля через две переменные.
dis_height = 600
dis = pygame.display.set_mode((dis_width, dis_width))
pygame.display.set_caption('Змейка от Skillbox')
 
game_over = False
x1 = dis_width/2 #Стартовое положение змейки по осям рассчитывается через переменные, указывающие размер игрового экрана.
y1 = dis_height/2
snake_block=10 #Укажем в переменной стандартную величину сдвига положения змейки при нажатии на клавиши.
x1_change = 0
y1_change = 0
 
clock = pygame.time.Clock()
snake_speed=15 #Ограничим скорость движения змейки.
font_style = pygame.font.SysFont(None, 50)
 
def message(msg,color): #Создадим функцию, которая будет показывать нам сообщения на игровом экране.
	mesg = font_style.render(msg, True, color)
	dis.blit(mesg, [dis_width/2, dis_height/2])
 
while not game_over:
	for event in pygame.event.get():
	    if event.type == pygame.QUIT:
	        game_over = True
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
	            x1_change = -snake_block
                y1_change = 0
            elif event.key == pygame.K_RIGHT:
                x1_change = snake_block
                y1_change = 0
            elif event.key == pygame.K_UP:
                y1_change = -snake_block
                x1_change = 0
            elif event.key == pygame.K_DOWN:
                y1_change = snake_block
                x1_change = 0
   
	if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
	game_over = True #Явно укажем, что если координаты змейки выходят за рамки игрового поля, то игра должна закончиться.
    x1 += x1_change
    y1 += y1_change
    dis.fill(white)
   
    pygame.draw.rect(dis, black, [x1, y1, snake_block, snake_block])
    pygame.display.update()
    clock.tick(snake_speed)
 
message("Вы проиграли :(",red) #Сообщение, которое появляется при проигрыше. В нашем случае — при выходе змейки за пределы игрового поля.
 
pygame.display.update()
time.sleep(2)
pygame.quit()
 
quit()
```

Теперь, если змейка достигнет края экрана, игра закончится, а на дисплее появится сообщение о проигрыше:
![[5.gif]]

## Шаг 5
Добавляем еду для змейки

Теперь добавим «еду». Используем библиотеку random, чтобы она появлялась в случайном месте на игровом поле. Когда наша змейка будет проходить через еду, то её длина будет увеличиваться. Это мы добавим на следующем шаге. Кроме того, дадим возможность игроку выйти из игры или начать игру заново после проигрыша.

```python
import pygame
import time
import random
 
pygame.init()
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
blue = (0, 0, 255)
dis_width = 800
dis_height = 600
dis = pygame.display.set_mode((dis_width, dis_height))
pygame.display.set_caption('Змейка от Skillbox')
clock = pygame.time.Clock()
snake_block = 10
snake_speed = 15
font_style = pygame.font.SysFont(None, 30)
 
def message(msg, color):
   mesg = font_style.render(msg, True, color)
   dis.blit(mesg, [dis_width/10, dis_height/3])
 
def gameLoop(): #Описываем всю игровую логику в одной функции.
   game_over = False
   game_close = False
   x1 = dis_width / 2
   y1 = dis_height / 2
   x1_change = 0
   y1_change = 0
   foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0 
#Создаём переменную, которая будет указывать расположение еды по оси х.
   foody = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0 
#Создаём переменную, которая будет указывать расположение еды по оси y.
   while not game_over:
       while game_close == True:
           dis.fill(white)
           message("Вы проиграли! Нажмите Q для выхода 
или C для повторной игры", red)
           pygame.display.update()
           for event in pygame.event.get():
               if event.type == pygame.KEYDOWN:
                   if event.key == pygame.K_q:
                       game_over = True
                       game_close = False
                   if event.key == pygame.K_c:
                       gameLoop()
       for event in pygame.event.get():
           if event.type == pygame.QUIT:
               game_over = True
           if event.type == pygame.KEYDOWN:
               if event.key == pygame.K_LEFT:
                   x1_change = -snake_block
                   y1_change = 0
               elif event.key == pygame.K_RIGHT:
                   x1_change = snake_block
                   y1_change = 0
               elif event.key == pygame.K_UP:
                   y1_change = -snake_block
                   x1_change = 0
               elif event.key == pygame.K_DOWN:
                   y1_change = snake_block
                   x1_change = 0
       if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
           game_close = True
       x1 += x1_change
       y1 += y1_change
       dis.fill(white)
       pygame.draw.rect(dis, blue, [foodx, foody, snake_block, snake_block])
       pygame.draw.rect(dis, black, [x1, y1, snake_block, snake_block])
       pygame.display.update()
   
   pygame.quit()
   quit()
 
gameLoop()
```

Теперь при запуске игры кроме самой змейки будет показана еда. В нашем случае — в виде чёрного квадрата.
![[Pygame/img/6.jpg]]

А если выполнить условие для завершения игры, то появится сообщение с предложением выйти из игры или начать её заново:
![[7.gif]]

## Шаг 6
## Увеличиваем длину змейки

Дополним наш код, чтобы длина змейки увеличивалась при поглощении еды. Для этого нам понадобится список, в котором будет храниться текущая длина змейки. Учтём ещё важный момент из правил: при столкновении головы змейки с её телом игра завершается.
```python
import pygame
import time
import random
 
pygame.init()
 
white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
 
dis_width = 800
dis_height = 600
dis = pygame.display.set_mode((dis_width, dis_height))
pygame.display.set_caption('Змейка от Skillbox')
clock = pygame.time.Clock()
snake_block = 10
snake_speed = 15
font_style = pygame.font.SysFont("bahnschrift", 25) #Укажем название шрифта и его размер для системных сообщений, например, при завершении игры.
score_font = pygame.font.SysFont("comicsansms", 35) #Укажем шрифт и его размер для отображения счёта. Это мы реализуем очень скоро.
 
def our_snake(snake_block, snake_list):
	for x in snake_list:
	    pygame.draw.rect(dis, black, [x[0], x[1], snake_block, snake_block])
 
def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width / 6, dis_height / 3])
 
 
def gameLoop():
    game_over = False
    game_close = False
    x1 = dis_width / 2
    y1 = dis_height / 2
    x1_change = 0
    y1_change = 0
    snake_List = [] #Создаём список, в котором будем хранить показатель текущей длины змейки.
    Length_of_snake = 1 
    foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
    foody = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0
    while not game_over:
        while game_close == True:
            dis.fill(blue)
            message("Вы проиграли! Нажмите Q для выхода 
или C для повторной игры", red)
		pygame.display.update()
	        for event in pygame.event.get():
	            if event.type == pygame.KEYDOWN:
	                if event.key == pygame.K_q:
	                    game_over = True
                        game_close = False
                    if event.key == pygame.K_c:
                        gameLoop()
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            if event.type == pygame.KEYDOWN:
	            if event.key == pygame.K_LEFT:
	                x1_change = -snake_block
                    y1_change = 0
                elif event.key == pygame.K_RIGHT:
                    x1_change = snake_block
                    y1_change = 0
                elif event.key == pygame.K_UP:
                    y1_change = -snake_block
                    x1_change = 0
                elif event.key == pygame.K_DOWN:
                    y1_change = snake_block
                    x1_change = 0
        if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
            game_close = True
        x1 += x1_change
        y1 += y1_change
        dis.fill(blue)
        pygame.draw.rect(dis, green, [foodx, foody, snake_block, snake_block])
        snake_Head = [] #Создаём список, в котором будет храниться показатель длины змейки при движениях.
        snake_Head.append(x1) #Добавляем значения в список при изменении по оси х.
        snake_Head.append(y1) #Добавляем значения в список при изменении по оси y.
        snake_List.append(snake_Head)
        if len(snake_List) > Length_of_snake:
            del snake_List[0] #Удаляем первый элемент в списке длины змейки, чтобы она не увеличивалась сама по себе при движениях.
        for x in snake_List[:-1]:
            if x == snake_Head:
                game_close = True
        our_snake(snake_block, snake_List)
        pygame.display.update()
        if x1 == foodx and y1 == foody: #Указываем, что в случаях, если координаты головы змейки совпадают с координатами еды, еда появляется в новом месте, а длина змейки увеличивается на одну клетку.
            foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
            foody = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0
            Length_of_snake += 1
        clock.tick(snake_speed)
    pygame.quit()
    quit()
 
gameLoop()
```

Дополнительно мы изменили цвет игрового поля и змейки, увеличив их контрастность.
![[8.gif]]

## Шаг 6
## Увеличиваем длину змейки

Добавим отображение счёта текущей игры. Для этого создадим функцию Your_score. Она будет отображать длину змейки, вычитая из неё 1 (ведь 1 — это начальный размер змейки, и это не является достижением игрока).

```python
def your_score(score):
    value = score_font.render("Ваш счёт: " + str(score), True, yellow)
    dis.blit(value, [0, 0])
```

И отдельно пропишем правило определения длины змейки, вычитая из текущей длины змейки единицу.

```python
your_score(Length_of_snake - 1)
```

Теперь на игровом поле будет отображаться текущий счёт:
![[9.gif]]

Можно считать, что наша работа над «Змейкой» закончена. Мы полностью реализовали геймплей, который запланировали на старте работы.

## Итоговый код

```python
import pygame
import time
import random
pygame.init()
white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dis_width = 800
dis_height = 600
dis = pygame.display.set_mode((dis_width, dis_height))
pygame.display.set_caption('Змейка от Skillbox')
clock = pygame.time.Clock()
snake_block = 10
snake_speed = 15
font_style = pygame.font.SysFont("bahnschrift", 25)
score_font = pygame.font.SysFont("comicsansms", 35)
 
def Your_score(score):
	value = score_font.render("Ваш счёт: " + str(score), True, yellow)
    dis.blit(value, [0, 0])
 
def our_snake(snake_block, snake_list):
    for x in snake_list:
        pygame.draw.rect(dis, black, [x[0], x[1], snake_block, snake_block])
 
def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width / 6, dis_height / 3])
 
def gameLoop():
    game_over = False
    game_close = False
    x1 = dis_width / 2
    y1 = dis_height / 2
    x1_change = 0
    y1_change = 0
    snake_List = []
    Length_of_snake = 1
    foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
    foody = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0
    while not game_over:
	    while game_close == True:
	        dis.fill(blue)
            message("Вы проиграли! Нажмите Q для выхода или C для повторной игры", red)
            Your_score(Length_of_snake - 1)
            pygame.display.update()
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_q:
                    game_over = True
                        game_close = False
                    if event.key == pygame.K_c:
                        gameLoop()
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
	            game_over = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                   x1_change = -snake_block
                   y1_change = 0
                elif event.key == pygame.K_RIGHT:
                    x1_change = snake_block
                    y1_change = 0
                elif event.key == pygame.K_UP:
                    y1_change = -snake_block
                    x1_change = 0
                elif event.key == pygame.K_DOWN:
	                y1_change = snake_block
	                x1_change = 0
		if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
			game_close = True
        x1 += x1_change
        y1 += y1_change
        dis.fill(blue)
        pygame.draw.rect(dis, green, [foodx, foody, snake_block, snake_block])
        snake_Head = []
        snake_Head.append(x1)
        snake_Head.append(y1)
        snake_List.append(snake_Head)
        if len(snake_List) > Length_of_snake:
            del snake_List[0]
        for x in snake_List[:-1]:
            if x == snake_Head:
                game_close = True
        our_snake(snake_block, snake_List)
        Your_score(Length_of_snake - 1)
        pygame.display.update()
        if x1 == foodx and y1 == foody:
            foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
            foody = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0
            Length_of_snake += 1
        clock.tick(snake_speed)
    pygame.quit()
    quit()
gameLoop()
```