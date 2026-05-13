<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Rose Run 🌹</title>

<style>
:root {
  --bg-pink: #fcebed;
  --main-pink: #f9d5db;
  --dark-pink: #d8909f;
  --line-pink: #e4aab7;
  --white: #ffffff;
}

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  -webkit-tap-highlight-color:transparent;
}

body{
  background:var(--bg-pink);
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  overflow:hidden;
}

/* 전체 게임 프레임 */

#game-frame{
  width:100%;
  max-width:430px;
  height:95vh;

  background:#fff;

  position:relative;

  border:8px solid #fff;
  outline:2px solid var(--line-pink);

  border-radius:40px;

  overflow:hidden;

  display:flex;
  flex-direction:column;

  box-shadow:0 15px 30px rgba(0,0,0,0.1);
}

.inner-bg{
  position:absolute;
  inset:0;

  background:
  radial-gradient(circle at center,#fff 0%,var(--bg-pink) 100%);

  opacity:0.5;

  z-index:0;
}

/* 도트 */

.pixel{
  position:absolute;
  color:#efbcc7;
  font-size:13px;
  z-index:0;
  animation:twinkle 2s infinite;
}

@keyframes twinkle{
  0%{opacity:.3;}
  50%{opacity:1;}
  100%{opacity:.3;}
}

/* HUD */

.hud{
  position:relative;
  z-index:5;

  padding:40px 20px 10px;

  display:flex;
  justify-content:space-between;
  align-items:center;
}

.rose-counter{
  background:#fff;
  border:2px solid var(--line-pink);

  padding:6px 15px;

  border-radius:12px;

  color:var(--dark-pink);

  font-weight:bold;
  font-size:14px;

  box-shadow:0 2px 0 var(--line-pink);
}

.fever-container{
  width:140px;
  height:22px;

  background:#fff;

  border:2px solid var(--line-pink);

  border-radius:11px;

  padding:2px;
}

.fever-fill{
  width:0%;
  height:100%;

  background:
  linear-gradient(to right,var(--main-pink),var(--dark-pink));

  border-radius:8px;

  transition:.2s;
}

/* 스테이지 */

.stage{
  flex:1;

  position:relative;

  overflow:hidden;

  z-index:1;
}

/* 바닥 */

.ground{
  position:absolute;

  bottom:0;

  width:100%;
  height:120px;

  background:
  linear-gradient(
    to bottom,
    #f9d5db,
    #f1bcc8
  );

  border-top:3px dashed white;
}

/* 캐릭터 */

.character-wrap{
  width:220px;
  height:300px;

  position:absolute;

  left:40px;
  bottom:90px;

  z-index:5;

  transition:bottom .05s linear;
}

/* 달리기 */

.running{
  animation:bounce .7s infinite ease-in-out;
}

@keyframes bounce{
  0%,100%{
    transform:translateY(0);
  }

  50%{
    transform:translateY(-12px);
  }
}

/* 장애물 */

.obstacle{
  position:absolute;

  bottom:90px;

  font-size:50px;

  z-index:4;
}

/* 장미 */

.rose{
  position:absolute;

  bottom:220px;

  font-size:34px;
}

/* 컨트롤 */

.controls{
  position:relative;
  z-index:5;

  padding:20px 25px 40px;

  display:flex;
  justify-content:space-between;
  align-items:center;

  gap:15px;
}

.btn-circle{
  width:75px;
  height:75px;

  background:#fff;

  border:2px solid var(--line-pink);

  border-radius:50%;

  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;

  color:var(--dark-pink);

  font-weight:bold;
  font-size:11px;

  box-shadow:0 4px 0 var(--line-pink);

  cursor:pointer;

  user-select:none;
}

.btn-start{
  flex:1;
  height:65px;

  background:#fff;

  border:2px solid var(--line-pink);

  border-radius:18px;

  color:var(--dark-pink);

  font-size:22px;
  font-weight:800;

  box-shadow:0 5px 0 var(--line-pink);

  cursor:pointer;

  display:flex;
  justify-content:center;
  align-items:center;

  user-select:none;
}

/* 시작 스토리 */

.story-screen{
  position:absolute;
  inset:0;

  z-index:20;

  background:
  rgba(255,248,250,.95);

  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;

  padding:30px;

  text-align:center;
}

.story-title{
  color:var(--dark-pink);
  font-size:42px;
  margin-bottom:16px;
}

.story-box{
  background:white;

  border:2px solid var(--line-pink);

  border-radius:24px;

  padding:24px;

  color:#9a6f79;

  line-height:1.8;

  font-size:18px;

  margin-bottom:24px;
}

/* 성공 */

.clear-screen{
  position:absolute;
  inset:0;

  background:
  rgba(255,248,250,.96);

  z-index:30;

  display:none;

  flex-direction:column;
  justify-content:center;
  align-items:center;

  text-align:center;

  padding:30px;
}
</style>
</head>

<body>

<div id="game-frame">

<div class="inner-bg"></div>

<div class="pixel" style="top:80px; left:30px;">✦</div>
<div class="pixel" style="top:150px; right:40px;">✦</div>
<div class="pixel" style="top:240px; left:70px;">✦</div>
<div class="pixel" style="bottom:190px; right:50px;">✦</div>

<!-- 스토리 -->

<div class="story-screen" id="storyScreen">

<div class="story-title">
Rose Run 🌹
</div>

<div class="story-box">

오늘은 공군 훈련소의 밤 ☁️<br><br>

준이는 잠이 오지 않았어요.<br><br>

“타올이 없으니까 잠이 안 와...”<br>
“엄마 보고 싶다...”

</div>

<div class="btn-start"
id="storyStartBtn">

장미 전해주러 가기 🌹

</div>

</div>

<!-- 성공 -->

<div class="clear-screen"
id="clearScreen">

<div class="story-title">
🌹 SUCCESS 🌹
</div>

<div class="story-box">

주희가 준이에게<br>
장미를 전달했어요 💗<br><br>

준이는 기뻐하며<br>
소원권 10장을 주었습니다 ☁️

</div>

<div style="
font-size:28px;
color:#d8909f;
margin-top:12px;
">
🎫 x10
</div>

</div>

<!-- HUD -->

<div class="hud">

<div class="rose-counter"
id="scoreText">
🌹 0 / 300
</div>

<div class="fever-container">
<div class="fever-fill"
id="feverFill"></div>
</div>

<div style="
font-size:22px;
color:var(--dark-pink);
">
🎀
</div>

</div>

<!-- 스테이지 -->

<div class="stage"
id="stage">

<!-- 캐릭터 -->

<div class="character-wrap"
id="player">

<!-- SVG 캐릭터 -->

<svg viewBox="0 0 100 120"
xmlns="http://www.w3.org/2000/svg">

<path d="M25 45 Q20 30 35 20 Q50 10 65 20 Q80 30 75 45"
fill="#e9c4b5"
stroke="#9a6f79"
stroke-width="1.2"/>

<rect x="30" y="35"
width="40"
height="35"
rx="10"
fill="#fbe3d6"
stroke="#9a6f79"
stroke-width="1.2"/>

<path d="M30 40 Q35 30 45 35 Q55 30 65 35 Q70 40 70 45 L30 45 Z"
fill="#e9c4b5"
stroke="#9a6f79"
stroke-width="1.2"/>

<circle cx="62" cy="52"
r="1.5"
fill="#5c4a44"/>

<circle cx="66" cy="58"
r="3"
fill="#f9d5db"
opacity="0.7"/>

<rect x="38" y="70"
width="24"
height="25"
fill="#fff"
stroke="#9a6f79"
stroke-width="1.2"/>

<path d="M38 82 H62 V95 H38 Z"
fill="#f9d5db"
stroke="#9a6f79"
stroke-width="1.2"/>

<rect x="42" y="70"
width="3"
height="12"
fill="#d8909f"/>

<rect x="55" y="70"
width="3"
height="12"
fill="#d8909f"/>

<path d="M62 78 Q75 78 80 85"
fill="none"
stroke="#fbe3d6"
stroke-width="4"
stroke-linecap="round"/>

<g transform="translate(75, 80) scale(0.6)">
<circle cx="10" cy="10" r="8" fill="#ff99aa"/>
<circle cx="15" cy="5" r="8" fill="#ffb3cc"/>
<rect x="10" y="15" width="4" height="12"
fill="#aacc99"
transform="rotate(-20)"/>
</g>

<rect x="40" y="95"
width="8"
height="12"
fill="#fbe3d6"
stroke="#9a6f79"
stroke-width="1.2"/>

<rect x="52" y="95"
width="8"
height="12"
fill="#fbe3d6"
stroke="#9a6f79"
stroke-width="1.2"/>

<path d="M36 107 H48 V112 H36 Z"
fill="#fff"
stroke="#9a6f79"
stroke-width="1.2"/>

<path d="M52 107 H64 V112 H52 Z"
fill="#fff"
stroke="#9a6f79"
stroke-width="1.2"/>

</svg>

</div>

<div class="ground"></div>

</div>

<!-- 컨트롤 -->

<div class="controls">

<div class="btn-circle"
id="jumpBtn">
JUMP<br>🎀
</div>

<div class="btn-start"
id="startBtn">
START
</div>

<div class="btn-circle">
RUN<br>🌹
</div>

</div>

</div>

<script>

const player =
document.getElementById("player");

const stage =
document.getElementById("stage");

const startBtn =
document.getElementById("startBtn");

const jumpBtn =
document.getElementById("jumpBtn");

const scoreText =
document.getElementById("scoreText");

const feverFill =
document.getElementById("feverFill");

const storyScreen =
document.getElementById("storyScreen");

const storyStartBtn =
document.getElementById("storyStartBtn");

const clearScreen =
document.getElementById("clearScreen");

let playing = false;
let jumping = false;
let dead = false;

let score = 0;

let playerBottom = 90;

/* 시작 */

function startGame(){

if(playing) return;

storyScreen.style.display = "none";

playing = true;

player.classList.add("running");

scoreLoop();

spawnObstacle();

spawnRose();

}

/* 점프 */

function jump(){

if(!playing || jumping) return;

jumping = true;

let velocity = 17;
let gravity = .8;

const jumpLoop = setInterval(()=>{

velocity -= gravity;

playerBottom += velocity;

if(playerBottom <= 90){

playerBottom = 90;

jumping = false;

clearInterval(jumpLoop);

}

player.style.bottom =
playerBottom + "px";

},20);

}

/* 점수 */

function scoreLoop(){

const loop = setInterval(()=>{

if(!playing){

clearInterval(loop);

return;

}

score++;

scoreText.innerHTML =
`🌹 ${score} / 300`;

feverFill.style.width =
(score/3) + "%";

if(score >= 300){

playing = false;

clearScreen.style.display = "flex";

}

},100);

}

/* 장애물 */

function spawnObstacle(){

if(!playing) return;

const obs =
document.createElement("div");

obs.classList.add("obstacle");

const random =
Math.random();

if(random < .5){

obs.innerHTML = "🪵";

}else{

obs.innerHTML = "😈";

}

stage.appendChild(obs);

let x = 450;

const move = setInterval(()=>{

if(!playing){

clearInterval(move);

obs.remove();

return;

}

x -= 8;

obs.style.left = x + "px";

if(
x < 140 &&
x > 40 &&
playerBottom < 220
){

playing = false;

alert("💔 장미를 지키지 못했어요...");

location.reload();

}

if(x < -100){

clearInterval(move);

obs.remove();

}

},20);

setTimeout(
spawnObstacle,
Math.random()*1500 + 1200
);

}

/* 장미 */

function spawnRose(){

if(!playing) return;

const rose =
document.createElement("div");

rose.classList.add("rose");

rose.innerHTML = "🌹";

stage.appendChild(rose);

let x = 450;

const y =
Math.random()*120 + 170;

rose.style.bottom =
y + "px";

const move = setInterval(()=>{

if(!playing){

clearInterval(move);

rose.remove();

return;

}

x -= 6;

rose.style.left = x + "px";

if(x < -100){

clearInterval(move);

rose.remove();

}

},20);

setTimeout(
spawnRose,
1000
);

}

/* 버튼 */

startBtn.addEventListener(
"click",
startGame
);

storyStartBtn.addEventListener(
"click",
startGame
);

jumpBtn.addEventListener(
"click",
jump
);

window.addEventListener(
"keydown",
e=>{

if(e.code === "Space"){

jump();

}

}
);

window.addEventListener(
"touchstart",
jump
);

</script>

</body>
</html>
