<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vertrau mir Bruder</title>
    <style>
        body {
            background-color: black;
            color: white;
            font-family: sans-serif;
            text-align: center;
        }
        #suchfeld {
            width: 500px;
            padding: 10px;
            font-size: 16px;
        }
        #artikel {
            margin-top: 20px;
            text-align: left;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            background: #222;
            padding: 15px;
            border-radius: 5px;
        }
        .result {
            margin-bottom: 10px;
            padding: 10px;
            border: 1px solid white;
            border-radius: 5px;
            cursor: pointer;
        }
        .result-title {
            font-weight: bold;
            color: white;
            text-decoration: none;
        }
        h2 {
            margin-top: 20px;
        }
        /* Stil für den Impressum-Link */
        .impressum {
            position: fixed;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 10px;
            color: gray;
            text-decoration: none;
            cursor: pointer;
        }
        .impressum:hover {
            color: white;
        }
        #impressumText {
            display: none;
            position: fixed;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 10px;
            color: gray;
            background: #222;
            padding: 5px;
            border-radius: 5px;
        }
    </style>
</head>
<body>

    <h1>Vertrau mir Bruder</h1>

    <input type="text" id="suchfeld" placeholder="Suchbegriff eingeben">
    <button onclick="wikipediaSuche()">Suchen</button>

    <h2>Nur Zuverlässige Quellen</h2>

    <div id="artikel"></div>

    <!-- Unauffälliger Impressum-Link -->
    <a class="impressum" onclick="toggleImpressum()">Impressum</a>
    <div id="impressumText"> Hey und willkommen auf meiner Seite. Ihr findet hier Inhalte aus vertraulichen Quellen. Ihr müsst jetzt nicht mehr das ganze Internet durchsuchen, sondern euch einfach nur diesen Link merken. Die Vorteile zu z.b. Chat gpt sind das ihr kurze Texte kriegt die von einem Menschen geschrieben wurden das heisst das euren Lehrern warscheinlich nicht auffallen wird wenn ihr diese Quelle benutzt, die Texte sind inhaltlich aber trotzdem richtig.</div>

    <script>
        function toggleImpressum() {
            const impressumText = document.getElementById("impressumText");
            if (impressumText.style.display === "none") {
                impressumText.style.display = "block";
            } else {
                impressumText.style.display = "none";
            }
        }

        function wikipediaSuche() {
            const suchbegriff = document.getElementById("suchfeld").value;
            const artikelDiv = document.getElementById("artikel");

            if (suchbegriff.trim() === "") {
                artikelDiv.innerHTML = "<p>Bitte einen Suchbegriff eingeben.</p>";
                return;
            }

            artikelDiv.innerHTML = "<p>Suche wird ausgeführt...</p>";

            const apiUrl = `https://de.wikipedia.org/w/api.php?action=query&list=search&srsearch=${encodeURIComponent(suchbegriff)}&format=json&origin=*`;

            fetch(apiUrl)
                .then(response => {
                    if (!response.ok) {
                        throw new Error(`HTTP Fehler! Status: ${response.status}`);
                    }
                    return response.json();
                })
                .then(data => {
                    if (data.query && data.query.search.length > 0) {
                        artikelDiv.innerHTML = "";

                        data.query.search.forEach(result => {
                            const resultDiv = document.createElement("div");
                            resultDiv.classList.add("result");

                            const titleDiv = document.createElement("div");
                            titleDiv.classList.add("result-title");
                            titleDiv.textContent = result.title;
                            titleDiv.addEventListener("click", () => {
                                zeigeArtikel(result.title);
                            });

                            const snippetDiv = document.createElement("div");
                            snippetDiv.innerHTML = result.snippet + "...";

                            resultDiv.appendChild(titleDiv);
                            resultDiv.appendChild(snippetDiv);
                            artikelDiv.appendChild(resultDiv);
                        });
                    } else {
                        artikelDiv.innerHTML = "<p>Keine Ergebnisse gefunden.</p>";
                    }
                })
                .catch(error => {
                    artikelDiv.innerHTML = `<p>Fehler bei der Suche: ${error.message}</p>`;
                    console.error("Wikipedia API Fehler:", error);
                });
        }

        function zeigeArtikel(titel) {
            const artikelDiv = document.getElementById("artikel");
            artikelDiv.innerHTML = "<p>Artikel wird geladen...</p>";

            const apiUrl = `https://de.wikipedia.org/w/api.php?action=query&prop=extracts&exintro=false&explaintext=false&titles=${encodeURIComponent(titel)}&format=json&origin=*`;

            fetch(apiUrl)
                .then(response => {
                    if (!response.ok) {
                        throw new Error(`HTTP Fehler! Status: ${response.status}`);
                    }
                    return response.json();
                })
                .then(data => {
                    const pages = data.query.pages;
                    const pageId = Object.keys(pages)[0];

                    if (pages[pageId].extract) {
                        artikelDiv.innerHTML = pages[pageId].extract;

                        const links = artikelDiv.querySelectorAll("a");
                        links.forEach(link => {
                            link.style.color = "white";
                            link.style.pointerEvents = "none";
                            link.style.textDecoration = "none";
                        });

                        const images = artikelDiv.querySelectorAll("img");
                        images.forEach(img => img.remove());
                    } else {
                        artikelDiv.innerHTML = "<p>Fehler beim Laden des Artikels.</p>";
                    }
                })
                .catch(error => {
                    artikelDiv.innerHTML = `<p>Fehler beim Laden des Artikels: ${error.message}</p>`;
                    console.error("Wikipedia API Fehler:", error);
                });
        }
    </script>

</body>
</html>