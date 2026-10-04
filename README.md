<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Vertical Glitch Table</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');

    :root {
      --bg: #050505;
      --panel: #12121a;
      --accent: #00fff9;
      --error: #ff004c;
      --gold: #ffb000;
      --text: #e0e0ff;
      --border: #333;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'VT323', monospace;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      overflow: hidden; /* Чтобы не было полос прокрутки у окна */
    }

    /* --- Glitch Overlay --- */
    .glitch-overlay {
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      pointer-events: none;
      z-index: 999;
      overflow: hidden;
    }
    
    .scanlines {
      position: absolute; top: 0; left: 0; width: 100%; height: 100%;
      background: linear-gradient(rgba(0,0,0,0) 50%, rgba(0,0,0,0.15) 50%);
      background-size: 100% 4px;
      animation: scan-anim 10s linear infinite;
      opacity: 0.6;
    }

    @keyframes scan-anim { 0% { background-position: 0 0; } 100% { background-position: 0 100%; } }

    /* --- Table Container --- */
    .table-wrapper {
      width: 600px;
      background: var(--panel);
      border: 2px solid var(--border);
      box-shadow: 0 0 30px rgba(0, 255, 249, 0.1);
      padding: 20px;
      position: relative;
      z-index: 10;
    }

    h2 {
      text-align: center;
      color: var(--accent);
      text-shadow: 0 0 10px var(--accent);
      margin-bottom: 20px;
      text-transform: uppercase;
      letter-spacing: 2px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
    }

    th, td {
      padding: 12px;
      text-align: center;
      border-bottom: 1px solid #444;
      position: relative;
      overflow: hidden;
    }

    th {
      color: #888;
      font-size: 12px;
      text-transform: uppercase;
    }

    /* --- Vertical Animation Targets --- */
    
    /* Скрываем строки изначально */
    tbody tr {
      opacity: 0;
      transform: translateY(-50px); /* Сдвиг вверх для вертикального появления */
      will-change: opacity, transform;
    }

    /* Ячейка с наградой */
    .reward-cell {
      position: relative;
    }

    .chak-chak-icon {
      width: 24px;
      height: 24px;
      opacity: 0;
      transform: scale(0.5) rotate(45deg);
      filter: drop-shadow(0 0 4px rgba(255, 176, 0, 0.8));
      position: absolute;
      top: 50%;
      left: 50%;
      transform-origin: center;
      will-change: opacity, transform;
    }

    .chak-chak-count {
      color: #fff;
      font-weight: bold;
      z-index: 2;
      position: relative;
      display: inline-block;
    }

    /* --- Admin Panel --- */
    .admin-area {
      margin-top: 20px;
      display: flex;
      justify-content: center;
      gap: 10px;
    }
    
    input {
      background: #000;
      border: 1px solid #444;
      color: white;
      padding: 8px;
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
    }
    button:hover {
      box-shadow: 0 0 15px var(--accent);
    }
  </style>
</head>
<body>

  <div class="glitch-overlay">
    <div class="scanlines"></div>
  </div>

  <div class="table-wrapper">
    <h2>ТУРНИРНАЯ ТАБЛИЦА</h2>
    
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
      <tbody id="table-body">
        <!-- Строки генерируются JS -->
      </tbody>
    </table>

    <div class="admin-area">
      <input type="password" id="pass" placeholder="Пароль">
      <button onclick="toggleAdmin()">Войти</button>
    </div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.1/anime.min.js"></script>
  <script>
    // --- ДАННЫЕ ---
    const players = [
      { id: 1, name: '45', champ: 1, final: 0, reward: 2 },
      { id: 2, name: '123', champ: 1, final: 0, reward: 2 },
      { id: 3, name: 'qweqwe', champ: 1, final: 0, reward: 2 },
      { id: 4, name: 'hhh', champ: 1, final: 0, reward: 2 },
      { id: 5, name: 'asdxzc3', champ: 1, final: 0, reward: 2 },
      { id: 6, name: 'chesalova20013', champ: 1, final: 0, reward: 2 },
      { id: 7, name: 'fg', champ: 1, final: 0, reward: 2 }
    ];

    const tbody = document.getElementById('table-body');
    
    // SVG иконка чак-чака (встроенная)
    const iconSvg = `<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <circle cx="12" cy="12" r="10" fill="#FFB000" stroke="#D4A017" stroke-width="2"/>
      <path d="M12 6C10.8954 6 10 6.89543 10 8C10 9.10457 10.8954 10 12 10C13.1046 10 14 9.10457 14 8C14 6.89543 13.1046 6 12 6Z" fill="#FFF" opacity="0.8"/>
      <path d="M12 14C10.8954 14 10 14.8954 10 16C10 17.1046 10.8954 18 12 18C13.1046 18 14 17.1046 14 16C14 14.8954 13.1046 14 12 14Z" fill="#FFF" opacity="0.6"/>
    </svg>`;

    // --- ГЕНЕРАЦИЯ HTML ---
    players.forEach((p, index) => {
      const tr = document.createElement('tr');
      // Важно: мы не показываем строки сразу, они скрыты через CSS (opacity 0, translateY)
      tr.innerHTML = `
        <td>\${p.id}</td>
        <td><span style="color:var(--accent)">\${p.name}</span></td>
        <td>\${p.champ}</td>
        <td>\${p.final}</td>
        <td class="reward-cell">
          <span class="chak-chak-count">\${p.reward}</span>
          <div class="chak-chak-icon">\${iconSvg}</div>
        </td>
      `;
      tbody.appendChild(tr);
    });

    // --- ВЕРТИКАЛЬНАЯ АНИМАЦИЯ (ГЛАВНОЕ) ---
    const tl = anime.timeline({
      delay: 500, // Пауза перед стартом
      easing: 'easeOutQuad'
    });

    // 1. Вертикальное появление строк (каскад сверху вниз)
    // translateY: -50px -> 0 (движение вниз)
    // stagger: 150ms задержка между строками
    tl.add({
      targets: 'tbody tr',
      opacity: [0, 1],
      translateY: [50, 0], // Движение сверху вниз
      delay: anime.stagger(150, { from: 'first' }), 
      duration: 400
    });

    // 2. Анимация начисления наград (тоже вертикально, по очереди)
    // Сначала иконка выпрыгивает, потом цифра мигает
    tl.add({
      targets: '.chak-chak-icon',
      opacity: [0, 1],
      scale: [0.5, 1],
      rotate: [45, 0],
      delay: anime.stagger(200, { from: 'first' }), // Чуть медленнее, чем строки
      duration: 300,
      easing: 'easeOutBack'
    }, '-=200'); // Начинаем чуть раньше, чем закончится появление строк

    // 3. Пульсация цифр (эффект начисления)
    tl.add({
      targets: '.chak-chak-count',
      scale: [1, 1.3, 1],
      color: ['#fff', '#ffb000', '#fff'],
      delay: anime.stagger(200, { from: 'first' }),
      duration: 400,
      easing: 'easeOutBack'
    }, '-=300');

    // --- АДМИН ПАНЕЛЬ ---
    function toggleAdmin() {
      const pass = document.getElementById('pass').value;
      if (pass === 'admin') {
        alert('Админ режим: здесь можно менять данные в массиве players');
        // Можно добавить логику редактирования
      } else {
        // Глитч эффект при ошибке
        anime({
          targets: '.table-wrapper',
          rotate: [-5, 5, -5, 5, 0],
          duration: 200,
          easing: 'easeOutBack'
        });
      }
    }
  </script>
</body>
</html>
