const symbols = ['🍒', '🍋', '🍇', '🔔', '🍉']; // Символы
const values = { '🍒': 5, '🍋': 10, '🍇': 15, '🔔': 20, '🍉': 25 }; // Значения символов
const reels = document.querySelectorAll('.reel');
const spinButton = document.getElementById('spin');
const winDisplay = document.getElementById('win-display');
const balanceDisplay = document.getElementById('balance');
const betAmountSelect = document.getElementById('bet-amount');

let balance = 1000; // Переменная баланса

// Заполнение барабанов
function populateReels() {
  reels.forEach(reel => {
    reel.innerHTML = '';
    for (let i = 0; i < 5; i++) {  // Каждому барабану дается по 5 символов
      const symbol = document.createElement('div');
      symbol.classList.add('symbol');
      symbol.textContent = symbols[Math.floor(Math.random() * symbols.length)];
      reel.appendChild(symbol);
    }
  });
}

// Анимация барабанов с улучшенной плавностью
function spinReels() {
  const betAmount = parseInt(betAmountSelect.value);
  if (balance < betAmount) {
    alert('Недостаточно средств!');
    return;
  }

  balance -= betAmount;
  balanceDisplay.textContent = balance;

  reels.forEach((reel, index) => {
    setTimeout(() => {
      reel.style.transition = 'transform 2s ease-out';
      reel.style.transform = `translateY(-${Math.random() * 500 + 1000}px)`;

      setTimeout(() => {
        reel.style.transition = 'none';
        reel.style.transform = 'translateY(0)';
        populateReels(); // Замена символов после анимации
      }, 2000);
    }, index * 500); // Задержка для каждого барабана
  });

  setTimeout(() => calculateWin(betAmount), 3000); // Проверить выигрыш после завершения
}

// Генерация выигрыша на основе логики с возможными совпадениями
function calculateWin(betAmount) {
  let winAmount = 0;

  // Получаем все символы на барабанах
  const reelSymbols = Array.from(reels).map(reel => Array.from(reel.children).map(child => child.textContent));

  // Проверка на выигрыш по горизонтальным линиям
  for (let i = 0; i < 5; i++) {
    const lineSymbols = reelSymbols[i]; // Горизонтальная линия
    const firstSymbol = lineSymbols[0]; // Первый символ на линии
    let isWin = true;

    // Проверяем, что все символы в линии одинаковы и идут подряд с левого края
    for (let j = 1; j < lineSymbols.length; j++) {
      if (lineSymbols[j] !== firstSymbol) {
        isWin = false;
        break;
      }
    }

    if (isWin) {
      winAmount += values[firstSymbol] * betAmount; // Умножаем на ставку
    }
  }

  // Проверка на выигрыш по вертикальным линиям
  for (let i = 0; i < 5; i++) {
    const columnSymbols = reelSymbols.map(reel => reel[i]); // Вертикальная линия
    const firstSymbol = columnSymbols[0]; // Первый символ на колонке
    let isWin = true;

    // Проверяем, что все символы в колонке одинаковы и идут подряд с верхней позиции
    for (let j = 1; j < columnSymbols.length; j++) {
      if (columnSymbols[j] !== firstSymbol) {
        isWin = false;
        break;
      }
    }

    if (isWin) {
      winAmount += values[firstSymbol] * betAmount; // Умножаем на ставку
    }
  }

  // Проверка на выигрыш по диагоналям (слева направо и справа налево)
  const diagonal1 = [reelSymbols[0][0], reelSymbols[1][1], reelSymbols[2][2], reelSymbols[3][3], reelSymbols[4][4]]; // Слева направо
  const diagonal2 = [reelSymbols[0][4], reelSymbols[1][3], reelSymbols[2][2], reelSymbols[3][1], reelSymbols[4][0]]; // Справа налево

  const diagonal1Symbol = diagonal1[0];
  let isDiagonal1Win = true;
  for (let i = 1; i < diagonal1.length; i++) {
    if (diagonal1[i] !== diagonal1Symbol) {
      isDiagonal1Win = false;
      break;
    }
  }

  const diagonal2Symbol = diagonal2[0];
  let isDiagonal2Win = true;
  for (let i = 1; i < diagonal2.length; i++) {
    if (diagonal2[i] !== diagonal2Symbol) {
      isDiagonal2Win = false;
      break;
    }
  }

  // Если есть выигрыш на диагоналях, добавляем его
  if (isDiagonal1Win) {
    winAmount += values[diagonal1Symbol] * betAmount;
  }

  if (isDiagonal2Win) {
    winAmount += values[diagonal2Symbol] * betAmount;
  }

  // Обновляем отображение выигрыша
  winDisplay.textContent = `Выигрыш: ${winAmount}`;

  // Добавляем выигрыш на баланс
  balance += winAmount;
  balanceDisplay.textContent = balance;
}

// Инициализация
populateReels();
spinButton.addEventListener('click', spinReels);


body {
  font-family: Arial, sans-serif;
  background-color: #000;
  color: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  margin: 0;
}

.slot-machine {
  width: 80%;
  max-width: 800px;
  background-color: #222;
  border: 2px solid #fff;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}

#reels {
  display: flex;
  justify-content: space-between;
  overflow: hidden;
  height: 300px;
  margin-bottom: 20px;
}

.reel {
  width: 18%;
  background-color: #444;
  border: 1px solid #fff;
  border-radius: 5px;
  position: relative;
  overflow: hidden;
}

.reel .symbol {
  width: 100%;
  height: 75px;
  text-align: center;
  font-size: 24px;
  line-height: 75px;
  font-weight: bold;
  background-color: #ffcc00;
  border-bottom: 1px solid #000;
  color: #000;
}

button {
  margin: 10px 0;
  padding: 10px 30px;
  font-size: 18px;
  font-weight: bold;
  background: #ff6600;
  border: none;
  border-radius: 5px;
  color: #fff;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #cc5200;
}

#win-display {
  font-size: 20px;
  font-weight: bold;
  margin: 10px 0;
  color: #fff200;
}

#controls {
  display: flex;
  justify-content: space-around;
  align-items: center;
  margin-top: 20px;
}

#bet-amount {
  padding: 5px;
  font-size: 16px;
}


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>20 Super Hot Slot</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="slot-machine">
    <h1>20 Super Hot Slot</h1>
    <div id="reels">
      <div class="reel" id="reel1"></div>
      <div class="reel" id="reel2"></div>
      <div class="reel" id="reel3"></div>
      <div class="reel" id="reel4"></div>
      <div class="reel" id="reel5"></div>
    </div>
    <p id="win-display">Выигрыш: 0</p>
    <button id="spin">SPIN</button>
    <div id="controls">
      <label for="bet-amount">Ставка:</label>
      <select id="bet-amount">
        <option value="10">10</option>
        <option value="50">50</option>
        <option value="100">100</option>
        <option value="500">500</option>
        <option value="1000">1000</option>
        <option value="2000">2000</option>
        <option value="5000">5000</option>
      </select>
      <p>Баланс: <span id="balance">1000</span></p>
    </div>
  </div>
  <script src="script.js"></script>
</body>
</html>
