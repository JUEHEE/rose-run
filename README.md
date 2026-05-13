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
    --text-brown: #9a6f79;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: var(--bg-pink);
    display: flex; justify-content: center; align-items: center;
    height: 100vh; overflow: hidden;
  }

  /* 이미지 비율에 맞춘 컨테이너 (430x880 추정 비율) */
  #game-frame {
    width: 430px;
    height: 880px;
    max-width: 100vw;
    max-height: 100vh;
    background: #fff;
    position: relative;
    border: 10px solid #fff;
    outline: 2px solid var(--line-pink);
    border-radius: 40px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }

  /* 배경 장식 */
  .inner-bg {
    position: absolute;
    inset: 0;
    background: url("https://i.imgur.com/2iG9Z6X.png") center/cover; /* 원본 느낌 배경 */
    opacity: 0.15;
    z-index: 0;
  }

  /* 상단 UI 정보 */
  .hud {
    position: relative;
    z-index: 2;
    padding: 60px 20px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .rose-counter {
    background: #fff;
    border: 1.5px solid var(--line-pink);
    padding: 5px 15px;
    border-radius: 10px;
    color: var(--dark-pink);
    font-weight: bold;
    font-size: 14px;
  }

  .fever-bar {
    width: 150px;
    height: 18px;
    background: #fff;
    border: 1.5px solid var(--line-pink);
    border-radius: 10px;
    overflow: hidden;
  }

  .fever-fill {
    width: 40%;
    height: 100%;
    background: var(--main-pink);
  }

  /* 캐릭터 렌더링 영역 */
  .stage {
    flex: 1;
    position: relative;
    z-index: 1;
    display: flex;
    justify-content: center;
    align-items: flex-end;
    padding-bottom: 150px;
  }

  /* 직접 코드로 그린 준이 캐릭터 (SVG) */
  .character-svg {
    width: 180px;
    height: auto;
    filter: drop-shadow(2px 4px 0px rgba(228, 170, 183, 0.3));
    animation: run 0.6s infinite alternate ease-in-out;
  }

  @keyframes run {
    0% { transform: translateY(0) rotate(-2deg); }
    100% { transform: translateY(-10px) rotate(2deg); }
  }

  /* 하단 컨트롤 레이아웃 */
  .controls {
    position: relative;
    z-index: 2;
    padding: 30px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: rgba(255, 255, 255, 0.4);
    border-top: 2px dashed var(--line-pink);
  }

  .btn-round {
    width: 85px;
    height: 85px;
    background: #fff;
    border: 2px solid var(--line-pink);
    border-radius: 50%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    color: var(--dark-pink);
    font-weight: bold;
    font-size: 13px;
    box-shadow: 0 4px 0 var(--line-pink);
  }

  .btn-start {
    padding: 15px 40px;
    background: #fff;
    border: 2px solid var(--line-pink);
    border-radius: 15px;
    color: var(--dark-pink);
    font-size: 24px;
    font-weight: bold;
    box-shadow: 0 5px 0 var(--line-pink);
  }

</style>
</head>
<body>

<div id="game-frame">
  <div class="inner-bg"></div>
  
  <div class="hud">
    <div class="rose-counter">🌹 125 / 300</div>
    <div class="fever-bar"><div class="fever-fill"></div></div>
    <div style="color:var(--line-pink); font-size: 24px;">||</div>
  </div>

  <div class="stage">
    <!-- 이미지 속 소년 캐릭터를 SVG 코드로 재현 -->
    <svg class="character-svg" viewBox="0 0 100 120" xmlns="http://www.w3.org/2000/svg">
      <!-- 머리 -->
      <path d="M30 40 Q30 20 50 20 Q70 20 70 40 L70 55 Q50 60 30 55 Z" fill="#f5d5c5" stroke="#ba8a95" stroke-width="1.5"/>
      <!-- 머리카락 -->
      <path d="M30 45 Q25 35 35 25 Q45 15 55 20 Q65 15 75 30 Q75 45 65 50 Q50 48 30 45" fill="#e9c4b5" stroke="#ba8a95" stroke-width="1.5"/>
      <!-- 눈/볼 -->
      <circle cx="62" cy="42" r="2" fill="#5c4a44"/>
      <circle cx="68" cy="46" r="3" fill="#f9d5db" opacity="0.6"/>
      <!-- 몸통 (멜빵) -->
      <path d="M40 55 L65 55 L70 85 L35 85 Z" fill="#fff" stroke="#ba8a95" stroke-width="1.5"/>
      <rect x="42" y="55" width="3" height="30" fill="#e4aab7"/>
      <rect x="60" y="55" width="3" height="30" fill="#e4aab7"/>
      <!-- 팔/장미 -->
      <path d="M65 65 Q80 65 85 75" fill="none" stroke="#ba8a95" stroke-width="2"/>
      <text x="75" y="80" font-size="15">💐</text>
      <!-- 다리 -->
      <rect x="40" y="85" width="8" height="15" rx="2" fill="#f5d5c5" stroke="#ba8a95" stroke-width="1.5"/>
      <rect x="58" y="85" width="8" height="15" rx="2" fill="#f5d5c5" stroke="#ba8a95" stroke-width="1.5"/>
      <path d="M35 100 H50 V105 H35 Z" fill="#fff" stroke="#ba8a95" stroke-width="1"/>
      <path d="M55 100 H70 V105 H55 Z" fill="#fff" stroke="#ba8a95" stroke-width="1"/>
    </svg>
  </div>

  <div class="controls">
    <div class="btn-round">JUMP<br>🎀</div>
    <div class="btn-start">START</div>
    <div class="btn-round">SLIDE<br>🎀</div>
  </div>
</div>

</body>
</html>
