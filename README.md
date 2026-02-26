<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NovaVPN</title>

<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#000000">

<style>
body {
  margin: 0;
  background: radial-gradient(circle at center, #0a0a0a 0%, #000 70%);
  color: white;
  font-family: -apple-system, BlinkMacSystemFont, sans-serif;
  overflow: hidden;
}

canvas { position: fixed; top:0; left:0; }

#ui {
  position: absolute;
  z-index: 10;
  width: 100%;
  padding: 20px;
  box-sizing: border-box;
}

#header {
  display:flex;
  justify-content:space-between;
  font-size:18px;
  font-weight:600;
}

#serverPanel {
  margin-top:20px;
  display:flex;
  gap:10px;
}

select, button {
  padding:10px;
  border:none;
  border-radius:8px;
  font-weight:600;
}

button {
  background:#ff0033;
  color:white;
}

#stats {
  margin-top:15px;
  font-size:14px;
  opacity:0.8;
}

#log {
  margin-top:15px;
  font-size:11px;
  max-height:100px;
  overflow-y:auto;
  opacity:0.6;
}
</style>
</head>

<body>

<div id="ui">
  <div id="header">
    <span>NovaVPN</span>
    <span id="status">Idle</span>
  </div>

  <div id="serverPanel">
    <select id="serverSelect"></select>
    <button id="connectBtn">Connect</button>
  </div>

  <div id="stats">
    Latency: <span id="latency">--</span> ms |
    Session: <span id="session">00:00</span>
  </div>

  <div id="log"></div>
</div>

<canvas id="globe"></canvas>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r152/three.min.js"></script>

<script>
const canvas = document.getElementById("globe");
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({canvas, alpha:true});
renderer.setSize(window.innerWidth, window.innerHeight);
camera.position.z = 3;

// Earth
const geometry = new THREE.SphereGeometry(1, 64, 64);
const material = new THREE.MeshBasicMaterial({wireframe:true, color:0x2222ff});
const earth = new THREE.Mesh(geometry, material);
scene.add(earth);

let line;

const servers = [
  { name:"Tokyo", lat:35.6, lon:139.6 },
  { name:"Los Angeles", lat:34.0, lon:-118.2 },
  { name:"Frankfurt", lat:50.1, lon:8.6 }
];

const serverSelect = document.getElementById("serverSelect");
servers.forEach((s,i)=>{
  const o=document.createElement("option");
  o.value=i;
  o.textContent=s.name;
  serverSelect.appendChild(o);
});

function latLonToVector3(lat, lon, radius=1){
  const phi=(90-lat)*(Math.PI/180);
  const theta=(lon+180)*(Math.PI/180);
  return new THREE.Vector3(
    -(radius*Math.sin(phi)*Math.cos(theta)),
    radius*Math.cos(phi),
    radius*Math.sin(phi)*Math.sin(theta)
  );
}

function createLine(target){
  if(line) scene.remove(line);

  const start=latLonToVector3(0,0);
  const end=latLonToVector3(target.lat,target.lon);

  const points=[];
  for(let i=0;i<=50;i++){
    const p=start.clone().lerp(end,i/50);
    p.normalize().multiplyScalar(1.2);
    points.push(p);
  }

  const geo=new THREE.BufferGeometry().setFromPoints(points);
  const mat=new THREE.LineBasicMaterial({color:0xff0033});
  line=new THREE.Line(geo,mat);
  scene.add(line);
}

function log(msg){
  const l=document.getElementById("log");
  l.innerHTML+=msg+"<br>";
  l.scrollTop=l.scrollHeight;
}

let state="idle";
let sessionTime=0;
let timer;

function connect(){
  if(state==="connected") return;

  const selected=servers[serverSelect.value];
  state="connecting";
  document.getElementById("status").textContent="Connecting...";
  log("Initiating secure handshake...");
  createLine(selected);

  setTimeout(()=>{
    state="connected";
    document.getElementById("status").textContent="Connected";
    const latency=Math.floor(Math.random()*80+20);
    document.getElementById("latency").textContent=latency;
    log("Tunnel established. AES-256 encrypted.");
    startTimer();
  },2000);
}

function startTimer(){
  sessionTime=0;
  timer=setInterval(()=>{
    sessionTime++;
    const m=String(Math.floor(sessionTime/60)).padStart(2,"0");
    const s=String(sessionTime%60).padStart(2,"0");
    document.getElementById("session").textContent=m+":"+s;
  },1000);
}

document.getElementById("connectBtn").addEventListener("click",connect);

function animate(){
  requestAnimationFrame(animate);
  earth.rotation.y+=0.002;
  renderer.render(scene,camera);
}
animate();

window.addEventListener("resize",()=>{
  camera.aspect=window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth,window.innerHeight);
});
</script>

</body>
</html>
