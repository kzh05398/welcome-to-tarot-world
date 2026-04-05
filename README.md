# welcome-to-tarot-world
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Thoth Oracle | 托特深層占卜殿堂</title>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Noto+Serif+TC:wght@300;500;600&display=swap" rel="stylesheet">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* 自定義樣式，確保 GitHub 渲染無誤 */
        :root {
            --gold: #c9a84c;
            --gold-dim: #7a6430;
        }
        body { 
            background: #050505; 
            color: #e8ddd0; 
            font-family: 'Noto Serif TC', serif; 
            min-height: 100vh;
        }
        .cinzel { font-family: 'Cinzel', serif; }
        .gold-gradient {
            background: linear-gradient(to bottom, #f9f295, #b8860b, #e8d5a0);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .gold-border { 
            border: 1px solid rgba(201, 168, 76, 0.3); 
            transition: all 0.4s ease; 
        }
        .gold-border:hover { 
            border-color: var(--gold); 
            box-shadow: 0 0 15px rgba(201, 168, 76, 0.2); 
        }
        .card-container { perspective: 1200px; height: 550px; width: 320px; }
        .card-inner { 
            transition: transform 0.8s cubic-bezier(0.2, 0.8, 0.2, 1); 
            transform-style: preserve-3d; 
            cursor: pointer; 
            width: 100%; height: 100%;
        }
        .card-flipped { transform: rotateY(180deg); }
        .card-face { 
            backface-visibility: hidden; 
            position: absolute; 
            width: 100%; height: 100%; 
            border-radius: 15px; 
            border: 1px solid rgba(201, 168, 76, 0.4); 
        }
        .card-back { 
            background: #0a0a0a; 
            display: flex; align-items: center; justify-content: center; 
            font-size: 1.5rem; letter-spacing: 0.5em; color: var(--gold-dim);
            writing-mode: vertical-rl;
        }
        .card-front { 
            transform: rotateY(180deg); 
            background: #0d0d0d; 
            padding: 1.5rem; 
            display: flex; flex-direction: column; 
            box-shadow: inset 0 0 40px rgba(0,0,0,0.9);
        }
        .scroll-box::-webkit-scrollbar { width: 3px; }
        .scroll-box::-webkit-scrollbar-thumb { background: var(--gold-dim); }
        .section-tag { font-size: 0.7rem; color: var(--gold-dim); font-weight: bold; margin-bottom: 4px; border-bottom: 1px solid #222; }
    </style>
</head>
<body class="flex items-center justify-center p-4">

    <div id="step-lang" class="text-center animate-fade-in">
        <h1 class="cinzel text-6xl md:text-8xl gold-gradient font-bold mb-16 tracking-widest">THOTH</h1>
        <div class="flex gap-10 justify-center">
            <button onclick="setLang('en')" class="gold-border px-10 py-3 tracking-widest hover:bg-yellow-900/10">ENGLISH</button>
            <button onclick="setLang('zh')" class="gold-border px-10 py-3 tracking-widest hover:bg-yellow-900/10">中文</button>
        </div>
    </div>

    <div id="step-cat" class="hidden text-center w-full max-w-4xl">
        <h2 id="cat-title" class="cinzel text-2xl mb-12 gold-gradient">SELECT YOUR SPHERE</h2>
        <div class="grid grid-cols-2 md:grid-cols-3 gap-6">
            <button onclick="startReading('love')" class="gold-border p-6"><span class="block text-lg">LOVE</span><span class="text-xs opacity-50">愛情</span></button>
            <button onclick="startReading('career')" class="gold-border p-6"><span class="block text-lg">CAREER</span><span class="text-xs opacity-50">事業</span></button>
            <button onclick="startReading('study')" class="gold-border p-6"><span class="block text-lg font-bold">STUDY</span><span class="text-xs opacity-50">學業</span></button>
            <button onclick="startReading('wealth')" class="gold-border p-6"><span class="block text-lg">WEALTH</span><span class="text-xs opacity-50">金錢</span></button>
            <button onclick="startReading('health')" class="gold-border p-6"><span class="block text-lg">HEALTH</span><span class="text-xs opacity-50">健康</span></button>
            <button onclick="startReading('yesno')" class="gold-border p-6"><span class="block text-lg">YES / NO</span><span class="text-xs opacity-50">是非</span></button>
        </div>
    </div>

    <div id="step-result" class="hidden w-full max-w-7xl text-center">
        <div id="current-intent-label" class="mb-8 cinzel text-sm tracking-[0.4em] text-yellow-700"></div>
        <div class="flex flex-wrap justify-center gap-8">
            <div class="card-container">
                <div class="card-inner" onclick="this.classList.toggle('card-flipped')">
                    <div class="card-face card-back">PAST 過去</div>
                    <div class="card-face card-front text-left" id="card-0"></div>
                </div>
            </div>
            <div class="card-container">
                <div class="card-inner" onclick="this.classList.toggle('card-flipped')">
                    <div class="card-face card-back">PRESENT 現在</div>
                    <div class="card-face card-front text-left" id="card-1"></div>
                </div>
            </div>
            <div class="card-container">
                <div class="card-inner" onclick="this.classList.toggle('card-flipped')">
                    <div class="card-face card-back">FUTURE 未來</div>
                    <div class="card-face card-front text-left" id="card-2"></div>
                </div>
            </div>
        </div>
        <button onclick="location.reload()" class="mt-16 cinzel text-xs tracking-[0.3em] opacity-30 hover:opacity-100 underline">RE-ENTER THE TEMPLE</button>
    </div>

    <script>
        let curLang = 'zh';
        let curTopic = '';

        // 核心資料庫 (22 張大阿卡納深度文案)
        const deck = [
            {
                name_en: "The Fool", name_zh: "0. 愚者",
                general: "代表無限的潛力、冒險與不顧後果的起點。托特體系中是純粹的靈魂意識。",
                readings: {
                    love: "【愛情】一段不受傳統束縛的新戀情。如果你正處於猶豫，這張牌告訴你：跳下去吧，靈魂的冒險不需導航。",
                    career: "【事業】嘗試未曾踏足的領域。這不是盲目，而是對直覺的極度信任，適合啟動新項目。",
                    study: "【學業】打破舊有的思維框架，適合接觸跨學科或全新的知識，保持孩童般的好奇心。",
                    wealth: "【金錢】意外的財務流動。不按常理出牌的投資可能會帶來驚喜，但要注意基礎開銷。",
                    health: "【健康】能量充沛，但需留意神經系統或因冒險造成的意外碰撞。",
                    yesno: "【裁決】YES。命運支持你的冒險。"
                }
            },
            {
                name_en: "The Magus", name_zh: "I. 魔術師",
                general: "意志、溝通與創造。你擁有將想法具象化為現實的所有工具。",
                readings: {
                    love: "【愛情】強大的吸引力與完美的溝通。你掌握著關係的節奏，現在是表達心意的最佳時刻。",
                    career: "【事業】展現專業技能與靈活性。你的多才多藝將解決眼前的困局，獲得領導青睞。",
                    study: "【學業】學習效率極高，能快速掌握核心邏輯，考試或面試表現會非常出色。",
                    wealth: "【金錢】靠智慧賺錢。你的創意與技術是最大的財富來源，適合多渠道發展。",
                    health: "【健康】精神狀態良好，注意呼吸系統的保養。",
                    yesno: "【裁決】YES。你擁有絕對的主動權。"
                }
            },
            {
                name_en: "The Devil", name_zh: "XV. 惡魔",
                general: "原始能量與物質束縛。代表潘神（Pan）的創造力，也是被慾望奴役的警示。",
                readings: {
                    love: "【愛情】充滿熱情但具備高度控制欲。小心陷入「成癮式」的關係中，這可能是肉體吸引力大於心靈共鳴。",
                    career: "【事業】權力鬥爭與物質誘惑。你可能正處於極大的壓力下，或者過度追求職位與金錢而失去了初心。",
                    study: "【學業】容易受到外界誘惑（社交媒體、成癮）而荒廢。或者對成績有著病態的偏執，需找回學習的樂趣。",
                    wealth: "【金錢】雖然有大筆金錢進入的機會，但極大可能伴隨債務或貪婪陷阱。不要成為數字的奴隸。",
                    health: "【健康】注意過度勞累與生活不規律。避免酒精或藥物依賴。",
                    yesno: "【裁決】NO。目前被物質或情緒蒙蔽，不宜行動。"
                }
            },
            {
                name_en: "The Tower", name_zh: "XVI. 塔",
                general: "突如其來的崩裂與解放。摧毀虛假的堡壘，讓靈魂得以從廢墟中重獲自由。",
                readings: {
                    love: "【愛情】震驚的劇變。可能是爭吵或關係重組。這是一場必要的地震，為了震掉早已腐爛的根基。",
                    career: "【事業】環境的劇烈動盪。公司變革或項目崩塌。這不是災難，而是強迫你離開不適合的崗位。",
                    study: "【學業】過去的學習模式徹底失效，或在重要考試中受挫。接受打擊，從廢墟中建立新的認知。",
                    wealth: "【金錢】財務震盪，意料之外的大開銷。現在是徹底止損、重塑金錢觀念的時刻。",
                    health: "【健康】小心突發性的炎症或身體損傷。注意休息以免崩潰。",
                    yesno: "【裁決】NO。強行推進只會導致崩毀。"
                }
            }
            // (其餘 18 張牌可依此類推增加...)
        ];

        function setLang(lang) {
            curLang = lang;
            document.getElementById('step-lang').classList.add('hidden');
            document.getElementById('step-cat').classList.remove('hidden');
        }

        function startReading(topic) {
            curTopic = topic;
            document.getElementById('step-cat').classList.add('hidden');
            document.getElementById('step-result').classList.remove('hidden');
            document.getElementById('current-intent-label').innerText = topic.toUpperCase();

            // 隨機抽 3 張
            const shuffled = [...deck].sort(() => 0.5 - Math.random());
            const selection = shuffled.slice(0, 3);

            selection.forEach((card, i) => {
                const el = document.getElementById(`card-${i}`);
                const name = curLang === 'zh' ? card.name_zh : card.name_en;
                const reading = card.readings[curTopic] || card.general;

                el.innerHTML = `
                    <div class="mb-4">
                        <div class="text-[10px] text-yellow-600 font-bold tracking-widest opacity-60">ARCANA</div>
                        <h3 class="cinzel text-xl gold-gradient font-bold">${name}</h3>
                    </div>
                    <div class="scroll-box overflow-y-auto flex-1 pr-1">
                        <div class="mb-4">
                            <div class="section-tag">ESSENCE / 牌義本質</div>
                            <p class="text-xs italic opacity-60 leading-relaxed">${card.general}</p>
                        </div>
                        <div class="mb-4">
                            <div class="section-tag">DEPTH READING / 深度解答</div>
                            <p class="text-sm leading-loose text-yellow-100">${reading}</p>
                        </div>
                    </div>
                `;
            });
        }
    </script>
</body>
</html>
