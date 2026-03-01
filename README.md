<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Attendance – Grade 12 Diamond</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
<script src="https://unpkg.com/html5-qrcode"></script>

<style>
:root{--maroon:#7a0c0c}
*{box-sizing:border-box}
body{margin:0;font-family:'Poppins',sans-serif;background:#f2f2f2}
#login{min-height:100vh;display:flex;justify-content:center;align-items:center;
background:linear-gradient(rgba(122,12,12,.85),rgba(122,12,12,.85)),
url("https://images.unsplash.com/photo-1524995997946-a1c2e315a42f");
background-size:cover;background-position:center;padding:15px}
.login-box{background:#fff;width:100%;max-width:380px;padding:25px;border-radius:18px;
box-shadow:0 15px 30px rgba(0,0,0,.35)}
.login-box h2{text-align:center;color:var(--maroon)}
input,select,button{width:100%;padding:12px;margin-top:10px;font-size:14px}
button{background:var(--maroon);color:white;border:none;border-radius:10px;cursor:pointer}
#main{display:none;padding:10px}
header{background:var(--maroon);color:white;padding:14px;text-align:center;border-radius:14px}
.sub{font-size:13px;opacity:.9}
#studentAction{display:none;margin-top:10px;background:white;padding:12px;border-radius:12px;text-align:center}
#timeNow{font-size:13px;margin-bottom:6px}
.table-wrap{overflow-x:auto;background:white;border-radius:14px;margin-top:12px}
table{border-collapse:collapse;width:100%;min-width:1200px}
th,td{border:2px solid #999;padding:6px;text-align:center;font-size:11px;min-width:60px}
th{background:#eee;font-weight:600}
.day-head{background:var(--maroon);color:white}
.sticky{position:sticky;left:0;background:white;z-index:4;font-weight:600}
.name{left:42px;z-index:5;background:white}
.summary-col{background:#f7f7f7;min-width:160px;font-weight:600}
.P{background:#c8f7c5;font-weight:600}
.T{background:#fff3b0;font-weight:600}
.C{background:#ffb3b3;font-weight:600}
.A{background:#e0e0e0;font-weight:600}
#qrAuthInterface{display:none;padding:20px;text-align:center}
#qrVideo{width:100%;max-width:400px;margin:auto}
#qrStatus{color:red;font-size:13px;margin-top:6px}
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

<!-- QR -->
<div id="qrAuthInterface">
  <h2>QR Code Authentication</h2>
  <div id="qrVideo"></div>
  <p id="qrStatus"></p>
  <button onclick="startQRScanner()">Start Scanner</button>
  <button onclick="cancelQR()">Cancel</button>
</div>

<!-- MAIN -->
<div id="main">
<header>
  DAILY ATTENDANCE – GRADE 12 DIAMOND
  <div class="sub">Adviser: January Lyn Alumbres</div>
</header>

<div id="studentAction">
  <div id="timeNow"></div>
  <button onclick="markPresent()">MARK PRESENT</button>
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

<!-- 🔥 FIREBASE + FULL SYSTEM -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.10.0/firebase-app.js";
import { getFirestore, doc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/12.10.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyBU5wgWTIgFetO38U28ikmoLC0CKryR05M",
  authDomain: "talaan-b5c66.firebaseapp.com",
  projectId: "talaan-b5c66",
  storageBucket: "talaan-b5c66.firebasestorage.app",
  messagingSenderId: "725034061224",
  appId: "1:725034061224:web:b2ac35e9e8f0f4c6e8a19d"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

/* ---------------- DATA ---------------- */
const adminPassword="123456789";
const subs=[["CHEM","07:30"],["DRRR","08:20"],["PE","09:30"],["INQ","10:20"],["MIL","13:00"],["PHYS","13:50"],["CAP","14:40"]];
const users={
"adiaton":"01","bacaycay":"02","broto":"03","caramol":"04","comedia":"05",
"cuyag":"06","de la cruz":"07","delmoro":"08","delorino":"09","enano":"10",
"esparto":"11","espinola":"12","etac":"13","florano":"14","herreras":"15",
"jumadiao":"16","loberiano":"17","mangada":"18","paulino":"19","tan":"20",
"velasco":"21","apelo":"22","arceo":"23","arniño":"24","balleta":"25",
"barojabo":"26","bobiles":"27","caro":"28","cornico":"29","de rafael":"30",
"escalante":"31","frigillana":"32","gallano":"33","gremio":"34","hipe":"35",
"imperial":"36","irinco":"37","lee":"38","lim":"39","magdaraog":"40","mangada k":"41",
"meregildo":"42","perez":"43","pulga":"44","ponferrada":"45",
"santos":"46","sidro":"47","sister":"48","teberio":"49","vibar":"50"
};

const students=Object.keys(users).map(n=>n.toUpperCase());
let roleType="", loggedStudent="", tardyMinutesData={}, html5QrCode;

const tbody=document.getElementById("tbody");
const subjectRow=document.getElementById("subjectRow");

/* SUBJECT HEADERS */
for(let d=0;d<5;d++){
  subs.forEach(s=>{
    subjectRow.innerHTML+=`<th>${s[0]}<div style="font-size:9px">${s[1]}</div></th>`;
  });
}

/* WEEK KEY */
function getWeekKey(){
  let now=new Date();
  let day=now.getDay();
  let diff=now.getDate()-day+(day===0?-6:1);
  let monday=new Date(now.setDate(diff));
  return "attendance_"+monday.toISOString().split("T")[0];
}

/* LOGIN */
window.loginUser=function(){
  roleType=role.value;
  let u=user.value.toLowerCase().trim();
  let p=pass.value.trim();
  if(roleType==="Student"){
    if(!users[u]||users[u]!==p) return alert("Invalid login");
    loggedStudent=u.toUpperCase();
    login.style.display="none";
    qrAuthInterface.style.display="block";
  }else{
    if(adminPass.value!==adminPassword) return alert("Wrong admin password");
    login.style.display="none";
    main.style.display="block";
    loadTable();
  }
};
role.onchange=()=>adminPass.style.display=role.value==="Student"?"none":"block";

/* LOAD TABLE */
function loadTable(){
  tbody.innerHTML="";
  students.forEach((s,i)=>{
    let tr=document.createElement("tr");
    tr.innerHTML=`<td class="sticky">${i+1}</td><td class="sticky name">${s}</td>`;
    for(let d=0;d<35;d++){
      let td=document.createElement("td");
      if(roleType!=="Student") td.onclick=()=>cycle(td,tr);
      tr.appendChild(td);
    }
    let summary=document.createElement("td");
    summary.className="summary-col";
    tr.appendChild(summary);
    tbody.appendChild(tr);
  });
  syncFirestore();
}

/* CYCLE */
function cycle(td,row){
  const states=["","✔","T","C","A"];
  let i=states.indexOf(td.textContent);
  td.textContent=states[(i+1)%5];
  td.className= td.textContent==="✔"?"P":td.textContent==="T"?"T":td.textContent==="C"?"C":td.textContent==="A"?"A":"";
  saveToFirestore();
}

/* SAVE */
async function saveToFirestore(){
  let data=[];
  [...tbody.rows].forEach(row=>{
    let rowData=[];
    for(let i=2;i<row.cells.length-1;i++){
      rowData.push(row.cells[i].textContent);
    }
    data.push(rowData);
  });
  await setDoc(doc(db,"attendance",getWeekKey()),{table:data,tardy:tardyMinutesData});
}

/* REALTIME SYNC */
function syncFirestore(){
  onSnapshot(doc(db,"attendance",getWeekKey()),snapshot=>{
    if(!snapshot.exists()) return;
    let data=snapshot.data().table||[];
    tardyMinutesData=snapshot.data().tardy||{};
    [...tbody.rows].forEach((row,r)=>{
      if(!data[r]) return;
      data[r].forEach((cell,c)=>{
        let td=row.cells[c+2];
        td.textContent=cell;
        td.className= cell==="✔"?"P":cell==="T"?"T":cell==="C"?"C":cell==="A"?"A":"";
      });
    });
  });
}
</script>

</body>
</html>
