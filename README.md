# Jurassic-jumper
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Indominus Rex Runner - Enhanced Edition</title>
  <style>
    body {
      margin: 0;
      background: linear-gradient(135deg, #66ccff, #ff66cc);
      overflow: hidden;
      font-family: 'Arial', sans-serif;
    }
    #game {
      position: relative;
      width: 800px;
      height: 200px;
      margin: 40px auto;
      background: #85d4f2;
      border-radius: 12px;
      overflow: hidden;
      box-shadow: 0 0 20px #333;
    }
    #ground {
      position: absolute;
      bottom: 0;
      width: 100%;
      height: 30px;
      background: linear-gradient(to right, #228822 0%, #66bb66 100%);
      box-shadow: inset 0 4px 8px #115511;
    }
    #rex {
      position: absolute;
      bottom: 30px;
      left: 50px;
      width: 80px;
      height: 80px;
      background: url('https://i.imgur.com/IndominusRexSprite.png') no-repeat center/contain;
      transition: bottom 0.2s;
    }
    #obstacle {
      position: absolute;
      bottom: 30px;
      right: -40px;
      width: 40px;
      height: 40px;
      background: #bb4444;
      border-radius: 8px;
      animation: obstacleMove 2s linear infinite;
      box-shadow: 0 0 10px #880000;
    }
    @keyframes obstacleMove {
      0% { right: -40px; }
      100% { right: 840px; }
    }
    #score {
      position: absolute;
      top: 10px;
      right: 20px;
      color: #fff;
      font-weight: bold;
      font-size: 24px;
      text-shadow: 2px 2px 5px #333;
    }
    #message {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      color: #fff;
      font-size: 32px;
      font-weight: bold;
      text-shadow: 3px 3px 7px #000;
      display: none;
    }
  </style>
</head>
<body>
  <div id="game">
    <div id="rex"></div>
    <div id="obstacle"></div>
    <div id="ground"></div>
    <div id="score">Score: 0</div>
    <div id="message">Game Over! Press Space to Restart</div>
  </div>

  <script>
    const rex = document.getElementById('rex');
    const obstacle = document.getElementById('obstacle');
    const scoreDisplay = document.getElementById('score');
    const message = document.getElementById('message');
    let jumping = false;
    let score = 0;
    let gameOver = false;

    function jump() {
      if (jumping || gameOver) return;
      jumping = true;
      let jumpHeight = 0;
      const jumpInterval = setInterval(() => {
        if (jumpHeight >= 100) {
          clearInterval(jumpInterval);
          let fallInterval = setInterval(() => {
            if (jumpHeight <= 0) {
              clearInterval(fallInterval);
              jumping = false;
              rex.style.bottom = '30px';
            } else {
              jumpHeight -= 10;
              rex.style.bottom = 30 + jumpHeight + 'px';
            }
          }, 20);
        } else {
          jumpHeight += 10;
          rex.style.bottom = 30 + jumpHeight + 'px';
        }
      }, 20);
    }

    function detectCollision() {
      const rexRect = rex.getBoundingClientRect();
      const obsRect = obstacle.getBoundingClientRect();
      if (
        rexRect.right > obsRect.left &&
        rexRect.left < obsRect.right &&
        rexRect.bottom > obsRect.top
      ) {
        return true;
      }
      return false;
    }

    function resetGame() {
      score = 0;
      gameOver = false;
      obstacle.style.animationPlayState = 'running';
      message.style.display = 'none';
      scoreDisplay.textContent = 'Score: 0';
    }

    document.addEventListener('keydown', e => {
      if (e.code === 'Space') {
        if (gameOver) {
          resetGame();
        } else {
          jump();
        }
      }
    });

    function gameLoop() {
      if (!gameOver) {
        if (detectCollision()) {
          gameOver = true;
          obstacle.style.animationPlayState = 'paused';
          message.style.display = 'block';
        } else {
          score++;
          scoreDisplay.textContent = 'Score: ' + score;
        }
      }
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>
