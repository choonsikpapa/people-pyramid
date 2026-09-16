<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>미래 인구 피라미드 시뮬레이터 (코호트 추계 모델)</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700;800&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; }
        
        /* Custom styling for range sliders */
        input[type=range]::-webkit-slider-thumb {
            box-shadow: 0 2px 6px rgba(0,0,0,0.2);
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-12">

    <!-- Header Section -->
    <header class="bg-indigo-900 text-white shadow-lg sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-4 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-xl sm:text-2xl font-extrabold flex items-center gap-3">
                    <i class="fa-solid fa-chart-area text-indigo-400"></i>
                    학생 설문 기반 미래 인구 피라미드 시뮬레이터
                </h1>
                <p class="text-indigo-200 text-xs sm:text-sm mt-1">
                    수학적 코호트 추계 모델(Cohort Progression Model)을 적용한 실시간 인구 예측 시뮬레이션
                </p>
            </div>
            <div class="flex items-center gap-2 bg-indigo-800/80 px-3 py-1.5 rounded-lg border border-indigo-700/80 text-xs shadow-inner">
                <i class="fa-solid fa-graduation-cap text-amber-400 text-sm"></i>
                <span>탐구 주제: 저출산 · 고령화 원인 및 인구 피라미드 유형 분석</span>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 mt-6">
        
        <!-- Top Section: Interactive Control Panel + Pyramid Chart -->
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
            
            <!-- Left Column: Controls & Indicators (5 cols) -->
            <div class="lg:col-span-5 space-y-6">
                
                <!-- Input Panel Card -->
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5">
                    <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-4">
                        <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-sliders text-indigo-600"></i>
                            설문 데이터 입력 (변수 설정)
                        </h2>
                        <span class="text-[11px] font-semibold text-indigo-600 bg-indigo-50 px-2.5 py-1 rounded-full border border-indigo-100 flex items-center gap-1">
                            <span class="w-2 h-2 bg-indigo-500 rounded-full animate-pulse"></span> 실시간 연동
                        </span>
                    </div>

                    <!-- Sliders -->
                    <div class="space-y-5">
                        <!-- Desired Children Slider -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="childrenInput" class="text-sm font-semibold text-slate-700">희망 자녀 수</label>
                                <span class="text-indigo-600 font-bold text-base"><span id="childrenVal">0.8</span> 명</span>
                            </div>
                            <input type="range" id="childrenInput" min="0.0" max="4.0" step="0.1" value="0.8"
                                   class="w-full h-2 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                            <div class="flex justify-between text-[10px] text-slate-400 mt-1 font-medium">
                                <span>0명 (극단 저출산)</span>
                                <span>2.1명 (대체출산)</span>
                                <span>4.0명 (고출산)</span>
                            </div>
                        </div>

                        <!-- Marriage Intention Slider -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="marriageInput" class="text-sm font-semibold text-slate-700">결혼 의향 비율</label>
                                <span class="text-indigo-600 font-bold text-base"><span id="marriageVal">60</span> %</span>
                            </div>
                            <input type="range" id="marriageInput" min="0" max="100" step="5" value="60"
                                   class="w-full h-2 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                            <div class="flex justify-between text-[10px] text-slate-400 mt-1 font-medium">
                                <span>0% (전원 독신)</span>
                                <span>50%</span>
                                <span>100% (전원 결혼)</span>
                            </div>
                        </div>

                        <!-- Expected Lifespan Slider -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="lifespanInput" class="text-sm font-semibold text-slate-700">평균 기대 수명</label>
                                <span class="text-indigo-600 font-bold text-base"><span id="lifespanVal">90</span> 세</span>
                            </div>
                            <input type="range" id="lifespanInput" min="50" max="100" step="1" value="90"
                                   class="w-full h-2 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                            <div class="flex justify-between text-[10px] text-slate-400 mt-1 font-medium">
                                <span>50세 (단수명)</span>
                                <span>80세 (현대 평균)</span>
                                <span>100세 (초장수)</span>
                            </div>
                        </div>
                    </div>

                    <!-- Presets Selection -->
                    <div class="mt-6 pt-4 border-t border-slate-100">
                        <span class="text-xs font-semibold text-slate-500 block mb-2 flex items-center gap-1.5">
                            <i class="fa-solid fa-wand-magic-sparkles text-amber-500"></i> 대표 시나리오 프리셋:
                        </span>
                        <div class="grid grid-cols-3 gap-2">
                            <button onclick="applyPreset(0.7, 50, 92)" class="px-2.5 py-2 bg-rose-50 hover:bg-rose-100 text-rose-700 border border-rose-200/70 rounded-lg text-xs font-semibold transition flex flex-col items-center gap-0.5">
                                <span>🚨 초저출산</span>
                                <span class="text-[10px] font-normal opacity-80">(항아리/역피라미드)</span>
                            </button>
                            <button onclick="applyPreset(2.1, 98, 85)" class="px-2.5 py-2 bg-emerald-50 hover:bg-emerald-100 text-emerald-700 border border-emerald-200/70 rounded-lg text-xs font-semibold transition flex flex-col items-center gap-0.5">
                                <span>🔔 대체출산율</span>
                                <span class="text-[10px] font-normal opacity-80">(종형 / 정체형)</span>
                            </button>
                            <button onclick="applyPreset(3.0, 100, 65)" class="px-2.5 py-2 bg-indigo-50 hover:bg-indigo-100 text-indigo-700 border border-indigo-200/70 rounded-lg text-xs font-semibold transition flex flex-col items-center gap-0.5">
                                <span>🔺 고출산 피라미드</span>
                                <span class="text-[10px] font-normal opacity-80">(전형적 피라미드)</span>
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Key Demographic Indicators Card -->
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5">
                    <h3 class="text-base font-bold text-slate-800 border-b border-slate-100 pb-3 mb-4 flex items-center justify-between">
                        <span class="flex items-center gap-2">
                            <i class="fa-solid fa-chart-pie text-indigo-600"></i>
                            예측 주요 인구 지표
                        </span>
                        <span id="metricSociety" class="text-xs px-2.5 py-1 rounded-full font-bold bg-rose-100 text-rose-700">초고령 사회</span>
                    </h3>

                    <div class="grid grid-cols-2 gap-3">
                        <div class="bg-indigo-50/60 p-3 rounded-xl border border-indigo-100">
                            <span class="text-xs text-indigo-700 font-medium block">추정 합계출산율 (TFR)</span>
                            <span id="metricTfr" class="text-xl font-extrabold text-indigo-900 mt-0.5 block">0.48 명</span>
                        </div>
                        <div class="bg-amber-50/60 p-3 rounded-xl border border-amber-100">
                            <span class="text-xs text-amber-800 font-medium block">노년 부양비</span>
                            <span id="metricDepend" class="text-xl font-extrabold text-amber-900 mt-0.5 block">0.0 명</span>
                            <span class="text-[10px] text-amber-700 block mt-0.5">(생산인구 100명당 노인)</span>
                        </div>
                        <div class="bg-sky-50/60 p-3 rounded-xl border border-sky-100">
                            <span class="text-xs text-sky-800 font-medium block">유소년 인구 (0~14세)</span>
                            <span id="metricYouth" class="text-lg font-bold text-sky-900 mt-0.5 block">0.0 %</span>
                        </div>
                        <div class="bg-emerald-50/60 p-3 rounded-xl border border-emerald-100">
                            <span class="text-xs text-emerald-800 font-medium block">생산연령 인구 (15~64세)</span>
                            <span id="metricWork" class="text-lg font-bold text-emerald-900 mt-0.5 block">0.0 %</span>
                        </div>
                        <div class="col-span-2 bg-rose-50/60 p-3 rounded-xl border border-rose-100 flex justify-between items-center">
                            <div>
                                <span class="text-xs text-rose-800 font-medium block">고령 인구 비율 (65세 이상)</span>
                                <span id="metricElder" class="text-lg font-bold text-rose-900">0.0 %</span>
                            </div>
                            <div class="text-right text-[11px] text-rose-700">
                                <span>기준: 7%(고령화), 14%(고령), 20%(초고령)</span>
                            </div>
                        </div>
                    </div>
                </div>

            </div>

            <!-- Right Column: Pyramid Chart View (7 cols) -->
            <div class="lg:col-span-7">
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5 h-full flex flex-col justify-between">
                    
                    <div>
                        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2 border-b border-slate-100 pb-3 mb-3">
                            <div>
                                <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                                    <i class="fa-solid fa-chart-bar text-indigo-600"></i>
                                    미래 인구 피라미드 차트
                                </h2>
                                <p class="text-xs text-slate-500 mt-0.5">
                                    바닥(0~4세)부터 정상(100세 이상)까지 5세 단위 연속 코호트 분포
                                </p>
                            </div>
                            <div class="flex items-center gap-4 text-xs font-semibold bg-slate-50 px-3 py-1.5 rounded-lg border border-slate-200">
                                <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-sky-500 rounded-sm inline-block"></span>남성</span>
                                <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-rose-400 rounded-sm inline-block"></span>여성</span>
                            </div>
                        </div>

                        <!-- Chart Canvas Container -->
                        <div class="relative w-full h-[510px]">
                            <canvas id="pyramidCanvas"></canvas>
                        </div>
                    </div>

                    <!-- Dynamic Model Status Banner -->
                    <div class="mt-3 p-3 bg-slate-50 rounded-xl border border-slate-200/80 text-xs text-slate-700 flex flex-col sm:flex-row justify-between items-center gap-2">
                        <div class="flex items-center gap-2">
                            <i class="fa-solid fa-calculator text-indigo-600 text-sm"></i>
                            <span>코호트 성장인자 $r$: <strong id="modelRVal" class="text-indigo-700">0.785</strong></span>
                            <span class="text-slate-300">|</span>
                            <span>피라미드 형태: <strong id="modelShapeText" class="text-indigo-700">역피라미드 / 항아리형</strong></span>
                        </div>
                        <div class="text-[11px] text-slate-500">
                            *수학식: $r = (TFR / 2.05)^{1/6}$, $w_i = r^{-i} \cdot \text{Sigmoid}(\text{Age})$
                        </div>
                    </div>

                </div>
            </div>

        </div>

        <!-- Bottom Section: Dynamic Educational Analysis & Causes -->
        <section class="mt-8 bg-white rounded-2xl shadow-sm border border-slate-200/80 p-6 space-y-6">
            
            <div class="border-b border-slate-100 pb-3">
                <h2 class="text-xl font-extrabold text-slate-800 flex items-center gap-2.5">
                    <i class="fa-solid fa-magnifying-glass-chart text-indigo-600"></i>
                    설문 조건에 따른 저출산 · 고령화 원인 진단 및 교육용 가이드
                </h2>
                <p class="text-xs text-slate-500 mt-1">
                    학생들이 설정한 지표 수치에 따라 현재 인구 구조의 문제점과 원인이 동적으로 분석됩니다.
                </p>
            </div>

            <!-- Dynamic Diagnosis Cards Grid -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Low Birth Rate Cause Diagnosis -->
                <div class="p-5 rounded-xl bg-amber-50/50 border border-amber-200/80 space-y-2">
                    <div class="flex justify-between items-center">
                        <h3 class="font-bold text-amber-900 text-sm flex items-center gap-2">
                            <i class="fa-solid fa-baby text-amber-600"></i> 저출산 원인 및 영향 진단
                        </h3>
                        <span id="birthStatusTag" class="text-xs font-bold px-2 py-0.5 rounded bg-amber-100 text-amber-800">초저출산 상태</span>
                    </div>
                    <p id="birthDiagnosisText" class="text-xs text-slate-700 leading-relaxed pt-1">
                        현재 설정된 희망 자녀 수와 결혼 의향으로는 합계출산율이 인구 유지선(2.05명)에 크게 못 미칩니다. 주거·양육 비용 부담, 비혼 가치관 확산 등의 요인이 반영되면 유소년층 인구가 급감하여 피라미드 최하단이 심각하게 축소됩니다.
                    </p>
                </div>

                <!-- Aging Cause Diagnosis -->
                <div class="p-5 rounded-xl bg-rose-50/50 border border-rose-200/80 space-y-2">
                    <div class="flex justify-between items-center">
                        <h3 class="font-bold text-rose-900 text-sm flex items-center gap-2">
                            <i class="fa-solid fa-wheelchair text-rose-600"></i> 고령화 원인 및 부양 부담 진단
                        </h3>
                        <span id="agingStatusTag" class="text-xs font-bold px-2 py-0.5 rounded bg-rose-100 text-rose-800">초고령 사회</span>
                    </div>
                    <p id="agingDiagnosisText" class="text-xs text-slate-700 leading-relaxed pt-1">
                        의학 기술의 발전과 보건 환경 개선으로 기대수명이 대폭 늘어나 고령층 비중이 압도적으로 증가합니다. 반면 출산율 감소로 생산연령인구는 줄어들어, 생산인구 1명이 부양해야 할 노인 수(노년 부양비)가 급격히 늘어나 사회적 부담이 가중됩니다.
                    </p>
                </div>
            </div>

            <!-- 3 Classic Population Pyramid Archetypes -->
            <div>
                <h3 class="text-sm font-bold text-slate-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-layer-group text-indigo-600"></i> 인구 피라미드 3대 전형(Archetype) 비교
                </h3>
                
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <!-- Pyramid Type -->
                    <div class="p-4 rounded-xl border border-slate-200 bg-slate-50/70 hover:bg-white transition">
                        <div class="flex justify-between items-center mb-2">
                            <span class="text-xs font-bold text-indigo-800 bg-indigo-100 px-2 py-0.5 rounded">피라미드형 (Triangle)</span>
                            <span class="text-[11px] font-semibold text-slate-500">TFR > 2.05</span>
                        </div>
                        <h4 class="font-bold text-xs text-slate-800 mb-1">고출산 · 높은 유소년 비율</h4>
                        <p class="text-[11px] text-slate-600 leading-relaxed">
                            밑변(0~4세)이 가장 넓고 위로 갈수록 좁아지는 형태. 과거 농경/산업화 초기 단계 또는 고출산 국가에서 나타나며 인구가 지속적으로 성장합니다.
                        </p>
                    </div>

                    <!-- Bell Type -->
                    <div class="p-4 rounded-xl border border-slate-200 bg-slate-50/70 hover:bg-white transition">
                        <div class="flex justify-between items-center mb-2">
                            <span class="text-xs font-bold text-emerald-800 bg-emerald-100 px-2 py-0.5 rounded">종형 / 정체형 (Bell)</span>
                            <span class="text-[11px] font-semibold text-slate-500">TFR ≈ 2.05</span>
                        </div>
                        <h4 class="font-bold text-xs text-slate-800 mb-1">대체출산율 달성 · 인구 안정</h4>
                        <p class="text-[11px] text-slate-600 leading-relaxed">
                            유소년층부터 고령층 직전까지 인구 비중이 고르게 유지되는 형태. 인구 규모가 급격한 증감 없이 안정적으로 유지되는 선진국형 구조입니다.
                        </p>
                    </div>

                    <!-- Urn / Inverted Type -->
                    <div class="p-4 rounded-xl border border-slate-200 bg-slate-50/70 hover:bg-white transition">
                        <div class="flex justify-between items-center mb-2">
                            <span class="text-xs font-bold text-rose-800 bg-rose-100 px-2 py-0.5 rounded">항아리/역피라미드 (Urn)</span>
                            <span class="text-[11px] font-semibold text-slate-500">TFR < 2.05</span>
                        </div>
                        <h4 class="font-bold text-xs text-slate-800 mb-1">저출산 · 초고령화 진행</h4>
                        <p class="text-[11px] text-slate-600 leading-relaxed">
                            최하단 유소년층이 가늘고 50~80대 연령층이 비대해진 형태. 생산연령인구 감소와 노년 부양비 폭증으로 경제적 동력이 낮아지는 구조입니다.
                        </p>
                    </div>
                </div>
            </div>

            <!-- Discussion Guide -->
            <div class="p-4 bg-indigo-50/70 rounded-xl border border-indigo-100 text-xs text-indigo-950 space-y-2">
                <h4 class="font-bold text-sm text-indigo-900 flex items-center gap-2">
                    <i class="fa-solid fa-comments text-indigo-600"></i> 수업 탐구 토론 질문 가이드
                </h4>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-3 pt-1">
                    <p>
                        <strong>1. 가치관과 인구 구조:</strong> 우리 반 설문 결과에서 희망 자녀 수나 결혼 의향이 감소한 가장 주요한 사회 환경적 원인은 무엇이며, 이를 바꾸기 위해 필요한 정책은 무엇일까요?
                    </p>
                    <p>
                        <strong>2. 수학적 모델의 이해:</strong> 출산율이 2.05명보다 클 때 코호트 성장인자 $r > 1$이 되어 왜 밑변(0~4세)이 가장 넓은 삼각형 피라미드가 형성되는지 인구 대물림 원리로 설명해 보세요.
                    </p>
                </div>
            </div>

        </section>

    </main>

    <script>
        // Age group labels ordered from TOP (100세 이상, index 0) to BOTTOM (0-4세, index 20)
        // Chart.js horizontal bar chart places array index 0 at the TOP of the canvas.
        const AGE_LABELS = [
            '100세 이상', '95-99세', '90-94세', '85-89세', '80-84세', 
            '75-79세', '70-74세', '65-69세', '60-64세', '55-59세', 
            '50-54세', '45-49세', '40-44세', '35-39세', '30-34세', 
            '25-29세', '20-24세', '15-19세', '10-14세', '5-9세', '0-4세'
        ];

        let chartInstance = null;

        // DOM Element references
        const childrenInput = document.getElementById('childrenInput');
        const marriageInput = document.getElementById('marriageInput');
        const lifespanInput = document.getElementById('lifespanInput');

        const childrenVal = document.getElementById('childrenVal');
        const marriageVal = document.getElementById('marriageVal');
        const lifespanVal = document.getElementById('lifespanVal');

        /**
         * Mathematical Cohort Progression Population Calculation Model
         *
         * @param {number} children - Average desired children per person
         * @param {number} marriagePct - Percentage of people willing to marry (0-100)
         * @param {number} lifespan - Average expected life expectancy
         */
        function calculateCohortPopulation(children, marriagePct, lifespan) {
            // 1. Calculate Estimated Total Fertility Rate (TFR)
            const tfr = children * (marriagePct / 100);
            
            // Prevent division by zero or NaN for extremely low TFR
            const safeTFR = Math.max(0.01, tfr);

            // 2. Growth Factor r per 5-year cohort step
            // 1 generation ≈ 30 years = 6 five-year steps.
            // Replacement fertility level = 2.05
            const r = Math.pow(safeTFR / 2.05, 1 / 6);

            // Weights array for cohorts from i=0 (0-4세) to i=20 (100+세)
            let cohortWeights = [];

            for (let i = 0; i <= 20; i++) {
                // Representative central age for cohort i
                let age = i * 5 + 2.5;

                // Base progression weight: r^(-i)
                // If TFR > 2.05 => r > 1 => r^(-i) decreases as i increases (0-4세 widest)
                // If TFR < 2.05 => r < 1 => r^(-i) increases as i increases (0-4세 narrowest)
                // If TFR ≈ 2.05 => r ≈ 1 => r^(-i) ≈ 1 (constant width)
                let base = Math.pow(r, -i);

                // Continuous Sigmoid Survival Function
                // Smooth drop-off as age approaches and exceeds expected lifespan
                let survival = 1.0 / (1.0 + Math.exp((age - lifespan) / 4.0));

                let weight = base * survival;
                cohortWeights.push(weight);
            }

            // 3. Normalize weights to 100% total population
            const totalWeight = cohortWeights.reduce((sum, val) => sum + val, 0);

            let malePcts = [];
            let femalePcts = [];

            let youthSum = 0;   // 0~14세 (cohorts i=0,1,2)
            let workSum = 0;    // 15~64세 (cohorts i=3..12)
            let elderSum = 0;   // 65세 이상 (cohorts i=13..20)

            // Loop from array index k=0 ('100세 이상') to k=20 ('0-4세')
            // Chart index k corresponds to cohort index i = (20 - k)
            for (let k = 0; k < AGE_LABELS.length; k++) {
                let i = 20 - k;
                let age = i * 5 + 2.5;
                let pct = (cohortWeights[i] / totalWeight) * 100;

                // Accumulate broad demographic categories
                if (age < 15) youthSum += pct;
                else if (age < 65) workSum += pct;
                else elderSum += pct;

                // Natural biological sex ratio adjustment (slightly higher female survival past age 75)
                let femaleRatio = age >= 75 ? 0.53 : 0.502;
                let maleRatio = 1.0 - femaleRatio;

                // Male values are negative for left-side bar plotting
                malePcts.push(-parseFloat((pct * maleRatio).toFixed(2)));
                femalePcts.push(parseFloat((pct * femaleRatio).toFixed(2)));
            }

            // Determine Shape Type Description
            let shapeText = '';
            if (tfr > 2.2) {
                shapeText = '전형적 피라미드형 (Triangle)';
            } else if (tfr >= 1.9) {
                shapeText = '종형 / 정체형 (Bell)';
            } else {
                shapeText = '항아리형 / 역피라미드 (Urn)';
            }

            return {
                malePcts,
                femalePcts,
                tfr: tfr.toFixed(2),
                rVal: r.toFixed(3),
                shapeText,
                youthSum: youthSum.toFixed(1),
                workSum: workSum.toFixed(1),
                elderSum: elderSum.toFixed(1),
                oldAgeDependency: workSum > 0 ? ((elderSum / workSum) * 100).toFixed(1) : '0.0'
            };
        }

        /**
         * Main function to refresh all UI metrics and Chart.js visualization
         */
        function updatePyramidChart() {
            const children = parseFloat(childrenInput.value);
            const marriage = parseFloat(marriageInput.value);
            const lifespan = parseFloat(lifespanInput.value);

            // Update text labels next to sliders
            childrenVal.textContent = children.toFixed(1);
            marriageVal.textContent = marriage;
            lifespanVal.textContent = lifespan;

            // Execute mathematical model
            const popData = calculateCohortPopulation(children, marriage, lifespan);

            // Update Indicator Cards
            document.getElementById('metricTfr').textContent = `${popData.tfr} 명`;
            document.getElementById('metricYouth').textContent = `${popData.youthSum} %`;
            document.getElementById('metricWork').textContent = `${popData.workSum} %`;
            document.getElementById('metricElder').textContent = `${popData.elderSum} %`;
            document.getElementById('metricDepend').textContent = `${popData.oldAgeDependency} 명`;
            
            document.getElementById('modelRVal').textContent = popData.rVal;
            document.getElementById('modelShapeText').textContent = popData.shapeText;

            // Society Type Tag Update
            const elderVal = parseFloat(popData.elderSum);
            const societyEl = document.getElementById('metricSociety');
            if (elderVal >= 20) {
                societyEl.textContent = '초고령 사회';
                societyEl.className = 'text-xs px-2.5 py-1 rounded-full font-bold bg-rose-100 text-rose-700';
            } else if (elderVal >= 14) {
                societyEl.textContent = '고령 사회';
                societyEl.className = 'text-xs px-2.5 py-1 rounded-full font-bold bg-amber-100 text-amber-700';
            } else if (elderVal >= 7) {
                societyEl.textContent = '고령화 사회';
                societyEl.className = 'text-xs px-2.5 py-1 rounded-full font-bold bg-yellow-100 text-yellow-700';
            } else {
                societyEl.textContent = '일반 사회';
                societyEl.className = 'text-xs px-2.5 py-1 rounded-full font-bold bg-emerald-100 text-emerald-700';
            }

            // Dynamic Educational Cause Diagnosis Texts
            const tfrVal = parseFloat(popData.tfr);
            const birthTag = document.getElementById('birthStatusTag');
            const birthText = document.getElementById('birthDiagnosisText');

            if (tfrVal < 1.3) {
                birthTag.textContent = '초저출산 경고';
                birthTag.className = 'text-xs font-bold px-2 py-0.5 rounded bg-rose-100 text-rose-800';
                birthText.textContent = `현재 추정 출산율(${tfrVal}명)은 인구 대체선(2.05명)보다 대단히 낮습니다. 유소년층(0~14세) 비중이 ${popData.youthSum}%로 급감하여 미래 생산연령인구가 크게 축소되는 역피라미드형 인구 구조가 형성됩니다. 주거비, 양육 부담, 비혼관 확산이 주요 요인입니다.`;
            } else if (tfrVal < 2.05) {
                birthTag.textContent = '출산율 인구대체 미달';
                birthTag.className = 'text-xs font-bold px-2 py-0.5 rounded bg-amber-100 text-amber-800';
                birthText.textContent = `현재 추정 출산율(${tfrVal}명)은 대체출산율(2.05명)에 다소 미치지 못합니다. 유소년 비중은 ${popData.youthSum}%이며 완만한 감소세를 보여 완만한 항아리형 인구 피라미드가 유지됩니다.`;
            } else {
                birthTag.textContent = '고출산 · 인구 성장';
                birthTag.className = 'text-xs font-bold px-2 py-0.5 rounded bg-emerald-100 text-emerald-800';
                birthText.textContent = `현재 추정 출산율(${tfrVal}명)은 대체출산율(2.05명)을 상회합니다! 유소년 비중이 ${popData.youthSum}%로 대폭 확대되어 최하단(0~4세)이 가장 넓고 위로 갈수록 점차 좁아지는 완벽한 삼각형 '전형적 피라미드' 구조가 형성됩니다.`;
            }

            const agingTag = document.getElementById('agingStatusTag');
            const agingText = document.getElementById('agingDiagnosisText');

            if (elderVal >= 20) {
                agingTag.textContent = '초고령 사회 진입';
                agingTag.className = 'text-xs font-bold px-2 py-0.5 rounded bg-rose-100 text-rose-800';
                agingText.textContent = `기대수명 ${lifespan}세 적용으로 65세 이상 고령 인구가 전체의 ${popData.elderSum}%에 달합니다. 노년 부양비가 ${popData.oldAgeDependency}명으로 증가하여 생산연령인구 100명이 노인 약 ${Math.round(parseFloat(popData.oldAgeDependency))}명을 부양해야 하는 심각한 경제적 부담이 발생합니다.`;
            } else if (elderVal >= 14) {
                agingTag.textContent = '고령 사회';
                agingTag.className = 'text-xs font-bold px-2 py-0.5 rounded bg-amber-100 text-amber-800';
                agingText.textContent = `고령 인구 비중이 ${popData.elderSum}%로 사회 전반의 고령화가 진행 중입니다. 복지 재정 확충 및 은퇴 후 재고용 정책 등의 대비가 필요합니다.`;
            } else {
                agingTag.textContent = '일반 / 젊은 인구 구조';
                agingTag.className = 'text-xs font-bold px-2 py-0.5 rounded bg-emerald-100 text-emerald-800';
                agingText.textContent = `고령 인구 비율이 ${popData.elderSum}%로 노년 부양 부담이 상대적으로 적고 생산연령층이 풍부한 활력 있는 인구 구조입니다.`;
            }

            // Chart.js Creation and Updates
            if (!chartInstance) {
                const ctx = document.getElementById('pyramidCanvas').getContext('2d');
                chartInstance = new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: AGE_LABELS,
                        datasets: [
                            {
                                label: '남성 인구 비중',
                                data: popData.malePcts,
                                backgroundColor: 'rgba(14, 165, 233, 0.85)', // Sky blue
                                borderColor: 'rgba(2, 132, 199, 1)',
                                borderWidth: 1,
                                barPercentage: 0.9,
                                categoryPercentage: 0.95
                            },
                            {
                                label: '여성 인구 비중',
                                data: popData.femalePcts,
                                backgroundColor: 'rgba(251, 113, 133, 0.85)', // Rose pink
                                borderColor: 'rgba(225, 29, 72, 1)',
                                borderWidth: 1,
                                barPercentage: 0.9,
                                categoryPercentage: 0.95
                            }
                        ]
                    },
                    options: {
                        indexAxis: 'y', // Horizontal bars
                        responsive: true,
                        maintainAspectRatio: false,
                        animation: {
                            duration: 200 // Instant smooth updates during slider dragging
                        },
                        scales: {
                            x: {
                                stacked: true,
                                ticks: {
                                    stepSize: 2,
                                    callback: function(value) {
                                        return Math.abs(value).toFixed(1) + '%'; // Format as absolute percentage
                                    },
                                    font: { size: 11 }
                                },
                                title: {
                                    display: true,
                                    text: '전체 인구 대비 비율 (%)',
                                    font: { size: 12, weight: 'bold' }
                                },
                                grid: {
                                    color: (context) => context.tick.value === 0 ? '#334155' : '#f1f5f9',
                                    lineWidth: (context) => context.tick.value === 0 ? 1.5 : 1
                                }
                            },
                            y: {
                                stacked: true,
                                ticks: {
                                    font: { size: 11, weight: '500' }
                                },
                                title: {
                                    display: true,
                                    text: '연령대 (5세 단위)',
                                    font: { size: 12, weight: 'bold' }
                                }
                            }
                        },
                        plugins: {
                            legend: { display: false },
                            tooltip: {
                                callbacks: {
                                    label: function(context) {
                                        let label = context.dataset.label || '';
                                        let val = Math.abs(context.raw).toFixed(2);
                                        return `${label}: ${val}%`;
                                    }
                                }
                            }
                        }
                    }
                });
            } else {
                // Update chart dataset in real time
                chartInstance.data.datasets[0].data = popData.malePcts;
                chartInstance.data.datasets[1].data = popData.femalePcts;
                chartInstance.update();
            }
        }

        /**
         * Quick scenario preset loader
         */
        function applyPreset(children, marriage, lifespan) {
            childrenInput.value = children;
            marriageInput.value = marriage;
            lifespanInput.value = lifespan;
            updatePyramidChart();
        }

        // Attach real-time input listeners for butter-smooth dragging experience
        [childrenInput, marriageInput, lifespanInput].forEach(input => {
            input.addEventListener('input', updatePyramidChart);
        });

        // Initialize simulator on page load
        window.addEventListener('DOMContentLoaded', () => {
            updatePyramidChart();
        });
    </script>
</body>
</html>
