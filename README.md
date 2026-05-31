<div align="center">

# 🗑️ Mass Instagram Unlike & Uncomment

**A browser console script to bulk remove likes and comments on Instagram — no extensions, no apps.**

![License](https://img.shields.io/badge/license-MIT-blue)
![Language](https://img.shields.io/badge/language-JavaScript-yellow)
![Platform](https://img.shields.io/badge/platform-Browser%20Console-lightgrey)
![Instagram](https://img.shields.io/badge/Instagram-Web-E1306C)

[🇬🇧 English](#-english) · [🇨🇿 Čeština](#-čeština)

</div>

---

## 🇬🇧 English

### ⚠️ Disclaimer

This script interacts with Instagram's web interface using browser automation. Use it **on your own account only**.

> [!WARNING]
> **Instagram 500 action limit** — Instagram allows a maximum of **500 combined actions per day** (likes, unlikes, comments, follows). Once you hit this limit, the API returns a `500 Internal Server Error`. You must wait **1–2 hours minimum** before resuming (some accounts may need up to 24 hours). The script's 30-second delay between rounds slows things down intentionally — do not remove it.

---

### ✨ Features

- Works directly in the browser console — no extensions or installs needed
- Automatically clicks **"Select"** on the activity page
- Selects all visible posts/comments in one round (~27 per batch)
- Clicks **"Remove"** and confirms the dialog automatically
- Waits **30 seconds** between rounds to avoid rate limiting
- Loops continuously until everything is removed
- Works with Instagram's Bloks UI (uses full `MouseEvent` simulation)
- Stop anytime via `window.stopUnlike = true` or `window.stopDelete = true`

---

### 🚀 How to Use

#### 1. Open the activity page

For **likes**:
```
https://www.instagram.com/your_activity/interactions/likes/
```

For **comments**:
```
https://www.instagram.com/your_activity/interactions/comments/
```

Make sure you are **logged in** to Instagram in the browser.

#### 2. Open the browser console

Press `F12` or `Ctrl+Shift+I` → go to the **Console** tab.

> In Chrome you may need to type `allow pasting` first if the console blocks paste.

#### 3. Paste and run the script

Copy the relevant script below, paste it into the console, and press **Enter**.

---

### 📜 Script 1 — Mass Unlike

```javascript
window.stopUnlike = false;

(async () => {
  const sleep = ms => new Promise(r => setTimeout(r, ms));
  let totalCount = 0;
  let round = 0;

  const realClick = (el) => {
    const rect = el.getBoundingClientRect();
    el.dispatchEvent(new MouseEvent('mousedown', { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('mouseup',   { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('click',     { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
  };

  const findAny = (text) =>
    [...document.querySelectorAll('button, div[role="button"], span[data-bloks-name]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findSpan = (text) =>
    [...document.querySelectorAll('span[data-bloks-name="bk.components.TextSpan"]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findButton = (text) =>
    [...document.querySelectorAll('button')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const countdown = async (seconds) => {
    for (let i = seconds; i > 0; i--) {
      if (window.stopUnlike) break;
      if (i % 5 === 0 || i <= 3) console.log(`⏳ Next round in ${i}s...`);
      await sleep(1000);
    }
  };

  while (!window.stopUnlike) {
    round++;
    console.log(`\n🔄 Round #${round} | Total so far: ${totalCount}`);

    const vybrátBtn = findAny('Vybrat');
    if (!vybrátBtn) { console.log('🎉 All done! Total unliked:', totalCount); break; }
    realClick(vybrátBtn);
    await sleep(1000);

    const cards = [...document.querySelectorAll('div[role="button"]')]
      .filter(el => {
        const lbl = el.getAttribute('aria-label') || '';
        return lbl.startsWith('Obrázek') || lbl.startsWith('Video') || lbl.startsWith('Reel');
      });

    if (!cards.length) {
      console.log('📭 No cards found – waiting 30s...');
      await countdown(30);
      continue;
    }

    for (const card of cards) { realClick(card); await sleep(80); }
    console.log(`🔲 Selected ${cards.length} posts`);
    await sleep(800);

    const unlikeSpan = findSpan('Odebrat To se mi líbí');
    if (!unlikeSpan) { console.warn('⚠️ Unlike span not found – retrying in 5s'); await sleep(5000); continue; }
    realClick(unlikeSpan);
    console.log('🗑️ Clicked "Remove Like"');
    await sleep(1500);

    const confirmBtn = findButton('Odebrat To se mi líbí');
    if (confirmBtn) { realClick(confirmBtn); console.log('✅ Confirmed'); await sleep(2000); }

    totalCount += cards.length;
    console.log(`💔 Round #${round} done. Total: ${totalCount}`);
    await countdown(30);
    window.scrollBy(0, 600);
    await sleep(1000);
  }

  console.log(`\n🏁 Finished. Total unliked: ${totalCount} in ${round} rounds`);
})();
```

**Stop anytime:**
```javascript
window.stopUnlike = true
```

---

### 📜 Script 2 — Mass Delete Comments

```javascript
window.stopDelete = false;

(async () => {
  const sleep = ms => new Promise(r => setTimeout(r, ms));
  let totalCount = 0;
  let round = 0;

  const realClick = (el) => {
    const rect = el.getBoundingClientRect();
    el.dispatchEvent(new MouseEvent('mousedown', { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('mouseup',   { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('click',     { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
  };

  const findAny = (text) =>
    [...document.querySelectorAll('button, div[role="button"], span[data-bloks-name]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findButton = (text) =>
    [...document.querySelectorAll('button')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findSpan = (text) =>
    [...document.querySelectorAll('span[data-bloks-name="bk.components.TextSpan"]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const countdown = async (seconds) => {
    for (let i = seconds; i > 0; i--) {
      if (window.stopDelete) break;
      if (i % 5 === 0 || i <= 3) console.log(`⏳ Next round in ${i}s...`);
      await sleep(1000);
    }
  };

  while (!window.stopDelete) {
    round++;
    console.log(`\n🔄 Round #${round} | Total deleted: ${totalCount}`);

    const vybrátBtn = findAny('Vybrat');
    if (!vybrátBtn) { console.log('🎉 All done! Total deleted:', totalCount); break; }
    realClick(vybrátBtn);
    console.log('✅ Select');
    await sleep(1000);

    const cards = [...document.querySelectorAll('div[role="button"]')]
      .filter(el => {
        const lbl = el.getAttribute('aria-label') || '';
        const txt = el.innerText?.trim() || '';
        return lbl.startsWith('Obrázek') || lbl.startsWith('Video') || lbl.startsWith('Reel')
          || (txt.includes('YOUR_USERNAME') && txt.length > 5); // ← CHANGE THIS
      });

    if (!cards.length) {
      console.log('📭 No cards found – waiting 30s...');
      await countdown(30);
      continue;
    }

    for (const card of cards) { realClick(card); await sleep(80); }
    console.log(`🔲 Selected ${cards.length} comments`);
    await sleep(800);

    const deleteSpan = findSpan('Odstranit') || findAny('Odstranit');
    if (!deleteSpan) { console.warn('⚠️ Delete button not found – retrying in 5s'); await sleep(5000); continue; }
    realClick(deleteSpan);
    console.log('🗑️ Clicked "Delete"');
    await sleep(1500);

    const confirmBtn = findButton('Odstranit');
    if (confirmBtn) { realClick(confirmBtn); console.log('✅ Confirmed'); await sleep(2000); }

    totalCount += cards.length;
    console.log(`🗑️ Round #${round} done. Total: ${totalCount}`);
    await countdown(30);
    window.scrollBy(0, 600);
    await sleep(1000);
  }

  console.log(`\n🏁 Finished. Total deleted: ${totalCount} comments in ${round} rounds`);
})();
```

> [!IMPORTANT]
> In Script 2, replace `YOUR_USERNAME` with your actual Instagram username before running.

**Stop anytime:**
```javascript
window.stopDelete = true
```

---

### 🌍 Language Note

Scripts use **Czech Instagram UI** labels. If your Instagram is in a different language, replace:

| Czech | English |
|---|---|
| `Vybrat` | `Select` |
| `Odebrat To se mi líbí` | `Unlike` |
| `Odstranit` | `Delete` |

---

### 🔧 Troubleshooting

| Problem | Solution |
|---|---|
| `500 Internal Server Error` in console | You hit Instagram's 500 action/day limit — wait 1–2 hours and resume |
| "Vybrat not found" | Make sure you are on the correct activity page |
| 0 posts selected | Scroll down so posts are visible before running |
| Button not found | Your Instagram UI language may differ — check labels and update the script |
| Account temporarily limited | Increase countdown from `30` to `60` and split across multiple sessions |

---

### 📋 Requirements

- Desktop browser (Chrome, Firefox, Edge, Safari)
- Logged-in Instagram session at `instagram.com`
- No extensions or third-party apps needed

---

### 📄 License

MIT — do whatever you want, just don't blame me if Instagram gets annoyed.

---
---

## 🇨🇿 Čeština

### ⚠️ Upozornění

Skript ovládá webové rozhraní Instagramu přes konzoli prohlížeče. Používej ho **pouze na vlastním účtu**.

> [!WARNING]
> **Instagram limit 500 akcí** — Instagram povoluje maximálně **500 kombinovaných akcí za den** (lajky, unlajky, komentáře, sledování). Po dosažení limitu API vrátí `500 Internal Server Error`. Je nutné počkat **minimálně 1–2 hodiny** než budete pokračovat (někdy až 24 hodin). Zabudovaná 30sekundová pauza mezi koly toto riziko snižuje — neodstraňuj ji.

---

### ✨ Funkce

- Funguje přímo v konzoli prohlížeče — žádné instalace ani rozšíření
- Automaticky klikne na **„Vybrat"** na stránce aktivit
- Označí všechny viditelné příspěvky/komentáře najednou (~27 za dávku)
- Klikne na **„Odebrat/Odstranit"** a potvrdí dialog automaticky
- Čeká **30 sekund** mezi koly kvůli ochraně účtu
- Jede dokola, dokud vše neodstraní
- Funguje s Bloks UI Instagramu (plná simulace `MouseEvent`)
- Kdykoliv zastavitelný přes `window.stopUnlike = true` nebo `window.stopDelete = true`

---

### 🚀 Jak použít

#### 1. Otevři stránku aktivit

Pro **lajky**:
```
https://www.instagram.com/your_activity/interactions/likes/
```

Pro **komentáře**:
```
https://www.instagram.com/your_activity/interactions/comments/
```

Musíš být **přihlášen** na instagram.com v prohlížeči.

#### 2. Otevři konzoli prohlížeče

Stiskni `F12` nebo `Ctrl+Shift+I` → záložka **Console**.

> V Chrome může být potřeba nejdřív napsat `allow pasting`, pokud konzole blokuje vkládání.

#### 3. Vlož a spusť skript

Zkopíruj příslušný skript, vlož do konzole a stiskni **Enter**.

---

### 📜 Skript 1 — Hromadné unlajkování

```javascript
window.stopUnlike = false;

(async () => {
  const sleep = ms => new Promise(r => setTimeout(r, ms));
  let totalCount = 0;
  let round = 0;

  const realClick = (el) => {
    const rect = el.getBoundingClientRect();
    el.dispatchEvent(new MouseEvent('mousedown', { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('mouseup',   { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('click',     { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
  };

  const findAny = (text) =>
    [...document.querySelectorAll('button, div[role="button"], span[data-bloks-name]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findSpan = (text) =>
    [...document.querySelectorAll('span[data-bloks-name="bk.components.TextSpan"]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findButton = (text) =>
    [...document.querySelectorAll('button')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const countdown = async (seconds) => {
    for (let i = seconds; i > 0; i--) {
      if (window.stopUnlike) break;
      if (i % 5 === 0 || i <= 3) console.log(`⏳ Další kolo za ${i}s...`);
      await sleep(1000);
    }
  };

  while (!window.stopUnlike) {
    round++;
    console.log(`\n🔄 Kolo #${round} | Celkem dosud: ${totalCount}`);

    const vybrátBtn = findAny('Vybrat');
    if (!vybrátBtn) { console.log('🎉 Vše hotovo! Celkem unlajkováno:', totalCount); break; }
    realClick(vybrátBtn);
    await sleep(1000);

    const cards = [...document.querySelectorAll('div[role="button"]')]
      .filter(el => {
        const lbl = el.getAttribute('aria-label') || '';
        return lbl.startsWith('Obrázek') || lbl.startsWith('Video') || lbl.startsWith('Reel');
      });

    if (!cards.length) {
      console.log('📭 Žádné karty – čekám 30s...');
      await countdown(30);
      continue;
    }

    for (const card of cards) { realClick(card); await sleep(80); }
    console.log(`🔲 Vybráno ${cards.length} příspěvků`);
    await sleep(800);

    const unlikeSpan = findSpan('Odebrat To se mi líbí');
    if (!unlikeSpan) { console.warn('⚠️ Span nenalezen – zkouším znovu za 5s'); await sleep(5000); continue; }
    realClick(unlikeSpan);
    console.log('🗑️ Kliknuto na "Odebrat To se mi líbí"');
    await sleep(1500);

    const confirmBtn = findButton('Odebrat To se mi líbí');
    if (confirmBtn) { realClick(confirmBtn); console.log('✅ Potvrzeno'); await sleep(2000); }

    totalCount += cards.length;
    console.log(`💔 Kolo #${round} hotovo. Celkem: ${totalCount}`);
    await countdown(30);
    window.scrollBy(0, 600);
    await sleep(1000);
  }

  console.log(`\n🏁 Hotovo. Celkem unlajkováno: ${totalCount} v ${round} kolech`);
})();
```

**Zastavení:**
```javascript
window.stopUnlike = true
```

---

### 📜 Skript 2 — Hromadné mazání komentářů

> [!IMPORTANT]
> Před spuštěním nahraď `TVOJE_UZIVATELSKEHO_JMENO` svým Instagram uživatelským jménem (řádek označený `// ← ZDE`).

```javascript
window.stopDelete = false;

(async () => {
  const sleep = ms => new Promise(r => setTimeout(r, ms));
  let totalCount = 0;
  let round = 0;

  const realClick = (el) => {
    const rect = el.getBoundingClientRect();
    el.dispatchEvent(new MouseEvent('mousedown', { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('mouseup',   { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
    el.dispatchEvent(new MouseEvent('click',     { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
  };

  const findAny = (text) =>
    [...document.querySelectorAll('button, div[role="button"], span[data-bloks-name]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findButton = (text) =>
    [...document.querySelectorAll('button')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const findSpan = (text) =>
    [...document.querySelectorAll('span[data-bloks-name="bk.components.TextSpan"]')]
      .find(el => el.innerText?.trim() === text && el.offsetParent !== null);

  const countdown = async (seconds) => {
    for (let i = seconds; i > 0; i--) {
      if (window.stopDelete) break;
      if (i % 5 === 0 || i <= 3) console.log(`⏳ Další kolo za ${i}s...`);
      await sleep(1000);
    }
  };

  while (!window.stopDelete) {
    round++;
    console.log(`\n🔄 Kolo #${round} | Celkem smazáno: ${totalCount}`);

    const vybrátBtn = findAny('Vybrat');
    if (!vybrátBtn) { console.log('🎉 Vše hotovo! Celkem smazáno:', totalCount); break; }
    realClick(vybrátBtn);
    console.log('✅ Vybrat');
    await sleep(1000);

    const cards = [...document.querySelectorAll('div[role="button"]')]
      .filter(el => {
        const lbl = el.getAttribute('aria-label') || '';
        const txt = el.innerText?.trim() || '';
        return lbl.startsWith('Obrázek') || lbl.startsWith('Video') || lbl.startsWith('Reel')
          || (txt.includes('TVOJE_UZIVATELSKE_JMENO') && txt.length > 5); // ← ZDE
      });

    if (!cards.length) {
      console.log('📭 Žádné karty – čekám 30s...');
      await countdown(30);
      continue;
    }

    for (const card of cards) { realClick(card); await sleep(80); }
    console.log(`🔲 Vybráno ${cards.length} komentářů`);
    await sleep(800);

    const deleteSpan = findSpan('Odstranit') || findAny('Odstranit');
    if (!deleteSpan) { console.warn('⚠️ Tlačítko nenalezeno – zkouším znovu za 5s'); await sleep(5000); continue; }
    realClick(deleteSpan);
    console.log('🗑️ Kliknuto na "Odstranit"');
    await sleep(1500);

    const confirmBtn = findButton('Odstranit');
    if (confirmBtn) { realClick(confirmBtn); console.log('✅ Potvrzeno'); await sleep(2000); }

    totalCount += cards.length;
    console.log(`🗑️ Kolo #${round} hotovo. Celkem: ${totalCount}`);
    await countdown(30);
    window.scrollBy(0, 600);
    await sleep(1000);
  }

  console.log(`\n🏁 Hotovo. Celkem smazáno: ${totalCount} komentářů v ${round} kolech`);
})();
```

**Zastavení:**
```javascript
window.stopDelete = true
```

---

### 🔧 Řešení problémů

| Problém | Řešení |
|---|---|
| `500 Internal Server Error` v konzoli | Dosáhl jsi limitu 500 akcí/den — počkej 1–2 hodiny a pokračuj |
| „Vybrat nenalezeno" | Zkontroluj, že jsi na správné stránce aktivit |
| Vybráno 0 příspěvků | Přeskroluj dolů, aby byly příspěvky viditelné |
| Tlačítko nenalezeno | Zkontroluj jazyk UI Instagramu a uprav texty v skriptu |
| Účet dočasně omezen | Zvyš countdown z `30` na `60` a rozděl na více sessions |

---

### 📋 Požadavky

- Desktopový prohlížeč (Chrome, Firefox, Edge, Safari)
- Přihlášená session na `instagram.com`
- Žádná rozšíření ani aplikace třetích stran nejsou potřeba
