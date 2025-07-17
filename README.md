
<html lang="fa" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>VIPZEXNET</title>
  <style>
    body { font-family: sans-serif; padding: 20px; transition: background 0.3s, color 0.3s; }
    .dark { background: #111; color: #fff; }
    .light { background: #fff; color: #000; }
    .config-box { background: #1e1e1e; border: 1px solid #333; padding: 15px; border-radius: 12px; margin-bottom: 20px; }
    .light .config-box { background: #f1f1f1; border: 1px solid #ccc; }
    .btn, .toggle-btn { margin-top: 10px; background: #00c853; color: white; padding: 10px 15px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; }
    .btn:hover, .toggle-btn:hover { background: #00e676; }
    .title { font-size: 20px; margin-bottom: 5px; color: #ffd600; }
    .light .title { color: #ff6f00; }
    .footer { text-align: center; margin-top: 40px; color: #999; }
    a { color: #00b0ff; text-decoration: none; }
    .settings-panel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); color: white; display: none; flex-direction: column; justify-content: center; align-items: center; z-index: 1000; }
    .settings-content { background: #222; padding: 20px; border-radius: 12px; text-align: center; }
    .settings-content h2 { margin-bottom: 20px; }
    .close-btn { margin-top: 20px; background: #ff5252; }
    .copied-msg { display: none; position: fixed; bottom: 20px; right: 20px; background: #00c853; color: white; padding: 10px 15px; border-radius: 8px; z-index: 1001; }
    .qr-code { margin-top: 10px; }
    .rating span { font-size: 22px; cursor: pointer; color: gold; }
    .search-bar { width: 100%; padding: 10px; margin-bottom: 20px; font-size: 16px; border-radius: 8px; border: 1px solid #ccc; }
    .vip-label { background: #ff6f00; color: white; padding: 4px 8px; border-radius: 6px; font-size: 13px; display: inline-block; margin-right: 10px; }
  </style>
</head>
<body class="dark" id="body">

  <h1 id="app-title">🚀 برنامه VIPZEXNET</h1>
  <input type="text" class="search-bar" placeholder="جستجو در سرورها..." onkeyup="searchConfigs(this.value)">
  <button class="toggle-btn" onclick="toggleSettings()">⚙️ تنظیمات</button>
  <div id="configs-container"></div>

  <div class="footer">
    <p id="update-label">🔧 ساخته شده توسط تیم وییزکس نت</p>
    <p>🌐 <a href="https://rubika.ir/VIPZEXNET" target="_blank">کانال سازنده در روبیکا</a></p>
  </div>

  <div class="settings-panel" id="settings-panel">
    <div class="settings-content">
      <h2>⚙️ تنظیمات</h2>
      <button class="btn" onclick="toggleTheme()">🌓 تغییر تم شب/روز</button><br><br>
      <button class="btn" onclick="toggleLang()">🌐 تغییر زبان</button><br><br>
      <button class="btn close-btn" onclick="toggleSettings()">❌ بستن</button>
    </div>
  </div>

  <div class="copied-msg" id="copied-message">✅ کانفینگ کپی شد!</div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrious/4.0.2/qrious.min.js"></script>
  <script>
    const configs = Array.from({length: 10}, (_, i) => ({
      title: `کانفیگ شماره ${i+1}`,
      server: `server${i+1}.example.com`,
      port: 443,
      type: i % 3 === 0 ? "shadowsocks" : "vless",
      expire: "2025-08-01",
      content: `{"v":"2","ps":"config ${i+1}","add":"server${i+1}.example.com","port":"443","id":"uuid-${i+1}","aid":"0","net":"ws","type":"none","host":"host${i+1}.com","path":"/path${i+1}","tls":"tls"}`,
      vip: i > 6,
      rating: 5,
      security: Math.floor(Math.random() * 6) + 5
    }));

    function renderConfigs() {
      const container = document.getElementById("configs-container");
      container.innerHTML = "";
      configs.forEach((conf, i) => {
        const box = document.createElement("div");
        box.className = "config-box";
        let status = Math.random() > 0.3 ? "🟢 آنلاین" : "🔴 آفلاین";
        box.innerHTML = `
          <div class="title">${conf.vip ? '<span class="vip-label">وی آی پی</span>' : ''}${conf.title}</div>
          <p>🌍 سرور: ${conf.server}</p>
          <p>🔌 پورت: ${conf.port}</p>
          <p>🔗 نوع: ${conf.type}</p>
          <p>📆 انقضا: ${conf.expire}</p>
          <p>📡 وضعیت: ${status}</p>
          <p>🛡️ امنیت: ${conf.security}/10</p>
          <button class="btn" onclick="copyConfig(${i})">📋 کپی کانفینگ</button>
          <canvas class="qr-code" id="qr${i}"></canvas>
          <div class="rating">★★★★★</div>
          <button class="btn" onclick="alert('❌ این کانفیگ برای من کار نکرد!')">گزارش مشکل</button>
        `;
        container.appendChild(box);
        new QRious({ element: document.getElementById("qr" + i), value: conf.content, size: 100 });
      });
    }

    function copyConfig(i) {
      navigator.clipboard.writeText(configs[i].content).then(() => {
        const msg = document.getElementById("copied-message");
        msg.style.display = "block";
        setTimeout(() => msg.style.display = "none", 2000);
      });
    }

    function searchConfigs(query) {
      document.querySelectorAll(".config-box").forEach(box => {
        box.style.display = box.innerText.includes(query) ? "block" : "none";
      });
    }

    let isDark = true;
    function toggleTheme() {
      isDark = !isDark;
      document.getElementById("body").className = isDark ? "dark" : "light";
    }

    let isFarsi = true;
    function toggleLang() {
      isFarsi = !isFarsi;
      document.documentElement.lang = isFarsi ? "fa" : "en";
      document.documentElement.dir = isFarsi ? "rtl" : "ltr";
      document.getElementById("app-title").innerText = isFarsi ? "برنامه VIPZEXNET" : "VIPZEXNET App";
      document.getElementById("update-label").innerText = isFarsi ? "🔧 ساخته شده توسط تیم وییزکس نت" : "🔧 Created by VIPZEXNET Team";
    }

    function toggleSettings() {
      const panel = document.getElementById("settings-panel");
      panel.style.display = panel.style.display === "flex" ? "none" : "flex";
    }

    document.addEventListener("DOMContentLoaded", renderConfigs);
  </script>

</body>
</html>
