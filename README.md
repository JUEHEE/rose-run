<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8" />
<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>ROSE RUN 🌹</title>

<style>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family:'Pretendard',sans-serif;
}

body{
  background:#f6e8ec;
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  overflow:hidden;
}

/* 게임 프레임 */

#game{
  position:relative;

  width:430px;
  height:820px;

  max-width:100vw;
  max-height:100vh;

  overflow:hidden;

  border-radius:32px;

  background:
  linear-gradient(#fff8fa,#ffeef3);

  border:5px solid white;
}

/* 도트 배경 */

#game::before{

  content:"";

  position:absolute;
  inset:0;

  background:
  radial-gradient(circle,#f1c7d1 1.5px,transparent 1.5px);

  background-size:24px 24px;

  opacity:.45;
}

/* 반짝이 */

.star{
  position:absolute;
  color:#efbcc7;
  font-size:13px;
  animation:twinkle 2s infinite;
}

@keyframes twinkle{

  0%{
    opacity:.3;
  }

  50%{
    opacity:1;
  }

  100%{
    opacity:.3;
  }
}

/* 화면 */

.screen{

  position:absolute;
  inset:0;

  display:none;

  flex-direction:column;
  justify-content:center;
  align-items:center;

  text-align:center;

  padding:34px;

  z-index:5;
}

.active{
  display:flex;
}

/* 타이틀 */

.title{

  font-size:54px;

  color:#d792a1;

  margin-bottom:14px;

  font-family:cursive;
}

.subtitle{

  color:#ba8893;

  line-height:1.7;

  font-size:19px;

  margin-bottom:24px;
}

/* 설명 */

.storyBox{

  width:100%;

  background:
  rgba(255,255,255,.72);

  border:
  2px solid #f2d5dc;

  border-radius:24px;

  padding:24px;

  color:#9c727c;

  line-height:1.9;

  font-size:20px;
}

/* 버튼 */

.btn{

  border:none;

  background:white;

  color:#d18e9d;

  padding:16px 34px;

  border-radius:999px;

  margin-top:26px;

  font-size:20px;
  font-weight:600;

  box-shadow:
  0 4px 12px rgba(225,170,182,.2);
}

/* 게임 UI */

#score{

  position:absolute;

  top:20px;
  left:20px;

  z-index:10;

  background:white;

  color:#d18e9d;

  padding:10px 20px;

  border-radius:999px;

  font-weight:700;
}

/* 바닥 */

#ground{

  position:absolute;

  bottom:0;

  width:100%;
  height:140px;

  background:#f4c5d0;

  border-top:
  3px dashed white;
}

/* 남자 캐릭터 */

#player{

  position:absolute;

  left:55px;
  bottom:140px;

  width:90px;
  height:130px;

  z-index:10;

  animation:run .35s infinite alternate;
}

@keyframes run{

  from{
    transform:translateY(0px);
  }

  to{
    transform:translateY(-8px);
  }
}

/* 단순 선 캐릭터 */

.head{

  width:42px;
  height:42px;

  border:4px solid #3a3a3a;

  border-radius:50%;

  margin:auto;

  background:#fff;
}

.body{

  width:4px;
  height:45px;

  background:#3a3a3a;

  margin:auto;
}

.arm{

  position:absolute;

  width:42px;
  height:4px;

  background:#3a3a3a;

  top:55px;
  left:24px;
}

.leg1,
.leg2{

  position:absolute;

  width:4px;
  height:42px;

  background:#3a3a3a;

  top:82px;
}

.leg1{
  left:34px;
  transform:rotate(18deg);
}

.leg2{
  left:54px;
  transform:rotate(-18deg);
}

/* 장미 */

.roseItem{

  position:absolute;

  font-size:40px;

  bottom:240px;
}

/* 장애물 */

.enemy{

  position:absolute;

  bottom:140px;

  font-size:60px;
}

.thorn{

  position:absolute;

  bottom:140px;

  font-size:52px;
}

/* 여자 캐릭터 */

#girl{

  width:110px;
  height:150px;

  position:relative;

  margin-top:10px;
}

/* 여자 머리 */

.girlHair{

  width:60px;
  height:60px;

  border:4px solid #3a3a3a;

  border-radius:50%;

  margin:auto;

  background:#fff;
}

/* 리본 */

.ribbon{

  position:absolute;

  top:-6px;
  left:38px;

  font-size:22px;

  color:#f1a5b7;
}

.girlBody{

  width:4px;
  height:52px;

  background:#3a3a3a;

  margin:auto;
}

.skirt{

  width:42px;
  height:26px;

  border-bottom:
  30px solid #f4c2cf;

  border-left:12px solid transparent;

  border-right:12px solid transparent;

  margin:auto;
}

/* 훈련소 */

.camp{

  font-size:72px;
  margin:12px 0;
}

</style>
</head>

<body>

<div id="game">

<div class="star" style="top:80px; left:40px;">✦</div>
<div class="star" style="top:150px; right:70px;">✦</div>
<div class="star" style="top:250px; left:100px;">✦</div>
<div class="star" style="bottom:200px; right:60px;">✦</div>

<!-- 시작 -->

<div id="startScreen"
class="screen active">

<div class="title">
Rose Run
</div>

<div class="camp">
🏕️
</div>

<div class="subtitle">

공군 훈련소의 밤 ☁️

</div>

<div class="storyBox">

준이는 오늘 잠이 오지 않았어요...<br><br>

“타올이 없으니까 잠이 안 와...”<br>
“엄마 보고 싶다...”

</div>

<button class="btn"
onclick="startGame()">

장미 전해주러 가기 🌹

</button>

</div>

<!-- 게임 -->

<div id="gameScreen"
class="screen">

<div id="score">
🌹 0
</div>

<!-- 남자 -->

<div id="player">

<div class="head"></div>

<div class="arm"></div>

<div class="body"></div>

<div class="leg1"></div>

<div class="leg2"></div>

</div>

<div id="ground"></div>

</div>

<!-- 성공 -->

<div id="clearScreen"
class="screen">

<div class="title"
style="font-size:42px;">

🌹 SUCCESS 🌹

</div>

<!-- 여자 -->

<div id="girl">

<div class="ribbon">
🎀
</div>

<div class="girlHair"></div>

<div class="girlBody"></div>

<div class="skirt"></div>

</div>

<div class="storyBox">

주희가 장미를 무사히 전달했어요 💗<br><br>

준이는 행복해졌어요 ☁️<br><br>

“고마워...”<br>
“대신 소원권 10장 줄게.”

</div>

<div style="
margin-top:24px;
font-size:28px;
color:#d58f9f;
">

🎫 소원권 x10

</div>

<button class="btn"
onclick="location.reload()">

처음으로

</button>

</div>

<!-- 실패 -->

<div id="failScreen"
class="screen">

<div class="title"
style="font-size:42px;">

💔 GAME OVER

</div>

<div class="storyBox">

장미를 끝까지 지키지 못했어요... ☁️

</div>

<button class="btn"
onclick="location.reload()">

다시하기

</button>

</div>

</div>

<script>

const player =
document.getElementById("player");

const gameScreen =
document.getElementById("gameScreen");

const scoreUI =
document.getElementById("score");

let jumping = false;

let dead = false;

let score = 0;

let playing = false;

/* 화면 */

function showScreen(id){

document
.querySelectorAll(".screen")
.forEach(s=>{

s.classList.remove("active");

});

document
.getElementById(id)
.classList.add("active");

}

/* 시작 */

function startGame(){

showScreen("gameScreen");

playing = true;

spawnObstacle();

spawnEnemy();

spawnRose();

scoreLoop();

}

/* 점프 */

function jump(){

if(jumping || dead || !playing)
return;

jumping = true;

let y = 140;

let velocity = 17;

let gravity = .85;

const jumpLoop = setInterval(()=>{

velocity -= gravity;

y += velocity;

if(y <= 140){

y = 140;

clearInterval(jumpLoop);

jumping = false;

}

player.style.bottom =
y + "px";

},20);

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

if(score >= 300){

playing = false;

clearInterval(loop);

showScreen("clearScreen");

}

},100);

}

/* 가시 */

function spawnObstacle(){

if(dead || !playing)
return;

const thorn =
document.createElement("div");

thorn.classList.add("thorn");

thorn.innerHTML = "🪵";

gameScreen.appendChild(thorn);

let x = 450;

const move = setInterval(()=>{

x -= 8;

thorn.style.left =
x + "px";

const playerY =
parseInt(
player.style.bottom || 140
);

if(
x < 130 &&
x > 50 &&
playerY < 210
){

dead = true;

showScreen("failScreen");

}

if(x < -100){

clearInterval(move);

thorn.remove();

}

},20);

setTimeout(
spawnObstacle,
Math.random()*1400 + 1200
);

}

/* 장미 뺏는 사람 */

function spawnEnemy(){

if(dead || !playing)
return;

const enemy =
document.createElement("div");

enemy.classList.add("enemy");

enemy.innerHTML = "😈";

gameScreen.appendChild(enemy);

let x = 500;

const move = setInterval(()=>{

x -= 10;

enemy.style.left =
x + "px";

const playerY =
parseInt(
player.style.bottom || 140
);

if(
x < 140 &&
x > 40 &&
playerY < 240
){

dead = true;

showScreen("failScreen");

}

if(x < -120){

clearInterval(move);

enemy.remove();

}

},20);

setTimeout(
spawnEnemy,
Math.random()*2500 + 2500
);

}

/* 장미 */

function spawnRose(){

if(dead || !playing)
return;

const rose =
document.createElement("div");

rose.classList.add("roseItem");

rose.innerHTML = "🌹";

gameScreen.appendChild(rose);

let x = 450;

rose.style.left =
x + "px";

const move = setInterval(()=>{

x -= 6;

rose.style.left =
x + "px";

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

/* 입력 */

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

window.addEventListener(
"mousedown",
jump
);

</script>

</body>
</html>
