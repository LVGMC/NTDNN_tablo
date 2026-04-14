<html lang="lv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Saules paneļi - Dashboard</title>

  <style>
    /* ========== RESET + BASE ========== */
    :root{
      --bg1:#0f172a;
      --bg2:#1e3a5f;
      --bg3:#0ea5e9;

      --card: rgba(255,255,255,0.08);
      --cardBorder: rgba(255,255,255,0.15);

      --accent: #fbbf24;
      --text: #f8fafc;
      --muted: #94a3b8;
      --muted2:#e2e8f0;

      --gap: clamp(12px, 1.2vw, 22px);
      --radius: clamp(18px, 1.2vw, 26px);

      /* TV-friendly font scaling */
      --h1: clamp(18px, 1.6vw, 28px);
      --h2: clamp(16px, 1.3vw, 22px);
      --p:  clamp(12px, 1.0vw, 18px);
      --bigTemp: clamp(64px, 6vw, 120px);
    }

    * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', system-ui, sans-serif; }
    html, body {
      height: 100%;
      width: 100%;
      /* IMPORTANT: nevis hidden, lai nekas netiek nogriezts */
      overflow-x: hidden;
      overflow-y: auto;
      background: linear-gradient(145deg, var(--bg1) 0%, var(--bg2) 50%, var(--bg3) 100%);
      color: var(--text);
    }

    /* ========== LAYOUT ========== */
    .wrap{
      min-height: 100dvh;
      width: 100%;
      display: flex;
      flex-direction: column;
      padding: var(--gap);
      gap: var(--gap);
    }

    /* Status bar (TV tablo) */
    .topbar{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap: var(--gap);
      padding: calc(var(--gap) * 0.75) var(--gap);
      border-radius: var(--radius);
      background: rgba(0,0,0,0.18);
      border: 1px solid rgba(255,255,255,0.12);
      backdrop-filter: blur(14px);
    }
    .brand{
      display:flex;
      align-items:center;
      gap: 12px;
      font-weight: 800;
      letter-spacing: 0.5px;
      font-size: clamp(16px, 1.3vw, 22px);
    }
    .brand .dot{
      width: 10px; height: 10px;
      border-radius: 999px;
      background: var(--accent);
      box-shadow: 0 0 18px rgba(251,191,36,0.35);
    }
    .status{
      display:flex;
      align-items:center;
      gap: 14px;
      font-size: var(--p);
      color: var(--muted2);
    }
    .pill{
      padding: 8px 14px;
      border-radius: 999px;
      background: rgba(251, 191, 36, 0.18);
      border: 1px solid rgba(251, 191, 36, 0.28);
      color: var(--accent);
      font-weight: 700;
      white-space: nowrap;
    }
    .clock{
      font-variant-numeric: tabular-nums;
      padding: 8px 14px;
      border-radius: 999px;
      background: rgba(255,255,255,0.08);
      border: 1px solid rgba(255,255,255,0.12);
      white-space: nowrap;
    }

    /* Main grid: TV = 3 kolonnas */
    .dashboard {
      flex: 1;
      width: 100%;
      display: grid;
      gap: var(--gap);
      align-items: stretch;

      /* elastīgi platumi, lai nekad "nepārsistu" ekrānu */
      grid-template-columns: minmax(300px, 420px) 1fr minmax(320px, 460px);
    }

    /* Ja ekrāns ir šaurāks (piem. 1280 vai TV pārlūkā zoom), pāriet uz 2+1 */
    @media (max-width: 1200px) {
      .dashboard { grid-template-columns: 1fr 1fr; }
      .gallery-panel { grid-column: 1 / -1; min-height: 360px; }
    }

    /* Telefonam / ļoti šauram */
    @media (max-width: 800px) {
      .dashboard { grid-template-columns: 1fr; }
      .wrap { padding: 12px; }
    }

    /* Cards */
    .card{
      background: var(--card);
      backdrop-filter: blur(18px);
      border-radius: var(--radius);
      border: 1px solid var(--cardBorder);
      overflow: hidden;
      display: flex;
      flex-direction: column;
      min-height: 0; /* ļauj iekšējiem elementiem skrollēties pareizi */
    }
    .card-inner{
      padding: clamp(16px, 1.4vw, 28px);
      display:flex;
      flex-direction: column;
      gap: 12px;
      height: 100%;
      min-height: 0;
    }
    h2{
      font-size: var(--h1);
      font-weight: 900;
      color: var(--accent);
      text-transform: uppercase;
      letter-spacing: 1px;
      display:flex;
      align-items:center;
      gap: 10px;
    }

    /* ========== LEFT: WEEKLY PLAN ========== */
    .plan-list{
      display:flex;
      flex-direction: column;
      gap: 10px;
      flex:1;
      min-height: 0;
      overflow: auto; /* ja saraksts garš - lai rullējas */
      padding-right: 4px;
    }
    .plan-day{
      background: rgba(251, 191, 36, 0.10);
      border-radius: clamp(14px, 1vw, 18px);
      padding: 12px 16px;
      border-left: 4px solid var(--accent);
      display:flex;
      flex-direction: column;
      gap: 4px;
    }
    .plan-day strong{
      color: var(--accent);
      font-size: var(--h2);
    }
    .plan-day p{
      color: var(--muted2);
      font-size: var(--p);
      line-height: 1.25;
    }
    .plan-day .hours{
      color: var(--muted);
      font-size: clamp(11px, 0.9vw, 14px);
      margin-top: 2px;
    }

    /* ========== CENTER: WEATHER ========== */
    .weather-panel{
      display:flex;
      flex-direction: column;
      gap: var(--gap);
      min-height: 0;
    }
    .date-header{
      display:flex;
      justify-content: space-between;
      align-items:center;
      gap: 12px;
      padding-bottom: 12px;
      border-bottom: 2px solid rgba(251, 191, 36, 0.25);
    }
    .today-date{
      font-size: clamp(18px, 1.8vw, 34px);
      font-weight: 900;
    }
    .solar-badge{
      background: rgba(251, 191, 36, 0.18);
      color: var(--accent);
      padding: 10px 18px;
      border-radius: 999px;
      font-size: var(--p);
      font-weight: 800;
      white-space: nowrap;
    }

    .current-weather-display{
      display:flex;
      align-items:center;
      gap: clamp(14px, 2vw, 30px);
      flex:1;
      min-height: 0;
    }
    .temp-main{
      font-size: var(--bigTemp);
      font-weight: 900;
      line-height: 1;
      letter-spacing: -1px;
      white-space: nowrap;
    }
    .weather-info{
      flex:1;
      min-width: 0;
      display:flex;
      flex-direction: column;
      gap: 8px;
    }
    .weather-desc{
      font-size: clamp(16px, 1.6vw, 28px);
      color: var(--accent);
      font-weight: 900;
    }
    .solar-tip{
      font-size: clamp(13px, 1.1vw, 20px);
      color: var(--muted);
    }
    .weather-details{
      display:flex;
      flex-wrap: wrap;
      gap: 14px 22px;
      color: var(--muted2);
      font-size: var(--p);
    }

    .forecast-row{
      display:grid;
      grid-template-columns: 1fr 1fr;
      gap: var(--gap);
      min-height: 260px;
    }
    @media (max-width: 1200px){
      .forecast-row{ grid-template-columns: 1fr; }
    }

    .forecast-box h3{
      color: var(--accent);
      font-size: clamp(13px, 1.0vw, 16px);
      text-transform: uppercase;
      letter-spacing: 1px;
      margin-bottom: 10px;
    }
    .forecast-content{
      display:flex;
      gap: 12px;
      flex:1;
      align-items: stretch;
      min-height: 0;
    }
    .forecast-item{
      flex:1;
      background: rgba(255,255,255,0.06);
      border-radius: clamp(14px, 1vw, 18px);
      padding: 12px;
      text-align:center;
      display:flex;
      flex-direction: column;
      justify-content:center;
      gap: 6px;
      min-width: 0;
    }
    .forecast-item .time{
      color: var(--muted);
      font-size: clamp(12px, 0.95vw, 16px);
      font-weight: 800;
    }
    .forecast-item .temp{
      color: var(--text);
      font-size: clamp(16px, 1.4vw, 26px);
      font-weight: 900;
    }
    .forecast-item .desc{
      color: var(--muted2);
      font-size: clamp(11px, 0.9vw, 14px);
      line-height: 1.1;
    }

    /* ========== RIGHT: GALLERY ========== */
    .gallery-grid{
      display:grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      flex:1;
      min-height: 0;
    }
    .gallery-item{
      position:relative;
      border-radius: clamp(14px, 1vw, 18px);
      overflow:hidden;
      background: rgba(255,255,255,0.05);
      min-height: 120px;
    }
    .gallery-item img{
      width:100%;
      height:100%;
      object-fit: cover;
      transition: transform 0.35s ease;
      will-change: transform;
    }
    .gallery-item:hover img{ transform: scale(1.06); }
    .gallery-item span{
      position:absolute;
      left:0; right:0; bottom:0;
      padding: 18px 12px 10px;
      background: linear-gradient(transparent, rgba(0,0,0,0.85));
      font-size: clamp(11px, 0.9vw, 14px);
      color: var(--text);
    }

    /* ========== STATES ========== */
    .loading{
      color: var(--muted);
      font-size: clamp(14px, 1.1vw, 20px);
      display:flex;
      align-items:center;
      justify-content:center;
      height: 100%;
      animation: pulse 2s infinite;
    }
    .error-message{
      background: rgba(220, 38, 38, 0.22);
      border: 1px solid rgba(220, 38, 38, 0.45);
      color: #fecaca;
      padding: 18px;
      border-radius: clamp(14px, 1vw, 18px);
      font-size: var(--p);
    }
    @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:.55} }

    /* ========== OPTIONAL: Anti burn-in micro shift (ļoti minimāls) ========== */
    .burnin-shift{
      animation: shift 120s linear infinite;
    }
    @keyframes shift{
      0%{ transform: translate(0,0); }
      25%{ transform: translate(2px,0); }
      50%{ transform: translate(2px,2px); }
      75%{ transform: translate(0,2px); }
      100%{ transform: translate(0,0); }
    }

    /* Scrollbar (neuzkrītošs) */
    *::-webkit-scrollbar { width: 10px; }
    *::-webkit-scrollbar-thumb {
      background: rgba(255,255,255,0.14);
      border-radius: 999px;
    }
    *::-webkit-scrollbar-track { background: rgba(0,0,0,0.12); }
  </style>
</head>

<body>
  <div class="wrap burnin-shift">
    <!-- TOP BAR -->
    <div class="topbar">
      <div class="brand">
        <span class="dot"></span>
        Saules paneļi — tablo
      </div>
      <div class="status">
        <span class="pill" id="lastUpdate">Atjauninās…</span>
        <span class="clock" id="clock">--:--</span>
      </div>
    </div>

    <!-- DASHBOARD GRID -->
    <div class="dashboard">
      <!-- LEFT -->
      <div class="card weekly-panel">
        <div class="card-inner">
          <h2>📋 Nedēļas plāns</h2>
          <div class="plan-list" id="weeklyPlanList">
            <div class="loading">Ielādē…</div>
          </div>
        </div>
      </div>

      <!-- CENTER -->
      <div class="weather-panel">
        <div class="card main-weather">
          <div class="card-inner">
            <div class="date-header">
              <div class="today-date" id="currentDate">--</div>
              <div class="solar-badge">☀️ Saules aktivitāte</div>
            </div>

            <div class="current-weather-display" id="currentWeatherBlock">
              <div class="loading">Ielādē laikapstākļus…</div>
            </div>
          </div>
        </div>

        <div class="forecast-row">
          <div class="card forecast-box">
            <div class="card-inner">
              <h3>⏱️ Nākamās 3 stundas</h3>
              <div class="forecast-content" id="hourly3h">
                <div class="loading">Ielādē…</div>
              </div>
            </div>
          </div>

          <div class="card forecast-box">
            <div class="card-inner">
              <h3>📅 Nākamās 3 dienas</h3>
              <div class="forecast-content" id="threeDaysForecast">
                <div class="loading">Ielādē…</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- RIGHT -->
      <div class="card gallery-panel">
        <div class="card-inner">
          <h2>📸 Galerija</h2>
          <div class="gallery-grid" id="googlePhotosContainer">
            <div class="loading" style="grid-column: 1 / -1;">Ielādē attēlus…</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    /***************
     * KONFIGURĀCIJA
     ***************/
    const API_KEY = 'c43d82886b01f58c9754b116e45751b7'; // <- ieliec savu
    const LAT = 56.9496;
    const LON = 24.1052;

    const REFRESH_MINUTES = 10; // atjauno ik pēc 10 min
    const REFRESH_MS = REFRESH_MINUTES * 60 * 1000;

    function $(id) { return document.getElementById(id); }

    function setLastUpdate(ok=true){
      const now = new Date();
      const t = now.toLocaleTimeString('lv-LV', { hour: '2-digit', minute: '2-digit' });
      $('lastUpdate').textContent = ok ? `Atjaunots: ${t}` : `Kļūda: ${t}`;
      $('lastUpdate').style.background = ok ? 'rgba(251, 191, 36, 0.18)' : 'rgba(220,38,38,0.18)';
      $('lastUpdate').style.borderColor = ok ? 'rgba(251, 191, 36, 0.28)' : 'rgba(220,38,38,0.35)';
      $('lastUpdate').style.color = ok ? 'var(--accent)' : '#fecaca';
    }

    function updateClock(){
      const now = new Date();
      $('clock').textContent = now.toLocaleTimeString('lv-LV', { hour:'2-digit', minute:'2-digit' });
    }

    function updateDate() {
      const now = new Date();
      const options = { weekday: 'long', day: 'numeric', month: 'long' };
      const formatted = now.toLocaleDateString('lv-LV', options);
      $('currentDate').textContent = formatted.charAt(0).toUpperCase() + formatted.slice(1);
    }

    function getIconUrl(icon) {
      return `https://openweathermap.org/img/wn/${icon}@4x.png`;
    }

    async function fetchWeather() {
      const url = `https://api.openweathermap.org/data/2.5/weather?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
      const res = await fetch(url, { cache: "no-store" });
      if (!res.ok) throw new Error('HTTP ' + res.status);
      return await res.json();
    }

    async function fetchForecast() {
      const url = `https://api.openweathermap.org/data/2.5/forecast?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
      const res = await fetch(url, { cache: "no-store" });
      if (!res.ok) throw new Error('HTTP ' + res.status);
      return await res.json();
    }

    async function showWeather() {
      const block = $('currentWeatherBlock');

      try {
        const w = await fetchWeather();
        const temp = Math.round(w.main.temp);
        const feels = Math.round(w.main.feels_like);
        const desc = w.weather[0].description;
        const icon = w.weather[0].icon;
        const main = w.weather[0].main;

        let tip = "🌍 Normāli apstākļi saules paneļiem";
        if (main === "Clear") tip = "☀️ Lieliska diena saules paneļiem!";
        else if (main === "Clouds") tip = "⛅ Vidēja efektivitāte (mākoņi)";
        else if (main === "Rain") tip = "🌧️ Zema ražība (lietus)";
        else if (main === "Snow") tip = "❄️ Notīri paneļus (sniegs)";

        block.innerHTML = `
          <img src="${getIconUrl(icon)}"
               alt="Ikona"
               style="width:clamp(96px, 10vw, 160px); height:auto; filter:drop-shadow(0 0 18px rgba(251,191,36,0.28));desc.slice(1)}</div>
            <div class="solar-tip">${tip}</div>
            <div class="weather-details">
              <span>🌡️ Jūtas kā ${feels}°C</span>
              <span>💧 ${w.main.humidity}%</span>
              <span>💨 ${Math.round(w.wind.speed)} m/s</span>
            </div>
          </div>
        `;
      } catch (err) {
        block.innerHTML = `<div class="error-message">❌ Laikapstākļu kļūda: ${err.message}</div>`;
        throw err;
      }
    }

    async function showHourly() {
      const el = $('hourly3h');

      try {
        const f = await fetchForecast();
        const items = f.list.slice(0, 3);

        el.innerHTML = items.map(item => {
          const t = new Date(item.dt * 1000);
          const hh = String(t.getHours()).padStart(2, '0');
          return `
            <div class="forecast-item">
              <div class="time">${hh}:00</div>
              <img src="${getIconUrl(item.weather[0].icon)}" alt="Ikona"
                   style="width:clamp(44px, 4vw, 62px); height:auto; margin: 4px auto.weather[0].description}</div>
            </div>
          `;
        }).join('');
      } catch (err) {
        el.innerHTML = `<div class="error-message">❌ Prognozes kļūda: ${err.message}</div>`;
        throw err;
      }
    }

    async function showDays() {
      const el = $('threeDaysForecast');

      try {
        const f = await fetchForecast();
        const daily = {};

        f.list.forEach(item => {
          const d = new Date(item.dt * 1000).toLocaleDateString('lv-LV');
          if (!daily[d]) daily[d] = { temps: [], icons: [], descs: [] };
          daily[d].temps.push(item.main.temp);
          daily[d].icons.push(item.weather[0].icon);
          daily[d].descs.push(item.weather[0].description);
        });

        const days = Object.keys(daily).slice(1, 4);
        const labels = ['Rīt', 'Parīt', 'Pēc 3 dienām'];

        el.innerHTML = days.map((d, i) => {
          const data = daily[d];
          const min = Math.round(Math.min(...data.temps));
          const max = Math.round(Math.max(...data.temps));
          const icon = data.icons[Math.floor(data.icons.length / 2)];
          const desc = data.descs[Math.floor(data.descs.length / 2)];

          return `
            <div class="forecast-item">
              <div class="time">${labels[i]}</div>
              <img src="${getIconUrl(icon)}" alt="Ikona"
                   style="width:clamp(44px, 4vw, iv class="desc">${desc}</div>
            </div>
          `;
        }).join('');
      } catch (err) {
        el.innerHTML = `<div class="error-message">❌ 3 dienu prognozes kļūda: ${err.message}</div>`;
        throw err;
      }
    }

    function generatePlan() {
      const days = ['Pirmdiena', 'Otrdiena', 'Trešdiena', 'Ceturtdiena', 'Piektdiena', 'Sestdiena', 'Svētdiena'];
      const acts = [
        { e: '🧹', t: 'Saules paneļu tīrīšana un pārbaude' },
        { e: '🔋', t: 'Bateriju uzlādes optimizācija' },
        { e: '📊', t: 'Enerģijas patēriņa analīze' },
        { e: '☀️', t: 'Maksimāla saules izmantošana' },
        { e: '🛠️', t: 'Invertora pārbaude un apkope' },
        { e: '🌿', t: 'Dārza laistīšana ar saules sūkni' },
        { e: '📈', t: 'Nedēļas atskaite par enerģiju' }
      ];

      $('weeklyPlanList').innerHTML = days.map((day, i) => `
        <div class="plan-day">
          <strong>${day}</strong>
          <p>${acts[i % acts.length].e} ${acts[i % acts.length].t}</p>
          <p class="hours">🌞 Saules stundas: ${8 + (i % 4)}h</p>
        </div>
      `).join('');
    }

    function loadGallery() {
      const ids = [10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21];
      $('googlePhotosContainer').innerHTML = ids.map((id, i) => `
        <div class="gallery-item">
          <img src="https://picsum.photos/id/${id}/800/600" alt="Foto ${i+1}</span>
        </div>
      `).join('');
    }

    async function refreshAll(){
      updateDate();

      try{
        await showWeather();
        await showHourly();
        await showDays();
        setLastUpdate(true);
      }catch(e){
        setLastUpdate(false);
      }
    }

    async function init() {
      // clock
      updateClock();
      setInterval(updateClock, 1000 * 5);

      // content
      generatePlan();
      loadGallery();
      await refreshAll();

      setInterval(refreshAll, REFRESH_MS);
    }

    document.addEventListener('DOMContentLoaded', init);
  </script>
</body>
</html>
