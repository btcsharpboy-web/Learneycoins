<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AlphaHub | Member Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/@supabase/supabase-js@2"></script>
    <style>
        body { background-color: #0b0b0b; color: #e5e7eb; font-family: 'Inter', sans-serif; }
        .glass-card { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.1); backdrop-filter: blur(10px); }
        .deriv-red { background-color: #ff444f; }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center p-4">

    <div id="auth-ui" class="w-full max-w-md mt-20 p-8 glass-card rounded-3xl text-center">
        <h1 class="text-3xl font-bold mb-2 tracking-tighter italic">ALPHA<span class="text-red-500">HUB</span></h1>
        <p class="text-gray-500 text-sm mb-8">Join the elite trading community</p>
        
        <input id="email" type="email" placeholder="Email Address" class="w-full bg-black border border-gray-800 p-3 rounded-xl mb-3 outline-none focus:border-red-500">
        <input id="password" type="password" placeholder="Password" class="w-full bg-black border border-gray-800 p-3 rounded-xl mb-6 outline-none focus:border-red-500">
        
        <button onclick="handleSignup()" class="w-full bg-white text-black font-bold p-3 rounded-xl mb-3 hover:bg-gray-200 transition">Create Account</button>
        <button onclick="handleLogin()" class="w-full border border-gray-700 font-bold p-3 rounded-xl hover:bg-gray-900 transition">Login</button>
    </div>

    <div id="main-dashboard" class="hidden w-full max-w-5xl">
        <div class="flex justify-between items-center mb-10">
            <h1 class="text-xl font-black italic">ALPHA<span class="text-red-500">HUB</span></h1>
            <div class="flex items-center gap-4">
                <span id="user-email" class="text-xs text-gray-500 font-mono"></span>
                <button onclick="logout()" class="text-xs text-red-500 hover:underline">Logout</button>
            </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div id="deriv-connector" class="glass-card p-8 rounded-3xl text-center">
                <img src="https://deriv.com/static/8636e05391307b0c3672d547f8976b32/deriv-logo.svg" class="h-8 mx-auto mb-4 grayscale opacity-50">
                <h2 class="text-lg font-bold mb-4">Connect to Deriv</h2>
                <p class="text-xs text-gray-500 mb-6 leading-relaxed">To start trading or use bots, you must link your official Deriv account via secure OAuth.</p>
                <button onclick="connectToDeriv()" class="deriv-red text-white font-bold px-8 py-3 rounded-full text-sm shadow-lg shadow-red-500/20">LINK ACCOUNT NOW</button>
            </div>

            <div class="glass-card p-8 rounded-3xl">
                <h3 class="font-black text-sm uppercase tracking-widest text-gray-600 mb-6">Exclusive XML Bots</h3>
                <div class="space-y-4">
                    <div class="flex items-center justify-between p-4 bg-white/5 rounded-2xl">
                        <div>
                            <p class="font-bold text-sm">Martingale Alpha v1</p>
                            <p class="text-[10px] text-gray-500">Vol 100 Index | High Frequency</p>
                        </div>
                        <button class="text-xs font-bold text-blue-400">DOWNLOAD</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // REPLACE THESE WITH YOUR OWN SUPABASE CREDENTIALS
        const SUPABASE_URL = 'YOUR_SUPABASE_URL';
        const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';
        const APP_ID = '33j6Qz5pgGaBP05ztvhhF';

        const supabase = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);

        // --- AUTH LOGIC ---
        async function handleSignup() {
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            const { data, error } = await supabase.auth.signUp({ email, password });
            if (error) alert(error.message); else alert("Check your email for confirmation!");
        }

        async function handleLogin() {
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            const { data, error } = await supabase.auth.signInWithPassword({ email, password });
            if (error) alert(error.message); else location.reload();
        }

        // --- SESSION MANAGEMENT ---
        supabase.auth.onAuthStateChange((event, session) => {
            if (session) {
                document.getElementById('auth-ui').classList.add('hidden');
                document.getElementById('main-dashboard').classList.remove('hidden');
                document.getElementById('user-email').innerText = session.user.email;
                checkDerivLink(session.user.id);
            }
        });

        // --- DERIV LINKING ---
        function connectToDeriv() {
            // After login, Deriv will send the user back with a token in the URL
            window.location.href = `https://oauth.deriv.com/oauth2/authorize?app_id=${APP_ID}&l=en&brand=deriv`;
        }

        async function checkDerivLink(userId) {
            // Check the URL for the token (this happens after redirect)
            const params = new URLSearchParams(window.location.search);
            const token = params.get('token1');
            
            if (token) {
                // Save the token to your database for this user!
                const { error } = await supabase.from('profiles').upsert({ id: userId, deriv_token: token });
                if (!error) {
                    document.getElementById('deriv-connector').innerHTML = `<h2 class="text-green-500 font-bold">✓ Account Linked Successfully</h2>`;
                    window.history.replaceState({}, document.title, "/"); 
                }
            }
        }

        async function logout() {
            await supabase.auth.signOut();
            location.reload();
        }
    </script>
</body>
</html>
