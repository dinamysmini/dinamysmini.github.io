# dinamysmini.github.io
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dynamys Perfis</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-950 text-gray-100 font-sans text-lg">
  <!-- Header -->
  <header class="flex items-center justify-between px-6 py-4 bg-gray-900 border-b border-gray-700">
    <h1 class="text-3xl font-bold text-cyan-400">⚡ Dynamys Perfis</h1>
  </header>

  <main class="p-6">
    <h2 class="text-2xl font-semibold mb-4">👥 Gerenciar Perfis</h2>

    <!-- Lista de Perfis -->
    <div id="perfil-list" class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
      <!-- Perfis vão aparecer aqui dinamicamente -->
    </div>

    <!-- Formulário de Perfis -->
    <div class="bg-gray-900 p-4 rounded-xl shadow max-w-md">
      <h3 class="text-xl font-semibold text-cyan-300 mb-3" id="form-title">➕ Adicionar Perfil</h3>
      <form id="perfil-form" class="space-y-3">
        <input type="hidden" id="edit-index">
        <div>
          <label class="block text-base">Nome</label>
          <input id="perfil-nome" type="text" class="w-full p-2 rounded bg-gray-800 border border-gray-600" required>
        </div>
        <div>
          <label class="block text-base">Descrição</label>
          <input id="perfil-desc" type="text" class="w-full p-2 rounded bg-gray-800 border border-gray-600" required>
        </div>
        <div>
          <label class="block text-base">Imagem (URL)</label>
          <input id="perfil-img" type="url" placeholder="https://..." class="w-full p-2 rounded bg-gray-800 border border-gray-600">
        </div>
        <button type="submit" class="bg-cyan-500 px-4 py-2 rounded-lg text-white font-semibold">Salvar</button>
        <button type="button" id="cancel-edit" class="hidden bg-gray-600 px-4 py-2 rounded-lg text-white">Cancelar</button>
      </form>
    </div>
  </main>

  <script>
    // Perfis iniciais
    let perfis = [
      { nome: "Carlos Silva", desc: "Perfil Trabalho - Econômico + Conforto", img: "https://i.pravatar.cc/80?img=3" },
      { nome: "Mariana Costa", desc: "Perfil Lazer - Som + Esportivo", img: "https://i.pravatar.cc/80?img=5" },
      { nome: "João & Ana", desc: "Perfil Família - Espaço + Segurança", img: "https://i.pravatar.cc/80?img=8" }
    ];

    const perfilList = document.getElementById("perfil-list");
    const perfilForm = document.getElementById("perfil-form");
    const formTitle = document.getElementById("form-title");
    const cancelEditBtn = document.getElementById("cancel-edit");
    const editIndexInput = document.getElementById("edit-index");

    function renderPerfis() {
      perfilList.innerHTML = "";
      perfis.forEach((p, index) => {
        const div = document.createElement("div");
        div.className = "bg-gray-900 p-4 rounded-xl shadow flex flex-col items-center";
        div.innerHTML = `
          <img src="${p.img || "https://via.placeholder.com/80"}" class="w-16 h-16 rounded-full border-2 border-cyan-400 mb-3">
          <h3 class="font-semibold text-cyan-300 text-lg">${p.nome}</h3>
          <p class="text-base text-gray-400 mb-3">${p.desc}</p>
          <div class="flex space-x-2">
            <button onclick="editPerfil(${index})" class="bg-yellow-500 px-3 py-1 rounded-lg">Editar</button>
            <button onclick="deletePerfil(${index})" class="bg-red-500 px-3 py-1 rounded-lg">Excluir</button>
          </div>
        `;
        perfilList.appendChild(div);
      });
    }

    perfilForm.addEventListener("submit", function(e) {
      e.preventDefault();
      const nome = document.getElementById("perfil-nome").value;
      const desc = document.getElementById("perfil-desc").value;
      const img = document.getElementById("perfil-img").value;

      if (editIndexInput.value === "") {
        // Adicionar
        perfis.push({ nome, desc, img });
      } else {
        // Editar
        const i = editIndexInput.value;
        perfis[i] = { nome, desc, img };
      }

      resetForm();
      renderPerfis();
    });

    function editPerfil(index) {
      const p = perfis[index];
      document.getElementById("perfil-nome").value = p.nome;
      document.getElementById("perfil-desc").value = p.desc;
      document.getElementById("perfil-img").value = p.img;
      editIndexInput.value = index;
      formTitle.textContent = "✏️ Editar Perfil";
      cancelEditBtn.classList.remove("hidden");
    }

    function deletePerfil(index) {
      if (confirm("Tem certeza que deseja excluir este perfil?")) {
        perfis.splice(index, 1);
        renderPerfis();
      }
    }

    function resetForm() {
      perfilForm.reset();
      editIndexInput.value = "";
      formTitle.textContent = "➕ Adicionar Perfil";
      cancelEditBtn.classList.add("hidden");
    }

    cancelEditBtn.addEventListener("click", resetForm);

    // Render inicial
    renderPerfis();
  </script>
</body>
</html>

