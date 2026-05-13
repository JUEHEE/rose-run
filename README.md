<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>ROSE RUN</title>
  <style>
    :root {
      --pink: #ff4f87;
      --soft-pink: #fff0f5;
    }
    * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
    
    body {
      background: #222;
      display: flex; justify-content: center; align-items: center;
      height: 100vh; overflow: hidden;
      font-family: sans-serif;
    }

    /* 게임 화면 컨테이너 */
    #game {
      position: relative;
      width: 100%; max-width: 450px; height: 100%;
      background: linear-gradient(var(--soft-pink), #ffcbdc);
      overflow: hidden;
    }

    /* 점수판 */
    #score {
      position: absolute; top: 20px; left: 20px;
      font-size: 20px; font-weight: bold; color: var(--pink);
      background: white; padding: 8px 20px; border-radius: 30px;
      z-index: 50; border: 2px solid var(--pink);
    }

    /* 바닥 */
    #ground {
      position: absolute; bottom: 0; width: 100%; height: 100px;
      background: #ff8da1; border-top: 4px solid var(--pink);
      z-index: 5;
    }

    /* 캐릭터 - 이미지가 없을 경우를 대비해 텍스트(🏃‍♂️) 포함 */
    #player {
      position: absolute; width: 70px; height: 70px;
      left: 40px; bottom: 100px;
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 450px auto;
      background-position: -12px -432px;
      background-repeat: no-repeat;
      display: flex; align-items: center; justify-content: center;
      font-size: 40px; z-index: 10;
    }

    /* 장애물 */
    .thorn {
      position: absolute; width: 40px; height: 40px;
      bottom: 100px; right: -50px;
      display: flex; align-items: center; justify-content: center;
      font-size: 30px; z-index: 10;
    }

    /* 안내 창 레이아웃 정돈 */
    .overlay {
      position: absolute; top: 0; left: 0; width: 100%; height: 100%;
      background: rgba(255, 255, 255, 0.9);
      display: none; /* 기본적으로 숨김 */
      flex-direction: column; justify-content: center;
      align-items: center; text-align: center; z-index: 100;
      padding: 30px;
    }
    
    #startScreen { display: flex; } /* 시작 화면만 처음에 보임 */

    .overlay h1 { color: var(--pink); font-size: 40px; margin-bottom: 20px; }
    .overlay p { font-size: 18px; color: #444; margin-bottom: 30px; line-height: 1.5; }
    
    .btn {
      padding: 15px 40px; background: var(--pink); color: white;
      border: none; border-radius: 50px; font-size: 20px; font-weight: bold;
      cursor: pointer; box-shadow: 0 4px 10px rgba(255,79,135,0.3);
    }

    .btn:active { transform: translateY(2px); }

    .flower-effect { position: absolute; font-size: 20px; z-index: 1; animation: fall linear forwards; }
    @keyframes fall { to { transform: translateY(100vh) rotate(360deg); } }
  </style>
</head>
<body>

<div id="game">
  <div id="score">🌹 0 / 300</div>

  <!-- 시작 화면 -->
  <div id="startScreen" class="overlay">
    <h1>ROSE RUN</h1>
    <p>장미 가시(🌵)를 점프로 피해<br>소년의 꽃다발을 전달하세요!</p>
    <button class="btn" onclick="startGame()">게임 시작</button>
  </div>

  <!-- 게임오버 화면 -->
  <div id="gameOverScreen" class="overlay">
    <h1>💔 앗!</h1>
    <p>가시에 걸려 꽃이 시들었어요.</p>
    <button class="btn" onclick="location.reload()">다시 하기</button>
  </div>

  <!-- 성공 화면 -->
  <div id="gameClearScreen" class="overlay">
    <h1 style="font-size: 60px;">👩‍❤️‍👨</h1>
    <h1>성공!</h1>
    <p>꽃다발이 무사히 전달되었습니다!<br>행복한 기념일 보내세요!</p>
    <button class="btn" onclick="location.reload()">한번 더 하기</button>
  </div>

  <div id="player">🏃‍♂️</div> <!-- 이미지 안나오면 이모티콘이라도 나옴 -->
  <div id="ground"></div>
</div>

<script>
const player = document.getElementById("player");
const game = document.getElementById("game");
const scoreText = document.getElementById("score");
const startScreen = document.getElementById("startScreen");
const gameOverScreen = document.getElementById("gameOverScreen");
const gameClearScreen = document.getElementById("gameClearScreen");

let isJumping = false;
let gameStarted = false;
let isDead = false;
let score = 0;
const targetScore = 300;

function jump() {
  if (isJumping || isDead || !gameStarted) return;
  isJumping = true;
  
  let jumpHeight = 0;
  let up = setInterval(() => {
    if (jumpHeight >= 160) {
      clearInterval(up);
      let down = setInterval(() => {
        if (jumpHeight <= 0) {
          clearInterval(down);
          isJumping = false;
        }
        jumpHeight -= 6;
        player.style.bottom = (100 + jumpHeight) + "px";
      }, 12);
    }
    jumpHeight += 8;
    player.style.bottom = (100 + jumpHeight) + "px";
  }, 12);
}

function startGame() {
  gameStarted = true;
  startScreen.style.display = "none";
  
  // 점수 체크 루프
  const mainLoop = setInterval(() => {
    if (isDead) { clearInterval(mainLoop); return; }
    score += 1;
    scoreText.innerHTML = `🌹 ${score} / ${targetScore}`;
    if (score >= targetScore) { winGame(); clearInterval(mainLoop); }
  }, 100);

  spawnThorn();
}

function spawnThorn() {
  if (!gameStarted || isDead) return;
  const thorn = document.createElement("div");
  thorn.classList.add("thorn");
  thorn.innerHTML = "🌵";
  game.appendChild(thorn);

  let thornX = game.offsetWidth;
  let move = setInterval(() => {
    if (isDead || !gameStarted) { clearInterval(move); thorn.remove(); return; }
    thornX -= 6;
    thorn.style.left = thornX + "px";

    // 충돌 감지
    const p = player.getBoundingClientRect();
    const t = thorn.getBoundingClientRect();
    if (!(p.right < t.left + 10 || p.left > t.right - 10 || p.bottom < t.top + 10)) {
      isDead = true;
      gameOverScreen.style.display = "flex";
    }

    if (thornX < -50) { clearInterval(move); thorn.remove(); }
  }, 10);

  setTimeout(spawnThorn, Math.random() * 1000 + 1200);
}

function winGame() {
  gameStarted = false;
  gameClearScreen.style.display = "flex";
}

// 입력 처리: 클릭, 터치, 스페이스바
window.addEventListener("mousedown", jump);
window.addEventListener("touchstart", (e) => {
  if (e.target.tagName !== "BUTTON") { e.preventDefault(); jump(); }
}, {passive: false});
window.addEventListener("keydown", (e) => { if(e.code === "Space") jump(); });

// 배경 꽃잎
setInterval(() => {
  if (isDead) return;
  const p = document.createElement("div");
  p.className = "flower-effect";
  p.innerHTML = "🌸";
  p.style.left = Math.random() * 100 + "%";
  p.style.animationDuration = (Math.random() * 3 + 2) + "s";
  game.appendChild(p);
  setTimeout(() => p.remove(), 5000);
}, 800);
</script>
</body>
</html>
