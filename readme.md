<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lhotse — Linux Network Assignment</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0c0f;
    --surface: #10141a;
    --surface2: #161c26;
    --border: #1e2a3a;
    --accent: #00e5ff;
    --accent2: #00ff9d;
    --accent3: #ff6b35;
    --warn: #ffd166;
    --text: #c8d8e8;
    --text-dim: #5a7a9a;
    --text-bright: #eaf4ff;
    --code-bg: #080b0f;
    --code-border: #1a2535;
    --green: #00ff9d;
    --red: #ff4d6d;
  }

- { margin: 0; padding: 0; box-sizing: border-box; }

html { scroll-behavior: smooth; }

body {
background: var(--bg);
color: var(--text);
font-family: 'Syne', sans-serif;
font-size: 16px;
line-height: 1.7;
overflow-x: hidden;
}

/_ GRID BACKGROUND _/
body::before {
content: '';
position: fixed;
inset: 0;
background-image:
linear-gradient(rgba(0,229,255,0.03) 1px, transparent 1px),
linear-gradient(90deg, rgba(0,229,255,0.03) 1px, transparent 1px);
background-size: 40px 40px;
pointer-events: none;
z-index: 0;
}

/_ SCANLINE _/
body::after {
content: '';
position: fixed;
inset: 0;
background: repeating-linear-gradient(
0deg,
transparent,
transparent 2px,
rgba(0,0,0,0.08) 2px,
rgba(0,0,0,0.08) 4px
);
pointer-events: none;
z-index: 0;
}

/_ SIDEBAR NAV _/
.sidebar {
position: fixed;
left: 0;
top: 0;
height: 100vh;
width: 260px;
background: var(--surface);
border-right: 1px solid var(--border);
padding: 32px 0;
z-index: 100;
overflow-y: auto;
display: flex;
flex-direction: column;
}

.sidebar-brand {
padding: 0 24px 28px;
border-bottom: 1px solid var(--border);
margin-bottom: 20px;
}

.sidebar-brand .logo-text {
font-size: 22px;
font-weight: 800;
color: var(--text-bright);
letter-spacing: -0.5px;
display: flex;
align-items: center;
gap: 10px;
}

.logo-dot {
width: 10px;
height: 10px;
background: var(--accent);
border-radius: 50%;
box-shadow: 0 0 12px var(--accent);
animation: pulse 2s infinite;
}

@keyframes pulse {
0%, 100% { opacity: 1; }
50% { opacity: 0.4; }
}

.sidebar-brand .sub {
font-size: 11px;
color: var(--text-dim);
font-family: 'Space Mono', monospace;
margin-top: 4px;
letter-spacing: 1px;
text-transform: uppercase;
}

.nav-section-label {
font-size: 10px;
font-family: 'Space Mono', monospace;
color: var(--text-dim);
text-transform: uppercase;
letter-spacing: 2px;
padding: 0 24px;
margin: 16px 0 6px;
}

.nav-item {
display: flex;
align-items: center;
gap: 10px;
padding: 9px 24px;
color: var(--text-dim);
text-decoration: none;
font-size: 13.5px;
font-weight: 600;
transition: all 0.2s;
border-left: 2px solid transparent;
cursor: pointer;
}

.nav-item:hover, .nav-item.active {
color: var(--text-bright);
background: rgba(0,229,255,0.05);
border-left-color: var(--accent);
}

.nav-item .nav-num {
font-family: 'Space Mono', monospace;
font-size: 10px;
color: var(--accent);
min-width: 20px;
opacity: 0.7;
}

.nav-status {
margin-left: auto;
width: 6px;
height: 6px;
border-radius: 50%;
background: var(--accent2);
box-shadow: 0 0 6px var(--accent2);
}

.sidebar-footer {
margin-top: auto;
padding: 20px 24px 0;
border-top: 1px solid var(--border);
}

.vm-badge {
display: flex;
align-items: center;
gap: 8px;
padding: 10px 14px;
border-radius: 8px;
font-size: 12px;
font-family: 'Space Mono', monospace;
margin-bottom: 8px;
}

.vm-badge.centos {
background: rgba(255,107,53,0.1);
border: 1px solid rgba(255,107,53,0.2);
color: var(--accent3);
}

.vm-badge.ubuntu {
background: rgba(0,229,255,0.08);
border: 1px solid rgba(0,229,255,0.2);
color: var(--accent);
}

/_ MAIN _/
.main {
margin-left: 260px;
min-height: 100vh;
position: relative;
z-index: 1;
}

/_ HERO _/
.hero {
padding: 72px 64px 56px;
border-bottom: 1px solid var(--border);
position: relative;
overflow: hidden;
}

.hero::before {
content: '';
position: absolute;
top: -80px; right: -80px;
width: 500px; height: 500px;
background: radial-gradient(circle, rgba(0,229,255,0.06) 0%, transparent 70%);
pointer-events: none;
}

.hero-tag {
display: inline-flex;
align-items: center;
gap: 8px;
font-family: 'Space Mono', monospace;
font-size: 11px;
color: var(--accent);
background: rgba(0,229,255,0.08);
border: 1px solid rgba(0,229,255,0.2);
padding: 6px 14px;
border-radius: 4px;
letter-spacing: 1.5px;
text-transform: uppercase;
margin-bottom: 24px;
}

.hero h1 {
font-size: 52px;
font-weight: 800;
color: var(--text-bright);
line-height: 1.1;
letter-spacing: -2px;
margin-bottom: 16px;
}

.hero h1 span {
color: var(--accent);
}

.hero-sub {
font-size: 17px;
color: var(--text-dim);
max-width: 560px;
margin-bottom: 36px;
}

.arch-diagram {
background: var(--code-bg);
border: 1px solid var(--border);
border-radius: 12px;
padding: 28px 32px;
font-family: 'Space Mono', monospace;
font-size: 13px;
color: var(--text-dim);
max-width: 640px;
position: relative;
overflow: hidden;
}

.arch-diagram::before {
content: '● ● ●';
position: absolute;
top: 12px; left: 16px;
font-size: 10px;
color: var(--text-dim);
letter-spacing: 6px;
opacity: 0.5;
}

.arch-diagram pre {
margin-top: 16px;
white-space: pre;
color: var(--text);
line-height: 1.5;
}

.arch-diagram pre .server-text { color: var(--accent3); }
.arch-diagram pre .client-text { color: var(--accent); }
.arch-diagram pre .line-text { color: var(--accent2); }

/_ IP CARD _/
.ip-callout {
display: inline-flex;
align-items: center;
gap: 12px;
background: rgba(0,255,157,0.06);
border: 1px solid rgba(0,255,157,0.2);
border-radius: 8px;
padding: 12px 20px;
font-family: 'Space Mono', monospace;
font-size: 13px;
color: var(--accent2);
margin: 20px 0;
}

.ip-callout strong { font-size: 15px; }

/_ SECTIONS _/
.content {
padding: 0 64px 80px;
}

/_ TASK HEADER _/
.task-header {
display: flex;
align-items: center;
gap: 20px;
padding: 48px 0 28px;
border-bottom: 1px solid var(--border);
margin-bottom: 36px;
position: relative;
}

.task-badge {
display: flex;
align-items: center;
justify-content: center;
width: 52px;
height: 52px;
border-radius: 12px;
font-family: 'Space Mono', monospace;
font-weight: 700;
font-size: 15px;
flex-shrink: 0;
}

.task-badge.t0 { background: rgba(255,107,53,0.12); color: var(--accent3); border: 1px solid rgba(255,107,53,0.25); }
.task-badge.t1 { background: rgba(0,229,255,0.1); color: var(--accent); border: 1px solid rgba(0,229,255,0.2); }
.task-badge.t2 { background: rgba(0,255,157,0.08); color: var(--accent2); border: 1px solid rgba(0,255,157,0.2); }
.task-badge.t3 { background: rgba(255,209,102,0.08); color: var(--warn); border: 1px solid rgba(255,209,102,0.2); }
.task-badge.t4 { background: rgba(255,77,109,0.08); color: var(--red); border: 1px solid rgba(255,77,109,0.2); }
.task-badge.t5 { background: rgba(0,229,255,0.06); color: var(--text-dim); border: 1px solid var(--border); }

.task-meta h2 {
font-size: 26px;
font-weight: 800;
color: var(--text-bright);
letter-spacing: -0.5px;
}

.task-meta p {
font-size: 14px;
color: var(--text-dim);
margin-top: 3px;
font-family: 'Space Mono', monospace;
}

/_ STEPS _/
.step {
background: var(--surface);
border: 1px solid var(--border);
border-radius: 12px;
padding: 28px 32px;
margin-bottom: 20px;
position: relative;
transition: border-color 0.2s;
}

.step:hover { border-color: #2a3f5a; }

.step-num {
display: inline-flex;
align-items: center;
gap: 8px;
font-family: 'Space Mono', monospace;
font-size: 11px;
font-weight: 700;
color: var(--accent);
background: rgba(0,229,255,0.08);
border: 1px solid rgba(0,229,255,0.15);
padding: 4px 10px;
border-radius: 4px;
letter-spacing: 1px;
text-transform: uppercase;
margin-bottom: 12px;
}

.step h3 {
font-size: 17px;
font-weight: 700;
color: var(--text-bright);
margin-bottom: 12px;
}

.step p {
font-size: 14.5px;
color: var(--text);
margin-bottom: 14px;
line-height: 1.7;
}

.step p:last-child { margin-bottom: 0; }

/_ CODE BLOCKS _/
.code-block {
background: var(--code-bg);
border: 1px solid var(--code-border);
border-radius: 8px;
overflow: hidden;
margin: 14px 0;
position: relative;
}

.code-header {
display: flex;
align-items: center;
justify-content: space-between;
padding: 10px 16px;
background: rgba(255,255,255,0.02);
border-bottom: 1px solid var(--code-border);
}

.code-lang {
font-family: 'Space Mono', monospace;
font-size: 10px;
color: var(--text-dim);
text-transform: uppercase;
letter-spacing: 1px;
}

.copy-btn {
background: none;
border: 1px solid var(--border);
color: var(--text-dim);
font-family: 'Space Mono', monospace;
font-size: 10px;
padding: 3px 10px;
border-radius: 3px;
cursor: pointer;
transition: all 0.2s;
letter-spacing: 0.5px;
}

.copy-btn:hover {
background: rgba(0,229,255,0.08);
border-color: var(--accent);
color: var(--accent);
}

.code-block pre, .code-block code {
font-family: 'Space Mono', monospace;
font-size: 12.5px;
line-height: 1.7;
color: var(--text);
padding: 18px 20px;
display: block;
overflow-x: auto;
white-space: pre;
}

.code-block .comment { color: #3d5a7a; }
.code-block .key { color: var(--accent3); }
.code-block .val { color: var(--accent2); }
.code-block .cmd { color: var(--accent); }

/_ CONFIG BLOCKS _/
.config-block {
background: #060a0d;
border: 1px solid var(--code-border);
border-left: 3px solid var(--accent2);
border-radius: 0 8px 8px 0;
padding: 20px 24px;
margin: 14px 0;
font-family: 'Space Mono', monospace;
font-size: 12.5px;
line-height: 1.8;
color: var(--text);
overflow-x: auto;
}

/_ CALLOUTS _/
.callout {
display: flex;
gap: 16px;
padding: 18px 22px;
border-radius: 8px;
margin: 16px 0;
font-size: 14px;
line-height: 1.6;
}

.callout-icon {
font-size: 18px;
flex-shrink: 0;
margin-top: 1px;
}

.callout.info {
background: rgba(0,229,255,0.06);
border: 1px solid rgba(0,229,255,0.15);
color: #8cc8d8;
}

.callout.warn {
background: rgba(255,209,102,0.06);
border: 1px solid rgba(255,209,102,0.2);
color: #c8a860;
}

.callout.success {
background: rgba(0,255,157,0.05);
border: 1px solid rgba(0,255,157,0.15);
color: #60c898;
}

.callout.danger {
background: rgba(255,77,109,0.06);
border: 1px solid rgba(255,77,109,0.15);
color: #c86878;
}

/_ SECTION SEPARATOR: Server / Client _/
.vm-section-label {
display: flex;
align-items: center;
gap: 14px;
margin: 32px 0 18px;
}

.vm-section-label .vm-pill {
display: inline-flex;
align-items: center;
gap: 8px;
padding: 8px 18px;
border-radius: 6px;
font-size: 13px;
font-weight: 700;
font-family: 'Space Mono', monospace;
letter-spacing: 0.5px;
}

.vm-pill.server {
background: rgba(255,107,53,0.1);
border: 1px solid rgba(255,107,53,0.25);
color: var(--accent3);
}

.vm-pill.client {
background: rgba(0,229,255,0.08);
border: 1px solid rgba(0,229,255,0.2);
color: var(--accent);
}

.vm-pill svg { width: 14px; height: 14px; }

.vm-line { flex: 1; height: 1px; background: var(--border); }

/_ TABLE _/
table {
width: 100%;
border-collapse: collapse;
font-size: 13.5px;
margin: 14px 0;
font-family: 'Space Mono', monospace;
}

th {
background: var(--surface2);
color: var(--text-dim);
font-size: 11px;
letter-spacing: 1px;
text-transform: uppercase;
padding: 10px 16px;
text-align: left;
border-bottom: 1px solid var(--border);
font-weight: 700;
}

td {
padding: 10px 16px;
border-bottom: 1px solid rgba(30,42,58,0.5);
color: var(--text);
}

tr:last-child td { border-bottom: none; }

td:first-child { color: var(--accent); }

table { border: 1px solid var(--border); border-radius: 8px; overflow: hidden; }

/_ SCREENSHOT MARKER _/
.screenshot-tip {
display: flex;
align-items: center;
gap: 10px;
background: rgba(255,209,102,0.05);
border: 1px dashed rgba(255,209,102,0.25);
border-radius: 6px;
padding: 12px 18px;
font-size: 13px;
color: var(--warn);
margin: 14px 0;
font-family: 'Space Mono', monospace;
}

/_ TROUBLESHOOT _/
.trouble-item {
background: var(--surface2);
border: 1px solid var(--border);
border-radius: 10px;
padding: 24px 28px;
margin-bottom: 16px;
position: relative;
overflow: hidden;
}

.trouble-item::before {
content: '';
position: absolute;
left: 0; top: 0; bottom: 0;
width: 3px;
background: var(--red);
}

.trouble-item h4 {
font-size: 16px;
font-weight: 700;
color: var(--text-bright);
margin-bottom: 10px;
}

.error-tag {
display: inline-block;
background: rgba(255,77,109,0.08);
border: 1px solid rgba(255,77,109,0.2);
color: var(--red);
font-family: 'Space Mono', monospace;
font-size: 11px;
padding: 4px 10px;
border-radius: 4px;
margin-bottom: 12px;
}

.fix-label {
display: inline-flex;
align-items: center;
gap: 6px;
font-size: 12px;
font-weight: 700;
font-family: 'Space Mono', monospace;
color: var(--accent2);
text-transform: uppercase;
letter-spacing: 1px;
margin: 12px 0 8px;
}

/_ PROGRESS BAR _/
.tasks-progress {
display: flex;
gap: 8px;
padding: 20px 64px;
background: var(--surface);
border-bottom: 1px solid var(--border);
position: sticky;
top: 0;
z-index: 50;
}

.prog-item {
flex: 1;
text-align: center;
font-family: 'Space Mono', monospace;
font-size: 10px;
color: var(--text-dim);
padding: 6px;
border-radius: 4px;
background: var(--surface2);
border: 1px solid var(--border);
letter-spacing: 0.5px;
text-transform: uppercase;
text-decoration: none;
transition: all 0.2s;
}

.prog-item:hover {
color: var(--accent);
border-color: rgba(0,229,255,0.3);
background: rgba(0,229,255,0.04);
}

/_ SCROLLBAR _/
::-webkit-scrollbar { width: 6px; height: 6px; }
::-webkit-scrollbar-track { background: var(--bg); }
::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }
::-webkit-scrollbar-thumb:hover { background: #2a3f5a; }

/_ RESPONSIVE _/
@media (max-width: 1100px) {
.sidebar { width: 220px; }
.main { margin-left: 220px; }
.hero { padding: 48px 36px 40px; }
.content { padding: 0 36px 60px; }
.tasks-progress { padding: 16px 36px; }
}

@media (max-width: 768px) {
.sidebar { display: none; }
.main { margin-left: 0; }
.hero { padding: 40px 20px; }
.content { padding: 0 20px 60px; }
.tasks-progress { padding: 12px 20px; display: grid; grid-template-columns: 1fr 1fr 1fr; }
.hero h1 { font-size: 34px; }
}

/_ ANIMATIONS _/
@keyframes fadeIn {
from { opacity: 0; transform: translateY(12px); }
to { opacity: 1; transform: translateY(0); }
}

.hero, .step, .trouble-item {
animation: fadeIn 0.4s ease both;
}

.step:nth-child(2) { animation-delay: 0.05s; }
.step:nth-child(3) { animation-delay: 0.1s; }
.step:nth-child(4) { animation-delay: 0.15s; }

/_ INLINE CODE _/
code {
font-family: 'Space Mono', monospace;
font-size: 12.5px;
background: rgba(0,229,255,0.07);
border: 1px solid rgba(0,229,255,0.12);
color: var(--accent);
padding: 1px 6px;
border-radius: 3px;
}

.step p code { font-size: 12px; }

.highlight { color: var(--accent2); font-weight: 600; }
.highlight-orange { color: var(--accent3); font-weight: 600; }
</style>

</head>
<body>

<!-- SIDEBAR -->
<nav class="sidebar">
  <div class="sidebar-brand">
    <div class="logo-text">
      <span class="logo-dot"></span>
      LHOTSE
    </div>
    <div class="sub">Linux Network Setup</div>
  </div>

  <div class="nav-section-label">Setup</div>
  <a href="#presetup" class="nav-item">
    <span class="nav-num">00</span>
    VirtualBox Network
  </a>
  <a href="#centos-init" class="nav-item">
    <span class="nav-num">01</span>
    CentOS Initial Setup
  </a>

  <div class="nav-section-label">Tasks</div>
  <a href="#task1" class="nav-item">
    <span class="nav-num">T1</span>
    DNS Server (BIND)
    <span class="nav-status"></span>
  </a>
  <a href="#task2" class="nav-item">
    <span class="nav-num">T2</span>
    Email Server
  </a>
  <a href="#task3" class="nav-item">
    <span class="nav-num">T3</span>
    Apache HTTPS
  </a>
  <a href="#task4" class="nav-item">
    <span class="nav-num">T4</span>
    FTP Server
  </a>

  <div class="nav-section-label">Reference</div>
  <a href="#troubleshoot" class="nav-item">
    <span class="nav-num">TS</span>
    Troubleshooting
  </a>

  <div class="sidebar-footer">
    <div class="vm-badge centos">
      <svg viewBox="0 0 24 24" width="14" fill="currentColor"><circle cx="12" cy="12" r="10"/></svg>
      CentOS — SERVER
    </div>
    <div class="vm-badge ubuntu">
      <svg viewBox="0 0 24 24" width="14" fill="currentColor"><circle cx="12" cy="12" r="10"/></svg>
      Ubuntu — CLIENT
    </div>
  </div>
</nav>

<!-- MAIN -->
<div class="main">

  <!-- STICKY PROGRESS NAV -->
  <div class="tasks-progress">
    <a href="#presetup" class="prog-item">Pre-Setup</a>
    <a href="#centos-init" class="prog-item">Init</a>
    <a href="#task1" class="prog-item">DNS</a>
    <a href="#task2" class="prog-item">Email</a>
    <a href="#task3" class="prog-item">Web</a>
    <a href="#task4" class="prog-item">FTP</a>
    <a href="#troubleshoot" class="prog-item">Fixes</a>
  </div>

  <!-- HERO -->
  <div class="hero">
    <div class="hero-tag">
      <svg width="8" height="8" viewBox="0 0 8 8" fill="currentColor"><circle cx="4" cy="4" r="4"/></svg>
      Group: Lhotse &nbsp;|&nbsp; CentOS Server + Ubuntu Client
    </div>
    <h1>Linux<br><span>Network</span><br>Assignment</h1>
    <p class="hero-sub">
      Building a small company network inside VirtualBox. CentOS acts as the server; Ubuntu acts as the client connecting to all services.
    </p>

    <div class="arch-diagram">
      <pre>              VirtualBox Network
                     │
       ┌─────────────┴─────────────┐
       │                           │

┌──────────────┐ ┌──────────────┐
│ <span class="server-text">CentOS VM</span> │ │ <span class="client-text">Ubuntu VM</span> │
│ (SERVER) │ │ (CLIENT) │
│ │ │ │
│ DNS :53 │ │ Test DNS │
│ SMTP :587 │◄──────────│ Thunderbird │
│ IMAP :993 │ │ Firefox │
│ HTTP :80/443 │ │ FileZilla │
│ FTP :21 │ │ │
└──────────────┘ └──────────────┘
<span class="server-text">192.168.56.101</span> <span class="client-text">192.168.56.102</span></pre>

</div>

    <div class="ip-callout">
      <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>
      Server Host-Only IP: <strong>192.168.56.101</strong> &nbsp;|&nbsp; Client: <strong>192.168.56.102</strong>
    </div>

  </div>

  <!-- CONTENT -->
  <div class="content">

    <!-- ===================== PRE-SETUP ===================== -->
    <div id="presetup">
      <div class="task-header">
        <div class="task-badge t0">00</div>
        <div class="task-meta">
          <h2>VirtualBox Network Configuration</h2>
          <p>pre-setup · do this before anything else</p>
        </div>
      </div>

      <div class="step">
        <div class="step-num">⚠ Important — Read First</div>
        <h3>Use Your Existing Host-Only Adapter</h3>
        <p>
          VirtualBox typically already has a Host-Only adapter called <code>VirtualBox Host-Only Ethernet Adapter</code> (Windows) or <code>vboxnet0</code> (Mac/Linux). <span class="highlight">Do NOT create a new one</span> — just use the existing one. Here's how to confirm it exists and configure both VMs to use it.
        </p>
        <div class="callout info">
          <span class="callout-icon">ℹ️</span>
          <div>To verify: open VirtualBox → <strong>File → Host Network Manager</strong> (or <strong>Tools → Network</strong> in newer versions). You should see an existing adapter listed — note its name. It's usually already assigned the subnet <code>192.168.56.0/24</code>.</div>
        </div>
      </div>

      <div class="step">
        <div class="step-num">Step 1</div>
        <h3>Configure the CentOS VM Network Adapters</h3>
        <p>In VirtualBox, click on your <span class="highlight-orange">CentOS VM</span> → <strong>Settings → Network</strong>:</p>
        <p><strong>Adapter 1:</strong> Enable → Attached to: <code>NAT</code> — this gives internet access.</p>
        <p><strong>Adapter 2:</strong> Enable → Attached to: <code>Host-Only Adapter</code> → Select the existing adapter from the dropdown (e.g., <code>VirtualBox Host-Only Ethernet Adapter</code> or <code>vboxnet0</code>). Do NOT click Create — just pick what's already there.</p>
        <div class="callout success">
          <span class="callout-icon">✅</span>
          <div>Click <strong>OK</strong> to save. Repeat the exact same steps for the Ubuntu VM.</div>
        </div>
      </div>

      <div class="step">
        <div class="step-num">Step 2</div>
        <h3>Configure the Ubuntu VM Network Adapters</h3>
        <p>Click on your <span class="highlight">Ubuntu VM</span> → <strong>Settings → Network</strong>:</p>
        <p><strong>Adapter 1:</strong> Enable → Attached to: <code>NAT</code></p>
        <p><strong>Adapter 2:</strong> Enable → Attached to: <code>Host-Only Adapter</code> → Pick the same existing adapter.</p>
        <div class="callout warn">
          <span class="callout-icon">⚠️</span>
          <div>Both VMs <strong>must</strong> use the same Host-Only adapter name. If the dropdown is empty, go to <strong>File → Host Network Manager</strong> and confirm the adapter is enabled there.</div>
        </div>
      </div>
    </div>

    <!-- ===================== CENTOS INIT ===================== -->
    <div id="centos-init">
      <div class="task-header">
        <div class="task-badge t0">01</div>
        <div class="task-meta">
          <h2>CentOS Server — Initial Setup</h2>
          <p>foundational commands · run before all tasks</p>
        </div>
      </div>

      <div class="vm-section-label">
        <div class="vm-pill server">
          <svg viewBox="0 0 24 24" fill="currentColor"><rect x="2" y="3" width="20" height="14" rx="2"/><line x1="8" y1="21" x2="16" y2="21" stroke="currentColor" stroke-width="2"/><line x1="12" y1="17" x2="12" y2="21" stroke="currentColor" stroke-width="2"/></svg>
          CentOS Server
        </div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 1</div>
        <h3>Update System & Set Hostname</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Update all packages</span>

sudo dnf update -y

<span class="comment"># Set your hostname</span>
sudo hostnamectl set-hostname server.lhotse.com

<span class="comment"># Verify it changed</span>
hostnamectl</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 2</div>
        <h3>Check Your Host-Only IP Address</h3>
        <p>Run this to see all network interfaces and find your Host-Only adapter IP:</p>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>ip addr show</pre>
        </div>
        <p>Look for an interface like <code>enp0s8</code> (the second adapter). If it shows <code>192.168.56.x</code> — you're good. If it has no IP, run:</p>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Check which interfaces exist</span>

nmcli device status

<span class="comment"># Connect the host-only adapter (replace enp0s8 if yours is named differently)</span>
sudo nmcli device connect enp0s8

<span class="comment"># Verify it got an IP</span>
ip addr show enp0s8</pre>

</div>
<div class="ip-callout">
📌 Your CentOS Host-Only IP (write this down): <strong>192.168.56.101</strong>
</div>
</div>

      <div class="step">
        <div class="step-num">Step 3</div>
        <h3>Disable Firewall & SELinux Temporarily</h3>
        <p>This prevents config headaches while setting things up. You'll re-enable the firewall later per-service.</p>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Stop and disable firewall while configuring</span>

sudo systemctl stop firewalld
sudo systemctl disable firewalld

<span class="comment"># Set SELinux to permissive (avoids cryptic permission errors)</span>
sudo setenforce 0
sudo sed -i 's/SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config</pre>

</div>
</div>
</div>

    <!-- ===================== TASK 1: DNS ===================== -->
    <div id="task1">
      <div class="task-header">
        <div class="task-badge t1">T1</div>
        <div class="task-meta">
          <h2>DNS Server using BIND</h2>
          <p>task 1 · bind9 · forward + reverse zones</p>
        </div>
      </div>

      <div class="vm-section-label">
        <div class="vm-pill server">CentOS Server</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 1</div>
        <h3>Install BIND</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo dnf install bind bind-utils -y</pre>
        </div>
      </div>

      <div class="step">
        <div class="step-num">Step 2</div>
        <h3>Configure Main BIND Config File</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/named.conf</pre>
        </div>
        <p>Replace the contents with the following (the IP <code>192.168.56.101</code> is your CentOS Host-Only IP):</p>
        <div class="config-block">options {
    listen-on port 53 { 127.0.0.1; 192.168.56.101; };
    listen-on-v6 port 53 { ::1; };
    directory       "/var/named";
    dump-file       "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    allow-query     { localhost; 192.168.56.0/24; };
    recursion yes;
    forwarders { 8.8.8.8; 8.8.4.4; };
    dnssec-validation yes;

};

zone "lhotse.com" IN {
type master;
file "/var/named/lhotse.com.zone";
allow-update { none; };
};

zone "56.168.192.in-addr.arpa" IN {
type master;
file "/var/named/lhotse.com.rev";
allow-update { none; };
};</div>

<div class="callout info">
<span class="callout-icon">ℹ️</span>
<div>The <code>forwarders</code> directive sends unknown DNS queries (like <code>google.com</code>) to Google's DNS so the client still has internet name resolution.</div>
</div>
</div>

      <div class="step">
        <div class="step-num">Step 3</div>
        <h3>Create the Forward Zone File</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /var/named/lhotse.com.zone</pre>
        </div>
        <div class="config-block">$TTL 86400

@ IN SOA server.lhotse.com. admin.lhotse.com. (
2026031101 ; Serial — update this when you modify the file
3600 ; Refresh — how often secondaries check for updates
1800 ; Retry — how often to retry if refresh fails
604800 ; Expire — how long secondary serves data after expiry
86400 ) ; Minimum TTL

; Name servers
@ IN NS server.lhotse.com.

; A Records (hostnames → IPs)
server IN A 192.168.56.101
client IN A 192.168.56.102
www IN A 192.168.56.101
portal IN A 192.168.56.101

; Mail record
@ IN MX 10 server.lhotse.com.</div>

</div>

      <div class="step">
        <div class="step-num">Step 4</div>
        <h3>Create the Reverse Zone File</h3>
        <p>This maps IPs back to hostnames (reverse DNS lookups).</p>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /var/named/lhotse.com.rev</pre>
        </div>
        <div class="config-block">$TTL 86400

@ IN SOA server.lhotse.com. admin.lhotse.com. (
2026031101
3600
1800
604800
86400 )

@ IN NS server.lhotse.com.

; PTR Records — only the last octet of the IP goes here
101 IN PTR server.lhotse.com.
102 IN PTR client.lhotse.com.</div>

</div>

      <div class="step">
        <div class="step-num">Step 5</div>
        <h3>Set Permissions, Validate & Start BIND</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Fix ownership so the named daemon can read the files</span>

sudo chown named:named /var/named/lhotse.com.zone
sudo chown named:named /var/named/lhotse.com.rev

<span class="comment"># Check syntax BEFORE starting — this catches typos</span>
sudo named-checkconf
sudo named-checkzone lhotse.com /var/named/lhotse.com.zone
sudo named-checkzone 56.168.192.in-addr.arpa /var/named/lhotse.com.rev

<span class="comment"># Both should say "OK" — if not, fix the error it shows</span>

<span class="comment"># Start and enable BIND</span>
sudo systemctl start named
sudo systemctl enable named
sudo systemctl status named</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 6</div>
        <h3>Re-enable Firewall and Open DNS Port</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo systemctl start firewalld

sudo systemctl enable firewalld
sudo firewall-cmd --permanent --add-service=dns
sudo firewall-cmd --reload</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 7</div>
        <h3>Test DNS on the Server Itself</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Forward lookups</span>

nslookup server.lhotse.com 127.0.0.1
nslookup client.lhotse.com 127.0.0.1
nslookup www.lhotse.com 127.0.0.1

<span class="comment"># Test external resolution (the forwarder)</span>
nslookup google.com 127.0.0.1

<span class="comment"># Reverse lookup</span>
dig -x 192.168.56.101
dig -x 192.168.56.102

<span class="comment"># Full details</span>
dig server.lhotse.com</pre>

</div>
<div class="screenshot-tip">📸 Screenshot: nslookup + dig results, reverse lookup, forward lookup</div>
</div>

      <div class="vm-section-label" style="margin-top:32px">
        <div class="vm-pill client">Ubuntu Client</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 8</div>
        <h3>Configure Ubuntu to Use Your DNS Server</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/systemd/resolved.conf</pre>
        </div>
        <p>Find the <code>[Resolve]</code> section. <strong>Uncomment</strong> the <code>DNS=</code> and <code>Domains=</code> lines (remove the <code>#</code>) and set them like this:</p>
        <div class="config-block">[Resolve]

DNS=192.168.56.101
Domains=lhotse.com</div>

<div class="code-block">
<div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
<pre><span class="comment"># Apply the changes</span>
sudo systemctl restart systemd-resolved

<span class="comment"># Test from the Ubuntu client</span>
nslookup server.lhotse.com
ping server.lhotse.com
ping www.lhotse.com</pre>

</div>
<div class="screenshot-tip">📸 Screenshot: nslookup from Ubuntu client resolving server.lhotse.com</div>
</div>
</div>

    <!-- ===================== TASK 2: EMAIL ===================== -->
    <div id="task2">
      <div class="task-header">
        <div class="task-badge t2">T2</div>
        <div class="task-meta">
          <h2>Email Server (Postfix + Dovecot + SSL)</h2>
          <p>task 2 · smtp port 587 · imap port 993 · self-signed tls</p>
        </div>
      </div>

      <div class="vm-section-label">
        <div class="vm-pill server">CentOS Server</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 1</div>
        <h3>Install Postfix, Dovecot & Mail Tools</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo dnf install postfix dovecot -y

sudo dnf install mailx -y <span class="comment"># command-line tool for testing</span></pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 2</div>
        <h3>Create Self-Signed SSL Certificate for Email</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo mkdir -p /etc/ssl/lhotse

cd /etc/ssl/lhotse

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
 -keyout mail.lhotse.com.key \
 -out mail.lhotse.com.crt \
 -subj "/C=NP/ST=Bagmati/L=Kathmandu/O=Lhotse/CN=mail.lhotse.com"

<span class="comment"># Restrict key permissions — important for security</span>
sudo chmod 600 mail.lhotse.com.key</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 3</div>
        <h3>Configure Postfix (main.cf)</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/postfix/main.cf</pre>
        </div>
        <p>Find and edit (or add at the bottom) these lines:</p>
        <div class="config-block">myhostname = server.lhotse.com

mydomain = lhotse.com
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8, 192.168.56.0/24
home_mailbox = Maildir/

# TLS settings

smtpd_tls_cert_file = /etc/ssl/lhotse/mail.lhotse.com.crt
smtpd_tls_key_file = /etc/ssl/lhotse/mail.lhotse.com.key
smtpd_use_tls = yes
smtpd_tls_security_level = may
smtp_tls_security_level = may
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_recipient_restrictions = permit_sasl_authenticated, permit_mynetworks, reject_unauth_destination</div>

<div class="callout warn">
<span class="callout-icon">⚠️</span>
<div>Use <code>yes</code> / <code>no</code> for boolean values in Postfix — NOT <code>true</code> / <code>false</code>. Using <code>true</code> will cause Postfix to fail to start.</div>
</div>
</div>

      <div class="step">
        <div class="step-num">Step 4</div>
        <h3>Enable Submission Port in Postfix (master.cf)</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/postfix/master.cf</pre>
        </div>
        <p>Find these lines and <strong>uncomment</strong> them (remove the leading <code>#</code>):</p>
        <div class="config-block">submission inet n       -       n       -       -       smtpd

-o syslog_name=postfix/submission
-o smtpd_tls_security_level=encrypt
-o smtpd_sasl_auth_enable=yes
-o smtpd_recipient_restrictions=permit_sasl_authenticated,reject</div>

</div>

      <div class="step">
        <div class="step-num">Step 5</div>
        <h3>Configure Dovecot (4 Files)</h3>
        <p><strong>File 1:</strong> <code>/etc/dovecot/dovecot.conf</code> — enable protocols</p>
        <div class="config-block">protocols = imap pop3 lmtp</div>

        <p style="margin-top:14px"><strong>File 2:</strong> <code>/etc/dovecot/conf.d/10-mail.conf</code> — set maildir location</p>
        <div class="config-block">mail_location = maildir:~/Maildir</div>

        <p style="margin-top:14px"><strong>File 3:</strong> <code>/etc/dovecot/conf.d/10-auth.conf</code> — allow plain auth</p>
        <div class="config-block">disable_plaintext_auth = no

auth_mechanisms = plain login</div>

        <p style="margin-top:14px"><strong>File 4:</strong> <code>/etc/dovecot/conf.d/10-ssl.conf</code> — point to our SSL cert</p>
        <div class="config-block">ssl = yes

ssl_cert = &lt;/etc/ssl/lhotse/mail.lhotse.com.crt
ssl_key = &lt;/etc/ssl/lhotse/mail.lhotse.com.key</div>

        <p style="margin-top:14px"><strong>File 5:</strong> <code>/etc/dovecot/conf.d/10-master.conf</code> — add auth socket for Postfix</p>
        <p>Inside the <code>service auth { }</code> block, add/uncomment:</p>
        <div class="config-block">unix_listener /var/spool/postfix/private/auth {
    mode = 0660
    user = postfix
    group = postfix

}</div>

</div>

      <div class="step">
        <div class="step-num">Step 6</div>
        <h3>Create Test Users</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Create user1 and user2</span>

sudo useradd -m user1
sudo passwd user1 <span class="comment"># set password e.g. Password123</span>
sudo useradd -m user2
sudo passwd user2

<span class="comment"># Create Maildir folder structure for each user</span>
sudo -u user1 mkdir -p /home/user1/Maildir/{new,cur,tmp}
sudo -u user2 mkdir -p /home/user2/Maildir/{new,cur,tmp}</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 7</div>
        <h3>Start Services & Open Firewall Ports</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo systemctl start postfix dovecot

sudo systemctl enable postfix dovecot
sudo systemctl status postfix
sudo systemctl status dovecot

<span class="comment"># Open all email-related ports</span>
sudo firewall-cmd --permanent --add-service=smtp
sudo firewall-cmd --permanent --add-service=smtps
sudo firewall-cmd --permanent --add-service=imap
sudo firewall-cmd --permanent --add-service=imaps
sudo firewall-cmd --permanent --add-service=pop3
sudo firewall-cmd --permanent --add-service=pop3s
sudo firewall-cmd --permanent --add-port=587/tcp
sudo firewall-cmd --reload</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 8</div>
        <h3>Test Email from Command Line</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Send a test email from user1 to user2</span>

echo "Hello from user1" | mail -s "Test Email" user2@lhotse.com

<span class="comment"># Verify user2 received it</span>
sudo ls /home/user2/Maildir/new/

<span class="comment"># Check mail logs if something is wrong</span>
sudo tail -f /var/log/maillog</pre>

</div>
</div>

      <div class="vm-section-label" style="margin-top:32px">
        <div class="vm-pill client">Ubuntu Client</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 9</div>
        <h3>Set Up Thunderbird on Ubuntu</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo apt install thunderbird -y</pre>
        </div>
        <p>Open Thunderbird and configure two accounts with these settings:</p>
        <table>
          <thead>
            <tr><th>Setting</th><th>Value (User 1)</th></tr>
          </thead>
          <tbody>
            <tr><td>Name</td><td>User One</td></tr>
            <tr><td>Email</td><td>user1@lhotse.com</td></tr>
            <tr><td>Incoming (IMAP)</td><td>192.168.56.101 · Port 993 · SSL/TLS</td></tr>
            <tr><td>Outgoing (SMTP)</td><td>192.168.56.101 · Port 587 · STARTTLS</td></tr>
            <tr><td>Authentication</td><td>Normal Password</td></tr>
          </tbody>
        </table>
        <div class="callout warn">
          <span class="callout-icon">⚠️</span>
          <div>Thunderbird will warn about the self-signed certificate. Click <strong>Accept</strong> / <strong>Confirm Security Exception</strong>. Do the same setup for user2@lhotse.com.</div>
        </div>
        <div class="screenshot-tip">📸 Screenshot: Thunderbird setup, sent email, received in inbox, SSL certificate info</div>
      </div>
    </div>

    <!-- ===================== TASK 3: APACHE ===================== -->
    <div id="task3">
      <div class="task-header">
        <div class="task-badge t3">T3</div>
        <div class="task-meta">
          <h2>Apache Web Server with HTTPS</h2>
          <p>task 3 · two virtual hosts · http → https redirect · ssl/tls</p>
        </div>
      </div>

      <div class="vm-section-label">
        <div class="vm-pill server">CentOS Server</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 1</div>
        <h3>Install Apache and SSL Module</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo dnf install httpd mod_ssl -y</pre>
        </div>
      </div>

      <div class="step">
        <div class="step-num">Step 2</div>
        <h3>Create SSL Certificate for the Web Server</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/lhotse/web.lhotse.com.key \
    -out /etc/ssl/lhotse/web.lhotse.com.crt \
    -subj "/C=NP/ST=Bagmati/L=Kathmandu/O=Lhotse/CN=www.lhotse.com"</pre>
        </div>
      </div>

      <div class="step">
        <div class="step-num">Step 3</div>
        <h3>Create Website HTML Files</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Create directories for both sites</span>

sudo mkdir -p /var/www/site1
sudo mkdir -p /var/www/site2

<span class="comment"># Website 1</span>
sudo nano /var/www/site1/index.html</pre>

</div>
<div class="config-block">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;&lt;title&gt;Lhotse - Site 1&lt;/title&gt;&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;Welcome to Lhotse Main Website&lt;/h1&gt;
&lt;p&gt;Server: server.lhotse.com | Secure HTTPS connection&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;</div>
<div class="code-block">
<div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
<pre>sudo nano /var/www/site2/index.html</pre>
</div>
<div class="config-block">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;&lt;title&gt;Lhotse - Site 2&lt;/title&gt;&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;Lhotse Secondary Website&lt;/h1&gt;
&lt;p&gt;This is a second virtual host on server.lhotse.com&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;</div>
</div>

      <div class="step">
        <div class="step-num">Step 4</div>
        <h3>Create Virtual Host Config Files</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/httpd/conf.d/site1.conf</pre>
        </div>
        <div class="config-block">&lt;VirtualHost *:80&gt;
    ServerName www.lhotse.com
    DocumentRoot /var/www/site1
    Redirect permanent / https://www.lhotse.com/

&lt;/VirtualHost&gt;

&lt;VirtualHost \*:443&gt;
ServerName www.lhotse.com
DocumentRoot /var/www/site1

    SSLEngine on
    SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
    SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key

    &lt;Directory /var/www/site1&gt;
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    &lt;/Directory&gt;

&lt;/VirtualHost&gt;</div>

        <div class="code-block" style="margin-top:16px">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/httpd/conf.d/site2.conf</pre>
        </div>
        <div class="config-block">&lt;VirtualHost *:80&gt;
    ServerName portal.lhotse.com
    DocumentRoot /var/www/site2
    Redirect permanent / https://portal.lhotse.com/

&lt;/VirtualHost&gt;

&lt;VirtualHost \*:443&gt;
ServerName portal.lhotse.com
DocumentRoot /var/www/site2

    SSLEngine on
    SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
    SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key

    &lt;Directory /var/www/site2&gt;
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    &lt;/Directory&gt;

&lt;/VirtualHost&gt;</div>

</div>

      <div class="step">
        <div class="step-num">Step 5</div>
        <h3>Fix the Default SSL Config (Common Error)</h3>
        <p>Apache's default ssl.conf references a certificate that doesn't exist. This will cause <code>apachectl configtest</code> to fail. Fix it before starting:</p>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/httpd/conf.d/ssl.conf</pre>
        </div>
        <p>Find these lines and update them to point to your certificates:</p>
        <div class="config-block"><span class="comment"># Find and change FROM:</span>

SSLCertificateFile /etc/pki/tls/certs/localhost.crt
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key

<span class="comment"># Change TO:</span>
SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key</div>

</div>

      <div class="step">
        <div class="step-num">Step 6</div>
        <h3>Test Config, Start Apache & Open Firewall</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># ALWAYS test before starting</span>

sudo apachectl configtest
<span class="comment"># Should say: Syntax OK</span>

sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl status httpd

<span class="comment"># Open HTTP and HTTPS in firewall</span>
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload</pre>

</div>
</div>

      <div class="vm-section-label" style="margin-top:32px">
        <div class="vm-pill client">Ubuntu Client</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 7</div>
        <h3>Test Websites from Ubuntu Client</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Test from terminal (the -k flag ignores self-signed cert warning)</span>

curl -k https://www.lhotse.com
curl -k https://portal.lhotse.com</pre>

</div>
<p>Or open Firefox and navigate to <code>https://www.lhotse.com</code> and <code>https://portal.lhotse.com</code>. Accept the self-signed certificate warning.</p>
<div class="screenshot-tip">📸 Screenshot: Both sites loading in Firefox with the padlock (HTTPS), certificate details popup</div>
</div>
</div>

    <!-- ===================== TASK 4: FTP ===================== -->
    <div id="task4">
      <div class="task-header">
        <div class="task-badge t4">T4</div>
        <div class="task-meta">
          <h2>Extra Service — FTP Server (vsftpd)</h2>
          <p>task 4 · vsftpd · passive mode · filezilla client</p>
        </div>
      </div>

      <div class="vm-section-label">
        <div class="vm-pill server">CentOS Server</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 1</div>
        <h3>Install vsftpd</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo dnf install vsftpd -y</pre>
        </div>
      </div>

      <div class="step">
        <div class="step-num">Step 2</div>
        <h3>Configure vsftpd</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo nano /etc/vsftpd/vsftpd.conf</pre>
        </div>
        <p>Find and set these options (or add them at the bottom if missing):</p>
        <div class="config-block">anonymous_enable=NO

local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=YES
listen_ipv6=NO
pam_service_name=vsftpd
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user_list

# Passive mode — required for VirtualBox networking to work properly

pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000

# Lock users to their home directory

chroot_local_user=YES
allow_writeable_chroot=YES</div>

</div>

      <div class="step">
        <div class="step-num">Step 3</div>
        <h3>Add Users to the Allowed List</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>echo "user1" | sudo tee -a /etc/vsftpd/user_list

echo "user2" | sudo tee -a /etc/vsftpd/user_list

<span class="comment"># Verify they're in the list</span>
cat /etc/vsftpd/user_list</pre>

</div>
</div>

      <div class="step">
        <div class="step-num">Step 4</div>
        <h3>Start vsftpd & Open Firewall</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo systemctl start vsftpd

sudo systemctl enable vsftpd
sudo systemctl status vsftpd

sudo firewall-cmd --permanent --add-service=ftp
sudo firewall-cmd --permanent --add-port=40000-50000/tcp
sudo firewall-cmd --reload</pre>

</div>
</div>

      <div class="vm-section-label" style="margin-top:32px">
        <div class="vm-pill client">Ubuntu Client</div>
        <div class="vm-line"></div>
      </div>

      <div class="step">
        <div class="step-num">Step 5</div>
        <h3>Test FTP from Ubuntu Client</h3>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo apt install ftp filezilla -y

<span class="comment"># Command-line test</span>
ftp 192.168.56.101
<span class="comment"># Log in with user1 / Password123</span>
<span class="comment"># Commands: ls, put testfile.txt, get testfile.txt, quit</span></pre>

</div>
<p>Or open <strong>FileZilla</strong> and connect with:</p>
<table>
<thead><tr><th>Field</th><th>Value</th></tr></thead>
<tbody>
<tr><td>Host</td><td>192.168.56.101</td></tr>
<tr><td>Username</td><td>user1</td></tr>
<tr><td>Password</td><td>Password123</td></tr>
<tr><td>Port</td><td>21</td></tr>
</tbody>
</table>
<div class="screenshot-tip">📸 Screenshot: FileZilla connected and showing home directory, file transfer in both directions</div>
</div>
</div>

    <!-- ===================== TROUBLESHOOTING ===================== -->
    <div id="troubleshoot">
      <div class="task-header">
        <div class="task-badge t5">TS</div>
        <div class="task-meta">
          <h2>Troubleshooting — Issues &amp; Fixes</h2>
          <p>common errors encountered and how they were resolved</p>
        </div>
      </div>

      <div class="callout danger">
        <span class="callout-icon">🔴</span>
        <div>Replace these sections with your <strong>own actual issues</strong> encountered during setup. The examples below are common ones — document what specifically happened on your machine.</div>
      </div>

      <div class="trouble-item">
        <div class="error-tag">5.1 — Dovecot Port Conflict</div>
        <h4>Dovecot Failed to Start — Address Already in Use</h4>
        <p>Error in logs: <code>service(submission-login): listen(*, 587) failed: Address already in use</code></p>
        <p>Port 587 was already occupied by Postfix's submission service, so Dovecot could not bind to the same port.</p>
        <div class="fix-label">✓ Fix Applied</div>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre><span class="comment"># Find where submission-login was defined in Dovecot</span>

grep -R "submission" /etc/dovecot

<span class="comment"># Comment out the submission-login block in the relevant conf file</span>
<span class="comment"># Then restart</span>
sudo systemctl restart dovecot</pre>

</div>
</div>

      <div class="trouble-item">
        <div class="error-tag">5.2 — Email Delivery Failure</div>
        <h4>Emails Stuck in Queue — Connection Refused</h4>
        <p>When sending via <code>mail</code> command, output showed: <code>connect to groupname.com:25: Connection refused</code>. Postfix tried to deliver the email externally instead of handling it locally.</p>
        <div class="fix-label">✓ Fix Applied</div>
        <p>The domain in <code>mydestination</code> was incorrect. Corrected <code>/etc/postfix/main.cf</code> to include:</p>
        <div class="config-block">mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain</div>
        <div class="code-block">
          <div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
          <pre>sudo systemctl restart postfix</pre>
        </div>
      </div>

      <div class="trouble-item">
        <div class="error-tag">5.3 — Thunderbird STARTTLS Error</div>
        <h4>Thunderbird: "Must issue a STARTTLS command first"</h4>
        <p>Email sending from Thunderbird failed because the SMTP configuration was not initiating TLS encryption correctly.</p>
        <div class="fix-label">✓ Fix Applied</div>
        <p>Updated Thunderbird SMTP settings to match exactly:</p>
        <table>
          <thead><tr><th>Setting</th><th>Value</th></tr></thead>
          <tbody>
            <tr><td>Server</td><td>192.168.56.101</td></tr>
            <tr><td>Port</td><td>587</td></tr>
            <tr><td>Encryption</td><td>STARTTLS</td></tr>
            <tr><td>Authentication</td><td>Normal Password</td></tr>
          </tbody>
        </table>
      </div>

      <div class="trouble-item">
        <div class="error-tag">5.4 — Postfix Boolean Config Error</div>
        <h4>Postfix: "bad boolean configuration: smtpd_use_tls = true"</h4>
        <p>Postfix failed to start because <code>true</code> was used instead of <code>yes</code> — Postfix only accepts <code>yes</code> or <code>no</code> for boolean values.</p>
        <div class="fix-label">✓ Fix Applied</div>
        <div class="config-block"><span class="comment"># Changed in /etc/postfix/main.cf:</span>

smtpd_use_tls = yes <span class="comment"># was: true</span></div>

</div>

      <div class="trouble-item">
        <div class="error-tag">5.5 — Apache SSL Cert Missing</div>
        <h4>Apache: "SSLCertificateFile does not exist"</h4>
        <p>Running <code>apachectl configtest</code> failed because the default <code>ssl.conf</code> referenced <code>/etc/pki/tls/certs/localhost.crt</code> which was never created.</p>
        <div class="fix-label">✓ Fix Applied</div>
        <p>Updated <code>/etc/httpd/conf.d/ssl.conf</code> to reference the certificate created earlier:</p>
        <div class="config-block">SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt

SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key</div>

</div>

      <div class="trouble-item">
        <div class="error-tag">5.6 — FTP 530 Permission Denied</div>
        <h4>FileZilla: "530 Permission denied"</h4>
        <p>FTP login failed because local user authentication was not enabled in vsftpd config.</p>
        <div class="fix-label">✓ Fix Applied</div>
        <p>Set the following in <code>/etc/vsftpd/vsftpd.conf</code>:</p>
        <div class="config-block">anonymous_enable=NO

local_enable=YES
write_enable=YES</div>

<div class="code-block">
<div class="code-header"><span class="code-lang">bash</span><button class="copy-btn" onclick="copyCode(this)">copy</button></div>
<pre>sudo systemctl restart vsftpd</pre>
</div>
</div>

      <div class="callout success" style="margin-top: 32px">
        <span class="callout-icon">✅</span>
        <div>
          <strong>All services configured successfully:</strong> DNS (BIND), Email (Postfix + Dovecot), Web Server (Apache HTTPS), and FTP (vsftpd). All tested from Ubuntu client via nslookup, Thunderbird, Firefox, and FileZilla.
        </div>
      </div>

    </div>

  </div><!-- /content -->
</div><!-- /main -->

<script>
function copyCode(btn) {
  const block = btn.closest('.code-block');
  const code = block.querySelector('pre, code').innerText;
  navigator.clipboard.writeText(code).then(() => {
    btn.textContent = 'copied!';
    btn.style.color = 'var(--accent2)';
    btn.style.borderColor = 'var(--accent2)';
    setTimeout(() => {
      btn.textContent = 'copy';
      btn.style.color = '';
      btn.style.borderColor = '';
    }, 1800);
  });
}

// Highlight active nav on scroll
const sections = document.querySelectorAll('[id]');
const navItems = document.querySelectorAll('.nav-item');

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      navItems.forEach(n => n.classList.remove('active'));
      const active = document.querySelector(`.nav-item[href="#${entry.target.id}"]`);
      if (active) active.classList.add('active');
    }
  });
}, { rootMargin: '-20% 0px -70% 0px' });

sections.forEach(s => observer.observe(s));
</script>
</body>
</html>
