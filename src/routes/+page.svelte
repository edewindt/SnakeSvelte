<script>
  //Imports
  import { onMount } from "svelte";

  //Board
  let blockSize = 25;
  let rows = 20;
  let cols = 20;
  let board;
  let c;

  //Snake Head
  let snakeX = blockSize * 5;
  let snakeY = blockSize * 5;

  let velocityX = 0;
  let velocityY = 0;

  let snakeBody = [];

  //Food
  let foodX;
  let foodY;

  let GameOver = false;

  const update = () => {
    if (GameOver) return;
    c.fillStyle = "black";
    c.fillRect(0, 0, board.width, board.height);

    c.fillStyle = "red";
    c.fillRect(foodX, foodY, blockSize, blockSize);

    if (snakeX == foodX && snakeY == foodY) {
      snakeBody = [...snakeBody, [foodX, foodY]];
      placeFood();
    }

    for (let i = snakeBody.length - 1; i > 0; i--) {
      snakeBody[i] = snakeBody[i - 1];
    }

    if (snakeBody.length) {
      snakeBody[0] = [snakeX, snakeY];
    }

    c.fillStyle = "lime";
    c.fillRect(snakeX, snakeY, blockSize, blockSize);

    snakeX += velocityX * blockSize;
    snakeY += velocityY * blockSize;

    for (let i = 0; i < snakeBody.length; i++) {
      c.fillRect(snakeBody[i][0], snakeBody[i][1], blockSize, blockSize);
    }

    //GameOver Conditions
    if (
      snakeX < 0 ||
      snakeX > cols * blockSize ||
      snakeY < 0 ||
      snakeY > rows * blockSize
    ) {
      GameOver = true;
      alert("Game Over");
    }

    for (let i = 0; i < snakeBody.length; i++) {
      if (snakeX == snakeBody[i][0] && snakeY == snakeBody[i][1]) {
        GameOver = true;
        alert("Game Over");
      }
    }
  };
  onMount(() => {
    c = board.getContext("2d");

    placeFood();
    // update();
    setInterval(update, 1000 / 10);
  });

  const placeFood = () => {
    foodX = Math.floor(Math.random() * cols) * blockSize;
    foodY = Math.floor(Math.random() * rows) * blockSize;
  };

  const changeDirection = (e) => {
    switch (e.code) {
      case "ArrowUp":
        if (velocityY != 1) {
          velocityX = 0;
          velocityY = -1;
        }
        break;
      case "ArrowDown":
        if (velocityY != -1) {
          velocityX = 0;
          velocityY = 1;
        }
        break;
      case "ArrowLeft":
        if (velocityX != 1) {
          velocityX = -1;
          velocityY = 0;
        }
        break;
      case "ArrowRight":
        if (velocityX != -1) {
          velocityX = 1;
          velocityY = 0;
        }
        break;

      default:
        break;
    }
  };
</script>

<svelte:window on:keydown={changeDirection} />
<h1>Snake</h1>
<canvas bind:this={board} width={rows * blockSize} height={cols * blockSize} />
