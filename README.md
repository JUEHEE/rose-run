<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>🌹 ROSE RUN 🌹</title>

  <style>
    :root {
      --bg-color: #fff0f5;
      --main-pink: #ff4f87;
      --ground-color: #ff8da1;
    }

    *{
      margin:0; padding:0; box-sizing:border-box;
      -webkit-touch-callout: none; -webkit-user-select: none; user-select: none;
    }

    body {
      background: #333; /* 배경은 어둡게 처리하여 게임 화면 강조 */
      display: flex; justify-content: center; align-items: center;
      height: 100vh; width: 100vw; overflow: hidden;
      font-family: 'Apple SD Gothic Neo', sans-serif;
    }

    /* 게임 컨테이너: 어떤 화면에서도 16:9 비율 유지 시도 */
    #game {
      position: relative;
      width: 100%; max-width: 500px; /* 모바일 폭에 맞춤 */
      height: 100%;
      background: linear-gradient(var(--bg-color), #ffd1dc);
      overflow: hidden;
      box-shadow: 0 0 50px rgba(0,0,0,0.5);
    }

    #score {
      position: absolute; top: 20px; left: 20px;
      font-size: 20px; font-weight: bold; color: var(--main-pink);
      background: white; padding: 10px 20px; border-radius: 50px;
      z-index: 100; border: 2px solid var(--main-pink);
    }

    #ground {
      position: absolute; bottom: 0; width: 100%; height: 20%;
      background: var(--ground-color); border-top: 5px solid var(--main-pink);
    }

    /* 캐릭터: 제공해주신 이미지 적용 */
    #player {
      position: absolute; width: 80px; height: 80px;
      left: 50px; bottom: 20%; /* 지면 바로 위 */
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 450px auto;
      background-position: -12px -432px; /* 꽃다발 든 소년 */
      background-repeat: no-repeat;
      z-index: 10;
    }

    .thorn {
      position: absolute; width: 40px; height: 40px;
      bottom: 20%; right: -50px; font-size: 30px;
      display: flex; align-items: center; justify-content: center;
    }

    .petal {
      position: absolute; font-size: 20px; opacity: 0.5;
      animation: fall linear forwards;
    }

    /* 안내 및 결과 화면 */
    .overlay {
      position: absolute; top: 0; left: 0; width: 100%; height: 100%;
      background: rgba(255, 255, 255, 0.85);
      display: flex; flex-direction: column; justify-content: center;
      align-items: center; text-align: center; z-index: 200; padding: 20px;
    }

    h1 { color: var(--main-pink); font-size: 32px; margin-bottom: 10px; }
    p { font-size: 18px; color: #555; line-height: 1.6; }
    
    .btn {
      margin-top: 30px; padding: 15px 40px;
      background: var(--main-pink); color: white;
      border: none; border-radius: 50px; font-size: 20px; font-weight: bold;
      cursor: pointer; box-shadow: 0 4px 15px rgba(255, 79, 135, 0.4);
    }

    /* 클리어 이미지 박스 */
    .couple-img {
      width: 150px; height: 150px; margin-bottom: 20px;
      background-image: url('KakaoTalk_20260513_141339739.jpg');
      background-size: 500px auto;
      background-position: -18px -430px; /* 커플 위치 */
      border-radius: 20px; border: 5px solid white;
    }

    @keyframes fall {
      to { transform: translateY(100vh) rotate(360deg); }
    }
  </style>
</head>
<body>

<div id="game">
  <div id="score">🌹 0 / 300</div>

  <!-- 시작 화면 -->
  <div id="startScreen" class="overlay">
    <h1>ROSE RUN</h1>
    <div class="couple-img" style="background-position: -12px -432px;"></div>
    <p>장미 가시(🌵)를 점프로 피해서<br>그녀에게 꽃다발을 전달하세요!</p>
    <p style="font-size: 14px; margin-top: 10px; color: #888;">(화면 아무데나 터치/클릭하면 점프!)</p>
    <button class="btn" onclick="startGame()">게임 시작</button>
  </div>

  <!-- 게임오버 화면 -->
  <div id="gameOverScreen" class="overlay" style="display:none;">
    <h1>💔 앗!</h1>
    <p>가시에 걸리고 말았습니다...</p>
    <button class="btn" onclick="location.reload()">다시 도전</button>
  </div>

  <!-- 클리어 화면 -->
  <div id="gameClearScreen" class="overlay" style="display:none;">
    <div class="couple-img"></div>
    <h1>💖 성공! 💖</h1>
    <p>소년의 진심이 전달되었습니다!<br>행복한 기념일이네요.</p>
    <button class="btn" onclick="location.reload()">처음으로</button>
  </div>

  <div id="player"></div>
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

// 메인 게임 루프 방지용 변수
let thornInterval;
let scoreInterval;

// 점프 함수
function jump() {
  if (isJumping || isDead || !gameStarted) return;
  isJumping = true;

  let jumpHeight = 0;
  const groundPos = 20; // % 기준
  
  let up = setInterval(() => {
    if (jumpHeight >= 150) {
      clearInterval(up);
      let down = setInterval(() => {
        if (jumpHeight <= 0) {
          clearInterval(down);
          isJumping = false;
          jumpHeight = 0;
        } else {
          jumpHeight -= 5;
        }
        player.style.transform = `translateY(${-jumpHeight}px)`;
      }, 12);
    } else {
      jumpHeight += 8;
    }
    player.style.transform = `translateY(${-jumpHeight}px)`;
  }, 12);
}

// 게임 시작
function startGame() {
  gameStarted = true;
  startScreen.style.display = "none";
  
  // 점수 증가
  scoreInterval = setInterval(() => {
    if (isDead) return;
    score += 2;
    scoreText.innerHTML = `🌹 ${score} / ${targetScore}`;
    
    if (score >= targetScore) {
      winGame();
    }
  }, 100);

  // 장애물 생성
  spawnThorn();
}

function spawnThorn() {
  if (!gameStarted || isDead) return;

  const thorn = document.createElement("div");
  thorn.classList.add("thorn");
  thorn.innerHTML = "🌵";
  game.appendChild(thorn);

  let thornX = game.offsetWidth;
  
  let moveThorn = setInterval(() => {
    if (isDead || score >= targetScore) {
      clearInterval(moveThorn);
      thorn.remove();
      return;
    }

    thornX -= 7;
    thorn.style.left = thornX + "px";

    // 충돌 체크
    const playerRect = player.getBoundingClientRect();
    const thornRect = thorn.getBoundingClientRect();

    if (
      playerRect.right > thornRect.left + 10 &&
      playerRect.left < thornRect.right - 10 &&
      playerRect.bottom > thornRect.top + 10
    ) {
      die();
    }

    if (thornX < -50) {
      clearInterval(moveThorn);
      thorn.remove();
    }
  }, 10);

  // 다음 장애물 생성 (랜덤 시간)
  setTimeout(spawnThorn, Math.random() * 1000 + 1000);
}

function die() {
  isDead = true;
  clearInterval(scoreInterval);
  gameOverScreen.style.display = "flex";
}

function winGame() {
  gameStarted = false;
  clearInterval(scoreInterval);
  gameClearScreen.style.display = "flex";
}

// 입력 이벤트 바인딩 (클릭 & 터치 & 키보드)
window.addEventListener("mousedown", (e) => {
  if(gameStarted) jump();
});

window.addEventListener("touchstart", (e) => {
  if(gameStarted) jump();
  // 스와이프나 스크롤 방지
  if(e.target.tagName !== 'BUTTON') e.preventDefault(); 
}, {passive: false});

window.addEventListener("keydown", (e) => {
  if (e.code === "Space") jump();
});

// 꽃잎 내리는 효과
setInterval(() => {
  if (isDead) return;
  const petal = document.createElement("div");
  petal.classList.add("petal");
  petal.innerHTML = "🌸";
  petal.style.left = Math.random() * 100 + "%";
  petal.style.animationDuration = (Math.random() * 2 + 3) + "s";
  game.appendChild(petal);
  setTimeout(() => petal.remove(), 5000);
}, 600);
</script>
</body>
</html>
