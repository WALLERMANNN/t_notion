<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Watchkeeper Dashboard</title>
    <style>
        :root {
            --bg-color: #191919;
            --card-bg: #202020;
            --border-color: #2f2f2f;
            --text-main: #ffffff;
            --text-muted: #838383;
            --accent-blue: #2eaadc;
            --accent-green: #2ecc71;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }
        .dashboard {
            width: 100%;
            max-width: 450px;
            background: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.4);
        }
        .header {
            text-align: center;
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 15px;
            margin-bottom: 20px;
        }
        .header h2 { margin: 0; font-size: 18px; font-weight: 500; color: var(--accent-blue); text-transform: uppercase; letter-spacing: 1px; }
        .clocks {
            display: flex;
            justify-content: space-between;
            margin-bottom: 25px;
        }
        .clock-card {
            background: #252525;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 10px;
            width: 45%;
            text-align: center;
        }
        .clock-time { font-size: 22px; font-weight: bold; font-family: monospace; color: #fff; margin-top: 5px; }
        .clock-label { font-size: 11px; color: var(--text-muted); text-transform: uppercase; }
        
        .watch-progress {
            margin-bottom: 25px;
        }
        .progress-bar-container {
            background: #2c2c2c;
            border-radius: 6px;
            height: 12px;
            width: 100%;
            overflow: hidden;
            margin-top: 8px;
        }
        .progress-bar {
            background: linear-gradient(90deg, var(--accent-blue), #45b3e3);
            height: 100%;
            width: 0%;
            transition: width 0.5s ease;
        }
        .section-title { font-size: 13px; color: var(--text-muted); text-transform: uppercase; margin: 0; display: flex; justify-content: space-between;}
        
        .contract-counter {
            background: #252525;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 15px;
            text-align: center;
        }
        .contract-counter input {
            background: #191919;
            border: 1px solid var(--border-color);
            color: #fff;
            padding: 5px;
            border-radius: 4px;
            margin-top: 8px;
            width: 80%;
            text-align: center;
        }
        .days-left { font-size: 32px; font-weight: bold; color: var(--accent-green); margin: 10px 0; }
    </style>
</head>
<body>

<div class="dashboard">
    <div class="header">
        <h2>Bridge Watchkeeper Dashboard</h2>
    </div>

    <div class="clocks">
        <div class="clock-card">
            <div class="clock-label">Local Time</div>
            <div class="clock-time" id="local-clock">00:00:00</div>
        </div>
        <div class="clock-card">
            <div class="clock-label">GMT / UTC</div>
            <div class="clock-time" id="utc-clock">00:00:00</div>
        </div>
    </div>

    <div class="watch-progress">
        <p class="section-title"><span>Текущая 4-ч вахта</span> <span id="progress-text">0%</span></p>
        <div class="progress-bar-container">
            <div class="progress-bar" id="watch-bar"></div>
        </div>
    </div>

    <div class="contract-counter">
        <p class="section-title" style="justify-content: center;">Дней до конца контракта (Sign-Off)</p>
        <input type="date" id="signoff-date" onchange="updateContract()">
        <div class="days-left" id="days-count">--</div>
    </div>
</div>

<script>
    function updateClocks() {
        const now = new Date();
        document.getElementById('local-clock').innerText = now.toTimeString().split(' ')[0];
        document.getElementById('utc-clock').innerText = now.toISOString().split('T')[1].slice(0, 8);

        const hours = now.getHours();
        const mins = now.getMinutes();
        const currentWatchHour = hours % 4;
        const totalMinutesPassed = (currentWatchHour * 60) + mins;
        const progressPercent = Math.round((totalMinutesPassed / 240) * 100);

        document.getElementById('watch-bar').style.width = progressPercent + '%';
        document.getElementById('progress-text').innerText = progressPercent + '%';
    }

    function updateContract() {
        const inputDate = document.getElementById('signoff-date').value;
        if (!inputDate) return;

        const end = new Date(inputDate);
        const start = new Date();
        start.setHours(0,0,0,0);
        end.setHours(0,0,0,0);

        const diff = end.getTime() - start.getTime();
        const days = Math.ceil(diff / (1000 * 60 * 60 * 24));

        const display = document.getElementById('days-count');
        if (days < 0) {
            display.innerText = "Home!";
            display.style.color = "#2ecc71";
        } else {
            display.innerText = days;
            display.style.color = "#2ecc71";
        }
        localStorage.setItem('signoff', inputDate);
    }

    const savedDate = localStorage.getItem('signoff');
    if (savedDate) {
        document.getElementById('signoff-date').value = savedDate;
        setTimeout(updateContract, 500);
    }

    setInterval(updateClocks, 1000);
    updateClocks();
</script>

</body>
</html>
