# Phrases d'encouragement implicite (FR)

Fichier JSON destiné à des apps (ex. Scriptable iOS) pour piocher aléatoirement des formules de remerciement/valorisation.
Licence : CC0-1.0 (domaine public).

## Structure
- `version`, `language`
- `categories`: liste des catégories `{id, name}`
- `phrases`: objets `{id, category, text, vous, formel}`

## Exemple d'usage (Scriptable)
```javascript
// Remplacez l'URL RAW par celle de votre dépôt GitHub
const RAW_URL = "https://raw.githubusercontent.com/<user>/<repo>/main/phrases_renforcement.json";

async function getPhrases() {
  try {
    const req = new Request(RAW_URL);
    const json = await req.loadJSON();
    return json.phrases;
  } catch (e) {
    // Fallback local (iCloud Drive → Scriptable)
    const fm = FileManager.iCloud();
    const path = fm.joinPath(fm.documentsDirectory(), "phrases_renforcement.json");
    if (!fm.fileExists(path)) throw e;
    await fm.downloadFileFromiCloud(path);
    const content = fm.readString(path);
    return JSON.parse(content).phrases;
  }
}

function pickRandom(arr) { return arr[Math.floor(Math.random()*arr.length)]; }

function filterPhrases(phrases, {category=null, vous=null, formel=null} = {}) {
  return phrases.filter(p => 
    (category ? p.category === category : true) &&
    (vous === null ? true : p.vous === vous) &&
    (formel === null ? true : p.formel === formel)
  );
}

async function main() {
  const phrases = await getPhrases();
  // Exemple: choisir au hasard une phrase « vente_occasion » en vouvoiement, formelle
  const pool = filterPhrases(phrases, {category: "vente_occasion", vous: true, formel: true});
  const phrase = pickRandom(pool.length ? pool : phrases);
  console.log(phrase.text);
}

main();
```

## Catégories disponibles
- livraison
- relationnel
- travail
- quotidien
- vente_occasion
