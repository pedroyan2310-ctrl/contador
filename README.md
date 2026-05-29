import os
from flask import Flask, render_template_string

app = Flask(__name__)

# Aqui está o código visual (HTML e JavaScript) do seu site
HTML_SITE = """
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>⏱️ Temporizador Digital</title>
    <style>
        body { font-family: 'Arial', sans-serif; text-align: center; margin-top: 50px; background-color: #f4f4f9; color: #333; }
        .container { max-width: 400px; margin: 0 auto; background: white; padding: 30px; border-radius: 10px; box-shadow: 0px 4px 10px rgba(0,0,0,0.1); }
        h1 { font-size: 24px; margin-bottom: 20px; }
        .inputs { margin-bottom: 20px; }
        input { width: 50px; padding: 10px; font-size: 18px; text-align: center; margin: 0 5px; border: 1px solid #ccc; border-radius: 5px; }
        .display { font-size: 48px; font-weight: bold; margin: 20px 0; color: #222; }
        button { padding: 12px 20px; font-size: 16px; margin: 5px; cursor: pointer; border: none; border-radius: 5px; font-weight: bold; width: 100px; transition: 0.2s; }
        .btn-start { background-color: #28a745; color: white; }
        .btn-start:hover { background-color: #218838; }
        .btn-pause { background-color: #ffc107; color: black; }
        .btn-pause:hover { background-color: #e0a800; }
        .btn-stop { background-color: #dc3545; color: white; }
        .btn-stop:hover { background-color: #c82333; }
    </style>
</head>
<body>

    <div class="container">
        <h1>⏱️ Temporizador Digital</h1>

        <div class="inputs">
            <input type="number" id="hrs" value="0" min="0"> h
            <input type="number" id="mins" value="0" min="0" max="59"> m
            <input type="number" id="secs" value="0" min="0" max="59"> s
        </div>

        <div class="display" id="timerDisplay">00:00:00</div>

        <div>
            <button class="btn-start" id="startBtn">Começar</button>
            <button class="btn-pause" id="pauseBtn">Pausar</button>
            <button class="btn-stop" id="stopBtn">Parar</button>
        </div>
    </div>

    <script>
        let countdown;
        let totalSeconds = 0;
        let isPaused = false;

        const display = document.getElementById('timerDisplay');
        const startBtn = document.getElementById('startBtn');
        const pauseBtn = document.getElementById('pauseBtn');
        const stopBtn = document.getElementById('stopBtn');

        function updateDisplay(secondsLeft) {
            const h = Math.floor(secondsLeft / 3600);
            const m = Math.floor((secondsLeft % 3600) / 60);
            const s = secondsLeft % 60;
            display.textContent = `${String(h).padStart(2, '0')}:${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
        }

        startBtn.addEventListener('click', () => {
            if (countdown && !isPaused) return;

            if (!isPaused) {
                const h = parseInt(document.getElementById('hrs').value) || 0;
                const m = parseInt(document.getElementById('mins').value) || 0;
                const s = parseInt(document.getElementById('secs').value) || 0;
                totalSeconds = (h * 3600) + (m * 60) + s;
            }

            if (totalSeconds <= 0) {
                alert("Por favor, insira um tempo maior que zero!");
                return;
            }

            isPaused = false;
            clearInterval(countdown);

            countdown = setInterval(() => {
                totalSeconds--;
                updateDisplay(totalSeconds);

                if (totalSeconds <= 0) {
                    clearInterval(countdown);
                    alert("O tempo acabou! 🔔");
                }
            }, 1000);
        });

        pauseBtn.addEventListener('click', () => {
            if (countdown) {
                clearInterval(countdown);
                isPaused = true;
            }
        });

        stopBtn.addEventListener('click', () => {
            clearInterval(countdown);
            totalSeconds = 0;
            isPaused = false;
            display.textContent = "00:00:00";
        });
    </script>
</body>
</html>
"""


@app.route("/")
def home():
    return render_template_string(HTML_SITE)


if __name__ == "__main__":
    # Configuração necessária para funcionar tanto no seu computador quanto no servidor de postagem
    port = int(os.environ.get("PORT", 5000))
    app.run(host="0.0.0.0", port=port)
