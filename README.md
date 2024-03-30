<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Игра Змейка</title>
<style>
  html, body {
    height: 100%;
    margin: 0;
  }
  body {
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #f0f0f0;
  }
  #game-board {
    width: 400px;
    height: 400px;
    background-color: #000;
    position: relative;
    box-sizing: border-box;
  }
  .snake {
    width: 20px;
    height: 20px;
    background-color: #0f0;
    position: absolute;
  }
  .apple {
    width: 20px;
    height: 20px;
    background-color: #f00;
    position: absolute;
  }
</style>
</head>
<body>
<div id="game-board"></div>
<script>
  const boardSize = 400;
  const cellSize = 20;
  const board = document.getElementById('game-board');
  let snake = [{ x: 160, y: 200 }, { x: 140, y: 200 }, { x: 120, y: 200 }];
  let direction = 'right';
  let apple = { x: 300, y: 200 };
  let gameRun = true;

 function createDiv(className, x, y) {
  const element = document.createElement('div');
  element.style.left = `${x}px`;
  element.style.top = `${y}px`;
  element.className = className;
  return element;
}

  function draw() {
    board.innerHTML = '';
    snake.forEach(segment => board.appendChild(createDiv('snake', segment.x, segment.y)));
    board.appendChild(createDiv('apple', apple.x, apple.y));
  }

  function moveSnake() {
  const newHead = { ...snake[0] };
  if (direction === 'right') { newHead.x += cellSize; }
  if (direction === 'left') { newHead.x -= cellSize; }
  if (direction === 'down') { newHead.y += cellSize; }
  if (direction === 'up') { newHead.y -= cellSize; }
  snake.unshift(newHead);
  if (snake[0].x === apple.x && snake[0].y === apple.y) {
    placeApple();
  } else {
    snake.pop();
  }
}

  function placeApple() {
    apple.x = Math.floor(Math.random() * (boardSize/cellSize)) * cellSize;
    apple.y = Math.floor(Math.random() * (boardSize/cellSize)) * cellSize;
  }

function checkGameOver() {
  for (let i = 4; i < snake.length; i++) { 
    if (snake[i].x === snake[0].x && snake[i].y === snake[0].y) gameRun = false; 
  } 
  if (snake[0].x < 0 || snake[0].x >= boardSize || snake[0].y < 0 || snake[0].y >= boardSize) gameRun = false;
}

  document.addEventListener('keydown', changeDirection);


const touchStartCoords = { x: 0, y: 0 };
const touchEndCoords = { x: 0, y: 0 };

function setTouchStartCoords(event) {
    touchStartCoords.x = event.touches[0].clientX;
    touchStartCoords.y = event.touches[0].clientY;
}

function setTouchEndCoords(event) {
    touchEndCoords.x = event.changedTouches[0].clientX;
    touchEndCoords.y = event.changedTouches[0].clientY;
}

function handleSwipeGesture() {
    const deltaX = touchEndCoords.x - touchStartCoords.x;
    const deltaY = touchEndCoords.y - touchStartCoords.y;
    
    if (Math.abs(deltaX) > Math.abs(deltaY)) {
        // Свайп по горизонтали
        if (deltaX > 0) {
            // Свайп вправо
            direction = 'right';
        } else {
            // Свайп влево
            direction = 'left';
        }
    } else {
        // Свайп по вертикали
        if (deltaY > 0) {
            // Свайп вниз
            direction = 'down';
        } else {
            // Свайп вверх
            direction = 'up';
        }
    }
}

document.addEventListener('touchstart', setTouchStartCoords);
document.addEventListener('touchend', function(event) {
    setTouchEndCoords(event);
    handleSwipeGesture();
});


// Функция для изменения направления движения
function changeDirection(event) {
    const LEFT_KEY = 37;
    const RIGHT_KEY = 39;
    const UP_KEY = 38;
    const DOWN_KEY = 40;

    const keyPressed = event.keyCode;
    const goingUp = direction === 'up';
    const goingDown = direction === 'down';
    const goingRight = direction === 'right';
    const goingLeft = direction === 'left';

    if (keyPressed === LEFT_KEY && !goingRight) { direction = 'left'; }
    if (keyPressed === UP_KEY && !goingDown) { direction = 'up'; }
    if (keyPressed === RIGHT_KEY && !goingLeft) { direction = 'right'; }
    if (keyPressed === DOWN_KEY && !goingUp) { direction = 'down'; }
}

document.addEventListener('keydown', changeDirection);




  function gameLoop() {
    if(gameRun) {
      moveSnake();
      checkGameOver();
      draw();
      setTimeout(gameLoop, 200);
    }
  
  }

  placeApple();
  gameLoop();
</script>
</body>
</html>
