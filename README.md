<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Météo Lombard & News</title>
<style>
body { font-family: Arial, sans-serif; background: #f0f4f8; margin:0; padding:20px; display:flex; justify-content:center; align-items:flex-start; }
.container { background:white; padding:30px 40px; border-radius:15px; box-shadow:0 6px 20px rgba(0,0,0,0.15); max-width:1000px; width:95%; }
h1 { color:#0077b6; font-size:28px; margin-bottom:15px; text-align:center; display:flex; justify-content:space-between; align-items:center; }
.nav { display:flex; justify-content:center; margin-bottom:20px; }
.nav button { padding:10px 20px; margin:0 5px; border:none; border-radius:8px; background:#0077b6; color:white; font-size:16px; cursor:pointer; }
.nav button.active { background:#023e8a; }
.jour { margin-bottom:40px; position:relative; }
.jour h2 { color:#023e8a; margin-bottom:15px; font-size:22px; display:inline-block; margin-right:10px; }
.btnJour { background:#0077b6; color:white; border:none; padding:5px 10px; border-radius:8px; cursor:pointer; font-size:16px; vertical-align:middle; }
.btnJour:hover { background:#005f87; }
.table-meteo { display:flex; justify-content:flex-start; flex-wrap:nowrap; overflow-x:auto; border-top:1px solid #ccc; border-bottom:1px solid #ccc; padding:15px 0; gap:15px; }
.heure { text-align:center; flex:0 0 70px; border-radius:10px; padding:5px 0; color:#fff; font-weight:bold; transition: transform 0.2s; position:relative; overflow:hidden; }
.heure img { width:50px; height:50px; margin-bottom:5px; display:block; margin-left:auto; margin-right:auto; }
.heure div { font-size:16px; }
.heure:hover { transform: scale(1.1); }
.soleil { animation: pulse 2s infinite; }
@keyframes pulse { 0%,100%{transform:scale(1);} 50%{transform:scale(1.2);} }
.nuage { animation: flotter 4s infinite alternate; }
@keyframes flotter { 0%{transform:translateY(0);} 50%{transform:translateY(4px);} 100%{transform:translateY(0);} }
.pluie::after { content:""; position:absolute; top:0; left:50%; width:2px; height:40px; background:#00f; animation:pluieTombe 1s linear infinite; opacity:0.6; transform:translateX(-50%); }
@keyframes pluieTombe { 0%{top:0;opacity:0.6;}50%{top:20px;opacity:1;}100%{top:40px;opacity:0.6;} }
.news { margin-bottom:15px; padding:10px; border-radius:10px; box-shadow:0 3px 10px rgba(0,0,0,0.1); background:#fff; }
.news h3 { margin:0 0 5px; font-size:18px; color:#0077b6; }
.news p { margin:0; font-size:14px; color:#333; }
.hidden { display:none; }
</style>
</head>
<body>
<div class="container">
<h1>
  Météo Lombard & News
  <span id="heureTemp" style="font-size:18px;color:#023e8a;"></span>
</h1>

<div class="nav">
  <button id="btnMeteo" class="active">🌦 Météo</button>
  <button id="btnNews">📰 News</button>
</div>

<div id="meteoPage">
  <div id="meteo">Chargement...</div>
</div>

<div id="newsPage" class="hidden">
  <div id="news">Chargement des news...</div>
</div>
</div>

<script>
const apiKey="94cda3c9bb1b4bd2855a9bf818f7d30a"; // OpenWeather
const ville="Lombard,FR";
const urlForecast=`https://api.openweathermap.org/data/2.5/forecast?q=${ville}&units=metric&lang=fr&appid=${apiKey}`;
const urlCurrent=`https://api.openweathermap.org/data/2.5/weather?q=${ville}&units=metric&lang=fr&appid=${apiKey}`;
const newsKey="acXITtzQSfv8gtDnwgJPpkaNYQBXt8VLey42PHJ8"; // Mets ta clé NewsAPI ici
const urlNews=`https://newsapi.org/v2/top-headlines?country=fr&pageSize=5&apiKey=${newsKey}`;
const isIOS=/iPad|iPhone|iPod/.test(navigator.userAgent);

function couleurTemperature(temp){if(temp<=0)return'#00bfff';if(temp<=10)return'#1e90ff';if(temp<=20)return'#3cb371';if(temp<=25)return'#ffa500';return'#ff4500';}
function couleurSoleil(heure){if(heure>=5 && heure<8) return '#FF4500'; if(heure>=8 && heure<17) return '#FFD700'; if(heure>=17 && heure<=22) return '#FF8C00'; return '#1E1E2F';}
function classeIcone(desc,icon){if(icon.includes('01'))return'soleil';if(icon.includes('02')||icon.includes('03')||icon.includes('04'))return'nuage';if(icon.includes('09')||icon.includes('10')||icon.includes('11'))return'pluie';return'';}
function updateHeureTemp(temp){const now=new Date();const h=now.getHours().toString().padStart(2,'0');const m=now.getMinutes().toString().padStart(2,'0');document.getElementById('heureTemp').innerText=`${h}:${m} - ${temp}°C`;}

// METEO COURANTE
fetch(urlCurrent).then(res=>res.json()).then(data=>{updateHeureTemp(Math.round(data.main.temp));});

// METEO FORECAST
fetch(urlForecast).then(res=>res.json()).then(data=>{
  const jours={};
  data.list.forEach(item=>{
    const date=new Date(item.dt*1000);
    const jour=date.toLocaleDateString("fr-FR",{ weekday:'long',day:'numeric',month:'long' });
    if(!jours[jour])jours[jour]=[];
    jours[jour].push({
      heure:date.getHours(),
      temp:Math.round(item.main.temp),
      desc:item.weather[0].description,
      icon:`https://openweathermap.org/img/wn/${item.weather[0].icon}.png`,
      codeIcon:item.weather[0].icon
    });
  });
  const joursCles=Object.keys(jours).slice(0,2);
  let html="";
  joursCles.forEach((jour,index)=>{
    html+=`<div class="jour"><h2>${jour}</h2>`;
    if(isIOS){html+=`<button class="btnJour" id="btnJour${index}">🔊</button>`;}
    html+=`<div class="table-meteo">`;
    jours[jour].forEach(h=>{
      const classe=classeIcone(h.desc,h.codeIcon);
      const bgColor=h.codeIcon.includes('01') ? couleurSoleil(h.heure) : couleurTemperature(h.temp);
      html+=`<div class="heure" style="background:${bgColor}"><div>${h.heure}h</div><img src="${h.icon}" alt="${h.desc}" title="${h.desc}" class="${classe}"><div>${h.temp}°C</div></div>`;
    });
    html+=`</div></div>`;
  });
  document.getElementById("meteo").innerHTML=html;
  const texteJours=joursCles.map(jour=>`Météo pour ${jour} : `+jours[jour].map(h=>`${h.heure}h, ${h.temp}°C, ${h.desc}`).join(", "));
  function lireTexte(texte){const synth=window.speechSynthesis;const utter=new SpeechSynthesisUtterance(texte);utter.lang="fr-FR";synth.speak(utter);}
  if(isIOS){joursCles.forEach((_,index)=>{document.getElementById(`btnJour${index}`).addEventListener("click",()=>lireTexte(texteJours[index]));});} else {lireTexte(texteJours.join(". "));}
}).catch(err=>{document.getElementById("meteo").innerText="Impossible de récupérer la météo.";console.error(err);});

// NEWS
function loadNews(){
  fetch(urlNews).then(res=>res.json()).then(data=>{
    let html="";
    data.articles.forEach(a=>{
      html+=`<div class="news"><h3>${a.title}</h3><p>${a.description||''}</p></div>`;
    });
    document.getElementById("news").innerHTML=html;
  }).catch(err=>{document.getElementById("news").innerText="Impossible de récupérer les news.";});
}

// NAVIGATION
document.getElementById("btnMeteo").addEventListener("click",()=>{
  document.getElementById("meteoPage").classList.remove("hidden");
  document.getElementById("newsPage").classList.add("hidden");
  document.getElementById("btnMeteo").classList.add("active");
  document.getElementById("btnNews").classList.remove("active");
});
document.getElementById("btnNews").addEventListener("click",()=>{
  document.getElementById("newsPage").classList.remove("hidden");
  document.getElementById("meteoPage").classList.add("hidden");
  document.getElementById("btnNews").classList.add("active");
  document.getElementById("btnMeteo").classList.remove("active");
  loadNews();
});
</script>
</body>
</html>

