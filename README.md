<!Nels1Radar>
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
            --sidebar-text: #8ec2ff; /* Azul claro estilo FlightRadar */
        }

        body, html {
            margin: 0; padding: 0;
            background-color: var(--bg);
            color: var(--radar-green);
            font-family: 'Segoe UI', Tahoma, sans-serif;
            height: 100vh; overflow: hidden;
            display: flex;
        }

        /* --- PAINEL LATERAL (LISTA DE VÔOS) --- */
        #sidebar {
            width: 220px;
            background: rgba(1, 4, 9, 0.9);
            border-right: 1px solid #30363d;
            padding: 10px;
            overflow-y: auto;
            z-index: 10;
        }

        .sidebar-title {
            font-size: 12px;
            color: var(--radar-blue);
            border-bottom: 1px solid #30363d;
            padding-bottom: 5px;
            margin-bottom: 10px;
            letter-spacing: 1px;
        }

        .asset-item {
            font-size: 10px; /* Tamanho solicitado */
            color: var(--sidebar-text);
            margin-bottom: 8px;
            line-height: 1.2;
            font-family: 'Courier New', monospace;
        }

        .asset-item b { color: #fff; }

        /* --- GLOBO E MAPA --- */
        #viewport {
            flex-grow: 1;
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            perspective: 1000px;
        }

        /* O Globo 3D */
        .world-sphere {
            width: 400px;
            height: 400px;
            border: 1px solid rgba(88, 166, 255, 0.2);
            border-radius: 50%;
            position: relative;
            transform-style: preserve-3d;
            animation: rotateGlobe 40s linear infinite;
        }

        /* Linhas de Latitude/Longitude */
        .world-sphere::before {
            content: '';
            position: absolute;
            width: 100%; height: 100%;
            border-radius: 50%;
            box-shadow: inset 0 0 50px rgba(88, 166, 255, 0.1);
        }

        @keyframes rotateGlobe {
            from { transform: rotateY(0deg) rotateX(15deg); }
            to { transform: rotateY(360deg) rotateX(15deg); }
        }

        /* Os "Aviões" (Ativos) */
        .asset-plane {
            position: absolute;
            top: 50%; left: 50%;
            font-size: 14px;
            color: #fff;
            transform-origin: 0 0;
            animation: orbit var(--d) linear infinite;
            white-space: nowrap;
            text-shadow: 0 0 10px var(--radar-blue);
        }

        @keyframes orbit {
            from { transform: rotateY(var(--ry)) rotateX(var(--rx)) translateZ(210px) rotateY(calc(-1 * var(--ry))); }
            to { transform: rotateY(calc(var(--ry) + 360deg)) rotateX(var(--rx)) translateZ(210px) rotateY(calc(-1 * (var(--ry) + 360deg))); }
        }

        /* TAG FREE FLOW */
        .tag-overlay {
            position: absolute;
            bottom: 20px; right: 20px;
            background: rgba(0, 255, 255, 0.1);
            border: 1px solid #00FFFF;
            padding: 5px 15px;
            font-size: 12px;
            color: #00FFFF;
            font-weight: bold;
            border-radius: 4px;
        }
    </style>
</head>
<body>

    <div id="sidebar">
        <div class="sidebar-title">📡 LIVE ASSETS (B3/ANDRÔMEDA)</div>
        
        <!-- Legendas solicitadas -->
        <div class="asset-item"><b>USA:</b> 818.869/BTC <br> (STRATEGY VOL)</div>
        <div class="asset-item"><b>USA:</b> 125.40/ETH <br> (POS_STAKING)</div>
        <div class="asset-item"><b>USA:</b> 450.00/USDC <br> MILHÕES (CASH)</div>
        <div class="asset-item"><b>BR:</b> BBAS3/B3 <br> DIVIDEND_FLOW</div>
        <div class="asset-item"><b>BR:</b> PETR4/B3 <br> ALTA_LUZ_VOL</div>
        <div class="asset-item"><b>S.P:</b> NELS1U/NASA <br> STATUS: OPERANTE</div>
    </div>

    <div id="viewport">
        <div class="world-sphere" id="globe">
            <!-- Os ativos serão injetados aqui via JS -->
        </div>
        
        <div class="tag-overlay">TAG FREE FLOW <-> ACTIVE</div>
    </div>

    <script>
        const assets = [
            { icon: '₿', name: 'BTC', rx: '20deg', ry: '0deg', d: '25s' },
            { icon: 'Ξ', name: 'ETH', rx: '-30deg', ry: '45deg', d: '35s' },
            { icon: 'S', name: 'USDC', rx: '60deg', ry: '90deg', d: '45s' },
            { icon: '🚀', name: 'NELS1', rx: '-10deg', ry: '180deg', d: '15s' }
        ];

        const globe = document.getElementById('globe');

        assets.forEach(asset => {
            const el = document.createElement('div');
            el.className = 'asset-plane';
            el.style.setProperty('--rx', asset.rx);
            el.style.setProperty('--ry', asset.ry);
            el.style.setProperty('--d', asset.d);
            el.innerHTML = asset.icon + ' <span style="font-size:8px">' + asset.name + '</span>';
            globe.appendChild(el);
        });
    </script>
</body>
</html>
