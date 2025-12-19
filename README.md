# HIDECOIN-
App for hiding 3d printed coins in sofia
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HIDECOIN Sofia</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }

        .app-container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            text-align: center;
            color: white;
            margin-bottom: 30px;
            padding: 20px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 15px;
        }

        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .header h2 {
            font-size: 1.2rem;
            opacity: 0.9;
        }

        .view {
            display: none;
            background: white;
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            min-height: 500px;
        }

        .view.active {
            display: block;
        }

        /* Auth Styles */
        .auth-container {
            max-width: 400px;
            margin: 50px auto;
        }

        .auth-form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .auth-form input, .auth-form select {
            padding: 12px 15px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 16px;
            transition: border-color 0.3s;
        }

        .auth-form input:focus, .auth-form select:focus {
            border-color: #667eea;
            outline: none;
        }

        .auth-btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.3s;
        }

        .auth-btn:hover {
            transform: translateY(-2px);
        }

        .auth-switch {
            text-align: center;
            margin-top: 20px;
            color: #666;
        }

        .auth-switch span {
            color: #667eea;
            cursor: pointer;
            font-weight: bold;
        }

        /* Map Styles */
        #map {
            height: 500px;
            border-radius: 10px;
            margin-bottom: 20px;
            border: 3px solid #667eea;
        }

        .map-controls {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .control-btn {
            padding: 10px 20px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.3s;
        }

        .control-btn:hover {
            background: #764ba2;
        }

        .control-btn.logout {
            background: #ff6b6b;
        }

        /* Coin Marker Styles */
        .coin-marker {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
            font-size: 18px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
            border: 3px solid white;
        }

        .coin-1 { background: #4CAF50; }
        .coin-2 { background: #8BC34A; }
        .coin-3 { background: #FFC107; }
        .coin-4 { background: #FF9800; }
        .coin-5 { background: #F44336; }

        /* Profile Styles */
        .profile-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            padding-bottom: 20px;
            border-bottom: 2px solid #f0f0f0;
        }

        .user-info h2 {
            color: #333;
            font-size: 1.8rem;
        }

        .user-info .role {
            display: inline-block;
            padding: 4px 12px;
            background: #667eea;
            color: white;
            border-radius: 20px;
            font-size: 0.9rem;
            margin-left: 10px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .stat-card {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            padding: 20px;
            border-radius: 10px;
            text-align: center;
        }

        .stat-card h3 {
            color: #666;
            margin-bottom: 10px;
        }

        .stat-card .value {
            font-size: 2.5rem;
            font-weight: bold;
            color: #333;
        }

        /* Hider Dashboard Styles */
        .hider-controls {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 30px;
        }

        .hider-form {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
        }

        .form-group label {
            margin-bottom: 5px;
            color: #666;
            font-weight: bold;
        }

        .star-rating {
            display: flex;
            gap: 5px;
        }

        .star {
            font-size: 24px;
            color: #ddd;
            cursor: pointer;
            transition: color 0.3s;
        }

        .star.active {
            color: #FFD700;
        }

        /* Coin List */
        .coins-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 20px;
        }

        .coin-card {
            background: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            transition: transform 0.3s;
            border: 2px solid #e0e0e0;
        }

        .coin-card:hover {
            transform: translateY(-5px);
        }

        .coin-card.found {
            border-color: #4CAF50;
        }

        .coin-rating {
            display: flex;
            gap: 2px;
            margin: 10px 0;
        }

        .coin-star {
            color: #FFD700;
            font-size: 18px;
        }

        .coin-status {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 15px;
            font-size: 0.8rem;
            font-weight: bold;
            margin-top: 10px;
        }

        .status-hidden { background: #ffeb3b; color: #333; }
        .status-found { background: #4CAF50; color: white; }

        /* Message Styles */
        .message {
            padding: 15px;
            border-radius: 8px;
            margin: 10px 0;
            text-align: center;
            display: none;
        }

        .message.error {
            background: #ffebee;
            color: #c62828;
            border: 1px solid #ffcdd2;
        }

        .message.success {
            background: #e8f5e9;
            color: #2e7d32;
            border: 1px solid #c8e6c9;
        }

        /* Responsive */
        @media (max-width: 768px) {
            .app-container {
                padding: 10px;
            }
            
            .header h1 {
                font-size: 1.8rem;
            }
            
            .hider-form {
                grid-template-columns: 1fr;
            }
            
            #map {
                height: 400px;
            }
        }

        .coin-popup {
            text-align: center;
            padding: 10px;
        }

        .popup-btn {
            background: #667eea;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            margin-top: 10px;
            cursor: pointer;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="header">
            <h1>🪙 HIDECOIN Sofia</h1>
            <h2>Find hidden 3D printed coins across Sofia!</h2>
        </div>

        <!-- Login View -->
        <div id="loginView" class="view active">
            <div class="auth-container">
                <h2 style="text-align: center; margin-bottom: 30px; color: #333;">Welcome to HIDECOIN</h2>
                <div id="loginMessage" class="message"></div>
                <form id="loginForm" class="auth-form">
                    <input type="text" id="loginUsername" placeholder="Username" required>
                    <input type="password" id="loginPassword" placeholder="Password" required>
                    <button type="submit" class="auth-btn">Login</button>
                </form>
                <div class="auth-switch">
                    Don't have an account? <span onclick="switchToRegister()">Register</span>
                </div>
            </div>
        </div>

        <!-- Register View -->
        <div id="registerView" class="view">
            <div class="auth-container">
                <h2 style="text-align: center; margin-bottom: 30px; color: #333;">Create Account</h2>
                <div id="registerMessage" class="message"></div>
                <form id="registerForm" class="auth-form">
                    <input type="text" id="registerUsername" placeholder="Username" required>
                    <input type="password" id="registerPassword" placeholder="Password" required>
                    <select id="registerRole">
                        <option value="finder">Finder</option>
                        <option value="hider">Hider</option>
                    </select>
                    <div id="hiderMessage" style="color: #666; font-size: 0.9rem; padding: 10px; background: #f0f0f0; border-radius: 5px;">
                        ⓘ Only one Hider account can exist. If a Hider already exists, you will be registered as a Finder.
                    </div>
                    <button type="submit" class="auth-btn" id="registerBtn">Create Account</button>
                </form>
                <div class="auth-switch">
                    Already have an account? <span onclick="switchToLogin()">Login</span>
                </div>
            </div>
        </div>

        <!-- Main App View -->
        <div id="appView" class="view">
            <!-- Map View -->
            <div id="mapView">
                <div class="profile-header">
                    <div class="user-info">
                        <h2>Welcome, <span id="currentUsername"></span> <span id="userRole" class="role"></span></h2>
                        <p id="userStats"></p>
                    </div>
                    <div class="map-controls">
                        <button class="control-btn" onclick="switchView('mapView')">🗺️ Map</button>
                        <button class="control-btn" onclick="switchView('profileView')">👤 Profile</button>
                        <button class="control-btn" onclick="switchView('coinsView')">🪙 My Coins</button>
                        <button id="hiderDashboardBtn" class="control-btn" style="display: none;" onclick="switchView('hiderView')">🎯 Hider Dashboard</button>
                        <button class="control-btn logout" onclick="logout()">🚪 Logout</button>
                    </div>
                </div>

                <div id="map"></div>
                <div style="text-align: center; margin-top: 20px; color: #666;">
                    <p>Click on coin markers to view details and claim coins!</p>
                    <p>⭐ Rating: 1=Easiest, 5=Hardest</p>
                </div>
            </div>

            <!-- Profile View -->
            <div id="profileView" style="display: none;">
                <div class="profile-header">
                    <h2>Your Profile</h2>
                    <button class="control-btn" onclick="switchView('mapView')">← Back to Map</button>
                </div>
                <div class="stats-grid">
                    <div class="stat-card">
                        <h3>Total Coins Found</h3>
                        <div class="value" id="totalCoinsFound">0</div>
                    </div>
                    <div class="stat-card">
                        <h3>Average Rating</h3>
                        <div class="value" id="avgRating">0</div>
                    </div>
                    <div class="stat-card">
                        <h3>Last Found</h3>
                        <div class="value" id="lastFound">-</div>
                    </div>
                </div>
                <div class="model-viewer">
                    🏆 Your Trophy Collection
                </div>
            </div>

            <!-- Hider Dashboard View -->
            <div id="hiderView" style="display: none;">
                <div class="profile-header">
                    <h2>🎯 Hider Dashboard</h2>
                    <button class="control-btn" onclick="switchView('mapView')">← Back to Map</button>
                </div>
                
                <div class="hider-controls">
                    <h3 style="margin-bottom: 15px;">Hide New Coin</h3>
                    <form id="hideCoinForm" class="hider-form">
                        <div class="form-group">
                            <label for="coinHint">Hint/Description</label>
                            <input type="text" id="coinHint" placeholder="Describe the hiding spot..." required>
                        </div>
                        <div class="form-group">
                            <label>Difficulty Rating</label>
                            <div class="star-rating">
                                <span class="star" onclick="setRating(1)">★</span>
                                <span class="star" onclick="setRating(2)">★</span>
                                <span class="star" onclick="setRating(3)">★</span>
                                <span class="star" onclick="setRating(4)">★</span>
                                <span class="star" onclick="setRating(5)">★</span>
                            </div>
                            <input type="hidden" id="coinRating" value="1">
                        </div>
                        <div class="form-group">
                            <label for="coinLat">Latitude</label>
                            <input type="number" id="coinLat" step="0.000001" placeholder="42.6977" value="42.6977" required>
                        </div>
                        <div class="form-group">
                            <label for="coinLng">Longitude</label>
                            <input type="number" id="coinLng" step="0.000001" placeholder="23.3219" value="23.3219" required>
                        </div>
                        <button type="submit" class="auth-btn" style="grid-column: span 2;">Hide This Coin 🪙</button>
                    </form>
                </div>

                <h3 style="margin: 30px 0 15px 0;">Your Hidden Coins</h3>
                <div id="hiddenCoinsList" class="coins-grid">
                    <!-- Dynamically populated -->
                </div>
            </div>

            <!-- Found Coins View -->
            <div id="coinsView" style="display: none;">
                <div class="profile-header">
                    <h2>Your Found Coins</h2>
                    <button class="control-btn" onclick="switchView('mapView')">← Back to Map</button>
                </div>
                <div id="foundCoinsList" class="coins-grid">
                    <!-- Dynamically populated -->
                </div>
            </div>
        </div>
    </div>

    <script>
        // =========== DATABASE & STATE ===========
        let users = JSON.parse(localStorage.getItem('hidecoin_users') || '[]');
        let coins = JSON.parse(localStorage.getItem('hidecoin_coins') || '[]');
        let currentUser = JSON.parse(localStorage.getItem('hidecoin_currentUser') || 'null');
        let map = null;
        let markers = [];
        let selectedRating = 1;

        // Sofia coordinates
        const SOFIA_CENTER = [42.6977, 23.3219];

        // =========== INITIALIZATION ===========
        function init() {
            // Check if user is logged in
            if (currentUser) {
                showApp();
            } else {
                showLogin();
            }
        }

        // =========== AUTHENTICATION ===========
        function login(username, password) {
            const user = users.find(u => 
                u.username === username && u.password === password
            );
            
            if (user) {
                currentUser = user;
                localStorage.setItem('hidecoin_currentUser', JSON.stringify(user));
                showApp();
                showMessage('loginMessage', 'Login successful!', 'success');
                return true;
            }
            showMessage('loginMessage', 'Invalid username or password!', 'error');
            return false;
        }

        function register(username, password, requestedRole) {
            // Check if username exists
            if (users.some(u => u.username === username)) {
                showMessage('registerMessage', 'Username already exists!', 'error');
                return false;
            }

            // Check if hider already exists
            const hiderExists = users.some(u => u.role === 'hider');
            let actualRole = requestedRole;
            
            // If trying to register as hider when one exists, force finder role
            if (requestedRole === 'hider' && hiderExists) {
                actualRole = 'finder';
                showMessage('registerMessage', 'Hider already exists! You have been registered as a Finder.', 'error');
            }

            const newUser = {
                id: Date.now().toString(),
                username,
                password,
                role: actualRole,
                foundCoins: [],
                createdAt: new Date().toISOString()
            };

            users.push(newUser);
            saveUsers();
            currentUser = newUser;
            localStorage.setItem('hidecoin_currentUser', JSON.stringify(newUser));
            
            // If no users existed before, make first user hider
            if (users.length === 1) {
                newUser.role = 'hider';
                saveUsers();
                showMessage('registerMessage', 'Congratulations! You are the first Hider!', 'success');
            } else if (requestedRole === 'hider' && !hiderExists) {
                showMessage('registerMessage', 'Congratulations! You are now the Hider!', 'success');
            } else {
                showMessage('registerMessage', 'Account created successfully!', 'success');
            }

            // Delay showing app to allow message to be seen
            setTimeout(() => showApp(), 1500);
            return true;
        }

        function logout() {
            currentUser = null;
            localStorage.removeItem('hidecoin_currentUser');
            showLogin();
        }

        // =========== COIN MANAGEMENT ===========
        function hideCoin(hint, rating, lat, lng) {
            const newCoin 
