<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Арбитражные связки</title>
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      background: #f7f7f7;
    }
    h1 {
      color: #333;
    }
    .opportunity {
      background: #fff;
      border: 1px solid #ddd;
      border-radius: 5px;
      padding: 10px;
      margin-bottom: 10px;
    }
    .spread {
      font-weight: bold;
      color: green;
    }
  </style>
</head>
<body>
  <h1>Арбитражные связки</h1>
  <div id="opportunities">
    <p>Ожидание данных...</p>
  </div>
  
  <script>
    // Инициализация Telegram Web App API
    const tg = window.Telegram.WebApp;
    tg.expand();

    // Укажите ваш публичный WebSocket URL (например, полученный через ngrok)
    const wsUrl = "wss://denibro555.loca.lt/ws";
    const ws = new WebSocket(wsUrl);

    ws.onopen = () => {
      console.log("WebSocket подключен");
    };

    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      updateOpportunities(data);
    };

    ws.onerror = (error) => {
      console.error("WebSocket ошибка:", error);
    };

    ws.onclose = () => {
      console.log("WebSocket соединение закрыто");
    };

    // Функция обновления списка возможностей на странице
    function updateOpportunities(data) {
      const container = document.getElementById("opportunities");
      container.innerHTML = ""; // очищаем содержимое
      if (!data || data.length === 0) {
        container.innerHTML = "<p>Нет арбитражных возможностей.</p>";
        return;
      }
      // data уже должна быть отсортирована по спреду (от большего к меньшему)
      data.forEach(item => {
        const div = document.createElement("div");
        div.className = "opportunity";
        div.innerHTML = `
          <h2>${item.symbol} (${item.exchangePair})</h2>
          <p>Спред: <span class="spread">${item.spread.toFixed(2)}%</span></p>
          <p><strong>ЛОНГ:</strong> ${item.long.exchange} – Цена: ${item.long.price} USDT | ASK: ${item.long.ask} | Funding: ${item.long.funding} | Volume: ${item.long.volume}</p>
          <p><strong>ШОРТ:</strong> ${item.short.exchange} – Цена: ${item.short.price} USDT | BID: ${item.short.bid} | Funding: ${item.short.funding} | Volume: ${item.short.volume}</p>
        `;
        container.appendChild(div);
      });
    }
  </script>
</body>
</html>
