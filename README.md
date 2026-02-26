<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>Quantum VPN</title>
<link rel="stylesheet" href="css/system.css">
</head>
<body>

<div class="app-shell">

  <header class="app-header">
    <h1>Quantum VPN</h1>
    <span id="statusBadge" class="badge">Disconnected</span>
  </header>

  <main class="app-content">

    <div class="card">

      <div class="field">
        <label>Server</label>
        <select id="serverSelect" class="dropdown">
          <option value="tokyo">Tokyo</option>
          <option value="newyork">New York</option>
          <option value="london">London</option>
          <option value="singapore">Singapore</option>
        </select>
      </div>

      <div class="field">
        <button id="toggleBtn" class="toggle-button">
          Connect
        </button>
      </div>

      <div class="metrics">
        <div class="metric">
          <span class="metric-label">Latency</span>
          <span id="latencyValue" class="metric-value">-- ms</span>
        </div>
        <div class="metric">
          <span class="metric-label">Throughput</span>
          <span id="throughputValue" class="metric-value">-- Mbps</span>
        </div>
      </div>

      <div class="progress">
        <div id="progressFill" class="progress-fill"></div>
      </div>

    </div>

  </main>

</div>

<script type="module" src="js/app.js"></script>
</body>
</html>
:root {
  --bg-primary: #0f1115;
  --bg-surface: #161a22;

  --text-primary: #e5e7eb;
  --text-muted: #9ca3af;

  --accent: #2dd4bf;
  --success: #22c55e;
  --danger: #ef4444;

  --border-subtle: #262b36;

  --space-2: 8px;
  --space-4: 16px;
  --space-6: 32px;

  --radius: 12px;

  --transition: 250ms cubic-bezier(0.4, 0, 0.2, 1);
}

body {
  margin: 0;
  font-family: system-ui, sans-serif;
  background: var(--bg-primary);
  color: var(--text-primary);
  display: flex;
  justify-content: center;
  padding: var(--space-6);
}

.app-shell {
  width: 100%;
  max-width: 720px;
}

.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--space-6);
}

.badge {
  padding: 4px 12px;
  border-radius: 999px;
  font-size: 12px;
  background: rgba(255,255,255,0.05);
  transition: var(--transition);
}

.badge.connected {
  background: rgba(34,197,94,0.15);
  color: var(--success);
}

.card {
  background: var(--bg-surface);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius);
  padding: var(--space-6);
}

.field {
  margin-bottom: var(--space-4);
}

.dropdown,
.toggle-button {
  width: 100%;
  padding: var(--space-2);
  border-radius: var(--radius);
  border: 1px solid var(--border-subtle);
  background: transparent;
  color: var(--text-primary);
  transition: var(--transition);
}

.dropdown:focus,
.toggle-button:hover {
  border-color: var(--accent);
}

.metrics {
  display: flex;
  justify-content: space-between;
  margin-top: var(--space-4);
}

.metric-label {
  font-size: 12px;
  color: var(--text-muted);
}

.metric-value {
  font-weight: 500;
}

.progress {
  margin-top: var(--space-4);
  height: 6px;
  background: rgba(255,255,255,0.05);
  border-radius: 999px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  width: 0%;
  background: var(--accent);
  transition: var(--transition);
}
const STATES = {
  DISCONNECTED: "DISCONNECTED",
  CONNECTING: "CONNECTING",
  CONNECTED: "CONNECTED"
};

let state = STATES.DISCONNECTED;
let progress = 0;

const badge = document.getElementById("statusBadge");
const toggleBtn = document.getElementById("toggleBtn");
const progressFill = document.getElementById("progressFill");
const latencyValue = document.getElementById("latencyValue");
const throughputValue = document.getElementById("throughputValue");

function render() {
  badge.textContent = state;

  if (state === STATES.CONNECTED) {
    badge.classList.add("connected");
    toggleBtn.textContent = "Disconnect";
  } else {
    badge.classList.remove("connected");
    toggleBtn.textContent = "Connect";
  }

  progressFill.style.width = progress + "%";
}

function simulateConnection() {
  state = STATES.CONNECTING;
  progress = 0;

  const interval = setInterval(() => {
    progress += 5;

    if (progress >= 100) {
      progress = 100;
      clearInterval(interval);
      state = STATES.CONNECTED;
      simulateMetrics();
    }

    render();
  }, 120);
}

function simulateMetrics() {
  setInterval(() => {
    if (state !== STATES.CONNECTED) return;

    const latency = 20 + Math.random() * 40;
    const throughput = 100 + Math.random() * 80;

    latencyValue.textContent = latency.toFixed(0) + " ms";
    throughputValue.textContent = throughput.toFixed(1) + " Mbps";
  }, 800);
}

toggleBtn.addEventListener("click", () => {
  if (state === STATES.DISCONNECTED) {
    simulateConnection();
  } else {
    state = STATES.DISCONNECTED;
    progress = 0;
    latencyValue.textContent = "-- ms";
    throughputValue.textContent = "-- Mbps";
    render();
  }
});

render();
