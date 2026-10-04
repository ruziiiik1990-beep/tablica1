<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Рейтинг</title>
    <script src="https://cloudflare.com"></script>
    <style>
        * { margin:0; padding:0; box-sizing:border-box; }
        body { overflow:hidden; background:#13151c; width:100vw; height:100vh; font-family:'Segoe UI', sans-serif; display:flex; justify-content:center; align-items:center; perspective:1000px; }
        #tablica1, .tablica1, h1 { display:none !important; visibility:hidden !important; opacity:0 !important; height:0 !important; }
        .card { position:relative; width:100%; max-width:650px; background:#13151c !important; border:1px solid rgba(59,130,246,0.2); border-radius:16px; padding:30px; box-shadow:0 10px 30px rgba(0,0,0,0.5); transform-style:preserve-3d; transition:transform 0.5s ease, border-color 0.5s ease; }
        .wrapper { position:absolute; top:0; left:0; width:100%; height:100%; border-radius:16px; pointer-events:none; transform-style:preserve-3d; }
        .wrapper::before, .wrapper::after { content:""; position:absolute; left:50%; transform:translateX(-50%) translateZ(-1px); width:80%; background:linear-gradient(90deg, transparent, #3b82f6, transparent); filter:blur(20px); opacity:0; transition:opacity 0.5s ease, height 0.5s ease; }
        .wrapper::before { top:-5px; height:5px; }
        .wrapper::after { bottom:-5px; height:5px; }
        .card:hover .wrapper::before, .card:hover .wrapper::after { opacity:1; } 
        .card:hover .wrapper::after { height:120px; background:linear-gradient(180deg, transparent, rgba(59,130,246,0.4)); }
        .card:hover { border-color:rgba(59,130,246,0.6); }
        .title { width:100%; transition:transform 0.5s ease; transform:translate3d(0%, 0px, 0px); }
        .card:hover .title { transform:translate3d(0%, -30px, 80px); }
        .text-line-container { display:flex; flex-direction:column; align-items:center; position:relative; width:100%; }
        .main-header-title { text-align:center; font-size:22px; font-weight:800; color:#3b82f6; text-transform:uppercase; letter-spacing:1.5px; text-shadow:0 0 10px rgba(59,130,246,0.5); transform:translateZ(40px); }
        .svg-line { width:100%; height:4px; margin-top:8px; display:block; }
        .line-path { stroke:#3b82f6; stroke-width:2; fill:none; stroke-dasharray:1000; stroke-dashoffset:1000; }
        table { width:100%; border-collapse:collapse; text-align:center; color:#fff; background:#13151c !important; margin-top:15px; }
        th { font-size:13px; color:#94a3b8; letter-spacing:1px; padding-bottom:10px; text-transform:uppercase; border-bottom:2px solid rgba(255,255,255,0.1); }
        td { padding:12px 5px; font-size:15px; font-weight:500; background:#13151c !important; }
        tr { border-bottom:1px solid rgba(255,255,255,0.05); background:#13151c !important; }
        .rank { color:#94a3b8; font-weight:bold; }
        .gold { color:#facc15; text-shadow:0 0 8px rgba(250,204,21,0.3); }
        .orange { color:#f97316; }
        .chak-icon { display:inline-flex; align-items:center; justify-content:center; gap:6px; color:#facc15; vertical-align:middle; }
        .chak-img { width:20px; height:20px; object-fit:contain; border-radius:50%; background:#fff !important; padding:1px; display:inline-block; }
        .footer-text { text-align:center; font-size:12px; color:#576575; letter-spacing:0.5px; }
    </style>
</head>
<body>

<div class="card" id="tableCard">
    <div class="wrapper"></div>
    <div class="title">
        <div class="text-line-container" style="margin-bottom:25px;">
            <div class="main-header-title">Таблица Рейтинга</div>
            <svg class="svg-line"><path class="line-path" d="M 0 2 L 650 2" /></svg>
        </div>
        <table>
            <thead>
                <tr>
                    <th style="width:10%;">#</th>
                    <th style="width:30%;">ИГРОК</th>
                    <th style="width:20%;">ЧЕМПИОН</th>
                    <th style="width:20%;">ФИНАЛИСТ</th>
                    <th style="width:20%;">ЧАК-ЧАК</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td class="rank">1</td><td class="gold">X-Slayer_99</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
                <tr>
                    <td class="rank">2</td><td>Neon_Viper</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
                <tr>
                    <td class="rank">3</td><td class="orange">Chak_Master</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
                <tr>
                    <td class="rank">4</td><td>Cyber_Glitch</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
                <tr>
                    <td class="rank">5</td><td>Zeus_Awper</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
                <tr>
                    <td class="rank">6</td><td>Shadow_Step</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
                <tr>
                    <td class="rank">7</td><td>Bullet_Rain</td><td class="orange">1</td><td class="orange">0</td>
                    <td><span class="chak-icon">2 <img src="https://moy.su" class="chak-img" alt="*"></span></td>
                </tr>
            </tbody>
        </table>
        <div class="text-line-container" style="margin-top:20px;">
            <div class="footer-text">Победитель финала +2 чак-чака · Финалист +1 чак-чак</div>
            <svg class="svg-line" style="margin-top:5px;"><path class="line-path" d="M 150 2 L 500 2" /></svg>
        </div>
    </div>
</div>

<script>
    function animateLines() {
        anime({ targets: '.line-path', strokeDashoffset: [anime.setDashoffset, 0], easing: 'easeInOutQuad', duration: 2000, delay: anime.stagger(150), loop: true, direction: 'alternate' });
    }
    const card = document.getElementById('tableCard');
    window.addEventListener('mousemove', (e) => {
        card.style.transform = `rotateX(${-((e.clientY / window.innerHeight) - 0.5) * 30}deg) rotateY(${((e.clientX / window.innerWidth) - 0.5) * 30}deg)`;
    });
    card.addEventListener('mouseleave', () => { card.style.transform = `rotateX(0deg) rotateY(0deg)`; });
    document.addEventListener("DOMContentLoaded", () => { animateLines(); });
</script>
</body>
</html>
