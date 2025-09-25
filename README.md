<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Dynamys — Painel</title>

  <!-- Tailwind CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            primary: "#1a1a1a",
            secondary: "#9b5de5",
            light: "#f5f5f5",
            muted: "#9ca3af"
          },
          boxShadow: {
            'glow': '0 6px 30px rgba(155,93,229,0.14)'
          }
        }
      }
    }
  </script>

  <style>
    /* Utility classes via Tailwind @apply are not available inline in CDN,
       so we create simple classes for repeated patterns */
    .card {
      background: var(--card-bg, #151515);
      border-radius: 14px;
      padding: 1rem;
      border: 2px solid rgba(155,93,229,0.18);
      box-shadow: 0 6px 30px rgba(0,0,0,0.35);
      transition: transform .18s ease, box-shadow .18s ease;
    }
    .card:hover { transform: translateY(-6px); box-shadow: 0 12px 40px rgba(0,0,0,0.5); }
    .card-title { color: #9b5de5; font-weight:600; margin-bottom:.5rem; }
    .input, .select { background: #0f0f0f; color:#f5f5f5; border:1px solid #2f2f2f; padding:.5rem .6rem; border-radius:8px; width:100% }
    .small { font-size:.9rem; color:#cbd5e1 }
    .btn { padding:.5rem .9rem; border-radius:10px; font-weight:600; }
    .btn-primary { background: #9b5de5; color:white }
    .btn-ghost { background:transparent; border:1px solid rgba(255,255,255,0.04); color:#f5f5f5 }
    .avatar { width:64px; height:64px; border-radius:999px; object-fit:cover; border:3px solid rgba(155,93,229,0.9) }
    .toast { position: fixed; right: 1rem; bottom: 6rem; z-index: 60 }
    @media (max-width: 820px) {
      header nav { display:none }
      .mobile-nav { display:flex }
    }
    @media (min-width: 821px) {
      .mobile-nav { display:none }
      .desktop-nav { display:flex }
    }
    /* Draggable placeholder style */
    .dragging { opacity: .6; transform: scale(.98) }
  </style>
</head>
<body id="appBody" class="min-h-screen bg-primary text-light antialiased">

  <!-- Header (desktop) -->
  <header class="flex items-center justify-between px-6 py-4 sticky top-0 z-40 border-b-2 border-secondary bg-primary/90 backdrop-blur-md">
    <div class="flex items-center gap-4">
      <h1 class="text-2xl font-bold text-secondary flex items-center gap-2">⚡ Dynamys</h1>
      <span class="small text-muted">display & controller</span>
    </div>

    <nav class="desktop-nav items-center space-x-3" role="navigation" aria-label="Principal">
      <button class="tab-btn btn-ghost" data-tab="dashboard">Painel</button>
      <button class="tab-btn btn-ghost" data-tab="perfis">Perfis</button>
      <button class="tab-btn btn-ghost" data-tab="modulos">Módulos</button>
      <button class="tab-btn btn-ghost" data-tab="historico">Histórico</button>
      <button class="tab-btn btn-ghost" data-tab="config">Configurações</button>
    </nav>

    <!-- Mobile nav (bottom) will be rendered separately -->
  </header>

  <main class="p-6 pb-28 max-w-6xl mx-auto">
    <!-- Top area: quick stats -->
    <section id="dashboard" class="tab-content">
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
        <div class="card">
          <div class="card-title">Status do Veículo</div>
          <ul class="space-y-2 small">
            <li>🔋 <strong id="battery-status">--%</strong> Bateria</li>
            <li>⛽ <strong id="fuel-status">-- km/L</strong> Consumo Médio</li>
            <li>🌡️ <strong id="temp-status">-- °C</strong> Motor</li>
            <li>🛞 <strong id="tire-status">Ok</strong> Pneus</li>
          </ul>
        </div>

        <div class="card flex flex-col items-center justify-center">
          <div class="card-title">Velocidade</div>
          <div id="speed-display" class="text-secondary text-6xl font-bold mb-2">0 km/h</div>
          <canvas id="speed-gauge" width="260" height="140" aria-hidden="true"></canvas>
          <div class="small mt-2 text-muted">Leitura simulada — para demo</div>
        </div>

        <div class="card">
          <div class="card-title">Sugestões Inteligentes</div>
          <p class="small text-gray-300">Recomendado: <strong class="text-secondary">Suspensão Confort</strong> para viagens longas.</p>
          <div class="mt-4 flex gap-2">
            <button id="apply-sug" class="btn btn-primary">Aplicar sugestão</button>
            <button id="export-config" class="btn btn-ghost">Exportar configs</button>
          </div>
        </div>
      </div>

      <!-- Panels: quick actions -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="card">
          <div class="card-title">Ações Rápidas</div>
          <div class="flex flex-col gap-2 mt-2">
            <button id="add-profile-quick" class="btn btn-primary">+ Novo perfil rápido</button>
            <button id="open-perfis" class="btn btn-ghost">Gerenciar perfis</button>
            <button id="clear-all" class="btn btn-ghost text-red-400">Resetar localStorage</button>
          </div>
        </div>

        <div class="card">
          <div class="card-title">Módulos Ativos</div>
          <ul class="space-y-2 small mt-2">
            <li>🔊 Som Premium — <span class="text-muted">Ativado</span></li>
            <li>🛞 Suspensão Eletrônica — <span class="text-muted">Desativado</span></li>
            <li>📡 Telemetria — <span class="text-muted">Ativo</span></li>
          </ul>
        </div>

        <div class="card">
          <div class="card-title">Informações</div>
          <p class="small text-gray-300">Última atualização: <span id="last-update">—</span></p>
          <p class="small text-gray-400 mt-2">Você pode exportar/importar perfis e configurações para backup.</p>
        </div>
      </div>
    </section>

    <!-- Perfis -->
    <section id="perfis" class="tab-content hidden">
      <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 mb-4">
        <div>
          <h2 class="text-2xl font-semibold text-secondary">👥 Perfis</h2>
          <div class="small text-muted">Adicione perfis com preferências do carro.</div>
        </div>
        <div class="flex gap-2">
          <input id="search-profiles" placeholder="Pesquisar perfil..." class="input" style="width:220px">
          <button id="btn-export-profiles" class="btn btn-ghost">Exportar</button>
          <button id="btn-import-profiles" class="btn btn-ghost">Importar</button>
        </div>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4" id="perfil-list" aria-live="polite"></div>

      <div class="card max-w-md">
        <div class="card-title" id="form-title">➕ Adicionar / Editar Perfil</div>
        <form id="perfil-form" class="space-y-3">
          <input type="hidden" id="edit-index" />
          <div>
            <label class="small block mb-1">Nome</label>
            <input id="perfil-nome" class="input" required />
          </div>
          <div>
            <label class="small block mb-1">Descrição</label>
            <input id="perfil-desc" class="input" required />
          </div>
          <div>
            <label class="small block mb-1">Imagem (URL)</label>
            <input id="perfil-img" class="input" placeholder="https://..." />
          </div>
          <div class="flex gap-2">
            <button type="submit" class="btn btn-primary">Salvar</button>
            <button type="button" id="btn-apply-profile" class="btn btn-ghost">Aplicar</button>
            <button type="button" id="btn-cancel-edit" class="btn btn-ghost hidden">Cancelar</button>
          </div>
        </form>
      </div>
    </section>

    <!-- Módulos -->
    <section id="modulos" class="tab-content hidden">
      <h2 class="text-2xl font-semibold text-secondary mb-3">🔧 Módulos</h2>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="card">
          <div class="card-title">Som</div>
          <p class="small text-gray-300">Equalizador, volume máximo, perfil auditivo.</p>
          <div class="mt-3 flex gap-2">
            <button class="btn btn-primary">Configurar</button>
            <button class="btn btn-ghost">Testar</button>
          </div>
        </div>
        <div class="card">
          <div class="card-title">Suspensão</div>
          <p class="small text-gray-300">Ajustes confort/esportivo, altura e amortecimento.</p>
          <div class="mt-3 flex gap-2">
            <button class="btn btn-primary">Ativar</button>
            <button class="btn btn-ghost">Ajustes</button>
          </div>
        </div>
      </div>
    </section>

    <!-- Histórico -->
    <section id="historico" class="tab-content hidden">
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-2xl font-semibold text-secondary">📜 Histórico</h2>
        <div class="flex gap-2">
          <button id="btn-export-hist" class="btn btn-ghost">Exportar</button>
          <button id="btn-clear-hist" class="btn btn-ghost text-red-400">Limpar</button>
        </div>
      </div>
      <div id="history-list" class="space-y-3"></div>
    </section>

    <!-- Configurações -->
    <section id="config" class="tab-content hidden">
      <h2 class="text-2xl font-semibold text-secondary mb-3">⚙️ Configurações do Veículo</h2>

      <div class="card space-y-4">
        <details open>
          <summary class="card-title cursor-pointer">🌐 Idioma & Tema</summary>
          <div class="mt-3 grid grid-cols-1 md:grid-cols-2 gap-3">
            <div>
              <label class="small mb-1 block">Idioma</label>
              <select id="config-idioma" class="select">
                <option>Português (BR)</option>
                <option>English (US)</option>
                <option>Español</option>
              </select>
            </div>
            <div>
              <label class="small mb-1 block">Tema</label>
              <select id="config-tema" class="select">
                <option value="escuro">Escuro</option>
                <option value="claro">Claro</option>
              </select>
            </div>
          </div>
        </details>

        <details>
          <summary class="card-title cursor-pointer">🚗 Condução</summary>
          <div class="mt-3 grid grid-cols-1 md:grid-cols-2 gap-3">
            <div>
              <label class="small mb-1 block">Modo de condução</label>
              <select id="config-modo" class="select">
                <option>Econômico</option>
                <option>Normal</option>
                <option>Esportivo</option>
              </select>
            </div>
            <div>
              <label class="small mb-1 block">Direção elétrica</label>
              <select id="config-direcao" class="select">
                <option>Leve</option>
                <option>Média</option>
                <option>Peso esportivo</option>
              </select>
            </div>
            <div class="md:col-span-2">
              <label class="small mb-1 block">Sensibilidade do acelerador: <span id="acc-val" class="text-secondary">5</span></label>
              <input id="config-acelerador" type="range" min="1" max="10" value="5" />
            </div>
          </div>
        </details>

        <details>
          <summary class="card-title cursor-pointer">🌡️ Conforto</summary>
          <div class="mt-3 grid grid-cols-1 md:grid-cols-2 gap-3">
            <div>
              <label class="small mb-1 block">Climatização automática</label>
              <label class="inline-flex items-center gap-2">
                <input id="config-clima" type="checkbox" class="accent-secondary" />
                <span class="small">Ativar</span>
              </label>
            </div>
            <div>
              <label class="small mb-1 block">Iluminação ambiente</label>
              <input id="config-luz" type="color" value="#9b5de5" class="h-10 w-16 rounded" />
            </div>
          </div>
        </details>

        <details>
          <summary class="card-title cursor-pointer">🛡️ Segurança</summary>
          <div class="mt-3 grid grid-cols-1 md:grid-cols-2 gap-3">
            <div>
              <label class="small mb-1 block">Travamento automático</label>
              <label class="inline-flex items-center gap-2">
                <input id="config-travas" type="checkbox" class="accent-secondary" />
                <span class="small">Trancar ao dirigir</span>
              </label>
            </div>
            <div>
              <label class="small block mb-1">Assistentes</label>
              <div class="flex flex-col gap-2">
                <label><input id="config-faixa" type="checkbox" class="accent-secondary" /> Assistente de faixa</label>
                <label><input id="config-frenagem" type="checkbox" class="accent-secondary" /> Frenagem automática</label>
                <label><input id="config-piloto" type="checkbox" class="accent-secondary" /> Piloto automático inteligente</label>
              </div>
            </div>
          </div>
        </details>

        <div class="flex gap-2 mt-2">
          <button id="btn-save-configs" class="btn btn-primary">Salvar configurações</button>
          <button id="btn-export-configs" class="btn btn-ghost">Exportar configs</button>
          <button id="btn-import-configs" class="btn btn-ghost">Importar configs</button>
        </div>
      </div>
    </section>

  </main>

  <!-- Mobile bottom nav -->
  <nav class="mobile-nav fixed bottom-3 left-1/2 transform -translate-x-1/2 z-50 bg-primary/95 border border-secondary/20 rounded-xl p-2 flex gap-2 shadow-glow">
    <button class="tab-btn btn-ghost px-3" data-tab="dashboard" aria-label="Painel">🏠</button>
    <button class="tab-btn btn-ghost px-3" data-tab="perfis" aria-label="Perfis">👥</button>
    <button class="tab-btn btn-ghost px-3" data-tab="modulos" aria-label="Módulos">🔧</button>
    <button class="tab-btn btn-ghost px-3" data-tab="historico" aria-label="Histórico">📜</button>
    <button class="tab-btn btn-ghost px-3" data-tab="config" aria-label="Configurações">⚙️</button>
  </nav>

  <!-- Toasts -->
  <div class="toast" id="toast-root" aria-live="polite"></div>

  <!-- Hidden inputs for import -->
  <input type="file" id="import-file" accept=".json" class="hidden" />

  <script>
    /*****************************************************************
     * Dynamys — Enhanced single-file app
     * - Uses localStorage for persistence
     * - Profiles CRUD + drag reorder + search + apply
     * - Configs CRUD + export/import + theme apply
     * - History log (adds when profile applied)
     *****************************************************************/

    // ---------- Helpers ----------
    const DOM = (sel, ctx=document) => ctx.querySelector(sel);
    const DOMAll = (sel, ctx=document) => [...ctx.querySelectorAll(sel)];
    const toastRoot = DOM('#toast-root');

    function toast(msg, time=2500) {
      const el = document.createElement('div');
      el.className = 'card small mb-2 p-3 bg-black/80';
      el.innerHTML = `<div>${msg}</div>`;
      toastRoot.appendChild(el);
      setTimeout(()=> el.classList.add('opacity-0'), time-600);
      setTimeout(()=> el.remove(), time);
    }

    function nowISO() { return new Date().toISOString(); }
    function formatDate(iso) { return new Date(iso).toLocaleString(); }
    function uid() { return Math.random().toString(36).slice(2,9); }

    // ---------- Storage keys ----------
    const KEYS = {
      PROFILES: 'dynamys_profiles_v1',
      CONFIGS: 'dynamys_configs_v1',
      HISTORY: 'dynamys_history_v1'
    };

    // ---------- Default data ----------
    const DEFAULT_PROFILES = [
      { id: uid(), nome: "Carlos Silva", desc: "Trabalho - Econômico", img: "https://i.pravatar.cc/150?img=3" },
      { id: uid(), nome: "Mariana Costa", desc: "Lazer - Som + Esportivo", img: "https://i.pravatar.cc/150?img=5" }
    ];

    const DEFAULT_CONFIGS = {
      idioma: "Português (BR)",
      tema: "escuro",
      modo: "Normal",
      direcao: "Média",
      acelerador: "5",
      clima: true,
      luz: "#9b5de5",
      travas: true,
      faixa: true,
      frenagem: false,
      piloto: false
    };

    // ---------- App state ----------
    let state = {
      profiles: JSON.parse(localStorage.getItem(KEYS.PROFILES)) || DEFAULT_PROFILES,
      configs: JSON.parse(localStorage.getItem(KEYS.CONFIGS)) || DEFAULT_CONFIGS,
      history: JSON.parse(localStorage.getItem(KEYS.HISTORY)) || []
    };

    // ---------- Save helpers ----------
    function saveProfiles() {
      localStorage.setItem(KEYS.PROFILES, JSON.stringify(state.profiles));
    }
    function saveConfigs() {
      localStorage.setItem(KEYS.CONFIGS, JSON.stringify(state.configs));
    }
    function saveHistory() {
      localStorage.setItem(KEYS.HISTORY, JSON.stringify(state.history));
    }

    // ---------- Tabs ----------
    function showTab(tabId, btn=null) {
      DOMAll('.tab-content').forEach(t=> t.classList.add('hidden'));
      DOM('#'+tabId).classList.remove('hidden');
      // active tab styling
      DOMAll('nav button').forEach(b => b.classList.remove('text-secondary','font-bold'));
      if (btn) btn.classList.add('text-secondary','font-bold');
      // update last update stamp
      DOM('#last-update').textContent = new Date().toLocaleString();
    }
    DOMAll('.tab-btn').forEach(b => {
      b.addEventListener('click', (ev) => {
        showTab(b.dataset.tab, b);
      });
    });
    // default
    showTab('dashboard', DOM('nav button[data-tab="dashboard"]'));

    // ---------- Profiles UI ----------
    const perfilList = DOM('#perfil-list');
    const perfilForm = DOM('#perfil-form');
    const inputNome = DOM('#perfil-nome');
    const inputDesc = DOM('#perfil-desc');
    const inputImg = DOM('#perfil-img');
    const editIndex = DOM('#edit-index');
    const btnCancelEdit = DOM('#btn-cancel-edit');
    const btnApplyProfile = DOM('#btn-apply-profile');
    const searchProfiles = DOM('#search-profiles');

    function renderProfiles(filterText='') {
      perfilList.innerHTML = '';
      const filter = filterText.trim().toLowerCase();
      state.profiles.forEach((p, idx) => {
        if (filter && !(p.nome.toLowerCase().includes(filter) || p.desc.toLowerCase().includes(filter))) return;
        const card = document.createElement('div');
        card.className = 'card flex flex-col items-center text-center';
        card.setAttribute('draggable','true');
        card.dataset.index = idx;
        card.innerHTML = `
          <img src="${p.img || avatarFor(p.nome)}" class="avatar mb-3" alt="Avatar ${p.nome}" onerror="this.src='${avatarFor(p.nome)}'"/>
          <div class="font-semibold text-secondary">${escapeHtml(p.nome)}</div>
          <div class="small text-gray-300 mb-3">${escapeHtml(p.desc)}</div>
          <div class="flex gap-2">
            <button class="btn-yellow" data-action="edit" data-idx="${idx}">Editar</button>
            <button class="btn-red" data-action="delete" data-idx="${idx}">Excluir</button>
            <button class="btn btn-ghost" data-action="apply" data-idx="${idx}">Aplicar</button>
          </div>
        `;
        perfilList.appendChild(card);
      });
      attachProfileListeners();
      enableDragReorder();
    }

    function avatarFor(name) {
      const hash = (name||'')[0] ? name.charCodeAt(0)%70 : Math.floor(Math.random()*70);
      return `https://i.pravatar.cc/150?img=${(30 + (hash%40))}`;
    }

    function escapeHtml(s){ return (s+'').replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c])); }

    function attachProfileListeners() {
      DOMAll('[data-action="edit"]').forEach(btn => btn.addEventListener('click', e => {
        const idx = +btn.dataset.idx;
        startEditProfile(idx);
      }));
      DOMAll('[data-action="delete"]').forEach(btn => btn.addEventListener('click', e => {
        const idx = +btn.dataset.idx;
        deleteProfile(idx);
      }));
      DOMAll('[data-action="apply"]').forEach(btn => btn.addEventListener('click', e => {
        const idx = +btn.dataset.idx; applyProfileByIndex(idx);
      }));
    }

    function startEditProfile(idx) {
      const p = state.profiles[idx];
      inputNome.value = p.nome; inputDesc.value = p.desc; inputImg.value = p.img || '';
      editIndex.value = idx;
      DOM('#form-title').textContent = '✏️ Editar Perfil';
      btnCancelEdit.classList.remove('hidden');
      scrollToForm();
    }

    function resetProfileForm() {
      perfilForm.reset(); editIndex.value = '';
      DOM('#form-title').textContent = '➕ Adicionar / Editar Perfil';
      btnCancelEdit.classList.add('hidden');
    }

    perfilForm.addEventListener('submit', (e) => {
      e.preventDefault();
      const nome = inputNome.value.trim(); const desc = inputDesc.value.trim(); const img = inputImg.value.trim();
      if (!nome) return toast('Nome é obrigatório');
      if (editIndex.value === '') {
        state.profiles.push({ id: uid(), nome, desc, img });
        toast('Perfil adicionado');
      } else {
        const i = +editIndex.value;
        state.profiles[i] = { ...state.profiles[i], nome, desc, img };
        toast('Perfil atualizado');
      }
      saveProfiles(); renderProfiles(searchProfiles.value);
      resetProfileForm();
    });

    btnCancelEdit.addEventListener('click', resetProfileForm);

    function deleteProfile(idx) {
      const removed = state.profiles.splice(idx,1)[0];
      saveProfiles(); renderProfiles(searchProfiles.value);
      // undo option
      const undoId = uid();
      toast(`Perfil "${removed.nome}" excluído — <a id="undo-${undoId}" href="#" class="text-secondary">Desfazer</a>`, 4000);
      // attach undo handler
      setTimeout(()=> {
        const undoEl = DOM(`#undo-${undoId}`);
        if (undoEl) {
          undoEl.addEventListener('click', (ev)=> {
            ev.preventDefault();
            state.profiles.splice(idx,0,removed); saveProfiles(); renderProfiles(searchProfiles.value);
            toast('Exclusão desfeita');
          });
        }
      }, 50);
    }

    // Apply profile: merges profile preferences into configs and logs history
    function applyProfileByIndex(idx) {
      const p = state.profiles[idx];
      // Apply simple mapping: e.g., if desc contains "esportivo" set mode
      const desc = (p.desc || '').toLowerCase();
      if (desc.includes('esport')) state.configs.modo = 'Esportivo';
      if (desc.includes('econ') || desc.includes('eco')) state.configs.modo = 'Econômico';
      // example: if contains 'confort' enable clima
      if (desc.includes('confort')) state.configs.clima = true;
      // set light preset
      if (desc.includes('som')) state.configs.luz = '#7c3aed';
      // update selected configs persistently
      saveConfigs();
      renderConfigsToUI();
      // add history
      state.history.unshift({ id: uid(), time: nowISO(), perfil: p.nome, info: `Aplicado perfil: ${p.nome}` });
      saveHistory();
      renderHistory();
      toast(`Perfil "${p.nome}" aplicado`);
    }

    // quick-add profile
    DOM('#add-profile-quick').addEventListener('click', ()=> {
      const nome = `Perfil ${state.profiles.length + 1}`;
      state.profiles.push({ id: uid(), nome, desc: 'Novo perfil', img: ''});
      saveProfiles(); renderProfiles();
      toast('Perfil rápido criado');
    });

    DOM('#open-perfis').addEventListener('click', () => {
      showTab('perfis', DOM('nav button[data-tab="perfis"]'));
    });

    // search
    searchProfiles.addEventListener('input', ()=> renderProfiles(searchProfiles.value));

    // Export/Import profiles
    DOM('#btn-export-profiles').addEventListener('click', ()=> {
      downloadJSON(state.profiles, 'dynamys-profiles.json');
    });
    DOM('#btn-import-profiles').addEventListener('click', ()=> {
      openImportFile((data) => {
        if (!Array.isArray(data)) return toast('Arquivo inválido');
        // basic validation
        data = data.filter(p => p.nome);
        state.profiles = data.map(p => ({ id: p.id || uid(), nome: p.nome, desc: p.desc || '', img: p.img || '' }));
        saveProfiles(); renderProfiles();
        toast('Perfis importados');
      });
    });

    // Drag and drop reorder
    function enableDragReorder() {
      let dragged = null;
      DOMAll('#perfil-list [draggable="true"]').forEach(el => {
        el.addEventListener('dragstart', e => { dragged = el; el.classList.add('dragging'); e.dataTransfer.effectAllowed='move'; });
        el.addEventListener('dragend', e => { el.classList.remove('dragging'); dragged=null; });
        el.addEventListener('dragover', e => {
          e.preventDefault();
          const rect = el.getBoundingClientRect();
          const next = (e.clientY - rect.top) > (rect.height/2);
          if (dragged && el !== dragged) {
            if (next) el.parentNode.insertBefore(dragged, el.nextSibling);
            else el.parentNode.insertBefore(dragged, el);
          }
        });
        el.addEventListener('drop', e => {
          // rebuild order
          const newOrder = DOMAll('#perfil-list > div').map(nd => state.profiles[+nd.dataset.index]);
          // fix if indexes are not aligned, fallback to reading names
          if (newOrder.every(Boolean)) {
            state.profiles = newOrder;
          } else {
            // fallback: reorder by DOM content names
            const names = DOMAll('#perfil-list > div .font-semibold').map(n=> n.textContent.trim());
            state.profiles.sort((a,b)=> names.indexOf(a.nome) - names.indexOf(b.nome));
          }
          saveProfiles(); renderProfiles(searchProfiles.value);
        });
      });
    }

    // ---------- Configs ----------
    const configEls = {
      idioma: DOM('#config-idioma'),
      tema: DOM('#config-tema'),
      modo: DOM('#config-modo'),
      direcao: DOM('#config-direcao'),
      acelerador: DOM('#config-acelerador'),
      clima: DOM('#config-clima'),
      luz: DOM('#config-luz'),
      travas: DOM('#config-travas'),
      faixa: DOM('#config-faixa'),
      frenagem: DOM('#config-frenagem'),
      piloto: DOM('#config-piloto')
    };

    function renderConfigsToUI() {
      const c = state.configs;
      if (!c) return;
      configEls.idioma.value = c.idioma || DEFAULT_CONFIGS.idioma;
      configEls.tema.value = c.tema || DEFAULT_CONFIGS.tema;
      configEls.modo.value = c.modo || DEFAULT_CONFIGS.modo;
      configEls.direcao.value = c.direcao || DEFAULT_CONFIGS.direcao;
      configEls.acelerador.value = c.acelerador || DEFAULT_CONFIGS.acelerador;
      configEls.clima.checked = !!c.clima;
      configEls.luz.value = c.luz || DEFAULT_CONFIGS.luz;
      configEls.travas.checked = !!c.travas;
      configEls.faixa.checked = !!c.faixa;
      configEls.frenagem.checked = !!c.frenagem;
      configEls.piloto.checked = !!c.piloto;
      DOM('#acc-val').textContent = configEls.acelerador.value;
      applyThemeToUI(c.tema || DEFAULT_CONFIGS.tema);
    }
    function readConfigsFromUI() {
      state.configs = {
        idioma: configEls.idioma.value,
        tema: configEls.tema.value,
        modo: configEls.modo.value,
        direcao: configEls.direcao.value,
        acelerador: configEls.acelerador.value,
        clima: configEls.clima.checked,
        luz: configEls.luz.value,
        travas: configEls.travas.checked,
        faixa: configEls.faixa.checked,
        frenagem: configEls.frenagem.checked,
        piloto: configEls.piloto.checked
      };
      saveConfigs();
    }

    // save config buttons
    DOM('#btn-save-configs').addEventListener('click', ()=> {
      readConfigsFromUI(); toast('Configurações salvas');
    });

    // export/import configs
    DOM('#btn-export-configs').addEventListener('click', ()=> downloadJSON(state.configs, 'dynamys-configs.json'));
    DOM('#btn-import-configs').addEventListener('click', ()=> {
      openImportFile((data) => {
        if (typeof data !== 'object') return toast('Arquivo inválido');
        state.configs = { ...DEFAULT_CONFIGS, ...data };
        saveConfigs(); renderConfigsToUI(); toast('Configurações importadas');
      });
    });

    DOM('#btn-export-configs').addEventListener('click', ()=> downloadJSON(state.configs, 'dynamys-configs.json'));

    // accelerate UI update on range
    configEls.acelerador.addEventListener('input', ()=> {
      DOM('#acc-val').textContent = configEls.acelerador.value;
    });

    // quick apply suggestion button: apply a sample config
    DOM('#apply-sug').addEventListener('click', ()=> {
      state.configs.modo = 'Econômico';
      state.configs.clima = true;
      state.configs.luz = '#8b5cf6';
      saveConfigs(); renderConfigsToUI();
      toast('Sugestão aplicada');
    });

    // ---------- History ----------
    function renderHistory() {
      const el = DOM('#history-list'); el.innerHTML = '';
      if (!state.history.length) { el.innerHTML = '<div class="small text-muted">Sem histórico</div>'; return; }
      state.history.forEach(h => {
        const row = document.createElement('div');
        row.className = 'card flex items-center justify-between';
        row.innerHTML = `<div>
          <div class="font-semibold">${escapeHtml(h.perfil || '—')}</div>
          <div class="small text-muted">${formatDate(h.time)} • ${escapeHtml(h.info || '')}</div>
        </div>
        <div class="small text-muted">${h.km ? h.km+' km' : ''}</div>`;
        el.appendChild(row);
      });
    }
    DOM('#btn-clear-hist').addEventListener('click', ()=> {
      if (!confirm('Limpar todo o histórico?')) return;
      state.history = []; saveHistory(); renderHistory(); toast('Histórico limpo');
    });
    DOM('#btn-export-hist').addEventListener('click', ()=> downloadJSON(state.history, 'dynamys-history.json'));

    // ---------- Helpers: import/export ----------
    function downloadJSON(obj, filename) {
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(obj, null, 2));
      const a = document.createElement('a'); a.setAttribute('href', dataStr); a.setAttribute('download', filename);
      document.body.appendChild(a); a.click(); a.remove();
    }
    function openImportFile(onData) {
      const f = DOM('#import-file');
      f.onchange = (ev) => {
        const file = ev.target.files[0]; if (!file) return;
        const reader = new FileReader();
        reader.onload = e => {
          try {
            const data = JSON.parse(e.target.result);
            onData(data);
          } catch(err) { toast('Erro ao ler JSON'); }
        };
        reader.readAsText(file);
        f.value = '';
      };
      f.click();
    }

    // ---------- Utilities ----------
    function scrollToForm() { DOM('#perfil-form').scrollIntoView({behavior:'smooth', block:'center'}); }

    // ---------- Apply theme ----------
    function applyThemeToUI(theme) {
      const body = document.getElementById('appBody');
      if (theme === 'claro') {
        body.classList.remove('bg-primary','text-light');
        body.classList.add('bg-light','text-primary');
        // lighter card bg
        document.documentElement.style.setProperty('--card-bg','#ffffff');
      } else {
        body.classList.remove('bg-light','text-primary');
        body.classList.add('bg-primary','text-light');
        document.documentElement.style.setProperty('--card-bg','#151515');
      }
    }

    function applyThemeToUIAndSave(t) {
      state.configs.tema = t; saveConfigs(); applyThemeToUI(t);
    }

    // ---------- Apply profile direct button (from form) ----------
    DOM('#btn-apply-profile').addEventListener('click', ()=> {
      const idx = editIndex.value;
      if (idx === '') return toast('Selecione um perfil para aplicar (editar e depois aplicar) ou use Aplicar no cartão');
      applyProfileByIndex(+idx);
    });

    // ---------- Clear localStorage (danger) ----------
    DOM('#clear-all').addEventListener('click', ()=> {
      if (!confirm('Resetar todos os dados locais? Isso irá apagar perfis, configs e histórico.')) return;
      localStorage.removeItem(KEYS.PROFILES); localStorage.removeItem(KEYS.CONFIGS); localStorage.removeItem(KEYS.HISTORY);
      // reset state
      state.profiles = DEFAULT_PROFILES.slice(); state.configs = {...DEFAULT_CONFIGS}; state.history = [];
      saveProfiles(); saveConfigs(); saveHistory();
      renderProfiles(); renderConfigsToUI(); renderHistory();
      toast('Dados resetados');
    });

    // ---------- Export/import config quick button (top) ----------
    DOM('#export-config').addEventListener('click', ()=> downloadJSON(state.configs, 'dynamys-configs.json'));
    DOM('#btn-import-configs').addEventListener('click', ()=> {
      openImportFile((data) => {
        if (!data) return toast('Arquivo inválido');
        state.configs = { ...DEFAULT_CONFIGS, ...data };
        saveConfigs(); renderConfigsToUI(); toast('Configurações importadas');
      });
    });

    // ---------- Profiles export top (also accessible earlier) ----------
    DOM('#btn-export-profiles').addEventListener('click', ()=> downloadJSON(state.profiles, 'dynamys-profiles.json'));

    // ---------- Simulate live data ----------
    function simulateData() {
      const speed = Math.floor(Math.random()*160);
      const battery = Math.floor(30 + Math.random()*70);
      const fuel = (6 + Math.random()*10).toFixed(1);
      const temp = Math.floor(70 + Math.random()*40);
      DOM('#speed-display').textContent = `${speed} km/h`;
      DOM('#battery-status').textContent = `${battery}%`;
      DOM('#fuel-status').textContent = `${fuel} km/L`;
      DOM('#temp-status').textContent = `${temp} °C`;
      // gauge draw
      drawGauge(speed);
    }
    setInterval(simulateData, 2000);

    // ---------- Speed gauge drawing ----------
    function drawGauge(speed) {
      const canvas = DOM('#speed-gauge');
      const ctx = canvas.getContext('2d');
      const w = canvas.width, h = canvas.height;
      ctx.clearRect(0,0,w,h);
      const cx = w/2, cy = h;
      const radius = Math.min(w/2 - 10, h - 10);
      // background arc
      ctx.beginPath();
      ctx.arc(cx, cy, radius, Math.PI, 2*Math.PI);
      ctx.lineWidth = 12;
      ctx.strokeStyle = '#222';
      ctx.stroke();
      // value arc
      const max = 180;
      const angle = Math.PI * (speed / max);
      ctx.beginPath();
      ctx.arc(cx, cy, radius, Math.PI, Math.PI + angle);
      ctx.strokeStyle = '#9b5de5';
      ctx.lineWidth = 12;
      ctx.stroke();
    }

    // ---------- History render & initial load ----------
    function initialRender() {
      renderProfiles();
      renderConfigsToUI();
      renderHistory();
    }
    initialRender();

    // ---------- Event: theme selection live save ----------
    configEls.tema.addEventListener('change', ()=> {
      const t = configEls.tema.value;
      applyThemeToUIAndSave(t);
    });

    // ---------- last bits: utility bindings ----------
    function applyThemeToUIAndSave(t) { state.configs.tema = t; saveConfigs(); applyThemeToUI(t); }
    // make sure UI config elements exist before binding others
    DOMAll('input[type="range"]').forEach(r => r.addEventListener('input', ()=> { if (r.id==='config-acelerador') DOM('#acc-val').textContent = r.value; }));

    // Accessibility: keyboard shortcut to toggle theme (T)
    document.addEventListener('keydown', (e)=> {
      if (e.key.toLowerCase() === 't' && e.altKey) {
        const newTheme = (state.configs.tema === 'escuro') ? 'claro' : 'escuro';
        applyThemeToUIAndSave(newTheme); toast('Tema alterado: ' + newTheme);
      }
    });

    // ---------- Utility: escape and safety ----------
    // (already defined escapeHtml above)

  </script>
</body>
</html>

