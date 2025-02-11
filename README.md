<!DOCTYPE html>
<html>
<head>
<title>Vertrau mir Bruder</title>
<style>
body {
  background-color: black;
  color: white;
  font-family: sans-serif;
}
#suchfeld {
  width: 500px;
  padding: 10px;
  font-size: 16px;
}
#artikel {
  margin-top: 20px;
}
.result {
  margin-bottom: 10px;
}
.result-title {
  font-weight: bold;
  color: white; /* Titel weiß */
  cursor: pointer;
  text-decoration: none;
}
#artikel a {
  color: white; /* Links im Artikel weiß */
  pointer-events: none; /* Deaktiviert das Anklicken der Links */
  text-decoration: none;
}
</style>
</head>
<body>

<h1>Vertrau mir Bruder</h1>

<input type="text" id="suchfeld" placeholder="Suchbegriff eingeben">
<button onclick="wikipediaSuche()">Suchen</button>
  

<div id="artikel"></div>

    </h2>Nur Zuverlässige Quellen</h2>
<script>
function wikipediaSuche() {
  const suchbegriff = document.getElementById("suchfeld").value;
  const artikelDiv = document.getElementById("artikel");

  artikelDiv.innerHTML = "Suche wird ausgeführt...";

  const apiUrl = https://de.wikipedia.org/w/api.php?action=query&list=search&srsearch=${suchbegriff}&format=json&origin=*;

  fetch(apiUrl)
    .then(response => {
      if (!response.ok) {
        throw new Error(HTTP Fehler! Status: ${response.status});
      }
      return response.json();
    })
    .then(data => {
      if (data.query && data.query.search && data.query.search.length > 0) {
        const searchResults = data.query.search;
        artikelDiv.innerHTML = ""; // Clear loading message

        searchResults.forEach(result => {
          const resultDiv = document.createElement("div");
          resultDiv.classList.add("result");

          const titleDiv = document.createElement("div");
          titleDiv.classList.add("result-title");
          titleDiv.textContent = result.title;
          titleDiv.addEventListener("click", () => { // Click event bleibt erhalten
            zeigeArtikel(result.title);
          });
          resultDiv.appendChild(titleDiv);

          const snippetDiv = document.createElement("div");
          snippetDiv.innerHTML = result.snippet;
          resultDiv.appendChild(snippetDiv);

          artikelDiv.appendChild(resultDiv);
        });
      } else {
        artikelDiv.textContent = "Keine Ergebnisse gefunden.";
      }
    })
    .catch(error => {
      artikelDiv.textContent = "Fehler bei der Suche: " + error.message;
      console.error("Wikipedia API Fehler:", error);
    });
}

function zeigeArtikel(titel) {
  const artikelDiv = document.getElementById("artikel");
  artikelDiv.innerHTML = "Artikel wird geladen...";

  const apiUrl = https://de.wikipedia.org/w/api.php?action=parse&page=${titel}&format=json&prop=text&origin=*;

  fetch(apiUrl)
    .then(response => {
      if (!response.ok) {
        throw new Error(HTTP Fehler! Status: ${response.status});
      }
      return response.json();
    })
    .then(data => {
      if (data.parse && data.parse.text && data.parse.text["*"]) {
        artikelDiv.innerHTML = data.parse.text["*"];

        // Entferne alle Bilder
        const images = artikelDiv.querySelectorAll('img');
        images.forEach(img => img.remove());

        const links = artikelDiv.querySelectorAll('a');
        links.forEach(link => {
          link.style.color = 'white';
          link.style.pointerEvents = 'none';
          link.style.textDecoration = 'none';
        });
      } else {
        artikelDiv.textContent = "Fehler beim Laden des Artikels.";
      }
    })
    .catch(error => {
      artikelDiv.textContent = "Fehler beim Laden des Artikels: " + error.message;
      console.error("Wikipedia API Fehler:", error);
    });
}
</script>

</body>
</html>
