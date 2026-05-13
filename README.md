<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>ROSE RUN</title>

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

/* 게임 프레임 */
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
}

/* 플레이어 */
#player{
  position:absolute;
  width:80px;
  height:80px;
  left:60px;
  bottom:140px;

  display:flex;
  justify-content:center;
  align-items:center;

  font-size:55px;
  z-index:10;
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
}

/* 시작창 */
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

.flower{
  position:absolute;
  top:-50px;
  font-size:24px;
  animation:fall linear forwards;
}

@keyframes fall{
  to{
    transform:translateY(1000px) rotate(360deg);
  }
}

</style>
</head>

<body>

<div id="game">

  <div id="score">🌹 0</div>

  <div id="startScreen" class="overlay">
    <h1>ROSE RUN</h1>

    <p>
      장미를 들고 달려서<br>
      목적지까지 도착하세요 🌹
    </p>

    <button onclick="startGame()">
      START
    </button>
  </div>

  <div id="gameOverScreen" class="overlay" style="display:none;">
    <h1>💔 GAME OVER</h1>

    <p>
      장미 가시에 부딪혔어요...
    </p>

    <button onclick="location.reload()">
      다시하기
    </button>
  </div>

  <div id="clearScreen" class="overlay" style="display:none;">
    <h1>🌹 SUCCESS</h1>

    <p>
      장미 전달 성공 💖
    </p>

    <button onclick="location.reload()">
      한번 더
    </button>
  </div>

  <div id="player">🏃‍♂️🌹</div>

  <div id="ground"></div>

</div>

<script>

const player = document.getElementById("player");
const game = document.getElementById("game");

const scoreUI = document.getElementById("score");

const startScreen = document.getElementById("startScreen");
const gameOverScreen = document.getElementById("gameOverScreen");
const clearScreen = document.getElementById("clearScreen");

let score = 0;

let gameStarted = false;
let dead = false;
let jumping = false;

const goal = 300;

/* 점프 */
function jump(){

  if(jumping || dead || !gameStarted) return;

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

    scoreUI.innerHTML = `🌹 ${score}`;

    if(score >= goal){

      clearInterval(loop);

      gameStarted = false;

      clearScreen.style.display = "flex";
    }

  },100);
}

/* 장애물 */
function obstacleLoop(){

  if(dead || !gameStarted) return;

  const thorn = document.createElement("div");

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

    const playerX = 60;
    const playerY = parseInt(player.style.bottom || 140);

    if(
      x < 120 &&
      x > 40 &&
      playerY < 210
    ){

      dead = true;

      gameOverScreen.style.display = "flex";

      clearInterval(move);
    }

    if(x < -100){

      clearInterval(move);

      thorn.remove();
    }

  },20);

  setTimeout(obstacleLoop,
    Math.random()*1200 + 1000
  );
}

/* 꽃잎 */

setInterval(()=>{

  if(dead) return;

  const flower = document.createElement("div");

  flower.classList.add("flower");

  flower.innerHTML = "🌸";

  flower.style.left =
    Math.random()*400 + "px";

  flower.style.animationDuration =
    Math.random()*3 + 2 + "s";

  game.appendChild(flower);

  setTimeout(()=>{
    flower.remove();
  },5000);

},500);

/* 입력 */

window.addEventListener("keydown",(e)=>{
  if(e.code === "Space"){
    jump();
  }
});

window.addEventListener("touchstart",jump);

window.addEventListener("mousedown",jump);

</script>

</body>
</html>
