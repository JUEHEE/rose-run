<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Rose Run 🌹 - Gesture Control</title>
<style>
  :root {
    --bg-pink: #fcebed;
    --main-pink: #f9d5db;
    --dark-pink: #d8909f;
    --line-pink: #e4aab7;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  body { background: var(--bg-pink); display: flex; justify-content: center; align-items: center; height: 100vh; overflow: hidden; touch-action: none; }

  #game-frame {
    width: 100%; max-width: 430px; height: 95vh;
    background: #fff; position: relative;
    border: 8px solid #fff; outline: 2px solid var(--line-pink);
    border-radius: 40px; overflow: hidden;
    display: flex; flex-direction: column;
  }

  /* 게임 화면 영역 */
  #stage { 
    flex: 1; position: relative; 
    background: linear-gradient(to bottom, #fff, var(--bg-pink)); 
    overflow: hidden; z-index: 1;
  }
  
  /* 안내 문구 UI */
  .guide-layer {
    position: absolute; bottom: 20px; width: 100%; text-align: center;
    color: var(--dark-pink); font-size: 14px; font-weight: bold; opacity: 0.6;
  }

  /* 캐릭터 */
  #player {
    position: absolute; bottom: 60px; left: 70px;
    width: 80px; height: 110px; z-index: 10;
    transition: transform 0.15s; transform-origin: bottom;
  }

  /* 가시 장애물 */
  .thorn {
    position: absolute; width: 45px; background: #9a6f79; 
    border: 2px solid #7a5a61; z-index: 5;
  }
  .thorn.bottom { bottom: 60px; border-radius: 20px 20px 5px 5px; }
  .thorn.top { top: 0; border-radius: 5px 5px 20px 20px; }

  /* 애니메이션 */
  .jumping { animation: jump-gesture 0.65s ease-out; }
  .sliding { transform: scaleY(0.55); } 

  @keyframes jump-gesture {
    0%, 100% { bottom: 60px; }
    50% { bottom: 230px; }
  }

  /* HUD */
  .hud { padding: 50px 25px 10px; display: flex; justify-content: space-between; align-items: center; z-index: 20; }
  .score-text { color: var(--dark-pink); font-size: 20px; font-weight: 900; }

  #overlay {
    position: absolute; inset: 0; background: rgba(255,255,255,0.8);
    display: flex; flex-direction: column; justify-content: center; align-items: center;
    z-index: 100; text-align: center;
  }
  .start-btn {
    margin-top: 20px; padding: 15px 40px; background: #fff;
    border: 2px solid var(--line-pink); border-radius: 20px;
    color: var(--dark-pink); font-size: 24px; font-weight: bold;
    box-shadow: 0 5px 0 var(--line-pink); cursor: pointer;
  }
</style>
</head>
<body>

<div id="game-frame">
  <div id="overlay">
    <h1 style="color:var(--dark-pink); font-size: 40px;">Rose Run</h1>
    <p style="color:#9a6f79; margin-top:10px;">탭: 점프 / 누르기: 슬라이드</p>
    <div class="start-btn" onclick="startGame()">START</div>
  </div>

  <div class="hud">
    <div class="score-text">🌹 <span id="score">0</span></div>
    <div style="font-size: 24px; color: var(--line-pink);">⏸</div>
  </div>

  <div id="stage">
    <div class="guide-layer">TAP TO JUMP / HOLD TO SLIDE</div>
    <div id="player">
      <svg viewBox="0 0 100 130">
        <!-- 모자 & 얼굴 -->
        <ellipse cx="50" cy="20" rx="30" ry="12" fill="#d8909f" stroke="#9a6f79"/>
        <rect x="30" y="30" width="40" height="40" rx="10" fill="#fbe3d6" stroke="#9a6f79"/>
        <circle cx="62" cy="48" r="1.5" fill="#5c4a44"/>
        <!-- 멜빵 치마 -->
        <path d="M32 70 L68 70 L78 105 H22 Z" fill="#f9d5db" stroke="#9a6f79"/>
        <rect x="40" y="70" width="3" height="15" fill="#d8909f"/>
        <rect x="57" y="70" width="3" height="15" fill="#d8909f"/>
        <!-- 꽃 & 다리 -->
        <text x="75" y="90" font-size="16">💐</text>
        <rect x="42" y="105" width="6" height="12" fill="#fbe3d6" stroke="#9a6f79"/>
        <rect x="52" y="105" width="6" height="12" fill="#fbe3d6" stroke="#9a6f79"/>
      </svg>
    </div>
  </div>
</div>

<script>
  const player = document.getElementById('player');
  const stage = document.getElementById('stage');
  const scoreEl = document.getElementById('score');
  const overlay = document.getElementById('overlay');
  
  let score = 0;
  let isGameOver = true;
  let gameLoop;

  // 조작 로직: 터치 시작(점프 또는 슬라이드 준비)
  stage.addEventListener('touchstart', (e) => {
    if (isGameOver) return;
    e.preventDefault();
    // 슬라이드 판정: 누르고 있으면 슬라이드
    player.dataset.pressStart = Date.now();
    player.classList.add('sliding');
  });

  // 터치 종료(슬라이드 해제 및 짧은 터치 시 점프)
  stage.addEventListener('touchend', (e) => {
    if (isGameOver) return;
    const pressDuration = Date.now() - player.dataset.pressStart;
    player.classList.remove('sliding');
    
    // 짧게 터치(200ms 미만)했을 때만 점프
    if (pressDuration < 200) {
      if (!player.classList.contains('jumping')) {
        player.classList.add('jumping');
        setTimeout(() => player.classList.remove('jumping'), 650);
      }
    }
  });

  function createObstacle() {
    if (isGameOver) return;
    const isTop = Math.random() > 0.5;
    const thorn = document.createElement('div');
    thorn.classList.add('thorn', isTop ? 'top' : 'bottom');
    stage.appendChild(thorn);

    let pos = -60;
    const height = Math.random() * 70 + 70;
    thorn.style.height = height + 'px';

    const move = setInterval(() => {
      if (isGameOver) { clearInterval(move); thorn.remove(); return; }
      pos += 6;
      thorn.style.right = pos + 'px';

      const p = player.getBoundingClientRect();
      const t = thorn.getBoundingClientRect();

      // 충돌 판정 (판정 범위를 살짝 줄여 쾌적하게 함)
      if (t.left + 10 < p.right - 10 && t.right - 10 > p.left + 10 && 
          t.top + 10 < p.bottom - 10 && t.bottom - 10 > p.top + 10) {
        endGame();
      }

      if (pos > 450) {
        clearInterval(move);
        thorn.remove();
        score++;
        scoreEl.innerText = score;
      }
    }, 20);

    gameLoop = setTimeout(createObstacle, Math.random() * 1500 + 1000);
  }

  function startGame() {
    isGameOver = false; score = 0; scoreEl.innerText = "0";
    overlay.style.display = 'none';
    createObstacle();
  }

  function endGame() {
    isGameOver = true;
    overlay.style.display = 'flex';
    overlay.querySelector('h1').innerText = "Game Over";
    overlay.querySelector('p').innerText = `최종 점수: ${score}점`;
    clearTimeout(gameLoop);
  }
</script>
</body>
</html>
