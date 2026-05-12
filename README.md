<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NELS1RADAR - GLOBAL ASSET TRACKER</title>
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

        /* PAINEL LATERAL CORRIGIDO */
        #sidebar {
            width: 240px;
            min-width: 240px; /* Impede que a tabela desconfigure */
            background: rgba(1, 4, 9, 0.95);
            border-right: 1px solid #30363d;
            padding: 15px;
            overflow-y: auto;
            z-index: 10;
            box-sizing: border-box;
        }

        .sidebar-title {
            font-size: 11px;
            color: var(--radar-blue);
            border-bottom: 1px solid #30363d;
            padding-bottom: 8px;
            margin-bottom: 15px;
            letter-spacing: 2px;
            font-weight: bold;
        }

        .asset-item {
            font-size: 10px;
            color: var(--sidebar-text);
            margin-bottom: 12px;
            line-height: 1.4;
            font-family: 'Courier New', monospace;
            border-left: 2px solid transparent;
            padding-left: 5px;
        }

        .asset-item:hover { border-left: 2px solid var(--radar-blue); }
        .asset-item b { color: #fff; }

        /* VIEWPORT DO GLOBO */
        #viewport {
            flex-grow: 1;
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            perspective: 1200px; /* Aumenta a profundidade 3D */
            background: radial-gradient(circle, #000814 0%, #000205 100%);
        }

        /* O GLOBO COM CURVATURA (FIM DA TERRA PLANA) */
        .world-sphere {
            width: 400px;
            height: 400px;
            border-radius: 50%;
            position: relative;
            transform-style: preserve-3d;
            animation: rotateGlobe 40s linear infinite;
            
            /* Sombra interna para dar volume de esfera */
            box-shadow: 
                inset -50px -50px 100px rgba(0,0,0,0.9),
                inset 20px 20px 80px rgba(88, 166, 255, 0.2),
                0 0 40px rgba(88, 166, 255, 0.1);
            
            /* Linhas de Grade (Latitude/Longitude) */
            background-image: 
                linear-gradient(rgba(88, 166, 255, 0.1) 1px, transparent 1px),
                linear-gradient(90deg, rgba(88, 166, 255, 0.1) 1px, transparent 1px);
            background-size: 30px 30px;
            background-position: center;
        }

        /* Brilho na borda da "atmosfera" */
        .world-sphere::after {
            content: '';
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            border-radius: 50%;
            box-shadow: inset 0 0 20px rgba(88, 166, 255, 0.3);
            pointer-events: none;
        }

        @keyframes rotateGlobe {
            from { transform: rotateY(0deg) rotateX(15deg); }
            to { transform: rotateY(360deg) rotateX(15deg); }
        }

        /* ATIVOS EM ÓRBITA */
        .asset-plane {
            position: absolute;
            top: 50%; left: 50%;
            font-size: 16px;
            color: #fff;
            transform-origin: 0 0;
            animation: orbit var(--d) linear infinite;
            white-space: nowrap;
            text-shadow: 0 0 10px var(--radar-blue);
            font-weight: bold;
        }

        @keyframes orbit {
            /* O translateZ(240px) tira o ícone de "dentro" da terra */
            from { transform: rotateY(var(--ry)) rotateX(var(--rx)) translateZ(240px) rotateY(calc(-1 * var(--ry))); }
            to { transform: rotateY(calc(var(--ry) + 360deg)) rotateX(var(--rx)) translateZ(240px) rotateY(calc(-1 * (var(--ry) + 360deg))); }
        }

        .tag-overlay {
            position: absolute;
            bottom: 20px; right: 20px;
            background: rgba(0, 255, 255, 0.05);
            border: 1px solid #00FFFF;
            padding: 8px 15px;
            font-size: 11px;
            color: #00FFFF;
            font-weight: bold;
            letter-spacing: 2px;
            border-radius: 2px;
        }

        /* Ajuste para telas pequenas */
        @media (max-width: 768px) {
            body { flex-direction: column; }
            #sidebar { width: 100%; height: auto; border-right: none; border-bottom: 1px solid #30363d; }
            .world-sphere { width: 250px; height: 250px; }
        }
    </style>
</head>
<body>

    <div id="sidebar">
        <div class="sidebar-title">📡 NELS1RADAR v7.0</div>
        
        <div class="asset-item"><b>USA:</b> 818.869/BTC <br> (STRATEGY VOLUME)</div>
        <div class="asset-item"><b>USA:</b> 125.40/ETH <br> (POS_STAKING_NODE)</div>
        <div class="asset-item"><b>USA:</b> 450.00/USDC <br> MILHÕES (LIQUIDITY)</div>
        <div class="asset-item"><b>BR:</b> BBAS3/B3 <br> DIVIDEND_FLOW_ACTIVE</div>
        <div class="asset-item"><b>BR:</b> PETR4/B3 <br> ALTA_LUZ_VOL</div>
        <div class="asset-item"><b>S.P:</b> NELS1U/NASA <br> STATUS: OPERANTE</div>
    </div>

    <div id="viewport">
        <div class="world-sphere" id="globe"></div>
        <div class="tag-overlay">TAG FREE FLOW <-> ACTIVE</div>
    </div>

    <script>
        const assets = [
            { icon: '₿', name: 'BTC', rx: '25deg', ry: '0deg', d: '20s' },
            { icon: 'Ξ', name: 'ETH', rx: '-15deg', ry: '120deg', d: '30s' },
            { icon: 'S', name: 'USDC', rx: '45deg', ry: '240deg', d: '40s' },
            { icon: '🚀', name: 'NELS1', rx: '10deg', ry: '60deg', d: '15s' }
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
