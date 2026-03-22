<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;700&family=Space+Grotesk:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --cyan: #00F5FF; --cyan-dim: rgba(0,245,255,0.12); --cyan-mid: rgba(0,245,255,0.35);
    --orange: #FF6B35; --green: #39FF14; --purple: #B66FFF;
    --dark: #060A0F; --dark2: #0D1520; --dark3: #111D2B;
    --border: rgba(0,245,255,0.18); --text: #C8D8E8; --text-dim: #5A7090;
    --mono: 'JetBrains Mono', monospace; --sans: 'Space Grotesk', sans-serif;
  }
  body { background: var(--dark); color: var(--text); font-family: var(--sans); overflow-x: hidden; min-height: 100vh; }
  #bg-canvas { position: fixed; inset: 0; z-index: 0; opacity: 0.35; }
  .wrapper { position: relative; z-index: 1; max-width: 860px; margin: 0 auto; padding: 0 20px 60px; }

  /* HERO */
  .hero { padding: 48px 0 32px; display: grid; grid-template-columns: auto 1fr; gap: 32px; align-items: center; border-bottom: 1px solid var(--border); }
  .avatar-ring { position: relative; width: 96px; height: 96px; flex-shrink: 0; }
  .avatar-ring svg { position: absolute; inset: 0; animation: spin 8s linear infinite; }
  .avatar-inner { position: absolute; inset: 8px; border-radius: 50%; background: linear-gradient(135deg, #0D1520, #1A2A40); border: 1.5px solid var(--cyan-mid); display: flex; align-items: center; justify-content: center; font-family: var(--mono); font-size: 28px; font-weight: 700; color: var(--cyan); letter-spacing: -1px; }
  @keyframes spin { to { transform: rotate(360deg); } }

  .hero-name { font-size: clamp(22px, 4vw, 36px); font-weight: 700; color: #fff; letter-spacing: -0.5px; line-height: 1.1; }
  .hero-name span { color: var(--cyan); }
  .hero-bio { font-size: 13.5px; color: var(--text-dim); line-height: 1.65; max-width: 520px; margin-top: 10px; }

  .status-bar { display: flex; align-items: center; gap: 8px; margin-top: 14px; font-family: var(--mono); font-size: 11px; }
  .status-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--green); box-shadow: 0 0 8px var(--green); animation: pulse-dot 2s ease-in-out infinite; }
  @keyframes pulse-dot { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:.5;transform:scale(.8)} }
  .status-text { color: var(--green); }

  .badge-row { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 14px; }
  .badge { font-family: var(--mono); font-size: 10px; padding: 4px 10px; border-radius: 3px; border: 1px solid; letter-spacing: .05em; transition: all .2s; }
  .badge:hover { transform: translateY(-1px); }
  .badge-cyan { border-color: var(--cyan-mid); color: var(--cyan); background: var(--cyan-dim); }
  .badge-orange { border-color: rgba(255,107,53,.3); color: var(--orange); background: rgba(255,107,53,.08); }
  .badge-purple { border-color: rgba(182,111,255,.3); color: var(--purple); background: rgba(182,111,255,.08); }

  /* SECTIONS */
  .section { margin-top: 36px; }
  .section-label { font-family: var(--mono); font-size: 11px; color: var(--text-dim); letter-spacing: .12em; text-transform: uppercase; margin-bottom: 16px; display: flex; align-items: center; gap: 10px; }
  .section-label::after { content: ''; flex: 1; height: 1px; background: linear-gradient(to right, var(--border), transparent); }

  /* TERMINAL */
  .term-bar { background: #0D1520; border: 1px solid var(--border); border-bottom: none; border-radius: 8px 8px 0 0; padding: 8px 14px; display: flex; align-items: center; gap: 6px; }
  .dot { width: 10px; height: 10px; border-radius: 50%; }
  .dot-r{background:#FF5F57} .dot-y{background:#FFBD2E} .dot-g{background:#28CA41}
  .term-title { font-family: var(--mono); font-size: 11px; color: var(--text-dim); margin: 0 auto; }
  .term-body { background: #060A0F; border: 1px solid var(--border); border-radius: 0 0 8px 8px; padding: 16px 20px; font-family: var(--mono); font-size: 12px; line-height: 2; }
  .term-line { display: block; }
  .term-prompt{color:var(--cyan)} .term-cmd{color:#fff} .term-out{color:var(--text-dim);padding-left:4px}
  .term-val{color:var(--green)} .term-key{color:var(--purple)}
  .cursor { display: inline-block; width: 8px; height: 14px; background: var(--cyan); animation: blink 1s step-start infinite; vertical-align: middle; margin-left: 2px; }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0} }

  .typing-text { font-family: var(--mono); font-size: 11px; color: var(--cyan); letter-spacing: .05em; overflow: hidden; border-right: 2px solid var(--cyan); white-space: nowrap; animation: typing 3s steps(40) .5s both, blink-caret .75s step-end infinite; display: inline-block; max-width: 100%; }
  @keyframes typing { from{width:0} to{width:100%} }
  @keyframes blink-caret { 0%,100%{border-color:var(--cyan)} 50%{border-color:transparent} }

  /* STATS */
  .stats-row { display: grid; grid-template-columns: repeat(4,1fr); gap: 10px; }
  @media(max-width:560px){.stats-row{grid-template-columns:repeat(2,1fr)}}
  .stat-card { background: var(--dark2); border: 1px solid var(--border); border-radius: 8px; padding: 16px; text-align: center; }
  .stat-num { font-family: var(--mono); font-size: 26px; font-weight: 700; color: var(--cyan); line-height: 1; margin-bottom: 4px; }
  .stat-label { font-size: 11px; color: var(--text-dim); letter-spacing: .05em; }

  /* STACK */
  .stack-grid { display: grid; grid-template-columns: repeat(3,1fr); gap: 12px; }
  @media(max-width:600px){.stack-grid{grid-template-columns:1fr 1fr}}
  .stack-group { background: var(--dark2); border: 1px solid var(--border); border-radius: 8px; padding: 14px 16px; transition: border-color .25s, background .25s; }
  .stack-group:hover { border-color: rgba(0,245,255,.4); background: var(--dark3); }
  .stack-group-title { font-family: var(--mono); font-size: 10px; color: var(--cyan); letter-spacing: .1em; margin-bottom: 10px; opacity: .75; }
  .tech-tag { display: inline-block; font-family: var(--mono); font-size: 11px; color: var(--text); background: rgba(255,255,255,.04); border: 1px solid rgba(255,255,255,.08); border-radius: 3px; padding: 3px 7px; margin: 2px 2px 2px 0; transition: all .15s; }
  .tech-tag:hover { background: var(--cyan-dim); border-color: var(--cyan-mid); color: var(--cyan); }

  /* SKILL BARS */
  .skill-bars { display: flex; flex-direction: column; gap: 10px; }
  .skill-row { display: grid; grid-template-columns: 120px 1fr 32px; align-items: center; gap: 10px; }
  .skill-name { font-family: var(--mono); font-size: 11.5px; color: var(--text); }
  .skill-track { height: 5px; background: rgba(255,255,255,.06); border-radius: 99px; overflow: hidden; }
  .skill-fill { height: 100%; border-radius: 99px; background: linear-gradient(to right, var(--cyan), #0095FF); transform: scaleX(0); transform-origin: left; transition: transform 1.2s cubic-bezier(.16,1,.3,1); }
  .skill-pct { font-family: var(--mono); font-size: 10px; color: var(--text-dim); text-align: right; }

  /* PROJECTS */
  .projects-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  @media(max-width:560px){.projects-grid{grid-template-columns:1fr}}
  .project-card { background: var(--dark2); border: 1px solid var(--border); border-radius: 8px; padding: 16px; cursor: pointer; transition: all .25s; position: relative; overflow: hidden; }
  .project-card::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 2px; background: linear-gradient(to right, var(--cyan), transparent); opacity: 0; transition: opacity .25s; }
  .project-card:hover { border-color: rgba(0,245,255,.4); transform: translateY(-2px); }
  .project-card:hover::before { opacity: 1; }
  .project-icon { font-size: 22px; margin-bottom: 8px; display: block; }
  .project-name { font-size: 14px; font-weight: 600; color: #fff; margin-bottom: 4px; }
  .project-desc { font-size: 12px; color: var(--text-dim); line-height: 1.55; margin-bottom: 10px; }
  .project-tags { display: flex; flex-wrap: wrap; gap: 4px; }
  .project-tag { font-family: var(--mono); font-size: 9.5px; padding: 2px 6px; border-radius: 2px; border: 1px solid rgba(0,245,255,.2); color: var(--cyan); opacity: .8; }

  /* ROADMAP */
  .roadmap { display: flex; flex-direction: column; gap: 12px; }
  .roadmap-item { display: flex; align-items: flex-start; gap: 14px; padding: 14px 16px; background: var(--dark2); border: 1px solid var(--border); border-radius: 8px; }
  .roadmap-icon { width: 28px; height: 28px; border-radius: 6px; display: flex; align-items: center; justify-content: center; font-size: 14px; flex-shrink: 0; }
  .roadmap-icon.active { background: var(--cyan-dim); border: 1px solid var(--cyan-mid); }
  .roadmap-icon.next { background: rgba(182,111,255,.1); border: 1px solid rgba(182,111,255,.3); }
  .roadmap-title { font-size: 13px; font-weight: 600; color: #fff; margin-bottom: 2px; }
  .roadmap-desc { font-size: 12px; color: var(--text-dim); }

  /* CONTACT LINKS */
  .contact-grid { display: grid; grid-template-columns: repeat(3,1fr); gap: 12px; }
  @media(max-width:560px){.contact-grid{grid-template-columns:1fr}}
  .contact-card { background: var(--dark2); border: 1px solid var(--border); border-radius: 8px; padding: 18px 16px; display: flex; align-items: center; gap: 12px; text-decoration: none; transition: all .25s; cursor: pointer; }
  .contact-card:hover { border-color: rgba(0,245,255,.45); transform: translateY(-2px); background: var(--dark3); }
  .contact-icon { width: 36px; height: 36px; border-radius: 8px; display: flex; align-items: center; justify-content: center; font-size: 18px; flex-shrink: 0; }
  .contact-icon.email { background: rgba(255,107,53,.12); border: 1px solid rgba(255,107,53,.3); }
  .contact-icon.github { background: rgba(200,216,232,.08); border: 1px solid rgba(200,216,232,.15); }
  .contact-icon.linkedin { background: rgba(0,119,181,.15); border: 1px solid rgba(0,119,181,.35); }
  .contact-label { font-family: var(--mono); font-size: 10px; color: var(--text-dim); letter-spacing: .06em; margin-bottom: 3px; }
  .contact-value { font-size: 12.5px; font-weight: 500; color: #fff; word-break: break-all; }

  /* CTA */
  .cta { margin-top: 40px; padding: 32px 28px; background: var(--dark2); border: 1px solid rgba(0,245,255,.25); border-radius: 10px; text-align: center; position: relative; overflow: hidden; }
  .cta::after { content: ''; position: absolute; top: -60px; left: 50%; transform: translateX(-50%); width: 280px; height: 120px; background: radial-gradient(ellipse, rgba(0,245,255,.08), transparent 70%); }
  .cta-title { font-size: 20px; font-weight: 700; color: #fff; margin-bottom: 6px; }
  .cta-sub { font-size: 13px; color: var(--text-dim); margin-bottom: 20px; }
  .cta-sub strong { color: var(--orange); }
  .cta-buttons { display: flex; justify-content: center; gap: 10px; flex-wrap: wrap; }
  .btn { font-family: var(--mono); font-size: 12px; padding: 10px 20px; border-radius: 6px; border: 1px solid; cursor: pointer; text-decoration: none; transition: all .2s; display: inline-block; }
  .btn-primary { background: var(--cyan); border-color: var(--cyan); color: #000; font-weight: 700; }
  .btn-primary:hover { background: #00d4e0; transform: translateY(-1px); }
  .btn-outline { background: transparent; border-color: var(--border); color: var(--text); }
  .btn-outline:hover { border-color: var(--cyan-mid); color: var(--cyan); transform: translateY(-1px); }
</style>

<canvas id="bg-canvas"></canvas>
<div class="wrapper">

  <!-- TERMINAL -->
  <div style="margin-top:32px">
    <div class="term-bar">
      <span class="dot dot-r"></span><span class="dot dot-y"></span><span class="dot dot-g"></span>
      <span class="term-title">~/Bnsidi/README.md</span>
    </div>
    <div class="term-body">
      <span class="term-line"><span class="term-prompt">❯ </span><span class="term-cmd">whoami</span></span>
      <span class="term-line"><span class="term-out"><span class="term-key">name     </span><span class="term-val">Yassine BENSIDI</span></span></span>
      <span class="term-line"><span class="term-out"><span class="term-key">role     </span><span class="term-val">Engineering Student — IS & Big Data (Y2)</span></span></span>
      <span class="term-line"><span class="term-out"><span class="term-key">email    </span><span class="term-val">bensidi.yassine0@gmail.com</span></span></span>
      <span class="term-line"><span class="term-out"><span class="term-key">github   </span><span class="term-val">github.com/Bnsidi</span></span></span>
      <span class="term-line"><span class="term-out"><span class="term-key">linkedin </span><span class="term-val">linkedin.com/in/yassine-bensidi</span></span></span>
      <span class="term-line"><span class="term-out"><span class="term-key">vibe     </span><span class="term-val">"Code is music. I compose systems." 🎧</span></span></span>
      <span class="term-line"><span class="term-prompt">❯ </span><span class="term-cmd">cat internship.txt</span></span>
      <span class="term-line"><span class="term-out" style="color:var(--orange)">→  Open to 2-month IT Internship — July 2026 🌱</span></span>
      <span class="term-line"><span class="term-prompt">❯ </span><span class="cursor"></span></span>
    </div>
  </div>

  <!-- HERO -->
  <div class="hero">
    <div class="avatar-ring">
      <svg viewBox="0 0 96 96" fill="none" xmlns="http://www.w3.org/2000/svg">
        <circle cx="48" cy="48" r="44" stroke="rgba(0,245,255,0.25)" stroke-width="1" stroke-dasharray="6 4"/>
        <circle cx="48" cy="4" r="3" fill="#00F5FF"/>
        <circle cx="92" cy="48" r="2" fill="#00F5FF" opacity="0.5"/>
      </svg>
      <div class="avatar-inner">YB</div>
    </div>
    <div>
      <h1 class="hero-name">Yassine <span>BENSIDI</span></h1>
      <div class="typing-text">Backend Dev · SysAdmin · Data Enthusiast</div>
      <p class="hero-bio">From slicing backend logic in Java & Laravel to automating workflows and administrating Linux servers — I chase excellence across every layer of the stack.</p>
      <div class="status-bar">
        <span class="status-dot"></span>
        <span class="status-text">Available for internship — July 2026</span>
      </div>
      <div class="badge-row">
        <span class="badge badge-cyan">Backend Dev</span>
        <span class="badge badge-cyan">System Admin</span>
        <span class="badge badge-orange">Big Data</span>
        <span class="badge badge-orange">Automation</span>
        <span class="badge badge-purple">IS Engineering</span>
      </div>
    </div>
  </div>

  <!-- STATS -->
  <div class="section">
    <div class="section-label">metrics</div>
    <div class="stats-row">
      <div class="stat-card"><div class="stat-num">6</div><div class="stat-label">Projects</div></div>
      <div class="stat-card"><div class="stat-num">5+</div><div class="stat-label">Languages</div></div>
      <div class="stat-card"><div class="stat-num">Y2</div><div class="stat-label">Engineering</div></div>
      <div class="stat-card"><div class="stat-num">∞</div><div class="stat-label">Grind</div></div>
    </div>
  </div>

  <!-- STACK -->
  <div class="section">
    <div class="section-label">tech stack</div>
    <div class="stack-grid">
      <div class="stack-group">
        <div class="stack-group-title">BACKEND</div>
        <span class="tech-tag">Java</span><span class="tech-tag">JEE</span><span class="tech-tag">Spring</span><span class="tech-tag">Laravel</span><span class="tech-tag">PHP</span><span class="tech-tag">Python</span>
      </div>
      <div class="stack-group">
        <div class="stack-group-title">DATABASE</div>
        <span class="tech-tag">MySQL</span><span class="tech-tag">Oracle</span><span class="tech-tag">PostgreSQL</span><span class="tech-tag">SQL</span>
      </div>
      <div class="stack-group">
        <div class="stack-group-title">INFRA & SYSADMIN</div>
        <span class="tech-tag">Linux</span><span class="tech-tag">Postfix</span><span class="tech-tag">SSH</span><span class="tech-tag">Bash</span>
      </div>
      <div class="stack-group">
        <div class="stack-group-title">AUTOMATION</div>
        <span class="tech-tag">n8n</span><span class="tech-tag">Gemini API</span><span class="tech-tag">REST API</span><span class="tech-tag">WebSockets</span>
      </div>
      <div class="stack-group">
        <div class="stack-group-title">DEVTOOLS</div>
        <span class="tech-tag">Git</span><span class="tech-tag">Docker</span><span class="tech-tag">GitHub</span><span class="tech-tag">Postman</span>
      </div>
      <div class="stack-group">
        <div class="stack-group-title">CLOUD — EN COURS</div>
        <span class="tech-tag" style="color:var(--orange);border-color:rgba(255,107,53,.3)">Azure</span>
        <span class="tech-tag" style="color:var(--orange);border-color:rgba(255,107,53,.3)">AWS</span>
        <span class="tech-tag" style="color:var(--orange);border-color:rgba(255,107,53,.3)">Databricks</span>
      </div>
    </div>
  </div>

  <!-- SKILL BARS -->
  <div class="section">
    <div class="section-label">proficiency</div>
    <div class="skill-bars">
      <div class="skill-row"><span class="skill-name">Laravel / PHP</span><div class="skill-track"><div class="skill-fill" data-pct="0.88"></div></div><span class="skill-pct">88%</span></div>
      <div class="skill-row"><span class="skill-name">Java / JEE</span><div class="skill-track"><div class="skill-fill" data-pct="0.82"></div></div><span class="skill-pct">82%</span></div>
      <div class="skill-row"><span class="skill-name">Databases</span><div class="skill-track"><div class="skill-fill" data-pct="0.78"></div></div><span class="skill-pct">78%</span></div>
      <div class="skill-row"><span class="skill-name">Linux / SysAdmin</span><div class="skill-track"><div class="skill-fill" data-pct="0.74"></div></div><span class="skill-pct">74%</span></div>
      <div class="skill-row"><span class="skill-name">Automation / n8n</span><div class="skill-track"><div class="skill-fill" data-pct="0.70"></div></div><span class="skill-pct">70%</span></div>
      <div class="skill-row"><span class="skill-name">Cloud (Azure/AWS)</span><div class="skill-track"><div class="skill-fill" data-pct="0.40" style="background:linear-gradient(to right,var(--orange),#FF4D4D)"></div></div><span class="skill-pct" style="color:var(--orange)">40%</span></div>
    </div>
  </div>

  <!-- PROJECTS -->
  <div class="section">
    <div class="section-label">epic projects</div>
    <div class="projects-grid">
      <div class="project-card">
        <span class="project-icon">🚗</span>
        <div class="project-name">TrackMyCar</div>
        <div class="project-desc">Mobile application for real-time vehicle tracking with event logging and mobile-first UX.</div>
        <div class="project-tags"><span class="project-tag">Mobile</span><span class="project-tag">Laravel</span><span class="project-tag">API</span></div>
      </div>
      <div class="project-card">
        <span class="project-icon">🤖</span>
        <div class="project-name">AI Chatbot</div>
        <div class="project-desc">Smart conversational agent powered by Laravel backend and Google Gemini API integration.</div>
        <div class="project-tags"><span class="project-tag">Laravel</span><span class="project-tag">Gemini API</span><span class="project-tag">NLP</span></div>
      </div>
      <div class="project-card">
        <span class="project-icon">⚙️</span>
        <div class="project-name">HR Automation Workflow</div>
        <div class="project-desc">Automated email & voice messaging for internship confirmations via n8n. Zero manual intervention.</div>
        <div class="project-tags"><span class="project-tag">n8n</span><span class="project-tag">Postfix</span><span class="project-tag">Automation</span></div>
      </div>
      <div class="project-card">
        <span class="project-icon">🛒</span>
        <div class="project-name">E-Commerce Platform</div>
        <div class="project-desc">Multi-vendor marketplace built from scratch — auth, cart, checkout, full vendor dashboard.</div>
        <div class="project-tags"><span class="project-tag">Laravel</span><span class="project-tag">MySQL</span><span class="project-tag">Full-Stack</span></div>
      </div>
      <div class="project-card">
        <span class="project-icon">🎓</span>
        <div class="project-name">Student Management System</div>
        <div class="project-desc">Enterprise-level academic platform with role-based access and reporting — Java/JEE architecture.</div>
        <div class="project-tags"><span class="project-tag">Java</span><span class="project-tag">JEE</span><span class="project-tag">Enterprise</span></div>
      </div>
      <div class="project-card">
        <span class="project-icon">💬</span>
        <div class="project-name">Teams Clone</div>
        <div class="project-desc">Real-time collaboration platform — WebSockets, channels, DMs, file sharing. Built like the real deal.</div>
        <div class="project-tags"><span class="project-tag">Laravel</span><span class="project-tag">WebSockets</span><span class="project-tag">Real-time</span></div>
      </div>
    </div>
  </div>

  <!-- ROADMAP -->
  <div class="section">
    <div class="section-label">mission & roadmap</div>
    <div class="roadmap">
      <div class="roadmap-item">
        <div class="roadmap-icon active">⚔️</div>
        <div>
          <div class="roadmap-title">Currently Grinding</div>
          <div class="roadmap-desc">Mastering system administration (Postfix, Oracle) and building robust, production-grade backends.</div>
        </div>
      </div>
      <div class="roadmap-item">
        <div class="roadmap-icon next">☁️</div>
        <div>
          <div class="roadmap-title">Side Quests</div>
          <div class="roadmap-desc">Cloud & Big Data certifications (Azure, AWS, Databricks) + polishing professional French communication.</div>
        </div>
      </div>
    </div>
  </div>

  <!-- CONTACT CARDS -->
  <div class="section">
    <div class="section-label">contact</div>
    <div class="contact-grid">
      <a class="contact-card" href="mailto:bensidi.yassine0@gmail.com">
        <div class="contact-icon email">📬</div>
        <div>
          <div class="contact-label">EMAIL</div>
          <div class="contact-value">bensidi.yassine0@gmail.com</div>
        </div>
      </a>
      <a class="contact-card" href="https://github.com/Bnsidi" target="_blank">
        <div class="contact-icon github">🐙</div>
        <div>
          <div class="contact-label">GITHUB</div>
          <div class="contact-value">github.com/Bnsidi</div>
        </div>
      </a>
      <a class="contact-card" href="https://www.linkedin.com/in/yassine-bensidi/" target="_blank">
        <div class="contact-icon linkedin">💼</div>
        <div>
          <div class="contact-label">LINKEDIN</div>
          <div class="contact-value">yassine-bensidi</div>
        </div>
      </a>
    </div>
  </div>

  <!-- CTA -->
  <div class="cta">
    <div class="cta-title">Let's build something extraordinary</div>
    <div class="cta-sub">Open for a <strong>2-month IT Internship starting July 2026</strong><br>Backend · SysAdmin · Data · Automation</div>
    <div class="cta-buttons">
      <a class="btn btn-primary" href="mailto:bensidi.yassine0@gmail.com">📬 Hit Me Up</a>
      <a class="btn btn-outline" href="https://www.linkedin.com/in/yassine-bensidi/" target="_blank">LinkedIn →</a>
      <a class="btn btn-outline" href="https://github.com/Bnsidi" target="_blank">GitHub →</a>
    </div>
  </div>

  <div style="text-align:center;margin-top:28px;font-family:var(--mono);font-size:10.5px;color:var(--text-dim);letter-spacing:.08em">
    "Vibe to code. Code to build. Build to matter." — Yassine BENSIDI
  </div>
</div>

<script>
// PARTICLE BG
const canvas = document.getElementById('bg-canvas');
const ctx = canvas.getContext('2d');
let W, H, particles = [];
function resize(){ W = canvas.width = window.innerWidth; H = canvas.height = window.innerHeight; }
resize(); window.addEventListener('resize', resize);
for(let i=0;i<60;i++) particles.push({ x:Math.random()*9999, y:Math.random()*9999, vx:(Math.random()-.5)*.3, vy:(Math.random()-.5)*.3, r:Math.random()*1.5+.5, op:Math.random()*.6+.2 });
function draw(){
  ctx.clearRect(0,0,W,H);
  for(let p of particles){
    p.x+=p.vx; p.y+=p.vy;
    if(p.x<0)p.x=W; if(p.x>W)p.x=0; if(p.y<0)p.y=H; if(p.y>H)p.y=0;
    ctx.beginPath(); ctx.arc(p.x%W,p.y%H,p.r,0,Math.PI*2);
    ctx.fillStyle=`rgba(0,245,255,${p.op})`; ctx.fill();
  }
  for(let i=0;i<particles.length;i++) for(let j=i+1;j<particles.length;j++){
    const dx=(particles[i].x%W)-(particles[j].x%W), dy=(particles[i].y%H)-(particles[j].y%H);
    const d=Math.sqrt(dx*dx+dy*dy);
    if(d<100){ ctx.beginPath(); ctx.moveTo(particles[i].x%W,particles[i].y%H); ctx.lineTo(particles[j].x%W,particles[j].y%H); ctx.strokeStyle=`rgba(0,245,255,${(1-d/100)*.12})`; ctx.lineWidth=.5; ctx.stroke(); }
  }
  requestAnimationFrame(draw);
}
draw();
// SKILL BARS
setTimeout(()=>{ document.querySelectorAll('.skill-fill').forEach(el=>{ el.style.transform=`scaleX(${el.dataset.pct})`; }); }, 400);
</script>
