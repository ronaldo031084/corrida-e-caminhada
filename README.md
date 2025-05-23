<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>1ª Corrida e Caminhada Drummond</title>
  <style>
    body { font-family: Arial; margin: 0; padding: 0; text-align: center; }
    main { max-width: 500px; margin: auto; padding: 20px; }
    img { width: 100%; max-width: 500px; }
    form { display: flex; flex-direction: column; gap: 10px; }
    input, select, button { padding: 10px; font-size: 1rem; }
    .pix { background: #eee; padding: 10px; word-break: break-all; }
    .hidden { display: none; }
  </style>
</head>
<body>
  <main>
    <img src="logo.png" alt="Logo Corrida Drummond" />
    <h1>1ª Corrida e Caminhada Drummond Futebol Clube</h1>

    <form id="inscricaoForm">
      <input type="text" id="nome" placeholder="Nome completo" required />
      <input type="date" id="nascimento" required />
      <input type="text" id="cpf" placeholder="CPF (somente números)" required />
      <select id="atividade" required>
        <option value="Corrida">Corrida</option>
        <option value="Caminhada">Caminhada</option>
      </select>
      <select id="camiseta" required>
        <option value="P">Camiseta P</option>
        <option value="M" selected>Camiseta M</option>
        <option value="G">Camiseta G</option>
        <option value="NÃO">NÃO</option>
      </select>
      <button type="submit">Enviar Inscrição</button>
      <p id="erro" style="color: red;"></p>
    </form>

    <div id="confirmacao" class="hidden">
      <h2>Inscrição enviada com sucesso!</h2>
      <p id="codigoInscricao" style="font-weight: bold; font-size: 1.2rem;"></p>
      <p>Valor: <strong>R$ 30,00</strong></p>
      <p>Chave PIX:</p>
      <div class="pix">chavepix@exemplo.com</div>
      <img src="https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=chavepix@exemplo.com" alt="QR Code Pix" />
      <p>Envie o comprovante para: <strong>(41) 99999-9999</strong></p>
      <button id="voltarBtn">Voltar ao Início</button>
    </div>
  </main>

  <script>
    function validarCPF(cpf) {
      cpf = cpf.replace(/[^\d]+/g, '');
      if (cpf.length !== 11 || /^(\d)\1{10}$/.test(cpf)) return false;
      let soma = 0, resto;
      for (let i = 1; i <= 9; i++) soma += parseInt(cpf[i - 1]) * (11 - i);
      resto = (soma * 10) % 11;
      if (resto === 10 || resto === 11) resto = 0;
      if (resto !== parseInt(cpf[9])) return false;
      soma = 0;
      for (let i = 1; i <= 10; i++) soma += parseInt(cpf[i - 1]) * (12 - i);
      resto = (soma * 10) % 11;
      if (resto === 10 || resto === 11) resto = 0;
      return resto === parseInt(cpf[10]);
    }

    function gerarCodigo() {
      const timestamp = Date.now().toString().slice(-5);
      return `DRU2025-${timestamp}`;
    }

    document.getElementById("inscricaoForm").addEventListener("submit", function(e) {
      e.preventDefault();
      const nome = document.getElementById("nome").value;
      const nascimento = document.getElementById("nascimento").value;
      const cpf = document.getElementById("cpf").value;
      const atividade = document.getElementById("atividade").value;
      const camiseta = document.getElementById("camiseta").value;

      const erro = document.getElementById("erro");
      if (!validarCPF(cpf)) {
        erro.textContent = "CPF inválido.";
        return;
      }

      erro.textContent = "";

      const codigo = gerarCodigo();

      // Salva inscrição no localStorage com código
      localStorage.setItem("inscricao-" + cpf, JSON.stringify({ nome, nascimento, cpf, atividade, camiseta, codigo }));

      // Mostrar confirmação
      document.getElementById("inscricaoForm").classList.add("hidden");
      const codigoEl = document.getElementById("codigoInscricao");
      codigoEl.textContent = `Seu código de inscrição é: ${codigo}`;
      document.getElementById("confirmacao").classList.remove("hidden");
    });

    document.getElementById("voltarBtn").addEventListener("click", function() {
      window.location.reload();
    });
  </script>
</body>
</html>
