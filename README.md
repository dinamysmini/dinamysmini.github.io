# dinamysmini.github.io
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dynamys Mini</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/@phosphor-icons/web"></script>
</head>
<body class="bg-gray-950 text-gray-100 font-sans">
  <!-- Header -->
  <header class="flex items-center justify-between px-6 py-4 bg-gray-900 border-b border-gray-700">
    <h1 class="text-2xl font-bold text-cyan-400">⚡ Dynamys Mini</h1>
    <nav class="space-x-4">
      <button onclick="openTab('dashboard')" class="tab-btn text-gray-300 hover:text-cyan-400">Painel</button>
      <button onclick="openTab('perfis')" class="tab-btn text-gray-300 hover:text-cyan-400">Perfis</button>
      <button onclick="openTab('modulos')" class="tab-btn text-gray-300 hover:text-cyan-400">Módulos</button>
      <button onclick="openTab('historico')" class="tab-btn text-gray-300 hover:text-cyan-400">Histórico</button>
      <button onclick="openTab('config')" class="tab-btn text-gray-300 hover:text-cyan-400">Configurações</button>
    </nav>
  </header>

  <!-- Main Content -->
  <main class="p-6">
    <!-- Painel -->
    <section id="dashboard" class="tab-content">
      <h2 class="text-xl font-semibold mb-4">📊 Painel Dinâmico</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <!-- Status -->
        <div class="p-4 bg-gray-900 rounded-2xl shadow">
          <h3 class="font-medium text-cyan-300 mb-2">Status do Veículo</h3>
          <ul class="space-y-2 text-sm text-gray-300">
            <li>🔋 Bateria: <span id="battery-status" class="text-green-400">--%</span></li>
            <li>⛽ Consumo Médio: <span id="fuel-status" class="text-yellow-300">-- km/L</span></li>
            <li>🛞 Pneus: <span id="tire-status" class="text-green-400">--</span></li>
            <li>🌡️ Temperatura Motor: <span id="temp-status" class="text-blue-400">-- °C</span></li>
            <li>⚡ RPM: <span id="rpm-status" class="text-cyan-400">-- rpm</span></li>
            <li>🛣️ Autonomia Estimada: <span id="range-status" class="text-cyan-300">-- km</span></li>
            <li>📡 Localização: <span id="gps-status" class="text-cyan-300">--</span></li>
            <li>🎵 Música Atual: <span id="music-status" class="text-cyan-300">--</span></li>
            <li>🌍 Conexão Internet: <span id="net-status" class="text-green-400">Conectado</span></li>
          </ul>
        </div>
        <!-- Velocidade -->
        <div class="p-4 bg-gray-900 rounded-2xl shadow flex flex-col items-center justify-center">
          <h3 class="font-medium text-cyan-300 mb-4">Velocidade Atual</h3>
          <div id="speed-display" class="text-5xl font-bold text-cyan-400">0 km/h</div>
          <canvas id="speed-gauge" width="180" height="100" class="mt-4"></canvas>
        </div>
        <!-- Sugestões -->
        <div class="p-4 bg-gray-900 rounded-2xl shadow">
          <h3 class="font-medium text-cyan-300 mb-2">Sugestões Inteligentes</h3>
          <p class="text-gray-400 text-sm">Baseado no seu uso, recomendamos o módulo <span class="text-cyan-400">Suspensão Confort</span> para viagens longas.</p>
        </div>
      </div>
    </section>

    <!-- Perfis -->
    <section id="perfis" class="tab-content hidden">
      <h2 class="text-xl font-semibold mb-4">👥 Perfis de Motoristas</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <div class="bg-gray-900 p-4 rounded-xl shadow hover:ring-2 hover:ring-cyan-400 cursor-pointer flex items-center space-x-3">
          <img src="https://i.pravatar.cc/80?img=3" alt="perfil trabalho" class="w-12 h-12 rounded-full border-2 border-cyan-400">
          <div>
            <h3 class="font-semibold text-cyan-300">Carlos Silva</h3>
            <p class="text-sm text-gray-400">Perfil Trabalho - Econômico + Conforto</p>
          </div>
        </div>
        <div class="bg-gray-900 p-4 rounded-xl shadow hover:ring-2 hover:ring-cyan-400 cursor-pointer flex items-center space-x-3">
          <img src="https://i.pravatar.cc/80?img=5" alt="perfil lazer" class="w-12 h-12 rounded-full border-2 border-cyan-400">
          <div>
            <h3 class="font-semibold text-cyan-300">Mariana Costa</h3>
            <p class="text-sm text-gray-400">Perfil Lazer - Som + Esportivo</p>
          </div>
        </div>
        <div class="bg-gray-900 p-4 rounded-xl shadow hover:ring-2 hover:ring-cyan-400 cursor-pointer flex items-center space-x-3">
          <img src="https://i.pravatar.cc/80?img=8" alt="perfil família" class="w-12 h-12 rounded-full border-2 border-cyan-400">
          <div>
            <h3 class="font-semibold text-cyan-300">João & Ana</h3>
            <p class="text-sm text-gray-400">Perfil Família - Espaço + Segurança</p>
          </div>
        </div>
      </div>
    </section>

    <!-- Módulos -->
    <section id="modulos" class="tab-content hidden">
      <h2 class="text-xl font-semibold mb-4">🔧 Módulos Instalados</h2>
      <ul class="space-y-3">
        <li class="bg-gray-900 p-3 rounded-xl flex justify-between items-center">
          <span>🎶 Som Premium</span>
          <button class="bg-cyan-500 px-3 py-1 rounded-lg text-sm">Configurar</button>
        </li>
        <li class="bg-gray-900 p-3 rounded-xl flex justify-between items-center">
          <span>🛞 Suspensão Eletrônica</span>
          <button class="bg-cyan-500 px-3 py-1 rounded-lg text-sm">Configurar</button>
        </li>
        <li class="bg-gray-900 p-3 rounded-xl flex justify-between items-center">
          <span>🌡️ Climatização Inteligente</span>
          <button class="bg-cyan-500 px-3 py-1 rounded-lg text-sm">Configurar</button>
        </li>
        <li class="bg-gray-900 p-3 rounded-xl flex justify-between items-center">
          <span>📡 GPS & Telemetria</span>
          <button class="bg-cyan-500 px-3 py-1 rounded-lg text-sm">Configurar</button>
        </li>
        <li class="bg-gray-900 p-3 rounded-xl flex justify-between items-center">
          <span>🗺️ Piloto Automático</span>
          <button class="bg-cyan-500 px-3 py-1 rounded-lg text-sm">Configurar</button>
        </li>
        <li class="bg-gray-900 p-3 rounded-xl flex justify-between items-center">
          <span>🛡️ Assistente de Segurança</span>
          <button class="bg-cyan-500 px-3 py-1 rounded-lg text-sm">Configurar</button>
        </li>
      </ul>
    </section>

    <!-- Histórico -->
    <section id="historico" class="tab-content hidden">
      <h2 class="text-xl font-semibold mb-4">📜 Histórico de Uso</h2>
      <ul class="text-gray-400 text-sm space-y-2">
        <li>🔹 14/09 - 32 km rodados - Perfil Carlos Silva</li>
        <li>🔹 13/09 - 120 km rodados - Perfil Mariana Costa</li>
        <li>🔹 12/09 - 18 km rodados - Perfil João & Ana</li>
      </ul>
    </section>

    <!-- Configurações -->
    <section id="config" class="tab-content hidden">
      <h2 class="text-xl font-semibold mb-4">⚙️ Configurações</h2>
      <div class="space-y-4">
        <div>
          <label class="block text-sm mb-1">Idioma</label>
          <select class="bg-gray-800 p-2 rounded-lg w-60">
            <option>Português (BR)</option>
            <option>English (US)</option>
          </select>
        </div>
        <div>
          <label class="block text-sm mb-1">Tema</label>
          <select class="bg-gray-800 p-2 rounded-lg w-60">
            <option>Escuro</option>
            <option>Claro</option>
          </select>
        </div>
        <div>
          <label class="block text-sm mb-1">Notificações</label>
          <input type="checkbox" checked class="accent-cyan-500"> Ativar notificações inteligentes
        </div>
        <div>
          <label class="block text-sm mb-1">Atualizações OTA</label>
          <input type="checkbox" checked class="accent-cyan-500"> Habilitar atualizações automáticas
        </div>
      </div>
    </section>
  </main>

  <script>
    function openTab(tabId) {
      document.querySelectorAll('.tab-content').forEach(tab => tab.classList.add('hidden'));
      document.getElementById(tabId).classList.remove('hidden');
    }

    // Simulação de dados em tempo real
    function simulateData() {
      const speed = Math.floor(Math.random() * 180);
      const battery = Math.floor(50 + Math.random() * 50);
      const fuel = (8 + Math.random() * 12).toFixed(1);
      const temp = Math.floor(70 + Math.random() * 40);
      const rpm = Math.floor(800 + Math.random() * 6000);
      const range = Math.floor(200 + Math.random() * 300);
      const gps = ["São Paulo", "Rio de Janeiro", "Belo Horizonte", "Curitiba"][Math.floor(Math.random()*4)];
      const music = ["Playlist Rock", "Sertanejo Hits", "Lo-Fi Beats", "Pop Internacional"][Math.floor(Math.random()*4)];
      const tireStatus = ["Ok", "Atenção", "Baixo Ar"];
      const tire = tireStatus[Math.floor(Math.random() * tireStatus.length)];

      document.getElementById('speed-display').textContent = speed + ' km/h';
      document.getElementById('battery-status').textContent = battery + '%';
      document.getElementById('fuel-status').textContent = fuel + ' km/L';
      document.getElementById('temp-status').textContent = temp + ' °C';
      document.getElementById('rpm-status').textContent = rpm + ' rpm';
      document.getElementById('tire-status').textContent = tire;
      document.getElementById('range-status').textContent = range + ' km';
      document.getElementById('gps-status').textContent = gps;
      document.getElementById('music-status').textContent = music;

      if (battery < 30) {
        document.getElementById('battery-status').className = 'text-red-400';
      } else {
        document.getElementById('battery-status').className = 'text-green-400';
      }

      if (temp > 100) {
        document.getElementById('temp-status').className = 'text-red-400';
      } else {
        document.getElementById('temp-status').className = 'text-blue-400';
      }

      drawGauge(speed);
    }

    // Gauge simples para a velocidade
    function drawGauge(speed) {
      const canvas = document.getElementById('speed-gauge');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      const maxSpeed = 180;
      const angle = (Math.PI * speed) / maxSpeed;

      // Fundo
      ctx.beginPath();
      ctx.arc(90, 90, 80, Math.PI, 2 * Math.PI);
      ctx.strokeStyle = '#374151';
      ctx.lineWidth = 15;
      ctx.stroke();

      // Indicador
      ctx.beginPath();
      ctx.arc(90, 90, 80, Math.PI, Math.PI + angle);
      ctx.strokeStyle = '#06b6d4';
      ctx.lineWidth = 15;
      ctx.stroke();
    }

    setInterval(simulateData, 2000); // atualiza a cada 2 segundos
  </script>
</body>
</html>
