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
                <h2 class="text-lg font-bold border-b pb-3">학습 데이터 및 커스텀 문제셋 관리</h2>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Backup & Restore -->
                    <div class="space-y-3">
                        <h3 class="font-semibold text-gray-700">기록 백업 및 복원</h3>
                        <p class="text-xs text-gray-500">로컬에 저장된 나의 오답 및 시험 기록을 JSON 파일로 백업하거나 가져올 수 있습니다.</p>
                        <div class="flex gap-2">
                            <button onclick="exportData()" class="bg-slate-800 text-white px-4 py-2 rounded-lg text-sm font-medium hover:bg-slate-700">
                                <i class="fa-solid fa-download"></i> 데이터 내보내기
                            </button>
                            <label class="bg-blue-600 text-white px-4 py-2 rounded-lg text-sm font-medium hover:bg-blue-700 cursor-pointer">
                                <i class="fa-solid fa-upload"></i> 데이터 가져오기
                                <input type="file" id="import-file" onchange="importData(event)" class="hidden" accept=".json">
                            </label>
                        </div>
                    </div>

                    <!-- Reset Data -->
                    <div class="space-y-3">
                        <h3 class="font-semibold text-red-600">초기화 옵션</h3>
                        <p class="text-xs text-gray-500">모든 학습 기록 및 오답 목록을 삭제하고 초기 상태로 되돌립니다.</p>
                        <button onclick="resetAllData()" class="bg-red-600 text-white px-4 py-2 rounded-lg text-sm font-medium hover:bg-red-700">
                            <i class="fa-solid fa-triangle-exclamation"></i> 전체 학습 데이터 초기화
                        </button>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="bg-slate-900 text-gray-400 py-6 border-t border-slate-800 text-center text-xs">
        <p>NA2-Study Network & Information Security Learning System</p>
        <p class="mt-1">기반 분석 리포지토리: github.com/sumilee-phd/NA2-study</p>
    </footer>

    <!-- JavaScript Application Logic -->
    <script>
        // Default Sample Dataset based on NA2-study Repository Syllabus
        const DEFAULT_DATASET = [
            {
                id: "q1",
                category: "이론",
                type: "choice",
                question: "OSI 7계층 중 물리적 매체를 통한 비트(Bit) 스트림 전송을 담당하는 계층은?",
                options: ["1. 데이터링크 계층", "2. 물리 계층", "3. 네트워크 계층", "4. 전송 계층"],
                answer: 1, // Index 1 = 2번
                explanation: "물리 계층(Physical Layer)은 전선, 광케이블 등 물리적 매체를 통해 구체적인 비트 단위의 전기적 신호를 전송합니다."
            },
            {
                id: "q2",
                category: "필기",
                type: "choice",
                question: "IPv4 주소 체계에서 C클래스의 기본 서브넷 마스크(Default Subnet Mask)로 올바른 것은?",
                options: ["1. 255.0.0.0", "2. 255.255.0.0", "3. 255.255.255.0", "4. 255.255.255.255"],
                answer: 2,
                explanation: "A클래스는 255.0.0.0, B클래스는 255.255.0.0, C클래스는 255.255.255.0이 기본 서브넷 마스크입니다."
            },
            {
                id: "q3",
                category: "실기",
                type: "short",
                question: "Cisco 라우터 CLI 환경에서 현재 동작 중인 활성화 설정(Running-Config)을 확인하기 위한 명령어는 무엇인가?",
                answer: "show running-config",
                explanation: "라우터의 RAM에 보관되어 실행 중인 인스턴스 설정을 출력할 때는 'show running-config' (또는 sh run) 명령어를 사용합니다."
            },
            {
                id: "q4",
                category: "이론",
                type: "choice",
                question: "3-Way Handshake 과정을 통해 신뢰성 있는 연결을 확립하는 전송 계층 프로토콜은?",
                options: ["1. UDP", "2. ICMP", "3. TCP", "4. ARP"],
                answer: 2,
                explanation: "TCP(Transmission Control Protocol)는 SYN, SYN-ACK, ACK 패킷을 주고받는 3-Way Handshake 방식으로 신뢰성 있는 세션을 생성합니다."
            },
            {
                id: "q5",
                category: "실기",
                type: "short",
                question: "IP 주소가 192.168.1.0/26 일 때, 할당 가능한 유효 서브넷 마스크의 십진수 표현은 무엇인가?",
                answer: "255.255.255.192",
                explanation: "/26은 24비트(255.255.255.0) + 2비트(128 + 64 = 192)이므로 서브넷 마스크는 255.255.255.192가 됩니다."
            }
        ];

        // Application State
        let currentQuestions = [];
        let currentIndex = 0;
        let userAnswers = {};
        let quizMode = 'PRACTICE'; // 'PRACTICE' or 'EXAM'
        let timerInterval = null;
        let timeLeft = 900; // 15 mins in seconds

        // Local Storage Keys
        const STORAGE_KEY_WRONG = 'na2_wrong_answers';
        const STORAGE_KEY_LOGS = 'na2_exam_logs';

        // Load Initial Data
        document.addEventListener("DOMContentLoaded", () => {
            initQuiz();
            updateWrongBadge();
            renderDashboard();
        });

        // Tab Switcher
        function switchTab(tabId) {
            ['dashboard', 'study', 'wrong', 'manage'].forEach(t => {
                document.getElementById(`view-${t}`).classList.add('hidden');
                document.getElementById(`tab-${t}`).classList.remove('active-tab');
            });
            document.getElementById(`view-${tabId}`).classList.remove('hidden');
            document.getElementById(`tab-${tabId}`).classList.add('active-tab');

            if (tabId === 'dashboard') renderDashboard();
            if (tabId === 'wrong') renderWrongList();
        }

        // Initialize Quiz Dataset
        function initQuiz() {
            const cat = document.getElementById('filter-category').value;
            if (cat === 'ALL') {
                currentQuestions = [...DEFAULT_DATASET];
            } else {
                currentQuestions = DEFAULT_DATASET.filter(q => q.category === cat);
            }
            currentIndex = 0;
            userAnswers = {};
            renderQuestion();
        }

        function toggleQuizMode() {
            quizMode = document.getElementById('quiz-mode').value;
            const timerBox = document.getElementById('exam-timer-box');
            const submitBtn = document.getElementById('btn-submit-exam');
            
            if (quizMode === 'EXAM') {
                timerBox.classList.remove('hidden');
                submitBtn.classList.remove('hidden');
                startTimer();
            } else {
                timerBox.classList.add('hidden');
                submitBtn.classList.add('hidden');
                clearInterval(timerInterval);
            }
            initQuiz();
        }

        function startTimer() {
            clearInterval(timerInterval);
            timeLeft = 900; // 15분
            updateTimerDisplay();
            timerInterval = setInterval(() => {
                timeLeft--;
                updateTimerDisplay();
                if (timeLeft <= 0) {
                    clearInterval(timerInterval);
                    alert("시험 시간이 종료되었습니다! 자동으로 제출합니다.");
                    submitExam();
                }
            }, 1000);
        }

        function updateTimerDisplay() {
            const m = Math.floor(timeLeft / 60).toString().padStart(2, '0');
            const s = (timeLeft % 60).toString().padStart(2, '0');
            document.getElementById('timer-display').innerText = `${m}:${s}`;
        }

        // Render Current Question
        function renderQuestion() {
            if (currentQuestions.length === 0) {
                document.getElementById('quiz-container').innerHTML = `<p class="text-center py-10 text-gray-500">해당 영역의 문제가 준비되지 않았습니다.</p>`;
                return;
            }

            const q = currentQuestions[currentIndex];
            document.getElementById('quiz-progress-text').innerText = `문제 ${currentIndex + 1} / ${currentQuestions.length}`;
            document.getElementById('quiz-badge-category').innerText = q.category;
            document.getElementById('quiz-progress-bar').style.width = `${((currentIndex + 1) / currentQuestions.length) * 100}%`;
            document.getElementById('quiz-question').innerText = `Q${currentIndex + 1}. ${q.question}`;

            const answersDiv = document.getElementById('quiz-answers');
            answersDiv.innerHTML = '';
            const expBox = document.getElementById('quiz-explanation-box');
            expBox.classList.add('hidden');

            if (q.type === 'choice') {
                q.options.forEach((opt, idx) => {
                    const btn = document.createElement('button');
                    btn.className = `w-full text-left p-4 rounded-lg border text-sm transition flex items-center justify-between ${
                        userAnswers[q.id] === idx ? 'border-blue-500 bg-blue-50 font-semibold text-blue-700' : 'border-gray-200 hover:bg-gray-50'
                    }`;
                    btn.innerText = opt;
                    btn.onclick = () => selectAnswer(idx);
                    answersDiv.appendChild(btn);
                });
            } else {
                // Short Answer
                const input = document.createElement('input');
                input.type = 'text';
                input.placeholder = '답안을 입력하세요 (예: show running-config)';
                input.className = 'w-full p-4 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500';
                input.value = userAnswers[q.id] || '';
                input.oninput = (e) => selectAnswer(e.target.value);
                answersDiv.appendChild(input);
            }

            // Buttons state
            document.getElementById('btn-prev').disabled = currentIndex === 0;
            document.getElementById('btn-next').innerText = currentIndex === currentQuestions.length - 1 ? '마지막 문제' : '다음 ';

            // If Practice mode & already answered, show explanation
            if (quizMode === 'PRACTICE' && userAnswers[q.id] !== undefined) {
                showPracticeExplanation(q);
            }
        }

        function selectAnswer(ans) {
            const q = currentQuestions[currentIndex];
            userAnswers[q.id] = ans;
            renderQuestion();

            if (quizMode === 'PRACTICE') {
                showPracticeExplanation(q);
                checkAndSaveWrong(q, ans);
            }
        }

        function showPracticeExplanation(q) {
            const expBox = document.getElementById('quiz-explanation-box');
            const expRes = document.getElementById('explanation-result');
            const expText = document.getElementById('explanation-text');
            expBox.classList.remove('hidden');

            let isCorrect = false;
            if (q.type === 'choice') {
                isCorrect = userAnswers[q.id] === q.answer;
            } else {
                isCorrect = String(userAnswers[q.id] || '').trim().toLowerCase() === String(q.answer).trim().toLowerCase();
            }

            if (isCorrect) {
                expRes.className = 'font-bold text-green-600';
                expRes.innerHTML = `<i class="fa-solid fa-circle-check"></i> 정답입니다!`;
            } else {
                expRes.className = 'font-bold text-red-600';
                expRes.innerHTML = `<i class="fa-solid fa-circle-xmark"></i> 오답입니다. (정답: ${q.type === 'choice' ? q.options[q.answer] : q.answer})`;
            }
            expText.innerText = q.explanation;
        }

        function checkAndSaveWrong(q, ans) {
            let isCorrect = false;
            if (q.type === 'choice') {
                isCorrect = ans === q.answer;
            } else {
                isCorrect = String(ans || '').trim().toLowerCase() === String(q.answer).trim().toLowerCase();
            }

            let wrongList = JSON.parse(localStorage.getItem(STORAGE_KEY_WRONG) || '[]');
            if (!isCorrect) {
                if (!wrongList.some(item => item.id === q.id)) {
                    wrongList.push(q);
                    localStorage.setItem(STORAGE_KEY_WRONG, JSON.stringify(wrongList));
                }
            }
            updateWrongBadge();
        }

        function prevQuestion() {
            if (currentIndex > 0) {
                currentIndex--;
                renderQuestion();
            }
        }

        function nextQuestion() {
            if (currentIndex < currentQuestions.length - 1) {
                currentIndex++;
                renderQuestion();
            }
        }

        function resetCurrentQuiz() {
            userAnswers = {};
            currentIndex = 0;
            renderQuestion();
        }

        // Submit Exam Mode
        function submitExam() {
            clearInterval(timerInterval);
            let score = 0;
            let correctCount = 0;

            currentQuestions.forEach(q => {
                const userAns = userAnswers[q.id];
                let isCorrect = false;
                if (q.type === 'choice') {
                    isCorrect = userAns === q.answer;
                } else {
                    isCorrect = String(userAns || '').trim().toLowerCase() === String(q.answer).trim().toLowerCase();
                }

                if (isCorrect) {
                    correctCount++;
                } else {
                    checkAndSaveWrong(q, userAns);
                }
            });

            score = Math.round((correctCount / currentQuestions.length) * 100);

            // Log Exam Result
            let logs = JSON.parse(localStorage.getItem(STORAGE_KEY_LOGS) || '[]');
            logs.push({
                date: new Date().toLocaleDateString(),
                score: score,
                total: currentQuestions.length,
                correct: correctCount
            });
            localStorage.setItem(STORAGE_KEY_LOGS, JSON.stringify(logs));

            alert(`시험이 완료되었습니다!\n점수: ${score}점 (${currentQuestions.length}문제 중 ${correctCount}개 맞춤)`);
            switchTab('dashboard');
        }

        // Update Badges & Dashboard Charts
        function updateWrongBadge() {
            const wrongList = JSON.parse(localStorage.getItem(STORAGE_KEY_WRONG) || '[]');
            document.getElementById('wrong-count-badge').innerText = wrongList.length;
        }

        function renderWrongList() {
            const wrongList = JSON.parse(localStorage.getItem(STORAGE_KEY_WRONG) || '[]');
            const container = document.getElementById('wrong-list-container');
            container.innerHTML = '';

            if (wrongList.length === 0) {
                container.innerHTML = `<p class="text-center py-12 text-gray-400">오답 기록이 없습니다. 완벽합니다!</p>`;
                return;
            }

            wrongList.forEach((q, i) => {
                const card = document.createElement('div');
                card.className = 'bg-white p-5 rounded-xl shadow-sm border border-gray-200 space-y-3';
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <span class="bg-red-100 text-red-600 text-xs px-2.5 py-1 rounded-full font-bold">${q.category}</span>
                        <button onclick="removeWrong('${q.id}')" class="text-gray-400 hover:text-red-500 text-sm"><i class="fa-solid fa-xmark"></i> 삭제</button>
                    </div>
                    <h3 class="font-bold text-gray-800">Q. ${q.question}</h3>
                    <p class="text-sm text-green-700 bg-green-50 p-3 rounded-lg"><strong>정답:</strong> ${q.type === 'choice' ? q.options[q.answer] : q.answer}</p>
                    <p class="text-xs text-gray-600 bg-gray-50 p-3 rounded-lg"><strong>해설:</strong> ${q.explanation}</p>
                `;
                container.appendChild(card);
            });
        }

        function removeWrong(id) {
            let wrongList = JSON.parse(localStorage.getItem(STORAGE_KEY_WRONG) || '[]');
            wrongList = wrongList.filter(item => item.id !== id);
            localStorage.setItem(STORAGE_KEY_WRONG, JSON.stringify(wrongList));
            updateWrongBadge();
            renderWrongList();
        }

        function clearWrongList() {
            if (confirm("모든 오답 기록을 삭제하시겠습니까?")) {
                localStorage.removeItem(STORAGE_KEY_WRONG);
                updateWrongBadge();
                renderWrongList();
            }
        }

        // Render Analytics & Charts
        let categoryChartInstance = null;
        let trendChartInstance = null;

        function renderDashboard() {
            const logs = JSON.parse(localStorage.getItem(STORAGE_KEY_LOGS) || '[]');
            const wrongList = JSON.parse(localStorage.getItem(STORAGE_KEY_WRONG) || '[]');

            document.getElementById('stat-total-attempts').innerText = `${logs.length} 회`;
            document.getElementById('stat-wrong-count').innerText = `${wrongList.length} 개`;

            const avgScore = logs.length > 0 ? Math.round(logs.reduce((acc, curr) => acc + curr.score, 0) / logs.length) : 0;
            document.getElementById('stat-accuracy').innerText = `${avgScore}%`;

            const maxScore = logs.length > 0 ? Math.max(...logs.map(l => l.score)) : 0;
            document.getElementById('stat-max-score').innerText = `${maxScore} 점`;

            // AI Feedback Logic
            const feedbackEl = document.getElementById('ai-feedback-text');
            if (logs.length === 0) {
                feedbackEl.innerText = "아직 시험 참여 기록이 없습니다. 문제 풀이 모드에서 연습을 진행하시면 맞춤형 분석이 제공됩니다.";
            } else if (avgScore >= 80) {
                feedbackEl.innerText = `현재 평균 점수는 ${avgScore}점으로 상위권입니다! 실기 명령어를 반복 연습하여 실전을 대비하세요.`;
            } else {
                feedbackEl.innerText = `현재 평균 점수는 ${avgScore}점입니다. 오답 노트를 기반으로 취약 영역 개념 정리를 권장합니다.`;
            }

            // Render Radar Chart
            const ctxCat = document.getElementById('categoryChart').getContext('2d');
            if (categoryChartInstance) categoryChartInstance.destroy();
            categoryChartInstance = new Chart(ctxCat, {
                type: 'radar',
                data: {
                    labels: ['이론 (Theory)', '필기 (Written)', '실기 (Practical)'],
                    datasets: [{
                        label: '영역별 이해도 (%)',
                        data: [85, 70, 60],
                        backgroundColor: 'rgba(59, 130, 246, 0.2)',
                        borderColor: '#3b82f6',
                        pointBackgroundColor: '#3b82f6'
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false }
            });

            // Render Trend Chart
            const ctxTrend = document.getElementById('trendChart').getContext('2d');
            if (trendChartInstance) trendChartInstance.destroy();
            
            const labels = logs.map((_, i) => `${i + 1}회차`);
            const scores = logs.map(l => l.score);

            trendChartInstance = new Chart(ctxTrend, {
                type: 'line',
                data: {
                    labels: labels.length > 0 ? labels : ['1회차', '2회차', '3회차'],
                    datasets: [{
                        label: '모의고사 점수 변화',
                        data: scores.length > 0 ? scores : [0, 0, 0],
                        borderColor: '#10b981',
                        backgroundColor: 'rgba(16, 185, 129, 0.1)',
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: { y: { min: 0, max: 100 } }
                }
            });
        }

        // Data Import/Export
        function exportData() {
            const data = {
                wrong: JSON.parse(localStorage.getItem(STORAGE_KEY_WRONG) || '[]'),
                logs: JSON.parse(localStorage.getItem(STORAGE_KEY_LOGS) || '[]')
            };
            const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'na2_study_backup.json';
            a.click();
        }

        function importData(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const data = JSON.parse(e.target.result);
                    if (data.wrong) localStorage.setItem(STORAGE_KEY_WRONG, JSON.stringify(data.wrong));
                    if (data.logs) localStorage.setItem(STORAGE_KEY_LOGS, JSON.stringify(data.logs));
                    alert("데이터 복원이 완료되었습니다!");
                    location.reload();
                } catch (err) {
                    alert("올바르지 않은 JSON 파일 형식입니다.");
                }
            };
            reader.readAsText(file);
        }

        function resetAllData() {
            if (confirm("정말로 모든 학습 기록과 오답 데이터를 삭제하시겠습니까?")) {
                localStorage.removeItem(STORAGE_KEY_WRONG);
                localStorage.removeItem(STORAGE_KEY_LOGS);
                alert("초기화가 완료되었습니다.");
                location.reload();
            }
        }
    </script>
</body>
</html>
