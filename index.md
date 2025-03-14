<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Арбитражные возможности</title>
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 20px;
      background-color: #f7f7f7;
    }
    h1 {
      text-align: center;
      margin-bottom: 20px;
    }
    .opportunity {
      background-color: #fff;
      border: 1px solid #ddd;
      padding: 10px;
      margin-bottom: 10px;
      border-radius: 5px;
    }
    .spread {
      font-weight: bold;
      color: green;
    }
  </style>
</head>
<body>
  <h1>Арбитражные возможности</h1>
  <div id="opportunities">Загрузка данных...</div>
  
  <script>
    // Инициализация Telegram Web App API
    const tg = window.Telegram.WebApp;
    tg.expand();

    // Укажите ваш публичный URL, полученный через Local Tunnel (с поддержкой wss)
    const wsUrl = "wss://brown-socks-tap.loca.lt/api/data";
    const ws = new WebSocket(wsUrl);

    ws.onopen = () => {
      console.log("WebSocket соединение установлено");
    };

    ws.onmessage = (event) => {
      try {
        // Получаем обновленные данные (ожидается, что сервер отправляет JSON-массив)
        const data = JSON.parse(event.data);
        renderOpportunities(data);
      } catch (error) {
        console.error("Ошибка при обработке сообщения", error);
      }
    };

    ws.onerror = (error) => {
      console.error("WebSocket ошибка: ", error);
    };

    ws.onclose = () => {
      console.log("WebSocket соединение закрыто");
    };

    // Функция отображения списка арбитражных возможностей
    function renderOpportunities(opportunities) {
      const container = document.getElementById("opportunities");
      container.innerHTML = "";
      if (!opportunities || opportunities.length === 0) {
        container.innerHTML = "<p>Нет арбитражных возможностей</p>";
        return;
      }
      opportunities.forEach(opp => {
        const div = document.createElement("div");
        div.className = "opportunity";
        div.innerHTML = `
          <h2>${opp.symbol} (${opp.exchangePair})</h2>
          <p>Спред: <span class="spread">${opp.spread.toFixed(2)}%</span></p>
          <p><strong>ЛОНГ:</strong> ${opp.long.exchange} – Цена: ${opp.long.price} USDT, ASK: ${opp.long.ask}<br>
             Funding: ${opp.long.funding}, Volume: ${opp.long.volume}</p>
          <p><strong>ШОРТ:</strong> ${opp.short.exchange} – Цена: ${opp.short.price} USDT, BID: ${opp.short.bid}<br>
             Funding: ${opp.short.funding}, Volume: ${opp.short.volume}</p>
        `;
        container.appendChild(div);
      });
    }
  </script>
</body>
</html>
