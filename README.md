<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>실시간 미래 인구 피라미드 (학급 연동)</title>
    <!-- Chart.js 라이브러리 -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root { --primary: #4f46e5; --bg: #f8fafc; --text: #1e293b; --card: #ffffff; }
        body { font-family: 'Pretendard', 'Malgun Gothic', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 0; }
        .header { background: linear-gradient(135deg, #312e81, #4f46e5); color: white; padding: 25px 20px; text-align: center; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .header h1 { margin: 0 0 15px 0; font-size: 24px; }
        
        .nav-buttons { display: flex; justify-content: center; gap: 10px; }
        .nav-btn { background: rgba(255,255,255,0.15); border: 1px solid rgba(255,255,255,0.3); color: white; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: bold; transition: all 0.2s; }
        .nav-btn:hover { background: rgba(255,255,255,0.25); }
        .nav-btn.active { background: white; color: var(--primary); box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        
        .container { max-width: 1100px; margin: 30px auto; padding: 0 20px; }
        .view-section { display: none; background: var(--card); padding: 30px; border-radius: 16px; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.05); }
        .view-section.active { display: block; animation: fadeIn 0.3s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 15px; margin-bottom: 25px; }
        .stat-card { background: #f1f5f9; padding: 20px 15px; border-radius: 12px; text-align: center; border: 1px solid #e2e8f0; }
        .stat-value { font-size: 26px; font-weight: 900; color: var(--primary); margin-top: 8px; }
        
        .overlay-controls { display: flex; align-items: center; justify-content: center; gap: 15px; margin: 20px 0; padding: 15px; background: #f8fafc; border-radius: 10px; border: 1px solid #e2e8f0; flex-wrap: wrap; }
        .overlay-controls span { font-weight: bold; color: #475569; }
        .radio-group { display: flex; gap: 10px; }
        .radio-label { cursor: pointer; display: flex; align-items: center; gap: 5px; font-size: 14px; font-weight: 600; padding: 8px 12px; background: white; border: 1px solid #cbd5e1; border-radius: 6px; transition: all 0.2s; }
        .radio-label:hover { background: #f1f5f9; }
        .radio-label input[type="radio"] { accent-color: var(--primary); }
        .radio-label.selected { border-color: var(--primary); background: #e0e7ff; color: var(--primary); }

        .chart-container { position: relative; height: 550px; width: 100%; margin-top: 10px; }
        
        .form-group { margin-bottom: 25px; background: #f8fafc; padding: 20px; border-radius: 12px; border: 1px solid #e2e8f0; }
        .form-group label { display: block; font-weight: bold; margin-bottom: 12px; font-size: 16px; color: #334155; }
        .form-group input { width: 100%; padding: 14px; border: 2px solid #cbd5e1; border-radius: 8px; box-sizing: border-box; font-size: 16px; outline: none; transition: border-color 0.2s; }
        .form-group input:focus { border-color: var(--primary); }
        .submit-btn { width: 100%; background: var(--primary); color: white; border: none; padding: 18px; border-radius: 12px; font-size: 18px; font-weight: bold; cursor: pointer; box-shadow: 0 4px 6px rgba(79, 70, 229, 0.25); transition: background 0.2s; }
        .submit-btn:hover { background: #4338ca; }
        .submit-btn:disabled { background: #94a3b8; cursor: not-allowed; }
        
        #status-msg { text-align: center; margin-bottom: 20px; font-weight: bold; color: #059669; padding: 10px; background: #d1fae5; border-radius: 8px; }
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
            <div id="status-msg">클라우드 데이터베이스 연결 중...</div>
            
            <div class="stats-grid">
                <div class="stat-card">
                    <div style="font-size:14px; color:#64748b;">참여 학생 수</div>
                    <div class="stat-value" id="stat-count">0명</div>
                </div>
                <div class="stat-card">
                    <div style="font-size:14px; color:#64748b;">평균 희망 자녀 수</div>
                    <div class="stat-value" id="stat-children">0.00명</div>
                </div>
                <div class="stat-card">
                    <div style="font-size:14px; color:#64748b;">평균 결혼 의향률</div>
                    <div class="stat-value" id="stat-marriage">0%</div>
                </div>
                <div class="stat-card">
                    <div style="font-size:14px; color:#64748b;">평균 기대 수명</div>
                    <div class="stat-value" id="stat-lifespan">0세</div>
                </div>
                <div class="stat-card" style="background:#e0e7ff; border-color:#c7d2fe;">
                    <div style="font-size:14px; font-weight:bold; color:#4338ca;">추정 합계출산율(TFR)</div>
                    <div class="stat-value" id="stat-tfr" style="color:#4338ca;">0.00</div>
                </div>
            </div>

            <!-- 오버레이 컨트롤 (비교 연도 선택) -->
            <div class="overlay-controls">
                <span>🔍 실제 시대별 인구구조 비교:</span>
                <div class="radio-group">
                    <label class="radio-label selected" id="lbl-none">
                        <input type="radio" name="overlayYear" value="none" checked onchange="changeOverlay(this.value)"> 비교 없음
                    </label>
                    <label class="radio-label" id="lbl-1960">
                        <input type="radio" name="overlayYear" value="1960" onchange="changeOverlay(this.value)"> 1960년 (다산다사)
                    </label>
                    <label class="radio-label" id="lbl-2025">
                        <input type="radio" name="overlayYear" value="2025" onchange="changeOverlay(this.value)"> 2025년 (고령화)
                    </label>
                    <label class="radio-label" id="lbl-2060">
                        <input type="radio" name="overlayYear" value="2060" onchange="changeOverlay(this.value)"> 2060년 (초고령사회)
                    </label>
                </div>
            </div>

            <div class="chart-container">
                <canvas id="pyramidChart"></canvas>
            </div>
            
            <div style="text-align: right; margin-top: 20px;">
                <button onclick="clearData()" style="background:#ef4444; color:white; border:none; padding:10px 15px; border-radius:8px; cursor:pointer; font-weight:bold; transition: background 0.2s;">
                    <i class="fa-solid fa-trash"></i> ⚠️ 전체 응답 초기화 (새 수업 시작)
                </button>
            </div>
        </div>

        <!-- 2. 학생 제출 뷰 -->
        <div id="view-submit" class="view-section">
            <h2 style="text-align: center; margin-bottom: 25px; color: var(--primary);">나의 미래 계획 입력하기</h2>
            <div class="form-group">
                <label>1. 나는 앞으로 자녀를 몇 명 낳고 싶나요? (명)</label>
                <input type="number" id="input-children" min="0" max="10" placeholder="예: 0, 1, 2...">
            </div>
            <div class="form-group">
                <label>2. 나는 성인이 된 후 결혼할 의향이 있나요? (%)</label>
                <input type="number" id="input-marriage" min="0" max="100" placeholder="결혼 안함(0) ~ 무조건 함(100)">
            </div>
            <div class="form-group">
                <label>3. 나는 몇 살까지 살고 싶나요? (세)</label>
                <input type="number" id="input-lifespan" min="50" max="120" placeholder="예: 85, 90, 100">
            </div>
            <button class="submit-btn" id="btn-submit">내 응답 제출하여 피라미드 만들기</button>
            <div id="submit-result" style="text-align:center; margin-top:20px; font-weight:bold; color:#059669; font-size:18px;"></div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-app.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, getDocs, deleteDoc, doc } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-firestore.js";

        // 선생님의 Firebase 고유 설정값
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

        // [핵심 변경 1] 연령대 레이블을 100세 이상부터 0~4세 순으로 '직접' 뒤집어서 생성
        // index 0: 100세 이상 ~ index 20: 0~4세
        let ageLabels = [];
        for (let i = 20; i >= 0; i--) {
            if (i === 20) ageLabels.push('100세 이상');
            else ageLabels.push(`${i*5}~${i*5+4}세`);
        }

        // 과거/미래 비교용 데이터 (0~4세부터 100세 이상 순서로 작성 후 역순 정렬)
        // 남성은 -, 여성은 + 비율
        const historicalData = {
            '1960': {
                male: [-8.8, -7.2, -6.1, -5.2, -4.5, -3.8, -3.2, -2.5, -2.0, -1.6, -1.2, -0.9, -0.6, -0.4, -0.2, -0.1, -0.05, -0.02, -0.01, -0.0, -0.0].reverse(),
                female: [8.5, 7.0, 6.0, 5.0, 4.3, 3.7, 3.1, 2.5, 2.1, 1.7, 1.3, 1.0, 0.8, 0.6, 0.4, 0.2, 0.1, 0.05, 0.02, 0.01, 0.0].reverse()
            },
            '2025': {
                male: [-1.4, -2.1, -2.5, -2.6, -3.2, -3.5, -3.8, -4.2, -4.3, -4.0, -4.2, -3.5, -2.8, -2.2, -1.5, -1.0, -0.6, -0.3, -0.1, -0.0, -0.0].reverse(),
                female: [1.3, 2.0, 2.4, 2.5, 3.0, 3.3, 3.6, 4.0, 4.2, 4.1, 4.3, 3.7, 3.2, 2.6, 1.9, 1.4, 1.0, 0.6, 0.3, 0.1, 0.1].reverse()
            },
            '2060': {
                male: [-0.9, -1.1, -1.4, -1.5, -1.8, -2.0, -2.2, -2.5, -2.8, -3.1, -3.5, -3.8, -4.2, -4.0, -3.5, -2.8, -2.0, -1.2, -0.6, -0.2, -0.1].reverse(),
                female: [0.8, 1.0, 1.3, 1.5, 1.7, 1.9, 2.1, 2.4, 2.7, 3.1, 3.6, 4.0, 4.5, 4.5, 4.2, 3.5, 2.8, 2.0, 1.2, 0.6, 0.3].reverse()
            }
        };

        let pyramidChart = null;
        let currentOverlay = 'none';
        let currentMaleData = Array(21).fill(0);
        let currentFemaleData = Array(21).fill(0);

        function initChart() {
            const ctx = document.getElementById('pyramidChart').getContext('2d');
            pyramidChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ageLabels, // 100세 이상 -> 0~4세 순서
                    datasets: [
                        // 학생 실시간 데이터 (막대)
                        { label: '남성 (우리반)', data: currentMaleData, backgroundColor: 'rgba(56, 189, 248, 0.85)', stack: 'Stack 0', order: 2 },
                        { label: '여성 (우리반)', data: currentFemaleData, backgroundColor: 'rgba(251, 113, 133, 0.85)', stack: 'Stack 0', order: 2 },
                        // 시대별 비교 오버레이 데이터 (선) - 초기에는 빈 배열로 숨김
                        { label: '남성 (비교)', data: [], type: 'line', borderColor: '#475569', borderWidth: 2, borderDash: [5, 5], fill: false, pointRadius: 0, order: 1 },
                        { label: '여성 (비교)', data: [], type: 'line', borderColor: '#475569', borderWidth: 2, borderDash: [5, 5], fill: false, pointRadius: 0, order: 1 }
                    ]
                },
                options: {
                    indexAxis: 'y',
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: {
                            stacked: false, // 선과 막대를 겹치게 하기 위해 x축 스택 해제
                            ticks: { callback: val => Math.abs(val).toFixed(1) + '%' },
                            title: { display: true, text: '전체 인구 대비 비율 (%)', font: {weight:'bold'} },
                            min: -15, max: 15
                        },
                        y: { 
                            stacked: true // 막대그래프를 양옆으로 밀어내기 위해 y축만 스택
                            /* [핵심 변경 2] 배열 자체를 역순으로 짰으므로 reverse: true 옵션 제거! */
                        }
                    },
                    plugins: {
                        legend: {
                            labels: { filter: function(item) { return item.text.includes('(우리반)'); } }
                        },
                        tooltip: {
                            callbacks: {
                                label: ctx => `${ctx.dataset.label}: ${Math.abs(ctx.raw).toFixed(2)}%`
                            }
                        }
                    },
                    animation: { duration: 800 }
                }
            });
        }

        function calculatePyramid(tfr, lifespan) {
            // 0~4세부터 계산
            const ages = Array.from({length: 21}, (_, i) => i * 5 + 2.5);
            const r = Math.pow(Math.max(0.1, tfr) / 2.05, 1.0 / 6.0);
            const baseSize = ages.map((_, i) => Math.pow(r, -i));
            const survival = ages.map(age => 1.0 / (1.0 + Math.exp((age - lifespan) / 4.0)));
            
            const weights = baseSize.map((base, i) => base * survival[i]);
            const total = weights.reduce((a, b) => a + b, 0);
            
            let percentages = weights.map(w => (w / total) * 100.0);
            
            // [핵심 변경 3] 0~4세부터 계산된 결과를 100세이상~0세 순서에 맞게 역순으로 반환
            return percentages.reverse();
        }

        window.changeOverlay = function(year) {
            currentOverlay = year;
            
            // 라디오 버튼 UI 업데이트
            document.querySelectorAll('.radio-label').forEach(el => el.classList.remove('selected'));
            document.getElementById('lbl-' + year).classList.add('selected');

            // 차트 데이터 업데이트
            if (year === 'none') {
                pyramidChart.data.datasets[2].data = [];
                pyramidChart.data.datasets[3].data = [];
            } else {
                pyramidChart.data.datasets[2].data = historicalData[year].male;
                pyramidChart.data.datasets[3].data = historicalData[year].female;
            }
            pyramidChart.update();
        }

        onSnapshot(query(surveyCol), (snapshot) => {
            let totalChildren = 0; let totalMarriage = 0; let totalLifespan = 0;
            const count = snapshot.size;

            const statusEl = document.getElementById('status-msg');
            statusEl.innerText = "🟢 실시간 연결 중 (데이터베이스 정상)";
            statusEl.style.color = "#059669";
            statusEl.style.backgroundColor = "#d1fae5";

            if (count > 0) {
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    totalChildren += data.children || 0;
                    totalMarriage += data.marriage || 0;
                    totalLifespan += data.lifespan || 0;
                });

                const avgChildren = totalChildren / count;
                const avgMarriage = totalMarriage / count;
                const avgLifespan = totalLifespan / count;
                const tfr = avgChildren * (avgMarriage / 100);

                document.getElementById('stat-count').innerText = `${count}명`;
                document.getElementById('stat-children').innerText = `${avgChildren.toFixed(2)}명`;
                document.getElementById('stat-marriage').innerText = `${avgMarriage.toFixed(1)}%`;
                document.getElementById('stat-lifespan').innerText = `${avgLifespan.toFixed(1)}세`;
                document.getElementById('stat-tfr').innerText = tfr.toFixed(2);

                const percentages = calculatePyramid(tfr, avgLifespan);
                currentMaleData = percentages.map(p => -(p * 0.49).toFixed(2));
                currentFemaleData = percentages.map(p => (p * 0.51).toFixed(2));

            } else {
                // 데이터 없음
                document.getElementById('stat-count').innerText = `0명`;
                document.getElementById('stat-children').innerText = `0.00명`;
                document.getElementById('stat-marriage').innerText = `0%`;
                document.getElementById('stat-lifespan').innerText = `0세`;
                document.getElementById('stat-tfr').innerText = `0.00`;
                
                currentMaleData = Array(21).fill(0);
                currentFemaleData = Array(21).fill(0);
            }

            if(pyramidChart) {
                pyramidChart.data.datasets[0].data = currentMaleData;
                pyramidChart.data.datasets[1].data = currentFemaleData;
                pyramidChart.update();
            }
        });

        // 학생 제출 처리
        document.getElementById('btn-submit').addEventListener('click', () => {
            const children = parseFloat(document.getElementById('input-children').value);
            const marriage = parseFloat(document.getElementById('input-marriage').value);
            const lifespan = parseFloat(document.getElementById('input-lifespan').value);

            if(isNaN(children) || isNaN(marriage) || isNaN(lifespan)) {
                alert('모든 항목에 정확한 숫자를 입력해주세요.'); return;
            }

            const btn = document.getElementById('btn-submit');
            btn.disabled = true; btn.innerText = "제출 중...";

            addDoc(surveyCol, { children, marriage, lifespan, timestamp: new Date() })
                .catch(error => console.error("제출 오류:", error));

            // 빠른 화면 응답
            setTimeout(() => {
                document.getElementById('submit-result').innerText = "✅ 성공적으로 제출되었습니다! 교사 대시보드 화면을 확인하세요.";
                document.getElementById('input-children').value = '';
                document.getElementById('input-marriage').value = '';
                document.getElementById('input-lifespan').value = '';
                
                btn.disabled = false; btn.innerText = "내 응답 다시 제출하기";
                setTimeout(() => { document.getElementById('submit-result').innerText = ""; }, 4000);
            }, 300);
        });

        // 탭 전환 
        window.switchView = function(view, btnElement) {
            document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
            document.getElementById('view-' + view).classList.add('active');
            if(btnElement) btnElement.classList.add('active');
        }

        // 초기화
        window.clearData = async function() {
            if(confirm("모든 학생의 응답 데이터를 영구적으로 삭제하시겠습니까? (복구 불가)")) {
                try {
                    const qs = await getDocs(surveyCol);
                    qs.forEach(async (d) => await deleteDoc(doc(db, "class_surveys", d.id)));
                    alert("전체 초기화 완료");
                } catch(e) { alert("삭제 실패: " + e.message); }
            }
        }

        window.onload = initChart;
    </script>
</body>
</html>
