<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>智慧籃球訓練系統</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC&display=swap" rel="stylesheet">
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: 'Noto Sans TC', sans-serif;
      margin: 0;
      padding: 0;
      background: #eef2f5;
      color: #333;
    }
    header {
      background: linear-gradient(to right, #1e3d59, #274472);
      color: white;
      padding: 2rem 1rem;
      text-align: center;
      box-shadow: 0 4px 10px rgba(0,0,0,0.2);
    }
    h1 { margin: 0; font-size: 2.2rem; }
    main {
      max-width: 800px;
      margin: 2rem auto;
      padding: 1.5rem;
      background: white;
      border-radius: 16px;
      box-shadow: 0 6px 18px rgba(0,0,0,0.1);
    }
    .section {
      margin-bottom: 2.5rem;
    }
    h2 {
      font-size: 1.5rem;
      margin-bottom: 1rem;
      border-left: 5px solid #007bff;
      padding-left: 0.5rem;
    }
    .score-box {
      font-size: 2.5rem;
      font-weight: bold;
      color: #007bff;
      text-align: center;
    }
    .status-text {
      text-align: center;
      font-size: 1.2rem;
      font-weight: bold;
      margin-top: 1rem;
    }
    button {
      display: inline-block;
      padding: 0.75rem 1.5rem;
      background: #007bff;
      border: none;
      color: white;
      border-radius: 8px;
      cursor: pointer;
      font-size: 1rem;
      transition: background 0.3s ease;
    }
    button:hover {
      background: #0056b3;
    }
    canvas {
      margin-top: 1rem;
    }
    iframe {
      border: none;
      border-radius: 10px;
    }
  </style>
</head>
<body>
  <header>
    <h1>🏀 智慧籃球訓練輔助系統</h1>
  </header>
  <main>
    <div class="section" id="bluetooth">
      <h2>🔵 藍牙連線</h2>
      <button onclick="connectBluetooth()">連接藍牙裝置</button>
      <p id="bt-status" class="status-text">尚未連線</p>
    </div>

    <div class="section" id="live-score">
      <h2>📍 即時分數與命中狀態</h2>
      <p class="score-box">目前得分：<span id="score">0</span></p>
      <p class="status-text">命中狀態：<span id="status">--</span></p>
    </div>

    <div class="section" id="records">
      <h2>📊 投籃紀錄圖表</h2>
      <canvas id="scoreChart"></canvas>
    </div>

    <div class="section" id="ai-detection">
      <h2>🤖 AI命中辨識（實驗性功能）</h2>
      <iframe src="https://teachablemachine.withgoogle.com/models/你的模型網址/" width="100%" height="300"></iframe>
    </div>
  </main>

  <script>
    let score = 0;
    let makes = 0;
    let misses = 0;
    const scoreDisplay = document.getElementById("score");
    const statusDisplay = document.getElementById("status");
    const btStatus = document.getElementById("bt-status");

    async function connectBluetooth() {
      try {
        const device = await navigator.bluetooth.requestDevice({
          acceptAllDevices: true,
          optionalServices: ["battery_service"]
        });
        btStatus.innerText = `✅ 已連線：${device.name}`;

        // 模擬資料接收
        setInterval(() => {
          const result = Math.random() > 0.5 ? "HIT" : "MISS";
          updateScore(result);
        }, 3000);

      } catch (error) {
        btStatus.innerText = "❌ 連線失敗";
        console.error(error);
      }
    }

    function updateScore(result) {
      if (result === "HIT") {
        score++;
        makes++;
        statusDisplay.innerText = "命中！+1分";
        statusDisplay.style.color = "#28a745";
      } else {
        misses++;
        statusDisplay.innerText = "未命中";
        statusDisplay.style.color = "#dc3545";
      }
      scoreDisplay.innerText = score;
      updateChart();
    }

    let chart;
    function updateChart() {
      if (!chart) {
        const ctx = document.getElementById("scoreChart").getContext("2d");
        chart = new Chart(ctx, {
          type: "doughnut",
          data: {
            labels: ["命中", "未命中"],
            datasets: [{
              data: [makes, misses],
              backgroundColor: ["#28a745", "#dc3545"],
              borderWidth: 2
            }]
          },
          options: {
            responsive: true,
            plugins: {
              legend: { position: 'bottom' }
            }
          }
        });
      } else {
        chart.data.datasets[0].data = [makes, misses];
        chart.update();
      }
    }
  </script>
</body>
</html>

