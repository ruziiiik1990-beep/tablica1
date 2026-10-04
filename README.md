<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Анимированная Таблица Лидеров</title>
    <!-- Подключаем библиотеку Anime.js -->
    <script src="https://cloudflare.com"></script>
    <style>
        body {
            background-color: #374151; /* Темно-серый фон, который отлично работал */
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
            /* Высота рассчитывается из количества строк (5 игроков * 56px) */
            height: 280px; 
            margin: 0;
            padding: 0;
            list-style: none;
        }

        .player-row {
            position: absolute;
            left: 0;
            width: 100%;
            height: 46px; /* Высота строки */
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 5px 15px;
            background-color: #374151; /* Фон ячеек совпадает с общим фоном */
            border-radius: 8px;
            box-sizing: border-box;
            transition: background-color 0.3s;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .player-info {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .rank-box {
            display: flex;
            align-items: center;
            gap: 6px;
            font-weight: bold;
            font-size: 16px;
            min-width: 55px;
        }

        /* Стилизация вашей картинки-логотипа, чтобы она корректно отображалась и не пропадала */
        .rank-logo {
            width: 22px;
            height: 22px;
            object-fit: contain;
            display: inline-block;
            vertical-align: middle;
            border-radius: 4px;
        }

        .player-name {
            font-size: 16px;
            font-weight: 500;
        }

        .player-points {
            font-size: 16px;
            font-weight: bold;
            color: #10b981;
            background: rgba(16, 185, 129, 0.1);
            padding: 4px 10px;
            border-radius: 6px;
            min-width: 40px;
            text-align: center;
        }
    </style>
</head>
<body>

<div class="leaderboard-container">
    <div class="table-header">
        <span>Игрок</span>
        <span>Очки</span>
    </div>
    <ul class="leaderboard-list" id="leaderboard">
        <!-- Строки генерируются и управляются скриптом динамически -->
    </ul>
</div>

<script>
    // Массив игроков со случайными именами (без админ-панели, как вы просили)
    let players = [
        { id: 1, name: "Безумный Макс", points: 150 },
        { id: 2, name: "Ночной Призрак", points: 120 },
        { id: 3, name: "Стальной Лис", points: 95 },
        { id: 4, name: "Гроза Арены", points: 70 },
        { id: 5, name: "Тайный Нео", points: 45 }
    ];

    const ROW_HEIGHT = 56; // Высота строки + отступ
    const logoUrl = "https://4ak4ak.moy.su/logo1.jpg"; // Ваша картинка

    // Инициализация таблицы в DOM
    function initLeaderboard() {
        const list = document.getElementById('leaderboard');
        list.innerHTML = '';
        
        // Сортируем изначально по очкам
        players.sort((a, b) => b.points - a.points);

        players.forEach((player, index) => {
            const li = document.createElement('li');
            li.className = 'player-row';
            li.setAttribute('data-id', player.id);
            // Устанавливаем начальную позицию по вертикали
            li.style.transform = `translateY(${index * ROW_HEIGHT}px)`;

            li.innerHTML = `
                <div class="player-info">
                    <div class="rank-box">
                        <span class="rank-num">#${index + 1}</span>
                        <img src="${logoUrl}" class="rank-logo" alt="logo" onerror="this.style.display='none'; console.log('Ошибка загрузки логотипа');">
                    </div>
                    <span class="player-name">${player.name}</span>
                </div>
                <div class="player-points" id="points-${player.id}">${player.points}</div>
            `;
            list.appendChild(li);
        });
    }

    // Функция обновления позиций строк и их плавного перемещения вверх/вниз
    function updatePositions() {
        // Создаем копию массива и сортируем актуальный рейтинг
        const sorted = [...players].sort((a, b) => b.points - a.points);

        sorted.forEach((player, newIndex) => {
            const row = document.querySelector(`.player-row[data-id="${player.id}"]`);
            if (row) {
                // Плавное обновление номера позиции (#1, #2...) внутри строки
                row.querySelector('.rank-num').innerText = `#${newIndex + 1}`;

                // Анимация Anime.js для физического перемещения строки по высоте
                anime({
                    targets: row,
                    translateY: newIndex * ROW_HEIGHT,
                    duration: 600,
                    easing: 'easeInOutQuad'
                });
            }
        });
    }

    // Бесконечный цикл симуляции начисления очков
    function startSimulation() {
        setInterval(() => {
            // Выбираем случайного игрока из списка
            const randomPlayerIndex = Math.floor(Math.random() * players.length);
            const addedPoints = Math.floor(Math.random() * 25) + 10; // Добавляем от 10 до 35 очков
            
            players[randomPlayerIndex].points += addedPoints;

            const player = players[randomPlayerIndex];
            const pointsElement = document.getElementById(`points-${player.id}`);
            
            if (pointsElement) {
                pointsElement.innerText = player.points;
                
                // Легкая анимация вспышки/увеличения для цифр при получении очков
                anime({
                    targets: pointsElement,
                    scale: [1, 1.3, 1],
                    duration: 400,
                    easing: 'easeOutSine'
                });
            }

            // Пересчитываем места и двигаем игроков вверх/вниз
            updatePositions();

        }, 3000); // Очки начисляются каждые 3 секунды
    }

    // Запуск скрипта после загрузки страницы
    document.addEventListener("DOMContentLoaded", () => {
        initLeaderboard();
        startSimulation();
    });
</script>

</body>
</html>
