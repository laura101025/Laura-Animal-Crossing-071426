Super, please transform previous design into 動物森友會城市大家庭 RPG game. Also adding additional feature that user can create new map, route and charming flowers, animals, gifts and more. Please let user to choose character, equipments and more. User can view each route with 5 different views. Please also adding 3 additional wow ai features (default gemini-3.1-flash-lite, user can select other models). Please fix potential bugs and iterate until get excellent results. Ending with 20 comprehensive follow up questions.


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aetheria Bloom: RPG Sandbox Expedition</title>
    <!-- Tailwind CSS for modern premium dashboard layout -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts for retro/fantasy look -->
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@700&family=Quicksand:wght@500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Quicksand', sans-serif;
            background: radial-gradient(circle, #1a2c1d 0%, #0d160e 100%);
            color: #e2f0d9;
        }
        .fantasy-header {
            font-family: 'Cinzel', serif;
        }
        .pixel-card {
            background: rgba(20, 35, 22, 0.85);
            backdrop-filter: blur(8px);
            border: 2px solid #3c7d45;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.5);
        }
        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #111e12;
        }
        ::-webkit-scrollbar-thumb {
            background: #3c7d45;
            border-radius: 3px;
        }
    </style>
</head>
<body class="min-h-screen p-4 flex flex-col items-center justify-start">

<div class="w-full max-w-7xl flex flex-col gap-6">
    
    <!-- Top Bar with Stats & Game Title -->
    <div class="w-full pixel-card rounded-2xl p-5 flex flex-col md:flex-row justify-between items-center gap-4">
        <div>
            <h1 class="text-3xl font-bold text-[#8fff9f] fantasy-header flex items-center gap-2">
                🌿 Aetheria Bloom <span class="text-xs bg-[#244f2b] text-[#8fff9f] border border-[#3c7d45] px-3 py-1 rounded-full uppercase tracking-widest font-sans">RPG Sandbox</span>
            </h1>
            <p class="text-xs text-gray-400 mt-1">Design your path, gear up your hero, and venture forth with your Pikmin party.</p>
        </div>
        
        <div class="flex flex-wrap gap-3 items-center">
            <div class="bg-[#111e12] px-4 py-2 rounded-xl border border-[#3c7d45] text-center min-w-[90px]">
                <span class="block text-[10px] text-gray-500 font-bold uppercase">Hero HP</span>
                <span id="stat-hp" class="text-lg font-bold text-red-400">100 / 100</span>
            </div>
            <div class="bg-[#111e12] px-4 py-2 rounded-xl border border-[#3c7d45] text-center min-w-[90px]">
                <span class="block text-[10px] text-gray-500 font-bold uppercase">Gold Coins</span>
                <span id="stat-gold" class="text-lg font-bold text-yellow-400">350</span>
            </div>
            <div class="bg-[#111e12] px-4 py-2 rounded-xl border border-[#3c7d45] text-center min-w-[90px]">
                <span class="block text-[10px] text-gray-500 font-bold uppercase">Flower Petals</span>
                <span id="stat-petals" class="text-lg font-bold text-pink-400">120</span>
            </div>
            <div class="bg-[#111e12] px-4 py-2 rounded-xl border border-[#3c7d45] text-center min-w-[90px]">
                <span class="block text-[10px] text-gray-500 font-bold uppercase">Expeditions</span>
                <span id="stat-expeditions" class="text-lg font-bold text-cyan-400">0</span>
            </div>
        </div>
    </div>

    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
        
        <!-- Left Sidebar: Character Creator & Inventory -->
        <div class="lg:col-span-3 flex flex-col gap-6">
            
            <!-- Character setup Card -->
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-4">
                <h2 class="text-lg font-bold text-[#8fff9f] border-b border-[#3c7d45] pb-2 flex items-center gap-2">🛡️ Hero Customization</h2>
                
                <div>
                    <label class="block text-xs text-gray-400 font-bold mb-1">Hero Name</label>
                    <input id="hero-name" type="text" value="Aveline Woodwalker" class="w-full bg-[#111e12] border border-[#3c7d45] rounded-lg p-2 text-sm text-white focus:outline-none focus:border-[#8fff9f]">
                </div>

                <div>
                    <label class="block text-xs text-gray-400 font-bold mb-1">RPG Archetype Class</label>
                    <select id="hero-class" class="w-full bg-[#111e12] border border-[#3c7d45] rounded-lg p-2 text-sm text-white" onchange="updateHeroStats()">
                        <option value="knight">🛡️ Fey Knight (+Attack/Armor)</option>
                        <option value="mage">🔮 Sprout Mage (+Magic/Spells)</option>
                        <option value="ranger">🏹 Bloom Ranger (+Critical/Speed)</option>
                        <option value="warden">🦌 Moss Warden (+Max HP/Defense)</option>
                    </select>
                </div>

                <div class="grid grid-cols-2 gap-2 text-xs">
                    <div class="bg-[#152517] p-2 rounded border border-[#2b5932]">
                        <span class="text-gray-400 block">Class Weapon:</span>
                        <span id="class-weapon" class="font-bold text-yellow-300">Sylvan Blade</span>
                    </div>
                    <div class="bg-[#152517] p-2 rounded border border-[#2b5932]">
                        <span class="text-gray-400 block">Spells Power:</span>
                        <span id="class-magic" class="font-bold text-pink-300">Nature Gust</span>
                    </div>
                </div>

                <div class="mt-2 border-t border-[#3c7d45] pt-3">
                    <h3 class="text-xs text-[#8fff9f] font-bold mb-2">Equip Mystical Accessories</h3>
                    <div class="grid grid-cols-3 gap-2">
                        <button id="gear-ring" class="bg-[#111e12] hover:bg-[#203622] p-2 rounded border border-yellow-500/50 text-[10px] text-center" onclick="equipAccessory('ring')">
                            💍 Star Ring
                        </button>
                        <button id="gear-amulet" class="bg-[#111e12] hover:bg-[#203622] p-2 rounded border border-[#3c7d45] text-[10px] text-center" onclick="equipAccessory('amulet')">
                            📿 Bloom Charm
                        </button>
                        <button id="gear-boots" class="bg-[#111e12] hover:bg-[#203622] p-2 rounded border border-[#3c7d45] text-[10px] text-center" onclick="equipAccessory('boots')">
                            🥾 Mossy Boots
                        </button>
                    </div>
                </div>
            </div>

            <!-- Sandbox Tool Palette -->
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-4">
                <h2 class="text-lg font-bold text-[#8fff9f] border-b border-[#3c7d45] pb-2 flex items-center gap-2">🎨 Sandbox Map Brush</h2>
                <p class="text-xs text-gray-400">Select an asset element below, then left-click directly on the grid-map to paint structures and paths.</p>
                
                <div class="grid grid-cols-2 gap-2">
                    <button id="brush-path" class="brush-btn bg-[#244f2b] border-2 border-yellow-400 text-[#8fff9f] p-2 rounded-xl text-xs font-bold transition-all" onclick="selectBrush('path')">
                        🧱 Walkway Path
                    </button>
                    <button id="brush-flower" class="brush-btn bg-[#111e12] hover:bg-[#244f2b] p-2 rounded-xl text-xs font-bold transition-all" onclick="selectBrush('flower')">
                        🌸 Magical Lily
                    </button>
                    <button id="brush-critter" class="brush-btn bg-[#111e12] hover:bg-[#244f2b] p-2 rounded-xl text-xs font-bold transition-all" onclick="selectBrush('critter')">
                        🦊 Forest Beast
                    </button>
                    <button id="brush-gift" class="brush-btn bg-[#111e12] hover:bg-[#244f2b] p-2 rounded-xl text-xs font-bold transition-all" onclick="selectBrush('gift')">
                        🎁 Mystic Treasure
                    </button>
                </div>

                <div class="bg-[#111e12] p-3 rounded-xl border border-[#3c7d45]">
                    <span class="text-xs text-gray-400 block mb-1">Custom Map Seed Generator</span>
                    <div class="flex gap-2">
                        <input id="map-seed" type="text" placeholder="Aetheria_Wilds" class="bg-[#192b1b] border border-[#3c7d45] text-xs p-1.5 rounded flex-1 focus:outline-none">
                        <button class="bg-[#2d5c34] text-xs font-bold px-3 py-1 rounded hover:bg-[#3d7a46]" onclick="generateRandomMap()">Generate</button>
                    </div>
                </div>
            </div>

        </div>

        <div class="lg:col-span-6 flex flex-col gap-4">
            
            <!-- Perspective Selector Header -->
            <div class="pixel-card rounded-2xl p-3 flex flex-wrap justify-between items-center gap-2">
                <span class="text-xs font-bold text-gray-400 uppercase tracking-widest pl-2">Camera Perspective:</span>
                <div class="flex flex-wrap gap-1">
                    <button id="view-meadow" class="view-tab-btn bg-[#244f2b] text-[#8fff9f] text-xs px-3 py-2 rounded-lg font-bold" onclick="changeView('meadow')">🎬 Meadow View</button>
                    <button id="view-tactical" class="view-tab-btn bg-[#111e12] hover:bg-[#1a2e1d] text-gray-400 text-xs px-3 py-2 rounded-lg font-bold" onclick="changeView('tactical')">🗺️ Tactical 2D</button>
                    <button id="view-isometric" class="view-tab-btn bg-[#111e12] hover:bg-[#1a2e1d] text-gray-400 text-xs px-3 py-2 rounded-lg font-bold" onclick="changeView('isometric')">🔷 Iso Grid</button>
                    <button id="view-firstperson" class="view-tab-btn bg-[#111e12] hover:bg-[#1a2e1d] text-gray-400 text-xs px-3 py-2 rounded-lg font-bold" onclick="changeView('firstperson')">👓 First Person</button>
                    <button id="view-log" class="view-tab-btn bg-[#111e12] hover:bg-[#1a2e1d] text-gray-400 text-xs px-3 py-2 rounded-lg font-bold" onclick="changeView('log')">📜 Battle Log</button>
                </div>
            </div>

            <!-- Canvas Viewport Frame -->
            <div class="relative overflow-hidden rounded-3xl border-4 border-[#3c7d45] shadow-2xl bg-[#090d09]">
                <canvas id="rpgCanvas" width="800" height="420" class="w-full block h-auto cursor-crosshair"></canvas>
            </div>

            <!-- Real-time Hero Action Deck -->
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-4">
                <div class="flex justify-between items-center border-b border-[#3c7d45] pb-2">
                    <h3 class="font-bold text-[#8fff9f] text-sm">⚔️ Battle Expedition Commands</h3>
                    <div class="flex gap-2">
                        <button id="btn-combat" class="bg-red-800 hover:bg-red-700 text-white text-xs font-bold px-3 py-1.5 rounded-lg border border-red-500" onclick="triggerManualAttack()">
                            ⚔️ Attack Beast
                        </button>
                        <button id="btn-gather" class="bg-blue-800 hover:bg-blue-700 text-white text-xs font-bold px-3 py-1.5 rounded-lg border border-blue-500" onclick="triggerHarvest()">
                            🌸 Harvest Flower
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-2 sm:grid-cols-4 gap-2">
                    <button class="bg-[#111e12] hover:bg-[#203622] text-xs p-2.5 rounded-xl border border-[#3c7d45] font-bold text-center" onclick="performRPGAction('heal')">
                        🍷 Brew Elixir (HP)
                    </button>
                    <button class="bg-[#111e12] hover:bg-[#203622] text-xs p-2.5 rounded-xl border border-[#3c7d45] font-bold text-center" onclick="performRPGAction('spell')">
                        ✨ Cast Sprout Gale
                    </button>
                    <button class="bg-[#111e12] hover:bg-[#203622] text-xs p-2.5 rounded-xl border border-[#3c7d45] font-bold text-center" onclick="performRPGAction('whistle')">
                        🎵 Recall Squad
                    </button>
                    <button class="bg-[#111e12] hover:bg-[#203622] text-xs p-2.5 rounded-xl border border-[#3c7d45] font-bold text-center" onclick="performRPGAction('boost')">
                        ⚡ Quick Dash
                    </button>
                </div>
            </div>

        </div>

        <div class="lg:col-span-3 flex flex-col gap-6">
            
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-4">
                <h2 class="text-lg font-bold text-[#8fff9f] border-b border-[#3c7d45] pb-2 flex items-center gap-2">🧠 AI Core Settings</h2>
                
                <div>
                    <label class="block text-xs text-gray-400 font-bold mb-1">Core AI Model</label>
                    <select id="ai-model" class="w-full bg-[#111e12] border border-[#3c7d45] rounded-lg p-2 text-xs text-white">
                        <option value="gemini-3.1-flash-lite">gemini-3.1-flash-lite (Default Fast)</option>
                        <option value="gemini-3-flash-preview">gemini-3-flash-preview (Creative High-Fi)</option>
                        <option value="gemini-2.5-flash-image-preview">gemini-2.5-flash-image-preview (Visions)</option>
                    </select>
                </div>

                <!-- Custom API Key input to prevent execution failures when user wants custom keys -->
                <div>
                    <label class="block text-xs text-gray-400 font-bold mb-1">Custom API Key (Optional)</label>
                    <input id="ai-key" type="password" placeholder="Canvas handles automatically" class="w-full bg-[#111e12] border border-[#3c7d45] rounded-lg p-2 text-xs text-white focus:outline-none focus:border-[#8fff9f]">
                </div>
            </div>

            <!-- Feature 1: AI Route Quest Narrator -->
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-3">
                <div class="flex justify-between items-center">
                    <h3 class="text-sm font-bold text-[#8fff9f]">📜 AI Quest Narrator</h3>
                    <button class="text-[10px] bg-[#244f2b] text-[#8fff9f] px-2 py-1 rounded border border-[#3c7d45] hover:bg-[#346d3b]" onclick="generateAIQuest()">Analyze Map</button>
                </div>
                <p class="text-[11px] text-gray-400">Generate local folklore and unique sidequests matching the elements painted on your canvas.</p>
                <div id="quest-output" class="bg-[#111e12] p-3 rounded-xl border border-[#2b4c2f] text-xs text-gray-300 max-h-[110px] overflow-y-auto leading-relaxed italic">
                    Create some items or path on the grid, then hit 'Analyze Map' to summon the Chronicler.
                </div>
            </div>

            <!-- Feature 2: AI Dream-Flower Sculptor -->
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-3">
                <div class="flex justify-between items-center">
                    <h3 class="text-sm font-bold text-[#8fff9f]">🌸 AI Dream-Flower</h3>
                    <button class="text-[10px] bg-[#244f2b] text-[#8fff9f] px-2 py-1 rounded border border-[#3c7d45] hover:bg-[#346d3b]" onclick="generateAIDreamFlower()">Sculpt Sprout</button>
                </div>
                <div class="flex gap-1">
                    <input id="flower-desc" type="text" placeholder="e.g. Glowing Void Poppy" class="flex-1 bg-[#111e12] border border-[#3c7d45] rounded p-1 text-xs text-white focus:outline-none">
                </div>
                <div id="flower-output" class="bg-[#111e12] p-3 rounded-xl border border-[#2b4c2f] text-xs text-gray-300 max-h-[100px] overflow-y-auto">
                    Design a botanical entity above and request an AI sculpt.
                </div>
            </div>

            <!-- Feature 3: AI Monster Companion Whisperer -->
            <div class="pixel-card rounded-2xl p-4 flex flex-col gap-3">
                <div class="flex justify-between items-center">
                    <h3 class="text-sm font-bold text-[#8fff9f]">🦊 Beast Whisperer</h3>
                    <button class="text-[10px] bg-[#244f2b] text-[#8fff9f] px-2 py-1 rounded border border-[#3c7d45] hover:bg-[#346d3b]" onclick="whisperToMonster()">Translate Mood</button>
                </div>
                <p class="text-[11px] text-gray-400">Negotiate with, tame, or listen to the emotional thoughts of forest monsters on the trail.</p>
                <div id="whisper-output" class="bg-[#111e12] p-3 rounded-xl border border-[#2b4c2f] text-xs text-gray-300 max-h-[100px] overflow-y-auto italic">
                    Wait for a Forest Beast to spawn, then listen to its thoughts.
                </div>
            </div>

        </div>

    </div>

</div>

<!-- System Notification Banners -->
<div id="toast" class="fixed top-5 right-5 z-50 bg-[#152e18] border border-[#48a652] text-white px-4 py-3 rounded-xl shadow-2xl opacity-0 transform translate-y-[-10px] transition-all duration-300 pointer-events-none text-sm font-bold">
    Notifications
</div>

<script>
    const canvas = document.getElementById('rpgCanvas');
    const ctx = canvas.getContext('2d');

    // Engine Core States
    let currentView = 'meadow'; 
    let activeBrush = 'path';
    let heroHP = 100;
    let maxHP = 100;
    let gold = 350;
    let petals = 120;
    let expeditionsCount = 0;
    let mapGrid = [];
    let customDreamFlowerStats = null;
    
    // Position/Movement Variables
    let bgOffset = 0;
    let heroX = 120;
    let heroY = 260;
    let targetHeroY = 260;
    let moveSpeed = 2;
    let activeWarpMultiplier = 1;
    let animationFrame = 0;

    // Grid Dimensions
    const GRID_COLS = 16;
    const GRID_ROWS = 9;
    const CELL_SIZE = 50; // Coordinates grid of 800x450 scaled to fit 800x420 nicely

    // RPG Entities
    let pikminSquad = [];
    let monsters = [];
    let particleSystems = [];
    let notificationsLog = [
        "Aetheria Expedition launched in Sylvan Meadow Mode.",
        "Your Pikmin squad has assembled behind you."
    ];

    // Character gear stats modifications
    let equipment = { ring: false, amulet: false, boots: false };

    // Set Up default maps
    function initSandboxGrid() {
        mapGrid = [];
        for (let r = 0; r < GRID_ROWS; r++) {
            mapGrid[r] = [];
            for (let c = 0; c < GRID_COLS; c++) {
                // Initialize default path in the center row
                if (r === 5) {
                    mapGrid[r][c] = { type: 'path', object: null };
                } else {
                    mapGrid[r][c] = { type: 'meadow', object: null };
                }
            }
        }
        
        // Spawn default landmarks
        mapGrid[5][4].object = { name: 'Sylvan Sprout', type: 'flower', color: '#ff7bf4' };
        mapGrid[5][10].object = { name: 'Mossy Bulborb', type: 'critter', behavior: 'sleepy' };
        mapGrid[5][13].object = { name: 'Chest of Spores', type: 'gift', amount: 50 };
    }

    // Dynamic map creation
    function generateRandomMap() {
        const seedStr = document.getElementById('map-seed').value || "Default";
        showToast("Generating map with Seed: " + seedStr);
        
        // Create path and scattering elements deterministically using hash of seed
        let hash = 0;
        for (let i = 0; i < seedStr.length; i++) {
            hash = seedStr.charCodeAt(i) + ((hash << 5) - hash);
        }

        for (let r = 0; r < GRID_ROWS; r++) {
            for (let c = 0; c < GRID_COLS; c++) {
                let val = Math.abs(Math.sin(hash + r * 13 + c * 37));
                if (r === 5 || val > 0.72) {
                    mapGrid[r][c] = { type: 'path', object: null };
                } else {
                    mapGrid[r][c] = { type: 'meadow', object: null };
                }

                // Scatter extra features
                if (val > 0.88 && r !== 5) {
                    mapGrid[r][c].object = { name: 'Charming Violet', type: 'flower', color: '#8d6eff' };
                } else if (val > 0.94) {
                    mapGrid[r][c].object = { name: 'Crying Wisp', type: 'critter', behavior: 'cautious' };
                } else if (val > 0.97) {
                    mapGrid[r][c].object = { name: 'Forgotten Bag', type: 'gift', amount: 25 };
                }
            }
        }
    }

    function setupSquad() {
        pikminSquad = [];
        const colors = ['#ff4444', '#ffa300', '#2575fc', '#ae3eff', '#36d399'];
        const features = ['nose', 'ears', 'gills', 'chubby', 'wings'];
        
        for (let i = 0; i < 5; i++) {
            pikminSquad.push({
                offsetIdx: i,
                x: heroX - 40 - (i * 28),
                y: heroY + 10,
                color: colors[i],
                feature: features[i],
                jumpOffset: Math.random() * Math.PI,
                jumpSpeed: 0.12 + Math.random() * 0.04
            });
        }
    }

    // Handle brush tools on canvas click
    canvas.addEventListener('click', function(event) {
        const rect = canvas.getBoundingClientRect();
        // Translate visual layout coordinate logic to pixel bounds
        const clickX = (event.clientX - rect.left) * (canvas.width / rect.width);
        const clickY = (event.clientY - rect.top) * (canvas.height / rect.height);
        
        const col = Math.floor(clickX / CELL_SIZE);
        const row = Math.floor(clickY / CELL_SIZE);

        if (row >= 0 && row < GRID_ROWS && col >= 0 && col < GRID_COLS) {
            applySandboxBrush(row, col);
        }
    });

    function selectBrush(brushName) {
        activeBrush = brushName;
        document.querySelectorAll('.brush-btn').forEach(btn => {
            btn.classList.remove('bg-[#244f2b]', 'border-yellow-400');
            btn.classList.add('bg-[#111e12]');
        });
        const currentBtn = document.getElementById(`brush-${brushName}`);
        if(currentBtn) {
            currentBtn.classList.remove('bg-[#111e12]');
            currentBtn.classList.add('bg-[#244f2b]', 'border-yellow-400');
        }
    }

    function applySandboxBrush(row, col) {
        if (activeBrush === 'path') {
            mapGrid[row][col].type = 'path';
            showToast(`Painted path block at [${col}, ${row}]`);
        } else if (activeBrush === 'flower') {
            mapGrid[row][col].object = { name: 'Charming Bloom', type: 'flower', color: '#ff69b4' };
            showToast(`Planted magical bloom at [${col}, ${row}]`);
        } else if (activeBrush === 'critter') {
            mapGrid[row][col].object = { name: 'Woodland Beast', type: 'critter', behavior: 'growly' };
            showToast(`Spawned forest creature at [${col}, ${row}]`);
        } else if (activeBrush === 'gift') {
            mapGrid[row][col].object = { name: 'Gilded Satchel', type: 'gift', amount: 80 };
            showToast(`Placed a hidden chest at [${col}, ${row}]`);
        }
    }

    function updateHeroStats() {
        const hClass = document.getElementById('hero-class').value;
        const wSpan = document.getElementById('class-weapon');
        const mSpan = document.getElementById('class-magic');
        
        if (hClass === 'knight') {
            wSpan.innerText = "Heavy Broadsword";
            mSpan.innerText = "Aura Shield";
            maxHP = 120;
            heroHP = 120;
        } else if (hClass === 'mage') {
            wSpan.innerText = "Yew Staff";
            mSpan.innerText = "Bramble Storm";
            maxHP = 80;
            heroHP = 80;
        } else if (hClass === 'ranger') {
            wSpan.innerText = "Sprouted Bow";
            mSpan.innerText = "Wind Arrow";
            maxHP = 90;
            heroHP = 90;
        } else if (hClass === 'warden') {
            wSpan.innerText = "Spear of Roots";
            mSpan.innerText = "Earthen Wall";
            maxHP = 150;
            heroHP = 150;
        }
        document.getElementById('stat-hp').innerText = `${heroHP} / ${maxHP}`;
        showToast(`Archetype changed: HP adjusted to ${heroHP}`);
    }

    function equipAccessory(type) {
        equipment[type] = !equipment[type];
        const btn = document.getElementById(`gear-${type}`);
        if(equipment[type]) {
            btn.classList.add('bg-yellow-800', 'border-yellow-400');
            showToast(`Equipped ${btn.innerText.trim()}! Stat boost activated.`);
        } else {
            btn.classList.remove('bg-yellow-800', 'border-yellow-400');
            showToast(`Removed ${btn.innerText.trim()}.`);
        }
    }

    function changeView(view) {
        currentView = view;
        document.querySelectorAll('.view-tab-btn').forEach(btn => {
            btn.classList.remove('bg-[#244f2b]', 'text-[#8fff9f]');
            btn.classList.add('bg-[#111e12]', 'text-gray-400');
        });
        const currentBtn = document.getElementById(`view-${view}`);
        if(currentBtn) {
            currentBtn.classList.remove('bg-[#111e12]', 'text-gray-400');
            currentBtn.classList.add('bg-[#244f2b]', 'text-[#8fff9f]');
        }
        showToast(`Visual viewport shifted: ${view.toUpperCase()}`);
    }

    // RPG Commands Execution Engine
    function triggerManualAttack() {
        // Attack adjacent critters
        let hit = false;
        for (let r = 0; r < GRID_ROWS; r++) {
            for (let c = 0; c < GRID_COLS; c++) {
                if (mapGrid[r][c].object && mapGrid[r][c].object.type === 'critter') {
                    createSparkleSystem(c * CELL_SIZE + 25, r * CELL_SIZE + 25, '#ff5555');
                    mapGrid[r][c].object = null;
                    gold += 75;
                    hit = true;
                }
            }
        }
        if (hit) {
            addLog("Sword Slash launched! Forest beast slain (+75 Gold).");
            document.getElementById('stat-gold').innerText = gold;
            showToast("Combat victory! Beast vanquished.");
        } else {
            showToast("No beasts within striking range.");
        }
    }

    function triggerHarvest() {
        let hit = false;
        for (let r = 0; r < GRID_ROWS; r++) {
            for (let c = 0; c < GRID_COLS; c++) {
                if (mapGrid[r][c].object && mapGrid[r][c].object.type === 'flower') {
                    createSparkleSystem(c * CELL_SIZE + 25, r * CELL_SIZE + 25, '#ffaaff');
                    mapGrid[r][c].object = null;
                    petals += 30;
                    hit = true;
                }
            }
        }
        if (hit) {
            addLog("Pikmin gathered magic petals from adjacent blooming wild lilies.");
            document.getElementById('stat-petals').innerText = petals;
            showToast("Harvest success! Flower gathered.");
        } else {
            showToast("No harvestable flowers nearby.");
        }
    }

    function performRPGAction(action) {
        if (action === 'heal') {
            if (gold >= 50) {
                gold -= 50;
                heroHP = Math.min(maxHP, heroHP + 40);
                addLog("Drank restorative Elixir. Recovered 40 HP.");
                document.getElementById('stat-hp').innerText = `${heroHP} / ${maxHP}`;
                document.getElementById('stat-gold').innerText = gold;
                showToast("Healed successfully!");
            } else {
                showToast("Insufficient gold coins to brew elixir.");
            }
        } else if (action === 'spell') {
            addLog("Cast Bramble Sprout gust, slowing down incoming hazards.");
            createSparkleSystem(heroX + 100, heroY, '#aaffaa');
            showToast("Spell cast completed!");
        } else if (action === 'whistle') {
            addLog("Blew Sylvan Whistle. Pikmin regrouped inside standard configuration.");
            setupSquad();
            showToast("Squad aligned.");
        } else if (action === 'boost') {
            activeWarpMultiplier = activeWarpMultiplier === 1 ? 2.5 : 1;
            showToast(activeWarpMultiplier > 1 ? "Dash Mode Activated!" : "Regular walk pace restored.");
        }
    }

    function createSparkleSystem(x, y, color) {
        for (let i = 0; i < 12; i++) {
            particleSystems.push({
                x: x,
                y: y,
                vx: (Math.random() - 0.5) * 6,
                vy: (Math.random() - 0.5) * 6,
                life: 1.0,
                decay: 0.03 + Math.random() * 0.04,
                color: color
            });
        }
    }

    function addLog(text) {
        notificationsLog.unshift(text);
        if (notificationsLog.length > 20) notificationsLog.pop();
    }

    function showToast(message) {
        const toast = document.getElementById('toast');
        toast.innerText = message;
        toast.style.opacity = '1';
        toast.style.transform = 'translateY(0px)';
        setTimeout(() => {
            toast.style.opacity = '0';
            toast.style.transform = 'translateY(-10px)';
        }, 2200);
    }

    function renderIsometric() {
        ctx.fillStyle = '#0f1f11';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        const isoX = canvas.width / 2;
        const isoY = 60;

        for (let r = 0; r < GRID_ROWS; r++) {
            for (let c = 0; c < GRID_COLS; c++) {
                // Projection transformation formulas
                let screenX = isoX + (c - r) * 24;
                let screenY = isoY + (c + r) * 14;

                // Alternate background shading
                ctx.fillStyle = (r + c) % 2 === 0 ? '#1e3822' : '#274b2c';
                if (mapGrid[r][c].type === 'path') {
                    ctx.fillStyle = '#6e5033';
                }

                ctx.beginPath();
                ctx.moveTo(screenX, screenY);
                ctx.lineTo(screenX + 24, screenY + 14);
                ctx.lineTo(screenX, screenY + 28);
                ctx.lineTo(screenX - 24, screenY + 14);
                ctx.closePath();
                ctx.fill();
                ctx.strokeStyle = '#325d37';
                ctx.stroke();

                // Draw features
                if (mapGrid[r][c].object) {
                    ctx.fillStyle = mapGrid[r][c].object.type === 'flower' ? '#ff83df' : '#ff4242';
                    ctx.fillRect(screenX - 4, screenY + 6, 8, 8);
                }
            }
        }
        
        ctx.fillStyle = '#8fff9f';
        ctx.font = '12px Quicksand';
        ctx.fillText("💠 Isometric Sandbox Grid Mapping", 20, 30);
    }

    function renderFirstPerson() {
        ctx.fillStyle = '#000000';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        // Render Horizon sky
        let skyGrad = ctx.createLinearGradient(0, 0, 0, 200);
        skyGrad.addColorStop(0, '#09150a');
        skyGrad.addColorStop(1, '#2c4f30');
        ctx.fillStyle = skyGrad;
        ctx.fillRect(0, 0, canvas.width, 200);

        // Render Forest Pathway Floor
        ctx.fillStyle = '#1c361e';
        ctx.fillRect(0, 200, canvas.width, 220);

        // Perspective Trail lines vanishing in center horizon
        ctx.strokeStyle = '#4e7e53';
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(canvas.width / 2, 200);
        ctx.lineTo(100, canvas.height);
        ctx.moveTo(canvas.width / 2, 200);
        ctx.lineTo(700, canvas.height);
        ctx.stroke();

        // Render HUD console gauges
        ctx.fillStyle = 'rgba(21, 37, 23, 0.9)';
        ctx.fillRect(50, 290, 280, 100);
        ctx.strokeStyle = '#3c7d45';
        ctx.strokeRect(50, 290, 280, 100);

        ctx.fillStyle = '#fff';
        ctx.font = '11px Arial';
        ctx.fillText(`HERO PROFILE: ${document.getElementById('hero-name').value}`, 65, 315);
        ctx.fillText(`CURRENT STATE: Exploring Route 9`, 65, 335);
        ctx.fillText(`SPEED FACTOR: ${moveSpeed * activeWarpMultiplier} m/s`, 65, 355);

        // Render companion representations on borders
        ctx.fillStyle = '#ff4444';
        ctx.fillRect(60, 240, 15, 25);
        ctx.fillStyle = '#36d399';
        ctx.fillRect(720, 240, 15, 25);
        
        ctx.fillStyle = '#fff';
        ctx.fillText("Companion Red", 40, 280);
        ctx.fillText("Companion Mint", 695, 280);
    }

    function renderMeadowView() {
        // Parallax celestial elements background
        ctx.fillStyle = '#0a140b';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        // Distant stars / wisps
        ctx.fillStyle = 'rgba(143, 255, 159, 0.4)';
        for (let i = 0; i < 15; i++) {
            let x = (Math.sin(i * 12) + 1) * 400;
            let y = (Math.cos(i * 24) + 1) * 80;
            ctx.fillRect(x, y, 2, 2);
        }

        // Parallax background hills
        ctx.fillStyle = '#0f2411';
        ctx.beginPath();
        ctx.arc(200, 320, 250, 0, Math.PI * 2);
        ctx.fill();

        ctx.fillStyle = '#163119';
        ctx.beginPath();
        ctx.arc(650, 360, 300, 0, Math.PI * 2);
        ctx.fill();

        // Meadows Ground
        ctx.fillStyle = '#224025';
        ctx.fillRect(0, 280, canvas.width, 140);

        // Loop grid maps horizontally
        let currentDrawOffset = -(bgOffset % CELL_SIZE);
        let startColIndex = Math.floor(bgOffset / CELL_SIZE);

        for (let i = 0; i < (GRID_COLS + 2); i++) {
            let colIdx = (startColIndex + i) % GRID_COLS;
            let cellX = currentDrawOffset + (i * CELL_SIZE);

            // Draw pathway lines if path
            if (mapGrid[5][colIdx].type === 'path') {
                ctx.fillStyle = '#5c4128';
                ctx.fillRect(cellX, 290, CELL_SIZE, 30);
            }

            // Draw objects in main row
            let obj = mapGrid[5][colIdx].object;
            if (obj) {
                if (obj.type === 'flower') {
                    // Petal stem
                    ctx.strokeStyle = '#2ecc71';
                    ctx.lineWidth = 3;
                    ctx.beginPath();
                    ctx.moveTo(cellX + 25, 290);
                    ctx.lineTo(cellX + 25, 255);
                    ctx.stroke();

                    // Blossom bud
                    ctx.fillStyle = obj.color || '#ff83df';
                    ctx.beginPath();
                    ctx.arc(cellX + 25, 255, 12, 0, Math.PI * 2);
                    ctx.fill();
                    
                    ctx.fillStyle = '#ffff66';
                    ctx.beginPath();
                    ctx.arc(cellX + 25, 255, 4, 0, Math.PI * 2);
                    ctx.fill();
                } else if (obj.type === 'critter') {
                    // Forest Beast Representation
                    ctx.fillStyle = '#b33939';
                    ctx.fillRect(cellX + 10, 250, 30, 40);
                    // Glowing yellow monster eyes
                    ctx.fillStyle = '#f1c40f';
                    ctx.fillRect(cellX + 15, 260, 4, 4);
                    ctx.fillRect(cellX + 28, 260, 4, 4);
                } else if (obj.type === 'gift') {
                    // Draw Chest box
                    ctx.fillStyle = '#f39c12';
                    ctx.fillRect(cellX + 15, 265, 20, 25);
                    ctx.fillStyle = '#ffff33';
                    ctx.fillRect(cellX + 15, 273, 20, 4);
                }
            }
        }

        // Draw Player Hero
        ctx.fillStyle = '#8fff9f';
        ctx.fillRect(heroX, heroY, 20, 32); // Main Body
        ctx.fillStyle = '#fff';
        ctx.fillRect(heroX + 12, heroY + 6, 6, 6); // Eyes looking right
        ctx.fillStyle = '#000';
        ctx.fillRect(heroX + 14, heroY + 8, 3, 3);

        // Draw Pikmin Squad Following Hero
        pikminSquad.forEach(p => {
            p.jumpOffset += p.jumpSpeed;
            let hopY = Math.abs(Math.sin(p.jumpOffset)) * 14;

            ctx.fillStyle = p.color;
            ctx.fillRect(p.x, p.y - hopY, 14, 20); // Body

            // Stalk leaf
            ctx.strokeStyle = '#2d5a27';
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.moveTo(p.x + 7, p.y - hopY);
            ctx.lineTo(p.x + 7, p.y - 12 - hopY);
            ctx.stroke();

            ctx.fillStyle = '#2ecc71';
            ctx.fillRect(p.x + 4, p.y - 16 - hopY, 6, 4);
        });

        // Background moving loop
        bgOffset += moveSpeed * activeWarpMultiplier;
    }

    function renderTacticalView() {
        ctx.fillStyle = '#102213';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        // Draw Grid system
        for (let r = 0; r < GRID_ROWS; r++) {
            for (let c = 0; c < GRID_COLS; c++) {
                let cell = mapGrid[r][c];
                ctx.strokeStyle = '#27472b';
                ctx.strokeRect(c * CELL_SIZE, r * CELL_SIZE, CELL_SIZE, CELL_SIZE);

                if (cell.type === 'path') {
                    ctx.fillStyle = '#513d29';
                    ctx.fillRect(c * CELL_SIZE, r * CELL_SIZE, CELL_SIZE, CELL_SIZE);
                }

                // Render objects inside tactical layout
                if (cell.object) {
                    if (cell.object.type === 'flower') {
                        ctx.fillStyle = cell.object.color;
                        ctx.beginPath();
                        ctx.arc(c * CELL_SIZE + 25, r * CELL_SIZE + 25, 10, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (cell.object.type === 'critter') {
                        ctx.fillStyle = '#e74c3c';
                        ctx.fillRect(c * CELL_SIZE + 15, r * CELL_SIZE + 15, 20, 20);
                    } else if (cell.object.type === 'gift') {
                        ctx.fillStyle = '#f1c40f';
                        ctx.fillRect(c * CELL_SIZE + 15, r * CELL_SIZE + 15, 20, 20);
                    }
                }
            }
        }

        // Draw player tactical dot blip
        ctx.fillStyle = '#2ecc71';
        ctx.beginPath();
        ctx.arc(heroX + 150, 5 * CELL_SIZE + 25, 12, 0, Math.PI * 2);
        ctx.fill();
        ctx.strokeStyle = '#ffffff';
        ctx.lineWidth = 2;
        ctx.stroke();
    }

    function renderBattleLog() {
        ctx.fillStyle = '#0c160e';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        ctx.fillStyle = '#8fff9f';
        ctx.font = 'bold 15px monospace';
        ctx.fillText("📜 REALTIME BATTLE EXPEDITION TELEMETRY", 40, 45);

        ctx.strokeStyle = '#274b2c';
        ctx.lineWidth = 2;
        ctx.strokeRect(30, 60, canvas.width - 60, canvas.height - 90);

        ctx.font = '12px monospace';
        for (let i = 0; i < 12; i++) {
            if (notificationsLog[i]) {
                ctx.fillStyle = i === 0 ? '#ffffff' : '#7ba980';
                ctx.fillText(`[Expedition Log] > ${notificationsLog[i]}`, 50, 95 + (i * 24));
            }
        }
    }

    // Main Execution Pipeline GameLoop
    function processAndRender() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // Animate particles systems
        particleSystems.forEach((p, idx) => {
            p.x += p.vx;
            p.y += p.vy;
            p.life -= p.decay;
            ctx.fillStyle = p.color;
            ctx.globalAlpha = p.life;
            ctx.fillRect(p.x, p.y, 4, 4);
            ctx.globalAlpha = 1.0;
            if (p.life <= 0) particleSystems.splice(idx, 1);
        });

        if (currentView === 'meadow') {
            renderMeadowView();
        } else if (currentView === 'tactical') {
            renderTacticalView();
        } else if (currentView === 'isometric') {
            renderIsometric();
        } else if (currentView === 'firstperson') {
            renderFirstPerson();
        } else if (currentView === 'log') {
            renderBattleLog();
        }

        // Rare automated events processing (random monster spawn / healing items discovery)
        animationFrame++;
        if (animationFrame % 240 === 0) {
            // Find empty cells to dynamically spawn wild items
            let randomCol = Math.floor(Math.random() * GRID_COLS);
            if (!mapGrid[5][randomCol].object) {
                mapGrid[5][randomCol].object = { name: 'Wild Blossom', type: 'flower', color: '#3498db' };
                addLog("Dynamic weather event caused a magical Sylvan flower to bloom along the route.");
            }
        }

        requestAnimationFrame(processAndRender);
    }

    async function fetchGeminiAI(userQuery, systemPrompt) {
        const selectedModel = document.getElementById('ai-model').value;
        const customKey = document.getElementById('ai-key').value;
        
        // Canvas automatic endpoint fallback with fallback keys
        const apiKey = customKey || ""; 
        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${selectedModel}:generateContent?key=${apiKey}`;

        const payload = {
            contents: [{ parts: [{ text: userQuery }] }],
            systemInstruction: {
                parts: [{ text: systemPrompt }]
            },
        };

        try {
            const response = await fetch(apiUrl, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(payload)
            });
            const result = await response.json();
            const textResult = result.candidates?.[0]?.content?.parts?.[0]?.text;
            if (textResult) {
                return textResult;
            }
            return "Unable to parse AI response. Ensure configuration settings are matching.";
        } catch (error) {
            console.error("Gemini API Error occurred: ", error);
            return `AI Connection Error. Check model selection or network connections. [Reason: ${error.message}]`;
        }
    }

    // AI Feature 1: Route Quest Narrator
    async function generateAIQuest() {
        const questBox = document.getElementById('quest-output');
        questBox.innerText = "🌀 Consulting Sylvan Chronicler via Gemini AI...";

        // Extract current map configuration stats to provide rich grounding
        let pathBlocks = 0;
        let flowerCount = 0;
        let monsterCount = 0;
        for (let r = 0; r < GRID_ROWS; r++) {
            for (let c = 0; c < GRID_COLS; c++) {
                if(mapGrid[r][c].type === 'path') pathBlocks++;
                if(mapGrid[r][c].object?.type === 'flower') flowerCount++;
                if(mapGrid[r][c].object?.type === 'critter') monsterCount++;
            }
        }

        const prompt = `Develop a brief fantasy side quest background lore and a quest objective matching the player's custom path. Current stats: Path blocks=${pathBlocks}, magic flowers planted=${flowerCount}, forest beasts spawned=${monsterCount}.`;
        const sys = "Act as the Master RPG Narrator for a whimsical forest. Provide one cohesive, engaging narrative paragraph with a concrete objective.";

        const response = await fetchGeminiAI(prompt, sys);
        questBox.innerText = response;
    }

    // AI Feature 2: Dream-Flower Sculptor
    async function generateAIDreamFlower() {
        const flowerBox = document.getElementById('flower-output');
        const inputDesc = document.getElementById('flower-desc').value || "Glowing Midnight Poppy";
        flowerBox.innerText = "🔮 Sprouting flower essence with Gemini AI...";

        const prompt = `Design custom combat and healing stats for a flower named: "${inputDesc}". Format output with: Flower Name, Magic Petals yield, health recovery, and active combat aura description.`;
        const sys = "You are a mystical herbalist. Invent creative stats and fantasy aura descriptions for mystical plants.";

        const response = await fetchGeminiAI(prompt, sys);
        flowerBox.innerText = response;

        // Apply visual custom flower into player's main expedition row
        customDreamFlowerStats = { color: '#bf55ec', name: inputDesc };
        mapGrid[5][6].object = { name: inputDesc, type: 'flower', color: '#bf55ec' };
        showToast(`AI flower "${inputDesc}" sprouted inside the central meadow pathway!`);
    }

    // AI Feature 3: Monster Companion Whisperer
    async function whisperToMonster() {
        const whisperBox = document.getElementById('whisper-output');
        whisperBox.innerText = "🦊 Tuning frequency to beast brain waves...";

        const prompt = "A player has encountered a 'Mossy Bulborb' forest monster sleeping on their route grid. What is the monster feeling and thinking? Provide a short, funny translation of its dreams and suggest one hilarious dialogue choice the player can use to negotiate safe passage.";
        const sys = "You translate forest monsters' inner monologues. Be humorous and eccentric.";

        const response = await fetchGeminiAI(prompt, sys);
        whisperBox.innerText = response;
    }

    // Initialize systems on window load
    window.onload = function() {
        initSandboxGrid();
        setupSquad();
        processAndRender();
        updateHeroStats();
    };

</script>

</body>
</html>
