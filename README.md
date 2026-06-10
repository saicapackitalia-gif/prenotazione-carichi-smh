<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Prenotazione Carichi</title>

<style>
body {font-family: Arial; margin:0; background:#f4f4f4;}
h1 {background:#0078D4; color:white; padding:15px; text-align:center;}

.container {padding:10px;}

input {
  width:100%;
  padding:12px;
  margin:5px 0;
  font-size:16px;
}

.grid {
  display:grid;
  grid-template-columns:70px 1fr 1fr;
}

.slot {
  padding:12px;
  border:1px solid #ccc;
  text-align:center;
  font-size:13px;
}

.free {background:#c8f7c5;}
.busy {background:#f7c5c5;}

.bigbtn {
  padding:15px;
  width:100%;
  margin-top:10px;
  background:green;
  color:white;
  border:none;
  font-size:16px;
}
</style>
</head>
<body>

<h1>Prenotazione Carichi</h1>

<div class="container">

<input id="company" placeholder="Azienda">
<input id="truck" placeholder="Targa camion">
<input type="date" id="date">

<button class="bigbtn" onclick="load()">Carica Giorno</button>

<div class="grid" id="grid"></div>

</div>

<script>

// ✅ SALVATAGGIO LOCALE (NON PERDE DATI)
let bookings = JSON.parse(localStorage.getItem("bookings") || "[]");

function save(){
  localStorage.setItem("bookings", JSON.stringify(bookings));
}

function isWeekend(d){
  let day = new Date(d).getDay();
  return day==0 || day==6;
}

function generate(){
  grid.innerHTML="";
  grid.innerHTML += "<div>Ora</div><div>Fogli</div><div>Scatole</div>";

  let h=6,m=0;

  while(true){
    let time = h.toString().padStart(2,'0')+":"+m.toString().padStart(2,'0');

    grid.innerHTML += `<div class='slot'>${time}</div>`;

    ["Fogli","Scatole"].forEach(zone=>{
      let cell = document.createElement("div");
      cell.className="slot free";

      // controllo se occupato
      let b = bookings.find(x =>
        x.date==date.value &&
        x.time==time &&
        x.zone==zone
      );

      if(b){
        cell.classList.remove("free");
        cell.classList.add("busy");
        cell.innerHTML = b.company+"<br>"+b.truck;
      } else {
        cell.innerText="LIBERO";
        cell.onclick=()=>prenota(time,zone);
      }

      grid.appendChild(cell);
    });

    m+=30;
    if(m==60){m=0;h++;}
    if(h==20) break;
  }
}

function load(){
  let d = date.value;

  if(!d){ alert("Seleziona data"); return; }
  if(isWeekend(d)){ alert("Weekend chiuso"); return; }

  generate();
}

function prenota(time,zone){

  let d = date.value;
  let companyVal = company.value;
  let truckVal = truck.value;

  if(!companyVal){ alert("Inserisci azienda"); return; }

  bookings.push({
    date:d,
    time:time,
    zone:zone,
    company:companyVal,
    truck:truckVal
  });

  save();
  generate();
}

</script>

</body>
</html># prenotazione-carichi-smh
prenotare carichi a smh
