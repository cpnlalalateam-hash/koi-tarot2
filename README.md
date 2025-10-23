index.html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>16タイプ人間関係戦略診断</title>
    <!-- Tailwind CSSを読み込み（GitHub Pagesで安定動作） -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* フォント設定 */
        body { font-family: "Inter", sans-serif; }
        /* トランジション設定 */
        .transition-colors { transition: background-color 0.2s, border-color 0.2s; }
    </style>
    <script>
        // Tailwindのカラー設定を上書き（カスタマイズ用）
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'primary-blue': '#1D4ED8',
                        'primary-orange': '#F59E0B',
                        'bg-light': '#F9FAFB',
                    }
                }
            }
        }
    </script>
</head>
<body class="bg-bg-light min-h-screen flex items-center justify-center p-4">

    <div id="app" class="w-full max-w-lg bg-white rounded-xl shadow-2xl p-6 md:p-8">
        
        <h1 class="text-3xl font-extrabold text-primary-blue text-center mb-6">
            16タイプ戦略診断
        </h1>
        <p class="text-center text-gray-500 mb-8">
            簡単な4つの質問で、あなたのタイプと人間関係戦略を導き出します。
        </p>
        
        <!-- 診断エリア -->
        <div id="quiz-area">
            <div class="mb-6">
                <p id="current-question-text" class="text-xl font-semibold text-gray-800 text-center mb-4 leading-relaxed"></p>
            </div>
            <div id="buttons-area" class="space-y-4">
                <!-- ボタンはJSで動的に追加されます -->
            </div>
        </div>

        <!-- 結果概要エリア (最初は非表示) -->
        <div id="result-area" class="hidden">
            <!-- 結果概要がJSで動的に追加されます -->
        </div>
        
    </div>
    
    <!-- 詳細コンテンツエリア -->
    <div id="detail-area" class="hidden w-full max-w-lg mt-6 bg-white rounded-xl shadow-2xl p-6 md:p-8">
        <h2 class="text-2xl font-bold text-center text-primary-orange mb-6 border-b pb-2">
            究極の戦略ガイド
        </h2>
        <div id="detail-content" class="space-y-6">
            <!-- 詳細な分析結果がJSで動的に追加されます -->
        </div>
        <div class="text-center mt-8">
             <button id="reload-button" class="px-6 py-3 bg-gray-500 text-white font-bold rounded-full hover:bg-gray-600 transition-colors shadow-md">
                🔄 もう一度診断する
            </button>
        </div>
    </div>


    <script>
        let questionIndex = 0;
        let result = '';

        const questions = [
            {
                type: 'E/I',
                text: 'あなたは、週末の予定として、賑やかなパーティーに参加するのと、家で静かに読書をするのと、どちらにエネルギーを感じますか？',
                options: [{ label: 'A: 賑やかな場所での交流 (外向 E)', value: 'E' }, { label: 'B: 静かな環境での充電 (内向 I)', value: 'I' }]
            },
            {
                type: 'S/N',
                text: 'あなたは、新しいプロジェクトに取り組むとき、具体的な事実や実績に基づいて慎重に進めるのと、可能性や大まかなアイデアを基に直感的に進めるのと、どちらを好みますか？',
                options: [{ label: 'A: 具体的な情報と現実 (感覚 S)', value: 'S' }, { label: 'B: 可能性と抽象的な概念 (直観 N)', value: 'N' }]
            },
            {
                type: 'T/F',
                text: '友人が深刻な悩みを抱えているとき、あなたは論理的に解決策を提示するのと、まずは共感し感情を共有するのと、どちらを優先しますか？',
                options: [{ label: 'A: 客観的な分析と解決策 (思考 T)', value: 'T' }, { label: 'B: 感情的なサポートと共感 (感情 F)', value: 'F' }]
            },
            {
                type: 'J/P',
                text: 'あなたは、旅行に行く際、事前に綿密な計画を立ててスケジュール通りに進めるのと、行き当たりばったりで状況に合わせて柔軟に楽しむのと、どちらが好きですか？',
                options: [{ label: 'A: 計画と秩序 (判断 J)', value: 'J' }, { label: 'B: 柔軟性と自発性 (知覚 P)', value: 'P' }]
            }
        ];

        // 診断結果データ（V8の内容をそのまま移植）
        const results = {
            INTJ: { alias: '建築家', group: '分析家', description: '想像力が豊かで、戦略的な思考の持ち主。', strengths: '論理的思考力と計画性。', weaknesses: '人間関係と感情の機微が苦手。', relationships: '深い知的な結びつきを求める。', careerAdvice: '長期的な戦略を立てる役職、AI研究者。', compatibility: { best: 'ESFP - エンターテイナー', worst: 'ESFJ - 領事官' }, careerCompatibility: { bestBoss: 'ENTJ - 指揮官', bestSubordinate: 'ISTJ - ロジスティシャン' }, difficultRelationship: { type: 'ESFJ - 領事官', advice: 'ESFJの気持ちを一度受け止めるワンクッションを挟む努力が必要。' } },
            INFP: { alias: '仲介者', group: '外交官', description: '詩人肌で親切な利他主義者。静かな理想主義者。', strengths: '共感性と創造性。', weaknesses: '現実逃避と実行力。', relationships: '真実性と深いつながりを求める。', careerAdvice: '作家、カウンセラー、セラピスト。', compatibility: { best: 'ENTJ - 指揮官', worst: 'ESTJ - 幹部' }, careerCompatibility: { bestBoss: 'ENFJ - 主人公', bestSubordinate: 'ISFP - 冒険家' }, difficultRelationship: { type: 'ESTJ - 幹部', advice: '具体的なデータや事実を最初に提示するよう意識しましょう。' } },
            ISTJ: { alias: 'ロジスティシャン', group: '番人', description: '事実重視で物事に取り組む傾向があり、現実的で信頼できる堅実タイプ。', strengths: '責任感が強く、計画と秩序を重んじる。', weaknesses: '変化への適応が苦手。', relationships: '忠実で真面目。', careerAdvice: '会計士、監査役、行政官。', compatibility: { best: 'ENFP - 運動家', worst: 'ENTP - 討論者' }, careerCompatibility: { bestBoss: 'INTJ - 建築家', bestSubordinate: 'ESTJ - 幹部' }, difficultRelationship: { type: 'ENTP - 討論者', advice: '「新しいアイデアを出す係」として役割を認めることで摩擦を減らす。' } },
            ESFP: { alias: 'エンターテイナー', group: '探検家', description: '明るくて社交的、生粋のムードメーカー。エネルギッシュで、思いつきで行動するのを好む。', strengths: '社交性と行動力。', weaknesses: '長期計画の苦手さ。', relationships: 'オープンで温かく、楽しい体験を共有。', careerAdvice: '俳優・タレント、イベントコーディネーター、接客業。', compatibility: { best: 'INTJ - 建築家', worst: 'ISTJ - ロジスティシャン' }, careerCompatibility: { bestBoss: 'ESFJ - 領事官', bestSubordinate: 'ISFP - 冒険家' }, difficultRelationship: { type: 'ISTJ - ロジスティシャン', advice: '期限と目的を明確にし、具体的な形でアウトプットを出す。' } },
            INTP: { alias: '論理学者', group: '分析家', description: '貪欲な知識欲がある革新的な発明家。論理的思考が得意なスーパー頭脳派。', strengths: '抽象的な概念の分析に優れる。', weaknesses: '現実世界の細かい作業に関心を示さない。', relationships: '知的好奇心を共有できる関係を求める。', careerAdvice: '大学教授、プログラマー、データサイエンティスト。', compatibility: { best: 'ESTJ - 幹部', worst: 'ISFJ - 擁護者' }, careerCompatibility: { bestBoss: 'INTP - 論理学者', bestSubordinate: 'ENTP - 討論者' }, difficultRelationship: { type: 'ISFJ - 擁護者', advice: 'ISFJの感情的なサポートに「感謝」の言葉を明確に伝える。' }},
            ENTJ: { alias: '指揮官', group: '分析家', description: '強い意志があり、大胆で想像力豊かな指導者タイプ。', strengths: '目標達成のために組織を導くカリスマ性。', weaknesses: '他者の感情に配慮を欠きがち。', relationships: '効率と共通の目標を重視。', careerAdvice: 'CEO、経営コンサルタント、政治家。', compatibility: { best: 'INFP - 仲介者', worst: 'ISFP - 冒険家' }, careerCompatibility: { bestBoss: 'INTJ - 建築家', bestSubordinate: 'ENFJ - 主人公' }, difficultRelationship: { type: 'ISFP - 冒険家', advice: 'ISFPの個人的な価値観を尊重し、指示ではなく「提案」として話を持ちかける。' }},
            ENTP: { alias: '討論者', group: '分析家', description: '賢くて好奇心旺盛な思考家。知的な挑戦は必ず受けて立つ、アイディアマン。', strengths: '抜群のトークセンスと柔軟な発想力。', weaknesses: 'アイデアを実行に移さず、途中で飽きることがある。', relationships: '議論を通じて成長できる関係を楽しむ。', careerAdvice: '起業家、弁護士、広報・マーケティング。', compatibility: { best: 'INFJ - 提唱者', worst: 'ISTJ - ロジスティシャン' }, careerCompatibility: { bestBoss: 'INTP - 論理学者', bestSubordinate: 'ENFP - 運動家' }, difficultRelationship: { type: 'ISTJ - ロジスティシャン', advice: '議論の前に、まずISTJの「実績や規則」を尊重する。' }},
            INFJ: { alias: '提唱者', group: '外交官', description: '寡黙で神秘的な理想主義者。確固たる信念と情熱を宿す高潔な思想家。', strengths: '直感が鋭く、人々の生活を改善したいという強い願望。', weaknesses: '理想主義が過ぎ、現実とのギャップに苦しむ。', relationships: '深い共感と相互理解を重視。', careerAdvice: 'カウンセラー、心理学者、作家、NPO職員。', compatibility: { best: 'ENTP - 討論者', worst: 'ISTP - 巨匠' }, careerCompatibility: { bestBoss: 'ENFJ - 主人公', bestSubordinate: 'INFP - 仲介者' }, difficultRelationship: { type: 'ISTP - 巨匠', advice: '感情的な話よりも具体的なアクションや事実の共有に集中する。' }},
            ENFJ: { alias: '主人公', group: '外交官', description: '人とのつながりが人生の軸。人々を鼓舞するカリスマ性を持ったリーダータイプ。', strengths: '共感力が非常に高く、周囲の潜在能力を引き出す。', weaknesses: '他人の問題を自分の問題として抱え込みすぎる。', relationships: '社交的で、コミュニティをまとめる調整役。', careerAdvice: '教師、コーチ、人事、広報、牧師・聖職者。', compatibility: { best: 'ISTP - 巨匠', worst: 'INTP - 論理学者' }, careerCompatibility: { bestBoss: 'ESFJ - 領事官', bestSubordinate: 'ENFP - 運動家' }, difficultRelationship: { type: 'INTP - 論理学者', advice: 'INTPの「感情抜きで議論したい」という要望を理解し、個人的な批判ではないことを確認する。' }},
            ENFP: { alias: '運動家', group: '外交官', description: '情熱的で創造力あふれる、社交的な自由人。生きることそのものを楽しむ天真爛漫なタイプ。', strengths: '新しい可能性や経験を探求し続ける好奇心。', weaknesses: '集中力が続かず、物事を最後までやり遂げられない。', relationships: '誰とでもフレンドリーで、刺激と笑いを求める。', careerAdvice: 'イベントプランナー、広告代理店、ジャーナリスト。', compatibility: { best: 'ISTJ - ロジスティシャン', worst: 'ISFJ - 擁護者' }, careerCompatibility: { bestBoss: 'ENTP - 討論者', bestSubordinate: 'ESFP - エンターテイナー' }, difficultRelationship: { type: 'ISFJ - 擁護者', advice: 'ISFJの安定を尊重し、急な計画変更は避けるか丁寧に説明する。' }},
            ISFJ: { alias: '擁護者', group: '番人', description: '控えめで温厚な、献身のプロフェッショナル。人を助けることに情熱を注ぐタイプ。', strengths: '細やかな気配りと献身的な姿勢。', weaknesses: '自己主張が苦手で、ストレスを溜め込みがち。', relationships: '温かく、縁の下の力持ちとしてサポート。', careerAdvice: '看護師、医療従事者、保育士、秘書。', compatibility: { best: 'ENTJ - 指揮官', worst: 'INTP - 論理学者' }, careerCompatibility: { bestBoss: 'ISTJ - ロジスティシャン', bestSubordinate: 'ISFJ - 擁護者' }, difficultRelationship: { type: 'INTP - 論理学者', advice: 'INTPの冷たい論理的な質問を個人的な攻撃と受け取らず、「問題解決のため」と割り切る。' }},
            ESTJ: { alias: '幹部', group: '番人', description: 'ルールと秩序を重んじる、信頼と責任のリーダー。実践的でリーダーシップがある。', strengths: '組織力と実行力。', weaknesses: '柔軟性の欠如。', relationships: '忠誠心があり、責任を果たす。', careerAdvice: '経営者、管理職、軍人、銀行の支店長。', compatibility: { best: 'INFP - 仲介者', worst: 'INFJ - 提唱者' }, careerCompatibility: { bestBoss: 'ESTJ - 幹部', bestSubordinate: 'ISTJ - ロジスティシャン' }, difficultRelationship: { type: 'INFJ - 提唱者', advice: '「目的を達成することで、結果的に多くの人を救える」といった文脈で共感を示す。' } },
            ESFJ: { alias: '領事官', group: '番人', description: '社交的で協力的、調和を重視するお世話好き。周囲を支えることで自らも喜びを感じる。', strengths: '周囲の感情に敏感で、チームをまとめる。', weaknesses: '批判に弱く、周囲からの評価を気にしすぎる。', relationships: '社交的で、和やかな雰囲気を作る。', careerAdvice: '教師、セールス、イベント企画、医療事務、人事。', compatibility: { best: 'ISTP - 巨匠', worst: 'INTJ - 建築家' }, careerCompatibility: { bestBoss: 'ENFJ - 主人公', bestSubordinate: 'ESFP - エンターテイナー' }, difficultRelationship: { type: 'INTJ - 建築家', advice: '具体的な状況を論理的に説明し、結論を先に伝える。' } },
            ISTP: { alias: '巨匠', group: '探検家', description: 'あらゆる道具を使いこなし、静かに我が道を進む問題解決者。大胆で実践重視のタイプ。', strengths: '柔軟で現実的、問題解決のプロ。', weaknesses: 'ルールや長期的なコミットメントを嫌う。', relationships: '自由と独立を尊重し、束縛を嫌う。', careerAdvice: 'エンジニア、職人、パイロット、メカニック、外科医。', compatibility: { best: 'ENFJ - 主人公', worst: 'INFJ - 提唱者' }, careerCompatibility: { bestBoss: 'ISTP - 巨匠', bestSubordinate: 'ESTP - 起業家' }, difficultRelationship: { type: 'INFJ - 提唱者', advice: '彼らの理想を「解決すべき課題」として捉え、具体的な手段を提案する。' } },
            ISFP: { alias: '冒険家', group: '探検家', description: '柔軟性がある魅力的なアーティスト。穏やかでマイペース、感受性豊かな自由人。', strengths: '感受性豊かで芸術的な才能。', weaknesses: '計画を立てるのが苦手で、衝動的な行動が多い。', relationships: '感情を共有できる、温かい関係を求める。', careerAdvice: 'デザイナー、ミュージシャン、自然ガイド。', compatibility: { best: 'ENTJ - 指揮官', worst: 'ESTJ - 幹部' }, careerCompatibility: { bestBoss: 'INFP - 仲介者', bestSubordinate: 'ESFP - エンターテイナー' }, difficultRelationship: { type: 'ENTJ - 指揮官', advice: '「自分のペースで進めるための期限延長」など、具体的な要望だけを伝える。' } },
            ESTP: { alias: '起業家', group: '探検家', description: '常にエネルギッシュな、人生の冒険者。賢くて洞察力が非常に優れている。', strengths: '行動力があり、即座の判断力と対応力。', weaknesses: '退屈を嫌い、リスクを冒しすぎる傾向。', relationships: '楽しく刺激的な体験を共有する関係を好む。', careerAdvice: 'セールス、起業家、トレーダー、緊急対応職。', compatibility: { best: 'INFJ - 提唱者', worst: 'ISFJ - 擁護者' }, careerCompatibility: { bestBoss: 'ESTP - 起業家', bestSubordinate: 'ISTP - 巨匠' }, difficultRelationship: { type: 'ISFJ - 擁護者', advice: '新しい挑戦を促す際は、安全策やリスクの具体的な回避方法を同時に提示する。' } },
        };

        const resultArea = document.getElementById('result-area');
        const quizArea = document.getElementById('quiz-area');
        const detailArea = document.getElementById('detail-area');
        
        function handleAnswer(answer) {
            result += answer;
            questionIndex++;
            showQuestion();
        }

        function showQuestion() {
            detailArea.classList.add('hidden');
            resultArea.classList.add('hidden');
            quizArea.classList.remove('hidden');

            const questionDisplay = document.getElementById('current-question-text');
            const buttonsArea = document.getElementById('buttons-area');
            
            if (questionIndex < questions.length) {
                const q = questions[questionIndex];
                questionDisplay.textContent = `Q${questionIndex + 1}: ${q.text}`;
                buttonsArea.innerHTML = ''; 

                q.options.forEach(option => {
                    const button = document.createElement('button');
                    button.textContent = option.label;
                    button.className = 'w-full py-4 px-6 bg-primary-blue text-white font-bold rounded-lg shadow-md hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-primary-blue focus:ring-opacity-50';
                    
                    button.onclick = function() {
                        handleAnswer(option.value);
                    };

                    buttonsArea.appendChild(button);
                });
            } else {
                showResultSummary();
            }
        }

        function showResultSummary() {
            quizArea.classList.add('hidden');
            resultArea.classList.remove('hidden');

            const type = result;
            const data = results[type];
            
            resultArea.innerHTML = '';

            if (!data) {
                resultArea.innerHTML = '<p class="text-red-500 text-center">エラー: 診断結果が見つかりませんでした。</p>';
                return;
            }

            // 1. タイトルエリア
            const titleDiv = document.createElement('div');
            titleDiv.className = 'text-center mb-6';
            titleDiv.innerHTML = `
                <h2 class="text-4xl font-extrabold text-green-600 mb-2">診断完了！</h2>
                <p class="text-gray-600 text-lg">あなたの性格タイプは...</p>
            `;
            resultArea.appendChild(titleDiv);

            // 2. 結果カード
            const cardDiv = document.createElement('div');
            cardDiv.className = 'bg-green-50 border-l-8 border-green-500 p-6 rounded-xl shadow-lg';
            cardDiv.innerHTML = `
                <p class="text-xl font-bold text-green-700 mb-1">${data.group} (グループ)</p>
                <h3 class="text-5xl font-black text-gray-900 mb-3">${type} - ${data.alias}</h3>
                <p class="text-gray-700 italic leading-relaxed">"${data.description}"</p>
            `;
            resultArea.appendChild(cardDiv);

            // 3. 詳細ボタン
            const buttonContainer = document.createElement('div');
            buttonContainer.className = 'text-center mt-8';

            const detailsButton = document.createElement('button');
            detailsButton.className = 'w-full py-4 px-6 bg-primary-orange text-white font-bold rounded-lg shadow-xl hover:bg-yellow-600 transition-colors text-lg focus:outline-none focus:ring-2 focus:ring-primary-orange focus:ring-opacity-50';
            detailsButton.textContent = '🔥 究極の戦略ガイドへ進む';

            detailsButton.onclick = function() {
                showDetails(type);
            };

            buttonContainer.appendChild(detailsButton);
            resultArea.appendChild(buttonContainer);
        }

        function showDetails(type) {
            const data = results[type];
            detailArea.classList.remove('hidden');
            const detailContent = document.getElementById('detail-content');
            detailContent.innerHTML = '';

            // --- ユーティリティ関数 ---
            const createSection = (title, icon, contentHTML) => {
                const section = document.createElement('div');
                section.className = 'border-t border-gray-200 pt-4';
                section.innerHTML = `
                    <h4 class="text-xl font-bold text-gray-800 mb-3 flex items-center">
                        <span class="text-2xl mr-2">${icon}</span> ${title}
                    </h4>
          ｇ
