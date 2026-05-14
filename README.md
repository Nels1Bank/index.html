
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Nels1Radar</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@600&family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #000; --surf: #0a0a0a; --prim: #00e5ff; --cry: #f59e0b;
            --txt: #fff; --dim: #888; --brd: #1a1a1a;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { 
            font-family: 'Inter', sans-serif; background: var(--bg); color: var(--txt); 
            height: 100vh; display: flex; flex-direction: column; overflow: hidden;
        }
        header { padding: 12px 0; border-bottom: 1px solid var(--brd); text-align: center; flex-shrink: 0; }
        h1 { font-family: 'Orbitron'; font-size: 1.1rem; color: var(--prim); letter-spacing: 2px; }
        
        /* Letreiro Digital - BitcoinTreasuries Data */
        .ticker { background: #050505; padding: 8px 0; overflow: hidden; white-space: nowrap; border-bottom: 1px solid var(--brd); }
        .track { display: inline-block; animation: scroll 25s linear infinite; font-size: 0.8rem; color: var(--prim); font-weight: 700; }
        .asset { padding: 0 20px; border-right: 1px solid var(--brd); }
        @keyframes scroll { from { transform: translateX(0); } to { transform: translateX(-50%); } }

        main { flex-grow: 1; padding: 12px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        .section-label { font-family: 'Orbitron'; font-size: 0.65rem; color: var(--dim); margin-bottom: 5px; }

        /* Grid de Criptos e B3 */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
        .card { background: var(--surf); border: 1px solid var(--brd); padding: 12px; border-radius: 10px; }
        .card label { display: block; font-size: 0.6rem; color: var(--dim); text-transform: uppercase; margin-bottom: 4px; }
        .card span { font-size: 1rem; font-weight: 700; }
        .cry-border { border-left: 3px solid var(--cry); }

        /* Gráfico B3 */
        .b3-container { flex-grow: 1; min-height: 250px; border: 1px solid var(--brd); border-radius: 10px; overflow: hidden; }

        footer { 
            padding: 12px; text-align: center; font-size: 0.7rem; color: var(--dim); 
            border-top: 1px solid var(--brd); background: #000; flex-shrink: 0;
        }
    </style>
</head>
<body>

<header><h1>Nels1Radar</h1></header>

<div class="ticker">
    <div class="track">
        <span class="asset">STRATEGY INC: 818,869 BTC ▲</span>
        <span class="asset">METAPLANET: 40,177 BTC ▲</span>
        <span class="asset">BPAC3: R$ 25,12 (CORRECTION) ▼</span>
        <span class="asset">IBOV: 128.450 pts ▲</span>
        <span class="asset">STRATEGY INC: 818,869 BTC ▲</span>
        <span class="asset">METAPLANET: 40,177 BTC ▲</span>
    </div>
</div>

<main>
    <div class="section-label">Cripto Assets (Live)</div>
    <div class="grid">
        <div class="card cry-border"><label>BITCOIN</label><span>$80,340.50</span></div>
        <div class="card"><label>ETHEREUM</label><span>$4,120.15</span></div>
        <div class="card"><label>SOLANA</label><span>$174.45</span></div>
        <div class="card"><label>USDC</label><span>$1.0001</span></div>
    </div>

    <div class="section-label">B3 Radar - BPAC3 & Market</div>
    <div class="b3-container">
        <!-- Widget focado em BPAC3 conforme seu comentário -->
        <iframe src="https://s.tradingview.com/widgetembed/?symbol=BMFBOVESPA%3ABPAC3&interval=D&hidesidetoolbar=1&symboledit=0&saveimage=0&toolbarbg=f1f3f6&studies=%5B%5D&theme=dark&style=1&timezone=America%2FSao_Paulo&studies_overrides=%7B%7D&overrides=%7B%7D&enabled_features=%5B%5D&disabled_features=%5B%5D&locale=br" width="100%" height="100%" frameborder="0"></iframe>
    </div>
</main>

<footer>
    Nels1Radar by MaquinadoDigital - 2026 - [&lt;o&gt;]
</footer>

</body>
</html>
