<!DOCTYPE html>
<html>
<head>
<title>Earn Coins Pro</title>

<!-- Firebase (FIXED VERSION) -->
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-firestore.js"></script>

<style>
body {
  margin:0;
  font-family: Arial;
  background: linear-gradient(135deg,#141e30,#243b55);
  color:white;
  text-align:center;
}

/* Loader */
#loader{
  position:fixed;
  width:100%;
  height:100%;
  background:black;
  display:flex;
  justify-content:center;
  align-items:center;
  z-index:999;
}
.spinner{
  width:50px;
  height:50px;
  border:5px solid #333;
  border-top:5px solid orange;
  border-radius:50%;
  animation:spin 1s linear infinite;
}
@keyframes spin{
  100%{transform:rotate(360deg);}
}

/* Card */
.card{
  background: rgba(255,255,255,0.1);
  margin:15px;
  padding:20px;
  border-radius:15px;
  backdrop-filter: blur(10px);
  transition:0.3s;
}
.card:hover{
  transform:scale(1.05);
}

/* Button */
button{
  padding:12px 20px;
  margin:10px;
  border:none;
  border-radius:10px;
  background: linear-gradient(45deg,orange,red);
  color:white;
  cursor:pointer;
  font-weight:bold;
}
button:hover{
  transform:scale(1.1);
  box-shadow:0 0 10px orange;
}

input{
  padding:10px;
  border-radius:8px;
  border:none;
}
</style>

</head>

<body>

<!-- LOADER -->
<div id="loader">
  <div class="spinner"></div>
</div>

<h1>🔥 Earn Coins Pro</h1>

<div class="card">
<button onclick="login()">Login with Google</button>
<h2 id="user"></h2>
</div>

<div class="card">
<button onclick="addCoins()">💰 Get 5 Coins</button>
<h2>Coins: <span id="coins">0</span></h2>
</div>

<div class="card">
<h2>🎁 Redeem</h2>
<button onclick="redeem()">Redeem 100 Coins</button>
</div>

<div class="card">
<h2>🎟️ Promo Code</h2>
<input id="promo" placeholder="Enter Code">
<button onclick="usePromo()">Apply</button>
<p id="msg"></p>
</div>

<script>

// 🔑 Firebase config (yahan apna daalna)
const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN",
  projectId: "YOUR_ID"
};

firebase.initializeApp(firebaseConfig);
let db = firebase.firestore();
let userId;

// 🔐 LOGIN
function login(){
  const provider = new firebase.auth.GoogleAuthProvider();
  firebase.auth().signInWithPopup(provider).then((res)=>{
    userId = res.user.uid;
    document.getElementById("user").innerText = res.user.email;
    loadCoins();
  });
}

// 💰 LOAD COINS
function loadCoins(){
  db.collection("users").doc(userId).get().then(doc=>{
    if(doc.exists){
      document.getElementById("coins").innerText = doc.data().coins;
    } else {
      db.collection("users").doc(userId).set({coins:0});
    }
  });
}

// ➕ ADD COINS (ANIMATION ADDED)
function addCoins(){
  let coinEl = document.getElementById("coins");

  db.collection("users").doc(userId).update({
    coins: firebase.firestore.FieldValue.increment(5)
  });

  coinEl.style.transform = "scale(1.3)";
  setTimeout(()=>{ coinEl.style.transform = "scale(1)"; },200);

  loadCoins();
}

// 🎁 REDEEM
function redeem(){
  let coinText = parseInt(document.getElementById("coins").innerText);

  if(coinText >= 100){
    db.collection("users").doc(userId).update({
      coins: coinText - 100
    });
    document.getElementById("msg").innerText = "✅ Redeem success!";
  } else {
    document.getElementById("msg").innerText = "❌ Not enough coins!";
  }
}

// 🎟️ PROMO
function usePromo(){
  let code = document.getElementById("promo").value;

  if(code == "FREE50"){
    db.collection("users").doc(userId).update({
      coins: firebase.firestore.FieldValue.increment(50)
    });
    document.getElementById("msg").innerText = "🎉 Promo applied!";
  } else {
    document.getElementById("msg").innerText = "❌ Invalid code!";
  }

  loadCoins();
}

// Loader hide
window.onload = function(){
  document.getElementById("loader").style.display="none";
};

</script>

</body>
</html>
