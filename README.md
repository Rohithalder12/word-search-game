<!DOCTYPE html>
<html>
<head>
    <title>বাংলা ওয়ার্ড সার্চ গেম</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        #puzzle {
            margin: 20px auto;
            border: 2px solid #333;
        }
        #words {
            column-count: 3;
            margin: 20px auto;
            max-width: 600px;
        }
        .found {
            text-decoration: line-through;
            color: #4CAF50;
        }
    </style>
</head>
<body>
    <h1>বাংলা ওয়ার্ড সার্চ গেম</h1>
    
    <div id="puzzle"></div>
    
    <h2>খুঁজে বের করুন:</h2>
    <div id="words"></div>
    
    <script>
        // বাংলা ওয়ার্ড লিস্ট
        const words = ["গোলাপ", "জাহাজ", "মেঘনা", "সূর্য", "বই", "কলম", "বাংলা", "নদী"];
        const gridSize = 10;
        let selected = [];
        let puzzle = [];
        
        // পাজল বোর্ড ইনিশিয়ালাইজ
        function initPuzzle() {
            puzzle = Array(gridSize).fill().map(() => Array(gridSize).fill(''));
            
            // র্যান্ডম অক্ষর দিয়ে ফিল করুন
            const banglaLetters = 'অআইঈউঊঋএঐওঔকখগঘঙচছজঝঞটঠডঢণতথদধনপফবভমযরলশষসহ';
            for (let i = 0; i < gridSize; i++) {
                for (let j = 0; j < gridSize; j++) {
                    if (!puzzle[i][j]) {
                        puzzle[i][j] = banglaLetters.charAt(Math.floor(Math.random() * banglaLetters.length));
                    }
                }
            }
            
            // ওয়ার্ডস প্লেস করুন
            words.forEach(word => {
                placeWord(word);
            });
            
            renderPuzzle();
            renderWordList();
        }
        
        // ওয়ার্ড প্লেস করার ফাংশন
        function placeWord(word) {
            let placed = false;
            while (!placed) {
                const direction = Math.floor(Math.random() * 3); // 0: horizontal, 1: vertical, 2: diagonal
                const row = Math.floor(Math.random() * (gridSize - word.length));
                const col = Math.floor(Math.random() * (gridSize - word.length));
                
                if (canPlaceWord(word, direction, row, col)) {
                    for (let i = 0; i < word.length; i++) {
                        if (direction === 0) {
                            puzzle[row][col + i] = word.charAt(i);
                        } else if (direction === 1) {
                            puzzle[row + i][col] = word.charAt(i);
                        } else {
                            puzzle[row + i][col + i] = word.charAt(i);
                        }
                    }
                    placed = true;
                }
            }
        }
        
        // ওয়ার্ড প্লেস করা সম্ভব কিনা চেক
        function canPlaceWord(word, direction, row, col) {
            for (let i = 0; i < word.length; i++) {
                let cell;
                if (direction === 0) {
                    cell = puzzle[row][col + i];
                } else if (direction === 1) {
                    cell = puzzle[row + i][col];
                } else {
                    cell = puzzle[row + i][col + i];
                }
                
                if (cell && cell !== word.charAt(i)) {
                    return false;
                }
            }
            return true;
        }
        
        // পাজল রেন্ডার
        function renderPuzzle() {
            const puzzleDiv = document.getElementById('puzzle');
            puzzleDiv.innerHTML = '';
            
            const table = document.createElement('table');
            table.style.borderCollapse = 'collapse';
            table.style.margin = '0 auto';
            
            for (let i = 0; i < gridSize; i++) {
                const tr = document.createElement('tr');
                for (let j = 0; j < gridSize; j++) {
                    const td = document.createElement('td');
                    td.textContent = puzzle[i][j];
                    td.style.width = '30px';
                    td.style.height = '30px';
                    td.style.border = '1px solid #ddd';
                    td.style.textAlign = 'center';
                    td.style.cursor = 'pointer';
                    td.dataset.row = i;
                    td.dataset.col = j;
                    
                    td.addEventListener('mousedown', startSelection);
                    td.addEventListener('mouseover', continueSelection);
                    td.addEventListener('mouseup', endSelection);
                    
                    tr.appendChild(td);
                }
                table.appendChild(tr);
            }
            
            puzzleDiv.appendChild(table);
        }
        
        // ওয়ার্ড লিস্ট রেন্ডার
        function renderWordList() {
            const wordsDiv = document.getElementById('words');
            wordsDiv.innerHTML = '';
            
            words.forEach(word => {
                const wordDiv = document.createElement('div');
                wordDiv.textContent = word;
                wordDiv.id = 'word-' + word;
                wordsDiv.appendChild(wordDiv);
            });
        }
        
        // সিলেকশন ফাংশনালিটি
        function startSelection(e) {
            selected = [{
                row: parseInt(e.target.dataset.row),
                col: parseInt(e.target.dataset.col)
            }];
            e.target.style.backgroundColor = '#4CAF50';
        }
        
        function continueSelection(e) {
            if (selected.length > 0 && e.buttons === 1) {
                const row = parseInt(e.target.dataset.row);
                const col = parseInt(e.target.dataset.col);
                
                if (!selected.some(cell => cell.row === row && cell.col === col)) {
                    selected.push({ row, col });
                    e.target.style.backgroundColor = '#4CAF50';
                }
            }
        }
        
        function endSelection() {
            if (selected.length < 2) {
                clearSelection();
                return;
            }
            
            const selectedWord = getSelectedWord();
            if (words.includes(selectedWord)) {
                document.getElementById('word-' + selectedWord).classList.add('found');
                alert('আপনি পেয়েছেন: ' + selectedWord);
            }
            
            clearSelection();
        }
        
        function getSelectedWord() {
            // সিলেক্ট করা অক্ষরগুলো থেকে ওয়ার্ড বানানো
            let word = '';
            selected.sort((a, b) => a.row - b.row || a.col - b.col);
            
            selected.forEach(cell => {
                word += puzzle[cell.row][cell.col];
            });
            
            return word;
        }
        
        function clearSelection() {
            document.querySelectorAll('#puzzle td').forEach(td => {
                td.style.backgroundColor = '';
            });
            selected = [];
        }
        
        // ইনিশিয়ালাইজেশন
        window.onload = initPuzzle;
    </script>
</body>
</html
