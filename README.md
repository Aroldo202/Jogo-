<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Venda de Ingressos - Arena</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      background: #f7f7f7;
    }
    header {
      background: #000;
      color: #fff;
      text-align: center;
      padding: 1rem;
    }
    .container {
      display: flex;
      flex-direction: column;
      gap: 1rem;
      padding: 1rem;
      max-width: 900px;
      margin: auto;
    }
    .arena {
      background: #fff;
      border-radius: 12px;
      padding: 1rem;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    }
    svg {
      max-width: 100%;
      height: auto;
      cursor: pointer;
    }
    .cart, .checkout {
      background: #fff;
      border-radius: 12px;
      padding: 1rem;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    }
    button {
      padding: 0.5rem 1rem;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
    }
    button:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
    .btn-primary { background: black; color: white; }
    .btn-secondary { background: #ddd; }
    .cart-items div { display: flex; justify-content: space-between; margin: 0.5rem 0; }
    input, select {
      width: 100%;
      padding: 0.5rem;
      margin: 0.3rem 0;
      border: 1px solid #ccc;
      border-radius: 6px;
    }
    @media (min-width: 768px) {
      .container { flex-direction: row; }
      .arena, .cart, .checkout { flex: 1; }
    }
  </style>
</head>
<body>
  <header>
    <h1>Venda de Ingressos - Arena</h1>
  </header>
  <div class="container">
    <div class="arena">
      <h2>Mapa da Arena</h2>
      <svg viewBox="0 0 300 200">
        <g data-id="norte" fill="#4ade80"><rect x="20" y="20" width="100" height="40" /></g>
        <g data-id="sul" fill="#60a5fa"><rect x="20" y="140" width="100" height="40" /></g>
        <g data-id="leste" fill="#facc15"><rect x="180" y="20" width="100" height="40" /></g>
        <g data-id="oeste" fill="#f87171"><rect x="180" y="140" width="100" height="40" /></g>
      </svg>
      <p>Clique em um setor para selecionar ingressos.</p>
    </div>

    <div class="cart">
      <h2>Carrinho</h2>
      <div class="cart-items"></div>
      <p><strong>Total:</strong> <span id="total">R$ 0,00</span></p>
      <button id="checkoutBtn" class="btn-primary" disabled>Ir para pagamento</button>
    </div>

    <div class="checkout">
      <h2>Checkout</h2>
      <form id="checkoutForm">
        <input type="text" id="nome" placeholder="Nome completo" required />
        <input type="text" id="cpf" placeholder="CPF (apenas números)" required maxlength="11" />
        <select id="pagamento" required>
          <option value="">Forma de pagamento</option>
          <option value="pix">PIX</option>
          <option value="credito">Cartão de Crédito</option>
          <option value="debito">Cartão de Débito</option>
        </select>
        <button type="submit" class="btn-primary">Finalizar Compra</button>
      </form>
      <div id="mensagem"></div>
    </div>
  </div>

  <script>
    const setores = {
      norte: { nome: "Arquibancada Norte", preco: 50 },
      sul: { nome: "Arquibancada Sul", preco: 50 },
      leste: { nome: "Cadeira Leste", preco: 100 },
      oeste: { nome: "Cadeira Oeste", preco: 100 }
    };

    let carrinho = [];

    document.querySelectorAll("svg g").forEach(g => {
      g.addEventListener("click", () => {
        const id = g.dataset.id;
        const setor = setores[id];
        const existente = carrinho.find(i => i.id === id);
        if (existente) existente.qtd++;
        else carrinho.push({ id, nome: setor.nome, preco: setor.preco, qtd: 1 });
        atualizarCarrinho();
      });
    });

    function atualizarCarrinho() {
      const div = document.querySelector(".cart-items");
      div.innerHTML = "";
      let total = 0;
      carrinho.forEach(item => {
        total += item.preco * item.qtd;
        const linha = document.createElement("div");
        linha.innerHTML = `
          <span>${item.nome} (x${item.qtd})</span>
          <span>R$ ${(item.preco * item.qtd).toFixed(2)}</span>
        `;
        div.appendChild(linha);
      });
      document.getElementById("total").textContent = `R$ ${total.toFixed(2)}`;
      document.getElementById("checkoutBtn").disabled = carrinho.length === 0;
    }

    document.getElementById("checkoutForm").addEventListener("submit", e => {
      e.preventDefault();
      const nome = document.getElementById("nome").value;
      const cpf = document.getElementById("cpf").value;
      if (!/^\d{11}$/.test(cpf)) {
        alert("CPF inválido! Digite 11 números.");
        return;
      }
      document.getElementById("mensagem").innerHTML = `<p>Compra confirmada! 🎉<br>Obrigado, ${nome}. Seu ingresso foi enviado para o e-mail cadastrado.</p>`;
      carrinho = [];
      atualizarCarrinho();
    });
  </script>
</body>
</html>
