<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>數學跑酷 Math Parkour</title>
<style>
  :root{
    --sky-top:#bfe3ff;
    --sky-mid:#d9ecff;
    --sky-warm:#ffe3ea;
    --panel:#fff7f0;
    --panel-edge:#ffd9c2;
    --accent-teal:#4fc9b8;
    --accent-coral:#ff9a76;
    --accent-yellow:#ffce54;
    --accent-purple:#a58bd6;
    --accent-red:#ff6f6f;
    --ink:#4a3f5c;
    --ink-soft:#8579a0;
    --coin:#ffb84d;
  }
  *{box-sizing:border-box; -webkit-tap-highlight-color: transparent;}
  html,body{
    margin:0; padding:0; width:100%; height:100%;
    background: linear-gradient(180deg, var(--sky-top), var(--sky-mid));
    font-family: "PingFang TC","Microsoft JhengHei","Heiti TC","Noto Sans TC",sans-serif;
    color:var(--ink);
    overflow:hidden;
    touch-action: manipulation;
    user-select:none;
  }
  #app{ width:100%; height:100vh; height:calc(var(--vh, 1vh) * 100); display:flex; align-items:center; justify-content:center; }
  .screen{ display:none; width:100%; max-width:760px; height:100%; height:calc(var(--vh, 1vh) * 100); max-height:960px; flex-direction:column; align-items:center; padding:14px; overflow-y:auto; -webkit-overflow-scrolling:touch; }
  .screen.active{ display:flex; }
  .screen::-webkit-scrollbar{ width:6px; }
  .screen::-webkit-scrollbar-thumb{ background:var(--panel-edge); border-radius:4px;}

  /* ---------- 標題 ---------- */
  .topbar{ width:100%; display:flex; justify-content:space-between; align-items:center; margin-bottom:6px;}
  .coin-badge{ background:linear-gradient(180deg,#fff3d6,#ffe0a3); border:2px solid var(--coin); border-radius:20px; padding:8px 16px; font-size:19px; font-weight:900; color:#8a5a00; box-shadow:0 3px 0 #e2a53d;}
  .title{
    font-size:clamp(30px,7.5vw,46px);
    font-weight:900;
    text-align:center;
    letter-spacing:2px;
    color:#6a4fc9;
    text-shadow: 2px 3px 0 #ffe3ea, -1px -1px 0 #fff;
    margin: 2px 0 0;
  }
  .subtitle{ font-size:15px; color:var(--ink-soft); margin-bottom:14px; text-align:center; letter-spacing:1px; font-weight:600;}

  .hero-scene{
    width:100%; height:150px; border-radius:20px; overflow:hidden; position:relative; margin-bottom:16px;
    background: linear-gradient(180deg, #cdeaff 0%, #ffe9d6 70%, #ffd7c2 100%);
    box-shadow: 0 8px 20px rgba(150,120,180,0.25), inset 0 0 0 3px #fff;
  }
  .hero-cloud{ position:absolute; background:#fff; border-radius:40px; opacity:0.9;}
  .hero-runner{position:absolute; bottom:10px; left:50%; transform:translateX(-50%); font-size:52px; filter:drop-shadow(0 6px 6px rgba(0,0,0,0.15)); animation: bob 0.5s infinite alternate ease-in-out;}
  @keyframes bob{ from{transform:translateX(-50%) translateY(0);} to{transform:translateX(-50%) translateY(-8px);} }

  /* ---------- 選項卡片 ---------- */
  .panel{ width:100%; background:var(--panel); border:2px solid var(--panel-edge); border-radius:18px; padding:14px 16px; margin-bottom:12px; box-shadow: 0 4px 0 var(--panel-edge);}
  .panel h3{ margin:0 0 10px; font-size:17px; color:var(--accent-purple); letter-spacing:1px; font-weight:900;}
  .choice-grid{ display:grid; grid-template-columns: repeat(3,1fr); gap:8px; }
  .choice-grid.op{ grid-template-columns: repeat(5,1fr); }
  .choice-btn{
    background:#fff; border:3px solid #f0e4ff; color:var(--ink);
    border-radius:14px; padding:12px 4px; font-size:15px; font-weight:800; cursor:pointer;
    transition: transform .12s ease, border-color .12s ease, background .12s ease;
    line-height:1.3;
  }
  .choice-btn small{ display:block; font-weight:600; font-size:11px; color:var(--ink-soft); margin-top:3px;}
  .choice-btn:active{ transform:scale(0.94); }
  .choice-btn.selected{ border-color:var(--accent-teal); background:#e2fbf5; box-shadow:0 0 0 3px rgba(79,201,184,0.25);}

  .loadout-row{ display:flex; align-items:center; gap:12px; flex-wrap:wrap; }
  .skin-preview-box{ width:64px; height:64px; border-radius:16px; background:#fff; border:3px solid var(--accent-teal); display:flex; align-items:center; justify-content:center; font-size:32px; flex-shrink:0;}
  .skin-name{ font-weight:800; font-size:16px;}
  .link-btn{ background:none; border:none; color:var(--accent-purple); font-weight:800; font-size:14px; text-decoration:underline; cursor:pointer; padding:6px 0;}
  .item-chip-row{ display:flex; gap:8px; flex-wrap:wrap; margin-top:8px;}
  .item-chip{ background:#fff; border:3px solid #f0e4ff; border-radius:14px; padding:8px 10px; font-size:13px; font-weight:700; cursor:pointer; text-align:center; min-width:64px;}
  .item-chip .qty{ font-size:11px; color:var(--ink-soft); font-weight:600;}
  .item-chip.equipped{ border-color:var(--accent-coral); background:#fff1ea; box-shadow:0 0 0 3px rgba(255,154,118,0.25);}
  .item-chip.disabled{ opacity:0.4; cursor:not-allowed;}

  .start-btn{
    margin-top:6px; width:100%; padding:18px; font-size:21px; font-weight:900; letter-spacing:2px;
    border:none; border-radius:18px; color:#5c3a00; cursor:pointer;
    background: linear-gradient(180deg, var(--accent-yellow), var(--coin));
    box-shadow: 0 6px 0 #d68a2c, 0 10px 18px rgba(0,0,0,0.12);
  }
  .start-btn:active{ transform: translateY(4px); box-shadow: 0 2px 0 #d68a2c;}
  .hint{ font-size:13px; color:var(--ink-soft); text-align:center; margin-top:10px; line-height:1.7; font-weight:600;}

  .shop-entry-btn{
    width:100%; padding:14px; font-size:17px; font-weight:900; border-radius:16px; border:2px solid var(--accent-purple);
    background:#f4edff; color:var(--accent-purple); cursor:pointer; margin-bottom:12px;
  }
  .shop-entry-btn:active{ transform:scale(0.97); }

  /* ---------- 遊戲畫面 ---------- */
  #screen-game{ padding:8px; }
  .hud{ width:100%; display:flex; align-items:center; justify-content:space-between; padding:4px 6px 8px; font-size:17px; font-weight:800;}
  .hud-left{ display:flex; align-items:center; gap:14px;}
  .lives{ font-size:19px; letter-spacing:2px;}
  .question-bar{
    width:100%; text-align:center; padding:14px 10px; border-radius:18px; margin-bottom:8px;
    background: linear-gradient(180deg, #fff, #fff3e6);
    border:3px solid var(--accent-yellow);
    box-shadow: 0 4px 0 #f0d090;
  }
  .question-text{ font-size:clamp(32px,9vw,54px); font-weight:900; color:#6a4fc9; text-shadow:1px 2px 0 #fff;}
  #gameCanvas{ width:100%; max-width:700px; aspect-ratio: 4/3; background:#cdeaff; border-radius:20px; box-shadow:0 8px 20px rgba(150,120,180,0.25); touch-action:none; }
  .controls{ display:flex; gap:16px; margin-top:10px; }
  .ctrl-btn{
    width:76px; height:62px; border-radius:16px; border:none; font-size:26px; font-weight:900; color:#3a5a52;
    background: linear-gradient(180deg, #a3ecdd, var(--accent-teal)); cursor:pointer;
    box-shadow: 0 5px 0 #2f9282;
  }
  .ctrl-btn:active{ transform:translateY(4px); box-shadow:0 1px 0 #2f9282;}

  /* ---------- 結束畫面 ---------- */
  .go-title{ font-size:34px; font-weight:900; color:var(--accent-red); margin-bottom:8px;}
  .go-score{ font-size:19px; margin-bottom:4px; color:var(--ink); font-weight:700;}
  .go-score b{ color:#d68a2c; font-size:26px;}
  .coin-gain{ font-size:18px; font-weight:900; color:#c9891f; background:#fff3d6; border-radius:14px; padding:8px 16px; margin:8px 0;}
  .history-panel{ width:100%; background:var(--panel); border:2px solid var(--panel-edge); border-radius:16px; padding:12px 14px; margin:10px 0; max-height:220px; overflow-y:auto;}
  .history-panel h3{ margin:2px 0 8px; font-size:16px; color:var(--accent-teal); font-weight:900;}
  table{ width:100%; border-collapse:collapse; font-size:14px;}
  th,td{ text-align:center; padding:7px 3px; border-bottom:1px solid var(--panel-edge);}
  th{ color:var(--ink-soft); font-weight:700;}
  tr.best td{ color:#d68a2c; font-weight:900;}
  .go-buttons{ width:100%; display:flex; gap:10px; margin-top:6px; position:sticky; bottom:-14px; padding:10px 0 14px; background:linear-gradient(180deg, rgba(191,227,255,0) 0%, var(--sky-mid) 35%); z-index:5;}
  .go-btn{ flex:1; padding:16px 6px; border-radius:16px; border:none; font-size:17px; font-weight:900; cursor:pointer;}
  .go-btn.continue{ background: linear-gradient(180deg, var(--accent-teal), #2f9282); color:#fff; box-shadow:0 5px 0 #1f6a5e;}
  .go-btn.back{ background: linear-gradient(180deg,#c9b8f0,#a58bd6); color:#fff; box-shadow:0 5px 0 #7a5fb0;}
  .go-btn:active{ transform:translateY(4px); box-shadow:none;}

  /* ---------- 商店畫面 ---------- */
  #screen-shop{ padding:10px; }
  .shop-tabs{ display:flex; gap:8px; width:100%; margin-bottom:12px;}
  .shop-tab{ flex:1; padding:12px; border-radius:14px; border:3px solid #f0e4ff; background:#fff; font-weight:900; font-size:16px; cursor:pointer; color:var(--ink-soft);}
  .shop-tab.active{ border-color:var(--accent-purple); background:#f4edff; color:var(--accent-purple);}
  .shop-grid{ width:100%; display:grid; grid-template-columns:1fr 1fr; gap:10px; margin-bottom:14px;}
  .shop-card{ background:var(--panel); border:2px solid var(--panel-edge); border-radius:16px; padding:12px; display:flex; flex-direction:column; align-items:center; text-align:center; box-shadow:0 3px 0 var(--panel-edge);}
  .shop-card .icon{ font-size:36px; margin-bottom:4px;}
  .shop-card .name{ font-weight:900; font-size:15px; margin-bottom:2px;}
  .shop-card .desc{ font-size:11.5px; color:var(--ink-soft); margin-bottom:8px; min-height:30px; font-weight:600;}
  .shop-card .price{ font-size:14px; font-weight:800; color:#c9891f; margin-bottom:8px;}
  .shop-buy{ width:100%; padding:9px; border-radius:12px; border:none; font-weight:900; font-size:13px; cursor:pointer; background:linear-gradient(180deg,var(--accent-yellow),var(--coin)); color:#5c3a00; box-shadow:0 3px 0 #d68a2c;}
  .shop-buy:active{ transform:translateY(3px); box-shadow:none;}
  .shop-buy.owned{ background:#d9f5ec; color:#2f9282; box-shadow:0 3px 0 #a3d9c9;}
  .shop-buy.equipped{ background:#ffd9c2; color:#c9581f; box-shadow:0 3px 0 #e2a37c;}
  .shop-buy:disabled{ opacity:0.5; cursor:not-allowed;}
  .back-btn{ width:100%; padding:14px; border-radius:16px; border:none; font-size:16px; font-weight:900; cursor:pointer; background:linear-gradient(180deg,#c9b8f0,#a58bd6); color:#fff; box-shadow:0 5px 0 #7a5fb0; margin-top:4px; position:sticky; bottom:-10px; z-index:5;}
  .back-btn:active{ transform:translateY(4px); box-shadow:none;}
  .save-note{ font-size:11.5px; color:var(--ink-soft); text-align:center; margin:6px 0 12px; line-height:1.6; font-weight:600;}

  /* ---------- 玩家名稱 ---------- */
  .player-badge{ display:flex; align-items:center; gap:8px; background:#fff; border:2px solid var(--accent-purple); border-radius:20px; padding:8px 14px 8px 10px; font-weight:900; font-size:16px; color:var(--accent-purple); cursor:pointer;}
  .player-badge .avatar{ font-size:20px;}
  #screen-players .panel{ margin-bottom:14px; }
  .player-list{ width:100%; display:flex; flex-direction:column; gap:10px; margin-bottom:6px;}
  .player-item{ display:flex; align-items:center; gap:12px; background:#fff; border:3px solid #f0e4ff; border-radius:16px; padding:12px 14px; cursor:pointer;}
  .player-item:active{ transform:scale(0.98); }
  .player-item .avatar2{ font-size:30px;}
  .player-item .info{ flex:1; text-align:left;}
  .player-item .pname{ font-weight:900; font-size:18px; color:var(--ink);}
  .player-item .pcoin{ font-size:13px; color:#c9891f; font-weight:700;}
  .player-item .del-btn{ background:#ffe3e3; border:none; border-radius:10px; padding:8px 10px; font-size:14px; cursor:pointer; color:#c94040; font-weight:800;}
  .new-player-row{ width:100%; display:flex; gap:8px; margin-top:6px;}
  .name-input{ flex:1; padding:14px; border-radius:14px; border:3px solid #f0e4ff; font-size:17px; font-weight:700; color:var(--ink); font-family:inherit;}
  .name-input:focus{ outline:none; border-color:var(--accent-teal); }
  .add-player-btn{ padding:14px 18px; border-radius:14px; border:none; font-size:16px; font-weight:900; cursor:pointer; background:linear-gradient(180deg,var(--accent-teal),#2f9282); color:#fff; box-shadow:0 4px 0 #1f6a5e;}
  .add-player-btn:active{ transform:translateY(3px); box-shadow:none;}
  .player-note{ font-size:12.5px; color:var(--ink-soft); text-align:center; margin-top:8px; font-weight:600; min-height:18px;}
</style>
</head>
<body>
<div id="app">

  <!-- ========== 開始畫面 ========== -->
  <div id="screen-start" class="screen active">
    <div class="topbar">
      <div class="player-badge" id="playerBadge"><span class="avatar">🙋</span><span id="playerNameLabel">玩家</span></div>
      <div class="coin-badge">🪙 <span id="coinTop">0</span></div>
    </div>
    <div class="hero-scene" id="heroScene">
      <div class="hero-runner">🏃</div>
    </div>
    <div class="title">數學跑酷</div>
    <div class="subtitle">MATH PARKOUR ・ 跳到正確跑道，一路過關斬將！</div>

    <div class="panel">
      <h3>👤 我的角色</h3>
      <div class="loadout-row">
        <div class="skin-preview-box" id="skinPreviewEmoji">🏃</div>
        <div>
          <div class="skin-name" id="skinNameLabel">運動男孩</div>
          <button class="link-btn" id="openShopFromStart">前往商店更換角色 / 購買道具 ›</button>
        </div>
      </div>
      <div class="item-chip-row" id="itemLoadoutRow"></div>
    </div>

    <div class="panel">
      <h3>① 選擇年級階段</h3>
      <div class="choice-grid" id="stageGrid">
        <button class="choice-btn" data-stage="low">低年級<small>個位數</small></button>
        <button class="choice-btn" data-stage="mid">中年級<small>個位＋十位</small></button>
        <button class="choice-btn" data-stage="high">高年級<small>十位數</small></button>
      </div>
    </div>

    <div class="panel">
      <h3>② 選擇運算類型</h3>
      <div class="choice-grid op" id="opGrid">
        <button class="choice-btn" data-op="add">加法<small>＋</small></button>
        <button class="choice-btn" data-op="sub">減法<small>－</small></button>
        <button class="choice-btn" data-op="mul">乘法<small>×</small></button>
        <button class="choice-btn" data-op="div">除法<small>÷</small></button>
        <button class="choice-btn" data-op="mix">綜合<small>混合</small></button>
      </div>
    </div>

    <button class="start-btn" id="btnStart">🚀 開始挑戰</button>
    <div class="hint">操作方式：← / → 方向鍵，或點畫面左／右半部、或使用下方按鈕切換跑道<br>跑到「正確答案」的跑道才能通過，答錯會扣血喔！答對愈多、速度會愈快！</div>
  </div>

  <!-- ========== 玩家選擇畫面 ========== -->
  <div id="screen-players" class="screen">
    <div class="title" style="font-size:clamp(24px,6vw,34px);">👋 是誰要來挑戰？</div>
    <div class="subtitle">最多可建立 10 位玩家，各自累積金幣與分數紀錄</div>
    <div class="panel">
      <h3>選擇玩家</h3>
      <div class="player-list" id="playerList"></div>
      <div class="new-player-row">
        <input type="text" class="name-input" id="newPlayerInput" placeholder="輸入新名字…" maxlength="8">
        <button class="add-player-btn" id="addPlayerBtn">➕ 新增</button>
      </div>
      <div class="player-note" id="playerNote"></div>
    </div>
  </div>

  <!-- ========== 遊戲畫面 ========== -->
  <div id="screen-game" class="screen">
    <div class="hud">
      <div class="hud-left">🏆 <span id="scoreVal">0</span></div>
      <div class="hud-left">🔥 <span id="comboVal">0</span></div>
      <div class="lives" id="livesVal">❤️❤️❤️</div>
    </div>
    <div class="question-bar"><span class="question-text" id="questionText">— </span></div>
    <div style="position:relative; width:100%; display:flex; justify-content:center;">
      <canvas id="gameCanvas" width="900" height="675"></canvas>
    </div>
    <div class="controls">
      <button class="ctrl-btn" id="btnLeft">⬅️</button>
      <button class="ctrl-btn" id="btnRight">➡️</button>
    </div>
  </div>

  <!-- ========== 結束畫面 ========== -->
  <div id="screen-gameover" class="screen">
    <div class="go-title">🎉 這次挑戰結束！</div>
    <div class="go-score">本次得分：<b id="finalScore">0</b> 分　最高連擊：<b id="finalCombo">0</b></div>
    <div class="coin-gain">🪙 獲得金幣 +<span id="coinGain">0</span></div>
    <div class="history-panel">
      <h3>📊 累積分數紀錄</h3>
      <table>
        <thead><tr><th>#</th><th>時間</th><th>年級</th><th>運算</th><th>分數</th><th>連擊</th></tr></thead>
        <tbody id="historyBody"></tbody>
      </table>
    </div>
    <div class="go-buttons">
      <button class="go-btn continue" id="btnContinue">🔄 繼續挑戰</button>
      <button class="go-btn back" id="btnBack">🔙 返回選單</button>
    </div>
  </div>

  <!-- ========== 商店畫面 ========== -->
  <div id="screen-shop" class="screen">
    <div class="topbar">
      <div style="font-size:22px;font-weight:900;color:var(--accent-purple);">🛍️ 商店</div>
      <div class="coin-badge">🪙 <span id="coinShop">0</span></div>
    </div>
    <div class="shop-tabs">
      <button class="shop-tab active" id="tabSkins">👤 角色</button>
      <button class="shop-tab" id="tabItems">🎒 道具</button>
    </div>
    <div class="shop-grid" id="shopGrid"></div>
    <div class="save-note">💾 資料會自動儲存在這台裝置的瀏覽器中；若在 Claude 預覽視窗內可能無法保存，建議把檔案下載後用瀏覽器開啟遊玩，才能長期累積金幣與紀錄。</div>
    <button class="back-btn" id="btnShopBack">🔙 返回</button>
  </div>

</div>

<script>
(function(){
  "use strict";

  /* ================= 修正手機瀏覽器（尤其 LINE／IG 等內建瀏覽器）100vh 計算不準的問題 ================= */
  /* 這類瀏覽器的網址列/工具列高度會被誤算進 100vh，導致畫面底部按鈕被擋住，需要靠 JS 抓真實高度來修正 */
  function setRealViewportHeight(){
    const vh = window.innerHeight * 0.01;
    document.documentElement.style.setProperty('--vh', vh + 'px');
  }
  setRealViewportHeight();
  window.addEventListener('resize', setRealViewportHeight);
  window.addEventListener('orientationchange', ()=>{ setTimeout(setRealViewportHeight, 200); });

  /* ================= 本機資料存取（含安全備援，支援最多 10 位玩家） ================= */
  const SAVE_KEY = 'mathParkourSaveV3';
  const MAX_PLAYERS = 10;
  let memoryFallback = null;
  function defaultProfile(name){
    return {
      name: name,
      coins: 0,
      ownedSkins: ['sporty'],
      equippedSkin: 'sporty',
      ownedItems: {},
      history: []
    };
  }
  function defaultSave(){ return { players:{}, lastPlayer:null }; }
  function loadSave(){
    try{
      const raw = localStorage.getItem(SAVE_KEY);
      if(raw){
        const parsed = JSON.parse(raw);
        if(parsed && parsed.players) return parsed;
      }
    }catch(e){ /* 略過，改用記憶體暫存 */ }
    return memoryFallback || defaultSave();
  }
  function persist(save){
    try{ localStorage.setItem(SAVE_KEY, JSON.stringify(save)); }
    catch(e){ memoryFallback = save; }
  }
  let SAVE = loadSave();
  function P(){ return SAVE.players[SAVE.lastPlayer]; }

  /* ================= 音效（Web Audio API，無需外部音檔） ================= */
  let actx = null;
  function ensureAudio(){ if(!actx){ try{ actx = new (window.AudioContext||window.webkitAudioContext)(); }catch(e){} } if(actx && actx.state==='suspended'){ actx.resume(); } }
  function beep(freq, dur, type, vol, delay){
    if(!actx) return;
    const t0 = actx.currentTime + (delay||0);
    const osc = actx.createOscillator();
    const gain = actx.createGain();
    osc.type = type || 'sine';
    osc.frequency.setValueAtTime(freq, t0);
    gain.gain.setValueAtTime(0, t0);
    gain.gain.linearRampToValueAtTime(vol!=null?vol:0.18, t0+0.02);
    gain.gain.exponentialRampToValueAtTime(0.001, t0+dur);
    osc.connect(gain); gain.connect(actx.destination);
    osc.start(t0); osc.stop(t0+dur+0.02);
  }
  function sndJump(){ beep(520,0.09,'square',0.12); }
  function sndCorrect(){ beep(660,0.1,'triangle',0.2); beep(880,0.14,'triangle',0.18,0.08); beep(1180,0.16,'triangle',0.16,0.16); }
  function sndWrong(){ beep(220,0.22,'sawtooth',0.2); beep(140,0.28,'sawtooth',0.18,0.08); }
  function sndShield(){ beep(760,0.12,'sine',0.2); beep(950,0.14,'sine',0.18,0.1); }
  function sndGameOver(){ beep(440,0.18,'sawtooth',0.2); beep(330,0.2,'sawtooth',0.18,0.16); beep(220,0.32,'sawtooth',0.18,0.32); }
  function sndClick(){ beep(760,0.06,'square',0.1); }
  function sndCoin(){ beep(988,0.08,'triangle',0.16); beep(1318,0.12,'triangle',0.14,0.06); }
  function sndBuy(){ beep(523,0.1,'triangle',0.18); beep(659,0.12,'triangle',0.16,0.08); beep(784,0.16,'triangle',0.14,0.16); }
  function sndMilestone(idx){
    const base = 560 + idx*70;
    beep(base,0.11,'triangle',0.22);
    beep(base*1.26,0.13,'triangle',0.2,0.09);
    beep(base*1.5,0.17,'triangle',0.2,0.18);
    if(idx>=2){ beep(base*2,0.2,'triangle',0.18,0.30); }
    if(idx>=4){ beep(base*2.5,0.22,'triangle',0.16,0.42); }
  }
  function sndWarning(){ beep(300,0.14,'square',0.2); beep(300,0.14,'square',0.2,0.2); }

  /* ================= 畫面切換 ================= */
  const screens = {
    start: document.getElementById('screen-start'),
    game: document.getElementById('screen-game'),
    gameover: document.getElementById('screen-gameover'),
    shop: document.getElementById('screen-shop'),
    players: document.getElementById('screen-players')
  };
  function showScreen(name){
    Object.values(screens).forEach(s=>s.classList.remove('active'));
    screens[name].classList.add('active');
    if(name==='start') refreshStartUI();
    if(name==='shop') renderShop();
    if(name==='players') renderPlayers();
  }

  /* ================= 玩家選擇 / 新增 ================= */
  function renderPlayers(){
    const list = document.getElementById('playerList');
    list.innerHTML = '';
    const names = Object.keys(SAVE.players);
    names.forEach(name=>{
      const prof = SAVE.players[name];
      const item = document.createElement('div');
      item.className = 'player-item';
      item.innerHTML = '<div class="avatar2">'+(SKINS[prof.equippedSkin]?SKINS[prof.equippedSkin].emoji:'🏃')+'</div>'+
        '<div class="info"><div class="pname">'+escapeHtml(name)+'</div><div class="pcoin">🪙 '+prof.coins+'　共挑戰 '+prof.history.length+' 次</div></div>'+
        '<button class="del-btn" data-del="'+escapeHtml(name)+'">刪除</button>';
      item.addEventListener('click', (e)=>{
        if(e.target.closest('.del-btn')) return;
        sndClick();
        SAVE.lastPlayer = name;
        persist(SAVE);
        showScreen('start');
      });
      list.appendChild(item);
    });
    list.querySelectorAll('[data-del]').forEach(btn=>{
      btn.addEventListener('click', (e)=>{
        e.stopPropagation();
        const name = btn.dataset.del;
        delete SAVE.players[name];
        if(SAVE.lastPlayer === name) SAVE.lastPlayer = null;
        persist(SAVE);
        renderPlayers();
      });
    });
    const note = document.getElementById('playerNote');
    note.textContent = names.length>=MAX_PLAYERS ? '已達 10 位玩家上限，請先刪除一位才能新增～' : '';
    document.getElementById('addPlayerBtn').disabled = names.length>=MAX_PLAYERS;
  }
  function escapeHtml(s){ const d=document.createElement('div'); d.textContent=s; return d.innerHTML; }

  document.getElementById('addPlayerBtn').addEventListener('click', ()=>{
    sndClick();
    const input = document.getElementById('newPlayerInput');
    const name = input.value.trim();
    const note = document.getElementById('playerNote');
    if(!name){ note.textContent = '請先輸入名字喔！'; return; }
    const names = Object.keys(SAVE.players);
    if(!names.includes(name) && names.length>=MAX_PLAYERS){ note.textContent = '已達 10 位玩家上限，請先刪除一位才能新增～'; return; }
    if(!SAVE.players[name]) SAVE.players[name] = defaultProfile(name);
    SAVE.lastPlayer = name;
    persist(SAVE);
    input.value = '';
    showScreen('start');
  });
  document.getElementById('newPlayerInput').addEventListener('keydown', (e)=>{
    if(e.key === 'Enter') document.getElementById('addPlayerBtn').click();
  });
  document.getElementById('playerBadge').addEventListener('click', ()=>{ sndClick(); showScreen('players'); });

  /* ================= 角色與道具設定 ================= */
  const SKINS = {
    sporty:  { name:'運動男孩', cost:0,   emoji:'🏃', hair:'#4a3222', jacket:'#4fc9b8', trim:'#ffffff', pants:'#3a5a6b', accessory:'cap',       skinTone:'#ffd9b0' },
    street:  { name:'潮流少年', cost:150, emoji:'🧢', hair:'#2b2b2b', jacket:'#a58bd6', trim:'#ffce54', pants:'#2b2b3a', accessory:'chain',     skinTone:'#f0c090' },
    esports: { name:'電競戰神', cost:300, emoji:'🎧', hair:'#1f5bd6', jacket:'#2b3a55', trim:'#4fe0ff', pants:'#1a1f30', accessory:'headset',   skinTone:'#ffdcb8' },
    ninja:   { name:'暗夜忍者', cost:500, emoji:'🥷', hair:'#111111', jacket:'#2a1030', trim:'#ff5c5c', pants:'#111111', accessory:'mask',      skinTone:'#e8b98a' },
    pilot:   { name:'王牌飛行員', cost:800, emoji:'🕶️', hair:'#7a4a20', jacket:'#c9891f', trim:'#fff3d6', pants:'#5c3a10', accessory:'goggles',   skinTone:'#ffdcb8' }
  };
  const ITEMS = {
    life:      { name:'❤️ 生命+1',      desc:'開局多一條命，多一次犯錯機會', cost:80,  icon:'❤️' },
    shield:    { name:'🛡️ 護盾一次',    desc:'第一次答錯不扣血、不斷連擊',   cost:150, icon:'🛡️' },
    bonus15:   { name:'💰 分數+15%',    desc:'本局所有得分乘以 1.15 倍',     cost:120, icon:'💰' },
    bonus30:   { name:'💎 分數+30%',    desc:'本局所有得分乘以 1.30 倍',     cost:250, icon:'💎' },
    stabilizer:{ name:'🐢 穩定器',      desc:'加速幅度變小，跑道更好掌握',   cost:100, icon:'🐢' },
    hint:      { name:'🔍 提示燈',      desc:'每題剛出現時，正確跑道會發光', cost:200, icon:'🔍' }
  };

  function ownedItemCount(id){ return P().ownedItems[id] || 0; }

  /* 本局出戰道具（每次進入遊戲畫面前可切換，開始時消耗 1 個） */
  let equippedForRun = {};

  function refreshStartUI(){
    if(!SAVE.lastPlayer || !SAVE.players[SAVE.lastPlayer]){ showScreen('players'); return; }
    document.getElementById('playerNameLabel').textContent = P().name;
    document.getElementById('coinTop').textContent = P().coins;
    const sk = SKINS[P().equippedSkin];
    document.getElementById('skinPreviewEmoji').textContent = sk.emoji;
    document.getElementById('skinNameLabel').textContent = sk.name;

    const row = document.getElementById('itemLoadoutRow');
    row.innerHTML = '';
    Object.keys(ITEMS).forEach(id=>{
      const qty = ownedItemCount(id);
      const chip = document.createElement('div');
      chip.className = 'item-chip' + (equippedForRun[id] ? ' equipped':'') + (qty<=0?' disabled':'');
      chip.innerHTML = ITEMS[id].icon+' '+ITEMS[id].name.replace(/^\S+\s/,'')+'<div class="qty">庫存 '+qty+'</div>';
      if(qty>0){
        chip.addEventListener('click', ()=>{
          sndClick();
          equippedForRun[id] = !equippedForRun[id];
          refreshStartUI();
        });
      }
      row.appendChild(chip);
    });
  }

  document.getElementById('openShopFromStart').addEventListener('click', ()=>{ sndClick(); showScreen('shop'); });
  document.getElementById('btnShopBack').addEventListener('click', ()=>{ sndClick(); showScreen('start'); });

  /* ================= 商店邏輯 ================= */
  let shopTab = 'skins';
  document.getElementById('tabSkins').addEventListener('click', ()=>{ sndClick(); shopTab='skins'; renderShop(); });
  document.getElementById('tabItems').addEventListener('click', ()=>{ sndClick(); shopTab='items'; renderShop(); });

  function renderShop(){
    const prof = P();
    document.getElementById('coinShop').textContent = prof.coins;
    document.getElementById('tabSkins').classList.toggle('active', shopTab==='skins');
    document.getElementById('tabItems').classList.toggle('active', shopTab==='items');
    const grid = document.getElementById('shopGrid');
    grid.innerHTML = '';

    if(shopTab==='skins'){
      Object.keys(SKINS).forEach(id=>{
        const s = SKINS[id];
        const owned = prof.ownedSkins.includes(id);
        const equipped = prof.equippedSkin === id;
        const card = document.createElement('div');
        card.className = 'shop-card';
        let btnHtml;
        if(equipped) btnHtml = '<button class="shop-buy equipped" disabled>✅ 使用中</button>';
        else if(owned) btnHtml = '<button class="shop-buy owned" data-equip="'+id+'">裝備角色</button>';
        else btnHtml = '<button class="shop-buy" data-buyskin="'+id+'" '+(prof.coins<s.cost?'disabled':'')+'>購買 🪙'+s.cost+'</button>';
        card.innerHTML = '<div class="icon">'+s.emoji+'</div><div class="name">'+s.name+'</div><div class="desc">'+(owned?'已解鎖角色造型':'解鎖專屬造型與配色')+'</div>'+(owned?'':'<div class="price">🪙 '+s.cost+'</div>')+btnHtml;
        grid.appendChild(card);
      });
    } else {
      Object.keys(ITEMS).forEach(id=>{
        const it = ITEMS[id];
        const qty = ownedItemCount(id);
        const card = document.createElement('div');
        card.className = 'shop-card';
        card.innerHTML = '<div class="icon">'+it.icon+'</div><div class="name">'+it.name.replace(/^\S+\s/,'')+'</div><div class="desc">'+it.desc+'</div><div class="price">🪙 '+it.cost+'　庫存 '+qty+'</div><button class="shop-buy" data-buyitem="'+id+'" '+(prof.coins<it.cost?'disabled':'')+'>購買 +1</button>';
        grid.appendChild(card);
      });
    }

    grid.querySelectorAll('[data-buyskin]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        const id = btn.dataset.buyskin;
        const s = SKINS[id];
        if(prof.coins >= s.cost){
          prof.coins -= s.cost;
          prof.ownedSkins.push(id);
          prof.equippedSkin = id;
          persist(SAVE);
          sndBuy();
          renderShop();
        }
      });
    });
    grid.querySelectorAll('[data-equip]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        prof.equippedSkin = btn.dataset.equip;
        persist(SAVE);
        sndClick();
        renderShop();
      });
    });
    grid.querySelectorAll('[data-buyitem]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        const id = btn.dataset.buyitem;
        const it = ITEMS[id];
        if(prof.coins >= it.cost){
          prof.coins -= it.cost;
          prof.ownedItems[id] = (prof.ownedItems[id]||0) + 1;
          persist(SAVE);
          sndBuy();
          renderShop();
        }
      });
    });
  }

  /* ================= 開始畫面：年級 / 運算選擇 ================= */
  let selStage = 'low';
  let selOp = 'mix';
  const stageGrid = document.getElementById('stageGrid');
  const opGrid = document.getElementById('opGrid');
  stageGrid.querySelectorAll('.choice-btn').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      ensureAudio(); sndClick();
      selStage = btn.dataset.stage;
      stageGrid.querySelectorAll('.choice-btn').forEach(b=>b.classList.remove('selected'));
      btn.classList.add('selected');
    });
  });
  opGrid.querySelectorAll('.choice-btn').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      ensureAudio(); sndClick();
      selOp = btn.dataset.op;
      opGrid.querySelectorAll('.choice-btn').forEach(b=>b.classList.remove('selected'));
      btn.classList.add('selected');
    });
  });
  stageGrid.querySelector('[data-stage="low"]').classList.add('selected');
  opGrid.querySelector('[data-op="mix"]').classList.add('selected');

  const STAGE_LABEL = { low:'低年級', mid:'中年級', high:'高年級' };
  const OP_LABEL = { add:'加法', sub:'減法', mul:'乘法', div:'除法', mix:'綜合' };

  /* ================= 出題邏輯 ================= */
  function randInt(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }
  function shuffle(arr){ for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; } return arr; }
  function pickOp(op){ if(op !== 'mix') return op; const ops=['add','sub','mul','div']; return ops[randInt(0,3)]; }

  // 依需求：低年級＝個位數；中年級＝個位數與十位數混合；高年級＝十位數（兩位數）
  function genQuestion(stage, opSel){
    const op = pickOp(opSel);
    let a,b,correct,text,symbol;

    if(op === 'add'){
      symbol = '＋';
      if(stage==='low'){
        // 40% 機率出現「湊十」題型：3 + ? = 10
        if(Math.random() < 0.4){
          a = randInt(1,9);
          const missing = 10-a;
          text = a+' ＋ ? ＝ 10';
          correct = missing;
          const opts = buildOptions(correct, 1, 9);
          return { text, correct, values:opts.values, correctLane:opts.correctLane };
        }
        a=randInt(1,9); b=randInt(1,9);
      } else if(stage==='mid'){
        a = mixDigit(); b = mixDigit();
      } else { // high：兩位數
        a=randInt(10,99); b=randInt(10,99);
      }
      correct = a+b;
      text = a+' ＋ '+b+' ＝ ?';
    } else if(op === 'sub'){
      symbol = '－';
      if(stage==='low'){ a=randInt(1,9); b=randInt(0,a); }
      else if(stage==='mid'){ a = mixDigit(); b = randInt(0, a); }
      else { a=randInt(10,99); b=randInt(10,a); }
      correct = a-b;
      text = a+' － '+b+' ＝ ?';
    } else if(op === 'mul'){
      symbol = '×';
      if(stage==='low'){ a=randInt(1,9); b=randInt(1,9); }
      else if(stage==='mid'){
        // 個位數與十位數混合：一個一位數、一個兩位數
        a=randInt(10,50); b=randInt(2,9);
      } else { a=randInt(10,30); b=randInt(10,20); }
      correct = a*b;
      text = a+' × '+b+' ＝ ?';
    } else { // div
      symbol = '÷';
      if(stage==='low'){ b=randInt(1,9); const q=randInt(1,9); a=b*q; }
      else if(stage==='mid'){ b=randInt(2,9); const q=randInt(2,20); a=b*q; }
      else { b=randInt(10,20); const q=randInt(2,9); a=b*q; }
      correct = a/b;
      text = a+' ÷ '+b+' ＝ ?';
    }

    const opts = buildOptions(correct);
    return { text, correct, values:opts.values, correctLane:opts.correctLane };
  }

  function mixDigit(){
    // 隨機回傳一位數或兩位數，達到「個位數與十位數混合」
    return Math.random() < 0.5 ? randInt(1,9) : randInt(10,99);
  }

  function buildOptions(correct, minV, maxV){
    minV = (minV==null)?0:minV;
    const scale = Math.max(1, Math.round(correct*0.15));
    const deltaPool = shuffle([1,2,3,-1,-2,-3,5,-5,scale,-scale,scale+2,-(scale+2)]);
    const vals = new Set([correct]);
    for(let i=0;i<deltaPool.length && vals.size<3;i++){
      let v = correct + deltaPool[i];
      if(v < minV) v = correct + Math.abs(deltaPool[i]) + 1;
      if(maxV!=null && v>maxV) v = correct - Math.abs(deltaPool[i]) - 1;
      if(!vals.has(v) && v>=minV && (maxV==null||v<=maxV)) vals.add(v);
    }
    while(vals.size<3){
      let v = correct + randInt(-8,8);
      if(v>=minV && (maxV==null||v<=maxV) && !vals.has(v)) vals.add(v);
    }
    const values = shuffle(Array.from(vals));
    const correctLane = values.indexOf(correct);
    return { values, correctLane };
  }

  /* ================= 遊戲場景常數（向前跑透視） ================= */
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');
  const CW = canvas.width, CH = canvas.height;
  const HORIZON_Y = CH*0.30;
  const NEAR_Y = CH*0.90;
  const VANISH_X = CW*0.5;
  const HORIZON_LANE_DX = CW*0.045;   // 地平線處三跑道很窄
  const NEAR_LANE_DX = CW*0.235;      // 靠近玩家時三跑道展開
  const PLAYER_Y = CH*0.86;
  const Z_MAX = 1;                    // 以 0~1 代表深度進度（0=遠端剛出現, 1=抵達玩家）

  function laneX(lane, t){
    const hx = VANISH_X + (lane-1)*HORIZON_LANE_DX;
    const nx = VANISH_X + (lane-1)*NEAR_LANE_DX;
    return hx + (nx-hx)*t;
  }
  function depthY(t){
    const te = Math.pow(t, 1.9); // 越靠近玩家移動越快，符合透視感
    return HORIZON_Y + (NEAR_Y-HORIZON_Y)*te;
  }
  function depthScale(t){
    const te = Math.pow(t, 1.9);
    return 0.5 + te*1.35;
  }

  /* ================= 遊戲狀態 ================= */
  let game = null;
  let rafId = null;

  function newGame(stage, op, loadout){
    const extraLife = loadout.life ? 1:0;
    return {
      stage, op, loadout,
      score:0, combo:0, maxCombo:0,
      lives:3+extraLife,
      shields: loadout.shield?1:0,
      scoreMult: 1 * (loadout.bonus15?1.15:1) * (loadout.bonus30?1.30:1),
      lane:1,
      playerX: laneX(1,1), playerXTarget: laneX(1,1),
      speed: (stage==='low'?0.0038:(stage==='mid'?0.0044:0.0050)),
      baseSpeed: (stage==='low'?0.0038:(stage==='mid'?0.0044:0.0050)),
      targetSpeed: (stage==='low'?0.0038:(stage==='mid'?0.0044:0.0050)),
      maxSpeed: (stage==='low'?0.0075:(stage==='mid'?0.0085:0.0095)),
      recentResults: [],
      tile: null, // {t, values, correctLane, resolved}
      cooldown: 20,
      hintActive:false, hintTimer:0,
      runCycle:0,
      shake:0,
      flashKind:null, flashT:0,
      popups:[],
      particles:[],
      bigMessage:null,
      lowLifeWarned:false,
      cloudOffset:0,
      treeOffset:0,
      lastTs:0,
      running:true
    };
  }

  function spawnQuestionTiles(g){
    const q = genQuestion(g.stage, g.op);
    document.getElementById('questionText').textContent = q.text;
    g.tile = { t:0.02, values:q.values, correctLane:q.correctLane, resolved:false };
    if(g.loadout.hint){ g.hintActive = true; g.hintTimer = 60; }
  }

  function updateHUD(g){
    document.getElementById('scoreVal').textContent = g.score;
    document.getElementById('comboVal').textContent = g.combo;
    const heartCount = Math.max(g.lives,0);
    document.getElementById('livesVal').textContent = '❤️'.repeat(heartCount) + '🖤'.repeat(Math.max(0,3-heartCount) + (heartCount>3?0:0));
  }

  function addPopup(g, text, color){
    g.popups.push({ text, color, x: g.playerX, y: PLAYER_Y-70, life:1 });
  }

  /* ---- 連擊里程碑特效：不同連擊數給不同文字、顏色與音效 ---- */
  const MILESTONES = [
    { n:3,  text:'不錯喔！👍',        color:'#4fc9b8', particles:12 },
    { n:5,  text:'Great! 太棒了！🌟', color:'#ffce54', particles:18 },
    { n:8,  text:'Amazing! 好厲害！🎉', color:'#ff9a76', particles:24 },
    { n:12, text:'Fantastic! 神扣連發！🚀', color:'#a58bd6', particles:30 },
    { n:16, text:'無人能敵！！🔥🔥', color:'#ff6f6f', particles:36 },
    { n:20, text:'數學小天才！！👑', color:'#ffd700', particles:44 }
  ];
  function checkMilestone(g){
    const idx = MILESTONES.findIndex(m=>m.n===g.combo);
    if(idx<0) return;
    const m = MILESTONES[idx];
    g.bigMessage = { text:m.text, color:m.color, life:1 };
    spawnParticles(g, m.particles, m.color);
    sndMilestone(idx);
  }
  function spawnParticles(g, count, color){
    for(let i=0;i<count;i++){
      const ang = Math.random()*Math.PI*2;
      const spd = 2.5+Math.random()*5;
      g.particles.push({ x:CW/2, y:CH*0.32, vx:Math.cos(ang)*spd, vy:Math.sin(ang)*spd-3, life:1, color, size:5+Math.random()*5 });
    }
  }

  function saveHistoryAndCoins(g){
    const prof = P();
    const coinGain = Math.floor(g.score/10) + g.maxCombo*2;
    prof.coins += coinGain;
    prof.history.push({
      time: new Date().toLocaleString('zh-TW',{month:'2-digit',day:'2-digit',hour:'2-digit',minute:'2-digit'}),
      stage: STAGE_LABEL[g.stage], op: OP_LABEL[g.op], score: g.score, combo: g.maxCombo
    });
    if(prof.history.length>30) prof.history = prof.history.slice(-30);
    persist(SAVE);
    return coinGain;
  }

  function endGame(g){
    g.running = false;
    cancelAnimationFrame(rafId);
    sndGameOver();
    const coinGain = saveHistoryAndCoins(g);
    document.getElementById('finalScore').textContent = g.score;
    document.getElementById('finalCombo').textContent = g.maxCombo;
    document.getElementById('coinGain').textContent = coinGain;
    renderHistory();
    showScreen('gameover');
  }

  function renderHistory(){
    const hist = P().history;
    const bestScore = hist.length ? Math.max.apply(null, hist.map(h=>h.score)) : -1;
    const tbody = document.getElementById('historyBody');
    tbody.innerHTML = '';
    hist.slice().reverse().forEach((h, idx)=>{
      const tr = document.createElement('tr');
      if(h.score === bestScore) tr.className = 'best';
      tr.innerHTML = '<td>'+(hist.length-idx)+'</td><td>'+h.time+'</td><td>'+h.stage+'</td><td>'+h.op+'</td><td>'+h.score+'</td><td>'+h.combo+'</td>';
      tbody.appendChild(tr);
    });
  }

  function setLane(g, dir){
    let nl = g.lane + dir;
    if(nl<0) nl=0; if(nl>2) nl=2;
    if(nl !== g.lane){
      g.lane = nl;
      sndJump();
    }
  }

  /* ================= 繪圖：柔和色系 + 向前跑透視 ================= */
  function drawBackground(g){
    const sky = ctx.createLinearGradient(0,0,0,HORIZON_Y+40);
    sky.addColorStop(0,'#bfe3ff');
    sky.addColorStop(0.6,'#d9ecff');
    sky.addColorStop(1,'#ffe9d6');
    ctx.fillStyle = sky;
    ctx.fillRect(0,0,CW,HORIZON_Y+40);

    // 雲朵
    ctx.fillStyle = 'rgba(255,255,255,0.85)';
    for(let i=0;i<4;i++){
      const cx = ((i*260 + g.cloudOffset) % (CW+200)) - 100;
      const cy = 50 + (i%2)*40;
      drawCloud(cx,cy, 44+i*6);
    }

    // 太陽
    const sunGrad = ctx.createRadialGradient(CW*0.82,90,4,CW*0.82,90,50);
    sunGrad.addColorStop(0,'#fff6d6');
    sunGrad.addColorStop(0.6,'#ffce54');
    sunGrad.addColorStop(1,'rgba(255,206,84,0)');
    ctx.fillStyle = sunGrad;
    ctx.beginPath(); ctx.arc(CW*0.82,90,50,0,Math.PI*2); ctx.fill();

    // 草地（左右）
    ctx.fillStyle = '#c9ecc9';
    ctx.fillRect(0, HORIZON_Y, CW, NEAR_Y-HORIZON_Y+40);

    // 道路（梯形透視）
    const roadHalfNear = NEAR_LANE_DX*1.55;
    const roadHalfFar = HORIZON_LANE_DX*1.55;
    ctx.fillStyle = '#e4defb';
    ctx.beginPath();
    ctx.moveTo(VANISH_X-roadHalfFar, HORIZON_Y);
    ctx.lineTo(VANISH_X+roadHalfFar, HORIZON_Y);
    ctx.lineTo(VANISH_X+roadHalfNear, NEAR_Y+50);
    ctx.lineTo(VANISH_X-roadHalfNear, NEAR_Y+50);
    ctx.closePath();
    ctx.fill();

    // 跑道分隔線（兩條，分出三線道）
    ctx.strokeStyle = 'rgba(255,255,255,0.8)';
    ctx.lineWidth = 3;
    ctx.setLineDash([16,14]);
    [0.33,0.67].forEach(f=>{
      const fx = VANISH_X-roadHalfFar + f*roadHalfFar*2;
      const nx = VANISH_X-roadHalfNear + f*roadHalfNear*2;
      ctx.beginPath(); ctx.moveTo(fx,HORIZON_Y); ctx.lineTo(nx,NEAR_Y+50); ctx.stroke();
    });
    ctx.setLineDash([]);

    // 路旁小樹（純裝飾，隨深度縮放製造前進感）
    for(let i=0;i<6;i++){
      const tt = ((g.treeOffset*0.6 + i*0.34) % 1.15);
      if(tt>1) continue;
      const side = i%2===0 ? -1: 1;
      const tx = laneX(side<0? -0.55: 2.55, tt);
      const ty = depthY(tt);
      const s = depthScale(tt);
      drawTree(tx,ty,s);
    }
  }

  function drawCloud(cx,cy,s){
    ctx.beginPath();
    ctx.arc(cx,cy,s*0.5,0,Math.PI*2);
    ctx.arc(cx+s*0.5,cy+6,s*0.4,0,Math.PI*2);
    ctx.arc(cx-s*0.5,cy+8,s*0.38,0,Math.PI*2);
    ctx.fill();
  }

  function drawTree(x,y,s){
    ctx.save();
    ctx.translate(x,y);
    ctx.scale(s,s);
    ctx.fillStyle = '#a9754a';
    ctx.fillRect(-4,-6,8,26);
    ctx.fillStyle = '#8fd6a0';
    ctx.beginPath(); ctx.arc(0,-24,20,0,Math.PI*2); ctx.fill();
    ctx.beginPath(); ctx.arc(-14,-12,14,0,Math.PI*2); ctx.fill();
    ctx.beginPath(); ctx.arc(14,-12,14,0,Math.PI*2); ctx.fill();
    ctx.restore();
  }

  function roundRect(ctx,x,y,w,h,r){
    ctx.beginPath();
    ctx.moveTo(x+r,y);
    ctx.arcTo(x+w,y,x+w,y+h,r);
    ctx.arcTo(x+w,y+h,x,y+h,r);
    ctx.arcTo(x,y+h,x,y,r);
    ctx.arcTo(x,y,x+w,y,r);
    ctx.closePath();
  }

  function drawTile(g){
    const tile = g.tile;
    if(!tile) return;
    tile.values.forEach((val, lane)=>{
      const t = tile.t;
      const x = laneX(lane, t);
      const y = depthY(t);
      const s = depthScale(t);
      const isCorrect = lane === tile.correctLane;
      const glow = g.hintActive && isCorrect;
      ctx.save();
      ctx.translate(x,y);
      ctx.scale(s,s);
      const w=150,h=100,r=20;
      if(glow){
        ctx.shadowColor = '#ffce54';
        ctx.shadowBlur = 30;
      }
      ctx.fillStyle = tile.resolved ? (isCorrect? '#c9f5e6':'#ffd9d9') : '#ffffff';
      ctx.strokeStyle = glow ? '#ffce54' : '#e6dcff';
      ctx.lineWidth = glow? 6:4;
      roundRect(ctx,-w/2,-h/2,w,h,r);
      ctx.fill(); ctx.stroke();
      ctx.shadowBlur = 0;
      ctx.fillStyle = '#4a3f5c';
      ctx.font = '900 62px sans-serif';
      ctx.textAlign='center'; ctx.textBaseline='middle';
      ctx.fillText(val, 0, 4);
      ctx.restore();
    });
  }

  /* ---- 角色繪製（依裝備造型變化） ---- */
  function drawPlayer(g){
    const cfg = SKINS[P().equippedSkin];
    const legPhase = Math.sin(g.runCycle);
    const bounce = Math.abs(Math.cos(g.runCycle))*6;
    const x = g.playerX, y = PLAYER_Y - bounce;
    ctx.save();
    ctx.translate(x,y);
    const SC = 1.9; // 人物放大倍率
    ctx.scale(SC,SC);

    // 影子
    ctx.fillStyle = 'rgba(80,60,100,0.25)';
    ctx.beginPath(); ctx.ellipse(0,40+bounce/2,26,8,0,0,Math.PI*2); ctx.fill();

    // 腳（面向鏡頭，交叉擺動）
    ctx.strokeStyle = cfg.pants; ctx.lineWidth=10; ctx.lineCap='round';
    ctx.beginPath(); ctx.moveTo(-8,14); ctx.lineTo(-8-legPhase*10, 36); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(8,14); ctx.lineTo(8+legPhase*10, 36); ctx.stroke();
    // 鞋子
    ctx.fillStyle = '#fff';
    ctx.beginPath(); ctx.ellipse(-8-legPhase*10,38,7,4,0,0,Math.PI*2); ctx.fill();
    ctx.beginPath(); ctx.ellipse(8+legPhase*10,38,7,4,0,0,Math.PI*2); ctx.fill();

    // 手臂
    ctx.strokeStyle = cfg.jacket; ctx.lineWidth=9;
    ctx.beginPath(); ctx.moveTo(-13,-6); ctx.lineTo(-13+legPhase*12,16); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(13,-6); ctx.lineTo(13-legPhase*12,16); ctx.stroke();

    // 身體（外套）
    ctx.fillStyle = cfg.jacket;
    roundRect(ctx,-15,-22,30,32,10); ctx.fill();
    ctx.fillStyle = cfg.trim;
    ctx.fillRect(-15,-6,30,5); // 條紋

    // 頭
    ctx.fillStyle = cfg.skinTone;
    ctx.beginPath(); ctx.arc(0,-34,15,0,Math.PI*2); ctx.fill();

    // 髮型
    ctx.fillStyle = cfg.hair;
    ctx.beginPath();
    ctx.moveTo(-15,-38);
    ctx.quadraticCurveTo(0,-58,15,-38);
    ctx.quadraticCurveTo(10,-46,0,-42);
    ctx.quadraticCurveTo(-10,-46,-15,-38);
    ctx.fill();

    // 配件（依角色不同）
    drawAccessory(cfg.accessory, cfg);

    ctx.restore();
  }

  function drawAccessory(type, cfg){
    ctx.save();
    if(type==='cap'){
      ctx.fillStyle = cfg.trim;
      ctx.beginPath(); ctx.arc(0,-40,15,Math.PI,Math.PI*2); ctx.fill();
      ctx.fillRect(-15,-40,30,4);
    } else if(type==='chain'){
      ctx.strokeStyle = '#ffce54'; ctx.lineWidth=2.5;
      ctx.beginPath(); ctx.moveTo(-8,-10); ctx.quadraticCurveTo(0,-2,8,-10); ctx.stroke();
      ctx.fillStyle='#333'; ctx.fillRect(-10,-42,20,4); // 反戴帽簷
    } else if(type==='headset'){
      ctx.strokeStyle = '#2b3a55'; ctx.lineWidth=4;
      ctx.beginPath(); ctx.arc(0,-38,17,Math.PI*1.1,Math.PI*1.9); ctx.stroke();
      ctx.fillStyle = '#4fe0ff';
      ctx.beginPath(); ctx.arc(-15,-32,5,0,Math.PI*2); ctx.fill();
      ctx.beginPath(); ctx.arc(15,-32,5,0,Math.PI*2); ctx.fill();
    } else if(type==='mask'){
      ctx.fillStyle = '#111';
      roundRect(ctx,-15,-32,30,14,6); ctx.fill();
      ctx.fillStyle = '#ff5c5c';
      ctx.fillRect(-15,-44,30,5); // 頭巾
    } else if(type==='goggles'){
      ctx.fillStyle = '#fff3d6';
      ctx.beginPath(); ctx.ellipse(0,-36,14,6,0,0,Math.PI*2); ctx.fill();
      ctx.strokeStyle = '#c9891f'; ctx.lineWidth=2; ctx.stroke();
    }
    // 太陽眼鏡（大部分造型都戴，增加帥氣感）
    if(type!=='mask'){
      ctx.fillStyle = 'rgba(40,30,50,0.85)';
      roundRect(ctx,-13,-35,26,8,4); ctx.fill();
    }
    ctx.restore();
  }

  function drawPopups(g){
    ctx.textAlign='center';
    g.popups.forEach(p=>{
      ctx.globalAlpha = Math.max(p.life,0);
      ctx.fillStyle = p.color;
      ctx.font = '900 30px sans-serif';
      ctx.fillText(p.text, p.x, p.y - (1-p.life)*46);
      ctx.globalAlpha = 1;
    });
  }

  function drawParticles(g){
    g.particles.forEach(p=>{
      ctx.globalAlpha = Math.max(p.life,0);
      ctx.fillStyle = p.color;
      ctx.beginPath(); ctx.arc(p.x,p.y,p.size,0,Math.PI*2); ctx.fill();
      ctx.globalAlpha = 1;
    });
  }

  function drawBigMessage(g){
    if(!g.bigMessage) return;
    const m = g.bigMessage;
    const pop = Math.min(1,(1-m.life)*6); // 快速放大進場
    const scale = 0.6 + pop*0.5 - (1-m.life)*0.05;
    ctx.save();
    ctx.globalAlpha = Math.min(1, m.life*2);
    ctx.translate(CW/2, CH*0.34);
    ctx.scale(scale,scale);
    ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.font = '900 46px sans-serif';
    ctx.lineWidth = 8; ctx.strokeStyle='#ffffff';
    ctx.strokeText(m.text,0,0);
    ctx.fillStyle = m.color;
    ctx.fillText(m.text,0,0);
    ctx.restore();
    ctx.globalAlpha = 1;
  }

  function drawLowLifeVignette(g){
    if(g.lives!==1) return;
    const pulse = 0.18 + Math.abs(Math.sin(g.runCycle*0.6))*0.16;
    const grad = ctx.createRadialGradient(CW/2,CH/2,CH*0.28,CW/2,CH/2,CH*0.62);
    grad.addColorStop(0,'rgba(255,111,111,0)');
    grad.addColorStop(1,'rgba(255,90,90,'+pulse+')');
    ctx.fillStyle = grad;
    ctx.fillRect(0,0,CW,CH);
  }

  /* ================= 遊戲主迴圈 ================= */
  const RESOLVE_T = 1.0;   // 方塊真正抵達玩家跑道時才判定，避免提前誤判
  const REMOVE_T = 1.08;   // 判定後很快移除，縮短「看起來還沒到卻已經判定」的落差
  const FLASH_COLORS = { correct:[79,201,184], shield:[165,139,214], wrong:[255,111,111] };

  function step(g, dt){
    if(!g.running) return;

    g.runCycle += 0.24*dt;
    g.cloudOffset += 0.15*dt;
    g.treeOffset += g.speed*10*dt;

    // 玩家左右平滑移動（依時間縮放，畫面更新速率不同也一樣順暢）
    g.playerXTarget = laneX(g.lane,1);
    g.playerX += (g.playerXTarget - g.playerX)*Math.min(1, 0.25*dt);

    // 速度朝目標值平滑靠近
    g.speed += (g.targetSpeed - g.speed)*Math.min(1, 0.06*dt);

    if(g.hintTimer>0){ g.hintTimer -= dt; if(g.hintTimer<=0){ g.hintTimer=0; g.hintActive=false; } }

    if(g.tile){
      g.tile.t += g.speed*dt;
      if(!g.tile.resolved && g.tile.t >= RESOLVE_T){
        g.tile.resolved = true;
        resolveAnswer(g);
      }
      if(g.tile.t >= REMOVE_T){
        g.tile = null;
        g.cooldown = 26;
      }
    } else if(g.cooldown>0){
      g.cooldown -= dt;
    } else {
      spawnQuestionTiles(g);
    }

    g.popups.forEach(p=> p.life -= 0.025*dt);
    g.popups = g.popups.filter(p=>p.life>0);

    g.particles.forEach(p=>{ p.vy += 0.12*dt; p.x += p.vx*dt; p.y += p.vy*dt; p.life -= 0.018*dt; });
    g.particles = g.particles.filter(p=>p.life>0);

    if(g.bigMessage){
      g.bigMessage.life -= 0.012*dt;
      if(g.bigMessage.life<=0) g.bigMessage = null;
    }

    if(g.shake>0) g.shake *= Math.pow(0.85, dt);
    if(g.flashT>0) g.flashT -= 0.045*dt;
  }

  function resolveAnswer(g){
    const correct = (g.lane === g.tile.correctLane);
    g.recentResults.push(correct);
    if(g.recentResults.length>5) g.recentResults.shift();

    if(correct){
      g.combo++;
      g.maxCombo = Math.max(g.maxCombo, g.combo);
      const bonus = Math.round((10 + Math.floor(g.combo/3)*5) * g.scoreMult);
      g.score += bonus;
      sndCorrect();
      addPopup(g, '+'+bonus+(g.combo>1?' 🔥x'+g.combo:''), '#2f9282');
      g.flashKind = 'correct'; g.flashT = 1;
      checkMilestone(g);
    } else {
      if(g.shields>0){
        g.shields--;
        sndShield();
        addPopup(g, '🛡️ 護盾抵擋！', '#a58bd6');
        g.flashKind = 'shield'; g.flashT = 1;
      } else {
        g.combo = 0;
        g.lives--;
        sndWrong();
        addPopup(g, '✗ 答錯了', '#ff6f6f');
        g.flashKind = 'wrong'; g.flashT = 1;
        g.shake = 7;
        if(g.lives===1 && !g.lowLifeWarned){
          g.lowLifeWarned = true;
          sndWarning();
          addPopup(g, '⚠️ 最後一條命！', '#ff6f6f');
        }
      }
    }
    updateHUD(g);

    // 依最近正確率自適應調整速度目標
    if(g.recentResults.length>=3){
      const acc = g.recentResults.filter(Boolean).length / g.recentResults.length;
      const rampStep = g.loadout.stabilizer ? 0.00018 : 0.00035;
      if(acc>=0.8){ g.targetSpeed = Math.min(g.targetSpeed+rampStep, g.maxSpeed); }
      else if(acc<=0.4){ g.targetSpeed = Math.max(g.targetSpeed-rampStep*0.8, g.baseSpeed*0.85); }
    }

    if(g.lives<=0){
      setTimeout(()=>{ if(game===g) endGame(g); }, 260);
    }
  }

  /* 柔和的邊緣暈影特效，取代整片刺眼的滿版色塊 */
  function drawFlashVignette(g){
    if(g.flashT<=0 || !g.flashKind) return;
    const c = FLASH_COLORS[g.flashKind];
    if(!c) return;
    const alpha = Math.max(g.flashT,0) * 0.22;
    const grad = ctx.createRadialGradient(CW/2,CH*0.5,CH*0.22,CW/2,CH*0.5,CH*0.68);
    grad.addColorStop(0, 'rgba('+c[0]+','+c[1]+','+c[2]+',0)');
    grad.addColorStop(1, 'rgba('+c[0]+','+c[1]+','+c[2]+','+alpha+')');
    ctx.fillStyle = grad;
    ctx.fillRect(0,0,CW,CH);
  }

  function render(g){
    ctx.save();
    if(g.shake>0.3){
      ctx.translate((Math.random()-0.5)*g.shake, (Math.random()-0.5)*g.shake);
    }
    drawBackground(g);
    drawTile(g);
    drawPlayer(g);
    drawPopups(g);
    drawParticles(g);
    drawLowLifeVignette(g);
    drawFlashVignette(g);
    ctx.restore();
    drawBigMessage(g);
  }

  function loop(ts){
    if(!game || !game.running) return;
    if(!game.lastTs) game.lastTs = ts;
    let deltaMs = ts - game.lastTs;
    game.lastTs = ts;
    let dt = deltaMs/16.6667;
    if(!isFinite(dt) || dt<=0) dt = 1;
    dt = Math.min(dt, 3); // 避免切換分頁回來時一次跳太多
    step(game, dt);
    if(game.running){
      render(game);
      rafId = requestAnimationFrame(loop);
    }
  }

  /* ================= 操控 ================= */
  document.addEventListener('keydown', (e)=>{
    if(!game || !game.running) return;
    if(e.key === 'ArrowLeft'){ setLane(game,-1); }
    else if(e.key === 'ArrowRight'){ setLane(game,1); }
  });
  document.getElementById('btnLeft').addEventListener('click', ()=>{ if(game&&game.running) setLane(game,-1); });
  document.getElementById('btnRight').addEventListener('click', ()=>{ if(game&&game.running) setLane(game,1); });
  canvas.addEventListener('pointerdown', (e)=>{
    if(!game || !game.running) return;
    const rect = canvas.getBoundingClientRect();
    const relX = (e.clientX - rect.left) / rect.width;
    if(relX < 0.5) setLane(game,-1); else setLane(game,1);
  });

  /* ================= 開始 / 重玩 / 返回 ================= */
  function startGame(){
    ensureAudio();
    const loadout = Object.assign({}, equippedForRun);
    const prof = P();
    // 消耗本局裝備的道具庫存
    Object.keys(loadout).forEach(id=>{
      if(loadout[id] && ownedItemCount(id)>0){
        prof.ownedItems[id] -= 1;
      } else {
        loadout[id] = false;
      }
    });
    persist(SAVE);
    equippedForRun = {};

    game = newGame(selStage, selOp, loadout);
    document.getElementById('questionText').textContent = '準備開始…';
    updateHUD(game);
    showScreen('game');
    cancelAnimationFrame(rafId);
    rafId = requestAnimationFrame(loop);
  }

  document.getElementById('btnStart').addEventListener('click', ()=>{ sndClick(); startGame(); });
  document.getElementById('btnContinue').addEventListener('click', ()=>{ sndClick(); startGame(); });
  document.getElementById('btnBack').addEventListener('click', ()=>{ sndClick(); showScreen('start'); });

  /* ================= 初始化 ================= */
  refreshStartUI();
})();
</script>
</body>
</html>

