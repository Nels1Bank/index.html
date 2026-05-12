<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <!-- O nome que aparece na aba do navegador -->
    <title>  Nels1Radar  | COMMAND CENTER</title>
    <style>
        :root { 
            --bg: #000205; 
            --neon-blue: #00d2ff; 
            --br-green: #00ff41; 
            --sidebar-blue: #8ec2ff;
        }
        body, html { margin: 0; padding: 0; background: var(--bg); color: var(--br-green); font-family: 'Times new roman', monospace; height: 100vh; overflow: hidden; display: flex; }
        
        /* SIDEBAR - DESIGN Nels1Radar */
        #sidebar { 
            width: 300px; 
            background: rgba(0, 10, 20, 0.98); 
            border-right: 1px solid #004400; 
            display: flex;
            flex-direction: column;
            z-index: 100;
            box-shadow: 10px 0 20px rgba(0,0,0,0.5);
        }

        .header-table {
            padding: 20px 15px;
            border-bottom: 2px solid #00ff41;
            background: linear-gradient(180deg, rgba(0, 30, 60, 0.4) 0%, rgba(0,0,0,0) 100%);
        }

        .system-name { font-size: 13px; color: var(--neon-blue); font-weight: bold; letter-spacing: 3px; text-align: center; }
        .system-status { font-size: 9px; color: var(--br-green); text-align: center; margin-top: 5px; letter-spacing: 1px; }

        #tx-feed { 
            flex-grow: 1;
            overflow-y: hidden; 
            padding: 15px;
        }

        .tx-row {
            display: grid;
            grid-template-columns: 1fr 2.5fr;
            font-size: 10px;
            color: var(--sidebar-blue);
            padding: 8px 0;
            border-bottom: 1px solid rgba(0, 255, 65, 0.1);
            animation: slideIn 0.3s ease-out;
        }

        .tx-row b { color: #fff; font-size: 11px; }
        .tx-row .val { text-align: right; font-weight: bold; }
        .tx-row .meta { grid-column: span 2; font-size: 8px; opacity: 0.5; color: var(--br-green); margin-top: 2px; }

        @keyframes slideIn { from { opacity: 0; transform: translateX(-15px); } to { opacity: 1; transform: translateX(0); } }

        /* VIEWPORT E GLOBO */
        #viewport { flex-grow: 1; display: flex; justify-content: center; align-items: center; perspective: 1200px; }
        .sphere { width: 440px; height: 440px; border-radius: 50%; position: relative; transform-style: preserve-3d; animation: rotateBr 45s linear infinite; 
            background: url('https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_(large).gif') center/cover;
            box-shadow: inset 0 0 100px #000, 0 0 60px rgba(0, 255, 65, 0.2); border: 1px solid rgba(0, 255, 65, 0.3); }
        
        @keyframes rotateBr { from { transform: rotateY(0deg) rotateX(10deg); } to { transform: rotateY(360deg) rotateX(10deg); } }

        /* AS CRYPTOS VOANDO */
        .flyer { position: absolute; font-weight: bold; font-size: 20px; pointer-events: none; text-shadow: 0 0 20px currentColor; z-index: 200; }
        
        .footer-tag { 
            position: absolute; bottom: 20px; right: 20px; 
            border: 1px solid var(--br-green); padding: 10px 20px; 
            font-size: 10px; color: var(--br-green); font-weight: bold; 
            background: rgba(0,0,0,0.9); box-shadow: 0 0 15px rgba(0,255,65,0.2);
        }
    </style>
</head>
<body>

<div id="sidebar">
    <div class="header-table">
        <div class="system-name">Nels1Radar v1.0</div>
        <div class="system-status">CORE: OPERATIONAL | BR-SP</div>
    </div>
    <div id="tx-feed">
        <!-- Live stream das transações -->
    </div>
</div>

<div id="viewport">
    <div class="sphere" id="globe"></div>
    <div class="footer-tag">SANTANA DE PARNAÍBA SP // ASSET_FLOW: ACTIVE</div>
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
        const row = document.createElement('div');
        row.className = 'tx-row';
        row.style.borderLeft = `4px solid ${color}`;
        row.style.paddingLeft = '10px';
        row.innerHTML = `
            <span><b>${coin}</b></span>
            <span class="val" style="color:${color}">${amount}</span>
            <span class="meta">NELS1_ID:${Math.random().toString(16).slice(2,10).toUpperCase()} | TRACE: OK</span>
        `;
        feed.prepend(row);
        if (feed.children.length > 22) feed.lastChild.remove();

        // Efeito de Voo
        const flyer = document.createElement('div');
        flyer.className = 'flyer';
        flyer.innerText = symbol;
        flyer.style.color = color;
        const angle = Math.random() * Math.PI * 2;
        const x = Math.cos(angle) * 300;
        const y = Math.sin(angle) * 300;
        flyer.style.left = `calc(50% + ${x}px)`;
        flyer.style.top = `calc(50% + ${y}px)`;
        document.getElementById('viewport').appendChild(flyer);
        flyer.animate([
            { transform: 'translate(0,0) scale(1.8)', opacity: 1 },
            { transform: `translate(${-x}px, ${-y}px) scale(0)`, opacity: 0 }
        ], { duration: 2500 }).onfinish = () => flyer.remove();
    }

    // Simulador Multi-ativos
    setInterval(() => {
        const rand = Math.random();
        if(rand > 0.85) processTx('ETH', (Math.random() * 4).toFixed(2), '#627eea', 'Ξ');
        else if(rand > 0.75) processTx('SOL', (Math.random() * 60).toFixed(1), '#14f195', '◎');
        else if(rand > 0.65) processTx('USDC', (Math.random() * 15000).toFixed(0), '#2775ca', 'S');
    }, 1800);
</script>

</body>
</html>
