<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NEXUS-9 SYSTEM</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@700;900&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        html, body {
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        body {
            background: #05070A;
            color: #00FFFF;
            font-family: 'Orbitron', monospace;
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
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .screen.active {
            opacity: 1;
            visibility: visible;
            pointer-events: auto;
        }

        /* ===== ENTRY ===== */
        #entry {
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
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .btn:hover {
            transform: translateY(-3px) scale(1.03);
            box-shadow: 0 0 40px rgba(0, 255, 255, 0.6);
        }

        /* ===== PLANNING ===== */
        #planning {
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
        }

        .planning-header {
            flex-shrink: 0;
            text-align: center;
            padding: 30px 20px;
            border-bottom: 2px solid rgba(0, 255, 255, 0.3);
            background: linear-gradient(to bottom, rgba(0, 255, 255, 0.05), transparent);
        }

        .planning-title {
            font-size: 2rem;
            font-weight: 900;
            text-shadow: 0 0 20px #00FFFF;
            margin-bottom: 10px;
        }

        .planning-subtitle {
            font-size: 0.9rem;
            opacity: 0.7;
        }

        /* Main scrollable area - CENTER ALIGNED */
        .planning-wrapper {
            flex: 1;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            overflow-y: auto;
            overflow-x: hidden;
            padding: 20px;
        }

        .planning-wrapper::-webkit-scrollbar {
            width: 12px;
        }

        .planning-wrapper::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
            border-radius: 6px;
        }

        .planning-content {
            width: 100%;
            max-width: 1200px;
            display: grid;
            grid-template-columns: 1fr 1.2fr;
            gap: 20px;
        }

        .node-selector {
            border: 2px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.05);
            border-radius: 4px;
            padding: 20px;
            overflow-y: auto;
            max-height: 500px;
        }

        .node-selector::-webkit-scrollbar {
            width: 8px;
        }

        .node-selector::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
            border-radius: 4px;
        }

        .selector-label {
            font-size: 0.9rem;
            font-weight: 700;
            margin-bottom: 15px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .node-item {
            padding: 12px;
            border: 1px solid rgba(0, 255, 255, 0.3);
            background: rgba(0, 255, 255, 0.02);
            margin-bottom: 10px;
            cursor: pointer;
            transition: all 0.2s;
            border-radius: 3px;
        }

        .node-item:hover {
            background: rgba(0, 255, 255, 0.1);
            border-color: #00FFFF;
        }

        .node-item.selected {
            background: rgba(0, 255, 0, 0.15);
            border-color: #00FF00;
            box-shadow: 0 0 10px rgba(0, 255, 0, 0.4);
        }

        .node-name {
            font-weight: 700;
            margin-bottom: 5px;
            font-size: 0.95rem;
        }

        .node-stats {
            font-size: 0.75rem;
            opacity: 0.7;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 5px;
        }

        .stat-row {
            display: flex;
            justify-content: space-between;
        }

        .stat-value {
            font-weight: 700;
            color: #00FF00;
        }

        .stat-value.danger {
            color: #FF6400;
        }

        .route-builder {
            border: 2px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.05);
            border-radius: 4px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            max-height: 500px;
        }

        .builder-label {
            font-size: 0.9rem;
            font-weight: 700;
            margin-bottom: 15px;
            text-transform: uppercase;
        }

        .route-path {
            display: flex;
            align-items: center;
            gap: 8px;
            margin-bottom: 20px;
            min-height: 60px;
            background: rgba(0, 0, 0, 0.3);
            padding: 15px;
            border-radius: 3px;
            border: 1px solid rgba(0, 255, 255, 0.2);
            overflow-x: auto;
            font-size: 0.85rem;
            flex-wrap: wrap;
        }

        .route-path::-webkit-scrollbar {
            height: 6px;
        }

        .route-path::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
        }

        .route-node {
            padding: 8px 12px;
            background: rgba(0, 255, 255, 0.15);
            border: 1px solid rgba(0, 255, 255, 0.4);
            border-radius: 3px;
            white-space: nowrap;
            font-weight: 700;
            flex-shrink: 0;
            font-size: 0.9rem;
        }

        .route-arrow {
            opacity: 0.5;
            flex-shrink: 0;
        }

        .route-stats {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-top: auto;
            font-size: 0.75rem;
            flex-shrink: 0;
        }

        .route-stat {
            border: 1px solid rgba(0, 255, 255, 0.3);
            padding: 10px;
            background: rgba(0, 255, 255, 0.05);
            border-radius: 3px;
            text-align: center;
        }

        .route-stat-label {
            opacity: 0.7;
            font-size: 0.65rem;
            text-transform: uppercase;
        }

        .route-stat-value {
            font-weight: 900;
            font-size: 1.1rem;
            color: #00FF00;
            margin-top: 5px;
        }

        .route-stat-value.danger {
            color: #FF0000;
        }

        .planning-actions {
            flex-shrink: 0;
            display: flex;
            gap: 15px;
            justify-content: center;
            padding: 20px;
            border-top: 2px solid rgba(0, 255, 255, 0.3);
        }

        .action-btn {
            padding: 12px 30px;
            border: 2px solid #00FFFF;
            background: rgba(0, 255, 255, 0.08);
            color: #00FFFF;
            font-family: 'Orbitron', monospace;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 1px;
            font-size: 0.95rem;
        }

        .action-btn:hover:not(:disabled) {
            background: rgba(0, 255, 255, 0.15);
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.5);
        }

        .action-btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        /* ===== HACK SCREEN ===== */
        #hack {
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .hack-container {
            width: 100%;
            max-width: 900px;
            display: flex;
            flex-direction: column;
            gap: 30px;
        }

        .hack-header {
            text-align: center;
        }

        .hack-title {
            font-size: 2rem;
            font-weight: 900;
            text-shadow: 0 0 20px #00FFFF;
            margin-bottom: 15px;
            letter-spacing: 2px;
        }

        .hack-status {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .status-box {
            border: 2px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.05);
            padding: 20px;
            border-radius: 4px;
            text-align: center;
        }

        .status-label {
            font-size: 0.85rem;
            text-transform: uppercase;
            opacity: 0.7;
            margin-bottom: 10px;
            letter-spacing: 1px;
        }

        .status-value {
            font-size: 2rem;
            font-weight: 900;
            color: #00FF00;
            font-family: 'Courier New', monospace;
        }

        .status-value.warning {
            color: #FFFF00;
            text-shadow: 0 0 10px #FFFF00;
        }

        .status-value.danger {
            color: #FF0000;
            text-shadow: 0 0 10px #FF0000;
        }

        .phase-box {
            border: 2px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.05);
            padding: 25px;
            border-radius: 4px;
        }

        .phase-title {
            font-size: 1.2rem;
            font-weight: 900;
            margin-bottom: 15px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .phase-status {
            font-size: 0.95rem;
            margin-bottom: 15px;
            opacity: 0.9;
        }

        .progress-bar {
            height: 8px;
            background: rgba(0, 255, 255, 0.1);
            border: 1px solid rgba(0, 255, 255, 0.3);
            border-radius: 4px;
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

        .phase-display {
            min-height: 80px;
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(0, 255, 255, 0.2);
            padding: 15px;
            border-radius: 3px;
            font-family: 'Courier New', monospace;
            font-size: 0.9rem;
            color: #00FF00;
            overflow-y: auto;
            max-height: 150px;
        }

        .phase-display::-webkit-scrollbar {
            width: 6px;
        }

        .phase-display::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
        }

        /* ===== RESULT ===== */
        #result {
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #0a1a0a 100%);
            padding: 20px;
            overflow-y: auto;
        }

        .result-container {
            max-width: 600px;
            text-align: center;
        }

        .result-title {
            font-size: 2.5rem;
            font-weight: 900;
            margin-bottom: 20px;
            letter-spacing: 3px;
        }

        .result-title.success {
            color: #00FF00;
            text-shadow: 0 0 30px #00FF00;
        }

        .result-title.failure {
            color: #FF0000;
            text-shadow: 0 0 30px #FF0000;
        }

        .result-reason {
            font-size: 1rem;
            margin-bottom: 30px;
            line-height: 1.6;
        }

        .result-analysis {
            text-align: left;
            background: rgba(0, 255, 255, 0.05);
            border: 1px solid rgba(0, 255, 255, 0.3);
            padding: 20px;
            border-radius: 4px;
            margin-bottom: 30px;
            font-size: 0.85rem;
        }

        .analysis-item {
            margin-bottom: 15px;
            padding-bottom: 15px;
            border-bottom: 1px solid rgba(0, 255, 255, 0.2);
        }

        .analysis-item:last-child {
            border-bottom: none;
        }

        .analysis-label {
            font-weight: 700;
            color: #00FFFF;
            text-transform: uppercase;
            font-size: 0.75rem;
            margin-bottom: 5px;
        }

        .analysis-value {
            opacity: 0.8;
            line-height: 1.4;
            font-family: 'Courier New', monospace;
            font-size: 0.85rem;
        }

        .result-actions {
            display: flex;
            gap: 15px;
            justify-content: center;
        }

        /* Responsive */
        @media (max-width: 1024px) {
            .planning-content {
                grid-template-columns: 1fr;
            }

            .hack-status {
                grid-template-columns: repeat(3, 1fr);
            }

            .planning-title {
                font-size: 1.5rem;
            }

            .hack-title {
                font-size: 1.5rem;
            }
        }

        @media (max-width: 768px) {
            .planning-header {
                padding: 20px 15px;
            }

            .planning-title {
                font-size: 1.3rem;
                margin-bottom: 5px;
            }

            .planning-subtitle {
                font-size: 0.8rem;
            }

            .entry-title {
                font-size: 2.5rem;
            }

            .hack-status {
                grid-template-columns: 1fr;
                gap: 15px;
            }

            .node-selector {
                max-height: 300px;
            }

            .route-builder {
                max-height: 300px;
            }

            .route-stats {
                grid-template-columns: 1fr;
            }

            .status-value {
                font-size: 1.5rem;
            }
        }
    </style>
</head>

<body>
    <!-- ===== ENTRY ===== -->
    <div id="entry" class="screen active">
        <canvas id="bg-canvas"></canvas>
        <div class="entry-content">
            <div class="entry-title">NEXUS-9</div>
            <button class="btn" onclick="ScreenManager.switchScreen('planning')">
                INITIATE BREACH
            </button>
        </div>
    </div>

    <!-- ===== PLANNING ===== -->
    <div id="planning" class="screen">
        <div class="planning-header">
            <div class="planning-title">ROUTE PLANNING</div>
            <div class="planning-subtitle">Select nodes and build your attack route</div>
        </div>

        <div class="planning-wrapper">
            <div class="planning-content">
                <div class="node-selector" id="node-selector">
                    <div class="selector-label">Available Nodes</div>
                </div>

                <div class="route-builder">
                    <div class="builder-label">Your Route</div>
                    <div class="route-path" id="route-path">
                        <span class="route-node">TOKYO</span>
                        <span class="route-arrow">→</span>
                        <span class="route-node">BERLIN</span>
                    </div>

                    <div class="route-stats">
                        <div class="route-stat">
                            <div class="route-stat-label">Total Risk</div>
                            <div class="route-stat-value" id="route-risk">SAFE</div>
                        </div>
                        <div class="route-stat">
                            <div class="route-stat-label">Distance</div>
                            <div class="route-stat-value" id="route-distance">0km</div>
                        </div>
                        <div class="route-stat">
                            <div class="route-stat-label">Latency</div>
                            <div class="route-stat-value" id="route-latency">--ms</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="planning-actions">
            <button class="action-btn" onclick="ScreenManager.switchScreen('entry')">← CANCEL</button>
            <button class="action-btn" id="execute-btn" disabled onclick="HackGame.startHack()">EXECUTE →</button>
        </div>
    </div>

    <!-- ===== HACK SCREEN ===== -->
    <div id="hack" class="screen">
        <div class="hack-container">
            <div class="hack-header">
                <div class="hack-title" id="hack-title">NETWORK INFILTRATION</div>
            </div>

            <div class="hack-status">
                <div class="status-box">
                    <div class="status-label">Time Left</div>
                    <div class="status-value" id="time-left">180s</div>
                </div>
                <div class="status-box">
                    <div class="status-label">Phase</div>
                    <div class="status-value" id="phase-indicator" style="font-size: 1.5rem;">CONNECTING</div>
                </div>
                <div class="status-box">
                    <div class="status-label">Detection</div>
                    <div class="status-value" id="detection-level">0%</div>
                </div>
            </div>

            <div class="phase-box">
                <div class="phase-title" id="phase-title">Phase 1: Connecting</div>
                <div class="phase-status" id="phase-status">Establishing connection...</div>
                <div class="progress-bar">
                    <div class="progress-fill" id="phase-progress"></div>
                </div>
                <div class="phase-display" id="phase-display"></div>
            </div>
        </div>
    </div>

    <!-- ===== RESULT ===== -->
    <div id="result" class="screen">
        <div class="result-container">
            <div class="result-title" id="result-title">OPERATION COMPLETE</div>
            <div class="result-reason" id="result-reason"></div>
            <div class="result-analysis" id="result-analysis"></div>
            <div class="result-actions">
                <button class="btn" onclick="ScreenManager.switchScreen('entry')">← RESTART</button>
            </div>
        </div>
    </div>

    <script>
        /* ===== NODE DATABASE ===== */
        const NodeDatabase = {
            nodes: {
                'TOKYO': { risk: 15, latency: 0 },
                'BEIJING': { risk: 35, latency: 85 },
                'MOSCOW': { risk: 70, latency: 180 },
                'ISTANBUL': { risk: 40, latency: 120 },
                'DUBAI': { risk: 25, latency: 140 },
                'SINGAPORE': { risk: 45, latency: 220 },
                'FRANKFURT': { risk: 60, latency: 200 },
                'AMSTERDAM': { risk: 35, latency: 190 },
                'LONDON': { risk: 38, latency: 210 },
                'PARIS': { risk: 42, latency: 205 },
                'BERLIN': { risk: 0, latency: 0, isTarget: true }
            }
        };

        /* ===== GAME STATE ===== */
        const GameState = {
            selectedRoute: ['TOKYO'],
            routeDetails: { totalDistance: 0, totalLatency: 0, totalRisk: 0 },

            addNodeToRoute(nodeName) {
                if (this.selectedRoute.includes(nodeName)) {
                    this.selectedRoute = this.selectedRoute.filter(n => n !== nodeName);
                } else if (nodeName !== 'BERLIN' && nodeName !== 'TOKYO') {
                    this.selectedRoute.splice(this.selectedRoute.length - 1, 0, nodeName);
                }
                this.recalculateRoute();
            },

            recalculateRoute() {
                let totalRisk = 0;
                let totalLatency = 0;

                for (let i = 1; i < this.selectedRoute.length; i++) {
                    const node = NodeDatabase.nodes[this.selectedRoute[i]];
                    totalRisk += node.risk;
                    totalLatency += node.latency;
                }

                this.routeDetails = { totalRisk, totalLatency };
                this.validateRoute();
            },

            validateRoute() {
                const route = this.selectedRoute;
                if (route[route.length - 1] !== 'BERLIN') {
                    document.getElementById('execute-btn').disabled = true;
                    return;
                }
                document.getElementById('execute-btn').disabled = false;
            }
        };

        /* ===== HACK STATE ===== */
        const HackState = {
            timeLimit: 180,
            timeLeft: 180,
            timer: null,
            phase: 'connecting',
            detection: 0
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
                    
                    if (target === 'planning') {
                        PlanningSystem.init();
                    }
                }, 300);
            }
        };

        /* ===== PLANNING SYSTEM ===== */
        const PlanningSystem = {
            init() {
                GameState.selectedRoute = ['TOKYO'];
                this.renderNodeSelector();
                this.updateRouteDisplay();
            },

            renderNodeSelector() {
                const selector = document.getElementById('node-selector');
                const nodeItems = selector.querySelectorAll('.node-item');
                nodeItems.forEach(item => item.remove());

                const nodeList = Object.keys(NodeDatabase.nodes)
                    .filter(key => key !== 'TOKYO' && key !== 'BERLIN')
                    .sort();

                nodeList.forEach(nodeName => {
                    const node = NodeDatabase.nodes[nodeName];
                    const html = `
                        <div class="node-item" onclick="PlanningSystem.selectNode('${nodeName}')">
                            <div class="node-name">${nodeName}</div>
                            <div class="node-stats">
                                <div class="stat-row"><span>Risk:</span><span class="stat-value ${node.risk > 50 ? 'danger' : ''}">${node.risk}%</span></div>
                            </div>
                        </div>
                    `;
                    selector.insertAdjacentHTML('beforeend', html);
                });
            },

            selectNode(nodeName) {
                GameState.addNodeToRoute(nodeName);
                this.updateRouteDisplay();
                this.highlightSelection();
            },

            highlightSelection() {
                document.querySelectorAll('.node-item').forEach(item => {
                    item.classList.remove('selected');
                });
                GameState.selectedRoute.forEach(nodeName => {
                    document.querySelectorAll('.node-item').forEach(item => {
                        if (item.textContent.includes(nodeName)) {
                            item.classList.add('selected');
                        }
                    });
                });
            },

            updateRouteDisplay() {
                const route = GameState.selectedRoute;
                const details = GameState.routeDetails;

                let routePath = '';
                for (let i = 0; i < route.length; i++) {
                    routePath += `<span class="route-node">${route[i]}</span>`;
                    if (i < route.length - 1) {
                        routePath += `<span class="route-arrow">→</span>`;
                    }
                }
                document.getElementById('route-path').innerHTML = routePath;

                document.getElementById('route-latency').textContent = details.totalLatency + 'ms';

                const riskLevel = details.totalRisk;
                const riskElement = document.getElementById('route-risk');
                if (riskLevel < 100) {
                    riskElement.textContent = 'SAFE';
                    riskElement.style.color = '#00FF00';
                } else if (riskLevel < 200) {
                    riskElement.textContent = 'MODERATE';
                    riskElement.style.color = '#FFFF00';
                } else {
                    riskElement.textContent = 'CRITICAL';
                    riskElement.style.color = '#FF0000';
                }
            }
        };

        /* ===== HACK GAME ===== */
        const HackGame = {
            startHack() {
                HackState.timeLeft = HackState.timeLimit;
                HackState.detection = 0;
                HackState.phase = 'connecting';

                ScreenManager.switchScreen('hack');

                this.startTimer();
                this.startPhase1();
            },

            startTimer() {
                HackState.timer = setInterval(() => {
                    HackState.timeLeft--;
                    document.getElementById('time-left').textContent = HackState.timeLeft + 's';

                    if (HackState.timeLeft <= 0) {
                        clearInterval(HackState.timer);
                        this.failMission('Connection lost (timeout)');
                    }
                }, 1000);
            },

            startPhase1() {
                HackState.phase = 'connecting';
                document.getElementById('phase-title').textContent = 'Phase 1: Connecting';
                document.getElementById('phase-indicator').textContent = 'CONNECTING';
                document.getElementById('phase-display').innerHTML = '';

                const hops = GameState.selectedRoute;
                let hopIndex = 0;
                let progress = 0;

                const phaseInterval = setInterval(() => {
                    progress += Math.random() * 10;

                    if (hopIndex < hops.length && progress > hopIndex * (100 / hops.length)) {
                        this.addLog(`[→] Connecting to ${hops[hopIndex]}...`);
                        hopIndex++;
                    }

                    document.getElementById('phase-progress').style.width = Math.min(progress, 100) + '%';

                    if (Math.random() < 0.03) {
                        this.addLog(`[!] Security probe detected (+5%)`);
                        this.increaseDetection(5);
                    }

                    if (progress >= 100) {
                        clearInterval(phaseInterval);
                        setTimeout(() => this.startPhase2(), 500);
                    }
                }, 200);
            },

            startPhase2() {
                HackState.phase = 'cracking';
                document.getElementById('phase-title').textContent = 'Phase 2: Security Bypass';
                document.getElementById('phase-indicator').textContent = 'CRACKING';
                document.getElementById('phase-display').innerHTML = '';
                document.getElementById('phase-status').textContent = 'Attempting brute force...';

                const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
                let fakePassword = '';
                let progress = 0;

                const crackInterval = setInterval(() => {
                    progress += Math.random() * 6;

                    fakePassword += chars[Math.floor(Math.random() * chars.length)];
                    this.addLog(`Trying: ${fakePassword}`);

                    document.getElementById('phase-progress').style.width = Math.min(progress, 100) + '%';

                    if (Math.random() < 0.08) {
                        this.addLog(`[!] Alert triggered (+8%)`);
                        this.increaseDetection(8);
                    }

                    if (progress >= 100) {
                        clearInterval(crackInterval);
                        this.addLog(`[✓] Password found: ${fakePassword}`);
                        setTimeout(() => this.startPhase3(), 500);
                    }
                }, 150);
            },

            startPhase3() {
                HackState.phase = 'extracting';
                document.getElementById('phase-title').textContent = 'Phase 3: Data Extraction';
                document.getElementById('phase-indicator').textContent = 'EXTRACTING';
                document.getElementById('phase-display').innerHTML = '';
                document.getElementById('phase-status').textContent = 'Exfiltrating classified data...';

                const files = [
                    'international_spy_network.dat',
                    'covert_operations.log',
                    'agent_list.enc',
                    'classified_communications.bin'
                ];

                let fileIndex = 0;
                let progress = 0;

                const extractInterval = setInterval(() => {
                    progress += Math.random() * 5;

                    if (fileIndex < files.length && progress > (fileIndex / files.length) * 100) {
                        this.addLog(`[↓] Downloading: ${files[fileIndex]}`);
                        fileIndex++;
                    }

                    document.getElementById('phase-progress').style.width = Math.min(progress, 100) + '%';

                    if (Math.random() < 0.05) {
                        this.increaseDetection(3);
                    }

                    if (progress >= 100) {
                        clearInterval(extractInterval);
                        this.successMission();
                    }
                }, 200);
            },

            addLog(message) {
                const display = document.getElementById('phase-display');
                const line = document.createElement('div');
                line.textContent = message;
                display.appendChild(line);
                display.scrollTop = display.scrollHeight;
            },

            increaseDetection(amount) {
                HackState.detection += amount;
                const detectionEl = document.getElementById('detection-level');
                detectionEl.textContent = Math.min(HackState.detection, 100) + '%';

                if (HackState.detection < 50) {
                    detectionEl.className = 'status-value';
                } else if (HackState.detection < 80) {
                    detectionEl.className = 'status-value warning';
                } else {
                    detectionEl.className = 'status-value danger';
                }

                if (HackState.detection >= 100) {
                    clearInterval(HackState.timer);
                    this.failMission('Security system triggered');
                }
            },

            successMission() {
                clearInterval(HackState.timer);

                document.getElementById('result-title').textContent = 'DATA ACQUIRED';
                document.getElementById('result-title').className = 'result-title success';
                document.getElementById('result-reason').textContent = 
                    'Classified intelligence files extracted successfully.';

                document.getElementById('result-analysis').innerHTML = `
                    <div class="analysis-item">
                        <div class="analysis-label">Files Retrieved</div>
                        <div class="analysis-value">international_spy_network.dat<br>covert_operations.log<br>agent_list.enc<br>classified_communications.bin</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Detection Level</div>
                        <div class="analysis-value">${Math.round(HackState.detection)}%</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Time Used</div>
                        <div class="analysis-value">${HackState.timeLimit - HackState.timeLeft}s / ${HackState.timeLimit}s</div>
                    </div>
                `;

                ScreenManager.switchScreen('result');
            },

            failMission(reason) {
                clearInterval(HackState.timer);

                document.getElementById('result-title').textContent = 'MISSION FAILED';
                document.getElementById('result-title').className = 'result-title failure';
                document.getElementById('result-reason').textContent = reason;

                document.getElementById('result-analysis').innerHTML = `
                    <div class="analysis-item">
                        <div class="analysis-label">Failure Reason</div>
                        <div class="analysis-value">${reason}</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Detection Level</div>
                        <div class="analysis-value">${Math.round(HackState.detection)}%</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Route Used</div>
                        <div class="analysis-value">${GameState.selectedRoute.join(' → ')}</div>
                    </div>
                `;

                ScreenManager.switchScreen('result');
            }
        };

        /* ===== NETWORK BACKGROUND ===== */
        const NetworkBackground = {
            canvas: null,
            ctx: null,
            particles: [],

            init() {
                this.canvas = document.getElementById('bg-canvas');
                if (!this.canvas) return;
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
            },

            animate() {
                if (!this.ctx) return;
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

                requestAnimationFrame(() => this.animate());
            }
        };

        /* ===== INITIALIZATION ===== */
        NetworkBackground.init();

        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') ScreenManager.switchScreen('entry');
        });
    </script>
</body>
</html>
