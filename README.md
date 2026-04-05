<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hacker Terminal</title>
    <link rel="stylesheet" href="styles.css">
    <script src="script.js" defer></script>
    <style>
        body {
            background-color: black;
            font-family: 'Courier New', monospace;
            color: #0F0;
            overflow: hidden;
        }
        #desktop {
            position: relative;
            width: 100%;
            height: 100vh;
            overflow: hidden;
        }
        .window {
            border: 2px solid #0F0;
            background-color: rgba(0, 0, 0, 0.8);
            padding: 10px;
            position: absolute;
            box-shadow: 0 0 10px #0F0;
        }
        .world-map {
            position: absolute;
            width: 100%;
            height: 100%;
            background-image: url('world-map.png'); /* A world map background image */
            background-size: cover;
            opacity: 0.5;
            animation: fadeIn 5s infinite alternate;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 0.5; }
        }
    </style>
</head>
<body>
    <div id="desktop">
        <div class="world-map"></div>
        <div class="window" style="left: 20px; top: 20px; width: 300px; height: 200px;">
            <h2>Authentication</h2>
            <input type="text" id="username" placeholder="Username">
            <input type="password" id="password" placeholder="Password">
            <button onclick="authenticate()">Login</button>
        </div>
        <div class="window" style="left: 350px; top: 20px; width: 300px; height: 200px;">
            <h2>Data Extraction</h2>
            <button onclick="extractData()">Extract Data</button>
            <div id="dataOutput"></div>
        </div>
    </div>
    <script>
        function authenticate() {
            // Simple authentication process
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;
            if(username === 'admin' && password === 'password123') {
                alert('Welcome, Hacker!');
            } else {
                alert('Access Denied!');
            }
        }
        function extractData() {
            const output = document.getElementById('dataOutput');
            output.innerHTML = 'Data extracted successfully!';
            // Here would be the code to extract actual data
        }
    </script>
</body>
</html>
