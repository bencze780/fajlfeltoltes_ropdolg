# 🚀 Feladat: Node.js Fájlfeltöltő Szerver Készítése

Ez a projekt egy 45 perces gyakorlati feladat, melynek célja a Node.js, az Express és a Multer könyvtár alapjainak elsajátítása.

---

## 🎯 Célkitűzés
Egy olyan működő backend szerver elkészítése, amely:
1. Képes fájlokat fogadni HTTP POST kérésen keresztül.
2. Automatikusan kezeli a tárolómappát.
3. Egyedi nevet generál a fájloknak az ütközések elkerülése végett.
4. Strukturált JSON választ ad vissza a feltöltés állapotáról.

---

## 🛠 Lépések

### 1. Projekt inicializálása (5 perc)
* Hozz létre egy új könyvtárat és lépj bele.
* Add ki az `npm init -y` parancsot a `package.json` létrehozásához.
* Telepítsd a függőségeket:
  ```bash
  npm install express multer
  ```

### 2. Alapszerver és Mappaellenőrzés (10 perc)
* Hozd létre az `index.js` fájlt.
* Importáld a modulokat: `express`, `multer`, `path`, `fs`.
* Állítsd be a szervert a 3000-es portra.
* **Feladat:** Írj egy logikát, ami ellenőrzi az `uploads` mappa létezését az `fs.existsSync()` segítségével, és ha nem létezik, hozd létre az `fs.mkdirSync()`-el!

### 3. Multer Konfiguráció (10 perc)
Hozd létre a `storage` objektumot a `multer.diskStorage` használatával:
* **Destination:** Állítsd be az `uploads/` mappát célként.
* **Filename:** Generálj egyedi nevet! Használd a `Date.now()`, egy véletlenszám és az eredeti kiterjesztés (`path.extname`) kombinációját.
* Példányosítsd az `upload` változót a konfigurációd alapján.

### 4. Az API Végpont Létrehozása (10 perc)
* Hozz létre egy `app.post('/upload', ...)` útvonalat.
* Alkalmazd az `upload.single('fajl')` middleware-t (figyelj, hogy a HTML mező neve is 'fajl' legyen).
* Kezeld a hibákat: ha nincs fájl a kérésben (`req.file`), küldj 400-as státuszt.
* Sikeres feltöltéskor küldj vissza egy JSON-t a fájl adataival (`originalname`, `filename`, `size`, `mimetype`).

### 5. Statikus kiszolgálás és tesztelés (10 perc)
* Használd az `app.use(express.static('public'))` parancsot a statikus fájlokhoz.
* Indítsd el a szervert az `app.listen()` segítségével.
* **Teszt:** Próbáld ki a feltöltést Postman-nel vagy egy egyszerű HTML űrlappal.

---

## ✅ Ellenőrző Lista
- [ ] A szerver hiba nélkül elindul a `node index.js` paranccsal?
- [ ] Létrejön az `uploads` mappa a szerver indulásakor?
- [ ] A feltöltött fájlok nevei tartalmaznak időbélyeget?
- [ ] A szerver JSON választ ad vissza a kliensnek?

---

> **Pro tipp:** Használj `try-catch` blokkokat a hibakezeléshez, hogy a szerver ne omoljon össze váratlan hiba esetén!
