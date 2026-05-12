< Real Time Nels1Radar>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NELS1RADAR - LIVE ASSET TRACKER</title>
    <style>
        :root {
            --bg: #000205;
            --radar-blue: #58a6ff;
            --radar-green: #3fb950;
            --sidebar-text: #8ec2ff;
        }

        body, html {
            margin: 0; padding: 0;
            background-color: var(--bg);
            color: var(--radar-green);
            font-family: 'Segoe UI', Tahoma, sans-serif;
            height: 100vh; overflow: hidden;
            display: flex;
        }

        /* PAINEL LATERAL - ESTILO RADAR MILITAR */
        #sidebar {
            width: 240px;
            background: rgba(1, 4, 9, 0.9);
            border-right: 1px solid #30363d;
            padding: 15px;
            overflow-y: auto;
            z-index: 10;
        }

        .sidebar-title {
            font-size: 12px;
            color: var(--radar-blue);
            border-bottom: 1px solid #30363d;
            padding-bottom: 8px;
            margin-bottom: 15px;
            letter-spacing: 1px;
            font-weight: bold;
        }

        .asset-item {
            font-size: 10px; /* Tamanho solicitado */
            color: var(--sidebar-text);
            margin-bottom: 12px;
            line-height: 1.4;
            font-family: 'Courier New', monospace;
            text-transform: uppercase;
        }

        .asset-item b { color: #fff; }

        /* ÁREA DO GLOBO */
        #viewport {
            flex-grow: 1;
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            perspective: 1200px;
        }

        .world-sphere {
            width: 450px;
            height: 450px;
            border: 1px solid rgba(88, 166, 255, 0.2);
            border-radius: 50%;
            position: relative;
            transform-style: preserve-3d;
            animation: rotateGlobe 40s linear infinite;
            background: radial-gradient(circle, rgba(88,166,255,0.05) 0%, transparent 70%);
        }

        @keyframes rotateGlobe {
            from { transform: rotateY(0deg) rotateX(15deg); }
            to { transform: rotateY(360deg) rotateX(15deg); }
        }

        /* ATIVOS EM ÓRBITA (AVIÕES) */
        .asset-plane {
            position: absolute;
            top: 50%; left: 50%;
            font-size: 14px;
            color: #fff;
            transform-origin: 0 0;
            animation: orbit var(--d) linear infinite;
            white-space: nowrap;
            text-shadow: 0 0 10px var(--radar-blue);
            font-weight: bold;
        }

        @keyframes orbit {
            from { transform: rotateY(var(--ry)) rotateX(var(--rx)) translateZ(230px) rotateY(calc(-1 * var(--ry))); }
            to { transform: rotateY(calc(var(--ry) + 360deg)) rotateX(var(--rx)) translateZ(230px) rotateY(calc(-1 * (var(--ry) + 360deg))); }
        }

        .tag-overlay {
            position: absolute;
            bottom: 25px; right: 25px;
            background: rgba(0, 255, 255, 0.05);
            border: 1px solid #00FFFF;
            padding: 8px 20px;
            font-size: 11px;
            color: #00FFFF;
            font-weight: bold;
            letter-spacing: 2px;
        }
    </style>
</head>
<body>

    <div id="sidebar">
        <div class="sidebar-title">📡 LIVE ASSETS (NELS1BANK)</div>
        
        <div class="asset-item"><b>USA:</b> 818.869/BTC <br> (STRATEGY VOLUME)</div>
        <div class="asset-item"><b>USA:</b> 125.40/ETH <br> (POS_STAKING_NODE)</div>
        <div class="asset-item"><b>USA:</b> 450.00/USDC <br> MILHÕES (LIQUIDITY)</div>
        <div class="asset-item"><b>BR:</b> BBAS3/B3 <br> DIVIDEND_FLOW_ACTIVE</div>
        <div class="asset-item"><b>BR:</b> PETR4/B3 <br> ALTA_LUZ_ESTRUTURA</div>
        <div class="asset-item"><b>S.P:</b> NELS1U/NASA <br> STATUS: OPERANTE</div>
    </div>

    <div id="viewport">
        <div class="world-sphere" id="globe"></div>
        <div class="tag-overlay">TAG FREE FLOW <-> ACTIVE</div>
    </div>

    <script>
        // Dados para as órbitas dos ativos
        const assets = [
            { icon: '₿', name: 'BTC', rx: '25deg', ry: '0deg', d: '22s' },
            { icon: 'Ξ', name: 'ETH', rx: '-20deg', ry: '120deg', d: '30s' },
            { icon: 'S', name: 'USDC', rx: '50deg', ry: '240deg', d: '40s' },
            { icon: '📡', name: 'NELS1', rx: '10deg', ry: '60deg', d: '18s' }
        ];

        const globe = document.getElementById('globe');

        assets.forEach(asset => {
            const el = document.createElement('div');
            el.className = 'asset-plane';
            el.style.setProperty('--rx', asset.rx);
            el.style.setProperty('--ry', asset.ry);
            el.style.setProperty('--d', asset.d);
            el.innerHTML = asset.icon + ' <span style="font-size:9px">' + asset.name + '</span>';
            globe.appendChild(el);
        });
    </script>
</body>
</html>
