<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>NovaVPN</title>

<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#000000">

<style>
html,body{
margin:0;
padding:0;
height:100%;
background:#000;
overflow:hidden;
font-family:-apple-system,BlinkMacSystemFont,sans-serif;
color:white;
}

canvas{position:fixed;inset:0;}

#boot{
position:fixed;
inset:0;
background:black;
display:flex;
align-items:center;
justify-content:center;
z-index:50;
animation:fadeOut 1s ease 3s forwards;
}

#logo{
width:80px;
height:80px;
background:white;
clip-path:polygon(50% 0%,70% 20%,85% 45%,70% 75%,50% 90%,30% 75%,15% 45%,30% 20%);
animation:pulse 1.5s infinite;
}

@keyframes pulse{
0%{transform:scale(1);opacity:.7}
50%{transform:scale(1.1);opacity:1}
100%{transform:scale(1);opacity:.7}
}

@keyframes fadeOut{
to{opacity:0;visibility:hidden}
}

#ui{
position:absolute;
inset:0;
padding:calc(env(safe-area-inset-top)+20px)
        calc(env(safe-area-inset-right)+20px)
        calc(env(safe-area-inset-bottom)+20px)
        calc(env(safe-area-inset-left)+20px);
display:flex;
flex-direction:column;
justify-content:space-between;
backdrop-filter:blur(20px);
}

.panel{
background:rgba(20,20,20,.6);
padding:15px;
border-radius:20px;
backdrop-filter:blur(20px);
}

button{
width:100%;
margin-top:10px;
padding:12px;
border:none;
border-radius:14px;
background:#ff0033;
color:white;
font-weight:600;
}

select{
width:100%;
margin-top:6px;
padding:10px;
border-radius:12px;
border:none;
}

.statgrid{
display:grid;
grid-template-columns:1fr 1fr;
gap:8px;
font-size:12px;
}

#chart{
height:60px;
}
</style>
</head>
<body>

<div id="boot"><div id="logo"></div></div>

<canvas id="globe"></canvas>

<div id="ui">
<div class="panel">
<select id="server"></select>
<select id="protocol">
<option>WireGuard</option>
<option>IKEv2</option>
<option>OpenVPN</option>
</select>
<button id="connect">Connect</button>
</div>

<div class="panel">
<div class="statgrid">
<div>Latency <div id="latency">--</div></div>
<div>Session <div id="session">00:00</div></div>
<div>IP <div id="ip">--</div></div>
<div>Encryption <div id="enc">--</div></div>
</div>
<canvas id="chart"></canvas>
</div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r152/three.min.js"></script>
<script>
const serverSelect = document.getElementById("server");
const protocolSelect = document.getElementById("protocol");
const connectBtn = document.getElementById("connect");
const latencyEl = document.getElementById("latency");
const sessionEl = document.getElementById("session");
const ipEl = document.getElementById("ip");
const encEl = document.getElementById("enc");

let state = "idle";
let session = 0;
let sessionTimer = null;
let networkTimer = null;

const servers = [
  {name:"Tokyo",ip:"103.21.244.1",basePing:22},
  {name:"LA",ip:"172.67.194.22",basePing:110},
  {name:"Frankfurt",ip:"104.26.10.78",basePing:180}
];

servers.forEach((s,i)=>{
  const o=document.createElement("option");
  o.value=i;
  o.textContent=s.name;
  serverSelect.appendChild(o);
});

function randomVariation(base){
  return base + Math.floor(Math.random()*15 - 7);
}

function startSession(){
  session = 0;
  sessionTimer = setInterval(()=>{
    session++;
    const m = String(Math.floor(session/60)).padStart(2,"0");
    const s = String(session%60).padStart(2,"0");
    sessionEl.textContent = m+":"+s;
  },1000);

  networkTimer = setInterval(()=>{
    const selected = servers[serverSelect.value];
    latencyEl.textContent = randomVariation(selected.basePing) + " ms";

    // たまにパケットロス風
    if(Math.random() < 0.05){
      latencyEl.textContent = "Timeout";
    }

  },2000);
}

function stopSession(){
  clearInterval(sessionTimer);
  clearInterval(networkTimer);
  sessionEl.textContent = "00:00";
  latencyEl.textContent = "--";
}

function simulateConnection(){
  state = "connecting";
  connectBtn.textContent = "Connecting...";
  connectBtn.disabled = true;

  setTimeout(()=>{
    // 10%で失敗
    if(Math.random() < 0.1){
      state = "idle";
      connectBtn.textContent = "Connect";
      connectBtn.disabled = false;
      alert("Handshake failed. Retry.");
      return;
    }

    state = "connected";
    connectBtn.textContent = "Disconnect";
    connectBtn.disabled = false;

    const selected = servers[serverSelect.value];
    ipEl.textContent = selected.ip;
    encEl.textContent = "AES-256-GCM ("+protocolSelect.value+")";

    startSession();

  },1500);
}

connectBtn.addEventListener("click",()=>{
  if(state === "idle"){
    simulateConnection();
  } else if(state === "connected"){
    state = "idle";
    connectBtn.textContent = "Connect";
    ipEl.textContent = "--";
    encEl.textContent = "--";
    stopSession();
  }
});

serverSelect.addEventListener("change",()=>{
  if(state === "connected"){
    alert("Server changed. Reconnecting...");
    stopSession();
    state = "idle";
    connectBtn.textContent = "Connect";
    ipEl.textContent = "--";
    encEl.textContent = "--";
  }
});
</script>

</body>
</html>
