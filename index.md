<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Random Dots</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            background-color: #f0f0f0;
        }
        .dot-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
        }
        .dot {
            position: absolute;
            border-radius: 50%;
            transform: translate(-50%, -50%);
        }
    </style>
</head>
<body>
    <div class="dot-container" id="dotContainer"></div>

    <script>
        const container = document.getElementById('dotContainer');
        const maxAge = 3 * 60 * 1000; // 3分钟毫秒数
        
        // 创建随机颜色
        function getRandomColor() {
            const letters = '0123456789ABCDEF';
            let color = '#';
            for (let i = 0; i < 6; i++) {
                color += letters[Math.floor(Math.random() * 16)];
            }
            return color;
        }
        
        // 创建新圆点
        function createDot() {
            const radius = Math.floor(Math.random() * 7) + 2; // 2-8像素
            const size = radius * 2 + 'px'; // 直径
            
            const dot = document.createElement('div');
            dot.className = 'dot';
            dot.style.width = size;
            dot.style.height = size;
            dot.style.backgroundColor = getRandomColor();
            dot.style.left = Math.random() * 100 + '%';
            dot.style.top = Math.random() * 100 + '%';
            
            // 记录创建时间
            dot.dataset.timestamp = Date.now();
            
            container.appendChild(dot);
            
            return dot;
        }
        
        // 检查并删除过期圆点
        function checkOldDots() {
            const now = Date.now();
            const dots = container.querySelectorAll('.dot');
            
            dots.forEach(dot => {
                const createdAt = parseInt(dot.dataset.timestamp);
                if (now - createdAt > maxAge) {
                    container.removeChild(dot);
                }
            });
        }
        
        // 每秒创建新圆点
        setInterval(() => {
            const newDot = createDot();
        }, 100);
        
        // 每秒检查并删除过期圆点
        setInterval(() => {
            checkOldDots();
        }, 100);
        
        // 初始化第一个圆点
        createDot();
    </script>
</body>
</html>
