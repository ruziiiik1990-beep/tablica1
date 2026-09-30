<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Турнирная таблица</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
 * { margin: 0; padding: 0; box-sizing: border-box; }
 html, body { height: 100%; }
 body {
   font-family: 'Inter', sans-serif;
   background-image: url('https://4ak4ak.moy.su/kolo13.jpg');
   background-size: cover;
   background-position: center;
   background-repeat: no-repeat;
   background-attachment: fixed;
   color: #fff;
   padding: 20px;
   min-height: 100vh;
 }
 .tournament-wrapper {
   max-width: 1400px;
   margin: 0 auto;
 }
 .join-section {
   text-align: center;
   margin-bottom: 25px;
 }
 .btn-join {
   display: inline-block;
   padding: 16px 44px;
   font-size: 18px;
   font-weight: 700;
   color: #fff;
   background: linear-gradient(135deg, #0a2a6b, #1a4a8b);
   border: 2px solid rgba(255,255,255,0.3);
   border-radius: 50px;
   cursor: pointer;
   transition: transform 0.2s, box-shadow 0.2s, background 0.3s;
   box-shadow: 0 4px 14px rgba(10,42,107,0.4);
 }
 .btn-join:hover {
   transform: translateY(-2px);
   box-shadow: 0 8px 22px rgba(10,42,107,0.6);
   background: linear-gradient(135deg, #1a4a8b, #2a6abb);
 }
 .btn-join:disabled {
   opacity: 0.5;
   cursor: not-allowed;
   transform: none;
 }
 .columns-container {
   display: flex;
   flex-wrap: wrap;
   gap: 20px;
   justify-content: center;
 }
 .column-table {
   flex: 1 1 380px;
   min-width: 320px;
   max-width: 500px;
   border-collapse: separate;
   border-spacing: 0;
   background: rgba(10,20,50,0.6);
   backdrop-filter: blur(10px);
   -webkit-backdrop-filter: blur(10px);
   border-radius: 14px;
   overflow: hidden;
   border: 1px solid rgba(74,158,255,0.25);
   box-shadow: 0 10px 40px rgba(0,0,0,0.6), 0 0 0 1px rgba(255,255,255,0.05) inset;
 }
 .column-table th {
   background: linear-gradient(135deg, #0a2a6b, #1a4a8b);
   color: #fff;
   padding: 14px 16px;
   text-align: left;
   font-weight: 800;
   font-size: 14px;
   text-shadow: 0 1px 3px rgba(0,0,0,0.6);
   border-bottom: 2px solid rgba(74,158,255,0.5);
   letter-spacing: 0.5px;
 }
 .column-table td {
   padding: 12px 16px;
   border-bottom: 1px solid rgba(255,255,255,0.07);
   font-size: 14px;
   color: #fff;
 }
 .column-table tr:last-child td {
   border-bottom: none;
 }
 .column-table tr:nth-child(even) td {
   background: rgba(255,255,255,0.04);
 }
 .column-table tr:hover td {
   background: rgba(74,158,255,0.08);
   transition: background 0.2s;
 }
 .row-number {
   width: 50px;
   text-align: center;
 }
 .number-highlight {
   font-weight: 800;
   background: linear-gradient(180deg, transparent 45%, #0a2a6bcc 45%);
   padding: 3px 8px;
   border-radius: 5px;
   color: #fff;
   text-shadow: 0 0 5px #0a2a6b, 0 0 10px #0a2a6b;
   display: inline-block;
   font-size: 16px;
 }
 .nick-highlight {
   font-weight: 800;
   background: linear-gradient(180deg, transparent 45%, #0a2a6bcc 45%);
   padding: 3px 8px;
   border-radius: 5px;
   color: #fff;
   text-shadow: 0 0 5px #0a2a6b, 0 0 10px #0a2a6b;
   display: inline-block;
 }
 .bot-badge {
   display: inline-block;
   background: linear-gradient(135deg, #e74c3c, #c0392b);
   color: #fff;
   font-size: 11px;
   padding: 3px 10px;
   border-radius: 6px;
   margin-left: 8px;
   font-weight: 700;
   box-shadow: 0 2px 8px rgba(231,76,60,0.5);
   text-transform: uppercase;
   letter-spacing: 0.5px;
 }
 .no-participants {
   text-align: center;
   padding: 40px;
   color: rgba(255,255,255,0.6);
   font-size: 16px;
   font-style: italic;
 }
 .admin-panel {
   margin-top: 25px;
   padding: 24px;
   background: rgba(10,42,107,0.3);
   backdrop-filter: blur(8px);
   -webkit-backdrop-filter: blur(8px);
   border: 1px solid rgba(74,158,255,0.35);
   border-radius: 14px;
   box-shadow: 0 4px 20px rgba(10,42,107,0.3);
 }
 .admin-panel h3 {
   font-size: 18px;
   margin-bottom: 16px;
   color: #4a9eff;
   text-shadow: 0 0 10px rgba(74,158,255,0.5);
   font-weight: 700;
 }
 .admin-row {
   display: flex;
   gap: 10px;
   margin-bottom: 12px;
   flex-wrap: wrap;
   align-items: center;
 }
 .admin-row input {
   flex: 1;
   min-width: 150px;
   padding: 12px 14px;
   border: 1px solid rgba(255,255,255,0.2);
   border-radius: 8px;
   background: rgba(0,0,0,0.4);
   color: #fff;
   font-size: 14px;
 }
 .admin-row input::placeholder { color: rgba(255,255,255,0.4); }
 .btn-admin {
   padding: 12px 22px;
   border: none;
   border-radius: 8px;
   font-weight: 600;
   cursor: pointer;
   font-size: 14px;
   transition: opacity 0.2s, transform 0.2s;
 }
 .btn-admin:hover { opacity: 0.85; transform: translateY(-1px); }
 .btn-add-bot {
   background: linear-gradient(135deg, #e74c3c, #c0392b);
   color: #fff;
   box-shadow: 0 3px 10px rgba(231,76,60,0.4);
 }
 .del-btn {
   background: rgba(192,57,43,0.85);
   color: #fff;
   border: 1px solid rgba(255,255,255,0.2);
   padding: 6px 14px;
   border-radius: 6px;
   cursor: pointer;
   font-size: 12px;
   font-weight: 600;
   transition: background 0.2s, transform 0.2s;
 }
 .del-btn:hover { background: #c0392b; transform: scale(1.05); }
 .admin-hint {
   font-size: 13px;
   color: rgba(255,255,255,0.5);
   margin-top: 10px;
 }
 .admin-login-row {
   text-align: center;
   margin-top: 20px;
   display: flex;
   justify-content: center;
   gap: 10px;
   flex-wrap: wrap;
 }
 .admin-login-row input {
   padding: 12px 18px;
   border: 1px solid rgba(255,255,255,0.2);
   border-radius: 8px;
   background: rgba(0,0,0,0.4);
   color: #fff;
   font-size: 14px;
   width: 200px;
 }
 .admin-login-row input::placeholder { color: rgba(255,255,255,0.4); }
 .joined-msg {
   text-align: center;
   color: #4a9eff;
   font-weight: 700;
   font-size: 18px;
   margin-bottom: 25px;
   text-shadow: 0 0 10px rgba(74,158,255,0.5);
 }
 .guest-warning {
   text-align: center;
   margin-bottom: 20px;
 }
 .guest-warning-text {
   font-weight: 800;
   background: linear-gradient(180deg, transparent 45%, #0a2a6bcc 45%);
   padding: 3px 10px;
   border-radius: 5px;
   color: #fff;
   text-shadow: 0 0 5px #0a2a6b, 0 0 10px #0a2a6b;
   display: inline-block;
   font-size: 15px;
 }
 .admin-active-badge {
   display: inline-block;
   background: linear-gradient(135deg, #27ae60, #2ecc71);
   color: #fff;
   font-size: 12px;
   padding: 4px 12px;
   border-radius: 20px;
   font-weight: 700;
   margin-left: 10px;
   box-shadow: 0 2px 8px rgba(39,174,96,0.4);
 }
</style>
</head>
<body>

<div class="tournament-wrapper">

  <div id="joinSection" class="join-section"></div>

  <div id="participantsContainer" class="columns-container"></div>

  <div class="admin-login-row">
    <input type="password" id="adminPassInput" placeholder="Админ-пароль" onkeydown="if(event.key==='Enter') toggleAdmin()">
    <button class="btn-join" style="padding:12px 26px; font-size:14px;" onclick="toggleAdmin()">Войти как админ</button>
  </div>

  <div class="admin-panel" id="adminPanel" style="display:none;">
    <h3>Админ-панель <span class="admin-active-badge" id="adminBadge">АКТИВЕН</span></h3>
    <div class="admin-row">
      <input type="text" id="botName" placeholder="Имя бота" maxlength="20" onkeydown="if(event.key==='Enter') addBot()">
      <button class="btn-admin btn-add-bot" onclick="addBot()">Добавить бота</button>
    </div>
    <div class="admin-hint">Чтобы удалить участника — нажми «Удалить» в строке таблицы.</div>
  </div>
</div>

<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database-compat.js"></script>

<script>
const firebaseConfig = {
  apiKey: "AIzaSyCNQ0WFAiQjnISQjXJnHoln-wI64G2BqWs",
  authDomain: "ak4ak-d948e.firebaseapp.com",
  databaseURL: "https://ak4ak-d948e-default-rtdb.firebaseio.com",
  projectId: "ak4ak-d948e",
  storageBucket: "ak4ak-d948e.firebasestorage.app",
  messagingSenderId: "787151252619",
  appId: "1:787151252619:web:05eff65dc74b01d6e8f88e",
  measurementId: "G-DBB4YBNF2Q"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();
const participantsRef = db.ref('tournament/participants');

const ADMIN_PASSWORD = '12$sacreD';
const MAX_PER_COLUMN = 20;
let isAdmin = false;
let myNick = null;

function getUrlParam(name) {
  var url = new URL(window.location.href);
  return url.searchParams.get(name);
}

myNick = getUrlParam('user');

(function initJoinSection() {
  var section = document.getElementById('joinSection');
  if (myNick && myNick !== 'null' && myNick !== '') {
    section.innerHTML = '<button class="btn-join" onclick="joinTournament()">Участвовать</button>';
  } else {
    section.innerHTML = '<p class="guest-warning"><span class="guest-warning-text">Войдите на сайт, чтобы участвовать в турнире</span></p>';
  }
})();

function renderTable(snapshot) {
  const data = snapshot.val() || {};
  const entries = Object.entries(data).sort(function(a, b) {
    return (a[1].order || 0) - (b[1].order || 0);
  });

  const container = document.getElementById('participantsContainer');

  if (entries.length === 0) {
    container.innerHTML = '<table class="column-table" style="max-width:500px;"><tbody>'
      + '<tr><td colspan="3" class="no-participants">Пока нет участников. Будь первым!</td></tr>'
      + '</tbody></table>';
    return;
  }

  // Обновляем секцию участия
  if (myNick) {
    let alreadyJoined = entries.some(function(e) {
      return e[1].name && e[1].name.toLowerCase() === myNick.toLowerCase();
    });
    if (alreadyJoined) {
      var myEntry = entries.find(function(e) {
        return e[1].name && e[1].name.toLowerCase() === myNick.toLowerCase();
      });
      var myNum = entries.indexOf(myEntry) + 1;
      var joinSec = document.getElementById('joinSection');
      joinSec.innerHTML = '<p class="joined-msg">Ты в игре! Твой номер: ' + myNum + '</p>';
    }
  }

  // Разбиваем на колонки по MAX_PER_COLUMN
  var columns = [];
  for (var i = 0; i < entries.length; i += MAX_PER_COLUMN) {
    columns.push(entries.slice(i, i + MAX_PER_COLUMN));
  }

  var html = '';
  columns.forEach(function(chunk) {
    html += '<table class="column-table"><thead><tr>'
      + '<th style="text-align:center;">№</th>'
      + '<th>Участник</th>'
      + '<th style="width:80px;">Действие</th>'
      + '</tr></thead><tbody>';

    chunk.forEach(function(entry, index) {
      var globalIndex = columns.indexOf(chunk) * MAX_PER_COLUMN + index;
      var id = entry[0];
      var p = entry[1];
      var num = globalIndex + 1;
      var isBot = p.isBot === true;
      var delBtn = isAdmin
        ? '<button class="del-btn" onclick="removeParticipant(\'' + id + '\')">Удалить</button>'
        : '';
      html += '<tr>'
        + '<td class="row-number"><span class="number-highlight">' + num + ')</span></td>'
        + '<td><span class="nick-highlight">' + escapeHtml(p.name) + '</span>' + (isBot ? '<span class="bot-badge">БОТ</span>' : '') + '</td>'
        + '<td>' + delBtn + '</td>'
        + '</tr>';
    });

    html += '</tbody></table>';
  });

  container.innerHTML = html;
  document.getElementById('adminPanel').style.display = isAdmin ? 'block' : 'none';
}

participantsRef.on('value', renderTable);

function joinTournament() {
  if (!myNick) {
    alert('Войдите на сайт, чтобы участвовать!');
    return;
  }

  participantsRef.once('value').then(function(snapshot) {
    const data = snapshot.val() || {};
    const entries = Object.values(data);
    const maxOrder = entries.length > 0
      ? Math.max.apply(null, entries.map(function(e) { return e.order || 0; }))
      : 0;

    const exists = entries.some(function(e) {
      return e.name && e.name.toLowerCase() === myNick.toLowerCase();
    });
    if (exists) {
      alert('Ты уже в таблице!');
      return;
    }

    const newRef = participantsRef.push();
    newRef.set({
      name: myNick,
      isBot: false,
      order: maxOrder + 1,
      joinedAt: Date.now()
    });
  });
}

function addBot() {
  if (!isAdmin) return;
  const name = document.getElementById('botName').value.trim();
  if (!name) {
    alert('Введите имя бота!');
    return;
  }

  participantsRef.once('value').then(function(snapshot) {
    const data = snapshot.val() || {};
    const entries = Object.values(data);
    const maxOrder = entries.length > 0
      ? Math.max.apply(null, entries.map(function(e) { return e.order || 0; }))
      : 0;

    const newRef = participantsRef.push();
    newRef.set({
      name: name,
      isBot: true,
      order: maxOrder + 1,
      joinedAt: Date.now()
    });

    document.getElementById('botName').value = '';
  });
}

function removeParticipant(id) {
  if (!isAdmin) return;
  participantsRef.child(id).remove();
}

function toggleAdmin() {
  const pass = document.getElementById('adminPassInput').value;
  if (!isAdmin) {
    if (pass === ADMIN_PASSWORD) {
      isAdmin = true;
      document.getElementById('adminPassInput').value = '';
      document.getElementById('adminPanel').style.display = 'block';
      alert('Админ-режим включён!');
      participantsRef.once('value').then(renderTable);
    } else {
      alert('Неверный пароль!');
    }
  } else {
    isAdmin = false;
    document.getElementById('adminPanel').style.display = 'none';
    alert('Админ-режим выключен.');
    participantsRef.once('value').then(renderTable);
  }
}

function escapeHtml(text) {
  var div = document.createElement('div');
  div.textContent = text;
  return div.innerHTML;
}
</script>

</body>
</html>
