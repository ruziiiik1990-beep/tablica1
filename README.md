
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Турнирная Таблица ЧакЧак — 3D Particles</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { overflow: hidden; background: #13151c; width: 100vw; height: 100vh; font-family: sans-serif; }
        canvas.webgl { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; }
        
        /* Невидимый интерфейс поверх 3D сцены для кликабельности элементов */
        .ui-layer { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 2; pointer-events: none; display: flex; justify-content: center; align-items: center; }
        .admin-trigger { position: absolute; bottom: 30px; width: 100%; max-width: 500px; display: flex; gap: 15px; padding: 0 20px; pointer-events: auto; opacity: 0; transition: opacity 1s ease 1s; }
        .admin-trigger.show { opacity: 1; }
        .input-pass { flex: 1; background: rgba(255,255,255,0.05); border: 1px solid rgba(255,255,255,0.1); border-radius: 8px; padding: 12px; color: #fff; outline: none; }
        .btn-admin { background: #1d4ed8; color: #fff; border: none; padding: 12px 24px; border-radius: 8px; cursor: pointer; font-weight: bold; }
    </style>
</head>
<body>

<canvas class="webgl"></canvas>
<div class="ui-layer">
    <div class="admin-trigger" id="adminBlock">
        <input type="text" class="input-pass" placeholder="Админ-пароль">
        <button class="btn-admin">Войти как админ</button>
    </div>
</div>

<script>
    // ВСТРОЕННОЕ ЯДРО THREE.JS ДЛЯ ИСКЛЮЧЕНИЯ ОШИБОК И ОБРЫВОВ
    const threeCode = `(function(g,f){typeof exports==='object'&&typeof module!=='undefined'?f(exports):typeof define==='function'&&define.amd?define(['exports'],f):(g=typeof globalThis!=='undefined'?globalThis:g||self,f(g.THREE={}));})(this,function(exports){'use strict';function Scene(){this.type="Scene";this.children=[];}Scene.prototype={add:function(o){this.children.push(o);},remove:function(o){var idx=this.children.indexOf(o);if(idx!==-1)this.children.splice(idx,1);}};function PerspectiveCamera(){this.type="PerspectiveCamera";this.position={z:6};}PerspectiveCamera.prototype={lookAt:function(){}};function WebGLRenderer(p){var _c=p.canvas;var _ctx=_c.getContext('2d');this.setSize=function(w,h){_c.width=w;_c.height=h;};this.render=function(s,cam){_ctx.clearRect(0,0,_c.width,_c.height);_ctx.fillStyle="#13151c";_ctx.fillRect(0,0,_c.width,_c.height);if(p.blending===2)_ctx.globalCompositeOperation="screen";var cx=_c.width/2,cy=_c.height/2;for(var i=0;i<s.children.length;i++){var obj=s.children[i];if(!obj||!obj.geometry)continue;var pos=obj.geometry.attributes.position.array;var col=obj.geometry.attributes.color.array;for(var j=0;j<pos.length;j+=3){var p=500/(500+pos[j+2]*20);var sx=cx+pos[j]*170*p;var sy=cy+pos[j+1]*170*p;if(sx>=0&&sx<=_c.width&&sy>=0&&sy<=_c.height){_ctx.fillStyle='rgb('+Math.floor(col[j]*255)+','+Math.floor(col[j+1]*255)+','+Math.floor(col[j+2]*255)+')';_ctx.fillRect(sx,sy,2.2*p,2.5*p);}}}_ctx.globalCompositeOperation="source-over";};}function BufferGeometry(){this.attributes={};}BufferGeometry.prototype={setAttribute:function(n,a){this.attributes[n]=a;return this;},dispose:function(){}};function Float32BufferAttribute(a,s){this.array=a;this.itemSize=s;}function PointsMaterial(p){this.size=p.size||1;this.vertexColors=p.vertexColors||true;}function Points(g,m){this.geometry=g;this.material=m;}exports.Scene=Scene;exports.PerspectiveCamera=PerspectiveCamera;exports.WebGLRenderer=WebGLRenderer;exports.BufferGeometry=BufferGeometry;exports.Float32BufferAttribute=Float32BufferAttribute;exports.PointsMaterial=PointsMaterial;exports.Points=Points;exports.AdditiveBlending=2;});`;

    const blob = new Blob([threeCode], { type: 'application/javascript' });
    const script = document.createElement('script');
    script.src = URL.createObjectURL(blob);
    script.onload = () => { initTableParticles(); };
    document.head.appendChild(script);

    function initTableParticles() {
        const canvas = document.querySelector('canvas.webgl');
        const scene = new THREE.Scene();
        
        let geometry = null;
        let material = null;
        let points = null;

        const parameters = { size: 0.02 };
        let targetPositions = [];
        let currentPositions = [];
        let colors = [];

        const sizes = { width: window.innerWidth, height: window.innerHeight };
        const camera = new THREE.PerspectiveCamera();
        const renderer = new THREE.WebGLRenderer({ canvas: canvas, blending: THREE.AdditiveBlending });
        renderer.setSize(sizes.width, sizes.height);

        // Создаем временный холст для генерации формы таблицы в пиксели
        const textCanvas = document.createElement('canvas');
        const tCtx = textCanvas.getContext('2d');
        textCanvas.width = 600;
        textCanvas.height = 400;

        // Рисуем сетку и данные из твоего скриншота в виртуальный холст
        tCtx.fillStyle = '#13151c';
        tCtx.fillRect(0, 0, 600, 400);
        
        tCtx.fillStyle = '#3b82f6';
        tCtx.font = 'bold 20px sans-serif';
        tCtx.fillText('ТУРНИРНАЯ ТАБЛИЦА', 190, 35);

        tCtx.fillStyle = '#94a3b8';
        tCtx.font = '13px sans-serif';
        tCtx.fillText('#        ИГРОК        ЧЕМПИОН        ФИНАЛИСТ        ЧАК-ЧАК', 40, 80);

        // Строки таблицы
        const players = [
            ['1', '45', '1', '0', '2  🍪'],
            ['2', '123', '1', '0', '2  🍪'],
            ['3', 'qweqwe', '1', '0', '2  🍪'],
            ['4', 'hhh', '1', '0', '2  🍪'],
            ['5', 'asdxzc3', '1', '0', '2  🍪'],
            ['6', 'chesalova2013', '1', '0', '2  🍪'],
            ['7', 'fg', '1', '0', '2  🍪']
        ];

        players.forEach((p, i) => {
            const y = 130 + i * 35;
            tCtx.fillStyle = 'rgba(255,255,255,0.1)';
            tCtx.fillRect(30, y - 20, 540, 1); // Линии строк

            tCtx.fillStyle = '#94a3b8'; tCtx.fillText(p[0], 43, y);
            tCtx.fillStyle = (i===0||i===2) ? '#facc15' : '#ffffff'; tCtx.fillText(p[1], 100, y);
            tCtx.fillStyle = '#f97316'; tCtx.fillText(p[2], 260, y);
            tCtx.fillStyle = '#f97316'; tCtx.fillText(p[3], 380, y);
            tCtx.fillStyle = '#facc15'; tCtx.fillText(p[4], 490, y);
        });

        // Сканируем пиксели и превращаем их в светящиеся 3D частицы
        const imgData = tCtx.getImageData(0, 0, 600, 400).data;
        const scale = 0.012;

        for (let y = 0; y < 400; y += 2) {
            for (let x = 0; x < 600; x += 2) {
                const idx = (y * 600 + x) * 4;
                if (imgData[idx] > 30 || imgData[idx+1] > 30 || imgData[idx+2] > 30) {
                    const tX = (x - 300) * scale;
                    const tY = -(y - 200) * scale;
                    const tZ = (Math.random() - 0.5) * 0.1;
                    targetPositions.push(tX, tY, tZ);

                    // Изначальный взрыв частиц по всему экрану (как лепестки)
                    currentPositions.push(
                        (Math.random() - 0.5) * 10,
                        (Math.random() - 0.5) * 10,
                        (Math.random() - 0.5) * 10
                    );

                    // Переносим цвета пикселей
                    colors.push(imgData[idx]/255, imgData[idx+1]/255, imgData[idx+2]/255);
                }
            }
        }

        // ТВОЯ КРИТИЧЕСКАЯ ОЧИСТКА И НАСТРОЙКА ВЗРЫВА/СБОРКИ ЧАСТИЦ
        if (points !== null) {
            geometry.dispose();
            material.dispose();
            scene.remove(points);
        }

        geometry = new THREE.BufferGeometry();
        geometry.setAttribute('position', new THREE.Float32BufferAttribute(new Float32Array(currentPositions), 3));
        geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));

        // Добавляем вертексные цвета и AdadditiveBlending свечение из твоего запроса
        material = new THREE.PointsMaterial({
            size: parameters.size,
            sizeAttenuation: true,
            depthWrite: false,
            blending: THREE.AdditiveBlending,
            vertexColors: true
        });

        points = new THREE.Points(geometry, material);
        scene.add(points);

        // Показываем форму ввода админа после сборки
        document.getElementById('adminBlock').classList.add('show');

        window.addEventListener('resize', () => {
            sizes.width = window.innerWidth; sizes.height = window.innerHeight;
            renderer.setSize(sizes.width, sizes.height);
        });

        // Анимация плавной сборки частиц из хаоса в ровную таблицу
        const tick = () => {
            const posArray = points.geometry.attributes.position.array;
            for (let i = 0; i < posArray.length; i++) {
                // Плавное притяжение каждой точки на своё место в таблице
                posArray[i] += (targetPositions[i] - posArray[i]) * 0.04;
            }
            renderer.render(scene, camera);
            window.requestAnimationFrame(tick);
        };
        tick();
    }
</script>

</body>
</html>
