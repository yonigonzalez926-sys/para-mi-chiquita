<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Para el Amor de Mi Vida - Lector de Huellas</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,400;1,600&family=Montserrat:wght@200;300;400;500;600;700&display=swap" rel="stylesheet">
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        gold: {
                            light: '#f3e5ab',
                            DEFAULT: '#d4af37',
                            dark: '#aa820a',
                            glow: 'rgba(212, 175, 55, 0.35)'
                        },
                        dark: {
                            bg: '#0a0a0c',
                            card: '#141418',
                            border: '#2a2a32'
                        }
                    },
                    fontFamily: {
                        serif: ['"Cormorant Garamond"', 'serif'],
                        sans: ['"Montserrat"', 'sans-serif']
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background-color: #0a0a0c;
            color: #f4f4f5;
            font-family: 'Montserrat', sans-serif;
            overflow-x: hidden;
            user-select: none;
        }

        #particles-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1;
        }

        .gold-border-glow {
            border: 1px solid rgba(212, 175, 55, 0.35);
            box-shadow: 0 10px 30px -10px rgba(0, 0, 0, 0.8), 0 0 25px rgba(212, 175, 55, 0.2);
        }

        .gold-gradient-text {
            background: linear-gradient(135deg, #fff3ca 0%, #d4af37 50%, #aa820a 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .gold-button {
            background: linear-gradient(135deg, #d4af37 0%, #b88e28 100%);
            color: #0a0a0c;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .gold-button:hover {
            background: linear-gradient(135deg, #f3e5ab 0%, #d4af37 100%);
            box-shadow: 0 0 20px rgba(212, 175, 55, 0.5);
            transform: translateY(-2px);
        }

        /* Scan Animation Effects */
        .fingerprint-wrapper {
            position: relative;
            cursor: pointer;
            touch-action: manipulation;
        }

        .scan-laser {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 4px;
            background: linear-gradient(90deg, transparent, #d4af37, #ffffff, #d4af37, transparent);
            box-shadow: 0 0 15px #d4af37, 0 0 30px #d4af37;
            opacity: 0;
        }

        .scanning .scan-laser {
            opacity: 1;
            animation: scanMove 1.8s infinite ease-in-out;
        }

        @keyframes scanMove {
            0% { top: 0%; }
            50% { top: 95%; }
            100% { top: 0%; }
        }

        .pulse-ring {
            position: absolute;
            inset: -10px;
            border: 2px solid rgba(212, 175, 55, 0.4);
            border-radius: 50%;
            animation: ringPulse 2s infinite ease-out;
        }

        @keyframes ringPulse {
            0% { transform: scale(0.9); opacity: 0.8; }
            100% { transform: scale(1.3); opacity: 0; }
        }

        .modal-backdrop {
            background: rgba(10, 10, 12, 0.88);
            backdrop-filter: blur(8px);
        }

        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0a0a0c;
        }
        ::-webkit-scrollbar-thumb {
            background: #2a2a32;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #d4af37;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between relative selection:bg-gold selection:text-dark-bg">

    <canvas id="particles-canvas"></canvas>

    <!-- PANTALLA ESCÁNER DE HUELLA DACTILAR -->
    <div id="scanner-screen" class="fixed inset-0 z-50 flex flex-col items-center justify-center p-4 bg-dark-bg transition-opacity duration-1000">
        <div class="text-center max-w-md w-full gold-border-glow bg-dark-card/90 p-8 rounded-3xl backdrop-blur-md relative overflow-hidden flex flex-col items-center">
            
            <span class="text-gold text-xs tracking-[0.3em] uppercase mb-2 font-medium">Acceso Biométrico de Amor</span>
            <h1 class="font-serif text-2xl md:text-3xl text-white font-normal mb-6">Escanea Tu Huella Dactilar</h1>
            
            <!-- Contenedor del Escáner -->
            <div id="fingerprint-btn" class="fingerprint-wrapper w-36 h-36 rounded-2xl border border-gold/30 bg-black/60 flex items-center justify-center relative overflow-hidden my-4 group">
                <div class="pulse-ring"></div>
                
                <!-- Icono de Huella Dactilar SVG -->
                <svg id="fingerprint-icon" class="w-24 h-24 text-gold/60 transition-all duration-300 group-hover:text-gold" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.2" d="M12 11c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zm0-4c-3.3 0-6 2.7-6 6 0 2.2 1.2 4.1 3 5.2M15 17c1.8-1.1 3-3 3-5.2 0-3.3-2.7-6-6-6m-6 3c0-1.7 1.3-3 3-3s3 1.3 3 3m-6 6c0 1.7 1.3 3 3 3s3-1.3 3-3"/>
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.2" d="M12 3C7.03 3 3 7.03 3 12c0 3.2 1.7 6 4.2 7.6M16.8 19.6C19.3 18 21 15.2 21 12c0-4.97-4.03-9-9-9"/>
                </svg>

                <div class="scan-laser"></div>
            </div>

            <!-- Progreso de Escaneo -->
            <div class="w-full bg-zinc-800 h-1.5 rounded-full overflow-hidden my-3 max-w-[200px]">
                <div id="scan-progress" class="bg-gold h-full w-0 transition-all duration-100"></div>
            </div>

            <p id="scan-instructions" class="text-zinc-400 text-xs font-light leading-relaxed">
                Mantén presionado el sensor de huella para desbloquear la carta
            </p>
        </div>
    </div>

    <main id="main-content" class="relative z-10 opacity-0 transition-opacity duration-1000 max-w-3xl mx-auto px-4 py-8 md:py-12 w-full">

        <!-- CABECERA -->
        <header class="text-center mb-8">
            <div class="inline-flex items-center justify-center gap-2 px-4 py-1.5 rounded-full border border-gold/30 bg-gold/5 text-gold text-xs tracking-[0.25em] uppercase mb-4">
                <i class="fa-solid fa-lock-open text-[10px]"></i> Dedicatoria Especial
            </div>
            <h1 class="font-serif text-4xl md:text-6xl text-white font-normal mb-2 tracking-wide">
                Para el Amor de Mi Vida
            </h1>
            <p id="girlfriend-name-display" class="font-serif italic text-2xl md:text-3xl gold-gradient-text">
                Mi Niña Hermosa
            </p>
        </header>

        <article class="gold-border-glow bg-dark-card/90 rounded-2xl p-6 md:p-10 mb-10 relative">
            <div class="absolute top-3 left-3 w-4 h-4 border-t border-l border-gold/40"></div>
            <div class="absolute top-3 right-3 w-4 h-4 border-t border-r border-gold/40"></div>
            <div class="absolute bottom-3 left-3 w-4 h-4 border-b border-l border-gold/40"></div>
            <div class="absolute bottom-3 right-3 w-4 h-4 border-b border-r border-gold/40"></div>

            <div class="text-center mb-6">
                <span class="text-gold text-2xl font-serif">✦</span>
            </div>

            <div class="font-serif text-lg md:text-xl text-zinc-200 leading-relaxed space-y-6 italic font-light text-justify md:text-center">
                <p>Mi niña hermosa,</p>
                <p>
                    Desde que llegaste a mi vida, absolutamente todo cambió para bien. Te has convertido en mi persona favorita, en la luz que ilumina mis días y en la razón de tantas de mis sonrisas.
                </p>
                <p>
                    Contigo aprendí lo bonito que es querer de verdad, sin dudas ni temores. Me das una paz increíble y no hay un solo momento a tu lado en el que no me sienta la persona más afortunada del mundo.
                </p>
                <p>
                    Gracias por ser mi lugar seguro, por regalarme tu cariño incondicional y por hacer que cada día juntos sea tan especial. Prometo cuidarte, valorarte y amarte hoy, mañana y siempre.
                </p>
            </div>

            <div class="mt-10 text-center pt-6 border-t border-gold/20">
                <p class="text-xs tracking-[0.25em] text-zinc-400 uppercase mb-2">Con todo mi amor,</p>
                <p id="boyfriend-name-display" class="font-serif text-2xl text-gold font-semibold">
                    Tu Amor
                </p>
            </div>
        </article>

        <section class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-10">
            <div class="gold-border-glow bg-dark-card/80 p-6 rounded-2xl text-center flex flex-col justify-center items-center">
                <div class="text-gold text-2xl mb-2"><i class="fa-solid fa-clock-rotate-left"></i></div>
                <h4 class="text-xs text-zinc-400 uppercase tracking-widest mb-1">Caminando juntos</h4>
                <div id="days-counter" class="font-serif text-3xl md:text-4xl font-semibold text-white my-1">
                    0 días
                </div>
                <p class="text-[11px] text-zinc-500 italic">Fecha especial: 24 de junio de 2026</p>
            </div>

            <div class="gold-border-glow bg-dark-card/80 p-6 rounded-2xl text-center flex flex-col justify-center items-center">
                <div class="text-gold text-2xl mb-2"><i class="fa-solid fa-heart-pulse"></i></div>
                <h4 class="text-xs text-zinc-400 uppercase tracking-widest mb-1">Razones para amarte</h4>
                <p class="text-xs text-zinc-400 mb-4">¿Quieres saber por qué eres tan especial para mí?</p>
                <button id="reasons-modal-btn" class="gold-button px-6 py-2.5 rounded-full text-xs font-semibold tracking-wider uppercase">
                    Ver Razones
                </button>
            </div>
        </section>

    </main>

    <footer class="relative z-10 text-center py-6 border-t border-zinc-900 text-zinc-500 text-xs font-light">
        <p>Hecho con todo el corazón ✦ Para la persona más especial</p>
    </footer>

    <!-- MODAL DE RAZONES -->
    <div id="reasons-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 modal-backdrop opacity-0 pointer-events-none transition-all duration-300">
        <div class="bg-dark-card border border-gold/40 max-w-md w-full rounded-2xl p-6 md:p-8 relative gold-border-glow max-h-[85vh] flex flex-col">
            <button id="close-modal-btn" class="absolute top-4 right-4 text-zinc-400 hover:text-white text-lg">
                <i class="fa-solid fa-xmark"></i>
            </button>
            
            <div class="text-center mb-6 flex-shrink-0">
                <i class="fa-solid fa-heart text-gold text-3xl mb-2"></i>
                <h3 class="font-serif text-2xl text-white">Por Qué Te Amo Tanto</h3>
                <p class="text-xs text-gold tracking-widest uppercase mt-1">Lo bonito de nuestro amor</p>
            </div>

            <div class="overflow-y-auto space-y-3.5 pr-1 flex-1 font-serif italic text-zinc-300 text-sm md:text-base">
                <div class="p-3.5 rounded-xl bg-zinc-900/60 border border-zinc-800 flex items-start gap-3">
                    <span class="text-gold not-italic font-bold">1.</span>
                    <p>Por la tranquilidad y paz que me das con solo abrazarme.</p>
                </div>
                <div class="p-3.5 rounded-xl bg-zinc-900/60 border border-zinc-800 flex items-start gap-3">
                    <span class="text-gold not-italic font-bold">2.</span>
                    <p>Por tu sonrisa bonita que ilumina cualquiera de mis días.</p>
                </div>
                <div class="p-3.5 rounded-xl bg-zinc-900/60 border border-zinc-800 flex items-start gap-3">
                    <span class="text-gold not-italic font-bold">3.</span>
                    <p>Porque escuchando música o en silencio, estar a tu lado siempre es mi momento favorito.</p>
                </div>
                <div class="p-3.5 rounded-xl bg-zinc-900/60 border border-zinc-800 flex items-start gap-3">
                    <span class="text-gold not-italic font-bold">4.</span>
                    <p>Por tu forma de cuidar de mí y por apoyarme en todo momento.</p>
                </div>
                <div class="p-3.5 rounded-xl bg-zinc-900/60 border border-zinc-800 flex items-start gap-3">
                    <span class="text-gold not-italic font-bold">5.</span>
                    <p>Simplemente por ser la persona más maravillosa que pude haber conocido.</p>
                </div>
            </div>

            <div class="mt-6 text-center flex-shrink-0 pt-4 border-t border-zinc-800">
                <button id="close-modal-btn-2" class="gold-button px-6 py-2 rounded-full text-xs font-semibold uppercase tracking-wider">
                    Cerrar con Un Beso
                </button>
            </div>
        </div>
    </div>

    <script>
        const state = {
            girlfriendName: "Mi Niña Hermosa",
            boyfriendName: "Tu Amor",
            startDate: "2026-06-24"
        };

        // Lector de Huella Logic
        let scanTimer = null;
        let scanProgress = 0;
        const fpBtn = document.getElementById('fingerprint-btn');
        const progressElem = document.getElementById('scan-progress');
        const instructionsElem = document.getElementById('scan-instructions');

        function startScanning(e) {
            if (e) e.preventDefault();
            if (!fpBtn) return;
            fpBtn.classList.add('scanning');
            if (instructionsElem) instructionsElem.innerText = "Escaneando huella dactilar...";
            
            scanTimer = setInterval(() => {
                scanProgress += 5;
                if (progressElem) progressElem.style.width = `${scanProgress}%`;

                if (scanProgress >= 100) {
                    clearInterval(scanTimer);
                    unlockCard();
                }
            }, 50);
        }

        function stopScanning() {
            if (scanProgress < 100) {
                clearInterval(scanTimer);
                fpBtn.classList.remove('scanning');
                scanProgress = 0;
                if (progressElem) progressElem.style.width = '0%';
                if (instructionsElem) instructionsElem.innerText = "Mantén presionado el sensor de huella para desbloquear la carta";
            }
        }

        function unlockCard() {
            const scanner = document.getElementById('scanner-screen');
            const mainContent = document.getElementById('main-content');

            if (scanner) scanner.classList.add('opacity-0', 'pointer-events-none');
            setTimeout(() => {
                if (scanner) scanner.style.display = 'none';
                if (mainContent) mainContent.classList.remove('opacity-0');
            }, 1000);
        }

        if (fpBtn) {
            fpBtn.addEventListener('mousedown', startScanning);
            fpBtn.addEventListener('mouseup', stopScanning);
            fpBtn.addEventListener('mouseleave', stopScanning);

            fpBtn.addEventListener('touchstart', startScanning, { passive: false });
            fpBtn.addEventListener('touchend', stopScanning);
        }

        function calculateDays() {
            const start = new Date(state.startDate);
            const today = new Date();
            const diffTime = Math.abs(today - start);
            const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
            const counterElem = document.getElementById('days-counter');
            if (counterElem) counterElem.innerText = `${diffDays} Días`;
        }

        // Particle Canvas
        const canvas = document.getElementById('particles-canvas');
        const ctx = canvas.getContext('2d');
        let particles = [];

        function resizeCanvas() {
            if (!canvas) return;
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        class Particle {
            constructor() {
                this.reset();
            }

            reset() {
                this.x = Math.random() * (canvas ? canvas.width : 500);
                this.y = (canvas ? canvas.height : 500) + Math.random() * 20;
                this.size = Math.random() * 3 + 1;
                this.speedY = Math.random() * 1.2 + 0.3;
                this.speedX = (Math.random() - 0.5) * 0.5;
                this.opacity = Math.random() * 0.6 + 0.2;
                this.isHeart = Math.random() < 0.3;
            }

            update() {
                this.y -= this.speedY;
                this.x += this.speedX;
                if (this.y < -10) this.reset();
            }

            draw() {
                if (!ctx) return;
                ctx.save();
                ctx.globalAlpha = this.opacity;
                if (this.isHeart) {
                    ctx.fillStyle = '#d4af37';
                    ctx.font = `${this.size * 3.5}px sans-serif`;
                    ctx.fillText('♥', this.x, this.y);
                } else {
                    ctx.fillStyle = '#f3e5ab';
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                    ctx.fill();
                }
                ctx.restore();
            }
        }

        function initParticles() {
            particles = [];
            const count = Math.min(Math.floor(window.innerWidth / 15), 50);
            for (let i = 0; i < count; i++) {
                particles.push(new Particle());
            }
        }

        function animateParticles() {
            if (canvas && ctx) {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                particles.forEach(p => {
                    p.update();
                    p.draw();
                });
            }
            requestAnimationFrame(animateParticles);
        }

        // Modal Controls
        const modal = document.getElementById('reasons-modal');
        const openModalBtn = document.getElementById('reasons-modal-btn');
        const closeModalBtn = document.getElementById('close-modal-btn');
        const closeModalBtn2 = document.getElementById('close-modal-btn-2');

        function toggleModal(show) {
            if (!modal) return;
            if (show) {
                modal.classList.remove('opacity-0', 'pointer-events-none');
            } else {
                modal.classList.add('opacity-0', 'pointer-events-none');
            }
        }

        if (openModalBtn) openModalBtn.addEventListener('click', () => toggleModal(true));
        if (closeModalBtn) closeModalBtn.addEventListener('click', () => toggleModal(false));
        if (closeModalBtn2) closeModalBtn2.addEventListener('click', () => toggleModal(false));

        window.onload = () => {
            calculateDays();
            initParticles();
            animateParticles();
        };
    </script>
</body>
</html>
