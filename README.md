<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alpha Bot Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #0e0e0e; color: white; font-family: 'Inter', sans-serif; }
        .card { background-color: #161616; border: 1px solid #2e2e2e; }
        .accent { color: #ff444f; } /* Deriv Red */
        .btn-red { background-color: #ff444f; transition: 0.3s; }
        .btn-red:hover { background-color: #d43b44; }
    </style>
</head>
<body class="p-4 md:p-10">
    <div class="flex justify-between items-center mb-10">
        <h1 class="text-2xl font-bold">ALPHA<span class="accent">BOTS</span></h1>
        <button id="login-btn" class="btn-red px-6 py-2 rounded-full font-bold">Login with Deriv</button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-10">
        <div class="card p-6 rounded-xl text-center">
            <p class="text-gray-400 text-sm">Account Balance</p>
            <h2 id="balance" class="text-3xl font-bold mt-2">$0.00</h2>
        </div>
        <div class="card p-6 rounded-xl text-center border-l-4 border-l-green-500">
            <p class="text-gray-400 text-sm">Total Profit</p>
            <h2 class="text-3xl font-bold mt-2 text-green-500">+$0.00</h2>
        </div>
        <div class="card p-6 rounded-xl text-center">
            <p class="text-gray-400 text-sm">Active App ID</p>
            <h2 class="text-xl font-mono mt-2 text-gray-300">33j6Qz5pgGaBP05ztvhhF</h2>
        </div>
    </div>

    <h3 class="text-xl font-bold mb-4">Your Bot Library</h3>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="card p-4 rounded-lg flex justify-between items-center">
            <span>🚀 Martingale Master v1.0 (XML)</span>
            <button class="bg-blue-600 px-4 py-1 rounded text-sm">Load Bot</button>
        </div>
        <div class="card p-4 rounded-lg flex justify-between items-center opacity-50">
            <span>📊 Trend Follower (Locked)</span>
            <span class="text-xs italic text-gray-500">Coming Soon</span>
        </div>
    </div>

    <script>
        const APP_ID = '33j6Qz5pgGaBP05ztvhhF';
        
        // Logic for Login Button
        document.getElementById('login-btn').onclick = () => {
            const redirectUrl = window.location.href; // Grabs your GitHub Pages URL automatically
            window.location.href = `https://oauth.deriv.com/oauth2/authorize?app_id=${APP_ID}&l=en&brand=deriv`;
        };

        // TODO: Add WebSocket logic here to fetch real-time balance
    </script>
</body>
</html>
