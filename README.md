<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Таблица Лидеров</title>
    <script src="https://cloudflare.com"></script>
    <style>
        body {
            background-color: #374151;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }

        .leaderboard-container {
            width: 100%;
            max-width: 500px;
            background: rgba(31, 41, 55, 0.7);
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(10px);
        }

        .table-header {
            display: flex;
            justify-content: space-between;
            padding: 10px 15px;
            font-weight: bold;
            text-transform: uppercase;
            font-size: 14px;
            letter-spacing: 1px;
            color: #9ca3af;
            border-bottom: 2px solid #4b5563;
            margin-bottom: 10px;
        }

        .leaderboard-list {
            position: relative;
            height: 280px; 
            margin: 0;
            padding: 0;
            list-style: none;
        }

        .player-row {
            position: absolute;
            left: 0;
            width: 100%;
            height: 46px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 5px 15px;
            background-color: transparent;
            border-radius: 8px;
            box-sizing: border-box;
        }

        .player-info {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .rank-box {
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: bold;
            font-size: 16px;
            min-width: 65px;
        }

        .rank-logo {
            width: 20px;
            height: 20px;
            object-fit: contain;
            display: inline-block;
        }

        .player-name-wrapper {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .player-name {
            font-size: 16px;
            font-weight: 500;
        }

        .badge {
            font-size: 11px;
            padding: 2px 8px;
            border-radius: 12px;
            font-weight: bold;
            text-transform: uppercase;
        }

        .badge-winner {
            background-color: #f59e0b;
            color: #1f2937;
        }

        .badge-finalist {
            background-color: #3b82f6;
            color: #ffffff;
        }

        .player-points {
            font-size: 16px;
            font-weight: bold;
            color: #10b981;
            padding: 4px 10px;
            min-width: 40px;
            text-align: right;
        }
    </style>
</head>
<body>

<div class="leaderboard-container">
    <div class="table-header">
        <span>Игрок</span>
        <span>Очки</span>
    </div>
    <ul class="leaderboard-list" id="leaderboard"></ul>
</div>

<script>
    let players = [
        { id: 1, name: "Безумный Макс", points: 150, role: "winner" },
        { id: 2, name: "Ночной Призрак", points: 120, role: "finalist" },
        { id: 3, name: "Стальной Лис", points: 95, role: "finalist" },
        { id: 4, name: "Гроза Арены", points: 70, role: "player" },
        { id: 5, name: "Тайный Нео", points: 45, role: "player" }
    ];

    const ROW_HEIGHT = 56; 
    const logoUrl = "https://4ak4ak.moy.su/logo1.jpg";

    function getRoleBadge(role) {
        if (role === "winner") return '<span class="badge badge-winner">Победитель</span>';
        if (role === "finalist") return '<span class="badge badge-finalist">Финалист</span>';
        return '';
    }

    function initLeaderboard() {
        const list = document.getElementById('leaderboard');
        list.innerHTML = '';
        
        players.sort((a, b) => b.points - a.points);

        players.forEach((player, index) => {
            const li = document.createElement('li');
            li.className = 'player-row';
            li.setAttribute('data-id', player.id);
            li.style.transform = `translateY(${index * ROW_HEIGHT}px)`;

            li.innerHTML = `
                <div class="player-info">
                    <div class="rank-box">
                        <span class="rank-num">#${index + 1}</span>
                        <img src="${logoUrl}" class="rank-logo" alt="logo">
                    </div>
                    <div class="player-name-wrapper">
                        <span class="player-name">${player.name}</span>
                        ${getRoleBadge(player.role)}
                    </div>
                </div>
                <div class="player-points" id="points-${player.id}">${player.points}</div>
            `;
            list.appendChild(li);
        });
    }

    function updatePositions() {
        const sorted = [...players].sort((a, b) => b.points - a.points);

        sorted.forEach((player, newIndex) => {
            const row = document.querySelector(`.player-row[data-id="${player.id}"]`);
            if (row) {
                row.querySelector('.rank-num').innerText = `#${newIndex + 1}`;

                anime({
                    targets: row,
                    translateY: newIndex * ROW_HEIGHT,
                    duration: 600,
                    easing: 'easeInOutQuad'
                });
            }
        });
    }

    function startSimulation() {
        setInterval(() => {
            const randomPlayerIndex = Math.floor(Math.random() * players.length);
            const addedPoints = Math.floor(Math.random() * 20) + 10;
            
            players[randomPlayerIndex].points += addedPoints;

            const player = players[randomPlayerIndex];
            const pointsElement = document.getElementById(`points-${player.id}`);
            
            if (pointsElement) {
                pointsElement.innerText = player.points;
                
                anime({
                    targets: pointsElement,
                    scale: [1, 1.2, 1],
                    duration: 300,
                    easing: 'easeOutSine'
                });
            }

            updatePositions();
        }, 3000);
    }

    document.addEventListener("DOMContentLoaded", () => {
        initLeaderboard();
        startSimulation();
    });
</script>

</body>
</html>
