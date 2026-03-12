<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>diário</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1e1e2f;
            color: #ffffff;
            text-align: center;
            padding: 20px;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background-color: #2a2a40;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
        }
        h1 { color: #4CAF50; }
        .money-display {
            font-size: 24px;
            font-weight: bold;
            margin: 20px 0;
            padding: 10px;
            background-color: #000;
            border-radius: 5px;
        }
        .warning { color: #ff4d4d; font-weight: bold; min-height: 20px; }
        .tabs { margin-bottom: 20px; }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 10px 15px;
            margin: 5px;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
            transition: 0.2s;
        }
        button:hover { background-color: #45a049; }
        .btn-danger { background-color: #f44336; }
        .btn-danger:hover { background-color: #da190b; }
        .btn-tab { background-color: #2196F3; }
        .btn-tab:hover { background-color: #0b7dda; }
        .btn-reset { background-color: #8a2be2; margin-top: 20px; width: 100%; }
        .btn-reset:hover { background-color: #6a1b9a; }
        
        /* Ocultar abas inativas */
        .tab-content { display: none; }
        .active-tab { display: block; }

        .store-item {
            background-color: #3a3a55;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        input {
            padding: 10px;
            font-size: 16px;
            width: 80%;
            margin-bottom: 10px;
            border-radius: 5px;
            border: none;
        }
        #inventoryDisplay {
            margin-top: 20px;
            text-align: left;
            background-color: #111;
            padding: 10px;
            border-radius: 5px;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Diário</h1>
    
    <div class="money-display" id="moneyDisplay">Dinheiro: $0.00</div>
    <div class="warning" id="warningMessage"></div>

    <div class="tabs">
        <button class="btn-tab" onclick="showTab('mainTab')">Principal</button>
        <button class="btn-tab" onclick="showTab('storeTab')">Loja</button>
        <button class="btn-tab" onclick="showTab('betTab')">Apostas</button>
    </div>

    <div id="mainTab" class="tab-content active-tab">
        <h3>Gerenciar Dinheiro</h3>
        <button onclick="changeMoney(3)">+ $3</button>
        <button onclick="changeMoney(10)">+ $10</button>
        <button class="btn-danger" onclick="changeMoney(-3)">- $3</button>
        <button class="btn-danger" onclick="changeMoney(-5)">- $5</button>
        
        <br><br>
        <hr style="border-color: #444;">
        <button class="btn-danger btn-reset" onclick="resetMoney()">⚠️ Resetar Todo o Progresso ⚠️</button>
    </div>

    <div id="storeTab" class="tab-content">
        <h3>Loja Virtual</h3>
        <div class="store-item"><span>iPhone 📱 ($ 1.000)</span> <button onclick="buyItem('iPhone', 1000)">Comprar</button></div>
        <div class="store-item"><span>PC Gamer 💻 ($ 5.000)</span> <button onclick="buyItem('PC Gamer', 5000)">Comprar</button></div>
        <div class="store-item"><span>Carro 🚗 ($ 50.000)</span> <button onclick="buyItem('Carro', 50000)">Comprar</button></div>
        <div class="store-item"><span>Casa 🏠 ($ 500.000)</span> <button onclick="buyItem('Casa', 500000)">Comprar</button></div>
        <div class="store-item"><span>Atributo de Status Máximo 👑 ($ 1.000.000.000)</span> <button onclick="buyItem('Atributo Máximo', 1000000000)">Comprar</button></div>
        
        <div id="inventoryDisplay">
            <strong>Seus Bens:</strong>
            <ul id="inventoryList"></ul>
        </div>
    </div>

    <div id="betTab" class="tab-content">
        <h3>Cassino Virtual</h3>
        <p>Insira um valor para apostar (Mínimo: $5).</p>
        <input type="number" id="betAmount" placeholder="Valor da aposta..." min="5">
        <br>
        <button onclick="placeBet()">Apostar</button>
        <p id="betResult" style="font-weight: bold; margin-top: 15px;"></p>
    </div>
</div>

<script>
    // Inicialização do Banco de Dados Local (localStorage)
    let money = parseFloat(localStorage.getItem('diario_dinheiro')) || 0;
    let inventory = JSON.parse(localStorage.getItem('diario_inventario')) || [];

    // Atualiza a tela ao carregar a página
    updateDisplay();

    function updateDisplay() {
        document.getElementById('moneyDisplay').innerText = `Dinheiro: $${money.toFixed(2)}`;
        localStorage.setItem('diario_dinheiro', money);

        // Atualiza inventário
        let invList = document.getElementById('inventoryList');
        invList.innerHTML = "";
        inventory.forEach(item => {
            let li = document.createElement('li');
            li.innerText = item;
            invList.appendChild(li);
        });
    }

    // Função de navegação de abas
    function showTab(tabId) {
        document.querySelectorAll('.tab-content').forEach(tab => {
            tab.classList.remove('active-tab');
        });
        document.getElementById(tabId).classList.add('active-tab');
    }

    // Adiciona ou remove dinheiro
    function changeMoney(amount) {
        money += amount;
        updateDisplay();
    }

    // Lógica da Loja
    function buyItem(itemName, cost) {
        if (money >= cost) {
            money -= cost;
            inventory.push(itemName);
            localStorage.setItem('diario_inventario', JSON.stringify(inventory));
            updateDisplay();
            alert(`Parabéns! Você comprou: ${itemName}`);
        } else {
            alert("Dinheiro insuficiente para comprar este item!");
        }
    }

    // Lógica de Apostas
    function placeBet() {
        let betInput = document.getElementById('betAmount').value;
        let bet = parseFloat(betInput);

        if (isNaN(bet) || bet < 5) {
            alert("A aposta mínima é de $5.");
            return;
        }

        if (bet > money) {
            alert("Você não tem saldo suficiente para essa aposta.");
            return;
        }

        money -= bet;

        let chance = Math.random(); 
        let finalPrize = 0;
        let resultMessage = "";

        if (chance < 0.70) {
            let badRoll = Math.random();
            if (badRoll < 0.20) {
                finalPrize = bet * 1; resultMessage = "1× (Recuperou a aposta)";
            } else if (badRoll < 0.40) {
                finalPrize = bet / 2; resultMessage = "÷2 (Perdeu metade)";
            } else if (badRoll < 0.60) {
                finalPrize = bet / 3; resultMessage = "÷3 (Perdeu dois terços)";
            } else if (badRoll < 0.75) {
                finalPrize = 0; money -= 10; resultMessage = "-10$ (Perdeu a aposta e mais $10 extra)";
            } else if (badRoll < 0.90) {
                finalPrize = 0; money -= 20; resultMessage = "-20$ (Perdeu a aposta e mais $20 extra)";
            } else {
                finalPrize = 0; money -= 30; resultMessage = "-30$ (Perdeu a aposta e mais $30 extra)";
            }
        } else {
            let goodRoll = Math.random();
            if (goodRoll < 0.30) {
                finalPrize = bet * 1.3; resultMessage = "×1.3 (Lucro leve)";
            } else if (goodRoll < 0.60) {
                finalPrize = bet * 1.5; resultMessage = "×1.5 (Bom lucro)";
            } else if (goodRoll < 0.80) {
                finalPrize = bet * 2; resultMessage = "×2 (Dobrou a aposta!)";
            } else if (goodRoll < 0.90) {
                finalPrize = bet + 20; resultMessage = "+20$ (Aposta + $20 bônus)";
            } else {
                finalPrize = bet + 30; resultMessage = "+30$ (Aposta + $30 bônus)";
            }
        }

        money += finalPrize;
        document.getElementById('betResult').innerText = `Resultado: ${resultMessage}`;
        updateDisplay();
    }

    // NOVA FUNÇÃO: Resetar Dinheiro e Progresso
    function resetMoney() {
        let confirmacao = confirm("Tem certeza que deseja resetar todo o seu dinheiro e bens? Isso não pode ser desfeito!");
        
        if (confirmacao) {
            // Zera as variáveis no código
            money = 0;
            inventory = [];
            
            // Zera no localStorage (banco de dados do navegador)
            localStorage.setItem('diario_dinheiro', 0);
            localStorage.setItem('diario_inventario', JSON.stringify([]));
            
            // Limpa mensagens
            document.getElementById('betResult').innerText = "";
            document.getElementById('betAmount').value = "";
            
            // Atualiza a tela
            updateDisplay();
            alert("Progresso resetado com sucesso.");
        }
    }
</script>

</body>
</html>