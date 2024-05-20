<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة طخ</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
        }
        #gameArea {
            position: relative;
            width: 100vw;
            height: 100vh;
            background-color: #000;
            overflow: hidden;
        }
        .player {
            position: absolute;
            bottom: 20px;
            left: 50%;
            width: 50px;
            height: 50px;
            background-color: green;
            transform: translateX(-50%);
        }
        .bullet {
            position: absolute;
            width: 5px;
            height: 10px;
            background-color: yellow;
        }
        .enemy {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div id="gameArea">
        <div class="player" id="player"></div>
    </div>
    <script>
        const gameArea = document.getElementById('gameArea');
        const player = document.getElementById('player');
        const gameWidth = gameArea.clientWidth;
        const gameHeight = gameArea.clientHeight;
        const playerWidth = player.clientWidth;
        const playerHeight = player.clientHeight;

        let playerPositionX = gameWidth / 2 - playerWidth / 2;
        let playerPositionY = gameHeight - playerHeight - 20;

        const keys = {
            ArrowLeft: false,
            ArrowRight: false,
            Space: false
        };

        document.addEventListener('keydown', (e) => {
            if (keys.hasOwnProperty(e.key)) keys[e.key] = true;
        });

        document.addEventListener('keyup', (e) => {
            if (keys.hasOwnProperty(e.key)) keys[e.key] = false;
        });

        function movePlayer() {
            if (keys.ArrowLeft && playerPositionX > 0) {
                playerPositionX -= 5;
            }
            if (keys.ArrowRight && playerPositionX < gameWidth - playerWidth) {
                playerPositionX += 5;
            }
            player.style.left = playerPositionX + 'px';
        }

        function shootBullet() {
            const bullet = document.createElement('div');
            bullet.classList.add('bullet');
            bullet.style.left = playerPositionX + playerWidth / 2 - 2.5 + 'px';
            bullet.style.top = playerPositionY + 'px';
            gameArea.appendChild(bullet);

            const bulletInterval = setInterval(() => {
                let bulletPositionY = bullet.offsetTop;
                if (bulletPositionY < 0) {
                    clearInterval(bulletInterval);
                    bullet.remove();
                } else {
                    bullet.style.top = bulletPositionY - 5 + 'px';
                }
                detectCollision(bullet, bulletInterval);
            }, 20);
        }

        function createEnemy() {
            const enemy = document.createElement('div');
            enemy.classList.add('enemy');
            enemy.style.left = Math.random() * (gameWidth - 50) + 'px';
            enemy.style.top = '-50px';
            gameArea.appendChild(enemy);

            const enemyInterval = setInterval(() => {
                let enemyPositionY = enemy.offsetTop;
                if (enemyPositionY > gameHeight) {
                    clearInterval(enemyInterval);
                    enemy.remove();
                } else {
                    enemy.style.top = enemyPositionY + 2 + 'px';
                }
            }, 20);
        }

        function detectCollision(bullet, bulletInterval) {
            const enemies = document.querySelectorAll('.enemy');
            enemies.forEach((enemy) => {
                const enemyRect = enemy.getBoundingClientRect();
                const bulletRect = bullet.getBoundingClientRect();

                if (bulletRect.x < enemyRect.x + enemyRect.width &&
                    bulletRect.x + bulletRect.width > enemyRect.x &&
                    bulletRect.y < enemyRect.y + enemyRect.height &&
                    bulletRect.y + bulletRect.height > enemyRect.y) {
                    clearInterval(bulletInterval);
                    bullet.remove();
                    enemy.remove();
                }
            });
        }

        function gameLoop() {
            movePlayer();
            if (keys.Space) {
                shootBullet();
                keys.Space = false;
            }
            requestAnimationFrame(gameLoop);
        }

        setInterval(createEnemy, 2000);

        gameLoop();
    </script>
</body>
</html>
