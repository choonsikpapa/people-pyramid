<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>실시간 학급 인구 피라미드 시뮬레이터</title>
    <!-- Chart.js 라이브러리 -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary: #4f46e5;
            --primary-hover: #4338ca;
            --bg: #f3f4f6;
            --text: #1f2937;
            --card: #ffffff;
            --accent: #d97706;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans KR", sans-serif;
            background-color: var(--bg);
            color: var(--text);
            line-height: 1.5;
        }

        /* Header */
        .header {
            background: linear-gradient(135deg, #312e81, #4f46e5);
            color: white;
            padding: 20px 15px;
            text-align: center;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }

        .header h1 {
            font-size: 22px;
            font-weight: 700;
            margin-bottom: 12px;
            word-break: keep-all;
        }

        .nav-buttons {
            display: flex;
            justify-content: center;
            gap: 8px;
            max-width: 500px;
            margin: 0 auto;
        }

        .nav-btn {
            flex: 1;
            background: rgba(255, 255, 255, 0.15);
            border: 1px solid rgba(255, 255, 255, 0.3);
            color: white;
            padding: 10px 14px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            font-size: 14px;
            transition: all 0.2s ease;
        }

        .nav-btn:hover {
            background: rgba(255, 255, 255, 0.25);
        }

        .nav-btn.active {
            background: white;
            color: var(--primary);
            border-color: white;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        /* Container */
        .container {
            max-width: 1100px;
            margin: 20px auto;
            padding: 0 12px;
        }

        .view-section {
            display: none;
            background: var(--card);
            padding: 24px;
            border-radius: 16px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
        }

        .view-section.active {
            display: block;
        }

        /* Status Badge */
        .mode-badge {
            text-align: center;
            padding: 10px 16px;
            border-radius: 8px;
            font-weight: 700;
            font-size: 14px;
            margin-bottom: 16px;
        }

        .mode-live {
            background: #d1fae5;
            color: #065f46;
            border: 1px solid #a7f3d0;
        }

        .mode-manual {
            background: #fef3c7;
            color: #92400e;
            border: 1px solid #fde68a;
        }

        /* Stats Grid (PC) */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
            gap: 12px;
            margin-bottom: 20px;
        }

        .stat-card {
            background: #f8fafc;
            padding: 14px 10px;
            border-radius: 10px;
            text-align: center;
            border: 1px solid #e2e8f0;
        }

        .stat-card .stat-label {
            font-size: 12px;
            color: #64748b;
            font-weight: 600;
            word-break: keep-all;
        }

        .stat-value {
            font-size: 20px;
            font-weight: 800;
            color: var(--primary);
            margin-top: 4px;
        }

        /* Manual Controls Panel */
        .manual-controls-panel {
            background: #fffbeb;
            border: 2px dashed #f59e0b;
            padding: 16px;
            border-radius: 12px;
            margin-bottom: 20px;
        }

        .manual-controls-panel h3 {
            margin: 0 0 12px 0;
            color: #b45309;
            font-size: 15px;
            text-align: center;
            word-break: keep-all;
        }

        .manual-inputs {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            justify-content: center;
        }

        .manual-inputs > div {
            display: flex;
            flex-direction: column;
            gap: 4px;
            flex: 1 1 120px;
            max-width: 160px;
        }

        .manual-inputs label {
            font-size: 12px;
            font-weight: 700;
            color: #78350f;
            text-align: center;
        }

        .manual-inputs input {
            padding: 8px;
            border: 1px solid #fcd34d;
            border-radius: 6px;
            text-align: center;
            font-weight: 700;
            font-size: 15px;
            background: #ffffff;
        }

        .manual-actions {
            display: flex;
            justify-content: center;
            gap: 8px;
            margin-top: 12px;
            flex-wrap: wrap;
        }

        .btn-apply {
            background: #d97706;
            color: white;
            border: none;
            padding: 10px 16px;
            border-radius: 8px;
            font-weight: 700;
            cursor: pointer;
            font-size: 13px;
        }

        .btn-reset {
            background: #4b5563;
            color: white;
            border: none;
            padding: 10px 16px;
            border-radius: 8px;
            font-weight: 700;
            cursor: pointer;
            font-size: 13px;
        }

        /* Overlay Radio Controls */
        .overlay-controls {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin: 15px 0;
            padding: 12px;
            background: #f1f5f9;
            border-radius: 10px;
            flex-wrap: wrap;
        }

        .overlay-controls span {
            font-weight: 700;
            font-size: 13px;
            color: #334155;
        }

        .radio-group {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }

        .radio-label {
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 4px;
            font-size: 13px;
            font-weight: 600;
            padding: 6px 10px;
            background: #ffffff;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            transition: all 0.2s;
        }

        .radio-label input[type="radio"] {
            accent-color: var(--primary);
        }

        .radio-label.selected {
            border-color: var(--primary);
            background: #e0e7ff;
            color: var(--primary);
        }

        /* Chart Container */
        .chart-container {
            position: relative;
            height: 520px;
            width: 100%;
            margin-top: 10px;
        }

        /* Form Controls */
        .form-group {
            margin-bottom: 20px;
            background: #f8fafc;
            padding: 16px;
            border-radius: 12px;
            border: 1px solid #e2e8f0;
        }

        .form-group label {
            display: block;
            font-size: 15px;
            font-weight: 700;
            color: #1e293b;
            margin-bottom: 8px;
            line-height: 1.4;
        }

        .form-group input {
            width: 100%;
            padding: 12px 14px;
            border: 2px solid #cbd5e1;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 600;
            background: white;
            outline: none;
            transition: border-color 0.2s;
        }

        .form-group input:focus {
            border-color: var(--primary);
        }

        .submit-btn {
            width: 100%;
            background: var(--primary);
            color: white;
            border: none;
            padding: 16px;
            border-radius: 10px;
            font-size: 17px;
            font-weight: 800;
            cursor: pointer;
            box-shadow: 0 4px 10px rgba(79, 70, 229, 0.3);
            transition: background 0.2s;
        }

        .submit-btn:hover {
            background: var(--primary-hover);
        }

        .submit-btn:disabled {
            background: #9ca3af;
            box-shadow: none;
            cursor: not-allowed;
        }

        #submit-result {
            text-align: center;
            margin-top: 15px;
            font-weight: 700;
            color: #059669;
            font-size: 15px;
            word-break: keep-all;
        }

        /* Mobile Only Responsive Styles (@media) */
        @media (max-width: 768px) {
            .header {
                padding: 15px 10px;
            }

            .header h1 {
                font-size: 18px;
            }

            .container {
                margin: 12px auto;
                padding: 0 8px;
            }

            .view-section {
                padding: 16px 12px;
                border-radius: 12px;
            }

            .stats-grid {
                grid-template-columns: repeat(2, 1fr);
                gap: 8px;
            }

            .stat-card {
                padding: 10px 6px;
            }

            .stat-value {
                font-size: 18px;
            }

            .chart-container {
                height: 380px;
            }

            .manual-controls-panel {
                padding: 12px 8px;
            }

            .manual-inputs > div {
                flex: 1 1 100%;
                max-width: 100%;
            }

            .overlay-controls {
                flex-direction: column;
                gap: 8px;
                align-items: stretch;
            }

            .radio-group {
                justify-content: space-between;
            }

            .radio-label {
                flex: 1;
                justify-content: center;
                padding: 8px 4px;
                font-size: 12px;
            }

            .form-group {
                padding: 14px 12px;
                margin-bottom: 14px;
            }

            .form-group label {
                font-size: 14px;
            }

            .form-group input {
                padding: 10px 12px;
                font-size: 16px;
            }

            .submit-btn {
                padding: 14px;
                font-size: 16px;
            }
        }
    </style>
</head>
<body>

    <div class="header">
        <h1>📊 실시간 학급 인구 피라미드 시뮬레이터</h1>
        <div class="nav-buttons">
            <button class="nav-btn active" onclick="switchView('dashboard', this)">📈 교사 대시보드</button>
            <button class="nav-btn" onclick="switchView('submit', this)">📱 학생 설문 제출</button>
        </div>
    </div>

    <div class="container">
        <!-- 1. 교사 대시보드 뷰 -->
        <div id="view-dashboard" class="view-section active">
            <div id="status-badge" class="mode-badge mode-live">
                🟢 실시간 학생 데이터 연동 중
            </div>
            
            <div class="stats-grid">
                <div class="stat-card">
                    <div class="stat-label">참여 학생 수</div>
                    <div class="stat-value" id="stat-count">0명</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">평균 희망 자녀</div>
                    <div class="stat-value" id="stat-children">0.00명</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">평균 결혼 의향률</div>
                    <div class="stat-value" id="stat-marriage">0.0%</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">평균 기대 수명</div>
                    <div class="stat-value" id="stat-lifespan">0.0세</div>
                </div>
                <div class="stat-card" style="background:#eff6ff; border-color:#bfdbfe;">
                    <div class="stat-label" style="color:#1e40af;">추정 합계출산율(TFR)</div>
                    <div class="stat-value" id="stat-tfr" style="color:#1d4ed8;">0.00</div>
                </div>
            </div>

            <!-- 수동 시뮬레이션 패널 -->
            <div class="manual-controls-panel">
                <h3>🛠️ 수동 시뮬레이션 설정 (선생님 직접 입력)</h3>
                <div class="manual-inputs">
                    <div>
                        <label>희망 자녀 수(명)</label>
                        <input type="number" id="manual-children" step="0.1" value="1.5">
                    </div>
                    <div>
                        <label>결혼 의향률(%)</label>
                        <input type="number" id="manual-marriage" step="1" value="80">
                    </div>
                    <div>
                        <label>기대 수명(세)</label>
                        <input type="number" id="manual-lifespan" step="1" value="85">
                    </div>
                </div>
                <div class="manual-actions">
                    <button class="btn-apply" onclick="applyManualSimulation()">수동 시뮬레이션 적용</button>
                    <button class="btn-reset" onclick="resetToLiveData()">학생 데이터로 복구</button>
                </div>
            </div>

            <!-- 시대별 비교 오버레이 라디오 버튼 -->
            <div class="overlay-controls">
                <span>🔍 실제 시대별 인구구조 비교:</span>
                <div class="radio-group">
                    <label class="radio-label selected">
                        <input type="radio" name="overlay" value="none" checked onchange="updateOverlay(this)"> 없음
                    </label>
                    <label class="radio-label">
                        <input type="radio" name="overlay" value="1960" onchange="updateOverlay(this)"> 1960년
                    </label>
                    <label class="radio-label">
                        <input type="radio" name="overlay" value="2025" onchange="updateOverlay(this)"> 2025년
                    </label>
                    <label class="radio-label">
                        <input type="radio" name="overlay" value="2060" onchange="updateOverlay(this)"> 2060년
                    </label>
                </div>
            </div>

            <div class="chart-container">
                <canvas id="pyramidChart"></canvas>
            </div>
            
            <button onclick="clearData()" style="margin-top:20px; background:#ef4444; color:white; border:none; padding:10px 15px; border-radius:6px; cursor:pointer; font-weight:bold;">⚠️ 전체 응답 초기화 (새 수업 시작)</button>
        </div>

        <!-- 2. 학생 제출 뷰 -->
        <div id="view-submit" class="view-section">
            <h2 style="text-align: center; margin-bottom: 20px; font-size:20px;">나의 미래 계획 입력하기</h2>
            <div class="form-group">
                <label>1. 나는 앞으로 자녀를 몇 명 낳고 싶나요? (명)</label>
                <input type="number" id="input-children" min="0" max="10" step="1" placeholder="예: 0, 1, 2...">
            </div>
            <div class="form-group">
                <label>2. 나는 성인이 된 후 결혼할 의향이 있나요? (%)</label>
                <input type="number" id="input-marriage" min="0" max="100" step="5" placeholder="결혼 안함(0) ~ 무조건 함(100)">
            </div>
            <div class="form-group">
                <label>3. 나는 몇 살까지 살고 싶나요? (세)</label>
                <input type="number" id="input-lifespan" min="50" max="120" step="1" placeholder="예: 85, 90, 100">
            </div>
            <button class="submit-btn" id="btn-submit">응답 제출하기</button>
            <div id="submit-result"></div>
        </div>
    </div>

    <!-- Firebase SDK (ES Module) -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-app.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, getDocs, deleteDoc, doc } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyDhBOpQWkZ92Q09PZy8WxprnVROTb_Pd_U",
            authDomain: "pyramid-89a8c.firebaseapp.com",
            projectId: "pyramid-89a8c",
            storageBucket: "pyramid-89a8c.firebasestorage.app",
            messagingSenderId: "635007709561",
            appId: "1:635007709561:web:350509054c10e302efffa8",
            measurementId: "G-TCS2Z2BQ2E"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const surveyCol = collection(db, "class_surveys");

        // 앱 상태 변수
        let isManualMode = false;
        let liveData = { children: 0, marriage: 0, lifespan: 0, count: 0 };
        let pyramidChart = null;
        let currentOverlay = 'none';

        // 연령대 라벨 (위: 100세 이상 ~ 아래: 0~4세 고정)
        const ageLabels = [
            '100세 이상', '95~99세', '90~94세', '85~89세', '80~84세',
            '75~79세', '70~74세', '65~69세', '60~64세', '55~59세',
            '50~54세', '45~49세', '40~44세', '35~39세', '30~34세',
            '25~29세', '20~24세', '15~19세', '10~14세', '5~9세', '0~4세'
        ];

        // 시대별 인구 비율 (위:100세+ ~ 아래:0~4세)
        const overlayProfiles = {
            '1960': [0.01, 0.05, 0.1, 0.3, 0.6, 1.1, 1.8, 2.7, 3.8, 4.8, 5.8, 6.5, 7.2, 8.0, 8.8, 9.5, 10.5, 11.8, 13.2, 14.5, 16.2],
            '2025': [0.1, 0.3, 0.8, 1.8, 3.2, 5.1, 6.8, 8.2, 8.9, 8.5, 8.2, 7.8, 7.2, 6.4, 5.4, 4.6, 4.2, 3.8, 3.5, 3.2, 2.8],
            '2060': [1.2, 3.5, 6.2, 8.8, 10.2, 10.8, 10.5, 9.2, 8.0, 7.1, 6.2, 5.5, 4.8, 4.2, 3.8, 3.2, 2.8, 2.4, 2.1, 1.8, 1.5]
        };

        // 탭 전환 함수
        window.switchView = function(view, btnElement) {
            document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
            
            document.getElementById('view-' + view).classList.add('active');
            if(btnElement) btnElement.classList.add('active');
        }

        // 학생 제출 함수
        document.getElementById('btn-submit').addEventListener('click', () => {
            const children = parseFloat(document.getElementById('input-children').value);
            const marriage = parseFloat(document.getElementById('input-marriage').value);
            const lifespan = parseFloat(document.getElementById('input-lifespan').value);

            if(isNaN(children) || isNaN(marriage) || isNaN(lifespan)) {
                alert('모든 항목에 숫자를 정확히 입력해 주세요.');
                return;
            }

            const btn = document.getElementById('btn-submit');
            btn.disabled = true;
            btn.innerText = "제출 중...";

            addDoc(surveyCol, {
                children: children,
                marriage: marriage,
                lifespan: lifespan,
                timestamp: new Date()
            }).catch(error => console.error("제출 오류:", error));

            setTimeout(() => {
                document.getElementById('submit-result').innerText = "✅ 성공적으로 제출되었습니다! 상단의 '대시보드' 탭을 확인하세요.";
                document.getElementById('input-children').value = '';
                document.getElementById('input-marriage').value = '';
                document.getElementById('input-lifespan').value = '';
                
                btn.disabled = false;
                btn.innerText = "응답 제출하기";

                setTimeout(() => {
                    document.getElementById('submit-result').innerText = "";
                }, 3500);
            }, 300);
        });

        // 차트 생성
        function initChart() {
            const ctx = document.getElementById('pyramidChart').getContext('2d');
            pyramidChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ageLabels,
                    datasets: [
                        { label: '남성', data: Array(21).fill(0), backgroundColor: 'rgba(54, 162, 235, 0.85)' },
                        { label: '여성', data: Array(21).fill(0), backgroundColor: 'rgba(255, 99, 132, 0.85)' },
                        { 
                            label: '비교 오버레이(남성)', 
                            data: Array(21).fill(0), 
                            type: 'line', 
                            borderColor: '#3b82f6', 
                            borderWidth: 2, 
                            borderDash: [4, 4], 
                            fill: false,
                            pointRadius: 0
                        },
                        { 
                            label: '비교 오버레이(여성)', 
                            data: Array(21).fill(0), 
                            type: 'line', 
                            borderColor: '#ec4899', 
                            borderWidth: 2, 
                            borderDash: [4, 4], 
                            fill: false,
                            pointRadius: 0
                        }
                    ]
                },
                options: {
                    indexAxis: 'y',
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: {
                            stacked: true,
                            ticks: { callback: val => Math.abs(val).toFixed(1) + '%' },
                            title: { display: true, text: '전체 인구 대비 비율 (%)' },
                            min: -15,
                            max: 15
                        },
                        y: { 
                            stacked: true,
                            ticks: { font: { size: 11 } }
                        }
                    },
                    plugins: {
                        tooltip: { callbacks: { label: ctx => `${ctx.dataset.label}: ${Math.abs(ctx.raw).toFixed(2)}%` } },
                        legend: {
                            labels: {
                                filter: item => !item.text.includes('비교 오버레이')
                            }
                        }
                    },
                    animation: { duration: 600 }
                }
            });
        }

        // 인구 분포 계산 로직 (위:100세+ ~ 아래:0~4세)
        function calculatePyramid(tfr, lifespan) {
            const r = Math.pow(Math.max(0.05, tfr) / 2.05, 1.0 / 6.0);
            const weights = [];

            for(let i = 0; i < 21; i++) {
                const k = 20 - i; // 0~4세가 k=0, 100세+가 k=20
                const age = k * 5 + 2.5;
                const baseSize = Math.pow(r, -k);
                const survival = 1.0 / (1.0 + Math.exp((age - lifespan) / 4.0));
                weights.push(baseSize * survival);
            }

            const total = weights.reduce((a, b) => a + b, 0);
            return weights.map(w => (w / total) * 100.0);
        }

        // 대시보드 화면 업데이트
        function updateDashboard(avgChildren, avgMarriage, avgLifespan, count) {
            const tfr = avgChildren * (avgMarriage / 100);

            document.getElementById('stat-count').innerText = `${count}명`;
            document.getElementById('stat-children').innerText = `${avgChildren.toFixed(2)}명`;
            document.getElementById('stat-marriage').innerText = `${avgMarriage.toFixed(1)}%`;
            document.getElementById('stat-lifespan').innerText = `${avgLifespan.toFixed(1)}세`;
            document.getElementById('stat-tfr').innerText = tfr.toFixed(2);

            const percentages = calculatePyramid(tfr, avgLifespan);
            const maleData = percentages.map(p => -(p * 0.49).toFixed(2));
            const femaleData = percentages.map(p => (p * 0.51).toFixed(2));

            pyramidChart.data.datasets[0].data = maleData;
            pyramidChart.data.datasets[1].data = femaleData;

            // 오버레이 업데이트
            if(currentOverlay !== 'none' && overlayProfiles[currentOverlay]) {
                const profile = overlayProfiles[currentOverlay];
                pyramidChart.data.datasets[2].data = profile.map(p => -(p * 0.49).toFixed(2));
                pyramidChart.data.datasets[3].data = profile.map(p => (p * 0.51).toFixed(2));
            } else {
                pyramidChart.data.datasets[2].data = Array(21).fill(0);
                pyramidChart.data.datasets[3].data = Array(21).fill(0);
            }

            pyramidChart.update();
        }

        // 실시간 데이터 수신
        onSnapshot(query(surveyCol), (snapshot) => {
            let totalChildren = 0;
            let totalMarriage = 0;
            let totalLifespan = 0;
            const count = snapshot.size;

            if (count > 0) {
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    totalChildren += data.children || 0;
                    totalMarriage += data.marriage || 0;
                    totalLifespan += data.lifespan || 0;
                });

                liveData = {
                    children: totalChildren / count,
                    marriage: totalMarriage / count,
                    lifespan: totalLifespan / count,
                    count: count
                };
            } else {
                liveData = { children: 0, marriage: 0, lifespan: 0, count: 0 };
            }

            if(!isManualMode) {
                updateDashboard(liveData.children, liveData.marriage, liveData.lifespan, liveData.count);
            }
        });

        // 수동 시뮬레이션 버튼 동작
        window.applyManualSimulation = function() {
            const ch = parseFloat(document.getElementById('manual-children').value);
            const ma = parseFloat(document.getElementById('manual-marriage').value);
            const li = parseFloat(document.getElementById('manual-lifespan').value);

            if(isNaN(ch) || isNaN(ma) || isNaN(li)) {
                alert('수동 설정값을 모두 입력해주세요.');
                return;
            }

            isManualMode = true;
            const badge = document.getElementById('status-badge');
            badge.className = "mode-badge mode-manual";
            badge.innerText = "🛠️ 수동 시뮬레이션 적용 중 (선생님 지정 값)";

            updateDashboard(ch, ma, li, liveData.count);
        }

        // 학생 데이터 복구
        window.resetToLiveData = function() {
            isManualMode = false;
            const badge = document.getElementById('status-badge');
            badge.className = "mode-badge mode-live";
            badge.innerText = "🟢 실시간 학생 데이터 연동 중";

            updateDashboard(liveData.children, liveData.marriage, liveData.lifespan, liveData.count);
        }

        // 시대별 라디오 버튼 오버레이
        window.updateOverlay = function(radio) {
            document.querySelectorAll('.radio-label').forEach(el => el.classList.remove('selected'));
            radio.parentElement.classList.add('selected');

            currentOverlay = radio.value;
            if(isManualMode) {
                applyManualSimulation();
            } else {
                updateDashboard(liveData.children, liveData.marriage, liveData.lifespan, liveData.count);
            }
        }

        // 초기화 기능
        window.clearData = async function() {
            if(confirm("모든 학생의 응답 데이터를 삭제하시겠습니까? (복구 불가)")) {
                try {
                    const qs = await getDocs(surveyCol);
                    qs.forEach(async (d) => {
                        await deleteDoc(doc(db, "class_surveys", d.id));
                    });
                    alert("데이터가 완전히 초기화되었습니다.");
                } catch(e) {
                    alert("초기화 실패: " + e.message);
                }
            }
        }

        window.onload = initChart;
    </script>
</body>
</html>
