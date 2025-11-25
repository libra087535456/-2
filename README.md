# 
乘法遊戲2
[遊戲9.html](https://github.com/user-attachments/files/23736649/9.html)
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>數學精靈：最終戰鬥</title>
    <style>
        /* --- 核心 CSS 樣式 (確保畫面佈局和顏色正確) --- */
        body {
            font-family: '微軟正黑體', Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            color: #eee; 
            padding: 20px;
            
            /* 森林背景圖片與動畫 */
            background-image: url('./forest_bg.png'); 
            background-size: cover; 
            background-position: center center; 
            background-repeat: no-repeat;
            animation: forestPan 60s linear infinite alternate; 
        }

        @keyframes forestPan {
            0% { background-position: 0% 0%; }    
            100% { background-position: 100% 100%; } 
        }

        .container {
            background: rgba(0, 0, 0, 0.5); 
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 40px rgba(0, 0, 0, 0.6); 
            text-align: center;
            width: 90%;
            max-width: 900px;
            margin-bottom: 20px;
            backdrop-filter: blur(8px); 
            -webkit-backdrop-filter: blur(8px); 
        }
        h1 {
            color: #e0b0ff; 
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.8); 
            margin-bottom: 20px;
        }
        
        /* --- 角色與怪物圖片區塊 --- */
        .character-display {
            display: flex;
            justify-content: space-around;
            align-items: flex-end;
            margin: 20px 0;
            min-height: 200px;
        }
        .player-sprite-container {
            width: 150px; 
            height: 180px; 
            background-size: cover;
            transform: scaleX(-1); 
            position: relative;
            overflow: hidden;
            transition: filter 0.2s;
        }
        .monster-image {
            max-width: 250px; 
            object-fit: contain;
            transition: transform 0.2s ease-in-out, filter 0.2s;
        }

        /* --- CSS 動態尺寸定義 (怪物大小) --- */
        .m-tiny { height: 120px; width: 100px; } 
        .m-small { height: 140px; width: 120px; }
        .m-medium { height: 160px; width: 140px; }
        .m-large { height: 190px; width: 170px; }
        .m-giant { height: 220px; width: 200px; } 


        /* --- CSS 動畫定義 --- */
        @keyframes attackAnimation {
            0%   { background-image: var(--attack-sprite-url); background-position: 0 0; } 
            20%  { background-position: -150px 0; }
            40%  { background-position: -300px 0; } 
            60%  { background-position: -450px 0; } 
            80%  { background-position: -600px 0; } 
            100% { background-position: -750px 0; } 
        }
        .player-sprite-container.attacking {
            animation: attackAnimation 0.8s steps(6) 1; 
        }

        .player-sprite-container.hit {
            filter: brightness(1.5) saturate(200%); 
        }
        .monster-image.hit {
            filter: brightness(1.5) saturate(200%); 
            transform: scale(0.90); 
            transition: transform 0.2s ease-out, filter 0.2s ease-out;
        }
        
        /* --- 狀態欄和介面樣式 --- */
        .status-header { 
            display: flex; 
            justify-content: space-around; 
            align-items: center; 
            margin-bottom: 10px; 
            padding: 10px; 
            background-color: rgba(255, 255, 255, 0.15); 
            color: #fff;
            border-radius: 8px; 
            flex-wrap: wrap; 
        }
        .status-item { margin: 5px 15px; text-align: left; }
        .status-item strong { color: #f0e68c; } 
        #playerCoinsDisplay { color: #ffd700; text-shadow: 1px 1px 1px #000;}
        #comboDisplay, #defendStatus { font-weight: bold; margin-left: 10px; } 

        .main-battle-area > div:first-of-type, .main-battle-area > div:nth-of-type(3) { 
            background-color: rgba(255, 255, 255, 0.15); 
            color: #fff;
            border: 1px solid rgba(255, 255, 255, 0.3);
            border-radius: 8px;
            padding: 10px;
            margin-bottom: 20px;
        }

        #battle-log, #question { 
            font-size: 1.5em; 
            margin: 20px 0; 
            font-weight: bold; 
            color: #fff; 
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.9); 
        }
        
        /* --- 輸入框和按鈕樣式 --- */
        #answerInput {
            background-color: rgba(0, 0, 0, 0.3); 
            border: 1px solid rgba(255, 255, 255, 0.4);
            color: #fff; 
            padding: 8px;
            border-radius: 5px;
            margin-right: 10px;
            font-size: 1em;
        }
        #answerInput::placeholder {
            color: rgba(255, 255, 255, 0.7); 
        }
        #submitBtn, .shop-item button, button[onclick="clearSave()"] {
            background-color: #5d3592; 
            color: #fff;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.1s ease;
        }
        #submitBtn:hover, .shop-item button:hover, button[onclick="clearSave()"]:hover {
            background-color: #7a46b5; 
            transform: translateY(-2px);
        }

        /* --- 角色選擇畫面樣式 --- */
        #startScreen { display: none; padding: 50px 20px; text-align: center; }
        .character-selection-grid { display: flex; justify-content: center; gap: 30px; margin-top: 30px; flex-wrap: wrap; }
        .char-card { background-color: rgba(255, 255, 255, 0.1); padding: 15px; border-radius: 10px; width: 220px; border: 3px solid transparent; cursor: pointer; transition: all 0.2s ease; }
        .char-card:hover { background-color: rgba(255, 255, 255, 0.2); transform: translateY(-5px); }
        .char-card.selected { border-color: #ffd700; box-shadow: 0 0 15px #ffd700; }
        .char-card img { width: 100%; height: auto; max-height: 200px; object-fit: contain; margin-bottom: 10px; border-radius: 5px; }
        .char-card h3 { color: #e0b0ff; }
        .char-card p { font-size: 0.9em; color: #bbb; }
        .status-text { font-weight: bold; margin-top: 5px;}
        .game-layout { display: none; }
    </style>
</head>
<body>
    
    <audio id="bgmPlayer" loop preload="auto">
        <source src="./bgm.mp3" type="audio/mp3">
        您的瀏覽器不支援 audio 元素。
    </audio>

    <div class="container">
        <div id="startScreen">
            <h1>歡迎來到數字森林</h1>
            <p>請選擇您的數學精靈戰士：</p>

            <div class="character-selection-grid">
                
                <div class="char-card" id="card-KIKI" onclick="selectCard('KIKI')">
                    <img src="./player_kiki.png" alt="琪琪 (Kiki)">
                    <h3>琪琪 (Kiki)</h3>
                    <p style="color: #ffc107;" class="status-text">⚡ 計數閃電：連擊加速</p>
                    <p>連續答對造成 1.5 倍傷害！</p>
                </div>

                <div class="char-card" id="card-LEO" onclick="selectCard('LEO')">
                    <img src="./player_leo.png" alt="雷歐 (Leo)">
                    <h3>雷歐 (Leo)</h3>
                    <p style="color: #4dd0e1;" class="status-text">🛡️ 翻倍大師：機率防禦</p>
                    <p>翻倍題答對，50% 機率擋下怪物攻擊。</p>
                </div>

                <div class="char-card" id="card-MIA" onclick="selectCard('MIA')">
                    <img src="./player_mia.png" alt="米亞 (Mia)">
                    <h3>米亞 (Mia)</h3>
                    <p style="color: #ff99cc;" class="status-text">💖 圖像魔術師：視覺輔助</p>
                    <p>成功解題可獲得特殊資源補充體力。</p>
                </div>
            </div>

            <button id="startGameBtn" onclick="initGame()" disabled style="margin-top: 40px; padding: 15px 40px; font-size: 1.2em; background-color: #8a2be2;">
                開始冒險
            </button>
        </div>

        <div id="gameLayout" class="game-layout">
            <div class="status-header">
                <div class="status-item">
                    <strong>等級:</strong> <span id="playerLevelDisplay">1</span>
                </div>
                <div class="status-item">
                    <strong>我的 HP:</strong> <span id="playerHPDisplay">100</span> / <span id="playerMaxHPDisplay">100</span>
                    <div class="hp-bar" style="width: 150px;"><div id="playerHPBar" class="hp-fill" style="width: 100%;"></div></div>
                </div>
                <div class="status-item">
                    <strong>金幣:</strong> <span id="playerCoinsDisplay">0 💰</span>
                </div>
                <div class="status-item">
                    <strong>經驗值 (XP):</strong> <span id="playerXPDisplay">0 / 100</span>
                </div>
                <button onclick="clearSave()">清除存檔</button>
            </div>

            <div class="game-layout">
                <div class="main-battle-area">
                    <div style="margin-bottom: 20px; padding: 10px; border: 1px solid rgba(255,255,255,0.3); border-radius: 8px;">
                        <strong>當前攻擊力:</strong> <span id="currentAttackDisplay">20</span> 
                        <span id="comboDisplay"></span>
                        <span id="defendStatus"></span>
                    </div>

                    <div class="character-display">
                        <div id="playerCharacter" class="player-sprite-container"></div>
                        <img id="monsterCharacter" src="" class="monster-image">
                    </div>
                    
                    <div style="margin-bottom: 20px; padding: 10px; border: 1px solid rgba(255,255,255,0.3); border-radius: 8px;">
                        <strong>野生怪物 HP:</strong> <span id="monsterHPDisplay">?</span>
                        <div class="hp-bar"><div id="monsterHPBar" class="hp-fill" style="width: 100%;"></div></div>
                    </div>

                    <div id="battle-log">新的怪物出現了！準備戰鬥！</div>
                    <div id="question"></div>

                    <div id="input-controls">
                        <input type="number" id="answerInput" placeholder="輸入答案">
                        <button id="submitBtn" onclick="checkAnswer()">提交答案</button>
                    </div>

                    <div class="equipment-display">
                        <strong>裝備狀態:</strong>
                        <div class="equipment-slot">武器: <span id="weaponSlot">無</span></div>
                        <div class="equipment-slot">防具: <span id="armorSlot">無</span></span>
                        <div class="equipment-slot">特殊道具: <span id="mathSpiritCount">0</span> 
                            <button onclick="useMathSpirit()" style="padding: 5px 10px; font-size: 0.8em; margin-left: 10px; background-color: #ff99cc;">使用精華</button>
                        </div>
                    </div>
                </div>

                <div class="shop-area">
                    <h2>精靈商店</h2>
                    <h3>🛡️ 裝備 (永久加成)</h3>
                    <div class="shop-item" data-item-type="Equipment" data-item-name="精算長劍" data-item-price="50">
                        <strong>精算長劍</strong> (+5 基礎傷害)
                        <button onclick="buyItem('精算長劍', 50, 'Weapon')">購買 50 💰</button>
                    </div>
                    <div class="shop-item" data-item-type="Equipment" data-item-name="心算盾牌" data-item-price="60">
                        <strong>心算盾牌</strong> (+20 Max HP)
                        <button onclick="buyItem('心算盾牌', 60, 'Armor')">購買 60 💰</button>
                    </div>

                    <h3>💊 道具 (單次使用)</h3>
                    <div class="shop-item" data-item-type="Consumable" data-item-name="超級藥水" data-item-price="40">
                        <strong>超級藥水</strong> (補 60 HP)
                        <br>價格: 40 💰 (庫存: <span id="superPotionCount">0</span>)
                    </div>
                    <div class="shop-item" data-item-type="Consumable" data-item-name="攻擊強化" data-item-price="25">
                        <strong>攻擊強化</strong> (下次傷害 +20)
                        <br>價格: 25 💰 (庫存: <span id="attackBoostCount">0</span>)
                    </div>
                    <div class="shop-item" data-item-type="Consumable" data-item-name="普通藥水" data-item-price="20">
                        <strong>普通藥水</strong> (補 30 HP)
                        <br>價格: 20 💰 (庫存: <span id="normalPotionShopCount">3</span>)
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // --- 圖片路徑定義 ---
        const PLAYER_CLASS_IMAGE = {
            'KIKI': './player_kiki.png',
            'LEO': './player_leo.png',
            'MIA': './player_mia.png'
        };
        const BASE_PLAYER_IMAGE = './player_base.png';             
        const EQUIPPED_PLAYER_IMAGE = './player_sword.png';         
        const EQUIPPED_SHIELD_IMAGE = './player_shield.png';        
        const EQUIPPED_BOTH_IMAGE = './player_sword_shield.png';    
        const EQUIPPED_ATTACK_SPRITE = './player_sword_attack.png'; 

        // --- 角色職業數據 ---
        const CHARACTER_CLASSES = {
            'KIKI': { role: 'KIKI', image: PLAYER_CLASS_IMAGE.KIKI, atkBonus: 5, hpBonus: 0 },
            'LEO': { role: 'LEO', image: PLAYER_CLASS_IMAGE.LEO, atkBonus: 0, hpBonus: 20 },
            'MIA': { role: 'MIA', image: PLAYER_CLASS_IMAGE.MIA, atkBonus: 2, hpBonus: 10 }
        };

        // --- 音效路徑定義 ---
        const SFX_ATTACK = './attack.mp3';
        const SFX_HIT = './hit.mp3';
        const SFX_MISS = './miss.mp3';
        const SFX_LEVELUP = './levelup.mp3';
        
        // --- 存檔金鑰 (略) ---
        const SAVE_KEY = 'mathBattleSave';

        // --- 遊戲狀態變數 ---
        let selectedClass = null; 
        let playerMaxHP = 100;
        let playerCurrentHP = 100;
        let playerXP = 0;
        let playerLevel = 1;      
        let xpToNextLevel = 100;  
        let playerCoins = 0; 
        let baseAttackDamage = 20; 

        // 裝備狀態
        let currentWeapon = '無'; 
        let currentArmor = '無'; 

        // 道具庫存
        let normalPotionCount = 3;
        let superPotionCount = 0;
        let attackBoostCount = 0;
        let nextAttackBoost = 0; 

        // 🚀 新增戰鬥專屬變數
        let comboCount = 0; 
        let lastAnswerTime = 0; 
        const KIKI_TIME_LIMIT = 3000; // Kiki 答題時限 3 秒
        let monsterAttackBlocked = false; // Leo 防禦旗標
        let mathSpiritCount = 0; // Mia 的數學精華
        
        let monsterMaxHP;
        let monsterCurrentHP;
        let monsterAttackDamage; 
        let correctAnswer; 
        
        const MONSTER_DATA = [
            { name: "算術圓球", file: "m1.png", hpMin: 50, hpMax: 80, sizeClass: "m-tiny" },
            { name: "測量精靈", file: "m2.png", hpMin: 90, hpMax: 130, sizeClass: "m-small" },
            { name: "計算皇冠", file: "m3.png", hpMin: 140, hpMax: 180, sizeClass: "m-medium" },
            { name: "幾何老虎", file: "m4.png", hpMin: 190, hpMax: 250, sizeClass: "m-large" },
            { name: "終極獅王", file: "m5.png", hpMin: 260, hpMax: 350, sizeClass: "m-giant" }
        ];

        let isBGMPlaying = false; 
        
        // --- 輔助函數 ---
        function getRandomNumber(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function playSound(file) {
            try {
                const audio = new Audio(file);
                audio.volume = 0.5; 
                audio.play();
            } catch (e) {
                console.error("無法播放音效:", e);
            }
        }
        
        function startBGM() {
            if (!isBGMPlaying) {
                const bgmPlayer = document.getElementById('bgmPlayer');
                bgmPlayer.volume = 0.2; 
                bgmPlayer.play().then(() => {
                    isBGMPlaying = true;
                }).catch(error => {
                    console.warn("BGM 自動播放被限制，需要用戶互動:", error);
                });
            }
        }

        // --- 核心：等級提升機制 ---
        function checkLevelUp() {
            let leveledUp = false;
            while (playerXP >= xpToNextLevel) {
                playerLevel++;
                playerXP -= xpToNextLevel; 
                xpToNextLevel = Math.floor(xpToNextLevel * 1.5); 

                baseAttackDamage += 3; 
                playerMaxHP += 10;     
                playerCurrentHP = playerMaxHP; 

                document.getElementById('battle-log').textContent += ` 🎊 升級了！達到 Level ${playerLevel}！攻擊力 +3, 最大HP +10！`;
                playSound(SFX_LEVELUP); 
                leveledUp = true;
            }
            return leveledUp;
        }
        
        // --- 🚀 角色選擇卡片邏輯 ---
        function selectCard(classId) {
            selectedClass = classId;
            document.querySelectorAll('.char-card').forEach(card => {
                card.classList.remove('selected');
            });
            document.getElementById(`card-${classId}`).classList.add('selected');
            document.getElementById('startGameBtn').disabled = false;
        }
        
        // --- 🚀 遊戲初始化 (修復載入 Bug) ---
        function initGame(loadedClass = null) {
            if (!loadedClass && !selectedClass) return;
            
            const classKey = loadedClass || selectedClass;
            const classData = CHARACTER_CLASSES[classKey];

            // 設置初始獎勵 (修復空白畫面的關鍵: 確保數值在顯示前被設定)
            playerMaxHP = 100 + classData.hpBonus;
            playerCurrentHP = playerMaxHP;
            baseAttackDamage = 20 + classData.atkBonus;
            
            // 隱藏開始畫面，顯示遊戲介面
            document.getElementById('startScreen').style.display = 'none';
            document.getElementById('gameLayout').style.display = 'block';

            // 更新玩家基礎圖片
            const playerImage = CHARACTER_CLASSES[classKey].image;
            document.getElementById('playerCharacter').style.backgroundImage = `url('${playerImage}')`;
            document.getElementById('playerCharacter').style.setProperty('--attack-sprite-url', `url('${EQUIPPED_ATTACK_SPRITE}')`);

            generateNewMonster();
        }

        // --- 角色圖片切換邏輯 (略) ---
        function updatePlayerAppearance() {
            const playerChar = document.getElementById('playerCharacter');
            const isSwordEquipped = (currentWeapon === '精算長劍');
            const isShieldEquipped = (currentArmor === '心算盾牌');

            let staticImagePath;
            let attackSpritePath = EQUIPPED_ATTACK_SPRITE; 

            if (isSwordEquipped && isShieldEquipped) {
                staticImagePath = EQUIPPED_BOTH_IMAGE;
            } else if (isSwordEquipped) {
                staticImagePath = EQUIPPED_PLAYER_IMAGE;
            } else if (isShieldEquipped) {
                staticImagePath = EQUIPPED_SHIELD_IMAGE; 
                attackSpritePath = BASE_PLAYER_IMAGE; 
            } else {
                staticImagePath = CHARACTER_CLASSES[selectedClass].image;
                attackSpritePath = BASE_PLAYER_IMAGE; 
            }
            
            playerChar.style.backgroundImage = `url('${staticImagePath}')`;
            playerChar.style.setProperty('--attack-sprite-url', `url('${attackSpritePath}')`);
        }


        // --- 存檔與讀檔功能 (略) ---
        function saveGame() {
            const gameState = {
                playerMaxHP, playerCurrentHP, playerXP, playerCoins, baseAttackDamage,
                currentWeapon, currentArmor, normalPotionCount, superPotionCount, attackBoostCount,
                playerLevel, xpToNextLevel, selectedClass,
                mathSpiritCount, monsterAttackBlocked, comboCount, lastAnswerTime
            };
            localStorage.setItem(SAVE_KEY, JSON.stringify(gameState));
            updatePlayerAppearance(); 
        }

        function loadGame() {
            const savedData = localStorage.getItem(SAVE_KEY);
            if (savedData) {
                const gameState = JSON.parse(savedData);
                
                selectedClass = gameState.selectedClass; 
                if (!selectedClass) return false; 
                
                playerMaxHP = gameState.playerMaxHP || 100;
                playerCurrentHP = gameState.playerCurrentHP || 100;
                playerXP = gameState.playerXP || 0;
                playerLevel = gameState.playerLevel || 1; 
                xpToNextLevel = gameState.xpToNextLevel || 100; 
                playerCoins = gameState.playerCoins || 0;
                baseAttackDamage = gameState.baseAttackDamage || 20;
                currentWeapon = gameState.currentWeapon || '無';
                currentArmor = gameState.currentArmor || '無';
                normalPotionCount = gameState.normalPotionCount || 3;
                superPotionCount = gameState.superPotionCount || 0;
                attackBoostCount = gameState.attackBoostCount || 0;

                mathSpiritCount = gameState.mathSpiritCount || 0;
                monsterAttackBlocked = gameState.monsterAttackBlocked || false;
                comboCount = gameState.comboCount || 0;
                lastAnswerTime = gameState.lastAnswerTime || 0;
                
                playerCurrentHP = Math.min(playerCurrentHP, playerMaxHP); 
                
                document.getElementById('battle-log').textContent = '💾 讀取進度成功！繼續戰鬥！';
                document.getElementById('startScreen').style.display = 'none';
                document.getElementById('gameLayout').style.display = 'block';

                updatePlayerAppearance(); 
                return true;
            }
            return false;
        }

        function clearSave() {
            if (confirm('確定要清除所有存檔並重新開始遊戲嗎？')) {
                localStorage.removeItem(SAVE_KEY);
                window.location.reload(); 
            }
        }

        // --- 狀態顯示更新 (略) ---
        function updateDisplay() {
            const currentTotalAttack = baseAttackDamage + nextAttackBoost;

            document.getElementById('playerMaxHPDisplay').textContent = playerMaxHP;
            document.getElementById('playerHPDisplay').textContent = playerCurrentHP;
            
            document.getElementById('playerLevelDisplay').textContent = playerLevel;
            document.getElementById('playerXPDisplay').textContent = `${playerXP} / ${xpToNextLevel}`;

            document.getElementById('playerCoinsDisplay').textContent = `${playerCoins} 💰`;
            document.getElementById('baseDamageDisplay').textContent = baseAttackDamage;
            
            document.getElementById('comboDisplay').textContent = (selectedClass === 'KIKI' && comboCount >= 3) ? ` (連擊 x1.5)` : '';
            document.getElementById('defendStatus').textContent = (selectedClass === 'LEO' && monsterAttackBlocked) ? ' [防禦中]' : '';
            
            document.getElementById('currentAttackDisplay').textContent = currentTotalAttack;
            
            document.getElementById('normalPotionShopCount').textContent = normalPotionCount;
            document.getElementById('superPotionCount').textContent = superPotionCount;
            document.getElementById('attackBoostCount').textContent = attackBoostCount;
            document.getElementById('mathSpiritCount').textContent = mathSpiritCount; 

            document.getElementById('weaponSlot').textContent = currentWeapon;
            document.getElementById('armorSlot').textContent = currentArmor;
            
            const playerBar = document.getElementById('playerHPBar');
            const monsterBar = document.getElementById('monsterHPBar');
            
            playerBar.style.width = `${(playerCurrentHP / playerMaxHP) * 100}%`;
            document.getElementById('monsterHPDisplay').textContent = monsterCurrentHP > 0 ? monsterCurrentHP : 0;
            monsterBar.style.width = `${(monsterCurrentHP / monsterMaxHP) * 100}%`;
            
            playerBar.style.backgroundColor = playerCurrentHP / playerMaxHP < 0.3 ? '#ff0000' : '#00b050';
            monsterBar.style.backgroundColor = monsterCurrentHP / monsterMaxHP < 0.3 ? '#ff0000' : '#00b050';

            if (document.getElementById('normalPotionBtn')) {
                document.getElementById('normalPotionBtn').textContent = `使用普通藥水 (${normalPotionCount})`;
                document.getElementById('normalPotionBtn').disabled = (normalPotionCount <= 0);
            }
            if (document.getElementById('superPotionBtn')) {
                document.getElementById('superPotionBtn').textContent = `使用超級藥水 (${superPotionCount})`;
                document.getElementById('superPotionBtn').disabled = (superPotionCount <= 0);
            }
            if (document.getElementById('attackBoostBtn')) {
                document.getElementById('attackBoostBtn').textContent = `使用攻擊強化 (${attackBoostCount})`;
                document.getElementById('attackBoostBtn').disabled = (attackBoostCount <= 0 || nextAttackBoost > 0); 
            }
        }

        // --- 遊戲核心邏輯：生成新怪物 (略) ---
        function generateNewMonster() {
            const difficultyLevel = Math.floor(playerXP / 500);
            
            const monsterIndex = getRandomNumber(0, MONSTER_DATA.length - 1);
            const selectedMonster = MONSTER_DATA[monsterIndex];

            const baseMinHP = selectedMonster.hpMin;
            const baseMaxHP = selectedMonster.hpMax;
            
            const scalingFactor = 1 + (difficultyLevel * 0.25); 

            const scaledMinHP = Math.floor(baseMinHP * scalingFactor);
            const scaledMaxHP = Math.floor(baseMaxHP * scalingFactor);
            
            monsterMaxHP = getRandomNumber(scaledMinHP, scaledMaxHP);
            monsterCurrentHP = monsterMaxHP;
            
            const monsterChar = document.getElementById('monsterCharacter');
            monsterChar.src = `./${selectedMonster.file}`;
            
            monsterChar.className = 'monster-image';
            monsterChar.classList.add(selectedMonster.sizeClass);

            document.getElementById('battle-log').textContent = `新的怪物 '${selectedMonster.name}' 出現了！HP: ${monsterMaxHP} (難度級別: ${difficultyLevel})`;
            
            generateNewQuestion();
            updateDisplay();
        }

        function generateNewQuestion() {
            const num1 = getRandomNumber(1, 9); 
            const num2 = getRandomNumber(1, 9);
            correctAnswer = num1 * num2;
            monsterAttackDamage = correctAnswer; 
            
            if (selectedClass === 'KIKI') {
                lastAnswerTime = Date.now();
            }

            document.getElementById('question').textContent = `請計算：${num1} × ${num2} = ?`;
            document.getElementById('answerInput').value = '';
            document.getElementById('answerInput').focus();
            document.getElementById('submitBtn').disabled = false;

            document.getElementById('playerCharacter').classList.remove('attacking');
            document.getElementById('monsterCharacter').classList.remove('hit');
            document.getElementById('playerCharacter').classList.remove('hit');
        }

        // --- 戰鬥函數：檢查答案與進行回合 (核心邏輯) ---
        function checkAnswer() {
            startBGM(); 
            
            const userAnswer = parseInt(document.getElementById('answerInput').value);
            const battleLog = document.getElementById('battle-log');
            const playerChar = document.getElementById('playerCharacter');
            const monsterChar = document.getElementById('monsterCharacter');
            
            const currentTotalAttack = baseAttackDamage + nextAttackBoost; 

            document.getElementById('submitBtn').disabled = true;

            if (isNaN(userAnswer)) {
                battleLog.textContent = '❌ 請輸入一個數字！';
                document.getElementById('submitBtn').disabled = false;
                return;
            }
[attack.mp3](https://github.com/user-attachments/files/23736655/attack.mp3)

            // 1. 判斷是否答對
            if (userAnswer === correctAnswer) {
                
                let finalDamageMultiplier = 1.0;
                let specialMessage = "";

                // --- KIKI: 連擊速度 (Speed Combo) ---
                if (selectedClass === 'KIKI') {
                    const timeTaken = Date.now() - lastAnswerTime;
                    if (comboCount > 0 && timeTaken > KIKI_TIME_LIMIT) {
                        comboCount = 0;
                        specialMessage += " (連擊中斷)";
                    } else {
                        comboCount++;
                        if (comboCount >= 3) {
                            finalDamageMultiplier = 1.5;
                            specialMessage += " (連擊 x1.5 傷害!)";
                        }
                    }
                    lastAnswerTime = Date.now();
                } else {
                    comboCount = 0; 
                }

                // --- LEO: 翻倍防禦 (Doubling Master) ---
                let isDoublingRelated = (correctAnswer % 2 === 0);
                if (selectedClass === 'LEO' && isDoublingRelated) {
                    if (Math.random() < 0.50) { 
                        monsterAttackBlocked = true;
                        specialMessage += " (防禦強化已準備就緒!)";
                    }
                }

                // --- MIA: 視覺連結 (Visual Link) ---
                if (selectedClass === 'MIA') {
                    if (Math.random() < 0.30) { 
                        mathSpiritCount++;
                        specialMessage += " (獲得數學精華!)";
                    }
                }

                // 2. 造成傷害
                const actualDamage = Math.floor(currentTotalAttack * finalDamageMultiplier);
                monsterCurrentHP -= actualDamage;
                battleLog.textContent = `✅ 答對了！你的攻擊造成 ${actualDamage} 傷害！` + specialMessage;
                
                // 動畫[levelup.mp3](https://github.com/user-attachments/files/23736654/levelup.mp3)
[hit.mp3](https://github.com/user-attachments/files/23736653/hit.mp3)
<img width="1632" height="640" alt="forest_bg" src="https://github.com/user-attachments/assets/6f9b63ac-1785-4262-abcc-a374b4239e0e" />
[bgm.mp3](https://github.com/user-attachments/files/23736651/bgm.mp3)

                playSound(SFX_ATTACK); 
                playerChar.classList.add('attacking');
                monsterChar.classList.add('hit'); 
                setTimeout(() => {
                    playerChar.classList.remove('attacking');
                    monsterChar.classList.remove('hit');
                    playSound(SFX_HIT); 
                }, 800); 

                // 3. 檢查怪物死亡
                if (monsterCurrentHP <= 0) {
                    const xpGained = 50; 
                    const coinsEarned = getRandomNumber(10, 20); 
                    playerXP += xpGained; 
                    playerCoins += coinsEarned;
                    
                    let lootMessage = "";
                    const lootChance = Math.random(); 

                    if (lootChance < 0.02) { 
                        if (currentArmor === '心算盾牌') { normalPotionCount++; lootMessage = '🛡️ 已擁有心算盾牌，額外獲得普通藥水 x1！'; } 
                        else { currentArmor = '心算盾牌'; playerMaxHP += 20; playerCurrentHP = playerMaxHP; lootMessage = '🌟 恭喜！掉落並裝備了心算盾牌 (+20 HP)！'; }
                    } else if (lootChance < 0.07) { superPotionCount++; lootMessage = '🎁 獲得超級藥水 x1！'; } 
                    else if (lootChance < 0.12) { attackBoostCount++; lootMessage = '✨ 獲得攻擊強化 x1！'; } 
                    else if (lootChance < 0.20) { normalPotionCount++; lootMessage = '🧪 獲得普通藥水 x1！'; } 

                    battleLog.textContent = `💥 擊敗了怪物，獲得 ${xpGained} XP 和 ${coinsEarned} 金幣！` + lootMessage;

                    const leveledUp = checkLevelUp(); 
                    saveGame(); 
                    updateDisplay();
                    
                    if (leveledUp) { setTimeout(generateNewMonster, 3000); } else { setTimeout(generateNewMonster, 2000); }
                    return;
                }

            } else { 
                // 4. 答錯：受到怪物攻擊
                
                // LEO 專屬：防禦判定
                if (selectedClass === 'LEO' && monsterAttackBlocked) {
                    monsterAttackBlocked = false; 
                    battleLog.textContent = "🛡️ 防禦啟動！擋下了怪物的攻擊！";
                } else {
                    playerCurrentHP -= monsterAttackDamage;
                    battleLog.textContent = `❌ 答錯了！怪物反擊，你受到 ${monsterAttackDamage} 點傷害！`;
                    
                    playSound(SFX_MISS); 
                    playerChar.classList.add('hit'); 
                    setTimeout(() => playerChar.classList.remove('hit'), 300); 
                }

                // KIKI 專屬：連擊中斷
                if (selectedClass === 'KIKI') {
                    comboCount = 0;
                    battleLog.textContent += " (連擊中斷)";
                }

                // 檢查玩家是否死亡
                if (playerCurrentHP <= 0) {
                    playerCurrentHP = 0;
                    updateDisplay();
                    battleLog.textContent = `你的精靈倒下了！遊戲結束。總經驗值: ${playerXP} / 剩餘金幣: ${playerCoins}`;
                    alert(`遊戲結束！你累積了 ${playerXP} 經驗值和 ${playerCoins} 金幣。請清除存檔後重新開始。`);
                    return;
                }
            }
            
            // 5. 回合結束
            saveGame(); 
            updateDisplay();
            setTimeout(generateNewQuestion, 1500); 
        }

        // --- 🚀 Mia 專屬：使用數學精華 (新增) ---
        function useMathSpirit() {
            startBGM();
            if (mathSpiritCount <= 0) {
                document.getElementById('battle-log').textContent = "💔 數學精華不足！";
                return;
            }
            
            mathSpiritCount--;
            const healAmount = Math.floor(playerMaxHP * 0.25); 
            playerCurrentHP = Math.min(playerCurrentHP + healAmount, playerMaxHP);

            document.getElementById('battle-log').textContent = `💖 使用數學精華！恢復 ${healAmount} 點 HP！`;
            playSound(SFX_HIT); 

            saveGame();
            updateDisplay();
            setTimeout(generateNewQuestion, 1500); 
        }

        // --- 道具和商店函數 (略) ---
        function usePotion(type) {
            startBGM(); 
            <img width="1024" height="1024" alt="m5" src="https://github.com/user-attachments/assets/eb0699b1-87bc-4994-894c-6c1c95b760e4" />
<img width="1024" height="1024" alt="m4" src="https://github.com/user-attachments/assets/006cff13-af98-41fd-b055-e0b4d6213d6d" /><img width="1024" height="1024" alt="m2" src="https://github.com/user-attachments/assets/447865a9-26e4-42df-b545-fa419150fd3f" />
<img width="1024" height="1024" alt="player_sword_attack" src="https://github.com/user-attachments/assets/2138bb0a-cf5c-4d4e-bfcc-ee73984a7e34" />

<img width="1024" height="1024" alt="m3" src="https://github.com/user-attachments/assets/9da15b18-4859-4bcb-8ed5-dd58cafd6c03" /><img width="1024" height="1024" alt="m1" src="https://github.com/user-attachments/assets/106fc17a-5edf-43a0-89a6-3b24d4b0fa40" />


            let healAmount = 0;
            if (type === 'Normal' && normalPotionCount > 0) { normalPotionCount--; healAmount = 30;
            } else if (type === 'Super' && superPotionCount > 0) { superPotionCount--; healAmount = 60;
            } else { document.getElementById('battle-log').textContent = `沒有 ${type} 藥水了！`; return; }
            
            playerCurrentHP = Math.min(playerCurrentHP + healAmount, playerMaxHP);
            document.getElementById('battle-log').textContent = `💊 使用 ${type} 藥水，恢復 ${healAmount} 點 HP！`;
            playSound(SFX_HIT); 
            saveGame(); 
            updateDisplay();
            setTimeout(generateNewQuestion, 1500);
        }

        function useAttackBoost() {
            startBGM(); 
            
            if (attackBoostCount > 0 && nextAttackBoost === 0) {
                attackBoostCount--;
                nextAttackBoost = 20; 
                document.getElementById('battle-log').textContent = `✨ 攻擊強化啟動！下次攻擊傷害提升 20 點！`;
                playSound(SFX_HIT); 
                saveGame(); 
                updateDisplay();
                setTimeout(generateNewQuestion, 1500);
            } else if (nextAttackBoost > 0) {
                document.getElementById('battle-log').textContent = `你已經處於強化狀態了！`;
            } else {
                document.getElementById('battle-log').textContent = `沒有攻擊強化道具了！`;
            }
        }
        
        function buyItem(itemName, price, type) {
            startBGM(); 
            
            if (playerCoins < price) { document.getElementById('battle-log').textContent = `😭 金幣不足！購買 ${itemName} 需要 ${price} 金幣。`; return; }

            if (type === 'Weapon' && currentWeapon === '精算長劍') { document.getElementById('battle-log').textContent = `你已經擁有並裝備了精算長劍！`; return; }
            if (type === 'Armor' && currentArmor === '心算盾牌') { document.getElementById('battle-log').textContent = `你已經擁有並裝備了心算盾牌！`; return; }

            playerCoins -= price;
            document.getElementById('battle-log').textContent = `🎉 購買並裝備/獲得了 ${itemName}，花費 ${price} 金幣。`;
            playSound(SFX_HIT); 

            if (type === 'Weapon') {
                baseAttackDamage = 25; 
                currentWeapon = itemName;
                updatePlayerAppearance(); 
            } else if (type === 'Armor') {
                playerMaxHP += 20; 
                playerCurrentHP = playerMaxHP; 
                currentArmor = itemName;
                updatePlayerAppearance(); 
            } else {
                switch (itemName) {
                    case '普通藥水': normalPotionCount++; break;
                    case '超級藥水': superPotionCount++; break;
                    case '攻擊強化': attackBoostCount++; break;
                }
            }
            
            saveGame(); 
            updateDisplay();
        }
        
        // --- 網頁初始化 (略) ---
        window.onload = function() {
            if (!loadGame()) {
                document.getElementById('startScreen').style.display = 'block';
            } else {
                 // 確保從存檔載入後，也能設定正確的初始屬性（這修復了上次的 Bug）
                 const classData = CHARACTER_CLASSES[selectedClass];
                 playerMaxHP = playerMaxHP || 100 + classData.hpBonus;
                 baseAttackDamage = baseAttackDamage || 20 + classData.atkBonus;
                 
                 initGame(selectedClass); 
            }
        };

    </script>
</body>
</html>
