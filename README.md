<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NEXUS-9 SYSTEM</title>

<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap" rel="stylesheet">

<style>
body{
margin:0;
background:#05070A;
color:#00FFFF;
font-family:'Orbitron',monospace;
overflow:hidden;
}

.screen{
position:absolute;
width:100%;
height:100%;
opacity:0;
display:none;
transition:opacity 0.6s ease-in-out;
}

.screen.active{
display:block;
opacity:1;
}

/* ノイズ */
body::after{
content:"";
position:fixed;
width:100%;
height:100%;
background:repeating-linear-gradient(
0deg,
rgba(0,255,255,0.02),
rgba(0,255,255,0.02) 1px,
transparent 1px,
transparent 2px
);
pointer-events:none;
}

/* Entry */
#entry{
display:flex;
flex-direction:column;
justify-content:center;
align-items:center;
}

canvas{
position:absolute;
width:100%;
height:100%;
z-index:0;
}

.title{
font-size:3rem;
font-weight:900;
text-shadow:0 0 20px #0ff;
z-index:1;
}

/* ボタン */
.button{
z-index:1;
margin:20px;
padding:15px 40px;
border:1px solid #00FFFF55;
cursor:pointer;
background:rgba(0,255,255,0.05);
transition:0.2s;
}
.button:hover{
transform:translateY(-5px);
box-shadow:0 0 20px cyan;
}

/* Desktop */
#desktop{
display:grid;
grid-template-columns:repeat(auto-fill,100px);
gap:30px;
padding:40px;
}

.file{
text-align:center;
cursor:pointer;
}
.icon{
width:60px;
height:60px;
margin:auto;
border:1px solid cyan;
box-shadow:0 0 10px cyan;
}

/* Window */
.window{
position:absolute;
width:300px;
background:#001520;
border:1px solid cyan;
box-shadow:0 0 20px cyan;
}
.window-header{
padding:5px;
cursor:move;
background:#002b3a;
}
.close-btn{
float:right;
cursor:pointer;
}

/* Connection */
#connection canvas{
position:absolute;
width:100%;
height:100%;
}
</style>
</head>

<body>

<!-- ENTRY -->
<div id="entry" class="screen active">
<canvas id="bg"></canvas>
<div class="title">NEXUS-9 SYSTEM ACCESS</div>
<div class="button" onclick="switchScreen('desktop')">DESKTOP INTERFACE</div>
</div>

<!-- DESKTOP -->
<div id="desktop" class="screen">
<div class="file" onclick="openEUNode()">
<div class="icon"></div>EU_NODE.exe
</div>
<div class="file" onclick="openBank()">
<div class="icon"></div>BANK_SYS.enc
</div>
<div class="file" onclick="openSurv()">
<div class="icon"></div>SURV_NET.log
</div>
</div>

<!-- CONNECTION -->
<div id="connection" class="screen">
<canvas id="mapCanvas"></canvas>
</div>

<div id="windows"></div>

<script>

/* 画面切替 */
let current='entry';
function switchScreen(target){
let c=document.getElementById(current);
let n=document.getElementById(target);
c.style.opacity=0;
setTimeout(()=>{
c.classList.remove('active');
n.classList.add('active');
n.style.opacity=1;
current=target;
},600);
}

/* 背景ネットワーク */
const canvas=document.getElementById("bg");
const ctx=canvas.getContext("2d");
canvas.width=window.innerWidth;
canvas.height=window.innerHeight;

let particles=[];
for(let i=0;i<80;i++){
particles.push({
x:Math.random()*canvas.width,
y:Math.random()*canvas.height,
dx:(Math.random()-0.5)*0.5,
dy:(Math.random()-0.5)*0.5
});
}

let packets=[];
let intensity=1;

function createPackets(){
particles.forEach(p1=>{
particles.forEach(p2=>{
let dx=p1.x-p2.x;
let dy=p1.y-p2.y;
let dist=Math.sqrt(dx*dx+dy*dy);
if(dist<120 && Math.random()<0.002*intensity){
packets.push({from:p1,to:p2,progress:0});
}
});
});
}

function drawPackets(){
packets.forEach(p=>{
p.progress+=0.01+(p.progress*0.02);
let x=p.from.x+(p.to.x-p.from.x)*p.progress;
let y=p.from.y+(p.to.y-p.from.y)*p.progress;
ctx.beginPath();
ctx.arc(x,y,2,0,Math.PI*2);
ctx.fill();
});
packets=packets.filter(p=>p.progress<1);
}

function drawLines(){
for(let i=0;i<particles.length;i++){
for(let j=i+1;j<particles.length;j++){
let dx=particles[i].x-particles[j].x;
let dy=particles[i].y-particles[j].y;
let dist=Math.sqrt(dx*dx+dy*dy);
if(dist<120){
ctx.beginPath();
ctx.strokeStyle="rgba(0,255,255,"+(1-dist/120)+")";
ctx.moveTo(particles[i].x,particles[i].y);
ctx.lineTo(particles[j].x,particles[j].y);
ctx.stroke();
}
}
}
}

function animate(){
ctx.clearRect(0,0,canvas.width,canvas.height);
particles.forEach(p=>{
p.x+=p.dx;
p.y+=p.dy;
if(p.x<0||p.x>canvas.width)p.dx*=-1;
if(p.y<0||p.y>canvas.height)p.dy*=-1;
ctx.beginPath();
ctx.arc(p.x,p.y,2,0,Math.PI*2);
ctx.fillStyle="#0ff";
ctx.fill();
});
createPackets();
drawLines();
drawPackets();
requestAnimationFrame(animate);
}
animate();

/* Window */
let zIndex=10;
function openWindow(title,content){
const win=document.createElement("div");
win.className="window";
win.style.top="100px";
win.style.left="100px";
win.style.zIndex=zIndex++;

win.innerHTML=`
<div class="window-header">${title}<span class="close-btn">X</span></div>
<div>${content}</div>
`;

win.querySelector(".close-btn").onclick=()=>win.remove();
win.onmousedown=()=>win.style.zIndex=zIndex++;

document.getElementById("windows").appendChild(win);
}

/* EU */
function openEUNode(){
openWindow("EU_NODE","Connecting...");
setTimeout(()=>{
switchScreen('connection');
startMap();
},1500);
}

/* MAP */
function startMap(){
const c=document.getElementById("mapCanvas");
const ctx=c.getContext("2d");
c.width=window.innerWidth;
c.height=window.innerHeight;

let jp={x:200,y:500};
let eu={x:800,y:200};
let t=0;

function loop(){
ctx.clearRect(0,0,c.width,c.height);
ctx.beginPath();
ctx.moveTo(jp.x,jp.y);
ctx.lineTo(eu.x,eu.y);
ctx.strokeStyle="#0ff";
ctx.stroke();

t+=0.01;
if(t>1)t=0;

let x=jp.x+(eu.x-jp.x)*t;
let y=jp.y+(eu.y-jp.y)*t;

ctx.beginPath();
ctx.arc(x,y,5,0,Math.PI*2);
ctx.fill();

requestAnimationFrame(loop);
}
loop();
}

/* BANK */
function openBank(){
openWindow("BANK","Decrypting...");
}

/* SURV */
function openSurv(){
openWindow("SURV","Accessing...");
}

</script>

</body>
</html>
