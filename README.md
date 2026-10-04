<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица ЧакЧак — Neon Anime.js</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            background: #1e222b;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: #ffffff;
            padding: 20px;
        }

        /* Контейнер таблицы в стиле интерфейса игровых лаунчеров */
        .table-container {
            background: rgba(43, 51, 69, 0.7);
            border: 2px solid #3b82f6;
            border-radius: 16px;
            padding: 24px;
            width: 100%;
            max-width: 700px;
            box-shadow: 0 0 20px rgba(59, 130, 246, 0.3), inset 0 0 15px rgba(59, 130, 246, 0.1);
            backdrop-filter: blur(10px);
        }

        .table-title {
            text-align: center;
            font-size: 24px;
            font-weight: bold;
            color: #3b82f6;
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-bottom: 20px;
            text-shadow: 0 0 10px rgba(59, 130, 246, 0.6);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: center;
        }

        th {
            font-size: 14px;
            color: #94a3b8;
            text-transform: uppercase;
            padding: 12px 8px;
            border-bottom: 2px solid rgba(255, 255, 255, 0.1);
        }

        /* Строки изначально скрыты для анимации Anime.js */
        tr.data-row {
            opacity: 0;
            transform: scale(0.7) translateY(30px);
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
            transition: background 0.3s;
        }

        tr.data-row:hover {
            background: rgba(59, 130, 246, 0.15);
            box-shadow: 0 0 10px rgba(59, 130, 246, 0.2);
        }

        td {
            padding: 14px 8px;
            font-size: 16px;
            font-weight: 500;
        }

        /* Выделение цветов из оригинального дизайна */
        .rank-col { color: #94a3b8; font-weight: bold; }
        .player-col { color: #ffffff; }
        .highlight-gold { color: #facc15; text-shadow: 0 0 8px rgba(250, 204, 21, 0.4); }
        .highlight-orange { color: #f97316; text-shadow: 0 0 8px rgba(249, 115, 22, 0.4); }

        /* Иконка Чак-Чак (заменяет оригинальный эмодзи/картинку) */
        .chak-icon {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            color: #facc15;
        }
        .chak-icon::after {
            content: "🍪"; /* Замени на тег <img src="путь">, если есть картинка чак-чака */
            font-size: 16px;
        }

        .footer-note {
            text-align: center;
            font-size: 13px;
            color: #64748b;
            margin-top: 16px;
        }
    </style>
</head>
<body>

<div class="table-container">
    <div class="table-title">Турнирная Таблица</div>
    
    <table>
        <thead>
            <tr>
                <th style="width: 10%;">#</th>
                <th style="width: 30%;">Игрок</th>
                <th style="width: 20%;">Чемпион</th>
                <th style="width: 20%;">Финалист</th>
                <th style="width: 20%;">Чак-Чак</th>
            </tr>
        </thead>
        <tbody>
            <!-- Строки таблицы с данными из твоего скриншота -->
            <tr class="data-row">
                <td class="rank-col">1</td>
                <td class="player-col highlight-gold">45</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
            <tr class="data-row">
                <td class="rank-col">2</td>
                <td class="player-col">123</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
            <tr class="data-row">
                <td class="rank-col">3</td>
                <td class="player-col highlight-orange">qweqwe</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
            <tr class="data-row">
                <td class="rank-col">4</td>
                <td class="player-col">hhh</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
            <tr class="data-row">
                <td class="rank-col">5</td>
                <td class="player-col">asdxzc3</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
            <tr class="data-row">
                <td class="rank-col">6</td>
                <td class="player-col">chesalova2013</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
            <tr class="data-row">
                <td class="rank-col">7</td>
                <td class="player-col">fg</td>
                <td class="highlight-orange">1</td>
                <td class="highlight-orange">0</td>
                <td><span class="chak-icon">2 </span></td>
            </tr>
        </tbody>
    </table>
    
    <div class="footer-note">Победитель финала +2 чак-чака · Финалист +1 чак-чак</div>
</div>

<!-- Подключаем стабильную Anime.js из CDN -->
<script src="https://cloudflare.com"></script>
<script>
    document.addEventListener('DOMContentLoaded', () => {
        // Каскадная неоновая анимация появления строк (эффект раскрытия цветка)
        anime({
            targets: 'tr.data-row',
            opacity:,
            scale: [0.7, 1],
            translateY:,
            delay: anime.stagger(120, {start: 300}), // Строки выходят по очереди с задержкой
            duration: 1000,
            easing: 'easeOutElastic(1, .8)' // Эластичный пружинящий эффект при остановке
        });

        // Плавное неоновое «дыхание» для заголовка таблицы
        anime({
            targets: '.table-title',
            textShadow: [
                '0 0 5px rgba(59, 130, 246, 0.4)',
                '0 0 20px rgba(59, 130, 246, 0.9)',
                '0 0 5px rgba(59, 130, 246, 0.4)'
            ],
            duration: 3000,
            loop: true,
            easing: 'linear'
        });
    });
</script>

</body>
</html>
