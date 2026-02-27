<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Neon Network Map</title>
<style>
body{margin:0;overflow:hidden;background:black;font-family:Arial;}
#ui{
position:absolute;
top:20px;
left:50%;
transform:translateX(-50%);
z-index:10;
background:rgba(0,0,0,0.6);
padding:10px 20px;
border-radius:20px;
color:#00ffff;
border:1px solid #00ffff;
}
select,button{
background:black;
color:#00ffff;
border:1px solid #00ffff;
padding:6px 12px;
border-radius:12px;
}
#tooltip{
position:absolute;
color:#00ffff;
background:rgba(0,0,0,0.8);
padding:5px 10px;
border:1px solid #00ffff;
border-radius:6px;
display:none;
font-size:12px;
}
</style>
</head>
<body>

<div id="ui">
<select id="server">
<option value="ny">New York Route</option>
<option value="london">London Route</option>
<option value="singapore">Singapore Route</option>
<option value="dubai">Dubai Route</option>
</select>
<button onclick="connectNetwork()">Connect</button>
</div>

<div id="tooltip"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128/examples/js/loaders/SVGLoader.js"></script>
<script>
let scene=new THREE.Scene();
let camera=new THREE.OrthographicCamera(
window.innerWidth/-2,
window.innerWidth/2,
window.innerHeight/2,
window.innerHeight/-2,
1,1000);
camera.position.z=10;

let renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setClearColor(0x000000);
document.body.appendChild(renderer.domElement);

let width=window.innerWidth;
let height=window.innerHeight;

// --------------------
// SVG 世界地図読み込み
// --------------------
const loader=new THREE.SVGLoader();
loader.load(
'https://upload.wikimedia.org/wikipedia/commons/8/80/World_map_-_low_resolution.svg',
function(data){
const paths=data.paths;
paths.forEach(path=>{
const shapes=THREE.SVGLoader.createShapes(path);
shapes.forEach(shape=>{
const geo=new THREE.ShapeGeometry(shape);
const mat=new THREE.MeshBasicMaterial({
color:0x00ffff,
transparent:true,
opacity:0.15,
blending:THREE.AdditiveBlending
});
const mesh=new THREE.Mesh(geo,mat);
mesh.scale.set(3,-3,1);
mesh.position.set(-width/2,-height/2,0);
scene.add(mesh);
});
});
});

// --------------------
// 緯度経度→XY
// --------------------
function latLonToXY(lat,lon){
let x=(lon+180)/360*width-width/2;
let y=(90-lat)/180*height-height/2;
return new THREE.Vector3(x,y,1);
}

// --------------------
// ネオンノード
// --------------------
let nodes=[];
function createNode(lat,lon,color,name){
let pos=latLonToXY(lat,lon);

let geo=new THREE.CircleGeometry(6,32);
let mat=new THREE.MeshBasicMaterial({
color:color,
blending:THREE.AdditiveBlending
});
let node=new THREE.Mesh(geo,mat);
node.position.copy(pos);
node.userData.name=name;
scene.add(node);
nodes.push(node);
return node;
}

// --------------------
// 順番進行ブリッジ
// --------------------
function animateBridge(start,end,color,delay){
setTimeout(()=>{
let progress=0;

let geo=new THREE.BufferGeometry().setFromPoints([start,start]);
let mat=new THREE.LineBasicMaterial({
color:color,
transparent:true,
opacity:0.9,
blending:THREE.AdditiveBlending
});
let line=new THREE.Line(geo,mat);
scene.add(line);

function grow(){
progress+=0.02;
if(progress>1)progress=1;

let current=start.clone().lerp(end,progress);
line.geometry.setFromPoints([start,current]);

if(progress<1){
requestAnimationFrame(grow);
}
}
grow();

},delay);
}

// --------------------
// ルート定義
// --------------------
let routes={
ny:[
{lat:35.6895,lon:139.6917,name:"Tokyo"},
{lat:55.7558,lon:37.6173,name:"Moscow"},
{lat:51.5074,lon:-0.1278,name:"London"},
{lat:40.7128,lon:-74.0060,name:"New York"}
],
london:[
{lat:35.6895,lon:139.6917,name:"Tokyo"},
{lat:28.6139,lon:77.2090,name:"Delhi"},
{lat:48.8566,lon:2.3522,name:"Paris"},
{lat:51.5074,lon:-0.1278,name:"London"}
],
singapore:[
{lat:35.6895,lon:139.6917,name:"Tokyo"},
{lat:22.3193,lon:114.1694,name:"Hong Kong"},
{lat:1.3521,lon:103.8198,name:"Singapore"},
{lat:-33.8688,lon:151.2093,name:"Sydney"}
],
dubai:[
{lat:35.6895,lon:139.6917,name:"Tokyo"},
{lat:19.0760,lon:72.8777,name:"Mumbai"},
{lat:25.2048,lon:55.2708,name:"Dubai"},
{lat:41.0082,lon:28.9784,name:"Istanbul"}
]
};

// --------------------
// 接続
// --------------------
function connectNetwork(){
nodes.forEach(n=>scene.remove(n));
nodes=[];

let selected=document.getElementById("server").value;
let route=routes[selected];

let created=[];
route.forEach((city,i)=>{
let color=[0xff0033,0x00ffff,0xffff00,0xff8800][i];
let node=createNode(city.lat,city.lon,color,city.name);
created.push(node);
});

for(let i=0;i<created.length-1;i++){
animateBridge(
created[i].position,
created[i+1].position,
created[i].material.color,
i*1000
);
}
}

// --------------------
// ホバー表示
// --------------------
let raycaster=new THREE.Raycaster();
let mouse=new THREE.Vector2();
let tooltip=document.getElementById("tooltip");

window.addEventListener("mousemove",event=>{
mouse.x=(event.clientX/window.innerWidth)*2-1;
mouse.y=-(event.clientY/window.innerHeight)*2+1;

raycaster.setFromCamera(mouse,camera);
let intersects=raycaster.intersectObjects(nodes);

if(intersects.length>0){
tooltip.style.display="block";
tooltip.style.left=event.clientX+10+"px";
tooltip.style.top=event.clientY+10+"px";
tooltip.innerHTML=intersects[0].object.userData.name;
}else{
tooltip.style.display="none";
}
});

// --------------------
// ズーム
// --------------------
window.addEventListener("wheel",e=>{
camera.zoom+=e.deltaY*-0.001;
camera.zoom=Math.min(Math.max(0.5,camera.zoom),5);
camera.updateProjectionMatrix();
});

// --------------------
function animate(){
requestAnimationFrame(animate);
renderer.render(scene,camera);
}
animate();
</script>
</body>
</html>
