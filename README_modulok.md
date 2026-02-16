# Felhasznált Modulok és Technológiák Ismertetése

Ez a dokumentum a fájlfeltöltő alkalmazás elkészítéséhez használt Node.js modulokat, azok szerepét és legfontosabb funkcióit mutatja be. A leírás célja, hogy elméleti hátteret biztosítson a kód megértéséhez.

## 1. Express (`express`)

Az **Express** a legnépszerűbb webes keretrendszer Node.js-hez. Minimalista, rugalmas és robusztus eszközkészletet biztosít webes és mobil alkalmazások fejlesztéséhez.

*   **Szerepe a projektben:**
    *   Webszerver létrehozása és HTTP kérések (GET, POST) kezelése.
    *   Útválasztás (Routing): Meghatározza, hogyan válaszoljon az alkalmazás egy adott végpontra érkező kérésre (pl. `/upload`).
    *   Statikus fájlok kiszolgálása: A `public` mappa tartalmának (HTML, CSS) elérhetővé tétele a kliens számára.

## 2. Multer (`multer`)

A **Multer** egy köztes szoftver (middleware) a Node.js számára, amelyet kifejezetten a `multipart/form-data` típusú adatok kezelésére terveztek. Ez a formátum szükséges, amikor fájlokat töltünk fel HTML űrlapokon keresztül.

*   **Szerepe a projektben:**
    *   A beérkező HTTP kérésből kinyeri a fájl adatfolyamát.
    *   Kezeli a fájlok tárolását (memóriában vagy lemezen).
    *   Lehetővé teszi a fájlok átnevezését és a célkönyvtár meghatározását a mentés pillanatában.
    *   A `req` objektumot kiegészíti egy `file` (vagy `files`) objektummal, amely tartalmazza a feltöltött fájl metaadatait (név, méret, típus).

*   **Fontos fogalom: Storage Engine (Tároló motor):**
    A projektben a `diskStorage` motort használjuk, amely teljes kontrollt ad a fájlok lemezre írása felett (hova mentsük és milyen néven).

## 3. Path (`path`)

A **Path** a Node.js beépített (core) modulja, amely segédprogramokat biztosít a fájl- és könyvtárútvonalakkal való munkához. Mivel az operációs rendszerek (Windows vs. Linux/macOS) eltérően kezelik az útvonalakat (pl. `\` vs `/`), a `path` modul használata biztosítja a platformfüggetlenséget.

*   **Szerepe a projektben:**
    *   `path.extname(filename)`: Kinyeri a fájl kiterjesztését (pl. `.jpg`, `.pdf`). Ez elengedhetetlen ahhoz, hogy a szerverre mentett fájl megőrizze eredeti típusát az átnevezés során.

## 4. File System (`fs`)

Az **FS** (File System) szintén a Node.js beépített modulja, amely lehetővé teszi a fájlrendszerrel való interakciót (olvasás, írás, törlés, mappák kezelése).

*   **Szerepe a projektben:**
    *   `fs.existsSync(path)`: Ellenőrzi, hogy létezik-e egy adott mappa (esetünkben az `uploads`).
    *   `fs.mkdirSync(path)`: Létrehozza a mappát, ha az még nem létezik. Ez biztosítja, hogy a szerver ne omoljon össze, ha a célkönyvtár hiányzik indításkor.

---

## Felhasznált Irodalom és Dokumentáció

A felkészüléshez és a további elmélyüléshez az alábbi hivatalos források ajánlottak:

1.  **Express dokumentáció:**
    *   Hivatalos oldal: https://expressjs.com/
    *   Routing útmutató: https://expressjs.com/en/guide/routing.html

2.  **Multer dokumentáció:**
    *   GitHub repository és leírás: https://github.com/expressjs/multer
    *   Részletes API leírás a `diskStorage` opcióról.

3.  **Node.js API dokumentáció (Path és FS):**
    *   Path modul: https://nodejs.org/api/path.html
    *   File System modul: https://nodejs.org/api/fs.html

4.  **MDN Web Docs (HTML Űrlapok):**
    *   A `multipart/form-data` működése: https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects
    *   Az `<input type="file">` elem: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/file
