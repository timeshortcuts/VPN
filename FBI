<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>INTERPOL GLOBAL SURVEILLANCE NETWORK — PHASE 1</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=VT323&display=swap');

  :root {
    --green: #00ff41;
    --green-dim: #00aa2a;
    --green-dark: #003010;
    --amber: #ffb000;
    --red: #ff2222;
    --bg: #020a04;
    --scan-line-color: rgba(0,255,65,0.03);
  }

  * { margin:0; padding:0; box-sizing:border-box; }

  body {
    background: var(--bg);
    color: var(--green);
    font-family: 'Share Tech Mono', monospace;
    font-size: 14px;
    min-height: 100vh;
    overflow: hidden;
    cursor: none;
    position: relative;
  }

  /* CRT scan lines */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background: repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      var(--scan-line-color) 2px,
      var(--scan-line-color) 4px
    );
    pointer-events: none;
    z-index: 1000;
  }

  /* CRT vignette */
  body::after {
    content: '';
    position: fixed;
    inset: 0;
    background: radial-gradient(ellipse at center, transparent 60%, rgba(0,0,0,0.85) 100%);
    pointer-events: none;
    z-index: 999;
  }

  #screen {
    position: relative;
    width: 100vw;
    height: 100vh;
    padding: 24px 32px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }

  /* Header bar */
  #header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid var(--green-dim);
    padding-bottom: 10px;
    margin-bottom: 18px;
    opacity: 0;
    animation: fadeIn 0.3s ease forwards;
  }

  .header-left {
    font-family: 'VT323', monospace;
    font-size: 22px;
    color: var(--green);
    letter-spacing: 3px;
    text-transform: uppercase;
  }

  .header-right {
    font-size: 11px;
    color: var(--green-dim);
    text-align: right;
    line-height: 1.8;
  }

  #clock {
    color: var(--amber);
    font-size: 12px;
  }

  /* Log area */
  #log {
    flex: 1;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    gap: 0;
    position: relative;
  }

  .line {
    display: flex;
    align-items: baseline;
    gap: 10px;
    line-height: 1.9;
    opacity: 0;
    transform: translateX(-8px);
    animation: slideIn 0.15s ease forwards;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .line .ts {
    color: var(--green-dim);
    font-size: 11px;
    min-width: 90px;
    flex-shrink: 0;
  }

  .line .tag {
    font-size: 11px;
    padding: 0 5px;
    border-radius: 2px;
    min-width: 70px;
    text-align: center;
    flex-shrink: 0;
  }

  .tag-sys  { background: #003a1a; color: var(--green);  border: 1px solid var(--green-dim); }
  .tag-conn { background: #001f4a; color: #4ab8ff;       border: 1px solid #2288cc; }
  .tag-warn { background: #3a2500; color: var(--amber);  border: 1px solid #886000; }
  .tag-err  { background: #3a0000; color: var(--red);    border: 1px solid #881010; }
  .tag-ok   { background: #003a1a; color: #00ff88;       border: 1px solid #009944; }
  .tag-back { background: #220033; color: #cc44ff;       border: 1px solid #882299; }

  .line .msg { flex: 1; }

  .c-green  { color: var(--green); }
  .c-dim    { color: var(--green-dim); }
  .c-amber  { color: var(--amber); }
  .c-red    { color: var(--red); }
  .c-blue   { color: #4ab8ff; }
  .c-purple { color: #cc44ff; }
  .c-bright { color: #ffffff; }

  /* Cursor */
  #cursor {
    display: inline-block;
    width: 9px;
    height: 15px;
    background: var(--green);
    margin-left: 3px;
    animation: blink 0.75s step-end infinite;
    vertical-align: middle;
  }

  /* Progress bar */
  .progress-wrap {
    display: flex;
    align-items: center;
    gap: 10px;
    opacity: 0;
    animation: fadeIn 0.2s ease forwards;
  }

  .progress-bar {
    flex: 1;
    height: 8px;
    background: var(--green-dark);
    border: 1px solid var(--green-dim);
    border-radius: 2px;
    overflow: hidden;
    max-width: 340px;
  }

  .progress-fill {
    height: 100%;
    background: var(--green);
    width: 0%;
    transition: width 0.05s linear;
    box-shadow: 0 0 6px var(--green);
  }

  .progress-pct {
    font-size: 12px;
    color: var(--amber);
    min-width: 38px;
  }

  /* Status panel */
  #status-panel {
    position: fixed;
    top: 24px;
    right: 32px;
    width: 240px;
    border: 1px solid var(--green-dim);
    background: rgba(0,10,4,0.92);
    padding: 12px 16px;
    font-size: 11px;
    line-height: 2;
    opacity: 0;
    animation: fadeIn 0.5s ease 1s forwards;
    z-index: 100;
  }

  .status-title {
    color: var(--amber);
    font-size: 12px;
    letter-spacing: 2px;
    text-transform: uppercase;
    border-bottom: 1px solid var(--green-dark);
    padding-bottom: 6px;
    margin-bottom: 8px;
  }

  .status-row {
    display: flex;
    justify-content: space-between;
  }

  .status-label { color: var(--green-dim); }
  .status-val   { color: var(--green); }
  .status-val.blink { animation: blink 1.2s step-end infinite; }

  /* Phase complete overlay */
  #complete {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.7);
    z-index: 900;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    gap: 24px;
  }

  #complete.show { display: flex; }

  .complete-box {
    border: 2px solid var(--green);
    padding: 40px 60px;
    text-align: center;
    box-shadow: 0 0 40px rgba(0,255,65,0.2);
    background: var(--bg);
    animation: flickerIn 0.5s ease forwards;
  }

  .complete-title {
    font-family: 'VT323', monospace;
    font-size: 42px;
    color: var(--green);
    letter-spacing: 6px;
    margin-bottom: 10px;
  }

  .complete-sub {
    color: var(--green-dim);
    font-size: 13px;
    letter-spacing: 2px;
  }

  .btn-next {
    font-family: 'Share Tech Mono', monospace;
    font-size: 14px;
    color: var(--bg);
    background: var(--green);
    border: none;
    padding: 12px 36px;
    cursor: pointer;
    letter-spacing: 3px;
    text-transform: uppercase;
    transition: box-shadow 0.2s;
  }

  .btn-next:hover { box-shadow: 0 0 20px var(--green); }

  /* Divider line */
  .divider {
    border: none;
    border-top: 1px solid var(--green-dark);
    margin: 6px 0;
    opacity: 0;
    animation: fadeIn 0.2s ease forwards;
  }

  @keyframes fadeIn   { to { opacity: 1; } }
  @keyframes slideIn  { to { opacity: 1; transform: translateX(0); } }
  @keyframes blink    { 50% { opacity: 0; } }
  @keyframes flickerIn {
    0%   { opacity: 0; }
    20%  { opacity: 1; }
    25%  { opacity: 0; }
    40%  { opacity: 1; }
    45%  { opacity: 0; }
    60%  { opacity: 1; }
    100% { opacity: 1; }
  }
</style>
</head>
<body>

<div id="screen">
  <div id="header">
    <div class="header-left">INTERPOL-GSN &nbsp;/&nbsp; SECURE TERMINAL v4.7.2</div>
    <div class="header-right">
      <div id="clock"></div>
      <div>NODE: JPY-TKY-ALPHA-07 &nbsp;|&nbsp; ENC: AES-256</div>
    </div>
  </div>
  <div id="log"></div>
</div>

<!-- Status panel (right side) -->
<div id="status-panel">
  <div class="status-title">▸ SYSTEM STATUS</div>
  <div class="status-row"><span class="status-label">NODE</span><span class="status-val">JPY-ALPHA-07</span></div>
  <div class="status-row"><span class="status-label">UPLINK</span><span class="status-val blink" id="st-uplink">NEGOTIATING</span></div>
  <div class="status-row"><span class="status-label">CAMERAS</span><span class="status-val" id="st-cams">0</span></div>
  <div class="status-row"><span class="status-label">INTEGRITY</span><span class="status-val" id="st-int">--</span></div>
  <div class="status-row"><span class="status-label">BACKDOOR</span><span class="status-val" id="st-back" style="color:var(--green-dim)">DORMANT</span></div>
  <div class="status-row"><span class="status-label">PHASE</span><span class="status-val c-amber" id="st-phase">1 / 4</span></div>
</div>

<!-- Phase complete overlay -->
<div id="complete">
  <div class="complete-box">
    <div class="complete-title">PHASE 1 COMPLETE</div>
    <div class="complete-sub">接続確立 — 47カ国 / 2,819カメラ統合</div>
  </div>
  <button class="btn-next" onclick="location.href='map_dashboard.html'">▶ PHASE 2 へ進む</button>
</div>

<script>
// Clock
function updateClock() {
  const now = new Date();
  const s = now.toISOString().replace('T',' ').slice(0,19) + ' UTC';
  document.getElementById('clock').textContent = s;
}
updateClock();
setInterval(updateClock, 1000);

// Log system
const logEl = document.getElementById('log');
let lineCount = 0;
const MAX_LINES = 34;

function ts() {
  return new Date().toISOString().replace('T',' ').slice(11,23);
}

function addLine(tag, tagClass, msg, delay) {
  return new Promise(resolve => {
    setTimeout(() => {
      if (lineCount >= MAX_LINES) {
        const first = logEl.querySelector('.line');
        if (first) first.remove();
      }
      const div = document.createElement('div');
      div.className = 'line';
      div.style.animationDelay = '0ms';
      div.innerHTML = `<span class="ts">${ts()}</span><span class="tag ${tagClass}">${tag}</span><span class="msg">${msg}</span>`;
      logEl.appendChild(div);
      lineCount++;
      logEl.scrollTop = logEl.scrollHeight;
      resolve();
    }, delay);
  });
}

function addDivider(delay) {
  return new Promise(resolve => {
    setTimeout(() => {
      const hr = document.createElement('hr');
      hr.className = 'divider';
      logEl.appendChild(hr);
      resolve();
    }, delay);
  });
}

function addProgress(label, duration, delay) {
  return new Promise(resolve => {
    setTimeout(() => {
      const wrap = document.createElement('div');
      wrap.className = 'line progress-wrap';
      wrap.innerHTML = `
        <span class="ts">${ts()}</span>
        <span class="tag tag-sys">${label}</span>
        <div class="progress-bar"><div class="progress-fill" id="pf-${label.replace(/\s/g,'')}"></div></div>
        <span class="progress-pct" id="pp-${label.replace(/\s/g,'')}">0%</span>
      `;
      logEl.appendChild(wrap);
      logEl.scrollTop = logEl.scrollHeight;

      const fill = document.getElementById('pf-' + label.replace(/\s/g,''));
      const pct  = document.getElementById('pp-' + label.replace(/\s/g,''));
      let v = 0;
      const step = 100 / (duration / 40);
      const iv = setInterval(() => {
        v = Math.min(100, v + step + (Math.random() * step * 0.5));
        fill.style.width = v + '%';
        pct.textContent = Math.floor(v) + '%';
        if (v >= 100) { clearInterval(iv); resolve(); }
      }, 40);
    }, delay);
  });
}

// Typing effect for a msg
function typeMsg(el, text, speed=22) {
  return new Promise(resolve => {
    let i = 0;
    const iv = setInterval(() => {
      el.textContent = text.slice(0,i) + (i<text.length ? '█' : '');
      i++;
      if (i > text.length) { el.textContent = text; clearInterval(iv); resolve(); }
    }, speed);
  });
}

// Status helpers
function setSt(id, val, cls) {
  const el = document.getElementById(id);
  el.textContent = val;
  if (cls) el.style.color = cls;
  el.classList.remove('blink');
}

// ── Main sequence ──────────────────────────────────────────────────
async function run() {
  let d = 100;
  const D = (ms) => { const r = d; d += ms; return r; };

  // Boot
  await addLine('SYSTEM', 'tag-sys', '<span class="c-bright">INTERPOL Global Surveillance Network — Secure Shell</span>', D(0));
  await addLine('SYSTEM', 'tag-sys', 'Kernel 5.15.0-interpol-sec / Arch: x86_64 / RAM: 64GB ECC', D(300));
  await addLine('SYSTEM', 'tag-sys', 'Loading cryptographic modules... <span class="c-green">OK</span>', D(600));
  await addLine('SYSTEM', 'tag-sys', 'Mounting encrypted volume /dev/sdb1 → /secure/gsn ... <span class="c-green">OK</span>', D(800));
  await addDivider(D(400));

  // Auth
  await addLine('AUTH', 'tag-sys', 'Authenticating operator: <span class="c-amber">AKAI_SHUICHI</span> [Badge #JP-007]', D(200));
  await addLine('AUTH', 'tag-sys', 'Biometric hash verified: <span class="c-dim">3a8f2b...d91c</span>', D(700));
  await addLine('AUTH', 'tag-sys', 'MFA token accepted — clearance level: <span class="c-bright">ULTRA</span>', D(600));
  await addLine('AUTH', 'tag-ok',  '<span class="c-green">✓ ACCESS GRANTED</span>', D(400));
  await addDivider(D(300));

  // Network uplink
  await addLine('UPLINK', 'tag-conn', 'Establishing secure tunnel: JPY-TKY → LYONS-HQ-INTERPOL', D(200));
  await addLine('UPLINK', 'tag-conn', 'TLS 1.3 handshake... cipher: CHACHA20-POLY1305', D(800));
  await addLine('UPLINK', 'tag-conn', 'Latency: <span class="c-amber">218ms</span>  Bandwidth: <span class="c-green">1.2 Gbps</span>', D(600));
  await addLine('UPLINK', 'tag-ok',  '✓ Uplink established — <span class="c-bright">LYONS-CORE-01</span> responding', D(500));
  setSt('st-uplink', 'CONNECTED', '#00ff88');

  await addDivider(D(200));

  // GSN init
  await addLine('GSN', 'tag-sys', 'Initializing Global Surveillance Network daemon v12.3', D(200));
  await addProgress('INIT', 1800, D(300));
  await addLine('GSN', 'tag-sys', 'Protocol stack loaded: RTSP / H.265 / WebRTC / ONVIF', D(200));
  await addLine('GSN', 'tag-sys', 'Loading regional node registry... <span class="c-dim">47 countries detected</span>', D(600));
  await addDivider(D(300));

  // Camera discovery
  const regions = [
    ['EU', 'tag-conn', 'Scanning EU nodes... PARIS-01, BERLIN-03, ROME-07, MADRID-02'],
    ['AS', 'tag-conn', 'Scanning ASIA nodes... TOKYO-07, BEIJING-11, DELHI-04, SEOUL-02'],
    ['NA', 'tag-conn', 'Scanning NA nodes... NYC-14, LA-09, CHICAGO-03, TORONTO-01'],
    ['ME', 'tag-warn', 'Scanning ME nodes... DUBAI-03 <span class="c-amber">[HIGH LATENCY]</span> CAIRO-01'],
    ['AF', 'tag-warn', 'Scanning AF nodes... NAIROBI-02 <span class="c-amber">[PACKET LOSS 4.2%]</span>'],
    ['SA', 'tag-conn', 'Scanning SA nodes... SAO_PAULO-05, BUENOS_AIRES-02'],
  ];

  let camTotal = 0;
  for (const [tag, cls, msg] of regions) {
    const camAdded = Math.floor(Math.random() * 200) + 200;
    camTotal += camAdded;
    await addLine(tag, cls, msg, D(500));
    setSt('st-cams', camTotal.toLocaleString());
  }

  await addDivider(D(200));
  await addLine('GSN', 'tag-ok', `✓ Camera federation complete — <span class="c-bright">${camTotal.toLocaleString()} feeds</span> unified`, D(300));
  await addProgress('SYNC', 2200, D(200));
  await addLine('GSN', 'tag-sys', 'Frame buffer pool allocated: 128 GB shared memory', D(200));
  await addLine('GSN', 'tag-sys', 'Integrity check running...', D(400));
  await addProgress('VERIFY', 1400, D(200));
  setSt('st-int', '100%', '#00ff88');

  await addDivider(D(200));

  // Backdoor detected
  await addLine('SCAN', 'tag-sys', 'Routine packet inspection: running deep signature analysis...', D(400));
  await addLine('SCAN', 'tag-sys', 'Checking firmware hashes on 2,819 endpoints...', D(800));
  await addLine('SCAN', 'tag-warn', '<span class="c-amber">⚠ Anomalous handshake pattern on 14 nodes — flagging</span>', D(900));
  await addLine('SCAN', 'tag-warn', '<span class="c-amber">⚠ Unknown certificate authority in chain: [REDACTED-CA]</span>', D(700));
  await addLine('SCAN', 'tag-err',  '<span class="c-red">✗ ALERT: Unregistered agent connected — signature mismatch</span>', D(600));
  await addLine('SCAN', 'tag-back', '<span class="c-purple">⬡ BACKDOOR SIGNATURE DETECTED — origin masked / routing via TOR EXIT</span>', D(500));
  setSt('st-back', 'ACTIVE', '#cc44ff');
  setSt('st-int', 'COMPROMISED', '#ff2222');
  document.getElementById('st-int').classList.add('blink');

  await addDivider(D(300));

  // Attempting lockdown
  await addLine('SEC', 'tag-err',  '<span class="c-red">Initiating emergency lockdown protocol...</span>', D(200));
  await addLine('SEC', 'tag-err',  '<span class="c-red">✗ LOCKDOWN FAILED — admin privileges revoked by remote agent</span>', D(800));
  await addLine('SEC', 'tag-err',  '<span class="c-red">✗ Firewall rules purged by unauthorized write</span>', D(600));
  await addLine('SEC', 'tag-back', '<span class="c-purple">⬡ Remote agent escalating privileges... ROOT ACQUIRED</span>', D(700));
  await addLine('SEC', 'tag-back', '<span class="c-purple">⬡ Transferring GSN control plane to external node...</span>', D(600));
  await addProgress('HIJACK', 2000, D(200));
  await addLine('SEC', 'tag-back', '<span class="c-purple">⬡ 2,819 camera feeds redirected — SYSTEM SEIZED</span>', D(300));

  // Done
  setTimeout(() => {
    document.getElementById('complete').classList.add('show');
  }, d + 600);
}

run();
</script>
</body>
</html>
