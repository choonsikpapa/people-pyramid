<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>실시간 학급 참여형 미래 인구 피라미드 시뮬레이터</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- QRCode.js for student link sharing -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <!-- Google Fonts -->
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700;800&display=swap">
    <style>
        body { font-family: 'Noto Sans KR', sans-serif; }
        .glass-panel {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(10px);
        }
        input[type=range]::-webkit-slider-thumb {
            box-shadow: 0 2px 8px rgba(79, 70, 229, 0.3);
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen flex flex-col justify-between selection:bg-indigo-500 selection:text-white">

    <!-- Navigation / Header -->
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

            <!-- Mode Switcher Tabs -->
            <div class="flex items-center gap-1.5 bg-indigo-950/80 p-1 rounded-xl border border-indigo-700/60 text-xs font-semibold">
                <button id="tabDashboard" onclick="switchView('dashboard')" 
                        class="px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 bg-indigo-600 text-white shadow-sm">
                    <i class="fa-solid fa-chart-line"></i>
                    <span>실시간 대시보드</span>
                </button>
                <button id="tabStudent" onclick="switchView('student')" 
                        class="px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 text-indigo-200 hover:text-white">
                    <i class="fa-solid fa-mobile-screen-button"></i>
                    <span>학생 설문 제출</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content Area -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-grow w-full">

        <!-- Connection Status & Sync Indicator Bar -->
        <div class="mb-4 flex items-center justify-between text-xs px-4 py-2 bg-white rounded-xl shadow-sm border border-slate-200">
            <div class="flex items-center gap-2">
                <span id="syncDot" class="w-2.5 h-2.5 rounded-full bg-amber-400 animate-ping"></span>
                <span id="syncStatus" class="font-semibold text-slate-600">클라우드 데이터 연결 중...</span>
            </div>
            <div id="participantBadge" class="font-bold text-indigo-700 bg-indigo-50 px-2.5 py-1 rounded-full border border-indigo-100 flex items-center gap-1">
                <i class="fa-solid fa-user-check text-indigo-500"></i>
                <span>응답 학생: <strong id="studentCountText" class="text-indigo-900">0</strong>명</span>
            </div>
        </div>

        <!-- VIEW 1: Teacher / Live Dashboard Mode -->
        <div id="dashboardView" class="space-y-6">
            
            <!-- Summary Metric Cards -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-3 sm:gap-4">
                <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <span class="text-xs text-slate-500 font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-baby text-rose-500"></i> 평균 희망 자녀 수
                    </span>
                    <div class="mt-2 flex items-baseline justify-between">
                        <span id="dashAvgChildren" class="text-2xl sm:text-3xl font-black text-slate-800">0.0</span>
                        <span class="text-xs text-slate-400 font-medium">명 / 인</span>
                    </div>
                </div>

                <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <span class="text-xs text-slate-500 font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-ring text-amber-500"></i> 평균 결혼 의향률
                    </span>
                    <div class="mt-2 flex items-baseline justify-between">
                        <span id="dashMarriageRate" class="text-2xl sm:text-3xl font-black text-slate-800">0</span>
                        <span class="text-xs text-slate-400 font-medium">%</span>
                    </div>
                </div>

                <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <span class="text-xs text-slate-500 font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-heart-pulse text-emerald-500"></i> 평균 목표 수명
                    </span>
                    <div class="mt-2 flex items-baseline justify-between">
                        <span id="dashAvgLifespan" class="text-2xl sm:text-3xl font-black text-slate-800">0</span>
                        <span class="text-xs text-slate-400 font-medium">세</span>
                    </div>
                </div>

                <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <span class="text-xs text-slate-500 font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-calculator text-indigo-500"></i> 추정 합계출산율(TFR)
                    </span>
                    <div class="mt-2 flex items-baseline justify-between">
                        <span id="dashTFR" class="text-2xl sm:text-3xl font-black text-indigo-600">0.00</span>
                        <span id="dashSocietyTag" class="text-[10px] px-2 py-0.5 rounded-md font-bold bg-slate-100 text-slate-600">계산 중</span>
                    </div>
                </div>
            </div>

            <!-- Middle Section: Chart + QR Code Sidebar -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                
                <!-- Main Population Pyramid Chart (8 cols) -->
                <div class="lg:col-span-8 bg-white rounded-2xl p-5 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <div>
                        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2 border-b border-slate-100 pb-3 mb-4">
                            <div>
                                <h2 class="text-base sm:text-lg font-bold text-slate-800 flex items-center gap-2">
                                    <i class="fa-solid fa-chart-bar text-indigo-600"></i>
                                    학급 응답 기반 미래 인구 피라미드
                                </h2>
                                <p class="text-xs text-slate-500 mt-0.5">5세 단위 연속 코호트 생존율 추계 모델</p>
                            </div>

                            <!-- Historical Benchmark Selector -->
                            <div class="flex items-center gap-1 bg-slate-100 p-1 rounded-lg text-xs font-medium">
                                <button onclick="setBenchmark('none')" id="bmNone" class="px-2.5 py-1 rounded-md bg-white shadow-sm font-bold text-indigo-700">학생 예측만</button>
                                <button onclick="setBenchmark('1970')" id="bm1970" class="px-2.5 py-1 rounded-md text-slate-600 hover:text-slate-900">1970년 비교</button>
                                <button onclick="setBenchmark('2024')" id="bm2024" class="px-2.5 py-1 rounded-md text-slate-600 hover:text-slate-900">2024년 비교</button>
                            </div>
                        </div>

                        <!-- Chart Canvas Container -->
                        <div class="relative w-full h-[480px]">
                            <canvas id="livePyramidCanvas"></canvas>
                        </div>
                    </div>

                    <div class="mt-4 pt-3 border-t border-slate-100 flex flex-wrap justify-between items-center text-xs text-slate-500 gap-2">
                        <div class="flex items-center gap-3 font-medium">
                            <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-sky-500 rounded-sm"></span>남성</span>
                            <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-rose-400 rounded-sm"></span>여성</span>
                            <span id="bmLegend" class="hidden items-center gap-1.5 text-amber-600 font-semibold"><span class="w-3 h-0.5 bg-amber-500 border border-dashed border-amber-600"></span>비교 기준 피라미드</span>
                        </div>
                        <div class="text-[11px] text-slate-400">
                            * 학생 제출 시 그래프 자동 애니메이션 갱신
                        </div>
                    </div>
                </div>

                <!-- Right Side: Student QR Code & Control Panel (4 cols) -->
                <div class="lg:col-span-4 space-y-6 flex flex-col justify-between">
                    
                    <!-- QR Code Sharing Card -->
                    <div class="bg-gradient-to-br from-indigo-900 to-slate-900 text-white rounded-2xl p-5 shadow-sm border border-indigo-800 flex flex-col items-center text-center">
                        <span class="text-xs font-bold text-indigo-300 uppercase tracking-wider mb-1 flex items-center gap-1.5">
                            <i class="fa-solid fa-qrcode"></i> 학생 참여 QR 코드
                        </span>
                        <h3 class="text-sm font-bold mb-3">스마트폰 카메라로 스캔하여 응답</h3>

                        <!-- QR Code Container -->
                        <div class="bg-white p-3 rounded-xl shadow-md mb-3">
                            <div id="qrcode" class="flex justify-center items-center"></div>
                        </div>

                        <p class="text-[11px] text-indigo-200 mb-3 px-2">
                            화면을 확대하거나 아래 링크를 복사하여 학생들에게 전달하세요.
                        </p>

                        <div class="w-full flex gap-2">
                            <button onclick="copyShareLink()" class="flex-1 bg-indigo-700 hover:bg-indigo-600 text-white py-2 px-3 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1.5">
                                <i class="fa-regular fa-copy"></i>
                                <span>링크 복사</span>
                            </button>
                            <button onclick="openQRModal()" class="bg-indigo-800 hover:bg-indigo-700 text-indigo-200 py-2 px-3 rounded-xl text-xs font-bold transition flex items-center justify-center">
                                <i class="fa-solid fa-expand"></i>
                            </button>
                        </div>
                    </div>

                    <!-- Teacher Class Data Reset Card -->
                    <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm space-y-3">
                        <div class="flex items-center justify-between border-b border-slate-100 pb-2">
                            <h4 class="text-xs font-bold text-slate-700 flex items-center gap-1.5">
                                <i class="fa-solid fa-gears text-indigo-600"></i> 학급 데이터 관리
                            </h4>
                            <span class="text-[10px] text-slate-400">교사 전용</span>
                        </div>
                        <p class="text-xs text-slate-600">
                            새로운 수업을 시작하거나 실습 데이터를 초기화합니다.
                        </p>
                        <button onclick="showResetConfirmModal()" class="w-full py-2.5 bg-rose-50 hover:bg-rose-100 text-rose-700 border border-rose-200 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1.5">
                            <i class="fa-solid fa-trash-can"></i>
                            <span>학급 설문 응답 전체 초기화</span>
                        </button>
                    </div>

                </div>
            </div>

            <!-- Educational Diagnosis Section -->
            <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-bold text-slate-800 flex items-center gap-2 border-b border-slate-100 pb-3">
                    <i class="fa-solid fa-microscope text-indigo-600"></i>
                    학급 응답 결과 바탕 저출산 · 고령화 원인 분석
                </h3>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div class="p-4 rounded-xl bg-amber-50/70 border border-amber-200/80 space-y-1.5">
                        <div class="flex justify-between items-center">
                            <span class="text-xs font-bold text-amber-900 flex items-center gap-1.5">
                                <i class="fa-solid fa-baby-carriage text-amber-600"></i> 저출산 원인 진단
                            </span>
                            <span id="diagBirthTag" class="text-[11px] font-bold px-2 py-0.5 rounded bg-amber-100 text-amber-800">진단 중</span>
                        </div>
                        <p id="diagBirthText" class="text-xs text-slate-700 leading-relaxed pt-1">
                            데이터 집계 중입니다.
                        </p>
                    </div>

                    <div class="p-4 rounded-xl bg-rose-50/70 border border-rose-200/80 space-y-1.5">
                        <div class="flex justify-between items-center">
                            <span class="text-xs font-bold text-rose-900 flex items-center gap-1.5">
                                <i class="fa-solid fa-person-cane text-rose-600"></i> 고령화 부양 부담 진단
                            </span>
                            <span id="diagAgingTag" class="text-[11px] font-bold px-2 py-0.5 rounded bg-rose-100 text-rose-800">진단 중</span>
                        </div>
                        <p id="diagAgingText" class="text-xs text-slate-700 leading-relaxed pt-1">
                            데이터 집계 중입니다.
                        </p>
                    </div>
                </div>
            </div>

        </div>

        <!-- VIEW 2: Student Survey Submission Mode -->
        <div id="studentView" class="hidden max-w-xl mx-auto space-y-6">
            
            <div class="bg-white rounded-2xl p-6 sm:p-8 border border-slate-200 shadow-md space-y-6">
                
                <div class="text-center border-b border-slate-100 pb-4">
                    <span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-bold bg-indigo-50 text-indigo-700 mb-2 border border-indigo-100">
                        <i class="fa-solid fa-mobile-screen"></i> 학생 개인 설문지
                    </span>
                    <h2 class="text-xl font-extrabold text-slate-800">나의 가치관과 미래 인구 설문</h2>
                    <p class="text-xs text-slate-500 mt-1">솔직하게 답변해 주세요! 학급 전체 결과에 즉시 반영됩니다.</p>
                </div>

                <!-- Form Inputs -->
                <form id="surveyForm" onsubmit="handleStudentSubmit(event)" class="space-y-6">
                    
                    <!-- Student Nickname / Name (Optional) -->
                    <div>
                        <label for="studentName" class="block text-xs font-bold text-slate-700 mb-1.5">
                            학생 닉네임 또는 번호 <span class="text-slate-400 font-normal">(선택 사항)</span>
                        </label>
                        <input type="text" id="studentName" placeholder="예: 1학년 2반 15번 무지개"
                               class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 text-xs font-medium transition">
                    </div>

                    <!-- 1. Desired Children -->
                    <div class="bg-slate-50 p-4 rounded-xl border border-slate-200/80 space-y-3">
                        <div class="flex justify-between items-center">
                            <label for="inputChildren" class="text-xs font-bold text-slate-800 flex items-center gap-2">
                                <span class="w-5 h-5 rounded-full bg-indigo-600 text-white flex items-center justify-center text-[11px]">1</span>
                                앞으로 희망하는 자녀 수는 몇 명인가요?
                            </label>
                            <span class="text-indigo-600 font-extrabold text-base"><span id="valChildren">1</span> 명</span>
                        </div>
                        <input type="range" id="inputChildren" min="0" max="5" step="1" value="1"
                               oninput="document.getElementById('valChildren').textContent = this.value"
                               class="w-full h-2.5 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                        <div class="flex justify-between text-[10px] text-slate-400 font-semibold px-0.5">
                            <span>0명 (무자녀)</span>
                            <span>1명</span>
                            <span>2명</span>
                            <span>3명</span>
                            <span>4명 이상</span>
                        </div>
                    </div>

                    <!-- 2. Marriage Intention -->
                    <div class="bg-slate-50 p-4 rounded-xl border border-slate-200/80 space-y-3">
                        <label class="block text-xs font-bold text-slate-800 flex items-center gap-2">
                            <span class="w-5 h-5 rounded-full bg-indigo-600 text-white flex items-center justify-center text-[11px]">2</span>
                            성인이 된 후 결혼할 의향이 있나요?
                        </label>
                        <div class="grid grid-cols-2 gap-3 pt-1">
                            <label class="cursor-pointer">
                                <input type="radio" name="inputMarriage" value="yes" checked class="peer sr-only">
                                <div class="p-3 text-center rounded-xl border-2 border-slate-200 peer-checked:border-indigo-600 peer-checked:bg-indigo-50/70 peer-checked:text-indigo-900 font-bold text-xs transition flex flex-col items-center gap-1">
                                    <span class="text-base">💍</span>
                                    <span>결혼하고 싶어요 (예)</span>
                                </div>
                            </label>
                            <label class="cursor-pointer">
                                <input type="radio" name="inputMarriage" value="no" class="peer sr-only">
                                <div class="p-3 text-center rounded-xl border-2 border-slate-200 peer-checked:border-rose-500 peer-checked:bg-rose-50/70 peer-checked:text-rose-900 font-bold text-xs transition flex flex-col items-center gap-1">
                                    <span class="text-base">🙅‍♂️</span>
                                    <span>비혼으로 살고 싶어요 (아니오)</span>
                                </div>
                            </label>
                        </div>
                    </div>

                    <!-- 3. Target Expected Lifespan -->
                    <div class="bg-slate-50 p-4 rounded-xl border border-slate-200/80 space-y-3">
                        <div class="flex justify-between items-center">
                            <label for="inputLifespan" class="text-xs font-bold text-slate-800 flex items-center gap-2">
                                <span class="w-5 h-5 rounded-full bg-indigo-600 text-white flex items-center justify-center text-[11px]">3</span>
                                몇 살까지 살고 싶나요? (목표 수명)
                            </label>
                            <span class="text-indigo-600 font-extrabold text-base"><span id="valLifespan">88</span> 세</span>
                        </div>
                        <input type="range" id="inputLifespan" min="50" max="100" step="1" value="88"
                               oninput="document.getElementById('valLifespan').textContent = this.value"
                               class="w-full h-2.5 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                        <div class="flex justify-between text-[10px] text-slate-400 font-semibold px-0.5">
                            <span>50세</span>
                            <span>70세</span>
                            <span>85세</span>
                            <span>100세</span>
                        </div>
                    </div>

                    <!-- Submit Button -->
                    <button type="submit" id="btnSubmit" class="w-full py-3.5 bg-indigo-600 hover:bg-indigo-700 text-white font-extrabold rounded-xl shadow-md transition flex items-center justify-center gap-2 text-sm">
                        <i class="fa-solid fa-paper-plane"></i>
                        <span>설문 응답 제출하기</span>
                    </button>
                </form>

                <!-- Submitted Success Card -->
                <div id="submittedBox" class="hidden bg-emerald-50 border border-emerald-200 rounded-2xl p-6 text-center space-y-4">
                    <div class="w-12 h-12 bg-emerald-500 text-white rounded-full flex items-center justify-center text-xl mx-auto shadow-md">
                        <i class="fa-solid fa-check"></i>
                    </div>
                    <div>
                        <h3 class="text-lg font-bold text-emerald-900">설문 제출이 완료되었습니다!</h3>
                        <p class="text-xs text-emerald-700 mt-1">선생님 스크린의 실시간 대시보드 인구 피라미드에 즉시 합산되었습니다.</p>
                    </div>
                    <button onclick="editMySubmission()" class="px-4 py-2 bg-white text-emerald-800 border border-emerald-300 rounded-xl text-xs font-bold hover:bg-emerald-100 transition shadow-sm">
                        <i class="fa-solid fa-pen-to-square"></i> 내 응답 수정하기
                    </button>
                </div>

            </div>

        </div>

    </main>

    <!-- Custom Modal 1: Enlarged QR Code -->
    <div id="qrModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl p-6 max-w-sm w-full text-center space-y-4 shadow-2xl">
            <h3 class="text-base font-bold text-slate-800">학생 설문 접속 QR 코드</h3>
            <div class="bg-slate-50 p-4 rounded-xl border border-slate-200 flex justify-center">
                <div id="qrcodeModal"></div>
            </div>
            <p class="text-xs text-slate-500">카메라 앱으로 위 QR 코드를 스캔하세요.</p>
            <button onclick="closeQRModal()" class="w-full py-2.5 bg-slate-800 text-white font-bold rounded-xl text-xs hover:bg-slate-900 transition">
                닫기
            </button>
        </div>
    </div>

    <!-- Custom Modal 2: Reset Data Confirmation -->
    <div id="resetModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl p-6 max-w-sm w-full space-y-4 shadow-2xl">
            <div class="w-10 h-10 rounded-full bg-rose-100 text-rose-600 flex items-center justify-center mx-auto text-lg">
                <i class="fa-solid fa-triangle-exclamation"></i>
            </div>
            <div class="text-center">
                <h3 class="text-base font-bold text-slate-800">학급 데이터를 초기화하시겠습니까?</h3>
                <p class="text-xs text-slate-500 mt-1">제출된 모든 학생 설문 응답이 데이터베이스에서 삭제됩니다.</p>
            </div>
            <div class="flex gap-2">
                <button onclick="closeResetModal()" class="flex-1 py-2.5 bg-slate-100 text-slate-700 font-bold rounded-xl text-xs hover:bg-slate-200 transition">
                    취소
                </button>
                <button onclick="executeResetData()" class="flex-1 py-2.5 bg-rose-600 text-white font-bold rounded-xl text-xs hover:bg-rose-700 transition">
                    전체 삭제
                </button>
            </div>
        </div>
    </div>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-5 right-5 z-50 hidden transform transition-all duration-300">
        <div id="toastBody" class="px-4 py-3 rounded-xl shadow-xl text-xs font-bold text-white flex items-center gap-2">
            <i id="toastIcon" class="fa-solid fa-circle-info"></i>
            <span id="toastMsg">알림 메시지</span>
        </div>
    </div>

    <!-- Footer -->
    <footer class="bg-white border-t border-slate-200 py-3 text-center text-xs text-slate-400">
        미래 인구 피라미드 시뮬레이터 &copy; 탐구 수업용 실시간 데이터베이스 연동 버전
    </footer>

    <!-- Firebase Standard Imports -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, deleteDoc, onSnapshot, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global Constants & State
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'demo-pyramid-app';
        const firebaseConfig = typeof __firebase_config !== 'undefined'
            ? JSON.parse(__firebase_config)
            : {
                apiKey: "demo-api-key",
                authDomain: "demo.firebaseapp.com",
                projectId: "demo-project",
                storageBucket: "demo.appspot.com",
                messagingSenderId: "123456789",
                appId: "1:123456789:web:demo"
            };

        // Firebase Instances
        let app, auth, db;
        let currentUser = null;

        // Unique Student Device Submission Key (Stored in LocalStorage)
        let myDeviceKey = localStorage.getItem('pyramid_device_key');
        if (!myDeviceKey) {
            myDeviceKey = 'user_' + Math.random().toString(36).substring(2, 10);
            localStorage.setItem('pyramid_device_key', myDeviceKey);
        }

        // Age Group Labels (TOP: 100+ to BOTTOM: 0-4)
        const AGE_LABELS = [
            '100세 이상', '95-99세', '90-94세', '85-89세', '80-84세', 
            '75-79세', '70-74세', '65-69세', '60-64세', '55-59세', 
            '50-54세', '45-49세', '40-44세', '35-39세', '30-34세', 
            '25-29세', '20-24세', '15-19세', '10-14세', '5-9세', '0-4세'
        ];

        // Benchmark Historical Pyramids Data (Percentages)
        const HISTORICAL_PYRAMIDS = {
            '1970': {
                male: [-0.1, -0.2, -0.4, -0.8, -1.2, -1.8, -2.5, -3.2, -3.8, -4.2, -4.5, -4.8, -5.0, -5.2, -5.5, -6.0, -6.5, -7.0, -7.5, -8.0, -8.5],
                female: [0.2, 0.4, 0.6, 1.0, 1.5, 2.0, 2.7, 3.4, 4.0, 4.4, 4.7, 5.0, 5.2, 5.4, 5.7, 6.2, 6.7, 7.2, 7.7, 8.2, 8.7]
            },
            '2024': {
                male: [-0.6, -1.1, -1.8, -2.6, -3.4, -3.9, -4.1, -4.0, -3.9, -4.0, -4.2, -4.0, -3.8, -3.5, -3.4, -3.2, -2.8, -2.2, -1.6, -1.2, -0.9],
                female: [1.2, 1.8, 2.4, 3.1, 3.7, 4.0, 4.1, 3.9, 3.8, 3.9, 4.1, 3.9, 3.7, 3.4, 3.3, 3.1, 2.7, 2.1, 1.5, 1.1, 0.8]
            }
        };

        let currentBenchmark = 'none'; // 'none', '1970', '2024'
        let chartInstance = null;
        let activeResponses = [];

        async function initFirebase() {
            try {
                app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);

                // Authentication
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    const userCredential = await signInWithCustomToken(auth, __initial_auth_token);
                    currentUser = userCredential.user;
                } else {
                    const userCredential = await signInAnonymously(auth);
                    currentUser = userCredential.user;
                }

                updateSyncState(true, "클라우드 실시간 연동 완료");
                listenToSurveyResponses();

            } catch (err) {
                console.error("Firebase Auth / Init Error:", err);
                updateSyncState(false, "클라우드 접속 오류 (오프라인 상태)");
            }
        }

        function updateSyncState(isOnline, text) {
            const dot = document.getElementById('syncDot');
            const status = document.getElementById('syncStatus');
            if (isOnline) {
                dot.className = "w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse";
                status.textContent = text;
                status.className = "font-semibold text-emerald-700";
            } else {
                dot.className = "w-2.5 h-2.5 rounded-full bg-rose-500";
                status.textContent = text;
                status.className = "font-semibold text-rose-700";
            }
        }

        function listenToSurveyResponses() {
            if (!currentUser) return;

            // Mandatory path rule: /artifacts/{appId}/public/data/survey_responses
            const surveyCol = collection(db, 'artifacts', appId, 'public', 'data', 'survey_responses');

            onSnapshot(surveyCol, (snapshot) => {
                activeResponses = [];
                let mySubmissionFound = false;

                snapshot.forEach((docSnap) => {
                    const data = docSnap.data();
                    activeResponses.push(data);
                    if (data.deviceId === myDeviceKey) {
                        mySubmissionFound = true;
                    }
                });

                // Update UI elements
                document.getElementById('studentCountText').textContent = activeResponses.length;
                
                // Show submitted box if student already submitted on this device
                if (mySubmissionFound) {
                    document.getElementById('surveyForm').classList.add('hidden');
                    document.getElementById('submittedBox').classList.remove('hidden');
                } else {
                    document.getElementById('surveyForm').classList.remove('hidden');
                    document.getElementById('submittedBox').classList.add('hidden');
                }

                // Refresh Pyramid Dashboard Chart
                calculateAndRenderDashboard();

            }, (error) => {
                console.error("Firestore error:", error);
                showToast("데이터 동기화 실패", "error");
            });
        }

        window.handleStudentSubmit = async function(e) {
            e.preventDefault();
            if (!currentUser) return;

            const name = document.getElementById('studentName').value.trim() || '익명 학생';
            const children = parseFloat(document.getElementById('inputChildren').value);
            const marriage = document.querySelector('input[name="inputMarriage"]:checked').value === 'yes';
            const lifespan = parseInt(document.getElementById('inputLifespan').value, 10);

            const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'survey_responses', myDeviceKey);

            try {
                await setDoc(docRef, {
                    deviceId: myDeviceKey,
                    name: name,
                    children: children,
                    marriage: marriage,
                    lifespan: lifespan,
                    updatedAt: new Date().toISOString()
                });

                showToast("응답이 성공적으로 제출되었습니다!", "success");
            } catch (err) {
                console.error("Submission failed:", err);
                showToast("제출 중 오류가 발생했습니다.", "error");
            }
        };

        window.editMySubmission = function() {
            document.getElementById('submittedBox').classList.add('hidden');
            document.getElementById('surveyForm').classList.remove('hidden');
        };

        function calculateAndRenderDashboard() {
            if (activeResponses.length === 0) {
                // Default fallback placeholder values if no submissions yet
                renderPyramidUI({
                    count: 0,
                    avgChildren: 0.8,
                    marriageRate: 60,
                    avgLifespan: 88,
                    tfr: 0.48,
                    malePcts: new Array(21).fill(0),
                    femalePcts: new Array(21).fill(0)
                });
                return;
            }

            let sumChildren = 0;
            let sumMarriage = 0;
            let sumLifespan = 0;

            activeResponses.forEach(r => {
                sumChildren += (r.children || 0);
                sumMarriage += (r.marriage ? 100 : 0);
                sumLifespan += (r.lifespan || 80);
            });

            const count = activeResponses.length;
            const avgChildren = sumChildren / count;
            const marriageRate = sumMarriage / count;
            const avgLifespan = sumLifespan / count;

            // TFR = Avg Children * (Marriage Rate / 100)
            const tfr = Math.max(0.01, avgChildren * (marriageRate / 100));

            // Cohort Growth factor r
            const r = Math.pow(tfr / 2.05, 1 / 6);

            let weights = [];
            for (let i = 0; i <= 20; i++) {
                let age = i * 5 + 2.5;
                let base = Math.pow(r, -i);
                let survival = 1.0 / (1.0 + Math.exp((age - avgLifespan) / 4.0));
                weights.push(base * survival);
            }

            const totalW = weights.reduce((a, b) => a + b, 0);
            let malePcts = [];
            let femalePcts = [];

            // Convert index k=0 (100+세) to k=20 (0-4세)
            for (let k = 0; k < 21; k++) {
                let i = 20 - k;
                let age = i * 5 + 2.5;
                let pct = (weights[i] / totalW) * 100;

                let femaleRatio = age >= 75 ? 0.53 : 0.502;
                let maleRatio = 1.0 - femaleRatio;

                malePcts.push(-parseFloat((pct * maleRatio).toFixed(2)));
                femalePcts.push(parseFloat((pct * femaleRatio).toFixed(2)));
            }

            renderPyramidUI({
                count,
                avgChildren: avgChildren.toFixed(1),
                marriageRate: Math.round(marriageRate),
                avgLifespan: Math.round(avgLifespan),
                tfr: tfr.toFixed(2),
                malePcts,
                femalePcts
            });
        }

        function renderPyramidUI(data) {
            document.getElementById('dashAvgChildren').textContent = data.avgChildren;
            document.getElementById('dashMarriageRate').textContent = data.marriageRate;
            document.getElementById('dashAvgLifespan').textContent = data.avgLifespan;
            document.getElementById('dashTFR').textContent = data.tfr;

            // Society tag update
            const tfrVal = parseFloat(data.tfr);
            const tagEl = document.getElementById('dashSocietyTag');
            if (tfrVal < 1.3) {
                tagEl.textContent = '초저출산 경고';
                tagEl.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-rose-100 text-rose-700';
            } else if (tfrVal < 2.05) {
                tagEl.textContent = '인구 감소세';
                tagEl.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-amber-100 text-amber-700';
            } else {
                tagEl.textContent = '인구 대물림 유지';
                tagEl.className = 'text-[10px] px-2 py-0.5 rounded-md font-bold bg-emerald-100 text-emerald-700';
            }

            // Diagnostic texts
            const birthTag = document.getElementById('diagBirthTag');
            const birthText = document.getElementById('diagBirthText');
            if (tfrVal < 1.3) {
                birthTag.textContent = '심각한 초저출산';
                birthTag.className = 'text-[11px] font-bold px-2 py-0.5 rounded bg-rose-100 text-rose-800';
                birthText.textContent = `학급 평균 추정 출산율(${data.tfr}명)이 대체출산율(2.05명)에 현저히 못 미칩니다. 피라미드 바닥(0~14세)이 급격히 축소되는 역피라미드 구조가 나타납니다. 양육 부담 및 독신 가치관 확산이 주요 원인입니다.`;
            } else {
                birthTag.textContent = '출산율 회복세';
                birthTag.className = 'text-[11px] font-bold px-2 py-0.5 rounded bg-emerald-100 text-emerald-800';
                birthText.textContent = `학급 평균 추정 출산율(${data.tfr}명)이 높아 유소년 인구가 밑바닥부터 안정적인 피라미드 모양을 유지합니다.`;
            }

            const agingTag = document.getElementById('diagAgingTag');
            const agingText = document.getElementById('diagAgingText');
            if (data.avgLifespan >= 85) {
                agingTag.textContent = '초고령화 진입';
                agingTag.className = 'text-[11px] font-bold px-2 py-0.5 rounded bg-rose-100 text-rose-800';
                agingText.textContent = `목표 수명 평균이 ${data.avgLifespan}세로 길어져 65세 이상 고령층 두께가 두꺼워집니다. 미래 청년층의 노년 부양 부담이 매우 커지는 구조입니다.`;
            } else {
                agingTag.textContent = '일반 수명 구조';
                agingTag.className = 'text-[11px] font-bold px-2 py-0.5 rounded bg-emerald-100 text-emerald-800';
                agingText.textContent = `고령층 비율이 완만하여 생산연령인구가 감당할 사회적 부양비 부담이 적당합니다.`;
            }

            // Update Chart
            updateChartJS(data.malePcts, data.femalePcts);
        }

        function updateChartJS(maleData, femaleData) {
            const ctx = document.getElementById('livePyramidCanvas').getContext('2d');

            let datasets = [
                {
                    label: '남성 인구 비중',
                    data: maleData,
                    backgroundColor: 'rgba(14, 165, 233, 0.85)',
                    borderColor: 'rgba(2, 132, 199, 1)',
                    borderWidth: 1,
                    barPercentage: 0.9
                },
                {
                    label: '여성 인구 비중',
                    data: femaleData,
                    backgroundColor: 'rgba(251, 113, 133, 0.85)',
                    borderColor: 'rgba(225, 29, 72, 1)',
                    borderWidth: 1,
                    barPercentage: 0.9
                }
            ];

            // Add Benchmark overlay if selected
            if (currentBenchmark !== 'none' && HISTORICAL_PYRAMIDS[currentBenchmark]) {
                const bm = HISTORICAL_PYRAMIDS[currentBenchmark];
                datasets.push({
                    label: `${currentBenchmark}년 남성 비교`,
                    data: bm.male,
                    type: 'line',
                    borderColor: 'rgba(217, 119, 6, 1)',
                    borderWidth: 2,
                    borderDash: [4, 4],
                    pointRadius: 0,
                    fill: false
                });
                datasets.push({
                    label: `${currentBenchmark}년 여성 비교`,
                    data: bm.female,
                    type: 'line',
                    borderColor: 'rgba(217, 119, 6, 1)',
                    borderWidth: 2,
                    borderDash: [4, 4],
                    pointRadius: 0,
                    fill: false
                });
            }

            if (!chartInstance) {
                chartInstance = new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: AGE_LABELS,
                        datasets: datasets
                    },
                    options: {
                        indexAxis: 'y',
                        responsive: true,
                        maintainAspectRatio: false,
                        animation: { duration: 400 },
                        scales: {
                            x: {
                                stacked: true,
                                ticks: {
                                    callback: (val) => Math.abs(val).toFixed(1) + '%'
                                },
                                title: { display: true, text: '전체 인구 대비 비율 (%)', font: { weight: 'bold' } }
                            },
                            y: {
                                stacked: true,
                                title: { display: true, text: '연령대 (5세 단위)', font: { weight: 'bold' } }
                            }
                        },
                        plugins: {
                            legend: { display: false },
                            tooltip: {
                                callbacks: {
                                    label: (ctx) => `${ctx.dataset.label}: ${Math.abs(ctx.raw).toFixed(2)}%`
                                }
                            }
                        }
                    }
                });
            } else {
                chartInstance.data.datasets = datasets;
                chartInstance.update();
            }
        }

        window.setBenchmark = function(type) {
            currentBenchmark = type;
            ['bmNone', 'bm1970', 'bm2024'].forEach(id => {
                document.getElementById(id).className = "px-2.5 py-1 rounded-md text-slate-600 hover:text-slate-900";
            });

            if (type === 'none') document.getElementById('bmNone').className = "px-2.5 py-1 rounded-md bg-white shadow-sm font-bold text-indigo-700";
            if (type === '1970') document.getElementById('bm1970').className = "px-2.5 py-1 rounded-md bg-amber-500 font-bold text-white shadow-sm";
            if (type === '2024') document.getElementById('bm2024').className = "px-2.5 py-1 rounded-md bg-amber-500 font-bold text-white shadow-sm";

            const bmLegend = document.getElementById('bmLegend');
            if (type === 'none') bmLegend.classList.add('hidden');
            else bmLegend.classList.remove('hidden');

            calculateAndRenderDashboard();
        };

        window.showResetConfirmModal = function() {
            document.getElementById('resetModal').classList.remove('hidden');
        };

        window.closeResetModal = function() {
            document.getElementById('resetModal').classList.add('hidden');
        };

        window.executeResetData = async function() {
            closeResetModal();
            if (!currentUser) return;

            try {
                const surveyCol = collection(db, 'artifacts', appId, 'public', 'data', 'survey_responses');
                const snapshot = await getDocs(surveyCol);

                const deletePromises = [];
                snapshot.forEach(docSnap => {
                    deletePromises.push(deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'survey_responses', docSnap.id)));
                });

                await Promise.all(deletePromises);
                showToast("학급 설문 데이터가 초기화되었습니다.", "info");

            } catch (err) {
                console.error("Reset failed:", err);
                showToast("초기화 중 오류가 발생했습니다.", "error");
            }
        };

        window.switchView = function(view) {
            const dashBtn = document.getElementById('tabDashboard');
            const studBtn = document.getElementById('tabStudent');
            const dashView = document.getElementById('dashboardView');
            const studView = document.getElementById('studentView');

            if (view === 'dashboard') {
                dashView.classList.remove('hidden');
                studView.classList.add('hidden');
                dashBtn.className = "px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 bg-indigo-600 text-white shadow-sm";
                studBtn.className = "px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 text-indigo-200 hover:text-white";
            } else {
                dashView.classList.add('hidden');
                studView.classList.remove('hidden');
                studBtn.className = "px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 bg-indigo-600 text-white shadow-sm";
                dashBtn.className = "px-3.5 py-1.5 rounded-lg transition-all duration-200 flex items-center gap-1.5 text-indigo-200 hover:text-white";
            }
        };

        function generateQRCodes() {
            const currentUrl = window.location.href;

            document.getElementById('qrcode').innerHTML = '';
            document.getElementById('qrcodeModal').innerHTML = '';

            new QRCode(document.getElementById('qrcode'), {
                text: currentUrl,
                width: 120,
                height: 120
            });

            new QRCode(document.getElementById('qrcodeModal'), {
                text: currentUrl,
                width: 220,
                height: 220
            });
        }

        window.openQRModal = function() {
            document.getElementById('qrModal').classList.remove('hidden');
        };

        window.closeQRModal = function() {
            document.getElementById('qrModal').classList.add('hidden');
        };

        window.copyShareLink = function() {
            const currentUrl = window.location.href;
            const tempInput = document.createElement('input');
            tempInput.value = currentUrl;
            document.body.appendChild(tempInput);
            tempInput.select();
            document.execCommand('copy');
            document.body.removeChild(tempInput);

            showToast("링크가 클립보드에 복사되었습니다!", "success");
        };

        function showToast(msg, type = "info") {
            const toast = document.getElementById('toast');
            const toastBody = document.getElementById('toastBody');
            const toastMsg = document.getElementById('toastMsg');

            toastMsg.textContent = msg;

            if (type === 'success') toastBody.className = "px-4 py-3 rounded-xl shadow-xl text-xs font-bold text-white flex items-center gap-2 bg-emerald-600";
            else if (type === 'error') toastBody.className = "px-4 py-3 rounded-xl shadow-xl text-xs font-bold text-white flex items-center gap-2 bg-rose-600";
            else toastBody.className = "px-4 py-3 rounded-xl shadow-xl text-xs font-bold text-white flex items-center gap-2 bg-indigo-600";

            toast.classList.remove('hidden');
            setTimeout(() => {
                toast.classList.add('hidden');
            }, 3000);
        }

        // Initialize on load
        window.addEventListener('DOMContentLoaded', () => {
            initFirebase();
            generateQRCodes();
        });
    </script>
</body>
</html>
