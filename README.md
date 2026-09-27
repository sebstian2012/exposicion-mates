<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Juego de Chocolate - Nivel 1</title>
    <style>
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: #2c1d11;
            color: #f3e5ab;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .container {
            background-color: #3d2817;
            border: 4px solid #8b5a2b;
            border-radius: 15px;
            padding: 25px;
            max-width: 650px;
            width: 100%;
            box-shadow: 0 10px 25px rgba(0,0,0,0.6);
            text-align: center;
        }

        h1 {
            color: #d4a373;
            margin-top: 0;
            font-size: 1.8rem;
        }

        p {
            color: #e6ccb2;
            font-size: 1.05rem;
            line-height: 1.4;
        }

        /* Área de dibujo del chocolate */
        .canvas-container {
            margin: 20px auto;
            background-color: #1e130a;
            border-radius: 10px;
            padding: 15px;
            display: inline-block;
            border: 2px dashed #8b5a2b;
        }

        canvas {
            display: block;
            margin: 0 auto;
        }

        /* Panel de preguntas e inputs */
        .controls {
            background-color: #2b1a0e;
            border-radius: 10px;
            padding: 15px;
            margin-top: 15px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            align-items: center;
        }

        .input-group {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 1.1rem;
            flex-wrap: wrap;
            justify-content: center;
        }

        label {
            color: #faedcd;
            font-weight: bold;
        }

        input[type="number"] {
            width: 80px;
            padding: 8px;
            font-size: 1.2rem;
            text-align: center;
            border-radius: 6px;
            border: 2px solid #8b5a2b;
            background-color: #fff9e6;
            color: #2c1d11;
            font-weight: bold;
        }

        input[type="number"]:focus {
            outline: none;
            border-color: #d4a373;
            box-shadow: 0 0 8px #d4a373;
        }

        button {
            background-color: #8b5a2b;
            color: #fff9e6;
            border: none;
            padding: 12px 25px;
            font-size: 1.1rem;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s ease;
            margin-top: 10px;
        }

        button:hover {
            background-color: #a46a32;
            transform: scale(1.03);
        }

        /* Mensajes de retroalimentación */
        #feedback {
            margin-top: 15px;
            font-size: 1.2rem;
            font-weight: bold;
            min-height: 30px;
        }

        .correct {
            color: #2a9d8f;
        }

        .incorrect {
            color: #e76f51;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>🍫 El Maestro Chocolatero - Nivel 1</h1>
    <p>Suma los bordes grabados en cada pieza para descubrir el <b>Ancho</b> y el <b>Alto</b>. Luego calcula el <b>Total</b> de cm².</p>

    <div class="canvas-container">
        <canvas id="chocolateCanvas" width="460" height="340"></canvas>
    </div>

    <div class="controls">
        <div class="input-group">
            <label for="anchoInput">Ancho Total:</label>
            <input type="number" id="anchoInput" placeholder="cm">
            <span>cm</span>
        </div>

        <div class="input-group">
            <label for="altoInput">Alto Total:</label>
            <input type="number" id="altoInput" placeholder="cm">
            <span>cm</span>
        </div>

        <div class="input-group">
            <label for="totalInput">Total (Ancho × Alto):</label>
            <input type="number" id="totalInput" placeholder="cm²">
            <span>cm²</span>
        </div>

        <button onclick="comprobarRespuesta()">Comprobar Resultado 🚀</button>
    </div>

    <div id="feedback"></div>
</div>

<script>
    const canvas = document.getElementById('chocolateCanvas');
    const ctx = canvas.getContext('2d');

    // Escala para ajustar la representación visual en el canvas (1 cm = 36 píxeles)
    const SCALE = 36;
    const OFFSET_X = 30;
    const OFFSET_Y = 30;

    // Colores tipo Chocolate
    const COLOR_NEGRO = '#3b2219';   // Bloques 5x5
    const COLOR_LECHE = '#7b4b2a';   // Barras 5x1 / 1x5
    const COLOR_BLANCO = '#e8d5b5';  // Pastillas 1x1
    const BORDER_COLOR = '#1f100b';

    // Función para dibujar una pieza con su etiqueta
    function drawPiece(x, y, widthCm, heightCm, color, text, isWhite = false) {
        const px = OFFSET_X + x * SCALE;
        const py = OFFSET_Y + y * SCALE;
        const pw = widthCm * SCALE;
        const ph = heightCm * SCALE;

        // Relleno de la pieza
        ctx.fillStyle = color;
        ctx.fillRect(px, py, pw, ph);

        // Borde
        ctx.strokeStyle = BORDER_COLOR;
        ctx.lineWidth = 2;
        ctx.strokeRect(px, py, pw, ph);

        // Texto grabado en la pieza
        ctx.fillStyle = isWhite ? '#3b2219' : '#f3e5ab';
        ctx.font = 'bold 13px Segoe UI, sans-serif';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText(text, px + pw / 2, py + ph / 2);
    }

    // Dibujar la tableta completa armada para el Nivel 1
    function drawChocolate() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // 1. Bloques Grandes (Chocolate Negro 5x5)
        drawPiece(0, 3, 5, 5, COLOR_NEGRO, '5x5');
        drawPiece(5, 3, 5, 5, COLOR_NEGRO, '5x5');

        // 2. Barras Delgadas Acostadas (Chocolate con Leche 5x1)
        drawPiece(0, 0, 5, 1, COLOR_LECHE, '5x1');
        drawPiece(0, 1, 5, 1, COLOR_LECHE, '5x1');
        drawPiece(0, 2, 5, 1, COLOR_LECHE, '5x1');

        drawPiece(5, 0, 5, 1, COLOR_LECHE, '5x1');
        drawPiece(5, 1, 5, 1, COLOR_LECHE, '5x1');
        drawPiece(5, 2, 5, 1, COLOR_LECHE, '5x1');

        // 3. Barra Delgada Parada (Chocolate con Leche 1x5)
        drawPiece(10, 3, 1, 5, COLOR_LECHE, '1x5');

        // 4. Pastillas Pequeñas (Chocolate Blanco 1x1)
        drawPiece(10, 0, 1, 1, COLOR_BLANCO, '1x1', true);
        drawPiece(10, 1, 1, 1, COLOR_BLANCO, '1x1', true);
        drawPiece(10, 2, 1, 1, COLOR_BLANCO, '1x1', true);
    }

    // Lógica para validar las respuestas del niño
    function comprobarRespuesta() {
        const userAncho = parseInt(document.getElementById('anchoInput').value);
        const userAlto = parseInt(document.getElementById('altoInput').value);
        const userTotal = parseInt(document.getElementById('totalInput').value);
        const feedback = document.getElementById('feedback');

        // Solución esperada: Ancho = 11, Alto = 8 (o invertidos) y Total = 88
        const esCorrectoLados = (userAncho === 11 && userAlto === 8) || (userAncho === 8 && userAlto === 11);
        const esCorrectoTotal = userTotal === 88;

        if (esCorrectoLados && esCorrectoTotal) {
            feedback.className = 'correct';
            feedback.innerHTML = '🎉 ¡Excelente! Sumaste correctamente los lados (11 cm × 8 cm) y hallaste el total de 88 cm².';
        } else if (!esCorrectoLados && esCorrectoTotal) {
            feedback.className = 'incorrect';
            feedback.innerHTML = '🤔 El total es correcto, pero revisa la suma de los lados en el Ancho y el Alto.';
        } else if (esCorrectoLados && !esCorrectoTotal) {
            feedback.className = 'incorrect';
            feedback.innerHTML = '💡 ¡Los lados están bien sumados! Revisa la multiplicación del Total (Ancho × Alto).';
        } else {
            feedback.className = 'incorrect';
            feedback.innerHTML = '❌ Vuelve a contar los bordes marcados en cada pieza. ¡Tú puedes!';
        }
    }

    // Inicializar el dibujo al cargar la página
    drawChocolate();
</script>

</body>
</html>
