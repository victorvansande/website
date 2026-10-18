# GekkooPedia: werkafspraken en projectkennis

Vrijwilligersgids van Gekkoo (jeugdwerk, Antwerpen en Sint-Niklaas). Statische single-page app, geen build-stap.
Live: https://gekkoo.victorvansande.workers.dev. Eigenaar: Victor (victor@gekkoo.be).

## Werkafspraken (altijd volgen)

- **Antwoord in het Nederlands**, kort en concreet.
- **Geen em dashes (—)** en geen typische AI-taal ("naadloos", "duik in", "niet alleen X maar ook Y", marketingvulsel). Niet in chat, UI-teksten, commits of docs. Gebruik komma's, punten, dubbelpunten of haakjes. Kom je bestaande em dashes tegen in teksten die je aanraakt: weghalen.
- **Na elke afgeronde wijziging meteen committen en pushen naar `main`**, zonder te vragen. Eén commit per afgeronde taak, geen half werk. Commitberichten in het Nederlands, eindigen met de versie, bv. `... (v76)`.
- **Bump bij elke wijziging aan code of assets de cacheversie** in `service-worker.js` (`const CACHE = 'gekkoo-vNN'`). Nieuwe afbeeldingen ook in de `ASSETS`-lijst zetten.
- Push naar `main` deployt automatisch via GitHub Actions (`.github/workflows/deploy.yml`, wrangler naar Cloudflare Workers assets). Controleer daarna dat de live `service-worker.js` de nieuwe versie toont. Let op: `/index.html` geeft een redirect, check dus `/` (met een `?x=random` query).
- Nooit secrets of API-tokens zelf invullen of beheren. De Cloudflare-tokens staan als GitHub secrets.
- De gebruiker gebruikt de site **90% op gsm**: elke wijziging ook op 375px breed nakijken.
- Teksten volgen [TAALGIDS.md](TAALGIDS.md) (klare taal, je/jij, vaste woordkeuze: vrijwilliger, spel vs. activiteit, Gekkoo-medewerkers, ...).
- Andere/nieuwe projecten nooit in deze map, altijd een eigen map.

## Bestanden

| Bestand | Inhoud |
|---|---|
| `index.html` | Alle views als `<section class="view">`: `hub` (homepage) en `module-watals`, `-spelmaken`, `-huisreglement`, `-hulp`, `-meer`, `-spel`, `-twizzit`, `-review`, `-shop`, `-zelfzorg`, `-regio` |
| `style.css` | Alles. Dark mode zit in een aparte laag "Gekkoo by night" (`body[data-theme="dark"]`, tokens `--d-*`), mobiele polish in `@media (max-width:540px)` |
| `script.js` | Routing (`showView(id, push, opts)`), zoekfunctie, badges, menu, vakantieteller, gecko, carrousel, bus. Secties beginnen met `/* ---- Naam ---- */` |
| `game.js`, `game2.js`, `game3.js` | Spelletjes (Gekkoo Hop, Vang, ...). Oude look behouden; een nieuwe look is bewust geschrapt |
| `regio.js` | Codeslot voor regio-ondersteuners (code `1234`, geen echte beveiliging) |
| `service-worker.js` | PWA-cache. Documenten (pdf, docx, ...) gaan nooit via de SW; index-fallback alleen voor echte paginanavigatie |
| `serve.ps1` | Lokale dev-server op poort 3000 (Windows). In de cloud/Linux: `python3 -m http.server 3000` |
| `.assetsignore` | Wat niet gedeployd wordt (deze CLAUDE.md, TAALGIDS.md, serve.ps1, ...) |

## Homepage (`#hub`), van boven naar onder

1. Hero: begroeting + "Alles wat je moet weten, op één plek" + vakantieteller (`#countdown`).
2. `#hub-principes` (wit), `#hub-rol` (goud), met read-sentinels die badges geven.
3. CTA "Meld je aan" (navy) naar https://gekkoo.be/helpen/vrijwilligers/aanmelden-als-vrijwilliger.
4. `#hub-gids` (blauw): tegels naar de modules + voortgangskaartje `#progress-card`.
5. `#hub-stem` (roze): carrousel "Vrijwilligers aan het woord".
6. `#hub-faq`, `#hub-media` (video + Instagram-kaart + embed).
7. `#hub-bus` (goud): teaser sponsoring Gekkoo-bus, bestuurbaar busje.
8. Footer met drie kolommen (contact, volg Gekkoo, nieuwsbrief).

Blokken gebruiken `.hub-block` + kleurklasse (`hb-white/gold/blue/pink/green/cta`), de achtergrond loopt full-bleed via `::before` en `--hb-bg`. Titels: `.kicker` (ProvidenceSansPro) + `.block-title` (Fredoka).

## Belangrijke details

- **Sticky header** werkt enkel met `body{overflow-x:clip}` (niet `hidden`). Globale fix `[hidden]{display:none!important}`.
- **Terug naar overzicht** brengt je terug op de hoogte van de tegels: `history.scrollRestoration='manual'` + `hubScrollY`.
- **Carrousel** (`#spot-carousel`): alle slides gestapeld in één grid-cel, tikt via `animationend` van de tijdsbalk (10 s), pauzeert bij hover/focus/touch/uit beeld. Volgorde wordt per bezoek geschud, maar **Burak staat altijd als laatste** (`data-pin="last"`). Nu: Bas, Luna, Alina, Leandro (nog gouden initiaal "L", wacht op foto), Burak. Pas bij een nieuwe slide ook de `aria-label` "x van N" aan.
- **Foto's van vrijwilligers**: vierkant 420×420 jpg, ongeveer 30 tot 45 KB, strak bijgesneden op het gezicht. Op Windows via PowerShell WPF (`System.Windows.Media.Imaging`, leest ook webp); in de cloud via ImageMagick of Python Pillow. Altijd een beschrijvende `alt`.
- **Vakantieteller**: telt af naar herfst 🍂, paas 🐣 of zomer ☀️ (Vlaamse kalender, hardcoded t.e.m. 2030 in `VAKANTIES`). Na 2030 aanvullen.
- **Gekkoo Hop cheat**: `*` op het toetsenbord tijdens het spelen = Supergekko (onsterfelijk, beukt buizen kapot), nog eens `*` = uit.
- **Site-gecko**: kruipt op gsm langs de linkerrand; tekstballon wordt binnen het scherm geklemd (`placeBubble`).
- **Bus-teaser**: busje rijdt automatisch; ← → sturen, ↑ springt (alleen als de bus half in beeld is en je niet typt), op gsm links/rechts ingedrukt houden. Na 4 s stilstaan rijdt hij weer vanzelf. Tekst: "Meer info volgt binnenkort", vragen bij Victor. Sponsorformules uit de sponsorbrief: Meelifter € 35, Opstapper € 75, Passagier € 150, Copiloot € 300, Chauffeur € 600, Motor € 1.200; € 4,60 = één speeldag met kansentarief uit het Gekkoo Fonds.
- **Zoekindex** bevat hub-blokken (via `data-search-title` / `data-search-icon`), FAQ, huisreglement en Wat-als-scenario's.
- **Zen-modus** verbergt afleiding maar laat de footer staan.

## Cloudsessies

- Werk op `main` en push naar `main`, net als lokaal. Krijgt de sessie een eigen branch toegewezen (bv. `main-xxxxx`), dan toch naar `main` pushen: zo wil Victor het.
- Aanwezig in de cloud: `python3` (dev-server), ImageMagick + Pillow (foto's), Chromium via Playwright (testen op 375px, licht en donker).
- Live controleren kan alleen als `gekkoo.victorvansande.workers.dev` bij Allowed domains van de cloudomgeving staat. Anders: deploy opvolgen via de GitHub Actions-run.
- Oude GitHub-geschiedenis (GitHub Pages-tijdperk, tot juni 2026) staat bewaard op branch `oud-main`.

## Testen

- Dev-server starten (Windows: launch-config "website" met `serve.ps1`; Linux: `python3 -m http.server 3000`) en in de browser kijken.
- Vóór testen de service worker uitschrijven en caches wissen, anders zie je oude bestanden:
  `for (const r of await navigator.serviceWorker.getRegistrations()) await r.unregister(); for (const k of await caches.keys()) await caches.delete(k); location.reload();`
- Is het preview-venster verborgen, dan draaien `requestAnimationFrame` en smooth scroll niet. Test animaties dan met korte waits of een tijdelijke debug-`data-`attribuut, en haal debugcode weg voor je commit.
- Overflow op gsm controleer je met DOM-metingen (`document.documentElement.scrollWidth === innerWidth`, `getBoundingClientRect`), niet enkel met geschaalde screenshots.
- Controleer licht én donker thema.

## Openstaand

- Foto van Leandro voor de carrousel (nu initiaal).
- Meer info over de bus-sponsoring volgt; teaser dan uitbreiden.
- Aangeboden, niet gevraagd: Hop en Vang framerate-onafhankelijk maken (te snel op 90 tot 120 Hz-schermen).
- Regio-pdf's leidden eens naar de homepage; opgelost via de SW-uitzondering voor documenten (v67). Als het terugkomt: toestel en browser vragen.
