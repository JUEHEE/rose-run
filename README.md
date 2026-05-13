<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>🌹 ROSE RUN: 프러포즈 대작전 🌹</title>

  <style>
    *{
      margin:0;
      padding:0;
      box-sizing:border-box;
      font-family:'Arial';
    }

    body{
      overflow:hidden;
      background:#fff0f5;
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
      height:100px;
      background:#ff8da1;
      border-top: 5px solid #ff4f87;
    }

    /* 이미지 캐릭터를 위한 스타일 */
    #player {
      position:absolute;
      width:100px;
      height:100px;
      left:100px;
      bottom:100px;
      /* 제공된 이미지 중 꽃다발을 든 소년 부분 위치 지정 */
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 500px auto; /* 이미지 크기에 맞춰 조절 */
      background-position: -15px -480px; /* 꽃다발 든 소년 위치(예시) */
      background-repeat: no-repeat;
      z-index: 10;
    }

    .thorn{
      position:absolute;
      width:50px;
      height:50px;
      bottom:100px;
      right:-100px;
      font-size:40px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .petal{
      position:absolute;
      font-size:25px;
      opacity:.6;
      animation:fall linear forwards;
    }

    #score{
      position:absolute;
      top:20px;
      left:20px;
      font-size:28px;
      font-weight:bold;
      color:#ff4f87;
      background: rgba(255,255,255,0.7);
      padding: 10px 20px;
      border-radius: 30px;
    }

    #startText, #gameClear, #gameOver {
      position:absolute;
      width:100%;
      top:40%;
      text-align:center;
      color:#ff4f87;
      text-shadow:1px 1px 2px rgba(0,0,0,0.1);
    }

    #gameClear, #gameOver { display: none; }

    /* 클리어 시 나오는 커플 이미지 구역 */
    .couple-img {
      width: 200px;
      height: 200px;
      margin: 0 auto 20px;
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 600px auto;
      background-position: -20px -470px; /* 꽃다발 같이 든 장면 위치 */
      background-repeat: no-repeat;
      border: 5px solid white;
      border-radius: 20px;
      box-shadow: 0 0 20px rgba(255,100,150,0.5);
    }

    button {
      margin-top:20px;
      padding:12px 25px;
      border:none;
      border-radius:25px;
      background:#ff4f87;
      color:white;
      font-size:18px;
      cursor:pointer;
      font-weight:bold;
      transition: transform 0.2s;
    }
    
    button:hover { transform: scale(1.1); }

    @keyframes fall{
      from{ transform:translateY(-50px) rotate(0deg); }
      to{ transform:translateY(110vh) rotate(360deg); }
    }
  </style>
</head>
<body>

<div id="game">
  <div id="score">🌹 0 / 500</div>

  <div id="startText">
    <h1 style="font-size: 50px; margin-bottom: 20px;">ROSE RUN</h1>
    <p style="font-size: 20px;">장미 가시를 피해 그녀에게 꽃을 전달하세요!</p>
    <p style="font-weight: bold; margin-top: 10px;">[스페이스바] 눌러서 점프 시작</p>
  </div>

  <div id="player"></div>
  <div id="ground"></div>

  <!-- 게임 오버 화면 -->
  <div id="gameOver">
    <h1 style="font-size: 50px;">💔 Oops!</h1>
    <p>가시에 걸려 꽃이 시들었습니다...</p>
    <button onclick="location.reload()">다시 도전</button>
  </div>

  <!-- 게임 클리어 화면 (꽃다발을 같이 든 커플) -->
  <div id="gameClear">
    <div class="couple-img"></div>
    <h1 style="font-size: 40px;">💖 SUCCESS! 💖</h1>
    <p>축하합니다! 소년의 마음이 전달되었습니다.</p>
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
const targetScore = 500; // 목표 점수

document.addEventListener("keydown", (e) => {
  if (e.code === "Space") {
    if (!gameStarted && !isGameOver && !isGameClear) {
      startGame();
    }
    jump();
  }
});

function jump() {
  if (jumping || isGameOver || isGameClear) return;
  jumping = true;

  let pos = 100;
  let up = setInterval(() => {
    if (pos >= 320) {
      clearInterval(up);
      let down = setInterval(() => {
        if (pos <= 100) {
          clearInterval(down);
          jumping = false;
        }
        pos -= 8;
        player.style.bottom = pos + "px";
      }, 15);
    }
    pos += 12;
    player.style.bottom = pos + "px";
  }, 15);
}

function startGame() {
  gameStarted = true;
  startText.style.display = "none";

  const thornGen = setInterval(createThorn, 1500);
  const petalGen = setInterval(createPetal, 600);

  const scoreInterval = setInterval(() => {
    if (isGameOver) {
      clearInterval(scoreInterval);
      clearInterval(thornGen);
      return;
    }
    
    if (score >= targetScore) {
      clearInterval(scoreInterval);
      clearInterval(thornGen);
      winGame();
      return;
    }

    score += 5;
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

    thornX -= 8;
    thorn.style.left = thornX + "px";

    const playerBottom = parseInt(window.getComputedStyle(player).getPropertyValue("bottom"));

    // 충돌 감지
    if (thornX > 80 && thornX < 140 && playerBottom < 160) {
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
  petal.innerHTML = Math.random() > 0.5 ? "🌸" : "🌹";
  petal.style.left = Math.random() * window.innerWidth + "px";
  petal.style.animationDuration = (Math.random() * 3 + 2) + "s";
  game.appendChild(petal);
  setTimeout(() => petal.remove(), 5000);
}

function endGame() {
  isGameOver = true;
  gameOverUI.style.display = "block";
}

function winGame() {
  isGameClear = true;
  player.style.display = "none"; // 기존 플레이어 숨김
  gameClearUI.style.display = "block";
}
</script>

</body>
</html>
