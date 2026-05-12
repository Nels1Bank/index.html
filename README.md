< Nels1Radar>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NELS1RADAR | COMMAND CENTER</title>
    <style>
        :root { 
            --bg: #030712; 
            --accent: #10b981; /* Verde esmeralda moderno */
            --btc: #f7931a;
            --eth: #627eea;
            --sol: #14f195;
            --usdc: #2775ca;
            --glass: rgba(17, 24, 39, 0.7);
        }

        * { box-sizing: border-box; }

        body, html { 
            margin: 0; padding: 0; 
            background-color: var(--bg); 
            color: #f3f4f6; 
            font-family: 'Inter', -apple-system, sans-serif; 
            height: 100vh; overflow: hidden; 
            display: flex; 
        }

        /* SIDEBAR MODERNA */
        #sidebar { 
            width: 320px; 
            background: var(--glass);
            backdrop-filter: blur(12px);
            border-right: 1px solid rgba(255, 255, 255, 0.1); 
            display: flex;
            flex-direction: column;
            z-index: 100;
        }

        .header-ui {
            padding: 24px;
            background: linear-gradient(to bottom, rgba(16, 185, 129, 0.1), transparent);
            border-bottom: 1px solid rgba(16, 185, 129, 0.2);
        }

        .system-name { 
            font-size: 18px; 
            font-weight: 800; 
            letter-spacing: -0.5px; 
            color: #fff;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .status-dot {
            width: 8px; height: 8px;
            background: var(--accent);
            border-radius: 50%;
            box-shadow: 0 0 10px var(--accent);
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; } 50% { opacity: 0.4; } 100% { opacity: 1; }
        }

        #tx-feed { 
            flex-grow: 1;
            overflow-y: hidden; 
            padding: 16px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        /* CARD DE TRANSAÇÃO */
        .tx-card {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.05);
            padding: 12px;
            border-radius: 12px;
            font-size: 11px;
            animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1);
            transition: transform 0.2s;
        }

        .tx-card:hover { background: rgba(255, 255, 255, 0.06); }

        .tx-header { display: flex; justify-content: space-between; margin-bottom: 4px; }
        .tx-coin { font-weight: 700; text-transform: uppercase; }
        .tx-val { font-family: 'Roboto Mono', monospace; font-weight: 600; }
        .tx-meta { font-size: 9px; color: #9ca3af; margin-top: 4px; }

        @keyframes slideUp { 
            from { opacity: 0; transform: translateY(20px); } 
            to { opacity: 1; transform: translateY(0); } 
        }

        /* VIEWPORT DO GLOBO */
        #viewport { 
            flex-grow: 1; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            background: radial-gradient(circle at center, #064e3b 0%, #030712 100%);
            position: relative;
        }

        .globe-container {
            position: relative;
            width: 460px; height: 460px;
            display: flex; justify-content: center; align-items: center;
        }

        .sphere { 
            width: 100%; height: 100%; 
            border-radius: 50%; 
            position: relative;
            transform-style: preserve-3d; 
            animation: rotateGlobe 50s linear infinite; 
            background: url('https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_(large).gif') center/cover;
            box-shadow: 
                inset 0 0 80px #000, 
                0 0 100px rgba(16, 185, 129, 0.15); 
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        @keyframes rotateGlobe { 
            from { transform: rotateY(0deg) rotateX(10deg); } 
            to { transform: rotateY(360deg) rotateX(10deg); } 
        }

        /* ATIVOS VOADORES */
        .flyer { 
            position: absolute; 
            font-size: 28px; 
            pointer-events: none; 
            filter: drop-shadow(0 0 10px currentColor); 
            z-index: 200;
            font-weight: bold;
        }

        .info-tag { 
            position: absolute; bottom: 32px; right: 32px; 
            background: var(--glass);
            padding: 12px 20px; 
            border-radius: 100px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            font-size: 10px; font-weight: 600; color: #fff;
            display: flex; align-items: center; gap: 8px;
        }
    </style>
</head>
<body>

<div id="sidebar">
    <div class="header-ui">
        <div class="system-name">
            <div class="status-dot"></div>
            NELS1RADAR
        </div>
        <div style="font-size: 9px; color: #6b7280; margin-top: 4px; letter-spacing: 1px;">BASE: SANTANA DE PARNAÍBA</div>
    </div>
    <div id="tx-feed">
        </div>
</div>

<div id="viewport">
    <div class="globe-container">
        <div class="sphere" id="globe"></div>
    </div>
    <div class="info-tag">
        <span style="color: var(--accent)">●</span> LIVE ASSET FLOW ACTIVE
    </div>
</div>

<script>
    const feed = document.getElementById('tx-feed');

    // CONEXÃO REAL BITCOIN
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
        card.style.borderLeft = `4px solid ${color}`;
        card.innerHTML = `
            <div class="tx-header">
                <span class="tx-coin" style="color:${color}">${coin}</span>
                <span class="tx-val">${amount}</span>
            </div>
            <div class="tx-meta">HASH: ${Math.random().toString(16).slice(2,12).toUpperCase()}</div>
        `;
        feed.prepend(card);
        if (feed.children.length > 12) feed.lastChild.remove();

        // Efeito de Voo mais suave
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
            { transform: 'translate(0,0) scale(1.5)', opacity: 0 },
            { transform: 'translate(0,0) scale(2)', opacity: 1, offset: 0.2 },
            { transform: `translate(${-x}px, ${-y}px) scale(0)`, opacity: 0 }
        ], { duration: 3000, easing: 'cubic-bezier(0.4, 0, 0.2, 1)' }).onfinish = () => flyer.remove();
    }

    // Simulador Ativos Premium
    setInterval(() => {
        const rand = Math.random();
        if(rand > 0.88) processTx('ETH', (Math.random() * 2.5).toFixed(2), '#627eea', 'Ξ');
        else if(rand > 0.78) processTx('SOL', (Math.random() * 35).toFixed(1), '#14f195', '◎');
        else if(rand > 0.68) processTx('USDC', (Math.random() * 8000).toFixed(0), '#2775ca', 'S');
    }, 2000);
</script>

</body>
</html>
