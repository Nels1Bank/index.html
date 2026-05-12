<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NELS1RADAR | MONITORAMENTO GLOBAL</title>
    <style>
        :root {
            --bg: #00050a;
            --blue: #58a6ff;
            --text-blue: #8ec2ff;
        }

        body, html {
            margin: 0; padding: 0;
            background-color: var(--bg);
            color: #fff;
            font-family: 'Segoe UI', sans-serif;
            height: 100vh; overflow: hidden;
            display: flex;
        }

        /* PAINEL LATERAL - FONTE 10 AZUL CLARO */
        #sidebar {
            width: 250px;
            background: rgba(0, 10, 20, 0.9);
            border-right: 1px solid #161b22;
            padding: 20px;
            z-index: 100;
        }

        .title {
            font-size: 12px;
            color: var(--blue);
            font-weight: bold;
            border-bottom: 1px solid #30363d;
            padding-bottom: 10px;
            margin-bottom: 20px;
            letter-spacing: 2px;
        }

        .item {
            font-size: 10px; /* Tamanho solicitado */
            color: var(--text-blue);
            margin-bottom: 15px;
            line-height: 1.5;
            font-family: 'Courier New', monospace;
            text-transform: uppercase;
        }

        .item b { color: #fff; }

        /* VIEWPORT DO GLOBO 3D REAL */
        #viewport {
            flex-grow: 1;
            display: flex;
            justify-content: center;
            align-items: center;
            perspective: 1000px;
        }

        .sphere {
            width: 380px;
            height: 380px;
            border-radius: 50%;
            position: relative;
            transform-style: preserve-3d;
            animation: rotate 30s linear infinite;
            box-shadow: inset -30px -30px 80px #000, 
                        inset 10px 10px 60px rgba(88, 166, 255, 0.2),
                        0 0 30px rgba(88, 166, 255, 0.1);
            background: repeating-linear-gradient(rgba(88, 166, 255, 0.05) 0 1px, transparent 1px 30px),
                        repeating-linear-gradient(90deg, rgba(88, 166, 255, 0.05) 0 1px, transparent 1px 30px);
        }

        @keyframes rotate {
            from { transform: rotateY(0deg) rotateX(15deg); }
            to { transform: rotateY(360deg) rotateX(15deg); }
        }

        /* ATIVOS EM VOO (ESTILO FLIGHTRADAR) */
        .plane {
            position: absolute;
            top: 50%; left: 50%;
            font-size: 16px;
            animation: orbit var(--speed) linear infinite;
        }

        @keyframes orbit {
            from { transform: rotateY(var(--ry)) rotateX(var(--rx)) translateZ(220px) rotateY(calc(-1 * var(--ry))); }
            to { transform: rotateY(calc(var(--ry) + 360deg)) rotateX(var(--rx)) translateZ(220px) rotateY(calc(-1 * (var(--ry) + 360deg))); }
        }

        .tag {
            position: absolute;
            bottom: 30px; right: 30px;
            border: 1px solid #0ff;
            padding: 10px 20px;
            font-size: 10px;
            color: #0ff;
            font-weight: bold;
            letter-spacing: 2px;
        }
    </style>
</head>
<body>

    <div id="sidebar">
        <div class="title">📡 RADAR NELS1BANK</div>
        <div class="item"><b>USA:</b> 818.869/BTC <br> (STRATEGY VOL)</div>
        <div class="item"><b>USA:</b> 125.40/ETH <br> (POS_STAKING)</div>
        <div class="item"><b>USA:</b> 450.00/USDC <br> MILHÕES (CASH)</div>
        <div class="item"><b>BR:</b> BBAS3/B3 <br> DIVIDEND_FLOW</div>
        <div class="item"><b>BR:</b> PETR4/B3 <br> ALTA_LUZ_VOL</div>
        <div class="item"><b>S.P:</b> NELS1U/NASA <br> STATUS: OPERANTE</div>
    </div>

    <div id="viewport">
        <div class="sphere">
            <div class="plane" style="--rx:20deg; --ry:0deg; --speed:20s;">₿</div>
            <div class="plane" style="--rx:-30deg; --ry:120deg; --speed:35s;">Ξ</div>
            <div class="plane" style="--rx:50deg; --ry:240deg; --speed:45s;">S</div>
            <div class="plane" style="--rx:10deg; --ry:60deg; --speed:15s;">🚀</div>
        </div>
        <div class="tag">TAG FREE FLOW <-> ACTIVE</div>
    </div>

</body>
</html>
