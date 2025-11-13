<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>神秘礼物卡片</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        
        body {
            font-family: 'Segoe UI', 'Microsoft YaHei', sans-serif;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            padding: 20px;
            overflow-x: hidden;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        
        .container {
            max-width: 1200px;
            width: 100%;
            text-align: center;
        }
        
        .header {
            margin-bottom: 40px;
            padding: 20px;
        }
        
        h1 {
            color: #2c3e50;
            font-size: 2.8rem;
            margin-bottom: 15px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
        }
        
        .subtitle {
            color: #7f8c8d;
            font-size: 1.2rem;
            max-width: 600px;
            margin: 0 auto;
            line-height: 1.6;
        }
        
        .controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-bottom: 40px;
            flex-wrap: wrap;
        }
        
        .btn {
            padding: 14px 30px;
            border: none;
            border-radius: 50px;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }
        
        .btn-primary {
            background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            color: white;
        }
        
        .btn-secondary {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
        }
        
        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 7px 20px rgba(0,0,0,0.15);
        }
        
        .btn:active {
            transform: translateY(1px);
        }
        
        .cards-container {
            position: relative;
            min-height: 500px;
            width: 100%;
            margin-top: 30px;
        }
        
        .card {
            width: 280px;
            border-radius: 16px;
            position: absolute;
            opacity: 0;
            transform: scale(0.1) rotate(-5deg);
            transition: 
                opacity 0.8s cubic-bezier(0.34, 1.56, 0.64, 1),
                transform 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
            box-shadow: 0 10px 30px rgba(0,0,0,0.15);
            overflow: hidden;
            cursor: pointer;
        }
        
        .card:hover {
            transform: scale(1.05) !important;
            box-shadow: 0 15px 35px rgba(0,0,0,0.2);
            z-index: 100;
        }
        
        .label-area {
            padding: 12px 20px;
            border-bottom: 1px solid rgba(255,255,255,0.3);
        }
        
        .label {
            display: flex;
            align-items: center;
            font-size: 14px;
            color: #fff;
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.2);
        }
        
        .label::before {
            content: "💝";
            margin-right: 8px;
            font-size: 16px;
        }
        
        .content-area {
            padding: 20px;
            font-size: 18px;
            color: #2c3e50;
            line-height: 1.6;
            min-height: 80px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .card-pink { 
            background: linear-gradient(135deg, #ff9a9e 0%, #fad0c4 100%);
        }
        
        .card-blue { 
            background: linear-gradient(135deg, #a1c4fd 0%, #c2e9fb 100%);
        }
        
        .card-yellow { 
            background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);
        }
        
        .card-green { 
            background: linear-gradient(135deg, #84fab0 0%, #8fd3f4 100%);
        }
        
        .card-purple { 
            background: linear-gradient(135deg, #d4fc79 0%, #96e6a1 100%);
        }
        
        .instructions {
            margin-top: 40px;
            color: #7f8c8d;
            font-size: 1rem;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            line-height: 1.6;
            padding: 20px;
            background: rgba(255,255,255,0.7);
            border-radius: 12px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.05);
        }
        
        .counter {
            position: fixed;
            top: 20px;
            right: 20px;
            background: rgba(255,255,255,0.9);
            padding: 10px 20px;
            border-radius: 50px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            font-weight: bold;
            color: #2c3e50;
            z-index: 1000;
        }
        
        @media (max-width: 768px) {
            h1 {
                font-size: 2.2rem;
            }
            
            .controls {
                flex-direction: column;
                align-items: center;
            }
            
            .btn {
                width: 80%;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>神秘礼物卡片</h1>
            <p class="subtitle">点击下方按钮，生成带有温馨祝福的动画卡片。每张卡片都有独特的消息和漂亮的渐变效果。</p>
        </div>
        
        <div class="controls">
            <button class="btn btn-primary" id="generateBtn">生成卡片 (120ms间隔)</button>
            <button class="btn btn-secondary" id="clearBtn">清空卡片</button>
        </div>
        
        <div class="counter">已生成: <span id="cardCount">0</span> 张卡片</div>
        
        <div class="cards-container" id="cardsContainer"></div>
        
        <div class="instructions">
            <h3>使用说明</h3>
            <p>• 点击"生成卡片"按钮开始创建动画卡片</p>
            <p>• 卡片会以120ms的间隔生成，并带有从小变大的动画效果</p>
            <p>• 鼠标悬停在卡片上可以查看放大效果</p>
            <p>• 点击"清空卡片"按钮可以移除所有卡片</p>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const messages = [
                "金榜题名 🎓",
                "你超棒的！✨",
                "我好想你 💖",
                "别太累啦，偶尔偷懒也好 😴",
                "愿所有烦恼消失 🌈",
                "早点休息 🌙",
                "每天都要元气满满 ☀️",
                "保持好心情 😊",
                "要相信自己奥 💪",
                "多喝水哦~ 💧",
                "记得好好护肤 🧴",
                "美梦成真 🌠",
                "你最好啦 🌸",
                "顺顺利利 🍀",
                "辛苦啦，今天也超级棒 👏",
                "别焦虑，一切都会顺顺利利 🌟",
                "别给自己压力，尽力就好 🙏",
                "别emo了，你超值得被爱 💕",
                "我爱你 ❤️",
                "愿所有美好如期而至 🎉"
            ];
            
            const cardColors = ["card-pink", "card-blue", "card-yellow", "card-green", "card-purple"];
            const cardWidth = 280;
            const cardHeight = 120;
            let cardCount = 0;
            let animationInterval;
            
            const generateBtn = document.getElementById('generateBtn');
            const clearBtn = document.getElementById('clearBtn');
            const cardsContainer = document.getElementById('cardsContainer');
            const cardCountElement = document.getElementById('cardCount');
            
            generateBtn.addEventListener('click', startCardGeneration);
            clearBtn.addEventListener('click', clearAllCards);
            
            function startCardGeneration() {
                // 先清空现有卡片
                clearAllCards();
                
                // 重置计数器
                cardCount = 0;
                updateCounter();
                
                // 生成20张卡片，每120ms一张
                let cardsGenerated = 0;
                const totalCards = 20;
                
                animationInterval = setInterval(() => {
                    if (cardsGenerated < totalCards) {
                        createCard();
                        cardsGenerated++;
                    } else {
                        clearInterval(animationInterval);
                    }
                }, 120);
            }
            
            function createCard() {
                const card = document.createElement("div");
                
                // 随机颜色和消息
                const colorIdx = Math.floor(Math.random() * cardColors.length);
                const textIdx = Math.floor(Math.random() * messages.length);
                
                card.className = `card ${cardColors[colorIdx]}`;
                card.innerHTML = `
                    <div class="label-area">
                        <div class="label">温馨祝福</div>
                    </div>
                    <div class="content-area">${messages[textIdx]}</div>
                `;
                
                // 随机位置
                const maxX = window.innerWidth - cardWidth - 40;
                const maxY = window.innerHeight - cardHeight - 40;
                const randomX = Math.floor(Math.random() * maxX);
                const randomY = Math.floor(Math.random() * maxY);
                
                card.style.left = `${randomX}px`;
                card.style.top = `${randomY}px`;
                
                cardsContainer.appendChild(card);
                
                // 使用setTimeout触发动画
                setTimeout(() => {
                    card.style.opacity = "1";
                    card.style.transform = "scale(1) rotate(0deg)";
                }, 10);
                
                // 更新计数器
                cardCount++;
                updateCounter();
                
                // 添加点击事件 - 点击卡片可以移除
                card.addEventListener('click', function() {
                    this.style.transform = "scale(0.1) rotate(5deg)";
                    this.style.opacity = "0";
                    setTimeout(() => {
                        if (this.parentNode) {
                            this.parentNode.removeChild(this);
                            cardCount--;
                            updateCounter();
                        }
                    }, 800);
                });
            }
            
            function clearAllCards() {
                if (animationInterval) {
                    clearInterval(animationInterval);
                }
                
                const cards = document.querySelectorAll('.card');
                cards.forEach(card => {
                    card.style.transform = "scale(0.1) rotate(5deg)";
                    card.style.opacity = "0";
                });
                
                setTimeout(() => {
                    cardsContainer.innerHTML = '';
                    cardCount = 0;
                    updateCounter();
                }, 800);
            }
            
            function updateCounter() {
                cardCountElement.textContent = cardCount;
            }
        });
    </script>
</body>
</html>
