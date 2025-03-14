<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Арбитражные возможности</title>
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
  <style>
    body {
      font-family: sans-serif;
      margin: 10px;
      background: #f9f9f9;
    }
    h1 {
      text-align: center;
      color: #333;
    }
    .opportunity {
      border: 1px solid #ccc;
      border-radius: 5px;
      background: #fff;
      padding: 10px;
      margin-bottom: 8px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    }
    .title {
      font-weight: bold;
      margin-bottom: 5px;
    }
    .detail {
      font-size: 0.9em;
      margin-bottom: 3px;
    }
  </style>
</head>
<body>
  <h1>Арбитражные возможности</h1>
  <div id="data">Загрузка данных...</div>
  
  <script>
    // Инициализация Telegram Web Apps API
    const tg = window.Telegram.WebApp;
    tg.expand();

    // Укажите ваш публичный URL API (например, полученный через Local Tunnel)
    const API_URL = "https://wicked-birds-bet.loca.lt/api/data";

    // Функция запроса данных с API (без кэширования)
    async function fetchData() {
      try {
        const response = await fetch(API_URL, { cache: "no-store" });
        if (!response.ok) {
          console.error("Ошибка HTTP:", response.status);
          return [];
        }
        const data = await response.json();
        return data;
      } catch (error) {
        console.error("Ошибка запроса:", error);
        return [];
      }
    }

    // Функция для рендеринга списка возможностей
    function renderData(opportunities) {
      const container = document.getElementById("data");
      if (!opportunities || opportunities.length === 0) {
        container.innerHTML = "<p>Нет арбитражных возможностей</p>";
        return;
      }
      // Формируем HTML для каждой возможности
      let html = "";
      opportunities.forEach(item => {
        html += `
          <div class="opportunity">
            <div class="title">${item.symbol} (${item.exchangePair})</div>
            <div class="detail">Спред: ${item.spread.toFixed(2)}%</div>
            <div class="detail"><strong>ЛОНГ:</strong> ${item.long.exchange} – Цена: ${item.long.price} USDT, ASK: ${item.long.ask}, Funding: ${item.long.funding}, Volume: ${item.long.volume}</div>
            <div class="detail"><strong>ШОРТ:</strong> ${item.short.exchange} – Цена: ${item.short.price} USDT, BID: ${item.short.bid}, Funding: ${item.short.funding}, Volume: ${item.short.volume}</div>
          </div>
        `;
      });
      container.innerHTML = html;
    }

    // Функция обновления данных, вызываемая каждые 0.5 сек
    async function updateData() {
      const opportunities = await fetchData();
      // Предполагается, что API уже возвращает отсортированный список по спреду
      renderData(opportunities);
    }

    // Запуск опроса API каждые 500 мс
    setInterval(updateData, 500);
  </script>
</body>
</html>
