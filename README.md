<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>INTERPOL GSN — FULL SYSTEM</title>
<style>
html,body{
  margin:0;
  height:100%;
  background:#000;
  overflow:hidden;
}
iframe{
  position:absolute;
  inset:0;
  width:100%;
  height:100%;
  border:none;
  display:none;
}
iframe.active{
  display:block;
}
</style>
</head>
<body>

<!-- 各フェーズ -->
<iframe id="p1"></iframe>
<iframe id="p2"></iframe>
<iframe id="p4"></iframe>

<script>
// ================== HTML埋め込み ==================
const phase1 = `<<<<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>INTERPOL GSN — PHASE 1: BOOT</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=VT323&display=swap');
  :root {
    --green:#00ff41;--green-dim:#00aa2a;--green-dark:#003010;
    --amber:#ffb000;--red:#ff2222;--bg:#020a04;--purple:#cc44ff;--blue:#4ab8ff;
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  body{background:var(--bg);color:var(--green);font-family:'Share Tech Mono',monospace;font-size:13px;height:100vh;overflow:hidden;position:relative;}
  body::before{content:'';position:fixed;inset:0;background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,255,65,0.025) 2px,rgba(0,255,65,0.025) 4px);pointer-events:none;z-index:1000;}
  body::after{content:'';position:fixed;inset:0;background:radial-gradient(ellipse at center,transparent 55%,rgba(0,0,0,0.9) 100%);pointer-events:none;z-index:999;}
 
  #screen{display:flex;flex-direction:column;height:100vh;padding:16px 20px;}
 
  #header{display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid var(--green-dim);padding-bottom:8px;margin-bottom:10px;flex-shrink:0;}
  .h-left{font-family:'VT323',monospace;font-size:20px;letter-spacing:3px;}
  .h-right{font-size:11px;color:var(--green-dim);text-align:right;line-height:1.6;}
  #clock{color:var(--amber);}
 
  /* Two-column layout */
  #body{display:flex;gap:16px;flex:1;min-height:0;}
 
  #log-col{flex:1;min-width:0;display:flex;flex-direction:column;min-height:0;}
  #log{flex:1;overflow:hidden;display:flex;flex-direction:column;gap:0;min-height:0;}
 
  .line{display:flex;align-items:baseline;gap:8px;line-height:1.75;opacity:0;animation:sli .12s ease forwards;flex-shrink:0;min-height:0;}
  .line .ts{color:var(--green-dim);font-size:10px;min-width:76px;flex-shrink:0;}
  .line .tag{font-size:10px;padding:0 4px;min-width:62px;text-align:center;flex-shrink:0;border-radius:2px;}
  .line .msg{flex:1;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
 
  .tag-sys {background:#003a1a;color:var(--green);border:1px solid var(--green-dim);}
  .tag-conn{background:#001f4a;color:var(--blue);border:1px solid #2288cc;}
  .tag-warn{background:#3a2500;color:var(--amber);border:1px solid #886000;}
  .tag-err {background:#3a0000;color:var(--red);border:1px solid #881010;}
  .tag-ok  {background:#003a1a;color:#00ff88;border:1px solid #009944;}
  .tag-back{background:#220033;color:var(--purple);border:1px solid #882299;}
 
  .divider{border:none;border-top:1px solid var(--green-dark);margin:3px 0;flex-shrink:0;opacity:0;animation:sli .1s ease forwards;}
 
  .prog-wrap{display:flex;align-items:center;gap:8px;opacity:0;animation:sli .1s ease forwards;flex-shrink:0;line-height:1.75;}
  .prog-bar{width:200px;height:7px;background:var(--green-dark);border:1px solid var(--green-dim);border-radius:2px;overflow:hidden;flex-shrink:0;}
  .prog-fill{height:100%;background:var(--green);width:0%;box-shadow:0 0 5px var(--green);}
  .prog-pct{font-size:11px;color:var(--amber);min-width:34px;}
 
  /* Status panel */
  #panel{width:210px;flex-shrink:0;border:1px solid var(--green-dim);padding:12px 14px;font-size:11px;line-height:2;background:rgba(0,10,4,0.95);height:fit-content;margin-top:2px;}
  .pan-title{color:var(--amber);font-size:12px;letter-spacing:2px;border-bottom:1px solid var(--green-dark);padding-bottom:5px;margin-bottom:6px;}
  .pan-row{display:flex;justify-content:space-between;}
  .pan-lbl{color:var(--green-dim);}
  .pan-val{color:var(--green);}
  .pan-val.blink{animation:blink 1s step-end infinite;}
 
  /* Complete */
  #complete{display:none;position:fixed;inset:0;background:rgba(0,0,0,0.75);z-index:900;align-items:center;justify-content:center;flex-direction:column;gap:20px;}
  #complete.show{display:flex;}
  .cbox{border:2px solid var(--green);padding:32px 52px;text-align:center;box-shadow:0 0 40px rgba(0,255,65,.2);background:var(--bg);animation:flk .4s ease forwards;}
  .ctitle{font-family:'VT323',monospace;font-size:38px;letter-spacing:5px;color:var(--green);}
  .csub{color:var(--green-dim);font-size:12px;letter-spacing:2px;margin-top:6px;}
  .btn-next{font-family:'Share Tech Mono',monospace;font-size:13px;color:var(--bg);background:var(--green);border:none;padding:10px 32px;cursor:pointer;letter-spacing:3px;text-transform:uppercase;transition:box-shadow .2s;}
  .btn-next:hover{box-shadow:0 0 18px var(--green);}
 
  @keyframes sli{to{opacity:1;}}
  @keyframes blink{50%{opacity:0;}}
  @keyframes flk{0%{opacity:0;}20%{opacity:1;}25%{opacity:0;}40%{opacity:1;}45%{opacity:0;}60%{opacity:1;}100%{opacity:1;}}
</style>
</head>
<body>
<div id="screen">
  <div id="header">
    <div class="h-left">INTERPOL-GSN &nbsp;/&nbsp; SECURE TERMINAL v4.7.2</div>
    <div class="h-right"><div id="clock"></div><div>NODE: JPY-TKY-ALPHA-07 &nbsp;|&nbsp; ENC: AES-256</div></div>
  </div>
  <div id="body">
    <div id="log-col"><div id="log"></div></div>
    <div id="panel">
      <div class="pan-title">▸ SYSTEM STATUS</div>
      <div class="pan-row"><span class="pan-lbl">NODE</span><span class="pan-val">JPY-ALPHA-07</span></div>
      <div class="pan-row"><span class="pan-lbl">UPLINK</span><span class="pan-val blink" id="st-uplink">NEGOTIATING</span></div>
      <div class="pan-row"><span class="pan-lbl">CAMERAS</span><span class="pan-val" id="st-cams">0</span></div>
      <div class="pan-row"><span class="pan-lbl">INTEGRITY</span><span class="pan-val" id="st-int">--</span></div>
      <div class="pan-row"><span class="pan-lbl">BACKDOOR</span><span class="pan-val" id="st-back" style="color:var(--green-dim)">DORMANT</span></div>
      <div class="pan-row"><span class="pan-lbl">PHASE</span><span class="pan-val" style="color:var(--amber)">1 / 4</span></div>
    </div>
  </div>
</div>
 
<div id="complete">
  <div class="cbox">
    <div class="ctitle">PHASE 1 COMPLETE</div>
    <div class="csub">接続確立 — 47カ国 / 2,819カメラ統合</div>
  </div>
  <button class="btn-next" onclick="location.href='phase2_cracker.html'">▶ PHASE 2 へ進む</button>
</div>
 
<script>
setInterval(()=>{document.getElementById('clock').textContent=new Date().toISOString().replace('T',' ').slice(0,19)+' UTC';},1000);
document.getElementById('clock').textContent=new Date().toISOString().replace('T',' ').slice(0,19)+' UTC';
 
const log=document.getElementById('log');
const MAX=28;
 
function ts(){return new Date().toISOString().replace('T',' ').slice(11,23);}
 
function setSt(id,val,col){const e=document.getElementById(id);e.textContent=val;if(col)e.style.color=col;e.classList.remove('blink');}
 
function addLine(tag,cls,msg,delay){
  return new Promise(r=>setTimeout(()=>{
    while(log.children.length>=MAX)log.children[0].remove();
    const d=document.createElement('div');d.className='line';
    d.innerHTML=`<span class="ts">${ts()}</span><span class="tag ${cls}">${tag}</span><span class="msg">${msg}</span>`;
    log.appendChild(d);r();
  },delay));
}
function addDiv(delay){return new Promise(r=>setTimeout(()=>{while(log.children.length>=MAX)log.children[0].remove();const h=document.createElement('hr');h.className='divider';log.appendChild(h);r();},delay));}
 
function addProg(label,dur,delay){
  return new Promise(r=>setTimeout(()=>{
    while(log.children.length>=MAX)log.children[0].remove();
    const id='pf'+Math.random().toString(36).slice(2);
    const d=document.createElement('div');d.className='prog-wrap';
    d.innerHTML=`<span class="ts">${ts()}</span><span class="tag tag-sys">${label}</span><div class="prog-bar"><div class="prog-fill" id="${id}"></div></div><span class="prog-pct" id="${id}p">0%</span>`;
    log.appendChild(d);
    const fill=document.getElementById(id),pct=document.getElementById(id+'p');
    let v=0;const step=100/(dur/35);
    const iv=setInterval(()=>{v=Math.min(100,v+step+Math.random()*step*.4);fill.style.width=v+'%';pct.textContent=Math.floor(v)+'%';if(v>=100){clearInterval(iv);r();}},35);
  },delay));
}
 
async function run(){
  let d=0;const D=ms=>{const r=d;d+=ms;return r;};
 
  await addLine('SYSTEM','tag-sys','INTERPOL Global Surveillance Network — Secure Shell',D(0));
  await addLine('SYSTEM','tag-sys','Kernel 5.15.0-interpol / RAM: 64GB ECC / Arch: x86_64',D(180));
  await addLine('SYSTEM','tag-sys','Cryptographic modules loaded... <span style="color:#00ff88">OK</span>',D(200));
  await addLine('SYSTEM','tag-sys','Encrypted volume /dev/sdb1 mounted... <span style="color:#00ff88">OK</span>',D(180));
  await addDiv(D(100));
  await addLine('AUTH','tag-sys','Operator: <span style="color:var(--amber)">AKAI_SHUICHI</span> [Badge #JP-007]  Biometric: <span style="color:var(--green-dim)">3a8f2b..d91c</span>',D(150));
  await addLine('AUTH','tag-sys','MFA verified — clearance: <span style="color:#fff">ULTRA</span>',D(250));
  await addLine('AUTH','tag-ok','✓ ACCESS GRANTED',D(200));
  await addDiv(D(100));
  await addLine('UPLINK','tag-conn','Tunnel: JPY-TKY → LYONS-HQ  TLS1.3 / CHACHA20-POLY1305',D(150));
  await addLine('UPLINK','tag-conn','Latency: <span style="color:var(--amber)">218ms</span>  Bandwidth: <span style="color:var(--green)">1.2 Gbps</span>',D(300));
  await addLine('UPLINK','tag-ok','✓ LYONS-CORE-01 responding',D(250));
  setSt('st-uplink','CONNECTED','#00ff88');
  await addDiv(D(100));
  await addLine('GSN','tag-sys','Initializing Global Surveillance Network daemon v12.3',D(150));
  await addProg('INIT',1200,D(100));
  await addLine('GSN','tag-sys','Protocol stack: RTSP / H.265 / WebRTC / ONVIF  <span style="color:#00ff88">OK</span>',D(100));
  await addDiv(D(80));
 
  const regions=[
    ['EU','tag-conn','PARIS-01 BERLIN-03 ROME-07 MADRID-02',412],
    ['AS','tag-conn','TOKYO-07 BEIJING-11 DELHI-04 SEOUL-02',388],
    ['NA','tag-conn','NYC-14 LA-09 CHICAGO-03 TORONTO-01',376],
    ['ME','tag-warn','DUBAI-03 <span style="color:var(--amber)">[HIGH LATENCY]</span> CAIRO-01',354],
    ['AF','tag-warn','NAIROBI-02 <span style="color:var(--amber)">[LOSS 4.2%]</span>',298],
    ['SA','tag-conn','SAO_PAULO-05 BUENOS_AIRES-02',312],
  ];
  let cams=0;
  for(const [tag,cls,msg,c] of regions){
    cams+=c;await addLine(tag,cls,msg,D(220));setSt('st-cams',cams.toLocaleString());
  }
  await addDiv(D(80));
  await addLine('GSN','tag-ok',`✓ Federation complete — <span style="color:#fff">${cams.toLocaleString()} feeds</span> unified`,D(150));
  await addProg('SYNC',1400,D(100));
  await addProg('VERIFY',900,D(100));
  setSt('st-int','100%','#00ff88');
  await addDiv(D(80));
 
  // Backdoor detection
  await addLine('SCAN','tag-sys','Deep packet inspection running...',D(200));
  await addLine('SCAN','tag-warn','<span style="color:var(--amber)">⚠ Anomalous handshake on 14 nodes — unknown CA in chain</span>',D(500));
  await addLine('SCAN','tag-err','<span style="color:var(--red)">✗ Unregistered agent — signature mismatch</span>',D(400));
  await addLine('SCAN','tag-back','<span style="color:var(--purple)">⬡ BACKDOOR DETECTED — TOR exit routing / origin masked</span>',D(350));
  setSt('st-back','ACTIVE','#cc44ff');
  setSt('st-int','COMPROMISED','#ff2222');
  document.getElementById('st-int').classList.add('blink');
  await addDiv(D(80));
  await addLine('SEC','tag-err','<span style="color:var(--red)">Lockdown initiated... ✗ FAILED — privileges revoked</span>',D(250));
  await addLine('SEC','tag-back','<span style="color:var(--purple)">⬡ Remote agent escalating... ROOT ACQUIRED</span>',D(400));
  await addLine('SEC','tag-back','<span style="color:var(--purple)">⬡ Transferring GSN control plane to external node...</span>',D(350));
 
  setTimeout(()=>document.getElementById('complete').classList.add('show'),d+500);
}
run();
</script>
</body>
</html>
 >>>`;
const phase2 = `<<<<html lang="ja"> <head> <meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>INTERPOL GSN — PHASE 2: INTRUSION</title> <style> @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=VT323&display=swap'); :root{ --green:#00ff41;--green-dim:#00aa2a;--green-dark:#003010; --amber:#ffb000;--red:#ff2222;--bg:#020a04;--purple:#cc44ff;--blue:#4ab8ff; } *{margin:0;padding:0;box-sizing:border-box;} body{background:var(--bg);color:var(--green);font-family:'Share Tech Mono',monospace;font-size:13px;height:100vh;overflow:hidden;} body::before{content:'';position:fixed;inset:0;background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,255,65,.025) 2px,rgba(0,255,65,.025) 4px);pointer-events:none;z-index:2000;} body::after{content:'';position:fixed;inset:0;background:radial-gradient(ellipse at center,transparent 55%,rgba(0,0,0,.88) 100%);pointer-events:none;z-index:1999;} #screen{display:flex;flex-direction:column;height:100vh;padding:14px 20px;gap:10px;} /* Header */ #hdr{display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid var(--green-dim);padding-bottom:7px;flex-shrink:0;} .h-l{font-family:'VT323',monospace;font-size:20px;letter-spacing:3px;} .h-r{font-size:10px;color:var(--green-dim);text-align:right;line-height:1.7;} #clk{color:var(--amber);} /* Progress bar top */ #hack-prog-wrap{flex-shrink:0;display:flex;align-items:center;gap:10px;} #hack-prog-wrap label{font-size:11px;color:var(--green-dim);min-width:120px;} #hack-bar-outer{flex:1;height:10px;background:var(--green-dark);border:1px solid var(--green-dim);border-radius:2px;overflow:hidden;} #hack-bar{height:100%;background:var(--green);width:0%;transition:width .3s;box-shadow:0 0 6px var(--green);} #hack-pct{font-size:12px;color:var(--amber);min-width:38px;text-align:right;} #hack-timer{font-size:12px;color:var(--red);min-width:60px;text-align:right;} /* Main layout */ #main{display:flex;gap:14px;flex:1;min-height:0;} /* Left: cracker visual */ #cracker{width:360px;flex-shrink:0;border:1px solid var(--green-dim);background:rgba(0,8,2,.97);display:flex;flex-direction:column;} .crack-hdr{background:#001a00;border-bottom:1px solid var(--green-dim);padding:7px 12px;font-size:11px;color:var(--amber);letter-spacing:2px;} .crack-body{padding:12px;flex:1;display:flex;flex-direction:column;gap:10px;} .field-row{display:flex;align-items:center;gap:8px;font-size:12px;} .field-lbl{color:var(--green-dim);min-width:80px;} .field-val{border:1px solid var(--green-dim);padding:3px 8px;color:var(--green);flex:1;font-family:'Share Tech Mono',monospace;font-size:12px;background:rgba(0,20,0,.5);} /* Password character grid */ #pw-grid{display:flex;flex-wrap:wrap;gap:4px;margin-top:4px;} .pw-char{width:26px;height:26px;border:1px solid var(--green-dim);display:flex;align-items:center;justify-content:center;font-size:13px;font-family:'Share Tech Mono',monospace;color:var(--green-dim);transition:all .08s;background:rgba(0,15,0,.5);} .pw-char.active{color:var(--green);border-color:var(--green);background:rgba(0,60,0,.6);box-shadow:0 0 6px var(--green);} .pw-char.cracked{color:#00ff88;border-color:#00ff88;background:rgba(0,80,20,.5);} /* Cracked chars display */ #cracked-display{display:flex;gap:4px;flex-wrap:wrap;} .cd-char{width:26px;height:26px;border:1px solid transparent;display:flex;align-items:center;justify-content:center;font-size:13px;color:var(--bg);background:var(--green-dim);} .cd-char.done{background:var(--green);color:var(--bg);} /* Checkmarks */ #checks{display:flex;gap:4px;flex-wrap:wrap;} .chk{width:26px;height:20px;border:1px solid var(--green-dim);display:flex;align-items:center;justify-content:center;font-size:11px;color:var(--green-dim);} .chk.ok{color:#00ff88;border-color:#00ff88;background:rgba(0,60,20,.4);} #result-line{font-size:11px;min-height:32px;padding:6px 8px;border:1px solid var(--green-dark);background:rgba(0,10,0,.5);} /* Lock icon */ #lock{text-align:center;padding:8px;} #lock-svg{transition:all .5s;} /* Right: log + typing game */ #right{flex:1;min-width:0;display:flex;flex-direction:column;gap:10px;} #log{flex:1;min-height:0;overflow:hidden;display:flex;flex-direction:column;gap:0;} .line{display:flex;align-items:baseline;gap:8px;line-height:1.7;opacity:0;animation:sli .1s ease forwards;flex-shrink:0;} .line .ts{color:var(--green-dim);font-size:10px;min-width:70px;flex-shrink:0;} .line .tag{font-size:10px;padding:0 4px;min-width:60px;text-align:center;flex-shrink:0;border-radius:2px;} .line .msg{flex:1;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;} .divider{border:none;border-top:1px solid var(--green-dark);margin:2px 0;flex-shrink:0;opacity:0;animation:sli .1s forwards;} .tag-sys {background:#003a1a;color:var(--green);border:1px solid var(--green-dim);} .tag-conn{background:#001f4a;color:var(--blue);border:1px solid #2288cc;} .tag-warn{background:#3a2500;color:var(--amber);border:1px solid #886000;} .tag-err {background:#3a0000;color:var(--red);border:1px solid #881010;} .tag-ok  {background:#003a1a;color:#00ff88;border:1px solid #009944;} .tag-back{background:#220033;color:var(--purple);border:1px solid #882299;} .tag-type{background:#002233;color:var(--blue);border:1px solid #224466;} /* TYPING GAME */ #typing-zone{flex-shrink:0;border:1px solid var(--green-dim);background:rgba(0,8,2,.98);padding:12px 14px;} .tz-title{font-size:10px;color:var(--amber);letter-spacing:2px;margin-bottom:8px;} #cmd-display{font-size:15px;color:var(--green-dim);margin-bottom:8px;letter-spacing:1px;min-height:22px;} #cmd-display .done-part{color:var(--green);} #cmd-display .cursor-part{color:#fff;background:var(--green);padding:0 1px;} #cmd-display .rest-part{color:var(--green-dim);} #cmd-jp{font-size:10px;color:var(--green-dim);margin-bottom:8px;min-height:16px;} #input-row{display:flex;align-items:center;gap:8px;} #input-row span{color:var(--amber);font-size:13px;} #cmd-input{flex:1;background:transparent;border:none;outline:none;color:var(--green);font-family:'Share Tech Mono',monospace;font-size:13px;caret-color:var(--green);} .cmd-feedback{font-size:11px;min-height:16px;margin-top:4px;} .fb-ok{color:#00ff88;} .fb-err{color:var(--red);animation:shake .15s ease 3;} /* Overlays */ .overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.8);z-index:3000;align-items:center;justify-content:center;flex-direction:column;gap:18px;} .overlay.show{display:flex;} .obox{border:2px solid var(--green);padding:30px 48px;text-align:center;background:var(--bg);animation:flk .4s ease forwards;} .obox.red{border-color:var(--red);box-shadow:0 0 40px rgba(255,34,34,.25);} .otitle{font-family:'VT323',monospace;font-size:38px;letter-spacing:5px;} .osub{font-size:11px;color:var(--green-dim);margin-top:6px;letter-spacing:2px;line-height:1.8;} .btn{font-family:'Share Tech Mono',monospace;font-size:12px;color:var(--bg);background:var(--green);border:none;padding:9px 28px;cursor:pointer;letter-spacing:3px;text-transform:uppercase;} .btn:hover{box-shadow:0 0 16px var(--green);} .btn-red{background:var(--red);} @keyframes sli{to{opacity:1;}} @keyframes blink{50%{opacity:0;}} @keyframes flk{0%{opacity:0;}20%{opacity:1;}25%{opacity:0;}40%{opacity:1;}45%{opacity:0;}60%{opacity:1;}100%{opacity:1;}} @keyframes shake{0%,100%{transform:translateX(0);}33%{transform:translateX(-4px);}66%{transform:translateX(4px);}} </style> </head> <body> <div id="screen"> <div id="hdr"> <div class="h-l">INTERPOL-GSN &nbsp;/&nbsp; BACKDOOR INTRUSION — PHASE 2</div> <div class="h-r"><div id="clk"></div><div>TARGET: INTERPOL-CORE-01 &nbsp;|&nbsp; LYONS, FR</div></div> </div> <div id="hack-prog-wrap"> <label>SYSTEM BREACH PROGRESS</label> <div id="hack-bar-outer"><div id="hack-bar"></div></div> <span id="hack-pct">0%</span> <span id="hack-timer">60s</span> </div> <div id="main"> <!-- Cracker panel --> <div id="cracker"> <div class="crack-hdr">▸ PASSWORD CRACKER v3.1 — BRUTE FORCE MODULE</div> <div class="crack-body"> <div class="field-row"> <span class="field-lbl">Target IP:</span> <span class="field-val" id="f-target">23.86.111.0</span> </div> <div class="field-row"> <span class="field-lbl">Database:</span> <span class="field-val" id="f-db">User table / Admin role</span> </div> <div style="font-size:10px;color:var(--green-dim);margin-top:2px;">Attempting combinations:</div> <div id="pw-grid"></div> <div style="font-size:10px;color:var(--green-dim);">Cracked chars:</div> <div id="cracked-display"></div> <div id="checks"></div> <div id="result-line" style="color:var(--green-dim);">Initializing brute force engine...</div> <div id="lock"> <svg id="lock-svg" width="60" height="72" viewBox="0 0 60 72"> <rect x="8" y="30" width="44" height="36" rx="5" fill="none" stroke="#00aa2a" stroke-width="2"/> <path d="M15 30 V22 A15 15 0 0 1 45 22 V30" fill="none" stroke="#00aa2a" stroke-width="2.5"/> <circle cx="30" cy="48" r="5" fill="#00aa2a"/> <rect x="28" y="48" width="4" height="7" rx="2" fill="#00aa2a"/> </svg> </div> </div> </div> <!-- Right column --> <div id="right"> <div id="log"></div> <!-- Typing zone --> <div id="typing-zone"> <div class="tz-title">▸ MANUAL EXPLOIT INPUT — TYPE THE COMMAND TO PROCEED</div> <div id="cmd-display"></div> <div id="cmd-jp"></div> <div id="input-row"> <span>root@gsn:~#</span> <input id="cmd-input" type="text" autocomplete="off" spellcheck="false" placeholder="コマンドを入力..."> </div> <div id="cmd-feedback" class="cmd-feedback"></div> </div> </div> </div> </div> <!-- Success overlay --> <div id="ov-success" class="overlay"> <div class="obox"> <div class="otitle" style="color:var(--green)">ACCESS GRANTED</div> <div class="osub">侵入成功 — システム制御権を取得<br>全2,819カメラフィードへのアクセス確立</div> </div> <button class="btn" onclick="location.href='phase4_takeover.html'">▶ PHASE 4 へ進む</button> </div> <!-- Fail overlay --> <div id="ov-fail" class="overlay"> <div class="obox red"> <div class="otitle" style="color:var(--red)">CONNECTION BLOCKED</div> <div class="osub" style="color:#ff8888;">サーバが接続を遮断しました<br>ファイアウォールルールが更新されました — 侵入失敗<br><br>GAME OVER</div> </div> <button class="btn btn-red" onclick="location.reload()">▶ 再挑戦する</button> </div> <script> // Clock setInterval(()=>{document.getElementById('clk').textContent=new Date().toISOString().replace('T',' ').slice(0,19)+' UTC';},1000); // ── Password cracker animation ────────────────────────────────────── const TARGET_PW = 'R00T_4CC3SS!'; const CHARS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*_+'; const grid = document.getElementById('pw-grid'); const crackedDisp = document.getElementById('cracked-display'); const checksDisp = document.getElementById('checks'); // Build grid TARGET_PW.split('').forEach((_,i)=>{ const c=document.createElement('div');c.className='pw-char';c.id='pc'+i;c.textContent='?';grid.appendChild(c); const d=document.createElement('div');d.className='cd-char';d.id='cd'+i;d.textContent='_';crackedDisp.appendChild(d); const k=document.createElement('div');k.className='chk';k.id='ck'+i;k.textContent='✗';checksDisp.appendChild(k); }); let crackedCount=0; let crackerInterval=null; function crackNextChar(){ if(crackedCount>=TARGET_PW.length)return; const idx=crackedCount; const target=TARGET_PW[idx]; let attempts=0; const totalAttempts=Math.floor(Math.random()*30)+15; const iv=setInterval(()=>{ const c=CHARS[Math.floor(Math.random()*CHARS.length)]; document.getElementById('pc'+idx).textContent=c; document.getElementById('pc'+idx).classList.add('active'); attempts++; if(attempts>=totalAttempts){ clearInterval(iv); document.getElementById('pc'+idx).textContent=target; document.getElementById('pc'+idx).classList.remove('active'); document.getElementById('pc'+idx).classList.add('cracked'); document.getElementById('cd'+idx).textContent=target; document.getElementById('cd'+idx).classList.add('done'); document.getElementById('ck'+idx).textContent='✓'; document.getElementById('ck'+idx).classList.add('ok'); crackedCount++; if(crackedCount<TARGET_PW.length){ setTimeout(crackNextChar,120+Math.random()*200); } else { document.getElementById('result-line').innerHTML= `<span style="color:#00ff88">SUCCESS: Admin password found @ 23.86.111.0</span><br>Password: <span style="color:var(--amber);background:#001a00;padding:0 6px">${TARGET_PW}</span>`; // Change lock to open/green document.getElementById('lock-svg').style.filter='drop-shadow(0 0 8px #00ff88)'; document.querySelector('#lock-svg path').setAttribute('stroke','#00ff88'); document.querySelector('#lock-svg rect:first-child').setAttribute('stroke','#00ff88'); document.querySelector('#lock-svg circle').setAttribute('fill','#00ff88'); document.querySelector('#lock-svg rect:last-child').setAttribute('fill','#00ff88'); } } },40); } // ── Log system ────────────────────────────────────────────────────── const logEl=document.getElementById('log'); const MAX=18; function ts(){return new Date().toISOString().replace('T',' ').slice(11,23);} function addLine(tag,cls,msg,delay){ return new Promise(r=>setTimeout(()=>{ while(logEl.children.length>=MAX)logEl.children[0].remove(); const d=document.createElement('div');d.className='line'; d.innerHTML=`<span class="ts">${ts()}</span><span class="tag ${cls}">${tag}</span><span class="msg">${msg}</span>`; logEl.appendChild(d);r(); },delay)); } function addDiv(delay){return new Promise(r=>setTimeout(()=>{while(logEl.children.length>=MAX)logEl.children[0].remove();const h=document.createElement('hr');h.className='divider';logEl.appendChild(h);r();},delay));} // ── Typing game ───────────────────────────────────────────────────── const COMMANDS = [ {cmd:'nmap -sV 23.86.111.0',    jp:'対象サーバのポートスキャン＆バージョン検出'}, {cmd:'exploit --cve-2024-0731', jp:'既知の脆弱性を使ってエクスプロイト実行'}, {cmd:'inject payload ROOT',     jp:'ルート権限取得ペイロードを注入'}, {cmd:'bypass firewall --force', jp:'ファイアウォールを強制突破'}, {cmd:'auth override --admin',   jp:'管理者認証を上書きしてログイン'}, {cmd:'escalate privileges',     jp:'権限をエスカレーション（昇格）'}, ]; let cmdIndex=0; let typingActive=false; let currentCmd=''; let hackPct=0; let timerSec=90; let timerInterval=null; let gameOver=false; let waitingForNextCmd=false; const cmdDisplay=document.getElementById('cmd-display'); const cmdJp=document.getElementById('cmd-jp'); const cmdInput=document.getElementById('cmd-input'); const cmdFeedback=document.getElementById('cmd-feedback'); const hackBar=document.getElementById('hack-bar'); const hackPctEl=document.getElementById('hack-pct'); const hackTimerEl=document.getElementById('hack-timer'); function renderCmdDisplay(typed){ const cmd=currentCmd; const correct=cmd.slice(0,typed.length); const isCorrect=typed===correct; if(isCorrect){ const done=`<span class="done-part">${escHtml(typed)}</span>`; const cursor=typed.length<cmd.length?`<span class="cursor-part">${escHtml(cmd[typed.length])}</span>`:''; const rest=typed.length<cmd.length?`<span class="rest-part">${escHtml(cmd.slice(typed.length+1))}</span>`:''; cmdDisplay.innerHTML=done+cursor+rest; } else { cmdDisplay.innerHTML=`<span style="color:var(--red)">${escHtml(typed)}</span><span class="rest-part">${escHtml(cmd.slice(typed.length))}</span>`; } } function escHtml(s){return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');} function loadCmd(idx){ if(idx>=COMMANDS.length){ // All commands done triggerSuccess(); return; } currentCmd=COMMANDS[idx].cmd; cmdJp.textContent='// '+COMMANDS[idx].jp; cmdDisplay.innerHTML=`<span class="cursor-part">${escHtml(currentCmd[0])}</span><span class="rest-part">${escHtml(currentCmd.slice(1))}</span>`; cmdInput.value=''; cmdFeedback.textContent=''; typingActive=true; waitingForNextCmd=false; cmdInput.focus(); } function advanceHack(amount){ hackPct=Math.min(100,hackPct+amount); hackBar.style.width=hackPct+'%'; hackPctEl.textContent=Math.floor(hackPct)+'%'; if(hackPct>=30&&crackedCount===0) crackNextChar(); } cmdInput.addEventListener('input',()=>{ if(!typingActive||gameOver||waitingForNextCmd)return; const typed=cmdInput.value; renderCmdDisplay(typed); // Check for wrong char if(typed!==currentCmd.slice(0,typed.length)){ cmdFeedback.className='cmd-feedback fb-err'; cmdFeedback.textContent='✗ 入力ミス — バックスペースで修正してください'; return; } cmdFeedback.className='cmd-feedback fb-ok'; cmdFeedback.textContent=''; if(typed===currentCmd){ // Command complete typingActive=false; waitingForNextCmd=true; const gain=Math.floor(100/COMMANDS.length); advanceHack(gain); cmdFeedback.className='cmd-feedback fb-ok'; cmdFeedback.textContent=`✓ COMMAND EXECUTED — [${cmdIndex+1}/${COMMANDS.length}] 次のコマンドを読み込み中...`; // Log it addLine('EXEC','tag-ok',`<span style="color:#00ff88">${escHtml(currentCmd)}</span>`,0); cmdIndex++; setTimeout(()=>loadCmd(cmdIndex),1200); } }); // Timer function startTimer(){ timerInterval=setInterval(()=>{ timerSec--; hackTimerEl.textContent=timerSec+'s'; if(timerSec<=15) hackTimerEl.style.color='var(--red)'; if(timerSec<=15&&timerSec%2===0) hackTimerEl.style.opacity=hackTimerEl.style.opacity==='0.3'?'1':'0.3'; if(timerSec<=0){clearInterval(timerInterval);triggerFail();} },1000); } function triggerSuccess(){ gameOver=true;typingActive=false; clearInterval(timerInterval); cmdDisplay.innerHTML='';cmdJp.textContent='';cmdFeedback.textContent=''; hackPct=100;hackBar.style.width='100%';hackPctEl.textContent='100%'; addLine('AUTH','tag-ok','✓ ROOT ACCESS GRANTED — システム制御権取得',0); setTimeout(()=>document.getElementById('ov-success').classList.add('show'),1200); } function triggerFail(){ if(gameOver)return; gameOver=true;typingActive=false; cmdDisplay.innerHTML=`<span style="color:var(--red)">CONNECTION TERMINATED</span>`; cmdJp.textContent='// サーバが接続を強制切断しました'; addLine('SEC','tag-err','<span style="color:var(--red)">✗ INTRUSION DETECTED — connection terminated by host</span>',0); setTimeout(()=>document.getElementById('ov-fail').classList.add('show'),1200); } // ── Boot sequence ─────────────────────────────────────────────────── async function boot(){ let d=0;const D=ms=>{const r=d;d+=ms;return r;}; await addLine('INIT','tag-sys','Backdoor module loaded — target: INTERPOL-CORE-01',D(0)); await addLine('INIT','tag-conn','Routing via TOR circuit: JP→NL→DE→FR',D(250)); await addLine('INIT','tag-sys','Connecting to C2 server... <span style="color:#00ff88">OK</span>',D(350)); await addDiv(D(80)); await addLine('SCAN','tag-conn','Port scan: 22(SSH) 443(HTTPS) 8443(ADMIN) 5432(DB)',D(250)); await addLine('SCAN','tag-warn','<span style="color:var(--amber)">⚠ CVE-2024-0731 detected on admin portal</span>',D(400)); await addLine('SCAN','tag-back','<span style="color:var(--purple)">⬡ Exploit payload staged — awaiting manual trigger</span>',D(350)); await addDiv(D(80)); await addLine('TYPE','tag-type','<span style="color:var(--blue)">▸ MANUAL INPUT REQUIRED — コマンドを入力して突破せよ</span>',D(200)); await addLine('TYPE','tag-type','<span style="color:var(--blue)">タイムリミット内に全コマンドを入力してください</span>',D(150)); // Start game setTimeout(()=>{ loadCmd(0); startTimer(); // Also start cracker animation after slight delay setTimeout(crackNextChar, 2000); }, d+300); } boot(); </script> </body> </html>>>>`;
const phase4 = `<<<<html lang="ja"> <head> <meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>INTERPOL GSN — PHASE 4: TAKEOVER</title> <style> @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=VT323&display=swap'); :root{ --green:#00ff41;--green-dim:#00aa2a;--green-dark:#003010; --amber:#ffb000;--red:#ff2222;--bg:#020a04;--purple:#cc44ff;--blue:#4ab8ff; } *{margin:0;padding:0;box-sizing:border-box;} body{background:var(--bg);color:var(--green);font-family:'Share Tech Mono',monospace;font-size:13px;height:100vh;overflow:hidden;} body::before{content:'';position:fixed;inset:0;background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,255,65,.025) 2px,rgba(0,255,65,.025) 4px);pointer-events:none;z-index:2000;} body::after{content:'';position:fixed;inset:0;background:radial-gradient(ellipse at center,transparent 55%,rgba(0,0,0,.88) 100%);pointer-events:none;z-index:1999;} #screen{display:flex;flex-direction:column;height:100vh;padding:14px 20px;gap:10px;} #hdr{display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid var(--red);padding-bottom:7px;flex-shrink:0;} .h-l{font-family:'VT323',monospace;font-size:20px;letter-spacing:3px;color:var(--red);} .h-r{font-size:10px;color:#ff8888;text-align:right;line-height:1.7;} #clk{color:var(--amber);} /* Alert banner */ #alert-banner{flex-shrink:0;border:1px solid var(--red);background:rgba(40,0,0,.8);padding:6px 14px;display:flex;align-items:center;gap:12px;animation:blink-bg 1.2s step-end infinite;} .alert-icon{color:var(--red);font-size:16px;animation:blink 0.6s step-end infinite;} .alert-text{color:var(--red);font-size:12px;letter-spacing:2px;} .alert-sub{color:#ff8888;font-size:10px;margin-left:auto;} #main{display:flex;gap:14px;flex:1;min-height:0;} /* Left: camera grid simulation */ #cam-panel{width:320px;flex-shrink:0;border:1px solid var(--red);display:flex;flex-direction:column;background:rgba(5,0,0,.95);} .cam-hdr{background:#1a0000;border-bottom:1px solid var(--red);padding:6px 10px;font-size:10px;color:var(--red);letter-spacing:2px;} #cam-grid{flex:1;display:grid;grid-template-columns:repeat(3,1fr);gap:3px;padding:6px;} .cam-cell{border:1px solid #330000;aspect-ratio:16/9;background:#050000;display:flex;align-items:center;justify-content:center;flex-direction:column;gap:2px;position:relative;overflow:hidden;} .cam-cell.taken{border-color:var(--red);} .cam-cell .cam-id{font-size:8px;color:#660000;} .cam-cell.taken .cam-id{color:var(--red);} .cam-cell .cam-dot{width:5px;height:5px;border-radius:50%;background:#330000;} .cam-cell.taken .cam-dot{background:var(--red);box-shadow:0 0 4px var(--red);animation:blink .8s step-end infinite;} .cam-count{padding:6px 10px;font-size:10px;color:var(--red);border-top:1px solid #330000;flex-shrink:0;} /* Right */ #right{flex:1;min-width:0;display:flex;flex-direction:column;gap:10px;} #log{flex:1;min-height:0;overflow:hidden;display:flex;flex-direction:column;gap:0;} .line{display:flex;align-items:baseline;gap:8px;line-height:1.7;opacity:0;animation:sli .1s ease forwards;flex-shrink:0;} .line .ts{color:var(--green-dim);font-size:10px;min-width:70px;flex-shrink:0;} .line .tag{font-size:10px;padding:0 4px;min-width:60px;text-align:center;flex-shrink:0;border-radius:2px;} .line .msg{flex:1;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;} .divider{border:none;border-top:1px solid var(--green-dark);margin:2px 0;flex-shrink:0;opacity:0;animation:sli .1s forwards;} .tag-sys {background:#003a1a;color:var(--green);border:1px solid var(--green-dim);} .tag-err {background:#3a0000;color:var(--red);border:1px solid #881010;} .tag-ok  {background:#003a1a;color:#00ff88;border:1px solid #009944;} .tag-back{background:#220033;color:var(--purple);border:1px solid #882299;} .tag-dl  {background:#002233;color:var(--blue);border:1px solid #224466;} .tag-warn{background:#3a2500;color:var(--amber);border:1px solid #886000;} /* File download panel */ #dl-panel{flex-shrink:0;border:1px solid var(--purple);background:rgba(10,0,20,.95);padding:12px 14px;} .dl-title{font-size:10px;color:var(--purple);letter-spacing:2px;margin-bottom:8px;} #file-list{display:flex;flex-direction:column;gap:4px;} .file-row{display:flex;align-items:center;gap:10px;font-size:11px;} .file-name{color:var(--blue);min-width:220px;} .file-size{color:var(--green-dim);min-width:60px;} .file-bar-outer{flex:1;height:6px;background:var(--green-dark);border:1px solid var(--green-dim);border-radius:2px;overflow:hidden;} .file-bar{height:100%;width:0%;transition:width .08s linear;} .file-bar.dl{background:var(--purple);box-shadow:0 0 4px var(--purple);} .file-bar.done{background:#00ff88;box-shadow:0 0 4px #00ff88;} .file-status{font-size:10px;min-width:50px;text-align:right;color:var(--green-dim);} .file-status.done{color:#00ff88;} #total-dl{font-size:11px;color:var(--amber);margin-top:8px;border-top:1px solid var(--green-dark);padding-top:6px;} /* Final overlay */ #ov-end{display:none;position:fixed;inset:0;background:rgba(0,0,0,.85);z-index:3000;align-items:center;justify-content:center;flex-direction:column;gap:20px;} #ov-end.show{display:flex;} .end-box{border:2px solid var(--purple);padding:32px 52px;text-align:center;background:var(--bg);animation:flk .5s ease forwards;max-width:520px;} .end-title{font-family:'VT323',monospace;font-size:42px;letter-spacing:5px;color:var(--purple);} .end-sub{font-size:11px;color:#cc88ff;margin-top:10px;line-height:2;letter-spacing:1px;} .end-files{margin-top:12px;text-align:left;font-size:10px;color:var(--green-dim);border:1px solid var(--green-dark);padding:8px 12px;} .end-files div{color:var(--green);margin:2px 0;} .btn-end{font-family:'Share Tech Mono',monospace;font-size:12px;color:var(--bg);background:var(--purple);border:none;padding:10px 28px;cursor:pointer;letter-spacing:3px;text-transform:uppercase;} .btn-end:hover{box-shadow:0 0 18px var(--purple);} @keyframes sli{to{opacity:1;}} @keyframes blink{50%{opacity:0;}} @keyframes blink-bg{50%{background:rgba(60,0,0,.6);}} @keyframes flk{0%{opacity:0;}20%{opacity:1;}25%{opacity:0;}40%{opacity:1;}45%{opacity:0;}60%{opacity:1;}100%{opacity:1;}} </style> </head> <body> <div id="screen"> <div id="hdr"> <div class="h-l">⚠ SYSTEM COMPROMISED — PHASE 4: TAKEOVER</div> <div class="h-r"><div id="clk"></div><div>INTERPOL-CORE-01 &nbsp;|&nbsp; UNAUTHORIZED ACCESS</div></div> </div> <div id="alert-banner"> <span class="alert-icon">▲</span> <span class="alert-text">CRITICAL SECURITY BREACH — ALL CAMERA FEEDS REDIRECTED</span> <span class="alert-sub">管理者権限奪取 — 外部ノードにコントロール移管中</span> </div> <div id="main"> <!-- Camera takeover grid --> <div id="cam-panel"> <div class="cam-hdr">▸ GLOBAL CAMERA FEED — UNAUTHORIZED CONTROL</div> <div id="cam-grid"></div> <div class="cam-count">Seized: <span id="cam-count" style="color:var(--red)">0</span> / 2,819</div> </div> <!-- Right --> <div id="right"> <div id="log"></div> <div id="dl-panel"> <div class="dl-title">▸ CLASSIFIED FILE EXFILTRATION — 機密ファイルのダウンロード</div> <div id="file-list"></div> <div id="total-dl" style="display:none"></div> </div> </div> </div> </div> <!-- Final overlay --> <div id="ov-end"> <div class="end-box"> <div class="end-title">MISSION COMPLETE</div> <div class="end-sub"> 全世界のINTERPOL監視カメラへのアクセス奪取に成功<br> 機密ファイル 5件 を組織サーバへ転送完了<br> 接続ログは消去済み — 追跡不可能 </div> <div class="end-files" style="margin-top:12px;"> <div>✓ interpol_agent_roster_2024.enc</div> <div>✓ gsn_master_crypto_keys.dat</div> <div>✓ operation_shadow_files.zip</div> <div>✓ witness_protection_db.sql</div> <div>✓ covert_ops_schedule_Q4.pdf</div> </div> </div> <button class="btn-end" onclick="showFinalCredits()">▶ エンドロール</button> </div> <!-- Credits screen --> <div id="ov-credits" style="display:none;position:fixed;inset:0;background:#000;z-index:4000;align-items:center;justify-content:center;flex-direction:column;gap:16px;"> <div style="font-family:'VT323',monospace;font-size:48px;letter-spacing:8px;color:var(--green);animation:flk .5s ease forwards;">DEMO COMPLETE</div> <div style="font-size:12px;color:var(--green-dim);text-align:center;line-height:2.2;letter-spacing:2px;"> INTERPOL GSN — Cinematic Hacking Demo<br> <span style="color:var(--amber)">フィクション作品です。実際のシステムへの不正アクセスは違法です。</span><br><br> <span style="color:var(--green)">Phase 1:</span> Terminal Boot<br> <span style="color:var(--green)">Phase 2:</span> Password Cracker + Typing Game<br> <span style="color:var(--green)">Phase 4:</span> System Takeover + File Exfiltration </div> <button onclick="location.href='terminal_boot.html'" style="font-family:'Share Tech Mono',monospace;font-size:12px;color:#000;background:var(--green);border:none;padding:10px 28px;cursor:pointer;letter-spacing:3px;margin-top:12px;">▶ 最初からプレイ</button> </div> <script> setInterval(()=>{document.getElementById('clk').textContent=new Date().toISOString().replace('T',' ').slice(0,19)+' UTC';},1000); // Camera grid const camGrid=document.getElementById('cam-grid'); const CAM_IDS=['PARIS-01','TOKYO-07','NYC-14','BERLIN-03','ROME-07','DUBAI-03','DELHI-04','LA-09','SAO_PAULO-05']; CAM_IDS.forEach((id,i)=>{ const c=document.createElement('div');c.className='cam-cell';c.id='cam'+i; c.innerHTML=`<div class="cam-dot"></div><div class="cam-id">${id}</div>`; camGrid.appendChild(c); }); let takenCams=0; function takeCam(idx){ const el=document.getElementById('cam'+idx); if(el)el.classList.add('taken'); takenCams+=Math.floor(Math.random()*320)+200; document.getElementById('cam-count').textContent=Math.min(2819,takenCams).toLocaleString(); } // Log const logEl=document.getElementById('log'); const MAX=18; function ts(){return new Date().toISOString().replace('T',' ').slice(11,23);} function addLine(tag,cls,msg,delay){ return new Promise(r=>setTimeout(()=>{ while(logEl.children.length>=MAX)logEl.children[0].remove(); const d=document.createElement('div');d.className='line'; d.innerHTML=`<span class="ts">${ts()}</span><span class="tag ${cls}">${tag}</span><span class="msg">${msg}</span>`; logEl.appendChild(d);r(); },delay)); } function addDiv(delay){return new Promise(r=>setTimeout(()=>{while(logEl.children.length>=MAX)logEl.children[0].remove();const h=document.createElement('hr');h.className='divider';logEl.appendChild(h);r();},delay));} // File download const FILES=[ {name:'interpol_agent_roster_2024.enc', size:'18.4 MB', dur:2800}, {name:'gsn_master_crypto_keys.dat',     size:'2.1 MB',  dur:1400}, {name:'operation_shadow_files.zip',     size:'347 MB',  dur:5000}, {name:'witness_protection_db.sql',      size:'92.7 MB', dur:3600}, {name:'covert_ops_schedule_Q4.pdf',     size:'4.8 MB',  dur:1800}, ]; function buildFileList(){ const fl=document.getElementById('file-list'); FILES.forEach((f,i)=>{ const row=document.createElement('div');row.className='file-row';row.id='fr'+i; row.innerHTML=` <span class="file-name">📄 ${f.name}</span> <span class="file-size">${f.size}</span> <div class="file-bar-outer"><div class="file-bar" id="fb${i}"></div></div> <span class="file-status" id="fs${i}">待機中</span> `; fl.appendChild(row); }); } function downloadFile(idx){ return new Promise(r=>{ const bar=document.getElementById('fb'+idx); const stat=document.getElementById('fs'+idx); bar.classList.add('dl'); stat.textContent='DL中...';stat.style.color='var(--purple)'; let v=0;const step=100/(FILES[idx].dur/40); const iv=setInterval(()=>{ v=Math.min(100,v+step+Math.random()*step*.3); bar.style.width=v+'%'; stat.textContent=Math.floor(v)+'%'; if(v>=100){ clearInterval(iv); bar.classList.remove('dl');bar.classList.add('done'); stat.textContent='完了';stat.className='file-status done'; r(); } },40); }); } async function runDownloads(delay){ await new Promise(r=>setTimeout(r,delay)); buildFileList(); document.getElementById('total-dl').style.display='block'; document.getElementById('total-dl').textContent='転送先: 組織サーバ [192.168.0.1 → TOR → UNKNOWN]'; for(let i=0;i<FILES.length;i++){ await addLine('EXFIL','tag-dl',`<span style="color:var(--blue)">Downloading: ${FILES[i].name} (${FILES[i].size})</span>`,0); await downloadFile(i); await addLine('EXFIL','tag-ok',`✓ Transfer complete: ${FILES[i].name}`,0); } await addDiv(0); await addLine('EXFIL','tag-back','<span style="color:var(--purple)">⬡ All files transferred — wiping access logs</span>',300); await addLine('EXFIL','tag-back','<span style="color:var(--purple)">⬡ Connection trace erased — ghost mode active</span>',500); setTimeout(()=>document.getElementById('ov-end').classList.add('show'),1200); } // Main sequence async function run(){ let d=0;const D=ms=>{const r=d;d+=ms;return r;}; await addLine('ROOT','tag-err','<span style="color:var(--red)">ROOT shell acquired on INTERPOL-CORE-01</span>',D(0)); await addLine('ROOT','tag-back','<span style="color:var(--purple)">⬡ Deploying control agent to all GSN nodes...</span>',D(300)); // Take cameras progressively for(let i=0;i<CAM_IDS.length;i++){ setTimeout(()=>takeCam(i),(i+1)*400+d); } await addLine('CTRL','tag-back','<span style="color:var(--purple)">⬡ Overriding RTSP stream routing table...</span>',D(350)); await addLine('CTRL','tag-err','<span style="color:var(--red)">All 2,819 feeds redirected to external node</span>',D(500)); await addDiv(D(100)); await addLine('CTRL','tag-warn','<span style="color:var(--amber)">⚠ INTERPOL security team detecting anomaly...</span>',D(350)); await addLine('CTRL','tag-back','<span style="color:var(--purple)">⬡ Deploying counter-IDS module — neutralizing</span>',D(450)); await addLine('CTRL','tag-ok','✓ Security alerts suppressed — blind zone active',D(400)); await addDiv(D(100)); await addLine('EXFIL','tag-dl','<span style="color:var(--blue)">Locating classified file directories...</span>',D(300)); await addLine('EXFIL','tag-dl','<span style="color:var(--blue)">Found: /secure/classified/ — 5 target files identified</span>',D(450)); await addLine('EXFIL','tag-dl','<span style="color:var(--blue)">Initiating encrypted exfiltration channel...</span>',D(350)); // Start downloads runDownloads(d+200); } function showFinalCredits(){ document.getElementById('ov-end').style.display='none'; const c=document.getElementById('ov-credits'); c.style.display='flex'; } run(); </script> </body> </html>>>>`;

// iframeに流し込み
document.getElementById('p1').srcdoc = phase1;
document.getElementById('p2').srcdoc = phase2;
document.getElementById('p4').srcdoc = phase4;

// 初期表示
show(1);

// ================== 切替 ==================
function show(n){
  document.querySelectorAll('iframe').forEach(f=>f.classList.remove('active'));
  document.getElementById('p'+n).classList.add('active');
}

// ================== フック（ボタン乗っ取り） ==================
window.addEventListener('message',e=>{
  if(e.data==='go2') show(2);
  if(e.data==='go4') show(4);
});

// iframe側から呼ぶための補助
function injectHooks(frame, next){
  frame.onload = ()=>{
    const doc = frame.contentWindow.document;
    const btn = doc.querySelector('button');
    if(btn){
      btn.onclick = ()=>{
        window.postMessage('go'+next,'*');
      };
    }
  };
}

// 各ボタンを差し替え
injectHooks(document.getElementById('p1'),2);
injectHooks(document.getElementById('p2'),4);

</script>
</body>
</html>
