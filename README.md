
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
        }

        body {
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

        /* ===== ENTRY ===== */
        #entry {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            overflow: hidden;
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
            position: relative;
            overflow: hidden;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .btn:hover {
            transform: translateY(-3px) scale(1.03);
            box-shadow: 0 0 40px rgba(0, 255, 255, 0.6);
        }

        .btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        /* ===== DESKTOP ===== */
        #desktop {
            display: grid;
            grid-template-columns: repeat(auto-fill, 100px);
            gap: 40px;
            padding: 40px;
            background: linear-gradient(135deg, #05070A 0%, #0a0d1a 100%);
            align-content: start;
            overflow: auto;
        }

        .desktop-header {
            grid-column: 1 / -1;
            padding: 15px 20px;
            border-bottom: 2px solid #00FFFF;
            font-size: 0.9rem;
            margin-bottom: 20px;
            display: flex;
            justify-content: space-between;
        }

        .file-icon {
            text-align: center;
            cursor: pointer;
            padding: 10px;
            transition: all 0.2s;
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
            box-shadow: 0 0 10px rgba(0, 255, 255, 0.3);
        }

        .icon-name {
            font-size: 0.9rem;
            margin-top: 8px;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        /* ===== PLANNING SCREEN (SCROLLABLE) ===== */
        #planning {
            display: flex;
            flex-direction: column;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            overflow: hidden;
            position: relative;
        }

        .planning-header {
            flex-shrink: 0;
            text-align: center;
            padding: 30px 40px;
            border-bottom: 2px solid rgba(0, 255, 255, 0.3);
            background: linear-gradient(to bottom, rgba(0, 255, 255, 0.05), transparent);
            z-index: 5;
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

        .planning-content {
            flex: 1;
            display: grid;
            grid-template-columns: 1fr 1.2fr;
            gap: 20px;
            padding: 20px;
            overflow: auto;
            min-height: 0;
        }

        /* Scrollbar styling for planning-content */
        .planning-content::-webkit-scrollbar {
            width: 12px;
            height: 12px;
        }

        .planning-content::-webkit-scrollbar-track {
            background: rgba(0, 255, 255, 0.05);
        }

        .planning-content::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
            border-radius: 6px;
            border: 2px solid rgba(0, 255, 255, 0.05);
        }

        .planning-content::-webkit-scrollbar-thumb:hover {
            background: rgba(0, 255, 255, 0.5);
        }

        /* Node selector */
        .node-selector {
            border: 2px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.05);
            border-radius: 4px;
            padding: 20px;
            overflow-y: auto;
            overflow-x: hidden;
        }

        .node-selector::-webkit-scrollbar {
            width: 8px;
        }

        .node-selector::-webkit-scrollbar-track {
            background: rgba(0, 255, 255, 0.05);
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
            position: sticky;
            top: 0;
            background: rgba(0, 255, 255, 0.05);
            padding: 10px 0;
            z-index: 2;
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

        .stat-label {
            opacity: 0.6;
        }

        .stat-value {
            font-weight: 700;
            color: #00FF00;
        }

        .stat-value.warning {
            color: #FFFF00;
        }

        .stat-value.danger {
            color: #FF6400;
        }

        /* Route builder */
        .route-builder {
            border: 2px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.05);
            border-radius: 4px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .builder-label {
            font-size: 0.9rem;
            font-weight: 700;
            margin-bottom: 15px;
            text-transform: uppercase;
            flex-shrink: 0;
        }

        .route-path {
            flex-shrink: 0;
            display: flex;
            align-items: center;
            justify-content: flex-start;
            gap: 8px;
            margin-bottom: 20px;
            min-height: 60px;
            background: rgba(0, 0, 0, 0.3);
            padding: 15px;
            border-radius: 3px;
            border: 1px solid rgba(0, 255, 255, 0.2);
            font-size: 0.85rem;
            overflow-x: auto;
            overflow-y: hidden;
        }

        .route-path::-webkit-scrollbar {
            height: 6px;
        }

        .route-path::-webkit-scrollbar-track {
            background: rgba(0, 255, 255, 0.05);
        }

        .route-path::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
            border-radius: 3px;
        }

        .route-node {
            padding: 8px 12px;
            background: rgba(0, 255, 255, 0.15);
            border: 1px solid rgba(0, 255, 255, 0.4);
            border-radius: 3px;
            white-space: nowrap;
            font-weight: 700;
            flex-shrink: 0;
        }

        .route-arrow {
            opacity: 0.5;
            flex-shrink: 0;
            font-weight: bold;
        }

        .route-stats {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-top: auto;
            font-size: 0.8rem;
            flex-shrink: 0;
        }

        .route-stat {
            border: 1px solid rgba(0, 255, 255, 0.3);
            padding: 10px;
            background: rgba(0, 255, 255, 0.05);
            border-radius: 3px;
        }

        .route-stat-label {
            opacity: 0.7;
            font-size: 0.7rem;
            text-transform: uppercase;
        }

        .route-stat-value {
            font-weight: 900;
            font-size: 1.2rem;
            color: #00FF00;
            margin-top: 5px;
        }

        .route-stat-value.warning {
            color: #FFFF00;
        }

        .route-stat-value.danger {
            color: #FF0000;
        }

        /* Planning actions */
        .planning-actions {
            flex-shrink: 0;
            display: flex;
            gap: 15px;
            justify-content: center;
            padding: 20px 40px;
            border-top: 2px solid rgba(0, 255, 255, 0.3);
            background: linear-gradient(to bottom, transparent, rgba(0, 255, 255, 0.05));
            z-index: 5;
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
        }

        .action-btn:hover:not(:disabled) {
            background: rgba(0, 255, 255, 0.15);
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.5);
        }

        .action-btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        .action-btn.danger {
            border-color: #FF6400;
            color: #FF6400;
        }

        .action-btn.danger:hover:not(:disabled) {
            background: rgba(255, 100, 0, 0.15);
            box-shadow: 0 0 20px rgba(255, 100, 0, 0.5);
        }

        /* ===== CONNECTION ===== */
        #connection {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            position: relative;
            overflow: hidden;
        }

        .connection-header {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            padding: 20px;
            border-bottom: 2px solid rgba(0, 255, 255, 0.3);
            z-index: 20;
        }

        .connection-status {
            font-size: 0.9rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: #00FFFF;
        }

        .connection-status.critical {
            color: #FF0000;
            text-shadow: 0 0 10px #FF0000;
        }

        .map-container {
            width: 90vw;
            max-width: 1100px;
            height: 70vh;
            border: 2px solid #00FFFF;
            box-shadow: 0 0 40px rgba(0, 255, 255, 0.3);
            position: relative;
            background: linear-gradient(135deg, rgba(0, 20, 40, 0.9) 0%, rgba(0, 10, 20, 0.9) 100%);
            overflow: hidden;
        }

        #map-svg {
            width: 100%;
            height: 100%;
            position: relative;
            z-index: 3;
        }

        .connection-info {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 15px 20px;
            border-top: 2px solid #00FFFF;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 15px;
            font-size: 0.8rem;
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
            letter-spacing: 0.5px;
            font-size: 0.7rem;
        }

        .info-value {
            font-weight: 700;
            color: #00FF00;
        }

        .info-value.danger {
            color: #FF0000;
        }

        /* ===== RESULT ===== */
        #result {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #05070A 0%, #0a1a0a 100%);
            padding: 40px;
            text-align: center;
            overflow: auto;
        }

        .result-container {
            z-index: 10;
            max-width: 700px;
        }

        .result-title {
            font-size: 3rem;
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
            font-size: 1.1rem;
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
            margin-bottom: 0;
            padding-bottom: 0;
        }

        .analysis-label {
            font-weight: 700;
            color: #00FFFF;
            text-transform: uppercase;
            font-size: 0.8rem;
            margin-bottom: 5px;
        }

        .analysis-value {
            opacity: 0.8;
            line-height: 1.4;
        }

        .result-actions {
            display: flex;
            gap: 15px;
            justify-content: center;
        }

        /* Responsive for smaller screens */
        @media (max-width: 1200px) {
            .planning-content {
                grid-template-columns: 1fr;
            }
        }

        @media (max-width: 768px) {
            .planning-header {
                padding: 20px;
            }

            .planning-title {
                font-size: 1.5rem;
            }

            .planning-content {
                padding: 15px;
                gap: 15px;
            }

            .planning-actions {
                padding: 15px 20px;
            }

            .entry-title {
                font-size: 2.5rem;
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
            <button class="btn" onclick="ScreenManager.switchScreen('desktop')">
                INITIATE BREACH
                <span class="btn-subtitle">Plan your attack</span>
            </button>
        </div>
    </div>

    <!-- ===== DESKTOP ===== -->
    <div id="desktop" class="screen">
        <div class="desktop-header">
            <div>SYSTEM DESKTOP</div>
        </div>
        <div class="file-icon" onclick="DesktopManager.openFile()">
            <div class="icon-image">🌐</div>
            <div class="icon-name">EU_NODE.exe</div>
        </div>
    </div>

    <!-- ===== PLANNING SCREEN ===== -->
    <div id="planning" class="screen">
        <div class="planning-header">
            <div class="planning-title">ROUTE PLANNING</div>
            <div class="planning-subtitle">Select nodes and build your attack route</div>
        </div>

        <div class="planning-content">
            <!-- Node Selector -->
            <div class="node-selector" id="node-selector">
                <div class="selector-label">Available Nodes</div>
            </div>

            <!-- Route Builder -->
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

        <div class="planning-actions">
            <button class="action-btn" onclick="ScreenManager.switchScreen('desktop')">← CANCEL</button>
            <button class="action-btn" id="execute-btn" disabled onclick="PlanningSystem.executeRoute()">EXECUTE →</button>
        </div>
    </div>

    <!-- ===== CONNECTION ===== -->
    <div id="connection" class="screen">
        <div class="connection-header">
            <div class="connection-status" id="connection-status">INITIALIZING...</div>
        </div>

        <div class="map-container">
            <svg id="map-svg" viewBox="0 0 1200 600"></svg>
            <div class="connection-info">
                <div class="info-item">
                    <span class="info-label">Current Hop</span>
                    <span class="info-value" id="current-hop">--</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Risk Level</span>
                    <span class="info-value" id="risk-level">--</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Detection</span>
                    <span class="info-value" id="detection-level">CLEAR</span>
                </div>
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
                'TOKYO': { 
                    x: 950, y: 250, 
                    lat: 35.6762, lon: 139.6503, 
                    traffic: 'HIGH',
                    security: 'STRONG',
                    risk: 15,
                    latency: 0,
                    inaccessible: false
                },
                'BEIJING': { 
                    x: 850, y: 220, 
                    lat: 39.9042, lon: 116.4074, 
                    traffic: 'MEDIUM',
                    security: 'STRONG',
                    risk: 35,
                    latency: 85,
                    inaccessible: false
                },
                'MOSCOW': { 
                    x: 550, y: 180, 
                    lat: 55.7558, lon: 37.6173, 
                    traffic: 'LOW',
                    security: 'CRITICAL',
                    risk: 70,
                    latency: 180,
                    inaccessible: false
                },
                'ISTANBUL': { 
                    x: 600, y: 220, 
                    lat: 41.0082, lon: 28.9784, 
                    traffic: 'HIGH',
                    security: 'MEDIUM',
                    risk: 40,
                    latency: 120,
                    inaccessible: false
                },
                'DUBAI': { 
                    x: 700, y: 300, 
                    lat: 25.2048, lon: 55.2708, 
                    traffic: 'VERY_HIGH',
                    security: 'WEAK',
                    risk: 25,
                    latency: 140,
                    inaccessible: false
                },
                'SINGAPORE': { 
                    x: 900, y: 380, 
                    lat: 1.3521, lon: 103.8198, 
                    traffic: 'MEDIUM',
                    security: 'MEDIUM',
                    risk: 45,
                    latency: 220,
                    inaccessible: false
                },
                'FRANKFURT': { 
                    x: 480, y: 160, 
                    lat: 50.1109, lon: 8.6821, 
                    traffic: 'HIGH',
                    security: 'CRITICAL',
                    risk: 60,
                    latency: 200,
                    inaccessible: false
                },
                'AMSTERDAM': {
                    x: 470, y: 140,
                    lat: 52.3676, lon: 4.9041,
                    traffic: 'MEDIUM',
                    security: 'MEDIUM',
                    risk: 35,
                    latency: 190,
                    inaccessible: false
                },
                'LONDON': {
                    x: 410, y: 130,
                    lat: 51.5074, lon: -0.1278,
                    traffic: 'HIGH',
                    security: 'MEDIUM',
                    risk: 38,
                    latency: 210,
                    inaccessible: false
                },
                'PARIS': {
                    x: 440, y: 145,
                    lat: 48.8566, lon: 2.3522,
                    traffic: 'MEDIUM',
                    security: 'STRONG',
                    risk: 42,
                    latency: 205,
                    inaccessible: false
                },
                'BERLIN': { 
                    x: 450, y: 150, 
                    lat: 52.5200, lon: 13.4050, 
                    traffic: 'MEDIUM',
                    security: 'CRITICAL',
                    risk: 0,
                    latency: 0,
                    inaccessible: false,
                    isTarget: true
                }
            },

            calculateDistance(node1, node2) {
                const R = 6371;
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

        /* ===== GAME STATE ===== */
        const GameState = {
            selectedRoute: ['TOKYO'],
            routeDetails: {
                totalDistance: 0,
                totalLatency: 0,
                totalRisk: 0,
                nodeSequence: []
            },

            addNodeToRoute(nodeName) {
                if (this.selectedRoute.includes(nodeName)) {
                    this.selectedRoute = this.selectedRoute.filter(n => n !== nodeName);
                } else if (nodeName !== 'BERLIN' && nodeName !== 'TOKYO') {
                    this.selectedRoute.splice(this.selectedRoute.length - 1, 0, nodeName);
                }
                this.recalculateRoute();
            },

            recalculateRoute() {
                let totalDistance = 0;
                let totalLatency = 0;
                let totalRisk = 0;

                for (let i = 0; i < this.selectedRoute.length - 1; i++) {
                    const from = NodeDatabase.nodes[this.selectedRoute[i]];
                    const to = NodeDatabase.nodes[this.selectedRoute[i + 1]];
                    
                    totalDistance += NodeDatabase.calculateDistance(from, to);
                    totalLatency += to.latency;
                    totalRisk += to.risk;
                }

                this.routeDetails = {
                    totalDistance,
                    totalLatency,
                    totalRisk,
                    nodeSequence: this.selectedRoute
                };

                this.validateRoute();
            },

            validateRoute() {
                const route = this.selectedRoute;
                if (route[route.length - 1] !== 'BERLIN') {
                    document.getElementById('execute-btn').disabled = true;
                    return;
                }

                let criticalCount = 0;
                for (let i = 1; i < route.length - 1; i++) {
                    const node = NodeDatabase.nodes[route[i]];
                    if (node.security === 'CRITICAL') {
                        criticalCount++;
                    }
                }

                if (criticalCount > 1) {
                    document.getElementById('execute-btn').disabled = true;
                    return;
                }

                document.getElementById('execute-btn').disabled = false;
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
                    
                    if (target === 'planning') {
                        PlanningSystem.init();
                    } else if (target === 'connection') {
                        ConnectionSimulation.start();
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
                                <div class="stat-row">
                                    <span class="stat-label">Risk:</span>
                                    <span class="stat-value ${node.risk > 50 ? 'danger' : node.risk > 30 ? 'warning' : ''}">${node.risk}%</span>
                                </div>
                                <div class="stat-row">
                                    <span class="stat-label">Sec:</span>
                                    <span class="stat-value">${node.security.charAt(0)}</span>
                                </div>
                                <div class="stat-row">
                                    <span class="stat-label">Traffic:</span>
                                    <span class="stat-value">${node.traffic.charAt(0)}</span>
                                </div>
                                <div class="stat-row">
                                    <span class="stat-label">Latency:</span>
                                    <span class="stat-value">${node.latency}ms</span>
                                </div>
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

                document.getElementById('route-distance').textContent = details.totalDistance + 'km';
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
            },

            executeRoute() {
                ScreenManager.switchScreen('connection');
            }
        };

        /* ===== CONNECTION SIMULATION ===== */
        const ConnectionSimulation = {
            currentHop: 0,
            route: GameState.selectedRoute,
            totalRisk: 0,
            detectionLevel: 0,

            async start() {
                this.route = GameState.selectedRoute;
                this.currentHop = 0;
                this.totalRisk = 0;
                this.detectionLevel = 0;

                this.drawNetwork();
                await this.traverseRoute();
            },

            drawNetwork() {
                const svg = document.getElementById('map-svg');
                svg.innerHTML = '';

                Object.keys(NodeDatabase.nodes).forEach(key => {
                    const node = NodeDatabase.nodes[key];
                    this.drawNode(svg, node, key);
                });

                for (let i = 0; i < this.route.length - 1; i++) {
                    const from = NodeDatabase.nodes[this.route[i]];
                    const to = NodeDatabase.nodes[this.route[i + 1]];

                    const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
                    line.setAttribute('x1', from.x);
                    line.setAttribute('y1', from.y);
                    line.setAttribute('x2', to.x);
                    line.setAttribute('y2', to.y);
                    line.setAttribute('stroke', 'rgba(0, 255, 255, 0.3)');
                    line.setAttribute('stroke-width', '2');
                    svg.appendChild(line);
                }
            },

            drawNode(svg, node, key) {
                const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                circle.setAttribute('cx', node.x);
                circle.setAttribute('cy', node.y);
                circle.setAttribute('r', '8');

                let color = '#00FFFF';
                if (key === 'TOKYO') color = '#00FF00';
                if (key === 'BERLIN') color = '#FF0000';
                if (this.route.includes(key) && key !== 'TOKYO' && key !== 'BERLIN') {
                    color = '#FFFF00';
                }

                circle.setAttribute('fill', color);
                circle.setAttribute('opacity', '0.7');
                svg.appendChild(circle);

                const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
                text.setAttribute('x', node.x);
                text.setAttribute('y', node.y - 20);
                text.setAttribute('text-anchor', 'middle');
                text.setAttribute('fill', color);
                text.setAttribute('font-size', '11');
                text.setAttribute('font-weight', 'bold');
                text.textContent = key;
                svg.appendChild(text);
            },

            async traverseRoute() {
                const route = this.route;

                for (let i = 1; i < route.length; i++) {
                    const nodeName = route[i];
                    const node = NodeDatabase.nodes[nodeName];

                    document.getElementById('connection-status').textContent = 
                        `TRAVERSING: ${route[i-1]} → ${nodeName}`;
                    document.getElementById('current-hop').textContent = nodeName;

                    this.totalRisk += node.risk;
                    this.detectionLevel = Math.min(this.totalRisk / 10, 100);

                    const riskEl = document.getElementById('risk-level');
                    if (this.detectionLevel < 30) {
                        riskEl.textContent = 'LOW';
                        riskEl.className = 'info-value';
                    } else if (this.detectionLevel < 70) {
                        riskEl.textContent = 'MEDIUM';
                        riskEl.className = 'info-value warning';
                    } else {
                        riskEl.textContent = 'HIGH';
                        riskEl.className = 'info-value danger';
                    }

                    document.getElementById('detection-level').textContent = 
                        Math.round(this.detectionLevel) + '%';

                    if (this.detectionLevel > 85) {
                        await new Promise(res => setTimeout(res, 1500));
                        this.failMission(`Detection threshold exceeded at ${nodeName}`);
                        return;
                    }

                    if (node.security === 'CRITICAL' && Math.random() < 0.4) {
                        await new Promise(res => setTimeout(res, 1500));
                        this.failMission(`${nodeName} security system triggered`);
                        return;
                    }

                    await new Promise(res => setTimeout(res, 2000));
                }

                this.successMission();
            },

            successMission() {
                const analysisHTML = `
                    <div class="analysis-item">
                        <div class="analysis-label">Route Chosen</div>
                        <div class="analysis-value">${GameState.selectedRoute.join(' → ')}</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Total Risk Accumulated</div>
                        <div class="analysis-value">${this.totalRisk}% (Below threshold: 100%)</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Detection Level</div>
                        <div class="analysis-value">${Math.round(this.detectionLevel)}% (Safe)</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Decision Quality</div>
                        <div class="analysis-value">Excellent route planning. You selected the optimal path with minimal security exposure.</div>
                    </div>
                `;

                document.getElementById('result-title').textContent = 'MISSION SUCCESS';
                document.getElementById('result-title').className = 'result-title success';
                document.getElementById('result-reason').textContent = 
                    'You successfully breached EU_NODE through careful route planning and risk management.';
                document.getElementById('result-analysis').innerHTML = analysisHTML;

                ScreenManager.switchScreen('result');
            },

            failMission(reason) {
                const analysisHTML = `
                    <div class="analysis-item">
                        <div class="analysis-label">Failure Reason</div>
                        <div class="analysis-value">${reason}</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Route Chosen</div>
                        <div class="analysis-value">${GameState.selectedRoute.join(' → ')}</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">Risk Accumulated</div>
                        <div class="analysis-value">${this.totalRisk}%</div>
                    </div>
                    <div class="analysis-item">
                        <div class="analysis-label">What Went Wrong</div>
                        <div class="analysis-value">Your route triggered security systems. Consider avoiding CRITICAL security nodes or reducing overall risk by choosing alternative paths.</div>
                    </div>
                `;

                document.getElementById('result-title').textContent = 'MISSION FAILED';
                document.getElementById('result-title').className = 'result-title failure';
                document.getElementById('result-reason').textContent = 
                    'Your breach attempt was detected and intercepted.';
                document.getElementById('result-analysis').innerHTML = analysisHTML;

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

        /* ===== DESKTOP MANAGER ===== */
        const DesktopManager = {
            openFile() {
                setTimeout(() => ScreenManager.switchScreen('planning'), 1500);
            }
        };

        /* ===== INITIALIZATION ===== */
        NetworkBackground.init();

        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') ScreenManager.switchScreen('entry');
        });

        window.addEventListener('resize', () => {
            if (NetworkBackground.canvas) {
                NetworkBackground.canvas.width = window.innerWidth;
                NetworkBackground.canvas.height = window.innerHeight;
            }
        });
    </script>
</body>
</html>
