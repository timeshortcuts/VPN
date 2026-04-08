<html lang="ja">
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
 
