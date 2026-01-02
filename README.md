<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <title>Yapay Zeka Web Sitesi</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    :root {
      --bg: #f4f4f4;
      --text: #222;
      --card: #ffffff;
      --primary: #4f46e5;
    }
    body.dark {
      --bg: #0f172a;
      --text: #e5e7eb;
      --card: #1e293b;
      --primary: #6366f1;
    }
    body {
      margin: 0;
      font-family: Arial, Helvetica, sans-serif;
      background: var(--bg);
      color: var(--text);
    }
    .container {
      max-width: 420px;
      margin: 60px auto;
      background: var(--card);
      padding: 24px;
      border-radius: 12px;
      box-shadow: 0 10px 25px rgba(0,0,0,.1);
    }
    h2, h3, h4 { text-align: center; }
    input, button, select {
      width: 100%;
      padding: 10px;
      margin-top: 10px;
      border-radius: 8px;
      border: 1px solid #ccc;
    }
    button {
      background: var(--primary);
      color: white;
      border: none;
      cursor: pointer;
    }
    button.secondary {
      background: transparent;
      color: var(--primary);
    }
    .link {
      text-align: center;
      margin-top: 10px;
      cursor: pointer;
      color: var(--primary);
    }
    .hidden { display: none; }
    .topbar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
    }
    .chat {
      background: var(--bg);
      padding: 12px;
      border-radius: 8px;
      min-height: 120px;
      margin-bottom: 10px;
      font-size: 14px;
    }
  </style>
</head>
<body>

<!-- GİRİŞ -->
<div class="container" id="loginBox">
  <h2>Giriş Yap</h2>
  <input id="loginUser" placeholder="Kullanıcı Adı" />
  <input id="loginPass" type="password" placeholder="Şifre" />
  <button onclick="login()">Giriş Yap</button>
  <div class="link" onclick="showRegister()">Kayıt Ol</div>
</div>

<!-- KAYIT -->
<div class="container hidden" id="registerBox">
  <h2>Kayıt Ol</h2>
  <input id="regUser" placeholder="Kullanıcı Adı" />
  <input id="regPass" type="password" placeholder="Şifre" />
  <button onclick="register()">Kayıt Ol</button>
  <div class="link" onclick="showLogin()">Girişe Dön</div>
</div>

<!-- PANEL -->
<div class="container hidden" id="panelBox">
  <div class="topbar">
    <strong id="welcome"></strong>
    <button class="secondary" onclick="openSettings()">⚙️ Ayarlar</button>
  </div>

  <h3>🧠 Yapay Zeka Sohbet</h3>
  <div class="chat" id="messages"></div>

  <input id="aiInput" placeholder="Bir soru sor veya görev yaz..." />
  <button onclick="askAI()">Gönder</button>
  <button onclick="buildSite()" style="margin-top:10px">🌐 Siteyi Otomatik Kur</button>
  <button onclick="buildApp()" style="margin-top:10px">📱 Uygulamayı Otomatik Kur</button>

  <!-- AYARLAR -->
  <div id="settings" class="hidden">
    <h4>⚙️ Ayarlar</h4>
    <label>Cevap Stili</label>
    <select id="style">
      <option value="normal">Normal</option>
      <option value="samimi">Samimi</option>
      <option value="resmi">Resmi</option>
    </select>

    <label>Cevap Uzunluğu</label>
    <select id="length">
      <option value="kisa">Kısa</option>
      <option value="orta" selected>Orta</option>
      <option value="uzun">Uzun</option>
    </select>

    <button onclick="toggleTheme()">🌙 Tema Değiştir</button>
    <button onclick="closeSettings()">Kapat</button>
  </div>

  <hr />
  <input id="newPass" type="password" placeholder="Yeni Şifre" />
  <button onclick="changePassword()">Şifre Değiştir</button>
  <button onclick="logout()" style="margin-top:10px">Çıkış Yap</button>
</div>

<script>
// === DOM HAZIR OLUNCA BAŞLAT ===
document.addEventListener('DOMContentLoaded', () => {
  const loginBox = document.getElementById('loginBox');
  const registerBox = document.getElementById('registerBox');
  const panelBox = document.getElementById('panelBox');
  const welcome = document.getElementById('welcome');
  const messages = document.getElementById('messages');
  const aiInput = document.getElementById('aiInput');
  const styleSelect = document.getElementById('style');
  const lengthSelect = document.getElementById('length');
  const settings = document.getElementById('settings');

  const users = JSON.parse(localStorage.getItem('users') || '{}');
  let currentUser = null;

  function saveUsers(){ localStorage.setItem('users', JSON.stringify(users)); }

  window.showRegister = () => { loginBox.classList.add('hidden'); registerBox.classList.remove('hidden'); };
  window.showLogin = () => { registerBox.classList.add('hidden'); loginBox.classList.remove('hidden'); };

  window.register = () => {
    const u = regUser.value.trim();
    const p = regPass.value;
    if (!u || !p) return alert('Bilgiler eksik');
    if (users[u]) return alert('Bu kullanıcı zaten var');
    users[u] = p; saveUsers(); alert('Kayıt başarılı'); showLogin();
  };

  window.login = () => {
    const u = loginUser.value.trim();
    const p = loginPass.value;
    if (!users[u]) return alert('❌ Kullanıcı bulunamadı');
    if (users[u] !== p) return alert('❌ Şifre hatalı');
    currentUser = u;
    loginBox.classList.add('hidden'); panelBox.classList.remove('hidden');
    welcome.innerText = 'Hoşgeldin ' + u;
  };

  window.logout = () => { currentUser = null; panelBox.classList.add('hidden'); loginBox.classList.remove('hidden'); };

  window.changePassword = () => {
    if (!newPass.value) return alert('Yeni şifre gir');
    users[currentUser] = newPass.value; saveUsers(); alert('Şifre değiştirildi'); newPass.value='';
  };

  window.askAI = () => {
    const q = aiInput.value.trim(); if (!q) return;
    messages.innerHTML += `<div><b>👤 Sen:</b> ${q}</div>`;
    messages.innerHTML += `<div><b>🤖 AI:</b> ${normalChatResponse(q)}</div>`;
    aiInput.value='';
  };

  function normalChatResponse(q){
    let base='';
    if(styleSelect.value==='samimi') base='🙂 ';
    if(styleSelect.value==='resmi') base='Bilgilendirme: ';
    let ans='Bu konuda sana yardımcı olmaya çalışırım.';
    if(q.toLowerCase().includes('merhaba')) ans='Merhaba! Sana nasıl yardımcı olabilirim?';
    if(q.toLowerCase().includes('yapay zeka')) ans='Yapay zeka, makinelerin öğrenmesini sağlayan teknolojidir.';
    if(lengthSelect.value==='uzun') ans+=' Daha detay istersen anlatabilirim.';
    if(lengthSelect.value==='kisa') ans=ans.split('.')[0]+'.';
    return base+ans;
  }

  window.buildSite = () => {
    const html = `<!DOCTYPE html><html><body><h1>AI Site</h1><p>Kullanıcı: ${currentUser}</p></body></html>`;
    downloadFile('ai-site.html', html);
    messages.innerHTML += `<div><b>🌐 Sistem:</b> Site oluşturuldu.</div>`;
  };

  window.buildApp = () => {
    downloadFile('AIApp.jsx', 'export default function App(){}');
    messages.innerHTML += `<div><b>📱 Sistem:</b> Uygulama taslağı oluşturuldu.</div>`;
  };

  function downloadFile(name, content){
    const blob=new Blob([content]);
    const a=document.createElement('a');
    a.href=URL.createObjectURL(blob); a.download=name; a.click();
    URL.revokeObjectURL(a.href);
  }

  window.openSettings = () => settings.classList.remove('hidden');
  window.closeSettings = () => settings.classList.add('hidden');
  window.toggleTheme = () => document.body.classList.toggle('dark');
});
</script>
</body>
</html>
