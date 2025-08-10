<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>ひとり残されたあなたへ｜タイプ診断</title>
  <meta name="description" content="感情や今の状態に寄り添う5タイプ診断。数分で結果がわかります。" />
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;600;700&display=swap" rel="stylesheet">
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    :root {
      --brand:#6b8afd; /* 優しい青 */
      --brand-2:#a7b8ff; /* 補助の淡い青 */
      --ink:#1f2937;
    }
    body { font-family: 'Noto Sans JP', system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, "Apple Color Emoji", "Segoe UI Emoji"; color: var(--ink); }
    .card { box-shadow: 0 10px 30px rgba(0,0,0,.08); }
    .fade-enter { opacity: 0; transform: translateY(6px); }
    .fade-enter-active { opacity: 1; transform: translateY(0); transition: all .25s ease; }
    .opt-btn:focus-visible { outline: 3px solid var(--brand-2); outline-offset: 3px; }
  </style>
</head>
<body class="min-h-screen bg-gradient-to-b from-white to-slate-50">
  <main class="mx-auto max-w-3xl px-4 py-10">
    <!-- Top Page -->
    <section id="top-page" class="card bg-white rounded-2xl p-8 md:p-10">
      <div class="flex items-start gap-4">
        <div class="shrink-0 h-12 w-12 rounded-2xl bg-indigo-100 flex items-center justify-center"><span class="text-2xl">🕊️</span></div>
        <div class="flex-1">
          <h1 class="text-2xl md:text-3xl font-bold leading-tight mb-3">ひとり残されたあなたへ｜タイプ診断</h1>
          <p class="text-slate-600 mb-6">今のあなたの心に寄り添うための、やさしい5タイプ診断です。全10問・所要約3分。直感でお選びください。</p>
          <ul class="text-slate-500 text-sm list-disc pl-5 space-y-1 mb-6">
            <li>回答はブラウザ内で処理され、サーバーに送信されません。</li>
            <li>途中で離れても、もう一度はじめからやり直せます。</li>
          </ul>
          <button id="start-button" class="inline-flex items-center gap-2 rounded-xl bg-indigo-600 text-white px-6 py-3 font-semibold hover:bg-indigo-500 transition focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-400">
            はじめる
          </button>
        </div>
      </div>
    </section>

    <!-- Quiz Area -->
    <section id="quiz-area" class="hidden card bg-white rounded-2xl p-8 md:p-10" aria-live="polite" aria-atomic="true">
      <!-- Progress -->
      <div class="mb-6">
        <div class="flex items-center justify-between mb-2">
          <p id="progress-label" class="text-sm text-slate-500">進捗：<span id="progress-count">0</span>/10</p>
          <button id="restart-button" class="text-sm text-indigo-600 hover:underline">最初からやり直す</button>
        </div>
        <div class="h-2 w-full bg-slate-100 rounded-full overflow-hidden">
          <div id="progress-bar" class="h-full w-0 bg-indigo-500 transition-all duration-300"></div>
        </div>
      </div>

      <!-- Question -->
      <h2 id="question-container" class="text-xl md:text-2xl font-semibold leading-relaxed mb-6"></h2>

      <!-- Options -->
      <div id="options-container" class="grid gap-3"></div>
    </section>

    <!-- Result Area -->
    <section id="result-area" class="hidden card bg-white rounded-2xl p-8 md:p-10">
      <div class="flex items-start gap-4">
        <div class="shrink-0 h-12 w-12 rounded-2xl bg-indigo-100 flex items-center justify-center"><span class="text-2xl">🌿</span></div>
        <div class="flex-1">
          <h2 id="result-title" class="text-2xl md:text-3xl font-bold mb-3">結果タイトル</h2>
          <p id="result-description" class="text-slate-700 leading-relaxed mb-5"></p>
          <div class="bg-indigo-50 border border-indigo-100 rounded-xl p-4 mb-6">
            <h3 class="font-semibold text-indigo-700 mb-2">今のあなたへのおすすめ</h3>
            <p id="recommendation-text" class="text-indigo-900/90"></p>
          </div>
          <div class="prose prose-slate max-w-none">
            <p id="encouragement-message" class="leading-relaxed"></p>
          </div>
          <div class="mt-8 flex flex-wrap gap-3">
            <button id="retry-button" class="rounded-xl bg-indigo-600 text-white px-5 py-3 font-semibold hover:bg-indigo-500 transition">もう一度診断する</button>
            <button id="copy-result" class="rounded-xl bg-white border px-5 py-3 font-semibold hover:bg-slate-50 transition">結果をコピー</button>
          </div>
        </div>
      </div>
    </section>

    <p class="text-center text-xs text-slate-400 mt-8">© ひとり残されたあなたへ チーム</p>
  </main>

  <script>
  document.addEventListener('DOMContentLoaded', () => {
    const startButton = document.getElementById('start-button');
    const topPage = document.getElementById('top-page');
    const quizArea = document.getElementById('quiz-area');
    const questionContainer = document.getElementById('question-container');
    const optionsContainer = document.getElementById('options-container');
    const resultArea = document.getElementById('result-area');
    const resultTitle = document.getElementById('result-title');
    const resultDescription = document.getElementById('result-description');
    const recommendationText = document.getElementById('recommendation-text');
    const encouragementMessage = document.getElementById('encouragement-message');
    const progressBar = document.getElementById('progress-bar');
    const progressCount = document.getElementById('progress-count');
    const restartButton = document.getElementById('restart-button');
    const retryButton = document.getElementById('retry-button');
    const copyButton = document.getElementById('copy-result');

    let currentQuestionIndex = 0;
    const typeScores = { 1: 0, 2: 0, 3: 0, 4: 0, 5: 0 };

    const questions = [
        {
            question: "Q1. 忙しい毎日のふとした瞬間に、どんな感情が心に浮かびますか？",
            options: [
                { text: "「どうして私だけ…」という、やり場のない寂しさ", type: 1 },
                { text: "「このままでいいのかな…」という、漠然とした不安や焦り", type: 2 },
                { text: "「私がしっかりしなきゃ…」と、自分を奮い立たせる使命感", type: 3 },
                { text: "「もう何もかも嫌だ…」と、投げ出したくなるような無力感", type: 4 },
                { text: "「私のせいだ」と自分を責めてしまう気持ち", type: 5 }
            ]
        },
        {
            question: "Q2. 自分の未来について考えるとき、最も強く感じることは？",
            options: [
                { text: "誰かそばで支えてくれる人がいてくれたらいいのに", type: 1 },
                { text: "自分に何ができるか、新しいことを学んでみたい", type: 2 },
                { text: "どんな困難も、きっと乗り越えられるはず", type: 3 },
                { text: "ただただ、穏やかに過ごしたい", type: 4 },
                { text: "「私がちゃんとしないと」と、子どもへの責任感に押しつぶされそうになる", type: 5 }
            ]
        },
        {
            question: "Q3. 誰かに悩みを話すとしたら、どんな言葉で伝えたいですか？",
            options: [
                { text: "「本当はつらかったんだよ」と、ありのままの気持ちを打ち明けたい", type: 1 },
                { text: "「私はこれからどうしたらいいんだろう」と、具体的なアドバイスが欲しい", type: 2 },
                { text: "「私、最近頑張ってるんだ」と、努力を認めてもらいたい", type: 3 },
                { text: "誰にも話さず、自分の心の中でそっと整理したい", type: 4 },
                { text: "「あの時、もっと…」と、後悔を打ち明けたい", type: 5 }
            ]
        },
        {
            question: "Q4. パートナーとの思い出を振り返る時、どんな気持ちになりますか？",
            options: [
                { text: "楽しかった過去の記憶に、胸が締め付けられるように切なくなる", type: 1 },
                { text: "「こうすればよかった」と、自分を責めてしまう", type: 2 },
                { text: "「たくさんの愛をもらっていたな」と、感謝の気持ちが湧いてくる", type: 3 },
                { text: "あまり考えないようにしている。思い出すとつらくなるから", type: 4 },
                { text: "「どうしてこんなことになってしまったの？」と、問いかける", type: 5 }
            ]
        },
        {
            question: "Q5. 子育てで大変な時、「自分」がどうなることを一番望みますか？",
            options: [
                { text: "「一人じゃないよ」と誰かにそっと肩を叩いてもらいたい", type: 1 },
                { text: "「よく頑張ってるね」と自分を褒めてあげられるようになりたい", type: 2 },
                { text: "子どもに優しく接したいのに、なぜか強く当たってしまう自分を変えたい", type: 3 },
                { text: "ただひたすらに、何もかも忘れてゆっくり休みたい", type: 4 },
                { text: "パートナーのせいで、こんなことになってしまったと思ってしまう", type: 5 }
            ]
        },
        {
            question: "Q6. 友人や知人がSNSで楽しそうな様子をアップしているのを見たとき、どう感じますか？",
            options: [
                { text: "いいなと思う反面、なぜか孤独を感じてしまう", type: 1 },
                { text: "私は私、と割り切り、自分の人生を見つめ直すきっかけにする", type: 2 },
                { text: "羨ましい気持ちより、「私も頑張らなきゃ」とモチベーションになる", type: 3 },
                { text: "見ないようにする。疲れてしまうから", type: 4 },
                { text: "「あの人だったら、こんなときどう思うんだろう？」と考える", type: 5 }
            ]
        },
        {
            question: "Q7. パートナーの死因について、誰かに聞かれた時、どう感じますか？",
            options: [
                { text: "聞かれることが怖い。どう答えたらいいか、いつも悩んでしまう", type: 1 },
                { text: "「これも私自身の成長の機会だ」と、前向きな意味を見出したい", type: 2 },
                { text: "相手に心配をかけたくないから、つい元気なふりをしてしまう", type: 3 },
                { text: "誰も私のことを深く聞かないでほしいと願う", type: 4 },
                { text: "「あの人は決して孤独ではなかった」と、自分の気持ちを伝えたい", type: 5 }
            ]
        },
        {
            question: "Q8. 感情が揺れ動く時、それをどう処理することが多いですか？",
            options: [
                { text: "涙を流すなど、感情を外に出すことで心を落ち着かせる", type: 1 },
                { text: "なぜそう感じたのか、原因を突き詰めて考える", type: 2 },
                { text: "家事をしたり、体を動かしたりして、気分転換をする", type: 3 },
                { text: "感情に気づかないふりをして、やり過ごす", type: 4 },
                { text: "「どうして私を置いていったの？」と、心の中で問いかける", type: 5 }
            ]
        },
        {
            question: "Q9. 新しいことを始めようとするとき、あなたの背中を押すものは何ですか？",
            options: [
                { text: "誰かから「やってみたら？」と後押しされること", type: 1 },
                { text: "「これをやれば変われるかも」という期待感", type: 2 },
                { text: "「私が頑張ることで、子どももきっと喜ぶ」という気持ち", type: 3 },
                { text: "「今の状態から抜け出したい」という強い衝動", type: 4 },
                { text: "「あの人もきっと応援してくれている」という思い", type: 5 }
            ]
        },
        {
            question: "Q10. 仕事やキャリアについて考えたとき、今一番頭に浮かぶのは？",
            options: [
                { text: "信頼できる仲間と一緒に、何か新しいことを始めたい", type: 1 },
                { text: "働きたい気持ちはあるけれど、今は心と体の調子を整えることが先決だ", type: 2 },
                { text: "もう一度、社会で活躍して子どもに誇らしい姿を見せたい", type: 3 },
                { text: "何もかも忘れて、ゆっくりと休む時間が欲しい", type: 4 },
                { text: "あの人がいた頃、仕事や働き方についてもっと話しておけばよかった", type: 5 }
            ]
        }
    ];

    const results = {
        1: {
            title: "タイプ1. 感情を分かち合う「共感の処方箋」タイプ",
            description: "誰にも話せない孤独や寂しさを感じ、心の中の感情を誰かと分かち合いたいと願っているあなた。一人で頑張ることに疲れてしまい、周囲にSOSを出せないでいるかもしれません。本当は「つらい」と泣き言を言える場所を探しています。",
            recommendation: "自分の感情をありのままに話せる相手を探すこと。同じような経験をした人たちのコミュニティに参加してみるのも良いでしょう。勇気を出して一歩踏み出すことで、孤独から解放され、心からの安らぎを得られます。"
        },
        2: {
            title: "タイプ2. 自分の心を育む「内省の処方箋」タイプ",
            description: "漠然とした不安や焦りを抱え、「これからどう生きていけばいいのか」と自問自答しているあなた。未来への見通しが立たず、自分軸を見失いがちです。過去を振り返り、「こうすればよかった」と自分を責めてしまうことも。でも、それは自分と真剣に向き合いたいという心の現れです。",
            recommendation: "自分の心を深く見つめ直す時間を持つこと。感情や思考をノートに書き出してみたり、本を読んでヒントを得たりするのも効果的です。自分の価値観や本当に大切にしたいことを見つけることで、未来への道筋が見えてきます。"
        },
        3: {
            title: "タイプ3. 頑張る自分を認める「行動の処方箋」タイプ",
            description: "子どものため、未来のために「私が頑張らなきゃ」と自分を奮い立たせているあなた。感情を後回しにし、行動することで前向きになろうとします。弱音を吐くことを良しとせず、頑張る自分を認めてもらいたい、という強い思いを秘めています。",
            recommendation: "努力している自分を客観的に認め、心から褒めてあげること。完璧を求めすぎず、時には誰かに甘えたり、頼ったりする時間も大切です。頑張ってきた自分を肯定することで、本当の意味での強さが育まれます。"
        },
        4: {
            title: "タイプ4. 心を解き放つ「休息の処方箋」タイプ",
            description: "もう何もかも嫌だと感じたり、無力感に襲われたりすることがあるあなた。心身ともに疲れ果て、自分でも気づかないうちに感情にフタをしてしまっているかもしれません。今は「頑張る」ことよりも、心を解き放ち、ただひたすらに休むことが何よりも大切です。",
            recommendation: "感情を無理にコントロールしようとせず、心と体を休ませることを最優先してください。一人で静かに過ごす時間を作ったり、好きなことに没頭したりと、自分を甘やかす時間を取りましょう。心に余裕ができた時、また少しずつ前に進む力が湧いてきます。"
        },
        5: {
            title: "タイプ5. 後悔を乗り越える「対話の処方箋」タイプ",
            description: "パートナーとの間に未完了の感情や、伝えきれなかった思いがあるあなた。心の奥底に後悔や罪悪感を抱えており、それが未来へ進む足を止めてしまっているようです。もう一度ちゃんと話したい、問いかけたいという心の声に耳を傾ける時が来ています。",
            recommendation: "実際に声に出して、パートナーに話しかけてみることです。日記に思いを綴ったり、手紙を書いてみたりするのも良いでしょう。心の中で対話を重ねることで、伝えきれなかった思いを整理し、自分を許し、心のわだかまりを解き放つことができます。"
        }
    };

    const encouragement = "診断はいかがでしたか？<br><br>もし今、あなたが孤独を感じていたり、未来への不安を抱えていたりしても、決して一人ではありません。<br><br>あなたの感情に蓋をせず、ありのままのあなたを受け入れることから、一歩ずつ前に進んでいくことができます。勇気づけとは、特別なことではありません。今のあなたの「頑張り」を認めることから始まります。<br><br>私たち『ひとり残されたあなたへ』チームは、いつでもあなたの心の声に耳を傾け、隣でそっと寄り添います。<br><br>あなたの心が穏やかになることを、心から願っています。";

    // UI helpers
    function show(el) { el.classList.remove('hidden'); el.classList.add('fade-enter'); requestAnimationFrame(() => { el.classList.add('fade-enter-active'); }); setTimeout(()=>{ el.classList.remove('fade-enter','fade-enter-active'); }, 280); }
    function hide(el) { el.classList.add('hidden'); }

    function resetState() {
      currentQuestionIndex = 0;
      for (const k in typeScores) typeScores[k] = 0;
      optionsContainer.innerHTML = '';
      updateProgressBar();
      progressCount.textContent = '0';
    }

    function updateProgressBar() {
      const progress = (currentQuestionIndex / questions.length) * 100;
      progressBar.style.width = `${progress}%`;
      progressCount.textContent = String(currentQuestionIndex);
    }

    function renderOptions(opts) {
      optionsContainer.innerHTML = '';
      opts.forEach((option) => {
        const btn = document.createElement('button');
        btn.type = 'button';
        btn.className = 'opt-btn text-left w-full rounded-xl border p-4 hover:bg-slate-50 transition focus:outline-none';
        btn.innerHTML = `<span class="block font-semibold">${option.text}</span>`;
        btn.setAttribute('aria-label', option.text);
        btn.addEventListener('click', () => selectAnswer(option.type));
        optionsContainer.appendChild(btn);
      });
    }

    function displayQuestion() {
      if (currentQuestionIndex < questions.length) {
        const q = questions[currentQuestionIndex];
        questionContainer.textContent = q.question;
        renderOptions(q.options);
        updateProgressBar();
      } else {
        showResult();
      }
    }

    function selectAnswer(type) {
      typeScores[type]++;
      currentQuestionIndex++;
      displayQuestion();
    }

    function showResult() {
      hide(quizArea);
      const [resultType, result] = computeResult();
      resultTitle.textContent = result.title;
      resultDescription.textContent = result.description;
      recommendationText.textContent = result.recommendation;
      encouragementMessage.innerHTML = encouragement;
      show(resultArea);
    }

    function computeResult() {
      let resultType = 1, maxScore = -1;
      for (const type in typeScores) {
        const score = typeScores[type];
        if (score > maxScore) { maxScore = score; resultType = parseInt(type, 10); }
      }
      return [resultType, results[resultType]];
    }

    // Event bindings
    startButton.addEventListener('click', () => {
      hide(topPage);
      resetState();
      show(quizArea);
      displayQuestion();
    });

    restartButton.addEventListener('click', () => {
      resetState();
      displayQuestion();
    });

    if (retryButton) {
      retryButton.addEventListener('click', () => {
        hide(resultArea);
        resetState();
        show(quizArea);
        displayQuestion();
      });
    }

    if (copyButton) {
      copyButton.addEventListener('click', async () => {
        const text = `【診断結果】\n${resultTitle.textContent}\n\n${resultDescription.textContent}\n\nおすすめ：${recommendationText.textContent}`;
        try {
          await navigator.clipboard.writeText(text);
          copyButton.textContent = 'コピーしました';
          setTimeout(()=> copyButton.textContent = '結果をコピー', 2000);
        } catch (e) {
          alert('コピーに失敗しました');
        }
      });
    }
  });
  </script>
</body>
</html>
