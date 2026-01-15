# SKIN SCAN - Instrukcja Hostowania i Zbierania Wyników

## 🚀 NAJPROŚCIEJ: GitHub Pages (darmowy hosting)

### Krok 1: Utwórz repozytorium na GitHub

1. Idź na https://github.com i zaloguj się (lub załóż konto)
2. Kliknij **"New repository"** (zielony przycisk)
3. Nazwa: `skinscan-study` (lub inna)
4. Zaznacz **"Public"**
5. Kliknij **"Create repository"**

### Krok 2: Wgraj pliki

Wgraj do repozytorium:
```
skinscan-study/
├── index.html              ← przemianuj skinscan_study.html na index.html
├── scientific_subset.json  ← dane quizu
└── images/                 ← folder ze zdjęciami ISIC_*.jpg
```

**UWAGA:** Folder `images/` może być duży. Alternatywnie możesz hostować zdjęcia gdzie indziej i zmienić `imageBasePath` w kodzie.

### Krok 3: Włącz GitHub Pages

1. W repozytorium kliknij **Settings** (góra)
2. W lewym menu wybierz **Pages**
3. W sekcji "Source" wybierz: **Deploy from a branch**
4. Branch: **main**, folder: **/ (root)**
5. Kliknij **Save**

Za kilka minut strona będzie dostępna pod:
```
https://TWOJA-NAZWA.github.io/skinscan-study/
```

---

## 📊 ZBIERANIE WYNIKÓW: Google Sheets

### Krok 1: Utwórz arkusz Google

1. Idź na https://sheets.google.com
2. Utwórz nowy arkusz
3. W pierwszym wierszu wpisz nagłówki:

```
timestamp | participant | experience | institution | accuracy | sensitivity | specificity | ppv | npv | tp | tn | fp | fn | correct | incorrect | total_time_seconds
```

### Krok 2: Utwórz Google Apps Script

1. W arkuszu kliknij **Rozszerzenia** → **Apps Script**
2. Usuń domyślny kod i wklej:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);
    
    sheet.appendRow([
      new Date().toISOString(),
      data.participant || '',
      data.experience || '',
      data.institution || '',
      data.accuracy || 0,
      data.sensitivity || 0,
      data.specificity || 0,
      data.ppv || 0,
      data.npv || 0,
      data.tp || 0,
      data.tn || 0,
      data.fp || 0,
      data.fn || 0,
      data.correct || 0,
      data.incorrect || 0,
      data.total_time_seconds || 0
    ]);
    
    return ContentService.createTextOutput(JSON.stringify({status: 'success'}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({status: 'error', message: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

3. Kliknij **💾 Zapisz** (Ctrl+S)
4. Nazwij projekt np. "SKIN SCAN Results"

### Krok 3: Wdróż jako Web App

1. Kliknij **Wdróż** → **Nowe wdrożenie**
2. Kliknij ⚙️ przy "Wybierz typ" → **Aplikacja internetowa**
3. Ustaw:
   - Opis: "SKIN SCAN Results Collector"
   - Wykonaj jako: **Ja**
   - Kto ma dostęp: **Każdy**
4. Kliknij **Wdróż**
5. Autoryzuj dostęp (kliknij przez ostrzeżenia)
6. **SKOPIUJ URL** aplikacji - wygląda tak:
   ```
   https://script.google.com/macros/s/AKfycbx.../exec
   ```

### Krok 4: Wklej URL do kodu HTML

W pliku `index.html` znajdź linię:
```javascript
const GOOGLE_SCRIPT_URL = 'YOUR_GOOGLE_SCRIPT_URL_HERE';
```

Zmień na:
```javascript
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbx.../exec';
```

Wgraj zaktualizowany plik na GitHub.

---

## ✅ GOTOWE!

Teraz możesz:
1. Wysłać link `https://TWOJA-NAZWA.github.io/skinscan-study/` do dermatolożów
2. Wyniki automatycznie trafiają do Twojego arkusza Google
3. Eksportuj dane z Google Sheets do CSV/Excel do analizy

---

## 🔧 ALTERNATYWY

### Hosting zdjęć (jeśli za duże dla GitHub)

GitHub ma limit 100MB na plik i 1GB na repo. Jeśli folder `images/` jest za duży:

**Opcja A: Google Drive**
1. Wgraj folder `images/` na Google Drive
2. Udostępnij publicznie
3. Zmień `imageBasePath` w kodzie na URL Google Drive

**Opcja B: Cloudinary (darmowy do 25GB)**
1. Załóż konto na cloudinary.com
2. Wgraj zdjęcia
3. Użyj ich URL jako `imageBasePath`

**Opcja C: Własny serwer**
Jeśli masz dostęp do serwera uczelnianego, wgraj tam.

---

## 📱 TESTOWANIE LOKALNE

Przed wgraniem na GitHub, przetestuj lokalnie:

```bash
cd folder-z-plikami
python -m http.server 8000
```

Otwórz: http://localhost:8000

---

## ❓ FAQ

**P: Czy wyniki są bezpieczne?**
O: Arkusz Google jest prywatny - tylko Ty masz dostęp. Dane są przesyłane przez HTTPS.

**P: Czy mogę edytować wygląd?**
O: Tak, wszystko jest w jednym pliku HTML. Zmień kolory w sekcji `:root { }`.

**P: Ile osób może jednocześnie korzystać?**
O: GitHub Pages i Google Sheets obsługują tysiące użytkowników jednocześnie.

**P: Czy mogę usunąć informację o poprawnej odpowiedzi?**
O: Tak, znajdź sekcję `feedback` w kodzie i usuń/zakomentuj.
