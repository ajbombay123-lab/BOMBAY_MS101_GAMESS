<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Math Genius - Arcade Edition</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts: Orbitron & Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&family=Orbitron:wght@600;800;900&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        arcade: ['Orbitron', 'sans-serif'],
                    },
                    colors: {
                        arcadeDark: '#0f172a',
                        arcadeCard: '#1e293b',
                        neonPink: '#ec4899',
                        neonCyan: '#06b6d4',
                        neonYellow: '#f59e0b',
                        neonGreen: '#10b981',
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background: radial-gradient(circle at center, #1e1b4b 0%, #0f172a 100%);
            min-height: 100vh;
            color: #f8fafc;
            user-select: none;
            overflow-x: hidden;
        }

        .glow-box {
            box-shadow: 0 0 25px rgba(6, 182, 212, 0.3);
            border: 2px solid rgba(6, 182, 212, 0.5);
        }

        .glow-pink {
            box-shadow: 0 0 25px rgba(236, 72, 153, 0.4);
            border: 2px solid rgba(236, 72, 153, 0.6);
        }

        .shake {
            animation: shake 0.4s cubic-bezier(.36,.07,.19,.97) both;
        }

        @keyframes shake {
            10%, 90% { transform: translate3d(-2px, 0, 0); }
            20%, 80% { transform: translate3d(4px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-6px, 0, 0); }
            40%, 60% { transform: translate3d(6px, 0, 0); }
        }

        .pop-in {
            animation: popIn 0.3s ease-out forwards;
        }

        @keyframes popIn {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }

        .bg-grid {
            background-size: 40px 40px;
            background-image: 
                linear-gradient(to right, rgba(255, 255, 255, 0.03) 1px, transparent 1px),
                linear-gradient(to bottom, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
        }

        /* Particle Canvas Overlay */
        #particleCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 50;
        }
    </style>
</head>
<body class="bg-grid font-sans flex flex-col justify-between min-h-screen relative">

    <canvas id="particleCanvas"></canvas>

    <!-- Header / Navbar -->
    <header class="p-4 w-full max-w-4xl mx-auto flex justify-between items-center border-b border-slate-800">
        <div class="flex items-center space-x-3 cursor-pointer" onclick="showScreen('menu')">
            <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-cyan-500 to-indigo-500 flex items-center justify-center text-xl font-arcade shadow-lg">
                <i class="fa-solid font-bold">÷</i>
            </div>
            <div>
                <h1 class="font-arcade text-xl sm:text-2xl font-black bg-clip-text text-transparent bg-gradient-to-r from-cyan-400 via-pink-500 to-amber-400 tracking-wider">
                    MATH GENIUS
                </h1>
                <p class="text-xs text-slate-400 tracking-widest uppercase">Arcade Math Edition</p>
            </div>
        </div>
        
        <button id="soundToggleBtn" onclick="toggleSound()" class="p-3 bg-slate-800/80 hover:bg-slate-700 rounded-xl text-slate-300 transition-all border border-slate-700">
            <i id="soundIcon" class="fa-solid fa-volume-high"></i>
        </button>
    </header>

    <!-- Main Container -->
    <main class="flex-grow flex items-center justify-center p-4">
        
        <!-- SCREEN 1: MAIN MENU -->
        <section id="menuScreen" class="w-full max-w-lg bg-arcadeCard/90 backdrop-blur-md p-6 sm:p-8 rounded-3xl glow-box border border-slate-700 text-center pop-in">
            <div class="mb-6">
                <span class="px-4 py-1.5 rounded-full text-xs font-semibold bg-indigo-500/20 text-indigo-300 border border-indigo-500/30">
                    <i class="fa-solid fa-gamepad mr-1"></i> Test Your Math Skills!
                </span>
                <h2 class="text-3xl font-arcade font-bold mt-3 text-white">SELECT MODE</h2>
                <p class="text-slate-400 text-sm mt-1">Answer questions before time runs out!</p>
            </div>

            <!-- Operations Grid -->
            <div class="grid grid-cols-2 gap-3 mb-6">
                <button onclick="selectOperation('add')" class="op-btn p-4 rounded-2xl bg-slate-800/80 hover:bg-cyan-600/30 border border-slate-700 hover:border-cyan-400 flex flex-col items-center justify-center group transition-all">
                    <i class="fa-solid fa-plus text-2xl text-cyan-400 group-hover:scale-125 transition-transform"></i>
                    <span class="mt-2 text-sm font-semibold text-slate-200">Addition (+)</span>
                </button>
                <button onclick="selectOperation('subtract')" class="op-btn p-4 rounded-2xl bg-slate-800/80 hover:bg-pink-600/30 border border-slate-700 hover:border-pink-400 flex flex-col items-center justify-center group transition-all">
                    <i class="fa-solid fa-minus text-2xl text-pink-400 group-hover:scale-125 transition-transform"></i>
                    <span class="mt-2 text-sm font-semibold text-slate-200">Subtraction (-)</span>
                </button>
                <button onclick="selectOperation('multiply')" class="op-btn p-4 rounded-2xl bg-slate-800/80 hover:bg-amber-600/30 border border-slate-700 hover:border-amber-400 flex flex-col items-center justify-center group transition-all">
                    <i class="fa-solid fa-xmark text-2xl text-amber-400 group-hover:scale-125 transition-transform"></i>
                    <span class="mt-2 text-sm font-semibold text-slate-200">Multiplication (×)</span>
                </button>
                <button onclick="selectOperation('divide')" class="op-btn p-4 rounded-2xl bg-slate-800/80 hover:bg-emerald-600/30 border border-slate-700 hover:border-emerald-400 flex flex-col items-center justify-center group transition-all">
                    <i class="fa-solid fa-divide text-2xl text-emerald-400 group-hover:scale-125 transition-transform"></i>
                    <span class="mt-2 text-sm font-semibold text-slate-200">Division (÷)</span>
                </button>
            </div>

            <!-- Mixed Mode Button -->
            <button onclick="selectOperation('mixed')" class="w-full mb-6 p-4 rounded-2xl bg-gradient-to-r from-purple-600/40 to-pink-600/40 hover:from-purple-600/60 hover:to-pink-600/60 border border-purple-500/50 flex items-center justify-center space-x-3 transition-all group">
                <i class="fa-solid fa-bolt text-xl text-yellow-300 group-hover:rotate-12 transition-transform"></i>
                <span class="font-arcade font-bold text-white tracking-wide">MIXED CHALLENGE MODE 🔥</span>
            </button>

            <!-- Difficulty Selector -->
            <div class="mb-6">
                <label class="block text-xs text-slate-400 font-semibold mb-2 uppercase tracking-wider">Difficulty Level:</label>
                <div class="grid grid-cols-3 gap-2 bg-slate-900/60 p-1.5 rounded-xl border border-slate-800">
                    <button id="diff-easy" onclick="setDifficulty('easy')" class="diff-btn py-2 text-xs font-bold rounded-lg text-emerald-400 bg-emerald-900/40 border border-emerald-500/40">EASY</button>
                    <button id="diff-medium" onclick="setDifficulty('medium')" class="diff-btn py-2 text-xs font-bold rounded-lg text-slate-400 hover:text-white">MEDIUM</button>
                    <button id="diff-hard" onclick="setDifficulty('hard')" class="diff-btn py-2 text-xs font-bold rounded-lg text-slate-400 hover:text-white">HARD</button>
                </div>
            </div>

            <!-- Start Game Button -->
            <button onclick="startGame()" class="w-full py-4 rounded-2xl bg-gradient-to-r from-cyan-500 to-blue-600 hover:from-cyan-400 hover:to-blue-500 font-arcade font-black text-lg text-slate-950 tracking-wider shadow-lg shadow-cyan-500/30 transform active:scale-95 transition-all">
                START GAME <i class="fa-solid fa-play ml-2"></i>
            </button>

            <!-- High Score Display -->
            <div class="mt-6 pt-4 border-t border-slate-800 flex justify-between items-center text-xs text-slate-400">
                <span><i class="fa-solid fa-trophy text-amber-400 mr-1"></i> High Score:</span>
                <span id="menuHighScore" class="font-arcade text-amber-400 font-bold text-sm">0</span>
            </div>
        </section>

        <!-- SCREEN 2: GAMEPLAY -->
        <section id="gameScreen" class="hidden w-full max-w-lg bg-arcadeCard/90 backdrop-blur-md p-6 sm:p-8 rounded-3xl glow-box border border-slate-700 text-center pop-in relative">
            
            <!-- Top Game Stats Bar -->
            <div class="flex justify-between items-center mb-6">
                <!-- Score -->
                <div class="text-left">
                    <span class="text-xs text-slate-400 font-semibold block uppercase">Score</span>
                    <span id="currentScore" class="font-arcade text-2xl font-black text-cyan-400">0</span>
                </div>

                <!-- Streak / Multiplier Badge -->
                <div id="streakBadge" class="hidden px-3 py-1 bg-amber-500/20 border border-amber-500/40 rounded-full animate-pulse">
                    <span class="text-xs font-bold text-amber-300">
                        🔥 STREAK: <span id="streakCount">0</span>x
                    </span>
                </div>

                <!-- Timer Counter -->
                <div class="text-right">
                    <span class="text-xs text-slate-400 font-semibold block uppercase">Time</span>
                    <span id="timerText" class="font-arcade text-2xl font-black text-pink-500">15s</span>
                </div>
            </div>

            <!-- Visual Progress Bar for Timer -->
            <div class="w-full bg-slate-800 rounded-full h-3 mb-8 overflow-hidden border border-slate-700 p-0.5">
                <div id="timerBar" class="bg-gradient-to-r from-pink-500 to-rose-500 h-full rounded-full w-full transition-all duration-100 linear"></div>
            </div>

            <!-- Problem Card -->
            <div id="problemCard" class="bg-slate-900/80 p-8 rounded-2xl border border-slate-800 mb-8 shadow-inner flex flex-col items-center justify-center min-h-[140px] relative">
                <span id="operationTag" class="absolute top-3 left-4 text-xs font-bold text-slate-500 uppercase tracking-widest">ADDITION</span>
                <div id="mathQuestion" class="font-arcade text-4xl sm:text-5xl font-black tracking-wider text-white">
                    12 + 8 = ?
                </div>
            </div>

            <!-- Choice Buttons Grid -->
            <div class="grid grid-cols-2 gap-4" id="choicesContainer">
                <button onclick="checkAnswer(0)" class="choice-btn py-5 px-4 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-2xl font-arcade font-bold text-slate-100 transition-all active:scale-95">
                    15
                </button>
                <button onclick="checkAnswer(1)" class="choice-btn py-5 px-4 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-2xl font-arcade font-bold text-slate-100 transition-all active:scale-95">
                    20
                </button>
                <button onclick="checkAnswer(2)" class="choice-btn py-5 px-4 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-2xl font-arcade font-bold text-slate-100 transition-all active:scale-95">
                    18
                </button>
                <button onclick="checkAnswer(3)" class="choice-btn py-5 px-4 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-2xl font-arcade font-bold text-slate-100 transition-all active:scale-95">
                    22
                </button>
            </div>

            <!-- Bottom Quit / Pause Option -->
            <div class="mt-8 text-center">
                <button onclick="quitGame()" class="text-xs text-slate-500 hover:text-slate-300 underline font-semibold">
                    <i class="fa-solid fa-arrow-left mr-1"></i> Back to Menu
                </button>
            </div>
        </section>

        <!-- SCREEN 3: GAME OVER -->
        <section id="gameOverScreen" class="hidden w-full max-w-lg bg-arcadeCard/90 backdrop-blur-md p-6 sm:p-8 rounded-3xl glow-pink border border-slate-700 text-center pop-in">
            <div class="mb-6">
                <div class="w-16 h-16 mx-auto mb-3 rounded-2xl bg-pink-500/20 text-pink-500 border border-pink-500/30 flex items-center justify-center text-3xl">
                    <i class="fa-solid fa-flag-checkered"></i>
                </div>
                <h2 class="text-3xl font-arcade font-black text-white">GAME OVER!</h2>
                <p class="text-slate-400 text-sm mt-1">Great effort! Here is your final score:</p>
            </div>

            <!-- New High Score Banner -->
            <div id="newHighScoreBadge" class="hidden mb-6 p-3 rounded-xl bg-amber-500/20 border border-amber-500/50 text-amber-300 font-arcade text-sm font-bold animate-bounce">
                🎉 NEW HIGH SCORE! CONGRATS! 🎉
            </div>

            <!-- Stats Summary -->
            <div class="bg-slate-900/70 p-5 rounded-2xl border border-slate-800 mb-6 space-y-4">
                <div class="flex justify-between items-center text-sm">
                    <span class="text-slate-400">Total Score:</span>
                    <span id="finalScore" class="font-arcade font-bold text-2xl text-cyan-400">0</span>
                </div>
                <div class="flex justify-between items-center text-sm border-t border-slate-800 pt-3">
                    <span class="text-slate-400">Best Combo Streak:</span>
                    <span id="finalStreak" class="font-arcade font-bold text-lg text-amber-400">0x</span>
                </div>
                <div class="flex justify-between items-center text-sm border-t border-slate-800 pt-3">
                    <span class="text-slate-400">Accuracy:</span>
                    <span id="finalAccuracy" class="font-arcade font-bold text-lg text-emerald-400">0%</span>
                </div>
            </div>

            <!-- Action Buttons -->
            <div class="space-y-3">
                <button onclick="startGame()" class="w-full py-4 rounded-2xl bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 font-arcade font-black text-lg text-slate-950 tracking-wider shadow-lg shadow-emerald-500/20 active:scale-95 transition-all">
                    PLAY AGAIN <i class="fa-solid fa-rotate-right ml-2"></i>
                </button>
                <button onclick="showScreen('menu')" class="w-full py-3 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold text-slate-300 text-sm transition-all">
                    MAIN MENU
                </button>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="p-4 text-center text-xs text-slate-600 border-t border-slate-800/50">
        Math Genius Arcade &copy; 2026 - Fun Math Practice
    </footer>

    <script>
        /* Modern Web Audio Synthesizer Engine (Zero External Audio Files Needed) */
        let soundEnabled = true;
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
        }

        function playSound(type) {
            if (!soundEnabled) return;
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                const now = audioCtx.currentTime;

                if (type === 'correct') {
                    // Upward Arpeggio Sound
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(523.25, now); // C5
                    osc.frequency.exponentialRampToValueAtTime(880, now + 0.15); // A5
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.15);
                    osc.start(now);
                    osc.stop(now + 0.15);
                } else if (type === 'wrong') {
                    // Low Buzz Error Sound
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(150, now);
                    osc.frequency.linearRampToValueAtTime(80, now + 0.25);
                    gain.gain.setValueAtTime(0.3, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
                    osc.start(now);
                    osc.stop(now + 0.25);
                } else if (type === 'click') {
                    // Subtle UI Click Sound
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(400, now);
                    gain.gain.setValueAtTime(0.05, now);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.05);
                    osc.start(now);
                    osc.stop(now + 0.05);
                } else if (type === 'gameover') {
                    // Sad Descending Sound
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(300, now);
                    osc.frequency.exponentialRampToValueAtTime(100, now + 0.5);
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.5);
                    osc.start(now);
                    osc.stop(now + 0.5);
                } else if (type === 'highScore') {
                    // Fanfare synth sound
                    osc.type = 'square';
                    osc.frequency.setValueAtTime(587.33, now); // D5
                    osc.frequency.setValueAtTime(880, now + 0.1); // A5
                    gain.gain.setValueAtTime(0.15, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                    osc.start(now);
                    osc.stop(now + 0.4);
                }
            } catch (e) {
                console.warn('Audio playback error:', e);
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            const soundIcon = document.getElementById('soundIcon');
            if (soundEnabled) {
                soundIcon.className = 'fa-solid fa-volume-high';
            } else {
                soundIcon.className = 'fa-solid fa-volume-xmark text-rose-400';
            }
        }

        // Game State Variables
        let currentOperation = 'add';
        let currentDifficulty = 'easy';
        let score = 0;
        let streak = 0;
        let maxStreak = 0;
        let totalQuestions = 0;
        let correctAnswersCount = 0;
        let currentCorrectAnswer = 0;
        let timer = null;
        let timeLeft = 15;
        let totalTimePerQuestion = 15;
        let isAnswering = false;

        // Initialize High Score from LocalStorage
        let highScore = parseInt(localStorage.getItem('math_genius_highscore') || '0');
        document.getElementById('menuHighScore').innerText = highScore;

        function setDifficulty(diff) {
            playSound('click');
            currentDifficulty = diff;
            const diffBtns = ['easy', 'medium', 'hard'];
            
            diffBtns.forEach(d => {
                const btn = document.getElementById(`diff-${d}`);
                if (d === diff) {
                    btn.className = `diff-btn py-2 text-xs font-bold rounded-lg ${
                        d === 'easy' ? 'text-emerald-400 bg-emerald-900/40 border border-emerald-500/40' :
                        d === 'medium' ? 'text-amber-400 bg-amber-900/40 border border-amber-500/40' :
                        'text-rose-400 bg-rose-900/40 border border-rose-500/40'
                    }`;
                } else {
                    btn.className = 'diff-btn py-2 text-xs font-bold rounded-lg text-slate-400 hover:text-white';
                }
            });

            // Set time based on difficulty
            if (diff === 'easy') totalTimePerQuestion = 15;
            else if (diff === 'medium') totalTimePerQuestion = 10;
            else if (diff === 'hard') totalTimePerQuestion = 7;
        }

        function selectOperation(op) {
            playSound('click');
            currentOperation = op;
            const buttons = document.querySelectorAll('.op-btn');
            buttons.forEach(btn => btn.classList.remove('ring-2', 'ring-cyan-400'));
            
            if (event && event.currentTarget) {
                event.currentTarget.classList.add('ring-2', 'ring-cyan-400');
            }
        }

        function showScreen(screenName) {
            playSound('click');
            document.getElementById('menuScreen').classList.add('hidden');
            document.getElementById('gameScreen').classList.add('hidden');
            document.getElementById('gameOverScreen').classList.add('hidden');

            if (screenName === 'menu') {
                document.getElementById('menuScreen').classList.remove('hidden');
                document.getElementById('menuHighScore').innerText = highScore;
            } else if (screenName === 'game') {
                document.getElementById('gameScreen').classList.remove('hidden');
            } else if (screenName === 'gameOver') {
                document.getElementById('gameOverScreen').classList.remove('hidden');
            }
        }

        function getRandomNumber(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function generateQuestion() {
            let num1, num2, answer, symbol, tagText;
            let op = currentOperation;

            if (op === 'mixed') {
                const ops = ['add', 'subtract', 'multiply', 'divide'];
                op = ops[Math.floor(Math.random() * ops.length)];
            }

            // Difficulty Multipliers
            let maxNum = 10;
            if (currentDifficulty === 'medium') maxNum = 30;
            if (currentDifficulty === 'hard') maxNum = 100;

            if (op === 'add') {
                num1 = getRandomNumber(1, maxNum);
                num2 = getRandomNumber(1, maxNum);
                answer = num1 + num2;
                symbol = '+';
                tagText = 'ADDITION';
            } else if (op === 'subtract') {
                num1 = getRandomNumber(1, maxNum);
                num2 = getRandomNumber(1, num1); // Ensure non-negative result
                answer = num1 - num2;
                symbol = '-';
                tagText = 'SUBTRACTION';
            } else if (op === 'multiply') {
                let multMax = currentDifficulty === 'easy' ? 10 : (currentDifficulty === 'medium' ? 12 : 20);
                num1 = getRandomNumber(1, multMax);
                num2 = getRandomNumber(1, multMax);
                answer = num1 * num2;
                symbol = '×';
                tagText = 'MULTIPLICATION';
            } else if (op === 'divide') {
                let divMax = currentDifficulty === 'easy' ? 10 : (currentDifficulty === 'medium' ? 12 : 15);
                num2 = getRandomNumber(1, divMax); // Divisor
                answer = getRandomNumber(1, divMax); // Quotient
                num1 = num2 * answer; // Dividend
                symbol = '÷';
                tagText = 'DIVISION';
            }

            currentCorrectAnswer = answer;
            document.getElementById('operationTag').innerText = tagText;
            document.getElementById('mathQuestion').innerText = `${num1} ${symbol} ${num2} = ?`;

            // Generate choices (1 correct, 3 wrong distractors)
            let choices = [answer];
            while (choices.length < 4) {
                let delta = getRandomNumber(1, 5) * (Math.random() < 0.5 ? 1 : -1);
                let wrongChoice = answer + delta;
                if (wrongChoice >= 0 && !choices.includes(wrongChoice)) {
                    choices.push(wrongChoice);
                }
            }

            // Shuffle Choices
            choices.sort(() => Math.random() - 0.5);

            // Render Choices to Buttons
            const buttons = document.querySelectorAll('.choice-btn');
            buttons.forEach((btn, idx) => {
                btn.innerText = choices[idx];
                btn.dataset.value = choices[idx];
                btn.className = 'choice-btn py-5 px-4 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-2xl font-arcade font-bold text-slate-100 transition-all active:scale-95';
            });
        }

        function startGame() {
            score = 0;
            streak = 0;
            maxStreak = 0;
            totalQuestions = 0;
            correctAnswersCount = 0;
            
            document.getElementById('currentScore').innerText = '0';
            document.getElementById('streakCount').innerText = '0';
            document.getElementById('streakBadge').classList.add('hidden');

            showScreen('game');
            nextQuestion();
        }

        function nextQuestion() {
            isAnswering = false;
            generateQuestion();
            resetTimer();
        }

        function resetTimer() {
            clearInterval(timer);
            timeLeft = totalTimePerQuestion;
            updateTimerDisplay();

            timer = setInterval(() => {
                timeLeft -= 0.1;
                updateTimerDisplay();

                if (timeLeft <= 0) {
                    clearInterval(timer);
                    timeOut();
                }
            }, 100);
        }

        function updateTimerDisplay() {
            const timerText = document.getElementById('timerText');
            const timerBar = document.getElementById('timerBar');

            const percent = Math.max(0, (timeLeft / totalTimePerQuestion) * 100);
            timerBar.style.width = `${percent}%`;
            timerText.innerText = `${Math.ceil(timeLeft)}s`;

            if (percent < 30) {
                timerBar.className = 'bg-gradient-to-r from-rose-600 to-red-600 h-full rounded-full transition-all duration-100 linear';
            } else {
                timerBar.className = 'bg-gradient-to-r from-pink-500 to-rose-500 h-full rounded-full transition-all duration-100 linear';
            }
        }

        function timeOut() {
            playSound('wrong');
            streak = 0;
            updateStreakUI();
            
            const problemCard = document.getElementById('problemCard');
            problemCard.classList.add('shake');
            setTimeout(() => problemCard.classList.remove('shake'), 400);

            totalQuestions++;
            setTimeout(nextQuestion, 600);
        }

        function checkAnswer(index) {
            if (isAnswering) return;
            isAnswering = true;
            clearInterval(timer);

            const selectedBtn = document.querySelectorAll('.choice-btn')[index];
            const selectedVal = parseInt(selectedBtn.dataset.value);
            totalQuestions++;

            if (selectedVal === currentCorrectAnswer) {
                // Correct Answer
                playSound('correct');
                score += 100 + (streak * 20); // Bonus score for high streak
                streak++;
                correctAnswersCount++;
                if (streak > maxStreak) maxStreak = streak;

                selectedBtn.className = 'choice-btn py-5 px-4 rounded-2xl bg-emerald-600 border border-emerald-400 text-2xl font-arcade font-bold text-white transition-all scale-105';
                spawnParticles(event ? event.clientX : null, event ? event.clientY : null);
                updateStreakUI();

                document.getElementById('currentScore').innerText = score;
                setTimeout(nextQuestion, 500);
            } else {
                // Wrong Answer
                playSound('wrong');
                streak = 0;
                updateStreakUI();

                selectedBtn.className = 'choice-btn py-5 px-4 rounded-2xl bg-rose-600 border border-rose-400 text-2xl font-arcade font-bold text-white transition-all shake';
                
                // Highlight the correct answer button
                document.querySelectorAll('.choice-btn').forEach(btn => {
                    if (parseInt(btn.dataset.value) === currentCorrectAnswer) {
                        btn.classList.add('ring-4', 'ring-emerald-400');
                    }
                });

                setTimeout(nextQuestion, 900);
            }
        }

        function updateStreakUI() {
            const streakBadge = document.getElementById('streakBadge');
            document.getElementById('streakCount').innerText = streak;

            if (streak >= 3) {
                streakBadge.classList.remove('hidden');
            } else {
                streakBadge.classList.add('hidden');
            }
        }

        function quitGame() {
            clearInterval(timer);
            finishGame();
        }

        function finishGame() {
            clearInterval(timer);
            
            let accuracy = totalQuestions > 0 ? Math.round((correctAnswersCount / totalQuestions) * 100) : 0;
            
            document.getElementById('finalScore').innerText = score;
            document.getElementById('finalStreak').innerText = `${maxStreak}x`;
            document.getElementById('finalAccuracy').innerText = `${accuracy}%`;

            const newHighScoreBadge = document.getElementById('newHighScoreBadge');
            
            if (score > highScore) {
                highScore = score;
                localStorage.setItem('math_genius_highscore', highScore.toString());
                newHighScoreBadge.classList.remove('hidden');
                playSound('highScore');
                triggerConfetti();
            } else {
                newHighScoreBadge.classList.add('hidden');
                playSound('gameover');
            }

            showScreen('gameOver');
        }

        /* Canvas Particle & Confetti Effect Engine */
        const canvas = document.getElementById('particleCanvas');
        const ctx = canvas.getContext('2d');
        let particles = [];

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function spawnParticles(x, y) {
            for (let i = 0; i < 15; i++) {
                particles.push({
                    x: x || canvas.width / 2,
                    y: y || canvas.height / 2,
                    vx: (Math.random() - 0.5) * 8,
                    vy: (Math.random() - 0.5) * 8,
                    size: Math.random() * 6 + 3,
                    color: ['#06b6d4', '#ec4899', '#f59e0b', '#10b981'][Math.floor(Math.random() * 4)],
                    life: 1
                });
            }
        }

        function triggerConfetti() {
            for (let i = 0; i < 80; i++) {
                particles.push({
                    x: Math.random() * canvas.width,
                    y: -10,
                    vx: (Math.random() - 0.5) * 4,
                    vy: Math.random() * 5 + 2,
                    size: Math.random() * 8 + 4,
                    color: ['#06b6d4', '#ec4899', '#f59e0b', '#10b981', '#a855f7'][Math.floor(Math.random() * 5)],
                    life: 1
                });
            }
        }

        function animateParticles() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            for (let i = particles.length - 1; i >= 0; i--) {
                let p = particles[i];
                p.x += p.vx;
                p.y += p.vy;
                p.life -= 0.02;

                ctx.fillStyle = p.color;
                ctx.globalAlpha = Math.max(0, p.life);
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                ctx.fill();

                if (p.life <= 0) {
                    particles.splice(i, 1);
                }
            }

            requestAnimationFrame(animateParticles);
        }
        animateParticles();
    </script>
</body>
</html>
