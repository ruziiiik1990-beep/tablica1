
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица Чак-Чак</title>
    <!-- Anime.js для перемещения игроков по рейтингу -->
    <script src="https://cloudflare.com"></script>
    <style>
        body {
            background-color: #374151; /* Твой темно-серый фон */
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }

        .table-container {
            width: 100%;
            max-width: 650px;
            background: #465a8a; /* Сине-голубой цвет подложки со скриншота */
            border-radius: 16px;
            padding: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.4);
            box-sizing: border-box;
        }

        .table-title {
            text-align: center;
            font-size: 20px;
            font-weight: bold;
            color: #3b82f6; /* Ярко-голубой заголовок */
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 20px;
        }

        /* Шапка таблицы */
        .table-header {
            display: grid;
            grid-template-columns: 0.6fr 2fr 1.2fr 1.2fr 1.2fr;
            text-align: center;
            font-weight: bold;
            font-size: 13px;
            text-transform: uppercase;
            color: #ffffff;
            padding: 12px 10px;
            border: 1px solid rgba(255, 255, 255, 0.3);
            border-bottom: 2px solid rgba(255, 255, 255, 0.4);
            background: rgba(255, 255, 255, 0.05);
            border-top-left-radius: 8px;
            border-top-right-radius: 8px;
        }

        /* Контейнер для строк списка (position relative нужен для translateY) */
        .leaderboard-list {
            position: relative;
            height: 315px; /* Высота под 7 строк (7 * 45px) */
            margin: 0;
            padding: 0;
            list-style: none;
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid rgba(255, 255, 255, 0.3);
            border-top: none;
            border-bottom-left-radius: 8px;
            border-bottom-right-radius: 8px;
            overflow: hidden;
        }

        /* Строка игрока */
        .player-row {
            position: absolute;
            left: 0;
            width: 100%;
            height: 45px;
            display: grid;
            grid-template-columns: 0.6fr 2fr 1.2fr 1.2fr 1.2fr;
            align-items: center;
            text-align: center;
            box-sizing: border-box;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
            font-size: 14px;
        }

        .player-row:last-child {
            border-bottom: none;
        }

        /* Цвета текста из оригинальной таблицы */
        .col-rank {
            color: #9ca3af;
            font-weight: bold;
        }

        .col-name {
            font-weight: bold;
            color: #ffffff;
        }

        /* Особые цвета для никнеймов из скриншота */
        .player-row[data-name="45"] .col-name { color: #f59e0b; }
        .player-row[data-name="qweqwe"] .col-name { color: #ed8936; }

        .col-champ { color: #ed8936; font-weight: bold; }
        .col-finalist { color: #ed8936; font-weight: bold; }

        /* Столбец Чак-Чак с круглой иконкой */
        .col-chak {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
            color: #eab308; /* Желтая цифра */
            font-weight: bold;
            font-size: 16px;
        }

        .chak-icon {
            width: 20px;
            height: 20px;
            object-fit: contain;
            border-radius: 50%;
            background: #ffffff; /* Белая подложка под круглую иконку */
            padding: 1px;
        }

        /* Сноска снизу */
        .table-footer-text {
            text-align: center;
            font-size: 11px;
            color: #9ca3af;
            margin-top: 15px;
            margin-bottom: 20px;
        }

        /* Админ-панель */
        .admin-panel {
            display: flex;
            justify-content: center;
            gap: 15px;
            align-items: center;
        }

        .admin-input {
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.3);
            border-radius: 20px;
            padding: 10px 20px;
            color: #ffffff;
            outline: none;
            width: 160px;
            font-size: 13px;
        }

        .admin-input::placeholder {
            color: #9ca3af;
        }

        .admin-btn {
            background: #1e3a8a;
            border: 1px solid #2563eb;
            color: #ffffff;
            border-radius: 20px;
            padding: 10px 24px;
            font-weight: bold;
            cursor: pointer;
            font-size: 13px;
            transition: background 0.2s;
        }

        .admin-btn:hover {
            background: #1d4ed8;
        }
    </style>
</head>
<body>

<div class="table-container">
    <div class="table-title">Турнирная Таблица</div>
    
    <div class="table-header">
        <div>#</div>
        <div>Игрок</div>
        <div>Чемпион</div>
        <div>Финалист</div>
        <div>Чак-Чак</div>
    </div>

    <ul class="leaderboard-list" id="leaderboard">
        <!-- Строки игроков генерируются здесь -->
    </ul>

    <div class="table-footer-text">
        Победитель финала +2 чак-чака • Финалист +1 чак-чака
    </div>

    <!-- Твоя админ-панель снизу -->
    <div class="admin-panel">
        <input type="password" class="admin-input" placeholder="Админ-пароль">
        <button class="admin-btn">Войти как админ</button>
    </div>
</div>

<script>
    // Исходный массив игроков со скриншота
    let players = [
        { id: 1, name: "45", champ: 1, finalist: 0, chak: 2 },
        { id: 2, name: "123", champ: 1, finalist: 0, chak: 2 },
        { id: 3, name: "qweqwe", champ: 1, finalist: 0, chak: 2 },
        { id: 4, name: "hhh", champ: 1, finalist: 0, chak: 2 },
        { id: 5, name: "asdxzc3", champ: 1, finalist: 0, chak: 2 },
        { id: 6, name: "chesalova20013", champ: 1, finalist: 0, chak: 2 },
        { id: 7, name: "fg", champ: 1, finalist: 0, chak: 2 }
    ];

    const ROW_HEIGHT = 45; // Высота ячейки в пикселях
    const logoUrl = "https://4ak4ak.moy.su/logo1.jpg"; // Твоя круглая картинка

    // Генерация таблицы в DOM
    function initLeaderboard() {
        const list = document.getElementById('leaderboard');
        list.innerHTML = '';
        
        // Сортировка по очкам Чак-Чака
        players.sort((a, b) => b.chak - a.chak);

        players.forEach((player, index) => {
            const li = document.createElement('li');
            li.className = 'player-row';
            li.setAttribute('data-id', player.id);
            li.setAttribute('data-name', player.name);
            li.style.transform = `translateY(${index * ROW_HEIGHT}px)`;

            li.innerHTML = `
                <div class="col-rank rank-num">${index + 1}</div>
                <div class="col-name">${player.name}</div>
                <div class="col-champ">${player.champ}</div>
                <div class="col-finalist">${player.finalist}</div>
                <div class="col-chak" id="chak-${player.id}">
                    <span class="chak-value">${player.chak}</span>
                    <img src="${logoUrl}" class="chak-icon" alt="🔥">
                </div>
            `;
            list.appendChild(li);
        });
    }

    // Функция перемещения игроков при изменении очков
    function updatePositions() {
        const sorted = [...players].sort((a, b) => b.chak - a.chak);

        sorted.forEach((player, newIndex) => {
            const row = document.querySelector(`.player-row[data-id="${player.id}"]`);
            if (row) {
                // Обновляем номер места в левой колонке
                row.querySelector('.rank-num').innerText = newIndex + 1;

                // Анимируем сдвиг строки на её новую позицию по вертикали
                anime({
                    targets: row,
                    translateY: newIndex * ROW_HEIGHT,
                    duration: 600,
                    easing: 'easeInOutQuad'
                });
            }
        });
    }

    // Симуляция: раз в 4 секунды случайный игрок получает +1 или +2 чак-чака
    function startSimulation() {
        setInterval(() => {
            const randomPlayerIndex = Math.floor(Math.random() * players.length);
            const addedChak = Math.random() > 0.5 ? 2 : 1; // +1 или +2 чак-чака согласно правилу финала
            
            players[randomPlayerIndex].chak += addedChak;

            const player = players[randomPlayerIndex];
            const chakElement = document.getElementById(`chak-${player.id}`);
            
            if (chakElement) {
                // Обновляем число очков
                chakElement.querySelector('.chak-value').innerText = player.chak;
                
                // Эффект увеличения очков при изменении
                anime({
                    targets: chakElement,
                    scale: [1, 1.3, 1],
                    duration: 400,
                    easing: 'easeOutSine'
                });
            }

            // Перестраиваем сетку
            updatePositions();
        }, 4000);
    }

    document.addEventListener("DOMContentLoaded", () => {
        initLeaderboard();
        startSimulation();
    });
</script>

</body>
</html>
