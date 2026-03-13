<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Page Indonesia</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins&display=swap" rel="stylesheet">

<style>

body{
font-family:'Poppins',sans-serif;
margin:0;
background:linear-gradient(135deg,#3498db,#8e44ad);
color:white;
text-align:center;
}

h1{
margin-top:40px;
}

.menu{
display:flex;
justify-content:center;
gap:40px;
margin:60px 0;
}

.kotak{
width:200px;
height:150px;
background:white;
color:#333;
display:flex;
align-items:center;
justify-content:center;
border-radius:20px;
font-size:22px;
font-weight:bold;
cursor:pointer;
box-shadow:0 6px 15px rgba(0,0,0,0.3);
}

.card{
background:white;
color:#333;
width:450px;
margin:30px auto;
padding:30px;
border-radius:15px;
display:none;
box-shadow:0 6px 20px rgba(0,0,0,0.3);
}

input{
width:90%;
padding:12px;
margin:10px 0;
border-radius:8px;
border:1px solid #ccc;
}

button{
padding:10px 20px;
border:none;
border-radius:8px;
background:#3498db;
color:white;
cursor:pointer;
}

table{
width:100%;
border-collapse:collapse;
margin-top:10px;
}

th{
background:#3498db;
color:white;
}

th,td{
padding:10px;
border-bottom:1px solid #ddd;
}

.profile{
width:90px;
height:90px;
border-radius:50%;
object-fit:cover;
border:3px solid #3498db;
margin-bottom:10px;
}

.dashboard{
display:grid;
grid-template-columns:repeat(3,1fr);
gap:15px;
margin:20px 0;
}

.box{
background:linear-gradient(135deg,#3498db,#2980b9);
color:white;
padding:15px;
border-radius:10px;
}

</style>
</head>

<body>

<h1>Page Indonesia</h1>

<div class="menu">
<div class="kotak" onclick="buka('login')">Login</div>
<div class="kotak" onclick="buka('register')">Register</div>
</div>

<div id="login" class="card">
<h2>Login</h2>
<input id="luser" placeholder="Username">
<input id="lpass" type="password" placeholder="Password">
<button onclick="login()">Masuk</button>
</div>

<div id="register" class="card">
<h2>Register</h2>
<input id="ruser" placeholder="Username">
<input id="rpass" type="password" placeholder="Password (5 digit)">
<button onclick="register()">Daftar</button>
</div>

<div id="member" class="card">

<img id="fotoMember" class="profile" src="https://i.imgur.com/2DhmtJ4.png">
<br>
<input type="file" onchange="uploadFoto(event,'member')">

<h2>Dashboard Member</h2>
<button onclick="logout()">Logout</button>

<p>Selamat datang <b id="nama"></b></p>

<h3>Saldo</h3>
<h2>Rp <span id="saldo">0</span></h2>

<button onclick="deposit()">Deposit Rp300000</button>

<h3>Riwayat Deposit</h3>

<table>
<tr>
<th>Jumlah</th>
<th>Status</th>
</tr>

<tbody id="riwayat"></tbody>
</table>

</div>

<div id="adminpanel" class="card">

<img id="fotoAdmin" class="profile" src="https://i.imgur.com/2DhmtJ4.png">
<br>
<input type="file" onchange="uploadFoto(event,'admin')">

<h2>Admin Dashboard</h2>
<button onclick="logout()">Logout</button>

<div class="dashboard">

<div class="box">
<h3>Total Member</h3>
<p id="totalMember">0</p>
</div>

<div class="box">
<h3>Total Deposit</h3>
<p id="totalDeposit">0</p>
</div>

<div class="box">
<h3>Deposit Menunggu</h3>
<p id="depositPending">0</p>
</div>

</div>

<h3>Daftar Member</h3>

<table>
<tr>
<th>User</th>
<th>Saldo</th>
<th>Aksi</th>
</tr>

<tbody id="memberlist"></tbody>
</table>

<h3>Deposit</h3>

<table>
<tr>
<th>User</th>
<th>Jumlah</th>
<th>Status</th>
<th>Aksi</th>
</tr>

<tbody id="depositlist"></tbody>
</table>

<h3>Ubah Nomor DANA</h3>

<input id="danabaru" placeholder="Nomor DANA baru">
<button onclick="ubahDana()">Ubah</button>

</div>

<script>

let members = JSON.parse(localStorage.getItem("members")) || []
let deposits = JSON.parse(localStorage.getItem("deposits")) || []
let currentUser = null
let dana = localStorage.getItem("dana") || "085136173514"

function simpan(){
localStorage.setItem("members",JSON.stringify(members))
localStorage.setItem("deposits",JSON.stringify(deposits))
localStorage.setItem("dana",dana)
}

function buka(id){

document.querySelectorAll(".card").forEach(x=>x.style.display="none")
document.getElementById(id).style.display="block"

if(id=="member" || id=="adminpanel"){
document.querySelector(".menu").style.display="none"
}else{
document.querySelector(".menu").style.display="flex"
}

}

function register(){

let user=ruser.value
let pass=rpass.value

if(pass.length!=5){
alert("Password harus 5 digit")
return
}

members.push({user,pass,saldo:0})

simpan()

alert("Register berhasil")

}

function login(){

let user=luser.value
let pass=lpass.value

if(user=="nagha.kancil@gmail.com" && pass=="12345"){

let fa=localStorage.getItem("fotoAdmin")
if(fa){
document.getElementById("fotoAdmin").src=fa
}

buka("adminpanel")
loadAdmin()
return
}

let m=members.find(x=>x.user==user && x.pass==pass)

if(m){
currentUser=m
nama.innerText=m.user
saldo.innerText=m.saldo

let fm=localStorage.getItem("fotoMember")
if(fm){
document.getElementById("fotoMember").src=fm
}

buka("member")
loadRiwayat()
}else{
alert("Login gagal")
}

}

function logout(){
location.reload()
}

function deposit(){

deposits.push({
user:currentUser.user,
jumlah:300000,
status:"Menunggu"
})

simpan()

alert("Transfer ke DANA "+dana)

loadRiwayat()

}

function loadRiwayat(){

riwayat.innerHTML=""

deposits.filter(x=>x.user==currentUser.user)
.forEach(x=>{

riwayat.innerHTML+=`
<tr>
<td>Rp ${x.jumlah}</td>
<td>${x.status}</td>
</tr>
`

})

}

function loadAdmin(){

totalMember.innerText=members.length

let total=deposits.reduce((a,b)=>a+b.jumlah,0)
totalDeposit.innerText="Rp "+total

depositPending.innerText=deposits.filter(x=>x.status=="Menunggu").length

memberlist.innerHTML=""

members.forEach((x,i)=>{

memberlist.innerHTML+=`
<tr>
<td>${x.user}</td>
<td>${x.saldo}</td>
<td>
<button onclick="hapusMember(${i})">Delete</button>
</td>
</tr>
`

})

depositlist.innerHTML=""

deposits.forEach((x,i)=>{

depositlist.innerHTML+=`
<tr>
<td>${x.user}</td>
<td>Rp ${x.jumlah}</td>
<td>${x.status}</td>
<td>
${x.status=="Menunggu"?`<button onclick="konfirmasi(${i})">Konfirmasi</button>`:""}
</td>
</tr>
`

})

}

function hapusMember(i){

if(confirm("Hapus member ini?")){

members.splice(i,1)

simpan()

loadAdmin()

}

}

function konfirmasi(i){

deposits[i].status="Berhasil"

let m=members.find(x=>x.user==deposits[i].user)

m.saldo+=deposits[i].jumlah

simpan()

loadAdmin()

}

function ubahDana(){

dana=danabaru.value

simpan()

alert("Nomor DANA diubah")

}

function uploadFoto(e,t){

let r=new FileReader()

r.onload=function(){

if(t=="member"){
fotoMember.src=r.result
localStorage.setItem("fotoMember",r.result)
}

if(t=="admin"){
fotoAdmin.src=r.result
localStorage.setItem("fotoAdmin",r.result)
}

}

r.readAsDataURL(e.target.files[0])

}

window.onload=function(){

let fm=localStorage.getItem("fotoMember")
let fa=localStorage.getItem("fotoAdmin")

if(fm){
document.getElementById("fotoMember").src=fm
}

if(fa){
document.getElementById("fotoAdmin").src=fa
}

}

</script>

</body>
</html>
