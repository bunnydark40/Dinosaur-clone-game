Dinosaur-clone-game
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <title>Pogromca Dinozaurów</title>
    <style>
        body { margin: 0; background: #f7f7f7; display: flex; justify-content: cente; align-items: center; height: 100vh;             dino.y += dino.vy;
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
