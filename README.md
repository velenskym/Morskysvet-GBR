# Great Barrier Reef kiosk — ZOO Sea World Prague

A touchscreen application running beside our Great Barrier Reef tank at ZOO Mořský svět (Sea World Prague), the Czech Republic's only public marine aquarium.

**Live:** https://velenskym.github.io/Morskysvet-GBR/
**Try it in a simulated kiosk screen:** https://velenskym.github.io/morskysvet-euac/

Plain HTML, CSS and JavaScript. No framework, no build step, no server. Hosted free on GitHub Pages, displayed by Chromium in kiosk mode on a small Linux box next to the glass.

---

## Pages

| File | What it is |
|---|---|
| `index.html` | Home — hero and three cards |
| `utes.html` | The reef: how it formed, how it works, what threatens it |
| `beleni.html` | Coral bleaching, with video |
| `zivocichove.html` | A guide to the animals in the tank — sidebar list, detail panel with photograph, names, facts and distribution map |
| `admin.html` | Content editor (see below) |

Both languages live in the same files: every text node carries `data-cs` and `data-en` attributes and one function swaps them.

## The admin panel — this is the interesting part

`admin.html` is a single page that talks to the **GitHub API** directly from the browser. It can edit the home page and article texts in both languages, swap the video, upload photographs, and add, edit or delete animals in the species guide. Saving writes a commit to this repository; the tablet picks the change up on its next page load, roughly a minute later through the GitHub Pages cache.

It works from a laptop, a phone or the tablet itself — anywhere with an internet connection. Authentication is a GitHub Personal Access Token with `repo` scope, entered once in the interface and kept in the browser's `localStorage`.

**Why it matters:** the person who knows the animals is the same person who edits the label text, and a wrong fact can be corrected from the exhibition floor in about a minute instead of waiting for a new printed panel. There is no CMS, no licence and no server to maintain.

**Security note if you reuse this:** the token lives in the browser of whoever uses the panel. Issue a fine-grained token scoped to this repository alone, never commit a token to the repository, and remember that `admin.html` is a public URL — its security rests entirely on the token, not on the URL being secret.

## Built for one specific screen

Authored at a fixed 1920×1080 layout and scaled with CSS to the tablet's 1366×768 panel. There is exactly one screen to support, so there is no responsive CSS in the project.

The practical consequence: **opening `index.html` on a laptop or a phone will not look right.** To see it as a visitor does, use the [kiosk simulator](https://velenskym.github.io/morskysvet-euac/).

## Running it locally

```bash
git clone https://github.com/velenskym/Morskysvet-GBR.git
cd Morskysvet-GBR
python3 -m http.server 8000
```

Then open http://localhost:8000 in a browser window sized to 1366×768 (in Chrome: DevTools → device toolbar → set a custom 1366×768 device).

## Reusing this for your own institution

You are welcome to. The code is MIT-licensed; the photographs, video and texts are not — see [LICENSE](LICENSE).

- **The machine underneath it** — Lubuntu with Openbox, autologin, Chromium kiosk flags, a watchdog and automatic power-off at closing time — is documented in [KIOSK-SETUP.md](https://github.com/velenskym/morskysvet-euac/blob/main/KIOSK-SETUP.md).
- The three sister applications: [jellyfish](https://github.com/velenskym/Morskysvet-meduzy), [octopus](https://github.com/velenskym/Morskysvet-chobotnice), [Raja Ampat](https://github.com/velenskym/Morskysvet-kiosk).

## Known gaps

The species guide still needs complete data and photographs for every animal in the tank.

## Contact

Mikuláš Velenský — Curator, ZOO Sea World Prague
velenskym@gmail.com · [morskysvet.cz](https://www.morskysvet.cz) · [github.com/velenskym](https://github.com/velenskym)

Shared with the EUAC community. If you build something from this, I would like to hear about it.
