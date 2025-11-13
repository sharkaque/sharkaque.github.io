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
        /* 卡片样式：加长到280px + 从小变大动画 */
        .card {
            width: 280px;
            padding: 0;
            margin: 8px;
            border-radius: 8px;
            position: absolute;
            opacity: 0;
            transform: scale(0.2); /* 初始缩小到20% */
            transition: 
                opacity 0.5s cubic-bezier(0.34, 1.56, 0.64, 1),
                transform 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
            box-shadow: 0 2px 6px rgba(0,0,0,0.08);
            overflow: hidden;
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
                <button class="btn" id="openBtn">确定</button>
            </div>
        </div>
    </div>

    <button class="add-btn">+</button>

    <script>
        // 等待DOM完全加载后再执行，避免元素未找到导致点击无效
        document.addEventListener('DOMContentLoaded', function() {
            // 20条指定暖心话语
            const messages = [
                "金榜题名",
                "你超棒的",
                "我好想你",
                "别太累啦，偶尔偷懒也好",
                "愿所有烦恼消失",
                "早点休息",
                "每天都要元气满满",
                "保持好心情",
                "要相信自己奥",
                "多喝水哦~",
                "记得好好护肤",
                "美梦成真",
                "你最好啦",
                "顺顺利利",
                "辛苦啦，今天也超级棒",
                "别焦虑，一切都会顺顺利利",
                "别给自己压力，尽力就好",
                "别emo了，你超值得被爱",
                "我爱你❤️",
                "愿所有美好如期而至"
            ];
            const cardColors = ["card-pink", "card-blue", "card-yellow", "card-green", "card-purple"];
            const cardWidth = 280;
            const cardHeight = 80;
            
            let usedIndexes = [];
            let zIndexCounter = 10; // 提高初始层级，避免被遮挡
            
            // 绑定确定按钮点击事件（确保元素已加载）
            document.getElementById("openBtn").onclick = function() {
                document.getElementById("modal").style.display = "none";
                document.querySelector(".add-btn").style.display = "block";
                usedIndexes = [];
                zIndexCounter = 10;
                // 100张卡片，100ms间隔
                for (let i = 0; i < 100; i++) {
                    setTimeout(() => {
                        createCard(i >= 80);
                    }, i * 100);
                }
            };

            function createCard(isNoRepeat) {
                const card = document.createElement("div");
                const randomColor = cardColors[Math.floor(Math.random() * cardColors.length)];
                card.className = `card ${randomColor}`;
                
                let randomText;
                if (isNoRepeat) {
                    let availableIndexes = messages.map((_, idx) => idx).filter(idx => !usedIndexes.includes(idx));
                    if (availableIndexes.length === 0) {
                        availableIndexes = messages.map((_, idx) => idx);
                        usedIndexes = [];
                    }
                    const randomIndex = availableIndexes[Math.floor(Math.random() * availableIndexes.length)];
                    randomText = messages[randomIndex];
                    usedIndexes.push(r
