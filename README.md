<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Интерактивный Слайдер с Анимацией</title>
    <!-- Подключение Three.js и Anime.js -->
    <script src="https://cloudflare.com"></script>
    <script src="https://cloudflare.com"></script>
    <style>
        @font-face {
            font-family: 'Gurlbones';
            src: local('Gurlbones'), url('https://cdnfonts.com') format('woff');
        }

        body {
            background-color: #374151; /* Тот самый темно-серый фон */
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: #ffffff;
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        /* Контейнер слайдера */
        .slider-wrapper {
            position: relative;
            width: 800px;
            height: 500px;
            background: rgba(31, 41, 55, 0.6);
            border-radius: 16px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(12px);
            overflow: hidden;
        }

        .slides-container {
            display: flex;
            width: 200%;
            height: 100%;
            transition: transform 0.5s ease-in-out;
        }

        .slide {
            width: 50%;
            height: 100%;
            position: relative;
            box-sizing: border-box;
            padding: 4px;
        }

        /* Первый слайд: 3D-модель / векторная графика */
        #canvas-container {
            width: 100%;
            height: 100%;
            position: relative;
        }

        /* Второй слайд: Карточка с белым фоном и iframe сзади */
        .slide-content-two {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .background-iframe {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border: none;
            z-index: 1;
        }

        .white-card {
            position: relative;
            z-index: 2;
            background: #ffffff;
            color: #1f2937;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            text-align: center;
            max-width: 400px;
        }

        /* Заголовок и элементы интерфейса */
        .heading-frame-row {
            position: absolute;
            top: 20px;
            left: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            z-index: 10;
        }

        .heading-frame-row h2 {
            font-family: 'Gurlbones', sans-serif;
            margin: 0;
            font-size: 28px;
            color: #ffffff;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
        }

        /* Картинка-логотип возле цифр (строго одна, не пропадает) */
        .rank-logo-main {
            width: 24px;
            height: 24px;
            object-fit: contain;
            display: inline-block;
        }

        .counter-display {
            font-size: 20px;
            font-weight: bold;
            background: rgba(0,0,0,0.4);
            padding: 5px 12px;
            border-radius: 20px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
    </style>
</head>
<body>

<div class="slider-wrapper">
    <!-- Верхний блок с заголовком и картинкой-логотипом -->
    <div class="heading-frame-row">
        <div class="counter-display">
            <span>Слайд:</span>
            <img src="https://4ak4ak.moy.su/logo1.jpg" class="rank-logo-main" id="logo-img" alt="logo">
            <span id="slide-num">1</span>
        </div>
    </div>

    <!-- Основные слайды -->
    <div class="slides-container" id="slidesContainer">
        <!-- СЛАЙД 1: Векторная 3D анимация вращения -->
        <div class="slide">
            <div id="canvas-container"></div>
        </div>

        <!-- СЛАЙД 2: Iframe на фоне и белая карточка впереди -->
        <div class="slide">
            <div class="slide-content-two">
                <iframe src="https://ruziiiik1990-beep.github.io/glavna9/" class="background-iframe"></iframe>
                <div class="white-card">
                    <h3 style="font-family: 'Gurlbones'; font-size: 24px;">Информационная панель</h3>
                    <p>Интегрированный фрейм главной страницы успешно загружен на задний план.</p>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    // --- НАСТРОЙКА THREE.JS ДЛЯ КРИСТАЛЛИЧЕСКОЙ СТРУКТУРЫ (ЗОЛОТАЯ ГОРА) ---
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    
    const camera = new THREE.PerspectiveCamera(45, container.clientWidth / container.clientHeight, 0.1, 100);
    camera.position.z = 15;

    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(800, 500);
    container.appendChild(renderer.domElement);

    // Освещение (эффект лучей сверху)
    const topLight = new THREE.DirectionalLight(0xffffff, 1.5);
    topLight.position.set(0, 10, 5).normalize();
    scene.add(topLight);

    const ambientLight = new THREE.AmbientLight(0x404040, 1.2);
    scene.add(ambientLight);

    // Создание группы объектов, имитирующей полигональную гору из хвороста/кристаллов
    const objectGroup = new THREE.Group();
    const geometry = new THREE.IcosahedronGeometry(0.6, 0); // Низкополигональные кристаллы
    
    // Золотой глянцевый материал
    const material = new THREE.MeshStandardMaterial({
        color: 0xd4af37,
        roughness: 0.2,
        metalness: 0.8,
        flatShading: true
    });

    // Генерируем кучу элементов в форме пирамиды/горы
    for (let i = 0; i < 60; i++) {
        const mesh = new THREE.Mesh(geometry, material);
        
        // Распределение по форме горы
        const layer = Math.floor(i / 15); 
        const radius = 2.5 - layer * 0.5;
        const theta = Math.random() * Math.PI * 2;
        
        mesh.position.set(
            Math.cos(theta) * Math.random() * radius,
            (layer * 0.8) - 1.5,
            Math.sin(theta) * Math.random() * radius
        );
        
        mesh.rotation.set(Math.random() * 2, Math.random() * 2, Math.random() * 2);
        mesh.scale.setScalar(Math.random() * 0.5 + 0.6);
        objectGroup.add(mesh);
    }
    
    scene.add(objectGroup);

    // Анимация вращения на 360 градусов при помощи Anime.js
    anime({
        targets: objectGroup.rotation,
        y: Math.PI * 2,
        duration: 12000,
        easing: 'linear',
        loop: true
    });

    // Рендер-цикл Three.js
    function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
    }
    animate();


    // --- НЕЗАВИСИМАЯ СМЕНА СЛАЙДОВ (КАЖДЫЕ 10 СЕКУНД) ---
    let currentSlide = 0;
    const slidesContainer = document.getElementById('slidesContainer');
    const slideNumDisplay = document.getElementById('slide-num');

    setInterval(() => {
        currentSlide = currentSlide === 0 ? 1 : 0;
        
        // Сдвиг контейнера слайдов
        slidesContainer.style.transform = `translateX(-${currentSlide * 50}%)`;
        slideNumDisplay.innerText = currentSlide + 1;

        // Одновременная плавная анимация появления элементов через Anime.js при смене слайда
        anime({
            targets: ['.white-card', '#canvas-container'],
            opacity:,
            scale: [0.95, 1],
            duration: 600,
            easing: 'easeOutQuad'
        });

    }, 10000); // Строго 10 секунд на один слайд

    // Обработка изменения размеров окна
    window.addEventListener('resize', () => {
        camera.aspect = container.clientWidth / container.clientHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(container.clientWidth, container.clientHeight);
    });
</script>

</body>
</html>
