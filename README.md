<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица</title>
    <!-- Подключаем библиотеку Anime.js -->
    <script src="https://cloudflare.com"></script>
    <style>
        body {
            background-color: #374151; /* Твой оригинальный темно-серый фон */
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
            background: #465a8a; /* Сине-голубой цвет карточки со скриншота */
            border-radius: 16px;
            padding: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.4);
            box-sizing: border-box;
        }

        /* Шапка таблицы — самый верхний элемент, заголовок удален */
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

        /* Контейнер списка обязательно position: relative и фиксированной высоты для работы absolute + translateY */
        .leaderboard-list {
            position: relative;
            height: 315px; /* Высота строго под 7 строк (7 игроков * 45px) */
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

        /* Строка игрока position: absolute, чтобы Anime.js мог двигать её по высоте */
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
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
            font-size: 14px;
            background: #465a8a; /* Чтобы при перекрытии строки не просвечивали */
            will-change: transform;
        }

        .col-rank {
            color: #9ca3af;
            font-weight: bold;
        }

        .col-name {
            font-weight: bold;
            color: #ffffff;
        }

        /* Подсветка ников из твоего интерфейса */
        .player-row[data-name="45"] .col-name { color: #f59e0b; }
        .player-row[data-name="qweqwe"] .col-name { color: #ed8936; }

        .col-champ { color: #ed8936; font-weight: bold; }
        .col-finalist { color: #ed8936; font-weight: bold; }

        /* Иконка чак-чака с белой круглой подложкой */
        .col-chak {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
            color: #eab308;
            font-weight: bold;
            font-size: 16px;
        }

        .chak-icon {
            width: 20px;
            height: 20px;
            object-fit: contain;
            border-radius: 50%;
            background: #ffffff;
            padding: 1px;
        }

        .table-footer-text {
            text-align: center;
            font-size: 11px;
            color: #9ca3af;
            margin-top: 15px;
            margin-bottom: 5px;
        }
    </style>
</head>
<body>

<div class="table-container">
    <div class="table-header">
        <div>#</div>
        <div>Игрок</div>
        <div>Чемпион</div>
        <div>Финалист</div>
        <div>Чак-Чак</div>
    </div>

    <ul class="leaderboard-list" id="leaderboard"></ul>

    <div class="table-footer-text">
        Победитель финала +2 чак-чака • Финалист +1 чак-чака
    </div>
</div>

<script>
    // Все игроки начинают строго с 0 очков во всех колонках
    let players = [
        { id: 1, name: "45", champ: 0, finalist: 0, chak: 0 },
        { id: 2, name: "123", champ: 0, finalist: 0, chak: 0 },
        { id: 3, name: "qweqwe", champ: 0, finalist: 0, chak: 0 },
        { id: 4, name: "hhh", champ: 0, finalist: 0, chak: 0 },
        { id: 5, name: "asdxzc3", champ: 0, finalist: 0, chak: 0 },
        { id: 6, name: "chesalova20013", champ: 0, finalist: 0, chak: 0 },
        { id: 7, name: "fg", champ: 0, finalist: 0, chak: 0 }
    ];

    const ROW_HEIGHT = 45; 
    const logoUrl = "https://4ak4ak.moy.su/logo1.jpg";

    // Функция авто-удаления текста tablica1, если он лезет из GitHub
    function removeExternalLabels() {
        const walker = document.createTreeWalker(document.body, NodeFilter.SHOW_TEXT, null, false);
        let node;
        while (node = walker.nextNode()) {
            if (node.nodeValue.toLowerCase().includes('tablica1')) {
                const parent = node.parentElement;
                if (parent && parent !== document.body && !parent.closest('.table-container')) {
                    parent.style.display = 'none';
                }
            }
        }
    }

    // Первичный вывод таблицы в DOM
    function initLeaderboard() {
        const list = document.getElementById('leaderboard');
        list.innerHTML = '';

        players.forEach((player, index) => {
            const li = document.createElement('li');
            li.className = 'player-row';
            li.setAttribute('data-id', player.id);
            li.setAttribute('data-name', player.name);
            // Устанавливаем начальное положение строк друг за другом по вертикали
            li.style.transform = `translateY(${index * ROW_HEIGHT}px)`;

            li.innerHTML = `
                <div class="col-rank rank-num">${index + 1}</div>
                <div class="col-name">${player.name}</div>
                <div class="col-champ" id="champ-${player.id}">${player.champ}</div>
                <div class="col-finalist" id="finalist-${player.id}">${player.finalist}</div>
                <div class="col-chak" id="chak-${player.id}">
                    <span class="chak-value">${player.chak}</span>
                    <img src="${logoUrl}" class="chak-icon" alt="чак-чак">
                </div>
            `;
            list.appendChild(li);
        });
    }

    // АНИМАЦИЯ ПЕРЕМЕЩЕНИЯ СТРОК ЧЕРЕЗ ANIME.JS
    function updatePositions() {
        // Создаем отсортированную копию массива на основе очков Чак-Чак
        const sorted = [...players].sort((a, b) => b.chak - a.chak);

        sorted.forEach((player, newIndex) => {
            const row = document.querySelector(`.player-row[data-id="${player.id}"]`);
            if (row) {
                // Плавно обновляем текст порядкового номера в левой колонке
                row.querySelector('.rank-num').innerText = newIndex + 1;

                // Запускаем физическое перемещение всей строки с очками на новые координаты Y
                anime({
                    targets: row,
                    translateY: newIndex * ROW_HEIGHT,
                    duration: 800,
                    easing: 'easeInOutCubic' // Плавное замедление в начале и конце движения
                });
            }
        });
    }

    // Симуляция: начисление очков раз в 3.5 секунды
    function startSimulation() {
        setInterval(() => {
            const randomPlayerIndex = Math.floor(Math.random() * players.length);
            const isWin = Math.random() > 0.5; 
            const player = players[randomPlayerIndex];

            if (isWin) {
                // Победитель финала: +2 чак-чака, +1 чемпион
                player.chak += 2;
                player.champ += 1;
                document.getElementById(`champ-${player.id}`).innerText = player.champ;
            } else {
                // Проигрыш в финале: +1 чак-чак, +1 финалист
                player.chak += 1;
                player.finalist += 1;
                document.getElementById(`finalist-${player.id}`).innerText = player.finalist;
            }

            // Обновляем число чак-чаков на экране
            const chakElement = document.getElementById(`chak-${player.id}`);
            if (chakElement) {
                chakElement.querySelector('.chak-value').innerText = player.chak;
                
                // Легкая пульсация блока очков в момент изменения
                anime({
                    targets: chakElement,
                    scale: [1, 1.25, 1],
                    duration: 300,
                    easing: 'easeOutSine'
                });
            }

            // Запускаем перемещение всей строки на новую позицию
            updatePositions();
        }, 3500);
    }

    document.addEventListener("DOMContentLoaded", () => {
        initLeaderboard();
        removeExternalLabels();
        startSimulation();
    });
</script>

</body
</html>
