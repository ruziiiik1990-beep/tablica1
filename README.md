
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Финал - Слайдер с Эффектами</title>
    <!-- Подключаем Swiper CSS и Anime.js -->
    <link rel="stylesheet" href="https://jsdelivr.net" />
    <script src="https://cloudflare.com"></script>
    <style>
        body {
            background-color: #374151; /* Тот самый рабочий темно-серый фон */
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Roboto, Helvetica, sans-serif;
            color: #ffffff;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        .slider-section {
            width: 100%;
            max-width: 850px;
            height: 550px;
            position: relative;
        }

        .swiper {
            width: 100%;
            height: 100%;
            border-radius: 16px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
        }

        /* Общие стили для слайдов-карточек */
        .promo-card {
            position: relative;
            background: #2563eb; /* Базовый синий для первого слайда */
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            box-sizing: border-box;
        }

        .promo-card-content {
            text-align: center;
            padding: 40px;
            z-index: 5;
            max-width: 500px;
        }

        .promo-card-content h2 {
            font-size: 32px;
            margin-bottom: 15px;
        }

        /* --- ВТОРОЙ СЛАЙД: СЛОИ И IFRAME --- */
        .slide-iframe-wrapper {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: #374151;
        }

        /* iframe строго ЗА белой карточкой */
        .background-iframe {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border: none;
            z-index: 1; /* Самый нижний слой внутри слайда */
            pointer-events: none; /* Чтобы не мешал наведению на карточку */
        }

        /* Белая карточка ПОВЕРХ iframe */
        .white-card-overlay {
            position: relative;
            z-index: 2; /* Слой выше iframe */
            background: #ffffff;
            color: #1f2937;
            padding: 35px;
            border-radius: 12px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.3);
            text-align: center;
            max-width: 420px;
            transform-style: preserve-3d;
            perspective: 1000px;
        }

        /* Фиксированный логотип, чтобы не исчезал */
        .fixed-weapon-logo {
            width: 32px;
            height: 32px;
            object-fit: contain;
            display: inline-block;
            margin-bottom: 10px;
        }

        /* Навигация слайдера */
        .promo-slider-controls {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            align-items: center;
            gap: 20px;
            z-index: 10;
            background: rgba(0,0,0,0.5);
            padding: 8px 16px;
            border-radius: 20px;
        }

        .swiper-btn {
            background: none;
            border: none;
            color: white;
            cursor: pointer;
            font-size: 18px;
            display: flex;
            align-items: center;
        }

        .promo-slider-fraction {
            font-weight: bold;
            font-size: 14px;
        }
    </style>
</head>
<body>

<div class="slider-section">
    <div class="swiper promo-slider">
        <div class="swiper-wrapper">
            
            <!-- СЛАЙД 1: Промо-контент -->
            <div class="swiper-slide promo-card">
                <div class="promo-card-content anime-element">
                    <h2>Discover Your Go-To Source!</h2>
                    <p>Get inspiration every day – our website is always there to offer something exciting!</p>
                </div>
            </div>

            <!-- СЛАЙД 2: БЕЛАЯ КАРТОЧКА ПОВЕРХ IFRAME -->
            <div class="swiper-slide promo-card" style="background: none;">
                <div class="slide-iframe-wrapper">
                    <!-- iframe на заднем плане -->
                    <iframe src="https://ruziiiik1990-beep.github.io/glavna9/" class="background-iframe"></iframe>
                    
                    <!-- Белая карточка на переднем плане -->
                    <div class="white-card-overlay anime-element">
                        <img src="https://4ak4ak.moy.su/logo1.jpg" class="fixed-weapon-logo" alt="logo">
                        <h2 style="color: #111827; margin: 0 0 10px 0; font-size: 24px;">Главная Панель</h2>
                        <p style="color: #4b5563; margin: 0;">Слой iframe успешно интегрирован под белую карточку без ошибок CSP.</p>
                    </div>
                </div>
            </div>

        </div>

        <!-- Контроллеры -->
        <div class="promo-slider-controls">
            <button class="swiper-btn swiper-btn-prev">◀</button>
            <div class="promo-slider-fraction"></div>
            <button class="swiper-btn swiper-btn-next">▶</button>
        </div>
    </div>
</div>

<!-- Подключаем Swiper JS -->
<script src="https://jsdelivr.net"></script>

<script>
    document.addEventListener('DOMContentLoaded', () => {
        // Инициализация Swiper
        const swiper = new Swiper('.promo-slider', {
            loop: true,
            spaceBetween: 24,
            pagination: {
                el: ".promo-slider-fraction",
                type: "fraction",
            },
            navigation: {
                nextEl: '.swiper-btn-next',
                prevEl: '.swiper-btn-prev',
            },
            on: {
                init: function () {
                    runAnimeEffect();
                },
                slideChange: function () {
                    runAnimeEffect();
                }
            }
        });

        // Функция той самой 6-секундной комплексной анимации Anime.js
        function runAnimeEffect() {
            // Сбрасываем текущие стили перед запуском анимации
            anime.remove('.anime-element');

            anime({
                targets: '.anime-element',
                // 1. Появление сверху вниз и зум (первые фазы цикла)
                translateY: [
                    { value: -400, duration: 0, easing: 'easeOutSine' }, // Стартуем высоко вверху
                    { value: 0, duration: 1500, easing: 'easeOutBack' }, // Падаем в центр
                    { value: 0, duration: 3000 },                        // Удерживаем позицию в центре
                    { value: 500, duration: 1500, easing: 'easeInBack' } // Падаем вниз, исчезая
                ],
                scale: [
                    { value: 0.2, duration: 0, easing: 'easeOutSine' },  // Сжаты в точку вверху
                    { value: 1.1, duration: 1000, easing: 'easeOutQuad' }, // Пролетаем с легким увеличением
                    { value: 1.0, duration: 500, easing: 'easeInOutQuad' },// Встаем в идеальный размер
                    { value: 1.0, duration: 3000 },                        // Держим размер пока читают
                    { value: 0.5, duration: 1500, easing: 'easeInQuad' }   // Сужаемся при падении вниз
                ],
                opacity: [
                    { value: 0, duration: 0 },
                    { value: 1, duration: 800 },
                    { value: 1, duration: 3700 },
                    { value: 0, duration: 1500 }
                ],
                duration: 6000, // Строго 6 секунд на весь цикл анимации
                loop: false
            });
        }
    });
</script>

</body>
</html>
