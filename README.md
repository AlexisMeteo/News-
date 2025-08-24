<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Météo Lombard 25 • Astro • News</title>
<style>
body { font-family: Arial, sans-serif; background: #f0f4f8; margin: 0; padding: 20px; display: flex; justify-content: center; align-items: flex-start; }
.container { background: white; padding: 30px 40px; border-radius: 15px; box-shadow: 0px 6px 20px rgba(0,0,0,0.15); max-width: 1000px; width: 95%; }
h1 { color: #0077b6; font-size: 28px; margin-bottom: 15px; text-align: center; display:flex; justify-content:space-between; align-items:center; }

.nav {display:flex;justify-content:center;margin-bottom:20px;}
.nav button {padding:10px 20px;margin:0 5px;border:none;border-radius:8px;background:#0077b6;color:white;font-size:16px;cursor:pointer;}
.nav button.active {background:#023e8a;}

.jour { margin-bottom: 40px; position: relative; }
.jour h2 { color: #023e8a; margin-bottom: 15px; font-size: 22px; display: inline-block; margin-right: 10px; }
.btnJour { background: #0077b6; color: white; border: none; padding: 5px 10px; border-radius: 8px; cursor: pointer; font-size: 16px; vertical-align: middle; }
.btnJour:hover { background: #005f87; }

.table-meteo { display: flex; justify-content: flex-start; flex-wrap: nowrap; overflow-x: auto; border-top: 1px solid #ccc; border-bottom: 1px solid #ccc; padding: 15px 0; gap: 15px; }
.heure { text-align: center; flex: 0 0 70px; border-radius: 10px; padding: 5px 0; color: #fff; font-weight: bold; transition: transform 0.2s; position: relative; overflow: hidden; }
.heure img { width: 50px; height: 50px; margin-bottom: 5px; display: block; margin-left: auto; margin-right: auto; }
.heure div { font-size: 16px; }
.heure:hover { transform: scale(1.1); }

.astro-card{background:white;padding:15px;border-radius:10px;box-shadow:0 4px 12px rgba(0,0,0,0.1);margin-bottom:15px;}
.astro-card h2{margin:0 0 10px;font-size:18px;color:#0077b6;display:flex;justify-content:space-between;align-items:center;}
.astro-card button{background:#0077b6;color:white;border:none;padding:5px 10px;border-radius:5px;cursor:pointer;}
.hidden{display:none;}

.news-card{background:#f9f9f9;padding:15px;margin-bottom:10px;border-left:5px solid #0077b6;border-radius:8px;}
.news-card a{font-weight:bold;color:#0077b6;text-decoration:none;}
.news-card a:hover{text-decoration:underline;}
</style>
</head>
<body>
<div class="container">
  <h1>
    Météo Lombard (Doubs, 25) • Astro • News
    <span id="heureTemp" style="font-size:18px;color:#023e8a;"></span>
  </h1>

  <div class="nav">
    <button id="btnMeteo" class="active">🌦 Météo</button>
    <button id="btnAstro">✨ Astro</button>
    <button id="btnNews">📰 News</button>
  </div>

  <!-- METEO -->
  <div id="meteoPage">
    <div id="meteo">Chargement...</div>
  </div>

  <!-- ASTRO -->
  <div id="astroPage" class="hidden">
    <div class="astro-card" id="belier"><h2>Bélier <button onclick="lireAstro('aries')">🔊</button></h2><p>Chargement...</p></div>
    <div class="astro-card" id="gemeaux"><h2>Gémeaux <button onclick="lireAstro('gemini')">🔊</button></h2><p>Chargement...</p></div>
    <div class="astro-card" id="verseau"><h2>Verseau <button onclick="lireAstro('aquarius')">🔊</button></h2><p>Chargement...</p></div>
    <div class="astro-card" id="balance"><h2>Balance <button onclick="lireAstro('libra')">🔊</button></h2><p>Chargement...</p></div>
  </div>

  <!-- NEWS -->
  <div id="newsPage" class="hidden">
    <h2>📰 Actualités</h2>
    <div id="news">Chargement des actualités...</div>
  </div>
</div>

<script>
const apiKey="94cda3c9bb1b4bd2855a9bf818f7d30a"; 
const ville="Lombard,FR";
const urlForecast=`https://api.openweathermap.org/data/2.5/forecast?q=${ville}&units=metric&lang=fr&appid=${apiKey}`;
const urlCurrent=`https://api.openweathermap.org/data/2.5/weather?q=${ville}&units=metric&lang=fr&appid=${apiKey}`;
const isIOS=/iPad|iPhone|iPod/.test(navigator.userAgent);

// Couleurs
function couleurTemperature(temp){if(temp<=0)return'#00bfff';if(temp<=10)return'#1e90ff';if(temp<=20)return'#3cb371';if(temp<=25)return'#ffa500';return'#ff4500';}
function couleurSoleil(heure){if(heure>=5 && heure<8) return '#FF4500';if(heure>=8 && heure<17) return '#FFD700';if(heure>=17 && heure<22) return '#FF8C00';return '#1e90ff';}
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
    jours[jour].push({heure:date.getHours(),temp:Math.round(item.main.temp),desc:item.weather[0].description,icon:`https://openweathermap.org/img/wn/${item.weather[0].icon}.png`,codeIcon:item.weather[0].icon});
  });
  const joursCles=Object.keys(jours).slice(0,2);
  let html="";
  joursCles.forEach((jour,index)=>{
    html+=`<div class="jour"><h2>${jour}</h2>`;
    if(isIOS){html+=`<button class="btnJour" id="btnJour${index}">🔊</button>`;}
    html+=`<div class="table-meteo">`;
    jours[jour].forEach(h=>{
      const classe=classeIcone(h.desc,h.codeIcon);
      const bgColor=h.codeIcon.includes('01')? couleurSoleil(h.heure) : couleurTemperature(h.temp);
      html+=`<div class="heure" style="background:${bgColor}"><div>${h.heure}h</div><img src="${h.icon}" alt="${h.desc}" title="${h.desc}" class="${classe}"><div>${h.temp}°C</div></div>`;
    });
    html+=`</div></div>`;
  });
  document.getElementById("meteo").innerHTML=html;
});

// NAVIGATION
document.getElementById("btnMeteo").addEventListener("click",()=>{
  document.getElementById("meteoPage").classList.remove("hidden");
  document.getElementById("astroPage").classList.add("hidden");
  document.getElementById("newsPage").classList.add("hidden");
  document.getElementById("btnMeteo").classList.add("active");
  document.getElementById("btnAstro").classList.remove("active");
  document.getElementById("btnNews").classList.remove("active");
});
document.getElementById("btnAstro").addEventListener("click",()=>{
  document.getElementById("astroPage").classList.remove("hidden");
  document.getElementById("meteoPage").classList.add("hidden");
  document.getElementById("newsPage").classList.add("hidden");
  document.getElementById("btnAstro").classList.add("active");
  document.getElementById("btnMeteo").classList.remove("active");
  document.getElementById("btnNews").classList.remove("active");
});
document.getElementById("btnNews").addEventListener("click",()=>{
  document.getElementById("newsPage").classList.remove("hidden");
  document.getElementById("astroPage").classList.add("hidden");
  document.getElementById("meteoPage").classList.add("hidden");
  document.getElementById("btnNews").classList.add("active");
  document.getElementById("btnMeteo").classList.remove("active");
  document.getElementById("btnAstro").classList.remove("active");
});

// ASTRO
const signesAPI = { belier:'aries', gemeaux:'gemini', verseau:'aquarius', balance:'libra' };
function lireAstro(sign){ 
  const texte = document.querySelector(`#${sign} p`).innerText;
  const synth = window.speechSynthesis;
  const utter = new SpeechSynthesisUtterance(texte);
  utter.lang="fr-FR";
  synth.speak(utter);
}
Object.keys(signesAPI).forEach(sign=>{
  fetch(`https://aztro.sameerkumar.website/?sign=${signesAPI[sign]}&day=today`, { method:'POST' })
    .then(res=>res.json())
    .then(data=>{ document.querySelector(`#${sign} p`).innerText = data.description; })
    .catch(err=>{ document.querySelector(`#${sign} p`).innerText="Impossible de récupérer l'horoscope du jour."; });
});

// NEWS
const newsUrl = "https://Alex9.infinityfreeapp.com/proxy.php";
function chargerNews(){
  fetch(newsUrl)
    .then(res => res.json())
    .then(data=>{
      const container=document.getElementById("news");
      container.innerHTML="";
      if(!data.articles){container.innerText="Pas d’articles trouvés.";return;}
      data.articles.forEach(article=>{
        const div=document.createElement("div");
        div.className="news-card";
        div.innerHTML=`<a href="${article.url}" target="_blank">${article.title}</a><p>${article.description || ""}</p>`;
        container.appendChild(div);
      });
    })
    .catch(err=>{
      document.getElementById("news").innerText="Impossible de charger les actualités.";
      console.error(err);
    });
}
chargerNews();
setInterval(chargerNews,30*60*1000);
</script>
</body>
</html>p
