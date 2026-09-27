# Changelog

Alle væsentlige ændringer i Timer-appen dokumenteres her.

Formatet følger [Keep a Changelog](https://keepachangelog.com/da/1.1.0/),
og projektet følger [Semantic Versioning](https://semver.org/lang/da/).

Versionsnummeret har én kilde: konstanten `APP_VERSION` i `timer.html`.
Den skrives til headeren ved opstart.

---

## [5.5.0] – 2026-09-27

Første version der følger de fælles udviklingsregler: changelog, SemVer,
Vestergaard-brand og WCAG 2.2 AA.

### Added
- `APP_VERSION`-konstant som eneste kilde til versionsnummeret. Headeren
  læser den ved opstart, så nummeret ikke længere står to steder.
- `CHANGELOG.md` (denne fil).
- Synlig fokusring (`:focus-visible`) på alle felter og knapper.
- Understøttelse af `prefers-reduced-motion` — appen har 18 animationer,
  som nu slås fra for brugere der har bedt om mindre bevægelse.
- Tastaturbetjening af de udfoldelige dag- og ugepaneler: Enter og
  mellemrum virker, og `aria-expanded` følger tilstanden.
- `aria-label` på kategorifelter og `<label>` på arbejdstidsfelterne.
- `role="status"` med `aria-live` på toast-beskeder og sync-indikatoren,
  så skærmlæsere annoncerer dem.

### Changed
- **Brand:** baggrunden er nu Vestergaard Corporate Blue `#051C2C`, og
  skriften er Tahoma. Logoet følger brandets gule regel — ved to ord er
  det sidste gult, altså hvid `VESTERGAARD` og gul `TIMER`.
  `--gold` var i forvejen præcis Corporate Yellow `#F5AC00`.
- **Farvepalet** justeret så alt opfylder WCAG AA (4,5:1 for tekst,
  3:1 for rammer). Alle værdier er beregnet med WCAG-formlen, ikke skønnet:
  `--slate` `#6B8FA8` → `#9BB8CE`, `--red` `#FF5C5C` → `#FF7B7B`,
  `--blue` `#4DA6FF` → `#6FB8FF`, `--purple` `#B06EFF` → `#C99BFF`.
  Baggrundsskalaen `--ink2`…`--ink5` er strammet, så teksten holder
  kontrast også på de lyseste flader.
- Ny `--line` `#678FB3` til rammer på interaktive elementer. De tidligere
  `rgba(255,255,255,.1)`-rammer gav kun 1,3:1 og brød 1.4.11.
- Sync-indikatoren viser nu et tegn (`●` lokal, `↻` synkroniserer,
  `✓` ok, `!` fejl) ud over farven, og er vokset fra 8 til 18 px.
  Fire tilstande adskilt udelukkende ved farve brød 1.4.1.
  Den har `role="img"` og ikke `aria-live` — en live-region ville lade
  skærmlæsere annoncere hver poll, altså hvert 10. sekund.
- Dag-headeren i UGE-fanen er kun interaktiv når dagen har registrerede
  timer. Før blev den annonceret som en knap selv når der intet var at
  folde ud.
- Trykflader i kategorirækker er hævet til mindst 24×24 px (2.5.8):
  ×-knappen var 31×19, u/ON-checkboxen 13×13.

### Fixed
- **Ingen synlig fokusmarkering på ~20 inputfelter.** `outline:none` stod
  som *inline* style og slog dermed stylesheet-reglen. Appen kunne ikke
  betjenes med tastatur alene.
- **Id-kollision:** `frStart` og `frStop` fandtes både i
  indstillingsmodalen og i frokost-baren på DAG-fanen. Da `#main` kommer
  før `#modalRoot` i DOM'en, ville `getElementById` ramme det forkerte
  felt hvis begge var fremme. Modalens felter hedder nu `setFrStart` og
  `setFrStop`.
- Hvid tekst på rød knap gav 3,03:1 — nu mørk tekst, 6,92:1.
- Hvid tekst på de lyse tidslinjeblokke (blå, lilla, blågrå og
  syg/barnsyg/§56) gav 2,56–4,05:1. Nu mørk tekst på alle.
- `maximum-scale=1.0, user-scalable=no` fjernet fra viewport — det
  blokerede zoom og brød 1.4.4.
- `aria-expanded` blev nulstillet ved hver re-render, altså hvert 10.
  sekund. Et åbent panel fremstod permanent lukket for skærmlæsere.
- Dag-headeren i UGE havde `cursor:pointer` også når den ikke var klikbar.
- **De flydende knapper dækkede indhold på telefon og kunne ikke scrolles
  fri.** `body` havde `min-height:100dvh`, så siden voksede ud over skærmen
  i stedet for at lade `main` scrolle internt. Timetal i "Totaler til
  Promak" var permanent skjult bag knappen. `body` har nu fast højde, og
  knapperne er flyttet ned i den plads `main`s bundpolstring frigør —
  med respekt for iPhones home-indikator.
- Placeholder-tekst arvede browserens grå og gav kun 3,3:1.
- Modaler kunne ikke betjenes med tastatur: Escape lukkede dem ikke, fokus
  blev ikke flyttet ind, og Tab førte bagom til elementer under overlayet.
  Nu lukker Escape øverste lag via dens egen luk-funktion (så kladder
  ryddes korrekt), fokus flyttes ind, og Tab holdes inde i panelet.
  Alle fem modaler har fået `role="dialog"` og `aria-modal`.
- Registreringer uden ON-nummer efterlod en tom, fed linje i UGE, RAPPORT
  og Promak-oversigten. Falder nu tilbage til kategorinavnet.
- "✕ Slet" fjernede en registrering uden bekræftelse og uden fortryd —
  i modsætning til appens fem andre destruktive handlinger.

### Added
- Hover-tilbagemelding på enheder med mus. Appen havde to `:hover`-regler
  i alt, så hver knap føltes død på en PC indtil man klikkede.

### Security
- **Hemmelig Outlook-kalenderadresse fjernet fra koden.** Den lå hardkodet
  i en fil i et *offentligt* repo. Adressen er en anonym delings-URL: med
  den kunne enhver hente hele kalenderen — mødetitler, tidspunkter,
  kunde- og kollega-navne — uden login. Den ligger nu i `localStorage`
  under `vct_icsurl`, samme mønster som gist-ID og token.
  **Feedet skal tilbagekaldes i Outlook** — adressen har været offentlig
  og skal betragtes som kompromitteret.
- **Tredjeparts-CORS-proxyer fjernet.** `corsproxy.io`, `allorigins.win` og
  `proxy.cors.sh` modtog både kalenderadressen og hele kalenderindholdet i
  klartekst. De er ikke godkendte databehandlere. Kalenderimport sker nu
  enten direkte eller via manuel filvalg, som altid har virket.
- **Stored XSS lukket.** Data fra registreringer nåede `innerHTML`
  uescaped 18 steder — herunder `value="${e.on}"`, hvor ét citationstegn
  var nok til at bryde ud. En stregkode med HTML-indhold blev gemt i `db`,
  synkroniseret til alle enheder og udført ved næste visning, i samme
  origin som GitHub-tokenet. Alle steder escapes nu, og
  stregkodeindhold renses ved kilden med `renTekst()`.
- `esc()` escaper nu også apostrof. Værdier interpoleres i
  `onclick="f('…')"`, hvor en apostrof ellers bryder ud af JS-strengen.
- **Content-Security-Policy** tilføjet: blokerer fremmede scripts,
  formularer, `<base>` og `<object>`, og låser netværket til GitHubs API.
  Den stopper ikke XSS i sig selv — `'unsafe-inline'` er nødvendigt så længe
  UI'et bruger inline-handlers. Værnet mod XSS er escaping plus validering.
- **Data udefra valideres ved indgangen.** Ny `rensEntry()` kontrollerer
  tider mod `HH:MM`, datoer mod `ÅÅÅÅ-MM-DD`, og dag- og special-typer mod
  faste lister; `on` og `cat` renses. Bruges både af filimport og af
  gist-synkronisering — gist-ID'et indtastes manuelt, og appen opfordrer
  selv til at flytte det mellem enheder, så et fremmed ID er en reel vej ind.
  Frokost-overstyringer og den aktive timer valideres tilsvarende.
- Import beder nu om bekræftelse med antal dage og registreringer, og
  afviser filer over 5 MB eller 10.000 poster. ICS-import har fået samme
  størrelsesgrænse plus et loft på 400 dage pr. begivenhed — en fejlagtigt
  eksporteret kalender kunne ellers fryse browseren.
- `gistApi()` validerer ID'et, så en manipuleret værdi i `localStorage` ikke
  kan pege tokenet mod et andet endpoint. Samme mønster bruges nu ved
  indtastning, så appen ikke kan gemme et ID den bagefter nægter at bruge.
- Token-feltet accepterer nu også fine-grained tokens (`github_pat_`), som
  har snævrere rettigheder end de klassiske.

---

## [5.4.0] – 2026-09-27

### Added
- Ikon til appen: et stopur hvor det gule felt er "den registrerede tid".
  Indlejret som data-URI'er i `<head>` (PNG i 16, 32 og 192 px plus et
  180 px apple-touch-icon), så det også virker når filen åbnes lokalt.
  `timer.ico` (16–256 px) til genvejen på Windows' proceslinje.
  Kilde i `ikon/timer-ikon.svg`, genskabes med `ikon/make_icon.py`.

---

## [5.3.0] – 2026-09-08

### Fixed
- Indstillinger sprang tilbage til RAPPORT efter få sekunder.
  `render()` sætter `#main.innerHTML` og slettede dermed modalerne, som lå
  netop dér — og baggrunds-pollen kalder `render()` hvert 10. sekund.
  Modaler ligger nu i `#modalRoot` uden for `#main`, og `safeRender()`
  springer over mens en modal er åben.

## [5.2.0] – 2026-09-08

### Changed
- ON-genveje er tomme som standard, så hver bruger opretter sine egne.
  Eksisterende brugere beholder deres via en engangsmigrering i `loadCfg()`.
- Tom genvejsliste kan nu gemmes; før krævede appen mindst ét nummer.

## [5.1.0] – 2026-09-08

### Fixed
- Indstillinger mistede ugemte indtastninger når man valgte backup-mappe.
  `showSettings()` byggede felterne fra `cfg` i stedet for fra det
  indtastede. En `settingsDraft`-kladde holder nu formen.

## [5.0.0] – 2026-09-08

### Added
- Kom-i-gang-vejledning ved første åbning i lokal tilstand.
- `erLokalFil()` skelner mellem "browseren kan ikke" og "appen kører som
  lokal fil", så mappe-backup forklares korrekt i stedet for at fejle.

## [4.8.0] – 2026-09-07

### Added
- Mappe-backup til kollega-installationer via File System Access API.
  Skriver `timer_data.json` plus ét dateret snapshot pr. dag, og rydder
  snapshots ældre end 60 dage.

## [4.7.0] – 2026-09-07

### Fixed
- Modaler blev strakt over hele skærmen på en PC (`position:fixed` måler
  fra viewporten, ikke fra `body{max-width:480px}`).
- FAB-knapperne lå over modalerne og stjal klik på knapper i højre side.
- `.btn` sætter `width:100%`, hvilket fik slet-knappen i kategorirækker til
  at fylde 1422 px og klemme navnefeltet ned til 23 px.

## [4.5.0] – 2026-09-07

### Added
- Redigerbare indstillinger: arbejdstid pr. ugedag, ON-numre, kategorier
  og frokosttid, gemt pr. enhed i `vct_cfg`.
- Automatisk gist-migrering: appen kan oprette en ny secret gist og flytte
  data derover med brugerens eget token.
- Mappe-backup og lokal tilstand, så kollegaer kan bruge samme fil uden
  GitHub-konto.

### Security
- `GIST_ID` flyttet fra koden til `localStorage`. Repoet er offentligt, så
  ID'et lå frit tilgængeligt og gav adgang til hele timeregistreringen.
  **Bemærk:** det gamle ID står stadig i git-historikken. Oprydningen er
  først færdig når der er oprettet en ny gist og den gamle er slettet.

### Fixed
- `ghLoad()` lod en tom gist overskrive lokale timer. Er gist tom og
  enheden har data, uploades de lokale data i stedet.

---

## [4.1.0] – 2026-09-07 og tidligere

Versionerne før 4.5 blev lagt op via GitHubs web-upload med commit-beskeden
"Add files via upload", så der findes ingen beskrivelse af de enkelte
ændringer. v4.1 fjernede KALENDER-fanen; ferie- og Outlook-import flyttede
til RAPPORT.
