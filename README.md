< Nels1Radar />
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title> | NELS1RADAR BR | LIVE TRACKER</title>
    <style>
        :root { --bg: #000205; --neon-blue: #00d2ff; --br-green: #00ff41; }
        body, html { margin: 0; padding: 0; background: var(--bg); color: var(--br-green); font-family: 'Courier New', monospace; height: 100vh; overflow: hidden; display: flex; }
        
        /* by MaquinadoDigital */
        #sidebar { width: 360px; background: rgba(0, 15, 30, 0.9); border-right: 1px solid #003300; padding: 15px; z-index: 100; overflow-y: auto; box-shadow: 5px 0 15px rgba(0, 255, 65, 0.1); }
        .title { font-size: 12px; color: var(--neon-blue); font-weight: bold; border-bottom: 1px solid #004400; padding-bottom: 10px; margin-bottom: 15px; letter-spacing: 2px; text-align: center; }
        .tx-item { font-size: 9px; color: #8ec2ff; margin-bottom: 8px; border-left: 2px solid var(--br-green); padding-left: 8px; animation: slideIn 0.3s ease-out; }
        @keyframes slideIn { from { opacity: 0; transform: translateX(-20px); } to { opacity: 1; transform: translateX(0); } }

        /* VIEWPORT E GLOBO */
        #viewport { flex-grow: 1; display: flex; justify-content: center; align-items: center; perspective: 1000px; background: radial-gradient(circle, #001a00 0%, #000205 100%); }
        .sphere { width: 420px; height: 420px; border-radius: 50%; position: relative; transform-style: preserve-3d; animation: rotateBr 40s linear infinite; 
            background: url('https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_(large).gif') center/cover;
            box-shadow: inset 0 0 80px #000, 0 0 50px rgba(0, 255, 65, 0.15); border: 1px solid rgba(0, 255, 65, 0.2); }
        
        @keyframes rotateBr { from { transform: rotateY(0deg) rotateX(15deg); } to { transform: rotateY(360deg) rotateX(15deg); } }

        /* AS CRYPTOS VOANDO */
        .flyer { position: absolute; font-weight: bold; font-size: 18px; pointer-events: none; text-shadow: 0 0 15px currentColor; z-index: 200; }
        
        .tag { position: absolute; bottom: 20px; right: 20px; border: 1px solid var(--br-green); padding: 8px 15px; font-size: 10px; color: var(--br-green); font-weight: bold; background: rgba(0,20,0,0.5); }
    </style>
</head>
<body>

<div id="sidebar">
    <div class="title">🇧🇷 Nels1Radar </div>
    <div id="tx-feed">
        <div class="tx-item">ESTABILIZANDO SINAL...</div>
    </div>
</div>

<div id="viewport">
    <div class="sphere" id="globe"></div>
    <div class="tag">NELS1BANK | SANTANA DE PARNAÍBA </div>
</div>

<script>
    const feed = document.getElementById('tx-feed');
    const globe = document.getElementById('globe');

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

    // 2. PROCESSADOR DE TRANSAÇÕES E VOO
    function processTx(coin, amount, color, symbol) {
        // Atualiza Tabela
        const div = document.createElement('div');
        div.className = 'tx-item';
        div.style.borderColor = color;
        div.innerHTML = `<b>${coin}:</b> ${amount}<br>LOC: BR-SÃO PAULO`;
        feed.prepend(div);
        if (feed.children.length > 18) feed.lastChild.remove();

        // Cria o Voo
        const flyer = document.createElement('div');
        flyer.className = 'flyer';
        flyer.innerText = symbol;
        flyer.style.color = color;
        
        const angle = Math.random() * Math.PI * 2;
        const dist = 220 + Math.random() * 50;
        const x = Math.cos(angle) * dist;
        const y = Math.sin(angle) * dist;
        
        flyer.style.left = `calc(50% + ${x}px)`;
        flyer.style.top = `calc(50% + ${y}px)`;
        
        document.getElementById('viewport').appendChild(flyer);
        
        flyer.animate([
            { transform: 'translate(0,0) scale(1.5)', opacity: 1 },
            { transform: `translate(${-x}px, ${-y}px) scale(0)`, opacity: 0 }
        ], { duration: 2500, easing: 'linear' }).onfinish = () => flyer.remove();
    }

    // 3. SIMULADOR ETH, SOL E USDC (PARA MANTER O RADAR VIVO)
    setInterval(() => {
        const rand = Math.random();
        if(rand > 0.8) processTx('ETH', (Math.random() * 2).toFixed(2), '#627eea', 'Ξ');
        else if(rand > 0.6) processTx('SOL', (Math.random() * 50).toFixed(1), '#14f195', '◎');
        else if(rand > 0.4) processTx('USDC', (Math.random() * 10000).toFixed(0), '#2775ca', 'S');
    }, 2000);
</script>

</body>
</html>
