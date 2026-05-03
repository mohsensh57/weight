<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>WeightTracker</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root { --primary: #2563eb; --bg: #f8fafc; --card: #ffffff; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background: var(--bg); margin: 0; padding: 20px; color: #1e293b; }
        .card { background: var(--card); padding: 20px; border-radius: 16px; box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); margin-bottom: 20px; }
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .stat-box { text-align: center; }
        .stat-val { font-size: 1.5rem; font-weight: bold; color: var(--primary); }
        .stat-label { font-size: 0.8rem; color: #64748b; text-transform: uppercase; }
        input, button { width: 100%; padding: 12px; margin-top: 10px; border-radius: 8px; border: 1px solid #e2e8f0; box-sizing: border-box; font-size: 1rem; }
        button { background: var(--primary); color: white; border: none; font-weight: bold; cursor: pointer; }
        h2 { margin-top: 0; font-size: 1.2rem; }
    </style>
</head>
<body>

    <div class="card">
        <h2>Progress Summary</h2>
        <div class="stats-grid">
            <div class="stat-box">
                <div class="stat-val" id="totalLost">0.0</div>
                <div class="stat-label">kg Lost</div>
            </div>
            <div class="stat-box">
                <div class="stat-val" id="currentWeight">0.0</div>
                <div class="stat-label">Current kg</div>
            </div>
        </div>
    </div>

    <div class="card">
        <canvas id="weightChart"></canvas>
    </div>

    <div class="card">
        <h2>Log New Weight</h2>
        <input type="number" id="weightInput" step="0.1" placeholder="Enter weight (kg)">
        <button onclick="addWeight()">Save Entry</button>
    </div>

    <script>
        // Initialize with your data
        let weightData = JSON.parse(localStorage.getItem('weightData')) || [
            { date: '2026-03-25', weight: 107.6 },
            { date: '2026-05-03', weight: 101.3 }
        ];

        let chart;

        function updateUI() {
            const current = weightData[weightData.length - 1].weight;
            const start = weightData[0].weight;
            document.getElementById('currentWeight').innerText = current.toFixed(1);
            document.getElementById('totalLost').innerText = (start - current).toFixed(1);
            renderChart();
        }

        function renderChart() {
            const ctx = document.getElementById('weightChart').getContext('2d');
            if (chart) chart.destroy();
            chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: weightData.map(d => new Date(d.date).toLocaleDateString(undefined, {month:'short', day:'numeric'})),
                    datasets: [{
                        label: 'Weight (kg)',
                        data: weightData.map(d => d.weight),
                        borderColor: '#2563eb',
                        tension: 0.3,
                        fill: true,
                        backgroundColor: 'rgba(37, 99, 235, 0.1)'
                    }]
                },
                options: { responsive: true, scales: { y: { beginAtZero: false } } }
            });
        }

        function addWeight() {
            const val = document.getElementById('weightInput').value;
            if (!val) return;
            weightData.push({ date: new Date().toISOString().split('T')[0], weight: parseFloat(val) });
            localStorage.setItem('weightData', JSON.stringify(weightData));
            updateUI();
            document.getElementById('weightInput').value = '';
        }

        updateUI();
    </script>
</body>
</html>

