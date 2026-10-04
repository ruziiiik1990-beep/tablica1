<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Анимированная Таблица Лидеров</title>
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
            position: relative;
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
            background-color: transparent; /* Полностью прозрачный фон ячеек из прошлого запроса */
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
            gap: 8px; /* Отступ между номером и картинкой */
            font-weight: bold;
            font-size: 16px;
            min-width: 65px;
        }

        /* Настройки вашей единственной картинки у цифр */
        .rank-logo {
            width: 20px;
            height: 20px;
            object-fit: contain;
            display: inline-block;
        }

        .player-name {
            font-size: 16px;
            font-weight: 500;
        }

        .player-points {
            font-size: 16px;
            font-weight: bold;
            color: #10b981;
            padding: 4px 10px;
            min-width: 40px;
            text-align: right;
        }

        /* Всплывающие уведомления из вашей прошлой версии */
        .notification {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: #10b981;
            color: white;
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 14px;
            font-weight: bold;
            pointer-events: none;
            opacity: 0;
            z-index: 10;
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
    <div class="notification" id="notification">Рейтинг обновлен!</div>
</div>

<script>
    // Исходный массив игроков (без админки)
    let players = [
        { id: 1, name: "Безумный Макс", points: 150 },
        { id: 2, name: "Ночной Призрак", points: 120 },
        { id: 3, name: "Стальной Лис", points: 95 },
        { id: 4, name: "Гроза Арены", points: 70 },
        { id: 5, name: "Тайный Нео", points: 45 }
    ];

    const ROW_HEIGHT = 56; 
    const logoUrl = "https://4ak4ak.moy.su/logo1.jpg"; // Ваша ссылка на логотип

    // Первая отрисовка таблицы
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
                    <span class="player-name">${player.name}</span>
                </div>
                <div class="player-points" id="points-${player.id}">${player.points}</div>
            `;
            list.appendChild(li);
        });
    }

    // Плавное перемещение строк вверх/вниз при изменении позиций
    function updatePositions() {
        const sorted = [...players].sort((a, b) => b.points - a.points);
        let wasPositionChanged = false;

        sorted.forEach((player, newIndex) => {
            const row = document.querySelector(`.player-row[data-id="${player.id}"]`);
            if (row) {
                const currentRankText = row.querySelector('.rank-num').innerText;
                const expectedRankText = `#${newIndex + 1}`;

                if (currentRankText !== expectedRankText) {
                    row.querySelector('.rank-num').innerText = expectedRankText;
                    wasPositionChanged = true;
                }

                // Анимация передвижения строки на ее новое место по вертикали
                anime({
                    targets: row,
                    translateY: newIndex * ROW_HEIGHT,
                    duration: 600,
                    easing: 'easeInOutQuad'
                });
            }
        });

        // Если кто-то кого-то обогнал — показываем всплывающее уведомление
        if (wasPositionChanged) {
            showNotification();
        }
    }

    function showNotification() {
        anime({
            targets: '#notification',
            opacity:,
            translateY: [20, 0, -20],
            duration: 1500,
            easing: 'easeOutExpo'
        });
    }

    // Бесконечный цикл автоматического начисления очков (каждые 3 секунды)
    function startSimulation() {
        setInterval(() => {
            const randomPlayerIndex = Math.floor(Math.random() * players.length);
            const addedPoints = Math.floor(Math.random() * 20) + 10;
            
            players[randomPlayerIndex].points += addedPoints;

            const player = players[randomPlayerIndex];
            const pointsElement = document.getElementById(`points-${player.id}`);
            
            if (pointsElement) {
                pointsElement.innerText = player.points;
                
                // Пульсация цифр очков
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
