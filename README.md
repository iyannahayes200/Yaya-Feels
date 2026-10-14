# Yaya-Feels
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Love Heart</title>
    <style>
        * {
            box-sizing: border-box;
        }
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #091710;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: "Arial", sans-serif;
            color: #ffffff;
        }

        /* Passcode Form Container styling */
        #lock-screen {
            position: absolute;
            z-index: 10;
            text-align: center;
            background: rgba(11, 22, 16, 0.9);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 0 25px rgba(255, 77, 141, 0.3);
            border: 2px solid #e8c5be;
            transition: opacity 0.5s ease;
        }
        #lock-screen h2 {
            margin-bottom: 20px;
            font-size: 22px;
            letter-spacing: 1px;
            color: #e8c5be;
        }
        #passcode-input {
            background-color: #091710;
            border: 2px solid #ff4d8d;
            border-radius: 8px;
            padding: 12px;
            color: #fff;
            font-size: 20px;
            text-align: center;
            width: 150px;
            letter-spacing: 4px;
            outline: none;
            margin-bottom: 15px;
        }
        #passcode-input:focus {
            border-color: #ff1a75;
            box-shadow: 0 0 10px rgba(255, 26, 117, 0.5);
        }
        #error-msg {
            color: #ff4d8d;
            font-size: 14px;
            height: 18px;
            margin-top: 5px;
        }

        /* Canvas Wrapper - Hidden until correct passcode */
        #canvas-container {
            display: none;
            position: relative;
            width: 800px;
            height: 800px;
            max-width: 100vw;
            max-height: 100vh;
        }
        canvas {
            width: 100%;
            height: 100%;
            background-color: #091710;
            display: block;
        }
    </style>
</head>
<body>

    <!-- Lock screen box overlay -->
    <div id="lock-screen">
        <h2>Enter Passcode</h2>
        <input type="password" id="passcode-input" maxlength="4" placeholder="••••" autocomplete="off">
        <div id="error-msg"></div>
    </div>

    <!-- Heart visual container -->
    <div id="canvas-container">
        <canvas id="heartCanvas" width="800" height="800"></canvas>
    </div>

    <!-- Romantic Background Instrumental Track -->
    <audio id="bg-music" loop>
        <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" type="audio/mpeg">
    </audio>

<script>
    const lockScreen = document.getElementById('lock-screen');
    const passInput = document.getElementById('passcode-input');
    const errorMsg = document.getElementById('error-msg');
    const container = document.getElementById('canvas-container');
    const canvas = document.getElementById('heartCanvas');
    const audio = document.getElementById('bg-music');
    const ctx = canvas.getContext('2d');

    // Automatically put cursor in input box
    passInput.focus();

    // Monitor password entry fields
    passInput.addEventListener('input', () => {
        if (passInput.value === '0820') {
            // Unlock and reveal the project smoothly
            lockScreen.style.opacity = '0';
            
            // Try to play audio immediately upon passcode entry interaction
            try {
                audio.play().catch(e => console.log("Audio play caught:", e));
            } catch(err) {
                console.log("Audio play failed:", err);
            }

            setTimeout(() => {
                lockScreen.style.display = 'none';
                container.style.display = 'block';
                // Trigger the engine setup logic and animation loops
                initHeartEngine();
            }, 500);
        } else if (passInput.value.length === 4) {
            errorMsg.innerText = "Incorrect passcode. Try again!";
            passInput.value = '';
            setTimeout(() => { errorMsg.innerText = ''; }, 2000);
        }
    });

    function initHeartEngine() {
        // Configuration Parameters
        const SC = 21;
        const OY = 20;
        const TICK = 16;
        const ANIM_FRAMES = 42;
        const LIFT = 34;
        const GAP = 7;
        const SIZES = [9, 10, 11, 12]; 
        const BG_COLOR = "#091710";
        const FLASH_COLOR = [255, 240, 245]; // Bright Neon White Pink (#fff0f5)

        // Base Bright Pink Colors for the heart text layout
        const brightPink1 = [255, 26, 117];  // #ff1a75
        const brightPink2 = [255, 77, 141];  // #ff4d8d
        const brightPink3 = [255, 105, 180]; // #ff69b4

        const PHRASES = [
            "Iyanna really", "really", "really", "really", "really", "really", "really", "really", 
            "really", "really", "really", "really", "really", "really", "really", "really", "really", 
            "really", "really", "really", "really", "really", "really", "really", "really", "really", 
            "really", "really", "really", "really", "really", "really", "really", "really", "really", 
            "really", "really", "really", "really", "really", "really", "really", "really", "really", 
            "really", "really", "really", "really", "really", "really", "really", "likes P"
        ];

        class SeededRandom {
            constructor(seed) { this.seed = seed; }
            next() {
                this.seed = (this.seed * 9301 + 49297) % 233280;
                return this.seed / 233280;
            }
            uniform(min, max) { return min + this.next() * (max - min); }
            choice(arr) { return arr[Math.floor(this.next() * arr.length)]; }
            shuffle(arr) {
                let copy = [...arr];
                for (let i = copy.length - 1; i > 0; i--) {
                    const j = Math.floor(this.next() * (i + 1));
                    [copy[i], copy[j]] = [copy[j], copy[i]];
                }
                return copy;
            }
        }
        const rng = new SeededRandom(11);

        function getHeartPoint(a) {
            const x = 16 * Math.pow(Math.sin(a), 3);
            const y = 13 * Math.cos(a) - 5 * Math.cos(2 * a) - 2 * Math.cos(3 * a) - Math.cos(4 * a);
            return { x: x * SC + 400, y: 400 - (y * SC + OY) }; 
        }

        const POLY = [];
        for (let i = 0; i < 720; i++) {
            POLY.push(getHeartPoint((2 * Math.PI * i) / 720));
        }
        
        const EDGES = [];
        for (let i = 0; i < POLY.length; i++) {
            EDGES.push([POLY[i], POLY[(i + 1) % POLY.length]]);
        }

        const yValues = POLY.map(p => p.y);
        const YMAX = Math.max(...yValues);
        const YMIN = Math.min(...yValues);
        const CY = (YMAX + YMIN) / 2;

        function getSpans(y) {
            let xs = [];
            for (let [p1, p2] of EDGES) {
                if ((p1.y <= y && y < p2.y) || (p2.y <= y && y < p1.y)) {
                    xs.push(p1.x + ((y - p1.y) * (p2.x - p1.x)) / (p2.y - p1.y));
                }
            }
            xs.sort((a, b) => a - b);
            let pairs = [];
            for (let i = 0; i < xs.length; i += 2) {
                if (xs[i+1] !== undefined) pairs.push([xs[i], xs[i+1]]);
            }
            return pairs;
        }

        function intersect(A, B) {
            let out = [];
            for (let a of A) {
                for (let b of B) {
                    let lo = Math.max(a[0], b[0]);
                    let hi = Math.min(a[1], b[1]);
                    if (hi - lo > 14) out.push([lo, hi]);
                }
            }
            return out;
        }

        let phraseQueue = [];
        function getNextPhrase() {
            if (phraseQueue.length === 0) {
                phraseQueue = rng.shuffle(PHRASES);
            }
            return phraseQueue.pop();
        }

        function mathDist(x, y) {
            const dx = (x - 400) / (16 * SC);
            const dy = (400 - y - OY) / (14.5 * SC);
            return Math.hypot(dx, dy);
        }

        function mixColors(c1, c2, k) {
            return c1.map((v, i) => v + (c2[i] - v) * k);
        }

        function colorToHex(c) {
            return "#" + c.map(v => Math.floor(Math.max(0, Math.min(255, v))).toString(16).padStart(2, '0')).join('');
        }

        let placed = [];
        const row_h = 16; 
        const half = row_h / 2;
        let currY = YMIN + half;

        ctx.font = "bold 12px Arial";

        while (currY < YMAX - half) {
            let row = intersect(getSpans(currY - half), getSpans(currY + half));
            for (let [lo, hi] of row) {
                lo += 4;
                hi -= 4;
                let cur = lo + rng.uniform(0, 8);
                let line = [];

                while (true) {
                    let fit = null;
                    for (let attempt = 0; attempt < 10; attempt++) {
                        let text = getNextPhrase();
                        let size = rng.choice(SIZES);
                        ctx.font = `bold ${size}px Arial`;
                        let w = ctx.measureText(text).width;
                        if (cur + w <= hi) {
                            fit = { text, size, w };
                            break;
                        }
                    }
                    if (!fit) break;
                    line.push({ x: cur + fit.w / 2, text: fit.text, size: fit.size, w: fit.w });
                    cur += fit.w + GAP;
                }
                if (line.length === 0) continue;
                if (line.length > 1) {
                    let left = hi - (line[line.length - 1].x + line[line.length - 1].w / 2);
                    for (let i = 0; i < line.length; i++) {
                        line[i].x += (left * i) / (line.length - 1);
                    }
                } else {
                    line[0].x = (lo + hi) / 2;
                }
                for (let i = 0; i < line.length; i++) {
                    let edge = (i === 0 || i === line.length - 1);
                    placed.push({ x: line[i].x, y: currY, text: line[i].text, size: line[i].size, edge: edge });
                }
            }
            currY += row_h;
        }

        placed.forEach(p => {
            let rad = Math.min(1.0, mathDist(p.x, p.y));
            // Mix across bright pink gradients exclusively to ensure the entire layout is vibrant bright pink
            let base = mixColors(brightPink1, brightPink2, 0.25 + 0.75 * rad);
            base = mixColors(base, brightPink3, rng.uniform(0, 0.35) + (p.edge ? 0.3 : 0));
            p.color = base;
            p.rad = rad;
            p.f = 0;
            p.dx = rng.uniform(-10, 10);
            p.isSpawned = false;
            p.isFinished = false;
            p.shimmerTime = 0;
        });

        let edges = placed.filter(p => p.edge);
        let inner = placed.filter(p => !p.edge);
        edges.sort((a, b) => (Math.atan2(a.x - 400, 400 - a.y - CY) % (2 * Math.PI)) - (Math.atan2(b.x - 400, 400 - b.y - CY) % (2 * Math.PI)));
        inner.sort((a, b) => (-a.rad + rng.uniform(0, 0.08)) - (-b.rad + rng.uniform(0, 0.08)));
        
        const sortedItems = [...edges, ...inner];

        let activeItems = [];
        let itemIndex = 0;
        const totalDurationMs = 10000;
        const ratePerFrame = sortedItems.length / (totalDurationMs / TICK);
        let acc = 0;
        let spawningActive = true;

        const bgRgb = [9, 23, 16]; // Matching #091710

        function mainLoop() {
            ctx.fillStyle = BG_COLOR;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            if (spawningActive) {
                acc += ratePerFrame;
                while (acc >= 1) {
                    acc -= 1;
                    if (itemIndex < sortedItems.length) {
                        let p = sortedItems[itemIndex++];
                        p.isSpawned = true;
                        activeItems.push(p);
                    } else {
                        spawningActive = false;
                        break;
                    }
                }
            }

            for (let i = activeItems.length - 1; i >= 0; i--) {
                let p = activeItems[i];
                p.f++;
                let u = Math.max(0, Math.min(1, p.f / ANIM_FRAMES));
                let e = 1 - Math.pow(1 - u, 3);
                let hover = (1 - e) * LIFT;
                let px = p.x + p.dx * (1 - e);
                let py = p.y + hover; 

                if (u >= 1) {
                    p.isFinished = true;
                    activeItems.splice(i, 1);
                    continue;
                }

                ctx.font = `bold ${p.size}px Arial`;
                ctx.fillStyle = "#000000";
                ctx.fillText(p.text, px + 2, py + 3);

                ctx.font = `bold ${p.size + 1}px Arial`;
                ctx.fillStyle = colorToHex(mixColors(bgRgb, mixColors(bgRgb, p.color, 0.20), e * e));
                ctx.fillText(p.text, px, py);

                ctx.font = `bold ${p.size}px Arial`;
                let flashVal = 0.75 * Math.sin(Math.PI * Math.max(0, Math.min(1, (u - 0.55) / 0.45)));
                let baseTextCol = mixColors(bgRgb, p.color, Math.pow(e, 1.5));
                let finalCol = mixColors(baseTextCol, FLASH_COLOR, flashVal);
                ctx.fillStyle = colorToHex(finalCol);
                ctx.fillText(p.text, px, py);
            }

            sortedItems.forEach(p => {
                if (p.isFinished) {
                    ctx.font = `bold ${p.size}px Arial`;
                    ctx.fillStyle = "#000000";
                    ctx.fillText(p.text, p.x + 2, p.y + 3);

                    let renderColor = p.color;
                    if (p.shimmerTime > 0) {
                        p.shimmerTime -= TICK;
                        renderColor = mixColors(p.color, FLASH_COLOR, 0.6);
                    }

                    ctx.fillStyle = colorToHex(renderColor);
                    ctx.fillText(p.text, p.x, p.y);
                }
            });

            if (!spawningActive && activeItems.length === 0) {
                if (Math.random() < 0.05) { 
                    let randomFinishedItem = sortedItems[Math.floor(Math.random() * sortedItems.length)];
                    if (randomFinishedItem.isFinished) {
                        randomFinishedItem.shimmerTime = 260; 
                    }
                }
            }

            setTimeout(mainLoop, TICK);
        }

        mainLoop();
    }
</script>

</body>
</html>
