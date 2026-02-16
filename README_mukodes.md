# A Fájlfeltöltő Projekt Működésének Részletes Leírása

Ez a dokumentum a projekt logikai felépítését és a kódban megvalósított folyamatokat tárgyalja. Célja, hogy átfogó képet adjon arról, mi történik a háttérben egy fájl feltöltésekor.

## 1. A Projekt Szerkezete

A projekt az alábbi fájlstruktúrára épül:
*   `index.js`: A szerver oldali belépési pont. Itt történik a konfiguráció és a kérések kezelése.
*   `public/index.html`: A kliens oldali felület, amely tartalmazza a feltöltő űrlapot.
*   `uploads/`: A szerver által dinamikusan létrehozott mappa, ahová a feltöltött fájlok kerülnek.
*   `package.json`: A projekt függőségeit (dependencies) leíró fájl.

## 2. Működési Folyamat Lépésről Lépésre

### A. Inicializálás és Előkészületek (`index.js`)

1.  **Modulok betöltése:** A kód elején a `require` utasítással behívjuk a szükséges csomagokat (`express`, `multer`, `path`, `fs`).
2.  **Szerver példányosítása:** Az `app = express()` sor létrehoz egy Express alkalmazást.
3.  **Biztonsági ellenőrzés (Mappa létrehozása):**
    *   A program futása elején ellenőrizzük az `fs.existsSync` függvénnyel, hogy létezik-e az `uploads` mappa.
    *   Ha nem, az `fs.mkdirSync` azonnal létrehozza. Ez kritikus lépés, mert a Multer hibát dobna, ha nem létező mappába próbálna írni.

### B. A Multer Konfigurációja (Storage Engine)

A `multer.diskStorage` metódussal határozzuk meg a fájlok kezelésének szabályait. Ez két fő függvényt tartalmaz:

1.  **`destination` (Hova mentsünk?):**
    *   Visszatérési értéke a célkönyvtár elérési útja (`uploads/`).
    *   A `cb` (callback) függvény első paramétere `null` (nincs hiba), a második az útvonal.

2.  **`filename` (Mi legyen a fájl neve?):**
    *   **Probléma:** Ha két felhasználó "kep.jpg" néven tölt fel fájlt, az egyik felülírná a másikat.
    *   **Megoldás:** Egyedi nevet generálunk.
    *   **Implementáció:** `Date.now()` (aktuális időbélyeg) + véletlen szám + eredeti kiterjesztés (`path.extname`).
    *   Eredmény: `167888654321-456789.jpg` formátumú, ütközésbiztos fájlnév.

### C. A Kliens Oldal (`public/index.html`)

A HTML űrlapnak két speciális tulajdonsággal kell rendelkeznie a fájlfeltöltéshez:
1.  `method="POST"`: Az adatok küldése a HTTP törzsben (body) történik.
2.  `enctype="multipart/form-data"`: Ez jelzi a böngészőnek, hogy nem csak szöveges adatokat, hanem bináris fájltartalmat is küldünk.

Az `<input type="file" name="fajl">` elem `name` attribútuma kulcsfontosságú, ennek egyeznie kell a szerver oldali konfigurációval.

### D. A Feltöltés Kezelése (Route Handler)

Amikor a felhasználó rákattint a "Feltöltés" gombra:

1.  A böngésző elküldi a kérést a `/upload` végpontra.
2.  **Middleware futása:** Az `upload.single('fajl')` middleware lép működésbe.
    *   Megkeresi a kérésben a "fajl" nevű mezőt.
    *   Lementi a fájlt a beállított mappába az új névvel.
    *   Létrehozza a `req.file` objektumot a fájl adataival.
3.  **Válaszadás:**
    *   A szerver ellenőrzi, hogy a `req.file` létezik-e. Ha nem (pl. üres űrlap), 400-as hibakódot küld.
    *   Siker esetén JSON formátumban visszaküldi a feltöltött fájl adatait (eredeti név, új név, méret, típus).

## 3. Hibakezelés

A kód `try-catch` blokkot használ a végponton belül. Bár a Multer hibáit (pl. túl nagy fájl) jellemzően a middleware szintjén vagy egy globális hibakezelővel szokás elkapni, itt a `catch` ág biztosítja, hogy váratlan szerverhiba esetén (pl. jogosultsági hiba a lemezíráskor) a kliens 500-as státuszkódot kapjon, és a szerver ne álljon le.

---

## Összefoglaló táblázat a folyamatról

| Lépés | Helyszín | Tevékenység |
| :--- | :--- | :--- |
| 1. | Szerver | Elindul, létrehozza az `uploads` mappát. |
| 2. | Kliens | A felhasználó kiválasztja a fájlt és elküldi (POST). |
| 3. | Szerver (Multer) | Fogadja a streamet, generál egy egyedi nevet. |
| 4. | Szerver (Multer) | Kiírja a fájlt a lemezre. |
| 5. | Szerver (Express) | A `req.file` objektumot átadja a route handlernek. |
| 6. | Szerver | JSON választ küld a kliensnek a sikerről. |

## Felhasznált Irodalom

*   **HTTP Protokoll:**
    *   POST metódus: https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST
    *   HTTP Státuszkódok (200, 400, 500): https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
*   **Node.js Stream-ek:**
    *   A fájlfeltöltés alapja a stream-ek kezelése: https://nodejs.org/api/stream.html
