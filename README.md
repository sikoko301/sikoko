<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>固體含量計算器（Sky 8.6V）</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            padding: 20px;
            max-width: 600px;
            margin: auto;
            background-color: #f5f5f5;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            color: #007BFF; /* 藍色 */
        }
        .input-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #555;
        }
        input[type="number"] {
            width: 100%;
            padding: 8px;
            box-sizing: border-box;
            border: 1px solid #ccc;
            border-radius: 4px;
            margin-bottom: 5px;
        }
        button {
            width: 48%;
            padding: 10px;
            background-color: #007BFF;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            margin: 1%;
        }
        button:hover {
            background-color: #0056b3;
        }
        .results, .history {
            margin-top: 20px;
        }
        .result-item, .history-item {
            padding: 10px;
            border-bottom: 1px solid #ddd;
        }
        .formula {
            margin-top: 20px;
            padding: 10px;
            background-color: #e9ecef;
            border-radius: 4px;
        }
        .error {
            color: red;
            margin-top: -10px;
            margin-bottom: 10px;
        }
        .history-item {
            border: 1px solid #ccc;
            border-radius: 4px;
            margin-bottom: 10px;
            padding: 10px;
            background-color: #ffffff;
        }
        .history-item:nth-child(odd) {
            background-color: #f0f0f0;
        }
        .timestamp {
            font-size: 0.85em;
            color: #666;
        }
        .history-header {
            font-weight: bold;
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <h1>固體含量計算器（Sky 8.6V）</h1>
    <div class="input-group">
        <label for="batch1Weight">第一批漿料的重量 (kg):</label>
        <input type="number" id="batch1Weight" step="0.01" required>
        <div id="batch1WeightError" class="error"></div>
    </div>
    <div class="input-group">
        <label for="batch1SolidPercentage">第一批漿料的固體百分比 (%):</label>
        <input type="number" id="batch1SolidPercentage" step="0.01" required>
        <div id="batch1SolidPercentageError" class="error"></div>
    </div>
    <div class="input-group">
        <label for="batch2Weight">第二批漿料的重量 (kg):</label>
        <input type="number" id="batch2Weight" step="0.01" required>
        <div id="batch2WeightError" class="error"></div>
    </div>
    <div class="input-group">
        <label for="batch2SolidPercentage">第二批漿料的固體百分比 (%):</label>
        <input type="number" id="batch2SolidPercentage" step="0.01" required>
        <div id="batch2SolidPercentageError" class="error"></div>
    </div>
    <button onclick="calculateSolidContentPercentage()">計算</button>
    <button onclick="resetForm()">重置</button>
    
    <div id="results" class="results"></div>
    <div id="history" class="history">
        <h2>歷史紀錄</h2>
    </div>

    <script>
        let historyCount = 0;

        function calculateSolidContentPercentage() {
            const batch1Weight = parseFloat(document.getElementById('batch1Weight').value);
            const batch1SolidPercentage = parseFloat(document.getElementById('batch1SolidPercentage').value);
            const batch2Weight = parseFloat(document.getElementById('batch2Weight').value);
            const batch2SolidPercentage = parseFloat(document.getElementById('batch2SolidPercentage').value);

            const errors = {
                batch1WeightError: batch1Weight <= 0 ? "請輸入有效的第一批漿料重量。" : "",
                batch1SolidPercentageError: (batch1SolidPercentage < 0 || batch1SolidPercentage > 100) ? "固體百分比應在0到100之間。" : "",
                batch2WeightError: batch2Weight <= 0 ? "請輸入有效的第二批漿料重量。" : "",
                batch2SolidPercentageError: (batch2SolidPercentage < 0 || batch2SolidPercentage > 100) ? "固體百分比應在0到100之間。" : "",
            };

            for (let id in errors) {
                document.getElementById(id).textContent = errors[id];
            }

            if (Object.values(errors).some(error => error !== "")) {
                document.getElementById('results').innerHTML = "";
                return;
            }

            const solidWeight1 = batch1Weight * batch1SolidPercentage / 100;
            const solidWeight2 = batch2Weight * batch2SolidPercentage / 100;

            const totalSolidWeight = solidWeight1 + solidWeight2;
            const totalWeight = batch1Weight + batch2Weight;
            const solidContentPercentage = (totalSolidWeight / totalWeight) * 100;

            const resultHTML = `
                <div class="result-item"><strong>第一批漿料的固體重量:</strong> ${solidWeight1.toFixed(2)} kg</div>
                <div class="result-item"><strong>第二批漿料的固體重量:</strong> ${solidWeight2.toFixed(2)} kg</div>
                <div class="result-item"><strong>總固體重量:</strong> ${totalSolidWeight.toFixed(2)} kg</div>
                <div class="result-item"><strong>總漿料重量:</strong> ${totalWeight.toFixed(2)} kg</div>
                <div class="result-item"><strong>混合後的固含量百分比:</strong> ${solidContentPercentage.toFixed(2)}%</div>
                <div class="formula">
                    <strong>計算公式:</strong><br>
                    第一批固體重量 = 第一批漿料重量 × 第一批固體百分比 / 100<br>
                    第二批固體重量 = 第二批漿料重量 × 第二批固體百分比 / 100<br>
                    總固體重量 = 第一批固體重量 + 第二批固體重量<br>
                    總漿料重量 = 第一批漿料重量 + 第二批漿料重量<br>
                    固含量百分比 = (總固體重量 / 總漿料重量) × 100
                </div>
            `;

            document.getElementById('results').innerHTML = resultHTML;

            // 紀錄歷史紀錄
            const historyDiv = document.createElement('div');
            historyDiv.className = 'history-item';
            const now = new Date();
            historyCount++;
            const timestamp = `<div class="timestamp">計算時間: ${now.toLocaleDateString()} ${now.toLocaleTimeString()}</div>`;
            const header = `<div class="history-header">紀錄 #${historyCount}</div>`;
            historyDiv.innerHTML = header + resultHTML + timestamp;
            document.getElementById('history').appendChild(historyDiv);
        }

        function resetForm() {
            document.getElementById('batch1Weight').value = '';
            document.getElementById('batch1SolidPercentage').value = '';
            document.getElementById('batch2Weight').value = '';
            document.getElementById('batch2SolidPercentage').value = '';
            document.getElementById('results').innerHTML = '';
            document.getElementById('batch1WeightError').textContent = '';
            document.getElementById('batch1SolidPercentageError').textContent = '';
            document.getElementById('batch2WeightError').textContent = '';
            document.getElementById('batch2SolidPercentageError').textContent = '';
            document.getElementById('history').innerHTML = '<h2>歷史紀錄</h2>';
            historyCount = 0;
        }
    </script>
</body>
</html>
