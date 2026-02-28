<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IrisStream - Divine Visions</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Faustina:ital,wght@0,400;0,700;1,400&display=swap');

        :root {
            --olympus-gold: #d4af37;
            --iris-purple: #4b0082;
            --nectar-white: #fdfbf7;
        }

        body {
            background-color: #0a0a0a;
            color: var(--nectar-white);
            font-family: 'Faustina', serif;
            margin: 0;
            overflow: hidden;
        }

        h1, h2, .greek-font {
            font-family: 'Cinzel', serif;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .rainbow-border {
            border-bottom: 3px solid;
            border-image: linear-gradient(to right, red, orange, yellow, green, blue, indigo, violet) 1;
        }

        .video-container {
            height: 100vh;
            scroll-snap-type: y mandatory;
            overflow-y: scroll;
            scrollbar-width: none;
        }

        .video-container::-webkit-scrollbar {
            display: none;
        }

        .vision-card {
            height: 100vh;
            width: 100%;
            scroll-snap-align: start;
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(180deg, rgba(0,0,0,0.2) 0%, rgba(0,0,0,0.8) 100%);
        }

        .vision-placeholder {
            width: 100%;
            height: 100%;
            object-fit: cover;
            background: #1a1a1a;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border: 2px solid var(--olympus-gold);
        }

        .interaction-bar {
            position: absolute;
            right: 15px;
            bottom: 100px;
            display: flex;
            flex-direction: column;
            gap: 25px;
            align-items: center;
        }

        .divine-btn {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(5px);
            border: 1px solid var(--olympus-gold);
            border-radius: 50%;
            width: 55px;
            height: 55px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .divine-btn:hover {
            transform: scale(1.1);
            box-shadow: 0 0 15px var(--olympus-gold);
        }

        .divine-btn i {
            font-size: 20px;
            color: var(--olympus-gold);
        }

        .divine-btn span {
            font-size: 10px;
            margin-top: 2px;
        }

        .vision-info {
            position: absolute;
            bottom: 25px;
            left: 20px;
            right: 80px;
        }

        .iris-badge {
            display: inline-flex;
            align-items: center;
            background: linear-gradient(90deg, #ff0000, #ff7f00, #ffff00, #00ff00, #0000ff, #4b0082, #8b00ff);
            padding: 2px 10px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: bold;
            color: white;
            margin-bottom: 10px;
        }

        nav {
            position: fixed;
            top: 0;
            width: 100%;
            z-index: 50;
            background: rgba(10, 10, 10, 0.9);
            padding: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .bottom-nav {
            position: fixed;
            bottom: 0;
            width: 100%;
            background: rgba(10, 10, 10, 0.95);
            display: flex;
            justify-content: space-around;
            padding: 15px;
            border-top: 1px solid var(--olympus-gold);
        }
    </style>
</head>
<body>

    <nav class="rainbow-border">
        <div class="greek-font text-xl font-bold tracking-tighter text-[#d4af37]">
            <i class="fas fa-eye mr-2"></i>IrisStream
        </div>
        <div class="flex gap-4">
            <span class="greek-font text-sm border-b-2 border-[#d4af37]">Following</span>
            <span class="greek-font text-sm opacity-50">For You</span>
        </div>
        <div>
            <i class="fas fa-search text-[#d4af37]"></i>
        </div>
    </nav>

    <div class="video-container" id="feed">
        <!-- Vision 1 -->
        <div class="vision-card">
            <div class="vision-placeholder">
                <i class="fas fa-bolt text-64px text-[#d4af37] mb-4 text-5xl"></i>
                <p class="greek-font opacity-50">Mount Olympus Livestream</p>
                <p class="italic text-xs mt-2">"Behold my lightning!" - Zeus</p>
            </div>
            
            <div class="interaction-bar">
                <div class="divine-btn">
                    <i class="fas fa-apple-whole"></i>
                    <span>1.2M</span>
                </div>
                <div class="divine-btn">
                    <i class="fas fa-scroll"></i>
                    <span>45K</span>
                </div>
                <div class="divine-btn">
                    <i class="fas fa-feather-pointed"></i>
                    <span>Share</span>
                </div>
            </div>

            <div class="vision-info">
                <div class="iris-badge">IRIS VERIFIED</div>
                <h2 class="text-xl font-bold mb-1">@KingZeus</h2>
                <p class="text-sm opacity-90">Testing out the new master bolt. Don't look directly at the glare. #Olympus #StormKing #DivineVibe</p>
                <div class="mt-2 text-[#d4af37] flex items-center">
                    <i class="fas fa-music mr-2 text-xs"></i>
                    <marquee class="w-32 text-xs">Original Sound - Orpheus' Lyre Hits</marquee>
                </div>
            </div>
        </div>

        <!-- Vision 2 -->
        <div class="vision-card">
            <div class="vision-placeholder">
                <i class="fas fa-water text-64px text-[#d4af37] mb-4 text-5xl"></i>
                <p class="greek-font opacity-50">Depths of the Aegean</p>
                <p class="italic text-xs mt-2">"Vibing with the Hippocampi" - Poseidon</p>
            </div>
            
            <div class="interaction-bar">
                <div class="divine-btn">
                    <i class="fas fa-apple-whole"></i>
                    <span>890K</span>
                </div>
                <div class="divine-btn">
                    <i class="fas fa-scroll"></i>
                    <span>12K</span>
                </div>
                <div class="divine-btn">
                    <i class="fas fa-feather-pointed"></i>
                    <span>Share</span>
                </div>
            </div>

            <div class="vision-info">
                <div class="iris-badge">IRIS VERIFIED</div>
                <h2 class="text-xl font-bold mb-1">@OceanLord_77</h2>
                <p class="text-sm opacity-90">Tsunami check! Just kidding, it's a calm day. Look at this coral reef glow. #DeepBlue #TridentLife</p>
                <div class="mt-2 text-[#d4af37] flex items-center">
                    <i class="fas fa-music mr-2 text-xs"></i>
                    <marquee class="w-32 text-xs">Whale Song Remix - Siren DJ</marquee>
                </div>
            </div>
        </div>
    </div>

    <div class="bottom-nav">
        <i class="fas fa-home text-2xl text-[#d4af37]"></i>
        <i class="fas fa-compass text-2xl opacity-50"></i>
        <div class="bg-[#d4af37] text-black w-12 h-8 rounded-lg flex items-center justify-center font-bold">
            <i class="fas fa-plus"></i>
        </div>
        <i class="fas fa-comment-dots text-2xl opacity-50"></i>
        <i class="fas fa-user-circle text-2xl opacity-50"></i>
    </div>

    <script>
        // Simple mock behavior: Like animation
        document.querySelectorAll('.divine-btn:first-child').forEach(btn => {
            btn.addEventListener('click', function() {
                const icon = this.querySelector('i');
                icon.classList.toggle('text-red-500');
                icon.classList.toggle('text-[#d4af37]');
            });
        });
    </script>
</body>
</html>

