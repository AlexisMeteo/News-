<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Météo Lombard 25 & Astro & News</title>
<style>
/* ... ici tu gardes tout ton CSS existant ... */
</style>
</head>
<body>
<div class="container">
  <h1>
    Météo Lombard (Doubs, 25) & Astro & News
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
    <div id="news">Chargement des news...</div>
  </div>
</div>

<script>
// =================== METEO & ASTRO ===================
// ... ici tu gardes tout ton JS existant pour météo & astro ...

// =================== NEWS via PHP ===================
document.getElementById("btnNews").addEventListener("click",()=>{
  document.getElementById("newsPage").classList.remove("hidden");
  document.getElementById("meteoPage").classList.add("hidden");
  document.getElementById("astroPage").classList.add("hidden");
  document.getElementById("btnNews").classList.add("active");
  document.getElementById("btnMeteo").classList.remove("active");
  document.getElementById("btnAstro").classList.remove("active");

  fetch("news.php") // <-- ton fichier PHP TheNewsAPI
    .then(res=>res.json())
    .then(data=>{
      if(data.error){
        document.getElementById("news").innerText = "Erreur : " + data.error;
      } else {
        let html = "";
        data.data.slice(0,5).forEach(article=>{
          html += `<div class="astro-card">
                      <h2><a href="${article.url}" target="_blank">${article.title}</a></h2>
                      <p>${article.description || ""}</p>
                   </div>`;
        });
        document.getElementById("news").innerHTML = html;
      }
    })
    .catch(err=>{
      document.getElementById("news").innerText = "Impossible de récupérer les news.";
      console.error(err);
    });
});
</script>
</body>
</html>
