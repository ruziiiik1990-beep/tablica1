<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Турнирная таблица — Glitch & Anime.js</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');

    :root {
      --bg: #1a1f33;
      --panel: #2b334d;
      --accent: #00fff9;
      --error: #ff004c;
      --gold: #ffb000;
      --text: #e0e0ff;
      --border: #444;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'VT323', monospace;
      overflow: hidden;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }

    /* Scanlines & Noise */
    .scanlines, .noise {
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      pointer-events: none;
      z-index: 999;
      opacity: 0.3;
    }
    .scanlines {
      background: linear-gradient(rgba(0,0,0,0) 50%, rgba(0,0,0,0.2) 50%);
      background-size: 100% 4px;
      animation: scanlines-move 10s linear infinite;
    }
    .noise {
      background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)' opacity='0.15'/%3E%3C/svg%3E");
      animation: noise-move 15s linear infinite;
    }

    @keyframes scanlines-move { 0% { background-position: 0 0; } 100% { background-position: 0 100%; } }
    @keyframes noise-move { 0% { transform: translate(0,0); } 100% { transform: translate(20px,20px); } }

    /* Panel */
    .panel {
      width: 640px;
      background: var(--panel);
      border: 2px solid var(--border);
      box-shadow: 0 0 20px rgba(0,0,0,0.5), inset 0 0 20px rgba(0,0,0,0.8);
      padding: 20px;
      position: relative;
      z-index: 10;
    }

    h2 {
      text-align: center;
      color: var(--accent);
      text-shadow: 0 0 10px var(--accent);
      margin-bottom: 20px;
      letter-spacing: 2px;
    }

    /* Table */
    table {
      width: 100%;
      border-collapse: collapse;
      text-align: center;
    }

    th, td {
      padding: 12px 8px;
      border-bottom: 1px solid #444;
    }

    th {
      color: #aaa;
      text-transform: uppercase;
      font-size: 14px;
    }

    tr:last-child td { border-bottom: none; }

    .player-name {
      font-weight: bold;
      color: var(--text);
      position: relative;
    }

    /* Чак-чак */
    .chak-chak-cell {
      position: relative;
      overflow: hidden;
    }

    .chak-chak-icon {
      width: 24px;
      height: 24px;
      display: inline-block;
      filter: drop-shadow(0 0 4px rgba(255,176,0,0.6));
      opacity: 0;
      transform: scale(0.5);
      will-change: transform, opacity;
    }

    .chak-chak-count {
      color: var(--gold);
      font-weight: bold;
      margin-right: 6px;
      display: inline-block;
      position: relative;
      z-index: 2;
    }

    /* Scrollbar */
    .scroll-container {
      max-height: 360px;
      overflow-y: auto;
      scrollbar-width: thin;
      scrollbar-color: var(--accent) var(--panel);
    }

    .scroll-container::-webkit-scrollbar { width: 8px; }
    .scroll-container::-webkit-scrollbar-track { background: var(--panel); }
    .scroll-container::-webkit-scrollbar-thumb { background: var(--accent); border-radius: 4px; }

    /* Scroll hint */
    .scroll-hint {
      text-align: center;
      color: #666;
      font-size: 12px;
      margin-top: 10px;
      animation: pulse 2s infinite;
    }

    @keyframes pulse { 0%, 100% { opacity: 0.4; } 50% { opacity: 1; } }

    /* Admin panel (bottom) */
    .admin-panel {
      margin-top: 15px;
      display: flex;
      gap: 10px;
      justify-content: center;
    }

    input[type="password"] {
      background: #1a1f33;
      border: 1px solid #444;
      color: var(--text);
      padding: 8px 12px;
      font-family: inherit;
    }

    button {
      background: var(--accent);
      color: #000;
      border: none;
      padding: 8px 20px;
      font-family: inherit;
      font-weight: bold;
      cursor: pointer;
      text-transform: uppercase;
      box-shadow: 0 0 10px rgba(0,255,249,0.4);
    }

    button:hover { background: #00e6e0; transform: scale(1.05); }

    /* Glitch effect for text */
    .glitch {
      position: relative;
      color: white;
      text-shadow: -2px 0 #ff004c, 2px 0 #00fff9;
    }
  </style>
</head>
<body>

  <div class="scanlines"></div>
  <div class="noise"></div>

  <div class="panel">
    <h2 class="glitch">ТУРНИРНАЯ ТАБЛИЦА</h2>

    <div class="scroll-container" id="scrollContainer">
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Игрок</th>
            <th>Чемпион</th>
            <th>Финалист</th>
            <th>Чак-чак</th>
          </tr>
        </thead>
        <tbody id="tableBody">
          <!-- Rows will be injected by JS -->
        </tbody>
      </table>
    </div>

    <p class="scroll-hint">Прокрутите вниз, чтобы увидеть начисление наград</p>

    <div class="admin-panel">
      <input type="password" id="adminPass" placeholder="Админ-пароль" />
      <button id="adminBtn">Войти как админ</button>
    </div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.1/anime.min.js"></script>
  <script>
    // --- DATA ---
    const players = [
      { id: 1, name: '45', champion: 1, finalist: 0, chakChak: 2 },
      { id: 2, name: '123', champion: 1, finalist: 0, chakChak: 2 },
      { id: 3, name: 'qweqwe', champion: 1, finalist: 0, chakChak: 2 },
      { id: 4, name: 'hhh', champion: 1, finalist: 0, chakChak: 2 },
      { id: 5, name: 'asdxzc3', champion: 1, finalist: 0, chakChak: 2 },
      { id: 6, name: 'chesalova20013', champion: 1, finalist: 0, chakChak: 2 },
      { id: 7, name: 'fg', champion: 1, finalist: 0, chakChak: 2 }
    ];

    // SVG иконка чак-чака (встроенная, не требует внешних файлов)
    const chakChakSvg = `<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M12 2C6.48 2 2 6.48 2 12C2 17.52 6.48 22 12 22C17.52 22 22 17.52 22 12C22 6.48 17.52 2 12 2Z" fill="#FFB000" stroke="#D4A017" stroke-width="2"/>
      <path d="M12 6C10.8954 6 10 6.89543 10 8C10 9.10457 10.8954 10 12 10C13.1046 10 14 9.10457 14 8C14 6.89543 13.1046 6 12 6Z" fill="#FFF" opacity="0.8"/>
      <path d="M12 14C10.8954 14 10 14.8954 10 16C10 17.1046 10.8954 18 12 18C13.1046 18 14 17.1046 14 16C14 14.8954 13.1046 14 12 14Z" fill="#FFF" opacity="0.6"/>
    </svg>`;

    const tableBody = document.getElementById('tableBody');
    const scrollContainer = document.getElementById('scrollContainer');

    // --- RENDER TABLE ---
    players.forEach((p, index) => {
      const tr = document.createElement('tr');
      tr.style.opacity = '0'; // Скрываем до анимации
      tr.innerHTML = `
        <td>\${p.id}</td>
        <td><span class="player-name">\${p.name}</span></td>
        <td>\${p.champion}</td>
        <td>\${p.finalist}</td>
        <td class="chak-chak-cell">
          <span class="chak-chak-count">\${p.chakChak}</span>
          <div class="chak-chak-icon" data-index="${index}">${chakChakSvg}</div>
        </td>
      `;
      tableBody.appendChild(tr);
    });

    // --- ANIMATION ---
    const tl = anime.timeline({
      easing: 'easeOutQuad',
      duration: 600
    });

    // 1. Появление таблицы (каскадом)
    tl.add({
      targets: 'tbody tr',
      opacity: [0, 1],
      translateY: [20, 0],
      delay: anime.stagger(100, { from: 'first' }),
      duration: 400
    });

    // 2. Имитация прокрутки вниз (автоматически)
    tl.add({
      targets: scrollContainer,
      scrollTop: scrollContainer.scrollHeight,
      duration: 1500,
      easing: 'easeInOutCubic',
      begin: () => {
        document.querySelector('.scroll-hint').style.opacity = '0';
      },
      complete: () => {
        document.querySelector('.scroll-hint').style.display = 'none';
      }
    }, '-=200');

    // 3. Анимация появления чак-чака (эффект начисления)
    const icons = document.querySelectorAll('.chak-chak-icon');
    tl.add({
      targets: icons,
      opacity: [0, 1],
      scale: [0.5, 1],
      rotate: [-10, 10, -10, 10, 0],
      delay: anime.stagger(150, { from: 'first' }),
      duration: 300,
      easing: 'easeOutBack'
    }, '-=800');

    // 4. Пульсация чисел очков
    const counts = document.querySelectorAll('.chak-chak-count');
    tl.add({
      targets: counts,
      scale: [1, 1.2, 1],
      color: ['#fff', '#ffb000', '#fff'],
      delay: anime.stagger(150, { from: 'first' }),
      duration: 400,
      easing: 'easeOutBack'
    }, '-=200');

    // --- ADMIN LOGIC ---
    document.getElementById('adminBtn').addEventListener('click', () => {
      const pass = document.getElementById('adminPass').value;
      if (pass === 'admin') {
        alert('Админ панель активирована! (Здесь можно добавить логику редактирования таблицы)');
        // Здесь можно добавить функционал редактирования
      } else {
        alert('Неверный пароль');
        // Глитч-эффект при ошибке
        anime({
          targets: '.panel',
          rotate: [-5, 5, -5, 5, 0],
          duration: 300,
          easing: 'easeOutBack'
        });
      }
    });
  </script>
</body>
</html>
