<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>실시간 학급 참여형 미래 인구 피라미드 시뮬레이터</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700;800&display=swap">
    <style>
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #f8fafc; }
        input[type=range]::-webkit-slider-thumb { box-shadow: 0 2px 8px rgba(79, 70, 229, 0.3); }
        .glass-panel { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); }
        .fade-in { animation: fadeIn 0.3s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col selection:bg-indigo-500 selection:text-white">

    <header class="bg-indigo-900 text-white shadow-md sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 py-3 sm:px-6 lg:px-8 flex flex-col sm:flex-row justify-between items-center gap-3">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-indigo-600 flex items-center justify-center text-amber-300 font-extrabold text-xl shadow-inner">
                    <i class="fa-solid fa-users-line"></i>
                </div>
                <div>
                    <h1 class="text-lg sm:text-xl font-extrabold tracking-tight">실시간 미래 인구 피라미드</h1>
                    <p class="text-indigo-200 text-xs">학급 전체 설문 실시간 연동 시뮬레이터</p>
                </div>
            </div>
            <div class="flex items-center gap-1.5 bg-indigo-950/80 p-1 rounded-xl border border-indigo-700/60 text-xs font-semibold">
                <button id="tabDashboard" onclick="switchView('dashboard')" class="px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 bg-indigo-600 text-white shadow-sm">
                    <i class="fa-solid fa-chart-line"></i><span>실시간 대시보드</span>
                </button>
                <button id="tabStudent" onclick="switchView('student')" class="px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 text-indigo-200 hover:text-white">
                    <i class="fa-solid fa-mobile-screen-button"></i><span>학생 설문 제출</span>
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-grow w-full">
        <div class="mb-5 flex items-center justify-between text-xs px-4 py-2.5 bg-white rounded-xl shadow-sm border border-slate-200">
            <div class="flex items-center gap-2">
                <span id="syncDot" class="w-2.5 h-2.5 rounded-full bg-slate-300"></span>
                <span id="syncStatus" class="font-bold text-slate-500">클라우드 연결 대기 중...</span>
            </div>
            <div class="font-bold text-indigo-700 bg-indigo-50 px-3 py-1.5 rounded-full border border-indigo-100 flex items-center gap-1.5">
                <i class="fa-solid fa-user-check text-indigo-500"></i>
                <span>응답 학생: <strong id="studentCountText" class="text-indigo-900 text-sm">0</strong>명</span>
            </div>
        </div>

        <div id="dashboardView" class="space-y-6 fade-in">
            <!-- Metrics Cards -->
            <div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                    <span class="text-xs text-slate-500 font-bold flex items-center gap-1.5 mb-2"><i class="fa-solid fa-baby text-rose-500"></i> 평균 희망 자녀 수</span>
                    <div class="flex items-baseline justify-between"><span id="dashAvgChildren" class="text-3xl font-black text-slate-800">0.0</span><span class="text-xs text-slate-400 font-medium">명 / 인</span></div>
                </div>
                <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                    <span class="text-xs text-slate-500 font-bold flex items-center gap-1.5 mb-2"><i class="fa-solid fa-ring text-amber-500"></i> 평균 결혼 의향률</span>
                    <div class="flex items-baseline justify-between"><span id="dashMarriageRate" class="text-3xl font-black text-slate-800">0</span><span class="text-xs text-slate-400 font-medium">%</span></div>
                </div>
                <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                    <span class="text-xs text-slate-500 font-bold flex items-center gap-1.5 mb-2"><i class="fa-solid fa-heart-pulse text-emerald-500"></i> 평균 목표 수명</span>
                    <div class="flex items-baseline justify-between"><span id="dashAvgLifespan" class="text-3xl font-black text-slate-800">0</span><span class="text-xs text-slate-400 font-medium">세</span></div>
                </div>
                <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                    <span class="text-xs text-slate-500 font-bold flex items-center gap-1.5 mb-2"><i class="fa-solid fa-calculator text-indigo-500"></i> 추정 합계출산율(TFR)</span>
                    <div class="flex items-baseline justify-between"><span id="dashTFR" class="text-3xl font-black text-indigo-600">0.00</span><span id="dashSocietyTag" class="text-[10px] px-2 py-0.5 rounded-md font-bold bg-slate-100 text-slate-500">대기중</span></div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                <!-- Main Chart -->
                <div class="lg:col-span-8 bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 border-b border-slate-100 pb-3 mb-4">
                        <div>
                            <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2"><i class="fa-solid fa-chart-bar text-indigo-600"></i> 미래 인구 피라미드 시각화</h2>
                        </div>
                        <div class="flex items-center gap-1 bg-slate-100 p-1 rounded-lg text-xs font-medium">
                            <button onclick="setBenchmark('none')" id="bmNone" class="px-2.5 py-1 rounded-md bg-white shadow-sm font-bold text-indigo-700">비교 없음</button>
                            <button onclick="setBenchmark('1970')" id="bm1970" class="px-2.5 py-1 rounded-md text-slate-600 hover:text-slate-900">1970년</button>
                            <button onclick="setBenchmark('2024')" id="bm2024" class="px-2.5 py-1 rounded-md text-slate-600 hover:text-slate-900">2024년</button>
                        </div>
                    </div>
                    <div class="relative w-full h-[500px]">
                        <canvas id="livePyramidCanvas"></canvas>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-100 flex justify-center items-center text-xs text-slate-500 gap-4">
                        <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-sky-500 rounded-sm"></span>남성</span>
                        <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-rose-400 rounded-sm"></span>여성</span>
                        <span id="bmLegend" class="hidden items-center gap-1.5 text-amber-600 font-semibold"><span class="w-3 h-0.5 bg-amber-500 border-2 border-dashed border-amber-600"></span>비교 데이터</span>
                    </div>
                </div>

                <div class="lg:col-span-4 space-y-6">
                    <!-- QR Share -->
                    <div class="bg-gradient-to-br from-indigo-800 to-slate-900 text-white rounded-2xl p-6 shadow-sm border border-indigo-700 text-center">
                        <span class="text-xs font-bold text-indigo-300 uppercase tracking-wider mb-2 flex justify-center items-center gap-1.5"><i class="fa-solid fa-qrcode"></i> 학생 참여 QR 코드</span>
                        <h3 class="text-base font-bold mb-4">스마트폰으로 스캔하여 설문 참여</h3>
                        <div class="bg-white p-3 rounded-xl shadow-lg inline-block mb-4"><div id="qrcode"></div></div>
                        <div class="flex gap-2">
                            <button onclick="copyShareLink()" class="flex-1 bg-indigo-600 hover:bg-indigo-500 text-white py-2.5 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1.5"><i class="fa-regular fa-copy"></i> 주소 복사</button>
                        </div>
                    </div>
                    <!-- Management -->
                    <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm space-y-3">
                        <h4 class="text-sm font-bold text-slate-800 flex items-center gap-1.5 border-b border-slate-100 pb-2"><i class="fa-solid fa-gears text-indigo-600"></i> 학급 데이터 관리 (교사용)</h4>
                        <button onclick="executeResetData()" class="w-full py-2.5 bg-rose-50 hover:bg-rose-100 text-rose-700 border border-rose-200 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1.5">
                            <i class="fa-solid fa-trash-can"></i> 전체 응답 초기화
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="studentView" class="hidden max-w-xl mx-auto space-y-6 fade-in">
            <div class="bg-white rounded-2xl p-6 sm:p-8 border border-slate-200 shadow-lg">
                <div class="text-center border-b border-slate-100 pb-5 mb-6">
                    <span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-bold bg-indigo-50 text-indigo-700 mb-3 border border-indigo-100"><i class="fa-solid fa-mobile-screen"></i> 학생 제출용</span>
                    <h2 class="text-2xl font-extrabold text-slate-800">미래 인구 예측 설문</h2>
                    <p class="text-sm text-slate-500 mt-2">여러분의 응답이 학급 전체의 미래 인구 피라미드를 만듭니다.</p>
                </div>

                <form id="surveyForm" onsubmit="handleStudentSubmit(event)" class="space-y-6">
                    <div>
                        <label class="block text-sm font-bold text-slate-700 mb-2">이름 또는 닉네임 (선택)</label>
                        <input type="text" id="studentName" placeholder="예: 1학년 3반 홍길동" class="w-full px-4 py-3 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 text-sm font-medium transition">
                    </div>

                    <div class="bg-slate-50 p-5 rounded-xl border border-slate-200">
                        <div class="flex justify-between items-center mb-4">
                            <label class="text-sm font-bold text-slate-800">1. 희망하는 자녀 수</label>
                            <span class="text-indigo-600 font-extrabold text-lg"><span id="valChildren">1</span> 명</span>
                        </div>
                        <input type="range" id="inputChildren" min="0" max="5" step="1" value="1" oninput="document.getElementById('valChildren').textContent = this.value" class="w-full h-2.5 bg-slate-300 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                        <div class="flex justify-between text-xs text-slate-500 font-semibold mt-2 px-1"><span>0명</span><span>5명+</span></div>
                    </div>

                    <div class="bg-slate-50 p-5 rounded-xl border border-slate-200">
                        <label class="block text-sm font-bold text-slate-800 mb-4">2. 결혼 의향</label>
                        <div class="grid grid-cols-2 gap-4">
                            <label class="cursor-pointer">
                                <input type="radio" name="inputMarriage" value="yes" checked class="peer sr-only">
                                <div class="p-4 text-center rounded-xl border-2 border-slate-200 peer-checked:border-indigo-600 peer-checked:bg-indigo-50 peer-checked:text-indigo-900 font-bold text-sm transition"><div class="text-2xl mb-1">💍</div>결혼 의향 있음</div>
                            </label>
                            <label class="cursor-pointer">
                                <input type="radio" name="inputMarriage" value="no" class="peer sr-only">
                                <div class="p-4 text-center rounded-xl border-2 border-slate-200 peer-checked:border-rose-500 peer-checked:bg-rose-50 peer-checked:text-rose-900 font-bold text-sm transition"><div class="text-2xl mb-1">🙅‍♂️</div>결혼 의향 없음 (비혼)</div>
                            </label>
                        </div>
                    </div>

                    <div class="bg-slate-50 p-5 rounded-xl border border-slate-200">
                        <div class="flex justify-between items-center mb-4">
                            <label class="text-sm font-bold text-slate-800">3. 목표 기대 수명</label>
                            <span class="text-indigo-600 font-extrabold text-lg"><span id="valLifespan">85</span> 세</span>
                        </div>
                        <input type="range" id="inputLifespan" min="50" max="110" step="1" value="85" oninput="document.getElementById('valLifespan').textContent = this.value" class="w-full h-2.5 bg-slate-300 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                        <div class="flex justify-between text-xs text-slate-500 font-semibold mt-2 px-1"><span>50세</span><span>110세</span></div>
                    </div>

                    <button type="submit" class="w-full py-4 bg-indigo-600 hover:bg-indigo-700 text-white font-extrabold rounded-xl shadow-md transition text-base">설문 응답 제출하기 <i class="fa-solid fa-paper-plane ml-1"></i></button>
                </form>

                <div id="submittedBox" class="hidden bg-emerald-50 border border-emerald-200 rounded-2xl p-8 text-center space-y-4">
                    <div class="w-16 h-16 bg-emerald-500 text-white rounded-full flex items-center justify-center text-3xl mx-auto shadow-md mb-2"><i class="fa-solid fa-check"></i></div>
                    <h3 class="text-xl font-bold text-emerald-900">제출 완료!</h3>
                    <p class="text-sm text-emerald-700">선생님 화면의 인구 피라미드에 즉시 반영되었습니다.</p>
                    <button onclick="editMySubmission()" class="mt-4 px-5 py-2.5 bg-white text-emerald-800 border border-emerald-300 rounded-xl text-sm font-bold hover:bg-emerald-100 transition shadow-sm">내 응답 다시 수정하기</button>
                </div>
            </div>
        </div>
    </main>

    <div id="toast" class="fixed bottom-5 right-5 z-50 hidden fade-in">
        <div id="toastBody" class="px-5 py-3.5 rounded-xl shadow-2xl text-sm font-bold text-white flex items-center gap-2">
            <i id="toastIcon" class="fa-solid fa-circle-info"></i><span id="toastMsg">알림</span>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, deleteDoc, onSnapshot, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // 1. Mandatory Environment Variables setup
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'demo-pyramid-app';
        let firebaseConfig = null;
        try {
            if (typeof __firebase_config !== 'undefined') {
                firebaseConfig = JSON.parse(__firebase_config);
            }
        } catch (e) {
            console.error("Firebase config parse error", e);
        }

        // Global State Variables
        let app, auth, db;
        let currentUser = null;
        let activeResponses = [];
        let chartInstance = null;
        let currentBenchmark = 'none';

        // Local Device ID for tracking student's own submission
        let myDeviceKey = localStorage.getItem('pyramid_device_key');
        if (!myDeviceKey) {
            myDeviceKey = 'user_' + crypto.randomUUID();
            localStorage.setItem('pyramid_device_key', myDeviceKey);
        }

        const AGE_LABELS = ['100세+', '95-99세', '90-94세', '85-89세', '80-84세', '75-79세', '70-74세', '65-69세', '60-64세', '55-59세', '50-54세', '45-49세', '40-44세', '35-39세', '30-34세', '25-29세', '20-24세', '15-19세', '10-14세', '5-9세', '0-4세'];
        
        // Historical benchmark data (percentages)
        const HISTORICAL = {
            '1970': { male: [-0.1, -0.2, -0.4, -0.8, -1.2, -1.8, -2.5, -3.2, -3.8, -4.2, -4.5, -4.8, -5.0, -5.2, -5.5, -6.0, -6.5, -7.0, -7.5, -8.0, -8.5], female: [0.2, 0.4, 0.6, 1.0, 1.5, 2.0, 2.7, 3.4, 4.0, 4.4, 4.7, 5.0, 5.2, 5.4, 5.7, 6.2, 6.7, 7.2, 7.7, 8.2, 8.7] },
            '2024': { male: [-0.6, -1.1, -1.8, -2.6, -3.4, -3.9, -4.1, -4.0, -3.9, -4.0, -4.2, -4.0, -3.8, -3.5, -3.4, -3.2, -2.8, -2.2, -1.6, -1.2, -0.9], female: [1.2, 1.8, 2.4, 3.1, 3.7, 4.0, 4.1, 3.9, 3.8, 3.9, 4.1, 3.9, 3.7, 3.4, 3.3, 3.1, 2.7, 2.1, 1.5, 1.1, 0.8] }
        };

        async function initializeAppSystem() {
            if (!firebaseConfig) {
                updateSyncState(false, "설정 오류: Firebase 환경 변수 누락");
                return;
            }

            try {
                app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);

                // RULE 3: Auth before Queries
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    const cred = await signInWithCustomToken(auth, __initial_auth_token);
                    currentUser = cred.user;
                } else {
                    const cred = await signInAnonymously(auth);
                    currentUser = cred.user;
                }

                updateSyncState(true, "클라우드 실시간 연동 중");
                listenToData();

            } catch (err) {
                console.error("Firebase init/auth failed:", err);
                updateSyncState(false, "데이터베이스 접근 권한 없음 (인증 실패)");
            }
        }

        function updateSyncState(isOnline, text) {
            const dot = document.getElementById('syncDot');
            const status = document.getElementById('syncStatus');
            status.textContent = text;
            if (isOnline) {
                dot.className = "w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse";
                status.className = "font-bold text-emerald-600";
            } else {
                dot.className = "w-2.5 h-2.5 rounded-full bg-rose-500";
                status.className = "font-bold text-rose-600";
            }
        }

        function listenToData() {
            if (!currentUser) return;

            // RULE 1: Strict public path usage, RULE 2: No complex queries
            const surveyCol = collection(db, 'artifacts', appId, 'public', 'data', 'survey_responses');

            onSnapshot(surveyCol, (snapshot) => {
                activeResponses = [];
                let mySubmissionFound = false;

                snapshot.forEach((docSnap) => {
                    const data = docSnap.data();
                    activeResponses.push(data);
                    if (data.deviceId === myDeviceKey) mySubmissionFound = true;
                });

                document.getElementById('studentCountText').textContent = activeResponses.length;
                
                // Toggle view if user submitted
                if (mySubmissionFound) {
                    document.getElementById('surveyForm').classList.add('hidden');
                    document.getElementById('submittedBox').classList.remove('hidden');
                }

                recalculatePyramid();

            }, (error) => {
                console.error("Snapshot error:", error);
                updateSyncState(false, "데이터 읽기 권한 오류");
            });
        }

        function recalculatePyramid() {
            if (activeResponses.length === 0) {
                renderDashboard(0, 0, 0, 0, new Array(21).fill(0), new Array(21).fill(0));
                return;
            }

            let sChild = 0, sMarr = 0, sLife = 0;
            activeResponses.forEach(r => {
                sChild += r.children || 0;
                sMarr += r.marriage ? 100 : 0;
                sLife += r.lifespan || 80;
            });

            const count = activeResponses.length;
            const avgChild = sChild / count;
            const avgMarr = sMarr / count;
            const avgLife = sLife / count;
            
            // TFR (Total Fertility Rate) estimation
            const tfr = Math.max(0.01, avgChild * (avgMarr / 100));
            const r = Math.pow(tfr / 2.05, 1 / 6); // Cohort growth ratio

            let weights = [];
            for (let i = 0; i <= 20; i++) {
                let age = i * 5 + 2.5;
                let base = Math.pow(r, -i);
                let survival = 1.0 / (1.0 + Math.exp((age - avgLife) / 4.0));
                weights.push(base * survival);
            }

            const totalW = weights.reduce((a, b) => a + b, 0);
            let maleData = [], femaleData = [];

            // Top to bottom approach (100+ -> 0-4)
            for (let k = 0; k < 21; k++) {
                let i = 20 - k; 
                let age = i * 5 + 2.5;
                let pct = (weights[i] / totalW) * 100;

                let fRatio = age >= 75 ? 0.53 : 0.502;
                maleData.push(-parseFloat((pct * (1 - fRatio)).toFixed(2)));
                femaleData.push(parseFloat((pct * fRatio).toFixed(2)));
            }

            renderDashboard(avgChild.toFixed(1), Math.round(avgMarr), Math.round(avgLife), tfr.toFixed(2), maleData, femaleData);
        }

        function renderDashboard(c, m, l, t, maleD, femaleD) {
            document.getElementById('dashAvgChildren').textContent = c;
            document.getElementById('dashMarriageRate').textContent = m;
            document.getElementById('dashAvgLifespan').textContent = l;
            document.getElementById('dashTFR').textContent = t;

            const tag = document.getElementById('dashSocietyTag');
            if (t == 0) { tag.textContent = '데이터 없음'; tag.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-slate-100 text-slate-500'; }
            else if (t < 1.3) { tag.textContent = '초저출산'; tag.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-rose-100 text-rose-700'; }
            else if (t < 2.05) { tag.textContent = '인구 감소'; tag.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-amber-100 text-amber-700'; }
            else { tag.textContent = '대물림 유지'; tag.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-emerald-100 text-emerald-700'; }

            updateChart(maleD, femaleD);
        }

        function updateChart(maleD, femaleD) {
            const ctx = document.getElementById('livePyramidCanvas').getContext('2d');
            let datasets = [
                { label: '남성', data: maleD, backgroundColor: 'rgba(14, 165, 233, 0.85)', borderColor: '#0284c7', borderWidth: 1, barPercentage: 0.95 },
                { label: '여성', data: femaleD, backgroundColor: 'rgba(251, 113, 133, 0.85)', borderColor: '#e11d48', borderWidth: 1, barPercentage: 0.95 }
            ];

            if (currentBenchmark !== 'none' && HISTORICAL[currentBenchmark]) {
                const bm = HISTORICAL[currentBenchmark];
                datasets.push({ label: `${currentBenchmark}남성`, data: bm.male, type: 'line', borderColor: '#d97706', borderWidth: 2, borderDash: [5, 5], fill: false, pointRadius: 0 });
                datasets.push({ label: `${currentBenchmark}여성`, data: bm.female, type: 'line', borderColor: '#d97706', borderWidth: 2, borderDash: [5, 5], fill: false, pointRadius: 0 });
            }

            if (!chartInstance) {
                chartInstance = new Chart(ctx, {
                    type: 'bar',
                    data: { labels: AGE_LABELS, datasets: datasets },
                    options: {
                        indexAxis: 'y',
                        responsive: true,
                        maintainAspectRatio: false,
                        animation: { duration: 400 },
                        scales: {
                            x: { stacked: true, ticks: { callback: (val) => Math.abs(val) + '%' }, title: { display: true, text: '전체 인구 대비 비율 (%)', font:{weight:'bold'} } },
                            y: { stacked: true, title: { display: true, text: '연령대 (5세 단위)', font:{weight:'bold'} } }
                        },
                        plugins: {
                            legend: { display: false },
                            tooltip: { callbacks: { label: (ctx) => `${ctx.dataset.label.replace(/\d+/g,'')}: ${Math.abs(ctx.raw)}%` } }
                        }
                    }
                });
            } else {
                chartInstance.data.datasets = datasets;
                chartInstance.update();
            }
        }

        window.handleStudentSubmit = async function(e) {
            e.preventDefault();
            if (!currentUser) { showToast('접속 권한이 확인되지 않았습니다.', 'error'); return; }

            const btn = e.target.querySelector('button[type="submit"]');
            btn.disabled = true; btn.innerHTML = '<i class="fa-solid fa-spinner fa-spin"></i> 전송 중...';

            const payload = {
                deviceId: myDeviceKey,
                name: document.getElementById('studentName').value.trim() || '익명',
                children: parseInt(document.getElementById('inputChildren').value),
                marriage: document.querySelector('input[name="inputMarriage"]:checked').value === 'yes',
                lifespan: parseInt(document.getElementById('inputLifespan').value),
                timestamp: new Date().toISOString()
            };

            try {
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'survey_responses', myDeviceKey);
                await setDoc(docRef, payload);
                showToast('설문 제출이 완료되었습니다!', 'success');
            } catch (err) {
                console.error(err);
                showToast('제출 중 오류가 발생했습니다.', 'error');
                btn.disabled = false; btn.innerHTML = '설문 응답 제출하기 <i class="fa-solid fa-paper-plane ml-1"></i>';
            }
        };

        window.executeResetData = async function() {
            if (!currentUser) return;
            showToast('전체 응답을 초기화하는 중...', 'info');
            try {
                const colRef = collection(db, 'artifacts', appId, 'public', 'data', 'survey_responses');
                const snap = await getDocs(colRef);
                const deletions = [];
                snap.forEach(d => { deletions.push(deleteDoc(d.ref)); });
                await Promise.all(deletions);
                showToast('모든 데이터가 초기화되었습니다.', 'success');
            } catch (e) {
                console.error(e);
                showToast('초기화 실패', 'error');
            }
        };

        // UI Helpers
        window.switchView = function(view) {
            document.getElementById('dashboardView').classList.toggle('hidden', view !== 'dashboard');
            document.getElementById('studentView').classList.toggle('hidden', view !== 'student');
            
            if(view === 'dashboard') {
                document.getElementById('tabDashboard').className = 'px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 bg-indigo-600 text-white shadow-sm';
                document.getElementById('tabStudent').className = 'px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 text-indigo-200 hover:text-white';
            } else {
                document.getElementById('tabDashboard').className = 'px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 text-indigo-200 hover:text-white';
                document.getElementById('tabStudent').className = 'px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 bg-indigo-600 text-white shadow-sm';
            }
        };

        window.setBenchmark = function(val) {
            currentBenchmark = val;
            ['none', '1970', '2024'].forEach(v => {
                const b = document.getElementById('bm' + v.charAt(0).toUpperCase() + v.slice(1));
                if (b) b.className = (v === val) ? 'px-2.5 py-1 rounded-md bg-white shadow-sm font-bold text-indigo-700' : 'px-2.5 py-1 rounded-md text-slate-600 hover:text-slate-900';
            });
            document.getElementById('bmLegend').style.display = (val === 'none') ? 'none' : 'flex';
            recalculatePyramid();
        };

        window.editMySubmission = function() {
            document.getElementById('submittedBox').classList.add('hidden');
            document.getElementById('surveyForm').classList.remove('hidden');
            const btn = document.querySelector('#surveyForm button[type="submit"]');
            btn.disabled = false; btn.innerHTML = '설문 응답 제출하기 <i class="fa-solid fa-paper-plane ml-1"></i>';
        };

        window.copyShareLink = function() {
            const url = window.location.href;
            const input = document.createElement('input');
            input.value = url;
            document.body.appendChild(input);
            input.select();
            try { document.execCommand('copy'); showToast('주소가 복사되었습니다.', 'success'); } 
            catch (e) { showToast('복사 실패', 'error'); }
            document.body.removeChild(input);
        };

        function showToast(msg, type='info') {
            const t = document.getElementById('toast');
            const tb = document.getElementById('toastBody');
            const ti = document.getElementById('toastIcon');
            document.getElementById('toastMsg').textContent = msg;
            
            if (type === 'success') { tb.className = "px-5 py-3.5 rounded-xl shadow-2xl text-sm font-bold text-white flex items-center gap-2 bg-emerald-600"; ti.className = "fa-solid fa-circle-check"; }
            else if (type === 'error') { tb.className = "px-5 py-3.5 rounded-xl shadow-2xl text-sm font-bold text-white flex items-center gap-2 bg-rose-600"; ti.className = "fa-solid fa-circle-exclamation"; }
            else { tb.className = "px-5 py-3.5 rounded-xl shadow-2xl text-sm font-bold text-white flex items-center gap-2 bg-slate-800"; ti.className = "fa-solid fa-circle-info"; }

            t.classList.remove('hidden');
            setTimeout(() => t.classList.add('hidden'), 3000);
        }

        // Initialize App on Load
        window.addEventListener('DOMContentLoaded', () => {
            new QRCode(document.getElementById("qrcode"), { text: window.location.href, width: 140, height: 140, colorDark: "#312e81", colorLight: "#ffffff" });
            recalculatePyramid(); // Draw empty
            initializeAppSystem(); // Boot Firebase
        });

    </script>
</body>
</html>
