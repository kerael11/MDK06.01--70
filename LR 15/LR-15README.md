Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: разработка игры TileMap

1.	Создание игровой среды
Добавляем создаем и меняем сетку(grid)
 
[![Screenshot-4.png](https://i.postimg.cc/ZqWyPD46/Screenshot-4.png)](https://postimg.cc/nstzp2sL)
Рисунок 1
2.	Создаем папку Palettes и Sprites и переносим в папку со спрайтами наши картинки миров.
 [![Screenshot-5.png](https://i.postimg.cc/jjBnMQSm/Screenshot-5.png)](https://postimg.cc/5Hqt0zvS)
Рисунок 2


3.	Теперь мы можем нарисовать любой мир какой мы хотим использовать в нашей игре с помощью Tile Palette
 
[![Screenshot-6.png](https://i.postimg.cc/prHhHW8B/Screenshot-6.png)](https://postimg.cc/xJ6TGS6k)
Рисунок 3		

4.	Создаем Tilemap Collider для всех обьектов на игровой карте 
	так же нам нужно добавть RGBD 2d с типом тела Static потому что если мы этого не сделаем то наша карта просто упадет за пределы экрана.
