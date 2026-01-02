#      hello world
<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <title>AI Chat – ChatGPT Tarzı</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    :root {
      --bg: #f7f7f8;
      --sidebar: #ececf1;
      --panel: #ffffff;
      --text: #111827;
      --primary: #10a37f;
    }
    body.dark {
      --bg: #343541;
      --sidebar: #202123;
      --panel: #444654;
      --text: #e5e7eb;
      --primary: #10a37f;
    }
    * { box-sizing: border-box; }
    body {
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
      background: var(--bg);
      color: var(--text);
      height: 100vh;
      display: flex;
    }

    /* === SOL SIDEBAR === */
    .sidebar {
      width: 260px;
      background: var(--sidebar);
      display: flex;
      flex-direction: column;
      border-right: 1px solid rgba(0,0,0,.1);
    }
    .sidebar h2 {
      padding: 16px;
      margin: 0;
      font-size: 16px;
    }
    .history {
      flex: 1;
      overflow-y: auto;
    }
    .history div {
      padding: 12px 16px;
      cursor: pointer;
      border-bottom: 1px solid rgba(0,0,0,.05);
    }
    .history div:hover { background: rgba(0,0,0,.05); }

    .sidebar-bottom {
      padding: 12px;
      border-top: 1px solid rgba(0,0,0,.1);
    }
    .sidebar-bottom button {
      width: 100%;
      padding: 10px;
      border-radius: 8px;
      border: none;
      background: var(--panel);
      cursor: pointer;
    }

    /* === ANA PANEL === */
    .main {
      flex: 1;
      display: flex;
      flex-direction: column;
    }
    .topbar {
      padding: 14px 20px;
      border-bottom: 1px solid rgba(0,0,0,.1);
      font-weight: 600;
    }
    .chat {
      flex: 1;
      padding: 20px;
      overflow-y: auto;
    }
    .msg {
      max-width: 700px;
      margin-bottom: 16px;
    }
    .user { font-weight: 600; }
    .ai { color: var(--primary); }

    .input-area {
      padding: 16px;
      border-top: 1px solid rgba(0,0,0,.1);
      display: flex;
      gap: 10px;
    }
    .input-area input {
      flex: 1;
      padding: 12px;
      border-radius: 10px;
      border: 1px solid #ccc;
    }
    .input-area button {
      padding: 12px 18px;
      border-radius: 10px;
      border: none;
      background: var(--primary);
      color: white;
      cursor: pointer;
    }

    /* === AYARLAR MODAL === */
    .modal {
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,.4);
      display: none;
      align-items: center;
      justify-content: center;
    }
    .modal-content {
      background: var(--panel);
      padding: 20px;
      border-radius: 12px;
      width: 300px;
    }
    .modal-content select, .modal-content button {
      width: 100%;
      margin-top: 10px;
      padding: 10px;
    }
  </style>
</head>
<body>

<div class="sidebar">
  <h2>💬 Sohbetler</h2>
  <div class="history" id="history"></div>
  <div class="sidebar-bottom">
    <button onclick="openSettings()">⚙️ Ayarlar</button>
  </div>
</div>

<div class="main">
  <div class="topbar">🤖 Yapay Zeka Sohbet</div>
  <div class="chat" id="chat"></div>
  <div class="input-area">
    <input id="input" placeholder="Mesaj yaz…" />
    <button onclick="send()">Gönder</button>
  </div>
</div>

<div class="modal" id="settings">
  <div class="modal-content">
    <h3>Ayarlar</h3>
    <select id="style">
      <option value="normal">Normal</option>
      <option value="samimi">Samimi</option>
      <option value="resmi">Resmi</option>
    </select>
    <select id="length">
      <option value="kisa">Kısa</option>
      <option value="orta" selected>Orta</option>
      <option value="uzun">Uzun</option>
    </select>
    <button onclick="toggleTheme()">🌙 Tema Değiştir</button>
    <button onclick="closeSettings()">Kapat</button>
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', () => {
  const chat = document.getElementById('chat');
  const input = document.getElementById('input');
  const historyBox = document.getElementById('history');
  const settings = document.getElementById('settings');

  let chats = JSON.parse(localStorage.getItem('chats') || '[]');

  function renderHistory() {
    historyBox.innerHTML = '';
    chats.forEach((c, i) => {
      const d = document.createElement('div');
      d.textContent = c.title;
      d.onclick = () => loadChat(i);
      historyBox.appendChild(d);
    });
  }

  function loadChat(i) {
    chat.innerHTML = '';
    chats[i].messages.forEach(m => {
      chat.innerHTML += `<div class='msg'><span class='${m.role}'>${m.role === 'user' ? 'Sen' : 'AI'}:</span> ${m.text}</div>`;
    });
  }

  window.send = () => {
    const text = input.value.trim();
    if (!text) return;
    const ai = respond(text);

    const convo = { title: text.slice(0, 20), messages: [
      { role: 'user', text },
      { role: 'ai', text: ai }
    ]};

    chats.unshift(convo);
    localStorage.setItem('chats', JSON.stringify(chats));
    renderHistory();
    loadChat(0);
    input.value = '';
  };

  function respond(q) {
    if (q.toLowerCase().includes('merhaba')) return 'Merhaba! Sana nasıl yardımcı olabilirim?';
    if (q.toLowerCase().includes('yapay zeka')) return 'Yapay zeka, makinelerin öğrenmesini sağlayan teknolojidir.';
    return 'Sorunu anladım. Biraz daha açmak ister misin?';
  }

  window.openSettings = () => settings.style.display = 'flex';
  window.closeSettings = () => settings.style.display = 'none';
  window.toggleTheme = () => document.body.classList.toggle('dark');

  renderHistory();
});
</script>
</body>
</html>
