<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dino Slayer - Arcade Game</title>
    <style>
        body { 
            margin: 0; 
            background: #121212; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            height: 100vh; 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
        }
        canvas { 
            background: #1e1e1e; 
            border-bottom: 3px solid #1db954; 
            box-shadow: 0 8px 24px rgba(0,0,0,0.5); 
            border-radius: 4px;
        }
        #score { 
            position: absolute; 
            top: 40px; 
            font-size: 28px; 
            color: #ffffff; 
            font-weight: 600; 
            letter-spacing: 1px;
        }
    </style>
</head>
<body>
    <div id="score">Score: 0</div>
    <canvas id="gameCanvas" width="800" height="300"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreEl = document.getElementById('score');

        // Game Configuration Constants
        const GRAVITY = 0.6;
        const JUMP_FORCE = -12;
        const GROUND_Y = 220;
        const MIN_SPAWN_INTERVAL = 70;
        const MAX_SPAWN_INTERVAL = 120;

        let score = 0;
        let gameOver = false;
        let obstacles = [];
        let spawnTimer = 0;

        // Player Entity
        const dino = { 
            x: 50, 
            y: GROUND_Y, 
            width: 40, 
            height: 50, 
            vy: 0, 
            grounded: true 
        };

        function spawnObstacle() {
            const minHeight = 20;
            const maxHeight = 40;
            let size = Math.random() * (maxHeight - minHeight) + minHeight;
            obstacles.push({ 
                x: canvas.width, 
                y: 270 - size, 
                width: 20, 
                height: size, 
                speed: 6 
            });
        }

        // Input Listeners
        window.addEventListener('keydown', (e) => {
            if ((e.code === 'Space' || e.code === 'ArrowUp') && dino.grounded && !gameOver) {
                dino.vy = JUMP_FORCE;
                dino.grounded = false;
            }
            if (gameOver && e.code === 'Space') {
                resetGame();
            }
        });

        function resetGame() {
            score = 0;
            gameOver = false;
            obstacles = [];
            dino.y = GROUND_Y;
            dino.vy = 0;
            scoreEl.innerText = 'Score: 0';
            loop();
        }

        // Main Game Loop
        function loop() {
            if (gameOver) {
                ctx.fillStyle = '#ffffff';
                ctx.font = 'bold 26px sans-serif';
                ctx.textAlign = 'center';
                ctx.fillText('GAME OVER', canvas.width / 2, canvas.height / 2 - 20);
                
                ctx.fillStyle = '#b3b3b3';
                ctx.font = '18px sans-serif';
                ctx.fillText('Press Space to Restart', canvas.width / 2, canvas.height / 2 + 20);
                return;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Physics Engine
            dino.vy += GRAVITY;
            dino.y += dino.vy;
            if (dino.y > GROUND_Y) { 
                dino.y = GROUND_Y; 
                dino.vy = 0; 
                dino.grounded = true; 
            }

            // Render Player
            ctx.fillStyle = '#1db954';
            ctx.fillRect(dino.x, dino.y, dino.width, dino.height);

            // Obstacle Spawning Logic
            spawnTimer++;
            const randomInterval = Math.random() * (MAX_SPAWN_INTERVAL - MIN_SPAWN_INTERVAL) + MIN_SPAWN_INTERVAL;
            if (spawnTimer > randomInterval) { 
                spawnObstacle(); 
                spawnTimer = 0; 
            }

            // Obstacle Update and Collision Detection
            for (let i = obstacles.length - 1; i >= 0; i--) {
                let o = obstacles[i];
                o.x -= o.speed;

                // Render Obstacle
                ctx.fillStyle = '#ff6700';
                ctx.fillRect(o.x, o.y, o.width, o.height);

                // AABB Collision Detection
                if (dino.x < o.x + o.width && 
                    dino.x + dino.width > o.x && 
                    dino.y < o.y + o.height && 
                    dino.y + dino.height > o.y) {
                    gameOver = true;
                }

                // Score Tracking
                if (o.x + o.width < 0) { 
                    obstacles.splice(i, 1); 
                    score++; 
                    scoreEl.innerText = 'Score: ' + score; 
                }
            }

            requestAnimationFrame(loop);
        }

        // Initialize Game
        loop();
    </script>
</body>
</html>

