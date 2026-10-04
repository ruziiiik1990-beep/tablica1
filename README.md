<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица ЧакЧак — Чистая Анимация</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            overflow: hidden; 
            background: #13151c; 
            width: 100vw; 
            height: 100vh; 
            font-family: 'Segoe UI', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            perspective: 1000px; /* Включаем 3D-пространство для эффектов */
        }
        
        /* ИНТЕРАКТИВНЫЙ 3D-КОНТЕЙНЕР ТАБЛИЦЫ */
        .card {
            position: relative;
            width: 100%;
            max-width: 650px;
            /* ИСПРАВЛЕНО: Полностью прозрачный фон карточки вместо полупрозрачного rgba */
            background: transparent; 
            border: 1px solid rgba(59, 130, 246, 0.2);
            border-radius: 16px;
            padding: 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            backdrop-filter: blur(10px);
            transform-style: preserve-3d;
            transition: transform 0.5s ease, border-color 0.5s ease;
        }

        /* ОБЁРТКА ДЛЯ НЕОНОВЫХ ЭФФЕКТОВ */
        .wrapper {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border-radius: 16px;
            pointer-events: none;
            transform-style: preserve-3d;
        }

        /* Псевдоэлементы неонового свечения (Изначально скрыты) */
        .wrapper::before, .wrapper::after {
            content: "";
            position: absolute;
            left: 50%;
            transform: translateX(-50%) translateZ(-1px);
            width: 80%;
            background: linear-gradient(90deg, transparent, #3b82f6, transparent);
            filter: blur(20px);
            opacity: 0;
            transition: opacity 0.5s ease, height 0.5s ease;
        }

        .wrapper::before { top: -5px; height: 5px; }
        .wrapper::after { bottom: -5px; height: 5px; }

        /* СТРОГО ПО ТВОЕМУ CSS: Зажигаем неон при наведении */
        .card:hover .wrapper::before, 
        .card:hover .wrapper::after { 
            opacity: 1; 
        } 

        /* СТРОГО ПО ТВОЕМУ CSS: Вытягиваем нижний неон до 120px */
        .card:hover .wrapper::after { 
            height: 120px; 
            background: linear-gradient(180deg, transparent, rgba(59, 130, 246, 0.4));
        }

        .card:hover {
            border-color: rgba(59, 130, 246, 0.6);
        }

        /* СТРОГО ПО ТВОЕМУ CSS: Заголовок и контент с 3D-трансформацией */
        .title { 
            width: 100%; 
            transition: transform 0.5s ease;
            transform: translate3d(0%, 0px, 0px);
        }

        /* СТРОГО ПО ТВОЕМУ CSS: Выталкиваем контент вперед по оси Z и вверх */
        .card:hover .title { 
            transform: translate3d(0%, -50px, 100px); 
        }

        /* СТИЛИЗАЦИЯ ТУРНИРНОЙ ТАБЛИЦЫ */
        .main-heading {
            text-align: center;
            font-size: 22px;
            font-weight: bold;
            color: #3b82f6;
            letter-spacing: 2px;
            margin-bottom: 25px;
            text-shadow: 0 0 10px rgba(59, 130, 246, 0.3);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: center;
            color: #ffffff;
            /* ИСПРАВЛЕНО: Гарантируем прозрачность подложки самой таблицы */
            background: transparent; 
        }

        th {
            font-size: 13px;
            color: #94a3b8;
            letter-spacing: 1px;
            padding-bottom: 15px;
            text-transform: uppercase;
            border-bottom: 2px solid rgba(255, 255, 255, 0.1);
        }

        td {
            padding: 12px 5px;
            font-size: 15px;
            font-weight: 500;
            /* ИСПРАВЛЕНО: Установлен прозрачный фон для ячеек */
            background: transparent; 
        }

        tr {
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
            /* ИСПРАВЛЕНО: Установлен прозрачный фон для строк */
            background: transparent; 
        }

        /* Цвета игроков и очков из оригинального скриншота */
        .rank { color: #94a3b8; font-weight: bold; }
        .gold { color: #facc15; text-shadow: 0 0 8px rgba(250, 204, 21, 0.3); }
        .orange { color: #f97316; }
        
        .chak-icon {
            display: inline-flex;
            align-items: center;
            gap: 4px;
            color: #facc15;
        }
        .chak-icon::after {
            content: "🍪";
            font-size: 14px;
        }

        .footer-text {
            text-align: center;
            font-size: 12px;
            color: #576575;
            margin-top: 20px;
            letter-spacing: 0.5px;
        }
    </style>
</head>
<body>

<!-- Интерактивная 3D Карточка турнирной таблицы -->
<div class="card" id="tableCard">
    <div class="wrapper"></div>
    
    <!-- Весь контент таблицы, реагирующий на 3D Hover -->
    <div class="title">
        <div class="main-heading">ТУРНИРНАЯ ТАБЛИЦА</div>
        
        <table>
            <thead>
                <tr>
                    <th style="width: 10%;">#</th>
                    <th style="width: 30%;">ИГРОК</th>
                    <th style="width: 20%;">ЧЕМПИОН</th>
                    <th style="width: 20%;">ФИНАЛИСТ</th>
                    <th style="width: 20%;">ЧАК-ЧАК</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td class="rank">1</td>
                    <td class="gold">45</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
                <tr>
                    <td class="rank">2</td>
                    <td>123</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
                <tr>
                    <td class="rank">3</td>
                    <td class="orange">qweqwe</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
                <tr>
                    <td class="rank">4</td>
                    <td>hhh</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
                <tr>
                    <td class="rank">5</td>
                    <td>asdxzc3</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
                <tr>
                    <td class="rank">6</td>
                    <td>chesalova2013</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
                <tr>
                    <td class="rank">7</td>
                    <td>fg</td>
                    <td class="orange">1</td>
                    <td class="orange">0</td>
                    <td><span class="chak-icon">2 </span></td>
                </tr>
            </tbody>
        </table>
        
        <div class="footer-text">Победитель финала +2 чак-чака · Финалист +1 чак-чак</div>
    </div>
</div>

<script>
    const card = document.getElementById('tableCard');

    // Скрипт отслеживания мыши для динамического наклона всей карточки в 3D
    window.addEventListener('mousemove', (e) => {
        const x = (e.clientX / window.innerWidth) - 0.5;
        const y = (e.clientY / window.innerHeight) - 0.5;
        
        // Поворачиваем таблицу по осям X и Y в зависимости от положения курсора
        card.style.transform = `rotateX(${-y * 30}deg) rotateY(${x * 30}deg)`;
    });

    // Возвращаем таблицу в исходное ровное положение, когда мышь уходит с экрана
    card.addEventListener('mouseleave', () => {
        card.style.transform = `rotateX(0deg) rotateY(0deg)`;
    });
</script>

</body>
</html>
