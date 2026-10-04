<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица ЧакЧак — Живой Турнир</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            overflow: hidden; background: #13151c; width: 100vw; height: 100vh; 
            font-family: 'Segoe UI', sans-serif; display: flex; justify-content: center; align-items: center; perspective: 1000px;
        }
        h1, .title h1, #tablica1, .tablica1 { display: none !important; }
        
        .card {
            position: relative; width: 100%; max-width: 650px; height: 480px; background: #13151c !important; 
            border: 1px solid rgba(59, 130, 246, 0.2); border-radius: 16px; padding: 25px 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5); backdrop-filter: blur(10px); transform-style: preserve-3d; transition: transform 0.5s ease;
        }
        .wrapper { position: absolute; top: 0; left: 0; width: 100%; height: 100%; border-radius: 16px; pointer-events: none; transform-style: preserve-3d; }
        .wrapper::before, .wrapper::after {
            content: ""; position: absolute; left: 50%; transform: translateX(-50%) translateZ(-1px); width: 80%;
            background: linear-gradient(90deg, transparent, #3b82f6, transparent); filter: blur(20px); opacity: 0; transition: opacity 0.5s, height 0.5s;
        }
        .wrapper::before { top: -5px; height: 5px; }
        .wrapper::after { bottom: -5px; height: 120px; }
        .card:hover .wrapper::before, .card:hover .wrapper::after { opacity: 1; } 
        .card:hover .wrapper::after { height: 120px; background: linear-gradient(180deg, transparent, rgba(59, 130, 246, 0.4)); }
        .title { width: 100%; height: 100%; transition: transform 0.5s ease; transform: translate3d(0%, 0px, 0px); display: flex; flex-direction: column; }
        .card:hover .title { transform: translate3d(0%, -30px, 100px); }

        .table-scroll-container { width: 100%; overflow-y: hidden; flex: 1; position: relative; }
        table { width: 100%; border-collapse: collapse; text-align: center; color: #ffffff; background: #13151c !important; position: relative; }
        th { font-size: 13px; color: #94a3b8; letter-spacing: 1px; padding-bottom: 12px; text-transform: uppercase; border-bottom: 2px solid rgba(255, 255, 255, 0.1); background: #13151c !important; }
        td { padding: 10px 5px; font-size: 15px; font-weight: 500; background: #13151c !important; }
        tr.player-row { border-bottom: 1px solid rgba(255, 255, 255, 0.05); background: #13151c !important; position: relative; top: 0; left: 0; }

        .rank { color: #94a3b8; font-weight: bold; }
        .gold { color: #facc15; text-shadow: 0 0 8px rgba(250, 204, 21, 0.3); }
        .orange { color: #f97316; }
        .chak-icon { display: inline-flex; align-items: center; justify-content: center; gap: 4px; color: #facc15; vertical-align: middle; }
        .chak-images-wrapper { display: inline-flex; gap: 2px; align-items: center; }
        .chak-img { width: 16px !important; height: 16px !important; display: inline-block !important; border-radius: 4px; }
        .footer-text { text-align: center; font-size: 12px; color: #576575; padding-top: 15px; background: #13151c !important; border-top: 1px solid rgba(255, 255, 255, 0.05); }
        .live-log { text-align: center; font-size: 13px; font-weight: bold; color: #3b82f6; margin-top: 8px; min-height: 18px; text-shadow: 0 0 8px rgba(59, 130, 246, 0.5); }
    </style>
</head>
<body>

<div class="card" id="tableCard">
    <div class="wrapper"></div>
    <div class="title">
        <div class="table-scroll-container" id="scrollBox">
            <table>
                <thead>
                    <tr>
                        <th style="width: 10%;">#</th>
                        <th style="width: 35%;">ИГРОК</th>
                        <th style="width: 15%;">ЧЕМПИОН</th>
                        <th style="width: 15%;">ФИНАЛИСТ</th>
                        <th style="width: 25%;">ЧАК-ЧАК</th>
                    </tr>
                </thead>
                <tbody id="tableBody"></tbody>
            </table>
        </div>
        <div class="footer-text">Победитель финала +2 чак-чака · Финалист +1 чак-чак</div>
        <div class="live-log" id="liveLog">Ожидание...</div>
    </div>
</div>

<script src="https://cloudflare.com"></script>
<script>
    const initialPlayers = [
        { id: 1, name: 'X-Slayer_99', champion: 1, finalist: 0, points: 2, isGold: true },
        { id: 2, name: 'Neon_Viper', champion: 1, finalist: 0, points: 2, isGold: false },
        { id: 3, name: 'Chak_Master', champion: 1, finalist: 0, points: 2, isGold: false },
        { id: 4, name: 'Cyber_Glitch', champion: 1, finalist: 0, points: 2, isGold: false },
        { id: 5, name: 'Zeus_Awper', champion: 1, finalist: 0, points: 2, isGold: false },
        { id: 6, name: 'Shadow_Step', champion: 1, finalist: 0, points: 2, isGold: false },
        { id: 7, name: 'Bullet_Rain', champion: 1, finalist: 0, points: 2, isGold: false }
    ];

    let currentPlayers = [];
    const tableBody = document.getElementById('tableBody');
    const scrollBox = document.getElementById('scrollBox');
    const liveLog = document.getElementById('liveLog');

    function renderTable() {
        tableBody.innerHTML = '';
        currentPlayers.forEach((player) => {
            const tr = document.createElement('tr');
            tr.className = 'player-row';
            tr.setAttribute('data-id', player.id);
            tr.innerHTML = `
                <td class="rank index-cell"></td>
                <td class="${player.isGold ? 'gold' : ''}">${player.name}</td>
                <td class="orange champ-cell">${player.champion}</td>
                <td class="orange finalist-cell">${player.finalist}</td>
                <td><span class="chak-icon"><span class="points-text">${player.points}</span><span class="chak-images-wrapper"></span></span></td>
            `;
            tableBody.appendChild(tr);
            updateChakImages(tr, player.points);
        });
        updateRankNumbers();
    }

    function updateChakImages(row, count) {
        const wrapper = row.querySelector('.chak-images-wrapper');
        wrapper.innerHTML = '';
        for(let i=0; i<count; i++) {
            wrapper.innerHTML += '<img src="https://moy.su" class="chak-img" alt="chak">';
        }
    }

    function updateRankNumbers() {
        const rows = Array.from(tableBody.querySelectorAll('tr.player-row'));
        rows.sort((a, b) => a.getBoundingClientRect().top - b.getBoundingClientRect().top);
        rows.forEach((row, idx) => { row.querySelector('.index-cell').innerText = idx + 1; });
    }

    function startTournamentCycle() {
        currentPlayers = JSON.parse(JSON.stringify(initialPlayers));
        renderTable();
        scrollBox.scrollTop = 0;
        liveLog.innerText = "Раунд начался. Расчёт матчей...";
        liveLog.style.color = "#94a3b8";

        setTimeout(() => { addPoints(2, 1, 0, 2, "Neon_Viper выиграл финал! +2 Чак-Чака 🔥"); }, 2000);
        setTimeout(() => { anime({ targets: scrollBox, scrollTop: 80, duration: 800, easing: 'easeInOutQuad' }); }, 4500);
        setTimeout(() => { addPoints(6, 0, 1, 1, "Shadow_Step пробился в финал! +1 Чак-Чак ⚡"); }, 6000);
        setTimeout(() => { anime({ targets: scrollBox, scrollTop: 0, duration: 600, easing: 'easeInOutQuad' }); }, 8000);

        setTimeout(startTournamentCycle, 10000);
    }

    function addPoints(id, addChamp, addFinal, addChak, msg) {
        const player = currentPlayers.find(p => p.id === id);
        if (!player) return;
        const row = tableBody.querySelector(`tr[data-id="${id}"]`);
        
        liveLog.innerText = msg;
        liveLog.style.color = "#3b82f6";

        const scoreObj = { c: player.champion, f: player.finalist, p: player.points };
        player.champion += addChamp; player.finalist += addFinal; player.points += addChak;

        anime({
            targets: scoreObj,
            c: player.champion, f: player.finalist, p: player.points,
            round: 1, duration: 600, easing: 'linear',
            update: () => {
                row.querySelector('.champ-cell').innerText = scoreObj.c;
                row.querySelector('.finalist-cell').innerText = scoreObj.f;
                row.querySelector('.points-text').innerText = scoreObj.p;
            },
            complete: () => {
                updateChakImages(row, player.points);
                sortRows();
            }
        });
    }

    function sortRows() {
        const rows = Array.from(tableBody.querySelectorAll('tr.player-row'));
        const before = rows.map(r => ({ el: r, top: r.getBoundingClientRect().top }));

        currentPlayers.sort((a, b) => b.points - a.points);
        currentPlayers.forEach(p => {
            const r = rows.find(row => parseInt(row.getAttribute('data-id')) === p.id);
            tableBody.appendChild(r);
        });

        before.forEach(item => {
            const diff = item.top - item.el.getBoundingClientRect().top;
            if (diff !== 0) {
                item.el.style.transform = `translateY(${diff}px)`;
                anime({ targets: item.el, translateY: 0, duration: 800, easing: 'easeOutQuad', update: () => updateRankNumbers() });
            }
        });
    }

    const card = document.getElementById('tableCard');
    window.addEventListener('mousemove', (e) => {
        card.style.transform = `rotateX(${-((e.clientY/window.innerHeight)-0.5)*30}deg) rotateY(${((e.clientX/window.innerWidth)-0.5)*30}deg)`;
    });
    card.addEventListener('mouseleave', () => { card.style.transform = `rotateX(0deg) rotateY(0deg)`; });

    startTournamentCycle();
</script>

</body>
</html>
