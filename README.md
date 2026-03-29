<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>ParkEase</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet@1.9.4/dist/leaflet.min.css"/>
  <script src="https://cdn.jsdelivr.net/npm/leaflet@1.9.4/dist/leaflet.min.js"></script>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: Arial, sans-serif; }
    body { background: #f5f5f5; color: #333; }
    .screen { display: none; min-height: 100vh; padding: 20px 20px 80px; }
    .screen.active { display: block; }
    #splash { background: #2c7be5; color: white; flex-direction: column; align-items: center; justify-content: center; text-align: center; padding: 20px; }
    #splash.active { display: flex; min-height: 100vh; }
    #splash h1 { font-size: 48px; margin: 10px 0; }
    #splash p { font-size: 16px; margin-bottom: 30px; opacity: 0.9; }
    .btn { background: #2c7be5; color: white; border: none; padding: 12px 20px; border-radius: 8px; font-size: 15px; cursor: pointer; width: 100%; margin-top: 10px; }
    .btn:hover { opacity: 0.9; }
    .btn-white { background: white; color: #2c7be5; }
    .btn-green { background: #28a745; }
    .btn-red { background: #dc3545; }
    .btn-outline { background: white; color: #2c7be5; border: 2px solid #2c7be5; }
    .btn-small { width: auto; padding: 8px 14px; font-size: 13px; margin-top: 0; }
    input { width: 100%; padding: 10px; margin-top: 6px; margin-bottom: 14px; border: 1px solid #ccc; border-radius: 8px; font-size: 15px; box-sizing: border-box; }
    label { font-weight: bold; font-size: 14px; color: #555; display: block; }
    .card { background: white; border-radius: 10px; padding: 15px; margin-bottom: 12px; box-shadow: 0 2px 6px rgba(0,0,0,0.08); cursor: pointer; }
    .topbar { display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; }
    .badge { display: inline-block; padding: 4px 10px; border-radius: 20px; font-size: 12px; font-weight: bold; }
    .badge-green { background: #d4edda; color: #155724; }
    .badge-blue { background: #cce5ff; color: #004085; }
    .badge-red { background: #f8d7da; color: #721c24; }
    .bottom-nav { position: fixed; bottom: 0; left: 0; right: 0; background: white; border-top: 1px solid #ddd; display: flex; z-index: 999; }
    .nav-btn { flex: 1; padding: 12px; text-align: center; font-size: 12px; border: none; background: transparent; color: #888; cursor: pointer; }
    .nav-btn.active { color: #2c7be5; font-weight: bold; }
    .role-toggle { display: flex; gap: 10px; margin-bottom: 20px; }
    .role-btn { flex: 1; padding: 10px; border: 2px solid #2c7be5; background: white; color: #2c7be5; border-radius: 8px; font-size: 15px; cursor: pointer; font-weight: bold; }
    .role-btn.active { background: #2c7be5; color: white; }
    .spot-row { display: flex; justify-content: space-between; align-items: flex-start; }
    .spot-price { font-size: 18px; font-weight: bold; color: #2c7be5; }
    .tag { display: inline-block; background: #eee; border-radius: 20px; padding: 3px 10px; font-size: 12px; margin: 2px; }
    .stats { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin: 15px 0; }
    .stat-box { background: #f0f4ff; border-radius: 8px; padding: 12px; text-align: center; }
    .stat-box b { font-size: 18px; display: block; }
    .stat-box small { color: #888; }
    .time-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-bottom: 15px; }
    .time-slot { padding: 10px; text-align: center; border: 1px solid #ccc; border-radius: 8px; cursor: pointer; font-size: 14px; }
    .time-slot.active { background: #2c7be5; color: white; border-color: #2c7be5; }
    .time-slot.taken { background: #eee; color: #aaa; cursor: not-allowed; }
    .dur-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 15px; }
    .dur-btn { padding: 10px; text-align: center; border: 1px solid #ccc; border-radius: 8px; cursor: pointer; font-size: 13px; background: white; }
    .dur-btn.active { background: #2c7be5; color: white; border-color: #2c7be5; }
    .summary { background: #f9f9f9; border-radius: 10px; padding: 15px; margin: 15px 0; }
    .sum-row { display: flex; justify-content: space-between; padding: 6px 0; border-bottom: 1px solid #eee; font-size: 14px; color: #555; }
    .sum-row:last-child { border: none; font-weight: bold; font-size: 16px; color: #333; }
    .pay-option { background: white; border: 2px solid #eee; border-radius: 10px; padding: 12px 15px; margin-bottom: 8px; cursor: pointer; display: flex; align-items: center; gap: 10px; }
    .pay-option.active { border-color: #2c7be5; background: #f0f4ff; }
    #confirm { text-align: center; padding-top: 40px; }
    #confirm .icon { font-size: 70px; margin-bottom: 15px; }
    .earn-card { background: #2c7be5; color: white; border-radius: 12px; padding: 20px; margin-bottom: 20px; }
    .earn-card h1 { font-size: 36px; }
    .earn-mini { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 15px; }
    .earn-mini div { background: rgba(255,255,255,0.2); border-radius: 8px; padding: 10px; text-align: center; }
    .earn-mini b { font-size: 20px; display: block; }
    .toggle { width: 44px; height: 24px; background: #ccc; border-radius: 50px; position: relative; cursor: pointer; border: none; transition: background 0.2s; flex-shrink: 0; }
    .toggle.on { background: #28a745; }
    .toggle::after { content: ''; position: absolute; top: 3px; left: 3px; width: 18px; height: 18px; background: white; border-radius: 50%; transition: transform 0.2s; }
    .toggle.on::after { transform: translateX(20px); }
    .upload-zone { border: 2px dashed #2c7be5; border-radius: 10px; padding: 30px; text-align: center; margin-bottom: 15px; color: #2c7be5; cursor: pointer; }
    .v-row { display: flex; gap: 8px; margin-bottom: 15px; }
    .v-btn { flex: 1; padding: 10px; border: 1px solid #ccc; border-radius: 8px; text-align: center; cursor: pointer; font-size: 14px; background: white; }
    .v-btn.active { border-color: #2c7be5; color: #2c7be5; background: #f0f4ff; font-weight: bold; }
    .flex-between { display: flex; justify-content: space-between; align-items: center; }
    .back-link { color: #888; cursor: pointer; margin-bottom: 20px; display: block; font-size: 14px; }
    .divider { height: 1px; background: #eee; margin: 15px 0; }
    #map { height: 260px; border-radius: 12px; margin-bottom: 15px; border: 1px solid #ddd; }
    .filter-row { display: flex; gap: 8px; margin-bottom: 15px; flex-wrap: wrap; }
    .filter-btn { padding: 7px 14px; border: 1px solid #ccc; border-radius: 20px; background: white; font-size: 13px; cursor: pointer; }
    .filter-btn.active { background: #2c7be5; color: white; border-color: #2c7be5; }
    .error-msg { color: red; font-size: 13px; margin-top: 5px; }
    .success-msg { color: green; font-size: 13px; margin-top: 5px; }
  </style>
</head>
<body>

<!-- SCREEN 1: SPLASH -->
<div class="screen active" id="splash">
  <div style="font-size:70px">🅿️</div>
  <h1>ParkEase</h1>
  <p>Book parking spots easily in Chennai</p>
  <button class="btn btn-white" style="width:auto;padding:14px 40px;font-size:17px;" onclick="show('login')">Get Started →</button>
</div>

<!-- SCREEN 2: LOGIN -->
<div class="screen" id="login">
  <span class="back-link" onclick="show('splash')">← Back</span>
  <h2 style="margin-bottom:5px;">Welcome 👋</h2>
  <p style="color:#888;margin-bottom:20px;">Login or Register to continue</p>
  <div class="role-toggle">
    <button class="role-btn active" id="seekerBtn" onclick="setRole('seeker')">🔍 Seeker</button>
    <button class="role-btn" id="hostBtn" onclick="setRole('host')">🏠 Host</button>
  </div>
  <label>Full Name <span style="color:#888;font-weight:normal;">(required for Register)</span></label>
  <input type="text" id="nameInput" placeholder="Enter your name"/>
  <label>Email</label>
  <input type="email" id="emailInput" placeholder="Enter your email"/>
  <label>Password</label>
  <input type="password" id="passInput" placeholder="Enter password"/>
  <div id="loginMsg"></div>
  <button class="btn" onclick="login()">Sign In</button>
  <button class="btn btn-outline" onclick="register()">Register New Account</button>
  <p style="text-align:center;margin-top:15px;color:#888;font-size:13px;">Seeker = find & book · Host = list & earn</p>
</div>

<!-- SCREEN 3: SEEKER HOME -->
<div class="screen" id="home">
  <div class="topbar">
    <div>
      <p style="color:#888;font-size:13px;">Good morning 👋</p>
      <h2 id="welcomeName">Find Parking</h2>
    </div>
    <button class="btn btn-outline btn-small" onclick="logout()">Logout</button>
  </div>
  <div style="display:flex;gap:8px;margin-bottom:15px;">
    <input type="text" id="searchInput" placeholder="🔍 Search area or name..." style="margin:0;flex:1;"/>
    <button class="btn btn-small" onclick="renderSpots()">Search</button>
  </div>
  <div id="map"></div>
  <div class="filter-row">
    <button class="filter-btn active" onclick="setFilter(this,'all')">All</button>
    <button class="filter-btn" onclick="setFilter(this,'car')">🚗 Car</button>
    <button class="filter-btn" onclick="setFilter(this,'bike')">🏍️ Bike</button>
    <button class="filter-btn" onclick="setFilter(this,'cheap')">Under ₹20</button>
  </div>
  <h3 style="margin-bottom:12px;">Available Spots Near You</h3>
  <div id="spotsList"></div>
  <div class="bottom-nav">
    <button class="nav-btn active" onclick="show('home')">🏠 Home</button>
    <button class="nav-btn" onclick="show('mybookings')">📋 Bookings</button>
    <button class="nav-btn" onclick="show('profile')">👤 Profile</button>
  </div>
</div>

<!-- SCREEN 4: SPOT DETAIL -->
<div class="screen" id="detail">
  <span class="back-link" onclick="show('home')">← Back to Search</span>
  <div id="detailContent"></div>
  <div class="bottom-nav">
    <button class="nav-btn" onclick="show('home')">🏠 Home</button>
    <button class="nav-btn" onclick="show('mybookings')">📋 Bookings</button>
    <button class="nav-btn" onclick="show('profile')">👤 Profile</button>
  </div>
</div>

<!-- SCREEN 5: BOOKING -->
<div class="screen" id="booking">
  <div class="topbar">
    <button class="btn btn-outline btn-small" onclick="show('detail')">← Back</button>
    <h2>Book Slot</h2>
  </div>
  <label>Select Date</label>
  <input type="date" id="dateInput"/>
  <label>Select Time</label>
  <div class="time-grid">
    <div class="time-slot taken">9 AM</div>
    <div class="time-slot taken">10 AM</div>
    <div class="time-slot active" onclick="setTime(this)">11 AM</div>
    <div class="time-slot" onclick="setTime(this)">12 PM</div>
    <div class="time-slot" onclick="setTime(this)">1 PM</div>
    <div class="time-slot" onclick="setTime(this)">2 PM</div>
    <div class="time-slot" onclick="setTime(this)">3 PM</div>
    <div class="time-slot" onclick="setTime(this)">4 PM</div>
    <div class="time-slot" onclick="setTime(this)">5 PM</div>
  </div>
  <label>Duration</label>
  <div class="dur-grid">
    <div class="dur-btn" onclick="setDur(this)">1 hr</div>
    <div class="dur-btn active" onclick="setDur(this)">2 hrs</div>
    <div class="dur-btn" onclick="setDur(this)">3 hrs</div>
    <div class="dur-btn" onclick="setDur(this)">Full Day</div>
  </div>
  <label>Payment Method</label>
  <div class="pay-option active" onclick="setPay(this)">
    <span style="font-size:24px;">📱</span>
    <div><b>UPI</b><br/><small style="color:#888;">GPay, PhonePe, Paytm</small></div>
  </div>
  <div class="pay-option" onclick="setPay(this)">
    <span style="font-size:24px;">💵</span>
    <div><b>Cash</b><br/><small style="color:#888;">Pay at the spot</small></div>
  </div>
  <div class="summary" id="bookingSummary"></div>
  <button class="btn btn-green" onclick="confirmBooking()">✅ Confirm Booking</button>
</div>

<!-- SCREEN 6: CONFIRMATION -->
<div class="screen" id="confirm">
  <div class="icon">✅</div>
  <h2>Booking Confirmed!</h2>
  <p style="color:#888;margin:10px 0 25px;">Your slot is successfully booked!</p>
  <div class="card" id="confirmDetails" style="text-align:left;max-width:400px;margin:0 auto 20px;"></div>
  <button class="btn" onclick="show('home')">Back to Home</button>
</div>

<!-- SCREEN 7: MY BOOKINGS -->
<div class="screen" id="mybookings">
  <div class="topbar"><h2>My Bookings</h2></div>
  <div id="bookingsList"><p style="color:#888;text-align:center;">Loading...</p></div>
  <div class="bottom-nav">
    <button class="nav-btn" onclick="show('home')">🏠 Home</button>
    <button class="nav-btn active">📋 Bookings</button>
    <button class="nav-btn" onclick="show('profile')">👤 Profile</button>
  </div>
</div>

<!-- SCREEN 8: PROFILE -->
<div class="screen" id="profile">
  <div style="text-align:center;padding:20px 0 30px;">
    <div style="font-size:60px;">👤</div>
    <h2 id="profileName" style="margin-top:10px;">My Profile</h2>
    <p id="profileEmail" style="color:#888;margin-top:5px;"></p>
    <p id="profileRole" style="color:#2c7be5;font-weight:bold;margin-top:5px;"></p>
  </div>
  <div class="card" onclick="show('mybookings')">📋 My Bookings →</div>
  <div class="card" onclick="show('host')">🏠 Switch to Host Mode →</div>
  <button class="btn btn-red" style="margin-top:10px;" onclick="logout()">Logout</button>
  <div class="bottom-nav">
    <button class="nav-btn" onclick="show('home')">🏠 Home</button>
    <button class="nav-btn" onclick="show('mybookings')">📋 Bookings</button>
    <button class="nav-btn active">👤 Profile</button>
  </div>
</div>

<!-- SCREEN 9: HOST DASHBOARD -->
<div class="screen" id="host">
  <div class="topbar">
    <div><p style="color:#888;font-size:13px;">Host Mode</p><h2 id="hostName">Dashboard</h2></div>
    <button class="btn btn-outline btn-small" onclick="logout()">Logout</button>
  </div>
  <div class="earn-card">
    <p style="opacity:.8;font-size:13px;">Total Earnings · March 2026</p>
    <h1>₹4,280</h1>
    <p style="opacity:.7;font-size:13px;">↑ 12% from last month</p>
    <div class="earn-mini">
      <div><b>23</b><small>Bookings</small></div>
      <div><b>⭐ 4.9</b><small>Rating</small></div>
    </div>
  </div>
  <div class="flex-between" style="margin-bottom:12px;">
    <h3>My Listings</h3>
    <button class="btn btn-small" onclick="show('addlisting')">+ Add New</button>
  </div>
  <div class="card">
    <div style="display:flex;align-items:center;justify-content:space-between;">
      <div><b>🏠 Home Garage</b><p style="color:#888;font-size:13px;margin-top:4px;">₹20/hr · 🚗 Car · 2 slots</p></div>
      <button class="toggle on" onclick="this.classList.toggle('on')"></button>
    </div>
  </div>
  <div class="card">
    <div style="display:flex;align-items:center;justify-content:space-between;">
      <div><b>🏡 Side Driveway</b><p style="color:#888;font-size:13px;margin-top:4px;">₹12/hr · 🏍️ Bike · 3 slots</p></div>
      <button class="toggle" onclick="this.classList.toggle('on')"></button>
    </div>
  </div>
  <div class="bottom-nav">
    <button class="nav-btn active">📊 Dashboard</button>
    <button class="nav-btn" onclick="show('addlisting')">➕ Add</button>
    <button class="nav-btn" onclick="show('profile')">👤 Profile</button>
  </div>
</div>

<!-- SCREEN 10: ADD LISTING -->
<div class="screen" id="addlisting">
  <div class="topbar">
    <button class="btn btn-outline btn-small" onclick="show('host')">← Back</button>
    <h2>Add Listing</h2>
  </div>
  <div class="upload-zone">📸<br/><span style="font-size:14px;">Tap to upload photos</span></div>
  <label>Listing Title</label>
  <input type="text" id="spotTitle" placeholder="e.g. My Home Garage"/>
  <label>Full Address</label>
  <input type="text" id="spotAddress" placeholder="Street, Area, Chennai"/>
  <label>Price per Hour (₹)</label>
  <input type="number" id="spotPrice" placeholder="e.g. 15"/>
  <label>Number of Slots</label>
  <input type="number" id="spotSlots" placeholder="e.g. 2"/>
  <label>Vehicle Type</label>
  <div class="v-row">
    <div class="v-btn active" onclick="this.classList.toggle('active')">🚗 Car</div>
    <div class="v-btn" onclick="this.classList.toggle('active')">🏍️ Bike</div>
    <div class="v-btn" onclick="this.classList.toggle('active')">🚐 Van</div>
  </div>
  <label>Open From</label>
  <input type="time" id="openFrom" value="08:00"/>
  <label>Open To</label>
  <input type="time" id="openTo" value="22:00"/>
  <div id="listingMsg"></div>
  <button class="btn btn-green" onclick="addSpot()">🚀 Publish Listing</button>
</div>

<script>
  const API = 'http://localhost:5000/api';
  var currentSpot = null;
  var currentFilter = 'all';
  var map;
  var role = 'seeker';

  // All parking spots - 10 real Chennai locations
  var allSpots = [
    { id:1,  name:"Ravi's Home Spot",     address:"12, Gandhi St, T. Nagar",             price:15, slots:3, type:"car",  rating:4.7, lat:13.0418, lng:80.2341, host:"Ravi Kumar"   },
    { id:2,  name:"Priya's Driveway",     address:"45, Lake View Rd, Nungambakkam",       price:10, slots:2, type:"bike", rating:4.4, lat:13.0569, lng:80.2425, host:"Priya Sharma" },
    { id:3,  name:"Madhan's Garage",       address:"8, Luz Church Rd, Mylapore",           price:12, slots:4, type:"both", rating:4.5, lat:13.0339, lng:80.2676, host:"Kumar Raja"   },
    { id:4,  name:"Suresh Parking",       address:"22, Anna Salai, Teynampet",            price:18, slots:5, type:"car",  rating:4.6, lat:13.0500, lng:80.2560, host:"Suresh B"     },
    { id:5,  name:"Meena's Open Lot",     address:"10, NSK St, Adyar",                    price:8,  slots:6, type:"both", rating:4.3, lat:13.0067, lng:80.2561, host:"Meena Devi"   },
    { id:6,  name:"Raja Covered Spot",    address:"33, Arcot Rd, Vadapalani",             price:20, slots:2, type:"car",  rating:4.8, lat:13.0504, lng:80.2121, host:"Raja M"       },
    { id:7,  name:"Lakshmi Home Spot",    address:"5, Poes Garden, Teynampet",            price:14, slots:3, type:"bike", rating:4.5, lat:13.0456, lng:80.2489, host:"Lakshmi V"    },
    { id:8,  name:"Venkat's Garage",      address:"17, ECR, Besant Nagar",                price:16, slots:4, type:"both", rating:4.7, lat:12.9990, lng:80.2707, host:"Venkat S"     },
    { id:9,  name:"Siva Corner Parking",  address:"9, Nehru St, Chromepet",               price:10, slots:5, type:"car",  rating:4.4, lat:12.9516, lng:80.1462, host:"Siva K"       },
    { id:10, name:"Anbu Home Garage",     address:"27, Poonamallee High Rd, Arumbakkam",  price:12, slots:3, type:"both", rating:4.6, lat:13.0732, lng:80.2099, host:"Anbu R"       }
  ];

  // SHOW SCREEN
  function show(id) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    window.scrollTo(0, 0);
    if (id === 'home') { renderSpots(); if (map) setTimeout(() => map.invalidateSize(), 200); }
    if (id === 'mybookings') renderBookings();
  }

  // ROLE TOGGLE
  function setRole(r) {
    role = r;
    document.getElementById('seekerBtn').classList.toggle('active', r === 'seeker');
    document.getElementById('hostBtn').classList.toggle('active', r === 'host');
  }

  // LOGIN
  async function login() {
    var email = document.getElementById('emailInput').value.trim();
    var pass = document.getElementById('passInput').value.trim();
    var msg = document.getElementById('loginMsg');
    if (!email || !pass) { msg.innerHTML = '<p class="error-msg">Please enter email and password!</p>'; return; }
    try {
      const res = await fetch(API + '/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, password: pass })
      });
      const data = await res.json();
      if (res.ok) {
        localStorage.setItem('name', data.name);
        localStorage.setItem('role', data.role);
        localStorage.setItem('email', email);
        afterLogin(data.name, data.role);
      } else {
        msg.innerHTML = '<p class="error-msg">' + data.message + '</p>';
      }
    } catch (err) {
      msg.innerHTML = '<p class="error-msg">⚠️ Server not running! Run: node server.js</p>';
    }
  }

  // REGISTER
  async function register() {
    var name = document.getElementById('nameInput').value.trim();
    var email = document.getElementById('emailInput').value.trim();
    var pass = document.getElementById('passInput').value.trim();
    var msg = document.getElementById('loginMsg');
    if (!name || !email || !pass) { msg.innerHTML = '<p class="error-msg">Please fill Name, Email and Password!</p>'; return; }
    try {
      const res = await fetch(API + '/auth/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email, password: pass, role })
      });
      const data = await res.json();
      if (res.ok) {
        msg.innerHTML = '<p class="success-msg">✅ Registered! Logging in...</p>';
        localStorage.setItem('name', name);
        localStorage.setItem('role', role);
        localStorage.setItem('email', email);
        setTimeout(() => afterLogin(name, role), 800);
      } else {
        msg.innerHTML = '<p class="error-msg">' + data.message + '</p>';
      }
    } catch (err) {
      msg.innerHTML = '<p class="error-msg">⚠️ Server not running! Run: node server.js</p>';
    }
  }

  // AFTER LOGIN
  function afterLogin(name, userRole) {
    document.getElementById('welcomeName').innerText = 'Hello, ' + name + ' 👋';
    document.getElementById('hostName').innerText = 'Hello, ' + name;
    document.getElementById('profileName').innerText = name;
    document.getElementById('profileEmail').innerText = localStorage.getItem('email') || '';
    document.getElementById('profileRole').innerText = userRole === 'host' ? '🏠 Host Account' : '🔍 Seeker Account';
    if (userRole === 'host') show('host');
    else show('home');
  }

  // LOGOUT
  function logout() {
    localStorage.clear();
    document.getElementById('emailInput').value = '';
    document.getElementById('passInput').value = '';
    document.getElementById('nameInput').value = '';
    document.getElementById('loginMsg').innerHTML = '';
    show('login');
  }

  // RENDER SPOTS
  function renderSpots() {
    var search = (document.getElementById('searchInput').value || '').toLowerCase();
    var spots = allSpots.filter(function(s) {
      var matchFilter = true;
      if (currentFilter === 'car') matchFilter = s.type === 'car' || s.type === 'both';
      else if (currentFilter === 'bike') matchFilter = s.type === 'bike' || s.type === 'both';
      else if (currentFilter === 'cheap') matchFilter = s.price < 20;
      var matchSearch = !search || s.name.toLowerCase().includes(search) || s.address.toLowerCase().includes(search);
      return matchFilter && matchSearch;
    });
    var html = spots.length === 0 ? '<p style="color:#888;text-align:center;margin-top:20px;">No spots found!</p>' :
      spots.map(function(s) {
        var typeLabel = s.type === 'car' ? '🚗 Car' : s.type === 'bike' ? '🏍️ Bike' : '🚗 Car · 🏍️ Bike';
        return '<div class="card" onclick="openDetail(' + s.id + ')">' +
          '<div class="spot-row"><div>' +
          '<b>🏠 ' + s.name + '</b>' +
          '<p style="color:#888;font-size:13px;margin-top:4px;">📍 ' + s.address + '</p>' +
          '<div style="margin-top:6px;"><span class="tag">' + typeLabel + '</span></div>' +
          '</div><div style="text-align:right;">' +
          '<div class="spot-price">₹' + s.price + '/hr</div>' +
          '<span class="badge badge-green">Available</span>' +
          '<p style="font-size:12px;color:#888;margin-top:4px;">⭐ ' + s.rating + '</p>' +
          '</div></div></div>';
      }).join('');
    document.getElementById('spotsList').innerHTML = html;
  }

  // SET FILTER
  function setFilter(btn, type) {
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentFilter = type;
    renderSpots();
  }

  // OPEN DETAIL
  function openDetail(id) {
    currentSpot = allSpots.find(s => s.id === id);
    if (!currentSpot) return;
    var typeLabel = currentSpot.type === 'car' ? '🚗 Car' : currentSpot.type === 'bike' ? '🏍️ Bike' : '🚗🏍️ Both';
    document.getElementById('detailContent').innerHTML =
      '<div style="font-size:80px;text-align:center;background:#e8f4ff;border-radius:12px;padding:20px;margin-bottom:15px;">🏠</div>' +
      '<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:5px;">' +
      '<h2>' + currentSpot.name + '</h2><span class="badge badge-green">● Available</span></div>' +
      '<p style="color:#888;margin-bottom:15px;">📍 ' + currentSpot.address + '</p>' +
      '<div class="stats">' +
      '<div class="stat-box"><b>₹' + currentSpot.price + '/hr</b><small>Price</small></div>' +
      '<div class="stat-box"><b style="color:green;">' + currentSpot.slots + ' Free</b><small>Slots</small></div>' +
      '<div class="stat-box"><b>' + typeLabel + '</b><small>Vehicle</small></div>' +
      '<div class="stat-box"><b>⭐ ' + currentSpot.rating + '</b><small>Rating</small></div>' +
      '</div>' +
      '<div class="card"><div style="display:flex;align-items:center;gap:12px;">' +
      '<div style="width:45px;height:45px;background:#2c7be5;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:20px;">👨</div>' +
      '<div><b>' + currentSpot.host + '</b><br/><small style="color:#888;">✅ Verified Host</small></div>' +
      '</div></div>' +
      '<p style="color:#888;font-size:14px;margin-bottom:20px;">🕐 Open: 7:00 AM – 10:00 PM · 🔒 Secure Parking</p>' +
      '<div style="display:flex;justify-content:space-between;align-items:center;">' +
      '<div><b style="font-size:20px;color:#2c7be5;">₹' + currentSpot.price + '/hr</b><br/><small style="color:#888;">Free cancellation</small></div>' +
      '<button class="btn" style="width:auto;padding:12px 30px;" onclick="goBooking()">Book Now →</button>' +
      '</div>';
    show('detail');
  }

  // GO TO BOOKING
  function goBooking() {
    if (!currentSpot) return;
    var total = (currentSpot.price * 2) + 3;
    document.getElementById('bookingSummary').innerHTML =
      '<div class="sum-row"><span>₹' + currentSpot.price + ' × 2 hrs</span><span>₹' + (currentSpot.price * 2) + '</span></div>' +
      '<div class="sum-row"><span>Service fee</span><span>₹3</span></div>' +
      '<div class="sum-row"><span>Total</span><span style="color:#2c7be5;">₹' + total + '</span></div>';
    show('booking');
  }

  // CONFIRM BOOKING
  async function confirmBooking() {
    if (!currentSpot) return;
    var date = document.getElementById('dateInput').value;
    var name = localStorage.getItem('name') || 'Guest';
    var total = (currentSpot.price * 2) + 3;
    var bookingId = 'PKE-' + Math.floor(Math.random() * 9000 + 1000) + '-CH';
    try {
      await fetch(API + '/bookings/add', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ seeker: name, spot: currentSpot.name, date, time: '11 AM', duration: '2 hrs', payment: 'UPI', total })
      });
    } catch(e) {}
    document.getElementById('confirmDetails').innerHTML =
      '<div class="sum-row"><span style="color:#888;">Location</span><b>' + currentSpot.name + '</b></div>' +
      '<div class="sum-row"><span style="color:#888;">Address</span><b>' + currentSpot.address + '</b></div>' +
      '<div class="sum-row"><span style="color:#888;">Date</span><b>' + (date || 'Today') + '</b></div>' +
      '<div class="sum-row"><span style="color:#888;">Time</span><b>11 AM – 1 PM</b></div>' +
      '<div class="sum-row"><span style="color:#888;">Payment</span><b>UPI ✓</b></div>' +
      '<div class="sum-row"><span style="color:#888;">Total Paid</span><b style="color:#2c7be5;">₹' + total + '</b></div>' +
      '<div class="sum-row"><span style="color:#888;">Booking ID</span><b style="color:#2c7be5;">' + bookingId + '</b></div>';
    show('confirm');
  }

  // RENDER BOOKINGS
  async function renderBookings() {
    var list = document.getElementById('bookingsList');
    var name = localStorage.getItem('name') || '';
    try {
      const res = await fetch(API + '/bookings/' + encodeURIComponent(name));
      const data = await res.json();
      if (!data.length) { list.innerHTML = '<p style="color:#888;text-align:center;margin-top:30px;">No bookings yet! Book a spot first.</p>'; return; }
      list.innerHTML = data.map(function(b) {
        return '<div class="card">' +
          '<div style="display:flex;justify-content:space-between;margin-bottom:8px;">' +
          '<b>' + b.spot + '</b><span class="badge badge-blue">' + b.status + '</span></div>' +
          '<p style="color:#888;font-size:13px;">📅 ' + b.date + ' · ⏰ ' + b.time + ' · 💰 ₹' + b.total + '</p>' +
          '</div>';
      }).join('');
    } catch(e) {
      list.innerHTML = '<p style="color:#888;text-align:center;">⚠️ Start server to see bookings!</p>';
    }
  }

  // ADD SPOT
  async function addSpot() {
    var title = document.getElementById('spotTitle').value;
    var address = document.getElementById('spotAddress').value;
    var price = document.getElementById('spotPrice').value;
    var slots = document.getElementById('spotSlots').value;
    var msg = document.getElementById('listingMsg');
    if (!title || !address || !price || !slots) { msg.innerHTML = '<p class="error-msg">Please fill all fields!</p>'; return; }
    try {
      const res = await fetch(API + '/spots/add', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ title, address, price, slots, host: localStorage.getItem('name') })
      });
      const data = await res.json();
      msg.innerHTML = '<p class="success-msg">✅ ' + data.message + '</p>';
      setTimeout(() => show('host'), 1000);
    } catch(e) {
      msg.innerHTML = '<p class="error-msg">⚠️ Server not running!</p>';
    }
  }

  // TIME / DUR / PAY
  function setTime(el) {
    if (el.classList.contains('taken')) return;
    document.querySelectorAll('.time-slot').forEach(t => t.classList.remove('active'));
    el.classList.add('active');
  }
  function setDur(el) {
    document.querySelectorAll('.dur-btn').forEach(d => d.classList.remove('active'));
    el.classList.add('active');
  }
  function setPay(el) {
    document.querySelectorAll('.pay-option').forEach(p => p.classList.remove('active'));
    el.classList.add('active');
  }

  document.getElementById('dateInput').valueAsDate = new Date();

  // MAP
  window.onload = function() {
    map = L.map('map').setView([13.0500, 80.2400], 11);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { attribution: '© OpenStreetMap' }).addTo(map);
    allSpots.forEach(function(spot) {
      L.marker([spot.lat, spot.lng]).addTo(map)
        .bindPopup('<b>' + spot.name + '</b><br/>₹' + spot.price + '/hr · ⭐' + spot.rating);
    });
    renderSpots();
  };
</script>
</body>
</html>
