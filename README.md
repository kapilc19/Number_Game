<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Number Click Game</title>
    <style>
        body {
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            background-color: #f0f0f0;
            overflow: hidden;
            touch-action: none; /* Prevent touch gestures like scrolling */
        }
        .number {
            position: absolute;
            font-size: 30px; /* Increased font size */
            cursor: pointer;
            user-select: none; /* Prevent text selection */
        }
        #counter, #timer, #reset {
            position: fixed;
            font-size: 16px;
            padding: 10px;
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            border-radius: 5px;
            z-index: 1000; /* Ensure these elements stay on top */
            user-select: none; /* Prevent text selection */
        }
        #counter {
            bottom: 10px;
            left: 10px;
        }
        #timer {
            bottom: 10px;
            right: 10px;
        }
        #reset {
            top: 10px;
            left: 10px;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div id="counter">Count: 0</div>
<div id="timer">Time: 30s</div>
<div id="reset">Reset Game</div>

<script>
    let currentNumber = 1;
    let count = 0;
    let timerStarted = false;
    let timeLeft = 30;
    let timerInterval;
    const placedPositions = [];
    const safeZones = [
        { x: 10, y: window.innerHeight - 50, width: 100, height: 40 }, // Counter
        { x: window.innerWidth - 100, y: window.innerHeight - 50, width: 100, height: 40 }, // Timer
        { x: 10, y: 10, width: 100, height: 40 } // Reset Button
    ];

    function isOverlapping(x, y, elementWidth, elementHeight) {
        for (let pos of placedPositions) {
            if (!(x + elementWidth < pos.x || x > pos.x + pos.width ||
                  y + elementHeight < pos.y || y > pos.y + pos.height)) {
                return true;
            }
        }
        for (let zone of safeZones) {
            if (!(x + elementWidth < zone.x || x > zone.x + zone.width ||
                  y + elementHeight < zone.y || y > zone.y + zone.height)) {
                return true;
            }
        }
        return false;
    }

    function createNumberElement(number) {
        const span = document.createElement('span');
        span.textContent = number;
        span.className = 'number';
        const elementWidth = 40; // Increased width for better spacing
        const elementHeight = 40; // Increased height for better spacing

        let overlap;
        let x, y;

        do {
            x = Math.random() * (window.innerWidth - elementWidth * 2); // Avoid edges
            y = Math.random() * (window.innerHeight - elementHeight * 2);
            overlap = isOverlapping(x, y, elementWidth, elementHeight);
        } while (overlap);

        placedPositions.push({ x: x, y: y, width: elementWidth, height: elementHeight });

        span.style.left = `${x}px`;
        span.style.top = `${y}px`;

        const handleClickOrTouch = () => {
            if (number === currentNumber) {
                span.remove();
                currentNumber++;
                count++;
                document.getElementById('counter').textContent = `Count: ${count}`;

                if (!timerStarted) {
                    startTimer();
                    timerStarted = true;
                }

                if (currentNumber > 100) {
                    clearInterval(timerInterval);
                }
            }
        };

        // Support both click and touch events
        span.onclick = handleClickOrTouch;
        span.ontouchstart = handleClickOrTouch;

        document.body.appendChild(span);
    }

    function startTimer() {
        timerInterval = setInterval(() => {
            timeLeft--;
            document.getElementById('timer').textContent = `Time: ${timeLeft}s`;
            if (timeLeft <= 0) {
                clearInterval(timerInterval);
                document.querySelectorAll('.number').forEach(num => num.style.pointerEvents = 'none');
            }
        }, 1000);
    }

    function resetGame() {
        clearInterval(timerInterval);
        currentNumber = 1;
        count = 0;
        timeLeft = 30;
        timerStarted = false;
        document.getElementById('counter').textContent = `Count: 0`;
        document.getElementById('timer').textContent = `Time: 30s`;
        document.querySelectorAll('.number').forEach(num => num.remove());
        placedPositions.length = 0; // Clear positions

        for (let i = 1; i <= 100; i++) {
            createNumberElement(i);
        }
    }

    document.getElementById('reset').onclick = resetGame;

    // Initial setup
    for (let i = 1; i <= 100; i++) {
        createNumberElement(i);
    }

    // Adjust the grid and elements if the window is resized
    window.addEventListener('resize', resetGame);
</script>

</body>
</html>
