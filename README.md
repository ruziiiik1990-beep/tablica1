<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Vertical Scroll Table - Anime.js</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

    :root {
      --bg-color: #2b364d;       /* Цвет фона как на фото */
      --panel-bg: #3e4b66;       /* Цвет панели таблицы */
      --border-color: #5a6b8c;   /* Цвет рамок */
      --text-main: #ffffff;      /* Белый текст */
      --text-accent: #00f3ff;   /* Неоновый голубой (как заголовок) */
      --text-gold: #ffd700;      /* Золотой для очков */
      --text-orange: #ff8c00;    /* Оранжевый для ников */
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      background-color: var(--bg-color);
      font-family: 'Press Start 2P', monospace; /* Пиксельный шрифт как на фото */
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      overflow: hidden; /* Важно: убираем скролл у самого браузера */
      color: var(--text-main);
    }

    /* --- Контейнер прокрутки --- */
    .scroll-wrapper {
      width: 600px;
      height: 450px; /* Фиксированная высота, как рамка на фото */
      overflow-y: hidden; /* Скрываем нативный скролл */
      position: relative;
      border: 4px solid var(--border-color);
      box-shadow: 0 0 20px rgba(0, 243, 255, 0.2);
      background: rgba(0, 0, 0, 0.3);
    }

    /* --- Таблица --- */
    table {
      width: 100%;
      border-collapse: collapse;
      /* Мы будем анимировать translateY этого элемента */
      transform-origin: top center; 
    }

    th, td {
      padding: 12px;
      text-align: center;
      border-bottom: 1px solid rgba(255,255,255,0.1);
      position: relative;
    }

    th {
      background-color: rgba(0, 0, 0, 0.3);
      color: var(--text-accent);
      text-transform: uppercase;
      font-size: 14px;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    tr:last-child td { border-bottom: none; }

    /* Цвета ячеек как на фото */
    .player-name {
      color: var(--text-orange);
      font-weight: bold;
    }
    
    .champ-val { color: #fff; }
    .final-val { color: #aaa; }
    
    .reward-cell {
      position: relative;
    }

    /* --- Элементы награды --- */
    .chak-chak-icon {
      width: 20px;
      height: 20px;
      opacity: 0;
      transform: scale(0.5);
      position: absolute;
      top: 50%;
      left: 50%;
      transform-origin: center;
      will-change: opacity, transform;
    }

    .chak-chak-count {
      color: var(--text-gold);
      font-weight: bold;
      z-index: 2;
      position: relative;
      display: inline-block;
    }

    /* --- Подвал (Admin & Rules) --- */
    .footer-area {
      margin-top: 20px;
      text-align: center;
      width: 600px;
      color: #888;
      font-size: 12px;
      line-height: 1.4;
    }

    .admin-panel {
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 10px;
      margin-top: 15px;
    }

    input {
      background: rgba(0,0,0,0.3);
      border: 1px solid #555;
      color: white;
      padding: 8px;
      font-family: inherit;
    }

    button {
      background: #00f3ff;
      color: #000;
      border: none;
      padding: 8px 20px;
      font-family: inherit;
      font-weight: bold;
      cursor: pointer;
      text-transform: uppercase;
    }

    button:hover {
      box-shadow: 0 0 15px #00f3ff;
    }

    /* --- Глитч эффекты поверх всего --- */
    .overlay {
      position: absolute;
      top: 0; left: 0; width: 100%; height: 100%;
      pointer-events: none;
      z-index: 100;
      overflow: hidden;
    }

    .scanlines {
      position: absolute; top: 0; left: 0; width: 100%; height: 100%;
      background: linear-gradient(rgba(0,0,0,0) 50%, rgba(0,0,0,0.15) 50%);
      background-size: 100% 4px;
      animation: scan-anim 5s linear infinite;
      opacity: 0.6;
    }

    @keyframes scan-anim { 0% { background-position: 0 0; } 100% { background-position: 0 100%; } }
  </style>
</head>
<body>

  <!-- Эффект полос -->
  <div class="overlay">
    <div class="scanlines"></div>
  </div>

  <!-- Контейнер прокрутки -->
  <div class="scroll-wrapper" id="scrollContainer">
    <table id="mainTable">
      <thead>
        <tr>
          <th>#</th>
          <th>ИГРОК</th>
          <th>ЧЕМПИОН</th>
          <th>ФИНАЛИСТ</th>
          <th>ЧАК-ЧАК</th>
        </tr>
      </thead>
      <tbody id="tableBody">
        <!-- Строки будут добавлены через JS -->
      </tbody>
    </table>
  </div>

  <!-- Подвал -->
  <div class="footer-area">
    <p>Победитель финала +2 чак-чака • Финалист +1 чак-чак</p>
    <div class="admin-panel">
      <input type="password" id="adminPass" placeholder="Админ-пароль">
      <button onclick="checkAdmin()">Войти как админ</button>
    </div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.1/anime.min.js"></script>
  <script>
    // --- ДАННЫЕ (как на фото) ---
    const players = [
      { id: 1, name: '45', champ: 1, final: 0, reward: 2 },
      { id: 2, name: '123', champ: 1, final: 0, reward: 2 },
      { id: 3, name: 'qweqwe', champ: 1, final: 0, reward: 2 },
      { id: 4, name: 'hhh', champ: 1, final: 0, reward: 2 },
      { id: 5, name: 'asdxzc3', champ: 1, final: 0, reward: 2 },
      { id: 6, name: 'chesalova20013', champ: 1, final: 0, reward: 2 },
      { id: 7, name: 'fg', champ: 1, final: 0, reward: 2 }
    ];

    const tbody = document.getElementById('tableBody');
    const scrollContainer = document.getElementById('scrollContainer');
    const table = document.getElementById('mainTable');

    // --- SVG Иконка чак-чака (встроенная, не нужен внешний файл) ---
    const chakChakSvg = `<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <circle cx="12" cy="12" r="10" fill="#FFB000" stroke="#D4A017" stroke-width="2"/>
      <path d="M12 6C10.8954 6 10 6.89543 10 8C10 9.10457 10.8954 10 12 10C13.1046 10 14 9.10457 14 8C14 6.89543 13.1046 6 12 6Z" fill="#FFF" opacity="0.8"/>
      <path d="M12 14C10.8954 14 10 14.8954 10 16C10 17.1046 10.8954 18 12 18C13.1046 18 14 17.1046 14 16C14 14.8954 13.1046 14 12 14Z" fill="#FFF" opacity="0.6"/>
    </svg>`;

    // --- ГЕНЕРАЦИЯ ТАБЛИЦЫ ---
    players.forEach((p, index) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>\${p.id}</td>
        <td><span class="player-name">\${p.name}</span></td>
        <td class="champ-val">\${p.champ}</td>
        <td class="final-val">\${p.final}</td>
        <td class="reward-cell">
          <span class="chak-chak-count">\${p.reward}</span>
          <div class="chak-chak-icon">\${chakChakSvg}</div>
        </td>
      `;
      tbody.appendChild(tr);
    });

    // --- РАСЧЕТ ВЫСОТЫ ---
    // Нам нужно знать, на сколько пикселей прокрутить вниз.
    // Высота таблицы = (высота строки * кол-во строк) + высота заголовка.
    // Но так как мы скроллим ВНУТРИ контейнера высотой 450px,
    // нам нужно прокрутить таблицу так, чтобы последняя строка оказалась внизу.
    
    const rowHeight = 50; // Примерная высота строки (с учетом padding)
    const headerHeight = 50;
    const totalTableHeight = headerHeight + (players.length * rowHeight);
    const containerHeight = scrollContainer.clientHeight;
    
    // Расстояние, которое должна проехать таблица:
    // Она должна уйти вверх так, чтобы самый низ таблицы совпал с низом контейнера.
    const scrollDistance = totalTableHeight - containerHeight;

    // --- ЗАПУСК АНИМАЦИИ ---
    const tl = anime.timeline({
      autoplay: true,
      duration: 5000, // Ровно 5 секунд, как ты просил
      easing: 'easeInOutCubic' // Плавное начало и конец
    });

    // 1. Анимация прокрутки (Листание вниз)
    // Мы двигаем саму таблицу вверх (translateY отрицательный), создавая иллюзию, что мы листаем вниз
    tl.add({
      targets: table,
      translateY: [-0, -scrollDistance], // От 0 до полной прокрутки
      duration: 5000,
      easing: 'easeInOutCubic'
    });

    // 2. Анимация наград (Чак-чак)
    // Запускается в конце прокрутки (или параллельно, но с задержкой)
    // Мы хотим, чтобы награды появлялись, когда строки "проезжают"
    tl.add({
      targets: '.chak-chak-icon',
      opacity: [0, 1],
      scale: [0.5, 1],
      rotate: [0, 10, 0], // Легкое покачивание
      delay: anime.stagger(150, { from: 'first' }), // По очереди, сверху вниз
      duration: 300,
      easing: 'easeOutBack'
    }, '-=1000'); // Начинаем за 1 секунду до конца прокрутки

    // 3. Пульсация цифр
    tl.add({
      targets: '.chak-chak-count',
      scale: [1, 1.3, 1],
      color: ['#fff', '#ffd700', '#fff'],
      delay: anime.stagger(150, { from: 'first' }),
      duration: 400,
      easing: 'easeOutBack'
    }, '-=200');

    // --- АДМИН ПАНЕЛЬ ---
    function checkAdmin() {
      const pass = document.getElementById('adminPass').value;
      if (pass === 'admin') {
        alert('Админ панель активирована!');
      } else {
        // Глитч при ошибке
        anime({
          targets: '.scroll-wrapper',
          rotate: [-5, 5, -5, 5, 0],
          duration: 200,
          easing: 'easeOutBack'
        });
      }
    }
  </script>
</body>
</html>
