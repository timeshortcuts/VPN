<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hacker Interface Entry Screen</title>
    <style>
        body {
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
            background: #000;
            color: #fff;
            font-family: 'Arial', sans-serif;
            position: relative;
        }
        .network {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: url('https://example.com/network-animation.gif') no-repeat center center fixed;
            background-size: cover;
            z-index: -1;
            animation: flicker 1.5s infinite;
        }
        @keyframes flicker {
            0%, 100% { opacity: 0.9; }
            50% { opacity: 0.6; }
        }
        .title {
            font-size: 3rem;
            text-align: center;
            animation: flicker-title 2s infinite;
        }
        @keyframes flicker-title {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.8; }
        }
        .mode-selection {
            margin-top: 20px;
            display: flex;
            justify-content: center;
        }
        .mode-button {
            background-color: #007acc;
            border: none;
            color: white;
            padding: 15px 25px;
            font-size: 1.5rem;
            margin: 0 10px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.3s ease;
        }
        .mode-button:hover {
            background-color: #005f99;
            transform: scale(1.05);
        }
    </style>
</head>
<body>
    <div class="network"></div>
    <div class="title">System Title</div>
    <div class="mode-selection">
        <button class="mode-button">Mode 1</button>
        <button class="mode-button">Mode 2</button>
    </div>
</body>
</html>
