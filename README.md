<!DOCTYPE html>
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

        /* Scan-line effect */
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

        /* Screen management */
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

        .btn:hover::before {
            left: 100%;
        }

        .btn:active {
            transform: translateY(0) scale(0.98);
        }

        .btn-subtitle {
            display: block;
            font-size: 0.8rem;
            font-weight: 500;
            margin-top: 8px;
            opacity: 0.7;
            letter-spacing: 1px;
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

        .desktop-status {
            display: flex;
            gap: 20px;
            font-size: 0.8rem;
        }

        .status-item {
            display: flex;
            gap: 8px;
            align-items: center;
        }

        .status-dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: #00FF00;
            animation: pulse 1s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.4; }
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
            background: rgba(0, 255, 255, 0.15);
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
            box-shadow: 0 0 30px rgba(0, 255, 255, 0.4), inset 0 0 20px rgba(0, 255, 255, 0.05);
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

        .window-controls {
            display: flex;
            gap: 8px;
        }

        .window-btn {
            width: 24px;
            height: 24px;
            border: 1px solid rgba(0, 255, 255, 0.5);
            background: rgba(0, 255, 255, 0.1);
            color: #00FFFF;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.8rem;
            transition: all 0.2s ease;
        }

        .window-btn:hover {
            background: rgba(0, 255, 255, 0.3);
            box-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
        }

        .window-content {
            padding: 16px;
            max-height: 400px;
            overflow-y: auto;
        }

        .window-content::-webkit-scrollbar {
            width: 8px;
        }

        .window-content::-webkit-scrollbar-track {
            background: rgba(0, 255, 255, 0.05);
        }

        .window-content::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
            border-radius: 4px;
        }

        .window-content::-webkit-scrollbar-thumb:hover {
            background: rgba(0, 255, 255, 0.5);
        }

        /* ===== CONNECTION SCREEN ===== */
        #connection {
            display: flex;
            flex-direction: column;
            background: linear-gradient(135deg, #05070A 0%, #0a0d15 100%);
            position: relative;
        }

        .map-header {
            padding: 20px;
            border-bottom: 2px solid #00FFFF;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 10;
        }

        .map-status {
            font-size: 0.9rem;
            font-weight: 700;
        }

        .map-container {
            flex: 1;
            position: relative;
            overflow: hidden;
        }

        #map-svg {
            width: 100%;
            height: 100%;
        }

        .connection-info {
            padding: 15px 20px;
            border-top: 2px solid #00FFFF;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 20px;
            font-size: 0.85rem;
            z-index: 10;
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
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3), inset 0 0 20px rgba(0, 255, 255, 0.05);
            word-break: break-all;
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

        .password-char.unlocking {
            color: #FFFF00;
            animation: charFlip 0.2s ease;
        }

        @keyframes charFlip {
            0% { transform: rotateY(0deg); }
            50% { transform: rotateY(90deg); }
            100% { transform: rotateY(0deg); }
        }

        .password-input {
            width: 100%;
            padding: 15px;
            background: rgba(0, 255, 255, 0.08);
            border: 2px solid #00FFFF;
            color: #00FFFF;
            font-family: 'Courier New', monospace;
            font-size: 1rem;
            margin-bottom: 20px;
            transition: all 0.2s ease;
        }

        .password-input:focus {
            outline: none;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.6);
            background: rgba(0, 255, 255, 0.12);
        }

        .password-input::placeholder {
            color: rgba(0, 255, 255, 0.4);
        }

        .auth-button {
            padding: 15px 40px;
            background: rgba(0, 255, 0, 0.1);
            border: 2px solid #00FF00;
            color: #00FF00;
            font-family: 'Orbitron', monospace;
            font-weight: 700;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 15px rgba(0, 255, 0, 0.3);
        }

        .auth-button:hover {
            background: rgba(0, 255, 0, 0.2);
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.6);
            transform: translateY(-2px);
        }

        .auth-button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
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

        .panel-content::-webkit-scrollbar {
            width: 6px;
        }

        .panel-content::-webkit-scrollbar-track {
            background: rgba(0, 255, 255, 0.05);
        }

        .panel-content::-webkit-scrollbar-thumb {
            background: rgba(0, 255, 255, 0.3);
        }

        .log-line {
            margin: 4px 0;
            opacity: 0;
            animation: logFade 0.3s ease forwards;
            line-height: 1.4;
        }

        @keyframes logFade {
            from {
                opacity: 0;
                transform: translateY(-5px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .log-success {
            color: #00FF00;
        }

        .log-warning {
            color: #FFFF00;
        }

        .log-error {
            color: #FF6600;
        }

        .log-info {
            color: #00FFFF;
        }

        .transfer-section {
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid rgba(0, 255, 255, 0.2);
        }

        .transfer-item {
            margin-bottom: 15px;
        }

        .transfer-name {
            font-size: 0.8rem;
            margin-bottom: 5px;
            color: #FFFF00;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .transfer-bar {
            height: 6px;
            background: rgba(0, 255, 255, 0.1);
            border: 1px solid rgba(0, 255, 255, 0.3);
            overflow: hidden;
            margin-bottom: 4px;
        }

        .transfer-fill {
            height: 100%;
            background: linear-gradient(90deg, #00FF00, #00FFFF);
            width: 0%;
            box-shadow: 0 0 8px #00FF00;
            transition: width 0.2s ease;
        }

        .transfer-speed {
            font-size: 0.7rem;
            color: #00FF00;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .extraction-stats {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid rgba(0, 255, 255, 0.2);
        }

        .stat-box {
            background: rgba(0, 255, 255, 0.05);
            padding: 8px;
            border: 1px solid rgba(0, 255, 255, 0.2);
            text-align: center;
            font-size: 0.7rem;
        }

        .stat-value {
            font-weight: 700;
            color: #00FF00;
            font-size: 1rem;
            margin: 4px 0;
        }

        .usb-transfer {
            text-align: center;
            padding: 20px;
        }

        .usb-icon {
            font-size: 3rem;
            margin-bottom: 10px;
            animation: usbBounce 2s infinite;
        }

        @keyframes usbBounce {
            0%, 100% { transform: translateY(0); opacity: 1; }
            50% { transform: translateY(-10px); opacity: 0.8; }
        }

        /* Responsive */
        @media (max-width: 1200px) {
            #extraction {
                grid-template-columns: 1fr;
            }
        }

        @media (max-width: 768px) {
            .entry-title {
                font-size: 2.5rem;
            }

            .button-group {
                flex-direction: column;
                gap: 15px;
            }

            .btn {
                width: 100%;
                max-width: 300px;
            }

            .window {
                min-width: 280px;
                max-width: 90vw;
            }

            #desktop {
                grid-template-columns: repeat(auto-fill, 80px);
                gap: 20px;
                padding: 20px;
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
                <button class="btn" onclick="alert('Mobile mode not yet implemented')">
                    MOBILE INTERFACE
                    <span class="btn-subtitle">Compact access mode</span>
                </button>
            </div>
        </div>
    </div>

    <!-- ===== DESKTOP SCREEN ===== -->
    <div id="desktop" class="screen">
        <div class="desktop-header">
            <div>SYSTEM DESKTOP</div>
            <div class="desktop-status">
                <div class="status-item">
                    <span class="status-dot"></span>
                    <span>SYSTEM ONLINE</span>
                </div>
                <div class="status-item">
                    <span class="status-dot"></span>
                    <span id="time-display">00:00:00</span>
                </div>
            </div>
        </div>

        <div class="file-icon" onclick="DesktopManager.openFile('CONNECTION')">
            <div class="icon-image">🌐</div>
            <div class="icon-name">EU_NODE.exe</div>
        </div>

        <div class="file-icon" onclick="DesktopManager.openFile('BANK')">
            <div class="icon-image">🔐</div>
            <div class="icon-name">BANK_SYS.enc</div>
        </div>

        <div class="file-icon" onclick="DesktopManager.openFile('SURVEILLANCE')">
            <div class="icon-image">📹</div>
            <div class="icon-name">SURV_NET.log</div>
        </div>

        <div class="file-icon" onclick="DesktopManager.openFile('SERVER')">
            <div class="icon-image">⚙️</div>
            <div class="icon-name">SERVER.sys</div>
        </div>

        <div class="file-icon" onclick="DesktopManager.openFile('SECURE')">
            <div class="icon-image">🔑</div>
            <div class="icon-name">SECURE.vault</div>
        </div>
    </div>

    <!-- ===== CONNECTION SCREEN ===== -->
    <div id="connection" class="screen">
        <div class="map-header">
            <div class="map-status">
                <div id="connection-status">INITIALIZING CONNECTION...</div>
            </div>
            <button class="btn" style="padding: 8px 16px; font-size: 0.9rem;" onclick="ScreenManager.switchScreen('desktop')">← BACK</button>
        </div>
        <div class="map-container">
            <svg id="map-svg" viewBox="0 0 1200 600"></svg>
        </div>
        <div class="connection-info">
            <div class="info-item">
                <span class="info-label">Source</span>
                <span class="info-value" id="source-info">TOKYO, JP</span>
            </div>
            <div class="info-item">
                <span class="info-label">Destination</span>
                <span class="info-value" id="dest-info">BERLIN, DE</span>
            </div>
            <div class="info-item">
                <span class="info-label">Latency</span>
                <span class="info-value" id="latency-info">--ms</span>
            </div>
            <div class="info-item">
                <span class="info-label">Bandwidth</span>
                <span class="info-value" id="bandwidth-info">--Mbps</span>
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

            <input type="password" class="password-input" id="password-input" placeholder="ENTER PASSWORD">
            <button class="auth-button" id="auth-submit" onclick="AuthSystem.checkPassword()">SUBMIT</button>

            <div style="margin-top: 20px;">
                <button class="btn" style="padding: 8px 16px; font-size: 0.9rem;" onclick="ScreenManager.switchScreen('desktop')">← BACK</button>
            </div>
        </div>
    </div>

    <!-- ===== DATA EXTRACTION SCREEN ===== -->
    <div id="extraction" class="screen">
        <div class="extraction-panel">
            <div class="panel-header">SYSTEM LOGS</div>
            <div class="panel-content" id="log-panel"></div>
        </div>

        <div class="extraction-panel">
            <div class="panel-header">FILE TRANSFER</div>
            <div class="panel-content">
                <div id="transfer-list"></div>
                
                <div class="usb-transfer">
                    <div class="usb-icon">💾</div>
                    <div style="font-size: 0.8rem; margin-top: 10px;">USB TRANSFER ACTIVE</div>
                </div>

                <div class="extraction-stats">
                    <div class="stat-box">
                        <div style="text-transform: uppercase; letter-spacing: 0.5px;">Files Extracted</div>
                        <div class="stat-value" id="files-count">0</div>
                    </div>
                    <div class="stat-box">
                        <div style="text-transform: uppercase; letter-spacing: 0.5px;">Data Transferred</div>
                        <div class="stat-value" id="data-size">0 GB</div>
                    </div>
                    <div class="stat-box">
                        <div style="text-transform: uppercase; letter-spacing: 0.5px;">Avg Speed</div>
                        <div class="stat-value" id="avg-speed">0 MB/s</div>
                    </div>
                    <div class="stat-box">
                        <div style="text-transform: uppercase; letter-spacing: 0.5px;">Time Elapsed</div>
                        <div class="stat-value" id="time-elapsed">00:00</div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Windows Container -->
    <div id="windows-container"></div>

    <script>
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
                    
                    // Trigger screen-specific initializations
                    if (target === 'connection') {
                        MapAnimation.init();
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
            windows: [],
            zIndex: 100,

            openFile(type) {
                const content = {
                    'CONNECTION': { title: 'EU_NODE.exe', content: 'Establishing secure connection...' },
                    'BANK': { title: 'BANK_SYS.enc', content: 'Decrypting financial data...' },
                    'SURVEILLANCE': { title: 'SURV_NET.log', content: 'Accessing surveillance network...' },
                    'SERVER': { title: 'SERVER.sys', content: 'Connecting to remote server...' },
                    'SECURE': { title: 'SECURE.vault', content: 'Opening secure vault...' }
                };

                const info = content[type];
                if (!info) return;

                if (type === 'CONNECTION') {
                    setTimeout(() => ScreenManager.switchScreen('connection'), 1500);
                } else if (type === 'BANK' || type === 'SECURE') {
                    setTimeout(() => ScreenManager.switchScreen('auth'), 1500);
                } else if (type === 'SURVEILLANCE') {
                    setTimeout(() => ScreenManager.switchScreen('extraction'), 1500);
                }

                this.createWindow(info.title, info.content);
            },

            createWindow(title, content) {
                const container = document.getElementById('windows-container');
                const win = document.createElement('div');
                win.className = 'window';
                win.style.left = (50 + this.windows.length * 30) + 'px';
                win.style.top = (50 + this.windows.length * 30) + 'px';
                win.style.zIndex = this.zIndex++;

                win.innerHTML = `
                    <div class="window-header">
                        <div class="window-title">${title}</div>
                        <div class="window-controls">
                            <div class="window-btn">−</div>
                            <div class="window-btn">□</div>
                            <div class="window-btn close-btn">×</div>
                        </div>
                    </div>
                    <div class="window-content">${content}</div>
                `;

                this.makeWindowDraggable(win);
                container.appendChild(win);
                this.windows.push(win);

                win.querySelector('.close-btn').addEventListener('click', () => {
                    win.style.animation = 'windowSlide 0.4s cubic-bezier(0.34, 1.56, 0.64, 1) reverse';
                    setTimeout(() => {
                        win.remove();
                        this.windows = this.windows.filter(w => w !== win);
                    }, 400);
                });

                win.addEventListener('mousedown', () => {
                    win.style.zIndex = this.zIndex++;
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
            packets: [],
            animationId: null,

            init() {
                this.canvas = document.getElementById('bg-canvas');
                this.ctx = this.canvas.getContext('2d');
                this.canvas.width = window.innerWidth;
                this.canvas.height = window.innerHeight;

                // Create particles
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

                // Update and draw particles
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

                // Draw connections
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

                this.animationId = requestAnimationFrame(() => this.animate());
            }
        };

        /* ===== MAP ANIMATION ===== */
        const MapAnimation = {
            svg: null,
            pathLength: 0,
            packets: [],
            latency: 245,
            bandwidth: 0,
            startTime: null,

            init() {
                this.svg = document.getElementById('map-svg');
                this.svg.innerHTML = '';
                this.packets = [];
                this.startTime = Date.now();

                this.drawWorldMap();
                this.drawConnection();
                this.startAnimation();
            },

            drawWorldMap() {
                // Simplified world map outline
                const defs = document.createElementNS('http://www.w3.org/2000/svg', 'defs');
                defs.innerHTML = `
                    <style>
                        .map-ocean { fill: rgba(0, 20, 40, 0.8); }
                        .map-land { fill: rgba(0, 80, 120, 0.3); stroke: rgba(0, 255, 255, 0.2); stroke-width: 0.5; }
                    </style>
                `;
                this.svg.appendChild(defs);

                // Ocean background
                const ocean = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
                ocean.setAttribute('width', '1200');
                ocean.setAttribute('height', '600');
                ocean.setAttribute('class', 'map-ocean');
                this.svg.appendChild(ocean);

                // Simplified continents
                this.drawContinent('M100,150 L200,100 L250,150 L200,200 Z', '#00FF00'); // North America
                this.drawContinent('M300,250 L450,200 L500,300 L400,350 Z', '#00FF00'); // Europe
                this.drawContinent('M650,300 L800,250 L850,350 L750,380 Z', '#00FF00'); // Asia
                this.drawContinent('M550,450 L650,430 L680,500 L580,520 Z', '#00FF00'); // Australia
            },

            drawContinent(path, color) {
                const land = document.createElementNS('http://www.w3.org/2000/svg', 'path');
                land.setAttribute('d', path);
                land.setAttribute('class', 'map-land');
                land.setAttribute('fill', `rgba(0, 255, 0, 0.1)`);
                this.svg.appendChild(land);
            },

            drawConnection() {
                const tokyo = { x: 950, y: 250 };
                const berlin = { x: 450, y: 150 };

                // Draw pulsing nodes
                this.createNode(tokyo, 'TOKYO');
                this.createNode(berlin, 'BERLIN');

                // Draw connection path
                const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
                path.setAttribute('d', `M${tokyo.x},${tokyo.y} Q600,100 ${berlin.x},${berlin.y}`);
                path.setAttribute('stroke', 'rgba(0, 255, 255, 0.3)');
                path.setAttribute('stroke-width', '2');
                path.setAttribute('fill', 'none');
                path.setAttribute('stroke-dasharray', '5,5');
                path.setAttribute('stroke-dashoffset', '0');
                path.style.animation = 'dash 20s linear infinite';
                this.svg.appendChild(path);

                // Add style for dash animation
                const style = document.createElementNS('http://www.w3.org/2000/svg', 'style');
                style.textContent = `
                    @keyframes dash {
                        to { stroke-dashoffset: -10; }
                    }
                    @keyframes nodePulse {
                        0%, 100% { r: 6; opacity: 1; }
                        50% { r: 10; opacity: 0.5; }
                    }
                    @keyframes nodeGlow {
                        0%, 100% { filter: drop-shadow(0 0 3px rgba(0, 255, 255, 0.5)); }
                        50% { filter: drop-shadow(0 0 15px rgba(0, 255, 255, 1)); }
                    }
                `;
                this.svg.appendChild(style);

                this.pathLength = path.getTotalLength();
            },

            createNode(pos, label) {
                const group = document.createElementNS('http://www.w3.org/2000/svg', 'g');

                const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                circle.setAttribute('cx', pos.x);
                circle.setAttribute('cy', pos.y);
                circle.setAttribute('r', '6');
                circle.setAttribute('fill', '#00FF00');
                circle.setAttribute('opacity', '0.8');
                circle.style.animation = 'nodePulse 2s infinite';
                group.appendChild(circle);

                const glow = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                glow.setAttribute('cx', pos.x);
                glow.setAttribute('cy', pos.y);
                glow.setAttribute('r', '8');
                glow.setAttribute('fill', 'none');
                glow.setAttribute('stroke', '#00FF00');
                glow.setAttribute('stroke-width', '1');
                glow.setAttribute('opacity', '0.4');
                glow.style.animation = 'nodeGlow 2s infinite';
                group.appendChild(glow);

                const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
                text.setAttribute('x', pos.x);
                text.setAttribute('y', pos.y + 25);
                text.setAttribute('text-anchor', 'middle');
                text.setAttribute('fill', '#00FFFF');
                text.setAttribute('font-size', '12');
                text.setAttribute('font-family', 'Orbitron');
                text.textContent = label;
                group.appendChild(text);

                this.svg.appendChild(group);
            },

            startAnimation() {
                const tokyo = { x: 950, y: 250 };
                const berlin = { x: 450, y: 150 };

                // Create packets along the path
                const createPacket = () => {
                    this.packets.push({
                        progress: 0,
                        startTime: Date.now()
                    });
                };

                // Create packets periodically
                const packetInterval = setInterval(() => {
                    if (this.packets.length < 8) createPacket();
                }, 300);

                const animatePackets = () => {
                    const now = Date.now();
                    const elapsed = (now - this.startTime) / 1000;

                    // Update status
                    const status = document.getElementById('connection-status');
                    if (elapsed < 3) {
                        status.textContent = 'CONNECTING...';
                        document.getElementById('latency-info').textContent = '--ms';
                        document.getElementById('bandwidth-info').textContent = '--Mbps';
                    } else if (elapsed < 8) {
                        status.textContent = 'HANDSHAKE VERIFIED';
                        this.latency = 245 + Math.random() * 20 - 10;
                        this.bandwidth = 850 + Math.random() * 150;
                        document.getElementById('latency-info').textContent = Math.round(this.latency) + 'ms';
                        document.getElementById('bandwidth-info').textContent = Math.round(this.bandwidth) + 'Mbps';
                    } else {
                        status.textContent = 'CONNECTION ESTABLISHED';
                        clearInterval(packetInterval);
                        setTimeout(() => {
                            if (ScreenManager.current === 'connection') {
                                ScreenManager.switchScreen('auth');
                            }
                        }, 3000);
                    }

                    // Remove old packets and SVG
                    document.querySelectorAll('[data-packet]').forEach(p => p.remove());

                    // Update packets
                    this.packets = this.packets.filter(p => {
                        const progress = ((now - p.startTime) / 4000) % 1;
                        if (progress < 1) {
                            const x = tokyo.x + (berlin.x - tokyo.x) * progress;
                            const y = tokyo.y + (berlin.y - tokyo.y) * progress;

                            const packet = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                            packet.setAttribute('cx', x);
                            packet.setAttribute('cy', y);
                            packet.setAttribute('r', '3');
                            packet.setAttribute('fill', '#FFFF00');
                            packet.setAttribute('opacity', '0.8');
                            packet.setAttribute('data-packet', '1');
                            packet.style.filter = 'drop-shadow(0 0 5px #FFFF00)';
                            this.svg.appendChild(packet);
                            return true;
                        }
                        return false;
                    });

                    requestAnimationFrame(animatePackets);
                };

                animatePackets();
            }
        };

        /* ===== AUTHENTICATION SYSTEM ===== */
        const AuthSystem = {
            correctPassword: '',
            displayPassword: '',
            crackProgress: 0,
            isLocked: [],

            init() {
                // Generate random 12-character password
                const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
                this.correctPassword = '';
                for (let i = 0; i < 12; i++) {
                    this.correctPassword += chars.charAt(Math.floor(Math.random() * chars.length));
                }

                this.isLocked = new Array(12).fill(false);
                this.crackProgress = 0;
                this.displayPassword = '';

                this.startCracking();
            },

            startCracking() {
                const statusEl = document.getElementById('auth-status');
                const displayEl = document.getElementById('password-display');
                const progressEl = document.getElementById('crack-progress');
                const inputEl = document.getElementById('password-input');
                const submitBtn = document.getElementById('auth-submit');

                statusEl.textContent = 'BRUTE FORCE ATTEMPT...';
                inputEl.disabled = true;
                submitBtn.disabled = true;

                const crackInterval = setInterval(() => {
                    this.crackProgress += Math.random() * 15;

                    if (this.crackProgress >= 100) this.crackProgress = 100;
                    progressEl.style.width = this.crackProgress + '%';

                    // Lock characters gradually
                    for (let i = 0; i < 12; i++) {
                        if (Math.random() < (this.crackProgress / 100) * 0.9) {
                            this.isLocked[i] = true;
                            this.displayPassword = this.correctPassword.substring(0, i + 1);
                        }
                    }

                    // Update display
                    displayEl.innerHTML = '';
                    for (let i = 0; i < 12; i++) {
                        const span = document.createElement('span');
                        span.className = 'password-char';
                        if (i < this.displayPassword.length) {
                            span.textContent = this.correctPassword[i];
                            span.classList.add('locked');
                        } else {
                            span.textContent = String.fromCharCode(65 + Math.floor(Math.random() * 26));
                            span.classList.add('unlocking');
                        }
                        displayEl.appendChild(span);
                    }

                    if (this.crackProgress >= 100) {
                        clearInterval(crackInterval);
                        statusEl.textContent = 'KEY MATCH FOUND';
                        this.displayPassword = this.correctPassword;
                        
                        displayEl.innerHTML = '';
                        for (let i = 0; i < 12; i++) {
                            const span = document.createElement('span');
                            span.className = 'password-char locked';
                            span.textContent = this.correctPassword[i];
                            displayEl.appendChild(span);
                        }

                        inputEl.disabled = false;
                        submitBtn.disabled = false;

                        setTimeout(() => {
                            statusEl.textContent = 'AWAITING USER INPUT...';
                        }, 2000);
                    }
                }, 100);
            },

            checkPassword() {
                const inputEl = document.getElementById('password-input');
                const statusEl = document.getElementById('auth-status');

                if (inputEl.value === this.correctPassword) {
                    statusEl.textContent = '✓ ACCESS GRANTED';
                    statusEl.style.color = '#00FF00';
                    inputEl.disabled = true;
                    document.getElementById('auth-submit').disabled = true;

                    setTimeout(() => {
                        ScreenManager.switchScreen('extraction');
                    }, 1500);
                } else {
                    statusEl.textContent = '✗ ACCESS DENIED';
                    statusEl.style.color = '#FF0000';
                    inputEl.style.borderColor = '#FF0000';
                    inputEl.style.boxShadow = '0 0 20px rgba(255, 0, 0, 0.6)';

                    setTimeout(() => {
                        inputEl.value = '';
                        statusEl.textContent = 'AWAITING USER INPUT...';
                        statusEl.style.color = '#00FFFF';
                        inputEl.style.borderColor = '#00FFFF';
                        inputEl.style.boxShadow = '';
                    }, 2000);
                }
            }
        };

        /* ===== DATA EXTRACTION ===== */
        const DataExtraction = {
            logs: [],
            transfers: [],
            startTime: null,
            filesCount: 0,
            dataSize: 0,

            init() {
                this.startTime = Date.now();
                this.filesCount = 0;
                this.dataSize = 0;
                this.logs = [];
                this.transfers = [];

                this.generateTransfers();
                this.startLogging();
                this.startTransfers();
                this.updateStats();
            },

            generateTransfers() {
                const files = [
                    { name: 'BANK_DATA.enc', size: 2.4 },
                    { name: 'USER_RECORDS.zip', size: 1.8 },
                    { name: 'TRANSACTION_LOG.db', size: 3.2 },
                    { name: 'SECURITY_CERTS.bin', size: 0.5 },
                    { name: 'API_KEYS.vault', size: 0.3 }
                ];

                files.forEach((file, idx) => {
                    this.transfers.push({
                        name: file.name,
                        size: file.size,
                        progress: 0,
                        speed: 0,
                        startTime: Date.now() + idx * 1000
                    });
                });
            },

            startLogging() {
                const logs = [
                    { type: 'info', text: '[INIT] Establishing secure tunnel...' },
                    { type: 'success', text: '[✓] Tunnel established - 256-bit encryption' },
                    { type: 'info', text: '[SCAN] Scanning network topology...' },
                    { type: 'success', text: '[✓] Target nodes identified (847 nodes)' },
                    { type: 'warning', text: '[!] TRACE RISK: 2.3% - Proceed with caution' },
                    { type: 'info', text: '[AUTH] Bypassing authentication layer...' },
                    { type: 'success', text: '[✓] Root access obtained' },
                    { type: 'info', text: '[EXTRACT] Beginning data exfiltration...' },
                    { type: 'warning', text: '[!] Firewall activity detected - masking traffic' },
                    { type: 'success', text: '[✓] Traffic successfully obfuscated' }
                ];

                logs.forEach((log, idx) => {
                    setTimeout(() => {
                        this.addLog(log.type, log.text);
                    }, idx * 400);
                });
            },

            addLog(type, text) {
                const panel = document.getElementById('log-panel');
                const line = document.createElement('div');
                line.className = `log-line log-${type}`;
                line.textContent = text;
                panel.appendChild(line);
                panel.scrollTop = panel.scrollHeight;
            },

            startTransfers() {
                const updateTransfers = () => {
                    const now = Date.now();
                    const container = document.getElementById('transfer-list');
                    container.innerHTML = '';

                    this.transfers.forEach((transfer, idx) => {
                        if (now - transfer.startTime < 0) return;

                        const elapsed = (now - transfer.startTime) / 1000;
                        transfer.progress = Math.min(elapsed / 8, 1);
                        transfer.speed = transfer.size / 8 * 1000; // MB/s

                        if (transfer.progress >= 1) {
                            this.filesCount++;
                            this.dataSize += transfer.size;
                        }

                        const div = document.createElement('div');
                        div.className = 'transfer-item';
                        div.innerHTML = `
                            <div class="transfer-name">${transfer.name}</div>
                            <div class="transfer-bar">
                                <div class="transfer-fill" style="width: ${transfer.progress * 100}%"></div>
                            </div>
                            <div class="transfer-speed">${transfer.speed.toFixed(1)} MB/s | ${(transfer.progress * 100).toFixed(1)}%</div>
                        `;
                        container.appendChild(div);
                    });

                    if (this.transfers.some(t => t.progress < 1)) {
                        requestAnimationFrame(updateTransfers);
                    }
                };

                updateTransfers();
            },

            updateStats() {
                const update = () => {
                    const elapsed = Math.floor((Date.now() - this.startTime) / 1000);
                    const minutes = Math.floor(elapsed / 60);
                    const seconds = elapsed % 60;

                    document.getElementById('files-count').textContent = this.filesCount;
                    document.getElementById('data-size').textContent = this.dataSize.toFixed(1) + ' GB';
                    document.getElementById('avg-speed').textContent = (this.dataSize / Math.max(elapsed, 1)).toFixed(1) + ' MB/s';
                    document.getElementById('time-elapsed').textContent = 
                        String(minutes).padStart(2, '0') + ':' + String(seconds).padStart(2, '0');

                    if (this.transfers.some(t => t.progress < 1)) {
                        requestAnimationFrame(() => this.updateStats());
                    }
                };

                update();
            }
        };

        /* ===== INITIALIZATION ===== */
        function updateClock() {
            const now = new Date();
            const time = String(now.getHours()).padStart(2, '0') + ':' +
                        String(now.getMinutes()).padStart(2, '0') + ':' +
                        String(now.getSeconds()).padStart(2, '0');
            document.getElementById('time-display').textContent = time;
        }

        // Start network background
        NetworkBackground.init();

        // Update clock
        setInterval(updateClock, 1000);
        updateClock();

        // Handle keyboard shortcuts
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') {
                ScreenManager.switchScreen('entry');
            }
            if (e.key === 'Enter' && ScreenManager.current === 'auth') {
                AuthSystem.checkPassword();
            }
        });

        // Handle window resize
        window.addEventListener('resize', () => {
            if (NetworkBackground.canvas) {
                NetworkBackground.onResize();
            }
        });
    </script>
</body>
</html>
