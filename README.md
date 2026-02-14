# mine-xmr
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>REAL Mining Dashboard - Live Status</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    
    :root {
      --primary: #e67e22;
      --success: #2ecc71;
      --danger: #e74c3c;
      --warning: #f1c40f;
      --dark: #0f1419;
    }

    body {
      font-family: 'Segoe UI', sans-serif;
      background: var(--dark);
      color: white;
      min-height: 100vh;
    }

    .bg {
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      background: 
        radial-gradient(ellipse at 20% 80%, rgba(230, 126, 34, 0.15) 0%, transparent 50%),
        radial-gradient(ellipse at 80% 20%, rgba(52, 152, 219, 0.15) 0%, transparent 50%),
        linear-gradient(135deg, #0a0e12 0%, #1a1f26 100%);
      z-index: -1;
    }

    header {
      background: rgba(15, 20, 25, 0.95);
      padding: 20px;
      border-bottom: 1px solid rgba(255,255,255,0.1);
      position: sticky;
      top: 0;
      z-index: 100;
    }

    .header-content {
      max-width: 1400px;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .status-indicator {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 8px 16px;
      border-radius: 20px;
      font-weight: 600;
    }

    .status-indicator.active {
      background: rgba(46, 204, 113, 0.2);
      color: var(--success);
      border: 1px solid rgba(46, 204, 113, 0.3);
    }

    .status-indicator.inactive {
      background: rgba(231, 76, 60, 0.2);
      color: var(--danger);
      border: 1px solid rgba(231, 76, 60, 0.3);
    }

    .pulse {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      background: currentColor;
      animation: pulse 2s infinite;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.3; }
    }

    .container {
      max-width: 1400px;
      margin: 0 auto;
      padding: 30px 20px;
    }

    .control-panel {
      background: rgba(26, 37, 47, 0.9);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 20px;
      padding: 30px;
      margin-bottom: 30px;
      backdrop-filter: blur(10px);
    }

    .form-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      margin-bottom: 20px;
    }

    .form-group {
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .form-group label {
      font-size: 0.85em;
      color: #94a3b8;
      text-transform: uppercase;
      letter-spacing: 1px;
      font-weight: 600;
    }

    .form-group input, .form-group select {
      padding: 12px;
      background: rgba(0,0,0,0.3);
      border: 1px solid rgba(255,255,255,0.2);
      border-radius: 10px;
      color: white;
      font-family: 'Courier New', monospace;
    }

    .btn {
      padding: 15px 40px;
      border: none;
      border-radius: 10px;
      font-weight: 700;
      cursor: pointer;
      transition: all 0.3s;
      text-transform: uppercase;
      letter-spacing: 1px;
    }

    .btn-primary {
      background: linear-gradient(135deg, var(--primary), #d35400);
      color: white;
    }

    .btn-danger {
      background: linear-gradient(135deg, var(--danger), #c0392b);
      color: white;
    }

    .btn:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }

    .stats-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      margin-bottom: 30px;
    }

    .stat-card {
      background: rgba(26, 37, 47, 0.9);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 15px;
      padding: 25px;
      position: relative;
      overflow: hidden;
    }

    .stat-card::before {
      content: '';
      position: absolute;
      top: 0; left: 0; width: 100%; height: 3px;
      background: linear-gradient(90deg, var(--primary), transparent);
    }

    .stat-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 15px;
    }

    .stat-title {
      font-size: 0.85em;
      color: #94a3b8;
      text-transform: uppercase;
      letter-spacing: 1px;
    }

    .stat-value {
      font-family: 'Courier New', monospace;
      font-size: 2.5em;
      font-weight: 700;
      color: var(--primary);
      margin: 10px 0;
    }

    .live-indicator {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      font-size: 0.75em;
      color: var(--success);
      margin-top: 10px;
    }

    .console {
      background: rgba(0, 0, 0, 0.7);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 10px;
      padding: 20px;
      font-family: 'Courier New', monospace;
      font-size: 0.9em;
      height: 300px;
      overflow-y: auto;
    }

    .console-line {
      margin-bottom: 5px;
      padding: 2px 0;
      border-left: 3px solid transparent;
      padding-left: 10px;
    }

    .console-line.hashrate { border-left-color: var(--primary); color: #f39c12; }
    .console-line.share { border-left-color: var(--success); color: #2ecc71; }
    .console-line.error { border-left-color: var(--danger); color: #e74c3c; }
    .console-line.info { border-left-color: #3498db; color: #5dade2; }

    .thread-visualizer {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
      gap: 10px;
      margin-top: 20px;
    }

    .thread-box {
      background: rgba(0,0,0,0.3);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 8px;
      padding: 10px;
      text-align: center;
      transition: all 0.3s;
    }

    .thread-box.active {
      background: rgba(46, 204, 113, 0.1);
      border-color: rgba(46, 204, 113, 0.5);
    }

    .thread-hashrate {
      font-family: monospace;
      font-size: 0.9em;
      color: var(--primary);
      margin-top: 5px;
    }

    .warning-box {
      background: rgba(241, 196, 15, 0.1);
      border: 1px solid rgba(241, 196, 15, 0.3);
      color: var(--warning);
      padding: 15px;
      border-radius: 10px;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>
  <div class="bg"></div>
  
  <header>
    <div class="header-content">
      <div>
        <h1>🔴 LIVE Mining Dashboard</h1>
        <p style="color: #94a3b8; font-size: 0.9em;">Real-time XMRig Control & Monitoring</p>
      </div>
      <div class="status-indicator inactive" id="connection-status">
        <span class="pulse"></span>
        <span id="status-text">DISCONNECTED</span>
      </div>
    </div>
  </header>

  <div class="container">
    <div class="warning-box">
      <strong>⚠️ REAL MINING MODE:</strong> This dashboard controls actual XMRig software on your computer. 
      It will use your CPU/GPU resources and generate real cryptocurrency (Monero, not Litecoin).
    </div>

    <!-- Configuration Panel -->
    <div class="control-panel" id="config-panel">
      <h3 style="margin-bottom: 20px; color: var(--primary);">Mining Configuration</h3>
      
      <div class="form-grid">
        <div class="form-group">
          <label>Algorithm</label>
          <select id="algo">
            <option value="rx/0">RandomX (Monero) - CPU</option>
            <option value="kawpow">KawPow (Ravencoin) - GPU</option>
            <option value="ghostrider">GhostRider (RTM) - CPU</option>
          </select>
        </div>
        
        <div class="form-group">
          <label>Mining Pool</label>
          <select id="pool">
            <option value="pool.supportxmr.com:3333">SupportXMR (Global)</option>
            <option value="xmrpool.eu:3333">XMRPool EU</option>
            <option value="us-west.minexmr.com:4444">MineXMR US-West</option>
          </select>
        </div>
        
        <div class="form-group">
          <label>Your XMR Wallet Address</label>
          <input type="text" id="wallet" placeholder="4A... or 8A..." value="">
        </div>
        
        <div class="form-group">
          <label>Worker Name</label>
          <input type="text" id="worker" value="dashboard-miner">
        </div>
        
        <div class="form-group">
          <label>CPU Threads (0 = auto)</label>
          <input type="number" id="threads" value="0" min="0" max="64">
        </div>
      </div>

      <div style="display: flex; gap: 15px;">
        <button class="btn btn-primary" id="start-btn" onclick="startMining()">
          ▶ START REAL MINING
        </button>
        <button class="btn btn-danger" id="stop-btn" onclick="stopMining()" disabled>
          ⏹ STOP MINING
        </button>
      </div>
    </div>

    <!-- Live Stats (Hidden until mining starts) -->
    <div id="mining-dashboard" style="display: none;">
      <div class="stats-grid">
        <div class="stat-card">
          <div class="stat-header">
            <span class="stat-title">Current Hashrate</span>
            <span style="font-size: 1.5em;">⚡</span>
          </div>
          <div class="stat-value" id="live-hashrate">0 H/s</div>
          <div class="live-indicator">
            <span class="pulse"></span>
            LIVE DATA FROM XMRIG
          </div>
        </div>

        <div class="stat-card">
          <div class="stat-header">
            <span class="stat-title">Shares Accepted</span>
            <span style="font-size: 1.5em;">✓</span>
          </div>
          <div class="stat-value" id="live-shares">0</div>
          <div style="color: #64748b; margin-top: 10px;">
            Rejected: <span id="live-rejected" style="color: var(--danger);">0</span>
          </div>
        </div>

        <div class="stat-card">
          <div class="stat-header">
            <span class="stat-title">Uptime</span>
            <span style="font-size: 1.5em;">⏱️</span>
          </div>
          <div class="stat-value" id="live-uptime">00:00:00</div>
          <div style="color: #64748b; margin-top: 10px;">
            Started: <span id="start-time">--:--:--</span>
          </div>
        </div>

        <div class="stat-card">
          <div class="stat-header">
            <span class="stat-title">Algorithm</span>
            <span style="font-size: 1.5em;">🔧</span>
          </div>
          <div class="stat-value" id="live-algo" style="font-size: 1.5em;">--</div>
          <div style="color: #64748b; margin-top: 10px;">
            Pool: <span id="live-pool">--</span>
          </div>
        </div>
      </div>

      <div class="control-panel">
        <h3 style="margin-bottom: 20px; color: var(--primary);">CPU Thread Activity (Real-time)</h3>
        <div class="thread-visualizer" id="thread-container">
          <!-- Threads populated dynamically -->
        </div>
      </div>

      <div class="control-panel">
        <h3 style="margin-bottom: 20px; color: var(--primary);">XMRig Console Output (Live)</h3>
        <div class="console" id="console"></div>
      </div>
    </div>
  </div>

  <script>
    let ws = null;
    let reconnectInterval = null;
    let uptimeInterval = null;

    // Connect to WebSocket for real-time updates
    function connectWebSocket() {
      const protocol = window.location.protocol === 'https:' ? 'wss:' : 'ws:';
      ws = new WebSocket(`${protocol}//${window.location.host}`);

      ws.onopen = () => {
        console.log('Connected to mining backend');
        updateConnectionStatus(true);
        clearInterval(reconnectInterval);
      };

      ws.onmessage = (event) => {
        const message = JSON.parse(event.data);
        handleWebSocketMessage(message);
      };

      ws.onclose = () => {
        console.log('Disconnected from backend');
        updateConnectionStatus(false);
        // Attempt reconnect
        reconnectInterval = setInterval(connectWebSocket, 3000);
      };

      ws.onerror = (error) => {
        console.error('WebSocket error:', error);
      };
    }

    function updateConnectionStatus(connected) {
      const indicator = document.getElementById('connection-status');
      const text = document.getElementById('status-text');
      
      if (connected) {
        indicator.className = 'status-indicator active';
        text.textContent = 'CONNECTED TO BACKEND';
      } else {
        indicator.className = 'status-indicator inactive';
        text.textContent = 'BACKEND OFFLINE';
      }
    }

    function handleWebSocketMessage(message) {
      if (message.type === 'status' || message.type === 'update') {
        const data = message.data;
        
        // Update UI with real data
        if (data.isRunning) {
          document.getElementById('live-hashrate').textContent = formatHashrate(data.hashrate);
          document.getElementById('live-shares').textContent = data.shares.accepted;
          document.getElementById('live-rejected').textContent = data.shares.rejected;
          document.getElementById('live-algo').textContent = data.algorithm.toUpperCase();
          document.getElementById('live-pool').textContent = data.pool;
          
          // Update thread visualization
          updateThreads(data.threads);
          
          // Show dashboard if hidden
          if (document.getElementById('mining-dashboard').style.display === 'none') {
            showMiningDashboard();
          }
        }
      }
    }

    function formatHashrate(h) {
      if (!h) return '0 H/s';
      if (h >= 1000000) return (h / 1000000).toFixed(2) + ' MH/s';
      if (h >= 1000) return (h / 1000).toFixed(2) + ' kH/s';
      return h.toFixed(1) + ' H/s';
    }

    function updateThreads(threads) {
      const container = document.getElementById('thread-container');
      
      // Initialize if empty
      if (container.children.length === 0 && threads) {
        threads.forEach((t, i) => {
          const div = document.createElement('div');
          div.className = 'thread-box active';
          div.id = `thread-${i}`;
          div.innerHTML = `
            <div style="font-size: 0.8em; color: #94a3b8;">CPU ${i}</div>
            <div class="thread-hashrate">0 H/s</div>
          `;
          container.appendChild(div);
        });
      }
      
      // Update values
      if (threads) {
        threads.forEach((t, i) => {
          const el = document.getElementById(`thread-${i}`);
          if (el) {
            el.querySelector('.thread-hashrate').textContent = formatHashrate(t.hashrate);
          }
        });
      }
    }

    async function startMining() {
      const config = {
        algorithm: document.getElementById('algo').value,
        pool: document.getElementById('pool').value,
        wallet: document.getElementById('wallet').value,
        worker: document.getElementById('worker').value,
        threads: parseInt(document.getElementById('threads').value)
      };

      if (!config.wallet) {
        alert('Please enter your XMR wallet address!');
        return;
      }

      try {
        const response = await fetch('/api/start', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(config)
        });
        
        const result = await response.json();
        
        if (result.success) {
          logConsole('Mining started successfully!', 'share');
          showMiningDashboard();
          document.getElementById('start-btn').disabled = true;
          document.getElementById('stop-btn').disabled = false;
          
          // Record start time
          const now = new Date();
          document.getElementById('start-time').textContent = now.toLocaleTimeString();
          
          // Start uptime counter
          let seconds = 0;
          uptimeInterval = setInterval(() => {
            seconds++;
            const h = Math.floor(seconds / 3600).toString().padStart(2, '0');
            const m = Math.floor((seconds % 3600) / 60).toString().padStart(2, '0');
            const s = (seconds % 60).toString().padStart(2, '0');
            document.getElementById('live-uptime').textContent = `${h}:${m}:${s}`;
          }, 1000);
        } else {
          logConsole('Error: ' + result.error, 'error');
        }
      } catch (error) {
        logConsole('Failed to start: ' + error.message, 'error');
      }
    }

    async function stopMining() {
      try {
        const response = await fetch('/api/stop', { method: 'POST' });
        const result = await response.json();
        
        if (result.success) {
          logConsole('Mining stopped', 'error');
          document.getElementById('start-btn').disabled = false;
          document.getElementById('stop-btn').disabled = true;
          clearInterval(uptimeInterval);
          
          // Clear threads
          document.getElementById('thread-container').innerHTML = '';
        }
      } catch (error) {
        logConsole('Failed to stop: ' + error.message, 'error');
      }
    }

    function showMiningDashboard() {
      document.getElementById('config-panel').style.display = 'none';
      document.getElementById('mining-dashboard').style.display = 'block';
    }

    function logConsole(message, type = 'info') {
      const console = document.getElementById('console');
      const line = document.createElement('div');
      line.className = `console-line ${type}`;
      const time = new Date().toLocaleTimeString();
      line.textContent = `[${time}] ${message}`;
      console.appendChild(line);
      console.scrollTop = console.scrollHeight;
      
      // Limit lines
      while (console.children.length > 100) {
        console.removeChild(console.firstChild);
      }
    }

    // Initialize
    window.onload = () => {
      connectWebSocket();
      logConsole('Dashboard initialized. Waiting for commands...', 'info');
    };
  </script>
</body>
</html>
