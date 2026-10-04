<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Рейтинг: Вертикальный Скролл</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

    :root {
      --bg: #1a1e29;
      --panel: #2c3545;
      --border: #4a5b75;
      --accent: #00f0ff;
      --gold: #ffd700;
      --orange: #ff8c00;
      --text: #ffffff;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      background: var(--bg);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      font-family: 'Press Start 2P', monospace;
      overflow: hidden; /* Убираем скролл браузера */
      color: var(--text);
    }

    /* Эффект старого монитора */
    .scanlines {
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      background: linear-gradient(rgba(0,0,0,0) 50%, rgba(0,0,0,0.15) 50%);
      background-size: 100% 4px;
      animation: scan-anim 10s linear infinite;
      pointer-events: none;
      z-index: 999;
      opacity: 0.6;
    }

    @keyframes scan-anim { 0% { background-position: 0 0; } 100% { background-position: 0 100%; } }

    /* Контейнер таблицы */
    .wrapper {
      width: 640px;
      height: 480px; /* Фиксированный размер "экрана" */
      border: 4px solid var(--border);
      box-shadow: 0 0 30px rgba(0, 240, 255, 0.1);
      background: rgba(0, 0, 0, 0.4);
      position: relative;
      overflow: hidden; /* Скрываем всё, что вылезает за рамки */
    }

    h2 {
      text-align: center;
      color: var(--accent);
      text-shadow: 0 0 10px var(--accent);
      padding: 15px 0;
      text-transform: uppercase;
      letter-spacing: 2px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      /* Таблица будет двигаться внутри wrapper */
      will-change: transform; 
    }

    th, td {
      padding: 12px;
      text-align: center;
      border-bottom: 1px solid rgba(255,255,255,0.1);
    }

    th {
      background: rgba(0,0,0,0.3);
      color: #aaa;
      text-transform: uppercase;
      font-size: 12px;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    /* Стили ячеек */
    .player-name {
      color: var(--orange);
      font-weight: bold;
    }

    .stat-num {
      color: #fff;
      font-weight: bold;
    }

    .reward-cell {
      position: relative;
    }

    .chak-icon {
      width: 20px;
      height: 20px;
      opacity: 0;
      transform: scale(0.5) rotate(45deg);
      position: absolute;
      top: 50%;
      left: 50%;
      transform-origin: center;
      will-change: opacity, transform;
    }

    .chak-count {
      color: var(--gold);
      z-index: 2;
      position: relative;
      display: inline-block;
    }

    /* Подпись внизу */
    .legend {
      text-align: center;
      color: #666;
      font-size: 10px;
      margin-top: 10px;
      text-transform: uppercase;
    }
  </style>
</head>
<body>

  <div class="scanlines"></div>

  <div class="wrapper">
    <h2>РЕЙТИНГОВАЯ СИСТЕМА</h2>
    <table id="ratingTable">
      <thead>
        <tr>
          <th>#</th>
          <th>Игрок</th>
          <th>Чемпион</th>
          <th>Финалист</th>
          <th>Чак-чак</th>
        </tr>
      </thead>
      <tbody id="tbody">
        <!-- Строки генерируются JS -->
      </tbody>
    </table>
    <div class="legend">Победитель финала +2 чак-чака • Финалист +1 чак-чак</div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.1/anime.min.js"></script>
  <script>
    // --- ДАННЫЕ (Имена и цифры) ---
    const players = [
      { id: 1, name: 'TurboRacer', champ: 1, final: 0, reward: 2 },
      { id: 2, name: 'DarkWolf', champ: 0, final: 1, reward: 1 },
      { id: 3, name: 'PixelQueen', champ: 1, final: 0, reward: 2 },
      { id: 4, name: 'NeonStrike', champ: 0, final: 0, reward: 0 },
      { id: 5, name: 'IronFist', champ: 1, final: 0, reward: 2 },
      { id: 6, name: 'CosmoFly', champ: 0, final: 1, reward: 1 },
      { id: 7, name: 'ShadowStep', champ: 0, final: 0, reward: 0 },
      { id: 8, name: 'LaserBlast', champ: 1, final: 0, reward: 2 },
      { id: 9, name: 'FrostKing', champ: 0, final: 0, reward: 0 },
      { id: 10, name: 'VoltMaster', champ: 1, final: 0, reward: 2 }
    ];

    const tbody = document.getElementById('tbody');
    const table = document.getElementById('ratingTable');
    
    // SVG иконка чак-чака
    const iconSvg = `<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><circle cx="12" cy="12" r="10" fill="#FFB000" stroke="#D4A017" stroke-width="2"/><path d="M12 6C10.8954 6 10 6.89543 10 8C10 9.10457 10.8954 10 12 10C13.1046 10 14 9.10457 14 8C14 6.89543 13.1046 6 12 6Z" fill="#FFF" opacity="0.8"/><path d="M12 14C10.8954 14 10 14.8954 10 16C10 17.1046 10.8954 18 12 18C13.1046 18 14 17.1046 14 16C14 14.8954 13.1046 14 12 14Z" fill="#FFF" opacity="0.6"/></svg>`;

    // --- ГЕНЕРАЦИЯ ТАБЛИЦЫ ---
    players.forEach((p, index) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>\${p.id}</td>
        <td><span class="player-name">\${p.name}</span></td>
        <td class="stat-num">\${p.champ}</td>
        <td class="stat-num">\${p.final}</td>
        <td class="reward-cell">
          <span class="chak-count">\${p.reward}</span>
          <div class="chak-icon">\${iconSvg}</div>
        </td>
      `;
      tbody.appendChild(tr);
    });

    // --- РАСЧЕТ ПРОКРУТКИ ---
    // Высота одной строки (примерно)
    const rowHeight = 48; 
    const headerHeight = 48;
    const totalHeight = headerHeight + (players.length * rowHeight);
    const containerHeight = 480; // Высота wrapper
    
    // На сколько нужно сдвинуть таблицу вверх, чтобы показать конец
    const scrollDistance = totalHeight - containerHeight;

    // --- АНИМАЦИЯ ---
    const tl = anime.timeline({
      autoplay: true,
      duration: 5000, // Ровно 5 секунд
      easing: 'easeInOutCubic'
    });

    // 1. Вертикальная прокрутка (Листание вниз)
    // Таблица двигается вверх, создавая эффект листания вниз
    tl.add({
      targets: table,
      translateY: [0, -scrollDistance],
      duration: 5000,
      easing: 'easeInOutCubic'
    });

    // 2. Эффект "Обгона" и начисления очков
    // Запускается в конце прокрутки.
    // Мы делаем так, что строки немного "подпрыгивают", а награды выпрыгивают.
    tl.add({
      targets: 'tbody tr',
      translateY: [0, -10, 0], // Легкий прыжок строки
      duration: 200,
      delay: anime.stagger(100, { from: 'first' }), // Волной сверху вниз
      easing: 'easeOutBack'
    }, '-=1000'); // Начинаем за 1 сек до конца скролла

    // 3. Появление наград (Чак-чак)
    tl.add({
      targets: '.chak-icon',
      opacity: [0, 1],
      scale: [0.5, 1],
      rotate: [45, 0],
      duration: 300,
      delay: anime.stagger(100, { from: 'first' }),
      easing: 'easeOutBack'
    }, '-=200');

    // 4. Пульсация цифр очков
    tl.add({
      targets: '.chak-count',
      scale: [1, 1.4, 1],
      color: ['#fff', '#ffd700', '#fff'],
      duration: 400,
      delay: anime.stagger(100, { from: 'first' }),
      easing: 'easeOutBack'
    }, '-=300');

  </script>
</body>
</html>
