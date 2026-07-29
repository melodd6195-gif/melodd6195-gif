<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Dennis Melo — dev journal</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:ital,wght@0,400;0,500;0,600;0,700;1,400&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --bg-app:      #17181c;
    --bg-side:     #1b1c21;
    --bg-editor:   #1e1f24;
    --bg-tab:      #232429;
    --bg-tab-active: #1e1f24;
    --bg-status:   #cc7832;
    --border:      #2c2d33;
    --text-main:   #a9b7c6;
    --text-dim:    #5f6169;
    --text-bright: #d3d8e0;
    --keyword:     #cc7832;
    --string:      #6a8759;
    --comment:     #6b6f7b;
    --type:        #b568d6;
    --func:        #ffc66d;
    --number:      #6897bb;
    --caret:       #ffc66d;
    --font-mono: 'JetBrains Mono', ui-monospace, monospace;
    --font-sans: 'Inter', sans-serif;
  }

  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    background:var(--bg-app);
    color:var(--text-main);
    font-family:var(--font-mono);
    font-size:14px;
    line-height:1.65;
    -webkit-font-smoothing:antialiased;
  }

  a{color:inherit;}

  .app{
    display:grid;
    grid-template-columns: 240px 1fr;
    grid-template-rows: 1fr 28px;
    height:100vh;
  }

  /* ---------- SIDEBAR ---------- */
  .sidebar{
    grid-row:1 / 2;
    background:var(--bg-side);
    border-right:1px solid var(--border);
    padding:18px 0;
    overflow-y:auto;
  }
  .sidebar-title{
    font-size:11px;
    letter-spacing:.12em;
    text-transform:uppercase;
    color:var(--text-dim);
    padding:0 18px 12px;
  }
  .profile-block{
    display:flex;
    align-items:center;
    gap:10px;
    padding:0 18px 18px;
    border-bottom:1px solid var(--border);
    margin-bottom:10px;
  }
  .avatar{
    width:42px;height:42px;border-radius:8px;
    background:linear-gradient(135deg,#cc7832,#b568d6);
    display:flex;align-items:center;justify-content:center;
    font-weight:700;color:#1e1f24;font-size:15px;
    flex:none;
  }
  .profile-name{color:var(--text-bright);font-weight:600;font-size:13px;}
  .profile-handle{color:var(--text-dim);font-size:12px;}

  .tree{padding:0 8px;}
  .tree-folder{
    padding:6px 10px;color:var(--text-dim);
    font-size:12.5px;display:flex;align-items:center;gap:6px;
  }
  .tree-item{
    padding:6px 10px 6px 26px;
    font-size:12.5px;
    color:var(--text-main);
    cursor:pointer;
    border-radius:4px;
    display:flex;align-items:center;gap:8px;
    white-space:nowrap;
  }
  .tree-item:hover{background:#25262c;}
  .tree-item.active{background:#2a2b32;color:var(--text-bright);}
  .dot{width:6px;height:6px;border-radius:50%;flex:none;}
  .dot.md{background:#6897bb;}
  .dot.java{background:#cc7832;}
  .dot.js{background:#f0db4f;}
  .dot.json{background:#b568d6;}

  .sidebar-links{padding:16px 18px 0;margin-top:8px;border-top:1px solid var(--border);}
  .sidebar-links a{
    display:flex;align-items:center;gap:8px;
    font-size:12px;color:var(--text-dim);
    text-decoration:none;padding:6px 0;
  }
  .sidebar-links a:hover{color:var(--func);}

  /* ---------- MAIN ---------- */
  .main{
    grid-row:1 / 2;
    display:flex;
    flex-direction:column;
    min-width:0;
  }

  .tabbar{
    display:flex;
    background:var(--bg-tab);
    border-bottom:1px solid var(--border);
    overflow-x:auto;
  }
  .tab{
    padding:9px 16px;
    font-size:12.5px;
    color:var(--text-dim);
    cursor:pointer;
    border-right:1px solid var(--border);
    display:flex;align-items:center;gap:8px;
    white-space:nowrap;
    user-select:none;
  }
  .tab.active{
    background:var(--bg-tab-active);
    color:var(--text-bright);
    box-shadow:inset 0 -2px 0 var(--keyword);
  }
  .tab:hover:not(.active){color:var(--text-main);}

  .editor{
    flex:1;
    display:flex;
    overflow:auto;
    background:var(--bg-editor);
  }
  .gutter{
    padding:24px 14px 24px 18px;
    text-align:right;
    color:#4b4d55;
    user-select:none;
    font-size:13px;
    line-height:1.9;
    flex:none;
    border-right:1px solid var(--border);
  }
  .code{
    padding:24px 28px;
    font-size:13.5px;
    line-height:1.9;
    white-space:pre-wrap;
    flex:1;
    min-width:0;
  }
  .code .kw{color:var(--keyword);}
  .code .str{color:var(--string);}
  .code .cm{color:var(--comment);font-style:italic;}
  .code .ty{color:var(--type);}
  .code .fn{color:var(--func);}
  .code .num{color:var(--number);}
  .code .pl{color:var(--text-main);}
  .code .em{color:var(--text-bright);font-weight:600;}

  .badges{margin:14px 0 4px;display:flex;gap:8px;flex-wrap:wrap;}
  .badge{
    display:inline-flex;align-items:center;gap:6px;
    padding:5px 10px;border-radius:5px;
    font-size:11.5px;font-weight:600;
    font-family:var(--font-sans);
  }
  .badge.java{background:#cc783222;color:#e39b5c;border:1px solid #cc783255;}
  .badge.intellij{background:#b568d622;color:#c98fe8;border:1px solid #b568d655;}
  .badge.git{background:#f0503022;color:#ff7a5c;border:1px solid #f0503055;}
  .badge.html{background:#e34c2622;color:#f0785a;border:1px solid #e34c2655;}
  .badge.css{background:#2965f122;color:#5f8fff;border:1px solid #2965f155;}
  .badge.jsn{background:#f0db4f22;color:#e0c93f;border:1px solid #f0db4f55;}

  .cursor{
    display:inline-block;width:7px;height:15px;
    background:var(--caret);
    vertical-align:middle;
    animation:blink 1s step-end infinite;
  }
  @keyframes blink{50%{opacity:0;}}

  .contact-link{
    display:inline-flex;align-items:center;gap:8px;
    color:var(--func);text-decoration:none;
    border-bottom:1px dashed #ffc66d55;
  }
  .contact-link:hover{border-bottom-style:solid;}

  .console{
    margin-top:18px;
    border:1px solid var(--border);
    border-radius:6px;
    background:#17181c;
    overflow:hidden;
  }
  .console-head{
    display:flex;align-items:center;gap:6px;
    padding:8px 12px;background:#1b1c21;
    border-bottom:1px solid var(--border);
    font-size:11px;color:var(--text-dim);
  }
  .console-head span{width:9px;height:9px;border-radius:50%;background:#3a3b42;}
  .console-body{padding:14px;font-size:12.5px;color:var(--string);}
  .console-body .cmt{color:var(--comment);}

  /* ---------- STATUS BAR ---------- */
  .statusbar{
    grid-column:1 / -1;
    background:#2b6f4f;
    color:#e8f5ee;
    display:flex;
    align-items:center;
    justify-content:space-between;
    padding:0 14px;
    font-size:11.5px;
    font-family:var(--font-sans);
  }
  .statusbar .left, .statusbar .right{display:flex;gap:16px;align-items:center;}

  /* ---------- SCROLLBARS ---------- */
  ::-webkit-scrollbar{width:10px;height:10px;}
  ::-webkit-scrollbar-track{background:transparent;}
  ::-webkit-scrollbar-thumb{background:#3a3b42;border-radius:6px;}

  /* ---------- MOBILE ---------- */
  @media (max-width: 780px){
    .app{grid-template-columns: 1fr;grid-template-rows: auto 1fr 28px;}
    .sidebar{
      grid-row:1;
      border-right:none;border-bottom:1px solid var(--border);
      max-height:150px;
    }
    .main{grid-row:2;}
    .gutter{display:none;}
    .code{padding:18px;}
  }

  .visually-hidden:focus{
    position:fixed;top:8px;left:8px;z-index:10;
    background:#000;color:#fff;padding:8px 12px;border-radius:4px;
  }
  a:focus-visible, .tree-item:focus-visible, .tab:focus-visible{
    outline:2px solid var(--func);outline-offset:2px;
  }

  @media (prefers-reduced-motion: reduce){
    .cursor{animation:none;opacity:1;}
  }
</style>
</head>
<body>
<a href="#main-content" class="visually-hidden">Pular para o conteúdo</a>

<div class="app">
  <!-- SIDEBAR -->
  <aside class="sidebar" aria-label="Explorador de arquivos">
    <div class="profile-block">
      <div class="avatar" aria-hidden="true">DM</div>
      <div>
        <div class="profile-name">Dennis Melo</div>
        <div class="profile-handle">he/him · 16 anos</div>
      </div>
    </div>

    <div class="sidebar-title">Explorer</div>
    <nav class="tree">
      <div class="tree-folder">📁 dennis-melo</div>
      <div class="tree-item active" data-tab="readme" tabindex="0"><span class="dot md"></span>README.md</div>
      <div class="tree-item" data-tab="skills" tabindex="0"><span class="dot java"></span>Skills.java</div>
      <div class="tree-item" data-tab="project" tabindex="0"><span class="dot json"></span>project.json</div>
      <div class="tree-item" data-tab="contact" tabindex="0"><span class="dot js"></span>contact.js</div>
    </nav>

    <div class="sidebar-links">
      <a href="mailto:melodd6195@gmail.com">✉ melodd6195@gmail.com</a>
      <a href="https://github.com/melodd6195-gif" target="_blank" rel="noopener">⌥ github.com/melodd6195-gif</a>
    </div>
  </aside>

  <!-- MAIN -->
  <main class="main" id="main-content">
    <div class="tabbar" role="tablist" aria-label="Arquivos abertos">
      <div class="tab active" data-tab="readme" role="tab" aria-selected="true" tabindex="0"><span class="dot md"></span>README.md</div>
      <div class="tab" data-tab="skills" role="tab" aria-selected="false" tabindex="0"><span class="dot java"></span>Skills.java</div>
      <div class="tab" data-tab="project" role="tab" aria-selected="false" tabindex="0"><span class="dot json"></span>project.json</div>
      <div class="tab" data-tab="contact" role="tab" aria-selected="false" tabindex="0"><span class="dot js"></span>contact.js</div>
    </div>

    <div class="editor">
      <div class="gutter" id="gutter" aria-hidden="true"></div>
      <div class="code" id="code-area"></div>
    </div>
  </main>

  <!-- STATUS BAR -->
  <div class="statusbar">
    <div class="left">
      <span>⎇ main</span>
      <span id="status-file">README.md</span>
    </div>
    <div class="right">
      <span>UTF-8</span>
      <span>Java 17</span>
      <span>Ln <span id="status-line">1</span>, Col 1</span>
    </div>
  </div>
</div>

<script>
  const files = {
    readme: {
      label: "README.md",
      html: `<span class="cm">/**</span>
<span class="cm"> * Olá, eu sou o </span><span class="em">Dennis</span><span class="cm"> 👋</span>
<span class="cm"> */</span>

<span class="kw">const</span> <span class="fn">dennis</span> = {
  <span class="pl">idade:</span> <span class="num">16</span>,
  <span class="pl">base:</span> [<span class="str">"Java"</span>, <span class="str">"lógica de programação"</span>],
  <span class="pl">aprendendoAgora:</span> [<span class="str">"HTML"</span>, <span class="str">"CSS"</span>, <span class="str">"JavaScript"</span>],
  <span class="pl">objetivo:</span> <span class="str">"expandir pro front-end e construir interfaces de verdade 🚀"</span>,
  <span class="pl">disponivel:</span> <span class="str">"aberto a trocar ideia sobre programação e aprendizado!"</span>
};

<span class="cm">// ferramentas do dia a dia</span>`,
      badges: `<div class="badges">
        <span class="badge java">🔥 JAVA</span>
        <span class="badge intellij">🧩 INTELLIJ IDEA</span>
        <span class="badge git">⑂ GIT</span>
        <span class="badge html">🌐 HTML</span>
        <span class="badge css">🎨 CSS</span>
        <span class="badge jsn">⚡ JAVASCRIPT</span>
      </div>`
    },
    skills: {
      label: "Skills.java",
      html: `<span class="kw">public</span> <span class="kw">class</span> <span class="ty">Skills</span> {

    <span class="kw">private</span> <span class="ty">String</span>[] <span class="fn">base</span> = {
        <span class="str">"Java"</span>,
        <span class="str">"lógica de programação"</span>
    };

    <span class="kw">private</span> <span class="ty">String</span>[] <span class="fn">aprendendoAgora</span> = {
        <span class="str">"HTML"</span>,
        <span class="str">"CSS"</span>,
        <span class="str">"JavaScript"</span>
    };

    <span class="kw">private</span> <span class="ty">String</span>[] <span class="fn">ferramentas</span> = {
        <span class="str">"IntelliJ IDEA"</span>,
        <span class="str">"Git"</span>
    };

    <span class="cm">// migrando pro front-end, um passo de cada vez ✏️</span>
}`
    },
    project: {
      label: "project.json",
      html: `{
  <span class="pl">"projeto"</span>: <span class="str">"em destaque"</span>,
  <span class="pl">"descricao"</span>: <span class="str">"repositório fixado no perfil do GitHub"</span>,
  <span class="pl">"status"</span>: <span class="str">"em desenvolvimento"</span>,
  <span class="pl">"estatisticas"</span>: {
    <span class="pl">"followers"</span>: <span class="num">1</span>,
    <span class="pl">"following"</span>: <span class="num">0</span>
  }
}

<span class="cm">// em breve mais projetos por aqui</span>`
    },
    contact: {
      label: "contact.js",
      html: `<span class="kw">function</span> <span class="fn">falarComDennis</span>() {
  <span class="kw">return</span> {
    <span class="pl">email:</span> <a class="contact-link" href="mailto:melodd6195@gmail.com">melodd6195@gmail.com</a>,
    <span class="pl">github:</span> <a class="contact-link" href="https://github.com/melodd6195-gif" target="_blank" rel="noopener">melodd6195-gif</a>
  };
}

<span class="cm">// chama aí pra trocar ideia sobre programação!</span>`,
      console: `<div class="console">
        <div class="console-head"><span></span><span></span><span></span>&nbsp;console</div>
        <div class="console-body">> falarComDennis()<br><span class="cmt">// aguardando sua mensagem...</span></div>
      </div>`
    }
  };

  const codeArea = document.getElementById('code-area');
  const gutter = document.getElementById('gutter');
  const statusFile = document.getElementById('status-file');
  const statusLine = document.getElementById('status-line');
  const tabs = document.querySelectorAll('.tab');
  const treeItems = document.querySelectorAll('.tree-item');

  function buildGutter(lineCount){
    let lines = '';
    for(let i=1;i<=lineCount;i++){ lines += i + '\n'; }
    gutter.textContent = lines;
  }

  function renderFile(key, animate){
    const file = files[key];
    const extra = (file.badges || '') + (file.console || '');
    const fullHTML = file.html + extra;
    const lineCount = file.html.split('\n').length + 3;

    tabs.forEach(t => {
      const active = t.dataset.tab === key;
      t.classList.toggle('active', active);
      t.setAttribute('aria-selected', active);
    });
    treeItems.forEach(t => t.classList.toggle('active', t.dataset.tab === key));

    statusFile.textContent = file.label;
    statusLine.textContent = '1';
    buildGutter(lineCount);

    if(!animate){
      codeArea.innerHTML = fullHTML + '<span class="cursor"></span>';
      return;
    }

    // digitação simples do texto puro, depois injeta HTML colorido
    codeArea.innerHTML = '<span class="cursor"></span>';
    setTimeout(() => { codeArea.innerHTML = fullHTML + '<span class="cursor"></span>'; }, 450);
  }

  function selectTab(key, animate){
    renderFile(key, animate);
  }

  tabs.forEach(tab => {
    tab.addEventListener('click', () => selectTab(tab.dataset.tab, false));
    tab.addEventListener('keydown', e => { if(e.key === 'Enter') selectTab(tab.dataset.tab, false); });
  });
  treeItems.forEach(item => {
    item.addEventListener('click', () => selectTab(item.dataset.tab, false));
    item.addEventListener('keydown', e => { if(e.key === 'Enter') selectTab(item.dataset.tab, false); });
  });

  // carregamento inicial com efeito de "digitando"
  selectTab('readme', true);
</script>
</body>
</html>
