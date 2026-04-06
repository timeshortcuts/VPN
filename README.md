<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NEXUS-9 SYSTEM</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@700;900&display=swap" rel="stylesheet">
    <style>
        /* ===== GLOBAL STYLES ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            width: 100vw;
            height: 100vh;
            background: #05070A;
            color: #00FFFF;
            font-family: 'Orbitron', monospace;
            overflow: hidden;
            position: relative;
        }

        body::before {
            content: "";
            position: fixed;
            width: 100%;
            height: 100%;
            background: repeating-linear-gradient(
                0deg,
                rgba(0, 255, 255, 0.03),
                rgba(0, 255, 255, 0.03) 1px,
                transparent 1px,
                transparent 2px
            );
            pointer-events: none;
            z-index: 999;
        }

        .screen {
            position: absolute;
            width: 100%;
            height: 100%;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            pointer-events: none;
        }

        .screen.active {
            opacity: 1;
            visibility: visible;
            pointer-events: auto;
        }

        /* ===== ENTRY SCREEN ===== */
        #entry {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
        }

        #bg-canvas {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
        }

        .entry-content {
            z-index: 10;
            text-align: center;
        }

        .entry-title {
            font-size: 3.5rem;
            font-weight: 900;
            text-shadow: 0 0 30px #00FFFF, 0 0 60px #0088FF;
            margin-bottom: 60px;
            letter-spacing: 3px;
            animation: titleFlicker 3s infinite;
        }

        @keyframes titleFlicker {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.98; }
        }

        .button-group {
            display: flex;
            gap: 30px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .btn {
            padding: 18px 50px;
            border: 2px solid #00FFFF;
            background: rgba(0, 255, 255, 0.08);
            color: #00FFFF;
            font-family: 'Orbitron', monospace;
            font-weight: 700;
            font-size: 1.1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .btn::before {
            content: "";
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: rgba(0, 255, 255, 0.1);
            transition: left 0.3s ease;
            z-index: -1;
        }

        .btn:hover {
            transform: translateY(-3px) scale(1.03);
            box-shadow: 0 0 40px rgba(0, 255, 255, 0.6);
            text-shadow: 0 0 10px #00FFFF;
        }

        /* ===== DESKTOP SCREEN ===== */
        #desktop {
            display: grid;
            grid-template-columns: repeat(auto-fill, 100px);
            gap: 40px;
            padding: 40px;
            background: linear-gradient(135deg, #05070A 0%, #0a0d1a 100%);
            align-content: start;
        }

        .desktop-header {
            grid-column: 1 / -1;
            padding: 15px 20px;
            border-bottom: 2px solid #00FFFF;
            font-size: 0.9rem;
            margin-bottom: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .file-icon {
            text-align: center;
            cursor: pointer;
            transition: all 0.2s ease;
            padding: 10px;
        }

        .file-icon:hover {
            transform: scale(1.1);
        }

        .icon-image {
            width: 60px;
            height: 60px;
            margin: auto 20px;
            border: 2px solid #00FFFF;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 28px;
            background: rgba(0, 255, 255, 0.1);
            transition: all 0.2s ease;
            box-shadow: 0 0 10px rgba(0, 255, 255, 0.3);
        }

        .file-icon:hover .icon-image {
            box-shadow: 0 0 25px rgba(0, 255, 255, 0.7);
        }

        .icon-name {
            font-size: 0.9rem;
            margin-top: 8px;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        /* ===== WINDOW SYSTEM ===== */
        #windows-container {
            position: absolute;
            width: 100%;
            height: 100%;
            z-index: 100;
            pointer-events: none;
        }

        .window {
            position: absolute;
            background: linear-gradient(135deg, #0a0d15 0%, #050709 100%);
            border: 2px solid #00FFFF;
            box-shadow: 0 0 30px rgba(0, 255, 255, 0.4);
            min-width: 350px;
            pointer-events: auto;
            z-index: 10;
            animation: windowSlide 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        @keyframes windowSlide {
            from {
                opacity: 0;
                transform: scale(0.85) translateY(-20px);
            }
            to {
                opacity: 1;
                transform: scale(1) translateY(0);
            }
        }

        .window-header {
            padding: 12px 16px;
            background: linear-gradient(90deg, rgba(0, 255, 255, 0.1) 0%, transparent 100%);
            border-bottom: 1px solid rgba(0, 255, 255, 0.3);
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: move;
            user-select: none;
            font-weight: 700;
            letter-spacing: 1px;
        }

        .window-title {
            flex: 1;
            text-transform: uppercase;
        }

        .window-close {
            cursor: pointer;
            font-size: 1.2rem;
            opacity: 0.7;
            transition: opacity 0.2s;
        }

        .window-close:hover {
            opacity: 1;
        }

        .window-content {
            padding: 16px;
        }

        /* ===== CONNECTION SCREEN ===== */
        #connection {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            position: relative;
            padding: 40px;
        }

        .connection-header {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            padding: 20px;
            border-bottom: 2px solid rgba(0, 255, 255, 0.3);
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 20;
        }

        .connection-status {
            font-size: 0.9rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: #00FFFF;
        }

        .map-container {
            width: 90vw;
            max-width: 1100px;
            height: 70vh;
            border: 2px solid #00FFFF;
            box-shadow: 0 0 40px rgba(0, 255, 255, 0.3), inset 0 0 30px rgba(0, 255, 255, 0.05);
            position: relative;
            background: linear-gradient(135deg, rgba(0, 20, 40, 0.9) 0%, rgba(0, 10, 20, 0.9) 100%);
            overflow: hidden;
        }

        .scan-overlay {
            position: absolute;
            width: 100%;
            height: 20px;
            background: linear-gradient(to bottom, rgba(0, 255, 255, 0.3), transparent);
            animation: scanMove 4s linear infinite;
            pointer-events: none;
            z-index: 5;
        }

        @keyframes scanMove {
            0% { top: -20px; }
            100% { top: 100%; }
        }

        #map-svg {
            width: 100%;
            height: 100%;
            position: relative;
            z-index: 3;
        }

        .target-focus-overlay {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 4;
        }

        .lock-ring {
            position: absolute;
            border: 2px solid #00FF00;
            border-radius: 50%;
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.8);
            pointer-events: none;
            z-index: 6;
            animation: rotateLock 3s linear infinite;
        }

        @keyframes rotateLock {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .coordinates {
            position: absolute;
            bottom: 10px;
            right: 10px;
            font-size: 0.7rem;
            color: #00FF00;
            text-transform: uppercase;
            letter-spacing: 1px;
            opacity: 0;
            animation: coordsFadeIn 0.8s ease 1s forwards;
        }

        @keyframes coordsFadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        .connection-info {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 15px 20px;
            border-top: 2px solid #00FFFF;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 20px;
            font-size: 0.85rem;
            z-index: 10;
            background: linear-gradient(to top, rgba(0, 10, 20, 0.95), transparent);
        }

        .info-item {
            display: flex;
            justify-content: space-between;
        }

        .info-label {
            opacity: 0.7;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .info-value {
            font-weight: 700;
            color: #00FF00;
            font-family: 'Courier New', monospace;
        }

        .route-info {
            position: absolute;
            bottom: 70px;
            left: 20px;
            background: rgba(0, 20, 40, 0.8);
            border: 1px solid rgba(0, 255, 255, 0.5);
            padding: 10px 15px;
            border-radius: 4px;
            font-size: 0.75rem;
            z-index: 10;
            max-width: 300px;
        }

        .route-hop {
            color: #00FFFF;
            margin: 2px 0;
            font-family: 'Courier New', monospace;
        }

        .route-hop.active {
            color: #FFFF00;
            text-shadow: 0 0 10px #FFFF00;
        }

        /* ===== AUTHENTICATION SCREEN ===== */
        #auth {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #1a0a1a 100%);
        }

        .auth-container {
            text-align: center;
            z-index: 10;
            max-width: 600px;
        }

        .auth-title {
            font-size: 2.5rem;
            font-weight: 900;
            margin-bottom: 40px;
            text-shadow: 0 0 20px #FF0080;
            letter-spacing: 2px;
        }

        .auth-status {
            font-size: 1.1rem;
            margin-bottom: 30px;
            height: 30px;
            font-weight: 700;
            letter-spacing: 1px;
            text-transform: uppercase;
            animation: statusPulse 0.8s infinite;
        }

        @keyframes statusPulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.6; }
        }

        .password-display {
            background: rgba(0, 255, 255, 0.05);
            border: 2px solid #00FFFF;
            padding: 20px;
            margin-bottom: 30px;
            font-size: 1.8rem;
            font-weight: 700;
            letter-spacing: 3px;
            font-family: 'Courier New', monospace;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
            min-height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .password-char {
            display: inline-block;
            width: 30px;
            text-align: center;
            transition: all 0.1s ease;
        }

        .password-char.locked {
            color: #00FF00;
            text-shadow: 0 0 10px #00FF00;
        }

        @keyframes charFlip {
            0% { transform: rotateY(0deg); }
            50% { transform: rotateY(90deg); }
            100% { transform: rotateY(0deg); }
        }

        .crack-progress {
            margin-top: 30px;
            text-align: left;
        }

        .progress-label {
            font-size: 0.85rem;
            margin-bottom: 8px;
            opacity: 0.8;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .progress-bar {
            height: 4px;
            background: rgba(0, 255, 255, 0.1);
            border: 1px solid rgba(0, 255, 255, 0.3);
            overflow: hidden;
            margin-bottom: 15px;
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #00FF00, #00FFFF);
            width: 0%;
            transition: width 0.3s ease;
            box-shadow: 0 0 10px #00FF00;
        }

        /* ===== DATA EXTRACTION SCREEN ===== */
        #extraction {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            padding: 20px;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            overflow: hidden;
        }

        .extraction-panel {
            background: linear-gradient(135deg, #0a0d15 0%, #050709 100%);
            border: 2px solid #00FFFF;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .panel-header {
            padding: 12px 16px;
            background: linear-gradient(90deg, rgba(0, 255, 255, 0.1) 0%, transparent 100%);
            border-bottom: 1px solid rgba(0, 255, 255, 0.3);
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
            font-size: 0.9rem;
        }

        .panel-content {
            flex: 1;
            overflow-y: auto;
            padding: 12px;
            font-size: 0.75rem;
            font-family: 'Courier New', monospace;
        }

        .log-line {
            margin: 4px 0;
            opacity: 0;
            animation: logFade 0.3s ease forwards;
            line-height: 1.4;
        }

        @keyframes logFade {
            from { opacity: 0; transform: translateY(-5px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .log-success { color: #00FF00; }
        .log-warning { color: #FFFF00; }
        .log-error { color: #FF6600; }
        .log-info { color: #00FFFF; }

        @media (max-width: 1200px) {
            #extraction {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>

<body>
    <!-- ===== ENTRY SCREEN ===== -->
    <div id="entry" class="screen active">
        <canvas id="bg-canvas"></canvas>
        <div class="entry-content">
            <div class="entry-title">NEXUS-9</div>
            <div class="button-group">
                <button class="btn" onclick="ScreenManager.switchScreen('desktop')">
                    DESKTOP INTERFACE
                    <span class="btn-subtitle">Full system control</span>
                </button>
            </div>
        </div>
    </div>

    <!-- ===== DESKTOP SCREEN ===== -->
    <div id="desktop" class="screen">
        <div class="desktop-header">
            <div>SYSTEM DESKTOP</div>
        </div>
        <div class="file-icon" onclick="DesktopManager.openFile('CONNECTION')">
            <div class="icon-image">🌐</div>
            <div class="icon-name">EU_NODE.exe</div>
        </div>
    </div>

    <!-- ===== CONNECTION SCREEN ===== -->
    <div id="connection" class="screen">
        <div class="connection-header">
            <div class="connection-status" id="connection-status">INITIALIZING...</div>
        </div>

        <div class="map-container">
            <div class="scan-overlay"></div>
            <svg id="map-svg" viewBox="0 0 1200 600"></svg>
            <div class="target-focus-overlay" id="target-focus-overlay"></div>
            <div class="route-info" id="route-info" style="display:none;"></div>
            <div class="coordinates" id="coordinates"></div>

            <div class="connection-info">
                <div class="info-item">
                    <span class="info-label">Source</span>
                    <span class="info-value" id="source-info">TOKYO, JP</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Current Hop</span>
                    <span class="info-value" id="current-hop">--</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Latency</span>
                    <span class="info-value" id="latency-info">--ms</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Packet Loss</span>
                    <span class="info-value" id="packet-loss">0%</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Bandwidth</span>
                    <span class="info-value" id="bandwidth-info">--Mbps</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Route Distance</span>
                    <span class="info-value" id="route-distance">--km</span>
                </div>
            </div>
        </div>
    </div>

    <!-- ===== AUTHENTICATION SCREEN ===== -->
    <div id="auth" class="screen">
        <div class="auth-container">
            <div class="auth-title">SECURITY ACCESS</div>
            <div class="auth-status" id="auth-status">INITIALIZING CRACK...</div>
            <div class="password-display" id="password-display"></div>
            <div class="crack-progress">
                <div class="progress-label">DECRYPTION PROGRESS</div>
                <div class="progress-bar">
                    <div class="progress-fill" id="crack-progress"></div>
                </div>
            </div>
            <div style="margin-top: 20px;">
                <button class="btn" style="padding: 8px 16px; font-size: 0.9rem;" onclick="ScreenManager.switchScreen('desktop')">← BACK</button>
            </div>
        </div>
    </div>

    <!-- ===== DATA EXTRACTION SCREEN ===== -->
    <div id="extraction" class="screen">
        <div class="extraction-panel">
            <div class="panel-header">REMOTE FILESYSTEM</div>
            <div class="panel-content" id="log-panel"></div>
        </div>
        <div class="extraction-panel">
            <div class="panel-header">TRANSFER STATUS</div>
            <div class="panel-content" id="transfer-panel"></div>
        </div>
    </div>

    <div id="windows-container"></div>

    <script>
        /* ===== REAL-WORLD NETWORK DATA ===== */
        const NetworkDatabase = {
            nodes: {
                'TOKYO': { x: 950, y: 250, lat: 35.6762, lon: 139.6503, name: 'TOKYO, JP', role: 'SOURCE' },
                'BEIJING': { x: 850, y: 220, lat: 39.9042, lon: 116.4074, name: 'BEIJING, CN', role: 'RELAY' },
                'MOSCOW': { x: 550, y: 180, lat: 55.7558, lon: 37.6173, name: 'MOSCOW, RU', role: 'RELAY' },
                'BERLIN': { x: 450, y: 150, lat: 52.5200, lon: 13.4050, name: 'BERLIN, DE', role: 'TARGET' },
                'ISTANBUL': { x: 600, y: 220, lat: 41.0082, lon: 28.9784, name: 'ISTANBUL, TR', role: 'RELAY' },
                'DUBAI': { x: 700, y: 300, lat: 25.2048, lon: 55.2708, name: 'DUBAI, AE', role: 'RELAY' },
                'SINGAPORE': { x: 900, y: 380, lat: 1.3521, lon: 103.8198, name: 'SINGAPORE, SG', role: 'RELAY' },
                'HONG_KONG': { x: 900, y: 300, lat: 22.3193, lon: 114.1694, name: 'HONG KONG, HK', role: 'RELAY' }
            },

            generateRoute() {
                // Generate random route from source to target
                const relays = ['BEIJING', 'ISTANBUL', 'MOSCOW', 'DUBAI', 'SINGAPORE', 'HONG_KONG'];
                const shuffled = relays.sort(() => Math.random() - 0.5);
                const selectedRelays = shuffled.slice(0, Math.floor(Math.random() * 2) + 2);
                
                return ['TOKYO', ...selectedRelays, 'BERLIN'];
            },

            calculateDistance(node1, node2) {
                // Haversine formula for approximate distance
                const R = 6371; // Earth radius in km
                const lat1 = node1.lat * Math.PI / 180;
                const lat2 = node2.lat * Math.PI / 180;
                const dLat = (node2.lat - node1.lat) * Math.PI / 180;
                const dLon = (node2.lon - node1.lon) * Math.PI / 180;
                
                const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                          Math.cos(lat1) * Math.cos(lat2) * Math.sin(dLon/2) * Math.sin(dLon/2);
                const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
                
                return Math.round(R * c);
            }
        };

        /* ===== SCREEN MANAGER ===== */
        const ScreenManager = {
            current: 'entry',
            
            switchScreen(target) {
                const current = document.getElementById(this.current);
                const next = document.getElementById(target);
                
                current.classList.remove('active');
                
                setTimeout(() => {
                    next.classList.add('active');
                    this.current = target;
                    
                    if (target === 'connection') {
                        CinematicFlow.start();
                    } else if (target === 'auth') {
                        AuthSystem.init();
                    } else if (target === 'extraction') {
                        DataExtraction.init();
                    }
                }, 300);
            }
        };

        /* ===== DESKTOP MANAGER ===== */
        const DesktopManager = {
            openFile(type) {
                const info = {
                    'CONNECTION': { title: 'EU_NODE.exe', content: 'Accessing remote node...' }
                };

                const config = info[type];
                if (config) {
                    this.createWindow(config.title, config.content);
                    setTimeout(() => ScreenManager.switchScreen('connection'), 1500);
                }
            },

            createWindow(title, content) {
                const container = document.getElementById('windows-container');
                const win = document.createElement('div');
                win.className = 'window';
                win.style.left = '50px';
                win.style.top = '50px';

                win.innerHTML = `
                    <div class="window-header">
                        <div class="window-title">${title}</div>
                        <div class="window-close">×</div>
                    </div>
                    <div class="window-content">${content}</div>
                `;

                this.makeWindowDraggable(win);
                container.appendChild(win);

                win.querySelector('.window-close').addEventListener('click', () => {
                    win.remove();
                });
            },

            makeWindowDraggable(win) {
                let offsetX = 0;
                let offsetY = 0;
                const header = win.querySelector('.window-header');

                header.addEventListener('mousedown', (e) => {
                    offsetX = e.clientX - win.offsetLeft;
                    offsetY = e.clientY - win.offsetTop;

                    const onMouseMove = (e) => {
                        win.style.left = (e.clientX - offsetX) + 'px';
                        win.style.top = (e.clientY - offsetY) + 'px';
                    };

                    const onMouseUp = () => {
                        document.removeEventListener('mousemove', onMouseMove);
                        document.removeEventListener('mouseup', onMouseUp);
                    };

                    document.addEventListener('mousemove', onMouseMove);
                    document.addEventListener('mouseup', onMouseUp);
                });
            }
        };

        /* ===== NETWORK BACKGROUND ===== */
        const NetworkBackground = {
            canvas: null,
            ctx: null,
            particles: [],

            init() {
                this.canvas = document.getElementById('bg-canvas');
                this.ctx = this.canvas.getContext('2d');
                this.canvas.width = window.innerWidth;
                this.canvas.height = window.innerHeight;

                for (let i = 0; i < 60; i++) {
                    this.particles.push({
                        x: Math.random() * this.canvas.width,
                        y: Math.random() * this.canvas.height,
                        vx: (Math.random() - 0.5) * 0.3,
                        vy: (Math.random() - 0.5) * 0.3,
                        radius: Math.random() * 2 + 1
                    });
                }

                this.animate();
                window.addEventListener('resize', () => this.onResize());
            },

            onResize() {
                this.canvas.width = window.innerWidth;
                this.canvas.height = window.innerHeight;
            },

            animate() {
                this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

                this.particles.forEach(p => {
                    p.x += p.vx;
                    p.y += p.vy;

                    if (p.x < 0 || p.x > this.canvas.width) p.vx *= -1;
                    if (p.y < 0 || p.y > this.canvas.height) p.vy *= -1;

                    this.ctx.beginPath();
                    this.ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                    this.ctx.fillStyle = 'rgba(0, 255, 255, 0.6)';
                    this.ctx.fill();
                });

                for (let i = 0; i < this.particles.length; i++) {
                    for (let j = i + 1; j < this.particles.length; j++) {
                        const dx = this.particles[i].x - this.particles[j].x;
                        const dy = this.particles[i].y - this.particles[j].y;
                        const dist = Math.sqrt(dx * dx + dy * dy);

                        if (dist < 150) {
                            this.ctx.beginPath();
                            this.ctx.moveTo(this.particles[i].x, this.particles[i].y);
                            this.ctx.lineTo(this.particles[j].x, this.particles[j].y);
                            this.ctx.strokeStyle = `rgba(0, 255, 255, ${0.2 * (1 - dist / 150)})`;
                            this.ctx.lineWidth = 0.5;
                            this.ctx.stroke();
                        }
                    }
                }

                requestAnimationFrame(() => this.animate());
            }
        };

        /* ===== CINEMATIC FLOW (REALWORLD VERSION) ===== */
        const CinematicFlow = {
            route: [],
            currentHopIndex: 0,
            totalDistance: 0,
            baseLatency: 0,
            packetLoss: 0,

            async start() {
                this.route = NetworkDatabase.generateRoute();
                this.calculateMetrics();
                
                await this.phase1();
                await this.phase2();
                await this.phase3();
                await this.phase4();
                await this.phase5();
                await this.phase6();
            },

            calculateMetrics() {
                let distance = 0;
                for (let i = 0; i < this.route.length - 1; i++) {
                    const from = NetworkDatabase.nodes[this.route[i]];
                    const to = NetworkDatabase.nodes[this.route[i + 1]];
                    distance += NetworkDatabase.calculateDistance(from, to);
                }
                this.totalDistance = distance;
                this.baseLatency = Math.round(distance / 130); // ~130km per ms
                this.packetLoss = Math.floor(Math.random() * 3);
            },

            wait(ms) {
                return new Promise(res => setTimeout(res, ms));
            },

            async phase1() {
                const status = document.getElementById('connection-status');
                status.textContent = 'INITIALIZING...';
                await this.wait(2000);
            },

            async phase2() {
                const status = document.getElementById('connection-status');
                status.textContent = 'SCANNING GLOBAL NETWORK...';
                this.drawWorldMap();
                this.displayRoute();
                await this.wait(3000);
            },

            async phase3() {
                const status = document.getElementById('connection-status');
                status.textContent = 'ROUTE ESTABLISHED';
                document.getElementById('route-info').style.display = 'block';
                await this.wait(2000);
            },

            async phase4() {
                const status = document.getElementById('connection-status');
                status.textContent = 'TRAVERSING PATH...';
                await this.animateRouteTraversal();
            },

            async phase5() {
                const status = document.getElementById('connection-status');
                status.textContent = 'LOCK ACQUIRED';
                
                const targetNode = NetworkDatabase.nodes['BERLIN'];
                const container = document.querySelector('.map-container');
                const scale = container.clientWidth / 1200;
                
                const lockX = targetNode.x * scale;
                const lockY = targetNode.y * scale;

                const lock = document.createElement('div');
                lock.className = 'lock-ring';
                lock.style.width = '80px';
                lock.style.height = '80px';
                lock.style.left = (lockX - 40) + 'px';
                lock.style.top = (lockY - 40) + 'px';
                container.appendChild(lock);

                document.getElementById('coordinates').textContent = 
                    `${targetNode.lat.toFixed(4)}°N ${targetNode.lon.toFixed(4)}°E`;

                await this.wait(3000);
            },

            async phase6() {
                const status = document.getElementById('connection-status');
                status.textContent = 'ACCESS GRANTED';
                status.style.color = '#00FF00';
                status.style.textShadow = '0 0 20px #00FF00';
                await this.wait(1500);
                ScreenManager.switchScreen('auth');
            },

            drawWorldMap() {
                const svg = document.getElementById('map-svg');
                svg.innerHTML = '';

                // Draw continents as outlines
                const continents = [
                    { label: 'NORTH AMERICA', x: 150, y: 150 },
                    { label: 'EUROPE', x: 450, y: 100 },
                    { label: 'ASIA', x: 800, y: 200 },
                    { label: 'AUSTRALIA', x: 600, y: 400 }
                ];

                continents.forEach(cont => {
                    const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                    circle.setAttribute('cx', cont.x);
                    circle.setAttribute('cy', cont.y);
                    circle.setAttribute('r', '70');
                    circle.setAttribute('fill', 'none');
                    circle.setAttribute('stroke', 'rgba(0, 255, 100, 0.2)');
                    circle.setAttribute('stroke-width', '1.5');
                    circle.setAttribute('stroke-dasharray', '5,5');
                    svg.appendChild(circle);

                    const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
                    text.setAttribute('x', cont.x);
                    text.setAttribute('y', cont.y + 85);
                    text.setAttribute('text-anchor', 'middle');
                    text.setAttribute('fill', 'rgba(0, 255, 100, 0.3)');
                    text.setAttribute('font-size', '10');
                    text.textContent = cont.label;
                    svg.appendChild(text);
                });

                // Draw all nodes
                Object.keys(NetworkDatabase.nodes).forEach(key => {
                    const node = NetworkDatabase.nodes[key];
                    this.drawNode(svg, node, key);
                });
            },

            drawNode(svg, node, key) {
                const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                circle.setAttribute('cx', node.x);
                circle.setAttribute('cy', node.y);
                circle.setAttribute('r', '5');
                circle.setAttribute('fill', node.role === 'TARGET' ? '#FF0000' : node.role === 'SOURCE' ? '#00FF00' : 'rgba(0, 255, 255, 0.4)');
                circle.setAttribute('opacity', '0.6');
                circle.style.filter = `drop-shadow(0 0 5px ${node.role === 'TARGET' ? '#FF0000' : node.role === 'SOURCE' ? '#00FF00' : '#00FFFF'})`;
                svg.appendChild(circle);

                const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
                text.setAttribute('x', node.x);
                text.setAttribute('y', node.y - 15);
                text.setAttribute('text-anchor', 'middle');
                text.setAttribute('fill', '#00FFFF');
                text.setAttribute('font-size', '10');
                text.setAttribute('font-weight', 'bold');
                text.textContent = key;
                svg.appendChild(text);
            },

            displayRoute() {
                const svg = document.getElementById('map-svg');
                const routeInfo = document.getElementById('route-info');
                
                let routeHTML = '<strong>ROUTE PATH:</strong><br>';
                let totalDist = 0;

                for (let i = 0; i < this.route.length; i++) {
                    const current = this.route[i];
                    routeHTML += `<div class="route-hop" id="hop-${i}">[${i}] ${current}</div>`;

                    if (i < this.route.length - 1) {
                        const next = this.route[i + 1];
                        const fromNode = NetworkDatabase.nodes[current];
                        const toNode = NetworkDatabase.nodes[next];
                        
                        const dist = NetworkDatabase.calculateDistance(fromNode, toNode);
                        totalDist += dist;

                        // Draw path line
                        const path = document.createElementNS('http://www.w3.org/2000/svg', 'line');
                        path.setAttribute('x1', fromNode.x);
                        path.setAttribute('y1', fromNode.y);
                        path.setAttribute('x2', toNode.x);
                        path.setAttribute('y2', toNode.y);
                        path.setAttribute('stroke', 'rgba(0, 255, 255, 0.3)');
                        path.setAttribute('stroke-width', '1.5');
                        path.setAttribute('stroke-dasharray', '5,5');
                        path.style.animation = 'pathDash 20s linear infinite';
                        svg.appendChild(path);
                    }
                }

                routeInfo.innerHTML = routeHTML + `<div style="margin-top:10px;color:#00FF00;"><strong>TOTAL: ${totalDist}km</strong></div>`;

                const style = document.createElementNS('http://www.w3.org/2000/svg', 'style');
                style.textContent = `
                    @keyframes pathDash { to { stroke-dashoffset: -10; } }
                `;
                svg.appendChild(style);
            },

            async animateRouteTraversal() {
                const svg = document.getElementById('map-svg');
                
                for (let i = 0; i < this.route.length - 1; i++) {
                    const from = NetworkDatabase.nodes[this.route[i]];
                    const to = NetworkDatabase.nodes[this.route[i + 1]];
                    
                    document.getElementById('current-hop').textContent = this.route[i];
                    document.getElementById(`hop-${i}`).classList.add('active');

                    await this.animatePacketFlow(from, to, svg);
                    await this.wait(800);
                }
            },

            animatePacketFlow(from, to, svg) {
                return new Promise(resolve => {
                    let progress = 0;
                    const packets = 3;

                    const animate = () => {
                        progress += 0.02;

                        for (let p = 0; p < packets; p++) {
                            const delay = p * 0.15;
                            const effectiveProgress = Math.max(0, progress - delay);

                            if (effectiveProgress > 0 && effectiveProgress <= 1) {
                                const x = from.x + (to.x - from.x) * effectiveProgress;
                                const y = from.y + (to.y - from.y) * effectiveProgress;

                                // Remove old packet
                                const old = document.getElementById(`packet-${p}`);
                                if (old) old.remove();

                                const packet = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                                packet.id = `packet-${p}`;
                                packet.setAttribute('cx', x);
                                packet.setAttribute('cy', y);
                                packet.setAttribute('r', '3');
                                packet.setAttribute('fill', '#FFFF00');
                                packet.style.filter = `drop-shadow(0 0 8px #FFFF00)`;
                                packet.style.opacity = (1 - effectiveProgress * 0.3).toString();
                                svg.appendChild(packet);
                            }
                        }

                        if (progress <= 1) {
                            requestAnimationFrame(animate);
                        } else {
                            document.querySelectorAll('[id^="packet-"]').forEach(p => p.remove());
                            
                            // Update latency and loss
                            const dist = NetworkDatabase.calculateDistance(from, to);
                            const hopLatency = Math.round(dist / 130);
                            document.getElementById('latency-info').textContent = 
                                (this.baseLatency + Math.random() * 50).toFixed(0) + 'ms';
                            
                            if (Math.random() < 0.2) {
                                this.packetLoss += Math.floor(Math.random() * 2) + 1;
                                document.getElementById('packet-loss').textContent = this.packetLoss + '%';
                            }

                            resolve();
                        }
                    };

                    animate();
                });
            }
        };

        /* ===== AUTHENTICATION SYSTEM ===== */
        const AuthSystem = {
            correctPassword: '',
            crackProgress: 0,

            init() {
                const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
                this.correctPassword = '';
                for (let i = 0; i < 12; i++) {
                    this.correctPassword += chars.charAt(Math.floor(Math.random() * chars.length));
                }

                this.crackProgress = 0;
                this.startCracking();
            },

            startCracking() {
                const statusEl = document.getElementById('auth-status');
                const displayEl = document.getElementById('password-display');
                const progressEl = document.getElementById('crack-progress');

                statusEl.textContent = 'BRUTE FORCE ATTEMPT...';

                const crackInterval = setInterval(() => {
                    this.crackProgress += Math.random() * 12;
                    if (this.crackProgress >= 100) this.crackProgress = 100;

                    progressEl.style.width = this.crackProgress + '%';

                    displayEl.innerHTML = '';
                    for (let i = 0; i < 12; i++) {
                        const span = document.createElement('span');
                        span.className = 'password-char';

                        const progress = Math.min(this.crackProgress / 100 * 1.2, 1);
                        if (i < progress * 12) {
                            span.textContent = this.correctPassword[i];
                            span.classList.add('locked');
                        } else {
                            span.textContent = String.fromCharCode(65 + Math.floor(Math.random() * 26));
                        }
                        displayEl.appendChild(span);
                    }

                    if (this.crackProgress >= 100) {
                        clearInterval(crackInterval);
                        statusEl.textContent = 'KEY MATCH FOUND';
                        
                        displayEl.innerHTML = '';
                        for (let i = 0; i < 12; i++) {
                            const span = document.createElement('span');
                            span.className = 'password-char locked';
                            span.textContent = this.correctPassword[i];
                            displayEl.appendChild(span);
                        }

                        setTimeout(() => {
                            statusEl.textContent = 'DECRYPTION COMPLETE';
                            statusEl.style.color = '#00FF00';
                            setTimeout(() => {
                                ScreenManager.switchScreen('extraction');
                            }, 1500);
                        }, 1000);
                    }
                }, 80);
            }
        };

        /* ===== DATA EXTRACTION ===== */
        const DataExtraction = {
            init() {
                this.startLogging();
                this.startTransfers();
            },

            startLogging() {
                const logs = [
                    { type: 'info', text: '[INIT] Mounting remote filesystem...' },
                    { type: 'success', text: '[✓] Root directory accessed' },
                    { type: 'warning', text: '[!] Encrypted partitions detected' },
                    { type: 'info', text: '[AUTH] Bypassing encryption...' },
                    { type: 'success', text: '[✓] Keys obtained' },
                    { type: 'info', text: '[EXTRACT] Exfiltrating data...' },
                    { type: 'success', text: '[✓] Transfer complete' }
                ];

                logs.forEach((log, idx) => {
                    setTimeout(() => {
                        const panel = document.getElementById('log-panel');
                        const line = document.createElement('div');
                        line.className = `log-line log-${log.type}`;
                        line.textContent = log.text;
                        panel.appendChild(line);
                        panel.scrollTop = panel.scrollHeight;
                    }, idx * 600);
                });
            },

            startTransfers() {
                const files = [
                    { name: 'DATABASE_BACKUP.sql', size: 2.4, duration: 8 },
                    { name: 'USER_RECORDS.zip', size: 1.8, duration: 6 }
                ];

                const container = document.getElementById('transfer-panel');

                files.forEach((file, idx) => {
                    setTimeout(() => {
                        const div = document.createElement('div');
                        div.innerHTML = `
                            <div style="font-size:0.8rem;margin:10px 0 5px 0;color:#FFFF00;">${file.name}</div>
                            <div style="height:6px;background:rgba(0,255,255,0.1);border:1px solid rgba(0,255,255,0.3);overflow:hidden;">
                                <div style="height:100%;background:linear-gradient(90deg,#00FF00,#00FFFF);width:0%;transition:width 0.2s;" class="transfer-progress"></div>
                            </div>
                            <div style="font-size:0.7rem;color:#00FF00;margin-top:3px;" class="transfer-speed">0%</div>
                        `;
                        container.appendChild(div);

                        let progress = 0;
                        const interval = setInterval(() => {
                            progress += 100 / (file.duration * 10);
                            if (progress >= 100) progress = 100;

                            div.querySelector('.transfer-progress').style.width = progress + '%';
                            div.querySelector('.transfer-speed').textContent = 
                                (file.size * progress / 100 / (file.duration * 0.1)).toFixed(1) + ' MB/s | ' + progress.toFixed(0) + '%';

                            if (progress >= 100) clearInterval(interval);
                        }, 100);
                    }, idx * 1200);
                });
            }
        };

        /* ===== INITIALIZATION ===== */
        NetworkBackground.init();

        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') ScreenManager.switchScreen('entry');
        });

        window.addEventListener('resize', () => {
            if (NetworkBackground.canvas) NetworkBackground.onResize();
        });
    </script>
</body>
</html>
