# Claude Code vs Claude Chat Page Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace `index.html` with a dark-mode split-screen comparison page contrasting Claude Code (CLI) and Claude Chat (web).

**Architecture:** Single self-contained `index.html` with inline CSS and JS. No build step, no dependencies, no framework. Deploys as static HTML to Vercel.

**Tech Stack:** HTML5, CSS (custom properties, grid, flexbox), vanilla JS (IntersectionObserver for fade-ins), Google Fonts (Inter).

---

## Chunk 1: Build and verify the HTML page

### Task 1: Write the replacement index.html

**Files:**
- Modify: `claude-comparison/index.html` (full replacement)

- [ ] **Step 1: Write the full HTML file**

Replace `claude-comparison/index.html` with the following complete page:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Claude Code vs Claude Chat — What's the Difference?</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet" />
  <style>
    :root {
      --bg: #0a0a0a;
      --surface: #111111;
      --surface2: #181818;
      --border: rgba(255,255,255,0.08);
      --text: #e8e8e8;
      --muted: #888;
      --chat-color: #a78bfa;
      --chat-glow: rgba(167,139,250,0.15);
      --chat-border: rgba(167,139,250,0.3);
      --code-color: #34d399;
      --code-glow: rgba(52,211,153,0.15);
      --code-border: rgba(52,211,153,0.3);
    }

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'Inter', system-ui, sans-serif;
      font-weight: 400;
      line-height: 1.6;
      overflow-x: hidden;
    }

    /* HEADER */
    header {
      padding: 1.25rem 3rem;
      border-bottom: 1px solid var(--border);
      display: flex;
      justify-content: space-between;
      align-items: center;
      position: sticky;
      top: 0;
      background: rgba(10,10,10,0.9);
      backdrop-filter: blur(12px);
      z-index: 100;
    }
    .logo {
      font-size: 0.8rem;
      font-weight: 600;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--muted);
    }
    .logo span { color: var(--text); }
    nav { display: flex; gap: 2rem; }
    nav a {
      font-size: 0.8rem;
      color: var(--muted);
      text-decoration: none;
      letter-spacing: 0.05em;
      transition: color 0.2s;
    }
    nav a:hover { color: var(--text); }

    /* HERO */
    .hero {
      padding: 6rem 3rem 5rem;
      text-align: center;
      max-width: 760px;
      margin: 0 auto;
    }
    .hero-eyebrow {
      font-size: 0.72rem;
      letter-spacing: 0.25em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 1.5rem;
    }
    .hero h1 {
      font-size: clamp(2.2rem, 5vw, 3.8rem);
      font-weight: 700;
      line-height: 1.15;
      letter-spacing: -0.02em;
      margin-bottom: 1.5rem;
      color: #fff;
    }
    .hero h1 .chat { color: var(--chat-color); }
    .hero h1 .code { color: var(--code-color); }
    .hero-sub {
      font-size: 1.05rem;
      color: var(--muted);
      max-width: 520px;
      margin: 0 auto 2.5rem;
      font-style: italic;
    }
    .hero-pills {
      display: flex;
      gap: 1rem;
      justify-content: center;
      flex-wrap: wrap;
    }
    .pill {
      padding: 0.4rem 1.1rem;
      border-radius: 999px;
      font-size: 0.75rem;
      font-weight: 500;
      letter-spacing: 0.06em;
    }
    .pill-chat { background: var(--chat-glow); border: 1px solid var(--chat-border); color: var(--chat-color); }
    .pill-code { background: var(--code-glow); border: 1px solid var(--code-border); color: var(--code-color); }

    /* SPLIT CARDS */
    .cards {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 1px;
      background: var(--border);
      border-top: 1px solid var(--border);
      border-bottom: 1px solid var(--border);
    }
    .card {
      background: var(--surface);
      padding: 3.5rem 3rem;
      position: relative;
      overflow: hidden;
      transition: background 0.3s;
    }
    .card:hover { background: var(--surface2); }
    .card::before {
      content: '';
      position: absolute;
      top: 0; left: 0; right: 0;
      height: 2px;
    }
    .card.chat::before { background: linear-gradient(90deg, transparent, var(--chat-color), transparent); }
    .card.code::before { background: linear-gradient(90deg, transparent, var(--code-color), transparent); }

    .card-glow {
      position: absolute;
      top: -60px; right: -60px;
      width: 250px; height: 250px;
      border-radius: 50%;
      pointer-events: none;
      opacity: 0.6;
    }
    .card.chat .card-glow { background: radial-gradient(circle, var(--chat-glow), transparent 70%); }
    .card.code .card-glow { background: radial-gradient(circle, var(--code-glow), transparent 70%); }

    .card-icon {
      font-size: 2.2rem;
      margin-bottom: 1.25rem;
      display: block;
    }
    .card-label {
      font-size: 0.65rem;
      letter-spacing: 0.22em;
      text-transform: uppercase;
      margin-bottom: 0.4rem;
    }
    .card.chat .card-label { color: var(--chat-color); }
    .card.code .card-label { color: var(--code-color); }

    .card-title {
      font-size: 1.9rem;
      font-weight: 700;
      letter-spacing: -0.02em;
      color: #fff;
      margin-bottom: 0.75rem;
    }
    .card-desc {
      font-size: 0.9rem;
      color: var(--muted);
      line-height: 1.7;
      margin-bottom: 2rem;
      max-width: 380px;
    }
    .feature-list {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 0.65rem;
    }
    .feature-list li {
      display: flex;
      align-items: flex-start;
      gap: 0.6rem;
      font-size: 0.88rem;
      color: var(--text);
    }
    .feature-list li::before {
      content: '→';
      flex-shrink: 0;
      margin-top: 0.05rem;
    }
    .card.chat .feature-list li::before { color: var(--chat-color); }
    .card.code .feature-list li::before { color: var(--code-color); }

    /* COMPARISON TABLE */
    .section {
      padding: 5rem 3rem;
      max-width: 1000px;
      margin: 0 auto;
    }
    .section-label {
      font-size: 0.68rem;
      letter-spacing: 0.25em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 0.75rem;
    }
    .section-title {
      font-size: 1.8rem;
      font-weight: 700;
      letter-spacing: -0.02em;
      color: #fff;
      margin-bottom: 2.5rem;
    }

    .table-wrap { overflow-x: auto; }
    table { width: 100%; border-collapse: collapse; }
    thead th {
      padding: 1rem 1.5rem;
      font-size: 0.72rem;
      letter-spacing: 0.15em;
      text-transform: uppercase;
      color: var(--muted);
      border-bottom: 1px solid var(--border);
      text-align: left;
      font-weight: 500;
    }
    thead th:nth-child(2) { color: var(--chat-color); }
    thead th:nth-child(3) { color: var(--code-color); }
    td {
      padding: 1rem 1.5rem;
      font-size: 0.88rem;
      border-bottom: 1px solid var(--border);
      vertical-align: top;
      line-height: 1.5;
    }
    td:first-child { color: var(--muted); font-size: 0.8rem; font-weight: 500; text-transform: uppercase; letter-spacing: 0.08em; }
    tbody tr:hover td { background: var(--surface); }
    .check { color: #34d399; }
    .cross { color: #f87171; }

    /* WHICH ONE */
    .which-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 1.5rem;
      margin-top: 0;
    }
    .which-card {
      background: var(--surface);
      border-radius: 12px;
      padding: 2rem;
      border: 1px solid var(--border);
      transition: border-color 0.3s, background 0.3s;
    }
    .which-card:hover { background: var(--surface2); }
    .which-card.chat:hover { border-color: var(--chat-border); }
    .which-card.code:hover { border-color: var(--code-border); }
    .which-label {
      font-size: 0.68rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      margin-bottom: 1rem;
      font-weight: 600;
    }
    .which-card.chat .which-label { color: var(--chat-color); }
    .which-card.code .which-label { color: var(--code-color); }
    .which-heading {
      font-size: 1rem;
      font-weight: 600;
      color: #fff;
      margin-bottom: 0.75rem;
    }
    .which-list {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
    }
    .which-list li {
      font-size: 0.83rem;
      color: var(--muted);
      padding-left: 1rem;
      position: relative;
    }
    .which-list li::before {
      content: '·';
      position: absolute;
      left: 0;
      color: var(--muted);
    }

    /* FOOTER */
    footer {
      border-top: 1px solid var(--border);
      padding: 2.5rem 3rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .footer-brand { font-size: 0.8rem; font-weight: 600; color: var(--muted); }
    .footer-brand span { color: var(--text); }
    .footer-note { font-size: 0.75rem; color: var(--muted); }
    .footer-links { display: flex; gap: 1.5rem; }
    .footer-links a { font-size: 0.75rem; color: var(--muted); text-decoration: none; }
    .footer-links a:hover { color: var(--text); }

    /* FADE IN */
    .fade-in { opacity: 0; transform: translateY(20px); transition: opacity 0.7s ease, transform 0.7s ease; }
    .fade-in.visible { opacity: 1; transform: translateY(0); }

    /* RESPONSIVE */
    @media (max-width: 768px) {
      header { padding: 1rem 1.5rem; }
      nav { display: none; }
      .hero { padding: 4rem 1.5rem 3rem; }
      .cards { grid-template-columns: 1fr; }
      .section { padding: 3.5rem 1.5rem; }
      .which-grid { grid-template-columns: 1fr; }
      footer { flex-direction: column; gap: 1rem; text-align: center; }
      .footer-links { justify-content: center; }
    }
  </style>
</head>
<body>

  <!-- HEADER -->
  <header>
    <div class="logo">Anthropic · <span>Product Guide</span></div>
    <nav>
      <a href="#compare">Compare</a>
      <a href="#table">Features</a>
      <a href="#which">Which One?</a>
    </nav>
  </header>

  <!-- HERO -->
  <section class="hero fade-in">
    <div class="hero-eyebrow">Anthropic · Claude Products</div>
    <h1>
      <span class="chat">Claude Chat</span><br>
      vs<br>
      <span class="code">Claude Code</span>
    </h1>
    <p class="hero-sub">Same intelligence. Different superpowers.</p>
    <div class="hero-pills">
      <span class="pill pill-chat">💬 Chat in your browser</span>
      <span class="pill pill-code">⌨️ Code in your terminal</span>
    </div>
  </section>

  <!-- SPLIT CARDS -->
  <div class="cards" id="compare">

    <div class="card chat fade-in">
      <div class="card-glow"></div>
      <span class="card-icon">💬</span>
      <div class="card-label">Claude Chat</div>
      <div class="card-title">For Everyone</div>
      <p class="card-desc">
        A web-based conversational interface at claude.ai. Open your browser, start typing.
        No installation, no configuration — just conversation.
      </p>
      <ul class="feature-list">
        <li>Runs in your browser — no install needed</li>
        <li>General Q&amp;A, writing, research, analysis</li>
        <li>Upload files, images, and documents</li>
        <li>Persistent conversation history</li>
        <li>Collaborative Projects with shared context</li>
        <li>Voice mode (mobile)</li>
        <li>Best for: non-developers, ad-hoc tasks, learning</li>
      </ul>
    </div>

    <div class="card code fade-in">
      <div class="card-glow"></div>
      <span class="card-icon">⌨️</span>
      <div class="card-label">Claude Code</div>
      <div class="card-title">For Developers</div>
      <p class="card-desc">
        A command-line tool that lives inside your codebase. It reads your files,
        runs commands, edits code, and works autonomously across your entire project.
      </p>
      <ul class="feature-list">
        <li>Runs in your terminal as a CLI tool</li>
        <li>Reads, writes, and edits files directly</li>
        <li>Executes shell commands and tests</li>
        <li>Understands your full codebase in context</li>
        <li>Integrates with VS Code, JetBrains, and more</li>
        <li>Agentic: completes multi-step tasks autonomously</li>
        <li>Best for: engineers, coding workflows, automation</li>
      </ul>
    </div>

  </div>

  <!-- COMPARISON TABLE -->
  <section class="section fade-in" id="table">
    <div class="section-label">Side by Side</div>
    <h2 class="section-title">Feature Comparison</h2>
    <div class="table-wrap">
      <table>
        <thead>
          <tr>
            <th>Feature</th>
            <th>💬 Claude Chat</th>
            <th>⌨️ Claude Code</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Interface</td>
            <td>Web browser (claude.ai)</td>
            <td>Terminal / CLI</td>
          </tr>
          <tr>
            <td>Setup required</td>
            <td><span class="check">✓</span> None — open and use</td>
            <td>npm install -g @anthropic-ai/claude-code</td>
          </tr>
          <tr>
            <td>Primary use</td>
            <td>Conversation, writing, research</td>
            <td>Software development, automation</td>
          </tr>
          <tr>
            <td>File access</td>
            <td>Manual upload only</td>
            <td><span class="check">✓</span> Direct read/write to your filesystem</td>
          </tr>
          <tr>
            <td>Runs commands</td>
            <td><span class="cross">✗</span> No</td>
            <td><span class="check">✓</span> Yes — bash, git, npm, tests, etc.</td>
          </tr>
          <tr>
            <td>Codebase awareness</td>
            <td>Paste code manually</td>
            <td><span class="check">✓</span> Full project context automatically</td>
          </tr>
          <tr>
            <td>Agentic operation</td>
            <td>Single-turn responses</td>
            <td><span class="check">✓</span> Multi-step autonomous task completion</td>
          </tr>
          <tr>
            <td>IDE integration</td>
            <td><span class="cross">✗</span> No</td>
            <td><span class="check">✓</span> VS Code, JetBrains, Vim, Emacs</td>
          </tr>
          <tr>
            <td>Image / file upload</td>
            <td><span class="check">✓</span> Yes</td>
            <td>Via filesystem path</td>
          </tr>
          <tr>
            <td>Best for</td>
            <td>Anyone; no coding required</td>
            <td>Developers working in a codebase</td>
          </tr>
          <tr>
            <td>Pricing</td>
            <td>Free tier + Claude Pro ($20/mo)</td>
            <td>Usage-based via Anthropic API</td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>

  <!-- WHICH ONE -->
  <section class="section fade-in" id="which">
    <div class="section-label">Decision Guide</div>
    <h2 class="section-title">Which one should I use?</h2>
    <div class="which-grid">
      <div class="which-card chat">
        <div class="which-label">Use Claude Chat if…</div>
        <div class="which-heading">You want to have a conversation</div>
        <ul class="which-list">
          <li>Writing, editing, or summarising documents</li>
          <li>Research, brainstorming, or learning something new</li>
          <li>Answering quick questions without a dev environment</li>
          <li>Analysing data, images, or uploaded files</li>
          <li>You don't want to install anything</li>
        </ul>
      </div>
      <div class="which-card code">
        <div class="which-label">Use Claude Code if…</div>
        <div class="which-heading">You want to build software</div>
        <ul class="which-list">
          <li>Writing, debugging, or refactoring code in a project</li>
          <li>Running tests, builds, or shell commands hands-free</li>
          <li>Automating multi-step engineering workflows</li>
          <li>Understanding or navigating a large codebase</li>
          <li>You want Claude to act, not just advise</li>
        </ul>
      </div>
    </div>
  </section>

  <!-- FOOTER -->
  <footer>
    <div class="footer-brand">Anthropic · <span>Product Guide</span></div>
    <div class="footer-links">
      <a href="https://claude.ai" target="_blank">Claude Chat →</a>
      <a href="https://docs.anthropic.com/en/docs/claude-code" target="_blank">Claude Code Docs →</a>
      <a href="https://anthropic.com" target="_blank">anthropic.com →</a>
    </div>
    <div class="footer-note">Information current as of March 2026</div>
  </footer>

  <script>
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(el => {
        if (el.isIntersecting) {
          el.target.classList.add('visible');
          observer.unobserve(el.target);
        }
      });
    }, { threshold: 0.08 });
    document.querySelectorAll('.fade-in').forEach(el => observer.observe(el));
  </script>
</body>
</html>
```

- [ ] **Step 2: Verify the file renders correctly**

Open `claude-comparison/index.html` in a browser and confirm:
- Dark background, three sections visible (cards, table, which-one)
- Purple tint on Claude Chat card, teal on Claude Code card
- Table rows highlight on hover
- Responsive at 375px width (cards stack vertically)

- [ ] **Step 3: Commit**

```bash
cd "claude-comparison"
git add index.html docs/superpowers/specs/2026-03-14-claude-code-vs-chat-design.md docs/superpowers/plans/2026-03-14-claude-code-vs-chat.md
git commit -m "feat: replace comparison page with Claude Code vs Claude Chat"
```

---

## Chunk 2: Deploy

### Task 2: Push to GitHub

**Note:** `git push` over HTTPS on Windows requires a credential dialog. The user must push manually or provide a PAT.

- [ ] **Step 1: Instruct user to push**

Run or ask user to run:
```bash
cd "C:/Users/arpan/Documents/ai ceo - vercel deployment/claude-comparison"
git push origin master
```
If prompted, enter GitHub credentials in the Windows dialog.

### Task 3: Deploy to Vercel

- [ ] **Step 1: Deploy via Vercel MCP**

Use the `deploy_to_vercel` MCP tool to deploy the `claude-comparison` directory to the existing project under team `automatewitharpan-2641s-projects`.

- [ ] **Step 2: Confirm deployment URL**

Retrieve and share the live Vercel URL with the user.
