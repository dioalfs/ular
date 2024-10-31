# ular
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Permainan Ular Realistis</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #282c34;
            color: #fff;
            font-family: Arial, sans-serif;
        }
        canvas {
            background-color: #222;
            border: 2px solid #fff;
            border-radius: 10px;
        }
        #score {
            position: absolute;
            top: 20px;
            font-size: 20px;
        }
        #highScore {
            position: absolute;
            top: 50px;
            font-size: 20px;
        }
    </style>
</head>
<body>
    <div id="score">Score: 0</div>
    <div id="highScore">High Score: 0</div>
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const boxSize = 20; // Ukuran setiap kotak
        let score = 0;
        let highScore = localStorage.getItem("highScore") ? parseInt(localStorage.getItem("highScore")) : 0;
        let isPaused = false;
        let gameOver = false;
        const initialSpeed = 150; // Kecepatan awal (dalam milidetik)
        let snakeSpeed = initialSpeed; // Menggunakan kecepatan awal

        let snake = [{ x: boxSize * 5, y: boxSize * 5 }];
        let direction = "RIGHT";

        let food = {
            x: Math.floor(Math.random() * canvas.width / boxSize) * boxSize,
            y: Math.floor(Math.random() * canvas.height / boxSize) * boxSize
        };

        document.getElementById("highScore").innerText = "High Score: " + highScore;
        document.addEventListener("keydown", handleKeydown);

        function handleKeydown(event) {
            if (event.key === " ") {
                if (gameOver) {
                    resetGame();  // Restart game jika game over
                } else {
                    isPaused = !isPaused;  // Pause atau unpause game
                }
            } else if (!isPaused) {
                // Kontrol arah hanya jika game tidak pause
                if (event.key === "ArrowUp" && direction !== "DOWN") direction = "UP";
                else if (event.key === "ArrowDown" && direction !== "UP") direction = "DOWN";
                else if (event.key === "ArrowLeft" && direction !== "RIGHT") direction = "LEFT";
                else if (event.key === "ArrowRight" && direction !== "LEFT") direction = "RIGHT";
            }
        }

        function resetGame() {
            // Mengatur ulang variabel untuk mulai permainan baru
            score = 0;
            gameOver = false;
            snake = [{ x: boxSize * 5, y: boxSize * 5 }];
            direction = "RIGHT";
            snakeSpeed = initialSpeed;  // Mengatur kecepatan kembali ke awal
            food = {
                x: Math.floor(Math.random() * canvas.width / boxSize) * boxSize,
                y: Math.floor(Math.random() * canvas.height / boxSize) * boxSize
            };
            document.getElementById("score").innerText = "Score: " + score;

            // Memperbarui interval
            clearInterval(gameInterval);
            gameInterval = setInterval(draw, snakeSpeed);
        }

        function draw() {
            if (isPaused || gameOver) return;  // Jangan lakukan apa-apa jika pause atau game over

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Menggambar makanan dengan efek
            ctx.fillStyle = "gold"; // Warna makanan baru
            ctx.shadowColor = "rgba(255, 215, 0, 0.5)"; // Bayangan makanan
            ctx.shadowBlur = 10; // Tingkat kekaburan bayangan
            ctx.fillRect(food.x, food.y, boxSize, boxSize);
            ctx.shadowColor = "transparent"; // Menghapus bayangan

            // Menggerakkan ular
            let head = { x: snake[0].x, y: snake[0].y };

            if (direction === "UP") head.y -= boxSize;
            if (direction === "DOWN") head.y += boxSize;
            if (direction === "LEFT") head.x -= boxSize;
            if (direction === "RIGHT") head.x += boxSize;

            // Menambahkan kepala baru ke badan ular
            snake.unshift(head);

            // Jika ular memakan makanan
            if (head.x === food.x && head.y === food.y) {
                score++;
                document.getElementById("score").innerText = "Score: " + score;

                // Peningkatan kecepatan setiap 5 poin
                if (score % 5 === 0 && snakeSpeed > 50) {
                    snakeSpeed -= 10;
                    clearInterval(gameInterval);
                    gameInterval = setInterval(draw, snakeSpeed);
                }

                food = {
                    x: Math.floor(Math.random() * canvas.width / boxSize) * boxSize,
                    y: Math.floor(Math.random() * canvas.height / boxSize) * boxSize
                };
            } else {
                snake.pop(); // Menghapus ekor jika tidak makan
            }

            // Game over jika ular keluar dari batas atau menabrak tubuhnya sendiri
            if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height ||
                snake.slice(1).some(segment => segment.x === head.x && segment.y === head.y)) {
                gameOver = true;
                // Memperbarui skor tertinggi
                if (score > highScore) {
                    highScore = score;
                    localStorage.setItem("highScore", highScore);
                    document.getElementById("highScore").innerText = "High Score: " + highScore;
                }
                alert("Game Over! Skor Anda: " + score + "\nTekan Spasi untuk Mulai Lagi");
            }

            // Menggambar badan ular
            for (let i = 0; i < snake.length; i++) {
                if (i === 0) {
                    ctx.fillStyle = "#32CD32"; // Kepala ular warna hijau tua
                    ctx.beginPath();
                    ctx.arc(snake[i].x + boxSize / 2, snake[i].y + boxSize / 2, boxSize / 2, 0, Math.PI * 2);
                    ctx.fill();
                } else {
                    ctx.fillStyle = "#228B22"; // Badan ular warna hijau lebih tua
                    ctx.fillRect(snake[i].x, snake[i].y, boxSize, boxSize);
                }
            }
        }

        // Menjalankan fungsi draw dengan interval berdasarkan snakeSpeed
        let gameInterval = setInterval(draw, snakeSpeed);
    </script>
</body>
</html>
 
