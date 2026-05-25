# Создаем игру «Змейка» с нуля: HTML, CSS и JavaScript

Привет! 

Сегодня мы отправимся в захватывающее приключение, где создадим свою собственную игру «Змейка». В процессе вы научитесь решать задачи, разбивая их на более мелкие и понятные шаги. К концу этого туториала вы узнаете много нового и почувствуете уверенность, чтобы самостоятельно исследовать мир веб-разработки.

Если вы новичок в программировании, не переживайте — мы пройдем этот путь вместе. Именно так я когда-то и начинала свой путь в IT! 

Ладно, хватит лирических отступлений — вы готовы начать?
<img width="800" height="677" alt="image" src="https://github.com/user-attachments/assets/24428818-db8d-46b2-a381-34ff8b30c6ae" />


---

## Начало работы

Начнём с создания файла `snake.html`, который будет содержать весь наш код. Поскольку это HTML-файл, первое, что нам нужно, — это объявление `<!DOCTYPE html>`. 

Откройте ваш редактор кода, создайте файл `snake.html` и введите следующее:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Моя игра Змейка</title>
  </head>
  <body>
    Добро пожаловать в Snake!
  </body>
</html>
```
Отлично, теперь откройте `snake.html` в своем браузере. Вы должны увидеть надпись «Добро пожаловать в Snake!». Хорошее начало!

### Создание холста (Canvas)

Для создания графики в нашей игре мы будем использовать HTML-элемент `<canvas>`. Именно он позволяет рисовать 2D-объекты с помощью JavaScript.

Замените приветственное сообщение в `snake.html` на этот код:

```html
<canvas id="gameCanvas" width="300" height="300"></canvas>
```
### Добавляем фон и рамку

Чтобы сделать наш холст видимым, я напишу немного кода на JavaScript. Для этого нам нужно добавить тег `<script>` сразу после холста.

Важно: весь JS-код должен находиться после тега `<canvas>`, иначе скрипт просто не найдет холст при загрузке страницы.

Добавьте этот код внутрь `<body>`:

```html
<script>
  // Получаем элемент canvas по его ID
  const gameCanvas = document.getElementById("gameCanvas");
  // Получаем 2D контекст для рисования
  const ctx = gameCanvas.getContext("2d");

  // Задаем белый цвет фона и черную рамку
  ctx.fillStyle = "white";
  ctx.strokeStyle = "black";

  // Рисуем прямоугольник размером с холст
  ctx.fillRect(0, 0, gameCanvas.width, gameCanvas.height);
  ctx.strokeRect(0, 0, gameCanvas.width, gameCanvas.height);
</script>
```
Обновите страницу. Ура, белый квадрат с черной рамкой! Теперь у нас есть игровое поле. Переходим к самому интересному!

### Рисуем нашу змейку

Для корректной работы игры нам необходимо знать местоположение змейки на холсте. Я решила представить змейку в виде массива координат.

Чтобы создать горизонтальную змейку в центре холста, мы пропишем следующие координаты:

```javascript
let snake = [
  {x: 150, y: 150},
  {x: 140, y: 150},
  {x: 130, y: 150},
  {x: 120, y: 150},
  {x: 110, y: 150},
];
```
Заметьте, что координата `y` для всех сегментов равна **150**. Координата `x` каждого сегмента на **10 пикселей меньше** предыдущего (сдвиг влево). Первая пара `{x: 150, y: 150}` — это голова нашей змейки.

Теперь давайте напишем функцию, которая будет отрисовывать один квадратик (сегмент) змейки:

```javascript
function drawSnakePart(snakePart) {
  ctx.fillStyle = 'lightgreen';
  ctx.strokeStyle = 'darkgreen';

  ctx.fillRect(snakePart.x, snakePart.y, 10, 10);
  ctx.strokeRect(snakePart.x, snakePart.y, 10, 10);
}
```
А теперь функцию, которая пройдет по всему массиву и нарисует змейку целиком:

```javascript
function drawSnake() {
  snake.forEach(drawSnakePart);
}

// Вызовем функцию, чтобы увидеть результат
drawSnake();
```
Обновите страницу. Вы должны увидеть зеленую змейку прямо посередине экрана. Потрясающе! 😊

### Учим змейку ползать

Далее я хочу наделить змейку способностью двигаться. Как это сделать?

Мы будем использовать понятия горизонтальной (`dx`) и вертикальной (`dy`) скорости. Добавим переменные скорости:

```javascript
let dx = 10;
let dy = 0;
```
Теперь создадим функцию `advanceSnake()`. Мы будем вычислять новую позицию головы змейки, добавлять её в начало массива с помощью `unshift()` и удалять хвостик с помощью `pop()`. Это создаст иллюзию движения!

```javascript
function advanceSnake() {
  const head = { x: snake[0].x + dx, y: snake[0].y + dy };
  
  // Добавляем новую голову
  snake.unshift(head);
  // Удаляем последний элемент (хвост)
  snake.pop();
}
```
### Автоматизируем движение (Игровой цикл)

Чтобы змейка ползла сама, а не только когда мы вызываем функцию вручную, нам нужен таймер. Но перед этим давайте проведем небольшой рефакторинг и вынесем очистку холста в отдельную функцию:

```javascript
function clearCanvas() {
  ctx.fillStyle = "white";
  ctx.strokeStyle = "black";
  ctx.fillRect(0, 0, gameCanvas.width, gameCanvas.height);
  ctx.strokeRect(0, 0, gameCanvas.width, gameCanvas.height);
}
```
Теперь создадим главную функцию `main()`, которая будет вызывать саму себя через равные промежутки времени (например, каждые 100 миллисекунд), создавая бесконечный цикл анимации:

```javascript
function main() {
  setTimeout(function onTick() {
    clearCanvas();
    advanceSnake();
    drawSnake();
    
    // Вызываем main снова
    main();
  }, 100);
}
// Запускаем игру
main();
```
Теперь наша змейка ползет вправо! Правда, она уползет за край экрана и исчезнет в неизвестности, но с этим мы разберемся чуть позже. Терпение, юный падаван!

### Изменение направления

Наша следующая задача — научить змейку слушаться клавиш-стрелочек. Я написала функцию `changeDirection`, которая будет менять скорости `dx` и `dy` в зависимости от нажатой кнопки.

```javascript
function changeDirection(event) {
  const LEFT_KEY = 37;
  const RIGHT_KEY = 39;
  const UP_KEY = 38;
  const DOWN_KEY = 40;

  const keyPressed = event.keyCode;
  
  // Текущее направление движения
  const goingUp = dy === -10;
  const goingDown = dy === 10;
  const goingRight = dx === 10;
  const goingLeft = dx === -10;

  if (keyPressed === LEFT_KEY && !goingRight) {
    dx = -10;
    dy = 0;
  }
  if (keyPressed === UP_KEY && !goingDown) {
    dx = 0;
    dy = -10;
  }
  if (keyPressed === RIGHT_KEY && !goingLeft) {
    dx = 10;
    dy = 0;
  }
  if (keyPressed === DOWN_KEY && !goingUp) {
    dx = 0;
    dy = 10;
  }
}
```
Обратите внимание: я добавила проверки (например, `!goingRight`), чтобы змейка не могла развернуться на 180 градусов и врезаться сама в себя.

Подключим обработчик событий к документу:

```javascript
document.addEventListener("keydown", changeDirection);
Ура, теперь змейкой можно управлять! Вы просто молодцы!

Кормим змейку и считаем очки
Для генерации еды я напишу функцию, которая будет выдавать случайные координаты, кратные 10 (так как размер нашей змейки — 10px).

JavaScript
let foodX;
let foodY;

function randomTen(min, max) {
  return Math.round((Math.random() * (max - min) + min) / 10) * 10;
}

function createFood() {
  foodX = randomTen(0, gameCanvas.width - 10);
  foodY = randomTen(0, gameCanvas.height - 10);

  // Проверяем, не появилась ли еда прямо на змейке
  snake.forEach(function isFoodOnSnake(part) {
    const foodIsOnSnake = part.x === foodX && part.y === foodY;
    if (foodIsOnSnake) createFood();
  });
}

function drawFood() {
  ctx.fillStyle = 'red';
  ctx.strokeStyle = 'darkred';
  ctx.fillRect(foodX, foodY, 10, 10);
  ctx.strokeRect(foodX, foodY, 10, 10);
}
```
Обновим функцию `main()`, чтобы рисовать еду:

```javascript
function main() {
  // ... (таймер)
    clearCanvas();
    drawFood();
    advanceSnake();
    drawSnake();
  // ...
}
```
#### Рост и счетчик очков

Добавим переменную счета:

```javascript
let score = 0;
```
И добавим в HTML элемент для вывода счета перед холстом:

```html
<div id="score">0</div>
```
Теперь обновим логику `advanceSnake()`. Если змейка съела еду, мы просто не удаляем её хвост (оставляем `pop()`) и генерируем новую еду:

```javascript
function advanceSnake() {
  const head = {x: snake[0].x + dx, y: snake[0].y + dy};
  snake.unshift(head);

  const didEatFood = snake[0].x === foodX && snake[0].y === foodY;
  
  if (didEatFood) {
    score += 10;
    document.getElementById('score').innerHTML = score;
    createFood();
  } else {
    snake.pop(); // Удаляем хвост только если не поели
  }
}
```
Уф, мы проделали огромную работу!

### Конец игры

Остался последний элемент — завершение игры при столкновении со стеной или собственным хвостом.

```javascript
function didGameEnd() {
  // Проверяем столкновение с собственным телом
  for (let i = 4; i < snake.length; i++) {
    const didCollide = snake[i].x === snake[0].x && snake[i].y === snake[0].y;
    if (didCollide) return true;
  }

  // Проверяем столкновение со стенами
  const hitLeftWall = snake[0].x < 0;
  const hitRightWall = snake[0].x > gameCanvas.width - 10;
  const hitTopWall = snake[0].y < 0;
  const hitBottomWall = snake[0].y > gameCanvas.height - 10;

  return hitLeftWall || hitRightWall || hitTopWall || hitBottomWall;
}
```
Обновляем нашу главную функцию, чтобы она останавливалась, если игра окончена:

```javascript
function main() {
  if (didGameEnd()) return; // Выход из цикла

  setTimeout(function onTick() {
    // ...
  }, 100);
}
```
### Исправляем коварные баги 🐛

Если вы поиграете в эту игру, то можете заметить, что иногда она заканчивается неожиданно, даже если вы не врезались в стену. Это отличный пример того, как баги незаметно проникают в код.

**В чем проблема?** Если змейка ползет влево, и вы очень быстро нажмете «вниз», а затем «вправо» (до того, как пройдет 100 мс игрового тика), скорости изменятся на противоположные, и змейка врежется сама в себя!

**Решение:** Нам нужно блокировать изменение направления до тех пор, пока змейка не сделает свой шаг на холсте. Я добавлю флаг `changingDirection`.

```javascript
let changingDirection = false;

function changeDirection(event) {
  // Если направление уже меняется в этом тике - выходим
  if (changingDirection) return; 
  changingDirection = true;

  // ... остальной код функции ...
}

function main() {
  if (didGameEnd()) return;

  setTimeout(function onTick() {
    changingDirection = false; // Сбрасываем флаг каждый тик
    
    clearCanvas();
    drawFood();
    advanceSnake();
    drawSnake();
    main();
  }, 100);
}
```
### Финальные штрихи

Чтобы холст и счет выглядели красиво по центру экрана, добавьте этот CSS-код в `<head>` вашего документа:

```html
<style>
  body {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    margin: 0;
    font-family: Arial, sans-serif;
  }
  #gameCanvas {
    box-shadow: 0 0 10px rgba(0,0,0,0.2);
  }
  #score {
    font-size: 30px;
    font-weight: bold;
    margin-bottom: 20px;
  }
</style>
```
### Поздравляю вы написали свою первую змейку!!!
<img width="320" height="336" alt="image" src="https://github.com/user-attachments/assets/a113fed4-ae6e-4db9-b031-441874b009c1" />

