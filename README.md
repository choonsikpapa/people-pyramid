    .header { background: linear-gradient(135deg, #312e81, #4f46e5); color: white; padding: 20px 15px; text-align: center; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
    .header h1 { margin: 0 0 12px 0; font-size: 20px; word-break: keep-all; line-height: 1.3; }
    .nav-buttons { display: flex; justify-content: center; gap: 8px; }
    .nav-btn { background: rgba(255,255,255,0.15); border: 1px solid rgba(255,255,255,0.3); color: white; padding: 10px 14px; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 14px; transition: all 0.2s; flex: 1; max-width: 180px; }
    .nav-btn:hover { background: rgba(255,255,255,0.25); }
    .nav-btn.active { background: white; color: var(--primary); box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
    
    .container { max-width: 1100px; margin: 15px auto; padding: 0 12px; }
    .view-section { display: none; background: var(--card); padding: 20px 16px; border-radius: 16px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
    .view-section.active { display: block; animation: fadeIn 0.3s ease-out; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    
    .mode-live { background: #d1fae5; color: #059669; padding: 10px; text-align: center; font-weight: bold; border-radius: 8px; margin-bottom: 15px; font-size: 14px; border: 1px solid #34d399; word-break: keep-all; }
    .mode-manual { background: #fef3c7; color: #d97706; padding: 10px; text-align: center; font-weight: bold; border-radius: 8px; margin-bottom: 15px; font-size: 14px; border: 1px solid #fbbf24; word-break: keep-all; }
    
    .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(130px, 1fr)); gap: 10px; margin-bottom: 20px; }
    .stat-card { background: #f1f5f9; padding: 12px 8px; border-radius: 12px; text-align: center; border: 1px solid #e2e8f0; transition: all 0.3s; }
    .stat-card .stat-label { font-size: 12px; color: #64748b; font-weight: 600; word-break: keep-all; }
    .stat-value { font-size: 20px; font-weight: 900; color: var(--primary); margin-top: 4px; }
    .stat-card.manual-override { background: #fff7ed; border-color: #fdba74; }
    .stat-card.manual-override .stat-value { color: #ea580c; }
    
    .manual-controls-panel { background: #fff7ed; border: 2px dashed #fdba74; padding: 15px; border-radius: 12px; margin-bottom: 20px; }
    .manual-controls-panel h3 { margin: 0 0 12px 0; color: #c2410c; font-size: 15px; text-align: center; word-break: keep-all; }
    .manual-inputs { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; margin-bottom: 12px; }
    .manual-inputs > div { display: flex; flex-direction: column; gap: 4px; width: 100%; max-width: 120px; }
    .manual-inputs label { font-size: 11px; font-weight: bold; color: #9a3412; text-align: center; }
    .manual-inputs input { padding: 8px; border: 1px solid #fdba74; border-radius: 6px; text-align: center; font-weight: bold; outline: none; font-size: 14px; }
    .manual-inputs input:focus { border-color: #ea580c; box-shadow: 0 0 0 2px rgba(234, 88, 12, 0.2); }
    .manual-actions { display: flex; justify-content: center; gap: 8px; flex-wrap: wrap; }
    .btn-apply { background: #ea580c; color: white; border: none; padding: 10px 14px; border-radius: 8px; font-weight: bold; cursor: pointer; transition: 0.2s; font-size: 13px; flex: 1; min-width: 120px; }
    .btn-apply:hover { background: #c2410c; }
    .btn-reset { background: #10b981; color: white; border: none; padding: 10px 14px; border-radius: 8px; font-weight: bold; cursor: pointer; transition: 0.2s; font-size: 13px; flex: 1; min-width: 120px; }
    .btn-reset:hover { background: #059669; }

    .overlay-controls { display: flex; align-items: center; justify-content: center; gap: 10px; margin: 15px 0; padding: 12px; background: #f8fafc; border-radius: 10px; border: 1px solid #e2e8f0; flex-wrap: wrap; }
    .overlay-controls span { font-weight: bold; color: #475569; font-size: 13px; width: 100%; text-align: center; }
    @media(min-width: 640px) {
        .overlay-controls span { width: auto; text-align: left; }
    }
    .radio-group { display: flex; gap: 6px; flex-wrap: wrap; justify-content: center; width: 100%; }
    .radio-label { cursor: pointer; display: flex; align-items: center; gap: 4px; font-size: 12px; font-weight: 600; padding: 8px 10px; background: white; border: 1px solid #cbd5e1; border-radius: 6px; transition: all 0.2s; flex: 1; min-width: 75px; justify-content: center; }
    .radio-label:hover { background: #f1f5f9; }
    .radio-label input[type="radio"] { accent-color: var(--primary); margin: 0; }
    .radio-label.selected { border-color: var(--primary); background: #e0e7ff; color: var(--primary); }

    .chart-container { position: relative; height: 450px; width: 100%; margin-top: 10px; }
    @media(min-width: 768px) {
        .chart-container { height: 550px; }
    }
    
    .form-group { margin-bottom: 20px; background: #f8fafc; padding: 16px; border-radius: 12px; border: 1px solid #e2e8f0; }
    .form-group label { display: block; font-weight: bold; margin-bottom: 8px; font-size: 15px; color: #334155; line-height: 1.4; word-break: keep-all; }
    .form-group input { width: 100%; padding: 12px 14px; border: 2px solid #cbd5e1; border-radius: 8px; box-sizing: border-box; font-size: 16px; outline: none; transition: border-color 0.2s; }
    .form-group input:focus { border-color: var(--primary); }
    .submit-btn { width: 100%; background: var(--primary); color: white; border: none; padding: 16px; border-radius: 12px; font-size: 17px; font-weight: bold; cursor: pointer; box-shadow: 0 4px 6px rgba(79, 70, 229, 0.25); transition: background 0.2s; }
    .submit-btn:hover { background: #4338ca; }
    .submit-btn:disabled { background: #94a3b8; cursor: not-allowed; }
    
    #submit-result { text-align: center; margin-top: 15px; font-weight: bold; color: #059669; font-size: 15px; word-break: keep-all; line-height: 1.4; }
</style>


<div class="header">
    <h1>📊 실시간 학급 인구 피라미드 시뮬레이터</h1>
    <div class="nav-buttons">
        <button class="nav-btn active" onclick="switchView('dashboard', this)">📈 교사 대시보드</button>
        <button class="nav-btn" onclick="switchView('submit', this)">📱 학생 설문 제출</button>
    </div>
</div>

<div class="container">
    <div id="view-dashboard" class="view-section active">
        <div id="mode-badge" class="mode-live">🟢 실시간 학생 데이터 연동 중 (Firebase)</div>
        
        <div class="stats-grid" id="stats-container">
            <div class="stat-card">
                <div class="stat-label">참여 학생 수</div>
                <div class="stat-value" id="stat-count">0명</div>
            </div>
            <div class="stat-card">
                <div class="stat-label">평균 희망 자녀 수</div>
                <div class="stat-value" id="stat-children">0.00명</div>
            </div>
            <div class="stat-card">
                <div class="stat-label">평균 결혼 의향률</div>
                <div class="stat-value" id="stat-marriage">0%</div>
            </div>
            <div class="stat-card">
                <div class="stat-label">평균 기대 수명</div>
                <div class="stat-value" id="stat-lifespan">0세</div>
            </div>
            <div class="stat-card" style="background:#e0e7ff; border-color:#c7d2fe;" id="tfr-card">
                <div class="stat-label" style="font-weight:bold; color:#4338ca;" id="tfr-label">추정 합계출산율(TFR)</div>
                <div class="stat-value" id="stat-tfr" style="color:#4338ca;">0.00</div>
            </div>
        </div>

        <div class="manual-controls-panel">
            <h3>🛠️ 수동 시뮬레이션 설정 (선생님 직접 입력)</h3>
            <div class="manual-inputs">
                <div>
                    <label>희망 자녀 수(명)</label>
                    <input type="number" id="man-child" step="0.1" value="1.5" min="0">
                </div>
                <div>
                    <label>결혼 의향률(%)</label>
                    <input type="number" id="man-marry" step="1" value="80" min="0" max="100">
                </div>
                <div>
                    <label>기대 수명(세)</label>
                    <input type="number" id="man-life" step="1" value="85" min="50" max="120">
                </div>
            </div>
            <div class="manual-actions">
                <button class="btn-apply" onclick="applyManualSimulation()">수동 시뮬레이션 적용</button>
                <button class="btn-reset" onclick="returnToLiveData()">학생 데이터 복귀</button>
            </div>
        </div>

        <div class="overlay-controls">
            <span>🔍 실제 시대별 인구구조 비교:</span>
            <div class="radio-group">
                <label class="radio-label selected" id="lbl-none">
                    <input type="radio" name="overlayYear" value="none" checked onchange="changeOverlay(this.value)"> 없음
                </label>
                <label class="radio-label" id="lbl-1960">
                    <input type="radio" name="overlayYear" value="1960" onchange="changeOverlay(this.value)"> 1960년
                </label>
                <label class="radio-label" id="lbl-2025">
                    <input type="radio" name="overlayYear" value="2025" onchange="changeOverlay(this.value)"> 2025년
                </label>
                <label class="radio-label" id="lbl-2060">
                    <input type="radio" name="overlayYear" value="2060" onchange="changeOverlay(this.value)"> 2060년
                </label>
            </div>
        </div>

        <div class="chart-container">
            <canvas id="pyramidChart"></canvas>
        </div>
        
        <div style="text-align: right; margin-top: 20px;">
            <button onclick="clearData()" style="background:#ef4444; color:white; border:none; padding:10px 14px; border-radius:8px; cursor:pointer; font-weight:bold; font-size:13px; transition: background 0.2s;">
                ⚠️ 전체 학생 응답 초기화
            </button>
        </div>
    </div>

    <div id="view-submit" class="view-section">
        <h2 style="text-align: center; margin-bottom: 20px; color: var(--primary); font-size: 20px;">나의 미래 계획 입력하기</h2>
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
        <div id="submit-result"></div>
    </div>
</div>

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

    // 연령대 레이블
    let ageLabels = [];
    for (let i = 20; i >= 0; i--) {
        if (i === 20) ageLabels.push('100세 이상');
        else ageLabels.push(`${i*5}~${i*5+4}세`);
    }

    // 시대별 데이터
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
    
    let mode = 'live'; 
    let liveStats = { count: 0, children: 0, marriage: 0, lifespan: 0, tfr: 0 };
    let manualStats = { children: 0, marriage: 0, lifespan: 0, tfr: 0 };

    function initChart() {
        const ctx = document.getElementById('pyramidChart').getContext('2d');
        pyramidChart = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: ageLabels,
                datasets: [
                    { label: '남성 (시뮬레이션)', data: Array(21).fill(0), backgroundColor: 'rgba(56, 189, 248, 0.85)', stack: 'Stack 0', order: 2 },
                    { label: '여성 (시뮬레이션)', data: Array(21).fill(0), backgroundColor: 'rgba(251, 113, 133, 0.85)', stack: 'Stack 0', order: 2 },
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
                        stacked: false, 
                        ticks: { callback: val => Math.abs(val).toFixed(1) + '%', font: { size: 10 } },
                        title: { display: true, text: '전체 인구 대비 비율 (%)', font: { weight:'bold', size: 12 } },
                        min: -15, max: 15
                    },
                    y: { 
                        stacked: true,
                        ticks: { font: { size: 10 } }
                    }
                },
                plugins: {
                    legend: { labels: { font: { size: 11 }, filter: function(item) { return item.text.includes('(시뮬레이션)'); } } },
                    tooltip: { callbacks: { label: ctx => `${ctx.dataset.label}: ${Math.abs(ctx.raw).toFixed(2)}%` } }
                },
                animation: { duration: 600 }
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
        let percentages = weights.map(w => (w / total) * 100.0);
        return percentages.reverse();
    }

    function renderDashboard() {
        const stats = mode === 'live' ? liveStats : manualStats;
        
        document.getElementById('stat-count').innerText = mode === 'live' ? `${stats.count}명` : `수동 모드`;
        document.getElementById('stat-children').innerText = `${stats.children.toFixed(2)}명`;
        document.getElementById('stat-marriage').innerText = `${stats.marriage.toFixed(1)}%`;
        document.getElementById('stat-lifespan').innerText = `${stats.lifespan.toFixed(1)}세`;
        document.getElementById('stat-tfr').innerText = stats.tfr.toFixed(2);

        const cards = document.querySelectorAll('.stat-card');
        const badge = document.getElementById('mode-badge');
        
        if (mode === 'manual') {
            cards.forEach(card => card.classList.add('manual-override'));
            document.getElementById('tfr-card').style.backgroundColor = '#ffedd5';
            document.getElementById('tfr-card').style.borderColor = '#fdba74';
            document.getElementById('tfr-label').style.color = '#c2410c';
            document.getElementById('stat-tfr').style.color = '#ea580c';
            
            badge.className = 'mode-manual';
            badge.innerText = '🟠 수동 시뮬레이션 모드 (선생님 입력 값 적용 중)';
        } else {
            cards.forEach(card => card.classList.remove('manual-override'));
            document.getElementById('tfr-card').style.backgroundColor = '#e0e7ff';
            document.getElementById('tfr-card').style.borderColor = '#c7d2fe';
            document.getElementById('tfr-label').style.color = '#4338ca';
            document.getElementById('stat-tfr').style.color = '#4338ca';
            
            badge.className = 'mode-live';
            badge.innerText = '🟢 실시간 학생 데이터 연동 중';
        }

        if (stats.count > 0 || mode === 'manual') {
            const percentages = calculatePyramid(stats.tfr, stats.lifespan);
            const maleData = percentages.map(p => -(p * 0.49).toFixed(2));
            const femaleData = percentages.map(p => (p * 0.51).toFixed(2));
            
            if(pyramidChart) {
                pyramidChart.data.datasets[0].data = maleData;
                pyramidChart.data.datasets[1].data = femaleData;
                pyramidChart.update();
            }
        } else {
            if(pyramidChart) {
                pyramidChart.data.datasets[0].data = Array(21).fill(0);
                pyramidChart.data.datasets[1].data = Array(21).fill(0);
                pyramidChart.update();
            }
        }
    }

    window.changeOverlay = function(year) {
        currentOverlay = year;
        document.querySelectorAll('.radio-label').forEach(el => el.classList.remove('selected'));
        document.getElementById('lbl-' + year).classList.add('selected');

        if (year === 'none') {
            pyramidChart.data.datasets[2].data = [];
            pyramidChart.data.datasets[3].data = [];
        } else {
            pyramidChart.data.datasets[2].data = historicalData[year].male;
            pyramidChart.data.datasets[3].data = historicalData[year].female;
        }
        pyramidChart.update();
    }

    window.applyManualSimulation = function() {
        const c = parseFloat(document.getElementById('man-child').value) || 0;
        const m = parseFloat(document.getElementById('man-marry').value) || 0;
        const l = parseFloat(document.getElementById('man-life').value) || 0;
        
        manualStats = {
            children: c,
            marriage: m,
            lifespan: l,
            tfr: c * (m / 100)
        };
        
        mode = 'manual';
        renderDashboard();
    }

    window.returnToLiveData = function() {
        mode = 'live';
        renderDashboard();
    }

    onSnapshot(query(surveyCol), (snapshot) => {
        let totalChildren = 0; let totalMarriage = 0; let totalLifespan = 0;
        const count = snapshot.size;

        if (count > 0) {
            snapshot.forEach((doc) => {
                const data = doc.data();
                totalChildren += data.children || 0;
                totalMarriage += data.marriage || 0;
                totalLifespan += data.lifespan || 0;
            });

            liveStats.count = count;
            liveStats.children = totalChildren / count;
            liveStats.marriage = totalMarriage / count;
            liveStats.lifespan = totalLifespan / count;
            liveStats.tfr = liveStats.children * (liveStats.marriage / 100);
        } else {
            liveStats = { count: 0, children: 0, marriage: 0, lifespan: 0, tfr: 0 };
        }

        if (mode === 'live') {
            renderDashboard();
        }
    });

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
            .then(() => {
                document.getElementById('submit-result').innerText = "✅ 성공적으로 제출되었습니다!";
                document.getElementById('input-children').value = '';
                document.getElementById('input-marriage').value = '';
                document.getElementById('input-lifespan').value = '';
                
                btn.disabled = false; btn.innerText = "내 응답 다시 제출하기";
                setTimeout(() => { document.getElementById('submit-result').innerText = ""; }, 4000);
            })
            .catch(error => {
                console.error("제출 오류:", error);
                alert("제출에 실패했습니다: " + error.message);
                btn.disabled = false; btn.innerText = "내 응답 제출하여 피라미드 만들기";
            });
    });

    window.switchView = function(view, btnElement) {
        document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
        document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
        document.getElementById('view-' + view).classList.add('active');
        if(btnElement) btnElement.classList.add('active');
    }

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
