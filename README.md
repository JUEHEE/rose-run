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

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  
  body {
    background: var(--bg-pink);
    display: flex; justify-content: center; align-items: center;
    height: 100vh; overflow: hidden;
  }

  /* 전체 게임 프레임: 비율 최적화 */
  #game-frame {
    width: 100%;
    max-width: 430px;
    height: 95vh; /* 버튼이 안 잘리도록 여유 공간 확보 */
    background: #fff;
    position: relative;
    border: 8px solid #fff;
    outline: 2px solid var(--line-pink);
    border-radius: 40px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    box-shadow: 0 15px 30px rgba(0,0,0,0.1);
  }

  .inner-bg {
    position: absolute; inset: 0;
    background: radial-gradient(circle at center, #fff 0%, var(--bg-pink) 100%);
    opacity: 0.5; z-index: 0;
  }

  /* 상단 HUD */
  .hud {
    position: relative; z-index: 2;
    padding: 40px 20px 10px;
    display: flex; justify-content: space-between; align-items: center;
  }

  .rose-counter {
    background: #fff; border: 2px solid var(--line-pink);
    padding: 6px 15px; border-radius: 12px;
    color: var(--dark-pink); font-weight: bold; font-size: 14px;
    box-shadow: 0 2px 0 var(--line-pink);
  }

  .fever-container {
    width: 140px; height: 22px;
    background: #fff; border: 2px solid var(--line-pink);
    border-radius: 11px; padding: 2px;
  }

  .fever-fill {
    width: 60%; height: 100%;
    background: linear-gradient(to right, var(--main-pink), var(--dark-pink));
    border-radius: 8px;
  }

  /* 캐릭터 스테이지 */
  .stage {
    flex: 1; position: relative; z-index: 1;
    display: flex; justify-content: center; align-items: center;
  }

  /* 정교하게 다시 그린 준이 캐릭터 */
  .character-wrap {
    width: 220px; height: 300px;
    display: flex; justify-content: center; align-items: center;
    animation: bounce 0.8s infinite ease-in-out;
  }

  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-15px); }
  }

  /* 하단 컨트롤 레이아웃: 잘림 방지 */
  .controls {
    position: relative; z-index: 2;
    padding: 20px 25px 40px;
    display: flex; justify-content: space-between; align-items: center;
    gap: 15px;
  }

  .btn-circle {
    width: 75px; height: 75px;
    background: #fff; border: 2px solid var(--line-pink);
    border-radius: 50%;
    display: flex; flex-direction: column; justify-content: center; align-items: center;
    color: var(--dark-pink); font-weight: bold; font-size: 11px;
    box-shadow: 0 4px 0 var(--line-pink);
    cursor: pointer;
  }

  .btn-start {
    flex: 1; height: 65px;
    background: #fff; border: 2px solid var(--line-pink);
    border-radius: 18px;
    color: var(--dark-pink); font-size: 22px; font-weight: 800;
    box-shadow: 0 5px 0 var(--line-pink);
    cursor: pointer;
    display: flex; justify-content: center; align-items: center;
  }
</style>
</head>
<body>

<div id="game-frame">
  <div class="inner-bg"></div>
  
  <div class="hud">
    <div class="rose-counter">🌹 125 / 300</div>
    <div class="fever-container"><div class="fever-fill"></div></div>
    <div style="font-size: 22px; color: var(--dark-pink);">⏸</div>
  </div>

  <div class="stage">
    <div class="character-wrap">
      <!-- 픽셀 아트 준이를 정교하게 재현한 SVG -->
      <svg viewBox="0 0 100 120" xmlns="http://www.w3.org/2000/svg">
        <!-- 머리카락 (뒷머리) -->
        <path d="M25 45 Q20 30 35 20 Q50 10 65 20 Q80 30 75 45" fill="#e9c4b5" stroke="#9a6f79" stroke-width="1.2"/>
        <!-- 얼굴 -->
        <rect x="30" y="35" width="40" height="35" rx="10" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
        <!-- 머리카락 (앞머리) -->
        <path d="M30 40 Q35 30 45 35 Q55 30 65 35 Q70 40 70 45 L30 45 Z" fill="#e9c4b5" stroke="#9a6f79" stroke-width="1.2"/>
        <!-- 눈 -->
        <circle cx="62" cy="52" r="1.5" fill="#5c4a44"/>
        <!-- 홍조 -->
        <circle cx="66" cy="58" r="3" fill="#f9d5db" opacity="0.7"/>
        <!-- 몸통 (셔츠) -->
        <rect x="38" y="70" width="24" height="25" fill="#fff" stroke="#9a6f79" stroke-width="1.2"/>
        <!-- 멜빵바지 -->
        <path d="M38 82 H62 V95 H38 Z" fill="#f9d5db" stroke="#9a6f79" stroke-width="1.2"/>
        <rect x="42" y="70" width="3" height="12" fill="#d8909f"/>
        <rect x="55" y="70" width="3" height="12" fill="#d8909f"/>
        <!-- 팔과 꽃다발 -->
        <path d="M62 78 Q75 78 80 85" fill="none" stroke="#fbe3d6" stroke-width="4" stroke-linecap="round"/>
        <g transform="translate(75, 80) scale(0.6)">
            <circle cx="10" cy="10" r="8" fill="#ff99aa"/>
            <circle cx="15" cy="5" r="8" fill="#ffb3cc"/>
            <rect x="10" y="15" width="4" height="12" fill="#aacc99" transform="rotate(-20)"/>
        </g>
        <!-- 다리 -->
        <rect x="40" y="95" width="8" height="12" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
        <rect x="52" y="95" width="8" height="12" fill="#fbe3d6" stroke="#9a6f79" stroke-width="1.2"/>
        <!-- 신발 -->
        <path d="M36 107 H48 V112 H36 Z" fill="#fff" stroke="#9a6f79" stroke-width="1.2"/>
        <path d="M52 107 H64 V112 H52 Z" fill="#fff" stroke="#9a6f79" stroke-width="1.2"/>
      </svg>
    </div>
  </div>

  <div class="controls">
    <div class="btn-circle">JUMP<br>🎀</div>
    <div class="btn-start">START</div>
    <div class="btn-circle">SLIDE<br>🎀</div>
  </div>
</div>

</body>
</html>
