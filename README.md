<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>光 · 涟漪 — 触碰的回响</title>

    <!-- ===== 字体 ===== -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,600;14..32,800&display=swap" rel="stylesheet" />

    <!-- ===== p5.js ===== -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.4/p5.min.js">
    </script>

    <style>
        /* ===== 全局重置 ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', 'Helvetica Neue', sans-serif;
            background: #000;
            color: #fff;
            overflow-x: hidden;
        }

        /* ===== 滚动条 ===== */
        ::-webkit-scrollbar {
            width: 2px;
        }
        ::-webkit-scrollbar-track {
            background: #000;
        }
        ::-webkit-scrollbar-thumb {
            background: #555;
        }

        /* ============================================================
                   页面一：演示页 (全屏 canvas)
                   ============================================================ */
        #demo-section {
            position: relative;
            width: 100vw;
            height: 100vh;
            overflow: hidden;
            background: #000;
            cursor: none;
        }

        #demo-section canvas {
            display: block;
            width: 100% !important;
            height: 100% !important;
        }

        /* ---- 左下角入口 ---- */
        .entry-link {
            position: absolute;
            bottom: 40px;
            left: 40px;
            z-index: 10;
            font-size: 13px;
            font-weight: 300;
            letter-spacing: 0.15em;
            text-transform: uppercase;
            color: rgba(255, 255, 255, 0.3);
            text-decoration: none;
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 10px 24px;
            transition: all 0.5s ease;
            cursor: pointer;
            background: rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(4px);
            user-select: none;
        }

        .entry-link:hover {
            color: #fff;
            border-color: rgba(255, 255, 255, 0.4);
            background: rgba(255, 255, 255, 0.05);
        }

        /* ---- 右下角提示 ---- */
        .hint {
            position: absolute;
            bottom: 40px;
            right: 40px;
            z-index: 10;
            font-size: 11px;
            font-weight: 300;
            letter-spacing: 0.08em;
            color: rgba(255, 255, 255, 0.15);
            text-align: right;
            line-height: 1.6;
            pointer-events: none;
            user-select: none;
        }

        /* ============================================================
                   页面二：说明页 (滚动内容)
                   ============================================================ */
        #about-section {
            background: #000;
            padding: 100px 5% 120px;
            max-width: 1200px;
            margin: 0 auto;
        }

        /* ---- 通用文字 ---- */
        .about-block {
            margin-bottom: 120px;
        }

        .about-block:last-child {
            margin-bottom: 0;
        }

        /* ---- 标题区 ---- */
        .title-block {
            margin-bottom: 80px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.06);
            padding-bottom: 60px;
        }

        .main-title {
            font-size: clamp(48px, 10vw, 110px);
            font-weight: 800;
            letter-spacing: -0.03em;
            line-height: 1;
            color: #fff;
            margin-bottom: 20px;
        }

        .main-title .light {
            font-weight: 300;
            color: rgba(255, 255, 255, 0.25);
        }

        .sub-title {
            font-size: clamp(14px, 1.4vw, 20px);
            font-weight: 300;
            letter-spacing: 0.2em;
            color: rgba(255, 255, 255, 0.3);
            text-transform: uppercase;
        }

        /* ---- 通用区块布局 ---- */
        .row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 60px;
            align-items: start;
        }

        .row.reverse {
            direction: rtl;
        }
        .row.reverse>* {
            direction: ltr;
        }

        .col-label {
            font-size: 11px;
            font-weight: 600;
            letter-spacing: 0.15em;
            text-transform: uppercase;
            color: rgba(255, 255, 255, 0.2);
            margin-bottom: 16px;
        }

        .col-heading {
            font-size: clamp(26px, 3vw, 42px);
            font-weight: 600;
            letter-spacing: -0.02em;
            line-height: 1.2;
            color: #fff;
            margin-bottom: 20px;
        }

        .col-text {
            font-size: clamp(14px, 1.1vw, 17px);
            font-weight: 300;
            line-height: 1.9;
            color: rgba(255, 255, 255, 0.55);
        }

        .col-text strong {
            color: rgba(255, 255, 255, 0.8);
            font-weight: 600;
        }

        /* ---- 视觉占位 ---- */
        .visual-placeholder {
            width: 100%;
            aspect-ratio: 4/3;
            background: #111;
            border: 1px solid rgba(255, 255, 255, 0.04);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 12px;
            letter-spacing: 0.1em;
            color: rgba(255, 255, 255, 0.1);
            text-transform: uppercase;
        }

        /* ---- 创作历程 (时间线) ---- */
        .timeline {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 40px 80px;
            margin-top: 10px;
        }

        .timeline-item {
            border-top: 1px solid rgba(255, 255, 255, 0.06);
            padding-top: 20px;
        }

        .timeline-item .step {
            font-size: 11px;
            font-weight: 600;
            letter-spacing: 0.1em;
            color: rgba(255, 255, 255, 0.15);
            text-transform: uppercase;
            margin-bottom: 8px;
        }

        .timeline-item .desc {
            font-size: 14px;
            font-weight: 300;
            line-height: 1.6;
            color: rgba(255, 255, 255, 0.5);
        }

        /* ---- 技术标签 ---- */
        .tag-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            margin-top: 6px;
        }

        .tag {
            font-size: 12px;
            font-weight: 300;
            letter-spacing: 0.05em;
            color: rgba(255, 255, 255, 0.4);
            border: 1px solid rgba(255, 255, 255, 0.06);
            padding: 6px 18px;
            background: rgba(255, 255, 255, 0.02);
        }

        /* ---- 页脚 ---- */
        .footer {
            border-top: 1px solid rgba(255, 255, 255, 0.04);
            padding-top: 40px;
            margin-top: 80px;
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            font-weight: 300;
            color: rgba(255, 255, 255, 0.15);
            letter-spacing: 0.05em;
        }

        /* ---- 返回入口 ---- */
        .back-link {
            position: fixed;
            top: 32px;
            left: 32px;
            z-index: 100;
            font-size: 11px;
            font-weight: 300;
            letter-spacing: 0.15em;
            text-transform: uppercase;
            color: rgba(255, 255, 255, 0.15);
            text-decoration: none;
            border: 1px solid rgba(255, 255, 255, 0.04);
            padding: 8px 20px;
            transition: all 0.4s ease;
            background: rgba(0, 0, 0, 0.6);
            backdrop-filter: blur(4px);
        }

        .back-link:hover {
            color: #fff;
            border-color: rgba(255, 255, 255, 0.2);
        }

        /* ===== 响应式 ===== */
        @media (max-width: 768px) {
            .row {
                grid-template-columns: 1fr;
                gap: 40px;
            }
            .row.reverse {
                direction: ltr;
            }
            .row.reverse>* {
                direction: ltr;
            }

            .timeline {
                grid-template-columns: 1fr;
                gap: 30px;
            }

            #about-section {
                padding: 60px 5% 80px;
            }

            .about-block {
                margin-bottom: 80px;
            }

            .entry-link {
                bottom: 24px;
                left: 24px;
                font-size: 11px;
                padding: 8px 16px;
            }
            .hint {
                display: none;
            }
            .back-link {
                top: 20px;
                left: 20px;
                font-size: 10px;
                padding: 6px 14px;
            }
        }

        @media (max-width: 480px) {
            .main-title {
                font-size: 36px;
            }
            .col-heading {
                font-size: 22px;
            }
        }
    </style>
</head>
<body>

    <!-- ============================================================
    页面一：演示页
    ============================================================ -->
    <section id="demo-section">
        <!-- p5.js 会渲染到这里 -->

        <!-- 左下：进入说明页 -->
        <a class="entry-link" id="enterAbout">↗ 关于项目</a>

        <!-- 右下：操作提示 -->
        <div class="hint">移动 · 探索<br />点击 · 涟漪</div>
    </section>

    <!-- ============================================================
    页面二：说明页
    ============================================================ -->
    <section id="about-section" style="display:none;">

        <!-- 返回按钮 -->
        <a class="back-link" id="backDemo">← 返回</a>

        <!-- ===== 标题区 ===== -->
        <div class="title-block">
            <h1 class="main-title">
                光 · 涟漪<br />
                <span class="light">触碰的回响</span>
            </h1>
            <p class="sub-title">光影互动装置 · 2026</p>
        </div>

        <!-- ===== 概念 ===== -->
        <div class="about-block">
            <div class="row">
                <div>
                    <div class="col-label">概念</div>
                    <h2 class="col-heading">在黑暗中，<br />光只为你而来。</h2>
                    <p class="col-text">
                        你走进一个黑暗的空间。头顶的光追随着你，照亮你周围的透明水管。
                        每一次触碰，水晃动，光影荡开，风铃轻响。
                        你看不清整个空间，只能一步步往前走，慢慢看见更多。
                    </p>
                    <p class="col-text" style="margin-top:16px;">
                        不预设故事，不解释情绪。
                        只是提供一个让观者沉浸其中、自我感知的 <strong>「光之丛林」</strong>。
                    </p>
                </div>
                <div>
                    <div class="visual-placeholder">▣ 概念影像占位</div>
                </div>
            </div>
        </div>

        <!-- ===== 创作历程 ===== -->
        <div class="about-block">
            <div class="col-label" style="margin-bottom:24px;">历程</div>
            <h2 class="col-heading" style="margin-bottom:40px;">从碎片到方案</h2>

            <div class="timeline">
                <div class="timeline-item">
                    <div class="step">01 · 初始</div>
                    <div class="desc">
                        黑白投影、泡泡、烟、光、丝线、欲望……<br />
                        从日常感知中收集了 <strong>六个零散片段</strong>。
                    </div>
                </div>
                <div class="timeline-item">
                    <div class="step">02 · 叙事尝试</div>
                    <div class="desc">
                        试图让线条承载「好故事/坏故事」的情绪变奏。<br />
                        发现 <strong>太复杂</strong>，反而削弱了直觉的力量。
                    </div>
                </div>
                <div class="timeline-item">
                    <div class="step">03 · 精简</div>
                    <div class="desc">
                        回归核心：<strong>触碰 = 涟漪</strong>。<br />
                        去掉叙事标签，只保留身体与光的直接对话。
                    </div>
                </div>
                <div class="timeline-item">
                    <div class="step">04 · 材质探索</div>
                    <div class="desc">
                        从细线 → 透明鱼线 → <strong>透明水管 + 水</strong>。<br />
                        加入风铃般的声音，让触觉、视觉、听觉合一。
                    </div>
                </div>
                <div class="timeline-item">
                    <div class="step">05 · 光的逻辑</div>
                    <div class="desc">
                        全黑空间中，<strong>光只跟随人</strong>。<br />
                        你走到哪，光就到哪。你看不清全貌，只能探索。
                    </div>
                </div>
                <div class="timeline-item">
                    <div class="step">06 · 当前</div>
                    <div class="desc">
                        沉淀为 <strong>「光 · 涟漪」</strong>。<br />
                        简约、直接、富有禅意。等待实体落地。
                    </div>
                </div>
            </div>
        </div>

        <!-- ===== 技术方向 ===== -->
        <div class="about-block">
            <div class="col-label" style="margin-bottom:24px;">技术</div>
            <h2 class="col-heading" style="margin-bottom:8px;">如何实现</h2>

            <div class="row" style="margin-top:24px;">
                <div>
                    <p class="col-text" style="margin-bottom:20px;">
                        装置的核心由以下技术支撑：
                    </p>
                    <div class="tag-grid">
                        <span class="tag">投影 Mapping</span>
                        <span class="tag">Kinect 人体追踪</span>
                        <span class="tag">透明水管 + 水</span>
                        <span class="tag">物理风铃发声</span>
                        <span class="tag">光的折射与散射</span>
                        <span class="tag">实时互动反馈</span>
                    </div>
                    <p class="col-text" style="margin-top:28px; font-size:13px; color:rgba(255,255,255,0.3);">
                        光跟随人移动，触碰引发水的晃动与光影涟漪，风铃随碰撞发声。
                        一切反馈都是 <strong>物理的、有机的、不可完全预测的</strong>。
                    </p>
                </div>
                <div>
                    <div class="visual-placeholder">▣ 技术示意占位</div>
                </div>
            </div>
        </div>

        <!-- ===== 页脚 ===== -->
        <div class="footer">
            <span>光 · 涟漪 / 触碰的回响</span>
            <span>毕业设计 · 2026</span>
        </div>

    </section>


    <!-- ============================================================
    p5.js 交互脚本
    ============================================================ -->
    <script>
        // ============================================================
        //  全局状态
        // ============================================================
        const STATE = {
            demoActive: true, // 当前是否显示演示页
            lines: [],
            ripples: [],
            mouseX: -9999,
            mouseY: -9999,
            mouseInside: false,
        };

        // ============================================================
        //  p5.js 主程序
        // ============================================================
        function setup() {
            const canvas = createCanvas(windowWidth, windowHeight);
            canvas.parent('demo-section');
            // 让 canvas 作为背景，不遮挡 HTML 元素
            canvas.style('pointer-events', 'none');
            // 但我们需要鼠标事件，所以用 window 监听
            pixelDensity(1);

            // 生成垂直线条
            const spacing = 28;
            const cols = Math.ceil(width / spacing) + 4;
            for (let i = 0; i < cols; i++) {
                const x = (i - 2) * spacing + (spacing / 2);
                STATE.lines.push({
                    x: x,
                    baseX: x,
                    phase: random(TWO_PI),
                    speed: 0.008 + random(0.006),
                    amp: 0,
                    targetAmp: 0,
                    height: height * (0.5 + random(0.4)),
                });
            }

            // 鼠标事件监听（用 window，因为 canvas 不阻挡）
            window.addEventListener('mousemove', (e) => {
                const rect = document.getElementById('demo-section').getBoundingClientRect();
                STATE.mouseX = e.clientX - rect.left;
                STATE.mouseY = e.clientY - rect.top;
                STATE.mouseInside = true;
            });

            window.addEventListener('mouseleave', () => {
                STATE.mouseInside = false;
            });

            window.addEventListener('click', (e) => {
                const rect = document.getElementById('demo-section').getBoundingClientRect();
                const mx = e.clientX - rect.left;
                const my = e.clientY - rect.top;
                // 在点击位置产生涟漪
                STATE.ripples.push({
                    x: mx,
                    y: my,
                    radius: 0,
                    maxRadius: 180,
                    life: 1.0,
                });
            });

            // 处理窗口缩放
            window.addEventListener('resize', () => {
                resizeCanvas(windowWidth, windowHeight);
                // 重新计算线条位置
                const spacing = 28;
                const cols = Math.ceil(width / spacing) + 4;
                STATE.lines = [];
                for (let i = 0; i < cols; i++) {
                    const x = (i - 2) * spacing + (spacing / 2);
                    STATE.lines.push({
                        x: x,
                        baseX: x,
                        phase: random(TWO_PI),
                        speed: 0.008 + random(0.006),
                        amp: 0,
                        targetAmp: 0,
                        height: height * (0.5 + random(0.4)),
                    });
                }
            });
        }

        function draw() {
            // ---- 清空 ----
            background(0);

            // ---- 更新线条晃动 ----
            const lightRadius = 160; // 光照半径
            const mouseX = STATE.mouseX;
            const mouseY = STATE.mouseY;
            const inside = STATE.mouseInside;

            for (let line of STATE.lines) {
                // 计算到鼠标的距离
                const dx = mouseX - line.x;
                const dy = mouseY - (height / 2);
                const dist = sqrt(dx * dx + dy * dy);

                // 是否在光照范围内
                const lit = inside && dist < lightRadius;

                // 目标振幅：光照范围内晃动，范围外静止
                const targetAmp = lit ? 12 * (1 - dist / lightRadius) : 0;
                line.targetAmp = targetAmp;

                // 平滑过渡
                line.amp += (line.targetAmp - line.amp) * 0.04;

                // 更新相位
                line.phase += line.speed;

                // 计算偏移
                const offset = sin(line.phase) * line.amp;
                const currentX = line.baseX + offset;

                // ---- 计算亮度 ----
                let brightness = 0;
                if (inside && dist < lightRadius) {
                    brightness = 1 - (dist / lightRadius);
                    brightness = pow(brightness, 0.7);
                }

                // ---- 绘制线条 ----
                const alpha = brightness * 0.9 + 0.05;
                const strokeW = 1.2 + brightness * 2.0;

                // 发光效果：多层绘制
                // 外层光晕
                if (brightness > 0.05) {
                    noStroke();
                    const glowAlpha = brightness * 0.06;
                    fill(255, 255, 255, glowAlpha);
                    const glowSize = 20 + brightness * 30;
                    ellipse(currentX, height / 2, glowSize, line.height * 0.9);
                }

                // 主线
                stroke(255, 255, 255, alpha * 200);
                strokeWeight(strokeW);
                const topY = height / 2 - line.height / 2;
                const bottomY = height / 2 + line.height / 2;
                line(currentX, topY, currentX, bottomY);

                // 细的二次线 （增加层次）
                if (brightness > 0.2) {
                    stroke(255, 255, 255, brightness * 0.08 * 100);
                    strokeWeight(0.5);
                    const offset2 = sin(line.phase * 1.3 + 1.2) * line.amp * 0.3;
                    line(currentX + offset2, topY + 10, currentX + offset2, bottomY - 10);
                }

                // ---- 线条上的光点（水珠感） ----
                if (brightness > 0.3) {
                    const dotCount = floor(3 + brightness * 6);
                    for (let i = 0; i < dotCount; i++) {
                        const t = (i + 0.5) / dotCount;
                        const yPos = topY + t * line.height;
                        const waveOffset = sin(line.phase * 1.7 + t * 6) * 3 * brightness;
                        const dotSize = 1.5 + brightness * 2.5;
                        const dotAlpha = (0.3 + 0.7 * (1 - abs(t - 0.5) * 2)) * brightness;
                        noStroke();
                        fill(255, 255, 255, dotAlpha * 0.6 * 200);
                        ellipse(currentX + waveOffset, yPos, dotSize, dotSize * 0.6);
                    }
                }
            }

            // ---- 绘制光照光圈 ----
            if (inside && mouseX > 0 && mouseY > 0) {
                // 主光圈
                noStroke();
                const grad = drawingContext.createRadialGradient(
                    mouseX, mouseY, 0,
                    mouseX, mouseY, lightRadius
                );
                grad.addColorStop(0, 'rgba(255,255,255,0.04)');
                grad.addColorStop(0.5, 'rgba(255,255,255,0.015)');
                grad.addColorStop(1, 'rgba(255,255,255,0)');
                drawingContext.fillStyle = grad;
                drawingContext.beginPath();
                drawingContext.arc(mouseX, mouseY, lightRadius, 0, TWO_PI);
                drawingContext.fill();

                // 内圈细线
                noFill();
                stroke(255, 255, 255, 12);
                strokeWeight(0.5);
                const pulse = 1 + sin(frameCount * 0.015) * 0.02;
                ellipse(mouseX, mouseY, lightRadius * 0.3 * pulse, lightRadius * 0.3 * pulse);

                // 十字准星（极淡）
                const crossSize = 6;
                stroke(255, 255, 255, 20);
                strokeWeight(0.5);
                line(mouseX - crossSize, mouseY, mouseX + crossSize, mouseY);
                line(mouseX, mouseY - crossSize, mouseX, mouseY + crossSize);
            }

            // ---- 绘制涟漪 ----
            for (let i = STATE.ripples.length - 1; i >= 0; i--) {
                const r = STATE.ripples[i];
                r.radius += 1.8;
                r.life -= 0.008;

                if (r.life <= 0 || r.radius > r.maxRadius) {
                    STATE.ripples.splice(i, 1);
                    continue;
                }

                const alpha = r.life * 0.3;
                noFill();
                stroke(255, 255, 255, alpha * 180);
                strokeWeight(0.6 + (1 - r.life) * 1.2);
                const waveCount = 2 + floor((1 - r.life) * 4);
                for (let w = 0; w < waveCount; w++) {
                    const offset = w * 6 * (1 - r.life * 0.5);
                    const rad = r.radius + offset;
                    const a = alpha * (1 - w / waveCount);
                    stroke(255, 255, 255, a * 180);
                    ellipse(r.x, r.y, rad * 2, rad * 2);
                }

                // 涟漪中心光点
                if (r.life > 0.5) {
                    noStroke();
                    const centerAlpha = (r.life - 0.5) * 2 * 0.2;
                    fill(255, 255, 255, centerAlpha * 200);
                    ellipse(r.x, r.y, 4, 4);
                }
            }

            // ---- 角落文字标识（极小） ----
            noStroke();
            fill(255, 255, 255, 8);
            textFont('Inter');
            textSize(9);
            textStyle(NORMAL);
            textAlign(LEFT, BOTTOM);
            text('光 · 涟漪', 20, height - 20);
        }

        // ============================================================
        //  页面切换逻辑
        // ============================================================
        const demoSection = document.getElementById('demo-section');
        const aboutSection = document.getElementById('about-section');
        const enterBtn = document.getElementById('enterAbout');
        const backBtn = document.getElementById('backDemo');

        function showDemo() {
            demoSection.style.display = 'block';
            aboutSection.style.display = 'none';
            document.body.style.overflow = 'hidden';
            STATE.demoActive = true;
            // 重新激活 p5 循环
            loop();
        }

        function showAbout() {
            demoSection.style.display = 'none';
            aboutSection.style.display = 'block';
            document.body.style.overflow = 'auto';
            STATE.demoActive = false;
            // 暂停 p5 循环以节省性能
            noLoop();
        }

        enterBtn.addEventListener('click', showAbout);
        backBtn.addEventListener('click', showDemo);

        // 键盘 ESC 返回
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape' && !STATE.demoActive) {
                showDemo();
            }
        });

        // 初始化：显示演示页
        showDemo();

        // 窗口变化时确保 canvas 尺寸正确
        function windowResized() {
            resizeCanvas(windowWidth, windowHeight);
        }

        // 小技巧：p5 的 mouseX/mouseY 可能不准，我们用自己的
        // 但为了让 p5 不报错，覆盖一下
        window.mouseX = 0;
        window.mouseY = 0;

        console.log('🌊 光 · 涟漪 — 触碰的回响');
        console.log('移动鼠标探索 · 点击产生涟漪');
    </script>

</body>
</html>
