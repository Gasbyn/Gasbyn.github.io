<!DOCTYPE html>
<html lang="cs">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gasbyn</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    html, body {
      width: 100%;
      min-height: 100vh;
      font-family: Arial, sans-serif;
      background: #0f0f0f;
      color: #fff;
    }

    .wrapper {
      width: 100%;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      padding: 0 0 80px 0;
    }

    /* TABS */
    .tabs {
      display: flex;
      gap: 0;
      background: #111;
      border-bottom: 2px solid #333;
      padding: 0 24px;
      position: sticky;
      top: 0;
      z-index: 10;
    }
    .tab-btn {
      padding: 16px 28px;
      border: none;
      border-bottom: 3px solid transparent;
      background: transparent;
      color: #888;
      font-size: 15px;
      font-weight: 600;
      cursor: pointer;
      transition: color 0.2s, border-color 0.2s;
      margin-bottom: -2px;
    }
    .tab-btn:hover { color: #fff; }
    .tab-btn.active { color: #fff; border-bottom-color: #53FC18; }

    /* PAGES */
    .page { display: none; padding: 40px 24px 24px; }
    .page.active { display: block; }

    /* HERO */
    .hero { text-align: center; padding: 40px 16px 32px; }
    .hero h1 { font-size: clamp(32px, 6vw, 60px); font-weight: 800; letter-spacing: -1px; margin-bottom: 10px; }
    .hero p { font-size: clamp(14px, 2vw, 18px); color: #aaa; }

    hr { border: none; border-top: 1px solid #222; margin: 24px 0; }

    /* CARDS */
    .cards {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 16px;
      margin: 24px 0;
    }
    .card {
      background: #1a1a1a;
      border: 1px solid #2a2a2a;
      border-radius: 14px;
      padding: 20px;
      transition: border-color 0.2s;
    }
    .card:hover { border-color: #444; }
    .card-icon { font-size: 28px; margin-bottom: 10px; }
    .card-title { font-size: 16px; font-weight: 700; margin-bottom: 6px; }
    .card-desc { font-size: 13px; color: #888; line-height: 1.5; }

    /* SOCIAL */
    .social {
      display: flex;
      gap: 12px;
      justify-content: center;
      flex-wrap: wrap;
      margin: 24px 0;
    }
    .btn {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      padding: 14px 28px;
      border-radius: 10px;
      font-size: 15px;
      font-weight: 700;
      cursor: pointer;
      text-decoration: none;
      transition: transform 0.15s, opacity 0.15s;
    }
    .btn:hover { transform: scale(1.05); }
    .btn:active { transform: scale(0.97); }
    .btn.kick { background: #53FC18; color: #000; }
    .btn.youtube { background: #FF0000; color: #fff; }
    .btn.twitch { background: #9146FF; color: #fff; }

    /* GRANULKY */
    .granulky-header { margin-bottom: 20px; }
    .granulky-header h2 { font-size: clamp(20px, 4vw, 28px); font-weight: 800; margin-bottom: 6px; }
    .granulky-header p { font-size: 14px; color: #888; }

    .command-list {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 10px;
    }
    .command-item {
      background: #1a1a1a;
      border: 1px solid #2a2a2a;
      border-radius: 12px;
      padding: 14px 18px;
      display: flex;
      align-items: center;
      gap: 14px;
      cursor: pointer;
      transition: border-color 0.2s, transform 0.1s;
      user-select: none;
    }
    .command-item:hover { border-color: #53FC18; background: #1e1e1e; }
    .command-item:active { transform: scale(0.97); }
    .command-name {
      background: #53FC18;
      color: #000;
      font-size: 13px;
      font-weight: 800;
      padding: 5px 12px;
      border-radius: 6px;
      white-space: nowrap;
      min-width: 100px;
      text-align: center;
      flex-shrink: 0;
    }
    .command-desc {
      font-size: 13px;
      color: #bbb;
      line-height: 1.4;
    }

    .footer { text-align: center; font-size: 12px; color: #444; padding: 24px 0 8px; }

    /* TOAST — bottom panel */
    #toast {
      position: fixed;
      bottom: 0; left: 0; right: 0;
      background: #161616;
      border-top: 2px solid #53FC18;
      padding: 14px 24px;
      display: flex;
      align-items: center;
      gap: 14px;
      transform: translateY(100%);
      transition: transform 0.3s cubic-bezier(0.34, 1.3, 0.64, 1);
      pointer-events: none;
      z-index: 999;
    }
    #toast.show { transform: translateY(0); }
    #toast .t-icon { font-size: 24px; flex-shrink: 0; }
    #toast .t-label { font-size: 11px; color: #888; margin-bottom: 2px; text-transform: uppercase; letter-spacing: 0.5px; }
    #toast .t-main { font-size: 16px; font-weight: 800; color: #53FC18; }

    /* RESPONSIVE */
    @media (max-width: 500px) {
      .tabs { padding: 0 12px; }
      .tab-btn { padding: 14px 16px; font-size: 14px; }
      .page { padding: 24px 16px; }
      .btn { padding: 12px 20px; font-size: 14px; width: 100%; justify-content: center; }
      .social { flex-direction: column; align-items: stretch; }
      .command-list { grid-template-columns: 1fr; }
    }

    @media (min-width: 1200px) {
      .cards { grid-template-columns: repeat(3, 1fr); }
      .command-list { grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); }
    }

    @media (min-width: 1800px) {
      .hero h1 { font-size: 72px; }
      .btn { padding: 18px 36px; font-size: 18px; }
      .command-item { padding: 18px 22px; }
      .command-name { font-size: 15px; min-width: 130px; }
      .command-desc { font-size: 15px; }
      #toast .t-main { font-size: 20px; }
    }
  </style>
</head>
<body>
<div class="wrapper">

  <div class="tabs">
    <button class="tab-btn active" onclick="switchTab('home', this)">🏠 Domů</button>
    <button class="tab-btn" onclick="switchTab('granulky', this)">🐾 Granulky</button>
  </div>

  <!-- HOME -->
  <div id="home" class="page active">
    <div class="hero">
      <h1>🎮 Gasbyn</h1>
      <p>Fortnite streamer • Kick • YouTube • Twitch</p>
    </div>

    <hr>

    <div class="cards">
      <div class="card">
        <div class="card-icon">🎯</div>
        <p class="card-title">Fortnite</p>
        <p class="card-desc">Custom games a viewer battles každý den</p>
      </div>
      <div class="card">
        <div class="card-icon">📡</div>
        <p class="card-title">Streamy</p>
        <p class="card-desc">Téměř každý den live na Kick</p>
      </div>
      <div class="card">
        <div class="card-icon">📺</div>
        <p class="card-title">YouTube</p>
        <p class="card-desc">Týdenní videa ve vertikálním formátu</p>
      </div>
    </div>

    <hr>

    <div class="social">
      <a class="btn kick" href="https://kick.com/gasbyn" target="_blank" onclick="showToast('🟢','Otevírá se Kick...','kick.com/gasbyn')">🟢 Kick</a>
      <a class="btn youtube" href="https://youtube.com/@gasbyn" target="_blank" onclick="showToast('▶','Otevírá se YouTube...','youtube.com/@gasbyn')">▶ YouTube</a>
      <a class="btn twitch" href="https://twitch.tv/gasbyn59" target="_blank" onclick="showToast('💜','Otevírá se Twitch...','twitch.tv/gasbyn59')">💜 Twitch</a>
    </div>

    <div class="footer">gasbyn • kick.com/gasbyn</div>
  </div>

  <!-- GRANULKY -->
  <div id="granulky" class="page">
    <div class="granulky-header">
      <h2>🐾 Granulky — Kick příkazy</h2>
      <p>Klikni na příkaz — zkopíruje se do schránky</p>
    </div>

    <div class="command-list">
      <div class="command-item" onclick="copyCmd('!příkaz1', this)"><span class="command-name">!příkaz1</span><span class="command-desc">Popis příkazu 1 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz2', this)"><span class="command-name">!příkaz2</span><span class="command-desc">Popis příkazu 2 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz3', this)"><span class="command-name">!příkaz3</span><span class="command-desc">Popis příkazu 3 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz4', this)"><span class="command-name">!příkaz4</span><span class="command-desc">Popis příkazu 4 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz5', this)"><span class="command-name">!příkaz5</span><span class="command-desc">Popis příkazu 5 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz6', this)"><span class="command-name">!příkaz6</span><span class="command-desc">Popis příkazu 6 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz7', this)"><span class="command-name">!příkaz7</span><span class="command-desc">Popis příkazu 7 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz8', this)"><span class="command-name">!příkaz8</span><span class="command-desc">Popis příkazu 8 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz9', this)"><span class="command-name">!příkaz9</span><span class="command-desc">Popis příkazu 9 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz10', this)"><span class="command-name">!příkaz10</span><span class="command-desc">Popis příkazu 10 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz11', this)"><span class="command-name">!příkaz11</span><span class="command-desc">Popis příkazu 11 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz12', this)"><span class="command-name">!příkaz12</span><span class="command-desc">Popis příkazu 12 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz13', this)"><span class="command-name">!příkaz13</span><span class="command-desc">Popis příkazu 13 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz14', this)"><span class="command-name">!příkaz14</span><span class="command-desc">Popis příkazu 14 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz15', this)"><span class="command-name">!příkaz15</span><span class="command-desc">Popis příkazu 15 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz16', this)"><span class="command-name">!příkaz16</span><span class="command-desc">Popis příkazu 16 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz17', this)"><span class="command-name">!příkaz17</span><span class="command-desc">Popis příkazu 17 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz18', this)"><span class="command-name">!příkaz18</span><span class="command-desc">Popis příkazu 18 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz19', this)"><span class="command-name">!příkaz19</span><span class="command-desc">Popis příkazu 19 — sem dej co příkaz dělá</span></div>
      <div class="command-item" onclick="copyCmd('!příkaz20', this)"><span class="command-name">!příkaz20</span><span class="command-desc">Popis příkazu 20 — sem dej co příkaz dělá</span></div>
    </div>
  </div>

</div>

<!-- TOAST -->
<div id="toast">
  <span class="t-icon" id="t-icon">✅</span>
  <div>
    <div class="t-label" id="t-label">Zkopírováno</div>
    <div class="t-main" id="t-main"></div>
  </div>
</div>

<script>
  function switchTab(pageId, btn) {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    document.getElementById(pageId).classList.add('active');
    btn.classList.add('active');
  }

  let toastTimer;
  function showToast(icon, label, main) {
    document.getElementById('t-icon').textContent = icon;
    document.getElementById('t-label').textContent = label;
    document.getElementById('t-main').textContent = main;
    const toast = document.getElementById('toast');
    toast.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(() => toast.classList.remove('show'), 2500);
  }

  function copyCmd(cmd, el) {
    const name = el.querySelector('.command-name');
    const orig = name.textContent;
    name.textContent = '✅ Zkopírováno!';
    setTimeout(() => { name.textContent = orig; }, 1400);
    showToast('✅', 'Příkaz zkopírován do schránky', cmd);

    if (navigator.clipboard && navigator.clipboard.writeText) {
      navigator.clipboard.writeText(cmd).catch(() => fallbackCopy(cmd));
    } else {
      fallbackCopy(cmd);
    }
  }

  function fallbackCopy(text) {
    const ta = document.createElement('textarea');
    ta.value = text;
    ta.style.cssText = 'position:fixed;opacity:0;top:0;left:0';
    document.body.appendChild(ta);
    ta.focus(); ta.select();
    document.execCommand('copy');
    document.body.removeChild(ta);
  }
</script>
</body>
</html>
