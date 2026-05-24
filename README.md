<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="manifest" href="manifest.json">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>AI Aura Glass Translator</title>
    <style>
        /* Modern Design System Reset */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        :root {
            --glass-bg: rgba(255, 255, 255, 0.03);
            --glass-border: rgba(255, 255, 255, 0.08);
            --glass-shine: rgba(255, 255, 255, 0.15);
            --text-primary: #f8fafc;
            --text-secondary: #94a3b8;
            --accent-cyan: #22d3ee;
            --accent-pink: #f472b6;
        }

        body {
            min-height: 100vh;
            min-height: -webkit-fill-available; /* Fixed mobile viewport stretching */
            background: #030712;
            color: var(--text-primary);
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 16px;
            overflow-x: hidden;
            position: relative;
        }

        html {
            height: -webkit-fill-available;
        }

        /* Animated Mesh Gradient Background - Creates the stunning glass refraction */
        .aura-bg {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            z-index: 0;
            overflow: hidden;
            pointer-events: none;
        }

        .orb {
            position: absolute;
            border-radius: 50%;
            filter: blur(80px);
            opacity: 0.45;
            mix-blend-mode: screen;
            animation: floatOrb 20s infinite alternate ease-in-out;
        }

        .orb-1 { width: 350px; height: 350px; background: #6366f1; top: -10%; left: -10%; }
        .orb-2 { width: 400px; height: 400px; background: #d946ef; bottom: -10%; right: -10%; animation-delay: -5s; }
        .orb-3 { width: 300px; height: 300px; background: #06b6d4; top: 40%; left: 50%; animation-delay: -10s; }

        @keyframes floatOrb {
            0% { transform: translate(0px, 0px) scale(1); }
            50% { transform: translate(40px, -60px) scale(1.2); }
            100% { transform: translate(-20px, 20px) scale(0.9); }
        }

        /* Glass Container Framework */
        .app-shell {
            position: relative;
            z-index: 1;
            width: 100%;
            max-width: 540px;
            background: var(--glass-bg);
            backdrop-filter: blur(30px);
            -webkit-backdrop-filter: blur(30px);
            border: 1px solid var(--glass-border);
            border-radius: 28px;
            padding: 24px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7);
        }

        header {
            text-align: center;
            margin-bottom: 24px;
        }

        header h1 {
            font-size: 1.6rem;
            font-weight: 700;
            letter-spacing: -0.5px;
            background: linear-gradient(135deg, var(--accent-cyan), var(--accent-pink));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        header p {
            font-size: 0.85rem;
            color: var(--text-secondary);
            margin-top: 4px;
        }

        /* Dynamic Visualizer Array Layout */
        .audio-wave {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 4px;
            height: 24px;
            margin-bottom: 20px;
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .audio-wave.active { opacity: 1; }

        .wave-bar {
            width: 3px;
            height: 6px;
            background: var(--accent-cyan);
            border-radius: 2px;
            transition: transform 0.1s ease;
        }

        /* Stack Cards styling */
        .panel-stack {
            display: flex;
            flex-direction: column;
            gap: 16px;
            margin-bottom: 24px;
        }

        .glass-card {
            background: rgba(255, 255, 255, 0.01);
            border: 1px solid rgba(255, 255, 255, 0.04);
            border-radius: 18px;
            padding: 16px;
            min-height: 110px;
            position: relative;
            transition: border-color 0.3s ease;
        }

        .glass-card:focus-within, .glass-card.listening-active {
            border-color: rgba(34, 211, 238, 0.3);
        }

        .card-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
        }

        .card-label {
            font-size: 0.72rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: var(--text-secondary);
        }

        .speaker-btn {
            background: none;
            border: none;
            color: var(--text-secondary);
            cursor: pointer;
            padding: 4px;
            display: none; /* Revealed dynamically when text populates */
            border-radius: 50%;
            transition: all 0.2s;
        }

        .speaker-btn:hover {
            color: var(--accent-pink);
            background: rgba(255, 255, 255, 0.05);
        }

        .display-box {
            font-size: 1.05rem;
            line-height: 1.5;
            color: var(--text-primary);
            max-height: 120px;
            overflow-y: auto;
        }

        .empty-state {
            color: rgba(255, 255, 255, 0.25);
            font-style: italic;
            font-size: 0.95rem;
        }

        /* Interactive Controls Area */
        .controls-wrapper {
            display: flex;
            flex-direction: column;
            gap: 16px;
        }

        .dropdown-container {
            position: relative;
        }

        select {
            width: 100%;
            padding: 14px 16px;
            background: rgba(255, 255, 255, 0.04);
            border: 1px solid var(--glass-border);
            border-radius: 16px;
            color: var(--text-primary);
            font-size: 0.95rem;
            font-weight: 500;
            outline: none;
            cursor: pointer;
            appearance: none;
            -webkit-appearance: none;
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            transition: all 0.3s;
        }

        select:focus {
            border-color: rgba(255, 255, 255, 0.2);
            background: rgba(255, 255, 255, 0.08);
        }

        select option {
            background: #111827;
            color: var(--text-primary);
            padding: 12px;
        }

        /* Mobile Fluid Micro-Interaction Input Trigger */
        .action-trigger-btn {
            width: 100%;
            background: linear-gradient(135deg, rgba(34, 211, 238, 0.15), rgba(244, 114, 182, 0.15));
            border: 1px solid rgba(34, 211, 238, 0.3);
            border-radius: 100px;
            padding: 18px;
            color: #ffffff;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
        }

        .action-trigger-btn:active {
            transform: scale(0.97);
        }

        .action-trigger-btn.recording-state {
            background: linear-gradient(135deg, #ef4444, #ec4899);
            border-color: #f43f5e;
            box-shadow: 0 0 25px rgba(239, 68, 68, 0.4);
        }

        .status-pill {
            align-self: center;
            font-size: 0.7rem;
            text-transform: uppercase;
            font-weight: 700;
            letter-spacing: 1px;
            padding: 6px 14px;
            border-radius: 30px;
            background: rgba(255, 255, 255, 0.04);
            border: 1px solid var(--glass-border);
            color: var(--text-secondary);
        }
    </style>
</head>
<body>

    <div class="aura-bg">
        <div class="orb orb-1"></div>
        <div class="orb orb-2"></div>
        <div class="orb orb-3"></div>
    </div>

    <main class="app-shell">
        <header>
            <h1>AI Voice Translator</h1>
            <p>Real-time Cross-Device Neural Network Interpreter</p>
        </header>

        <div class="audio-wave" id="visualizerWave">
            <div class="wave-bar"></div><div class="wave-bar"></div><div class="wave-bar"></div>
            <div class="wave-bar"></div><div class="wave-bar"></div><div class="wave-bar"></div>
            <div class="wave-bar"></div><div class="wave-bar"></div><div class="wave-bar"></div>
        </div>

        <section class="panel-stack">
            <div class="glass-card" id="inputCard">
                <div class="card-meta">
                    <span class="card-label">Captured Native Speech</span>
                </div>
                <div class="display-box empty-state" id="nativeTextDisplay">
                    Tap below and talk clearly into your mic...
                </div>
            </div>

            <div class="glass-card" style="background: rgba(255,255,255,0.02)">
                <div class="card-meta">
                    <span class="card-label" style="color: var(--accent-pink)">English Translation</span>
                    <button class="speaker-btn" id="ttsBtn" title="Listen to English translation">
                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 5L6 9H2v6h4l5 4V5z"/><path d="M15.54 8.46a5 5 0 0 1 0 7.07M19.07 4.93a10 10 0 0 1 0 14.14"/></svg>
                    </button>
                </div>
                <div class="display-box empty-state" id="englishTextDisplay">
                    Translation will systematically stream here...
                </div>
            </div>
        </section>

        <section class="controls-wrapper">
            <div class="dropdown-container">
                <select id="langInputSelector">
                    <option value="kn-IN" selected>Kannada (ಕನ್ನಡ)</option>
                    <option value="hi-IN">Hindi (हिन्दी)</option>
                    <option value="ta-IN">Tamil (தமிழ்)</option>
                    <option value="te-IN">Telugu (తెలుగు)</option>
                    <option value="ml-IN">Malayalam (മലയാളം)</option>
                    <option value="mr-IN">Marathi (मराठी)</option>
                    <option value="es-ES">Spanish (Español)</option>
                </select>
            </div>

            <button class="action-trigger-btn" id="masterMicToggle">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M12 2a3 3 0 0 0-3 3v7a3 3 0 0 0 6 0V5a3 3 0 0 0-3-3Z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2"/><line x1="12" x2="12" y1="19" y2="22"/></svg>
                <span>Initialize AI Capture</span>
            </button>

            <div class="status-pill" id="systemLogStatus">System Ready</div>
        </section>
    </main>

    <script>
        const masterMicToggle = document.getElementById('masterMicToggle');
        const btnTitleText = masterMicToggle.querySelector('span');
        const langInputSelector = document.getElementById('langInputSelector');
        const nativeTextDisplay = document.getElementById('nativeTextDisplay');
        const englishTextDisplay = document.getElementById('englishTextDisplay');
        const systemLogStatus = document.getElementById('systemLogStatus');
        const visualizerWave = document.getElementById('visualizerWave');
        const inputCard = document.getElementById('inputCard');
        const ttsBtn = document.getElementById('ttsBtn');

        let speechRecognizerEngine = null;
        let activeListeningStatus = false;
        let audioLevelLoopInterval = null;
        let compiledTranslationString = "";

        // Initialization routine for the Speech Web API Matrix
        function initializeSpeechRecognitionPipeline() {
            const BrowserSpeechImplementation = window.SpeechRecognition || window.webkitSpeechRecognition;
            
            if (!BrowserSpeechImplementation) {
                alert("This mobile browser environment does not support real-time speech capture features. Use Google Chrome or Safari.");
                systemLogStatus.innerText = "Error: Environment unsupported";
                return false;
            }

            speechRecognizerEngine = new BrowserSpeechImplementation();
            speechRecognizerEngine.continuous = false; // Yields precise text execution arrays
            speechRecognizerEngine.interimResults = false;

            // Triggered on hardware input validation extraction
            speechRecognizerEngine.onresult = async (event) => {
                const nativeResultString = event.results[0][0].transcript;
                
                nativeTextDisplay.classList.remove('empty-state');
                nativeTextDisplay.innerText = nativeResultString;
                
                systemLogStatus.innerText = "Processing Translation...";
                
                const localizedLanguageIsoCode = langInputSelector.value.split('-')[0];
                await executeNeuralLanguageTranslation(nativeResultString, localizedLanguageIsoCode, 'en');
            };

            // Event Hooks for User Experience Transitions
            speechRecognizerEngine.onstart = () => {
                activeListeningStatus = true;
                btnTitleText.innerText = "Stop Processing Feed";
                masterMicToggle.classList.add('recording-state');
                visualizerWave.classList.add('active');
                inputCard.classList.add('listening-active');
                systemLogStatus.innerText = "Streaming Mic Live...";
                
                nativeTextDisplay.classList.add('empty-state');
                nativeTextDisplay.innerText = "Listening to your voice...";
                englishTextDisplay.classList.add('empty-state');
                englishTextDisplay.innerText = "Waiting for finalized input processing...";
                ttsBtn.style.display = "none";
                
                executeMicrophoneWaveSimulation(true);
            };

            speechRecognizerEngine.onend = () => {
                activeListeningStatus = false;
                btnTitleText.innerText = "Initialize AI Capture";
                masterMicToggle.classList.remove('recording-state');
                visualizerWave.classList.remove('active');
                inputCard.classList.remove('listening-active');
                executeMicrophoneWaveSimulation(false);
                
                if (systemLogStatus.innerText.includes("Streaming")) {
                    systemLogStatus.innerText = "System Ready";
                }
            };

            speechRecognizerEngine.onerror = (event) => {
                console.error("Hardware Stream Exception:", event.error);
                systemLogStatus.innerText = `Error State: ${event.error}`;
                if(event.error === 'not-allowed') {
                    alert("Localhost network security blocked mic hardware access. Permit permissions or access through an HTTPS proxy tunnel.");
                }
            };

            return true;
        }

        // Pure Client-side Zero-Key Neural Network Translation Pipeline
        async function executeNeuralLanguageTranslation(payloadText, sourceLanguageIso, destinationLanguageIso) {
            try {
                const gatewayUrl = `https://translate.googleapis.com/translate_a/single?client=gtx&sl=${sourceLanguageIso}&tl=${destinationLanguageIso}&dt=t&q=${encodeURIComponent(payloadText)}`;
                
                const rawResponse = await fetch(gatewayUrl);
                const collectionArray = await rawResponse.json();
                
                if (collectionArray && collectionArray[0]) {
                    compiledTranslationString = collectionArray[0].map(segment => segment[0]).join('');
                    
                    englishTextDisplay.classList.remove('empty-state');
                    englishTextDisplay.innerText = compiledTranslationString;
                    systemLogStatus.innerText = "Success";
                    
                    // Reveal the smart audio reader button
                    ttsBtn.style.display = "block";
                }
            } catch (err) {
                console.error("Translation stream anomaly:", err);
                englishTextDisplay.classList.remove('empty-state');
                englishTextDisplay.innerText = "Error completing cloud parsing array.";
                systemLogStatus.innerText = "Execution Failure";
            }
        }

        // Text-to-Speech Engine Array (Reads the translation back out loud)
        ttsBtn.addEventListener('click', () => {
            if (!compiledTranslationString) return;
            const textToSpeechUtterance = new SpeechSynthesisUtterance(compiledTranslationString);
            textToSpeechUtterance.lang = 'en-US';
            window.speechSynthesis.speak(textToSpeechUtterance);
        });

        // Fluid Aesthetic Live Waveform Simulated Array Handler
        function executeMicrophoneWaveSimulation(shouldAnimate) {
            const waveElements = document.querySelectorAll('.wave-bar');
            if (!shouldAnimate) {
                clearInterval(audioLevelLoopInterval);
                waveElements.forEach(bar => bar.style.transform = `scaleY(1)`);
                return;
            }
            
            audioLevelLoopInterval = setInterval(() => {
                waveElements.forEach(bar => {
                    const randomScale = Math.random() * 3.5 + 0.5;
                    bar.style.transform = `scaleY(${randomScale})`;
                });
            }, 100);
        }

        // Action Trigger Event Listener Routing
        masterMicToggle.addEventListener('click', () => {
            if (!speechRecognizerEngine) {
                const successfulInit = initializeSpeechRecognitionPipeline();
                if (!successfulInit) return;
            }

            if (activeListeningStatus) {
                speechRecognizerEngine.stop();
            } else {
                speechRecognizerEngine.lang = langInputSelector.value;
                speechRecognizerEngine.start();
            }
        });
    </script>
</body>
</html>
