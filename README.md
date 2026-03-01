<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Attendance – Grade 12 Diamond</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">

  <!-- QR Scanner library -->
  <script src="https://unpkg.com/html5-qrcode"></script>

  <!-- Firebase v12 -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.10.0/firebase-app.js";
    import { getDatabase, ref, set, onValue } from "https://www.gstatic.com/firebasejs/12.10.0/firebase-database.js";

    const firebaseConfig = {
      apiKey: "AIzaSyBU5wgWTIgFetO38U28ikmoLC0CKryR05M",
      authDomain: "talaan-b5c66.firebaseapp.com",
      databaseURL: "https://talaan-b5c66-default-rtdb.asia-southeast1.firebasedatabase.app",
      projectId: "talaan-b5c66",
      storageBucket: "talaan-b5c66.firebasestorage.app",
      messagingSenderId: "725034061224",
      appId: "1:725034061224:web:b2ac35e9e8f0f4c6e8a19d"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    /* ----------------- WEEK KEY ----------------- */
    function getWeekKey() {
      let now = new Date();
      let day = now.getDay();
      let diff = now.getDate() - day + (day === 0 ? -6 : 1);
      let monday = new Date(now.setDate(diff));
      return "attendance_" + monday.toISOString().split("T")[0];
    }

    /* ----------------- SAVE ----------------- */
    window.saveToDatabase = function(data, tardy) {
      set(ref(db, "attendance/" + getWeekKey()), {
        table: data,
        tardy: tardy
      });
    };

    /* ----------------- REALTIME LISTENER ----------------- */
    window.listenToDatabase = function(callback) {
      onValue(ref(db, "attendance/" + getWeekKey()), (snapshot) => {
        if (snapshot.exists()) {
          callback(snapshot.val());
        }
      });
    };
  </script>

  <style>
    :root { --maroon: #7a0c0c; }
    * { box-sizing: border-box; }
    body { margin: 0; font-family: 'Poppins', sans-serif; background: #f2f2f2; }

    /* LOGIN */
    #login {
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: linear-gradient(rgba(122,12,12,.85), rgba(122,12,12,.85)),
                  url("https://images.unsplash.com/photo-1524995997946-a1c2e315a42f");
      background-size: cover;
      background-position: center;
      padding: 15px;
    }
    .login-box {
      background: #fff;
      width: 100%;
      max-width: 380px;
      padding: 25px;
      border-radius: 18px;
      box-shadow: 0 15px 30px rgba(0,0,0,.35);
    }
    .login-box h2 {
      text-align: center;
      color: var(--maroon);
    }
    input, select, button {
      width: 100%;
      padding: 12px;
      margin-top: 10px;
      font-size: 14px;
    }
    button {
      background: var(--maroon);
      color: white;
      border: none;
      border-radius: 10px;
      cursor: pointer;
    }

    /* MAIN */
    #main { display: none; padding: 10px; }
    header {
      background: var(--maroon);
      color: white;
      padding: 14px;
      text-align: center;
      border-radius: 14px;
    }
    .sub { font-size: 13px; opacity: .9; }
    #studentAction {
      display: none;
      margin-top: 10px;
      background: white;
      padding: 12px;
      border-radius: 12px;
      text-align: center;
    }
    #timeNow { font-size: 13px; margin-bottom: 6px; }

    /* TOP BAR */
    .top-bar {
      margin-top: 12px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    #searchInput {
      max-width: 260px;
      padding: 8px;
      border-radius: 8px;
      border: 1px solid #ccc;
    }
    #dateDisplay { font-weight: 600; }
    #adminReset {
      display: none;
      padding: 8px 12px;
      background: #b30000;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-left: 10px;
    }

    /* TABLE */
    .table-wrap { overflow-x: auto; background: white; border-radius: 14px; margin-top: 12px; }
    table { border-collapse: collapse; width: 100%; min-width: 1200px; }
    th, td {
      border: 2px solid #999;
      padding: 6px;
      text-align: center;
      font-size: 11px;
      min-width: 60px;
    }
    th { background: #eee; font-weight: 600; }
    .day-head { background: var(--maroon); color: white; border-right: 4px solid black; }
    .sticky { position: sticky; left: 0; background: white; z-index: 4; font-weight: 600; }
    .name { left: 42px; z-index: 5; background: white; }
    .summary-col {
      background: #f7f7f7;
      min-width: 160px;
      font-weight: 600;
    }
    .P { background: #c8f7c5; font-weight: 600; }
    .T { background: #fff3b0; font-weight: 600; }
    .C { background: #ffb3b3; font-weight: 600; }
    .A { background: #e0e0e0; font-weight: 600; }

    /* QR Interface */
    #qrAuthInterface { display: none; padding: 20px; text-align: center; }
    #qrVideo { width: 100%; max-width: 400px; border: 1px solid #ccc; }
    #qrStatus { color: red; font-size: 13px; margin-top: 6px; }

    /* MOBILE */
    @media screen and (max-width:480px) {
      #qrVideo { width: 100%; height: auto; }
      header { font-size: 14px; padding: 12px; }
      .sub { font-size: 11px; }
      #searchInput { width: 100%; margin-top: 8px; }
      .top-bar { flex-direction: column; align-items: stretch; gap: 6px; }
      th, td { font-size: 10px; padding: 4px; min-width: 55px; }
      .summary-col { min-width: 180px; font-size: 10px; }
    }
    .sticky { box-shadow: 2px 0 4px rgba(0,0,0,0.1); }
  </style>
</head>

<body>

  <!-- LOGIN -->
  <div id="login">
    <div class="login-box">
      <h2>Attendance Login</h2>
      <select id="role">
        <option>Student</option>
        <option>Teacher</option>
        <option>Student Officer</option>
      </select>
      <input id="user" placeholder="Last Name">
      <input id="pass" type="password" placeholder="Student Number">
      <input id="adminPass" type="password" placeholder="Admin Password" style="display:none">
      <button onclick="loginUser()">Log in</button>
    </div>
  </div>

  <!-- QR AUTH -->
  <div id="qrAuthInterface">
    <h2>QR Code Authentication</h2>
    <p>Scan your personal QR code to access the attendance sheet.</p>
    <div id="qrVideo" style="width:100%;max-width:400px;margin:auto;"></div>
    <p id="qrStatus"></p>
    <button onclick="startQRScanner()">Start Scanner</button>
    <button onclick="cancelQR()">Cancel</button>
  </div>

  <!-- MAIN ATTENDANCE -->
  <div id="main">
    <header>
      DAILY ATTENDANCE – GRADE 12 DIAMOND
      <div class="sub">Adviser: January Lyn Alumbres</div>
    </header>

    <div id="studentAction">
      <div id="timeNow"></div>
      <button onclick="markPresent()">MARK PRESENT</button>
    </div>

    <div class="top-bar">
      <div>
        <span id="dateDisplay"></span>
        <button id="adminReset" onclick="manualReset()">RESET WEEK</button>
      </div>
      <input type="text" id="searchInput" placeholder="🔍 Search student...">
    </div>

    <div style="margin-top:10px;">
      <label for="weekSelect">Select Week: </label>
      <select id="weekSelect"></select>
    </div>

    <div class="table-wrap">
      <table>
        <thead>
          <tr>
            <th rowspan="2" class="sticky">#</th>
            <th rowspan="2" class="sticky name">Student</th>
            <th colspan="7" class="day-head">MON</th>
            <th colspan="7" class="day-head">TUE</th>
            <th colspan="7" class="day-head">WED</th>
            <th colspan="7" class="day-head">THU</th>
            <th colspan="7" class="day-head">FRI</th>
            <th rowspan="2" class="summary-col">SUMMARY</th>
          </tr>
          <tr id="subjectRow"></tr>
        </thead>
        <tbody id="tbody"></tbody>
      </table>
    </div>
  </div>

<script>
/* ----------------- DATA ----------------- */
const adminPassword = "123456789";
const subs = [
  ["CHEM", "07:30"],
  ["DRRR", "08:20"],
  ["PE", "09:30"],
  ["INQ", "10:20"],
  ["MIL", "13:00"],
  ["PHYS", "13:50"],
  ["CAP", "14:40"]
];

const users = {
  "adiaton": "01", "bacaycay": "02", "broto": "03", "caramol": "04",
  "comedia": "05", "cuyag": "06", "de la cruz": "07", "delmoro": "08",
  "delorino": "09", "enano": "10", "esparto": "11", "espinola": "12",
  "etac": "13", "florano": "14", "herreras": "15", "jumadiao": "16",
  "loberiano": "17", "mangada": "18", "paulino": "19", "tan": "20",
  "velasco": "21", "apelo": "22", "arceo": "23", "arniño": "24",
  "balleta": "25", "barojabo": "26", "bobiles": "27", "caro": "28",
  "cornico": "29", "de rafael": "30", "escalante": "31", "frigillana": "32",
  "gallano": "33", "gremio": "34", "hipe": "35", "imperial": "36",
  "irinco": "37", "lee": "38", "lim": "39", "magdaraog": "40",
  "mangada k": "41", "meregildo": "42", "perez": "43", "pulga": "44",
  "ponferrada": "45", "santos": "46", "sidro": "47", "sister": "48",
  "teberio": "49", "vibar": "50"
};

const students = Object.keys(users).map(n => n.toUpperCase());
let roleType = "", loggedStudent = "", tardyMinutesData = {}, html5QrCode;

/* ----------------- QR CODES ----------------- */
const studentQRCodes = {};
students.forEach((s, i) => studentQRCodes[s] = "QR" + String(i + 1).padStart(3, "0"));

/* ----------------- WEEK RANGE ----------------- */
function getWeekRange() {
  let now = new Date();
  let day = now.getDay();
  let diff = now.getDate() - day + (day === 0 ? -6 : 1);
  let monday = new Date(now.setDate(diff));
  let friday = new Date(monday);
  friday.setDate(monday.getDate() + 4);
  let opt = { month: "short", day: "numeric" };
  return `${monday.toLocaleDateString("en-US", opt)} - ${friday.toLocaleDateString("en-US", opt)}, ${friday.getFullYear()}`;
}
document.getElementById("dateDisplay").innerText = "Date: " + getWeekRange();

/* ----------------- SUBJECT ROW ----------------- */
const subjectRow = document.getElementById("subjectRow");
subjectRow.innerHTML = "";
for (let d = 0; d < 5; d++) {
  subs.forEach(s => {
    subjectRow.innerHTML += `<th>${s[0]}<div style="font-size:9px">${s[1]}</div></th>`;
  });
}

/* ----------------- LOGIN ----------------- */
role.onchange = () => adminPass.style.display = role.value === "Student" ? "none" : "block";

function loginUser() {
  roleType = role.value;
  let u = user.value.toLowerCase().trim();
  let p = pass.value.trim();

  if (roleType === "Student") {
    if (!users[u] || users[u] !== p) return alert("Invalid student login");
    loggedStudent = u.toUpperCase();
    login.style.display = "none";
    qrAuthInterface.style.display = "block";
  } else {
    if (adminPass.value !== adminPassword) return alert("Invalid admin password");
    adminReset.style.display = "inline-block";
    login.style.display = "none";
    main.style.display = "block";
    loadTable();
    setInterval(updateClock, 1000);
    syncFirestore();
  }
}

/* ----------------- QR SCANNER ----------------- */
function startQRScanner() {
  const status = document.getElementById("qrStatus");
  status.style.color = "black";
  status.textContent = "Accessing camera...";

  if (html5QrCode) html5QrCode.stop().then(() => {}).catch(() => {});

  html5QrCode = new Html5Qrcode("qrVideo");
  const config = { fps: 10, qrbox: 250 };

  Html5Qrcode.getCameras().then(devices => {
    if (!devices || devices.length === 0) {
      status.style.color = "red";
      status.textContent = "No camera found!";
      return;
    }

    let cameraId = devices.find(d => d.label.toLowerCase().includes("back"))?.id || devices[0].id;

    html5QrCode.start(
      cameraId,
      config,
      (decodedText) => {
        if (decodedText === studentQRCodes[loggedStudent]) {
          html5QrCode.stop().then(() => {
            status.style.color = "green";
            status.textContent = "QR Verified! Loading attendance...";
            setTimeout(() => {
              qrAuthInterface.style.display = "none";
              main.style.display = "block";
              studentAction.style.display = "block";
              loadTable();
              setInterval(updateClock, 1000);
              syncFirestore();
            }, 500);
          });
        } else {
          status.style.color = "red";
          status.textContent = "Invalid QR code";
        }
      },
      (errorMessage) => {}
    ).catch(err => {
      status.style.color = "red";
      status.textContent = "Camera access denied or not available";
    });
  }).catch(err => {
    status.style.color = "red";
    status.textContent = "Unable to access cameras";
  });
}

// Auto-start scanner when QR interface shows
const observer = new MutationObserver(mutations => {
  mutations.forEach(m => {
    if (m.target.style.display === "block") startQRScanner();
  });
});
observer.observe(document.getElementById("qrAuthInterface"), { attributes: true, attributeFilter: ["style"] });

function cancelQR() {
  if (html5QrCode) html5QrCode.stop().then(() => {}).catch(() => {});
  qrAuthInterface.style.display = "none";
  login.style.display = "block";
}

/* ----------------- AUTO LOGOUT ----------------- */
let logoutTimer;
function resetLogoutTimer() {
  clearTimeout(logoutTimer);
  logoutTimer = setTimeout(() => { alert("Session expired."); location.reload(); }, 600000);
}
document.addEventListener("click", resetLogoutTimer);
document.addEventListener("keypress", resetLogoutTimer);

/* ----------------- TABLE FUNCTIONS ----------------- */
const tbody = document.getElementById("tbody");

function loadTable() {
  tbody.innerHTML = "";
  students.forEach((s, i) => {
    let tr = document.createElement("tr");
    tr.innerHTML = `<td class="sticky">${i + 1}</td><td class="sticky name">${s}</td>`;
    for (let d = 0; d < 35; d++) {
      let td = document.createElement("td");
      if (roleType !== "Student") td.onclick = () => { cycle(td, tr); saveToFirestore(); };
      tr.appendChild(td);
    }
    let summary = document.createElement("td");
    summary.className = "summary-col";
    tr.appendChild(summary);
    tbody.appendChild(tr);
  });
  updateAllSummaries();
}

/* ----------------- SEARCH ----------------- */
document.addEventListener("input", function (e) {
  if (e.target.id === "searchInput") {
    let value = e.target.value.toLowerCase();
    [...tbody.rows].forEach(row => {
      let name = row.cells[1].textContent.toLowerCase();
      row.style.display = name.includes(value) ? "" : "none";
    });
  }
});

/* ----------------- CYCLE MARKS ----------------- */
function cycle(td, row) {
  const states = ["", "✔", "T", "C", "A", "E"];
  let i = states.indexOf(td.textContent);
  td.textContent = states[(i + 1) % 5];
  td.className =
    td.textContent === "✔" ? "P" :
    td.textContent === "T" ? "T" :
    td.textContent === "C" ? "C" :
    td.textContent === "A" ? "A" : "";
  updateRowSummary(row);
}

/* ----------------- MARK PRESENT ----------------- */
function markPresent() {
  let now = new Date();
  let minutes = now.getHours() * 60 + now.getMinutes();
  let subjectIndex = -1;

  subs.forEach((s, i) => {
    let [h, m] = s[1].split(":");
    let start = parseInt(h) * 60 + parseInt(m);
    if (minutes >= start && minutes <= start + 60) subjectIndex = i;
  });

  if (subjectIndex < 0) return alert("Not within class time");

  let dayIndex = now.getDay() - 1;
  if (dayIndex < 0 || dayIndex > 4) return alert("Not school day");

  let row = [...tbody.rows].find(r => r.cells[1].textContent === loggedStudent);
  let col = 2 + subjectIndex + (dayIndex * 7);
  let td = row.cells[col];
  if (td.textContent !== "") return alert("Already marked");

  let [h, m] = subs[subjectIndex][1].split(":");
  let start = parseInt(h) * 60 + parseInt(m);
  let diff = minutes - start;

  if (diff <= 5) { td.textContent = "✔"; td.className = "P"; }
  else if (diff <= 60) { 
    td.textContent = "T"; td.className = "T"; 
    if (!tardyMinutesData[loggedStudent]) tardyMinutesData[loggedStudent] = 0;
    tardyMinutesData[loggedStudent] += diff;
  } else { td.textContent = "C"; td.className = "C"; }

  updateRowSummary(row);
  saveToFirestore();
}

/* ----------------- SUMMARY ----------------- */
function updateRowSummary(row) {
  let present = 0, tardy = 0, cutting = 0, absent = 0, excused = 0;
  for (let i = 2; i < row.cells.length - 1; i++) {
    let val = row.cells[i].textContent;
    if (val === "✔") present++;
    else if (val === "T") tardy++;
    else if (val === "C") cutting++;
    else if (val === "A") absent++;
    else if (val === "E") excused++;
  }
  let name = row.cells[1].textContent;
  let mins = tardyMinutesData[name] || 0;
  row.cells[row.cells.length - 1].innerHTML = `✔ ${present} | T ${tardy} (${mins}m) | C ${cutting} | A ${absent}`;
}

function updateAllSummaries() { [...tbody.rows].forEach(row => updateRowSummary(row)); }

/* ----------------- CLOCK ----------------- */
function updateClock() {
  let now = new Date();
  timeNow.innerText = `Time: ${now.toLocaleTimeString()}`;
}

/* ----------------- FIRESTORE SAVE & SYNC ----------------- */
function saveToFirestore() {
  let data = [];
  [...tbody.rows].forEach(row => {
    let rowData = [];
    for (let i = 2; i < row.cells.length - 1; i++) rowData.push(row.cells[i].textContent);
    data.push(rowData);
  });
  saveToDatabase(data, tardyMinutesData);
}

function syncFirestore() {
  listenToDatabase((data) => {
    if (!data) return;
    let table = data.table || [];
    tardyMinutesData = data.tardy || {};
    [...tbody.rows].forEach((row, r) => {
      if (!table[r]) return;
      table[r].forEach((cell, c) => {
        let td = row.cells[c + 2];
        td.textContent = cell;
        td.className =
          cell === "✔" ? "P" :
          cell === "T" ? "T" :
          cell === "C" ? "C" :
          cell === "A" ? "A" :
          cell === "E" ? "E" : "";
      });
    });
    updateAllSummaries();
  });
}
</script>
</body>
</html>
