<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Catch the Hearts 💖</title>
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #ffdde1, #ee9ca7);
    overflow: hidden;
    color: #333;
    touch-action: none;
    user-select: none;
  }
  #gameArea {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
  }
  /* Player heart */
  #player {
    position: absolute;
    bottom: 25px;
    left: 50%;
    transform: translateX(-50%);
    width: 80px;
    height: 72px;
    cursor: pointer;
    z-index: 10;
  }
  /* CSS Heart Shape */
  .heart-shape {
    position: relative;
    width: 80px;
    height: 72px;
  }
  .heart-shape::before,
  .heart-shape::after {
    content: "";
    position: absolute;
    width: 50px;
    height: 80px;
    background: #e74c3c;
    border-radius: 50px 50px 0 0;
    top: 0;
    left: 15px;
    transform: rotate(-45deg);
    transform-origin: 0 100%;
    transition: background 0.3s;
  }
  .heart-shape::after {
    left: 0;
    transform: rotate(45deg);
    transform-origin: 100% 100%;
  }
  /* flutter animation */
  @keyframes flutter {
    0% { transform: translateX(-50%) scale(1) rotate(0deg); }
    25% { transform: translateX(-50%) scale(1.2) rotate(15deg); }
    50% { transform: translateX(-50%) scale(1) rotate(-15deg); }
    75% { transform: translateX(-50%) scale(1.1) rotate(10deg); }
    100% { transform: translateX(-50%) scale(1) rotate(0deg); }
  }
  .player-flutter {
    animation: flutter 0.5s ease-in-out;
  }
  /* Smaller hearts */
  .heart {
    position: absolute;
    width: 40px;
    height: 36px;
    top: 0;
    background: transparent;
  }
  .heart .heart-shape {
    width: 40px;
    height: 36px;
  }
  .heart .heart-shape::before,
  .heart .heart-shape::after {
    width: 25px;
    height: 40px;
    top: 0;
    left: 7.5px;
  }
  .heart .heart-shape::after {
    left: 0;
  }
  #scoreBoard {
    position: absolute;
    top: 12px;
    left: 12px;
    font-size: 18px;
    font-weight: 600;
    color: #222;
    text-shadow: 0 0 5px rgba(255, 255, 255, 0.7);
    user-select: none;
  }
  #lives {
    position: absolute;
    top: 12px;
    right: 12px;
    font-size: 22px;
    user-select: none;
    letter-spacing: 4px;
    color: #e74c3c;
    text-shadow: 0 0 5px rgba(255, 255, 255, 0.7);
  }
  #gameOver {
    position: absolute;
    top: 45%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 28px;
    color: #c0392b;
    background: rgba(255, 255, 255, 0.9);
    padding: 25px 40px;
    border-radius: 16px;
    box-shadow: 0 0 20px #c0392b;
    text-align: center;
    display: none;
    z-index: 20;
    user-select: none;
  }
  #compliment {
    position: absolute;
    bottom: 100px;
    left: 50%;
    transform: translateX(-50%);
    font-size: 20px;
    color: #2c3e50;
    background: #ffdde1;
    padding: 12px 24px;
    border-radius: 20px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    display: none;
    user-select: none;
  }
  #startBtn {
    position: absolute;
    top: 45%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 26px;
    padding: 18px 50px;
    border-radius: 15px;
    border: none;
    background: #e74c3c;
    color: white;
    cursor: pointer;
    box-shadow: 0 5px 15px rgba(231, 76, 60, 0.7);
    z-index: 1000;
    user-select: none;
    transition: background 0.3s;
  }
  #startBtn:hover {
    background: #c0392b;
  }
  /* Responsive adjustments */
  @media (max-width: 480px) {
    #player {
      width: 60px;
      height: 54px;
    }
    .heart-shape {
      width: 60px;
      height: 54px;
    }
    .heart-shape::before,
    .heart-shape::after {
      width: 38px;
      height: 60px;
      left: 11px;
    }
    .heart-shape::after {
      left: 0;
    }
    .heart {
      width: 30px;
      height: 27px;
    }
    .heart .heart-shape {
      width: 30px;
      height: 27px;
    }
    .heart .heart-shape::before,
    .heart .heart-shape::after {
      width: 18px;
      height: 27px;
      left: 5.5px;
    }
    .heart .heart-shape::after {
      left: 0;
    }
    #scoreBoard, #lives {
      font-size: 16px;
    }
    #compliment {
      font-size: 18px;
      padding: 10px 20px;
      bottom: 80px;
    }
    #startBtn {
      font-size: 22px;
      padding: 15px 40px;
    }
  }
</style>
</head>
<body>

<div id="gameArea">
  <div id="player"><div class="heart-shape"></div></div>
  <div id="scoreBoard">Score: 0 | High Score: 0</div>
  <div id="lives">❤️❤️❤️</div>
  <div id="gameOver">
    <strong>Game Over 💔</strong><br />
    Refresh to play again!
  </div>
  <div id="compliment">You're amazing! 🌟</div>

  <button id="startBtn">Start Game</button>

  <audio id="bgMusic" loop>
    <source src="aashiqtera.mp3" type="audio/mpeg" />
    Your browser does not support the audio element.
  </audio>
</div>

<script>
  const gameArea = document.getElementById("gameArea");
  const player = document.getElementById("player");
  const scoreBoard = document.getElementById("scoreBoard");
  const livesDisplay = document.getElementById("lives");
  const gameOver = document.getElementById("gameOver");
  const compliment = document.getElementById("compliment");
  const startBtn = document.getElementById("startBtn");
  const bgMusic = document.getElementById("bgMusic");

  let score = 0;
  let highScore = 0;
  let lives = 3;
  let heartFallSpeed = 2;
  let heartSpawner;
  let speedIncreaser;
  let hearts = [];

  function createHeart() {
    const heart = document.createElement("div");
    heart.classList.add("heart");
    heart.style.left = Math.random() * (window.innerWidth - 40) + "px";
    const heartShape = document.createElement("div");
    heartShape.classList.add("heart-shape");
    heart.appendChild(heartShape);
    gameArea.appendChild(heart);
    return heart;
  }

  function dropHeart() {
    const heart = createHeart();
    let top = 0;
    hearts.push(heart);

    const fallInterval = setInterval(() => {
      top += heartFallSpeed;
      heart.style.top = top + "px";

      // Check collision with player
      const heartRect = heart.getBoundingClientRect();
      const playerRect = player.getBoundingClientRect();

      if (
        heartRect.bottom >= playerRect.top &&
        heartRect.left + 20 >= playerRect.left &&
        heartRect.left <= playerRect.right
      ) {
        // Caught the heart
        clearInterval(fallInterval);
        gameArea.removeChild(heart);
        hearts = hearts.filter(h => h !== heart);

        score++;
        if (score > highScore) {
          highScore = score;
          showCompliment();
        }
        updateScore();

        // flutter animation trigger
        player.classList.add("player-flutter");
        setTimeout(() => {
          player.classList.remove("player-flutter");
        }, 500);
      }

      // If missed, remove heart and reduce life
      else if (top > window.innerHeight) {
        clearInterval(fallInterval);
        gameArea.removeChild(heart);
        hearts = hearts.filter(h => h !== heart);

        lives--;
        updateLives();

        if (lives === 0) {
          endGame();
        }
      }
    }, 20);
  }

  function updateScore() {
    scoreBoard.textContent = `Score: ${score} | High Score: ${highScore}`;
  }

  function updateLives() {
    livesDisplay.textContent = "❤️".repeat(lives);
  }

  function showCompliment() {
    const compliments = [
      "You're amazing! 🌟",
      "Keep shining! ✨",
      "Awesome job! 💖",
      "You're a star! 🌟",
      "Keep going! 💪"
    ];
    compliment.textContent = compliments[Math.floor(Math.random() * compliments.length)];
    compliment.style.display = "block";
    setTimeout(() => {
      compliment.style.display = "none";
    }, 2000);
  }

  function startGame() {
    score = 0;
    lives = 3;
    heartFallSpeed = 2;
    updateScore();
    updateLives();
    gameOver.style.display = "none";
    startBtn.style.display = "none";

    bgMusic.play();

    heartSpawner = setInterval(dropHeart, 1200);
    speedIncreaser = setInterval(() => {
      if (heartFallSpeed < 15) heartFallSpeed += 0.15;
    }, 5000);
  }

  function endGame() {
    clearInterval(heartSpawner);
    clearInterval(speedIncreaser);
    gameOver.style.display = "block";
    startBtn.style.display = "block";
    bgMusic.pause();
    bgMusic.currentTime = 0;

    // Remove any remaining hearts
    hearts.forEach(h => {
      if (gameArea.contains(h)) gameArea.removeChild(h);
    });
    hearts = [];
  }

  // Move player with touch or mouse drag
  let dragging = false;
  gameArea.addEventListener("mousedown", e => {
    dragging = true;
    movePlayer(e.clientX);
  });
  gameArea.addEventListener("mouseup", () => {
    dragging = false;
  });
  gameArea.addEventListener("mousemove", e => {
    if (dragging) movePlayer(e.clientX);
  });
  gameArea.addEventListener("touchstart", e => {
    dragging = true;
    movePlayer(e.touches[0].clientX);
  });
  gameArea.addEventListener("touchend", () => {
    dragging = false;
  });
  gameArea.addEventListener("touchmove", e => {
    if (dragging) {
      movePlayer(e.touches[0].clientX);
      e.preventDefault();
    }
  });

  function movePlayer(x) {
    const minX = 40;
    const maxX = window.innerWidth - 40;
    if (x < minX) x = minX;
    if (x > maxX) x = maxX;
    player.style.left = x + "px";
  }

  startBtn.addEventListener("click", startGame);
</script>

</body>
</html>
