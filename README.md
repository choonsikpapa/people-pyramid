<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>미래 인구 피라미드 시뮬레이터 & 비교 분석</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-12">

    <!-- Header Section -->
    <header class="bg-indigo-900 text-white shadow-md">
        <div class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl sm:text-3xl font-bold flex items-center gap-3">
                    <i class="fa-solid fa-users-line text-indigo-400"></i>
                    학생 설문 기반 미래 인구 피라미드
                </h1>
                <p class="text-indigo-200 text-sm mt-1">설문 결과를 바탕으로 미래 인구 구조를 예측하고 저출산·고령화 원인을 탐구합니다.</p>
            </div>
            <div class="flex items-center gap-2 bg-indigo-800/80 px-4 py-2 rounded-lg border border-indigo-700 text-xs">
                <i class="fa-solid fa-graduation-cap text-yellow-400 text-base"></i>
                <span>탐구 주제: 저출산 · 고령화 원인 및 인구 부양비 변화</span>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 mt-6">
        
        <!-- Top Row: Input Panel + Chart Area -->
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
            
            <!-- Left Column: Controls & Indicators (5 cols) -->
            <div class="lg:col-span-5 space-y-6">
                
                <!-- Input Panel Card -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5">
                    <h2 class="text-lg font-bold text-slate-800 border-b border-slate-100 pb-3 mb-4 flex items-center justify-between">
                        <span><i class="fa-solid fa-sliders text-indigo-600 mr-2"></i>설문 데이터 입력</span>
                        <span class="text-xs font-normal text-indigo-600 bg-indigo-50 px-2 py-1 rounded">실시간 반영 중</span>
                    </h2>

                    <!-- Inputs -->
                    <div class="space-y-5">
                        <!-- Desired Children -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="childrenInput" class="text-sm font-semibold text-slate-700">희망 자녀 수</label>
                                <span class="text-indigo-600 font-bold text-base"><span id="childrenVal">0.8</span>명</span>
                            </div>
                            <input type="range" id="childrenInput" min="0" max="3" step="0.1" value="0.8"
                                   class="w-full h-2 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                            <p class="text-xs text-slate-400 mt-1">유소년(0~14세) 인구 비율 형성에 직접 영향을 줍니다.</p>
                        </div>

                        <!-- Marriage Intention -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="marriageInput" class="text-sm font-semibold text-slate-700">결혼 의향 비율</label>
                                <span class="text-indigo-600 font-bold text-base"><span id="marriageVal">60</span>%</span>
                            </div>
                            <input type="range" id="marriageInput" min="0" max="100" step="5" value="60"
                                   class="w-full h-2 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                            <p class="text-xs text-slate-400 mt-1">출산율 가중치 산정에 반영됩니다.</p>
                        </div>

                        <!-- Expected Lifespan -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="lifespanInput" class="text-sm font-semibold text-slate-700">평균 기대 수명</label>
                                <span class="text-indigo-600 font-bold text-base"><span id="lifespanVal">90</span>세</span>
                            </div>
                            <input type="range" id="lifespanInput" min="65" max="110" step="1" value="90"
                                   class="w-full h-2 bg-slate-200 rounded-lg appearance-none cursor-pointer accent-indigo-600">
                            <p class="text-xs text-slate-400 mt-1">고령(65세 이상) 인구층의 높이와 두께를 결정합니다.</p>
                        </div>
                    </div>

                    <!-- Preset Scenario Buttons -->
                    <div class="mt-6 pt-4 border-t border-slate-100">
                        <span class="text-xs font-semibold text-slate-500 block mb-2">💡 프리셋 시나리오 선택:</span>
                        <div class="grid grid-cols-3 gap-2">
                            <button onclick="applyPreset(0.7, 50, 92)" class="px-2 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded text-xs font-medium transition">
                                초저출산 경고
                            </button>
                            <button onclick="applyPreset(1.3, 75, 88)" class="px-2 py-1.5 bg-indigo-50 hover:bg-indigo-100 text-indigo-700 rounded text-xs font-medium transition">
                                학생 평균 예시
                            </button>
                            <button onclick="applyPreset(2.1, 85, 85)" class="px-2 py-1.5 bg-emerald-50 hover:bg-emerald-100 text-emerald-700 rounded text-xs font-medium transition">
                                대체출산율 달성
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Demographic Indicators Output Card -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5">
                    <h3 class="text-md font-bold text-slate-800 border-b border-slate-100 pb-3 mb-4 flex items-center">
                        <i class="fa-solid fa-chart-pie text-indigo-600 mr-2"></i>예측 주요 인구 지표
                    </h3>

                    <div class="grid grid-cols-2 gap-3">
                        <div class="bg-slate-50 p-3 rounded-lg border border-slate-100">
                            <span class="text-xs text-slate-500 block">추정 합계출산율</span>
                            <span id="metricTfr" class="text-xl font-bold text-indigo-600">0.48 명</span>
                        </div>
                        <div class="bg-slate-50 p-3 rounded-lg border border-slate-100">
                            <span class="text-xs text-slate-500 block">사회 구조 판단</span>
                            <span id="metricSociety" class="text-sm font-bold text-rose-600">초고령 사회</span>
                        </div>
                        <div class="bg-sky-50 p-3 rounded-lg border border-sky-100">
                            <span class="text-xs text-sky-700 block">유소년 인구 (0~14세)</span>
                            <span id="metricYouth" class="text-lg font-bold text-sky-800">0.0 %</span>
                        </div>
                        <div class="bg-emerald-50 p-3 rounded-lg border border-emerald-100">
                            <span class="text-xs text-emerald-700 block">생산연령 인구 (15~64세)</span>
                            <span id="metricWork" class="text-lg font-bold text-emerald-800">0.0 %</span>
                        </div>
                        <div class="bg-rose-50 p-3 rounded-lg border border-rose-100">
                            <span class="text-xs text-rose-700 block">고령 인구 (65세 이상)</span>
                            <span id="metricElder" class="text-lg font-bold text-rose-800">0.0 %</span>
                        </div>
                        <div class="bg-amber-50 p-3 rounded-lg border border-amber-100">
                            <span class="text-xs text-amber-700 block">노년 부양비</span>
                            <span id="metricDepend" class="text-lg font-bold text-amber-800">0.0 명</span>
                            <span class="text-[10px] text-amber-600 block">(생산인구 100명당 노인수)</span>
                        </div>
                    </div>
                </div>

            </div>

            <!-- Right Column: Pyramid Chart View (7 cols) -->
            <div class="lg:col-span-7">
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5 h-full flex flex-col justify-between">
                    
                    <div>
                        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2 border-b border-slate-100 pb-3 mb-4">
                            <div>
                                <h2 class="text-lg font-bold text-slate-800 flex items-center">
                                    <i class="fa-solid fa-chart-bar text-indigo-600 mr-2"></i>미래 인구 피라미드 차트
                                </h2>
                                <p class="text-xs text-slate-500">바닥(0~4세)부터 정상(100세 이상)까지 5세 단위 구분</p>
                            </div>
                            <div class="flex items-center gap-4 text-xs font-semibold">
                                <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-sky-500 rounded-sm inline-block"></span>남성</span>
                                <span class="flex items-center gap-1.5"><span class="w-3 h-3 bg-rose-400 rounded-sm inline-block"></span>여성</span>
                            </div>
                        </div>

                        <!-- Chart Canvas Box -->
                        <div class="relative w-full h-[520px]">
                            <canvas id="pyramidCanvas"></canvas>
                        </div>
                    </div>

                    <div class="mt-4 p-3 bg-indigo-50/60 rounded-lg border border-indigo-100 text-xs text-indigo-900 flex items-center gap-2">
                        <i class="fa-solid fa-circle-info text-indigo-500 text-base"></i>
                        <span>X축의 범위(-8% ~ +8%)가 고정되어 있어 설정 변경에 따른 인구 비중 확축을 직관적으로 확인할 수 있습니다.</span>
                    </div>

                </div>
            </div>

        </div>

        <!-- Bottom Row: Historical Comparison & Educational Exploration Section -->
        <section class="mt-8 bg-white rounded-xl shadow-sm border border-slate-200 p-6">
            <h2 class="text-xl font-bold text-slate-800 mb-4 flex items-center">
                <i class="fa-solid fa-clock-rotate-left text-indigo-600 mr-2.5"></i>
                시대별 인구 피라미드 비교 및 원인 탐구
            </h2>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <!-- 1970s -->
                <div class="bg-amber-50/50 rounded-xl p-5 border border-amber-200/60">
                    <div class="flex justify-between items-center mb-3">
                        <span class="text-xs font-bold text-amber-800 bg-amber-100 px-2 py-0.5 rounded">과거 (1970년대)</span>
                        <span class="text-xs font-semibold text-amber-700">합계출산율 ~4.5명</span>
                    </div>
                    <h3 class="font-bold text-slate-800 mb-2">전형적인 피라미드형 (다산소사)</h3>
                    <ul class="text-xs text-slate-600 space-y-2 leading-relaxed">
                        <li>• <strong class="text-slate-700">유소년 비중 압도적 높음:</strong> 밑변이 매우 넓고 위로 갈수록 급격히 좁아지는 형태.</li>
                        <li>• <strong class="text-slate-700">저출산 원인 차이:</strong> 농경/산업화 초기 단계로 노동력 확보를 위한 다산 선호.</li>
                        <li>• <strong class="text-slate-700">고령층 비중 미비:</strong> 의학 기술 한계로 기대수명이 상대적으로 짧음.</li>
                    </ul>
                </div>

                <!-- 2020s -->
                <div class="bg-slate-50 rounded-xl p-5 border border-slate-200">
                    <div class="flex justify-between items-center mb-3">
                        <span class="text-xs font-bold text-slate-800 bg-slate-200 px-2 py-0.5 rounded">현재 (2020년대)</span>
                        <span class="text-xs font-semibold text-slate-700">합계출산율 ~0.7명</span>
                    </div>
                    <h3 class="font-bold text-slate-800 mb-2">항아리형 / 종형 (저출산 진행)</h3>
                    <ul class="text-xs text-slate-600 space-y-2 leading-relaxed">
                        <li>• <strong class="text-slate-700">생산연령층 정점:</strong> 40~60대 베이비붐 세대가 두껍고, 최하단 유소년층 급감.</li>
                        <li>• <strong class="text-slate-700">저출산 주요 원인:</strong> 주거비·양육비 부담, 경쟁 심화, 개인 가치관 변화.</li>
                        <li>• <strong class="text-slate-700">고령화 가속:</strong> 기대수명 증가(83세 이상)로 고령층 비중 20% 진입 직전.</li>
                    </ul>
                </div>

                <!-- Future Student Model -->
                <div class="bg-indigo-50/50 rounded-xl p-5 border border-indigo-200/60">
                    <div class="flex justify-between items-center mb-3">
                        <span class="text-xs font-bold text-indigo-800 bg-indigo-100 px-2 py-0.5 rounded">학생 예측 미래</span>
                        <span class="text-xs font-semibold text-indigo-700">설문 기반 동적 반영</span>
                    </div>
                    <h3 class="font-bold text-slate-800 mb-2">역피라미드형 / 역종형 (초고령화)</h3>
                    <ul class="text-xs text-slate-600 space-y-2 leading-relaxed">
                        <li>• <strong class="text-slate-700">가늘어진 바닥:</strong> 학생들의 희망 자녀 수와 결혼 의향이 감소하면 최하단 부실화.</li>
                        <li>• <strong class="text-slate-700">비대해진 상단:</strong> 기대수명 90세 이상 확장 시 70~90대 비중 폭증.</li>
                        <li>• <strong class="text-slate-700">사회적 영향:</strong> 생산연령인구 1명당 부양해야 할 노인 수가 폭증하여 심각한 사회적 부담 발생.</li>
                    </ul>
                </div>
            </div>

            <!-- Discussion Questions Card -->
            <div class="mt-6 p-4 bg-slate-100/70 rounded-lg text-xs text-slate-700 space-y-2">
                <h4 class="font-bold text-sm text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-comments text-indigo-600"></i> 수업 탐구 토론 질문가이드
                </h4>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-3 pt-1">
                    <p><strong>1. 저출산 원인:</strong> 우리 반 설문 결과에서 나타난 희망 자녀 수가 과거(1970년대 4.5명)와 비교해 크게 줄어든 가장 결정적인 가치관이나 환경적 원인은 무엇인가요?</p>
                    <p><strong>2. 고령화 및 부양 부담:</strong> 시뮬레이터의 '노년 부양비' 수치를 확인해 보세요. 생산연령인구가 감당해야 할 경제적·사회적 부담을 줄이기 위해 어떤 정책이 필요할까요?</p>
                </div>
            </div>
        </section>

    </main>

    <script>
        // Age group labels from TOP (100+) to BOTTOM (0-4)
        // Chart.js horizontal bar chart displays index 0 at the TOP and last index at the BOTTOM.
        // Therefore, placing '100세 이상' at index 0 ensures 0-4세 is at the VERY BOTTOM!
        const ageLabels = [
            '100세 이상', '95-99세', '90-94세', '85-89세', '80-84세', 
            '75-79세', '70-74세', '65-69세', '60-64세', '55-59세', 
            '50-54세', '45-49세', '40-44세', '35-39세', '30-34세', 
            '25-29세', '20-24세', '15-19세', '10-14세', '5-9세', '0-4세'
        ];

        let chartInstance = null;

        // DOM elements
        const childrenInput = document.getElementById('childrenInput');
        const marriageInput = document.getElementById('marriageInput');
        const lifespanInput = document.getElementById('lifespanInput');

        const childrenVal = document.getElementById('childrenVal');
        const marriageVal = document.getElementById('marriageVal');
        const lifespanVal = document.getElementById('lifespanVal');

        /**
         * Calculate population distribution based on survey parameters
         */
        function calculatePopulation(children, marriagePct, lifespan) {
            const tfr = children * (marriagePct / 100);
            let weights = [];

            // Loop from index 0 (100+) to index 20 (0-4)
            for (let i = 0; i < ageLabels.length; i++) {
                // Calculate representative age for each index
                let age = 102.5 - (i * 5); // Index 0 -> 102.5, Index 20 -> 2.5
                let w = 0;

                if (age < 15) {
                    // Youth (0~14): Linked directly to TFR
                    w = Math.max(0.15, tfr * 2.8 * (1 + (age / 15) * 0.15));
                } else if (age < 65) {
                    // Working age (15~64): Smooth bell shape (sine wave)
                    let normalizedAge = (age - 15) / 50; // 0 to 1
                    w = 3.6 + Math.sin(normalizedAge * Math.PI) * 1.4;
                } else {
                    // Elderly (65+): Decay curve according to lifespan
                    if (age <= lifespan) {
                        let remainingRatio = (lifespan - age) / Math.max(1, (lifespan - 65));
                        w = 4.2 * Math.pow(Math.max(0, remainingRatio), 0.65) + 0.1;
                    } else {
                        // Exponential tail off past expected lifespan
                        let overAge = age - lifespan;
                        w = 0.1 * Math.exp(-overAge / 4);
                    }
                }
                weights.push(w);
            }

            // Normalize weights to 100%
            const totalWeight = weights.reduce((sum, val) => sum + val, 0);
            const malePcts = [];
            const femalePcts = [];

            let youthSum = 0;
            let workSum = 0;
            let elderSum = 0;

            weights.forEach((w, idx) => {
                let age = 102.5 - (idx * 5);
                let pct = (w / totalWeight) * 100;

                // Accumulate demographic cohorts
                if (age < 15) youthSum += pct;
                else if (age < 65) workSum += pct;
                else elderSum += pct;

                // Split Male (-) and Female (+)
                // Slight female bias in upper ages due to biological longevity
                let femaleRatio = age >= 70 ? 0.54 : 0.505;
                let maleRatio = 1 - femaleRatio;

                malePcts.push(-parseFloat((pct * maleRatio).toFixed(2)));
                femalePcts.push(parseFloat((pct * femaleRatio).toFixed(2)));
            });

            return {
                malePcts,
                femalePcts,
                tfr: tfr.toFixed(2),
                youthSum: youthSum.toFixed(1),
                workSum: workSum.toFixed(1),
                elderSum: elderSum.toFixed(1),
                oldAgeDependency: workSum > 0 ? ((elderSum / workSum) * 100).toFixed(1) : 0
            };
        }

        /**
         * Initialize or update Chart.js instance
         */
        function updatePyramidChart() {
            const children = parseFloat(childrenInput.value);
            const marriage = parseFloat(marriageInput.value);
            const lifespan = parseFloat(lifespanInput.value);

            // Update UI value labels
            childrenVal.textContent = children.toFixed(1);
            marriageVal.textContent = marriage;
            lifespanVal.textContent = lifespan;

            // Compute math
            const popData = calculatePopulation(children, marriage, lifespan);

            // Update Key Metrics Card
            document.getElementById('metricTfr').textContent = `${popData.tfr} 명`;
            document.getElementById('metricYouth').textContent = `${popData.youthSum} %`;
            document.getElementById('metricWork').textContent = `${popData.workSum} %`;
            document.getElementById('metricElder').textContent = `${popData.elderSum} %`;
            document.getElementById('metricDepend').textContent = `${popData.oldAgeDependency} 명`;

            // Classify Society Type
            const elderVal = parseFloat(popData.elderSum);
            const societyEl = document.getElementById('metricSociety');
            if (elderVal >= 20) {
                societyEl.textContent = '초고령 사회';
                societyEl.className = 'text-sm font-bold text-rose-600';
            } else if (elderVal >= 14) {
                societyEl.textContent = '고령 사회';
                societyEl.className = 'text-sm font-bold text-amber-600';
            } else if (elderVal >= 7) {
                societyEl.textContent = '고령화 사회';
                societyEl.className = 'text-sm font-bold text-yellow-600';
            } else {
                societyEl.textContent = '일반 사회';
                societyEl.className = 'text-sm font-bold text-emerald-600';
            }

            // Create or Update Chart
            if (!chartInstance) {
                const ctx = document.getElementById('pyramidCanvas').getContext('2d');
                chartInstance = new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: ageLabels,
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
                            duration: 250 // Smooth real-time update
                        },
                        scales: {
                            x: {
                                stacked: true,
                                min: -8, // FIXED RANGE to clearly show shrinkage/growth
                                max: 8,  // FIXED RANGE
                                ticks: {
                                    stepSize: 2,
                                    callback: function(value) {
                                        return Math.abs(value) + '%'; // Remove negative sign for male
                                    },
                                    font: { size: 11 }
                                },
                                title: {
                                    display: true,
                                    text: '전체 인구 대비 비율 (%)',
                                    font: { size: 12, weight: 'bold' }
                                },
                                grid: {
                                    color: (context) => context.tick.value === 0 ? '#334155' : '#e2e8f0',
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
                            legend: { display: false }, // Custom legend used in header
                            tooltip: {
                                callbacks: {
                                    label: function(context) {
                                        let label = context.dataset.label || '';
                                        let val = Math.abs(context.raw);
                                        return `${label}: ${val}%`;
                                    }
                                }
                            }
                        }
                    }
                });
            } else {
                // Real-time update chart data
                chartInstance.data.datasets[0].data = popData.malePcts;
                chartInstance.data.datasets[1].data = popData.femalePcts;
                chartInstance.update();
            }
        }

        /**
         * Preset Applicator
         */
        function applyPreset(children, marriage, lifespan) {
            childrenInput.value = children;
            marriageInput.value = marriage;
            lifespanInput.value = lifespan;
            updatePyramidChart();
        }

        // Event Listeners for real-time input sliders
        [childrenInput, marriageInput, lifespanInput].forEach(input => {
            input.addEventListener('input', updatePyramidChart);
        });

        // Initialize on page load
        window.addEventListener('DOMContentLoaded', () => {
            updatePyramidChart();
        });
    </script>
</body>
</html>
