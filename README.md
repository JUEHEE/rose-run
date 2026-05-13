<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Rose Run 🌹 - Playable</title>
<style>
  :root {
    --bg-pink: #fcebed;
    --main-pink: #f9d5db;
    --dark-pink: #d8909f;
    --line-pink: #e4aab7;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  body { background: var(--bg-pink); display: flex; justify-content: center; align-items: center; height: 100vh; overflow: hidden; font-family: sans-serif; }

  #game-frame {
    width: 100%; max-width: 400px; height: 92vh;
    background: #fff; position: relative;
    border: 8px solid #fff; outline: 2px solid var(--line-pink);
    border-radius: 40px; overflow: hidden;
    display: flex; flex-direction: column;
  }

  /* HUD */
  .hud { position: relative; z-index: 10; padding: 40px 20px 10px; display: flex; justify-content: space-between; align-items: center; }
  .rose-counter { background: #fff; border: 2px solid var(--line-pink); padding: 6px 12px; border-radius: 12px; color: var(--dark-pink); font-weight: bold; font-size: 14px; }

  /* Stage & Game Logic */
  #stage { flex: 1; position: relative; background: linear-gradient(#fff, var(--bg-pink)); overflow: hidden; cursor: pointer; }
  
  /* 캐릭터 */
  #player {
    position: absolute; bottom: 50px; left: 50px;
    width: 80px; height: 100px; z-index: 5;
    transition: bottom 0.1s;
  }

  /* 가시 장애물 (이미지 속 동그란 가시 재현) */
  .obstacle {
    position: absolute; bottom: 50px; right: -50px;
    width: 50px; height: 50px;
    background: #9a6f79; border: 4px solid #7a5a61; border-radius: 50%;
    display: flex; justify-content: center; align-items: center;
  }
  .obstacle::before { content: '⚠️'; font-size: 20px; }

  /* 컨트롤러 */
  .controls { padding: 20px 25px 40px; display: flex; justify-content: space-between; align-items: center; gap: 10px; background: #fff; }
  .btn-circle { width: 70px; height: 70px; background: #fff; border: 2px solid var(--line-pink); border-radius: 50%; display: flex; flex-direction: column; justify-content: center; align-items: center; color: var(--dark-pink); font-weight: bold; font-size: 11px; box-shadow: 0 4px 0 var(--line-pink); cursor: pointer; }
  .btn-start { flex: 1; height: 60px; background: #fff; border: 2px solid var(--line-pink); border-radius: 15px; color: var(--dark-pink); font-size: 20px; font-weight: 800; box-shadow: 0 5px 0 var(--line-pink); cursor: pointer; display: flex; justify-content: center; align-items: center; }

  /* 상태 메시지 */
  #msg { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 100; font-size: 24px; color: var(--dark-pink); font-weight: bold; text-align: center; display: none; background: rgba(255,255,255,0.9); padding: 20px; border-radius: 20px; border: 2px solid var(--line-pink); }

  /* 점프 애니메이션 클래스 */
  .jumping { animation: jump-anim 0.6s forwards; }
  @keyframes jump-anim {
    0% { bottom: 50px; }
    50% { bottom: 180px; }
    100% { bottom: 50px; }
  }
</style>
</head>
<body>

<div id="game-frame">
  <div class="hud">
    <div class="rose-counter">🌹 Score: <span id="score">0</span></div>
    <div style="font-size: 22px; color: var(--dark-pink);">⏸</div>
  </div>

  <div id="stage" onclick="jump()">
    <div id="msg">GAME OVER<br><span style="font-size:14px">다시 하려면 START 클릭</span></div>
    
    <!-- 정교한 준이 캐릭터 -->
    <div id="player">
      <svg viewBox="0 0 100 120">
        <path d="M25 45 Q20 30 35 20 Q50 10 65 20 Q80 30 75 45" fill="#e9c4b5" stroke="#9a6f79" stroke-width="1.2"/>
        <rect x="30" y="35" width="40" height="35" rx="10" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
        <circle cx="62" cy="52" r="1.5" fill="#5c4a44"/>
        <rect x="38" y="70" width="24" height="25" fill="#fff" stroke="#9a6f79" stroke-width="1.2"/>
        <path d="M38 82 H62 V95 H38 Z" fill="#f9d5db" stroke="#9a6f79" stroke-width="1.2"/>
        <path d="M62 78 Q75 78 80 85" fill="none" stroke="#fbe3d6" stroke-width="4" stroke-linecap="round"/>
        <text x="75" y="90" font-size="12">🌹</text>
        <rect x="40" y="95" width="8" height="12" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
        <rect x="52" y="95" width="8" height="12" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
      </svg>
    </div>
  </div>

  <div class="controls">
    <div class="btn-circle" onclick="jump()">JUMP<br>🎀</div>
    <div class="btn-start" onclick="startGame()">START</div>
    <div class="btn-circle">SLIDE<br>🎀</div>
  </div>
</div>

<script>
  const player = document.getElementById('player');
  const stage = document.getElementById('stage');
  const scoreEl = document.getElementById('score');
  const msg = document.getElementById('msg');
  let score = 0;
  let isGameOver = true;
  let obstacleTimeout;

  function jump() {
    if (isGameOver) return;
    if (!player.classList.contains('jumping')) {
      player.classList.add('jumping');
      setTimeout(() => player.classList.remove('jumping'), 600);
    }
  }

  function createObstacle() {
    if (isGameOver) return;
    const obs = document.createElement('div');
    obs.classList.add('obstacle');
    stage.appendChild(obs);

    let pos = -50;
    const move = setInterval(() => {
      if (isGameOver) { clearInterval(move); obs.remove(); return; }
      pos += 5;
      obs.style.right = pos + 'px';

      // 충돌 체크
      const playerRect = player.getBoundingClientRect();
      const obsRect = obs.getBoundingClientRect();

      if (
        obsRect.left < playerRect.right &&
        obsRect.right > playerRect.left &&
        obsRect.top < playerRect.bottom &&
        playerRect.bottom > obsRect.top
      ) {
        gameOver();
      }

      if (pos > 450) {
        clearInterval(move);
        obs.remove();
        score++;
        scoreEl.innerText = score;
      }
    }, 20);

    obstacleTimeout = setTimeout(createObstacle, Math.random() * 2000 + 1000);
  }

  function startGame() {
    if (!isGameOver) return;
    isGameOver = false;
    score = 0;
    scoreEl.innerText = score;
    msg.style.display = 'none';
    createObstacle();
  }

  function gameOver() {
    isGameOver = true;
    msg.style.display = 'block';
    clearTimeout(obstacleTimeout);
  }
</script>
</body>
</html>
