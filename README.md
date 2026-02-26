<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>NovaVPN</title>

<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="NovaVPN">
<meta name="theme-color" content="#000000">

<style>
html, body {
  margin:0;
  padding:0;
  height:100%;
  background:#000;
  overflow:hidden;
  font-family:-apple-system,BlinkMacSystemFont,sans-serif;
  color:white;
}

canvas { position:fixed; inset:0; }

#ui {
  position:absolute;
  inset:0;
  padding:
    calc(env(safe-area-inset-top)+20px)
    calc(env(safe-area-inset-right)+20px)
    calc(env(safe-area-inset-bottom)+20px)
    calc(env(safe-area-inset-left)+20px);
  box-sizing:border-box;
  display:flex;
  flex-direction:column;
  justify-content:space-between;
  backdrop-filter: blur(20px);
}

#topbar {
  display:flex;
  justify-content:space-between;
  font-weight:600;
}

.panel {
  background:rgba(20,20,20,0.6);
  padding:15px;
  border-radius:16px;
  backdrop-filter:blur(20px);
}

select,button {
  width:100%;
  margin-top:8px;
  padding:10px;
  border:none;
  border-radius:10px;
  font-weight:600;
}

button {
  background:#ff0033;
  color:white;
}

.statgrid {
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:8px;
  font-size:13px;
}

.label { opacity:0.6; font-size:11px; }

#log {
  font-size:10px;
  max-height:80px;
  overflow-y:auto;
  opacity:0.6;
  margin-top:8px;
}
</style>
</head>

<body>

<canvas id="globe"></canvas>

<div id="ui">
  <div id="topbar">
    <div>NovaVPN</div>
    <div id="status">Idle</div>
  </div>

  <div class="panel">
    <div class="label">Server</div>
    <select id="server"></select>

    <div class="label">Protocol</div>
    <select id="protocol">
      <option>WireGuard</option>
      <option>IKEv2</option>
      <option>OpenVPN</option>
    </select>

    <button id="connect">Connect</button>
  </div>

  <div class="panel">
    <div class="statgrid">
      <div><div class="label">Latency</div><div id="latency">-- ms</div></div>
      <div><div class="label">Session</div><div id="session">00:00</div></div>
      <div><div class="label">Virtual IP</div><div id="ip">--</div></div>
      <div><div class="label">Encryption</div><div id="enc">--</div></div>
      <div><div class="label">Download</div><div id="down">0 KB</div></div>
      <div><div class="label">Upload</div><div id="up">0 KB</div></div>
    </div>
    <div id="log"></div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r152/three.min.js"></script>

<script>
/* ===== 3D Globe ===== */
const scene=new THREE.Scene();
const camera=new THREE.PerspectiveCamera(75,innerWidth/innerHeight,0.1,1000);
const renderer=new THREE.WebGLRenderer({canvas:globe,alpha:true});
renderer.setSize(innerWidth,innerHeight);
camera.position.z=3;

const sphere=new THREE.Mesh(
  new THREE.SphereGeometry(1,64,64),
  new THREE.MeshBasicMaterial({wireframe:true,color:0x2222ff})
);
scene.add(sphere);

function animate(){
  requestAnimationFrame(animate);
  sphere.rotation.y+=0.002;
  renderer.render(scene,camera);
}
animate();

addEventListener("resize",()=>{
  camera.aspect=innerWidth/innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth,innerHeight);
});

/* ===== Data ===== */
const servers=[
  {name:"Tokyo",ip:"103.21.244.1"},
  {name:"Los Angeles",ip:"172.67.194.22"},
  {name:"Frankfurt",ip:"104.26.10.78"}
];

servers.forEach((s,i)=>{
  const o=document.createElement("option");
  o.value=i;
  o.textContent=s.name;
  server.appendChild(o);
});

/* ===== VPN Simulation ===== */
let state="idle";
let session=0,down=0,up=0;
let timer,trafficTimer;

function log(msg){
  logDiv.innerHTML+=msg+"<br>";
  logDiv.scrollTop=logDiv.scrollHeight;
}

function connect(){
  if(state==="connected") return;
  state="connecting";
  status.textContent="Connecting...";
  log("Initiating handshake...");
  setTimeout(()=>{
    state="connected";
    status.textContent="Connected";
    ip.textContent=servers[server.value].ip;
    enc.textContent="AES-256-GCM";
    latency.textContent=Math.floor(Math.random()*50+20)+" ms";
    log("Tunnel established.");
    startSession();
  },2000);
}

function startSession(){
  session=0; down=0; up=0;
  timer=setInterval(()=>{
    session++;
    const m=String(Math.floor(session/60)).padStart(2,"0");
    const s=String(session%60).padStart(2,"0");
    sessionEl.textContent=m+":"+s;
  },1000);

  trafficTimer=setInterval(()=>{
    down+=Math.floor(Math.random()*50);
    up+=Math.floor(Math.random()*20);
    downEl.textContent=down+" KB";
    upEl.textContent=up+" KB";
  },1000);
}

connectBtn.addEventListener("click",connect);

/* ===== Element refs ===== */
const status=document.getElementById("status");
const server=document.getElementById("server");
const protocol=document.getElementById("protocol");
const connectBtn=document.getElementById("connect");
const latency=document.getElementById("latency");
const sessionEl=document.getElementById("session");
const ip=document.getElementById("ip");
const enc=document.getElementById("enc");
const downEl=document.getElementById("down");
const upEl=document.getElementById("up");
const logDiv=document.getElementById("log");
</script>

</body>
</html>
