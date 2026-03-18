# ZOO Mořský svět · Velký bariérový útes
### Kiosk aplikace pro interaktivní dotykový displej

Webová kiosk aplikace pro druhé akvárium ZOO Mořský svět Praha — téma **Velký bariérový útes**. Běží jako fullscreen webová aplikace v Chromiu na dotykové AIO stanici.

---

## Náhled

| Homepage | Článek | Živočichové |
|---|---|---|
| 3 tematické karty | Scrollovatelný obsah + foto | Sidebar + detail |

---

## Struktura souborů

```
/
├── index.html            ← Homepage (hero + 3 karty)
├── utes.html             ← Článek: Velký bariérový útes
├── beleni.html           ← Článek: Bělení korálů (YouTube video)
├── zivocichove.html      ← Průvodce živočichy nádrže
├── admin.html            ← Admin panel (správa obsahu)
├── README.md
└── images/
    ├── logo.png              ← Logo ZOO Mořský svět
    ├── hero.jpg              ← Hero fotografie homepage
    ├── card_utes.jpg         ← Karta Velký bariérový útes
    ├── card_beleni.jpg       ← Karta Bělení korálů
    ├── card_zivocichove.jpg  ← Karta Živočichové
    ├── utes_hero.jpg         ← Hero foto článku o útesu
    ├── utes_foto1.jpg        ← Inline foto v článku
    ├── utes_foto2.jpg        ← Inline foto v článku
    ├── beleni_hero.jpg       ← Hero foto článku o bělení
    ├── beleni_foto1.jpg      ← Inline foto v článku
    ├── zivoc_001.jpg         ← Foto živočicha 1
    └── mapa_rozsireni.svg    ← Mapa rozšíření živočichů
```

---

## Technický stack

- **Čisté HTML + CSS + JavaScript** — žádný framework, žádný build systém
- **Hosting:** GitHub Pages (statické soubory)
- **Prohlížeč:** Chromium v kiosk módu
- **OS:** Ubuntu 24.04 LTS
- **Font:** DM Sans (Google Fonts)

### Barvy

```css
--blue:   #00A0E3
--navy:   #004469
--deeper: #00325a
--orange: #EF7F1A
--bg:     #001a2e
```

### Rozlišení a škálování

Aplikace je navržena na **1920×1080 px**. Na tabletu (1366×768) se automaticky škáluje:

```css
html {
  transform-origin: top left;
  transform: scale(0.7115); /* 1366/1920 */
  width: 1920px;
  height: 1080px;
  overflow: hidden;
  position: fixed;
}
```

Tento blok je v `<head>` každé stránky.

---

## Nasazení na GitHub Pages

1. Vytvoř repozitář `Morskysvet-GBR` na GitHubu
2. Nahraj všechny soubory do rootu repozitáře
3. V nastavení repozitáře → **Pages** → Source: `main` branch, root `/`
4. Aplikace bude dostupná na `https://velenskym.github.io/Morskysvet-GBR`

> ⚠️ GitHub Pages je case-sensitive — názvy souborů a složek musí přesně odpovídat referencím v HTML.

---

## Kiosk setup — Ubuntu 24.04 LTS

### 1. Autologin

Uprav `/etc/lightdm/lightdm.conf`:

```ini
[Seat:*]
autologin-user=koisk
autologin-user-timeout=0
user-session=openbox
```

Uživatel `koisk` musí být ve skupině `nopasswdlogin`:

```bash
sudo groupadd -f nopasswdlogin
sudo usermod -aG nopasswdlogin koisk
```

### 2. Openbox autostart

Soubor `/home/koisk/.config/openbox/autostart`:

```bash
unclutter -idle 3 -root &
xset s off &
xset -dpms &
xset s noblank &
/home/koisk/kiosk-start.sh &
```

### 3. Spouštěcí skript

Soubor `/home/koisk/kiosk-start.sh`:

```bash
#!/bin/bash
KIOSK_URL="https://velenskym.github.io/Morskysvet-GBR"
sleep 5
rm -f /home/koisk/.config/chromium/SingletonLock 2>/dev/null || true
find /home/koisk/.config/chromium -name "*.lock" -delete 2>/dev/null || true
exec /snap/bin/chromium \
  --kiosk \
  --noerrdialogs \
  --disable-infobars \
  --disable-session-crashed-bubble \
  --disable-restore-session-state \
  --no-first-run \
  --disable-pinch \
  --overscroll-history-navigation=0 \
  --disable-features=TranslateUI \
  --disable-translate \
  --check-for-update-interval=31536000 \
  "$KIOSK_URL"
```

```bash
chmod +x /home/koisk/kiosk-start.sh
```

### 4. Watchdog (systemd)

Soubor `/etc/systemd/system/kiosk-watchdog.service`:

```ini
[Unit]
Description=Kiosk Chromium Watchdog
After=graphical.target

[Service]
User=koisk
Environment=DISPLAY=:0
Restart=always
RestartSec=10
ExecStart=/bin/bash -c 'while true; do pgrep -x chromium || /home/koisk/kiosk-start.sh; sleep 15; done'

[Install]
WantedBy=graphical.target
```

```bash
sudo systemctl enable kiosk-watchdog
sudo systemctl start kiosk-watchdog
```

---

## Správa obsahu

### Admin panel

Otevři `admin.html` v prohlížeči (PC, tablet, mobil). Panel umožňuje:

- Editaci textů článků (CS + EN)
- Nahrání fotek s náhledem
- Správu živočichů (přidat / upravit / smazat)
- Nastavení YouTube videa

> Admin panel je zatím standalone — změny se ukládají pouze lokálně (v paměti prohlížeče). Pro trvalé ukládání je potřeba napojit na backend (viz plánované funkce).

### Přidání / změna videa (YouTube)

V souboru `beleni.html` najdi tento blok a nahraď `VIDEO_ID` identifikátorem YouTube videa:

```html
<iframe
  src="https://www.youtube.com/embed/VIDEO_ID?autoplay=1&mute=1&cc_load_policy=0&rel=0&modestbranding=1&iv_load_policy=3&disablekb=1&loop=1&playlist=VIDEO_ID"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen>
</iframe>
```

YouTube ID najdeš v URL videa za `watch?v=` — např. `https://youtu.be/DRsUWSgad2s` → ID je `DRsUWSgad2s`.

Parametry embeddovaného videa:

| Parametr | Hodnota | Popis |
|---|---|---|
| `autoplay` | `1` | Automatické spuštění |
| `mute` | `1` | Bez zvuku (nutné pro autoplay) |
| `cc_load_policy` | `0` | Bez titulků |
| `rel` | `0` | Bez doporučených videí |
| `modestbranding` | `1` | Minimální YouTube branding |
| `loop` | `1` | Opakování |

> ⚠️ Tablet musí mít připojení k internetu — video se streamuje z YouTube.

### Přidání živočicha

V souboru `zivocichove.html` najdi pole `animals` v JavaScriptu a přidej nový objekt:

```javascript
{
  cs: 'Název česky',
  en: 'English name',
  la: 'Genus species',
  tag_cs: 'Velký bariérový útes',
  tag_en: 'Great Barrier Reef',
  desc_cs: 'Popis živočicha česky...',
  desc_en: 'Animal description in English...',
  img: 'images/zivoc_XXX.jpg',
  map: 'images/mapa_rozsireni.svg'
}
```

Zároveň přidej odpovídající položku do `<nav class="sidebar">` v HTML a aktualizuj hodnotu `const total`.

### Přidání fotky

1. Optimalizuj fotografii (JPEG, kvalita 80–85, max 1200px na delší straně)
2. Pojmenuj ji bez diakritiky a mezer (např. `utes_foto3.jpg`)
3. Nahraj do složky `images/`
4. Referencuj v HTML jako `src="images/utes_foto3.jpg"`

---

## Dvoujazyčnost (CS / EN)

Každý textový element má atributy `data-cs` a `data-en`. Přepínání jazyků funguje automaticky přes tlačítka CS / EN v headeru.

```html
<div data-cs="Korálový útes" data-en="Coral Reef">Korálový útes</div>
```

Při přidávání nového textu vždy vyplň oba atributy.

---

## Auto-reset po nečinnosti

Na všech podstránkách (ne na `index.html`) se po **60 sekundách** nečinnosti aplikace automaticky vrátí na homepage. Timeout je nastaven v každé podstránce:

```javascript
var TIMEOUT = 60 * 1000; // 1 minuta
```

---

## Plánované funkce

- [ ] Živočichové — doplnit reálná data a fotky pro všechny druhy nádrže
- [ ] Admin panel napojit na backend (Supabase) pro trvalé ukládání a správu z jednoho místa
- [ ] Statistiky návštěvnosti — sběr dat o interakcích na všech tabletech
- [ ] Centrální správa obsahu pro více kiosků najednou

---

## Souvisejicí projekty

- [Morskysvet-kiosk](https://github.com/velenskym/Morskysvet-kiosk) — první akvárium (Raja Ampat)

---

*ZOO Mořský svět Praha · [www.morskysvet.cz](https://www.morskysvet.cz)*
