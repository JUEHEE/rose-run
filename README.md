<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>🌹 ROSE RUN 🌹</title>

<style>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  touch-action:none;
}

body{
  background:#222;
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  overflow:hidden;
  font-family:sans-serif;
}

/* 게임 화면 */
#game{
  position:relative;
  width:430px;
  height:820px;
  max-width:100vw;
  max-height:100vh;
  overflow:hidden;

  background:linear-gradient(#fff0f5,#ffc1d6);

  border-radius:30px;
  border:6px solid white;
}

/* 움직이는 배경 */
#game::before{

  content:"";

  position:absolute;
  inset:0;

  background:
    repeating-linear-gradient(
      to right,
      rgba(255,255,255,.15) 0px,
      rgba(255,255,255,.15) 40px,
      transparent 40px,
      transparent 80px
    );

  animation:bgMove 2s linear infinite;

  opacity:.3;
}

@keyframes bgMove{
  from{
    transform:translateX(0);
  }

  to{
    transform:translateX(-80px);
  }
}

/* 점수 */
#score{
  position:absolute;
  top:20px;
  left:20px;

  z-index:100;

  background:white;
  color:#ff4f87;

  padding:10px 20px;

  border-radius:999px;

  font-weight:bold;
  font-size:24px;
}

/* 바닥 */
#ground{
  position:absolute;
  bottom:0;
  width:100%;
  height:140px;

  background:#ff7fa8;

  z-index:2;
}

/* 플레이어 */
#player{
  position:absolute;

  width:90px;
  height:90px;

  left:60px;
  bottom:140px;

  display:flex;
  justify-content:center;
  align-items:center;

  font-size:60px;

  z-index:10;

  animation:run .35s infinite alternate;
}

/* 뛰는 느낌 */
@keyframes run{

  from{
    transform:translateY(0px) rotate(-3deg);
  }

  to{
    transform:translateY(-8px) rotate(3deg);
  }
}

/* 장애물 */
.thorn{
  position:absolute;

  width:50px;
  height:50px;

  bottom:140px;
  right:-60px;

  display:flex;
  justify-content:center;
  align-items:center;

  font-size:40px;

  z-index:5;
}

/* 장미 */
.rose{
  position:absolute;

  top:-50px;

  font-size:28px;

  animation:fall linear forwards;
}

@keyframes fall{
  to{
    transform:translateY(1000px) rotate(360deg);
  }
}

/* 화면 */
.overlay{
  position:absolute;
  inset:0;

  background:rgba(255,255,255,.92);

  z-index:999;

  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;

  text-align:center;

  padding:30px;
}

.overlay h1{
  color:#ff4f87;
  font-size:55px;
  margin-bottom:20px;
}

.overlay p{
  color:#444;
  line-height:1.5;
  font-size:22px;
  margin-bottom:30px;
}

button{
  border:none;

  background:#ff4f87;
  color:white;

  padding:16px 35px;

  border-radius:999px;

  font-size:22px;
  font-weight:bold;
}

</style>
</head>

<body>

<div id="game">

  <!-- 점수 -->
  <div id="score">🌹 0</div>

  <!-- 시작 -->
  <div id="startScreen" class="overlay">

    <h1>🌹 ROSE RUN 🌹</h1>

    <p>
      장미 가시를 피해<br>
      로즈데이를 성공하세요 💖
    </p>

    <button onclick="startGame()">
      START
    </button>

  </div>

  <!-- 게임오버 -->
  <div id="gameOverScreen"
       class="overlay"
       style="display:none;">

    <h1>💔 GAME OVER</h1>

    <p>
      장미가 시들어버렸어요...
    </p>

    <button onclick="location.reload()">
      다시하기
    </button>

  </div>

  <!-- 성공 -->
  <div id="clearScreen"
       class="overlay"
       style="display:none;">

    <h1>🌹 SUCCESS 🌹</h1>

    <p>
      양준이가 장미 전달에 성공했습니다 💖
    </p>

    <button onclick="location.reload()">
      한번 더 하기
    </button>

  </div>

  <!-- 플레이어 -->
  <div id="player">
    🌹🏃
  </div>

  <!-- 바닥 -->
  <div id="ground"></div>

</div>

<script>

const player =
document.getElementById("player");

const game =
document.getElementById("game");

const scoreUI =
document.getElementById("score");

const startScreen =
document.getElementById("startScreen");

const gameOverScreen =
document.getElementById("gameOverScreen");

const clearScreen =
document.getElementById("clearScreen");

let score = 0;

let gameStarted = false;
let dead = false;
let jumping = false;

const goal = 300;

/* 점프 */

function jump(){

  if(jumping || dead || !gameStarted)
    return;

  jumping = true;

  let y = 140;

  let velocity = 16;

  let gravity = 0.8;

  const jumpLoop = setInterval(()=>{

    velocity -= gravity;

    y += velocity;

    if(y <= 140){

      y = 140;

      clearInterval(jumpLoop);

      jumping = false;
    }

    player.style.bottom = y + "px";

  },20);
}

/* 시작 */

function startGame(){

  gameStarted = true;

  startScreen.style.display = "none";

  obstacleLoop();

  scoreLoop();
}

/* 점수 */

function scoreLoop(){

  const loop = setInterval(()=>{

    if(dead){

      clearInterval(loop);

      return;
    }

    score++;

    scoreUI.innerHTML =
      `🌹 ${score}`;

    if(score >= goal){

      clearInterval(loop);

      gameStarted = false;

      clearScreen.style.display =
        "flex";
    }

  },100);
}

/* 장애물 */

function obstacleLoop(){

  if(dead || !gameStarted)
    return;

  const thorn =
    document.createElement("div");

  thorn.classList.add("thorn");

  thorn.innerHTML = "🌵";

  game.appendChild(thorn);

  let x = 430;

  const move = setInterval(()=>{

    if(dead){

      clearInterval(move);

      thorn.remove();

      return;
    }

    x -= 7;

    thorn.style.left = x + "px";

    /* 충돌 */

    const playerY =
      parseInt(player.style.bottom || 140);

    if(
      x < 120 &&
      x > 40 &&
      playerY < 210
    ){

      dead = true;

      gameOverScreen.style.display =
        "flex";

      clearInterval(move);
    }

    if(x < -100){

      clearInterval(move);

      thorn.remove();
    }

  },20);

  setTimeout(
    obstacleLoop,
    Math.random()*1200 + 1000
  );
}

/* 장미 떨어짐 */

setInterval(()=>{

  if(dead) return;

  const rose =
    document.createElement("div");

  rose.classList.add("rose");

  rose.innerHTML = "🌹";

  rose.style.left =
    Math.random()*400 + "px";

  rose.style.animationDuration =
    Math.random()*3 + 2 + "s";

  game.appendChild(rose);

  setTimeout(()=>{
    rose.remove();
  },5000);

},500);

/* 입력 */

window.addEventListener(
  "keydown",
  (e)=>{

    if(e.code === "Space"){

      jump();
    }
  }
);

window.addEventListener(
  "touchstart",
  jump
);

window.addEventListener(
  "mousedown",
  jump
);

</script>

</body>
</html>
