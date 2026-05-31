# Instagram Mass Unlike Script

> A browser console script to bulk remove likes on Instagram’s activity page — no extensions, no apps, no login required beyond your own session.

-----

## ⚠️ Disclaimer

This script interacts with Instagram’s web interface using browser automation. Use it **on your own account only**. Instagram may temporarily limit your account if actions are performed too quickly. The built-in 30-second delay between rounds is intentional to reduce this risk.

-----

## ✨ Features

- Automatically clicks **“Select All”** on the likes activity page
- Selects all visible posts in one round
- Clicks **“Remove Like”** and confirms the dialog
- Waits **30 seconds** between rounds to avoid rate limiting
- Loops continuously until all likes are removed
- Works with Instagram’s Bloks UI (uses `MouseEvent` instead of `.click()`)
- Stop anytime via `window.stopUnlike = true`

-----

## 🚀 How to Use

### 1. Open the Likes page

Go to:

```
https://www.instagram.com/your_activity/interactions/likes/
```

Make sure you are **logged in** to Instagram in the browser.

### 2. Open the browser console

Press `F12` or `Ctrl+Shift+I` → go to the **Console** tab.

> In Chrome, you may need to type `allow pasting` first if the console blocks paste.

### 3. Paste and run the script

Copy the script below, paste it into the console, and press **Enter**.

-----

## 📜 Script

```javascript
window.stopUnlike = false;

(async () => {
  const sleep = ms => new Promise(r => setTimeout(r, ms));
  let totalCount = 0;
  let round = 0;

  const realClick = (el) => {
    const rect = el.getBoundingClientRect();
    el.dispatchEvent(new MouseEvent('click', { bubbles: true, clientX: rect.left + rect.width/2, clientY: rect.top + rect.height/2 }));
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
    if (!vybrátBtn) {
      console.log('🎉 All done! Total unliked:', totalCount);
      break;
    }
    realClick(vybrátBtn);
    await sleep(1000);

    const cards = [...document.querySelectorAll('div[role="button"]')]
      .filter(el => {
        const lbl = el.getAttribute('aria-label') || '';
        return lbl.startsWith('Obrázek') || lbl.startsWith('Video') || lbl.startsWith('Reel');
      });

    if (!cards.length) {
      console.log('📭 No cards found – waiting 30s for page to load...');
      await countdown(30);
      continue;
    }

    for (const card of cards) { realClick(card); await sleep(80); }
    console.log(`🔲 Selected ${cards.length} posts`);
    await sleep(800);

    const unlikeSpan = findSpan('Odebrat To se mi líbí');
    if (!unlikeSpan) {
      console.warn('⚠️ Unlike span not found – retrying in 5s');
      await sleep(5000);
      continue;
    }
    realClick(unlikeSpan);
    console.log('🗑️ Clicked "Remove Like"');
    await sleep(1500);

    const confirmBtn = findButton('Odebrat To se mi líbí');
    if (confirmBtn) {
      realClick(confirmBtn);
      console.log('✅ Confirmed in dialog');
      await sleep(2000);
    } else {
      console.warn('⚠️ Confirm button not found');
    }

    totalCount += cards.length;
    console.log(`💔 Round #${round} done. Total: ${totalCount}`);

    await countdown(30);

    window.scrollBy(0, 600);
    await sleep(1000);
  }

  console.log(`\n🏁 Script finished. Total unliked: ${totalCount} in ${round} rounds`);
})();
```

-----

## ⏹ Stop the Script

At any time, paste this into the console:

```javascript
window.stopUnlike = true
```

-----

## 🌍 Language Note

Instagram’s UI language affects button labels. This script is set up for **Czech language** (`Odebrat To se mi líbí`, `Vybrat`). If your Instagram is in a different language, replace those strings with the correct translations:

|Czech                  |English |
|-----------------------|--------|
|`Vybrat`               |`Select`|
|`Odebrat To se mi líbí`|`Unlike`|

-----

## 🔧 Troubleshooting

|Problem                       |Solution                                                                           |
|------------------------------|-----------------------------------------------------------------------------------|
|Script says “Vybrat not found”|Make sure you are on the correct page: `/your_activity/interactions/likes/`        |
|0 posts selected              |Scroll down so posts are visible before running                                    |
|Unlike button not found       |Your Instagram UI language may differ — check the button text and update the script|
|Account temporarily limited   |Increase the countdown value from `30` to `60` or more                             |

-----

## 📋 Requirements

- A desktop browser (Chrome, Firefox, Edge, Safari)
- Logged-in Instagram session at `instagram.com`
- No extensions or third-party apps needed

-----

## 📄 License

MIT — do whatever you want, just don’t blame me if Instagram gets annoyed.

-----

-----

# Instagram Mass Unlike Script (CZ)

> Skript do konzole prohlížeče pro hromadné odebrání lajků na stránce aktivit Instagramu — bez rozšíření, bez aplikací.

-----

## ⚠️ Upozornění

Skript ovládá webové rozhraní Instagramu přes konzoli prohlížeče. Používej ho **pouze na vlastním účtu**. Instagram může dočasně omezit účet při příliš rychlém provádění akcí. Zabudovaná 30sekundová pauza mezi koly toto riziko snižuje.

-----

## ✨ Funkce

- Automaticky klikne na **„Vybrat”** na stránce aktivit
- Označí všechny viditelné příspěvky najednou
- Klikne na **„Odebrat To se mi líbí”** a potvrdí dialog
- Čeká **30 sekund** mezi koly kvůli ochraně účtu
- Jede dokola, dokud neodebere všechny lajky
- Funguje s Bloks UI Instagramu (používá `MouseEvent`)
- Kdykoliv zastavitelný přes `window.stopUnlike = true`

-----

## 🚀 Jak použít

### 1. Otevři stránku lajků

Přejdi na:

```
https://www.instagram.com/your_activity/interactions/likes/
```

Musíš být **přihlášen** na instagram.com v prohlížeči.

### 2. Otevři konzoli prohlížeče

Stiskni `F12` nebo `Ctrl+Shift+I` → záložka **Console**.

> V Chrome může být potřeba nejdřív napsat `allow pasting`, pokud konzole blokuje vkládání.

### 3. Vlož a spusť skript

Zkopíruj skript výše (sekce Script), vlož do konzole a stiskni **Enter**.

-----

## ⏹ Zastavení skriptu

Kdykoliv vlož do konzole:

```javascript
window.stopUnlike = true
```

-----

## 🔧 Řešení problémů

|Problém                    |Řešení                                                                     |
|---------------------------|---------------------------------------------------------------------------|
|„Vybrat nenalezeno”        |Zkontroluj, že jsi na správné stránce: `/your_activity/interactions/likes/`|
|Vybráno 0 příspěvků        |Přeskroluj dolů, aby byly příspěvky viditelné                              |
|Tlačítko odebrat nenalezeno|Zkontroluj jazyk UI Instagramu a uprav texty v skriptu                     |
|Účet dočasně omezen        |Zvyš hodnotu countdown z `30` na `60` nebo více                            |

-----

## 📋 Požadavky

- Desktopový prohlížeč (Chrome, Firefox, Edge, Safari)
- Přihlášená session na `instagram.com`
- Žádná rozšíření ani aplikace třetích stran nejsou potřeba
