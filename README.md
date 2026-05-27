# Dinosaur-clone-game
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <title>Pogromca Dinozaurów</title>
    <style>
        body { margin: 0; background: #f7f7f7; display: flex; justify-content: center; align-items: center; height: 100vh; font-family: sans-serif; }
        canvas { background: #fff; border-bottom: 2px solid #333; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        #score { position: absolute; top: 20px; font-size: 24px; color: #333; font-weight: bold; }
    </style>
</head>
<body>
    <div id="score">Punkty: 0</div>
    <canvas id="gameCanvas" width="800" height="300"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreEl = document.getElementById('score');

        let score = 0;
        let gameOver = false;

        // Dinozaur
        const dino = { x: 50, y: 220, width: 40, height: 50, vy: 0, gravity: 0.6, jumpForce: -12, grounded: true };

        // Przeszkody (Kaktusy)
        let obstacles = [];
        let spawnTimer = 0;

        function spawnObstacle() {
            let size = Math.random() * (40 - 20) + 20;
            obstacles.push({ x: canvas.width, y: 270 - size, width: 20, height: size, speed: 6 });
        }

        window.addEventListener('keydown', (e) => {
            if ((e.code === 'Space' || e.code === 'ArrowUp') && dino.grounded && !gameOver) {
                dino.vy = dino.jumpForce;
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
            dino.y = 220;
            dino.vy = 0;
            loop();
        }

        function loop() {
            if (gameOver) {
                ctx.fillStyle = '#333';
                ctx.font = '30px Arial';
                ctx.fillText('GRA PRZEGRANA! Spacja = Restart', canvas.width/2 - 220, canvas.height/2);
                return;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Fizyka Dino
            dino.vy += dino.gravity;
            dino.y += dino.vy;
            if (dino.y > 220) { dino.y = 220; dino.vy = 0; dino.grounded = true; }

            // Rysuj Dino
            ctx.fillStyle = '#535353';
            ctx.fillRect(dino.x, dino.y, dino.width, dino.height);

            // Generator przeszkód
            spawnTimer++;
            if (spawnTimer > Math.random() * (120 - 70) + 70) { spawnObstacle(); spawnTimer = 0; }

            // Ruch i rysowanie przeszkód
            for (let i = obstacles.length - 1; i >= 0; i--) {
                let o = obstacles[i];
                o.x -= o.speed;

                ctx.fillStyle = '#228B22';
                ctx.fillRect(o.x, o.y, o.width, o.height);

                // Kolizje
                if (dino.x < o.x + o.width && dino.x + dino.width > o.x && dino.y < o.y + o.height && dino.y + dino.height > o.y) {
                    gameOver = true;
                }

                if (o.x + o.width < 0) { obstacles.splice(i, 1); score++; scoreEl.innerText = 'Punkty: ' + score; }
            }

            requestAnimationFrame(loop);
        }
        loop();
    </script>
</body>
</html>
