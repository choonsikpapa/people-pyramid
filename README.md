<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>학생 설문 기반 미래 인구 피라미드 시각화</title>
    <!-- 차트 라이브러리 (Chart.js) 불러오기 -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Malgun Gothic', '맑은 고딕', sans-serif; background-color: #f4f7f6; padding: 20px; margin: 0; }
        .container { max-width: 850px; margin: 0 auto; background: #ffffff; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h2 { text-align: center; color: #2c3e50; margin-bottom: 20px; }
        .input-box { display: flex; gap: 15px; flex-wrap: wrap; background: #eef2f5; padding: 15px; border-radius: 8px; margin-bottom: 15px; }
        .input-group { flex: 1; min-width: 180px; }
        label { display: block; font-weight: bold; margin-bottom: 5px; color: #34495e; font-size: 0.9em; }
        input { width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        button { width: 100%; padding: 12px; background-color: #3498db; color: white; border: none; border-radius: 6px; font-weight: bold; cursor: pointer; font-size: 1em; }
        button:hover { background-color: #2980b9; }
        .chart-container { position: relative; height: 600px; margin-top: 20px; }
    </style>
</head>
<body>

<div class="container">
    <h2>📊 학생 설문 기반 미래 인구 피라미드</h2>
    
    <div class="input-box">
        <div class="input-group">
            <label for="children">희망 자녀 수 (명)</label>
            <input type="number" id="children" value="0.8" step="0.1" min="0">
        </div>
        <div class="input-group">
            <label for="lifespan">평균 기대 수명 (세)</label>
            <input type="number" id="lifespan" value="90" min="50" max="120">
        </div>
        <div class="input-group">
            <label for="marriage">결혼 의향 비율 (%)</label>
            <input type="number" id="marriage" value="60" min="0" max="100">
        </div>
    </div>
    
    <button onclick="generatePyramid()">인구 피라미드 생성하기</button>

    <div class="chart-container">
        <canvas id="pyramidChart"></canvas>
    </div>
</div>

<script>
let pyramidChart = null;

const ageLabels = [
    '0-4세', '5-9세', '10-14세', '15-19세', '20-24세', '25-29세', 
    '30-34세', '35-39세', '40-44세', '45-49세', '50-54세', '55-59세', 
    '60-64세', '65-69세', '70-74세', '75-79세', '80-84세', '85-89세', 
    '90-94세', '95-99세', '100세 이상'
];

function generatePyramid() {
    const children = parseFloat(document.getElementById('children').value) || 0;
    const lifespan = parseFloat(document.getElementById('lifespan').value) || 80;
    const marriage = parseFloat(document.getElementById('marriage').value) || 0;

    // 설문 데이터를 5세 단위 21개 구간 인구 비중(%)으로 가공
    const tfr = children * (marriage / 100); // 추정 출산율
    let weights = [];

    for (let i = 0; i < ageLabels.length; i++) {
        let age = i * 5 + 2.5; // 구간 중앙값
        let weight = 0;

        if (age < 15) {
            // 유소년층 (0~14세): 출산율 반영
            weight = Math.max(0.3, tfr * 3.5);
        } else if (age < 65) {
            // 생산연령층 (15~64세): 완만한 인구 유지 곡선
            weight = 4.0 + Math.sin((i - 3) / 10 * Math.PI) * 1.5;
        } else {
            // 고령층 (65세 이상): 기대수명 반영 감쇠 곡선
            if (age <= lifespan) {
                let factor = (lifespan - age) / (lifespan - 65);
                weight = 4.5 * Math.pow(factor, 0.7);
            } else {
                weight = 0.1;
            }
        }
        weights.push(weight);
    }

    // 총합 100% 정규화 및 남/녀 분할
    const totalWeight = weights.reduce((a, b) => a + b, 0);
    const maleData = [];
    const femaleData = [];

    weights.forEach((w, i) => {
        let pct = (w / totalWeight) * 100;
        // 남성은 음수로 처리하여 좌측 배치
        maleData.push(-parseFloat((pct * 0.49).toFixed(2))); 
        femaleData.push(parseFloat((pct * 0.51).toFixed(2)));
    });

    renderChart(maleData, femaleData);
}

function renderChart(maleData, femaleData) {
    const ctx = document.getElementById('pyramidChart').getContext('2d');
    
    if (pyramidChart) {
        pyramidChart.destroy();
    }

    pyramidChart = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: ageLabels,
            datasets: [
                {
                    label: '남성',
                    data: maleData,
                    backgroundColor: 'rgba(54, 162, 235, 0.75)',
                    borderColor: 'rgba(54, 162, 235, 1)',
                    borderWidth: 1
                },
                {
                    label: '여성',
                    data: femaleData,
                    backgroundColor: 'rgba(255, 99, 132, 0.75)',
                    borderColor: 'rgba(255, 99, 132, 1)',
                    borderWidth: 1
                }
            ]
        },
        options: {
            indexAxis: 'y', // 가로 막대 그래프
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: {
                    stacked: true,
                    ticks: {
                        callback: function(val) {
                            return Math.abs(val) + '%'; // 음수 기호 제거 후 출력
                        }
                    },
                    title: { display: true, text: '전체 인구 대비 비중 (%)' }
                },
                y: {
                    stacked: true,
                    title: { display: true, text: '연령대 (5세 단위)' }
                }
            },
            plugins: {
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            let label = context.dataset.label || '';
                            let value = Math.abs(context.raw);
                            return label + ': ' + value + '%';
                        }
                    }
                }
            }
        }
    });
}

// 최초 실행
window.onload = generatePyramid;
</script>

</body>
</html>
