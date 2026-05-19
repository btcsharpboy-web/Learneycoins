<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AlphaHub | Trading Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/@supabase/supabase-js@2"></script>
    <style>
        body { background-color: #0b0b0b; color: #e5e7eb; font-family: 'Inter', sans-serif; }
        .glass-card { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.1); backdrop-filter: blur(10px); }
        .deriv-red { background-color: #ff444f; }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center p-4">

    <div id="auth-ui" class="w-full max-w-md mt-10 p-8 glass-card rounded-3xl text-center">
        <h1 class="text-3xl font-bold mb-2 tracking-tighter italic">ALPHA<span class="text-red-500">HUB</span></h1>
        <p class="text-gray-500 text-sm mb-8 font-semibold uppercase tracking-widest">Elite Trading Community</p>
        
        <input id="email" type="email" placeholder="Email Address" class="w-full bg-black border border-gray-800 p-3 rounded-xl mb-3 outline-none focus:border-red-500 text-white">
        <input id="password" type="password" placeholder="Password" class="w-full bg-black border border-gray-800 p-3 rounded-xl mb-6 outline-none focus:border-red-500 text-white">
        
        <button id="btn-signup" class="w-full bg-white text-black font-bold p-3 rounded-xl mb-3 hover:bg-gray-200 transition active:scale-95">Create Account</button>
        <button id="btn-login" class="w-full border border-gray-700 font-bold p-3 rounded-xl hover:bg-gray-900 transition active:scale-95">Login</button>
        
        <p id="status-msg" class="mt-4 text-xs font-bold"></p>
    </div>

    <div id="main-dashboard" class="hidden w-full max-w-5xl mt-5">
        <div class="flex justify-between items-center mb-10 px-4">
            <h1 class="text-xl font-black italic uppercase">ALPHA<span class="text-red-500">HUB</span></h1>
            <button onclick="logout()" class="text-xs text-red-500 font-bold underline">LOGOUT</button>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div id="deriv-connector" class="glass-card p-8 rounded-3xl text-center">
                <h2 class="text-lg font-bold mb-4">Connect Deriv Account</h2>
                <button onclick="connectToDeriv()" class="deriv-red text-white font-bold px-8 py-3 rounded-full text-sm shadow-lg shadow-red-500/20">LINK NOW</button>
            </div>
            <div class="glass-card p-8 rounded-3xl">
                <h3 class="font-bold text-sm mb-4">XML Bot Downloads</h3>
                <div class="p-4 bg-white/5 rounded-2xl flex justify-between items-center border border-white/10">
                    <span class="text-sm font-medium">Martingale Master v1</span>
                    <button class="text-blue-400 text-xs font-bold">GET XML</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // --- CONFIGURATION ---
        const SUPABASE_URL = 'https://YOUR_PROJECT_ID.supabase.co'; // <--- Change this
        const SUPABASE_KEY = 'YOUR_ANON_KEY'; // <--- Change this
        const APP_ID = '33j6Qz5pgGaBP05ztvhhF';

        const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);

        // --- BUTTON LOGIC ---
        document.getElementById('btn-signup').onclick = async () => {
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            const msg = document.getElementById('status-msg');
            
            msg.innerText = "Processing...";
            const { data, error } = await supabaseClient.auth.signUp({ email, password });
            
            if (error) {
                msg.className = "mt-4 text-xs font-bold text-red-500";
                msg.innerText = error.message;
            } else {
                msg.className = "mt-4 text-xs font-bold text-green-500";
                msg.innerText = "Success! Check your email to confirm.";
            }
        };

        document.getElementById('btn-login').onclick = async () => {
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            const msg = document.getElementById('status-msg');

            msg.innerText = "Logging in...";
            const { data, error } = await supabaseClient.auth.signInWithPassword({ email, password });
            
            if (error) {
                msg.className = "mt-4 text-xs font-bold text-red-500";
                msg.innerText = error.message;
            } else {
                location.reload();
            }
        };

        // --- AUTH STATE OBSERVER ---
        supabaseClient.auth.onAuthStateChange((event, session) => {
            if (session) {
                document.getElementById('auth-ui').classList.add('hidden');
                document.getElementById('main-dashboard').classList.remove('hidden');
                console.log("Logged in as:", session.user.email);
            }
        });

        function connectToDeriv() {
            window.location.href = `https://oauth.deriv.com/oauth2/authorize?app_id=${APP_ID}&l=en&brand=deriv`;
        }

        async function logout() {
            await supabaseClient.auth.signOut();
            location.reload();
        }
    </script>
</body>
</html>
