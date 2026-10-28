<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NA2-Study 스마트 학습 & 시험 분석 시스템</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        .active-tab {
            border-bottom: 3px solid #3b82f6;
            color: #3b82f6;
            font-weight: bold;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen text-gray-800 flex flex-col font-sans">

    <!-- Header & Navigation -->
    <header class="bg-slate-900 text-white shadow-lg sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-4 flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-network-wired text-blue-400 text-2xl"></i>
                <h1 class="text-xl font-bold tracking-wide">NA2-Study 스마트 학습 시스템</h1>
            </div>
            
            <nav class="flex gap-2 overflow-x-auto w-full md:w-auto">
                <button onclick="switchTab('dashboard')" id="tab-dashboard" class="px-4 py-2 rounded-lg text-sm transition hover:bg-slate-800 flex items-center gap-2 active-tab">
                    <i class="fa-solid fa-chart-line"></i> 대시보드
                </button>
                <button onclick="switchTab('study')" id="tab-study" class="px-4 py-2 rounded-lg text-sm transition hover:bg-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-book-open"></i> 문제 풀이
                </button>
                <button onclick="switchTab('wrong')" id="tab-wrong" class="px-4 py-2 rounded-lg text-sm transition hover:bg-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-triangle-exclamation text-yellow-400"></i> 오답 노트
                    <span id="wrong-count-badge" class="bg-red-500 text-white text-xs px-2 py-0.5 rounded-full">0</span>
                </button>
                <button onclick="switchTab('manage')" id="tab-manage" class="px-4 py-2 rounded-lg text-sm transition hover:bg-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-database"></i> 데이터 관리
                </button>
            </nav>
        </div>
    </header>

    <!-- Main Content Container -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full">

        <!-- SECTION 1: DASHBOARD -->
        <section id="view-dashboard" class="space-y-6">
            <!-- Summary Stats Cards -->
            <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 flex items-center gap-4">
                    <div class="p-3 bg-blue-100 text-blue-600 rounded-lg"><i class="fa-solid fa-graduation-cap text-2xl"></i></div>
                    <div>
                        <p class="text-xs text-gray-500 uppercase font-semibold">전체 학습 시도</p>
                        <h3 id="stat-total-attempts" class="text-2xl font-bold">0 회</h3>
                    </div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 flex items-center gap-4">
                    <div class="p-3 bg-green-100 text-green-600 rounded-lg"><i class="fa-solid fa-circle-check text-2xl"></i></div>
                    <div>
                        <p class="text-xs text-gray-500 uppercase font-semibold">평균 정답률</p>
                        <h3 id="stat-accuracy" class="text-2xl font-bold">0%</h3>
                    </div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 flex items-center gap-4">
                    <div class="p-3 bg-yellow-100 text-yellow-600 rounded-lg"><i class="fa-solid fa-bookmark text-2xl"></i></div>
                    <div>
                        <p class="text-xs text-gray-500 uppercase font-semibold">복습 필요 오답</p>
                        <h3 id="stat-wrong-count" class="text-2xl font-bold">0 개</h3>
                    </div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 flex items-center gap-4">
                    <div class="p-3 bg-purple-100 text-purple-600 rounded-lg"><i class="fa-solid fa-award text-2xl"></i></div>
                    <div>
                        <p class="text-xs text-gray-500 uppercase font-semibold">최고 모의고사 점수</p>
                        <h3 id="stat-max-score" class="text-2xl font-bold">0 점</h3>
                    </div>
                </div>
            </div>

            <!-- Charts & AI Feedback -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Accuracy Radar Chart -->
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200">
                    <h2 class="text-base font-bold mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-chart-pie text-blue-500"></i> 영역별 학습 달성도
                    </h2>
                    <div class="h-64 flex justify-center items-center">
                        <canvas id="categoryChart"></canvas>
                    </div>
                </div>

                <!-- Score Trend Line Chart -->
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 lg:col-span-2">
                    <h2 class="text-base font-bold mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-chart-line text-green-500"></i> 모의고사 점수 변화 추이
                    </h2>
                    <div class="h-64">
                        <canvas id="trendChart"></canvas>
                    </div>
                </div>
            </div>

            <!-- AI Weakness Analysis Box -->
            <div class="bg-blue-50 border border-blue-200 p-5 rounded-xl flex items-start gap-4">
                <div class="p-3 bg-blue-500 text-white rounded-lg mt-1"><i class="fa-solid fa-brain text-xl"></i></div>
                <div>
                    <h3 class="font-bold text-blue-900 text-lg mb-1">스마트 학습 리포트 & 분석 피드백</h3>
                    <p id="ai-feedback-text" class="text-blue-800 text-sm leading-relaxed">
                        아직 충분한 학습 데이터가 쌓이지 않았습니다. 문제 풀이 모드에서 연습 및 모의고사를 진행해보세요!
                    </p>
                </div>
            </div>
        </section>

        <!-- SECTION 2: STUDY MODE -->
        <section id="view-study" class="hidden space-y-6">
            <!-- Filter & Setup Control Bar -->
            <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 flex flex-wrap gap-4 justify-between items-center">
                <div class="flex flex-wrap gap-3 items-center">
                    <div>
                        <label class="block text-xs font-semibold text-gray-500 mb-1">영역 선택</label>
                        <select id="filter-category" onchange="initQuiz()" class="border border-gray-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                            <option value="ALL">전체 영역 (이론/필기/실기)</option>
                            <option value="이론">이론 (Theory)</option>
                            <option value="필기">필기 (Written)</option>
                            <option value="실기">실기 (Practical)</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-500 mb-1">학습 모드</label>
                        <select id="quiz-mode" onchange="toggleQuizMode()" class="border border-gray-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                            <option value="PRACTICE">연습 모드 (즉시 정답확인)</option>
                            <option value="EXAM">모의고사 모드 (타이머 + 최종 채점)</option>
                        </select>
                    </div>
                </div>

                <div class="flex items-center gap-3">
                    <div id="exam-timer-box" class="hidden bg-red-50 border border-red-200 text-red-600 px-4 py-2 rounded-lg font-mono font-bold flex items-center gap-2">
                        <i class="fa-solid fa-stopwatch"></i> <span id="timer-display">15:00</span>
                    </div>
                    <button onclick="resetCurrentQuiz()" class="bg-gray-100 hover:bg-gray-200 text-gray-700 px-4 py-2 rounded-lg text-sm font-semibold transition">
                        <i class="fa-solid fa-rotate-right"></i> 다시 시작
                    </button>
                </div>
            </div>

            <!-- Quiz Card Container -->
            <div id="quiz-container" class="bg-white p-6 md:p-8 rounded-xl shadow-sm border border-gray-200 space-y-6">
                <!-- Quiz Progress Bar -->
                <div class="flex justify-between items-center text-sm font-semibold text-gray-500">
                    <span id="quiz-progress-text">문제 1 / 10</span>
                    <span id="quiz-badge-category" class="bg-blue-100 text-blue-700 px-3 py-1 rounded-full text-xs font-bold">필기</span>
                </div>
                <div class="w-full bg-gray-200 rounded-full h-2">
                    <div id="quiz-progress-bar" class="bg-blue-600 h-2 rounded-full transition-all duration-300" style="width: 10%"></div>
                </div>

                <!-- Question Text -->
                <div>
                    <h2 id="quiz-question" class="text-lg md:text-xl font-bold text-gray-800 leading-snug">
                        문제를 불러오는 중입니다...
                    </h2>
                </div>

                <!-- Answer Area (Dynamic Choice or Input) -->
                <div id="quiz-answers" class="space-y-3">
                    <!-- Dynamic rendering via JS -->
                </div>

                <!-- Feedback / Explanation (Practice Mode) -->
                <div id="quiz-explanation-box" class="hidden p-4 rounded-lg bg-slate-50 border border-slate-200 space-y-2">
                    <div id="explanation-result" class="font-bold text-base"></div>
                    <p id="explanation-text" class="text-sm text-gray-700 leading-relaxed"></p>
                </div>

                <!-- Navigation Controls -->
                <div class="flex justify-between items-center pt-4 border-t border-gray-100">
                    <button id="btn-prev" onclick="prevQuestion()" class="px-5 py-2.5 bg-gray-100 hover:bg-gray-200 rounded-lg text-sm font-semibold text-gray-700 disabled:opacity-40">
                        <i class="fa-solid fa-arrow-left"></i> 이전
                    </button>
                    <button id="btn-submit-exam" onclick="submitExam()" class="hidden px-6 py-2.5 bg-green-600 hover:bg-green-700 text-white rounded-lg text-sm font-semibold shadow transition">
                        <i class="fa-solid fa-check"></i> 최종 제출 및 채점
                    </button>
                    <button id="btn-next" onclick="nextQuestion()" class="px-5 py-2.5 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm font-semibold transition shadow">
                        다음 <i class="fa-solid fa-arrow-right"></i>
                    </button>
                </div>
            </div>
        </section>

        <!-- SECTION 3: WRONG ANSWER REVIEW -->
        <section id="view-wrong" class="hidden space-y-6">
            <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 flex justify-between items-center">
                <div>
                    <h2 class="text-lg font-bold">오답 노트 목록</h2>
                    <p class="text-sm text-gray-500">틀린 문제를 다시 풀어보고 취약점을 완벽하게 보완하세요.</p>
                </div>
                <button onclick="clearWrongList()" class="bg-red-50 hover:bg-red-100 text-red-600 px-4 py-2 rounded-lg text-sm font-semibold transition border border-red-200">
                    <i class="fa-solid fa-trash-can"></i> 전체 비우기
                </button>
            </div>

            <div id="wrong-list-container" class="space-y-4">
                <!-- Dynamic rendering -->
            </div>
        </section>

        <!-- SECTION 4: DATA MANAGEMENT -->
        <section id="view-manage" class="hidden space-y-6">
            <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-200 space-y-4">
