<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>실시간 미래 인구 피라미드 (학급 연동)</title>
    <!-- Chart.js 라이브러리 -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root { --primary: #4f46e5; --bg: #f3f4f6; --text: #1f2937; --card: #ffffff; }
        body { font-family: 'Pretendard', 'Malgun Gothic', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 0; }
        .header { background: var(--primary); color: white; padding: 20px; text-align: center; }
        .nav-buttons { display: flex; justify-content: center; gap: 10px; margin-top: 15px; }
        .nav-btn { background: rgba(255,255,255,0.2); border: 1px solid rgba(255,255,255,0.5); color: white; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: bold; }
        .nav-btn.active { background: white; color: var(--primary); }
        .container { max-width: 1000px; margin: 20px auto; padding: 0 15px; }
        .view-section { display: none; background: var(--card); padding: 25px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
        .view-section.active { display: block; }
        
        /* 대시보드 스타일 */
        .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 20px; }
        .stat-card { background: #f8fafc; padding: 15px; border-radius: 8px; text-align: center; border: 1px solid #e2e8f0; }
        .stat-value { font-size: 24px; font-weight: bold; color: var(--primary); margin-top: 10px; }
        .chart-container { position: relative; height: 500px; margin-top: 20px; }
        
        /* 제출 폼 스타일 */
        .form-group { margin-bottom: 20px; }
        .form-group label { display: block; font-weight: bold; margin-bottom: 8px; }
        .form-group input { width: 100%; padding: 12px; border: 1px solid #cbd5e1; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        .submit-btn { width: 100%; background: var(--primary); color: white; border: none; padding: 14px; border-radius: 6px; font-size: 16px; font-weight: bold; cursor: pointer; }
        .submit-btn:hover { background: #4338ca; }
        
        /* 로딩/알림 */
        #status-msg { text-align: center; margin-bottom: 15px; font-weight: bold; color: #059669; }
    </style>
</head>
<body>

    <div class="header">
        <h1>📊 실시간 학급 인구 피라미드</h1>
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
                    <div>참여 학생 수</div>
                    <div class="stat-value" id="stat-count">0명</div>
                </div>
                <div class="stat-card">
                    <div>평균 희망 자녀 수</div>
                    <div class="stat-value" id="stat-children">0.00명</div>
                </div>
                <div class="stat-card">
                    <div>평균 결혼 의향률</div>
                    <div class="stat-value" id="stat-marriage">0%</div>
                </div>
                <div class="stat-card">
                    <div>평균 기대 수명</div>
                    <div class="stat-value" id="stat-lifespan">0세</div>
                </div>
                <div class="stat-card" style="background:#eff6ff;">
                    <div>추정 합계출산율(TFR)</div>
                    <div class="stat-value" id="stat-tfr">0.00</div>
                </div>
            </div>

            <div class="chart-container">
                <canvas id="pyramidChart"></canvas>
            </div>
            
            <button onclick="clearData()" style="margin-top:20px; background:#ef4444; color:white; border:none; padding:10px 15px; border-radius:6px; cursor:pointer;">⚠️ 전체 응답 초기화 (새 수업 시작)</button>
        </div>

        <!-- 2. 학생 제출 뷰 -->
        <div id="view-submit" class="view-section">
            <h2 style="text-align: center; margin-bottom: 25px;">나의 미래 계획 입력하기</h2>
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
            <button class="submit-btn" id="btn-submit">응답 제출하기</button>
            <div id="submit-result" style="text-align:center; margin-top:15px; font-weight:bold; color:#059669;"></div>
        </div>
    </div>

    <!-- Firebase SDK -->
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

        // 탭 전환 로직
        window.switchView = function(view, btnElement) {
            document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
            
            document.getElementById('view-' + view).classList.add('active');
            if(btnElement) btnElement.classList.add('active');
        }

        // 학생 데이터 제출 로직 (UI 즉시 반응형으로 수정)
        document.getElementById('btn-submit').addEventListener('click', () => {
            const children = parseFloat(document.getElementById('input-children').value);
            const marriage = parseFloat(document.getElementById('input-marriage').value);
            const lifespan = parseFloat(document.getElementById('input-lifespan').value);

            if(isNaN(children) || isNaN(marriage) || isNaN(lifespan)) {
                alert('모든 항목에 숫자를 입력해주세요.');
                return;
            }

            const btn = document.getElementById('btn-submit');
            btn.disabled = true;
            btn.innerText = "제출 중...";

            // Firebase에 데이터 전송 (백그라운드 처리)
            addDoc(surveyCol, {
                children: children,
                marriage: marriage,
                lifespan: lifespan,
                timestamp: new Date()
            }).catch(error => console.error("제출 오류:", error));

            // 데이터베이스 응답 대기 없이 화면 리셋을 0.3초 뒤에 즉시 실행
            setTimeout(() => {
                document.getElementById('submit-result').innerText = "✅ 성공적으로 제출되었습니다! 상단의 '대시보드' 탭을 확인하세요.";
                document.getElementById('input-children').value = '';
                document.getElementById('input-marriage').value = '';
                document.getElementById('input-lifespan').value = '';
                
                btn.disabled = false;
                btn.innerText = "응답 제출하기";

                // 3초 뒤에 성공 메시지 숨기기
                setTimeout(() => {
                    document.getElementById('submit-result').innerText = "";
                }, 3000);
            }, 300);
        });

        // 차트 초기화
        let pyramidChart = null;
        const ageLabels = Array.from({length: 21}, (_, i) => `${i*5}~${i*5+4}세`);
        ageLabels[20] = '100세 이상';

        function initChart() {
            const ctx = document.getElementById('pyramidChart').getContext('2d');
            pyramidChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ageLabels,
                    datasets: [
                        { label: '남성', data: Array(21).fill(0), backgroundColor: 'rgba(54, 162, 235, 0.8)' },
                        { label: '여성', data: Array(21).fill(0), backgroundColor: 'rgba(255, 99, 132, 0.8)' }
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
                            min: -15, /* 그래프 양옆 최대폭 고정 (흔들림 방지) */
                            max: 15
                        },
                        y: { 
                            stacked: true,
                            reverse: true /* [핵심 수정] 0세가 바닥으로 가도록 위아래 순서 뒤집기 */
                        }
                    },
                    plugins: {
                        tooltip: { callbacks: { label: ctx => `${ctx.dataset.label}: ${Math.abs(ctx.raw).toFixed(2)}%` } }
                    },
                    animation: { duration: 800 }
                }
            });
        }

        function calculatePyramid(tfr, lifespan) {
            const ages = Array.from({length: 21}, (_, i) => i * 5 + 2.5);
            const r = Math.pow(Math.max(0.1, tfr) / 2.05, 1.0 / 6.0);
            const baseSize = ages.map((_, i) => Math.pow(r, -i));
            const survival = ages.map(age => 1.0 / (1.0 + Math.exp((age - lifespan) / 4.0)));
            
            const weights = baseSize.map((base, i) => base * survival[i]);
            const total = weights.reduce((a, b) => a + b, 0);
            
            return weights.map(w => (w / total) * 100.0);
        }

        onSnapshot(query(surveyCol), (snapshot) => {
            let totalChildren = 0;
            let totalMarriage = 0;
            let totalLifespan = 0;
            const count = snapshot.size;

            document.getElementById('status-msg').innerText = "🟢 실시간 연결 중 (데이터베이스 정상)";

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
                const maleData = percentages.map(p => -(p * 0.49).toFixed(2));
                const femaleData = percentages.map(p => (p * 0.51).toFixed(2));

                pyramidChart.data.datasets[0].data = maleData;
                pyramidChart.data.datasets[1].data = femaleData;
                pyramidChart.update();
            } else {
                document.getElementById('stat-count').innerText = `0명`;
                document.getElementById('stat-children').innerText = `0.00명`;
                document.getElementById('stat-marriage').innerText = `0%`;
                document.getElementById('stat-lifespan').innerText = `0세`;
                document.getElementById('stat-tfr').innerText = `0.00`;
                
                pyramidChart.data.datasets[0].data = Array(21).fill(0);
                pyramidChart.data.datasets[1].data = Array(21).fill(0);
                pyramidChart.update();
            }
        });

        window.clearData = async function() {
            if(confirm("모든 학생의 응답 데이터를 삭제하시겠습니까? (복구 불가)")) {
                try {
                    const qs = await getDocs(surveyCol);
                    qs.forEach(async (d) => {
                        await deleteDoc(doc(db, "class_surveys", d.id));
                    });
                    alert("초기화 완료");
                } catch(e) {
                    alert("삭제 실패: " + e.message);
                }
            }
        }

        window.onload = initChart;
    </script>
</body>
</html>
