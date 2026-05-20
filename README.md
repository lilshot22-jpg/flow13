<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>FLOW-13 // FINANCIAL MATRIX</title>
    <script src="https://telegram.org/js/telegram-webapps.js"></script>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
        body {
            background-color: #0a0a0c;
            color: #fff;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            overflow-x: hidden;
            padding: 20px;
            padding-bottom: 40px;
        }
        
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            margin-top: 10px;
        }
        .logo { font-size: 0.9rem; letter-spacing: 4px; font-weight: 800; color: #444; }
        .logo span { color: #fff; }
        
        .total-balance-box {
            text-align: center;
            margin-bottom: 35px;
            animation: fadeIn 0.5s ease;
        }
        .total-label { font-size: 0.75rem; color: #666; letter-spacing: 2px; text-transform: uppercase; margin-bottom: 5px; }
        .total-amount { font-size: 2.4rem; font-weight: 800; letter-spacing: -1px; }

        /* Карточки Сейфов */
        .safes-container { display: flex; flex-direction: column; gap: 16px; margin-bottom: 35px; }
        .safe-card {
            background: #121216;
            border: 1px solid #1c1c24;
            border-radius: 16px;
            padding: 20px;
            position: relative;
            overflow: hidden;
            transition: transform 0.2s;
        }
        .safe-card:active { transform: scale(0.98); }
        
        .safe-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px; }
        .safe-title { font-size: 0.85rem; font-weight: 700; letter-spacing: 1.5px; text-transform: uppercase; }
        .safe-pct { font-size: 0.8rem; color: #666; background: #1c1c24; padding: 3px 8px; border-radius: 20px; }
        .safe-balance { font-size: 1.6rem; font-weight: 700; }
        
        /* Неоновые акценты */
        .safe-card.hold { border-left: 4px solid #007fff; box-shadow: 0 4px 20px rgba(0, 127, 255, 0.03); }
        .safe-card.hold .safe-title { color: #007fff; }
        
        .safe-card.flow { border-left: 4px solid #ffffff; box-shadow: 0 4px 20px rgba(255, 255, 255, 0.03); }
        .safe-card.flow .safe-title { color: #ffffff; }
        
        .safe-card.pump { border-left: 4px solid #00ff66; box-shadow: 0 4px 20px rgba(0, 255, 102, 0.03); }
        .safe-card.pump .safe-title { color: #00ff66; }

        /* Кнопки */
        .actions-grid { display: flex; gap: 12px; margin-bottom: 25px; }
        .btn {
            flex: 1;
            background: #fff;
            color: #000;
            border: none;
            padding: 16px;
            border-radius: 12px;
            font-size: 0.95rem;
            font-weight: 700;
            cursor: pointer;
            text-align: center;
            transition: background 0.2s;
        }
        .btn:active { background: #dcdcdc; }
        .btn-black { background: #121216; color: #fff; border: 1px solid #222; }
        .btn-black:active { background: #1c1c24; }

        /* Формы и модалки */
        .modal {
            background: #121216;
            border: 1px solid #1c1c24;
            border-radius: 20px;
            padding: 25px;
            margin-bottom: 25px;
            animation: slideUp 0.3s ease;
        }
        .modal-title { font-size: 1rem; font-weight: 700; letter-spacing: 1px; margin-bottom: 20px; text-transform: uppercase; color: #888; }
        
        .input-group { position: relative; margin-bottom: 20px; }
        input[type="number"] {
            width: 100%;
            background: transparent;
            border: none;
            border-bottom: 2px solid #222;
            color: #fff;
            font-size: 1.8rem;
            font-weight: 700;
            padding: 10px 0;
            outline: none;
            transition: border-color 0.3s;
        }
        input[type="number"]:focus { border-bottom-color: #fff; }
        
        select {
            width: 100%;
            background: #1c1c24;
            border: 1px solid #2d2d3d;
            color: #fff;
            padding: 14px;
            border-radius: 10px;
            font-size: 1rem;
            outline: none;
            margin-bottom: 20px;
            -webkit-appearance: none;
        }

        .pct-inputs { display: flex; gap: 10px; margin-bottom: 20px; }
        .pct-field { flex: 1; text-align: center; }
        .pct-field label { font-size: 0.7rem; color: #666; display: block; margin-bottom: 5px; text-transform: uppercase; }
        .pct-field input { width: 100%; background: #1c1c24; border: 1px solid #2d2d3d; color: #fff; padding: 10px; border-radius: 8px; text-align: center; font-weight: 700; }

        .hidden { display: none !important; }
        
        /* История логов */
        .history-box { background: #0d0d11; border-radius: 14px; padding: 15px; border: 1px solid #15151a; }
        .history-title { font-size: 0.75rem; color: #444; letter-spacing: 2px; text-transform: uppercase; margin-bottom: 12px; }
        .history-item { display: flex; justify-content: space-between; font-size: 0.85rem; padding: 8px 0; border-bottom: 1px solid #15151a; }
        .history-item:last-child { border: none; }
        .hist-inc { color: #00ff66; }
        .hist-exp { color: #ff3344; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes slideUp { from { transform: translateY(10px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    </style>
</head>
<body>

    <header>
        <div class="logo">FLOW<span>//13</span></div>
        <div id="matrix-status" style="font-size:0.7rem; color:#666; letter-spacing:1px;">SYSTEM ONLINE</div>
    </header>

    <div class="total-balance-box">
        <div class="total-label">Общий капитал</div>
        <div class="total-amount" id="total-balance">0 ₽</div>
    </div>

    <div class="safes-container">
        <div class="safe-card hold">
            <div class="safe-header">
                <div class="safe-title">HOLD // База и Безопасность</div>
                <div class="safe-pct" id="badge-hold">30%</div>
            </div>
            <div class="safe-balance" id="bal-hold">0 ₽</div>
        </div>
        <div class="safe-card flow">
            <div class="safe-header">
                <div class="safe-title">FLOW // Жизнь и Расходы</div>
                <div class="safe-pct" id="badge-flow">50%</div>
            </div>
            <div class="safe-balance" id="bal-flow">0 ₽</div>
        </div>
        <div class="safe-card pump">
            <div class="safe-header">
                <div class="safe-title">PUMP // Движ и Проекты</div>
                <div class="safe-pct" id="badge-pump">20%</div>
            </div>
            <div class="safe-balance" id="bal-pump">0 ₽</div>
        </div>
    </div>

    <div class="actions-grid">
        <button class="btn" onclick="toggleModal('modal-income')">+ Поступление</button>
        <button class="btn btn-black" onclick="toggleModal('modal-expense')">− Расход</button>
        <button class="btn btn-black" style="max-width:60px;" onclick="toggleModal('modal-settings')">⚙️</button>
    </div>

    <div id="modal-income" class="modal hidden">
        <div class="modal-title">Влить кэш в систему (Авто-распределение)</div>
        <div class="input-group">
            <input type="number" id="income-amount" placeholder="0" pattern="[0-9]*" inputmode="numeric">
        </div>
        <button class="btn" onclick="executeIncome()">Распределить по сейфам</button>
    </div>

    <div id="modal-expense" class="modal hidden">
        <div class="modal-title">Списать из сейфа</div>
        <select id="expense-target">
            <option value="flow">FLOW // Жизнь и Расходы</option>
            <option value="pump">PUMP // Движ и Проекты</option>
            <option value="hold">HOLD // База (Крайний случай)</option>
        </select>
        <div class="input-group">
            <input type="number" id="expense-amount" placeholder="0" pattern="[0-9]*" inputmode="numeric">
        </div>
        <button class="btn btn-black" onclick="executeExpense()">Зафиксировать расход</button>
    </div>

    <div id="modal-settings" class="modal hidden">
        <div class="modal-title">Настройка долей (Сумма должна быть равна 100%)</div>
        <div class="pct-inputs">
            <div class="pct-field">
                <label>Hold %</label>
                <input type="number" id="set-hold" value="30">
            </div>
            <div class="pct-field">
                <label>Flow %</label>
                <input type="number" id="set-flow" value="50">
            </div>
            <div class="pct-field">
                <label>Pump %</label>
                <input type="number" id="set-pump" value="20">
            </div>
        </div>
        <button class="btn" onclick="saveSettings()">Сохранить матрицу</button>
    </div>

    <div class="history-box">
        <div class="history-title">Последние логи операций</div>
        <div id="history-list">
            <div class="history-item" style="color:#444; justify-content:center;">История пуста</div>
        </div>
    </div>

    <script>
        // Инициализация Telegram Web App SDK
        if (window.Telegram && window.Telegram.WebApp) {
            const tg = window.Telegram.WebApp;
            tg.ready();
            tg.expand();
            tg.backgroundColor = "#0a0a0c";
            tg.headerColor = "#0a0a0c";
        }

        // ЗАЩИТА ОТ КОНФЛИКТА КЭША: Полная проверка структуры памяти
        let data;
        try {
            let cached = localStorage.getItem('flow13_data');
            data = JSON.parse(cached);
            if (!data || !data.balances || !data.settings || data.balances.hold === undefined) {
                throw new Error("Reset old cache");
            }
        } catch (e) {
            data = {
                balances: { hold: 0, flow: 0, pump: 0 },
                settings: { hold: 30, flow: 50, pump: 20 },
                history: []
            };
        }

        // Красивое форматирование валюты
        function formatCurrency(num) {
            return new Intl.NumberFormat('ru-RU').format(Math.round(num)) + ' ₽';
        }

        // Обновление элементов UI
        function updateUI() {
            document.getElementById('bal-hold').innerText = formatCurrency(data.balances.hold);
            document.getElementById('bal-flow').innerText = formatCurrency(data.balances.flow);
            document.getElementById('bal-pump').innerText = formatCurrency(data.balances.pump);

            const total = data.balances.hold + data.balances.flow + data.balances.pump;
            document.getElementById('total-balance').innerText = formatCurrency(total);

            document.getElementById('badge-hold').innerText = data.settings.hold + '%';
            document.getElementById('badge-flow').innerText = data.settings.flow + '%';
            document.getElementById('badge-pump').innerText = data.settings.pump + '%';

            document.getElementById('set-hold').value = data.settings.hold;
            document.getElementById('set-flow').value = data.settings.flow;
            document.getElementById('set-pump').value = data.settings.pump;

            const histList = document.getElementById('history-list');
            if (!data.history || data.history.length === 0) {
                histList.innerHTML = `<div class="history-item" style="color:#444; justify-content:center;">История пуста</div>`;
            } else {
                histList.innerHTML = data.history.map(item => `
                    <div class="history-item">
                        <span style="color:#aaa;">${item.desc}</span>
                        <span class="${item.type === 'inc' ? 'hist-inc' : 'hist-exp'}">
                            ${item.type === 'inc' ? '+' : '−'} ${formatCurrency(item.amount)}
                        </span>
                    </div>
                `).join('');
            }

            localStorage.setItem('flow13_data', JSON.stringify(data));
        }

        // Переключатель модальных окон
        function toggleModal(modalId) {
            const modals = ['modal-income', 'modal-expense', 'modal-settings'];
            modals.forEach(id => {
                const el = document.getElementById(id);
                if(id === modalId) {
                    el.classList.toggle('hidden');
                } else {
                    el.classList.add('hidden');
                }
            });
        }

        // Логика кнопки «+ Поступление» (Автоматическое распределение долей)
        function executeIncome() {
            const input = document.getElementById('income-amount');
            const amount = parseFloat(input.value);
            
            if (!amount || amount <= 0) {
                if(window.Telegram && window.Telegram.WebApp) {
                    window.Telegram.WebApp.showAlert("Введи сумму больше нуля, бро!");
                } else {
                    alert("Введи сумму больше нуля, бро!");
                }
                return;
            }

            // Матричное деление кэша по долям
            const hShare = amount * (data.settings.hold / 100);
            const fShare = amount * (data.settings.flow / 100);
            const pShare = amount * (data.settings.pump / 100);

            data.balances.hold += hShare;
            data.balances.flow += fShare;
            data.balances.pump += pShare;

            addLog('inc', amount, 'Вливание в матрицу');

            input.value = '';
            toggleModal('');
            updateUI();
        }

        // Логика кнопки «- Расход»
        function executeExpense() {
            const input = document.getElementById('expense-amount');
            const target = document.getElementById('expense-target').value;
            const amount = parseFloat(input.value);

            if (!amount || amount <= 0) {
                if(window.Telegram && window.Telegram.WebApp) {
                    window.Telegram.WebApp.showAlert("Укажи сумму расхода, бро.");
                } else {
                    alert("Укажи сумму расхода, бро.");
                }
                return;
            }
            
            if (data.balances[target] < amount) {
                if(window.Telegram && window.Telegram.WebApp) {
                    window.Telegram.WebApp.showAlert("Недостаточно средств в выбранном сейфе!");
                } else {
                    alert("Недостаточно средств в выбранном сейфе!");
                }
                return;
            }

            data.balances[target] -= amount;

            const targetNames = { hold: 'Списание из HOLD', flow: 'Расход FLOW', pump: 'Инвестиция PUMP' };
            addLog('exp', amount, targetNames[target]);

            input.value = '';
            toggleModal('');
            updateUI();
        }

        // Настройка процентной матрицы
        function saveSettings() {
            const h = parseInt(document.getElementById('set-hold').value) || 0;
            const f = parseInt(document.getElementById('set-flow').value) || 0;
            const p = parseInt(document.getElementById('set-pump').value) || 0;

            if ((h + f + p) !== 100) {
                if(window.Telegram && window.Telegram.WebApp) {
                    window.Telegram.WebApp.showAlert("Матрица нарушена! В сумме должно быть строго 100%. Сейчас: " + (h+f+p) + "%");
                } else {
                    alert("Матрица нарушена! В сумме должно быть строго 100%. Сейчас: " + (h+f+p) + "%");
                }
                return;
            }

            data.settings.hold = h;
            data.settings.flow = f;
            data.settings.pump = p;

            toggleModal('');
            updateUI();
        }

        // Функция добавления логов
        function addLog(type, amount, desc) {
            if (!data.history) data.history = [];
            data.history.unshift({ type, amount, desc });
            if (data.history.length > 5) data.history.pop();
        }

        // Первичный запуск отрисовки данных при загрузке страницы
        updateUI();
    </script>
</body>
</html>
