<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nels1Radar</title>
    <style>
        :root { 
            --bg: #030712; 
            --btc-orange: #f7931a; 
            --accent: #10b981; 
            --glass: rgba(17, 24, 39, 0.8);
        }

        * { box-sizing: border-box; outline: none; }

        body, html { 
            margin: 0; padding: 0; 
            background-color: var(--bg); 
            color: #f3f4f6; 
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; 
            height: 100vh; overflow: hidden; 
            display: flex; 
        }

        /* SIDEBAR PROFISSIONAL */
        #sidebar { 
            width: 320px; 
            background: var(--glass);
            backdrop-filter: blur(15px);
            border-right: 1px solid rgba(247, 147, 26, 0.2); 
            display: flex;
            flex-direction: column;
            z-index: 100;
            box-shadow: 5px 0 15px rgba(0,0,0,0.5);
        }

        .header-ui {
            padding: 30px 20px;
            background: linear-gradient(to bottom, rgba(247, 147, 26, 0.1), transparent);
            border-bottom: 1px solid var(--btc-orange);
            text-align: center;
        }

        .system-name { 
            font-size: 24px; 
            font-weight: 900; 
            letter-spacing: 1px; 
            color: var(--btc-orange); /* LARANJA BTC NO TÍTULO */
            text-transform: uppercase;
            text-shadow: 0 0 15px rgba(247, 147, 26, 0.4);
        }

        .location-tag {
            font-size: 10px;
            color: #9ca3af;
            margin-top: 8px;
            letter-spacing: 2px;
            font-weight: bold;
        }

        #tx-feed { 
            flex-grow: 1; 
            overflow: hidden; 
            padding: 20px; 
            display: flex; 
            flex-direction: column; 
            gap: 12px; 
        }

        .tx-card {
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid rgba(255, 255, 255, 0.05);
            padding: 14px;
            border-radius: 8px;
            font-size: 12px;
            border-left: 4px solid var(--btc-orange);
            animation: fadeIn 0.5s ease;
        }

        .tx-header { display: flex; justify-content: space-between; margin-bottom: 5px; font-weight: bold; }
        .tx-id { font-family: monospace; font-size: 10px; color: #6b7280; }

        @keyframes fadeIn { from { opacity: 0; transform: translateX(-10px); } to { opacity: 1; transform: translateX(0); } }

        /* VIEWPORT DO GLOBO */
        #viewport { 
            flex-grow: 1; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            background: radial-gradient(circle at center, #0a1a10 0%, #030712 100%);
            position: relative;
        }

        .sphere { 
            width: 480px; height: 480px; 
            border-radius: 50%; 
            animation: rotateGlobe 60s linear infinite; 
            background: url('https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_(large).gif') center/cover;
            box-shadow: inset 0 0 50px #000, 0 0 30px rgba(16, 185, 129, 0.1);
            opacity: 0.8;
        }

        @keyframes rotateGlobe { from { transform: rotateY(0deg); } to { transform: rotateY(360deg); } }

        .flyer { position: absolute; font-size: 30px; pointer-events: none; filter: drop-shadow(0 0 10px currentColor); z-index: 200; font-weight: bold; }

        .status-bar { 
            position: absolute; bottom: 20px; right: 20px; 
            font-size: 11px; font-weight: bold; color: var(--accent);
            background: rgba(0,0,0,0.8); padding: 8px 15px; border-radius: 4px;
            border: 1px solid var(--accent);
        }
    </style>
</head>
<body>

<div id="sidebar">
    <div class="header-ui">
        <div class="system-name">Nels1Radar</div>
        <div class="location-tag">SANTANA DE PARNAÍBA</div>
    </div>
    <div id="tx-feed">
        <!-- Transações entram aqui -->
    </div>
</div>

<div id="viewport">
    <div class="sphere"></div>
    <div class="status-bar">● SISTEMA OPERANTE</div>
</div>

<script>
    const feed = document.getElementById('tx-feed');
    const btcSocket = new WebSocket('wss://ws.blockchain.info/inv');
    
    btcSocket.onopen = () => btcSocket.send(JSON.stringify({"op":"unconfirmed_sub"}));
    btcSocket.onmessage = (msg) => {
        const data = JSON.parse(msg.data);
        if (data.op === 'utx') {
            const val = (data.x.out.reduce((a, b) => a + b.value, 0) / 100000000).toFixed(4);
            processTx('BTC', val, '#f7931a', '₿');
        }
    };

    function processTx(coin, amount, color, symbol) {
        const card = document.createElement('div');
        card.className = 'tx-card';
        card.style.borderLeftColor = color;
        card.innerHTML = `
            <div class="tx-header">
                <span style="color:${color}">${coin}</span>
                <span>${amount}</span>
            </div>
            <div class="tx-id">HASH: ${Math.random().toString(16).slice(2,10).toUpperCase()}</div>
        `;
        feed.prepend(card);
        if (feed.children.length > 14) feed.lastChild.remove();

        const flyer = document.createElement('div');
        flyer.className = 'flyer';
        flyer.innerText = symbol;
        flyer.style.color = color;
        const angle = Math.random() * Math.PI * 2;
        const x = Math.cos(angle) * 350;
        const y = Math.sin(angle) * 350;
        flyer.style.left = `calc(50% + ${x}px)`;
        flyer.style.top = `calc(50% + ${y}px)`;
        document.getElementById('viewport').appendChild(flyer);
        flyer.animate([
            { transform: 'scale(1.2)', opacity: 1 },
            { transform: `translate(${-x}px, ${-y}px) scale(0)`, opacity: 0 }
        ], { duration: 3000 }).onfinish = () => flyer.remove();
    }

    // Outras moedas para volume
    setInterval(() => {
        const rand = Math.random();
        if(rand > 0.8) processTx('ETH', (Math.random() * 1.5).toFixed(2), '#627eea', 'Ξ');
        else if(rand > 0.7) processTx('SOL', (Math.random() * 20).toFixed(1), '#14f195', '◎');
    }, 2500);
</script>

</body>
</html>
