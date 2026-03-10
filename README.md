[index.html](https://github.com/user-attachments/files/25859461/index.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>极速方块挑战</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            font-family: 'PingFang SC', 'Microsoft YaHei', sans-serif;
            overflow: hidden;
        }

        #game-container {
            position: relative;
            width: 100vw;
            height: 100vh;
            max-width: 800px;
            max-height: 1200px;
            background: #1a1a2e;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            overflow: hidden;
        }

        #game-canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        .ui-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 20px;
        }

        .hud {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            color: white;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }

        .hud-item {
            font-size: 24px;
            margin-bottom: 10px;
        }

        .hud-label {
            font-size: 14px;
            color: #888;
        }

        .hud-value {
            font-size: 28px;
            font-weight: bold;
            color: #4ECDC4;
        }

        .lives {
            display: flex;
            gap: 10px;
        }

        .heart {
            font-size: 30px;
        }

        .menu-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.85);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            pointer-events: all;
            z-index: 100;
        }

        .menu-overlay.hidden {
            display: none;
        }

        .game-title {
            font-size: 48px;
            font-weight: bold;
            color: #FF6B6B;
            text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.5);
            margin-bottom: 20px;
            text-align: center;
        }

        .game-subtitle {
            font-size: 32px;
            color: #4ECDC4;
            margin-bottom: 40px;
            text-align: center;
        }

        .high-score {
            font-size: 24px;
            color: #FFD700;
            margin-bottom: 40px;
            text-align: center;
        }

        .btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 15px 60px;
            font-size: 24px;
            border-radius: 30px;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
            pointer-events: all;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        }

        .btn:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.4);
        }

        .btn:active {
            transform: scale(0.95);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
        }

        .btn-gold {
            background: linear-gradient(135deg, #FFD700 0%, #FFA500 100%);
            color: #333;
        }

        .score-display {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 60px;
            font-weight: bold;
            color: #FFD700;
            text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.5);
            opacity: 0;
            transition: all 0.3s;
            pointer-events: none;
        }

        .score-display.show {
            opacity: 1;
            animation: scorePopup 1s ease-out;
        }

        @keyframes scorePopup {
            0% {
                transform: translate(-50%, -50%) scale(0.5);
                opacity: 0;
            }
            50% {
                transform: translate(-50%, -50%) scale(1.2);
                opacity: 1;
            }
            100% {
                transform: translate(-50%, -80%) scale(1);
                opacity: 0;
            }
        }

        .level-up {
            position: absolute;
            top: 30%;
            left: 50%;
            transform: translateX(-50%);
            font-size: 48px;
            font-weight: bold;
            color: #4ECDC4;
            text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.5);
            opacity: 0;
            pointer-events: none;
        }

        .level-up.show {
            animation: levelUpPopup 2s ease-out;
        }

        @keyframes levelUpPopup {
            0% {
                opacity: 0;
                transform: translateX(-50%) translateY(20px);
            }
            20% {
                opacity: 1;
                transform: translateX(-50%) translateY(0);
            }
            80% {
                opacity: 1;
                transform: translateX(-50%) translateY(0);
            }
            100% {
                opacity: 0;
                transform: translateX(-50%) translateY(-20px);
            }
        }

        .instructions {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            color: rgba(255, 255, 255, 0.7);
            font-size: 14px;
            text-align: center;
            pointer-events: none;
        }

        .touch-indicator {
            position: absolute;
            width: 100px;
            height: 100px;
            border: 3px solid rgba(255, 255, 255, 0.5);
            border-radius: 50%;
            pointer-events: none;
            animation: touchRipple 0.5s ease-out;
        }

        @keyframes touchRipple {
            0% {
                transform: scale(0);
                opacity: 1;
            }
            100% {
                transform: scale(2);
                opacity: 0;
            }
        }

        .ad-banner {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 60px;
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            color: white;
            font-size: 14px;
            pointer-events: none;
            z-index: 50;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <canvas id="game-canvas"></canvas>

        <div class="ui-overlay" id="hud">
            <div class="hud">
                <div>
                    <div class="hud-item">
                        <span class="hud-label">分数</span><br>
                        <span class="hud-value" id="score">0</span>
                    </div>
                    <div class="hud-item">
                        <span class="hud-label">等级</span><br>
                        <span class="hud-value" id="level">1</span>
                    </div>
                </div>
                <div class="lives" id="lives">
                    <span class="heart">❤️</span>
                    <span class="heart">❤️</span>
                    <span class="heart">❤️</span>
                </div>
            </div>
        </div>

        <div class="score-display" id="score-display"></div>
        <div class="level-up" id="level-up"></div>

        <div class="menu-overlay" id="menu-overlay">
            <h1 class="game-title">极速方块</h1>
            <h2 class="game-subtitle">挑战</h2>
            <div class="high-score">最高分: <span id="menu-high-score">0</span></div>
            <button class="btn" id="start-btn">开始游戏</button>
            <button class="btn btn-secondary" id="signin-btn">每日签到</button>
            <div class="instructions">拖动方块收集目标 | 方向键也可移动</div>
        </div>

        <div class="menu-overlay hidden" id="gameover-overlay">
            <h1 class="game-title">游戏结束</h1>
            <div class="high-score">本局分数: <span id="final-score">0</span></div>
            <div class="high-score">最高分: <span id="high-score">0</span></div>
            <button class="btn" id="restart-btn">重新开始</button>
            <button class="btn btn-gold" id="revive-btn">看广告复活</button>
            <button class="btn btn-secondary" id="menu-btn">返回菜单</button>
        </div>

        <div class="ad-banner" id="ad-banner">
            🎮 广告位 - 感谢您的支持！
        </div>
    </div>

    <script>
        // 游戏核心逻辑
        const canvas = document.getElementById('game-canvas');
        const ctx = canvas.getContext('2d');
        const container = document.getElementById('game-container');

        // UI元素
        const scoreEl = document.getElementById('score');
        const levelEl = document.getElementById('level');
        const livesEl = document.getElementById('lives');
        const menuOverlay = document.getElementById('menu-overlay');
        const gameoverOverlay = document.getElementById('gameover-overlay');
        const scoreDisplay = document.getElementById('score-display');
        const levelUpEl = document.getElementById('level-up');
        const adBanner = document.getElementById('ad-banner');

        // 游戏状态
        let gameState = 'menu'; // menu, playing, gameover
        let score = 0;
        let level = 1;
        let lives = 3;
        let gameSpeed = 1.0;
        let highScore = localStorage.getItem('highScore') || 0;
        let animationId = null;

        // 方块
        let playerBlock = null;
        let blocks = [];
        const BLOCK_SIZE = 60;
        const COLORS = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4', '#FFEAA7', '#DDA0DD'];

        // 输入状态
        let isDragging = false;
        let keys = { left: false, right: false, up: false, down: false };

        // 初始化Canvas尺寸
        function initCanvas() {
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
        }

        initCanvas();
        window.addEventListener('resize', initCanvas);

        // 更新最高分显示
        document.getElementById('menu-high-score').textContent = highScore;

        // 创建玩家方块
        function createPlayerBlock() {
            playerBlock = {
                x: canvas.width / 2 - BLOCK_SIZE / 2,
                y: canvas.height - BLOCK_SIZE - 100,
                width: BLOCK_SIZE * 1.2,
                height: BLOCK_SIZE * 1.2,
                color: COLORS[0]
            };
        }

        // 生成目标方块
        function spawnBlock() {
            const margin = 50;
            const x = margin + Math.random() * (canvas.width - margin * 2 - BLOCK_SIZE);
            const isBonus = Math.random() < 0.1;

            blocks.push({
                x: x,
                y: -BLOCK_SIZE - Math.random() * 200,
                width: BLOCK_SIZE,
                height: BLOCK_SIZE,
                color: isBonus ? '#FFD700' : COLORS[Math.floor(Math.random() * COLORS.length)],
                velocityY: (2 + Math.random() * 2) * gameSpeed,
                value: isBonus ? (Math.floor(Math.random() * 20) + 10) * 2 : Math.floor(Math.random() * 20) + 10,
                isBonus: isBonus
            });
        }

        // 碰撞检测
        function checkCollision(rect1, rect2) {
            return rect1.x < rect2.x + rect2.width &&
                   rect1.x + rect1.width > rect2.x &&
                   rect1.y < rect2.y + rect2.height &&
                   rect1.y + rect1.height > rect2.y;
        }

        // 显示得分
        function showScorePopup(points, x, y) {
            scoreDisplay.textContent = `+${points}`;
            scoreDisplay.style.left = x + 'px';
            scoreDisplay.style.top = y + 'px';
            scoreDisplay.classList.remove('show');
            void scoreDisplay.offsetWidth;
            scoreDisplay.classList.add('show');
        }

        // 显示升级
        function showLevelUp() {
            levelUpEl.textContent = `等级提升！Lv.${level}`;
            levelUpEl.classList.remove('show');
            void levelUpEl.offsetWidth;
            levelUpEl.classList.add('show');
        }

        // 更新生命显示
        function updateLives() {
            let heartsHTML = '';
            for (let i = 0; i < lives; i++) {
                heartsHTML += '<span class="heart">❤️</span>';
            }
            for (let i = lives; i < 3; i++) {
                heartsHTML += '<span class="heart" style="opacity: 0.3">❤️</span>';
            }
            livesEl.innerHTML = heartsHTML;
        }

        // 游戏开始
        function startGame() {
            gameState = 'playing';
            score = 0;
            level = 1;
            lives = 3;
            gameSpeed = 1.0;
            blocks = [];

            createPlayerBlock();
            updateLives();
            updateScore();
            updateLevel();

            menuOverlay.classList.add('hidden');
            gameoverOverlay.classList.add('hidden');
            adBanner.style.display = 'flex';

            // 开始游戏循环
            if (animationId) cancelAnimationFrame(animationId);
            gameLoop();
        }

        // 更新分数
        function updateScore() {
            scoreEl.textContent = score;

            // 检查升级
            const newLevel = Math.floor(score / 200) + 1;
            if (newLevel > level) {
                level = newLevel;
                gameSpeed = 1.0 + (level - 1) * 0.1;
                updateLevel();
                showLevelUp();
            }
        }

        function updateLevel() {
            levelEl.textContent = level;
        }

        // 游戏结束
        function gameOver() {
            gameState = 'gameover';

            if (score > highScore) {
                highScore = score;
                localStorage.setItem('highScore', highScore);
            }

            document.getElementById('final-score').textContent = score;
            document.getElementById('high-score').textContent = highScore;
            document.getElementById('menu-high-score').textContent = highScore;

            gameoverOverlay.classList.remove('hidden');
            adBanner.style.display = 'none';
        }

        // 渲染背景
        function renderBackground() {
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#1a1a2e');
            gradient.addColorStop(1, '#16213e');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 网格
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.05)';
            ctx.lineWidth = 1;
            const gridSize = 50;

            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, canvas.height);
                ctx.stroke();
            }

            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(canvas.width, y);
                ctx.stroke();
            }
        }

        // 绘制方块
        function drawBlock(block, isPlayer = false) {
            ctx.shadowColor = block.color;
            ctx.shadowBlur = isPlayer ? 25 : 15;

            const gradient = ctx.createLinearGradient(block.x, block.y, block.x + block.width, block.y + block.height);
            gradient.addColorStop(0, block.color);
            gradient.addColorStop(1, isPlayer ? '#ffffff' : block.color);

            ctx.fillStyle = gradient;
            ctx.fillRect(block.x, block.y, block.width, block.height);

            ctx.strokeStyle = '#ffffff';
            ctx.lineWidth = isPlayer ? 3 : 2;
            ctx.strokeRect(block.x, block.y, block.width, block.height);

            if (block.isBonus) {
                ctx.fillStyle = '#ffffff';
                ctx.font = '20px Arial';
                ctx.textAlign = 'center';
                ctx.fillText('★', block.x + block.width / 2, block.y + block.height / 2 + 7);
            }

            if (isPlayer) {
                ctx.beginPath();
                ctx.arc(block.x + block.width / 2, block.y + block.height / 2, 8, 0, Math.PI * 2);
                ctx.fillStyle = '#ffffff';
                ctx.fill();
            }

            ctx.shadowBlur = 0;
        }

        // 游戏循环
        let lastSpawnTime = 0;
        function gameLoop(timestamp) {
            if (gameState !== 'playing') return;

            // 清空画布
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            renderBackground();

            // 生成方块
            if (timestamp - lastSpawnTime > 1500 / gameSpeed) {
                spawnBlock();
                lastSpawnTime = timestamp;
            }

            // 更新方块位置
            blocks = blocks.filter(block => {
                block.y += block.velocityY;

                // 检测碰撞
                if (checkCollision(playerBlock, block)) {
                    score += block.value;
                    updateScore();
                    showScorePopup(block.value, block.x, block.y);
                    return false;
                }

                // 检测是否落到底部
                if (block.y > canvas.height - BLOCK_SIZE) {
                    lives--;
                    updateLives();
                    if (lives <= 0) {
                        gameOver();
                    }
                    return false;
                }

                return block.y < canvas.height + 100;
            });

            // 键盘移动
            if (keys.left) playerBlock.x -= 8;
            if (keys.right) playerBlock.x += 8;
            if (keys.up) playerBlock.y -= 8;
            if (keys.down) playerBlock.y += 8;

            // 限制玩家方块在屏幕内
            playerBlock.x = Math.max(0, Math.min(canvas.width - playerBlock.width, playerBlock.x));
            playerBlock.y = Math.max(0, Math.min(canvas.height - playerBlock.height - 60, playerBlock.y));

            // 绘制所有方块
            blocks.forEach(block => drawBlock(block));

            // 绘制玩家方块
            if (playerBlock) {
                drawBlock(playerBlock, true);
            }

            animationId = requestAnimationFrame(gameLoop);
        }

        // 触摸/鼠标事件
        function getEventPos(e) {
            const rect = canvas.getBoundingClientRect();
            const clientX = e.touches ? e.touches[0].clientX : e.clientX;
            const clientY = e.touches ? e.touches[0].clientY : e.clientY;
            return {
                x: clientX - rect.left,
                y: clientY - rect.top
            };
        }

        canvas.addEventListener('mousedown', (e) => {
            if (gameState !== 'playing') return;
            const pos = getEventPos(e);
            if (checkCollision({ x: pos.x - 25, y: pos.y - 25, width: 50, height: 50 }, playerBlock)) {
                isDragging = true;
            }
        });

        canvas.addEventListener('mousemove', (e) => {
            if (gameState !== 'playing' || !isDragging) return;
            const pos = getEventPos(e);
            playerBlock.x = pos.x - playerBlock.width / 2;
            playerBlock.y = pos.y - playerBlock.height / 2;
        });

        canvas.addEventListener('mouseup', () => {
            isDragging = false;
        });

        canvas.addEventListener('mouseleave', () => {
            isDragging = false;
        });

        // 触摸事件
        canvas.addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (gameState !== 'playing') return;
            const pos = getEventPos(e);
            if (checkCollision({ x: pos.x - 30, y: pos.y - 30, width: 60, height: 60 }, playerBlock)) {
                isDragging = true;
            }
        });

        canvas.addEventListener('touchmove', (e) => {
            e.preventDefault();
            if (gameState !== 'playing' || !isDragging) return;
            const pos = getEventPos(e);
            playerBlock.x = pos.x - playerBlock.width / 2;
            playerBlock.y = pos.y - playerBlock.height / 2;
        });

        canvas.addEventListener('touchend', () => {
            isDragging = false;
        });

        // 键盘事件
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a') keys.left = true;
            if (e.key === 'ArrowRight' || e.key === 'd') keys.right = true;
            if (e.key === 'ArrowUp' || e.key === 'w') keys.up = true;
            if (e.key === 'ArrowDown' || e.key === 's') keys.down = true;
        });

        document.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a') keys.left = false;
            if (e.key === 'ArrowRight' || e.key === 'd') keys.right = false;
            if (e.key === 'ArrowUp' || e.key === 'w') keys.up = false;
            if (e.key === 'ArrowDown' || e.key === 's') keys.down = false;
        });

        // 按钮事件
        document.getElementById('start-btn').addEventListener('click', startGame);
        document.getElementById('restart-btn').addEventListener('click', startGame);
        document.getElementById('menu-btn').addEventListener('click', () => {
            gameState = 'menu';
            gameoverOverlay.classList.add('hidden');
            menuOverlay.classList.remove('hidden');
            adBanner.style.display = 'flex';
        });

        document.getElementById('revive-btn').addEventListener('click', () => {
            // 模拟看广告复活
            alert('观看广告成功！获得额外生命！');
            lives = 1;
            updateLives();
            gameState = 'playing';
            gameoverOverlay.classList.add('hidden');
            adBanner.style.display = 'flex';
            lastSpawnTime = performance.now();
            gameLoop(lastSpawnTime);
        });

        document.getElementById('signin-btn').addEventListener('click', () => {
            const lastSign = localStorage.getItem('lastSignDate');
            const today = new Date().toDateString();

            if (lastSign === today) {
                alert('今日已签到！');
            } else {
                localStorage.setItem('lastSignDate', today);
                alert(`签到成功！获得 15 金币！\n连续签到可获得更多奖励！`);
            }
        });

        // 初始渲染
        renderBackground();
    </script>
</body>
</html>
