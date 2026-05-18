<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AlphaBot | Third-Party Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #0e0e0e; color: white; font-family: 'Inter', sans-serif; }
        .card { background-color: #161616; border: 1px solid #2e2e2e; }
        .accent-red { color: #ff444f; }
        .bg-deriv-red { background-color: #ff444f; }
        .bg-deriv-red:hover { background-color: #d43b44; }
        .status-online { color: #4bb543; }
    </style>
</head>
<body class="p-4 md:p-10">
    <div class="flex justify-between items-center mb-10">
        <h1 class="text-2xl font-bold tracking-tighter">ALPHA<span class="accent-red">BOTS</span></h1>
        <div id="auth-section">
            <button id="login-btn" class="bg-deriv-red px-6 py-2 rounded-full font-bold text-sm transition">LOGIN WITH DERIV</button>
        </div>
    </div>

    <div class="mb-6 flex items-center gap-2 text-xs uppercase tracking-widest text-gray-500">
        <span id="connection-dot" class="h-2 w-2 rounded-full bg-gray-500"></span>
        <span id="connection-text">Offline</span>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-10">
        <div class="card p-6 rounded-2xl">
            <p class="text-gray-400 text-xs uppercase font-semibold">Live Balance</p>
            <h2 id="balance-display" class="text-4xl font-bold mt-2">$0.00</h2>
        </div>
        <div class="card p-6 rounded-2xl border-b-4 border-blue-500">
            <p class="text-gray-400 text-xs uppercase font-semibold">Account Type</p>
            <h2 id="account-type" class="text-2xl font-bold mt-2 uppercase text-blue-400">---</h2>
        </div>
        <div class="card p-6 rounded-2xl">
            <p class="text-gray-400 text-xs uppercase font-semibold">Currency</p>
            <h2 id="currency-display" class="text-2xl font-bold mt-2">---</h2>
        </div>
    </div>

    <div class="card p-8 rounded-3xl">
        <h3 class="text-xl font-bold mb-6">Available XML Bots</h3>
        <div class="space-y-4">
            <div class="flex items-center justify-between p-4 bg-black/30 rounded-xl border border-white/5">
                <div>
                    <h4 class="font-bold text-gray-200">Martingale Master v1.0</h4>
                    <p class="text-xs text-gray-500">Market: Volatility 100 (1s) Index</p>
                </div>
                <button onclick="alert('Downloading XML...')" class="text-xs font-bold border border-gray-600 px-4 py-2 rounded-lg hover:bg-white hover:text-black transition">DOWNLOAD XML</button>
            </div>
            </div>
    </div>

    <script>
        const APP_ID = '33j6Qz5pgGaBP05ztvhhF'; 
        let ws;

        // 1. Handle OAuth Redirect
        const urlParams = new URLSearchParams(window.location.search);
        const tokenFromUrl = urlParams.get('token1');

        if (tokenFromUrl) {
            localStorage.setItem('deriv_token', tokenFromUrl);
            window.history.replaceState({}, document.title, window.location.pathname); // Clean URL
            initWebSocket(tokenFromUrl);
        } else if (localStorage.getItem('deriv_token')) {
            initWebSocket(localStorage.getItem('deriv_token'));
        }

        // 2. Login Function
        document.getElementById('login-btn').onclick = () => {
            window.location.href = `https://oauth.deriv.com/oauth2/authorize?app_id=${APP_ID}&l=en&brand=deriv`;
        };

        // 3. WebSocket Connection
        function initWebSocket(token) {
            ws = new WebSocket(`wss://ws.binaryws.com/websockets/v3?app_id=${APP_ID}`);

            ws.onopen = () => {
                ws.send(JSON.stringify({ authorize: token }));
                document.getElementById('connection-dot').classList.replace('bg-gray-500', 'bg-green-500');
                document.getElementById('connection-text').innerText = 'Connected';
                document.getElementById('auth-section').innerHTML = `<button onclick="logout()" class="text-xs text-gray-500 underline">Logout</button>`;
            };

            ws.onmessage = (msg) => {
                const data = JSON.parse(msg.data);
                
                if (data.msg_type === 'authorize') {
                    document.getElementById('balance-display').innerText = `$${data.authorize.balance}`;
                    document.getElementById('account-type').innerText = data.authorize.loginid.startsWith('VRTC') ? 'Demo' : 'Real';
                    document.getElementById('currency-display').innerText = data.authorize.currency;
                    
                    // Subscribe to ongoing balance updates
                    ws.send(JSON.stringify({ balance: 1, subscribe: 1 }));
                }

                if (data.msg_type === 'balance') {
                    document.getElementById('balance-display').innerText = `$${data.balance.balance}`;
                }
            };
        }

        function logout() {
            localStorage.removeItem('deriv_token');
            window.location.reload();
        }
    </script>
</body>
</html>
