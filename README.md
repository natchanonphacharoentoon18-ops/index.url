<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Delta Ninja Explorer - Web Cloud Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Fira+Code:wght@400;600&display=swap');

        body {
            font-family: 'Orbitron', sans-serif;
            background-color: #05050a;
            color: #fff;
            min-height: 100vh;
            overflow-x: hidden;
        }

        .code-font {
            font-family: 'Fira Code', monospace;
        }

        .ninja-glow {
            box-shadow: 0 0 25px rgba(124, 58, 237, 0.4), inset 0 0 15px rgba(124, 58, 237, 0.2);
        }

        .btn-run-effect {
            background: linear-gradient(135deg, #7c3aed 0%, #4f46e5 100%);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .btn-run-effect:hover {
            transform: scale(1.05);
            box-shadow: 0 0 20px #8b5cf6;
        }

        .btn-stop-effect {
            background: linear-gradient(135deg, #dc2626 0%, #991b1b 100%);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .btn-stop-effect:hover {
            transform: scale(1.05);
            box-shadow: 0 0 20px #ef4444;
        }

        /* Floating Ninja Widget */
        #floating-ninja {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 9999;
            cursor: grab;
            touch-action: none;
        }
        #floating-ninja:active {
            cursor: grabbing;
        }
    </style>
</head>
<body class="flex flex-col items-center justify-center p-6 relative">

    <!-- Background Grid Effect -->
    <div class="absolute inset-0 bg-[radial-gradient(ellipse_80%_80%_at_50%_-20%,rgba(120,119,198,0.15),rgba(255,255,255,0))] pointer-events-none"></div>

    <!-- WELCOME CONTAINER -->
    <div class="text-center z-10 max-w-xl mx-auto mt-10">
        <div class="mb-6 inline-block p-6 rounded-full bg-purple-900/20 border border-purple-500/30 ninja-glow animate-pulse">
            <i class="fa-solid fa-user-ninja text-6xl text-purple-400"></i>
        </div>
        <h1 class="text-4xl font-black tracking-wider mb-4 text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-indigo-300">
            DELTA NINJA WEB CLOUD
        </h1>
        <p class="text-gray-400 text-sm mb-8 leading-relaxed">
            ระบบถูกเปิดใช้งานแล้ว! สังเกตที่มุมจอ จะมี **ไอคอนนินจาลอยอยู่** สามารถลากไปวางตรงไหนก็ได้ แล้วจิ้มเพื่อเปิดหน้าต่าง Executor ได้ตลอดเวลาแม้จะเลื่อนหน้าเว็บไปมา
        </p>
        <button onclick="openDeltaUI()" class="btn-run-effect text-white font-bold px-8 py-3 rounded-xl text-md tracking-wider border border-purple-400/50 inline-flex items-center gap-3 shadow-2xl">
            <i class="fa-solid fa-terminal"></i> เปิดหน้าต่าง EXECUTOR
        </button>
    </div>

    <!-- FLOATING NINJA WIDGET (ลอยตามหน้าจอเสมอ) -->
    <div id="floating-ninja" onclick="openDeltaUI()" class="bg-[#12121f] border border-purple-500/50 p-3 rounded-full ninja-glow flex items-center justify-center shadow-2xl transition hover:scale-110">
        <i class="fa-solid fa-user-ninja text-2xl text-purple-400"></i>
    </div>

    <!-- DELTA NINJA MODAL UI -->
    <div id="delta-ui" class="fixed inset-0 bg-black/80 backdrop-blur-md hidden items-center justify-center z-[10000] p-4 transition-all duration-300">
        <div class="w-full max-w-4xl h-[85vh] bg-[#0b0b12] border border-purple-500/40 rounded-2xl ninja-glow flex flex-col overflow-hidden shadow-2xl">
            
            <!-- TOP BAR -->
            <div class="bg-[#12121f] px-5 py-3 border-b border-purple-900/40 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="w-3 h-3 rounded-full bg-red-500/80 cursor-pointer" onclick="closeDeltaUI()"></div>
                    <div class="w-3 h-3 rounded-full bg-yellow-500/80"></div>
                    <div class="w-3 h-3 rounded-full bg-green-500/80"></div>
                    <span class="ml-3 text-sm font-bold text-purple-300 flex items-center gap-2">
                        <i class="fa-solid fa-user-ninja text-purple-400"></i> Delta Explorer v2.8 [Cloud Web Edition]
                    </span>
                </div>
                <button onclick="closeDeltaUI()" class="text-gray-400 hover:text-white transition">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <!-- CONTENT BODY -->
            <div class="flex-1 flex flex-col md:flex-row overflow-hidden">
                
                <!-- SIDEBAR TABS -->
                <div class="w-full md:w-56 bg-[#0f0f19] border-r border-purple-900/30 p-3 flex md:flex-col gap-2 overflow-x-auto">
                    <button class="flex items-center gap-3 px-4 py-3 rounded-lg bg-purple-600/20 text-purple-300 font-semibold border border-purple-500/30 text-sm transition">
                        <i class="fa-solid fa-code"></i> Executor
                    </button>
                    <button onclick="loadPreset('continuous')" class="flex items-center gap-3 px-4 py-3 rounded-lg hover:bg-white/5 text-gray-400 hover:text-white text-sm transition">
                        <i class="fa-solid fa-rotate"></i> Sample Loop (รันต่อเนื่อง)
                    </button>
                    <button onclick="loadPreset('alert')" class="flex items-center gap-3 px-4 py-3 rounded-lg hover:bg-white/5 text-gray-400 hover:text-white text-sm transition">
                        <i class="fa-solid fa-bolt"></i> Sample Alert
                    </button>
                    <button onclick="clearEditor()" class="flex items-center gap-3 px-4 py-3 rounded-lg hover:bg-red-500/10 text-red-400 text-sm transition mt-auto">
                        <i class="fa-solid fa-trash"></i> Clear Script
                    </button>
                </div>

                <!-- MAIN EDITOR & OUTPUT AREA -->
                <div class="flex-1 flex flex-col bg-[#07070d]">
                    
                    <!-- EDITOR -->
                    <div class="flex-1 p-4 flex flex-col">
                        <div class="text-xs text-purple-400 font-semibold mb-2 flex justify-between items-center">
                            <span>SCRIPT INPUT CONSOLE</span>
                            <span class="text-gray-500">Language: JavaScript</span>
                        </div>
                        <textarea id="code-input" class="flex-1 w-full bg-[#0d0d17] border border-purple-900/40 rounded-xl p-4 code-font text-purple-200 text-sm focus:outline-none focus:border-purple-500 resize-none shadow-inner" placeholder="// พิมพ์โค้ด JavaScript ที่ต้องการรันต่อเนื่องตรงนี้..."></textarea>
                    </div>

                    <!-- CONTROL BAR -->
                    <div class="px-4 py-3 bg-[#0f0f19] border-t border-purple-900/30 flex items-center justify-between">
                        <div id="status-indicator" class="text-xs text-gray-400 flex items-center gap-2">
                            <span class="w-2 h-2 rounded-full bg-gray-500"></span>
                            <span>Sandbox Status: Idle</span>
                        </div>
                        <div class="flex items-center gap-3">
                            <button onclick="executeCode()" class="btn-run-effect text-white font-bold px-5 py-2 rounded-lg text-sm flex items-center gap-2 shadow-lg">
                                <i class="fa-solid fa-play"></i> รัน (RUN)
                            </button>
                            <button onclick="stopExecution()" class="btn-stop-effect text-white font-bold px-5 py-2 rounded-lg text-sm flex items-center gap-2 shadow-lg">
                                <i class="fa-solid fa-stop"></i> หยุดรัน (STOP)
                            </button>
                        </div>
                    </div>

                    <!-- OUTPUT CONSOLE -->
                    <div class="h-32 bg-[#05050a] border-t border-purple-900/30 p-3 overflow-y-auto">
                        <div class="text-[11px] text-gray-500 font-semibold mb-1">EXECUTION OUTPUT:</div>
                        <div id="output-log" class="code-font text-xs text-green-400 space-y-1">
                            <span class="text-gray-500">[System]: พร้อมรับคำสั่งรันผ่าน Cloud Web...</span>
                        </div>
                    </div>

                </div>

            </div>

        </div>
    </div>

    <script>
        let activeIntervals = [];

        // ลากไอคอนนินจาไปรอบหน้าจอ
        const floatingNinja = document.getElementById('floating-ninja');
        let isDragging = false;
        let startX, startY, initialX, initialY;

        floatingNinja.addEventListener('pointerdown', (e) => {
            isDragging = true;
            startX = e.clientX;
            startY = e.clientY;
            const rect = floatingNinja.getBoundingClientRect();
            initialX = rect.left;
            initialY = rect.top;
            floatingNinja.setPointerCapture(e.pointerId);
        });

        floatingNinja.addEventListener('pointermove', (e) => {
            if (!isDragging) return;
            const dx = e.clientX - startX;
            const dy = e.clientY - startY;
            floatingNinja.style.left = `${initialX + dx}px`;
            floatingNinja.style.top = `${initialY + dy}px`;
            floatingNinja.style.right = 'auto';
        });

        floatingNinja.addEventListener('pointerup', () => {
            isDragging = false;
        });

        function openDeltaUI() {
            const deltaUI = document.getElementById('delta-ui');
            deltaUI.classList.remove('hidden');
            deltaUI.classList.add('flex');
        }

        function closeDeltaUI() {
            const deltaUI = document.getElementById('delta-ui');
            deltaUI.classList.remove('flex');
            deltaUI.classList.add('hidden');
        }

        function clearEditor() {
            document.getElementById('code-input').value = '';
        }

        function loadPreset(type) {
            const input = document.getElementById('code-input');
            if (type === 'continuous') {
                input.value = "// ตัวอย่าง: รันแสดงข้อความต่อเนื่องทุกๆ 1 วินาที\nlet round = 0;\nsetInterval(() => {\n    round++;\n    console.log('🥷 นินจา Cloud กำลังรันวนลูป รอบที่: ' + round);\n}, 1000);";
            } else if (type === 'alert') {
                input.value = "alert('สวัสดีจาก Delta Ninja Web Cloud!');\nconsole.log('Alert executed.');";
            }
        }

        const originalSetInterval = window.setInterval;
        window.setInterval = function(callback, delay) {
            const id = originalSetInterval(callback, delay);
            activeIntervals.push(id);
            return id;
        };

        function executeCode() {
            stopExecution();

            const code = document.getElementById('code-input').value;
            const outputLog = document.getElementById('output-log');
            const statusIndicator = document.getElementById('status-indicator');
            
            statusIndicator.innerHTML = '<span class="w-2 h-2 rounded-full bg-green-500 animate-ping"></span><span class="text-green-400">Sandbox Status: Running...</span>';
            outputLog.innerHTML = `<div class="text-purple-300">> เริ่มต้นรันสคริปต์ Cloud...</div>`;

            let logs = [];
            const originalConsoleLog = console.log;
            console.log = function(...args) {
                logs.push(args.join(' '));
                originalConsoleLog.apply(console, args);
                
                let logHtml = `<div class="text-purple-300">> กำลังทำงานต่อเนื่อง...</div>`;
                logs.forEach(l => {
                    logHtml += `<div class="text-green-400">> ${l}</div>`;
                });
                outputLog.innerHTML = logHtml;
                outputLog.scrollTop = outputLog.scrollHeight;
            };

            try {
                eval(code);
            } catch (error) {
                outputLog.innerHTML += `<div class="text-red-400">> เกิดข้อผิดพลาด: ${error.message}</div>`;
                statusIndicator.innerHTML = '<span class="w-2 h-2 rounded-full bg-red-500"></span><span class="text-red-400">Sandbox Status: Error</span>';
            }
        }

        function stopExecution() {
            activeIntervals.forEach(id => clearInterval(id));
            activeIntervals = [];

            const outputLog = document.getElementById('output-log');
            const statusIndicator = document.getElementById('status-indicator');

            statusIndicator.innerHTML = '<span class="w-2 h-2 rounded-full bg-gray-500"></span><span class="text-gray-400">Sandbox Status: Stopped</span>';
            outputLog.innerHTML += `<div class="text-red-400 font-bold">> [ระบบ]: หยุดการทำงานทั้งหมดแล้ว (Stopped)</div>`;
            outputLog.scrollTop = outputLog.scrollHeight;
        }
    </script>
</body>
</html>
