<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Automation Trainer</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Golos+Text:wght@400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0f0f11;
  --bg2: #16161a;
  --bg3: #1e1e24;
  --border: rgba(255,255,255,0.08);
  --border2: rgba(255,255,255,0.14);
  --text: #e8e6f0;
  --muted: #8a8799;
  --accent: #7c6ef5;
  --accent2: #5b4fd8;
  --green: #3ecf8e;
  --green-bg: rgba(62,207,142,0.1);
  --red: #f56565;
  --red-bg: rgba(245,101,101,0.1);
  --amber: #f5a623;
  --amber-bg: rgba(245,166,35,0.1);
  --radius: 10px;
  --radius-lg: 16px;
  --mono: 'JetBrains Mono', monospace;
  --sans: 'Golos Text', sans-serif;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: var(--sans); background: var(--bg); color: var(--text); min-height: 100vh; font-size: 15px; line-height: 1.6; }

/* Layout */
.app { display: flex; min-height: 100vh; }
.sidebar { width: 260px; background: var(--bg2); border-right: 1px solid var(--border); padding: 24px 0; flex-shrink: 0; position: sticky; top: 0; height: 100vh; overflow-y: auto; display: flex; flex-direction: column; }
.main { flex: 1; padding: 40px; max-width: 760px; }
@media (max-width: 720px) {
  .app { flex-direction: column; }
  .sidebar { width: 100%; height: auto; position: relative; padding: 16px 0 8px; }
  .main { padding: 20px 16px; }
}

/* Logo */
.logo { padding: 0 20px 24px; border-bottom: 1px solid var(--border); margin-bottom: 16px; }
.logo-title { font-size: 15px; font-weight: 600; color: var(--text); letter-spacing: -0.3px; }
.logo-sub { font-size: 12px; color: var(--muted); margin-top: 2px; }

/* Progress */
.sidebar-progress { padding: 0 20px; margin-bottom: 20px; }
.progress-label { font-size: 11px; color: var(--muted); margin-bottom: 6px; display: flex; justify-content: space-between; }
.progress-track { height: 4px; background: var(--border2); border-radius: 2px; }
.progress-fill { height: 4px; border-radius: 2px; background: var(--accent); transition: width .5s ease; }

/* Nav modules */
.nav-section { padding: 0 12px; margin-bottom: 4px; }
.nav-label { font-size: 10px; font-weight: 600; color: var(--muted); letter-spacing: .8px; text-transform: uppercase; padding: 0 8px; margin-bottom: 6px; }
.nav-item { display: flex; align-items: center; gap: 10px; padding: 9px 10px; border-radius: var(--radius); cursor: pointer; transition: background .15s; margin-bottom: 2px; border: none; background: none; width: 100%; text-align: left; }
.nav-item:hover { background: var(--bg3); }
.nav-item.active { background: rgba(124,110,245,0.15); }
.nav-item.locked { opacity: .4; cursor: default; }
.nav-dot { width: 20px; height: 20px; border-radius: 50%; border: 1.5px solid var(--border2); display: flex; align-items: center; justify-content: center; font-size: 10px; flex-shrink: 0; }
.nav-dot.done { background: var(--green); border-color: var(--green); color: #fff; }
.nav-dot.active { border-color: var(--accent); color: var(--accent); }
.nav-dot.locked { border-style: dashed; }
.nav-text { font-size: 13px; color: var(--text); line-height: 1.3; }
.nav-text small { display: block; font-size: 11px; color: var(--muted); }

/* Stats at bottom of sidebar */
.sidebar-stats { margin-top: auto; padding: 16px 20px 0; border-top: 1px solid var(--border); display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
.stat-mini { background: var(--bg3); border-radius: var(--radius); padding: 10px; text-align: center; }
.stat-mini-val { font-size: 20px; font-weight: 600; color: var(--text); }
.stat-mini-label { font-size: 10px; color: var(--muted); margin-top: 1px; }

/* Screens */
.screen { display: none; animation: fadeIn .25s ease; }
.screen.active { display: block; }
@keyframes fadeIn { from { opacity:0; transform: translateY(6px); } to { opacity:1; transform: translateY(0); } }

/* Lesson content */
.lesson-tag { font-size: 11px; font-weight: 600; color: var(--accent); text-transform: uppercase; letter-spacing: .8px; margin-bottom: 10px; }
.lesson-title { font-size: 26px; font-weight: 600; color: var(--text); letter-spacing: -0.5px; margin-bottom: 6px; line-height: 1.2; }
.lesson-desc { font-size: 14px; color: var(--muted); margin-bottom: 28px; line-height: 1.6; }
.lesson-meta { display: flex; gap: 12px; margin-bottom: 28px; flex-wrap: wrap; }
.meta-chip { font-size: 12px; color: var(--muted); background: var(--bg3); border: 1px solid var(--border); padding: 4px 10px; border-radius: 20px; }

/* Content blocks */
.block { margin-bottom: 20px; }
.block-theory { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: 18px 20px; }
.block-theory h3 { font-size: 13px; font-weight: 600; color: var(--muted); text-transform: uppercase; letter-spacing: .6px; margin-bottom: 10px; }
.block-theory p { font-size: 14px; color: var(--text); line-height: 1.7; margin-bottom: 10px; }
.block-theory p:last-child { margin-bottom: 0; }
.block-theory b { font-weight: 600; color: var(--text); }
.block-example { border-left: 2px solid var(--accent); background: rgba(124,110,245,0.06); border-radius: 0 var(--radius) var(--radius) 0; padding: 14px 16px; }
.block-example .ex-label { font-size: 11px; font-weight: 600; color: var(--accent); text-transform: uppercase; letter-spacing: .6px; margin-bottom: 8px; }
.block-example p { font-size: 14px; color: var(--text); line-height: 1.65; }
.block-code { background: #13131a; border: 1px solid var(--border); border-radius: var(--radius-lg); overflow: hidden; }
.code-header { padding: 10px 16px; border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; }
.code-lang { font-size: 11px; font-weight: 600; color: var(--muted); text-transform: uppercase; letter-spacing: .6px; }
.code-dots { display: flex; gap: 5px; }
.code-dot { width: 9px; height: 9px; border-radius: 50%; background: var(--border2); }
.code-body { padding: 16px; font-family: var(--mono); font-size: 13px; line-height: 1.7; color: #c9c7d8; overflow-x: auto; white-space: pre; }
.kw { color: #c792ea; }
.str { color: #c3e88d; }
.key { color: #82aaff; }
.num { color: #f78c6c; }
.cmt { color: #546e7a; font-style: italic; }
.url { color: #89ddff; }

/* Quiz */
.quiz-section { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: 20px; margin-top: 24px; }
.quiz-label { font-size: 11px; font-weight: 600; color: var(--amber); text-transform: uppercase; letter-spacing: .6px; margin-bottom: 12px; }
.quiz-q { font-size: 15px; font-weight: 500; color: var(--text); margin-bottom: 16px; line-height: 1.5; }
.quiz-opts { display: flex; flex-direction: column; gap: 8px; }
.quiz-opt { background: var(--bg3); border: 1px solid var(--border); border-radius: var(--radius); padding: 12px 14px; cursor: pointer; font-size: 14px; color: var(--text); transition: all .15s; text-align: left; font-family: var(--sans); width: 100%; }
.quiz-opt:hover { border-color: var(--accent); background: rgba(124,110,245,0.08); color: var(--text); }
.quiz-opt.correct { border-color: var(--green); background: var(--green-bg); color: var(--green); }
.quiz-opt.wrong { border-color: var(--red); background: var(--red-bg); color: var(--red); }
.quiz-opt:disabled { cursor: default; }
.quiz-feedback { margin-top: 14px; padding: 12px 14px; border-radius: var(--radius); font-size: 14px; line-height: 1.5; display: none; }
.quiz-feedback.show { display: block; }
.quiz-feedback.ok { background: var(--green-bg); color: var(--green); border: 1px solid rgba(62,207,142,0.2); }
.quiz-feedback.no { background: var(--red-bg); color: var(--red); border: 1px solid rgba(245,101,101,0.2); }

/* Interview section */
.interview-section { margin-top: 24px; background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: 20px; }
.interview-label { font-size: 11px; font-weight: 600; color: var(--green); text-transform: uppercase; letter-spacing: .6px; margin-bottom: 12px; }
.interview-q { font-size: 14px; color: var(--muted); font-style: italic; line-height: 1.6; margin-bottom: 14px; padding: 12px 14px; background: var(--bg3); border-radius: var(--radius); border-left: 2px solid var(--green); }
.interview-q strong { color: var(--text); font-style: normal; display: block; font-size: 12px; font-weight: 600; text-transform: uppercase; letter-spacing: .5px; margin-bottom: 6px; color: var(--green); }
.interview-hint { font-size: 13px; color: var(--muted); background: var(--bg3); border-radius: var(--radius); padding: 12px 14px; margin-top: 10px; display: none; line-height: 1.6; }
.interview-hint.show { display: block; }

/* Buttons */
.btn { font-family: var(--sans); font-size: 14px; padding: 11px 20px; border-radius: var(--radius); border: 1px solid var(--border2); background: var(--bg3); color: var(--text); cursor: pointer; transition: all .15s; }
.btn:hover { background: var(--bg2); border-color: var(--border2); }
.btn-primary { background: var(--accent); border-color: var(--accent); color: #fff; }
.btn-primary:hover { background: var(--accent2); border-color: var(--accent2); }
.btn-sm { font-size: 12px; padding: 7px 14px; }
.btn-row { display: flex; gap: 10px; margin-top: 20px; flex-wrap: wrap; }

/* Home screen */
.home-hero { margin-bottom: 36px; }
.home-hero h1 { font-size: 32px; font-weight: 600; letter-spacing: -1px; color: var(--text); margin-bottom: 10px; line-height: 1.15; }
.home-hero p { font-size: 15px; color: var(--muted); line-height: 1.7; max-width: 520px; }
.accent-word { color: var(--accent); }
.modules-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 12px; margin-bottom: 32px; }
.module-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: 18px; cursor: pointer; transition: all .2s; }
.module-card:hover { border-color: var(--accent); transform: translateY(-2px); }
.module-card.locked { opacity: .4; cursor: default; transform: none; }
.module-card.done { border-left: 2px solid var(--green); }
.mc-icon { font-size: 22px; margin-bottom: 10px; }
.mc-title { font-size: 14px; font-weight: 600; color: var(--text); margin-bottom: 4px; }
.mc-desc { font-size: 12px; color: var(--muted); line-height: 1.5; margin-bottom: 10px; }
.mc-badge { font-size: 11px; padding: 3px 8px; border-radius: 4px; font-weight: 500; display: inline-block; }
.mc-badge.done { background: var(--green-bg); color: var(--green); }
.mc-badge.open { background: rgba(124,110,245,0.12); color: var(--accent); }
.mc-badge.locked { background: var(--bg3); color: var(--muted); }

/* Step nav */
.step-nav { display: flex; justify-content: space-between; align-items: center; margin-top: 28px; padding-top: 20px; border-top: 1px solid var(--border); }
.step-counter { font-size: 12px; color: var(--muted); }

/* Tip block */
.tip { background: var(--amber-bg); border: 1px solid rgba(245,166,35,0.2); border-radius: var(--radius); padding: 12px 14px; font-size: 13px; color: var(--amber); line-height: 1.6; margin-bottom: 14px; }
.tip strong { font-weight: 600; }

/* Celebration */
.celebrate { text-align: center; padding: 40px 20px; }
.celebrate-icon { font-size: 52px; margin-bottom: 16px; }
.celebrate h2 { font-size: 24px; font-weight: 600; margin-bottom: 10px; letter-spacing: -0.5px; }
.celebrate p { font-size: 15px; color: var(--muted); margin-bottom: 24px; }
</style>
</head>
<body>
<div class="app">

<!-- Sidebar -->
<aside class="sidebar">
  <div class="logo">
    <div class="logo-title">AI Automation Trainer</div>
    <div class="logo-sub">Junior → Middle за 2 недели</div>
  </div>
  <div class="sidebar-progress">
    <div class="progress-label"><span>Прогресс</span><span id="prog-pct">0%</span></div>
    <div class="progress-track"><div class="progress-fill" id="prog-bar" style="width:0%"></div></div>
  </div>
  <div class="nav-section">
    <div class="nav-label">Неделя 1 — Фундамент</div>
    <button class="nav-item active" onclick="goScreen('home')">
      <div class="nav-dot active">🏠</div>
      <div class="nav-text">Главная<small>Обзор программы</small></div>
    </button>
    <button class="nav-item" id="nav-l1" onclick="goLesson(1)">
      <div class="nav-dot" id="dot-l1">1</div>
      <div class="nav-text">API basics<small>HTTP, JSON, Postman</small></div>
    </button>
    <button class="nav-item" id="nav-l2" onclick="goLesson(2)">
      <div class="nav-dot" id="dot-l2">2</div>
      <div class="nav-text">AI-агенты<small>Как работают агенты</small></div>
    </button>
    <button class="nav-item" id="nav-l3" onclick="goLesson(3)">
      <div class="nav-dot" id="dot-l3">3</div>
      <div class="nav-text">OpenAI API<small>Запросы и ответы</small></div>
    </button>
    <button class="nav-item locked" id="nav-l4" onclick="goLesson(4)">
      <div class="nav-dot locked" id="dot-l4">4</div>
      <div class="nav-text">n8n Automation<small>Workflow без кода</small></div>
    </button>
    <button class="nav-item locked" id="nav-l5" onclick="goLesson(5)">
      <div class="nav-dot locked" id="dot-l5">5</div>
      <div class="nav-text">Telegram Bot<small>Alert система</small></div>
    </button>
  </div>
  <div class="nav-section" style="margin-top:12px">
    <div class="nav-label">Неделя 2 — Pet-project</div>
    <button class="nav-item locked" id="nav-l6" onclick="goLesson(6)">
      <div class="nav-dot locked" id="dot-l6">6</div>
      <div class="nav-text">Marketplace API<small>Данные о товарах</small></div>
    </button>
    <button class="nav-item locked" id="nav-l7" onclick="goLesson(7)">
      <div class="nav-dot locked" id="dot-l7">7</div>
      <div class="nav-text">AI-анализ дефицита<small>Decision chain</small></div>
    </button>
    <button class="nav-item locked" id="nav-l8" onclick="goLesson(8)">
      <div class="nav-dot locked" id="dot-l8">8</div>
      <div class="nav-text">Mock Interview<small>Вопросы на позицию</small></div>
    </button>
  </div>
  <div class="sidebar-stats">
    <div class="stat-mini"><div class="stat-mini-val" id="stat-done">0</div><div class="stat-mini-label">уроков</div></div>
    <div class="stat-mini"><div class="stat-mini-val" id="stat-quiz">0</div><div class="stat-mini-label">ответов</div></div>
  </div>
</aside>

<!-- Main -->
<main class="main">

  <!-- HOME -->
  <div class="screen active" id="screen-home">
    <div class="home-hero">
      <h1>Стань <span class="accent-word">AI Automation</span> специалистом</h1>
      <p>Тренажёр от новичка до junior/middle за 14 дней. Теория + квизы + mock-интервью + pet-project.</p>
    </div>
    <div class="modules-grid" id="home-modules"></div>
    <div class="tip"><strong>Как работать:</strong> Проходи уроки по порядку. После каждого урока — квиз и вопрос как на интервью. Следующий урок открывается после правильного ответа.</div>
    <button class="btn btn-primary" onclick="goLesson(1)">Начать с урока 1 →</button>
  </div>

  <!-- LESSONS -->
  <div class="screen" id="screen-lesson"></div>

  <!-- DONE -->
  <div class="screen" id="screen-done">
    <div class="celebrate">
      <div class="celebrate-icon">🎉</div>
      <h2>Урок пройден!</h2>
      <p>Следующий урок разблокирован. Продолжай в том же темпе — ты на правильном пути.</p>
      <div class="btn-row" style="justify-content:center">
        <button class="btn" onclick="goScreen('home')">На главную</button>
        <button class="btn btn-primary" id="next-lesson-btn">Следующий урок →</button>
      </div>
    </div>
  </div>

</main>
</div>

<script>
const LESSONS = [
  {
    id: 1,
    title: "API basics",
    tag: "Урок 1 · Неделя 1",
    desc: "Главный строительный блок любой автоматизации. Без понимания API ты не сможешь связать ни один сервис.",
    time: "45 мин",
    theory: [
      {
        head: "Что такое API?",
        text: `<p>Представь ресторан. <b>Ты</b> — это твой код или n8n. <b>Официант</b> — это API. <b>Кухня</b> — это сторонний сервис (OpenAI, Telegram, маркетплейс).</p><p>Ты не идёшь на кухню сам. Ты говоришь официанту что хочешь — он уходит и возвращается с ответом. Тебе не важно как устроена кухня внутри.</p><p><b>API — это официант между твоим кодом и чужим сервисом.</b></p>`
      },
      {
        head: "4 части любого API-запроса",
        text: `<p><b>1. URL</b> — адрес куда идёт запрос<br><b>2. Method</b> — что ты хочешь сделать (GET = получить, POST = отправить)<br><b>3. Headers</b> — служебная информация, ключ доступа<br><b>4. Body</b> — данные которые ты передаёшь (только в POST)</p>`
      }
    ],
    example: {
      label: "Бизнес-кейс: Supply Chain",
      text: "Твой AI-агент делает GET-запрос к API маркетплейса → получает остатки товаров → делает POST к OpenAI → получает анализ дефицита → делает POST к Telegram API → менеджер получает alert. Каждая стрелка — это один API-запрос."
    },
    code: {
      lang: "HTTP Request",
      body: `<span class="kw">POST</span> <span class="url">https://api.openai.com/v1/chat/completions</span>
<span class="key">Authorization:</span> <span class="str">Bearer sk-ваш-ключ</span>
<span class="key">Content-Type:</span> <span class="str">application/json</span>

{
  <span class="key">"model"</span>: <span class="str">"gpt-4o-mini"</span>,
  <span class="key">"messages"</span>: [
    {
      <span class="key">"role"</span>: <span class="str">"user"</span>,
      <span class="key">"content"</span>: <span class="str">"Остаток: 3 шт, норма: 50 шт. Дефицит?"</span>
    }
  ]
}`
    },
    quiz: {
      q: "Менеджер хочет получать данные об остатках с маркетплейса каждый час. Какой HTTP-метод использует агент?",
      opts: ["POST — отправляем данные на маркетплейс", "GET — получаем данные с маркетплейса", "DELETE — удаляем старые остатки", "PUT — обновляем остатки"],
      correct: 1,
      feedback_ok: "Верно! GET используется для получения данных. Агент делает GET-запрос к API маркетплейса и получает JSON с остатками. POST — это когда ты сам отправляешь данные.",
      feedback_no: "Не совсем. GET — это 'получить данные'. Мы хотим получить остатки с маркетплейса, а не отправить что-то туда. POST используется когда ты сам передаёшь данные (например, запрос к OpenAI с текстом для анализа)."
    },
    interview: {
      q: "Объясни своими словами: что такое API и как оно используется в supply chain автоматизации?",
      hint: "Сильный ответ: «API — это интерфейс, через который сервисы обмениваются данными. В supply chain мы используем API маркетплейсов для получения остатков, API OpenAI для AI-анализа дефицита, и API Telegram для отправки алертов менеджерам. Всё это соединяется в автоматический workflow.»"
    }
  },
  {
    id: 2,
    title: "AI-агенты",
    tag: "Урок 2 · Неделя 1",
    desc: "Как AI принимает решения и почему агент — это больше чем просто ChatGPT.",
    time: "50 мин",
    theory: [
      {
        head: "Агент vs просто AI",
        text: `<p>Обычный AI (ChatGPT) — это <b>вопрос → ответ</b>. Ты спросил, он ответил, всё.</p><p><b>AI-агент</b> — это AI который может: принимать данные из внешних систем, принимать решения на основе этих данных, и <b>выполнять действия</b> (отправить алерт, записать в таблицу, создать задачу).</p><p>Агент работает в цикле: <b>Наблюдай → Думай → Действуй → Повтори</b></p>`
      },
      {
        head: "Из чего состоит агент",
        text: `<p><b>LLM</b> — мозг агента (GPT-4, Claude). Принимает решения.<br><b>Tools (инструменты)</b> — то что агент может делать (читать API, писать в Sheets, слать в Telegram).<br><b>Memory</b> — контекст и история (что уже было сделано).<br><b>Prompt</b> — инструкция: кто ты, что делать, как решать.</p>`
      }
    ],
    example: {
      label: "Агент мониторинга склада",
      text: "Каждый час агент: (1) читает остатки через API маркетплейса, (2) GPT-4 анализирует — есть ли дефицит по каждому SKU, (3) если риск высокий — агент сам отправляет алерт в Telegram и создаёт задачу в системе. Менеджер только получает готовое решение."
    },
    code: {
      lang: "Логика агента (псевдокод)",
      body: `<span class="cmt">// Агент запускается каждый час</span>
<span class="kw">function</span> <span class="key">runAgent</span>() {
  
  <span class="cmt">// 1. Наблюдай — получи данные</span>
  <span class="kw">const</span> stock = <span class="key">getFromMarketplaceAPI</span>();
  
  <span class="cmt">// 2. Думай — передай AI</span>
  <span class="kw">const</span> analysis = <span class="key">askOpenAI</span>(<span class="str">\`
    Вот остатки: \${stock}
    Есть ли дефицит? Какой риск-уровень?
    Ответь JSON: {risk, items, action}
  \`</span>);
  
  <span class="cmt">// 3. Действуй — если риск высокий</span>
  <span class="kw">if</span> (analysis.risk === <span class="str">"HIGH"</span>) {
    <span class="key">sendTelegramAlert</span>(analysis);
    <span class="key">writeToGoogleSheets</span>(analysis);
  }
}`
    },
    quiz: {
      q: "Чем AI-агент отличается от обычного запроса к ChatGPT?",
      opts: [
        "Агент работает быстрее",
        "Агент может сам выполнять действия: вызывать API, отправлять алерты, записывать данные",
        "Агент использует более умную модель",
        "Нет разницы, это одно и то же"
      ],
      correct: 1,
      feedback_ok: "Точно! Ключевое слово — действия. Агент не просто отвечает, он интегрирован с внешними сервисами и может выполнять задачи автономно. Именно это делает его полезным для автоматизации бизнес-процессов.",
      feedback_no: "Главное отличие — не в скорости или модели, а в возможности действовать. Агент подключён к внешним инструментам (API, базы данных, мессенджеры) и может автономно выполнять задачи без участия человека."
    },
    interview: {
      q: "Как бы ты объяснил руководителю: зачем нам AI-агент для мониторинга склада, если у нас уже есть Excel и менеджер проверяет остатки вручную?",
      hint: "Сильный ответ: «Агент работает 24/7 и реагирует в течение минут, а не часов. Он обрабатывает все SKU одновременно, а человек — нет. При дефиците агент сам эскалирует задачу, пока менеджер занят другим. Это снижает риск остановки продаж из-за человеческой ошибки или задержки реакции.»"
    }
  },
  {
    id: 3,
    title: "OpenAI API",
    tag: "Урок 3 · Неделя 1",
    desc: "Как правильно разговаривать с AI через API и строить эффективные промпты для агентов.",
    time: "55 мин",
    theory: [
      {
        head: "Структура запроса к OpenAI",
        text: `<p>Каждый запрос к OpenAI API состоит из <b>messages</b> — массива сообщений с ролями:</p><p><b>system</b> — инструкция для AI (кто он, что делать, в каком формате отвечать)<br><b>user</b> — входящие данные от пользователя или от агента<br><b>assistant</b> — предыдущие ответы AI (для контекста)</p><p>Качество ответа AI на <b>80% зависит от system-промпта</b>. Это главный навык AI PM.</p>`
      },
      {
        head: "Prompt Engineering для агентов",
        text: `<p>Хороший промпт для агента содержит: <b>роль</b> (ты — аналитик склада), <b>контекст</b> (вот данные), <b>задача</b> (найди дефицит), <b>формат ответа</b> (верни JSON с полями risk, items, action). Без указания формата AI отвечает произвольно — агент не сможет обработать ответ.</p>`
      }
    ],
    example: {
      label: "Промпт для анализа остатков",
      text: "System: «Ты аналитик цепочки поставок. Получаешь данные об остатках. Анализируй дефицит. Отвечай ТОЛЬКО JSON без лишнего текста.» — такой промпт даёт предсказуемый, машиночитаемый ответ, который агент может передать дальше в workflow."
    },
    code: {
      lang: "OpenAI API запрос",
      body: `{
  <span class="key">"model"</span>: <span class="str">"gpt-4o-mini"</span>,
  <span class="key">"messages"</span>: [
    {
      <span class="key">"role"</span>: <span class="str">"system"</span>,
      <span class="key">"content"</span>: <span class="str">"Ты аналитик склада. Получаешь JSON с остатками.
Найди товары с дефицитом (остаток < 20% от нормы).
Верни ТОЛЬКО JSON формат:
{
  'risk': 'HIGH|MEDIUM|LOW',
  'deficit_items': [...],
  'recommendation': '...'
}"</span>
    },
    {
      <span class="key">"role"</span>: <span class="str">"user"</span>,
      <span class="key">"content"</span>: <span class="str">"Остатки: [{'sku':'A1','stock':3,'norm':50},
{'sku':'B2','stock':45,'norm':50}]"</span>
    }
  ],
  <span class="key">"temperature"</span>: <span class="num">0.1</span>  <span class="cmt">// низкая = предсказуемый ответ</span>
}`
    },
    quiz: {
      q: "Агент должен анализировать остатки и возвращать данные для следующего шага в workflow. Какой параметр критически важен в промпте?",
      opts: [
        "Попросить AI быть вежливым и подробным",
        "Указать конкретный формат ответа (JSON) — чтобы агент мог обработать его программно",
        "Установить temperature: 1.0 для творческих ответов",
        "Написать промпт как можно длиннее"
      ],
      correct: 1,
      feedback_ok: "Правильно! В агентах AI — это один шаг в цепочке. Его ответ читает программный код, а не человек. Поэтому нужен строгий формат (JSON) — иначе следующий шаг не сможет обработать данные. Temperature 0.1 добавляет предсказуемость.",
      feedback_no: "В контексте агентов ответ AI читает код, а не человек. Важен не стиль, а формат. Если AI вернёт свободный текст вместо JSON — следующий шаг в workflow упадёт с ошибкой. Всегда указывай точный формат ответа в system-промпте."
    },
    interview: {
      q: "Что такое prompt engineering и почему это важный навык для AI Project Manager?",
      hint: "Сильный ответ: «Prompt engineering — это проектирование инструкций для AI, которые дают предсказуемый и нужный результат. Для AI PM это ключевой навык: ты не пишешь код, но задаёшь AI точное поведение. От качества промпта зависит точность агента — неправильный промпт даёт мусор на выходе, который ломает весь workflow.»"
    }
  }
];

let state = { done: new Set(), quizCorrect: 0, current: 0 };

function loadState() {
  try {
    const s = JSON.parse(localStorage.getItem('ai_trainer') || '{}');
    if (s.done) state.done = new Set(s.done);
    if (s.quizCorrect) state.quizCorrect = s.quizCorrect;
  } catch(e) {}
}

function saveState() {
  localStorage.setItem('ai_trainer', JSON.stringify({ done: [...state.done], quizCorrect: state.quizCorrect }));
}

function updateUI() {
  const total = LESSONS.length;
  const done = state.done.size;
  const pct = Math.round(done / total * 100);
  document.getElementById('prog-pct').textContent = pct + '%';
  document.getElementById('prog-bar').style.width = pct + '%';
  document.getElementById('stat-done').textContent = done;
  document.getElementById('stat-quiz').textContent = state.quizCorrect;

  LESSONS.forEach((l, i) => {
    const dot = document.getElementById('dot-l' + l.id);
    const nav = document.getElementById('nav-l' + l.id);
    const unlocked = i === 0 || state.done.has(LESSONS[i-1].id);
    if (state.done.has(l.id)) {
      dot.textContent = '✓'; dot.className = 'nav-dot done';
      nav.classList.remove('locked');
    } else if (unlocked) {
      dot.textContent = l.id; dot.className = 'nav-dot active';
      nav.classList.remove('locked');
    } else {
      dot.textContent = l.id; dot.className = 'nav-dot locked';
      nav.classList.add('locked');
    }
  });

  renderHomeModules();
}

function renderHomeModules() {
  const grid = document.getElementById('home-modules');
  const icons = ['🔌','🤖','🧠','⚡','📱','🛒','📊','🎯'];
  grid.innerHTML = LESSONS.map((l, i) => {
    const unlocked = i === 0 || state.done.has(LESSONS[i-1].id);
    const done = state.done.has(l.id);
    const cls = done ? 'done' : unlocked ? '' : 'locked';
    const badge = done ? '<span class="mc-badge done">✓ Пройден</span>' : unlocked ? '<span class="mc-badge open">Открыт</span>' : '<span class="mc-badge locked">Заблокирован</span>';
    return `<div class="module-card ${cls}" onclick="${unlocked?'goLesson('+l.id+')':''}">
      <div class="mc-icon">${icons[i]||'📘'}</div>
      <div class="mc-title">${l.title}</div>
      <div class="mc-desc">${l.desc}</div>
      ${badge}
    </div>`;
  }).join('');
}

function goScreen(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.getElementById('screen-' + id).classList.add('active');
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  window.scrollTo(0,0);
}

function goLesson(id) {
  const idx = LESSONS.findIndex(l => l.id === id);
  if (idx < 0) return;
  if (idx > 0 && !state.done.has(LESSONS[idx-1].id)) return;
  state.current = id;
  const l = LESSONS[idx];
  renderLesson(l, idx);
  goScreen('lesson');

  const nav = document.getElementById('nav-l' + id);
  if (nav) { document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active')); nav.classList.add('active'); }
}

function renderLesson(l, idx) {
  const next = LESSONS[idx + 1];
  const theoryHtml = l.theory.map(t => `
    <div class="block block-theory">
      <h3>${t.head}</h3>
      ${t.text}
    </div>`).join('');

  document.getElementById('screen-lesson').innerHTML = `
    <div class="lesson-tag">${l.tag}</div>
    <div class="lesson-title">${l.title}</div>
    <div class="lesson-desc">${l.desc}</div>
    <div class="lesson-meta">
      <span class="meta-chip">⏱ ${l.time}</span>
      <span class="meta-chip">📋 Теория + квиз + интервью</span>
      ${state.done.has(l.id) ? '<span class="meta-chip" style="color:var(--green)">✓ Пройден</span>' : ''}
    </div>

    ${theoryHtml}

    <div class="block block-example">
      <div class="ex-label">${l.example.label}</div>
      <p>${l.example.text}</p>
    </div>

    <div class="block block-code">
      <div class="code-header">
        <div class="code-dots"><div class="code-dot"></div><div class="code-dot"></div><div class="code-dot"></div></div>
        <div class="code-lang">${l.code.lang}</div>
      </div>
      <div class="code-body">${l.code.body}</div>
    </div>

    <div class="quiz-section" id="quiz-section">
      <div class="quiz-label">Проверь себя</div>
      <div class="quiz-q">${l.quiz.q}</div>
      <div class="quiz-opts">
        ${l.quiz.opts.map((o,i) => `<button class="quiz-opt" onclick="checkAnswer(${i}, ${l.quiz.correct}, '${escStr(l.quiz.feedback_ok)}', '${escStr(l.quiz.feedback_no)}', ${l.id})">${o}</button>`).join('')}
      </div>
      <div class="quiz-feedback" id="quiz-feedback"></div>
    </div>

    <div class="interview-section" id="interview-section">
      <div class="interview-label">Вопрос с интервью</div>
      <div class="interview-q"><strong>Интервьюер спрашивает:</strong>${l.interview.q}</div>
      <button class="btn btn-sm" onclick="toggleHint()">Показать сильный ответ</button>
      <div class="interview-hint" id="int-hint">${l.interview.hint}</div>
    </div>

    <div class="step-nav">
      <div class="step-counter">Урок ${l.id} из ${LESSONS.length}</div>
      <div class="btn-row" style="margin:0">
        ${idx > 0 ? `<button class="btn btn-sm" onclick="goLesson(${LESSONS[idx-1].id})">← Назад</button>` : ''}
        ${next ? `<button class="btn btn-sm btn-primary" id="next-btn" onclick="goLesson(${next.id})" ${state.done.has(l.id)?'':'disabled'}>Следующий →</button>` : ''}
      </div>
    </div>
  `;
}

function escStr(s) { return s.replace(/'/g, "\\'").replace(/\n/g, ' '); }

function checkAnswer(chosen, correct, feedOk, feedNo, lessonId) {
  const opts = document.querySelectorAll('.quiz-opt');
  opts.forEach(o => o.disabled = true);
  opts[chosen].classList.add(chosen === correct ? 'correct' : 'wrong');
  if (chosen !== correct) opts[correct].classList.add('correct');

  const fb = document.getElementById('quiz-feedback');
  if (chosen === correct) {
    fb.className = 'quiz-feedback show ok';
    fb.textContent = '✓ ' + feedOk;
    if (!state.done.has(lessonId)) {
      state.done.add(lessonId);
      state.quizCorrect++;
      saveState();
      updateUI();
      const nb = document.getElementById('next-btn');
      if (nb) nb.disabled = false;
    }
  } else {
    fb.className = 'quiz-feedback show no';
    fb.textContent = '✗ ' + feedNo;
  }
}

function toggleHint() {
  const h = document.getElementById('int-hint');
  h.classList.toggle('show');
}

loadState();
updateUI();
goScreen('home');
</script>
</body>
</html>
