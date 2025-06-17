<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Progression Keys</title>
    <!-- three.js CDN for the live wallpaper -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&family=Poppins:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
    <style>
        /* General Reset and Body Styles */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', sans-serif;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            color: #e8eaed; /* General text color for visibility */
            overflow-y: auto; /* Changed from hidden to auto to allow page scrolling */
            position: relative;
        }

        /* Canvas for live wallpaper */
        #backgroundCanvas {
            position: fixed; /* Keep it fixed to cover the entire viewport */
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: -2; /* Ensure it's behind everything */
            display: block;
        }

        /* Custom Scrollbar for Dark Theme */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(30, 30, 45, 0.7);
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #6a6ab0;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #8a8ac0;
        }

        /* Main Container Styles */
        .container {
            background: rgba(15, 15, 35, 0.85);
            backdrop-filter: blur(20px);
            border-radius: 24px;
            padding: 3rem;
            box-shadow:
                0 25px 50px rgba(0, 0, 0, 0.5),
                0 0 0 1px rgba(255, 255, 255, 0.1),
                inset 0 1px 0 rgba(255, 255, 255, 0.1);
            max-width: 1000px;
            width: 95%;
            animation: containerSlide 1s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            z-index: 1; /* Ensure container is above the canvas */
            /* Added min-height to ensure it doesn't collapse, but allows content to push it */
            min-height: 80vh; /* Allow it to take up a good portion of screen, but not be rigid */
            display: flex;
            flex-direction: column; /* Allow flex children for proper spacing */
            justify-content: space-between; /* Distribute space */
        }

        @keyframes containerSlide {
            from {
                opacity: 0;
                transform: translateY(60px) scale(0.9);
            }
            to {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
        }

        /* Header and Title Styles */
        .header {
            text-align: center;
            margin-bottom: 3rem;
        }

        .title {
            font-family: 'Poppins', sans-serif;
            font-size: 4rem;
            font-weight: 900;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 25%, #f093fb 50%, #f5576c 75%, #4facfe 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 0.5rem;
            animation: titleGlow 3s ease-in-out infinite alternate;
            letter-spacing: -2px;
            line-height: 1.1;
        }

        @keyframes titleGlow {
            from {
                filter: drop-shadow(0 0 20px rgba(102, 126, 234, 0.4));
            }
            to {
                filter: drop-shadow(0 0 40px rgba(245, 87, 108, 0.6));
            }
        }

        .subtitle {
            font-family: 'Inter', sans-serif;
            font-size: 1.2rem;
            color: #a0a3bd;
            font-weight: 400;
            letter-spacing: 0.5px;
        }

        /* Level Info Section */
        .level-info {
            display: grid;
            grid-template-columns: 1fr auto 1fr;
            align-items: center;
            margin-bottom: 2rem;
            padding: 1.5rem;
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            animation: levelInfoFade 0.6s ease-out;
        }

        @keyframes levelInfoFade {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .level-badge {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 0.8rem 1.5rem;
            border-radius: 50px;
            font-weight: 700;
            font-size: 1.1rem;
            color: white;
            text-transform: uppercase;
            letter-spacing: 1px;
            animation: badgePulse 2s ease-in-out infinite;
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
        }

        @keyframes badgePulse {
            0%, 100% { transform: scale(1); box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4); }
            50% { transform: scale(1.05); box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6); }
        }

        .timer {
            font-family: 'JetBrains Mono', monospace;
            font-size: 2rem;
            font-weight: 700;
            color: #4facfe;
            text-align: center;
            animation: timerPulse 1s ease-in-out infinite alternate;
            text-shadow: 0 0 20px rgba(79, 172, 254, 0.5);
        }

        @keyframes timerPulse {
            from {
                transform: scale(1);
                text-shadow: 0 0 20px rgba(79, 172, 254, 0.5);
            }
            to {
                transform: scale(1.1);
                text-shadow: 0 0 30px rgba(79, 172, 254, 0.8);
            }
        }

        .wpm-display {
            text-align: right;
            font-family: 'JetBrains Mono', monospace;
        }

        .wpm-label {
            font-size: 0.9rem;
            color: #a0a3bd;
            display: block;
            margin-bottom: 0.2rem;
        }

        .wpm-value {
            font-size: 1.5rem;
            font-weight: 700;
            color: #f093fb;
        }

        /* Stats Section */
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            gap: 1.5rem;
            margin-bottom: 2.5rem;
        }

        .stat-card {
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.05) 0%, rgba(255, 255, 255, 0.02) 100%);
            padding: 1.5rem;
            border-radius: 16px;
            text-align: center;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            border: 1px solid rgba(255, 255, 255, 0.1);
            position: relative;
            overflow: hidden;
        }

        .stat-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.1), transparent);
            transition: left 0.6s;
        }

        .stat-card:hover {
            transform: translateY(-8px) scale(1.02);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
            border-color: rgba(102, 126, 234, 0.3);
        }

        .stat-card:hover::before {
            left: 100%;
        }

        .stat-value {
            font-family: 'JetBrains Mono', monospace;
            font-size: 2rem;
            font-weight: 700;
            color: #4facfe;
            margin-bottom: 0.5rem;
            text-shadow: 0 0 10px rgba(79, 172, 254, 0.3);
        }

        .stat-label {
            font-size: 0.9rem;
            color: #a0a3bd;
            font-weight: 500;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        /* Text Display Area */
        .text-display {
            background: linear-gradient(135deg, rgba(0, 0, 0, 0.4) 0%, rgba(15, 15, 35, 0.6) 100%);
            padding: 2.5rem;
            border-radius: 20px;
            margin-bottom: 2rem;
            font-family: 'JetBrains Mono', monospace; /* Consistent monospace font */
            font-size: 1.3rem;
            line-height: 1.8;
            border: 2px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s ease;
            color: #e8eaed; /* Very light text for visibility */
            box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.3);
            overflow-y: auto; /* Ensure scrollability within the text display */
            max-height: 200px; /* Limit height for scrollbar to appear */
            flex-grow: 1; /* Allow it to grow and shrink in the flex container */
        }

        .text-display:hover {
            border-color: rgba(102, 126, 234, 0.3);
            box-shadow:
                inset 0 2px 10px rgba(0, 0, 0, 0.3),
                0 0 20px rgba(102, 126, 234, 0.2);
        }

        .char {
            position: relative;
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
            border-radius: 4px;
            padding: 2px 1px;
            display: inline-block; /* Ensure spans flow correctly */
        }

        .char.correct {
            background: linear-gradient(135deg, rgba(76, 175, 80, 0.3) 0%, rgba(56, 142, 60, 0.3) 100%);
            color: #81c784; /* Lighter green for visibility */
            animation: correctPop 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
            box-shadow: 0 0 10px rgba(76, 175, 80, 0.3);
        }

        @keyframes correctPop {
            0% { transform: scale(1); }
            50% { transform: scale(1.3); }
            100% { transform: scale(1); }
        }

        .char.incorrect {
            background: linear-gradient(135deg, rgba(244, 67, 54, 0.3) 0%, rgba(198, 40, 40, 0.3) 100%);
            color: #ef5350; /* Lighter red for visibility */
            animation: incorrectShake 0.6s ease-in-out;
            box-shadow: 0 0 10px rgba(244, 67, 54, 0.4);
        }

        @keyframes incorrectShake {
            0%, 100% { transform: translateX(0); }
            10%, 30%, 50%, 70%, 90% { transform: translateX(-4px); }
            20%, 40%, 60%, 80% { transform: translateX(4px); }
        }

        .char.current {
            background: linear-gradient(135deg, rgba(255, 193, 7, 0.4) 0%, rgba(255, 152, 0, 0.4) 100%);
            color: #ffcc02; /* Vibrant yellow for current char */
            animation: currentBlink 1.2s ease-in-out infinite;
            box-shadow: 0 0 15px rgba(255, 193, 7, 0.5);
        }

        @keyframes currentBlink {
            0%, 50% { opacity: 1; transform: scale(1); }
            51%, 100% { opacity: 0.4; transform: scale(1.1); }
        }

        /* Input Area */
        .input-area {
            width: 100%;
            padding: 1.5rem;
            font-family: 'JetBrains Mono', monospace;
            font-size: 1.2rem;
            border: 2px solid rgba(255, 255, 255, 0.1);
            border-radius: 16px;
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.05) 0%, rgba(255, 255, 255, 0.02) 100%);
            color: #e8eaed; /* Light text color */
            outline: none;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            resize: none;
            font-weight: 500;
        }

        .input-area:focus {
            border-color: #667eea;
            box-shadow:
                0 0 30px rgba(102, 126, 234, 0.3),
                inset 0 1px 0 rgba(255, 255, 255, 0.1);
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.05) 100%);
        }

        .input-area::placeholder {
            color: #6c7293; /* Subtle but readable placeholder */
            font-style: italic;
        }

        /* Control Buttons */
        .controls {
            display: flex;
            gap: 1.5rem;
            justify-content: center;
            margin-top: 2.5rem;
        }

        .btn {
            padding: 1rem 2.5rem;
            border: none;
            border-radius: 50px;
            font-family: 'Inter', sans-serif;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            text-transform: uppercase;
            letter-spacing: 1px;
            position: relative;
            overflow: hidden;
            display: flex; /* For icon and text alignment */
            align-items: center;
            justify-content: center;
            gap: 0.5rem;
        }

        .btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
            transition: left 0.6s;
        }

        .btn:hover::before {
            left: 100%;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
        }

        .btn-primary:hover {
            transform: translateY(-3px) scale(1.05);
            box-shadow: 0 8px 25px rgba(102, 126, 234, 0.6);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
            box-shadow: 0 4px 15px rgba(240, 147, 251, 0.4);
        }

        .btn-secondary:hover {
            transform: translateY(-3px) scale(1.05);
            box-shadow: 0 8px 25px rgba(245, 87, 108, 0.6);
        }

        .btn-accent {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
            box-shadow: 0 4px 15px rgba(79, 172, 254, 0.4);
        }

        .btn-accent:hover {
            transform: translateY(-3px) scale(1.05);
            box-shadow: 0 8px 25px rgba(79, 172, 254, 0.6);
        }

        /* Progress Bar */
        .progress-bar {
            width: 100%;
            height: 8px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 4px;
            margin-bottom: 1.5rem;
            overflow: hidden;
            box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.2);
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #667eea 0%, #764ba2 50%, #f093fb 100%);
            width: 0%;
            transition: width 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            border-radius: 4px;
            box-shadow: 0 0 10px rgba(102, 126, 234, 0.5);
            animation: progressGlow 2s ease-in-out infinite alternate;
        }

        @keyframes progressGlow {
            from { box-shadow: 0 0 10px rgba(102, 126, 234, 0.5); }
            to { box-shadow: 0 0 20px rgba(240, 147, 251, 0.7); }
        }

        /* Level Complete Modal */
        .level-complete {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, rgba(15, 15, 35, 0.95) 0%, rgba(26, 26, 46, 0.95) 100%);
            backdrop-filter: blur(20px);
            padding: 3rem;
            border-radius: 24px;
            text-align: center;
            z-index: 1000;
            animation: modalSlide 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 25px 50px rgba(0, 0, 0, 0.5);
            max-width: 500px;
            width: 90%;
            display: none; /* Hidden by default */
        }

        @keyframes modalSlide {
            from {
                opacity: 0;
                transform: translate(-50%, -50%) scale(0.8) rotateX(10deg);
            }
            to {
                opacity: 1;
                transform: translate(-50%, -50%) scale(1) rotateX(0deg);
            }
        }

        .modal-title {
            font-family: 'Poppins', sans-serif;
            font-size: 2.5rem;
            font-weight: 800;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 50%, #f093fb 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 1.5rem;
        }

        .modal-stats {
            color: #a0a3bd;
            font-size: 1.1rem;
            line-height: 1.8;
            margin-bottom: 2rem;
        }

        .modal-stats strong {
            color: #4facfe;
            font-family: 'JetBrains Mono', monospace;
        }

        /* Confetti Effect */
        .confetti {
            position: fixed;
            width: 12px;
            height: 12px;
            border-radius: 50%;
            animation: confettiFall 4s linear forwards;
            z-index: 1001;
        }

        @keyframes confettiFall {
            0% {
                transform: translateY(-100vh) rotate(0deg);
                opacity: 1;
            }
            100% {
                transform: translateY(100vh) rotate(1080deg);
                opacity: 0;
            }
        }

        /* Accuracy Indicator Badge */
        .accuracy-indicator {
            display: inline-block;
            margin-left: 15px;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.9rem;
            padding: 0.4rem 0.8rem;
            border-radius: 20px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .accuracy-high {
            background: linear-gradient(135deg, rgba(76, 175, 80, 0.2) 0%, rgba(56, 142, 60, 0.2) 100%);
            color: #81c784;
            border: 1px solid rgba(76, 175, 80, 0.3);
        }

        .accuracy-medium {
            background: linear-gradient(135deg, rgba(255, 193, 7, 0.2) 0%, rgba(255, 152, 0, 0.2) 100%);
            color: #ffcc02;
            border: 1px solid rgba(255, 193, 7, 0.3);
        }

        .accuracy-low {
            background: linear-gradient(135deg, rgba(244, 67, 54, 0.2) 0%, rgba(198, 40, 40, 0.2) 100%);
            color: #ef5350;
            border: 1px solid rgba(244, 67, 54, 0.3);
        }

        /* Responsive Adjustments */
        @media (max-width: 768px) {
            .title {
                font-size: 2.5rem;
            }

            .container {
                padding: 2rem;
                min-height: unset; /* Remove min-height for mobile to allow it to shrink more */
                height: auto;
            }

            .level-info {
                grid-template-columns: 1fr;
                gap: 1rem;
                text-align: center;
            }

            .controls {
                flex-direction: column;
                align-items: center;
            }

            .btn {
                width: 100%;
                max-width: 250px;
            }
            .text-display {
                font-size: 1rem;
                padding: 1.5rem;
                max-height: 150px; /* Adjust max-height for smaller screens if needed */
            }
            .input-area {
                font-size: 1rem;
                padding: 1rem;
            }
            .modal-title {
                font-size: 2rem;
            }
            .modal-stats {
                font-size: 1rem;
            }
        }
    </style>
</head>
<body>
    <canvas id="backgroundCanvas"></canvas> <!-- Canvas for Three.js live wallpaper -->

    <div class="container">
        <div class="header">
            <h1 class="title">Progression Keys</h1>
            <p class="subtitle">Elevate your typing skills to the next level</p>
        </div>

        <div class="level-info">
            <div class="level-badge">Level <span id="currentLevel">1</span></div>
            <div class="timer">
                <span id="timeRemaining">60</span>s
            </div>
            <div class="wpm-display">
                <span class="wpm-label">Words Per Minute</span>
                <span class="wpm-value" id="wpm">0</span>
                <span id="accuracyIndicator" class="accuracy-indicator accuracy-high">100%</span>
            </div>
        </div>

        <div class="progress-bar">
            <div class="progress-fill" id="progressFill"></div>
        </div>

        <div class="stats">
            <div class="stat-card">
                <div class="stat-value" id="accuracy">100</div>
                <div class="stat-label">Accuracy %</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="wordsTyped">0</div>
                <div class="stat-label">Words Typed</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="errors">0</div>
                <div class="stat-label">Errors</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="streak">0</div>
                <div class="stat-label">Best Streak</div>
            </div>
        </div>

        <div class="text-display" id="textDisplay"></div>

        <textarea
            class="input-area"
            id="textInput"
            placeholder="Click Start Game and begin typing here... (Scroll for full text)"
            autocomplete="off"
            spellcheck="false"
            rows="3"
            disabled
        ></textarea>

        <div class="controls">
            <button class="btn btn-primary" id="startBtn">Start Game</button>
            <button class="btn btn-secondary" id="resetBtn">Reset Level</button>
            <button class="btn btn-accent" id="nextLevelBtn" style="display: none;">Next Level</button>
        </div>
    </div>

    <!-- Level Complete Modal Structure -->
    <div class="level-complete" id="levelCompleteModal">
        <h3 class="modal-title" id="modalTitle"></h3>
        <p class="modal-stats">
            Your WPM: <strong id="finalWpm">0</strong><br>
            Accuracy: <strong id="finalAccuracy">0%</strong><br>
            Time: <strong id="finalTime">0s</strong><br>
            Errors: <strong id="finalErrors">0</strong><br>
            Best Streak: <strong id="finalStreak">0</strong>
        </p>
        <button class="btn btn-primary" id="modalActionBtn"></button>
    </div>

    <script>
        // Three.js Background Animation
        let scene, camera, renderer, stars, starGeo;

        function initThreeJSBackground() {
            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 1, 1000);
            camera.position.z = 1; // Position camera slightly away

            renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('backgroundCanvas'), antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setClearColor(0x0e0e1a, 1); // Set background color to match initial body background

            starGeo = new THREE.BufferGeometry();
            const positions = [];
            const sizes = [];

            for (let i = 0; i < 6000; i++) { // Increased number of stars for denser effect
                positions.push(
                    Math.random() * 600 - 300, // X
                    Math.random() * 600 - 300, // Y
                    Math.random() * 600 - 300  // Z
                );
                sizes.push(Math.random() * 1.5 + 0.5); // Random sizes for sparkle effect
            }
            starGeo.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
            starGeo.setAttribute('size', new THREE.Float32BufferAttribute(sizes, 1));

            const starMaterial = new THREE.PointsMaterial({
                color: 0xffffff, // White stars
                size: 1, // Base size, adjusted by size attribute
                map: createStarTexture(), // Use a small white circle texture for smoother stars
                transparent: true,
                blending: THREE.AdditiveBlending, // For glowing effect
                sizeAttenuation: true // Stars appear smaller when further away
            });

            stars = new THREE.Points(starGeo, starMaterial);
            scene.add(stars);

            animateThreeJSBackground(); // Start the animation loop
        }

        // Function to create a simple circular texture for stars
        function createStarTexture() {
            const canvas = document.createElement('canvas');
            canvas.width = 16;
            canvas.height = 16;
            const context = canvas.getContext('2d');
            const gradient = context.createRadialGradient(
                canvas.width / 2, canvas.height / 2, 0,
                canvas.width / 2, canvas.height / 2, canvas.width / 2
            );
            gradient.addColorStop(0, 'rgba(255,255,255,1)');
            gradient.addColorStop(0.2, 'rgba(255,255,255,1)');
            gradient.addColorStop(1, 'rgba(255,255,255,0)');
            context.fillStyle = gradient;
            context.fillRect(0, 0, canvas.width, canvas.height);
            return new THREE.CanvasTexture(canvas);
        }

        function animateThreeJSBackground() {
            requestAnimationFrame(animateThreeJSBackground);

            // Animate star movement (e.g., drift slowly)
            const positions = stars.geometry.attributes.position.array;
            for (let i = 0; i < positions.length; i += 3) {
                positions[i + 1] -= 0.1; // Move stars down (Y-axis)
                if (positions[i + 1] < -300) { // If star goes off screen, reset it to the top
                    positions[i + 1] = 300;
                }
            }
            stars.geometry.attributes.position.needsUpdate = true; // Tell Three.js to update geometry

            // Optional: subtle rotation of the entire starfield
            stars.rotation.y += 0.0005;
            stars.rotation.x += 0.0002;

            renderer.render(scene, camera);
        }

        // Handle window resizing
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // Ensure Three.js background initializes after window is fully loaded
        window.onload = function() {
            initThreeJSBackground();
        };


        class TypingMaster {
            constructor() {
                this.currentLevel = 1;
                this.maxLevel = 100; // Increased to 100 levels
                this.currentText = '';
                this.currentIndex = 0;
                this.startTime = null;
                this.timeLimit = 60; // Initial time limit, will be overwritten by level
                this.timerInterval = null;
                this.gameActive = false;
                this.stats = {
                    correctChars: 0,
                    incorrectChars: 0,
                    totalCharsTyped: 0,
                    wpm: 0,
                    accuracy: 100,
                    errors: 0,
                    wordsTyped: 0,
                    streak: 0,
                    currentStreak: 0
                };

                this.baseTexts = [
                    // Easy Levels (1-3)
                    "The quick brown fox jumps over the lazy dog.", // Level 1
                    "Brightly lit, the onyx sphinx quickly gazes.", // Level 2
                    "Are you planning a future journey to the Vortex?", // Level 3

                    // Harder Levels (4-100) - Mix of mythology and personal development
                    // Indian Mythology (Ramayanam, Mahabharatam)
                    "Dharma protects those who protect it.", // From Mahabharata
                    "Lord Rama exemplified righteousness and duty.", // From Ramayanam
                    "Krishna's teachings guide righteous action.", // From Mahabharata (Bhagavad Gita)
                    "Hanuman, son of the wind, possessed immense strength and devotion.", // From Ramayanam
                    "The celestial city of Ayodhya, ruled by Dasharatha.", // From Ramayanam
                    "Arjuna faced his dharma on the battlefield of Kurukshetra.", // From Mahabharata
                    "Sita's purity and resilience shone through adversity.", // From Ramayanam
                    "The epic war of Lanka, a clash of divine and demonic forces.", // From Ramayanam
                    "Yudhisthira's unwavering adherence to truth defined his character.", // From Mahabharata
                    "Vishnu's avatars descend to restore cosmic order.", // General Hindu Mythology
                    "The divine discourse of the Bhagavad Gita.", // From Mahabharata
                    "Ravana's arrogance led to his eventual downfall.", // From Ramayanam
                    "The Pandavas endured exile with courage and fortitude.", // From Mahabharata
                    "Lakshmana's devotion to Rama was unparalleled.", // From Ramayanam
                    "Indra, the king of gods, wields the thunderbolt.", // General Hindu Mythology
                    "The celestial weapon, Brahmastra, held immense power.", // From various epics
                    "Draupadi's unwavering spirit in the face of injustice.", // From Mahabharata
                    "Shiva, the destroyer, represents transformation.", // General Hindu Mythology
                    "The sacred river Ganga purifies all who bathe in her waters.", // General Hindu Mythology
                    "Lord Vishnu sleeps on Ananta Shesha, the cosmic serpent.", // General Hindu Mythology
                    "The righteous path, Dharma, leads to liberation.", // Indian philosophy
                    "Karma dictates the fruits of one's actions.", // Indian philosophy
                    "Moksha: liberation from the cycle of rebirth.", // Indian philosophy
                    "Brahma, the creator, fashioned the universe.", // General Hindu Mythology
                    "Goddess Lakshmi embodies wealth and prosperity.", // General Hindu Mythology
                    "The sacred syllable Om, the sound of the universe.", // Hindu philosophy

                    // Personal Development Quotes
                    "Believe you can and you're halfway there.",
                    "The only way to do great work is to love what you do.",
                    "Success is not final, failure is not fatal: it is the courage to continue that counts.",
                    "The best preparation for tomorrow is doing your best today.",
                    "Difficult roads often lead to beautiful destinations.",
                    "Your time is limited, don't waste it living someone else's life.",
                    "The mind is everything. What you think you become.",
                    "Go confidently in the direction of your dreams.",
                    "It is never too late to be what you might have been.",
                    "Develop success from failures.",
                    "The only source of knowledge is experience.",
                    "Do one thing every day that scares you.",
                    "Happiness comes from your own actions.",
                    "Be the change that you wish to see in the world.",
                    "To improve is to change; to be perfect is to change often.",
                    "Life is 10% what happens and 90% how you react.",
                    "The journey of a thousand miles begins with a single step.",
                    "Every challenge is an opportunity for growth.",
                    "Focus, consistency, and patience are keys to mastery.",
                    "Learn from yesterday, live for today, hope for tomorrow.",
                    "Simplicity is the ultimate sophistication.",
                    "The pursuit of knowledge is an endless endeavor.",
                    "Embrace the struggle; it is a vital part of the process.",
                    "Creativity requires the courage to let go of certainties.",
                    "Every line of code tells a story.",
                    "The quiet determination often yields the loudest results.",
                    "Progress, not perfection, is the goal.",
                    "A dream does not become reality through magic; it takes sweat, determination, and hard work.",
                    "The impossible is often the untried.",
                    "Excellence is not a skill. It is an attitude.",
                    "The journey matters as much as the destination.",
                    "Small steps accumulate into great leaps.",
                    "Adaptability defines long-term success.",
                    "True learning happens at the edge of discomfort.",
                    "The symphony of creation begins with a single note.",
                    "Logic is the beginning of wisdom, not the end.",
                    "Harnessing data for foresight is the new frontier.",
                    "Precision in thought leads to clarity in action.",
                    "Continuous iteration drives meaningful advancements.",
                    "The greatest glory in living lies in rising every time we fall.",
                    "The future belongs to those who believe in their dreams.",
                    "Strive not to be a success, but rather to be of value.",
                    "The difference between ordinary and extraordinary is that little extra.",
                    "Patience and perseverance conquer all things.",
                    "It does not matter how slowly you go as long as you do not stop.",
                    "You miss 100% of the shots you don't take.",
                    "The path to success is to take massive, determined action.",
                    "Opportunities don't happen. You create them.",
                    "To live a creative life, we must lose our fear of being wrong.",
                    "If you are not willing to risk the usual, you will have to settle for the ordinary.",
                    "The only limit to our realization of tomorrow will be our doubts of today.",
                    "What seems to us as bitter trials are blessings in disguise.",
                    "The most difficult thing is the decision to act, the rest is merely tenacity.",
                    "Do not wait for leaders; do it alone, person to person.",
                    "Believe in yourself! Have faith in your abilities!",
                    "You are never too old to set another goal or to dream a new dream.",
                    "The power of imagination makes us infinite.",
                    "Optimism is the faith that leads to achievement.",
                    "The miracle is not that we do this work, but that we are happy to do it.",
                    "I attribute my success to this: I never gave or took any excuse.",
                    "The question isn't who is going to let me; it's who is going to stop me.",
                    "If you want to lift yourself up, lift up someone else.",
                    "Remember that not getting what you want is sometimes a wonderful stroke of luck.",
                    "You can't use up creativity. The more you use, the more you have.",
                    "Courage is grace under pressure.",
                    "When we strive to become better than we are, everything around us becomes better too.",
                    "The only constant in life is change.",
                    "The journey of a lifetime starts with a single step.",
                    "It is hard to fail, but it is worse never to have tried to succeed."
                ];

                this.initializeElements();
                this.bindEvents();
                this.loadLevel(this.currentLevel);
            }

            // Dynamically generate time limits based on level difficulty
            generateTimeLimit(level) {
                if (level === 1) {
                    return 25; // Very easy for the first level
                } else if (level === 2) {
                    return 20; // Easy for the second level
                } else if (level === 3) {
                    return 18; // Moderately easy for the third level
                } else {
                    // For levels 4 and above, difficulty increases more
                    const baseTimeForHard = 15; // Starting point for harder levels
                    if (level <= 10) {
                        // Gradual increase for initial hard levels
                        return baseTimeForHard + (level - 4) * 1.5;
                    } else if (level <= 50) {
                        // Slower increase for mid-range levels, capping at 60s
                        return Math.min(60, baseTimeForHard + (10 - 4) * 1.5 + (level - 10) * 0.8);
                    } else {
                        // Even slower increase for higher levels, capping at 80s
                        return Math.min(80, 60 + (level - 50) * 0.5);
                    }
                }
            }

            // Function to get text for a given level
            getTextForLevel(level) {
                // Use modulo to cycle through baseTexts for levels beyond the initial list length
                const textIndex = (level - 1) % this.baseTexts.length;
                return this.baseTexts[textIndex];
            }


            initializeElements() {
                this.elements = {
                    currentLevel: document.getElementById('currentLevel'),
                    timeRemaining: document.getElementById('timeRemaining'),
                    wpm: document.getElementById('wpm'),
                    accuracy: document.getElementById('accuracy'),
                    wordsTyped: document.getElementById('wordsTyped'),
                    errors: document.getElementById('errors'),
                    streak: document.getElementById('streak'),
                    textDisplay: document.getElementById('textDisplay'),
                    textInput: document.getElementById('textInput'),
                    startBtn: document.getElementById('startBtn'),
                    resetBtn: document.getElementById('resetBtn'),
                    nextLevelBtn: document.getElementById('nextLevelBtn'),
                    progressFill: document.getElementById('progressFill'),
                    accuracyIndicator: document.getElementById('accuracyIndicator'),
                    // Modal elements
                    levelCompleteModal: document.getElementById('levelCompleteModal'),
                    modalTitle: document.getElementById('modalTitle'),
                    finalWpm: document.getElementById('finalWpm'),
                    finalAccuracy: document.getElementById('finalAccuracy'),
                    finalTime: document.getElementById('finalTime'),
                    finalErrors: document.getElementById('finalErrors'),
                    finalStreak: document.getElementById('finalStreak'),
                    modalActionBtn: document.getElementById('modalActionBtn')
                };
            }

            bindEvents() {
                this.elements.startBtn.addEventListener('click', () => this.startGame());
                this.elements.resetBtn.addEventListener('click', () => this.resetGame());
                // The nextLevelBtn in the main UI is only controlled by loadLevel and endgame
                // this.elements.nextLevelBtn.addEventListener('click', () => this.nextLevel()); // Removed to prevent manual skipping
                this.elements.textInput.addEventListener('input', (e) => this.handleInput(e));
                this.elements.textInput.addEventListener('keydown', (e) => this.handleKeydown(e));
                this.elements.modalActionBtn.addEventListener('click', () => this.hideModal());
            }

            loadLevel(level) {
                if (level > this.maxLevel) {
                    this.showModal("Congratulations! Ultimate Master of Typing!", "You've conquered all 100 levels of Progression Keys and are now the ULTIMATE Master of Typing!", () => {
                        this.currentLevel = 1; // Reset for starting over
                        this.loadLevel(this.currentLevel);
                        this.elements.modalActionBtn.textContent = "Start Over";
                        this.elements.startBtn.style.display = 'block';
                        this.elements.resetBtn.style.display = 'block';
                        this.elements.nextLevelBtn.style.display = 'none'; // Ensure it's hidden
                        this.elements.textInput.disabled = true;
                    });
                    return;
                }

                this.currentLevel = level;
                this.currentText = this.getTextForLevel(level); // Get dynamic text
                this.timeLimit = Math.round(this.generateTimeLimit(level)); // Get dynamic time limit
                this.currentIndex = 0;
                this.stats = {
                    correctChars: 0,
                    incorrectChars: 0,
                    totalCharsTyped: 0,
                    wpm: 0,
                    accuracy: 100,
                    errors: 0,
                    wordsTyped: 0,
                    streak: 0,
                    currentStreak: 0
                };
                clearInterval(this.timerInterval);
                this.startTime = null;
                this.gameActive = false;

                this.elements.currentLevel.textContent = this.currentLevel;
                this.elements.timeRemaining.textContent = this.timeLimit;
                this.elements.textInput.value = '';
                this.elements.textInput.disabled = true; // Disabled until "Start Game" clicked
                this.elements.startBtn.style.display = 'block';
                this.elements.resetBtn.style.display = 'block';
                this.elements.nextLevelBtn.style.display = 'none'; // Ensure it's hidden when a new level is loaded

                this.updateStatsDisplay();
                this.displayText();
                this.updateProgressBar();
                this.updateAccuracyIndicator();
            }

            displayText() {
                this.elements.textDisplay.innerHTML = '';
                this.currentText.split('').forEach((char, index) => {
                    const charSpan = document.createElement('span');
                    charSpan.textContent = char;
                    charSpan.className = 'char';
                    this.elements.textDisplay.appendChild(charSpan);
                });
                this.highlightCurrentChar();
            }

            highlightCurrentChar() {
                // Remove 'current' from previous
                const prevChar = this.elements.textDisplay.children[this.currentIndex - 1];
                if (prevChar) {
                    prevChar.classList.remove('current');
                }
                // Add 'current' to the new one
                const currentChar = this.elements.textDisplay.children[this.currentIndex];
                if (currentChar) {
                    currentChar.classList.add('current');
                    // Scroll into view if the current character is outside the visible area of textDisplay
                    const charRect = currentChar.getBoundingClientRect();
                    const textDisplayRect = this.elements.textDisplay.getBoundingClientRect();

                    // If character is below visible area OR character is above visible area
                    if (charRect.bottom > textDisplayRect.bottom || charRect.top < textDisplayRect.top) {
                        currentChar.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
                    }
                }
            }

            startGame() {
                if (this.gameActive) return; // Prevent multiple starts
                this.gameActive = true;
                this.elements.textInput.disabled = false;
                this.elements.textInput.focus();
                this.startTime = new Date().getTime();
                this.startTimer();
                this.elements.startBtn.style.display = 'none';
                this.elements.resetBtn.style.display = 'none'; // Hide reset button once game starts
                this.elements.nextLevelBtn.style.display = 'none'; // Ensure it's hidden
            }

            resetGame() {
                clearInterval(this.timerInterval);
                this.loadLevel(this.currentLevel);
            }

            nextLevel() {
                this.currentLevel++;
                this.loadLevel(this.currentLevel);
            }

            startTimer() {
                let timeLeft = this.timeLimit;
                this.elements.timeRemaining.textContent = timeLeft;

                this.timerInterval = setInterval(() => {
                    timeLeft--;
                    this.elements.timeRemaining.textContent = timeLeft;
                    this.updateWPM();
                    if (timeLeft <= 0) {
                        this.endGame(false); // Time's up!
                    }
                }, 1000);
            }

            handleInput(event) {
                if (!this.gameActive) return; // Only process input if game is active

                const typedValue = this.elements.textInput.value;
                const expectedChar = this.currentText[this.currentIndex];
                const typedChar = typedValue[this.currentIndex];

                // Handle regular typing
                if (this.currentIndex < this.currentText.length) {
                    this.stats.totalCharsTyped++;

                    const charSpan = this.elements.textDisplay.children[this.currentIndex];
                    if (charSpan) { // Ensure charSpan exists before modifying
                        if (typedChar === expectedChar) {
                            charSpan.classList.remove('incorrect');
                            charSpan.classList.add('correct');
                            this.stats.correctChars++;
                            this.stats.currentStreak++;
                            if (this.stats.currentStreak > this.stats.streak) {
                                this.stats.streak = this.stats.currentStreak;
                            }
                        } else {
                            charSpan.classList.remove('correct');
                            charSpan.classList.add('incorrect');
                            this.stats.incorrectChars++;
                            this.stats.errors++;
                            this.stats.currentStreak = 0; // Reset streak on error
                        }
                    }
                    this.currentIndex++;
                }

                // Check for end of text
                if (this.currentIndex === this.currentText.length) {
                    this.endGame(true); // Passed the level
                    return;
                }

                // Update real-time displays
                this.updateStatsDisplay();
                this.updateProgressBar();
                this.updateAccuracyIndicator();
                this.highlightCurrentChar(); // Highlight next char
            }

            handleKeydown(event) {
                // Handle Backspace
                if (event.key === 'Backspace') {
                    if (this.currentIndex > 0) {
                        this.currentIndex--;
                        this.stats.totalCharsTyped--; // Decrement total typed chars

                        const charSpan = this.elements.textDisplay.children[this.currentIndex];
                        if (charSpan) { // Ensure charSpan exists before modifying
                            if (charSpan.classList.contains('correct')) {
                                this.stats.correctChars--;
                            } else if (charSpan.classList.contains('incorrect')) {
                                this.stats.incorrectChars--;
                                this.stats.errors--;
                            }
                            charSpan.classList.remove('correct', 'incorrect', 'current'); // Remove all feedback classes
                        }
                        this.elements.textInput.value = this.elements.textInput.value.slice(0, this.currentIndex); // Adjust input value
                        this.highlightCurrentChar(); // Highlight current (previous) char
                        this.updateStatsDisplay();
                        this.updateProgressBar();
                        this.updateAccuracyIndicator();
                    }
                    event.preventDefault(); // Prevent default backspace behavior (going back in history)
                } else if (event.key.length === 1) {
                    // Only allow actual typing characters
                    // Prevent typing past the current text length
                    if (this.currentIndex >= this.currentText.length) {
                        event.preventDefault();
                    }
                } else {
                    // Prevent other keys like Tab, Enter (if not handled for new lines), etc.
                    // This prevents issues with game state
                    if (event.key !== 'Shift' && event.key !== 'Control' && event.key !== 'Alt' && event.key !== 'Meta' && event.key !== 'CapsLock' && event.key !== 'ArrowLeft' && event.key !== 'ArrowRight' && event.key !== 'ArrowUp' && event.key !== 'ArrowDown') {
                        event.preventDefault();
                    }
                }
            }


            updateWPM() {
                if (!this.startTime) {
                    this.stats.wpm = 0;
                    return;
                }
                const elapsedTimeInMinutes = (new Date().getTime() - this.startTime) / (1000 * 60);
                if (elapsedTimeInMinutes > 0) {
                    this.stats.wpm = Math.round((this.stats.correctChars / 5) / elapsedTimeInMinutes);
                } else {
                    this.stats.wpm = 0;
                }
                this.elements.wpm.textContent = this.stats.wpm;
            }

            updateAccuracy() {
                if (this.stats.totalCharsTyped === 0) {
                    this.stats.accuracy = 100;
                } else {
                    this.stats.accuracy = ((this.stats.correctChars / this.stats.totalCharsTyped) * 100).toFixed(0);
                }
            }

            updateStatsDisplay() {
                this.updateAccuracy();
                this.updateWPM();
                this.elements.accuracy.textContent = this.stats.accuracy;
                this.elements.errors.textContent = this.stats.errors;
                this.elements.streak.textContent = this.stats.streak;

                // Calculate words typed based on spaces or end of text for approximation
                const wordsTypedApprox = this.currentText.substring(0, this.currentIndex).split(' ').filter(word => word.length > 0).length;
                this.elements.wordsTyped.textContent = wordsTypedApprox;
            }

            updateProgressBar() {
                const progress = (this.currentIndex / this.currentText.length) * 100;
                this.elements.progressFill.style.width = `${progress}%`;
            }

            updateAccuracyIndicator() {
                const accuracy = parseFloat(this.stats.accuracy);
                const indicator = this.elements.accuracyIndicator;
                indicator.textContent = `${this.stats.accuracy}%`;

                // Reset classes
                indicator.classList.remove('accuracy-high', 'accuracy-medium', 'accuracy-low');

                if (accuracy >= 90) {
                    indicator.classList.add('accuracy-high');
                } else if (accuracy >= 70) {
                    indicator.classList.add('accuracy-medium');
                } else {
                    indicator.classList.add('accuracy-low');
                }
            }

            endGame(passed) {
                if (!this.gameActive) return; // Prevent ending game multiple times
                this.gameActive = false;
                clearInterval(this.timerInterval);
                this.elements.textInput.disabled = true;

                // Ensure final stats are calculated
                this.updateWPM();
                this.updateAccuracy();
                this.updateStatsDisplay(); // Update all stat displays one last time

                const finalTime = this.timeLimit - parseInt(this.elements.timeRemaining.textContent);

                this.elements.finalWpm.textContent = this.stats.wpm;
                this.elements.finalAccuracy.textContent = `${this.stats.accuracy}%`;
                this.elements.finalTime.textContent = `${Math.max(0, finalTime)}s`; // Ensure time doesn't go negative
                this.elements.finalErrors.textContent = this.stats.errors;
                this.elements.finalStreak.textContent = this.stats.streak;

                let modalTitleText = "Level Completed!";
                let modalButtonText = "Next Level";
                let modalCallback = () => this.nextLevel();

                if (passed) {
                    if (this.currentLevel === this.maxLevel) {
                        modalTitleText = "Congratulations! Ultimate Master of Typing!";
                        modalButtonText = "Start Over";
                        modalCallback = () => {
                            this.currentLevel = 1;
                            this.loadLevel(this.currentLevel);
                            this.elements.startBtn.style.display = 'block';
                            this.elements.resetBtn.style.display = 'block';
                            this.elements.nextLevelBtn.style.display = 'none'; // Explicitly hide on Start Over
                        };
                    } else if (this.currentLevel === 50) {
                        modalTitleText = "Congratulations! Gold Master of Typing!";
                    } else if (this.currentLevel === 25) {
                        modalTitleText = "Congratulations! Silver Master of Typing!";
                    }
                    this.triggerConfetti();
                } else {
                    modalTitleText = "Time's Up / Failed!";
                    modalButtonText = "Retry Level";
                    modalCallback = () => this.resetGame();
                    // Explicitly hide next level button if the level was failed
                    this.elements.nextLevelBtn.style.display = 'none';
                }

                this.elements.modalTitle.textContent = modalTitleText;
                this.elements.modalActionBtn.textContent = modalButtonText;
                this.elements.modalActionBtn.onclick = () => {
                    this.hideModal(modalCallback);
                };

                this.elements.levelCompleteModal.style.display = 'block'; // Show modal
                this.elements.startBtn.style.display = 'none'; // Hide start button when modal is open
                this.elements.resetBtn.style.display = 'none'; // Hide reset button when modal is open
            }

            hideModal(callback = null) {
                this.elements.levelCompleteModal.style.display = 'none';
                if (callback) callback();
                // After modal is hidden, loadLevel (called by callback) will reset button visibility.
                // The main nextLevelBtn should not be controlled from here,
                // it is controlled by loadLevel and endgame.
            }

            triggerConfetti() {
                const colors = ['#f093fb', '#667eea', '#4facfe', '#00f2fe', '#81c784', '#ffcc02'];
                for (let i = 0; i < 100; i++) {
                    const confetti = document.createElement('div');
                    confetti.className = 'confetti';
                    confetti.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                    confetti.style.left = Math.random() * 100 + 'vw';
                    confetti.style.animationDelay = Math.random() * 0.5 + 's';
                    confetti.style.animationDuration = (Math.random() * 2 + 3) + 's'; // 3 to 5 seconds
                    document.body.appendChild(confetti);

                    confetti.addEventListener('animationend', () => {
                        confetti.remove();
                    });
                }
            }
        }

        // Initialize the game when the DOM is fully loaded
        document.addEventListener('DOMContentLoaded', () => {
            // Note: Three.js background is initialized via window.onload, which fires after DOMContentLoaded
            new TypingMaster();
        });
    </script>
</body>
</html>
