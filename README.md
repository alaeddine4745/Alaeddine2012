<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JS Chess Simulation</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: Arial, sans-serif;
      background-color: #1e1e2e;
      color: #ffffff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
    }
    h1 { margin-bottom: 10px; font-size: 1.8rem; color: #f5e0dc; }
    #status { margin-bottom: 15px; font-size: 1.1rem; color: #a6adc8; }
    #board {
      display: grid;
      grid-template-columns: repeat(8, 1fr);
      grid-template-rows: repeat(8, 1fr);
      width: 90vw;
      max-width: 440px;
      height: 90vw;
      max-height: 440px;
      border: 4px solid #45475a;
      box-shadow: 0 10px 30px rgba(0,0,0,0.5);
    }
    .square {
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2rem;
      cursor: pointer;
      user-select: none;
    }
    .white-square { background-color: #b8c0e0; color: #11111b; }
    .black-square { background-color: #45475a; color: #11111b; }
    .selected { background-color: #a6da95 !important; }
    .valid-move { position: relative; }
    .valid-move::after {
      content: '';
      width: 16px;
      height: 16px;
      background-color: rgba(166, 218, 149, 0.7);
      border-radius: 50%;
      position: absolute;
    }
  </style>
</head>
<body>

  <h1>Chess Simulation</h1>
  <div id="status">Turn: White</div>
  <div id="board"></div>

  <script>
    const boardElement = document.getElementById('board');
    const statusElement = document.getElementById('status');

    // Unicode Chess Symbols
    const PIECES = {
      'r': '♜', 'n': '♞', 'b': '♝', 'q': '♛', 'k': '♚', 'p': '♟',
      'R': '♖', 'N': '♘', 'B': '♗', 'Q': '♕', 'K': '♔', 'P': '♙'
    };

    // Initial Board State
    let boardState = [
      ['r','n','b','q','k','b','n','r'],
      ['p','p','p','p','p','p','p','p'],
      ['','','','','','','',''],
      ['','','','','','','',''],
      ['','','','','','','',''],
      ['','','','','','','',''],
      ['P','P','P','P','P','P','P','P'],
      ['R','N','B','Q','K','B','N','R']
    ];

    let selectedSquare = null;
    let currentTurn = 'white'; // 'white' (Uppercase) or 'black' (Lowercase)

    function isWhitePiece(piece) { return piece && piece === piece.toUpperCase(); }
    function isBlackPiece(piece) { return piece && piece === piece.toLowerCase(); }

    function renderBoard() {
      boardElement.innerHTML = '';
      for (let r = 0; r < 8; r++) {
        for (let c = 0; c < 8; c++) {
          const square = document.createElement('div');
          square.classList.add('square');
          square.classList.add((r + c) % 2 === 0 ? 'white-square' : 'black-square');
          square.dataset.row = r;
          square.dataset.col = c;

          const piece = boardState[r][c];
          if (piece) square.textContent = PIECES[piece];

          if (selectedSquare && selectedSquare.r === r && selectedSquare.c === c) {
            square.classList.add('selected');
          }

          square.addEventListener('click', () => handleSquareClick(r, c));
          boardElement.appendChild(square);
        }
      }
    }

    function handleSquareClick(r, c) {
      const piece = boardState[r][c];

      if (selectedSquare) {
        // Move attempt
        if (selectedSquare.r === r && selectedSquare.c === c) {
          selectedSquare = null; // Deselect
        } else {
          executeMove(selectedSquare.r, selectedSquare.c, r, c);
          selectedSquare = null;
        }
      } else {
        // Selection attempt
        if (piece) {
          if ((currentTurn === 'white' && isWhitePiece(piece)) ||
              (currentTurn === 'black' && isBlackPiece(piece))) {
            selectedSquare = { r, c };
          }
        }
      }
      renderBoard();
    }

    function executeMove(fromR, fromC, toR, toC) {
      const movingPiece = boardState[fromR][fromC];
      const targetPiece = boardState[toR][toC];

      // Prevent capturing own pieces
      if (targetPiece) {
        if (isWhitePiece(movingPiece) && isWhitePiece(targetPiece)) return;
        if (isBlackPiece(movingPiece) && isBlackPiece(targetPiece)) return;
      }

      // Execute move
      boardState[toR][toC] = movingPiece;
      boardState[fromR][fromC] = '';

      // Switch turn
      currentTurn = currentTurn === 'white' ? 'black' : 'white';
      statusElement.textContent = `Turn: ${currentTurn.charAt(0).toUpperCase() + currentTurn.slice(1)}`;
    }

    renderBoard();
  </script>
</body>
</html>
