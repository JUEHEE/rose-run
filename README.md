<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Rose Run 🌹 - New Style</title>
<style>
  :root {
    --bg-pink: #fcebed;
    --main-pink: #f9d5db;
    --dark-pink: #d8909f;
    --line-pink: #e4aab7;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  body { background: var(--bg-pink); display: flex; justify-content: center; align-items: center; height: 100vh; overflow: hidden; }

  #game-frame {
    width: 100%; max-width: 400px; height: 92vh;
    background: #fff; position: relative;
    border: 8px solid #fff; outline: 2px solid var(--line-pink);
    border-radius: 40px; overflow: hidden;
    display: flex; flex-direction: column;
  }

  /* 게임 화면 */
  #stage { flex: 1; position: relative; background: linear-gradient(to bottom, #fff, var(--bg-pink)); overflow: hidden; }
  
  /* 캐릭터 스타일 */
  #player {
    position: absolute; bottom: 50px; left: 60px;
    width: 80px; height: 100px; z-index: 10;
    transition: transform 0.2s, height 0.2s;
    transform-origin: bottom;
  }

  /* 가시 덩굴 (바닥/천장) */
  .thorn {
    position: absolute; width: 40px; height: 0px; 
    background: #9a6f79; border: 2px solid #7a5a61;
    transition: height 0.3s ease-out; z-index: 5;
  }
  .thorn.bottom { bottom: 50px; border-radius: 20px 20px 0 0; }
  .thorn.top { top: 0; border-radius: 0 0 20px 20px; }

  /* 컨트롤러 */
  .controls { padding: 20px 25px 40px; display: flex; justify-content: space-between; align-items: center; background: #fff; z-index: 20; }
  .btn-circle { width: 75px; height: 75px; background: #fff; border: 2px solid var(--line-pink); border-radius: 50%; display: flex; flex-direction: column; justify-content: center; align-items: center; color: var(--dark-pink); font-weight: bold; font-size: 12px; box-shadow: 0 4px 0 var(--line-pink); cursor: pointer; }
  .btn-start { flex: 1; height: 60px; margin: 0 15px; background: #fff; border: 2px solid var(--line-pink); border-radius: 15px; color: var(--dark-pink); font-size: 22px; font-weight: 900; box-shadow: 0 5px 0 var(--line-pink); cursor: pointer; }

  /* 애니메이션 상태 */
  .jumping { animation: jump-action 0.7s forwards; }
  .sliding { transform: scaleY(0.5); } /* 슬라이드 시 키가 절반으로 */

  @keyframes jump-action {
    0%, 100% { bottom: 50px; }
    50% { bottom: 200px; }
  }

  #msg { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 100; text-align: center; display: none; background: #fff; padding: 25px; border-radius: 25px; border: 3px solid var(--line-pink); }
</style>
</head>
<body>

<div id="game-frame">
  <div style="padding: 40px 20px 10px; display: flex; justify-content: space-between;">
    <div style="color:var(--dark-pink); font-weight:bold;">Score: <span id="score">0</span></div>
  </div>

  <div id="stage">
    <div id="msg">
        <h2 style="color:var(--dark-pink);">GAME OVER</h2>
        <button onclick="startGame()" style="margin-top:10px; border:none; background:var(--main-pink); color:#fff; padding:8px 20px; border-radius:10px;">RETRY</button>
    </div>
    
    <div id="player">
      <svg viewBox="0 0 100 120">
        <!-- 귀여운 빵모자 -->
        <ellipse cx="50" cy="25" rx="35" ry="15" fill="#d8909f" stroke="#9a6f79" stroke-width="1"/>
        <circle cx="50" cy="15" r="4" fill="#fff" stroke="#d8909f" stroke-width="1"/>
        
        <!-- 얼굴 -->
        <rect x="30" y="35" width="40" height="35" rx="10" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
        <circle cx="62" cy="52" r="1.5" fill="#5c4a44"/>
        
        <!-- 멜빵 치마 (Skirt) -->
        <path d="M35 70 L65 70 L75 95 H25 Z" fill="#f9d5db" stroke="#9a6f79" stroke-width="1.2"/>
        <rect x="42" y="70" width="3" height="10" fill="#d8909f"/>
        <rect x="55" y="70" width="3" height="10" fill="#d8909f"/>
        
        <!-- 다리 & 꽃 -->
        <path d="M62 78 Q75 78 80 85" fill="none" stroke="#fbe3d6" stroke-width="4" stroke-linecap="round"/>
        <text x="75" y="90" font-size="14">💐</text>
        <rect x="42" y="95" width="6" height="10" fill="#fbe3d6"/>
        <rect x="52" y="95" width="6" height="10" fill="#fbe3d6"/>
      </svg>
    </div>
  </div>

  <div class="controls">
    <div class="btn-circle" onmousedown="jump()" ontouchstart="jump()">JUMP<br>🎀</div>
    <button class="btn-start" onclick="startGame()">START</button>
    <div class="btn-circle" onmousedown="startSlide()" onmouseup="stopSlide()" ontouchstart="startSlide()" ontouchend="stopSlide()">SLIDE<br>🎀</div>
  </div>
</div>

<script>
  const player = document.getElementById('player');
  const stage = document.getElementById('stage');
  const scoreEl = document.getElementById('score');
  const msg = document.getElementById('msg');
  let score = 0;
  let isGameOver = true;
  let gameTimer;

  function jump() {
    if (isGameOver || player.classList.contains('jumping') || player.classList.contains('sliding')) return;
    player.classList.add('jumping');
    setTimeout(() => player.classList.remove('jumping'), 700);
  }

  function startSlide() {
    if (isGameOver || player.classList.contains('jumping')) return;
    player.classList.add('sliding');
  }

  function stopSlide() {
    player.classList.remove('sliding');
  }

  function createThorn() {
    if (isGameOver) return;
    const isTop = Math.random() > 0.5; // 위/아래 랜덤 생성
    const thorn = document.createElement('div');
    thorn.classList.add('thorn', isTop ? 'top' : 'bottom');
    stage.appendChild(thorn);

    let pos = -50;
    // 가시가 슝슝 자라나는 효과
    setTimeout(() => { thorn.style.height = (Math.random() * 80 + 60) + 'px'; }, 10);

    const move = setInterval(() => {
      if (isGameOver) { clearInterval(move); thorn.remove(); return; }
      pos += 5;
      thorn.style.right = pos + 'px';

      const p = player.getBoundingClientRect();
      const t = thorn.getBoundingClientRect();

      // 충돌 판정
      if (t.left < p.right && t.right > p.left && t.top < p.bottom && t.bottom > p.top) {
        gameOver();
      }

      if (pos > 450) {
        clearInterval(move);
        thorn.remove();
        score++;
        scoreEl.innerText = score;
      }
    }, 20);

    gameTimer = setTimeout(createThorn, 1500);
  }

  function startGame() {
    isGameOver = false; score = 0; scoreEl.innerText = score;
    msg.style.display = 'none';
    document.querySelectorAll('.thorn').forEach(t => t.remove());
    createThorn();
  }

  function gameOver() {
    isGameOver = true; msg.style.display = 'block';
    clearTimeout(gameTimer);
  }
</script>
</body>
</html>
