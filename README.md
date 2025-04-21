# Discount-Project

This code project is a brief demonstration of Discount App workflow where demonstrates the vendor and user side. In this case the vendor enter the product with price per kg and the disccount applied. In the next step the user select the offer, payment type and delivery or take-away.

<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Plataforma Antidesperdício</title>

  <!-- Fontes e ícones -->
  <link href="https://fonts.googleapis.com/css2?family=Nunito&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

  <!-- Estilo -->
  <style>
    body {
      font-family: 'Nunito', sans-serif;
      background: #f1f7f5;
      padding: 20px;
      color: #333;
    }

    .container {
      max-width: 800px;
      margin: auto;
      background: #ffffff;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }

    h2, h3 {
      text-align: center;
      color: #2d6a4f;
    }

    input, select, button {
      width: 100%;
      padding: 12px;
      margin: 8px 0;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 16px;
      transition: border 0.3s ease;
    }

    input:focus, select:focus {
      border-color: #52b788;
      outline: none;
    }

    button {
      background-color: #40916c;
      color: white;
      border: none;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background-color: #2d6a4f;
    }

    ul {
      list-style-type: none;
      padding: 0;
    }

    li {
      background-color: #d8f3dc;
      margin-bottom: 10px;
      padding: 15px;
      border-radius: 8px;
      cursor: pointer;
      box-shadow: 0 1px 4px rgba(0,0,0,0.05);
    }

    li:hover {
      background-color: #b7e4c7;
    }

    .carrinho {
      background-color: #fff3cd;
      padding: 20px;
      border-radius: 10px;
      margin-top: 20px;
      border-left: 5px solid #f9c74f;
    }

    .mensagem {
      background-color: #d8f3dc;
      padding: 20px;
      border-radius: 10px;
      margin-top: 20px;
      font-weight: bold;
      font-size: 18px;
      color: #2d6a4f;
      text-align: center;
      border-left: 5px solid #40916c;
    }

    .hidden {
      display: none;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Fornecedor - Cadastro de Produto</h2>
    <form id="form-produto">
      <input type="text" id="nome" placeholder="Nome do Produto" required />
      <input type="number" id="preco" placeholder="Preço original (R$)" step="0.01" required />
      <input type="number" id="desconto" placeholder="Desconto (%)" step="0.01" required />
      <button type="submit">Cadastrar Produto</button>
    </form>

    <h2><i class="fas fa-shopping-cart"></i> Consumidor - Produtos Disponíveis</h2>
    <ul id="lista-produtos"></ul>

    <div class="carrinho hidden" id="carrinho">
      <h3>Carrinho de Compras</h3>
      <ul id="lista-carrinho"></ul>
      <p><strong>Total: R$ <span id="valor-total">0.00</span></strong></p>

      <label for="pagamento">Forma de pagamento:</label>
      <select id="pagamento">
        <option value="">-- Selecione --</option>
        <option value="Dinheiro">Dinheiro</option>
        <option value="PIX">PIX</option>
        <option value="Cartão">Cartão</option>
      </select>

      <label for="recebimento">Forma de recebimento:</label>
      <select id="recebimento">
        <option value="">-- Selecione --</option>
        <option value="Retirada">Retirada</option>
        <option value="Entrega">Entrega</option>
      </select>

      <button onclick="finalizarCompra()">Finalizar Compra</button>
    </div>

    <div id="mensagem" class="mensagem hidden"></div>
  </div>

  <script>
    const produtos = [];
    const carrinho = [];

    const form = document.getElementById("form-produto");
    const lista = document.getElementById("lista-produtos");
    const listaCarrinho = document.getElementById("lista-carrinho");
    const valorTotal = document.getElementById("valor-total");
    const divCarrinho = document.getElementById("carrinho");
    const mensagem = document.getElementById("mensagem");

    form.addEventListener("submit", (e) => {
      e.preventDefault();
      const nome = document.getElementById("nome").value;
      const preco = parseFloat(document.getElementById("preco").value);
      const desconto = parseFloat(document.getElementById("desconto").value);

      const precoFinal = (preco * (1 - desconto / 100)).toFixed(2);

      produtos.push({ nome, preco, desconto, precoFinal });
      form.reset();
      atualizarLista();
    });

    function atualizarLista() {
      lista.innerHTML = "";
      produtos.forEach((produto, index) => {
        const li = document.createElement("li");
        li.innerHTML = `
          <strong>${produto.nome}</strong><br>
          Preço original: R$ ${produto.preco.toFixed(2)}<br>
          Desconto: ${produto.desconto}%<br>
          <strong>Preço com desconto: R$ ${produto.precoFinal}</strong>
        `;
        li.onclick = () => adicionarAoCarrinho(index);
        lista.appendChild(li);
      });
    }

    function adicionarAoCarrinho(index) {
      const produto = produtos[index];
      carrinho.push(produto);
      atualizarCarrinho();
    }

    function atualizarCarrinho() {
      listaCarrinho.innerHTML = "";
      let total = 0;
      carrinho.forEach((item) => {
        const li = document.createElement("li");
        li.textContent = `${item.nome} - R$ ${item.precoFinal}`;
        listaCarrinho.appendChild(li);
        total += parseFloat(item.precoFinal);
      });
      valorTotal.textContent = total.toFixed(2);
      divCarrinho.classList.remove("hidden");
    }

    function finalizarCompra() {
      const pagamento = document.getElementById("pagamento").value;
      const recebimento = document.getElementById("recebimento").value;

      if (!pagamento || !recebimento || carrinho.length === 0) {
        alert("Preencha todas as opções e adicione pelo menos um produto.");
        return;
      }

      mensagem.innerHTML = `
        Você escolheu pagar com <strong>${pagamento}</strong> e optou por <strong>${recebimento}</strong>.<br><br>
        Total da compra: <strong>R$ ${valorTotal.textContent}</strong><br><br>
        <strong>Obrigado por lutar contra o desperdício de alimentos! 🌱</strong>
      `;
      mensagem.classList.remove("hidden");
    }
  </script>
</body>
</html>
