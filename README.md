<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PolyBot — Copy Trading Intelligence</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #080c10;
    --surface: #0d1117;
    --card: #111820;
    --border: #1e2d3d;
    --accent: #00f5a0;
    --accent2: #00d4ff;
    --accent3: #ff6b35;
    --warn: #ffd23f;
    --danger: #ff4757;
    --text: #e8edf2;
    --muted: #5a7085;
    --green: #00f5a0;
    --red: #ff4757;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Grid noise background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,245,160,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,245,160,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  /* Glow orbs */
  body::after {
    content: '';
    position: fixed;
    top: -200px;
    right: -200px;
    width: 600px;
    height: 600px;
    background: radial-gradient(circle, rgba(0,245,160,0.06) 0%, transparent 70%);
    pointer-events: none;
    z-index: 0;
  }

  .app { position: relative; z-index: 1; }

  /* HEADER */
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 18px 32px;
    border-bottom: 1px solid var(--border);
    background: rgba(8,12,16,0.9);
    backdrop-filter: blur(20px);
    position: sticky;
    top: 0;
    z-index: 100;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .logo-icon {
    width: 36px;
    height: 36px;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
  }

  .logo-text {
    font-size: 20px;
    font-weight: 800;
    letter-spacing: -0.5px;
  }

  .logo-text span { color: var(--accent); }

  .status-bar {
    display: flex;
    align-items: center;
    gap: 24px;
    font-family: 'Space Mono', monospace;
    font-size: 12px;
  }

  .status-dot {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: var(--accent);
    animation: pulse 2s infinite;
  }

  .dot.red { background: var(--danger); animation: none; }
  .dot.yellow { background: var(--warn); }

  @keyframes pulse {
    0%, 100% { opacity: 1; box-shadow: 0 0 0 0 rgba(0,245,160,0.4); }
    50% { opacity: 0.8; box-shadow: 0 0 0 6px rgba(0,245,160,0); }
  }

  .header-controls {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .btn {
    padding: 8px 16px;
    border-radius: 6px;
    border: none;
    cursor: pointer;
    font-family: 'Syne', sans-serif;
    font-size: 13px;
    font-weight: 600;
    transition: all 0.2s;
  }

  .btn-outline {
    background: transparent;
    border: 1px solid var(--border);
    color: var(--muted);
  }

  .btn-outline:hover {
    border-color: var(--accent);
    color: var(--accent);
  }

  .btn-primary {
    background: var(--accent);
    color: #000;
  }

  .btn-primary:hover {
    background: #00e090;
    transform: translateY(-1px);
    box-shadow: 0 4px 20px rgba(0,245,160,0.3);
  }

  .btn-danger {
    background: rgba(255,71,87,0.15);
    border: 1px solid rgba(255,71,87,0.3);
    color: var(--danger);
  }

  .btn-danger:hover { background: rgba(255,71,87,0.25); }

  /* LAYOUT */
  .layout {
    display: grid;
    grid-template-columns: 280px 1fr 300px;
    gap: 0;
    min-height: calc(100vh - 65px);
  }

  /* SIDEBAR */
  .sidebar {
    border-right: 1px solid var(--border);
    padding: 24px 0;
    overflow-y: auto;
  }

  .sidebar-section {
    padding: 0 20px 24px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 24px;
  }

  .sidebar-label {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 12px;
  }

  /* Stats mini */
  .stat-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 0;
    border-bottom: 1px solid rgba(30,45,61,0.5);
  }

  .stat-row:last-child { border-bottom: none; }

  .stat-label { font-size: 13px; color: var(--muted); }

  .stat-val {
    font-family: 'Space Mono', monospace;
    font-size: 14px;
    font-weight: 700;
  }

  .stat-val.green { color: var(--green); }
  .stat-val.red { color: var(--red); }
  .stat-val.accent { color: var(--accent2); }

  /* Config inputs */
  .input-group { margin-bottom: 14px; }

  .input-label {
    font-size: 12px;
    color: var(--muted);
    margin-bottom: 6px;
    display: block;
  }

  .input-field {
    width: 100%;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 8px 12px;
    color: var(--text);
    font-family: 'Space Mono', monospace;
    font-size: 13px;
    outline: none;
    transition: border-color 0.2s;
  }

  .input-field:focus { border-color: var(--accent); }

  .toggle-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 0;
  }

  .toggle-label { font-size: 13px; }

  .toggle {
    width: 40px;
    height: 22px;
    background: var(--border);
    border-radius: 11px;
    cursor: pointer;
    position: relative;
    transition: background 0.3s;
  }

  .toggle.on { background: var(--accent); }

  .toggle::after {
    content: '';
    position: absolute;
    width: 16px;
    height: 16px;
    background: #fff;
    border-radius: 50%;
    top: 3px;
    left: 3px;
    transition: transform 0.3s;
  }

  .toggle.on::after { transform: translateX(18px); }

  /* MAIN AREA */
  .main {
    overflow-y: auto;
    padding: 24px;
  }

  /* Tabs */
  .tabs {
    display: flex;
    gap: 0;
    border-bottom: 1px solid var(--border);
    margin-bottom: 24px;
  }

  .tab {
    padding: 10px 20px;
    font-size: 13px;
    font-weight: 600;
    color: var(--muted);
    cursor: pointer;
    border-bottom: 2px solid transparent;
    transition: all 0.2s;
  }

  .tab.active {
    color: var(--accent);
    border-bottom-color: var(--accent);
  }

  .tab:hover:not(.active) { color: var(--text); }

  .tab-content { display: none; }
  .tab-content.active { display: block; }

  /* Traders Table */
  .table-wrapper {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 12px;
    overflow: hidden;
    margin-bottom: 24px;
  }

  .table-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 20px;
    border-bottom: 1px solid var(--border);
  }

  .table-title {
    font-size: 14px;
    font-weight: 700;
  }

  .table-actions { display: flex; gap: 8px; }

  table {
    width: 100%;
    border-collapse: collapse;
  }

  thead th {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 1px;
    text-transform: uppercase;
    padding: 10px 20px;
    text-align: left;
    background: rgba(0,0,0,0.2);
  }

  tbody tr {
    border-bottom: 1px solid rgba(30,45,61,0.5);
    transition: background 0.15s;
    cursor: pointer;
  }

  tbody tr:hover { background: rgba(0,245,160,0.03); }
  tbody tr:last-child { border-bottom: none; }

  td {
    padding: 14px 20px;
    font-size: 13px;
  }

  .trader-info {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .avatar {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
    font-weight: 700;
    flex-shrink: 0;
  }

  .trader-name { font-weight: 600; font-size: 14px; }
  .trader-addr {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
  }

  .badge {
    display: inline-flex;
    align-items: center;
    padding: 3px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 700;
    font-family: 'Space Mono', monospace;
  }

  .badge-green { background: rgba(0,245,160,0.15); color: var(--green); }
  .badge-red { background: rgba(255,71,87,0.15); color: var(--red); }
  .badge-blue { background: rgba(0,212,255,0.15); color: var(--accent2); }
  .badge-orange { background: rgba(255,107,53,0.15); color: var(--accent3); }

  .copy-btn {
    padding: 5px 12px;
    border-radius: 5px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    font-size: 12px;
    cursor: pointer;
    transition: all 0.2s;
    font-family: 'Syne', sans-serif;
    font-weight: 600;
  }

  .copy-btn:hover {
    border-color: var(--accent);
    color: var(--accent);
    background: rgba(0,245,160,0.05);
  }

  .copy-btn.copying {
    border-color: var(--accent);
    color: var(--accent);
    background: rgba(0,245,160,0.1);
  }

  /* Market feed */
  .feed-item {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 16px;
    margin-bottom: 12px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.2s, transform 0.2s;
  }

  .feed-item:hover {
    border-color: rgba(0,245,160,0.3);
    transform: translateX(4px);
  }

  .feed-item.new {
    animation: slideIn 0.4s ease;
    border-left: 3px solid var(--accent);
  }

  @keyframes slideIn {
    from { opacity: 0; transform: translateX(-10px); }
    to { opacity: 1; transform: translateX(0); }
  }

  .feed-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 10px;
  }

  .feed-market { font-size: 14px; font-weight: 600; line-height: 1.4; }
  .feed-time {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    white-space: nowrap;
    margin-left: 12px;
  }

  .feed-details {
    display: flex;
    align-items: center;
    gap: 12px;
    flex-wrap: wrap;
  }

  .prob-bar {
    flex: 1;
    min-width: 120px;
    height: 4px;
    background: var(--border);
    border-radius: 2px;
    overflow: hidden;
  }

  .prob-fill {
    height: 100%;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    border-radius: 2px;
    transition: width 1s ease;
  }

  /* Right panel */
  .right-panel {
    border-left: 1px solid var(--border);
    padding: 24px;
    overflow-y: auto;
  }

  .panel-title {
    font-size: 14px;
    font-weight: 700;
    margin-bottom: 20px;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 1px;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
  }

  /* Log */
  .log {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 12px;
    height: 280px;
    overflow-y: auto;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    line-height: 1.8;
    margin-bottom: 24px;
  }

  .log-entry { padding: 2px 0; }
  .log-entry .ts { color: var(--muted); }
  .log-entry .msg { margin-left: 8px; }
  .log-entry.info .msg { color: var(--accent2); }
  .log-entry.success .msg { color: var(--green); }
  .log-entry.warn .msg { color: var(--warn); }
  .log-entry.error .msg { color: var(--red); }
  .log-entry.trade .msg { color: var(--accent); }

  /* Portfolio */
  .portfolio-item {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 12px;
    margin-bottom: 8px;
  }

  .port-market {
    font-size: 12px;
    font-weight: 600;
    margin-bottom: 8px;
    line-height: 1.4;
  }

  .port-row {
    display: flex;
    justify-content: space-between;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    margin-bottom: 4px;
  }

  .port-pnl { font-weight: 700; }
  .port-pnl.pos { color: var(--green); }
  .port-pnl.neg { color: var(--red); }

  /* Scanner animation */
  .scanner {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 20px;
    margin-bottom: 20px;
    position: relative;
    overflow: hidden;
  }

  .scanner::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 2px;
    background: linear-gradient(90deg, transparent, var(--accent), transparent);
    animation: scan 3s linear infinite;
  }

  @keyframes scan {
    0% { left: -100%; }
    100% { left: 100%; }
  }

  .scanner-title {
    font-size: 12px;
    font-weight: 700;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 14px;
    font-family: 'Space Mono', monospace;
  }

  .scanner-stats {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;
  }

  .scan-stat {
    text-align: center;
  }

  .scan-val {
    font-family: 'Space Mono', monospace;
    font-size: 22px;
    font-weight: 700;
    color: var(--accent);
    display: block;
  }

  .scan-label {
    font-size: 11px;
    color: var(--muted);
    margin-top: 2px;
  }

  /* Opportunity cards */
  .opp-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 14px;
    margin-bottom: 10px;
    cursor: pointer;
    transition: all 0.2s;
  }

  .opp-card:hover {
    border-color: var(--accent);
    background: rgba(0,245,160,0.03);
  }

  .opp-title { font-size: 13px; font-weight: 600; margin-bottom: 8px; }

  .opp-metrics {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }

  .metric-chip {
    background: rgba(0,0,0,0.3);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 3px 7px;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
  }

  .metric-chip.highlight {
    border-color: rgba(0,245,160,0.3);
    color: var(--accent);
  }

  /* Chart bars */
  .mini-chart {
    display: flex;
    align-items: flex-end;
    gap: 3px;
    height: 40px;
    margin-top: 8px;
  }

  .bar {
    flex: 1;
    background: var(--border);
    border-radius: 2px 2px 0 0;
    transition: height 0.5s ease;
  }

  .bar.up { background: rgba(0,245,160,0.4); }
  .bar.down { background: rgba(255,71,87,0.4); }

  /* Scrollbar */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }
  ::-webkit-scrollbar-thumb:hover { background: var(--muted); }

  /* Notifications */
  .notif-container {
    position: fixed;
    top: 80px;
    right: 20px;
    z-index: 1000;
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .notif {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 12px 16px;
    font-size: 13px;
    display: flex;
    align-items: center;
    gap: 10px;
    min-width: 280px;
    animation: notifIn 0.3s ease;
    border-left: 3px solid var(--accent);
  }

  .notif.trade { border-left-color: var(--accent2); }
  .notif.warn { border-left-color: var(--warn); }
  .notif.err { border-left-color: var(--red); }

  @keyframes notifIn {
    from { opacity: 0; transform: translateX(20px); }
    to { opacity: 1; transform: translateX(0); }
  }

  .notif-icon { font-size: 16px; }

  /* Modal */
  .modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.8);
    z-index: 200;
    display: flex;
    align-items: center;
    justify-content: center;
    backdrop-filter: blur(4px);
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.3s;
  }

  .modal-overlay.open {
    opacity: 1;
    pointer-events: all;
  }

  .modal {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 28px;
    width: 480px;
    max-width: 90vw;
    transform: translateY(20px);
    transition: transform 0.3s;
  }

  .modal-overlay.open .modal { transform: translateY(0); }

  .modal-title {
    font-size: 18px;
    font-weight: 800;
    margin-bottom: 6px;
  }

  .modal-sub {
    font-size: 13px;
    color: var(--muted);
    margin-bottom: 24px;
  }

  .modal-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 16px;
  }

  .modal-actions {
    display: flex;
    gap: 10px;
    justify-content: flex-end;
    margin-top: 20px;
  }

  .risk-slider {
    width: 100%;
    accent-color: var(--accent);
  }

  .slider-labels {
    display: flex;
    justify-content: space-between;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    margin-top: 4px;
  }

  @media (max-width: 1100px) {
    .layout { grid-template-columns: 240px 1fr; }
    .right-panel { display: none; }
  }

  @media (max-width: 768px) {
    .layout { grid-template-columns: 1fr; }
    .sidebar { display: none; }
  }
</style>
</head>
<body>
<div class="app">

<!-- HEADER -->
<header>
  <div class="logo">
    <div class="logo-icon">⬡</div>
    <div class="logo-text">Poly<span>Bot</span></div>
  </div>

  <div class="status-bar">
    <div class="status-dot">
      <div class="dot" id="connDot"></div>
      <span id="connStatus">CONECTADO</span>
    </div>
    <span style="color:var(--muted)">|</span>
    <span>POLYGON MAINNET</span>
    <span style="color:var(--muted)">|</span>
    <span id="liveTime" style="color:var(--accent)">--:--:--</span>
  </div>

  <div class="header-controls">
    <button class="btn btn-outline" onclick="openModal()">⚙ Configurar</button>
    <button class="btn btn-danger" id="stopBtn" onclick="toggleBot()">■ Parar Bot</button>
    <button class="btn btn-primary" id="startBtn" onclick="toggleBot()" style="display:none">▶ Iniciar Bot</button>
  </div>
</header>

<!-- MAIN LAYOUT -->
<div class="layout">

  <!-- SIDEBAR -->
  <aside class="sidebar">

    <div class="sidebar-section">
      <div class="sidebar-label">Carteira</div>
      <div class="stat-row">
        <span class="stat-label">Saldo USDC</span>
        <span class="stat-val accent" id="balance">$2,847.50</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">Alocado</span>
        <span class="stat-val">$1,230.00</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">P&L Hoje</span>
        <span class="stat-val green" id="pnlToday">+$184.20</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">P&L Total</span>
        <span class="stat-val green">+$1,042.80</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">Win Rate</span>
        <span class="stat-val green">67.3%</span>
      </div>
    </div>

    <div class="sidebar-section">
      <div class="sidebar-label">Bot Status</div>
      <div class="stat-row">
        <span class="stat-label">Status</span>
        <span class="badge badge-green" id="botStatusBadge">RODANDO</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">Trades hoje</span>
        <span class="stat-val" id="tradesCount">23</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">Mercados</span>
        <span class="stat-val accent">847</span>
      </div>
      <div class="stat-row">
        <span class="stat-label">Traders copiados</span>
        <span class="stat-val" id="copiedCount">3</span>
      </div>
    </div>

    <div class="sidebar-section" style="border:none">
      <div class="sidebar-label">Configuração Rápida</div>
      <div class="input-group">
        <label class="input-label">Max por trade (USDC)</label>
        <input class="input-field" type="number" value="50" id="maxTrade">
      </div>
      <div class="input-group">
        <label class="input-label">Max posições abertas</label>
        <input class="input-field" type="number" value="10" id="maxPos">
      </div>
      <div class="input-group">
        <label class="input-label">Stop loss (%)</label>
        <input class="input-field" type="number" value="30" id="stopLoss">
      </div>
      <div class="toggle-row">
        <span class="toggle-label">Copy automático</span>
        <div class="toggle on" id="autoCopy" onclick="this.classList.toggle('on')"></div>
      </div>
      <div class="toggle-row">
        <span class="toggle-label">Filtrar <$100 apostas</span>
        <div class="toggle on" id="filterSmall" onclick="this.classList.toggle('on')"></div>
      </div>
      <div class="toggle-row">
        <span class="toggle-label">Alertas sonoros</span>
        <div class="toggle" id="soundAlerts" onclick="this.classList.toggle('on')"></div>
      </div>
    </div>

  </aside>

  <!-- MAIN CONTENT -->
  <main class="main">

    <!-- Scanner summary -->
    <div class="scanner">
      <div class="scanner-title">Scanner em Tempo Real</div>
      <div class="scanner-stats">
        <div class="scan-stat">
          <span class="scan-val" id="scanMarkets">847</span>
          <div class="scan-label">Mercados Ativos</div>
        </div>
        <div class="scan-stat">
          <span class="scan-val" id="scanOpps" style="color:var(--warn)">12</span>
          <div class="scan-label">Oportunidades</div>
        </div>
        <div class="scan-stat">
          <span class="scan-val" id="scanTraders" style="color:var(--accent2)">156</span>
          <div class="scan-label">Traders Rastreados</div>
        </div>
        <div class="scan-stat">
          <span class="scan-val" id="scanVolume" style="color:var(--accent3)">$2.1M</span>
          <div class="scan-label">Volume 24h</div>
        </div>
      </div>
    </div>

    <!-- Tabs -->
    <div class="tabs">
      <div class="tab active" onclick="switchTab(this,'traders')">👥 Top Traders</div>
      <div class="tab" onclick="switchTab(this,'feed')">⚡ Feed ao Vivo</div>
      <div class="tab" onclick="switchTab(this,'opps')">🎯 Oportunidades</div>
      <div class="tab" onclick="switchTab(this,'history')">📋 Histórico</div>
    </div>

    <!-- TAB: Traders -->
    <div class="tab-content active" id="tab-traders">
      <div class="table-wrapper">
        <div class="table-header">
          <div class="table-title">Melhores Traders para Copiar</div>
          <div class="table-actions">
            <button class="btn btn-outline" onclick="refreshTraders()">↻ Atualizar</button>
          </div>
        </div>
        <table>
          <thead>
            <tr>
              <th>#</th>
              <th>Trader</th>
              <th>ROI 30d</th>
              <th>Win Rate</th>
              <th>Volume</th>
              <th>Trades</th>
              <th>Risco</th>
              <th>Ação</th>
            </tr>
          </thead>
          <tbody id="tradersTable"></tbody>
        </table>
      </div>
    </div>

    <!-- TAB: Feed -->
    <div class="tab-content" id="tab-feed">
      <div id="feedContainer"></div>
    </div>

    <!-- TAB: Oportunidades -->
    <div class="tab-content" id="tab-opps">
      <div id="oppsContainer"></div>
    </div>

    <!-- TAB: Histórico -->
    <div class="tab-content" id="tab-history">
      <div class="table-wrapper">
        <div class="table-header">
          <div class="table-title">Histórico de Trades Copiados</div>
        </div>
        <table>
          <thead>
            <tr>
              <th>Horário</th>
              <th>Mercado</th>
              <th>Copiado de</th>
              <th>Lado</th>
              <th>Entrada</th>
              <th>Valor</th>
              <th>P&L</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody id="historyTable"></tbody>
        </table>
      </div>
    </div>

  </main>

  <!-- RIGHT PANEL -->
  <aside class="right-panel">
    <div class="panel-title">Log do Bot</div>
    <div class="log" id="botLog"></div>

    <div class="panel-title">Posições Abertas</div>
    <div id="openPositions"></div>
  </aside>

</div>

<!-- NOTIFICATIONS -->
<div class="notif-container" id="notifContainer"></div>

<!-- MODAL CONFIG -->
<div class="modal-overlay" id="configModal">
  <div class="modal">
    <div class="modal-title">⚙ Configuração Avançada</div>
    <div class="modal-sub">Configure a estratégia de copy trading</div>

    <div class="modal-row">
      <div class="input-group" style="margin:0">
        <label class="input-label">Endereço da Carteira</label>
        <input class="input-field" placeholder="0x..." id="walletAddr">
      </div>
      <div class="input-group" style="margin:0">
        <label class="input-label">Private Key (encriptada)</label>
        <input class="input-field" type="password" placeholder="••••••••" id="privKey">
      </div>
    </div>

    <div class="modal-row">
      <div class="input-group" style="margin:0">
        <label class="input-label">Delay de cópia (seg)</label>
        <input class="input-field" type="number" value="5" id="copyDelay">
      </div>
      <div class="input-group" style="margin:0">
        <label class="input-label">Slippage máx (%)</label>
        <input class="input-field" type="number" value="2" id="slippage">
      </div>
    </div>

    <div class="input-group">
      <label class="input-label">Nível de Risco: <span id="riskVal">Médio (50%)</span></label>
      <input class="risk-slider" type="range" min="10" max="90" value="50"
        oninput="document.getElementById('riskVal').textContent = this.value<=30?'Baixo ('+this.value+'%)':this.value<=60?'Médio ('+this.value+'%)':'Alto ('+this.value+'%)'">
      <div class="slider-labels"><span>Conservador</span><span>Agressivo</span></div>
    </div>

    <div class="toggle-row">
      <span class="toggle-label">Copiar apenas mercados com >$10k liquidez</span>
      <div class="toggle on" onclick="this.classList.toggle('on')"></div>
    </div>
    <div class="toggle-row">
      <span class="toggle-label">Ignorar mercados resolvendo em <24h</span>
      <div class="toggle" onclick="this.classList.toggle('on')"></div>
    </div>
    <div class="toggle-row">
      <span class="toggle-label">Proporcional ao trade original</span>
      <div class="toggle on" onclick="this.classList.toggle('on')"></div>
    </div>

    <div class="modal-actions">
      <button class="btn btn-outline" onclick="closeModal()">Cancelar</button>
      <button class="btn btn-primary" onclick="saveConfig()">Salvar Configuração</button>
    </div>
  </div>
</div>

<script>
// =================== DATA ===================
const TRADERS = [
  { rank: 1, name: "CryptoSage", addr: "0x4a9f...3b2e", roi: 312, wr: 78, vol: 142000, trades: 234, risk: "Baixo", emoji: "🎯", color: "#00f5a0", copying: true },
  { rank: 2, name: "PredictorX", addr: "0x7c1d...8f4a", roi: 247, wr: 71, vol: 89000, trades: 189, risk: "Médio", emoji: "🔮", color: "#00d4ff", copying: true },
  { rank: 3, name: "OracleWhale", addr: "0x2e8b...5c7f", roi: 198, wr: 69, vol: 210000, trades: 312, risk: "Médio", emoji: "🐋", color: "#ffd23f", copying: false },
  { rank: 4, name: "DataDriven", addr: "0x9a3c...1d6e", roi: 176, wr: 65, vol: 67000, trades: 145, risk: "Baixo", emoji: "📊", color: "#ff6b35", copying: true },
  { rank: 5, name: "AlphaSeeker", addr: "0x5f7a...4b9c", roi: 154, wr: 62, vol: 34000, trades: 98, risk: "Alto", emoji: "⚡", color: "#ff4757", copying: false },
  { rank: 6, name: "MarketWizard", addr: "0x1b4e...7a3d", roi: 131, wr: 60, vol: 55000, trades: 167, risk: "Baixo", emoji: "🧙", color: "#a29bfe", copying: false },
  { rank: 7, name: "NewsBot9k", addr: "0x8d2f...6c1a", roi: 118, wr: 58, vol: 28000, trades: 203, risk: "Médio", emoji: "📰", color: "#fd79a8", copying: false },
];

const MARKETS = [
  "Will Trump sign executive order on crypto before April?",
  "Will BTC exceed $100k by end of Q2 2025?",
  "Will the Fed cut rates in March 2025?",
  "Will Elon Musk leave DOGE before June?",
  "Will ChatGPT-5 launch before GPT-6?",
  "Will Argentina win Copa America 2025?",
  "Will US inflation drop below 2% in 2025?",
  "Will Apple release AR glasses in 2025?",
  "Will Solana flip Ethereum in market cap by 2026?",
  "Will there be a government shutdown before May 2025?",
];

let botRunning = true;
let tradeCount = 23;
let feedItems = [];
let openPos = [];

// =================== TIME ===================
function updateTime() {
  const now = new Date();
  document.getElementById('liveTime').textContent =
    now.toLocaleTimeString('pt-BR');
}
setInterval(updateTime, 1000);
updateTime();

// =================== TRADERS TABLE ===================
function renderTraders() {
  const tbody = document.getElementById('tradersTable');
  tbody.innerHTML = '';
  TRADERS.forEach(t => {
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td style="color:var(--muted);font-family:'Space Mono',monospace;font-size:12px">#${t.rank}</td>
      <td>
        <div class="trader-info">
          <div class="avatar" style="background:${t.color}22;color:${t.color}">${t.emoji}</div>
          <div>
            <div class="trader-name">${t.name}</div>
            <div class="trader-addr">${t.addr}</div>
          </div>
        </div>
      </td>
      <td><span class="badge badge-green">+${t.roi}%</span></td>
      <td style="font-family:'Space Mono',monospace;font-size:13px">${t.wr}%</td>
      <td style="font-family:'Space Mono',monospace;font-size:13px">$${(t.vol/1000).toFixed(0)}k</td>
      <td style="font-family:'Space Mono',monospace;font-size:13px">${t.trades}</td>
      <td><span class="badge ${t.risk==='Baixo'?'badge-green':t.risk==='Médio'?'badge-blue':'badge-red'}">${t.risk}</span></td>
      <td>
        <button class="copy-btn ${t.copying?'copying':''}" onclick="toggleCopy(${t.rank-1},this)">
          ${t.copying?'✓ Copiando':'+ Copiar'}
        </button>
      </td>
    `;
    tbody.appendChild(tr);
  });
}

function toggleCopy(idx, btn) {
  TRADERS[idx].copying = !TRADERS[idx].copying;
  btn.textContent = TRADERS[idx].copying ? '✓ Copiando' : '+ Copiar';
  btn.className = 'copy-btn ' + (TRADERS[idx].copying ? 'copying' : '');
  const count = TRADERS.filter(t => t.copying).length;
  document.getElementById('copiedCount').textContent = count;
  if (TRADERS[idx].copying) {
    addLog(`Iniciando cópia de ${TRADERS[idx].name}`, 'info');
    showNotif(`📋 Copiando ${TRADERS[idx].name}`, '');
  } else {
    addLog(`Parando cópia de ${TRADERS[idx].name}`, 'warn');
  }
}

function refreshTraders() {
  TRADERS.forEach(t => {
    t.roi = Math.max(50, t.roi + Math.round((Math.random()-0.4)*20));
    t.wr = Math.min(85, Math.max(50, t.wr + Math.round((Math.random()-0.5)*3)));
  });
  TRADERS.sort((a,b) => b.roi - a.roi);
  TRADERS.forEach((t,i) => t.rank = i+1);
  renderTraders();
  addLog('Ranking de traders atualizado', 'info');
}

// =================== FEED ===================
function renderFeed() {
  const c = document.getElementById('feedContainer');
  c.innerHTML = feedItems.slice(0, 20).map((item,i) => `
    <div class="feed-item ${i===0?'new':''}">
      <div class="feed-header">
        <div>
          <div class="feed-market">${item.market}</div>
          <div style="display:flex;gap:8px;margin-top:6px;flex-wrap:wrap">
            <span class="badge ${item.side==='YES'?'badge-green':'badge-red'}">${item.side}</span>
            <span class="badge badge-blue">${item.trader}</span>
            <span style="font-family:'Space Mono',monospace;font-size:11px;color:var(--muted)">$${item.amount}</span>
          </div>
        </div>
        <div style="text-align:right">
          <div class="feed-time">${item.time}</div>
          <div style="font-family:'Space Mono',monospace;font-size:13px;margin-top:4px;color:${item.side==='YES'?'var(--green)':'var(--red)'}">${item.prob}%</div>
        </div>
      </div>
      <div class="prob-bar">
        <div class="prob-fill" style="width:${item.prob}%"></div>
      </div>
      ${item.copied ? `<div style="margin-top:8px;font-size:11px;color:var(--accent)">✓ Trade copiado automaticamente</div>` : ''}
    </div>
  `).join('');
}

// =================== OPPORTUNITIES ===================
const OPPS = [
  { title: "Will BTC exceed $100k by Q2?", edge: "+8.2%", conf: "Alto", prob: 63, vol: "$45k", traders: 3 },
  { title: "Fed rate cut in March 2025?", edge: "+5.7%", conf: "Médio", prob: 38, vol: "$120k", traders: 5 },
  { title: "Argentina win Copa America?", edge: "+11.3%", conf: "Alto", prob: 71, vol: "$28k", traders: 2 },
  { title: "ChatGPT-5 before GPT-6?", edge: "+4.1%", conf: "Baixo", prob: 55, vol: "$15k", traders: 4 },
];

function renderOpps() {
  const c = document.getElementById('oppsContainer');
  c.innerHTML = OPPS.map(o => `
    <div class="opp-card" onclick="betOnOpp(this,'${o.title}')">
      <div style="display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:8px">
        <div class="opp-title">${o.title}</div>
        <span class="badge badge-green" style="font-size:13px;padding:5px 10px">${o.edge}</span>
      </div>
      <div class="opp-metrics">
        <div class="metric-chip highlight">Prob: ${o.prob}%</div>
        <div class="metric-chip">Vol: ${o.vol}</div>
        <div class="metric-chip">${o.traders} traders</div>
        <div class="metric-chip ${o.conf==='Alto'?'highlight':''}">Conf: ${o.conf}</div>
      </div>
      <div class="mini-chart">
        ${Array.from({length:16}, (_,i) => {
          const h = 20 + Math.random() * 80;
          const up = Math.random() > 0.4;
          return `<div class="bar ${up?'up':'down'}" style="height:${h}%"></div>`;
        }).join('')}
      </div>
    </div>
  `).join('');
}

function betOnOpp(el, title) {
  el.style.borderColor = 'var(--accent)';
  setTimeout(() => el.style.borderColor = '', 1000);
  addLog(`Analisando oportunidade: ${title.substring(0,40)}...`, 'info');
  showNotif(`🎯 Executando bet: ${title.substring(0,35)}...`, 'trade');
  addTrade(title);
}

// =================== HISTORY ===================
let historyData = [];

function addTrade(market, fromFeed) {
  const traders = TRADERS.filter(t => t.copying);
  const trader = traders[Math.floor(Math.random()*traders.length)] || TRADERS[0];
  const side = Math.random() > 0.45 ? 'YES' : 'NO';
  const entry = (30 + Math.random()*50).toFixed(0);
  const amount = (20 + Math.random()*80).toFixed(2);
  const pnl = ((Math.random()-0.4)*30).toFixed(2);
  const closed = Math.random() > 0.5;

  const now = new Date();
  const time = now.toLocaleTimeString('pt-BR');

  historyData.unshift({ time, market: market||MARKETS[Math.floor(Math.random()*MARKETS.length)], trader: trader.name, side, entry, amount, pnl, closed });
  tradeCount++;
  document.getElementById('tradesCount').textContent = tradeCount;
  renderHistory();

  if (closed && !fromFeed) {
    const pos = parseFloat(pnl);
    const cur = parseFloat(document.getElementById('pnlToday').textContent.replace(/[+$]/g,''));
    const newPnl = cur + pos;
    document.getElementById('pnlToday').textContent = (newPnl>=0?'+':'') + '$' + Math.abs(newPnl).toFixed(2);
    document.getElementById('pnlToday').className = 'stat-val ' + (newPnl >= 0 ? 'green' : 'red');
  }
}

function renderHistory() {
  const tbody = document.getElementById('historyTable');
  tbody.innerHTML = historyData.slice(0,25).map(h => `
    <tr>
      <td style="font-family:'Space Mono',monospace;font-size:11px;color:var(--muted)">${h.time}</td>
      <td style="font-size:12px;max-width:200px">${h.market.substring(0,45)}${h.market.length>45?'...':''}</td>
      <td><span class="badge badge-blue" style="font-size:11px">${h.trader}</span></td>
      <td><span class="badge ${h.side==='YES'?'badge-green':'badge-red'}">${h.side}</span></td>
      <td style="font-family:'Space Mono',monospace;font-size:12px">${h.entry}%</td>
      <td style="font-family:'Space Mono',monospace;font-size:12px">$${h.amount}</td>
      <td style="font-family:'Space Mono',monospace;font-size:12px;color:${parseFloat(h.pnl)>=0?'var(--green)':'var(--red)'}">${parseFloat(h.pnl)>=0?'+':''}$${Math.abs(h.pnl)}</td>
      <td><span class="badge ${h.closed?parseFloat(h.pnl)>=0?'badge-green':'badge-red':'badge-orange'}">${h.closed?'Fechado':'Aberto'}</span></td>
    </tr>
  `).join('');
}

// =================== OPEN POSITIONS ===================
function renderPositions() {
  const c = document.getElementById('openPositions');
  const open = historyData.filter(h => !h.closed).slice(0, 6);
  if (open.length === 0) {
    c.innerHTML = '<div style="color:var(--muted);font-size:12px;text-align:center;padding:20px">Nenhuma posição aberta</div>';
    return;
  }
  c.innerHTML = open.map(p => {
    const pnl = parseFloat(p.pnl);
    return `
    <div class="portfolio-item">
      <div class="port-market">${p.market.substring(0,50)}${p.market.length>50?'...':''}</div>
      <div class="port-row">
        <span>Entrada</span>
        <span>${p.entry}%</span>
      </div>
      <div class="port-row">
        <span>Valor</span>
        <span>$${p.amount}</span>
      </div>
      <div class="port-row">
        <span>P&L</span>
        <span class="port-pnl ${pnl>=0?'pos':'neg'}">${pnl>=0?'+':''}$${Math.abs(pnl)}</span>
      </div>
    </div>
  `}).join('');
}

// =================== LOG ===================
function addLog(msg, type='info') {
  const log = document.getElementById('botLog');
  const now = new Date().toLocaleTimeString('pt-BR');
  const div = document.createElement('div');
  div.className = `log-entry ${type}`;
  div.innerHTML = `<span class="ts">[${now}]</span><span class="msg">${msg}</span>`;
  log.insertBefore(div, log.firstChild);
  if (log.children.length > 80) log.removeChild(log.lastChild);
}

// =================== NOTIFICATIONS ===================
function showNotif(msg, type='') {
  const c = document.getElementById('notifContainer');
  const icons = { '': '✅', 'trade': '💰', 'warn': '⚠️', 'err': '❌' };
  const div = document.createElement('div');
  div.className = `notif ${type}`;
  div.innerHTML = `<span class="notif-icon">${icons[type]||'ℹ️'}</span><span>${msg}</span>`;
  c.appendChild(div);
  setTimeout(() => { div.style.opacity = '0'; div.style.transform = 'translateX(20px)'; div.style.transition = 'all 0.3s'; setTimeout(() => div.remove(), 300); }, 4000);
}

// =================== BOT SIMULATION ===================
let botInterval;

function simulateBot() {
  if (!botRunning) return;

  const rand = Math.random();

  // Generate feed item
  const copying = TRADERS.filter(t => t.copying);
  if (copying.length === 0) return;
  const trader = copying[Math.floor(Math.random()*copying.length)];
  const market = MARKETS[Math.floor(Math.random()*MARKETS.length)];
  const side = Math.random() > 0.45 ? 'YES' : 'NO';
  const prob = Math.round(30 + Math.random() * 55);
  const amount = Math.round(50 + Math.random() * 500);
  const now = new Date();
  const shouldCopy = rand < 0.7;

  const feedItem = {
    market, side, prob, amount,
    trader: trader.name,
    time: now.toLocaleTimeString('pt-BR'),
    copied: shouldCopy
  };

  feedItems.unshift(feedItem);
  if (feedItems.length > 30) feedItems.pop();
  renderFeed();

  if (shouldCopy) {
    addLog(`✦ Copiando ${trader.name}: ${side} "${market.substring(0,40)}" @ ${prob}%`, 'trade');
    addTrade(market, true);
    showNotif(`💰 Trade copiado de ${trader.name} — ${side} $${amount}`, 'trade');
  } else {
    addLog(`Monitorando: ${trader.name} apostou ${side} em "${market.substring(0,35)}..."`, 'info');
  }

  renderPositions();

  // Occasional scanner updates
  if (Math.random() > 0.7) {
    document.getElementById('scanOpps').textContent = Math.round(8 + Math.random() * 15);
    document.getElementById('scanMarkets').textContent = Math.round(820 + Math.random() * 60);
  }
}

function startBotLoop() {
  clearInterval(botInterval);
  botInterval = setInterval(simulateBot, 3500 + Math.random() * 2000);
}

// =================== BOT TOGGLE ===================
function toggleBot() {
  botRunning = !botRunning;
  const stopBtn = document.getElementById('stopBtn');
  const startBtn = document.getElementById('startBtn');
  const badge = document.getElementById('botStatusBadge');
  const dot = document.getElementById('connDot');

  if (botRunning) {
    stopBtn.style.display = '';
    startBtn.style.display = 'none';
    badge.className = 'badge badge-green';
    badge.textContent = 'RODANDO';
    dot.className = 'dot';
    addLog('Bot iniciado', 'success');
    showNotif('▶ Bot iniciado com sucesso', '');
    startBotLoop();
  } else {
    stopBtn.style.display = 'none';
    startBtn.style.display = '';
    badge.className = 'badge badge-red';
    badge.textContent = 'PARADO';
    dot.className = 'dot red';
    addLog('Bot pausado pelo usuário', 'warn');
    showNotif('■ Bot pausado', 'warn');
    clearInterval(botInterval);
  }
}

// =================== TABS ===================
function switchTab(el, tab) {
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
  el.classList.add('active');
  document.getElementById('tab-'+tab).classList.add('active');
}

// =================== MODAL ===================
function openModal() {
  document.getElementById('configModal').classList.add('open');
}
function closeModal() {
  document.getElementById('configModal').classList.remove('open');
}
function saveConfig() {
  closeModal();
  addLog('Configuração salva', 'success');
  showNotif('✓ Configurações atualizadas', '');
}
document.getElementById('configModal').addEventListener('click', function(e) {
  if (e.target === this) closeModal();
});

// =================== INIT ===================
renderTraders();
renderOpps();
renderHistory();
renderPositions();

// Initial logs
addLog('Sistema inicializado', 'success');
addLog('Conectado à Polymarket API v2', 'info');
addLog('WebSocket ativo — recebendo eventos', 'info');
addLog('Carregados 847 mercados ativos', 'info');
addLog('Iniciando monitoramento de traders...', 'info');

// Initial feed
for (let i = 0; i < 6; i++) {
  const trader = TRADERS[Math.floor(Math.random()*3)];
  feedItems.push({
    market: MARKETS[Math.floor(Math.random()*MARKETS.length)],
    side: Math.random()>0.45?'YES':'NO',
    prob: Math.round(35+Math.random()*50),
    amount: Math.round(50+Math.random()*400),
    trader: trader.name,
    time: new Date(Date.now()-i*180000).toLocaleTimeString('pt-BR'),
    copied: Math.random()>0.3
  });
}
renderFeed();

// Initial history
for (let i = 0; i < 8; i++) addTrade(null, true);

// Start simulation
startBotLoop();
</script>
</body>
</html>
