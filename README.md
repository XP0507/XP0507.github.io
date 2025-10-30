<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>温馨提醒网页版</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Microsoft YaHei', sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            overflow: hidden;
            touch-action: manipulation;
        }

        .batch-window {
            width: 80vw;
            max-width: 300px;
            min-height: 100px;
            border-radius: 15px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
            display: flex;
            justify-content: center;
            align-items: center;
            text-align: center;
            font-size: 18px;
            font-weight: bold;
            color: #333;
            position: absolute;
            transition: transform 0.3s;
            padding: 15px;
            animation: fadeIn 0.5s ease-out;
            line-height: 1.4;
            word-break: break-word;
            overflow: hidden;
        }

        .batch-window:active {
            transform: scale(0.98);
        }

        .instructions {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(255, 255, 255, 0.9);
            padding: 12px 20px;
            border-radius: 25px;
            font-size: 14px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            z-index: 1000;
            text-align: center;
            width: 90%;
            max-width: 300px;
        }

        .close-btn {
            position: absolute;
            top: 8px;
            right: 12px;
            font-size: 20px;
            cursor: pointer;
            color: #666;
            width: 24px;
            height: 24px;
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.7);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: scale(0.8); }
            to { opacity: 1; transform: scale(1); }
        }

        .start-btn {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 18px 35px;
            font-size: 20px;
            background-color: skyblue;
            border: none;
            border-radius: 15px;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s;
            z-index: 1000;
            min-width: 200px;
        }

        .start-btn:active {
            transform: translate(-50%, -50%) scale(0.95);
        }

        .stop-btn {
            position: fixed;
            top: 20px;
            right: 20px;
            padding: 12px 20px;
            font-size: 16px;
            background-color: rgba(255, 100, 100, 0.9);
            color: white;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
            z-index: 1001;
            display: none;
        }

        .stop-btn:active {
            transform: scale(0.95);
        }

        /* 移动端优化 */
        @media (max-width: 768px) {
            .batch-window {
                width: 85vw;
                font-size: 16px;
                min-height: 90px;
                padding: 12px;
            }

            .start-btn {
                padding: 16px 30px;
                font-size: 18px;
                min-width: 180px;
            }

            .instructions {
                font-size: 13px;
                padding: 10px 15px;
            }

            .stop-btn {
                padding: 10px 16px;
                font-size: 14px;
                top: 15px;
                right: 15px;
            }
        }

        /* 防止文本选择 */
        .no-select {
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }
    </style>
</head>
<body class="no-select">
    <!-- 开始按钮 -->
    <button id="startBtn" class="start-btn">开始温馨提醒</button>

    <!-- 停止按钮 -->
    <button id="stopBtn" class="stop-btn">停止</button>

    <!-- 批量窗口容器 -->
    <div id="batchContainer"></div>

    <!-- 使用说明 -->
    <div class="instructions">点击"停止"按钮或按ESC键停止弹窗</div>

    <script>
        // 丰富温馨提醒内容
        const tips = [
            '多喝水哦~', '保持微笑', '每天都要开开心心',
            '记得吃水果', '保持好心情', '好好爱自己', '我想你了',
            '梦想成真', '期待下一次见面', '会很轻松',
            '顺畅相伴', '早点休息', '愿所有烦恼都消失',
            '别气馁', '今天过得开心吗', '天冷了, 多穿衣服', '爱你',
            '你是最棒的', '一切都会好起来的', '相信自己',
            '今天也要加油哦', '不要太累', '记得按时吃饭',
            '你笑起来真好看', '世界因你而美好', '坚持就是胜利',
            '放松一下', '深呼吸', '你已经做得很好了',
            '保持乐观', '感恩每一天', '小小的幸福也很重要',
            '别忘了休息', '照顾好自己', '你值得被爱',
            '阳光总在风雨后', '每一天都是新的开始', '向前看',
            '不要放弃', '你是独一无二的', '生活很美好',
            '保持好奇心', '学习新东西', '和朋友聊聊天',
            '散散步吧', '听听音乐', '读一本好书',
            '给自己一个小奖励', '做自己喜欢的事', '保持健康',
            '珍惜当下', '未来可期', '你是重要的',
            '让心情放个假', '享受宁静时刻', '感恩遇见你',
            '小小的进步也是进步', '不要和别人比较', '做真实的自己',
            '原谅自己的不完美', '给自己一点时间', '一切都会过去的',
            '保持内心的平静', '相信美好', '温柔对待自己',
            '你并不孤单', '有人在乎你', '世界需要你',
            '每一天都值得珍惜', '微笑是最好的语言', '善良是一种力量',
            '坚持你的梦想', '勇敢面对挑战', '成长需要时间',
            '错误是学习的机会', '你有无限可能', '做自己的光',
            '保持希望', '爱与被爱都很幸福', '简单生活也很好',
            '感谢努力的自己', '你比想象中更坚强', '今天也是美好的一天'
        ];

        // 背景颜色
        const bgColors = [
            'lightpink', 'skyblue', 'lightgreen', 'lavender',
            'lightyellow', 'plum', 'coral', 'bisque', 'aquamarine',
            'lightcyan', 'peachpuff', 'mistyrose', 'palegoldenrod',
            'paleturquoise', 'thistle', 'wheat', 'powderblue'
        ];

        // 存储所有弹窗的数组
        let windows = [];
        let intervalId = null;
        let isRunning = false;

        // 开始批量弹窗
        function startBatchTips() {
            // 隐藏开始按钮，显示停止按钮
            document.getElementById('startBtn').style.display = 'none';
            document.getElementById('stopBtn').style.display = 'block';
            isRunning = true;

            // 创建弹窗 - 持续创建直到停止
            intervalId = setInterval(() => {
                createBatchWindow();
            }, 400); // 每400毫秒创建一个新窗口
        }

        // 创建单个批量弹窗
        function createBatchWindow() {
            const container = document.getElementById('batchContainer');
            const windowElement = document.createElement('div');
            windowElement.className = 'batch-window';

            // 随机选择提示和背景颜色
            const tip = tips[Math.floor(Math.random() * tips.length)];
            const bgColor = bgColors[Math.floor(Math.random() * bgColors.length)];

            // 设置窗口内容和样式
            windowElement.innerHTML = `<span>${tip}</span><div class="close-btn" onclick="closeWindow(this.parentElement)">×</div>`;
            windowElement.style.backgroundColor = bgColor;

            // 随机位置 - 确保分散在屏幕各处
            const maxX = window.innerWidth - windowElement.offsetWidth;
            const maxY = window.innerHeight - windowElement.offsetHeight;
            const x = Math.floor(Math.random() * maxX);
            const y = Math.floor(Math.random() * maxY);

            windowElement.style.left = `${x}px`;
            windowElement.style.top = `${y}px`;

            // 添加到容器和数组
            container.appendChild(windowElement);
            windows.push(windowElement);

            // 限制最大弹窗数量，防止内存泄漏
            if (windows.length > 50) {
                const oldestWindow = windows.shift();
                if (oldestWindow && oldestWindow.parentNode) {
                    oldestWindow.parentNode.removeChild(oldestWindow);
                }
            }
        }

        // 关闭单个窗口
        function closeWindow(element) {
            if (element && element.parentNode) {
                element.parentNode.removeChild(element);
            }
            // 从数组中移除
            const index = windows.indexOf(element);
            if (index > -1) {
                windows.splice(index, 1);
            }
        }

        // 停止所有弹窗
        function stopAllWindows() {
            // 清除创建弹窗的定时器
            if (intervalId) {
                clearInterval(intervalId);
                intervalId = null;
            }

            // 移除所有弹窗
            windows.forEach(window => {
                if (window && window.parentNode) {
                    window.parentNode.removeChild(window);
                }
            });
            windows = [];

            // 显示开始按钮，隐藏停止按钮
            document.getElementById('startBtn').style.display = 'block';
            document.getElementById('stopBtn').style.display = 'none';
            isRunning = false;
        }

        // ESC键停止功能
        document.addEventListener('keydown', function(event) {
            if (event.key === 'Escape') {
                stopAllWindows();
            }
        });

        // 点击开始按钮
        document.getElementById('startBtn').addEventListener('click', startBatchTips);

        // 点击停止按钮
        document.getElementById('stopBtn').addEventListener('click', stopAllWindows);

        // 防止移动端双击缩放
        let lastTouchEnd = 0;
        document.addEventListener('touchend', function (event) {
            const now = (new Date()).getTime();
            if (now - lastTouchEnd <= 300) {
                event.preventDefault();
            }
            lastTouchEnd = now;
        }, false);

        // 防止移动端下拉刷新
        document.addEventListener('touchmove', function (event) {
            if (event.scale !== 1) {
                event.preventDefault();
            }
        }, false);
    </script>
</body>
</html>
