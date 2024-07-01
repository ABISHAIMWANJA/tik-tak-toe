# tik-tak-toe
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tic Tac Toe</title>
    <style>
        * {
            box-sizing: border-box;
        }

        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: #f0f0f0;
            font-family: 'Arial', sans-serif;
            margin: 0;
        }

        .container {
            text-align: center;
        }

        h1 {
            margin-bottom: 20px;
            color: #333;
        }

        #game-board {
            display: grid;
            grid-template-columns: repeat(3, 100px);
            grid-template-rows: repeat(3, 100px);
            gap: 10px;
        }

        .cell {
            width: 100px;
            height: 100px;
            background: #fff;
            border: 2px solid #000;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2rem;
            cursor: pointer;
            transition: background 0.3s;
            position: relative;
        }

        .cell.x::before,
        .cell.x::after {
            content: '';
            position: absolute;
            width: 70%;
            height: 10px;
            background-color: #ff6347; /* Tomato red for X */
        }

        .cell.x::before {
            transform: rotate(45deg);
        }

        .cell.x::after {
            transform: rotate(-45deg);
        }

        .cell.circle::before {
            content: '';
            position: absolute;
            width: 70%;
            height: 70%;
            border: 10px solid #4682b4; /* Steel blue for O */
            border-radius: 50%;
        }

        .cell:hover {
            background: #f0f0f0;
        }

        .winning-message {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: #fff;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            text-align: center;
        }

        .winning-message button {
            margin-top: 10px;
            padding: 10px 20px;
            font-size: 1rem;
            cursor: pointer;
            background: #333;
            color: #fff;
            border: none;
            border-radius: 5px;
            transition: background 0.3s;
        }

        .winning-message button:hover {
            background: #555;
        }

        .show {
            display: block;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Tic Tac Toe</h1>
        <div id="game-board">
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
        </div>
        <div id="winning-message" class="winning-message">
            <div data-winning-message-text></div>
            <button id="restartButton">Restart</button>
        </div>
    </div>
    <script>
        const X_CLASS = 'x';
        const CIRCLE_CLASS = 'circle';
        const WINNING_COMBINATIONS = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8],
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8],
            [0, 4, 8],
            [2, 4, 6]
        ];
        const cellElements = document.querySelectorAll('[data-cell]');
        const board = document.getElementById('game-board');
        const winningMessageElement = document.getElementById('winning-message');
        const winningMessageTextElement = document.querySelector('[data-winning-message-text]');
        const restartButton = document.getElementById('restartButton');
        let circleTurn;

        startGame();

        restartButton.addEventListener('click', startGame);

        function startGame() {
            circleTurn = false;
            cellElements.forEach(cell => {
                cell.classList.remove(X_CLASS);
                cell.classList.remove(CIRCLE_CLASS);
                cell.removeEventListener('click', handleClick);
                cell.addEventListener('click', handleClick, { once: true });
            });
            setBoardHoverClass();
            winningMessageElement.classList.remove('show');
        }

        function handleClick(e) {
            const cell = e.target;
            const currentClass = X_CLASS;
            placeMark(cell, currentClass);
            if (checkWin(currentClass)) {
                endGame(false);
            } else if (isDraw()) {
                endGame(true);
            } else {
                swapTurns();
                computerMove();
            }
        }

        function computerMove() {
            const availableCells = [...cellElements].filter(cell => !cell.classList.contains(X_CLASS) && !cell.classList.contains(CIRCLE_CLASS));
            if (availableCells.length > 0) {
                const randomCell = availableCells[Math.floor(Math.random() * availableCells.length)];
                placeMark(randomCell, CIRCLE_CLASS);
                if (checkWin(CIRCLE_CLASS)) {
                    endGame(false);
                } else if (isDraw()) {
                    endGame(true);
                } else {
                    swapTurns();
                }
            }
        }

        function endGame(draw) {
            if (draw) {
                winningMessageTextElement.innerText = 'Draw!';
            } else {
                winningMessageTextElement.innerText = `${circleTurn ? "O's" : "X's"} Wins!`;
            }
            winningMessageElement.classList.add('show');
        }

        function isDraw() {
            return [...cellElements].every(cell => {
                return cell.classList.contains(X_CLASS) || cell.classList.contains(CIRCLE_CLASS);
            });
        }

        function placeMark(cell, currentClass) {
            cell.classList.add(currentClass);
        }

        function swapTurns() {
            circleTurn = !circleTurn;
            setBoardHoverClass();
        }

        function setBoardHoverClass() {
            board.classList.remove(X_CLASS);
            board.classList.remove(CIRCLE_CLASS);
            if (circleTurn) {
                board.classList.add(CIRCLE_CLASS);
            } else {
                board.classList.add(X_CLASS);
            }
        }

        function checkWin(currentClass) {
            return WINNING_COMBINATIONS.some(combination => {
                return combination.every(index => {
                    return cellElements[index].classList.contains(currentClass);
                });
            });
        }
    </script>
</body>
</html>
