<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>Comparador de Placas</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-50 text-gray-800 min-h-screen flex flex-col items-center p-8">

  <div class="w-full max-w-6xl">
    <h1 class="text-3xl font-bold text-center text-indigo-600 mb-8">🚗 Comparador de Placas</h1>

    <!-- FORMATADOR DE PLACAS -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
      <div>
        <label for="formatador" class="block text-sm font-semibold mb-2">✍️ Cole ou digite placas aqui:</label>
        <textarea id="formatador" class="w-full p-3 border border-indigo-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-400" placeholder="Ex: abc1234"></textarea>
        <button id="btnFormatar" class="mt-3 bg-indigo-600 text-white px-4 py-2 rounded shadow hover:bg-indigo-700 transition">
          🛠️ Adicionar hífen e caixa alta
        </button>
      </div>
      <div>
        <label for="formatadoResultado" class="block text-sm font-semibold mb-2">📎 Placas formatadas:</label>
        <div class="flex">
          <textarea id="formatadoResultado" class="w-full p-3 bg-gray-100 border border-gray-300 rounded-lg shadow-sm text-gray-700" readonly></textarea>
          <button onclick="copiarTexto('formatadoResultado')" class="ml-2 bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600 transition">📋 Copiar</button>
        </div>
      </div>
    </div>

    <!-- CAMPOS DE COMPARAÇÃO -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
      <div>
        <label for="placasAntigas" class="block text-sm font-semibold mb-2">📄 Placas desatualizadas (antigas):</label>
        <textarea id="placasAntigas" class="w-full p-3 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-400" placeholder="Uma placa por linha..."></textarea>
      </div>
      <div>
        <label for="placasNovas" class="block text-sm font-semibold mb-2">🆕 Placas atualizadas (novas):</label>
        <textarea id="placasNovas" class="w-full p-3 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-400" placeholder="Uma placa por linha..."></textarea>
      </div>
    </div>

    <div class="text-center mb-10">
      <button onclick="compararPlacas()" class="bg-indigo-600 text-white px-6 py-3 rounded-lg text-lg shadow hover:bg-indigo-700 transition">🔍 Comparar Placas</button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <div class="bg-white p-5 rounded-xl shadow">
        <h2 class="text-xl font-semibold text-green-600 mb-3">🔁 Placas que permanecem ativas</h2>
        <div class="flex">
          <pre id="placasRepetidas" class="text-sm whitespace-pre-wrap text-gray-700 min-h-[120px]">Nenhuma</pre>
          <button onclick="copiarTexto('placasRepetidas')" class="ml-2 bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600 transition">📋 Copiar</button>
        </div>
      </div>
      <div class="bg-white p-5 rounded-xl shadow">
        <h2 class="text-xl font-semibold text-red-500 mb-3">❌ Placas que devem ser desativadas</h2>
        <div class="flex">
          <pre id="placasDesativar" class="text-sm whitespace-pre-wrap text-gray-700 min-h-[120px]">Nenhuma</pre>
          <button onclick="copiarTexto('placasDesativar')" class="ml-2 bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600 transition">📋 Copiar</button>
        </div>
      </div>
      <div class="bg-white p-5 rounded-xl shadow">
        <h2 class="text-xl font-semibold text-blue-500 mb-3">✅ Placas que devem ser cadastradas</h2>
        <div class="flex">
          <pre id="placasCadastrar" class="text-sm whitespace-pre-wrap text-gray-700 min-h-[120px]">Nenhuma</pre>
          <button onclick="copiarTexto('placasCadastrar')" class="ml-2 bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600 transition">📋 Copiar</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    // BOTÃO DE FORMATAR COM HÍFEN E CAIXA ALTA
    document.getElementById('btnFormatar').addEventListener('click', () => {
      const input = document.getElementById('formatador').value;
      const linhas = input.split('\n');

      const formatadas = linhas.map(placa => {
        const clean = placa.replace(/[^a-zA-Z0-9]/g, '').toUpperCase();
        return clean.length === 7 ? `${clean.slice(0, 3)}-${clean.slice(3)}` : clean;
      });

      document.getElementById('formatadoResultado').value = formatadas.join('\n');
    });

    // FUNÇÃO PARA ADICIONAR O HÍFEN AUTOMATICAMENTE AO DIGITAR
    function formatarPlacaComHifen(event) {
      let valor = event.target.value;
      valor = valor.replace(/[^a-zA-Z0-9]/g, '').toUpperCase(); // Remove caracteres não alfanuméricos e coloca em caixa alta
      if (valor.length > 3) {
        valor = `${valor.slice(0, 3)}-${valor.slice(3, 7)}`;
      }
      event.target.value = valor;
    }

    // ADICIONANDO O EVENTO PARA FORMATAÇÃO AUTOMÁTICA
    document.getElementById('placasAntigas').addEventListener('input', formatarPlacaComHifen);
    document.getElementById('placasNovas').addEventListener('input', formatarPlacaComHifen);

    // COMPARAÇÃO DAS PLACAS
    function compararPlacas() {
      const antigas = document.getElementById('placasAntigas').value.split('\n').map(p => p.trim().toUpperCase()).filter(p => p);
      const novas = document.getElementById('placasNovas').value.split('\n').map(p => p.trim().toUpperCase()).filter(p => p);

      const setAntigas = new Set(antigas);
      const setNovas = new Set(novas);

      const repetidas = antigas.filter(p => setNovas.has(p));
      const desativar = antigas.filter(p => !setNovas.has(p));
      const cadastrar = novas.filter(p => !setAntigas.has(p));

      document.getElementById('placasRepetidas').innerText = repetidas.join('\n') || 'Nenhuma';
      document.getElementById('placasDesativar').innerText = desativar.join('\n') || 'Nenhuma';
      document.getElementById('placasCadastrar').innerText = cadastrar.join('\n') || 'Nenhuma';
    }

    // FUNÇÃO PARA COPIAR O CONTEÚDO
    function copiarTexto(id) {
      const texto = document.getElementById(id).innerText || document.getElementById(id).value;
      const tempInput = document.createElement('textarea');
      document.body.appendChild(tempInput);
      tempInput.value = texto;
      tempInput.select();
      document.execCommand('copy');
      document.body.removeChild(tempInput);
      alert('Texto copiado!');
    }
  </script>

</body>
</html>
