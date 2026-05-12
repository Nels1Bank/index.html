<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>NELS1RADAR BR | TERMINAL</title>
    <style>
        :root { 
            --bg: #000205; 
            --neon-blue: #00d2ff; 
            --br-green: #00ff41; 
            --sidebar-blue: #8ec2ff;
        }
        body, html { margin: 0; padding: 0; background: var(--bg); color: var(--br-green); font-family: 'Courier New', monospace; height: 100vh; overflow: hidden; display: flex; }
        
        /* SIDEBAR ESTILIZADA - TABELA DISTRIBUÍDA */
        #sidebar { 
            width: 300px; 
            background: rgba(0, 10, 20, 0.95); 
            border-right: 1px solid #003300; 
            display: flex;
            flex-direction: column;
            z-index: 100;
        }

        .header-table {
            padding: 15px;
            border-bottom: 2px solid #004400;
            background: rgba(0, 30, 60, 0.3);
        }

        .title { font-size: 12px; color: var(--neon-blue); font-weight: bold; letter-spacing: 2px; text-align: center; margin-bottom: 5px; }
        .subtitle { font-size: 9px; color: var(--br-green); text-align: center; opacity: 0.7; }

        /* AREA DE LOGS (TABELA DINÂMICA) */
        #tx-feed { 
            flex-grow: 1;
            overflow-y: hidden; 
            padding: 10px;
        }

        .tx-row {
            display: grid;
            grid-template-columns: 1fr 2fr;
            font-size: 10px; /* Tamanho 10 solicitado */
            color: var(--sidebar-blue);
            padding: 6px 0;
            border-bottom: 1px solid rgba(0, 255, 65, 0.1);
            animation: slideIn 0.3s ease-out;
        }

        .tx-row b { color: #fff; }
        .tx-row .val { text-align: right; font-weight: bold; }
        .tx-row .meta { grid-column: span 2; font-size: 8px; opacity: 0.6; color: var(--br-green); }

        @keyframes slideIn { from { opacity: 0; transform: translateX(-10px); } to { opacity: 1; transform: translateX(0); } }

        /* VIEWPORT E GLOBO */
        #viewport { flex-grow: 1; display: flex; justify-content: center; align-items: center; perspective: 1000px; background: radial-gradient(circle, #001a00 0%, #000205 100%); }
        .sphere { width: 420px; height: 420px; border-radius: 50%; position: relative; transform-style: preserve-3d; animation: rotateBr 40s linear infinite; 
            background: url('https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_(large).gif') center/cover;
            box-shadow: inset 0 0 80px #000, 0 0 50px rgba(0, 255, 65, 0.15); border: 1px solid rgba(0, 255, 65, 0.2); }
        
        @keyframes rotateBr { from { transform: rotateY(0deg); } to { transform: rotateY(360deg); } }

        /* CRYPTOS VOANDO */
        .flyer { position: absolute; font-weight: bold; font-size: 20px; pointer-events: none; text-shadow: 0 0 15px currentColor; z-index: 200; }
        .tag { position: absolute; bottom: 20px; right: 20px; border: 1px solid var(--br-green); padding: 8px 15px; font-size: 10px; color: var(--br-green); font-weight: bold; background: rgba(0,20,0,0.8); }
    </style>
</head>
<body>

<div id="sidebar">
    <div class="header-table">
        <div class="title">🇧🇷 RADAR NELS1BANK</div>
        <div class="subtitle">NODE: SANTANA DE PARNAÍBA</div>
    </div>
    <div id="tx-feed">
        <!-- Linhas injetadas via JS -->
    </div>
</div>

<div id="viewport">
    <div class="sphere" id="globe"></div>
    <div class="tag">STATUS: OPERANTE <-> FREE FLOW</div>
</div>

<script>
    const feed = document.getElementById('tx-feed');

    // 1. CONEXÃO REAL BITCOIN
    const btcSocket = new WebSocket('wss://ws.blockchain.info/inv');
    btcSocket.onopen = () => btcSocket.send(JSON.stringify({"op":"unconfirmed_sub"}));
    btcSocket.onmessage = (msg) => {
        const data = JSON.parse(msg.data);
        if (data.op === 'utx') {
            const val = (data.x.out.reduce((a, b) => a + b.value, 0) / 100000000).toFixed(4);
            processTx('BTC', val, '#f7931a', '₿');
        }
    };

    // 2. PROCESSADOR DE TABELA E VOO
    function processTx(coin, amount, color, symbol) {
        // Cria linha na Tabela
        const row = document.createElement('div');
        row.className = 'tx-row';
        row.style.borderLeft = `3px solid ${color}`;
        row.style.paddingLeft = '8px';
        row.innerHTML = `
            <span><b>${coin}</b></span>
            <span class="val" style="color:${color}">${amount}</span>
            <span class="meta">ID:${Math.random().toString(16).slice(2,10).toUpperCase()} | LOC: BR-SP</span>
        `;
        feed.prepend(row);
        if (feed.children.length > 20) feed.lastChild.remove();

        // Cria o Voo
        const flyer = document.createElement('div');
        flyer.className = 'flyer';
        flyer.innerText = symbol;
        flyer.style.color = color;
        
        const angle = Math.random() * Math.PI * 2;
        const x = Math.cos(angle) * 280;
        const y = Math.sin(angle) * 280;
        
        flyer.style.left = `calc(50% + ${x}px)`;
        flyer.style.top = `calc(50% + ${y}px)`;
        
        document.getElementById('viewport').appendChild(flyer);
        
        flyer.animate([
            { transform: 'translate(0,0) scale(1.5)', opacity: 1 },
            { transform: `translate(${-x}px, ${-y}px) scale(0)`, opacity: 0 }
        ], { duration: 2500 }).onfinish = () => flyer.remove();
    }

    // 3. SIMULADOR MULTI-ATIVOS (ETH, SOL, USDC)
    setInterval(() => {
        const rand = Math.random();
        if(rand > 0.85) processTx('ETH', (Math.random() * 3).toFixed(2), '#627eea', 'Ξ');
        else if(rand > 0.75) processTx('SOL', (Math.random() * 40).toFixed(1), '#14f195', '◎');
        else if(rand > 0.65) processTx('USDC', (Math.random() * 5000).toFixed(0), '#2775ca', 'S');
    }, 1500);
</script>

</body>
</html>
