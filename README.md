<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Колесо фортуны</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f5f5f5;
        }
        
        .wheel-container {
            position: relative;
            width: 350px;
            height: 350px;
            margin-bottom: 20px;
        }
        
        .wheel {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background: conic-gradient(
                #FF9AA2 0% 16.66%,
                #FFB7B2 16.66% 33.33%,
                #FFDAC1 33.33% 50%,
                #E2F0CB 50% 66.66%,
                #B5EAD7 66.66% 83.33%,
                #C7CEEA 83.33% 100%
            );
            position: relative;
            transition: transform 4s cubic-bezier(0.17, 0.67, 0.12, 0.99);
            transform: rotate(0deg);
            box-shadow: 0 0 10px rgba(0,0,0,0.2);
        }
        
        .wheel-center {
            position: absolute;
            width: 80px;
            height: 80px;
            background: white;
            border-radius: 50%;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            border: 5px solid #333;
            z-index: 2;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 24px;
        }
        
        .pointer {
            position: absolute;
            top: 50%;
            right: -15px;
            transform: translateY(-50%) rotate(180deg);
            width: 0;
            height: 0;
            border-top: 20px solid transparent;
            border-bottom: 20px solid transparent;
            border-left: 40px solid #ff5252;
            z-index: 0;
            filter: drop-shadow(0 0 5px rgba(0,0,0,0.3));
        }
        
        .wheel-item {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            transform-origin: center;
            font-size: 13px;
            font-weight: bold;
            color: #333;
            text-align: center;
            display: flex;
            align-items: center;
            justify-content: center;
            padding-left: 0px;
        }
        
        .wheel-item div {
            transform: rotate(calc(-1 * var(--item-rotate) + 0deg)) translateX(110px);
            white-space: nowrap;
        }
        
        .title {
            font-size: 28px;
            margin-bottom: 30px;
            font-weight: bold;
            color: #333;
            text-transform: uppercase;
        }
        
        .spin-btn {
            padding: 15px 30px;
            background-color: #ff5252;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 18px;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            font-weight: bold;
            letter-spacing: 2px;
            margin-top: 20px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        }
        
        .spin-btn:hover {
            background-color: #ff3232;
            transform: translateY(-2px);
            box-shadow: 0 6px 12px rgba(0,0,0,0.3);
        }
        
        .spin-btn:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }
        
        /* Стили для модального окна */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.7);
            z-index: 100;
            justify-content: center;
            align-items: center;
        }
        
        .modal-content {
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            text-align: center;
            max-width: 300px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        
        .modal-title {
            font-size: 24px;
            margin-bottom: 20px;
            color: #ff5252;
        }
        
        .modal-prize {
            font-size: 20px;
            margin-bottom: 25px;
            font-weight: bold;
        }
        
        .claim-btn {
            padding: 12px 25px;
            background-color: #ff5252;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .claim-btn:hover {
            background-color: #ff3232;
            transform: translateY(-2px);
        }
    </style>
</head>
<body>
    <div class="title">Колесо фортуны</div>
    
    <div class="wheel-container">
        <div class="wheel" id="wheel">
            <div class="wheel-center">GO</div>
            
            <div class="wheel-item" style="--item-rotate: 0deg;">
                <div>Подарок 1</div>
            </div>
            <div class="wheel-item" style="--item-rotate: 60deg;">
                <div>Подарок 2</div>
            </div>
            <div class="wheel-item" style="--item-rotate: 120deg;">
                <div>Подарок 3</div>
            </div>
            <div class="wheel-item" style="--item-rotate: 180deg;">
                <div>Подарок 4</div>
            </div>
            <div class="wheel-item" style="--item-rotate: 240deg;">
                <div>Подарок 5</div>
            </div>
            <div class="wheel-item" style="--item-rotate: 300deg;">
                <div>Подарок 6</div>
            </div>
        </div>
        <div class="pointer"></div>
    </div>
    
    <button class="spin-btn" id="spinBtn">Крутить колесо</button>
    
    <!-- Модальное окно для приза -->
    <div class="modal" id="prizeModal">
        <div class="modal-content">
            <div class="modal-title">Поздравляем!</div>
            <div class="modal-prize" id="prizeText">Ваш приз: Подарок 1</div>
            <button class="claim-btn" id="claimBtn">Забрать приз</button>
        </div>
    </div>
    
    <script>
        const wheel = document.getElementById('wheel');
        const spinBtn = document.getElementById('spinBtn');
        const prizeModal = document.getElementById('prizeModal');
        const prizeText = document.getElementById('prizeText');
        const claimBtn = document.getElementById('claimBtn');
        
        let isSpinning = false;
        let hasSpun = false;
        
        // Вероятности для каждого приза
        const prizeProbabilities = [13.75, 40, 13.75, 13.75, 13.75, 5];
        const prizes = [
            "Подарок 1",
            "Подарок 2",
            "Подарок 3",
            "Подарок 4",
            "Подарок 5",
            "Подарок 6"
        ];
        
        function getWeightedRandomPrize() {
            const random = Math.random() * 100;
            let cumulative = 0;
            
            for (let i = 0; i < prizeProbabilities.length; i++) {
                cumulative += prizeProbabilities[i];
                if (random <= cumulative) return i;
            }
            
            return 1; // fallback
        }
        
        spinBtn.addEventListener('click', () => {
            if (isSpinning || hasSpun) return;
            
            isSpinning = true;
            spinBtn.disabled = true;
            spinBtn.textContent = "Крутится...";
            
            // Выбираем приз
            const prizeIndex = getWeightedRandomPrize();
            
            // Угол сегмента (60° для 6 сегментов)
            const segmentAngle = 60;
            
            // Фиксированный сдвиг 14° + небольшой случайный (для натуральности)
            const edgeOffset =  (Math.random() * 4 - 14); // 14° ±2°
            const targetStopAngle = prizeIndex * segmentAngle + edgeOffset;
            
            // Полные обороты (3-5) + точный угол остановки
            const fullRotations = 3 + Math.floor(Math.random() * 3);
            const spinDegrees = fullRotations * 360 + (360 - targetStopAngle);
            
            wheel.style.transform = `rotate(${-spinDegrees}deg)`;
            
            setTimeout(() => {
                isSpinning = false;
                hasSpun = true;
                spinBtn.textContent = "Уже крутили";
                
                // Показываем модальное окно с призом
                prizeText.textContent = `Ваш приз: ${prizes[prizeIndex]}`;
                prizeModal.style.display = 'flex';
            }, 4000);
        });
        
        // Обработчик для кнопки "Забрать приз"
        claimBtn.addEventListener('click', () => {
            prizeModal.style.display = 'none';
        });
    </script>
</body>
</html>
