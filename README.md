<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Таблица Рейтинга</title>
    <!-- Библиотека Anime.js для перемещения строк -->
    <script src="https://cloudflare.com"></script>
    <style>
        /* Полное уничтожение любых линий, разделителей и фонового мусора вне таблицы */
        hr, border, .separator, ::before, ::after {
            display: none !important;
            border: none !important;
            height: 0 !important;
            opacity: 0 !important;
        }

        body { 
            background-color: #374151; 
            font-family: 'Segoe UI', Roboto, Helvetica, sans-serif; 
            color: #fff; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            min-height: 100vh; 
            margin: 0; 
            perspective: 1000px; 
        }

        /* Контейнер-обертка для 3D наклона */
        .table-wrapper-3d { 
            width: 100%; 
            max-width: 650px; 
            transition: transform 0.1s ease-out; 
            transform-style: preserve-3d; 
            cursor: pointer;
            border-top: none !important;
        }

        .table-container { 
            width: 100%; 
            background: #465a8a; 
            border-radius: 16px; 
            padding: 20px; 
            box-shadow: 0 15px 35px rgba(0,0,0,0.4); 
            box-sizing: border-box; 
        }

        /* Шапка таблицы */
        .table-header { 
            display: grid; 
            grid-template-columns: 0.6fr 2fr 1.2fr 1.2fr 1.2fr; 
            text-align: center; 
            font-weight: bold; 
            font-size: 13px; 
            text-transform: uppercase; 
            padding: 12px 10px; 
            border: 1px solid rgba(255,255,255,0.3); 
            border-bottom: 2px solid rgba(255,255,255,0.4); 
            background: rgba(255,255,255,0.05); 
            border-top-left-radius: 8px; 
            border-top-right-radius: 8px; 
            transform: translateZ(30px); 
        }

        .leaderboard-list { 
            position: relative; 
            height: 315px; 
            margin: 0; 
            padding: 0; 
            list-style: none; 
            background: rgba(255,255,255,0.02); 
            border: 1px solid rgba(255,255,255,0.3); 
            border-top: none; 
            border-bottom-left-radius: 8px; 
            border-bottom-right-radius: 8px; 
            overflow: hidden; 
            transform: translateZ(20px); 
        }

        /* Строка игрока */
        .player-row { 
            position: absolute; 
            left: 0; 
            top: 0; 
            width: 100%; 
            height: 45px; 
            display: grid; 
            grid-template-columns: 0.6fr 2fr 1.2fr 1.2fr 1.2fr; 
            align-items: center; 
            text-align: center; 
            box-sizing: border-box; 
            border-bottom: 1px solid rgba(255,255,255,0.2); 
            font-size: 14px; 
            background: #465a8a; 
            will-change: transform; 
            transform-style: preserve-3d; 
        }

        .col-rank { color: #9ca3af; font-weight: bold; transform: translateZ(15px); transition: color 0.3s; }
        .col-name { font-weight: bold; transform: translateZ(25px); }
        
        /* Стили для топ-3 мест */
        .rank-gold { color: #ffd700 !important; text-shadow: 0 0 8px rgba(255, 215, 0, 0.4); }
        .rank-silver { color: #c0c0c0 !important; text-shadow: 0 0 8px rgba(192, 192, 192, 0.4); }
        .rank-bronze { color: #cd7f32 !important; text-shadow: 0 0 8px rgba(205, 127, 50, 0.4); }

        /* Цвета ников */
        .player-row[data-name="45"] .col-name { color: #f59e0b; }
        .player-row[data-name="qweqwe"] .col-name { color: #ed8936; }
        
        .col-champ, .col-finalist { color: #ed8936; font-weight: bold; transform: translateZ(15px); }
        
        /* Чак-чак с белой подложкой */
        .col-chak { display: flex; align-items: center; justify-content: center; gap: 6px; color: #eab308; font-weight: bold; font-size: 16px; transform: translateZ(35px); }
        .chak-icon { width: 20px; height: 20px; object-fit: contain; border-radius: 50%; background: #ffffff; padding: 1px; }
        
        .table-footer-text { text-align: center; font-size: 11px; color: #9ca3af; margin-top: 15px; transform: translateZ(10px); }
    </style>
</head>
<body>

<div class="table-wrapper-3d" id="card3d">
    <div class="table-container">
        <div class="table-header">
            <div>#</div><div>Игрок</div><div>Чемпион</div><div>Финалист</div><div>Чак-Чак</div>
        </div>
        <ul class="leaderboard-list" id="leaderboard"></ul>
        <div class="table-footer-text">Победитель финала +2 чак-чака • Финалист +1 чак-чака</div>
    </div>
</div>

<script>
    const namesPool = ["Безумный Макс", "Ночной Призрак", "Стальной Лис", "Гроза Арены", "Тайный Нео", "Cyber_Chak", "45", "qweqwe", "123", "hhh", "fg"];
    
    function generatePlayers() {
        const shuffled = [...namesPool].sort(() => 0.5 - Math.random());
        return Array.from({length: 7}, (_, i) => ({ id: i + 1, name: shuffled[i], champ: 0, finalist: 0, chak: 0 }));
    }

    let players = generatePlayers();
    const ROW_HEIGHT = 45;
    const logoUrl = "https://moy.su";

    // Функция авто-удаления текстового мусора и внешних полос хостинга
    function cleanExternalLayout() {
        const walker = document.createTreeWalker(document.body, NodeFilter.SHOW_TEXT, null, false);
        let node;
        while (node = walker.nextNode()) {
            const val = node.nodeValue.toLowerCase();
            if (val.includes('tablica1') || val.includes('doctype')) {
                const parent = node.parentElement;
                if (parent && !parent.closest('.table-container') && parent !== document.body) {
                    parent.style.display = 'none';
                }
            }
        }
    }

    function initLeaderboard() {
        const list = document.getElementById('leaderboard');
        list.innerHTML = '';
        players.forEach((p, i) => {
            const li = document.createElement('li');
            li.className = 'player-row';
            li.setAttribute('data-id', p.id);
            li.setAttribute('data-name', p.name);
            li.style.transform = `translateY(${i * ROW_HEIGHT}px)`;
            li.innerHTML = `<div class="col-rank rank-num">${i + 1}</div><div class="col-name">${p.name}</div><div class="col-champ" id="champ-${p.id}">${p.champ}</div><div class="col-finalist" id="finalist-${p.id}">${p.finalist}</div><div class="col-chak" id="chak-${p.id}"><span class="chak-value">${p.chak}</span><img src="${logoUrl}" class="chak-icon" alt="*"></div>`;
            list.appendChild(li);
        });
        applyRankColors();
    }

    function applyRankColors() {
        const rows = document.querySelectorAll('.player-row');
        rows.forEach(row => {
            const rankBox = row.querySelector('.rank-num');
            const currentRank = parseInt(rankBox.innerText);
            
            rankBox.classList.remove('rank-gold', 'rank-silver', 'rank-bronze');
            
            if (currentRank === 1) rankBox.classList.add('rank-gold');
            else if (currentRank === 2) rankBox.classList.add('rank-silver');
            else if (currentRank === 3) rankBox.classList.add('rank-bronze');
        });
    }

    function updatePositions() {
        const sorted = [...players].sort((a, b) => b.chak - a.chak);
        sorted.forEach((p, newIndex) => {
            const row = document.querySelector(`.player-row[data-id="${p.id}"]`);
            if (row) {
                row.querySelector('.rank-num').innerText = newIndex + 1;
                anime({ 
                    targets: row, 
                    translateY: newIndex * ROW_HEIGHT, 
                    duration: 800, 
                    easing: 'easeInOutCubic',
                    complete: function() {
                        applyRankColors();
                    }
                });
            }
        });
    }

    function startSimulation() {
        setInterval(() => {
            const rIdx = Math.floor(Math.random() * players.length);
            const isWin = Math.random() > 0.5;
            const p = players[rIdx];

            if (isWin) { p.chak += 2; p.champ += 1; document.getElementById(`champ-${p.id}`).innerText = p.champ; }
            else { p.chak += 1; p.finalist += 1; document.getElementById(`finalist-${p.id}`).innerText = p.finalist; }

            const chakEl = document.getElementById(`chak-${p.id}`);
            if (chakEl) {
                chakEl.querySelector('.chak-value').innerText = p.chak;
                anime({ targets: chakEl, scale: [1, 1.25, 1], duration: 300, easing: 'easeOutSine' });
            }
            updatePositions();
        }, 3500);
    }

    const card = document.getElementById('card3d');
    card.addEventListener('mousemove', (e) => {
        const r = card.getBoundingClientRect();
        const rotateX = ((r.height / 2) - (e.clientY - r.top)) / 10;
        const rotateY = ((e.clientX - r.left) - (r.width / 2)) / 15;
        card.style.transform = `rotateX(${rotateX}deg) rotateY(${rotateY}deg)`;
    });
    card.addEventListener('mouseleave', () => card.style.transform = 'rotateX(0deg) rotateY(0deg)');

    document.addEventListener("DOMContentLoaded", () => { initLeaderboard(); cleanExternalLayout(); startSimulation(); });
</script>
</body>
</html>
