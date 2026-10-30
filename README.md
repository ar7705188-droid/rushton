# rushton-]
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mr. Rushton's Basics in Education and Learning 3D</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=VT323&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'VT323', monospace;
            background-color: #1e3a8a;
            user-select: none;
            overflow: hidden;
            margin: 0;
            padding: 0;
        }
        .font-pixel {
            font-family: 'Press Start 2P', cursive;
        }
        .scanlines::before {
            content: " ";
            display: block;
            position: absolute;
            top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(255, 255, 255, 0) 50%, rgba(0, 0, 0, 0.03) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.01), rgba(0, 255, 0, 0.01), rgba(0, 0, 255, 0.01));
            z-index: 10;
            background-size: 100% 4px, 6px 100%;
            pointer-events: none;
        }
    </style>
</head>
<body class="w-screen h-screen relative scanlines text-gray-900">

    <!-- 3D Canvas Viewport -->
    <div id="game-container" class="w-full h-full absolute top-0 left-0"></div>

    <!-- FPS Crosshair -->
    <div id="crosshair" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 pointer-events-none z-20 flex items-center justify-center">
        <div class="w-2.5 h-2.5 bg-yellow-400 rounded-full border border-black"></div>
    </div>

    <!-- HUD Overlay -->
    <div id="hud" class="absolute top-0 left-0 right-0 p-4 flex justify-between items-start pointer-events-none z-20 text-shadow">
        <div class="bg-blue-100/95 border-4 border-yellow-400 p-3 rounded-lg shadow-2xl flex flex-col gap-2 text-black">
            <div class="text-blue-800 font-pixel text-xs sm:text-sm">MR. RUSHTON'S SCHOOLHOUSE</div>
            <div class="text-xl sm:text-2xl font-bold">
                Notebooks: <span id="notebook-count" class="text-red-600 font-bold">0/7</span>
            </div>
            <div class="flex items-center space-x-2">
                <span class="text-xs text-blue-900 font-pixel">STAMINA:</span>
                <div class="w-32 sm:w-48 bg-gray-300 border-2 border-black h-4 rounded overflow-hidden">
                    <div id="stamina-fill" class="bg-green-500 h-full w-full transition-all duration-75"></div>
                </div>
            </div>
        </div>

        <div class="bg-blue-100/95 border-4 border-yellow-400 p-2 sm:p-3 rounded-lg shadow-2xl flex items-center gap-2 pointer-events-auto">
            <span class="text-xs text-blue-900 font-pixel hidden sm:inline">ITEMS:</span>
            <div id="slot-0" onclick="useItem(0)" class="w-10 h-10 sm:w-12 sm:h-12 border-2 border-blue-600 bg-white rounded flex items-center justify-center text-2xl cursor-pointer hover:border-yellow-500 shadow"></div>
            <div id="slot-1" onclick="useItem(1)" class="w-10 h-10 sm:w-12 sm:h-12 border-2 border-blue-600 bg-white rounded flex items-center justify-center text-2xl cursor-pointer hover:border-yellow-500 shadow"></div>
            <div id="slot-2" onclick="useItem(2)" class="w-10 h-10 sm:w-12 sm:h-12 border-2 border-blue-600 bg-white rounded flex items-center justify-center text-2xl cursor-pointer hover:border-yellow-500 shadow"></div>
        </div>
    </div>

    <!-- Interaction Prompt -->
    <div id="interaction-prompt" class="absolute top-2/3 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-yellow-300 border-2 border-black font-pixel text-black px-4 py-2 text-xs sm:text-sm rounded pointer-events-none hidden z-20 shadow-lg">
        Press [E] or Click to Interact
    </div>

    <div id="game-message" class="absolute top-24 left-1/2 -translate-x-1/2 bg-yellow-400 text-black font-pixel text-xs sm:text-sm px-6 py-3 rounded-lg shadow-2xl border-4 border-black hidden transition-opacity z-30">
        Message Here
    </div>

    <!-- START SCREEN -->
    <div id="start-screen" class="absolute inset-0 bg-blue-700/95 flex flex-col items-center justify-center p-6 text-center z-40 text-white">
        <h1 class="font-pixel text-yellow-300 text-2xl sm:text-4xl md:text-5xl mb-2 leading-tight">
            MR. RUSHTON'S BASICS<br><span class="text-white text-base sm:text-2xl">IN EDUCATION AND LEARNING 3D</span>
        </h1>
        
        <h2 class="text-yellow-200 font-pixel text-xs sm:text-sm mb-6 tracking-wide">
            Created by Arturo, Featuring Zyker & Myles
        </h2>

        <div class="w-28 h-28 bg-yellow-300 border-4 border-black rounded-full flex items-center justify-center mb-6 text-6xl shadow-inner">👨‍🏫</div>

        <p class="text-lg sm:text-2xl max-w-xl text-yellow-100 mb-6 leading-relaxed">
            Collect all <span class="text-yellow-300 font-bold">7 Notebooks</span> and open <span class="text-yellow-300 font-bold">BOTH Exit Doors</span> to escape!
            <br><span class="text-red-300 font-bold text-base sm:text-lg">Zyker guards the west hall, Myles trades Chess Boards, and Mr. Rushton is hunting!</span>
        </p>

        <button id="start-btn" onclick="startGame()" class="font-pixel bg-red-600 hover:bg-red-500 text-white border-4 border-black px-8 py-4 text-base sm:text-xl rounded-lg shadow-2xl transform hover:scale-105 transition-all cursor-pointer">
            CLICK TO START GAME
        </button>

        <div class="mt-8 grid grid-cols-2 gap-4 text-xs sm:text-sm text-yellow-100 bg-blue-900/90 p-4 rounded-lg border-2 border-yellow-400">
            <div><span class="text-yellow-300 font-bold">W / S:</span> Move Up / Down</div>
            <div><span class="text-yellow-300 font-bold">A / D:</span> Move Left / Right</div>
            <div><span class="text-yellow-300 font-bold">MOUSE:</span> Look Around</div>
            <div><span class="text-yellow-300 font-bold">SHIFT / E:</span> Sprint / Interact</div>
        </div>
    </div>

    <!-- MATH QUIZ MODAL -->
    <div id="quiz-modal" class="absolute inset-0 bg-black/85 flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-blue-100 border-8 border-red-600 rounded-xl p-6 w-full max-w-md text-black shadow-2xl relative">
            <div class="bg-blue-700 text-white font-pixel text-xs p-2 rounded mb-4 flex justify-between items-center border-2 border-black">
                <span>RUSHTON-PAD 3D</span>
                <span id="quiz-progress" class="text-yellow-300">PROBLEM 1/3</span>
            </div>
            <div class="bg-white border-4 border-black p-6 rounded mb-4 text-center min-h-[100px] flex items-center justify-center">
                <p id="quiz-question" class="text-2xl sm:text-3xl font-bold font-mono text-gray-900">5 + 3 = ?</p>
            </div>
            <div id="quiz-options" class="grid grid-cols-1 gap-3 mb-4"></div>
            <div id="quiz-feedback" class="h-6 text-center font-bold text-sm font-pixel"></div>
        </div>
    </div>

    <!-- ZYKER TOLL MODAL -->
    <div id="zyker-modal" class="absolute inset-0 bg-black/85 flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-blue-900 border-8 border-yellow-400 rounded-xl p-6 w-full max-w-md text-white shadow-2xl relative">
            <div class="bg-red-700 text-white font-pixel text-xs p-2 rounded mb-4 flex justify-between items-center border-2 border-black">
                <span>ZYKER THE TOLL-TAKER</span>
                <span>CHOOSE ITEM</span>
            </div>
            <div class="bg-blue-950 border-4 border-black p-4 rounded mb-4 text-center">
                <p class="text-lg font-bold font-mono text-yellow-300">"Give me an item or you're stuck here!"</p>
            </div>
            <div id="zyker-item-options" class="grid grid-cols-1 gap-3 mb-4"></div>
            <button onclick="closeZykerModal()" class="w-full bg-red-600 hover:bg-red-500 text-white font-pixel text-xs py-2 rounded border-2 border-black cursor-pointer">CANCEL</button>
        </div>
    </div>

    <!-- GAME OVER / WIN SCREEN -->
    <div id="end-screen" class="absolute inset-0 bg-red-950/95 flex flex-col items-center justify-center p-6 text-center z-50 hidden text-white">
        <h1 id="end-title" class="font-pixel text-yellow-400 text-3xl sm:text-5xl mb-4 leading-tight">GAME OVER!</h1>
        <p id="end-subtitle" class="text-xl sm:text-2xl text-white mb-6">Mr. Rushton caught you!</p>
        <div id="end-icon" class="text-7xl mb-6">👨‍🏫📏</div>
        <button onclick="resetGame()" class="font-pixel bg-yellow-400 hover:bg-yellow-300 text-black border-4 border-black px-8 py-4 text-lg rounded shadow-lg transform hover:scale-105 transition-all cursor-pointer">
            TRY AGAIN
        </button>
    </div>

    <script>
        // --- AUDIO SYNTHESIZER ---
        const AudioContext = window.AudioContext || window.webkitAudioContext;
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) audioCtx = new AudioContext();
            if (audioCtx.state === 'suspended') audioCtx.resume();
        }

        function playSound(type) {
            if (!audioCtx) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            const now = audioCtx.currentTime;

            if (type === 'pickup') {
                osc.type = 'square';
                osc.frequency.setValueAtTime(523.25, now);
                osc.frequency.setValueAtTime(659.25, now + 0.08);
                gain.gain.setValueAtTime(0.15, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
                osc.start(now); osc.stop(now + 0.25);
            } else if (type === 'correct') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(440, now);
                osc.frequency.setValueAtTime(880, now + 0.1);
                gain.gain.setValueAtTime(0.2, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
                osc.start(now); osc.stop(now + 0.3);
            } else if (type === 'wrong') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(140, now);
                osc.frequency.setValueAtTime(90, now + 0.15);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                osc.start(now); osc.stop(now + 0.4);
            } else if (type === 'slap') {
                osc.type = 'square';
                osc.frequency.setValueAtTime(700, now);
                osc.frequency.exponentialRampToValueAtTime(60, now + 0.12);
                gain.gain.setValueAtTime(0.35, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.12);
                osc.start(now); osc.stop(now + 0.12);
            } else if (type === 'jumpscare') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(800, now);
                osc.frequency.linearRampToValueAtTime(100, now + 0.8);
                gain.gain.setValueAtTime(0.5, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.8);
                osc.start(now); osc.stop(now + 0.8);
            } else if (type === 'drink') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(300, now);
                osc.frequency.exponentialRampToValueAtTime(600, now + 0.25);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
                osc.start(now); osc.stop(now + 0.25);
            } else if (type === 'stun') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(800, now);
                osc.frequency.setValueAtTime(400, now + 0.15);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                osc.start(now); osc.stop(now + 0.4);
            } else if (type === 'bully') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(220, now);
                osc.frequency.setValueAtTime(140, now + 0.2);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
                osc.start(now); osc.stop(now + 0.3);
            }
        }

        // --- THREE.JS GLOBALS & NAVIGATOR ---
        let scene, camera, renderer;
        let isGameActive = false;
        let isQuizActive = false;
        let isZykerModalActive = false;
        let exitsOpenedCount = 0;
        const totalExits = 2;

        const rushtonMiniMap = {
            gridSizeX: 40,
            gridSizeZ: 40,
            cellSize: 4.5,
            gridOriginX: -90,
            gridOriginZ: -90,
            nodes: [],
            initMap() {
                this.nodes = [];
                for (let x = 0; x < this.gridSizeX; x++) {
                    this.nodes[x] = [];
                    for (let z = 0; z < this.gridSizeZ; z++) {
                        const worldX = this.gridOriginX + x * this.cellSize + this.cellSize / 2;
                        const worldZ = this.gridOriginZ + z * this.cellSize + this.cellSize / 2;
                        const isWalkable = !checkCollision({ x: worldX, z: worldZ }, 0.6);
                        this.nodes[x][z] = { walkable: isWalkable, x: worldX, z: worldZ };
                    }
                }
            },
            getBestNavDir(currentPos, targetPos) {
                const distToTarget = currentPos.distanceTo(targetPos);
                if (distToTarget < 12.0) {
                    const direct = new THREE.Vector3().subVectors(targetPos, currentPos);
                    direct.y = 0;
                    if (direct.lengthSq() > 0.001) direct.normalize();
                    return direct;
                }

                let curX = Math.floor((currentPos.x - this.gridOriginX) / this.cellSize);
                let curZ = Math.floor((currentPos.z - this.gridOriginZ) / this.cellSize);
                let tgtX = Math.floor((targetPos.x - this.gridOriginX) / this.cellSize);
                let tgtZ = Math.floor((targetPos.z - this.gridOriginZ) / this.cellSize);

                curX = Math.max(0, Math.min(this.gridSizeX - 1, curX));
                curZ = Math.max(0, Math.min(this.gridSizeZ - 1, curZ));
                tgtX = Math.max(0, Math.min(this.gridSizeX - 1, tgtX));
                tgtZ = Math.max(0, Math.min(this.gridSizeZ - 1, tgtZ));

                const dirs = [
                    { x: 0, z: -1 }, { x: 0, z: 1 }, { x: -1, z: 0 }, { x: 1, z: 0 },
                    { x: -1, z: -1 }, { x: 1, z: -1 }, { x: -1, z: 1 }, { x: 1, z: 1 }
                ];

                let bestNeighbor = null;
                let minDist = Infinity;

                for (let d of dirs) {
                    let nx = curX + d.x;
                    let nz = curZ + d.z;
                    if (nx >= 0 && nx < this.gridSizeX && nz >= 0 && nz < this.gridSizeZ) {
                        if (this.nodes[nx][nz].walkable) {
                            let dist = Math.hypot(nx - tgtX, nz - tgtZ);
                            if (dist < minDist) {
                                minDist = dist;
                                bestNeighbor = this.nodes[nx][nz];
                            }
                        }
                    }
                }

                if (bestNeighbor) {
                    const navTarget = new THREE.Vector3(bestNeighbor.x, 1.6, bestNeighbor.z);
                    const dir = new THREE.Vector3().subVectors(navTarget, currentPos);
                    dir.y = 0;
                    if (dir.lengthSq() > 0.01) {
                        dir.normalize();
                        return dir;
                    }
                }

                const direct = new THREE.Vector3().subVectors(targetPos, currentPos);
                direct.y = 0;
                direct.normalize();
                return direct;
            }
        };

        const player = {
            position: new THREE.Vector3(0, 1.6, 75),
            rotation: { yaw: 0, pitch: 0 },
            stamina: 180,
            maxStamina: 180,
            inventory: [null, null, null],
            notebooksCollected: 0,
            radius: 0.6
        };

        const keyState = {};
        let isPointerLocked = false;
        const wallBoxes = [];
        const interactiveObjects = [];
        let rushtonSprite, nonoSprite, zykerSprite, mylesSprite, zykerBox;

        const rushton = {
            position: new THREE.Vector3(0, 1.6, -20),
            baseSpeed: 0.052,
            slapTimer: 0,
            baseSlapInterval: 34,
            angerLevel: 1.0,
            radius: 0.9,
            stunDurationTimer: 0,
            lastPosition: new THREE.Vector3(0, 1.6, -20),
            stuckCheckTimer: 0,
            isAggro: false
        };

        const nono = {
            position: new THREE.Vector3(-12, 1.8, 15),
            homePosition: new THREE.Vector3(-12, 1.8, 15),
            speed: 0.035,
            radius: 0.9,
            isChasingRushton: false,
            lastPosition: new THREE.Vector3(-12, 1.8, 15),
            stuckCheckTimer: 0
        };

        const zyker = {
            position: new THREE.Vector3(-55, 1.8, 0),
            active: true
        };

        const myles = {
            position: new THREE.Vector3(-4, 1.8, -2),
        };

        // UNSTRETCHED BRICK TEXTURE GENERATOR
        function getBrickMaterial(width, height) {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 256;
            const ctx = canvas.getContext('2d');

            ctx.fillStyle = '#450a0a';
            ctx.fillRect(0, 0, 256, 256);

            const brickRows = 8;
            const brickCols = 4;
            const rH = 256 / brickRows;
            const rW = 256 / brickCols;

            ctx.fillStyle = '#b91c1c';
            for (let r = 0; r < brickRows; r++) {
                const offsetX = (r % 2 === 0) ? 0 : rW / 2;
                for (let c = -1; c <= brickCols; c++) {
                    const bx = c * rW + offsetX;
                    const by = r * rH;
                    ctx.fillRect(bx + 2, by + 2, rW - 4, rH - 4);

                    ctx.fillStyle = '#dc2626';
                    ctx.fillRect(bx + 3, by + 3, rW - 6, 3);
                    ctx.fillStyle = '#991b1b';
                    ctx.fillRect(bx + 3, by + rH - 5, rW - 6, 3);
                    ctx.fillStyle = '#b91c1c';
                }
            }

            const tex = new THREE.CanvasTexture(canvas);
            tex.wrapS = THREE.RepeatWrapping;
            tex.wrapT = THREE.RepeatWrapping;
            tex.repeat.set(Math.max(1, width / 4), 1);

            return new THREE.MeshStandardMaterial({ map: tex, roughness: 0.8, metalness: 0.1 });
        }

        function init3D() {
            const container = document.getElementById('game-container');
            container.innerHTML = '';

            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x93c5fd);
            scene.fog = new THREE.FogExp2(0x93c5fd, 0.005);

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.rotation.order = 'YXZ';
            camera.position.copy(player.position);

            renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: "high-performance" });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            container.appendChild(renderer.domElement);

            const ambientLight = new THREE.AmbientLight(0xffffff, 1.2);
            scene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xfffaed, 1.0);
            dirLight.position.set(30, 80, 30);
            scene.add(dirLight);

            buildSchoolMap();
            rushtonMiniMap.initMap();

            createRushtonEnemy();
            createNonoCharacter();
            createZykerCharacter();
            createMylesCharacter();

            window.addEventListener('resize', onWindowResize);
            setupControls();
        }

        function createRushtonSprite() {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 384;
            const ctx = canvas.getContext('2d');

            ctx.clearRect(0, 0, 256, 384);
            
            ctx.fillStyle = '#18181b';
            ctx.fillRect(68, 160, 120, 220);

            ctx.beginPath();
            ctx.moveTo(96, 160); ctx.lineTo(128, 190); ctx.lineTo(160, 160);
            ctx.fillStyle = '#27272a'; ctx.fill();

            ctx.fillStyle = '#fce2d0';
            ctx.beginPath(); ctx.ellipse(128, 90, 48, 54, 0, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#a55229';
            for (let i = 0; i < 35; i++) {
                const angle = (i / 35) * Math.PI * 1.3 + Math.PI * 0.85;
                const rx = 128 + Math.cos(angle) * 46;
                const ry = 72 + Math.sin(angle) * 42;
                ctx.beginPath(); ctx.arc(rx, ry, 10 + (i % 3) * 2, 0, Math.PI * 2); ctx.fill();
            }

            ctx.fillStyle = '#ffffff';
            ctx.beginPath(); ctx.ellipse(108, 84, 9, 7, 0, 0, Math.PI * 2); ctx.fill();
            ctx.beginPath(); ctx.ellipse(148, 84, 9, 7, 0, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#2563eb';
            ctx.beginPath(); ctx.arc(108, 84, 3.5, 0, Math.PI * 2); ctx.arc(148, 84, 3.5, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#9c4221';
            ctx.beginPath();
            ctx.moveTo(88, 92); ctx.lineTo(168, 92); ctx.lineTo(160, 142); ctx.lineTo(128, 154); ctx.lineTo(96, 142);
            ctx.closePath(); ctx.fill();

            ctx.fillStyle = '#ffffff';
            ctx.beginPath(); ctx.roundRect(110, 120, 36, 14, 4); ctx.fill();
            
            ctx.fillStyle = '#8e3815';
            ctx.beginPath(); ctx.ellipse(128, 110, 22, 8, 0, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#f59e0b';
            ctx.fillRect(170, 130, 14, 180);
            ctx.fillStyle = '#111827';
            for(let mark = 140; mark < 300; mark += 15) {
                ctx.fillRect(170, mark, 6, 2);
            }

            return new THREE.CanvasTexture(canvas);
        }

        function createRushtonEnemy() {
            const texture = createRushtonSprite();
            const spriteMat = new THREE.SpriteMaterial({ map: texture });
            rushtonSprite = new THREE.Sprite(spriteMat);
            rushtonSprite.scale.set(2.6, 3.8, 1);
            rushtonSprite.position.copy(rushton.position);
            scene.add(rushtonSprite);
        }

        function createNonoCharacter() {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 384;
            const ctx = canvas.getContext('2d');

            ctx.clearRect(0, 0, 256, 384);
            ctx.fillStyle = '#2563eb';
            ctx.fillRect(58, 150, 140, 230);

            ctx.strokeStyle = '#facc15';
            ctx.lineWidth = 3;
            for (let x = 68; x <= 188; x += 14) {
                ctx.beginPath(); ctx.moveTo(x, 150); ctx.lineTo(x, 380); ctx.stroke();
            }

            ctx.fillStyle = '#fcd5ce';
            ctx.beginPath(); ctx.ellipse(128, 90, 46, 52, 0, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#dc2626';
            ctx.beginPath(); ctx.ellipse(128, 52, 50, 28, 0, Math.PI, Math.PI * 2); ctx.fill();
            ctx.fillRect(118, 48, 36, 8);

            ctx.fillStyle = '#1e293b';
            ctx.beginPath(); ctx.arc(108, 80, 4, 0, Math.PI * 2); ctx.arc(148, 80, 4, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#ffffff';
            ctx.beginPath(); ctx.ellipse(128, 114, 24, 14, 0, 0, Math.PI); ctx.fill();

            const texture = new THREE.CanvasTexture(canvas);
            const spriteMat = new THREE.SpriteMaterial({ map: texture });
            nonoSprite = new THREE.Sprite(spriteMat);
            nonoSprite.scale.set(2.4, 3.6, 1);
            nonoSprite.position.copy(nono.position);
            scene.add(nonoSprite);

            nonoSprite.userData = { type: 'nono' };
            interactiveObjects.push(nonoSprite);
        }

        function createZykerCharacter() {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 384;
            const ctx = canvas.getContext('2d');
            ctx.clearRect(0, 0, 256, 384);

            ctx.fillStyle = '#1e3a8a';
            ctx.fillRect(50, 130, 156, 160);
            ctx.fillStyle = '#f8fafc';
            ctx.fillRect(85, 130, 86, 160);
            ctx.fillStyle = '#dc2626';
            ctx.fillRect(50, 280, 156, 10);

            ctx.fillStyle = '#ffefcd';
            ctx.beginPath(); ctx.ellipse(128, 70, 46, 54, 0, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#facc15';
            ctx.beginPath(); ctx.ellipse(128, 44, 44, 22, 0, 0, Math.PI * 2); ctx.fill();
            ctx.fillStyle = '#ca8a04';
            for (let i = 0; i < 25; i++) {
                ctx.beginPath(); ctx.arc(110 + (Math.random()*36), 34 + (Math.random()*18), 2, 0, Math.PI * 2); ctx.fill();
            }

            ctx.fillStyle = '#fff'; ctx.beginPath(); ctx.arc(108, 65, 7, 0, Math.PI * 2); ctx.arc(148, 65, 7, 0, Math.PI * 2); ctx.fill();
            ctx.fillStyle = '#1e3a8a'; ctx.beginPath(); ctx.arc(108, 65, 4, 0, Math.PI * 2); ctx.arc(148, 65, 4, 0, Math.PI * 2); ctx.fill();

            const texture = new THREE.CanvasTexture(canvas);
            const spriteMat = new THREE.SpriteMaterial({ map: texture });
            zykerSprite = new THREE.Sprite(spriteMat);
            zykerSprite.scale.set(2.6, 3.8, 1);
            zykerSprite.position.copy(zyker.position);
            scene.add(zykerSprite);

            zykerSprite.userData = { type: 'zyker' };
            interactiveObjects.push(zykerSprite);

            const invisibleWallGeo = new THREE.BoxGeometry(3, 4, 24);
            const invisibleWallMat = new THREE.MeshBasicMaterial({ visible: false });
            const mesh = new THREE.Mesh(invisibleWallGeo, invisibleWallMat);
            mesh.position.copy(zyker.position);
            scene.add(mesh);
            zykerBox = new THREE.Box3().setFromObject(mesh);
            wallBoxes.push(zykerBox);
        }

        function createMylesCharacter() {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 384;
            const ctx = canvas.getContext('2d');
            ctx.clearRect(0, 0, 256, 384);

            ctx.fillStyle = '#2563eb';
            ctx.fillRect(58, 140, 140, 240);

            ctx.fillStyle = '#fce2d0';
            ctx.beginPath(); ctx.ellipse(128, 85, 48, 56, 0, 0, Math.PI * 2); ctx.fill();

            ctx.fillStyle = '#442c1e';
            ctx.beginPath(); ctx.ellipse(128, 48, 50, 24, 0, 0, Math.PI * 2); ctx.fill();
            for (let i = 0; i < 25; i++) {
                ctx.beginPath(); ctx.arc(128 + (Math.random()-0.5)*80, 40 + (Math.random()-0.5)*30, 4 + Math.random()*2, 0, Math.PI * 2); ctx.fill();
            }

            ctx.lineWidth = 3;
            ctx.strokeStyle = '#1e3a8a';
            ctx.beginPath(); ctx.rect(98, 72, 24, 18); ctx.stroke();
            ctx.beginPath(); ctx.rect(134, 72, 24, 18); ctx.stroke();
            ctx.beginPath(); ctx.moveTo(122, 81); ctx.lineTo(134, 81); ctx.stroke();

            ctx.fillStyle = '#ffffff'; ctx.beginPath(); ctx.arc(110, 81, 6, 0, Math.PI * 2); ctx.arc(146, 81, 6, 0, Math.PI * 2); ctx.fill();
            ctx.fillStyle = '#0284c7'; ctx.beginPath(); ctx.arc(110, 81, 3.5, 0, Math.PI * 2); ctx.arc(146, 81, 3.5, 0, Math.PI * 2); ctx.fill();
            ctx.fillStyle = '#0f172a'; ctx.beginPath(); ctx.arc(110, 81, 1.5, 0, Math.PI * 2); ctx.arc(146, 81, 1.5, 0, Math.PI * 2); ctx.fill();

            const texture = new THREE.CanvasTexture(canvas);
            const spriteMat = new THREE.SpriteMaterial({ map: texture });
            mylesSprite = new THREE.Sprite(spriteMat);
            mylesSprite.scale.set(2.4, 3.6, 1);
            mylesSprite.position.copy(myles.position);
            scene.add(mylesSprite);

            mylesSprite.userData = { type: 'myles' };
            interactiveObjects.push(mylesSprite);
        }

        function createWall(x, z, width, depth, height = 4, color = 0x2563eb) {
            const group = new THREE.Group();
            group.position.set(x, 0, z);

            const wW = width > 2 ? width : 2;
            const wD = depth > 2 ? depth : 2;

            const upperGeo = new THREE.BoxGeometry(wW, height * 0.55, wD);
            const upperMat = new THREE.MeshStandardMaterial({ color: color, roughness: 0.5 });
            const upper = new THREE.Mesh(upperGeo, upperMat);
            upper.position.y = height * 0.72;
            group.add(upper);

            const brickGeo = new THREE.BoxGeometry(wW, height * 0.45, wD);
            const brickMat = getBrickMaterial(Math.max(wW, wD), height * 0.45);
            const brick = new THREE.Mesh(brickGeo, brickMat);
            brick.position.y = height * 0.225;
            group.add(brick);

            scene.add(group);

            const bbox = new THREE.Box3().setFromObject(upper);
            wallBoxes.push(bbox);
            return group;
        }

        function buildClassroomWithCorridor(x, z, width, depth, doorSide = 'south', wallColor = 0x3b82f6) {
            const hW = width / 2;
            const hD = depth / 2;
            const doorGap = 4;

            if (doorSide === 'south') {
                createWall(x, z - hD, width, 1.5, 4, wallColor);
                createWall(x - hW / 2 - doorGap / 4, z + hD, hW - doorGap / 2, 1.5, 4, wallColor);
                createWall(x + hW / 2 + doorGap / 4, z + hD, hW - doorGap / 2, 1.5, 4, wallColor);
            } else if (doorSide === 'north') {
                createWall(x - hW / 2 - doorGap / 4, z - hD, hW - doorGap / 2, 1.5, 4, wallColor);
                createWall(x + hW / 2 + doorGap / 4, z - hD, hW - doorGap / 2, 1.5, 4, wallColor);
                createWall(x, z + hD, width, 1.5, 4, wallColor);
            } else {
                createWall(x, z - hD, width, 1.5, 4, wallColor);
                createWall(x, z + hD, width, 1.5, 4, wallColor);
            }

            if (doorSide === 'east') {
                createWall(x - hW, z, 1.5, depth, 4, wallColor);
                createWall(x + hW, z - hD / 2 - doorGap / 4, 1.5, hD - doorGap / 2, 4, wallColor);
                createWall(x + hW, z + hD / 2 + doorGap / 4, 1.5, hD - doorGap / 2, 4, wallColor);
            } else if (doorSide === 'west') {
                createWall(x - hW, z - hD / 2 - doorGap / 4, 1.5, hD - doorGap / 2, 4, wallColor);
                createWall(x - hW, z + hD / 2 + doorGap / 4, 1.5, hD - doorGap / 2, 4, wallColor);
                createWall(x + hW, z, 1.5, depth, 4, wallColor);
            } else {
                createWall(x - hW, z, 1.5, depth, 4, wallColor);
                createWall(x + hW, z, 1.5, depth, 4, wallColor);
            }

            let dX = x, dZ = z, dRot = 0;
            if (doorSide === 'south') { dZ += hD; dRot = 0; }
            else if (doorSide === 'north') { dZ -= hD; dRot = 0; }
            else if (doorSide === 'east') { dX += hW; dRot = Math.PI / 2; }
            else if (doorSide === 'west') { dX -= hW; dRot = Math.PI / 2; }

            const doorFrameGeo = new THREE.BoxGeometry(3.8, 3.4, 0.4);
            const doorFrameMat = new THREE.MeshStandardMaterial({ color: 0xfacc15, roughness: 0.3 });
            const frame = new THREE.Mesh(doorFrameGeo, doorFrameMat);
            frame.position.set(dX, 1.7, dZ);
            frame.rotation.y = dRot;
            scene.add(frame);

            const doorGeo = new THREE.BoxGeometry(3.4, 3.0, 0.2);
            const doorMat = new THREE.MeshStandardMaterial({ color: 0xdc2626, roughness: 0.4 });
            const classroomDoor = new THREE.Mesh(doorGeo, doorMat);
            classroomDoor.position.set(dX, 1.5, dZ);
            classroomDoor.rotation.y = dRot;
            scene.add(classroomDoor);

            const winGeo = new THREE.BoxGeometry(0.2, 1.6, 3.0);
            const winMat = new THREE.MeshStandardMaterial({ color: 0x93c5fd, transparent: true, opacity: 0.7, roughness: 0.1 });
            const win1 = new THREE.Mesh(winGeo, winMat);
            win1.position.set(x - hW, 2.2, z);
            scene.add(win1);

            const win2 = new THREE.Mesh(winGeo, winMat);
            win2.position.set(x + hW, 2.2, z);
            scene.add(win2);

            const boardGeo = new THREE.BoxGeometry(width * 0.4, 1.5, 0.1);
            const boardMat = new THREE.MeshStandardMaterial({ color: 0x15803d, roughness: 0.5 });
            const board = new THREE.Mesh(boardGeo, boardMat);
            board.position.set(x, 2, z - hD + 0.2);
            scene.add(board);
        }

        function buildSchoolMap() {
            const floorGeo = new THREE.PlaneGeometry(180, 180);
            const floorMat = new THREE.MeshStandardMaterial({ color: 0x2563eb, roughness: 0.4, metalness: 0.1 });
            const floor = new THREE.Mesh(floorGeo, floorMat);
            floor.rotation.x = -Math.PI / 2;
            scene.add(floor);

            const ceilingMat = new THREE.MeshStandardMaterial({ color: 0xfde047, roughness: 0.7, metalness: 0.1 });
            const ceiling = new THREE.Mesh(floorGeo, ceilingMat);
            ceiling.position.y = 4;
            ceiling.rotation.x = Math.PI / 2;
            scene.add(ceiling);

            createWall(0, -90, 180, 2, 4, 0x1d4ed8);
            createWall(0, 90, 180, 2, 4, 0x1d4ed8);
            createWall(-90, 0, 2, 180, 4, 0x1d4ed8);
            createWall(90, 0, 2, 180, 4, 0x1d4ed8);

            createWall(-12, 35, 2, 60, 4, 0x1e40af);
            createWall(12, 35, 2, 60, 4, 0x1e40af);
            createWall(-12, -35, 2, 60, 4, 0x1e40af);
            createWall(12, -35, 2, 60, 4, 0x1e40af);

            createWall(-45, -12, 40, 2, 4, 0x1e40af);
            createWall(45, -12, 40, 2, 4, 0x1e40af);
            createWall(-45, 12, 40, 2, 4, 0x1e40af);
            createWall(45, 12, 40, 2, 4, 0x1e40af);

            createWall(-30, 70, 30, 2, 4, 0x1e40af);
            createWall(30, 70, 30, 2, 4, 0x1e40af);
            createWall(-30, -70, 30, 2, 4, 0x1e40af);
            createWall(30, -70, 30, 2, 4, 0x1e40af);
            createWall(-70, -30, 2, 30, 4, 0x1e40af);
            createWall(70, -30, 2, 30, 4, 0x1e40af);

            const classrooms = [
                { x: -65, z: -65, w: 18, d: 14, door: 'south' },
                { x: 0, z: -65, w: 18, d: 14, door: 'south' },
                { x: 65, z: -65, w: 18, d: 14, door: 'south' },
                { x: -65, z: 25, w: 18, d: 14, door: 'east' },
                { x: 65, z: 25, w: 18, d: 14, door: 'west' },
                { x: -65, z: 65, w: 18, d: 14, door: 'north' },
                { x: 65, z: 65, w: 18, d: 14, door: 'north' }
            ];

            classrooms.forEach((room, idx) => {
                buildClassroomWithCorridor(room.x, room.z, room.w, room.d, room.door, 0x2563eb);
                spawnNotebook(room.x, room.z, idx);
            });

            const cafeFloorGeo = new THREE.PlaneGeometry(30, 20);
            const cafeFloorMat = new THREE.MeshStandardMaterial({ color: 0x3b82f6, roughness: 0.4 });
            const cafeFloorMesh = new THREE.Mesh(cafeFloorGeo, cafeFloorMat);
            cafeFloorMesh.rotation.x = -Math.PI / 2;
            cafeFloorMesh.position.set(-65, 0.02, -20);
            scene.add(cafeFloorMesh);

            createWall(-65, -30, 30, 1.5, 4, 0x1e40af);
            createWall(-80, -20, 1.5, 20, 4, 0x1e40af);
            createWall(-65, -10, 30, 1.5, 4, 0x1e40af);
            createWall(-50, -26, 1.5, 8, 4, 0x1e40af);
            createWall(-50, -14, 1.5, 8, 4, 0x1e40af);

            for (let tx = -74; tx <= -56; tx += 9) {
                const tableGeo = new THREE.BoxGeometry(5, 1.2, 3.5);
                const tableMat = new THREE.MeshStandardMaterial({ color: 0xfacc15, roughness: 0.3 });
                const table = new THREE.Mesh(tableGeo, tableMat);
                table.position.set(tx, 0.6, -20);
                scene.add(table);
                wallBoxes.push(new THREE.Box3().setFromObject(table));
            }

            spawnVendingMachine(-20, 25, 0);
            spawnVendingMachine(20, -25, Math.PI);

            spawnItem(-30, 50, 'coin', '🪙 Quarter');
            spawnItem(30, 25, 'coin', '🪙 Quarter');
            spawnItem(-40, -20, 'soda', '🥤 Energy Soda');

            // Escape Doors with Two Distinct Windows and Golden Round Door Knobs
            spawnExitDoor(0, 89.2, 6, 3.2, 0.8, 0);
            spawnExitDoor(89.2, 0, 0.8, 3.2, 6, Math.PI / 2);
        }

        function spawnExitDoor(x, z, w, h, d, rotationY) {
            const group = new THREE.Group();
            group.position.set(x, 1.6, z);
            group.rotation.y = rotationY;

            // Yellow Exit Door Frame/Body
            const exitGeo = new THREE.BoxGeometry(w, h, d);
            const exitMat = new THREE.MeshStandardMaterial({ color: 0xfacc15, emissive: 0xca8a04, emissiveIntensity: 0.5, roughness: 0.2 });
            const exitDoor = new THREE.Mesh(exitGeo, exitMat);
            group.add(exitDoor);

            // Two Distinct Glass Windows (Left and Right)
            const winGeo = new THREE.BoxGeometry(w * 0.26, h * 0.45, d * 1.5);
            const winMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, emissive: 0x0284c7, emissiveIntensity: 0.4, transparent: true, opacity: 0.6, roughness: 0.1 });
            
            const winLeft = new THREE.Mesh(winGeo, winMat);
            winLeft.position.set(-w * 0.25, 0.4, 0);
            group.add(winLeft);

            const winRight = new THREE.Mesh(winGeo, winMat);
            winRight.position.set(w * 0.25, 0.4, 0);
            group.add(winRight);

            // Golden Round Door Knobs
            const knobGeo = new THREE.SphereGeometry(0.2, 16, 16);
            const knobMat = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.9, roughness: 0.1, emissive: 0xd97706, emissiveIntensity: 0.3 });
            
            const knob = new THREE.Mesh(knobGeo, knobMat);
            knob.position.set(w * 0.35, -0.2, d * 0.6);
            group.add(knob);

            group.userData = { type: 'exit', opened: false, mesh: exitDoor };
            scene.add(group);
            interactiveObjects.push(group);
            wallBoxes.push(new THREE.Box3().setFromObject(exitDoor));
        }

        function spawnVendingMachine(x, z, rotationY) {
            const group = new THREE.Group();
            group.position.set(x, 0, z);
            group.rotation.y = rotationY;

            const bodyGeo = new THREE.BoxGeometry(2.2, 3.8, 1.6);
            const bodyMat = new THREE.MeshStandardMaterial({ color: 0xdc2626, metalness: 0.5, roughness: 0.2 });
            const body = new THREE.Mesh(bodyGeo, bodyMat);
            body.position.y = 1.9;
            group.add(body);

            const glassGeo = new THREE.BoxGeometry(1.6, 2.0, 0.1);
            const glassMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, emissive: 0x0284c7, emissiveIntensity: 0.4, transparent: true, opacity: 0.7 });
            const glass = new THREE.Mesh(glassGeo, glassMat);
            glass.position.set(0, 2.2, 0.81);
            group.add(glass);

            const canColors = [0xef4444, 0x3b82f6, 0xfacc15, 0x10b981];
            for (let row = 0; row < 3; row++) {
                for (let col = -2; col <= 2; col++) {
                    const canGeo = new THREE.CylinderGeometry(0.12, 0.12, 0.4, 12);
                    const canMat = new THREE.MeshStandardMaterial({ color: canColors[(row + col) % canColors.length], metalness: 0.6 });
                    const can = new THREE.Mesh(canGeo, canMat);
                    can.position.set(col * 0.28, 1.6 + row * 0.45, 0.72);
                    group.add(can);
                }
            }

            group.userData = { type: 'vending' };
            scene.add(group);

            const bbox = new THREE.Box3().setFromObject(body);
            wallBoxes.push(bbox);
            interactiveObjects.push(group);
        }

        function spawnItem(x, z, itemType, name) {
            const group = new THREE.Group();
            group.position.set(x, 0, z);

            if (itemType === 'coin') {
                group.position.y = 0.5;
                const coinGeo = new THREE.CylinderGeometry(0.45, 0.45, 0.1, 32);
                const coinMat = new THREE.MeshStandardMaterial({ color: 0xfacc15, metalness: 0.9, roughness: 0.1, emissive: 0xca8a04, emissiveIntensity: 0.2 });
                const coin = new THREE.Mesh(coinGeo, coinMat);
                coin.rotation.x = Math.PI / 2;
                group.add(coin);
            } else if (itemType === 'soda') {
                group.position.y = 0.4;
                const canGeo = new THREE.CylinderGeometry(0.32, 0.32, 1.0, 24);
                const canMat = new THREE.MeshStandardMaterial({ color: 0xdc2626, metalness: 0.7, roughness: 0.2 });
                const can = new THREE.Mesh(canGeo, canMat);
                group.add(can);
            }

            group.userData = { type: 'pickup', itemType: itemType, name: name, collected: false };
            scene.add(group);
            interactiveObjects.push(group);
        }

        function spawnNotebook(x, z, id) {
            const deskGeo = new THREE.BoxGeometry(2.2, 1.2, 1.6);
            const deskMat = new THREE.MeshStandardMaterial({ color: 0x78350f, roughness: 0.6 });
            const desk = new THREE.Mesh(deskGeo, deskMat);
            desk.position.set(x, 0.6, z);
            desk.userData = { type: 'notebook', id: id, collected: false };
            scene.add(desk);
            wallBoxes.push(new THREE.Box3().setFromObject(desk));

            const nbGeo = new THREE.BoxGeometry(1.2, 0.2, 1.0);
            const nbMat = new THREE.MeshStandardMaterial({ color: 0xdc2626, roughness: 0.2 });
            const notebook = new THREE.Mesh(nbGeo, nbMat);
            notebook.position.set(x, 1.35, z);
            notebook.userData = { type: 'notebook', id: id, collected: false };
            scene.add(notebook);

            interactiveObjects.push(notebook);
            interactiveObjects.push(desk);
        }

        function setupControls() {
            window.addEventListener('keydown', (e) => {
                keyState[e.code] = true;
                if (e.code === 'KeyE' && isPointerLocked && !isQuizActive && !isZykerModalActive) {
                    checkInteraction();
                }
            });
            window.addEventListener('keyup', (e) => {
                keyState[e.code] = false;
            });

            document.addEventListener('mousemove', (e) => {
                if (!isPointerLocked || isQuizActive || isZykerModalActive) return;
                player.rotation.yaw -= e.movementX * 0.0025;
                player.rotation.pitch -= e.movementY * 0.0025;
                player.rotation.pitch = Math.max(-Math.PI / 2 + 0.1, Math.min(Math.PI / 2 - 0.1, player.rotation.pitch));
                camera.rotation.set(player.rotation.pitch, player.rotation.yaw, 0);

                updateInteractionPrompt();
            });

            document.addEventListener('click', () => {
                if (!isPointerLocked && isGameActive && !isQuizActive && !isZykerModalActive) {
                    document.body.requestPointerLock();
                } else if (isPointerLocked && !isQuizActive && !isZykerModalActive) {
                    checkInteraction();
                }
            });

            document.addEventListener('pointerlockchange', () => {
                isPointerLocked = document.pointerLockElement === document.body;
            });
        }

        function updateInteractionPrompt() {
            const raycaster = new THREE.Raycaster();
            raycaster.setFromCamera(new THREE.Vector2(0, 0), camera);
            const intersects = raycaster.intersectObjects(interactiveObjects, true);

            const prompt = document.getElementById('interaction-prompt');
            if (intersects.length > 0 && intersects[0].distance < 8.0) {
                let obj = intersects[0].object;
                while (obj.parent && obj.parent !== scene && !obj.userData.type) {
                    obj = obj.parent;
                }
                if ((obj.userData.type === 'notebook' && !obj.userData.collected) || obj.userData.type === 'pickup' || obj.userData.type === 'vending' || obj.userData.type === 'nono' || obj.userData.type === 'myles' || (obj.userData.type === 'exit' && !obj.userData.opened) || (obj.userData.type === 'zyker' && zyker.active)) {
                    prompt.classList.remove('hidden');
                    return;
                }
            }
            prompt.classList.add('hidden');
        }

        function checkInteraction() {
            const raycaster = new THREE.Raycaster();
            raycaster.setFromCamera(new THREE.Vector2(0, 0), camera);
            const intersects = raycaster.intersectObjects(interactiveObjects, true);

            if (intersects.length > 0 && intersects[0].distance < 8.0) {
                let obj = intersects[0].object;
                while (obj.parent && obj.parent !== scene && !obj.userData.type) {
                    obj = obj.parent;
                }

                if (obj.userData.type === 'notebook' && !obj.userData.collected) {
                    openQuizModal(obj);
                } else if (obj.userData.type === 'pickup' && !obj.userData.collected) {
                    obj.userData.collected = true;
                    scene.remove(obj);
                    playSound('pickup');
                    addItemToInventory(obj.userData.name);
                } else if (obj.userData.type === 'zyker' && zyker.active) {
                    openZykerModal();
                } else if (obj.userData.type === 'myles') {
                    if (hasItem('🪙 Quarter')) {
                        removeItem('🪙 Quarter');
                        playSound('pickup');
                        addItemToInventory('♟️ Chess Board');
                        showMessage("Myles: 'Here is a chess board! Use it to block and skip a wrong answer penalty!'");
                    } else {
                        playSound('correct');
                        showMessage("Myles: 'Bring me a Quarter and I'll trade you a chess board!'");
                    }
                } else if (obj.userData.type === 'nono') {
                    if (hasItem('🥤 Energy Soda')) {
                        removeItem('🥤 Energy Soda');
                        playSound('stun');
                        nono.isChasingRushton = true;
                        showMessage("Gave Energy Soda to Nono! Nono is hunting Mr. Rushton!");
                    } else {
                        playSound('correct');
                        showMessage("Nono says: 'Bring me an Energy Soda to stun Mr. Rushton!'");
                    }
                } else if (obj.userData.type === 'vending') {
                    if (hasItem('🪙 Quarter')) {
                        removeItem('🪙 Quarter');
                        playSound('pickup');
                        addItemToInventory('🥤 Energy Soda');
                        showMessage("Used Quarter! Got Energy Soda!");
                    } else {
                        showMessage("You need a Quarter for the vending machine!");
                    }
                } else if (obj.userData.type === 'exit' && !obj.userData.opened) {
                    if (player.notebooksCollected >= 7) {
                        obj.userData.opened = true;
                        obj.userData.mesh.material.color.setHex(0x22c55e);
                        exitsOpenedCount++;
                        playSound('correct');

                        if (exitsOpenedCount >= totalExits) {
                            alert("BOTH EXITS OPENED! YOU WIN!");
                            location.reload();
                        } else {
                            showMessage(`Exit opened! (${exitsOpenedCount}/${totalExits} exits activated)`);
                        }
                    } else {
                        showMessage("Find all 7 notebooks first!");
                    }
                }
            }
        }

        function openZykerModal() {
            const availableItems = player.inventory.filter(item => item !== null);
            if (availableItems.length === 0) {
                playSound('bully');
                showMessage("Zyker says: 'You have nothing I want! Come back with an item!'");
                return;
            }

            isZykerModalActive = true;
            document.exitPointerLock();
            const modal = document.getElementById('zyker-modal');
            const optionsContainer = document.getElementById('zyker-item-options');
            optionsContainer.innerHTML = '';

            player.inventory.forEach((item, slotIdx) => {
                if (item) {
                    const btn = document.createElement('button');
                    btn.className = "w-full bg-yellow-400 hover:bg-yellow-300 border-2 border-black font-pixel text-xs sm:text-sm py-3 px-4 rounded cursor-pointer flex justify-between items-center text-black font-bold";
                    btn.innerText = `GIVE ${item.toUpperCase()}`;
                    btn.onclick = () => {
                        giveItemToZyker(slotIdx);
                    };
                    optionsContainer.appendChild(btn);
                }
            });

            modal.classList.remove('hidden');
        }

        function giveItemToZyker(slotIdx) {
            const givenItem = player.inventory[slotIdx];
            removeItem(givenItem);
            playSound('bully');
            
            zyker.active = false;
            scene.remove(zykerSprite);
            
            const idx = wallBoxes.indexOf(zykerBox);
            if (idx !== -1) wallBoxes.splice(idx, 1);

            closeZykerModal();
            showMessage(`Zyker says: "Ayo chill!" and takes your ${givenItem}. West corridor cleared!`);
        }

        function closeZykerModal() {
            isZykerModalActive = false;
            document.getElementById('zyker-modal').classList.add('hidden');
            document.body.requestPointerLock();
        }

        function addItemToInventory(itemName) {
            for (let i = 0; i < 3; i++) {
                if (!player.inventory[i]) {
                    player.inventory[i] = itemName;
                    updateInventoryUI();
                    return;
                }
            }
            showMessage("Inventory full!");
        }

        function hasItem(itemName) {
            return player.inventory.includes(itemName);
        }

        function removeItem(itemName) {
            const idx = player.inventory.indexOf(itemName);
            if (idx !== -1) {
                player.inventory[idx] = null;
                updateInventoryUI();
            }
        }

        function useItem(slotIdx) {
            const item = player.inventory[slotIdx];
            if (item === '🥤 Energy Soda') {
                player.stamina = player.maxStamina;
                playSound('drink');
                player.inventory[slotIdx] = null;
                updateInventoryUI();
                showMessage("Restored Full Stamina!");
            } else if (item === '♟️ Chess Board') {
                showMessage("Chess Board ready! It will automatically block the next wrong answer penalty.");
            }
        }

        function updateInventoryUI() {
            for (let i = 0; i < 3; i++) {
                const slot = document.getElementById(`slot-${i}`);
                slot.innerText = player.inventory[i] ? player.inventory[i].split(' ')[0] : '';
            }
        }

        function showMessage(msg) {
            const el = document.getElementById('game-message');
            el.innerText = msg;
            el.classList.remove('hidden');
            setTimeout(() => el.classList.add('hidden'), 2500);
        }

        let currentNotebookObj = null;
        let currentProblemIndex = 0;

        function openQuizModal(nbObj) {
            isQuizActive = true;
            currentNotebookObj = nbObj;
            currentProblemIndex = 0;
            document.exitPointerLock();
            document.getElementById('quiz-modal').classList.remove('hidden');
            loadQuizProblem();
        }

        function loadQuizProblem() {
            if (currentProblemIndex >= 3) {
                currentNotebookObj.userData.collected = true;
                
                interactiveObjects.forEach(item => {
                    if (item.userData.id === currentNotebookObj.userData.id) {
                        item.userData.collected = true;
                        scene.remove(item);
                    }
                });

                player.notebooksCollected++;
                document.getElementById('notebook-count').innerText = `${player.notebooksCollected}/7`;
                
                rushton.angerLevel += 0.22;
                showMessage(`Notebook collected! Mr. Rushton is getting faster! (${player.notebooksCollected}/7)`);

                document.getElementById('quiz-modal').classList.add('hidden');
                document.getElementById('interaction-prompt').classList.add('hidden');
                
                isQuizActive = false;
                document.body.requestPointerLock();
                return;
            }

            document.getElementById('quiz-progress').innerText = `PROBLEM ${currentProblemIndex + 1}/3`;
            
            const isGlitchedProblem = (player.notebooksCollected >= 1 && currentProblemIndex === 2);

            let ans = 0;
            let opts = [];

            if (isGlitchedProblem) {
                document.getElementById('quiz-question').innerText = "CRASH_OVF_99 / 0 = ?";
                opts = [404, 666, 1337, -999];
            } else {
                const n1 = Math.floor(Math.random() * 9) + 1;
                const n2 = Math.floor(Math.random() * 9) + 1;
                ans = n1 + n2;
                document.getElementById('quiz-question').innerText = `${n1} + ${n2} = ?`;
                opts = [ans, ans + 1, ans - 1, ans + 2];
            }

            opts.sort(() => Math.random() - 0.5);
            const optsContainer = document.getElementById('quiz-options');
            optsContainer.innerHTML = '';

            opts.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = "w-full bg-yellow-400 hover:bg-yellow-300 border-2 border-black font-mono font-bold text-xl py-2 rounded cursor-pointer text-black";
                btn.innerText = opt;
                btn.onclick = () => {
                    if (!isGlitchedProblem && opt === ans) {
                        playSound('correct');
                        currentProblemIndex++;
                        loadQuizProblem();
                    } else {
                        if (hasItem('♟️ Chess Board')) {
                            removeItem('♟️ Chess Board');
                            playSound('correct');
                            showMessage("Chess Board used! Evaded wrong answer penalty!");
                            currentProblemIndex++;
                            loadQuizProblem();
                        } else {
                            playSound('wrong');
                            rushton.angerLevel += 0.35;
                            
                            if (player.notebooksCollected >= 1 || currentProblemIndex >= 2) {
                                if (!rushton.isAggro) {
                                    rushton.isAggro = true;
                                    showMessage("Mr. Rushton heard you get that wrong! He is now chasing you!");
                                }
                            }

                            currentProblemIndex++;
                            loadQuizProblem();
                        }
                    }
                };
                optsContainer.appendChild(btn);
            });
        }

        function checkCollision(newPos, radius = 0.6) {
            const playerBox = new THREE.Box3(
                new THREE.Vector3(newPos.x - radius, 0.1, newPos.z - radius),
                new THREE.Vector3(newPos.x + radius, 3.2, newPos.z + radius)
            );

            for (let box of wallBoxes) {
                if (playerBox.intersectsBox(box)) return true;
            }
            return false;
        }

        function updatePlayerPhysics() {
            if (!isGameActive || isQuizActive || isZykerModalActive) return;

            const isSprinting = !!(keyState['ShiftLeft'] || keyState['ShiftRight'] || keyState['Shift']);
            let speed = (isSprinting && player.stamina > 0) ? 0.09 : 0.06;

            if (isSprinting && player.stamina > 0) {
                player.stamina = Math.max(0, player.stamina - 0.45);
            } else if (player.stamina < player.maxStamina) {
                player.stamina = Math.min(player.maxStamina, player.stamina + 0.3);
            }
            document.getElementById('stamina-fill').style.width = `${(player.stamina / player.maxStamina) * 100}%`;

            // DIRECT AXIS MOVEMENT SCHEME (W = Up/Forward Z, S = Down/Backward Z, A = Left X, D = Right X)
            let moveX = 0, moveZ = 0;
            if (keyState['KeyW'] || keyState['ArrowUp'] || keyState['KeyZ']) moveZ -= 1; // Move Up (-Z)
            if (keyState['KeyS'] || keyState['ArrowDown']) moveZ += 1;                  // Move Down (+Z)
            if (keyState['KeyA'] || keyState['ArrowLeft'] || keyState['KeyQ']) moveX -= 1; // Move Left (-X)
            if (keyState['KeyD'] || keyState['ArrowRight']) moveX += 1;                 // Move Right (+X)

            const len = Math.hypot(moveX, moveZ);
            if (len > 0) {
                const nextX = player.position.x + (moveX / len) * speed;
                const nextZ = player.position.z + (moveZ / len) * speed;

                if (!checkCollision({ x: nextX, z: player.position.z }, player.radius)) player.position.x = nextX;
                if (!checkCollision({ x: player.position.x, z: nextZ }, player.radius)) player.position.z = nextZ;
            }

            camera.position.copy(player.position);
        }

        function getDirectSteeringDir(currentPos, targetPos, radius) {
            const direct = new THREE.Vector3().subVectors(targetPos, currentPos);
            direct.y = 0;
            direct.normalize();

            const testPos = { x: currentPos.x + direct.x * 1.5, z: currentPos.z + direct.z * 1.5 };
            if (!checkCollision(testPos, radius)) {
                return direct;
            }

            const testAngles = [0.4, -0.4, 0.8, -0.8, 1.2, -1.2, 1.6, -1.6, 2.0, -2.0, 2.4, -2.4, 2.8, -2.8, Math.PI];
            for (let angle of testAngles) {
                const rotated = direct.clone().applyAxisAngle(new THREE.Vector3(0, 1, 0), angle);
                const samplePos = { x: currentPos.x + rotated.x * 1.5, z: currentPos.z + rotated.z * 1.5 };
                if (!checkCollision(samplePos, radius)) {
                    return rotated;
                }
            }

            return direct;
        }

        function updateRushtonAI() {
            if (!isGameActive || isQuizActive || isZykerModalActive) return;

            if (!rushton.isAggro) {
                rushtonSprite.position.copy(rushton.position);
                return;
            }

            if (rushton.stunDurationTimer > 0) {
                rushton.stunDurationTimer--;
                return;
            }

            rushton.stuckCheckTimer++;
            if (rushton.stuckCheckTimer > 2400) {
                rushton.stuckCheckTimer = 0;
                if (rushton.position.distanceTo(rushton.lastPosition) < 0.8) {
                    let telePlaced = false;
                    for (let attempt = 0; attempt < 10; attempt++) {
                        const angle = Math.random() * Math.PI * 2;
                        const dist = 10 + Math.random() * 4;
                        const tX = player.position.x + Math.cos(angle) * dist;
                        const tZ = player.position.z + Math.sin(angle) * dist;
                        if (!checkCollision({ x: tX, z: tZ }, rushton.radius)) {
                            rushton.position.set(tX, 1.6, tZ);
                            telePlaced = true;
                            break;
                        }
                    }
                    if (!telePlaced) {
                        rushton.position.set(player.position.x + 10, 1.6, player.position.z + 10);
                    }
                }
                rushton.lastPosition.copy(rushton.position);
            }

            rushton.slapTimer++;
            const currentSlapInterval = Math.max(8, rushton.baseSlapInterval - (player.notebooksCollected * 3));
            
            if (rushton.slapTimer >= currentSlapInterval) {
                rushton.slapTimer = 0;
                playSound('slap');

                const stepDist = rushton.baseSpeed * rushton.angerLevel * 3.8;
                const moveDir = rushtonMiniMap.getBestNavDir(rushton.position, player.position);

                const nextX = rushton.position.x + moveDir.x * stepDist;
                const nextZ = rushton.position.z + moveDir.z * stepDist;

                if (!checkCollision({ x: nextX, z: rushton.position.z }, rushton.radius)) {
                    rushton.position.x = nextX;
                }
                if (!checkCollision({ x: rushton.position.x, z: nextZ }, rushton.radius)) {
                    rushton.position.z = nextZ;
                }

                rushtonSprite.position.copy(rushton.position);
            }

            if (rushton.position.distanceTo(player.position) < 2.5) {
                playSound('jumpscare');
                isGameActive = false;
                document.exitPointerLock();
                document.getElementById('end-screen').classList.remove('hidden');
            }
        }

        function updateNonoAI() {
            if (!isGameActive || isQuizActive || isZykerModalActive) return;

            let targetPos = nono.homePosition;
            if (nono.isChasingRushton) {
                targetPos = rushton.position;

                nono.stuckCheckTimer++;
                if (nono.stuckCheckTimer > 2400) {
                    nono.stuckCheckTimer = 0;
                    if (nono.position.distanceTo(nono.lastPosition) < 0.8) {
                        const escapeDir = new THREE.Vector3().subVectors(rushton.position, nono.position).normalize();
                        const nudgePos = { x: nono.position.x + escapeDir.x * 3.0, z: nono.position.z + escapeDir.z * 3.0 };
                        if (!checkCollision(nudgePos, nono.radius)) {
                            nono.position.x = nudgePos.x;
                            nono.position.z = nudgePos.z;
                        }
                    }
                    nono.lastPosition.copy(nono.position);
                }

                if (nono.position.distanceTo(rushton.position) < 3.5) {
                    playSound('stun');
                    rushton.stunDurationTimer = 1200;
                    nono.isChasingRushton = false;
                    nono.position.copy(nono.homePosition);
                    showMessage("Nono stunned Mr. Rushton!");
                }
            }

            const stepDist = nono.speed;
            const moveDir = getDirectSteeringDir(nono.position, targetPos, nono.radius);

            const nextX = nono.position.x + moveDir.x * stepDist;
            const nextZ = nono.position.z + moveDir.z * stepDist;

            if (!checkCollision({ x: nextX, z: nono.position.z }, nono.radius)) {
                nono.position.x = nextX;
            }
            if (!checkCollision({ x: nextX, z: nextZ }, nono.radius)) {
                nono.position.z = nextZ;
            }

            nonoSprite.position.copy(nono.position);
        }

        function animate() {
            requestAnimationFrame(animate);
            updatePlayerPhysics();
            updateRushtonAI();
            updateNonoAI();
            renderer.render(scene, camera);
        }

        function startGame() {
            initAudio();
            init3D();
            isGameActive = true;
            document.getElementById('start-screen').classList.add('hidden');
            setTimeout(() => {
                document.body.requestPointerLock();
            }, 100);
            animate();
        }

        function resetGame() {
            location.reload();
        }

        function onWindowResize() {
            if (!camera || !renderer) return;
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }
    </script>
</body>
</html>
