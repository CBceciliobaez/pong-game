# pong-game
Juego de PONG en HTML

<!DOCTYPE html>
<html>
<head>
  <title>Pong</title>
  <style>
    canvas {
      background-color: black;
    }
  </style>
</head>
<body>
  <canvas id="pong" width="1000" height="500"></canvas>
  <button id="resetButton" onclick="resetGame()">Reiniciar juego</button>
  <script>
    // Obtener el lienzo y el contexto del canvas
    const canvas = document.getElementById('pong');
    const context = canvas.getContext('2d');

    // Configuración del juego
    const tableColor = 'blue';  // Color de la mesa
    const paddleColor = 'white';
    const ballColor = 'white';
    const netColor = 'white';
    const scoreColor = 'white';
    const tableWidth = canvas.width;
    const tableHeight = canvas.height;
    const paddleWidth = 10;
    const paddleHeight = 60;
    const ballRadius = 5;
    const paddleSpeed = 5;
    let player1Score = 0;
    let player2Score = 0;

    // Posiciones y velocidades iniciales
    let paddle1Y = tableHeight / 2 - paddleHeight / 2;
    let paddle2Y = tableHeight / 2 - paddleHeight / 2;
    let ballX = tableWidth / 2;
    let ballY = tableHeight / 2;
    let ballSpeedX = -3;
    let ballSpeedY = 3;

    // Variables de control de movimiento de las raquetas
    let upKeyPressed = false;
    let downKeyPressed = false;

    // Dibuja la mesa
    function drawTable() {
      context.fillStyle = tableColor;
      context.fillRect(0, 0, tableWidth, tableHeight);
    }

    // Dibuja las raquetas
    function drawPaddles() {
      // Raqueta izquierda
      context.fillStyle = paddleColor;
      context.fillRect(0, paddle1Y, paddleWidth, paddleHeight);

      // Raqueta derecha
      context.fillStyle = paddleColor;
      context.fillRect(tableWidth - paddleWidth, paddle2Y, paddleWidth, paddleHeight);
    }

    // Mueve las raquetas
    function movePaddles() {
      if (upKeyPressed && paddle1Y > 0) {
        paddle1Y -= paddleSpeed;
      }
      if (downKeyPressed && paddle1Y < tableHeight - paddleHeight) {
        paddle1Y += paddleSpeed;
      }

      // Movimiento de la raqueta derecha controlada por la computadora
      // Calcula la posición de la raqueta para seguir la pelota
      let paddle2YCenter = paddle2Y + paddleHeight / 2;
      if (paddle2YCenter < ballY - 35) {
        paddle2Y += paddleSpeed;
      }
      if (paddle2YCenter > ballY + 35) {
        paddle2Y -= paddleSpeed;
      }
    }

    // Dibuja la pelota
    function drawBall() {
      context.fillStyle = ballColor;
      context.beginPath();
      context.arc(ballX, ballY, ballRadius, 0, Math.PI * 2, false);
      context.closePath();
      context.fill();
    }

    // Mueve la pelota
    function moveBall() {
      ballX += ballSpeedX;
      ballY += ballSpeedY;

      // Verifica las colisiones con las paredes
      if (ballY + ballRadius >= tableHeight || ballY - ballRadius <= 0) {
        ballSpeedY = -ballSpeedY;
      }

      // Verifica la colisión con la raqueta izquierda
      if (
        ballX - ballRadius <= paddleWidth &&
        ballY + ballRadius >= paddle1Y &&
        ballY - ballRadius <= paddle1Y + paddleHeight
      ) {
        ballSpeedX = -ballSpeedX;
      }

      // Verifica la colisión con la raqueta derecha
      if (
        ballX + ballRadius >= tableWidth - paddleWidth &&
        ballY + ballRadius >= paddle2Y &&
        ballY - ballRadius <= paddle2Y + paddleHeight
      ) {
        ballSpeedX = -ballSpeedX;
      }

      // Verifica si se anotó un punto
      if (ballX - ballRadius <= 0) {
        // Jugador 2 anotó un punto
        player2Score++;
        resetBall();
      } else if (ballX + ballRadius >= tableWidth) {
        // Jugador 1 anotó un punto
        player1Score++;
        resetBall();
      }
    }

    // Reinicia la posición de la pelota
    function resetBall() {
      ballX = tableWidth / 2;
      ballY = tableHeight / 2;
      ballSpeedX = -ballSpeedX;
      ballSpeedY = 3;
    }

    // Dibuja la red
    function drawNet() {
      context.fillStyle = netColor;
      for (let i = 0; i < tableHeight; i += 15) {
        context.fillRect(tableWidth / 2 - 1, i, 2, 10);
      }
    }

    // Dibuja los marcadores de puntos
    function drawScore() {
      context.fillStyle = scoreColor;
      context.font = "16px Arial";
      context.fillText("Player 1: " + player1Score, 20, 30);
      context.fillText("Player 2: " + player2Score, tableWidth - 100, 30);
    }

    // Control de teclado
    function keyDownHandler(event) {
      if (event.key === 'w') {
        upKeyPressed = true;
      } else if (event.key === 's') {
        downKeyPressed = true;
      }
    }

    function keyUpHandler(event) {
      if (event.key === 'w') {
        upKeyPressed = false;
      } else if (event.key === 's') {
        downKeyPressed = false;
      }
    }

    // Reinicia el juego
    function resetGame() {
      player1Score = 0;
      player2Score = 0;
      resetBall();
    }

    // Asigna los controladores de eventos del teclado
    document.addEventListener('keydown', keyDownHandler);
    document.addEventListener('keyup', keyUpHandler);

    // Función de animación
    function animate() {
      // Limpia el lienzo
      context.clearRect(0, 0, tableWidth, tableHeight);

      // Dibuja la mesa, las raquetas y la pelota
      drawTable();
      drawPaddles();
      drawBall();
      drawNet();
      drawScore();

      // Mueve las raquetas y la pelota
      movePaddles();
      moveBall();

      // Solicita la siguiente animación
      requestAnimationFrame(animate);
    }

    // Inicia el juego
    animate();
  </script>
</body>
</html>
