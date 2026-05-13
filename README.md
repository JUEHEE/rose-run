<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>🌹 ROSE RUN Mobile 🌹</title>

  <style>
    *{
      margin:0;
      padding:0;
      box-sizing:border-box;
      font-family:'Apple SD Gothic Neo', 'Malgun Gothic', sans-serif;
      /* 모바일에서 길게 터치 시 메뉴가 뜨지 않도록 방지 */
      -webkit-touch-callout: none;
      -webkit-user-select: none;
      user-select: none;
    }

    body{
      overflow:hidden;
      background:#fff0f5;
      touch-action: none; /* 브라우저 기본 제스처 방지 */
    }

    #game{
      position:relative;
      width:100vw;
      height:100vh;
      background:linear-gradient(#fff0f5, #ffd1dc);
      overflow:hidden;
    }

    #ground{
      position:absolute;
      bottom:0;
      width:100%;
      height:80px;
      background:#ff8da1;
      border-top: 4px solid #ff4f87;
    }

    #player {
      position:absolute;
      width:80px;
      height:80px;
      left:50px;
      bottom:80px;
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 450px auto;
      background-position: -12px -432px; /* 꽃다발 든 소년 */
      background-repeat: no-repeat;
      z-index: 10;
      transition: bottom 0.1s linear;
    }

    .thorn{
      position:absolute;
      width:40px;
      height:40px;
      bottom:80px;
      right:-50px;
      font-size:30px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .petal{
      position:absolute;
      font-size:20px;
      opacity:.6;
      animation:fall linear forwards;
    }

    #score{
      position:absolute;
      top:15px;
      left:15px;
      font-size:22px;
      font-weight:bold;
      color:#ff4f87;
      background: rgba(255,255,255,0.8);
      padding: 8px 15px;
      border-radius: 20px;
      z-index: 100;
    }

    #startText, #gameClear, #gameOver {
      position:absolute;
      width:90%;
      left: 5%;
      top:35%;
      text-align:center;
      color:#ff4f87;
      z-index: 100;
    }

    /* 클리어 시 나오는 커플 이미지 */
    .couple-img {
      width: 180px;
      height: 180px;
      margin: 0 auto 15px;
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 550px auto;
      background-position: -20px -430px; /* 꽃다발 같이 든 장면 */
      background-repeat: no-repeat;
      border: 4px solid white;
      border-radius: 20px;
      box-shadow: 0 0 15px rgba(255,100,150,0.4);
    }

    button {
      margin-top:20px;
      padding:15px 35px;
      border:none;
      border-radius:30px;
      background:#ff4f87;
      color:white;
      font-size:20px;
      cursor:pointer;
      font-weight:bold;
      box-shadow: 0 4px 0 #d13d6a;
    }

    button:active {
      transform: translateY(2px);
      box-shadow: 0 2px 0 #d13d6a;
    }

    @keyframes fall{
      from{ transform:translateY(-50px) rotate(0deg); }
      to{ transform:translateY(110vh) rotate(360deg); }
    }
  </style>
</head>
<body>

<div id="game">
  <div id="score">🌹 0 / 300</div>

  <div id="startText">
    <h1 style="font-size: 40px; margin-bottom: 10px;">ROSE RUN</h1>
    <p>화면을 터치해서 장애물을 넘으세요!</p>
    <button>게임 시작</button>
  </div>

  <div id="player"></div>
  <div id="ground"></div>

  <div id="gameOver">
    <h1 style="font-size: 40px;">💔 Oops!</h1>
    <p>장미 가시에 걸렸어요...</p>
    <button onclick="location.reload()">다시 하기</button>
  </div>

  <div id="gameClear">
    <div class="couple-img"></div>
    <h1 style="font-size: 35px;">💖 성공! 💖</h1>
    <p>사랑이 전달되었습니다!</p>
    <button onclick="location.reload()">행복한 재시작</button>
  </div>
</div>

<script>
const player = document.getElementById("player");
const game = document.getElementById("game");
const scoreText = document.getElementById("score");
const gameOverUI = document.getElementById("gameOver");
const gameClearUI = document.getElementById("gameClear");
const startText = document.getElementById("startText");

let jumping = false;
let gameStarted = false;
let isGameOver = false;
let isGameClear = false;
let score = 0;
const targetScore = 300; 

// --- 터치 및 키보드 입력 통합 관리 ---
function handleInput(e) {
  if (e.type === 'touchstart') e.preventDefault(); // 브라우저 기본 동작 방지

  if (!gameStarted && !isGameOver && !isGameClear) {
    startGame();
  } else {
    jump();
  }
}

// 스페이스바 대응
document.addEventListener("keydown", (e) => {
  if (e.code === "Space") handleInput(e);
});

// 화면 전체 터치 대응
document.addEventListener("touchstart", handleInput, {passive: false});

function jump() {
  if (jumping || isGameOver || isGameClear) return;
  jumping = true;

  let pos = 80;
  let jumpHeight = 0;
  let up = setInterval(() => {
    if (jumpHeight >= 150) {
      clearInterval(up);
      let down = setInterval(() => {
        if (jumpHeight <= 0) {
          clearInterval(down);
          jumping = false;
        }
        jumpHeight -= 6;
        player.style.bottom = (pos + jumpHeight) + "px";
      }, 12);
    }
    jumpHeight += 10;
    player.style.bottom = (pos + jumpHeight) + "px";
  }, 12);
}

function startGame() {
  gameStarted = true;
  startText.style.display = "none";

  const thornGen = setInterval(createThorn, 1800);
  const petalGen = setInterval(createPetal, 700);

  const scoreInterval = setInterval(() => {
    if (isGameOver) {
      clearInterval(scoreInterval);
      return;
    }
    
    if (score >= targetScore) {
      clearInterval(scoreInterval);
      clearInterval(thornGen);
      winGame();
      return;
    }

    score += 2;
    scoreText.innerHTML = `🌹 ${score} / ${targetScore}`;
  }, 100);
}

function createThorn() {
  if (isGameOver || isGameClear) return;

  const thorn = document.createElement("div");
  thorn.classList.add("thorn");
  thorn.innerHTML = "🌵"; 
  game.appendChild(thorn);

  let thornX = window.innerWidth;

  let move = setInterval(() => {
    if (isGameOver || isGameClear) {
      clearInterval(move);
      thorn.remove();
    }

    thornX -= 6; // 모바일 속도에 맞춰 조정
    thorn.style.left = thornX + "px";

    const playerBottom = parseInt(window.getComputedStyle(player).getPropertyValue("bottom"));

    // 충돌 판정 (모바일 최적화)
    if (thornX > 40 && thornX < 100 && playerBottom < 130) {
      clearInterval(move);
      endGame();
    }

    if (thornX < -50) {
      clearInterval(move);
      thorn.remove();
    }
  }, 10);
}

function createPetal() {
  if (isGameOver || isGameClear) return;
  const petal = document.createElement("div");
  petal.classList.add("petal");
  petal.innerHTML = "🌸";
  petal.style.left = Math.random() * window.innerWidth + "px";
  petal.style.animationDuration = (Math.random() * 3 + 2) + "s";
  game.appendChild(petal);
  setTimeout(() => petal.remove(), 4000);
}

function endGame() {
  isGameOver = true;
  gameOverUI.style.display = "block";
}

function winGame() {
  isGameClear = true;
  player.style.display = "none";
  gameClearUI.style.display = "block";
}
</script>

</body>
</html>
