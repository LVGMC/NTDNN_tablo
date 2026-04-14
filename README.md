<!DOCTYPE html>
<html lang="lv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Saules paneļi - Dashboard</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', system-ui, sans-serif; }
    
    /* ✅ LABOJUMS #1: neliekam overflow: hidden; citādi TV nogriež platumu/augstumu */
    html, body { 
      height: 100%;
      width: 100%;
      overflow-x: hidden;  /* paslēpj horizontālo, ja kādreiz parādās */
      overflow-y: auto;    /* ļauj redzēt visu, ja kaut kas neietilpst augstumā */
      background: linear-gradient(145deg, #0f172a 0%, #1e3a5f 50%, #0ea5e9 100%);
    }

    .dashboard { 
      min-height: 100vh;
      width: 100%;
      display: grid;

      /* ✅ LABOJUMS #2: elastīgas kolonnas, lai nekad neizstumj ārpus ekrāna */
      grid-template-columns: minmax(280px, 380px) 1fr minmax(300px, 420px);

      gap: 20px; 
      padding: 20px;
    }

    /* ✅ LABOJUMS #3: ja TV/pārlūks ir šaurāks (vai zoom), pārkārto, nevis nogriež */
    @media (max-width: 1200px) {
      .dashboard {
        grid-template-columns: 1fr 1fr;
        grid-auto-rows: minmax(200px, auto);
      }
      .gallery-panel {
        grid-column: 1 / -1;
      }
    }

    /* Kreisā kolonna — nedēļas plāns */
    .weekly-panel {
      background: rgba(255, 255, 255, 0.08);
      backdrop-filter: blur(20px);
      border-radius: 24px;
      border: 1px solid rgba(255, 255, 255, 0.15);
      padding: 24px;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    .weekly-panel h2 {
      font-size: 1.8rem;
      font-weight: 700;
      color: #fbbf24;
      margin-bottom: 16px;
      text-transform: uppercase;
      letter-spacing: 1px;
    }

    .plan-list {
      display: flex;
      flex-direction: column;
      gap: 8px;
      overflow: hidden;
    }

    .plan-day {
      background: rgba(251, 191, 36, 0.1);
      border-radius: 16px;
      padding: 12px 16px;
      border-left: 4px solid #fbbf24;
      flex: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
    }

    .plan-day strong {
      color: #fbbf24;
      font-size: 1.1rem;
      display: block;
      margin-bottom: 4px;
    }

    .plan-day p {
      color: #e2e8f0;
      font-size: 0.95rem;
      margin: 2px 0;
    }

    .plan-day .hours {
      color: #94a3b8;
      font-size: 0.8rem;
      margin-top: 4px;
    }

    /* Vidējā kolonna — laikapstākļi */
    .weather-panel {
      display: flex;
      flex-direction: column;
      gap: 20px;
    }

    .main-weather {
      background: rgba(255, 255, 255, 0.08);
      backdrop-filter: blur(20px);
      border-radius: 24px;
      border: 1px solid rgba(255, 255, 255, 0.15);
      padding: 30px;
      flex: 1;
      display: flex;
      flex-direction: column;
    }

    .date-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      padding-bottom: 16px;
      border-bottom: 2px solid rgba(251, 191, 36, 0.3);
    }

    .today-date {
      font-size: 2rem;
      font-weight: 700;
      color: #f8fafc;
    }

    .solar-badge {
      background: rgba(251, 191, 36, 0.2);
      color: #fbbf24;
      padding: 8px 20px;
      border-radius: 40px;
      font-size: 0.9rem;
      font-weight: 600;
    }

    .current-weather-display {
      display: flex;
      align-items: center;
      gap: 30px;
      flex: 1;
    }

    .temp-main {
      font-size: 6rem;
      font-weight: 800;
      color: #f8fafc;
      line-height: 1;
    }

    .weather-info {
      flex: 1;
    }

    .weather-desc {
      font-size: 1.8rem;
      color: #fbbf24;
      font-weight: 600;
      margin-bottom: 8px;
    }

    .solar-tip {
      font-size: 1.2rem;
      color: #94a3b8;
      margin-bottom: 20px;
    }

    .weather-details {
      display: flex;
      gap: 30px;
      color: #e2e8f0;
      font-size: 1.1rem;
    }

    .weather-details span {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .forecast-row {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
      height: 280px;
    }

    .forecast-box {
      background: rgba(255, 255, 255, 0.08);
      backdrop-filter: blur(20px);
      border-radius: 24px;
      border: 1px solid rgba(255, 255, 255, 0.15);
      padding: 20px;
      display: flex;
      flex-direction: column;
    }

    .forecast-box h3 {
      color: #fbbf24;
      font-size: 1.1rem;
      margin-bottom: 16px;
      text-transform: uppercase;
      letter-spacing: 1px;
    }

    .forecast-content {
      display: flex;
      gap: 12px;
      flex: 1;
      align-items: center;
    }

    .forecast-item {
      flex: 1;
      background: rgba(255, 255, 255, 0.05);
      border-radius: 16px;
      padding: 16px;
      text-align: center;
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
    }

    .forecast-item .time {
      color: #94a3b8;
      font-size: 0.9rem;
      margin-bottom: 8px;
    }

    .forecast-item .temp {
      color: #f8fafc;
      font-size: 1.6rem;
      font-weight: 700;
      margin: 8px 0;
    }

    .forecast-item .desc {
      color: #e2e8f0;
      font-size: 0.85rem;
    }

    /* Labā kolonna — galerija */
    .gallery-panel {
      background: rgba(255, 255, 255, 0.08);
      backdrop-filter: blur(20px);
      border-radius: 24px;
      border: 1px solid rgba(255, 255, 255, 0.15);
      padding: 24px;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    .gallery-panel h2 {
      font-size: 1.6rem;
      font-weight: 700;
      color: #fbbf24;
      margin-bottom: 16px;
      text-transform: uppercase;
      letter-spacing: 1px;
    }

    .gallery-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      flex: 1;
    }

    .gallery-item {
      position: relative;
      border-radius: 16px;
      overflow: hidden;
      background: rgba(255, 255, 255, 0.05);
    }

    .gallery-item img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform 0.3s ease;
    }

    .gallery-item:hover img {
      transform: scale(1.05);
    }

    .gallery-item span {
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      background: linear-gradient(transparent, rgba(0,0,0,0.8));
      color: #f8fafc;
      padding: 20px 12px 12px;
      font-size: 0.9rem;
    }

    .loading {
      color: #94a3b8;
      font-size: 1.2rem;
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100%;
      animation: pulse 2s infinite;
    }

    .error-message {
      background: rgba(220, 38, 38, 0.2);
      border: 1px solid rgba(220, 38, 38, 0.4);
      color: #fecaca;
      padding: 20px;
      border-radius: 16px;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.5; }
    }
  </style>
</head>
<body>
  <div class="dashboard">
    <!-- Kreisā kolonna -->
    <div class="weekly-panel">
      <h2>📋 Nedēļas plāns</h2>
      <div class="plan-list" id="weeklyPlanList">
        <div class="loading">Ielādē...</div>
      </div>
    </div>

    <!-- Vidējā kolonna -->
    <div class="weather-panel">
      <div class="main-weather">
        <div class="date-header">
          <div class="today-date" id="currentDate">--</div>
          <div class="solar-badge">☀️ Saules aktivitāte</div>
        </div>
        <div class="current-weather-display" id="currentWeatherBlock">
          <div class="loading">Ielādē laikapstākļus...</div>
        </div>
      </div>

      <div class="forecast-row">
        <div class="forecast-box">
          <h3>⏱️ Nākamās 3 stundas</h3>
          <div class="forecast-content" id="hourly3h">
            <div class="loading">Ielādē...</div>
          </div>
        </div>
        <div class="forecast-box">
          <h3>📅 Nākamās 3 dienas</h3>
          <div class="forecast-content" id="threeDaysForecast">
            <div class="loading">Ielādē...</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Labā kolonna -->
    <div class="gallery-panel">
      <h2>📸 Galerija</h2>
      <div class="gallery-grid" id="googlePhotosContainer">
        <div class="loading" style="grid-column: 1 / -1;">Ielādē attēlus...</div>
      </div>
    </div>
  </div>

<script>
const API_KEY = 'c43d82886b01f58c9754b116e45751b7';
const LAT = 56.9496;
const LON = 24.1052;

function $(id) { return document.getElementById(id); }

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
  const res = await fetch(url);
  if (!res.ok) throw new Error('HTTP ' + res.status);
  return await res.json();
}

async function fetchForecast() {
  const url = `https://api.openweathermap.org/data/2.5/forecast?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
  const res = await fetch(url);
  if (!res.ok) throw new Error('HTTP ' + res.status);
  return await res.json();
}

async function showWeather() {
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
    
    $('currentWeatherBlock').innerHTML = `
      <img src="${getIconUrl(icon)}" style="width:140px;height:140px;filter:drop-shadow(0eather-info">
        <div class="weather-desc">${desc.charAt(0).toUpperCase() + desc.slice(1)}</div>
        <div class="solar-tip">${tip}</div>
        <div class="weather-details">
          <span>🌡️ Jūtas kā ${feels}°C</span>
          <span>💧 ${w.main.humidity}%</span>
          <span>💨 ${Math.round(w.wind.speed)} m/s</span>
        </div>
      </div>`;
  } catch (err) {
    $('currentWeatherBlock').innerHTML = `<div class="error-message">❌ ${err.message}</div>`;
  }
}

async function showHourly() {
  try {
    const f = await fetchForecast();
    const items = f.list.slice(0, 3);
    $('hourly3h').innerHTML = items.map(item => {
      const t = new Date(item.dt * 1000);
      const hh = String(t.getHours()).padStart(2, '0');
      return `
        <div class="forecast-item">
          <div class="time">${hh}:00</div>
          <img src="${getIconUrl(item.weather[0].icon)}" style="width:50px;height:50px;margin:8pxtemp)}°C</div>
          <div class="desc">${item.weather[0].description}</div>
        </div>`;
    }).join('');
  } catch (err) {
    $('hourly3h').innerHTML = `<div class="error-message">❌ ${err.message}</div>`;
  }
}

async function showDays() {
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
    
    $('threeDaysForecast').innerHTML = days.map((d, i) => {
      const data = daily[d];
      const min = Math.round(Math.min(...data.temps));
      const max = Math.round(Math.max(...data.temps));
      const icon = data.icons[Math.floor(data.icons.length / 2)];
      const desc = data.descs[Math.floor(data.descs.length / 2)];
      return `
        <div class="forecast-item">
          <div class="time">${labels[i]}</div>
          <img src="${getIconUrl(icon)}" style="width:50px;height:50px;margin:8px auto      <div class="desc">${desc}</div>
        </div>`;
    }).join('');
  } catch (err) {
    $('threeDaysForecast').innerHTML = `<div class="error-message">❌ ${err.message}</div>`;
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
      <p>${acts[i % 7].e} ${acts[i % 7].t}</p>
      <p class="hours">🌞 Saules stundas: ${8 + (i % 4)}h</p>
    </div>`).join('');
}

function loadGallery() {
  const ids = [10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21];
  $('googlePhotosContainer').innerHTML = ids.map((id, i) => `
    <div class="gallery-item">
      <img src="https://picsum.photos/id/${id}/400/300" alt="Foto ${i+1pan>
    </div>`).join('');
}

async function init() {
  updateDate();
  generatePlan();
  loadGallery();
  await showWeather();
  await showHourly();
  await showDays();
  
  setInterval(() => {
    updateDate();
    showWeather();
    showHourly();
    showDays();
  }, 600000);
}

document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
