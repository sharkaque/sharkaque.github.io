<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>一份神秘礼物</title>
    <style>
        body { margin: 0; padding: 20px; background: #f0f2f5; min-height: 100vh; overflow-x: hidden; }
        /* 弹窗样式 */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.3);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 999;
        }
        .modal-box {
            background: #fff;
            border-radius: 8px;
            width: 85%;
            max-width: 400px;
            overflow: hidden;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }
        .modal-header {
            padding: 12px 16px;
            display: flex;
            align-items: center;
            gap: 8px;
            border-bottom: 1px solid #eee;
        }
        .devil-icon {
            font-size: 20px;
            color: #a82ee5;
            background: #f3e5ff;
            border-radius: 50%;
            padding: 2px 6px;
        }
        .modal-title {
            font-size: 18px;
            font-weight: bold;
            color: #333;
        }
        .modal-body {
            padding: 20px 16px;
            font-size: 16px;
            color: #333;
        }
        .modal-footer {
            padding: 12px 16px;
            text-align: right;
            border-top: 1px solid #eee;
        }
        .btn {
            padding: 8px 20px;
            background: #1677ff;
            color: #fff;
            border: none;
            border-radius: 4px;
            font-size: 14px;
            cursor: pointer;
        }
        /* 卡片样式：修复移动端动画问题 */
        .card {
            width: 280px;
            padding: 0;
            margin: 8px;
            border-radius: 8px;
            position: absolute;
            opacity: 0;
            transform: scale(0.2); /* 初始缩小到20% */
            transition: 
                opacity 1.2s cubic-bezier(0.34, 1.56, 0.64, 1),
                transform 1.2s cubic-bezier(0.34, 1.56, 0.64, 1);
            box-shadow: 0 2px 6px rgba(0,0,0,0.08);
            overflow: hidden;
            /* 添加GPU加速 */
            will-change: transform, opacity;
            /* 修复移动端渲染 */
            -webkit-transform: scale(0.2);
            -webkit-transition: opacity 1.2s cubic-bezier(0.34, 1.56, 0.64, 1), 
                              -webkit-transform 1.2s cubic-bezier(0.34, 1.56, 0.64, 1);
        }
        /* 提示区：适配加长卡片 */
        .card .label-area {
            background: var(--label-color);
            padding: 8px 20px;
            border-bottom: 1px solid #e0e0e0;
        }
        .card .label {
            display: flex;
            align-items: center;
            font-size: 12px;
            color: #ff4444;
            font-weight: bold;
        }
        .card .label::before {
            content: "💝";
            margin-right: 4px;
        }
        /* 内容区：适配加长卡片 */
        .card .content-area {
            background: var(--content-color);
            padding: 12px 20px;
            font-size: 15px;
            color: #333;
            line-height: 1.6;
        }
        /* 卡片颜色组 */
        .card-pink { 
            --label-color: #ffe0e0;
            --content-color: #fff1f0;
        }
        .card-blue { 
            --label-color: #d0efff;
            --content-color: #f0f9ff;
        }
        .card-yellow { 
            --label-color: #ffefd0;
            --content-color: #fff9f0;
        }
        .card-green { 
            --label-color: #e0ffd0;
            --content-color: #f7fff0;
        }
        .card-purple { 
            --label-color: #efd0ff;
            --content-color: #f9f0ff;
        }
        /* 添加按钮 */
        .add-btn {
            position: fixed;
            bottom: 30px;
            right: 30px;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background: #ff4444;
            color: #fff;
            border: none;
            font-size: 22px;
            cursor: pointer;
            box-shadow: 0 3px 10px rgba(255,68,68,0.2);
            z-index: 999;
            display: none;
        }
    </style>
</head>
<body>
    <div class="modal" id="modal">
        <div class="modal-box">
            <div class="modal-header">
                <span class="devil-icon">👾</span>
                <span class="modal-title">神秘礼物</span>
            </div>
            <div class="modal-body">
                这里有一份礼物，确定要打开吗？
            </div>
            <div class="modal-footer">
                <button class="btn" id="confirmBtn">确定</button>
            </div>
        </div>
    </div>

    <button class="add-btn">+</button>

    <script>
        // 全局挂载核心变量，避免作用域问题
        window.messages = [
            "金榜题名 🎓",
            "你超棒的 ✨",
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
        window.cardColors = ["card-pink", "card-blue", "card-yellow", "card-green", "card-purple"];
        window.cardWidth = 280;
        window.cardHeight = 80;
        window.usedIndexes = [];
        window.zIndexCounter = 10;

        // 使用事件监听器替代内联onclick
        document.addEventListener('DOMContentLoaded', function() {
            document.getElementById('confirmBtn').addEventListener('click', openGift);
        });

        // 全局函数，确保按钮能直接调用
        window.openGift = function() {
            // 先关闭弹窗（优先执行，视觉上立即反馈）
            document.getElementById("modal").style.display = "none";
            // 显示添加按钮
            document.querySelector(".add-btn").style.display = "block";
            // 重置变量
            window.usedIndexes = [];
            window.zIndexCounter = 10;
            // 100张卡片，150ms间隔，平稳生成
            for (let i = 0; i < 100; i++) {
                setTimeout(() => {
                    createCard(i >= 80);
                }, i * 150); // 150ms间隔
            }
        };

        // 修复移动端卡片创建函数
        function createCard(isNoRepeat) {
            const card = document.createElement("div");
            // 随机颜色
            const colorIdx = Math.floor(Math.random() * window.cardColors.length);
            card.className = `card ${window.cardColors[colorIdx]}`;
            
            // 随机文字（简化去重逻辑，避免阻塞）
            let textIdx;
            if (isNoRepeat) {
                const available = window.messages.map((_, idx) => idx).filter(idx => !window.usedIndexes.includes(idx));
                textIdx = available.length ? available[Math.floor(Math.random() * available.length)] : Math.floor(Math.random() * window.messages.length);
                window.usedIndexes.push(textIdx);
            } else {
                textIdx = Math.floor(Math.random() * window.messages.length);
            }
            const randomText = window.messages[textIdx];

            // 简化HTML结构，减少DOM解析负担
            card.innerHTML = `<div class="label-area"><div class="label">提示</div></div><div class="content-area">${randomText}</div>`;
            
            // 随机位置 - 优化移动端位置计算
            const maxX = Math.max(0, window.innerWidth - window.cardWidth - 20);
            const maxY = Math.max(0, window.innerHeight - window.cardHeight - 20);
            const randomX = Math.floor(Math.random() * maxX);
            const randomY = Math.floor(Math.random() * maxY);
            
            card.style.left = `${randomX}px`;
            card.style.top = `${randomY}px`;
            card.style.zIndex = window.zIndexCounter++;
            
            document.body.appendChild(card);
            
            // 修复移动端动画 - 强制重绘
            void card.offsetWidth; // 触发重绘
            
            // 使用双requestAnimationFrame确保动画触发
            requestAnimationFrame(() => {
                requestAnimationFrame(() => {
                    card.style.opacity = "1";
                    card.style.transform = "scale(1)";
                    card.style.webkitTransform = "scale(1)"; // iOS Safari
                });
            });
            
            // 添加点击事件
            card.addEventListener('click', function() {
                this.style.transform = "scale(1.05)";
                this.style.webkitTransform = "scale(1.05)";
                setTimeout(() => {
                    this.style.transform = "scale(1)";
                    this.style.webkitTransform = "scale(1)";
                }, 150);
            });
        }
        
        // 防止移动端双击缩放
        document.addEventListener('touchstart', function() {}, {passive: true});
    </script>
</body>
</html>
