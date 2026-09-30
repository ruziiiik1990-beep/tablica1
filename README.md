<!DOCTYPE html>
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
   max-width: 800px;
   margin: 0 auto;
 }
 .join-section {
   text-align: center;
   margin-bottom: 25px;
 }
 .btn-join {
   display: inline-block;
   padding: 16px 40px;
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
 .participants-table {
   width: 100%;
   border-collapse: separate;
   border-spacing: 0;
   background: rgba(10,20,50,0.55);
   backdrop-filter: blur(8px);
   -webkit-backdrop-filter: blur(8px);
   border-radius: 12px;
   overflow: hidden;
   border: 1px solid rgba(255,255,255,0.15);
   box-shadow: 0 8px 32px rgba(0,0,0,0.5);
 }
 .participants-table th {
   background: rgba(10,42,107,0.85);
   color: #fff;
   padding: 14px;
   text-align: left;
   font-weight: 700;
   font-size: 15px;
   text-shadow: 0 1px 2px rgba(0,0,0,0.5);
   border-bottom: 2px solid rgba(74,158,255,0.4);
 }
 .participants-table td {
   padding: 12px 14px;
   border-bottom: 1px solid rgba(255,255,255,0.08);
   font-size: 15px;
   color: #fff;
 }
 .participants-table tr:last-child td {
   border-bottom: none;
 }
 .participants-table tr:nth-child(even) td {
   background: rgba(255,255,255,0.04);
 }
 .row-number {
   font-weight: 800;
   color: #4a9eff;
   width: 60px;
   font-size: 16px;
   text-shadow: 0 0 6px rgba(74,158,255,0.5);
 }
 .bot-badge {
   display: inline-block;
   background: #e74c3c;
   color: #fff;
   font-size: 11px;
   padding: 2px 8px;
   border-radius: 4px;
   margin-left: 8px;
   font-weight: 600;
   box-shadow: 0 2px 6px rgba(231,76,60,0.4);
 }
 .no-participants {
   text-align: center;
   padding: 35px;
   color: rgba(255,255,255,0.6);
   font-size: 16px;
   font-style: italic;
 }
 .admin-panel {
   margin-top: 25px;
   padding: 22px;
   background: rgba(10,42,107,0.25);
   backdrop-filter: blur(6px);
   -webkit-backdrop-filter: blur(6px);
   border: 1px solid rgba(74,158,255,0.3);
   border-radius: 12px;
 }
 .admin-panel h3 {
   font-size: 17px;
   margin-bottom: 15px;
   color: #4a9eff;
   text-shadow: 0 0 8px rgba(74,158,255,0.4);
 }
 .admin-row {
   display: flex;
   gap: 10px;
   margin-bottom: 12px;
   flex-wrap: wrap;
 }
 .admin-row input {
   flex: 1;
   min-width: 150px;
   padding: 11px;
   border: 1px solid rgba(255,255,255,0.2);
   border-radius: 8px;
   background: rgba(0,0,0,0.35);
   color: #fff;
   font-size: 14px;
 }
 .admin-row input::placeholder { color: rgba(255,255,255,0.4); }
 .btn-admin {
   padding: 11px 20px;
   border: none;
   border-radius: 8px;
   font-weight: 600;
   cursor: pointer;
   font-size: 14px;
   transition: opacity 0.2s, transform 0.2s;
 }
 .btn-admin:hover { opacity: 0.85; transform: translateY(-1px); }
 .btn-add-bot { background: #e74c3c; color: #fff; }
 .del-btn {
   background: rgba(192,57,43,0.85);
   color: #fff;
   border: 1px solid rgba(255,255,255,0.2);
   padding: 5px 12px;
   border-radius: 6px;
   cursor: pointer;
   font-size: 12px;
   font-weight: 600;
   transition: background 0.2s;
 }
 .del-btn:hover { background: #c0392b; }
 .admin-hint {
   font-size: 13px;
   color: rgba(255,255,255,0.5);
   margin-top: 10px;
 }
 .admin-login-row {
   text-align: center;
   margin-top: 20px;
 }
 .admin-login-row input {
   padding: 10px 16px;
   border: 1px solid rgba(255,255,255,0.2);
   border-radius: 8px;
   background: rgba(0,0,0,0.35);
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
   color: #e74c3c;
   font-weight: 600;
   font-size: 15px;
   margin-bottom: 20px;
   text-shadow: 0 0 8px rgba(231,76,60,0.4);
 }
</style>
</head>
<body>

<div class="tournament-wrapper">

  <div id="joinSection" class="join-section"></div>

  <table class="participants-table">
    <thead>
      <tr>
        <th>№</th>
        <th>Участник</th>
        <th style="width:90px;">Действие</th>
      </tr>
    </thead>
    <tbody id="participantsBody">
      <tr><td colspan="3" class="no-participants">Пока нет участников. Будь первым!</td></tr>
    </tbody>
  </table>

  <div class="admin-panel" id="adminPanel" style="display:none;">
    <h3>Админ-панель</h3>
    <div class="admin-row">
      <input type="text" id="botName" placeholder="Имя бота" maxlength="20">
      <button class="btn-admin btn-add-bot" onclick="addBot()">Добавить бота</button>
    </div>
    <div class="admin-hint">Чтобы удалить участника — нажми «Удалить» в строке таблицы.</div>
  </div>

  <div class="admin-login-row">
    <input type="password" id="adminPassInput" placeholder="Админ-пароль" onkeydown="if(event.key==='Enter') toggleAdmin()">
    <button class="btn-join" style="padding:10px 24px; font-size:14px;" onclick="toggleAdmin()">Войти как админ</button>
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

const ADMIN_PASSWORD = '12$sacreD!';
let isAdmin = false;
let myId = null;
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
    section.innerHTML = '<p class="guest-warning">Войдите на сайт, чтобы участвовать в турнире</p>';
  }
})();

participantsRef.on('value', function(snapshot) {
  const data = snapshot.val() || {};
  const entries = Object.entries(data).sort(function(a, b) {
    return (a[1].order || 0) - (b[1].order || 0);
  });

  const tbody = document.getElementById('participantsBody');

  if (entries.length === 0) {
    tbody.innerHTML = '<tr><td colspan="3" class="no-participants">Пока нет участников. Будь первым!</td></tr>';
    document.getElementById('adminPanel').style.display = isAdmin ? 'block' : 'none';
    return;
  }

  let alreadyJoined = false;
  if (myNick) {
    alreadyJoined = entries.some(function(e) {
      return e[1].name && e[1].name.toLowerCase() === myNick.toLowerCase();
    });
  }
  if (alreadyJoined) {
    var myEntry = entries.find(function(e) {
      return e[1].name && e[1].name.toLowerCase() === myNick.toLowerCase();
    });
    var myNum = entries.indexOf(myEntry) + 1;
    var joinSec = document.getElementById('joinSection');
    joinSec.innerHTML = '<p class="joined-msg">Ты в игре! Твой номер: ' + myNum + '</p>';
  }

  let html = '';
  entries.forEach(function(entry, index) {
    const id = entry[0];
    const p = entry[1];
    const num = index + 1;
    const isBot = p.isBot === true;
    const delBtn = isAdmin
      ? '<button class="del-btn" onclick="removeParticipant(\'' + id + '\')">Удалить</button>'
      : '';
    html += '<tr>'
      + '<td class="row-number">' + num + ')</td>'
      + '<td>' + escapeHtml(p.name) + (isBot ? '<span class="bot-badge">БОТ</span>' : '') + '</td>'
      + '<td>' + delBtn + '</td>'
      + '</tr>';
  });
  tbody.innerHTML = html;

  document.getElementById('adminPanel').style.display = isAdmin ? 'block' : 'none';
});

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
    myId = newRef.key;
    newRef.set({
      name: myNick,
      isBot: false,
      order: maxOrder + 1,
      joinedAt: Date.now()
    });

    document.getElementById('joinSection').innerHTML =
      '<p class="joined-msg">Ты в игре! Твой номер: ' + (maxOrder + 1) + '</p>';
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
      alert('Админ-режим включён!');
    } else {
      alert('Неверный пароль!');
    }
  } else {
    isAdmin = false;
    alert('Админ-режим выключен.');
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
