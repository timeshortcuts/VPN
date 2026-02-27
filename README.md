<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>NovaCore X Ultimate</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

<style>
:root{
--green:#00ff88;
--red:#ff0033;
}

*{margin:0;padding:0;box-sizing:border-box;font-family:-apple-system;}
body{background:#000;color:#fff;height:100vh;overflow:hidden;}

.screen{
position:absolute;
width:100%;
height:100%;
top:0;
left:0;
display:flex;
flex-direction:column;
align-items:center;
justify-content:center;
transition:.8s;
}

.hidden{opacity:0;transform:scale(.95);pointer-events:none;}

.logo{
width:90px;height:90px;
background:radial-gradient(circle,var(--green),#003322);
clip-path:polygon(50% 0%,90% 25%,90% 75%,50% 100%,10% 75%,10% 25%);
box-shadow:0 0 40px var(--green);
animation:pulse 2s infinite alternate;
margin-bottom:20px;
}
@keyframes pulse{
from{box-shadow:0 0 20px var(--green);}
to{box-shadow:0 0 60px var(--green);}
}

select,button{
width:80%;
padding:14px;
margin:8px 0;
border-radius:14px;
border:none;
font-size:16px;
}

select{
background:#111;
color:var(--green);
border:1px solid var(--green);
}

button{
background:var(--green);
color:#000;
font-weight:600;
cursor:pointer;
}

.progress{
width:80%;
height:8px;
background:#111;
border-radius:8px;
overflow:hidden;
margin-top:15px;
}
.bar{
height:100%;
width:0%;
background:var(--red);
transition:width .3s;
}
.statusText{margin-top:15px;color:var(--green);}

/* 3Dマップ画面 */
#mapContainer{
position:absolute;
width:100%;
height:100%;
display:flex;
justify-content:center;
align-items:center;
background:radial-gradient(circle,#001a11,#000);
}

#metrics{
position:absolute;
top:20px;
right:20px;
background:rgba(0,0,0,0.4);
padding:15px;
border-radius:15px;
box-shadow:0 0 20px var(--green);
backdrop-filter:blur(10px);
display:none;
font-size:14px;
}

#log{
position:absolute;
bottom:0;
width:100%;
height:120px;
background:#000;
color:var(--green);
font-size:12px;
overflow:hidden;
padding:5px;
}

.statusBox{
background:#000;
padding:25px;
border-radius:20px;
box-shadow:0 0 40px var(--green);
}

.safe{
padding-bottom:env(safe-area-inset-bottom);
padding-top:env(safe-area-inset-top);
}
</style>
</head>
<body>

<!-- Phase1 -->
<div id="phase1" class="screen safe">
<div class="logo"></div>
<h1>NOVA CORE X</h1>

<select id="server">
<option value="35.6895,139.6917">Tokyo</option>
<option value="40.7128,-74.0060">New York</option>
<option value="51.5074,-0.1278">London</option>
<option value="52.52,13.405">Berlin</option>
<option value="1.3521,103.8198">Singapore</option>
</select>

<button onclick="startConnection()">CONNECT</button>

<div class="progress"><div class="bar" id="bar"></div></div>
<div class="statusText" id="statusText"></div>
</div>

<!-- 3D Map -->
<div id="mapContainer" class="screen hidden">
<div id="metrics">
Ping: <span id="ping"></span> ms<br>
Down: <span id="down"></span> Mbps<br>
Up: <span id="up"></span> Mbps
</div>
<div id="log"></div>
</div>

<!-- Phase2 -->
<div id="phase2" class="screen hidden safe">
<h2 style="color:var(--green);margin-bottom:20px;">SECURE TUNNEL ACTIVE</h2>
<div class="statusBox">
<p>Status: Encrypted</p>
<p>Protocol: NovaShield X</p>
<p>IP Masking: Active</p>
<p>Latency: <span id="finalPing"></span> ms</p>
</div>
<button onclick="location.reload()">DISCONNECT</button>
</div>

<script>
/* =====================
   Phase1 Progress
===================== */
const steps=[
"Initializing secure tunnel...",
"Authenticating server...",
"Establishing encryption...",
"Routing global traffic...",
"Securing DNS layer...",
"Finalizing session..."
];

function startConnection(){
let bar=document.getElementById("bar");
let status=document.getElementById("statusText");
let width=0; let stepIndex=0;

let interval=setInterval(()=>{
width+=2;
bar.style.width=width+"%";

if(width%15===0 && stepIndex<steps.length){
status.innerText=steps[stepIndex++];
}

if(width>=100){
clearInterval(interval);
status.innerText="Connection Established";
setTimeout(show3DMap,800);
}
},100);
}

/* =====================
   3D Globe Setup
===================== */
let scene=new THREE.Scene();
let camera=new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,1000);
let renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.getElementById("mapContainer").appendChild(renderer.domElement);

let loader=new THREE.TextureLoader();
let earthTexture=loader.load('https://threejs.org/examples/textures/land_ocean_ice_cloud_2048.jpg');
let bumpTexture=loader.load('https://threejs.org/examples/textures/earthbump1k.jpg');
let specTexture=loader.load('https://threejs.org/examples/textures/earthspec1k.jpg');

let material=new THREE.MeshPhongMaterial({
map:earthTexture,
bumpMap:bumpTexture,
bumpScale:0.05,
specularMap:specTexture,
specular:new THREE.Color('grey')
});

let globe=new THREE.Mesh(new THREE.SphereGeometry(2,64,64),material);
scene.add(globe);

camera.position.z=5;

let ambient=new THREE.AmbientLight(0x404040);
scene.add(ambient);

let light=new THREE.DirectionalLight(0xffffff,1);
light.position.set(5,3,5);
scene.add(light);

function animate(){
requestAnimationFrame(animate);
globe.rotation.y+=0.0015;
renderer.render(scene,camera);
}
animate();

function latLonToVector3(lat,lon,radius){
let phi=(90-lat)*(Math.PI/180);
let theta=(lon+180)*(Math.PI/180);
return new THREE.Vector3(
-(radius*Math.sin(phi)*Math.cos(theta)),
radius*Math.cos(phi),
radius*Math.sin(phi)*Math.sin(theta)
);
}

function focusOn(target){
let dir=target.clone().normalize();
let targetRotation=Math.atan2(dir.x,dir.z);
let startRotation=globe.rotation.y;
let duration=1000;
let startTime=performance.now();

function rotate(now){
let elapsed=now-startTime;
let progress=Math.min(elapsed/duration,1);
globe.rotation.y=startRotation+(targetRotation-startRotation)*progress;
if(progress<1){requestAnimationFrame(rotate);}
}
requestAnimationFrame(rotate);
}

function show3DMap(){
document.getElementById("phase1").classList.add("hidden");
document.getElementById("mapContainer").classList.remove("hidden");
connect3D();
simulateTraffic();
startLogs();
setTimeout(finishConnection,6000);
}

function connect3D(){
let val=document.getElementById("server").value.split(",");
let lat=parseFloat(val[0]);
let lon=parseFloat(val[1]);

let start=latLonToVector3(35.6895,139.6917,2);
let end=latLonToVector3(lat,lon,2);

let curve=new THREE.QuadraticBezierCurve3(
start,
start.clone().add(end).multiplyScalar(0.5).setLength(3),
end
);

let points=curve.getPoints(50);
let geometry=new THREE.BufferGeometry().setFromPoints(points);
let materialLine=new THREE.LineBasicMaterial({color:0xff0000});
let line=new THREE.Line(geometry,materialLine);
scene.add(line);

focusOn(end);
}

/* Traffic + Logs */
let latestPing=0;

function simulateTraffic(){
document.getElementById("metrics").style.display="block";
setInterval(()=>{
latestPing=(10+Math.random()*40).toFixed(0);
document.getElementById("ping").innerText=latestPing;
document.getElementById("down").innerText=(50+Math.random()*100).toFixed(1);
document.getElementById("up").innerText=(10+Math.random()*50).toFixed(1);
},1000);
}

function startLogs(){
let log=document.getElementById("log");
setInterval(()=>{
log.innerText+="["+new Date().toLocaleTimeString()+"] Secure packet transmitted\n";
log.scrollTop=log.scrollHeight;
},800);
}

function finishConnection(){
document.getElementById("mapContainer").classList.add("hidden");
document.getElementById("phase2").classList.remove("hidden");
document.getElementById("finalPing").innerText=latestPing;
}
</script>

</body>
</html>
