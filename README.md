// NEWS via NewsAPI avec rafraîchissement automatique
const newsApiKey = "fa8b23b2b6ac4d009c1cb31ad933290a";
const newsUrl = `https://newsapi.org/v2/top-headlines?country=fr&apiKey=${newsApiKey}`;
const importantKeywords = ["France", "Économie", "Politique", "International", "Santé", "Éducation"]; // mots-clés prioritaires

function chargerNews() {
  fetch(newsUrl)
    .then(res => res.json())
    .then(data => {
      const container = document.getElementById("news");
      container.innerHTML = "";

      // Séparer les articles importants des secondaires
      const importants = [];
      const secondaires = [];
      data.articles.forEach(article => {
        const texte = `${article.title} ${article.description || ""}`.toLowerCase();
        if (importantKeywords.some(k => texte.includes(k.toLowerCase()))) {
          importants.push(article);
        } else {
          secondaires.push(article);
        }
      });

      // Concaténer : importants d'abord
      const articlesTries = importants.concat(secondaires);

      articlesTries.forEach(article => {
        const div = document.createElement("div");
        div.className = "news-card";
        div.innerHTML = `<a href="${article.url}" target="_blank">${article.title}</a><p>${article.description || ""}</p>`;
        container.appendChild(div);
      });
    })
    .catch(err => {
      document.getElementById("news").innerText = "Impossible de charger les actualités.";
      console.error(err);
    });
}

// Charger dès le départ
chargerNews();

// Rafraîchissement toutes les 30 minutes
setInterval(chargerNews, 30 * 60 * 1000); // 1800000 ms
