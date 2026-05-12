</Nels1Radar>
<html lang="pt-br">
<head>
    <meta charset="UTF-9">
    <title> NELS1RADAR | REAL-TIME BLOCKCHAIN</title>
    <style>
        :root { --bg: #00050a; --blue: #58a6ff; --text-blue: #8ec2ff; }
        body, html { margin: 0; padding: 0; background: var(--bg); color: #fff; font-family: 'Courier New', monospace; height: 100vh; overflow: hidden; display: flex; }
        
        /* SIDEBAR DINÂMICA */
        #sidebar { width: 280px; background: rgba(0, 10, 20, 0.95); border-right: 1px solid #161b22; padding: 15px; z-index: 100; overflow-y: auto; }
        .title { font-size: 12px; color: var(--blue); font-weight: bold; border-bottom: 1px solid #30363d; padding-bottom: 10px; margin-bottom: 15px; letter-spacing: 2px; }
        .tx-item { font-size: 9px; color: var(--text-blue); margin-bottom: 8px; border-left: 2px solid var(--blue); padding-left: 5px; animation: fadeIn 0.5s; }
        @keyframes fadeIn { from { opacity: 0; transform: translateX(-10px); } to { opacity: 1; transform: translateX(0); } }

        /* GLOBO E ÓRBITAS */
        #viewport { flex-grow: 1; display: flex; justify-content: center; align-items: center; perspective: 1200px; background: radial-gradient(circle, #001529 0%, #00050a 100%); }
        .sphere { width: 400px; height: 400px; border-radius: 50%; position: relative; transform-style: preserve-3d; animation: rotateGlobe 40s linear infinite; background: url('https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_ (large).gif') center/cover; opacity: 0.8; box-shadow: inset 0 0 100px #000, 0 0 40px rgba(88, 166, 255, 0.2); }
        
        @keyframes rotateGlobe { from { transform: rotateY(0deg); } to { transform: rotateY(360deg); } }

        /* SÍMBOLOS VOADORES (PARTÍCULAS DE TX) */
        .crypto-flyer { position: absolute; font-weight: bold; pointer-events: none; text-shadow: 0 0 10px currentColor; }
    </style>
</head>
<body>

<div id="sidebar">
    <div class="title">📡 LIVE TRANSACTIONS</div>
    <div id="tx-feed">
        <div class="tx-item">AGUARDANDO CONEXÃO...</div>
    </div>
</div>

<div id="viewport">
    <div class="sphere" id="globe"></div>
</div>

<script>
    const feed = document.getElementById('tx-feed');
    const globe = document.getElementById('globe');

    // CONEXÃO WEBSOCKET REAL (BLOCKCHAIN.INFO)
    const btcSocket = new WebSocket('wss://ws.blockchain.info/inv');
    
    btcSocket.onopen = () => {
        btcSocket.send(JSON.stringify({"op":"unconfirmed_sub"}));
    };

    btcSocket.onmessage = (msg) => {
        const data = JSON.parse(msg.data);
        if (data.op === 'utx') {
            const val = (data.x.out.reduce((a, b) => a + b.value, 0) / 100000000).toFixed(4);
            updateFeed('BTC', val, '#f7931a');
            createFlyer('₿', '#f7931a');
        }
    };

    function updateFeed(coin, amount, color) {
        const div = document.createElement('div');
        div.className = 'tx-item';
        div.style.borderColor = color;
        div.innerHTML = `<b>${coin}:</b> ${amount}<br>TX_${Math.random().toString(16).slice(2,8).toUpperCase()}`;
        feed.prepend(div);
        if (feed.children.length > 15) feed.lastChild.remove();
    }

    function createFlyer(symbol, color) {
        const flyer = document.createElement('div');
        flyer.className = 'crypto-flyer';
        flyer.innerText = symbol;
        flyer.style.color = color;
        
        // Posicionamento Aleatório ao redor do globo
        const angle = Math.random() * Math.PI * 2;
        const x = Math.cos(angle) * 250;
        const y = Math.sin(angle) * 250;
        
        flyer.style.left = `calc(50% + ${x}px)`;
        flyer.style.top = `calc(50% + ${y}px)`;
        
        document.getElementById('viewport').appendChild(flyer);
        
        // Animação de "Voo"
        flyer.animate([
            { transform: 'translate(0,0) scale(1)', opacity: 1 },
            { transform: `translate(${-x/2}px, ${-y/2}px) scale(0)`, opacity: 0 }
        ], { duration: 2000, easing: 'ease-out' }).onfinish = () => flyer.remove();
    }

    // Simulador de ETH/USDC para manter o radar ativo (Websocket real de ETH exige API Key)
    setInterval(() => {
        if(Math.random() > 0.7) {
            updateFeed('ETH', (Math.random() * 5).toFixed(2), '#627eea');
            createFlyer('Ξ', '#627eea');
        }
        if(Math.random() > 0.8) {
            updateFeed('USDC', (Math.random() * 1000).toFixed(0), '#2775ca');
            createFlyer('S', '#2775ca');
        }
    }, 3000);
</script>

</body>
</html>
