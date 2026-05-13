<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>Rose Run 🌹</title>

<style>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family:'Pretendard',sans-serif;
}

body{
  background:#f7e6ea;
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
  linear-gradient(
    rgba(255,248,250,.92),
    rgba(255,240,244,.95)
  );

  border:5px solid white;
}

/* 배경 */

#game::before{

  content:"";

  position:absolute;
  inset:0;

  background:

  radial-gradient(circle,#f3c8d2 1px,transparent 1px);

  background-size:24px 24px;

  opacity:.5;
}

/* 별 */

.star{
  position:absolute;

  color:#efb9c6;

  font-size:14px;

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

  padding:32px;

  text-align:center;

  z-index:5;
}

.active{
  display:flex;
}

/* 타이틀 */

.title{

  font-size:58px;

  color:#d694a2;

  font-family:cursive;

  margin-bottom:14px;
}

.subtitle{

  color:#b48691;

  line-height:1.7;

  font-size:19px;

  margin-bottom:28px;
}

/* 버튼 */

.btn{

  border:none;

  background:white;

  color:#cf8e9d;

  padding:16px 34px;

  border-radius:999px;

  font-size:20px;
  font-weight:600;

  margin-top:24px;

  box-shadow:
  0 4px 12px rgba(225,170,182,.25);
}

/* 캐릭터 */

.character{

  font-size:92px;

  margin:16px 0;
}

/* 게임 UI */

#score{

  position:absolute;

  top:24px;
  left:24px;

  z-index:10;

  background:white;

  padding:10px 20px;

  border-radius:999px;

  color:#cf8e9d;

  font-weight:700;
}

#goal{

  position:absolute;

  top:24px;
  right:24px;

  z-index:10;

  background:white;

  padding:10px 20px;

  border-radius:999px;

  color:#cf8e9d;

  font-weight:700;
}

/* 바닥 */

#ground{

  position:absolute;

  bottom:0;

  width:100%;
  height:140px;

  background:#f5c4cf;

  border-top:
  3px dashed #fff;
}

/* 플레이어 */

#player{

  position:absolute;

  left:60px;
  bottom:140px;

  font-size:64px;

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

/* 장애물 */

.thorn{

  position:absolute;

  bottom:140px;

  font-size:58px;

  z-index:5;
}

/* 장미 */

.rose{

  position:absolute;

  bottom:220px;

  font-size:42px;

  z-index:5;
}

/* 설명창 */

.storyBox{

  background:
  rgba(255,255,255,.75);

  border:
  2px solid #f1d4dc;

  border-radius:26px;

  padding:24px;

  line-height:1.8;

  font-size:20px;

  color:#9c717d;

  width:100%;
}

</style>
</head>

<body>

<div id="game">

<!-- 별 -->

<div class="star" style="top:80px; left:40px;">✦</div>
<div class="star" style="top:130px; right:70px;">✦</div>
<div class="star" style="top:260px; left:80px;">✦</div>
<div class="star" style="bottom:180px; right:40px;">✦</div>
<div class="star" style="bottom:250px; left:60px;">✦</div>

<!-- 시작 -->

<div id="startScreen"
class="screen active">

<div class="title">
Rose Run
</div>

<div class="subtitle">

준이는 오늘 잠이 오지 않았어요 ☁️

</div>

<div class="character">
🥺
</div>

<div class="storyBox">

“타올이 없으니까 잠이 안 와...”<br>
“엄마 보고 싶다...”

</div>

<button class="btn"
onclick="startStory()">

장미 전해주러 가기 🌹

</button>

</div>

<!-- 게임 -->

<div id="gameScreen"
class="screen">

<div id="score">
🌹 0
</div>

<div id="goal">
300까지 달리기
</div>

<div id="player">
🏃🏻🌹
</div>

<div id="ground"></div>

</div>

<!-- 성공 -->

<div id="clearScreen"
class="screen">

<div class="title"
style="font-size:42px;">

🌹 CLEAR 🌹

</div>

<div class="character">
😊
</div>

<div class="storyBox">

주희가 무사히 장미를 전달했어요 💗<br><br>

준이는 정말 행복해졌어요.<br><br>

“고마워...”<br>
“대신 소원권 10장 줄게.”

</div>

<div style="
margin-top:28px;
font-size:28px;
color:#d6909f;
">

🎫 소원권 x10 획득

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

<div class="character">
🥀
</div>

<div class="storyBox">

장미가 가시에 걸려버렸어요...<br><br>

준이는 아직 슬퍼보여요 ☁️

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

let score = 0;

let gameStarted = false;

let jumping = false;

let dead = false;

/* 화면 */

function showScreen(id){

document
.querySelectorAll(".screen")
.forEach(screen=>{

screen.classList.remove("active");

});

document
.getElementById(id)
.classList.add("active");

}

/* 시작 */

function startStory(){

showScreen("gameScreen");

gameStarted = true;

spawnObstacle();

spawnRose();

scoreLoop();

}

/* 점프 */

function jump(){

if(!gameStarted || jumping || dead)
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

clearInterval(loop);

gameStarted = false;

showScreen("clearScreen");

}

},100);

}

/* 장애물 */

function spawnObstacle(){

if(dead || !gameStarted)
return;

const thorn =
document.createElement("div");

thorn.classList.add("thorn");

thorn.innerHTML = "🪵";

gameScreen.appendChild(thorn);

let x = 430;

const move = setInterval(()=>{

if(dead){

clearInterval(move);

thorn.remove();

return;

}

x -= 7;

thorn.style.left = x + "px";

const playerY =
parseInt(player.style.bottom || 140);

if(
x < 120 &&
x > 40 &&
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
Math.random()*1200 + 1000
);

}

/* 장미 */

function spawnRose(){

if(dead || !gameStarted)
return;

const rose =
document.createElement("div");

rose.classList.add("rose");

rose.innerHTML = "🌹";

gameScreen.appendChild(rose);

let x = 430;

rose.style.left = x + "px";

const move = setInterval(()=>{

x -= 6;

rose.style.left = x + "px";

if(x < -100){

clearInterval(move);

rose.remove();

}

},20);

setTimeout(
spawnRose,
1200
);

}

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
