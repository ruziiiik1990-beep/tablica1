
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица ЧакЧак — Чистая Анимация Игроков</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            overflow: hidden; 
            background: #13151c; 
            width: 100vw; 
            height: 100vh; 
            font-family: sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            perspective: 1000px;
        }
        
        /* Интерактивный 3D-контейнер */
        .card {
            position: relative;
            width: 650px;
            height: 450px;
            z-index: 2;
            cursor: pointer;
            transform-style: preserve-3d;
            transition: transform 0.5s ease;
        }

        /* Обёртка для эффектов свечения */
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

        /* Псевдоэлементы свечения: изначально скрыты */
        .wrapper::before, .wrapper::after {
            content: "";
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            width: 80%;
            background: linear-gradient(90deg, transparent, #3b82f6, transparent);
            filter: blur(15px);
            opacity: 0;
            transition: opacity 0.5s ease, height 0.5s ease;
        }

        .wrapper::before { top: -5px; height: 5px; }
        .wrapper::after { bottom: -5px; height: 5px; }

        /* При наведении зажигаем неоновые полосы */
        .card:hover .wrapper::before, 
        .card:hover .wrapper::after { 
            opacity: 1; 
        } 

        /* Нижнее свечение вытягивается до 120px */
        .card:hover .wrapper::after { 
            height: 120px; 
            background: linear-gradient(180deg, transparent, rgba(59, 130, 246, 0.35));
        }

        /* Контент с плавной трансформацией */
        .title { 
            position: absolute;
            top: 40px;
            left: 0;
            width: 100%; 
            transition: transform 0.5s ease;
            transform: translate3d(0%, 0px, 0px);
        }

        /* Выталкиваем контент по оси Z вперед и вверх */
        .card:hover .title { 
            transform: translate3d(0%, -50px, 100px); 
        }

        canvas.webgl { 
            position: absolute; 
            top: 0; 
            left: 0; 
            width: 100%; 
            height: 100%; 
            z-index: 1; 
            pointer-events: none; 
        }
    </style>
</head>
<body>

<div class="card" id="tableCard">
    <div class="wrapper"></div>
    <div class="title"></div>
</div>

<canvas class="webgl"></canvas>

<!-- Подключаем официальный, стабильный Three.js из CDN -->
<script src="https://cloudflare.com"></script>

<script>
    document.addEventListener('DOMContentLoaded', () => {
        const imgLogo = new Image();
        imgLogo.crossOrigin = "Anonymous"; 
        imgLogo.src = 'https://moy.su';

        imgLogo.onload = () => {
            buildInteractiveParticles(imgLogo);
        };
    });

    function buildInteractiveParticles(imgLogo) {
        const canvas = document.querySelector('canvas.webgl');
        const scene = new THREE.Scene();
        
        let geometry = null, material = null, points = null;
        let targetPositions = [], currentPositions = [], colors = [];

        const sizes = { width: window.innerWidth, height: window.innerHeight };
        
        const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 0.1, 100);
        camera.position.z = 5.5;
        
        const renderer = new THREE.WebGLRenderer({ canvas: canvas, alpha: true });
        renderer.setSize(sizes.width, sizes.height);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

        const textCanvas = document.createElement('canvas');
        const tCtx = textCanvas.getContext('2d');
        textCanvas.width = 600; textCanvas.height = 400;

        // ИСПРАВЛЕНО: Фон холста залит строго в цвет заднего плана сайта (#13151c), ячейки сливаются с ним
        tCtx.fillStyle = '#13151c'; 
        tCtx.fillRect(0, 0, 600, 400);

        // ИСПРАВЛЕНО: Убраны все верхние надписи таблицы и шапки колонок, генерация начинается сразу со строк игроков
        // ИСПРАВЛЕНО: Добавлены рандомные геймерские никнеймы
        const players = [
            ['1', 'X-Slayer_99', '5', '2 ', '12 '], 
            ['2', 'Neon_Viper', '4', '1 ', '9 '],
            ['3', 'Chak_Master', '3', '3 ', '8 '], 
            ['4', 'Cyber_Glitch', '2', '0 ', '6 '],
            ['5', 'Zeus_Awper', '2', '1 ', '5 '], 
            ['6', 'Shadow_Step', '1', '2 ', '4 '],
            ['7', 'Bullet_Rain', '1', '0 ', '3 ']
        ];

        players.forEach((p, i) => {
            const y = 60 + i * 45; // Сместили строки повыше, так как заголовков больше нет
            
            // ИСПРАВЛЕНО: Фоны ячеек и разделительные линии полностью отсутствуют, цвет сливается с задним фоном
            tCtx.fillStyle = '#94a3b8'; tCtx.font = '15px sans-serif'; tCtx.fillText(p, 43, y);
            tCtx.fillStyle = (i===0||i===2) ? '#facc15' : '#ffffff'; tCtx.fillText(p, 100, y);
            tCtx.fillStyle = '#f97316'; tCtx.fillText(p, 280, y);
            tCtx.fillStyle = '#f97316'; tCtx.fillText(p, 400, y);
            tCtx.fillStyle = '#facc15'; tCtx.fillText(p, 500, y);
            
            // Отрисовка твоего логотипа чак-чака рядом с очками
            tCtx.drawImage(imgLogo, 520, y - 14, 18, 16);
        });

        const imgData = tCtx.getImageData(0, 0, 600, 400).data;
        const scale = 0.011;

        for (let y = 0; y < 400; y += 2) {
            for (let x = 0; x < 600; x += 2) {
                const idx = (y * 600 + x) * 4;
                if (imgData[idx] > 25 || imgData[idx+1] > 25 || imgData[idx+2] > 25) {
                    targetPositions.push((x - 300) * scale, -(y - 200) * scale, (Math.random() - 0.5) * 0.05);
                    currentPositions.push((Math.random() - 0.5) * 12, (Math.random() - 0.5) * 12, (Math.random() - 0.5) * 12);
                    colors.push(imgData[idx]/255, imgData[idx+1]/255, imgData[idx+2]/255);
                }
            }
        }

        if (points !== null) {
            geometry.dispose();
            material.dispose();
            scene.remove(points);
        }

        geometry = new THREE.BufferGeometry();
        geometry.setAttribute('position', new THREE.Float32BufferAttribute(new Float32Array(currentPositions), 3));
        geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));

        material = new THREE.PointsMaterial({
            size: 0.02,
            sizeAttenuation: true,
            depthWrite: false,
            blending: THREE.AdditiveBlending,
            vertexColors: true
        });

        points = new THREE.Points(geometry, material);
        scene.add(points);

        const card = document.getElementById('tableCard');
        let tRotX = 0, tRotY = 0;

        window.addEventListener('mousemove', (e) => {
            const x = (e.clientX / window.innerWidth) - 0.5;
            const y = (e.clientY / window.innerHeight) - 0.5;
            tRotY = x * 0.4;
            tRotX = -y * 0.4;
            card.style.transform = `rotateX(${tRotX * 45}deg) rotateY(${tRotY * 45}deg)`;
        });

        card.addEventListener('mouseleave', () => {
            tRotX = 0; tRotY = 0;
            card.style.transform = `rotateX(0deg) rotateY(0deg)`;
        });

        window.addEventListener('resize', () => {
            sizes.width = window.innerWidth; sizes.height = window.innerHeight;
            camera.aspect = sizes.width / sizes.height;
            camera.updateProjectionMatrix();
            renderer.setSize(sizes.width, sizes.height);
        });

        const tick = () => {
            const posArray = points.geometry.attributes.position.array;
            for (let i = 0; i < posArray.length; i++) {
                posArray[i] += (targetPositions[i] - posArray[i]) * 0.05;
            }
            points.geometry.attributes.position.needsUpdate = true;
            points.rotation.y += (tRotY - points.rotation.y) * 0.1;
            points.rotation.x += (tRotX - points.rotation.x) * 0.1;

            renderer.render(scene, camera);
            window.requestAnimationFrame(tick);
        };
        tick();
    }
</script>

</body>
</html>
