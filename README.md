<!DOCTYPE html>
<html>
<head>
    <title>Tetris</title>
    <style>
        #tetris {
            display: flex;
            flex-wrap: wrap;
            width: 300px;
        }
        .cell {
            width: 30px;
            height: 30px;
            border: 1px solid #ccc;
        }
    </style>
</head>
<body>
    <div id="tetris"></div>

    <script>
        const width = 10;
        const height = 20;
        const tetris = document.getElementById('tetris');
        const cells = [];
        let currentPosition = 4;
        let currentRotation = 0;
        let currentShape = shapes[0];

        // Tetris shapes
        const shapes = [
            [
                [1, 1, 1, 1],
                [0, 0, 0, 0],
                [0, 0, 0, 0],
                [0, 0, 0, 0]
            ],
            [
                [1, 1],
                [1, 1]
            ],
            // Add more shapes here...
        ];

        // Create the grid
        for (let row = 0; row < height; row++) {
            for (let col = 0; col < width; col++) {
                const cell = document.createElement('div');
                cell.classList.add('cell');
                tetris.appendChild(cell);
                cells.push(cell);
            }
        }

        // Draw the tetromino
        function draw() {
            currentShape[currentRotation].forEach((row, rowIndex) => {
                row.forEach((value, colIndex) => {
                    if (value === 1) {
                        const position = currentPosition + rowIndex * width + colIndex;
                        cells[position].classList.add('tetromino');
                    }
                });
            });
        }

        // Undraw the tetromino
        function undraw() {
            currentShape[currentRotation].forEach((row, rowIndex) => {
                row.forEach((value, colIndex) => {
                    if (value === 1) {
                        const position = currentPosition + rowIndex * width + colIndex;
                        cells[position].classList.remove('tetromino');
                    }
                });
            });
        }

        // Move down the tetromino
        function moveDown() {
            undraw();
            currentPosition += width;
            draw();
            freeze();
        }

        // Freeze the tetromino if it reaches the bottom or touches another tetromino
        function freeze() {
            if (currentShape[currentRotation].some((row, rowIndex) => {
                return row.some((value, colIndex) => {
                    const position = currentPosition + rowIndex * width + colIndex;
                    return value === 1 && (position >= width * height || cells[position + width].classList.contains('taken'));
                });
            })) {
                currentShape[currentRotation].forEach((row, rowIndex) => {
                    row.forEach((value, colIndex) => {
                        if (value === 1) {
                            const position = currentPosition + rowIndex * width + colIndex;
                            cells[position].classList.add('taken');
                        }
                    });
                });
                // Check for complete lines
                checkForLineClear();
                // Spawn a new tetromino
                spawnNewTetromino();
            }
        }

        // Check for complete lines and clear them
        function checkForLineClear() {
            for (let row = height - 1; row >= 0; row--) {
                if (cells.slice(row * width, (row + 1) * width).every(cell => cell.classList.contains('taken'))) {
                    cells.slice(row * width, (row + 1) * width).forEach(cell => {
                        cell.classList.remove('taken');
                    });
                    tetris.removeChild(tetris.childNodes[row]);
                    tetris.insertBefore(tetris.firstChild, tetris.childNodes[0]);
                }
            }
        }

        // Rotate the tetromino
        function rotate() {
            undraw();
            currentRotation = (currentRotation + 1) % currentShape.length;
            draw();
        }

        // Move the tetromino left
        function moveLeft() {
            undraw();
            const isAtLeftEdge = currentShape[currentRotation].some((row, rowIndex) => {
                return row.some((value, colIndex) => {
                    const position = currentPosition + rowIndex * width + colIndex;
                    return value === 1 && (position % width === 0 || cells[position - 1].classList.contains('taken'));
                });
            });
            if (!isAtLeftEdge) {
                currentPosition--;
            }
            draw();
        }

        // Move the tetromino right
        function moveRight() {
            undraw();
            const isAtRightEdge = currentShape[currentRotation].some((row, rowIndex) => {
                return row.some((value, colIndex) => {
                    const position = currentPosition + rowIndex * width + colIndex;
                    return value === 1 && (position % width === width - 1 || cells[position + 1].classList.contains('taken'));
                });
            });
            if (!isAtRightEdge) {
                currentPosition++;
            }
            draw();
        }

        // Spawn a new tetromino
        function spawnNewTetromino() {
            currentShape = shapes[Math.floor(Math.random() * shapes.length)];
            currentPosition = 4;
            currentRotation = 0;
            draw();
        }

        // Handle keyboard controls
        document.addEventListener('keydown', event => {
            if (event.code === 'ArrowUp') {
                rotate();
            } else if (event.code === 'ArrowDown') {
                moveDown();
            } else if (event.code === 'ArrowLeft') {
                moveLeft();
            } else if (event.code === 'ArrowRight') {
                moveRight();
            }
        });

        // Start the game
        spawnNewTetromino();
    </script>
</body>
</html>
