# Requisiti implementativi Web HMI (HTML / CSS / JavaScript) derivati da MIL-STD-1472H

> **Origine:** ogni requisito di [`MIL-STD-1472H-web-HMI-requirements.md`](MIL-STD-1472H-web-HMI-requirements.md) (estratto di MIL-STD-1472H, 15 settembre 2020) è riportato con la **numerazione originale** e accompagnato dai requisiti implementativi derivati.
> **Scopo:** tradurre i requisiti di fattore umano in requisiti verificabili sulle **entità HTML, CSS e JavaScript** di una web application a schermo touch.
> **Lingua:** italiano (i termini tecnici di HTML/CSS/JS e i nomi di classi, id, attributi e funzioni sono in inglese).

## 1. Come leggere il documento

- Ogni requisito originale è presentato come `#### <n. paragrafo> – <titolo>` seguito da una **sintesi** del requisito (la formulazione normativa completa è nell'estratto in inglese) e da un elenco di requisiti implementativi.
- Ogni requisito implementativo ha identificativo **`IMPL-<n. paragrafo>-<nn>`**, un'etichetta di **tecnologia** e una di **area**:
  - Tecnologia: `[HTML]` struttura/semantica/attributi ARIA; `[CSS]` presentazione, layout, stati, animazioni; `[JS]` comportamento, stato, logica, eventi, API del browser. Un requisito che coinvolge più tecnologie ne riporta più di una (es. `[HTML][JS]`).
  - Area: `[Header]`, `[Main]`, `[Footer]` oppure `[Globale]` (trasversale: documento, tema, servizi comuni).
- Le parole **deve / dovrebbe / può** corrispondono a *shall / should / may* del requisito originale da cui derivano; un requisito originale "should" genera requisiti implementativi "dovrebbe" salvo diversa indicazione.
- I valori numerici (soglie, tempi, dimensioni) sono quelli dello standard; dove lo standard demanda la scelta al programma, si indica un **valore di progetto** configurabile in `config.js` (vedi §2.5).
- Ogni requisito originale ha almeno un requisito implementativo; quando un requisito è già soddisfatto da uno comune, il rimando è esplicito (es. «vedi `IMPL-5.1.1.8-01`»).

## 2. Architettura di riferimento della web application

### 2.1 Vincoli generali

| Vincolo | Descrizione |
|---|---|
| Tipo di applicazione | Web application (single page, HTML5 + CSS3 + JavaScript ES2020, **senza dipendenza obbligatoria da framework**; l'uso di un framework non deve alterare le entità sotto descritte). |
| Dispositivo di input primario | **Schermo touch** (tocco con dito), con mouse/tastiera/penna supportati come input secondari tramite Pointer Events. |
| Risoluzione minima | **800 × 600 px** (CSS px, 4:3), in orientamento landscape e portrait (600 × 800). Nessuna funzione primaria può richiedere scroll orizzontale a 800 × 600. |
| Scalabilità | Il layout scala **fluidamente** da 800 × 600 a risoluzioni maggiori (es. 1024 × 768, 1280 × 800, 1920 × 1080, 2560 × 1440 e oltre) senza perdita di funzioni, di leggibilità o di dimensione minima dei target touch. |
| Meta viewport | `<meta name="viewport" content="width=device-width, initial-scale=1">`; lo zoom dell'utente non deve essere disabilitato. |

### 2.2 Aree dell'applicazione

L'applicazione è composta da **tre aree** disposte in una griglia verticale che occupa l'intero viewport (`100dvh`). Il solo `main panel` può scorrere (e solo in verticale); header e footer restano sempre visibili.

```
+-----------------------------------------------------------+
| HEADER      <header id="app-header">    altezza --header-h |
+-----------------------------------------------------------+
| MAIN PANEL  <main id="main-panel">      1fr, scroll interno|
|   #nav-region      (menu / navigazione)                   |
|   #content-region  (viste, form, grafici, tabelle)        |
|   #aux-region      (dettagli / help contestuale)          |
+-----------------------------------------------------------+
| FOOTER      <footer id="app-footer">    altezza --footer-h |
+-----------------------------------------------------------+
```

| Area | Elemento HTML | Contenuto previsto |
|---|---|---|
| **Header** | `<header id="app-header">` | `#app-title` (nome applicazione/vista corrente, `<h1>`), `#mode-indicator` (modalità operativa corrente), `#control-station-indicator` (stazione di controllo), `#user-menu` (utente, ruolo, logout), `#connection-indicator` (stato connessione), `#clock` (data/ora), `#alert-summary` (contatore allarmi attivi/non riconosciuti) e controlli rapidi (`#btn-home`, `#btn-back`, `#btn-help`, `#btn-display-settings`). |
| **Main panel** | `<main id="main-panel">` | `#nav-region` (`<nav>` con menu principale e breadcrumb), `#content-region` (viste applicative: form, tabelle, grafici, mappe, display dinamici, dialoghi), `#aux-region` (pannelli laterali/contestuali: dettagli, help in linea, log messaggi), `#dialog-host` (host dei `<dialog>` modali), `#toast-region` (messaggi transitori), `#alert-region` (banner allarmi/avvisi persistenti, `role="alert"`). |
| **Footer** | `<footer id="app-footer">` | `#status-line` (stato di sistema e feedback di processo, `role="status"`), `#message-line` (ultimo messaggio/avviso/errore), `#progress-indicator`, `#function-keys` (azioni contestuali/scorciatoie touch), `#session-timer` (tempo di sessione/inattività), `#app-version`. |

### 2.3 Layout scalabile (CSS)

- Layout principale: `body { display: grid; grid-template-rows: var(--header-h) minmax(0, 1fr) var(--footer-h); height: 100dvh; margin: 0; }`.
- Unità fluide: `rem`, `%`, `fr`, `clamp()`, `minmax()`; il font-size base è `html { font-size: clamp(16px, 0.9vw + 11px, 24px); }` così che a 800 × 600 sia ≥ 16 px e cresca con la risoluzione.
- Breakpoint di riferimento (min-width, in CSS px): **S** 800 (base, mobile-first), **M** 1024, **L** 1280, **XL** 1920, **XXL** 2560. Sotto 800 px di larghezza (non obbligatorio) il layout degrada a colonna singola mantenendo le stesse aree.
- Orientamento: `@media (orientation: landscape|portrait)`; il layout `#nav-region`/`#content-region`/`#aux-region` è a tre colonne in landscape ≥ M, a due colonne (nav + content, aux come pannello a comparsa) a 800 px, in colonna singola in portrait.
- Nessun testo né controllo deve essere ridotto con `transform: scale()` o `zoom` per adattarsi alla risoluzione; la scala si ottiene con `rem` e `clamp()`.

### 2.4 Token di progetto comuni (CSS custom properties su `:root`)

| Token | Significato / valore di partenza |
|---|---|
| `--px-per-mm` | Pixel CSS per millimetro fisico; default `3.78` (96 dpi di riferimento), tarabile da `config.js` in base alla densità reale dello schermo. |
| `--touch-target-min` | `calc(15 * var(--px-per-mm))` – dimensione minima dell'area di attuazione (A) dei controlli (tabella *Touch* di 5.1.3.1.17: 15 × 15 mm minimo, 38 × 38 mm massimo, 16 × 16 mm preferito per tastiere). |
| `--touch-target-max` | `calc(38 * var(--px-per-mm))`. |
| `--touch-gap-min` | `calc(3 * var(--px-per-mm))` (separazione minima 3 mm; 5 mm con strategia *first contact*); `--touch-gap-max`: 6 mm. |
| `--header-h`, `--footer-h` | `clamp(3.5rem, 8vh, 5rem)` e `clamp(2.5rem, 6vh, 4rem)`. |
| `--font-family-ui` | `Arial, Verdana, Helvetica, sans-serif` (font standard sans serif). |
| `--fs-body`, `--fs-small`, `--fs-title` | Dimensioni testo derivate dal font-size base (rem). |
| `--color-*` | Palette semantica: `--color-bg`, `--color-fg`, `--color-critical` (rosso), `--color-alarm` (arancione), `--color-warning` (giallo), `--color-normal` (verde), `--color-advisory` (ciano/blu), `--color-inactive` (grigio), `--color-focus`, `--color-selected`. Ciascun colore semantico è sempre accompagnato da icona/forma/testo (doppia codifica). |
| `--contrast-*` | Temi `theme-light`, `theme-dark`, `theme-high-contrast` applicati con `data-theme` su `<html>`; rapporto di contrasto testo/sfondo ≥ 6:1 (obiettivo ≥ 10:1). |
| `--z-*` | Livelli di sovrapposizione: `--z-header`, `--z-dialog`, `--z-alert`, `--z-toast`. |

### 2.5 Moduli JavaScript comuni e configurazione

Le seguenti entità JS sono *servizi comuni* richiamati dai requisiti implementativi (moduli ES `type="module"`; stato centrale immutabile con eventi `CustomEvent`):

| Modulo | Responsabilità |
|---|---|
| `config.js` | Valori di progetto configurabili (tempi di risposta, timeout di sessione/inattività, soglie, `pxPerMm`, lingua, ruoli, tabelle di codifica colori). |
| `state.js` | Stato applicativo (modalità operativa, vista corrente, utente/ruolo, stazione di controllo, connessione, allarmi). Unica fonte di verità per i display. |
| `feedback.js` | `acknowledge(el)`, `setStatus(msg, level)`, `showProgress()`, `beep(type)`: feedback visivo/sonoro/aptico (`navigator.vibrate`) entro i tempi di Tabella V. |
| `notify.js` | Coda messaggi/avvisi/allarmi con priorità, riconoscimento, storico (`#alert-region`, `#toast-region`, `#message-line`). |
| `dialog.js` | Gestione dialoghi modali/non modali su `<dialog>`, conferme, annullamento, closure. |
| `validate.js` | Validazione dati in ingresso (tipo, formato, intervallo), messaggi di errore e correzione. |
| `auth.js` | Login/logout, ruoli, password, blocco account, timeout di inattività. |
| `nav.js` | Navigazione, cronologia, back/home, breadcrumb, scorciatoie. |
| `prefs.js` | Preferenze utente (tema, dimensione testo, luminosità/contrasto, audio, orientamento) in `localStorage`. |
| `i18n.js` | Testi, unità di misura e formati data/ora/numero localizzati. |
| `net.js` | Comunicazione con il back-end (`fetch`/WebSocket), stato di connessione, ritentativi, trasmissione dati. |
| `log.js` | Registrazione azioni utente ed eventi di sicurezza (inoltrata al server). |

### 2.6 Convenzioni trasversali

- **Accessibilità**: HTML semantico, WAI-ARIA (`role`, `aria-live`, `aria-label`, `aria-describedby`, `aria-invalid`, `aria-current`), ordine di tabulazione logico, `:focus-visible` sempre visibile.
- **Input**: Pointer Events (`pointerdown/up/cancel`) per touch/mouse/penna; strategia di attuazione *last contact* (azione al `pointerup` sul target) salvo diversa indicazione; `touch-action: manipulation` sui controlli; nessuna funzione solo-hover; nessun gesto multi-dito come unico mezzo.
- **Tempi di risposta (Tabella V, Appendice A dell'estratto)**: risposta al tocco/click ≤ 0,1 s; eco carattere ≤ 0,2 s; errore ≤ 0,2 s; aggiornamento locale ≤ 0,5 s; cambio pagina ≤ 1,0 s; aggiornamento da host ≤ 2,0 s; aggiornamento da file ≤ 10 s. Oltre 1 s si mostra un indicatore di avanzamento.
- **Stato visivo dei controlli**: classi/attributi `:hover` (solo mouse), `:focus-visible`, `:active`, `[aria-pressed]`, `[aria-selected]`, `[aria-current]`, `[disabled]`/`[aria-disabled]`, `.is-selected`, `.is-busy`.
- **Test di accettazione**: ogni requisito implementativo è verificabile per *ispezione* del DOM/CSS (I), *test automatico* (T, es. Playwright/Jest) o *dimostrazione* manuale (D) su viewport 800 × 600, 1024 × 768 e 1920 × 1080.

## 3. Requisiti implementativi per requisito originale

Le sezioni 3.1–3.16 ricalcano le sezioni 1–16 dell'estratto dei requisiti. Il documento copre tutti i **866** requisiti originali dell'estratto e ne deriva **957** requisiti implementativi (`IMPL-…`).

### 3.1 Principi generali di progetto

Criteri trasversali applicabili a qualunque HMI, inclusa una web HMI; per i requisiti di natura più processuale/organizzativa si derivano vincoli architetturali e criteri di revisione verificabili.

#### 4.5.2 – Applicazione
*Sintesi (shall/should/may):* I criteri dello standard devono (shall) essere applicati alla progettazione di hardware, software, sistemi, apparecchiature e strutture con interfaccia o interazione umana, incluse operazione, manutenzione, addestramento e supporto.

- **IMPL-4.5.2-01** `[HTML][JS]` `[Globale]` Tutte le viste applicative (operazione, manutenzione, configurazione, help/training) devono riusare le stesse aree (`#app-header`/`#main-panel`/`#app-footer`) e gli stessi moduli comuni (`state.js`, `config.js`) di 00-head.md §2, senza pattern UI alternativi.
- **IMPL-4.5.2-02** `[JS]` `[Globale]` Criterio di revisione/test di accettazione (I): checklist di code review che verifichi, per ogni nuova vista, la conformità alle sezioni 2.1-2.6 prima del merge.

#### 4.6.1 – Fail-safe design
*Sintesi:* Deve essere garantito (shall) un design fail-safe nelle aree dove un guasto può causare danni alle apparecchiature, lesioni al personale o azionamento accidentale di apparecchiature critiche.

- **IMPL-4.6.1-01** `[JS]` `[Main]` `validate.js` deve richiedere conferma esplicita via `dialog.js` (modale) prima di eseguire azioni distruttive/irreversibili su funzioni critiche, con l'opzione "annulla" preselezionata.
- **IMPL-4.6.1-02** `[JS]` `[Globale]` `net.js` deve gestire gli errori di comunicazione portando lo stato applicativo a una modalità sicura nota (fallback read-only via `state.js`) e segnalarlo in `#alert-region`, invece di eseguire azioni indeterminate.

#### 4.6.2 – Remote and local control
*Sintesi:* Per funzioni di controllo/monitoraggio critiche devono (shall) esistere funzioni normali e di backup, commutabili in qualsiasi momento secondo un protocollo definito; deve essere indicata la stazione che detiene il controllo sia alla stazione normale sia a quella di backup.

- **IMPL-4.6.2-01** `[HTML][JS]` `[Header]` `#control-station-indicator` deve mostrare in tempo reale quale stazione (normale/backup) detiene il controllo, aggiornato da `state.js` tramite `CustomEvent` al cambio di controllo.
- **IMPL-4.6.2-02** `[JS]` `[Globale]` `state.js`/`nav.js` devono esporre una funzione `handoverControl()` che consenta alla stazione di backup di assumere il controllo in qualsiasi momento, registrata da `log.js`.
- **IMPL-4.6.2-03** `[HTML][CSS]` `[Header]` Lo stato "in controllo"/"in attesa" in `#control-station-indicator` deve essere ridondante (testo + icona + colore semantico `--color-*`), senza ambiguità percettiva.

#### 4.7.1 – Equipment simplicity
*Sintesi:* L'apparecchiatura deve (shall) rappresentare il design più semplice coerente con i requisiti funzionali e le condizioni operative attese.

- **IMPL-4.7.1-01** `[HTML][JS]` `[Globale]` Criterio architetturale/test di accettazione (I): ogni nuova vista/componente deve riusare le entità già definite (§2.2, §2.5) ed evitare dipendenze da framework non necessarie (vedi §2.1).
- **IMPL-4.7.1-02** `[HTML]` `[Main]` I form di `#content-region` devono esporre solo i controlli necessari al compito corrente, con opzioni avanzate raggruppate in sezioni collassabili (`<details>`) non visibili di default.

#### 4.9.1 – System and personnel safety factors
*Sintesi:* Il design deve (shall) riflettere i fattori di sicurezza del sistema e del personale, minimizzando l'errore umano in condizioni di allerta, stress operativo o emergenza.

- **IMPL-4.9.1-01** `[JS]` `[Globale]` `notify.js` deve dare priorità e persistenza maggiori (banner `#alert-region` senza auto-dismiss) ai messaggi di livello critico/allarme rispetto a quelli informativi durante condizioni di emergenza.
- **IMPL-4.9.1-02** `[JS]` `[Main]` `validate.js`/`dialog.js` devono richiedere conferma a due passaggi per i comandi critici anche in condizioni di carico elevato (più allarmi attivi), per minimizzare l'errore umano.

#### 4.13.1 – Automation of functions
*Sintesi:* Le funzioni devono (shall) essere automatizzate solo per ottenere maggiore efficacia, efficienza, affidabilità, semplicità, economia e sicurezza del sistema complessivo, non per affidarsi solo alle prestazioni umane.

- **IMPL-4.13.1-01** `[JS]` `[Globale]` Criterio di progetto/revisione (I): ogni automatismo introdotto in `state.js` deve essere documentato con la relativa motivazione (efficacia/efficienza/sicurezza), verificabile in review.
- **IMPL-4.13.1-02** `[HTML][JS]` `[Main]` Le funzioni automatizzate devono esporre un controllo esplicito di attivazione/disattivazione visibile in `#content-region` (`[aria-pressed]`), senza attivazioni implicite.

#### 4.13.2 – Human involvement
*Sintesi:* Indipendentemente dal livello di automazione, il design deve (shall) garantire che l'utente mantenga il comando, resti coinvolto nelle operazioni in corso e sia informato per mantenere consapevolezza situazionale.

- **IMPL-4.13.2-01** `[HTML][JS]` `[Main]` Ogni funzione automatizzata deve pubblicare il proprio stato/azioni correnti tramite `state.js` verso `#aux-region` (pannello "attività automatizzate"), con `aria-live="polite"`.
- **IMPL-4.13.2-02** `[JS]` `[Globale]` Le funzioni automatizzate devono esporre un comando override/stop sempre raggiungibile da tastiera e touch (`--touch-target-min`), gestito da `feedback.js` con conferma immediata.

#### 4.13.3 – Automated function characteristics
*Sintesi:* Le funzioni automatizzate devono (shall) minimizzare le sorprese, offrire un'adeguata gamma di opzioni, monitorare le azioni dell'utente per minimizzare/resistere/tollerare errori ed essere annullabili dall'utente.

- **IMPL-4.13.3-01** `[JS]` `[Globale]` `notify.js` deve notificare in anticipo (preavviso configurabile in `config.js`) ogni cambio di stato automatico rilevante, evitando transizioni silenziose.
- **IMPL-4.13.3-02** `[JS]` `[Main]` `validate.js` deve intercettare azioni dell'utente in conflitto con un automatismo attivo e richiedere conferma prima di procedere.
- **IMPL-4.13.3-03** `[HTML][JS]` `[Main]` Ogni funzione automatizzata deve esporre un pulsante "Annulla/Override" (≥ `--touch-target-min`) sempre attivo durante l'esecuzione.

#### 4.13.4 – Indication of operating mode
*Sintesi:* Deve (shall) essere fornita in ogni momento un'indicazione chiara della modalità operativa correntemente selezionata.

- **IMPL-4.13.4-01** `[HTML][JS]` `[Header]` `#mode-indicator` deve mostrare sempre (non su richiesta) la modalità operativa corrente, aggiornato da `state.js` tramite evento `modechange`.
- **IMPL-4.13.4-02** `[CSS]` `[Header]` `#mode-indicator` deve codificare la modalità con testo + colore semantico (`--color-*`) + icona, mai solo colore, garantendo la doppia codifica.

#### 4.14 – Functional use of color
*Sintesi:* Dove non in conflitto con i codici colore specificati, i colori usati per scopi funzionali devono (shall) essere accessibili a utenti con deficit della visione dei colori.

- **IMPL-4.14-01** `[CSS]` `[Globale]` Ogni uso funzionale del colore (`--color-critical`/`--color-alarm`/`--color-warning`/`--color-normal`/`--color-advisory`/`--color-inactive`) deve essere affiancato da icona/forma/testo (doppia codifica), come da 00-head.md §2.4.
- **IMPL-4.14-02** `[JS][CSS]` `[Globale]` `prefs.js` deve offrire una modalità "daltonismo" con palette alternativa idonea per deuteranopia/protanopia, selezionabile e persistita in `localStorage`.

#### 4.16 – System integration
*Sintesi:* Il design deve (shall) essere compatibile con tutti i sistemi che l'utente deve utilizzare, mantenere o supportare.

- **IMPL-4.16-01** `[JS]` `[Globale]` `net.js` deve esporre un'interfaccia di integrazione basata su standard web (`fetch`/WebSocket, JSON) documentata, senza dipendenze proprietarie non standard.
- **IMPL-4.16-02** `[JS]` `[Globale]` Test di accettazione (D): dimostrazione di interoperabilità (login, scambio dati) con i sistemi esterni di riferimento elencati in `config.js` prima del rilascio.

#### 5.17.1.1 – Functional interface
*Sintesi:* I programmi software e le interfacce delle apparecchiature devono (shall) fornire un'interfaccia funzionale tra il sistema per cui sono progettati e i suoi utenti.

- **IMPL-5.17.1.1-01** `[HTML][JS]` `[Main]` `#content-region` deve esporre, per ciascuna funzione di sistema prevista, una vista/form dedicata collegata a `state.js`, senza funzioni di sistema prive di controllo/visualizzazione in UI.

#### 5.17.1.2 – Compatible with personnel
*Sintesi:* Le interfacce devono (shall) essere compatibili con il lavoro svolto e con i processi cognitivi del personale.

- **IMPL-5.17.1.2-01** `[HTML][JS]` `[Main]` Il flusso di navigazione (`nav.js`, breadcrumb in `#nav-region`) deve rispecchiare la sequenza dei compiti operativi dell'utente (IA task-based), verificato con test con utenti/esperti di dominio (D).
- **IMPL-5.17.1.2-02** `[JS]` `[Globale]` `i18n.js` deve usare terminologia e unità di misura del dominio operativo dell'utente, configurabili per ruolo/profilo.

#### 5.17.1.3 – Human performance
*Sintesi:* L'interfaccia dovrebbe (should) minimizzare le condizioni che possono degradare le prestazioni umane o contribuire all'errore umano.

- **IMPL-5.17.1.3-01** `[JS]` `[Main]` `validate.js` dovrebbe segnalare in tempo reale (inline, non solo al submit) gli input che possano condurre a errore, con messaggi correttivi (vedi `IMPL-5.1.2.1.6.11-01`).
- **IMPL-5.17.1.3-02** `[CSS][HTML]` `[Globale]` I controlli ad alto rischio di errore (azioni distruttive) dovrebbero essere visivamente distinti (colore, posizione separata) dai controlli di routine.

#### 5.17.1.4 – Display content
*Sintesi:* Le interfacce forniscono un'interfaccia funzionale tra sistema e utenti; dovrebbero (should) ottimizzare la compatibilità con il personale e minimizzare le condizioni che degradano le prestazioni o contribuiscono all'errore.

- **IMPL-5.17.1.4-01** `[HTML][JS]` `[Main]` Vedi `IMPL-5.17.1.1-01` e `IMPL-5.17.1.3-01`; in aggiunta `#content-region` deve mostrare solo i dati rilevanti al compito corrente (progressive disclosure), configurabile da `state.js` in base al ruolo utente.

### 3.2 Feedback, stato del sistema e tempi di risposta

I valori di riferimento sono quelli della Tabella V (Appendice A, riportati in 00-head.md §2.6): risposta al tocco/click ≤ 0,1 s; eco carattere ≤ 0,2 s; errore ≤ 0,2 s; aggiornamento locale ≤ 0,5 s; cambio pagina ≤ 1,0 s; aggiornamento da host ≤ 2,0 s; aggiornamento da file ≤ 10 s; oltre 1 s va mostrato un indicatore di avanzamento.

#### 5.1.1.8 – Feedback
*Sintesi:* Non deve (shall) esserci ritardo percepibile tra un cambiamento di una condizione di sistema controllata/monitorata e la sua indicazione su un display.

- **IMPL-5.1.1.8-01** `[JS]` `[Globale]` `state.js` deve propagare ogni cambiamento di stato monitorato ai display interessati entro la soglia "aggiornamento locale" (≤ 0,5 s, Tabella V) tramite `CustomEvent` sincroni.
- **IMPL-5.1.1.8-02** `[JS]` `[Header]` `net.js` deve propagare gli aggiornamenti ricevuti dall'host entro ≤ 2,0 s (Tabella V) e segnalare un ritardo superiore come anomalia in `#connection-indicator`.

#### 5.1.1.8.1 – Unavoidable time lag
*Sintesi:* Se un ritardo tra l'azionamento del controllo e lo stato finale del sistema è inevitabile, il sistema deve (shall) fornire feedback immediato su processo e direzione del cambiamento di parametro.

- **IMPL-5.1.1.8.1-01** `[JS][HTML]` `[Main]` `feedback.js` deve mostrare immediatamente (≤ 0,1 s, Tabella V) un indicatore "in corso" con direzione/andamento del parametro (es. freccia, barra) ogni volta che un comando ha effetto non istantaneo.

#### 5.1.1.8.2 – Feedback indication
*Sintesi:* Il feedback deve (shall) indicare senza ambiguità, incertezza o errore che il controllo è attuato correttamente, che la risposta desiderata è ottenuta e quando è completa.

- **IMPL-5.1.1.8.2-01** `[JS]` `[Globale]` `feedback.js:acknowledge(el)` deve applicare in sequenza stati distinti e univoci (`.is-busy` → `.is-success`/`.is-error` → stato finale), ciascuno con testo + icona, senza stati intermedi ambigui.

#### 5.1.1.8.3 – Critical control functions
*Sintesi:* Le funzioni di controllo critiche, come quelle da tastiera, devono (shall) fornire feedback prima dell'invio per garantire che l'inserimento sia privo di errori e corrisponda a quanto desiderato.

- **IMPL-5.1.1.8.3-01** `[JS][HTML]` `[Main]` `dialog.js` deve presentare una conferma con riepilogo del valore/comando inserito prima dell'invio per ogni controllo marcato `data-critical="true"`.

#### 5.1.2.1.1 – Relationship
*Sintesi:* Le relazioni tra un controllo e il relativo display, e viceversa, devono (shall) essere immediatamente evidenti e non ambigue per l'utente.

- **IMPL-5.1.2.1.1-01** `[HTML][CSS]` `[Main]` Ogni controllo e il display associato devono condividere un contenitore (`<fieldset>`/`<section>` con `aria-labelledby`) e prossimità visiva in `#content-region`.

#### 5.1.2.1.3 – Design
*Sintesi:* Le relazioni controllo/display devono (shall) essere rese evidenti tramite prossimità, somiglianza dei raggruppamenti, codifica, inquadramento, etichettatura e tecniche simili.

- **IMPL-5.1.2.1.3-01** `[CSS][HTML]` `[Main]` I gruppi funzionalmente correlati devono usare bordo/`<fieldset>` comune, `<label>` espliciti e la stessa classe di codifica colore (`--color-*`, 00-head.md §2.4).

#### 5.1.2.1.4 – Complexity and precision
*Sintesi:* La complessità e la precisione richieste per manipolare i controlli e monitorare i display devono (shall) essere coerenti con la precisione richiesta dal sistema.

- **IMPL-5.1.2.1.4-01** `[HTML][JS]` `[Main]` `validate.js` deve definire per ogni campo numerico `step`/`min`/`max` coerenti con la precisione operativa richiesta dal dominio, senza cifre decimali eccedenti.

#### 5.1.2.1.5 – User ability
*Sintesi:* La complessità/precisione di controllo e display non deve (shall not) eccedere la capacità dell'utente di discriminare i dettagli o manipolare i controlli nelle condizioni dinamiche attese.

- **IMPL-5.1.2.1.5-01** `[CSS]` `[Globale]` I target touch devono rispettare `--touch-target-min` (15×15 mm) anche nelle viste a maggiore densità informativa, senza ridurli sotto soglia.
- **IMPL-5.1.2.1.5-02** `[JS]` `[Main]` `prefs.js` deve permettere un profilo "precisione ridotta" (testo/controlli ingranditi) senza rompere il layout (vedi §2.3).

#### 5.1.2.1.6.1 – Use
*Sintesi:* Deve (shall) essere fornito un feedback che presenti informazioni di stato, conferma e verifica dell'input durante tutta l'interazione con il sistema.

- **IMPL-5.1.2.1.6.1-01** `[JS][HTML]` `[Footer]` `#status-line` (`role="status"`) deve riportare, tramite `feedback.js:setStatus(msg, level)`, lo stato/conferma di ogni azione utente rilevante.

#### 5.1.2.1.6.2 – System status
*Sintesi:* Agli utenti deve (shall) essere sempre fornita l'informazione sullo stato del sistema riguardo modalità operative e disponibilità, automaticamente o su richiesta.

- **IMPL-5.1.2.1.6.2-01** `[HTML][JS]` `[Header]` `#mode-indicator` e `#connection-indicator` devono mostrare continuamente modalità operativa e disponibilità; `#btn-help` deve permettere di richiamarne il dettaglio on-demand in `#aux-region`.

#### 5.1.2.1.6.3 – Computer response
*Sintesi:* Ogni input dell'utente deve (shall) produrre una risposta percepibile e coerente dal computer; se non c'è feedback visivo intenzionale per un input non valido, deve essere prodotta una forma alternativa (es. suono) per garantirne il riconoscimento.

- **IMPL-5.1.2.1.6.3-01** `[JS]` `[Globale]` `feedback.js` deve generare una risposta percepibile (visiva e/o `beep('error')`) entro ≤ 0,1 s da ogni interazione (Tabella V).
- **IMPL-5.1.2.1.6.3-02** `[JS]` `[Globale]` Quando un input non valido non produce feedback visivo, `feedback.js:beep('invalid')` deve fungere da canale alternativo, rispettando le preferenze audio di `prefs.js`.

#### 5.1.2.1.6.4 – System response time
*Sintesi:* I tempi di risposta massimi per sistemi real-time (es. fire control, comando e controllo) non devono (shall) eccedere i valori di Tabella V; per teleoperazioni i tempi accettabili dovrebbero (should) essere specificati dal programma di acquisizione.

- **IMPL-5.1.2.1.6.4-01** `[JS]` `[Globale]` `config.js` deve definire i tempi massimi di Tabella V (0,1/0,2/0,2/0,5/1,0/2,0/10 s) come soglie verificabili da test automatico (T).
- **IMPL-5.1.2.1.6.4-02** `[JS]` `[Globale]` Per le viste di teleoperazione, `net.js` deve esporre una soglia configurabile separatamente in `config.js` (valore di progetto), distinta da quelle standard di Tabella V.

#### 5.1.2.1.6.4.1 – Non-real-time systems
*Sintesi:* I sistemi non real-time possono (may) consentire tempi di risposta rilassati.

- **IMPL-5.1.2.1.6.4.1-01** `[JS]` `[Globale]` `config.js` deve permettere di marcare singole viste/funzioni come `realtime: false`, applicando soglie di risposta rilassate (valore di progetto) rispetto a Tabella V.

#### 5.1.2.1.6.4.2 – Existing or predecessor systems
*Sintesi:* I tempi di risposta per sistemi real-time e non real-time non devono (shall) eccedere il tempo di risposta del sistema esistente o predecessore equivalente.

- **IMPL-5.1.2.1.6.4.2-01** `[JS]` `[Globale]` `config.js` deve registrare, per funzione, il tempo di risposta del sistema predecessore (se noto) come limite oltre il quale il test di performance automatico (T) fallisce.

#### 5.1.2.1.6.4.3 – Response time greater than 1 second
*Sintesi:* Se il tempo di risposta del computer supererà 1 secondo, l'utente deve (shall) ricevere un messaggio che indica che il sistema sta elaborando (per sistemi automatizzati remoti vedi 5.12).

- **IMPL-5.1.2.1.6.4.3-01** `[JS][HTML]` `[Footer]` `net.js`/`feedback.js` devono mostrare in `#status-line`/`#progress-indicator` il messaggio "elaborazione in corso" non appena un'operazione supera 1,0 s senza completarsi.

#### 5.1.2.1.6.5 – Task performance time
*Sintesi:* Il tempo per completare accuratamente un'azione/sequenza standard time-sensitive (incluso il tempo di risposta del sistema) non deve (shall) eccedere quello per le stesse azioni sul sistema esistente o predecessore equivalente.

- **IMPL-5.1.2.1.6.5-01** `[JS]` `[Globale]` Test di accettazione (T): per i task critici elencati in `config.js`, un test automatico Playwright deve misurare il tempo end-to-end e confrontarlo con il limite del sistema predecessore registrato in `config.js`.

#### 5.1.2.1.6.6 – Time-consuming processes
*Sintesi:* Il sistema deve (shall) fornire un'informazione di avviso quando viene invocato un comando che richiederà tempo lungo o uso intensivo di risorse.

- **IMPL-5.1.2.1.6.6-01** `[JS][HTML]` `[Main]` `dialog.js` deve mostrare un avviso preventivo (tempo stimato, risorse) prima di avviare operazioni marcate `data-long-running="true"` in `config.js`.

#### 5.1.2.1.6.7 – Aborting time-consuming processes
*Sintesi:* Agli utenti deve (shall) essere fornita un'opzione per interrompere i processi time-consuming.

- **IMPL-5.1.2.1.6.7-01** `[HTML][JS]` `[Footer]` `#progress-indicator` deve includere un pulsante "Annulla" (≥ `--touch-target-min`) collegato a una funzione `abort()` esposta da `net.js`/`state.js`.

#### 5.1.2.1.6.8 – Stand-by
*Sintesi:* Quando il funzionamento del sistema richiede l'attesa dell'utente, un messaggio o un'icona appropriata deve (shall) essere visualizzata finché l'interazione non è di nuovo possibile.

- **IMPL-5.1.2.1.6.8-01** `[JS][HTML]` `[Footer]` `feedback.js:showProgress()` deve attivare in `#status-line`/`#progress-indicator` un messaggio/icona "attendere" e impostare `aria-busy="true"` sui controlli bloccati.

#### 5.1.2.1.6.8.1 – Delays in excess of 1 second
*Sintesi:* Quando il ritardo supera 1 secondo, l'utente deve (shall) essere informato.

- **IMPL-5.1.2.1.6.8.1-01** `[JS]` `[Footer]` Vedi `IMPL-5.1.2.1.6.4.3-01`: la stessa soglia di 1,0 s in `config.js` deve attivare il messaggio in `#status-line`.

#### 5.1.2.1.6.8.2 – Delays in excess of 10 seconds
*Sintesi:* Per ritardi superiori a 10 secondi, un display di conto alla rovescia deve (shall) mostrare il tempo di ritardo rimanente.

- **IMPL-5.1.2.1.6.8.2-01** `[JS][HTML]` `[Footer]` Oltre la soglia di 10 s (Tabella V, "aggiornamento da file") definita in `config.js`, `#progress-indicator` deve mostrare un conto alla rovescia (mm:ss) aggiornato ogni secondo da `feedback.js`.

#### 5.1.2.1.6.9 – Startup
*Sintesi:* Quando il sistema non è immediatamente disponibile dopo l'avvio, deve (shall) fornire feedback sul tempo medio di risposta o i periodi noti di indisponibilità, e tastiera/puntatore devono essere disabilitati finché l'avvio non è completo.

- **IMPL-5.1.2.1.6.9-01** `[JS][HTML]` `[Main]` Durante l'avvio, l'applicazione deve mostrare un overlay di caricamento con il tempo medio stimato (da `config.js`) e impostare `inert`/`disabled` sui controlli interattivi finché `state.js` non segnala `app:ready`.

#### 5.1.2.1.6.10 – Input confirmation
*Sintesi:* La conferma degli input non deve (shall not) causare la rimozione dei dati visualizzati.

- **IMPL-5.1.2.1.6.10-01** `[JS]` `[Main]` `dialog.js:confirm()` non deve azzerare né nascondere i valori del form sottostante; i dati inseriti restano visibili in `#content-region` dopo la conferma.

#### 5.1.2.1.6.11 – User input rejection
*Sintesi:* Se il sistema rifiuta un input dell'utente, deve (shall) essere fornito un feedback che indichi il motivo del rifiuto e l'azione correttiva richiesta.

- **IMPL-5.1.2.1.6.11-01** `[JS][HTML]` `[Main]` `validate.js` deve impostare `aria-invalid="true"` e un messaggio esplicito collegato via `aria-describedby` che indichi motivo del rifiuto e correzione richiesta.

#### 5.1.2.1.6.12 – Highlighted option selection
*Sintesi:* Qualsiasi messaggio o dato selezionato come opzione o input al sistema deve (shall) essere evidenziato per indicare il riconoscimento da parte del sistema.

- **IMPL-5.1.2.1.6.12-01** `[CSS][HTML]` `[Main]` Gli elementi selezionati devono ricevere la classe `.is-selected` e/o `[aria-selected="true"]` con stile distintivo (`--color-selected`) non appena riconosciuti dal sistema.

#### 5.1.2.1.6.13 – Process outcome
*Sintesi:* Quando un processo o sequenza di controllo è completato o interrotto dal sistema, deve (shall) essere presentata un'indicazione positiva dell'esito e dei requisiti per l'azione successiva dell'utente.

- **IMPL-5.1.2.1.6.13-01** `[JS][HTML]` `[Footer]` `notify.js` deve inviare a `#message-line`/`#toast-region` un messaggio di esito (successo/interruzione) con l'eventuale azione successiva richiesta.

#### 5.1.2.1.6.14 – Feedback message content
*Sintesi:* Gli utenti non devono (shall not) essere tenuti a tradurre i messaggi di feedback usando un sistema di riferimento o fogli di codice.

- **IMPL-5.1.2.1.6.14-01** `[JS]` `[Globale]` `i18n.js` deve fornire i testi dei messaggi di feedback in linguaggio naturale completo (nessun codice/sigla non risolta), recuperabile tramite chiave di traduzione.

#### 5.1.2.1.6.15 – Abbreviations
*Sintesi:* Le abbreviazioni dovrebbero (should) essere evitate nei messaggi di feedback a meno che non siano ben note a tutti i potenziali utenti.

- **IMPL-5.1.2.1.6.15-01** `[JS]` `[Globale]` Criterio di revisione (I): il dizionario testi di `i18n.js` deve essere verificato in review per assenza di abbreviazioni non standard nei messaggi di feedback.

#### 5.1.2.3.1 – Lack of ambiguity
*Sintesi:* Gli indicatori del display devono (shall) dirigere e guidare in modo chiaro e univoco la risposta di controllo appropriata.

- **IMPL-5.1.2.3.1-01** `[HTML][CSS]` `[Main]` Ogni indicatore che richiede un'azione (allarme, campo non valido) deve includere un'etichetta testuale esplicita dell'azione richiesta, non solo icona/colore.

#### 5.1.2.3.2 – Response to control movements
*Sintesi:* La risposta di un display ai movimenti del controllo deve (shall) essere coerente, prevedibile e compatibile con le aspettative dell'utente.

- **IMPL-5.1.2.3.2-01** `[JS]` `[Main]` I controlli con display collegato (slider, stepper) devono aggiornare il display in modo monotono e coerente con la direzione del gesto (`pointermove`), senza inversioni o salti.

#### 5.1.2.3.3 – Display response time
*Sintesi:* Non deve (shall) esserci ritardo percepibile tra un cambiamento di una condizione real-time controllata/monitorata e la sua indicazione sul display (vedi 5.1.2.1.6.4).

- **IMPL-5.1.2.3.3-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.1.8-01`; per le viste real-time, `state.js` deve aggiornare il display associato entro la soglia "aggiornamento locale" ≤ 0,5 s di Tabella V.

#### 5.1.2.3.4 – Time lag
*Sintesi:* Il ritardo tra la risposta del sistema a un input di controllo e la presentazione sul display di tale risposta deve (shall) essere minimizzato, compatibilmente con un funzionamento sicuro ed efficace.

- **IMPL-5.1.2.3.4-01** `[JS]` `[Globale]` Test di accettazione (T): `net.js` deve misurare e registrare (`log.js`) la latenza comando→display per le funzioni critiche, con soglia di allarme configurabile in `config.js`.

#### 5.17.9.1 – Feedback
*Sintesi:* Deve (shall) essere fornito feedback che presenti stato, conferma e verifica dell'input durante l'interazione col sistema (vedi 5.1.2.1.6.1).

- **IMPL-5.17.9.1-01** `[JS][HTML]` `[Footer]` Vedi `IMPL-5.1.2.1.6.1-01`; `#status-line` deve restare la fonte unica anche per le interfacce di cybersecurity/amministrazione (login, gestione ruoli).

#### 5.17.9.2 – System status
*Sintesi:* Le informazioni sullo stato del sistema su modalità operative e disponibilità devono (shall) essere accessibili agli utenti, automaticamente o su richiesta.

- **IMPL-5.17.9.2-01** `[HTML][CSS]` `[Header]` `#app-header` (con `#mode-indicator`, `#connection-indicator`) deve restare visibile anche quando un dialogo modale è aperto in `#dialog-host`, poiché il layout a griglia (§2.2) mantiene header e footer sempre presenti.

#### 5.17.9.3 – Computer responses
*Sintesi:* Ogni input dell'utente deve (shall) produrre una risposta percepibile e coerente dal computer (vedi 5.1.2.1.6.3).

- **IMPL-5.17.9.3-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.3-01`.

#### 5.17.9.4 – Alternative form
*Sintesi:* Se il feedback non è presentato visivamente, deve (shall) essere fornita una forma alternativa di feedback (es. suono).

- **IMPL-5.17.9.4-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.3-02`; `feedback.js:beep(type)` deve coprire anche i casi di feedback non visivo nelle schermate di cybersecurity (es. blocco account).

#### 5.17.9.5 – System response time
*Sintesi:* I tempi di risposta massimi per sistemi critici (fire control, comando e controllo) non devono (shall) eccedere i valori di Tabella V.

- **IMPL-5.17.9.5-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.4-01`.

#### 5.17.9.5.1 – Non-critical systems
*Sintesi:* I sistemi non critici possono (may) consentire tempi di risposta rilassati.

- **IMPL-5.17.9.5.1-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.4.1-01`.

#### 5.17.9.5.2 – Existing or predecessor systems
*Sintesi:* I tempi di risposta per sistemi critici e non critici non devono (shall) eccedere quelli dei sistemi esistenti o predecessori equivalenti.

- **IMPL-5.17.9.5.2-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.4.2-01`.

#### 5.17.9.5.3 – System processing indication
*Sintesi:* Quando i tempi di risposta del computer supereranno 1 secondo, il sistema deve (shall) fornire un messaggio che indica che sta elaborando (per sistemi automatizzati remoti vedi 5.12).

- **IMPL-5.17.9.5.3-01** `[JS][HTML]` `[Footer]` Vedi `IMPL-5.1.2.1.6.4.3-01`.

#### 5.17.9.6 – Task performance time
*Sintesi:* Il tempo per completare accuratamente un'azione/sequenza time-sensitive (inclusi i tempi di risposta) non deve (shall) eccedere quello dei sistemi esistenti o predecessori equivalenti.

- **IMPL-5.17.9.6-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.5-01`.

#### 5.17.9.7 – Time-consuming processes
*Sintesi:* Il sistema deve (shall) fornire informazioni di avviso quando viene invocato un comando che richiederà tempo lungo o uso intensivo di risorse.

- **IMPL-5.17.9.7-01** `[JS][HTML]` `[Main]` Vedi `IMPL-5.1.2.1.6.6-01`.

#### 5.17.9.8 – Aborting time-consuming processes
*Sintesi:* Il sistema deve (shall) fornire agli utenti un'opzione per interrompere i processi time-consuming.

- **IMPL-5.17.9.8-01** `[HTML][JS]` `[Footer]` Vedi `IMPL-5.1.2.1.6.7-01`.

#### 5.17.9.9 – System processing
*Sintesi:* Quando l'elaborazione del sistema richiede che l'utente attenda prima di agire ulteriormente, un messaggio o icona appropriata deve (shall) essere visualizzata finché l'interazione non è di nuovo possibile.

- **IMPL-5.17.9.9-01** `[JS][HTML]` `[Footer]` Vedi `IMPL-5.1.2.1.6.8-01`.

#### 5.17.9.9.1 – Status message
*Sintesi:* Quando il ritardo previsto supera 1 secondo, il sistema deve (shall) fornire un messaggio di stato.

- **IMPL-5.17.9.9.1-01** `[JS]` `[Footer]` Vedi `IMPL-5.1.2.1.6.8.1-01`.

#### 5.17.9.9.2 – Progress indicator
*Sintesi:* Per ritardi superiori a 10 secondi, il display deve (shall) fornire un indicatore di avanzamento.

- **IMPL-5.17.9.9.2-01** `[JS][HTML]` `[Footer]` Vedi `IMPL-5.1.2.1.6.8.2-01`; `#progress-indicator` deve mostrare anche una barra di avanzamento percentuale quando la quantità totale di lavoro è nota.

#### 5.17.9.10 – User input rejection
*Sintesi:* Se il sistema rifiuta un input dell'utente, deve (shall) essere fornito un feedback che indichi il motivo del rifiuto e l'azione correttiva richiesta.

- **IMPL-5.17.9.10-01** `[JS][HTML]` `[Main]` Vedi `IMPL-5.1.2.1.6.11-01`.

#### 5.17.9.11 – Highlighted option selection
*Sintesi:* Qualsiasi elemento selezionato come opzione o input al sistema deve (shall) essere evidenziato per indicarne il riconoscimento da parte del sistema.

- **IMPL-5.17.9.11-01** `[CSS][HTML]` `[Main]` Vedi `IMPL-5.1.2.1.6.12-01`.

#### 5.17.9.12 – Process outcome
*Sintesi:* Quando un processo di controllo o una sequenza di azioni correlate è completato o interrotto dal sistema, il sistema deve (shall) fornire un'indicazione dell'esito all'utente.

- **IMPL-5.17.9.12-01** `[JS][HTML]` `[Footer]` Vedi `IMPL-5.1.2.1.6.13-01`.

#### 5.17.9.13 – Subsequent actions
*Sintesi:* A seguito di un'azione interrotta, il sistema deve (shall) fornire ogni requisito per l'azione successiva dell'utente.

- **IMPL-5.17.9.13-01** `[JS][HTML]` `[Footer]` `notify.js` deve includere nel messaggio di esito (vedi `IMPL-5.1.2.1.6.13-01`) un elenco esplicito delle azioni successive richieste quando il processo è stato interrotto, non solo in caso di completamento.

#### 5.17.9.14 – Feedback message content
*Sintesi:* I messaggi di feedback devono (shall) essere espliciti e informativi.

- **IMPL-5.17.9.14-01** `[JS]` `[Globale]` `i18n.js` deve garantire che ogni messaggio di feedback includa causa ed effetto (cosa è successo, perché, cosa fare), non solo un codice di stato.

#### 5.17.9.14.1 – Understandable
*Sintesi:* Il feedback non deve (shall not) richiedere un sistema di riferimento o un codebook per essere compreso.

- **IMPL-5.17.9.14.1-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.14-01`.

#### 5.17.9.14.2 – Non-standard abbreviations
*Sintesi:* Le abbreviazioni non standard dovrebbero (should) essere evitate nei messaggi di feedback.

- **IMPL-5.17.9.14.2-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.2.1.6.15-01`.

### 3.3 Cybersecurity: controllo accessi, autenticazione, login/logout, protezione dati

I requisiti seguenti riguardano le schermate di login, sessione e amministrazione dell'applicazione (SA = amministratore di sistema; ISSO = responsabile della sicurezza informatica). Si realizzano tramite `auth.js` (login/logout, ruoli, blocco account, inattività), `state.js` (utente/ruolo/sessione attivi), `notify.js`/`log.js` (notifiche e audit) e viste dedicate in `#content-region`/`#dialog-host`.

#### 5.16 – Cybersecurity (principio generale)
*Sintesi (shall):* Le considerazioni di cybersecurity (DoDI 8510.01, CNSSI 1253, NIST SP 800-53) riguardano il controllo accessi e l'identificazione/autenticazione utente; gli elementi di interazione umana rilevanti devono essere identificati, analizzati e implementati nel processo di risk management.

- **IMPL-5.16-01** `[JS]` `[Globale]` `auth.js` e `log.js` devono implementare le funzioni di controllo accessi/identificazione/autenticazione come servizi centrali richiamati da tutte le viste che richiedono autorizzazione, senza duplicarne la logica nei singoli moduli applicativi.
- **IMPL-5.16-02** `[JS]` `[Globale]` I criteri di sicurezza (ruoli, soglie di blocco, durata sessione) devono essere valori di progetto configurabili in `config.js`, revisionabili senza modificare il codice applicativo.
- **IMPL-5.16-03** `[HTML]` `[Globale]` Ogni vista con dati sensibili deve dichiarare nel markup l'attributo `data-requires-role` verificato da `auth.js` prima del rendering del contenuto in `#content-region`.

#### 5.16.1.1 – User access
*Sintesi (shall):* Il controllo accessi basato sui ruoli (role-based access control) deve essere usato per gestire l'accesso utente.

- **IMPL-5.16.1.1-01** `[JS]` `[Globale]` `auth.js` deve mantenere in `state.js` l'insieme di ruoli/permessi dell'utente autenticato e `nav.js` deve nascondere/disabilitare (`[disabled]`/`[aria-disabled]`) le voci di menu non autorizzate per il ruolo corrente.
- **IMPL-5.16.1.1-02** `[HTML]` `[Globale]` Ogni azione protetta (pulsante, link, voce `#function-keys`) deve avere attributo `data-role-required` controllato lato client e rivalidato lato server prima dell'esecuzione.

#### 5.16.1.2 – Access notifications
*Sintesi (should):* SA e ISSO dovrebbero ricevere una notifica quando le autorizzazioni d'accesso sono incoerenti con le restrizioni.

- **IMPL-5.16.1.2-01** `[JS]` `[Globale]` `notify.js` dovrebbe generare un avviso in coda prioritaria (livello `warning`) verso i ruoli SA/ISSO quando `auth.js` rileva un'incoerenza tra ruolo assegnato e restrizioni di accesso, visibile in `#alert-summary`.

#### 5.16.1.3.1 – Linked user profiles
*Sintesi (shall):* Se un utente ha più ruoli nel sistema, il suo profilo deve essere collegato a tutti i ruoli e permessi.

- **IMPL-5.16.1.3.1-01** `[JS]` `[Globale]` `state.js` deve rappresentare il profilo utente come oggetto con array `roles[]` collegato a un unico `userId`, consultato da `auth.js` per risolvere i permessi effettivi.
- **IMPL-5.16.1.3.1-02** `[HTML]` `[Header]` `#user-menu` deve elencare tutti i ruoli associati al profilo attivo, non solo quello in uso nella sessione corrente.

#### 5.16.1.3.2 – User role login
*Sintesi (should):* L'utente dovrebbe essere informato sotto quale ruolo è connesso.

- **IMPL-5.16.1.3.2-01** `[HTML][CSS]` `[Header]` `#user-menu` dovrebbe mostrare in modo permanente e leggibile il ruolo corrente (es. badge testuale accanto al nome utente), aggiornato da `state.js` ad ogni cambio ruolo.

#### 5.16.1.4 – Visible active accounts
*Sintesi (shall):* Per tutta la durata della sessione l'utente deve poter vedere quale account di sistema è attivo.

- **IMPL-5.16.1.4-01** `[HTML][JS]` `[Header]` `#user-menu` deve mostrare in modo persistente l'identificativo dell'account attivo, risincronizzato da `state.js` ad ogni variazione di sessione; nessuna interazione deve essere necessaria per visualizzarlo.

#### 5.16.1.5 – Access to roles and permissions
*Sintesi (shall):* SA e ISSO devono avere accesso alle matrici di ruoli e permessi del sistema.

- **IMPL-5.16.1.5-01** `[HTML][JS]` `[Main]` Una vista di amministrazione in `#content-region` (accessibile solo a SA/ISSO via `data-role-required`) deve presentare la matrice ruoli×permessi come tabella consultabile, alimentata da `auth.js`.

#### 5.16.1.6 – Separation of duties
*Sintesi (should):* SA e ISSO dovrebbero disporre dei ruoli e dei compiti associati per mantenere la consapevolezza e la segregazione dei compiti organizzativi.

- **IMPL-5.16.1.6-01** `[HTML]` `[Main]` La vista amministrativa di cui a IMPL-5.16.1.5-01 dovrebbe includere, per ogni ruolo, l'elenco dei compiti/funzioni associate, per evidenziare eventuali sovrapposizioni di competenza.

#### 5.16.1.7 – Automated restrictions
*Sintesi (shall):* I login non validi devono attivare restrizioni di accesso automatiche secondo il piano di cybersecurity del sistema.

- **IMPL-5.16.1.7-01** `[JS]` `[Globale]` `auth.js` deve contare i tentativi di login falliti per account (soglia configurabile in `config.js`) e applicare automaticamente il blocco account al superamento, registrando l'evento con `log.js`.

#### 5.16.1.8 – Automated notifications
*Sintesi (should):* Il SA dovrebbe ricevere una notifica automatica quando l'accesso di un utente è stato limitato dal sistema.

- **IMPL-5.16.1.8-01** `[JS]` `[Globale]` Quando `auth.js` blocca un account (vedi IMPL-5.16.1.7-01), `notify.js` dovrebbe inviare una notifica automatica ai ruoli SA, visibile in `#alert-summary`/`#toast-region`.

#### 5.16.1.9 – Monitoring logs
*Sintesi (shall):* Il SA deve poter determinare chi è connesso al sistema in tempo reale o in passato (tramite log di sistema).

- **IMPL-5.16.1.9-01** `[HTML][JS]` `[Main]` Una vista amministrativa deve mostrare l'elenco degli utenti attualmente connessi (da `state.js`/`net.js`) e deve offrire una ricerca/filtro sui log storici di accesso prodotti da `log.js`.

#### 5.16.1.10 – Unlock an account
*Sintesi (shall/should):* Il SA deve poter sbloccare un account; dovrebbe poterlo fare da remoto.

- **IMPL-5.16.1.10-01** `[HTML][JS]` `[Main]` La vista amministrativa deve includere, per ogni account bloccato, un pulsante `.btn-unlock-account` (`min-block-size: var(--touch-target-min)`) che invoca `auth.js` per revocare il blocco via `net.js`, utilizzabile da postazione remota.

#### 5.16.1.11 – Create user profiles
*Sintesi (shall):* Il SA deve poter creare profili utente.

- **IMPL-5.16.1.11-01** `[HTML][JS]` `[Main]` La vista amministrativa deve includere un form di creazione profilo (nome, ruoli, credenziali iniziali) validato da `validate.js` e inviato tramite `net.js`.

#### 5.16.1.12 – Access user profiles
*Sintesi (shall):* Il SA deve poter accedere ai profili utente.

- **IMPL-5.16.1.12-01** `[HTML][JS]` `[Main]` La vista amministrativa deve offrire un elenco/ricerca dei profili utente con dettaglio consultabile (dati anagrafici, ruoli, stato account) in `#aux-region`.

#### 5.16.1.13 – Edit user profiles
*Sintesi (should):* SA e ISSO dovrebbero poter modificare i profili utente.

- **IMPL-5.16.1.13-01** `[HTML][JS]` `[Main]` Il dettaglio profilo (IMPL-5.16.1.12-01) dovrebbe includere un form di modifica (ruoli, stato, dati) con validazione `validate.js` e conferma tramite `dialog.js`.

#### 5.16.1.14 – Account setup feedback
*Sintesi (shall):* Il SA deve essere informato al completamento del processo di creazione account.

- **IMPL-5.16.1.14-01** `[JS]` `[Globale]` Al termine della creazione profilo (IMPL-5.16.1.11-01), `feedback.js`/`notify.js` deve mostrare conferma esplicita (`#toast-region`, `role="status"`) al SA entro i tempi di Tabella V (aggiornamento locale ≤ 0,5 s).

#### 5.16.1.15 – Disabled account feedback
*Sintesi (shall):* Il SA deve ricevere un riscontro quando l'account è stato disabilitato.

- **IMPL-5.16.1.15-01** `[JS]` `[Globale]` `notify.js` deve mostrare conferma al SA alla disattivazione di un account (stesso canale di IMPL-5.16.1.14-01), con stato persistito da `state.js`.

#### 5.16.1.16 – Purged account feedback
*Sintesi (shall):* Il SA deve ricevere un riscontro quando l'account è stato eliminato definitivamente.

- **IMPL-5.16.1.16-01** `[JS]` `[Globale]` `notify.js` deve mostrare conferma esplicita al SA all'esito dell'operazione di eliminazione definitiva dell'account, con doppia conferma preventiva tramite `dialog.js` (azione irreversibile).

#### 5.16.2.1 – User identification
*Sintesi (shall):* Le procedure di identificazione utente devono essere il più semplici possibile, coerentemente con le prassi comuni di protezione dati.

- **IMPL-5.16.2.1-01** `[HTML][JS]` `[Main]` Il form di login deve limitarsi ai campi minimi necessari (identificativo, fattore di autenticazione), con etichette chiare, autocompletamento HTML standard (`autocomplete="username"`) e nessun passaggio superfluo.

#### 5.16.2.2 – Notification message or banner
*Sintesi (shall):* Dove richiesto da policy, il sistema deve supportare la visualizzazione di banner/avvisi di sicurezza e privacy fino a riconoscimento esplicito dell'utente.

- **IMPL-5.16.2.2-01** `[HTML][JS]` `[Globale]` Un `<dialog id="security-banner">` gestito da `dialog.js` deve essere mostrato prima/durante il login, bloccante finché l'utente non attiva un pulsante esplicito `#btn-acknowledge-banner`; il testo è configurabile via `i18n.js`/`config.js`.

#### 5.16.2.3 – Multifactor authentication
*Sintesi (shall):* L'autenticazione multifattore per accesso in rete e locale deve essere implementata, con almeno due fattori tra: a. oggetto fisico (es. CAC/token); b. elemento noto all'utente (password/PIN); c. dato biometrico.

- **IMPL-5.16.2.3-01** `[HTML][JS]` `[Main]` Il flusso di login gestito da `auth.js` deve richiedere in sequenza almeno due fattori tra: lettura token/CAC (API browser dedicata o integrazione hardware), password/PIN (`<input type="password">`) e, se disponibile, biometria (WebAuthn), con stato di avanzamento visibile.
- **IMPL-5.16.2.3-02** `[JS]` `[Globale]` `config.js` deve definire quali combinazioni di fattori (a/b/c) sono abilitate per il deployment corrente, verificate da `auth.js` prima di considerare completata l'autenticazione.

#### 5.16.2.4 – Viable multifactor authentication options
*Sintesi (shall):* L'utente deve essere informato delle opzioni di autenticazione multifattore disponibili per accedere al sistema.

- **IMPL-5.16.2.4-01** `[HTML]` `[Main]` La schermata di login deve elencare in modo leggibile i metodi di autenticazione disponibili (es. "CAC + PIN", "password + biometria") prima che l'utente scelga come procedere.

#### 5.16.2.5 – Status feedback
*Sintesi (shall):* L'utente deve ricevere un riscontro sullo stato (accettato/respinto) degli input di autenticazione.

- **IMPL-5.16.2.5-01** `[JS]` `[Main]` Dopo ogni invio di fattore di autenticazione, `feedback.js` deve mostrare esito immediato (accettato/respinto) tramite `#status-line`/`#message-line` ed `aria-live="polite"`, entro ≤ 0,2 s (errore, Tabella V).

#### 5.16.2.6 – Password not echoed on display
*Sintesi (should):* Se si usa il fattore username/password, la password non dovrebbe essere visualizzata in chiaro (es. asterisco per ogni carattere).

- **IMPL-5.16.2.6-01** `[HTML]` `[Main]` Il campo password deve usare `<input type="password">` (mascheramento nativo con punto/asterisco); un controllo opzionale "mostra password" (`aria-pressed`) può essere offerto all'utente, disattivato di default.

#### 5.16.2.7 – Error message
*Sintesi (shall):* Deve essere fornito un messaggio di errore quando un identificativo utente/dispositivo selezionato non è valido, con istruzioni per selezionarne uno nuovo.

- **IMPL-5.16.2.7-01** `[JS]` `[Main]` `validate.js` deve segnalare via `#message-line` e `[aria-invalid="true"]` sul campo l'identificativo non valido, con testo d'istruzione su come correggerlo, generato da `i18n.js`.

#### 5.16.2.8 – User and device identifier notification
*Sintesi (shall):* L'utente deve essere avvisato quando l'assegnazione dell'identificativo utente/dispositivo è stata completata con successo.

- **IMPL-5.16.2.8-01** `[JS]` `[Main]` `feedback.js` deve confermare via `#status-line` l'avvenuta assegnazione dell'identificativo utente/dispositivo, entro ≤ 0,5 s (aggiornamento locale, Tabella V).

#### 5.16.2.9 – User status
*Sintesi (shall):* Lo stato di un'azione richiesta deve essere visualizzato.

- **IMPL-5.16.2.9-01** `[JS]` `[Globale]` Ogni azione avviata dall'utente (login, richiesta SA, ecc.) deve avere uno stato visibile (in corso/completata/fallita) gestito da `feedback.js`/`#status-line`, con `#progress-indicator` oltre 1 s.

#### 5.16.2.10 – Reason for failure feedback
*Sintesi (shall):* Un motivo specifico deve essere visualizzato in caso di azione fallita.

- **IMPL-5.16.2.10-01** `[JS]` `[Main]` `validate.js`/`notify.js` deve mostrare in `#message-line` un messaggio specifico (non generico) sul motivo del fallimento, evitando testi tipo "errore generico".

#### 5.16.2.11 – Corrective action suggestion
*Sintesi (shall):* Un'azione correttiva deve essere suggerita in caso di diniego di accesso.

- **IMPL-5.16.2.11-01** `[JS]` `[Main]` Il messaggio di diniego di accesso generato da `auth.js`/`notify.js` deve includere un suggerimento operativo esplicito (es. "contattare l'amministratore", "reinserire PIN").

#### 5.16.2.12 – Maximum number of concurrent sessions
*Sintesi (should):* Il numero massimo di sessioni concorrenti per account dovrebbe essere imposto, con diniego di accesso oltre la soglia.

- **IMPL-5.16.2.12-01** `[JS]` `[Globale]` `auth.js` dovrebbe verificare presso `net.js` il numero di sessioni attive per account rispetto alla soglia in `config.js` e negare l'accesso con messaggio esplicito se superata.

#### 5.16.2.13 – Concurrent session control
*Sintesi (should):* SA e ISSO dovrebbero avere informazioni su tutte le sessioni concorrenti attive per ciascun account.

- **IMPL-5.16.2.13-01** `[HTML][JS]` `[Main]` La vista amministrativa (IMPL-5.16.1.9-01) dovrebbe elencare, per ogni account, le sessioni concorrenti attive (dispositivo, orario, stato).

#### 5.16.2.14 – Multiple concurrent sessions
*Sintesi (shall):* Se è permessa più di una sessione per account, l'utente deve essere informato di tutte le proprie sessioni attive.

- **IMPL-5.16.2.14-01** `[HTML][JS]` `[Header]` `#user-menu` deve includere una voce che elenca le sessioni attive dell'utente corrente, aggiornata da `state.js`/`net.js`.

#### 5.16.3.1 – Logon procedures
*Sintesi (shall):* Il logon deve essere una procedura separata da completare prima che l'utente possa selezionare opzioni operative.

- **IMPL-5.16.3.1-01** `[HTML][JS]` `[Main]` `nav.js` deve impedire l'accesso a `#nav-region`/`#content-region` finché `auth.js` non segnala autenticazione completata; la vista di login è l'unica raggiungibile prima del login.

#### 5.16.3.2 – Automatic logon display
*Sintesi (shall):* Le richieste di logon devono essere mostrate automaticamente senza azioni speciali oltre l'accensione del terminale.

- **IMPL-5.16.3.2-01** `[JS]` `[Globale]` All'avvio dell'applicazione (`DOMContentLoaded`), `auth.js` deve mostrare automaticamente la schermata/form di login senza richiedere interazioni preliminari dell'utente.

#### 5.16.3.3 – Logon attempts notification
*Sintesi (shall):* La possibilità di visualizzare lo storico di logon degli account deve essere disponibile a SA e ISSO.

- **IMPL-5.16.3.3-01** `[HTML][JS]` `[Main]` La vista amministrativa deve offrire una tabella consultabile/filtrabile dello storico di logon per account, alimentata da `log.js`.

#### 5.16.3.4 – Logon metadata
*Sintesi (shall):* Nome, account, ruolo, data e ora dei tentativi di logon devono essere registrati.

- **IMPL-5.16.3.4-01** `[JS]` `[Globale]` `log.js` deve registrare per ogni tentativo di logon: nome utente, account, ruolo, data e ora, inoltrando il record al back-end tramite `net.js`.

#### 5.16.3.5 – Invalid logon attempts
*Sintesi (shall):* Se il sistema impone un limite di tentativi di login non validi, l'utente deve essere informato del numero di tentativi rimanenti prima del blocco.

- **IMPL-5.16.3.5-01** `[JS]` `[Main]` `auth.js` deve mostrare in `#message-line` il numero di tentativi residui dopo ogni login fallito, aggiornato ad ogni tentativo, fino al blocco (vedi IMPL-5.16.1.7-01).

#### 5.16.3.6 – Logon reset request
*Sintesi (shall):* Se l'utente è bloccato, deve poter richiedere al SA il reset del numero di tentativi di logon.

- **IMPL-5.16.3.6-01** `[HTML][JS]` `[Main]` La schermata di blocco account deve presentare un pulsante/link `#btn-request-reset` che invia (via `net.js`) una richiesta di sblocco al SA, con conferma di invio mostrata all'utente.

#### 5.16.4.1 – Automatic logoff
*Sintesi (shall):* Dove richiesto da policy, dopo un periodo predefinito di inattività l'utente deve essere disconnesso automaticamente senza perdita di dati.

- **IMPL-5.16.4.1-01** `[JS]` `[Globale]` `auth.js`, tramite `#session-timer` e un timeout configurabile in `config.js`, deve eseguire il logout automatico dopo inattività, salvando preventivamente lo stato non confermato (bozze) tramite `state.js`/`net.js` per evitare perdita di dati.
- **IMPL-5.16.4.1-02** `[HTML][CSS]` `[Footer]` `#session-timer` deve mostrare visivamente il tempo residuo prima del logout automatico, con stato di avviso (`--color-warning`) negli ultimi secondi configurabili.

#### 5.16.4.2 – Logoff procedures
*Sintesi (shall):* Alla richiesta di logoff/uscita/arresto il sistema deve: a. verificare le transazioni pendenti e avvisare di possibile perdita dati; b. richiedere conferma prima di eseguire il logoff.

- **IMPL-5.16.4.2-01** `[JS]` `[Globale]` `auth.js` deve interrogare `state.js` per transazioni/modifiche non salvate prima del logoff e, se presenti, mostrare un avviso esplicito di possibile perdita dati (punto a).
- **IMPL-5.16.4.2-02** `[HTML][JS]` `[Globale]` Un `<dialog id="confirm-logoff">` gestito da `dialog.js` deve richiedere conferma esplicita dell'utente prima di eseguire logoff/uscita/arresto (punto b).

#### 5.16.5.1 – Displayed security classification
*Sintesi (shall):* Quando i dati visualizzati sono classificati, deve essere fornita un'indicazione prominente del livello di classificazione.

- **IMPL-5.16.5.1-01** `[HTML][CSS]` `[Header]` Un indicatore `#classification-banner` (sempre visibile, contrasto elevato, colore distinto `--color-critical`/dedicato) deve mostrare il livello di classificazione dei dati correnti, aggiornato da `state.js`.

#### 5.16.5.2 – Warnings
*Sintesi (should):* La logica di sistema dovrebbe generare messaggi e segnali di allarme per avvisare di tentativi di intrusione da parte di utenti non autorizzati.

- **IMPL-5.16.5.2-01** `[JS]` `[Globale]` `notify.js`, su segnalazione di `net.js`/`auth.js` di tentativi di accesso anomali, dovrebbe generare un allarme ad alta priorità (visivo `#alert-summary` + sonoro `feedback.js.beep('alarm')`).

#### 5.16.6.1 – Simulated data
*Sintesi (shall):* Quando sono forniti dati e funzioni simulate (es. per addestramento), i dati reali devono essere protetti.

- **IMPL-5.16.6.1-01** `[JS]` `[Globale]` `state.js` deve gestire una modalità `simulation` esplicita, isolando l'accesso ai dati reali (bloccato lato `net.js`) quando tale modalità è attiva.

#### 5.16.6.2 – Real system use
*Sintesi (shall):* L'uso reale del sistema deve essere chiaramente distinto da tutte le operazioni simulate.

- **IMPL-5.16.6.2-01** `[HTML][CSS]` `[Header]` `#mode-indicator` deve mostrare in modo permanente e visivamente distinto (colore/etichetta/icona) se la sessione è in modalità reale o simulata.

#### 5.16.6.3 – Clear indication
*Sintesi (shall):* Nelle applicazioni in cui possono essere mostrati dati reali o simulati, deve essere inclusa un'indicazione chiara dei dati simulati.

- **IMPL-5.16.6.3-01** `[HTML][CSS]` `[Main]` Ogni vista in `#content-region` che mostra dati deve applicare una classe `.is-simulated` (bordo/etichetta "DATI SIMULATI" distintiva) quando `state.js` segnala modalità simulazione, in aggiunta a IMPL-5.16.6.2-01.

#### 5.16.7.1 – Password criteria
*Sintesi (shall):* I criteri per una password accettabile (es. numero minimo e tipi di caratteri) devono essere mostrati all'utente durante la generazione della password.

- **IMPL-5.16.7.1-01** `[HTML]` `[Main]` Il form di impostazione/cambio password deve mostrare in modo persistente (non solo su errore) l'elenco dei criteri richiesti, definiti in `config.js` e validati da `validate.js`.

#### 5.16.7.2 – Dynamic feedback
*Sintesi (shall):* L'utente deve ricevere un riscontro dinamico su quali criteri di complessità password non sono stati soddisfatti durante la generazione.

- **IMPL-5.16.7.2-01** `[JS]` `[Main]` `validate.js` deve aggiornare in tempo reale (`input` event, eco ≤ 0,2 s Tabella V) lo stato di ciascun criterio elencato in IMPL-5.16.7.1-01, con indicatore soddisfatto/non soddisfatto (`aria-live="polite"`).

#### 5.16.7.5 – Changing passwords
*Sintesi (shall):* Dove le password devono essere cambiate periodicamente per policy, il sistema deve supportare l'imposizione della policy; entro l'intervallo obbligatorio l'utente deve poter cambiare la password quando lo desidera.

- **IMPL-5.16.7.5-01** `[JS]` `[Main]` `auth.js` deve forzare il reindirizzamento al form di cambio password quando `config.js` segnala scadenza della password corrente, e deve consentire il cambio volontario in qualunque momento tramite `#user-menu`.

#### 5.16.7.6 – Password managers
*Sintesi (should):* Quando appropriato rispetto alle operazioni di sistema, il sistema dovrebbe supportare l'uso di gestori di password per l'inserimento delle credenziali.

- **IMPL-5.16.7.6-01** `[HTML]` `[Main]` I campi di login/credenziali devono usare attributi `autocomplete` standard (`username`, `current-password`, `new-password`) e non devono bloccare incolla/autoriempimento, per compatibilità con i password manager del browser.

#### 5.19.1.9.2 – Cybersecurity (PED)
*Sintesi (should):* Durante l'uso, i dispositivi elettronici portatili (PED) dovrebbero avere misure di sicurezza per evitare manomissioni e rispettare i requisiti DoD di cybersecurity e il §5.16.

- **IMPL-5.19.1.9.2-01** `[JS]` `[Globale]` Quando l'applicazione gira su dispositivo mobile/tablet (PED), `auth.js` dovrebbe applicare le stesse misure di controllo accessi/autenticazione definite in questa sezione (vedi IMPL-5.16.*), senza eccezioni per il form factor.

### 3.4 Controlli, puntamento e input touch

Requisiti applicabili ai controlli a schermo e all'uso touchscreen (i requisiti relativi a hardware meccanico, tastiera/mouse/trackball/joystick/penna ottica fisici sono esclusi, non pertinenti a una web HMI). Si realizzano con i token `--touch-target-min/max`, `--touch-gap-min/max` (§2.4), Pointer Events, `feedback.js`, `dialog.js`, `nav.js`.

#### 5.1.1.3.1 – Grouping
*Sintesi (shall):* I controlli azionati in sequenza task-driven o usati insieme devono essere raggruppati assieme ai relativi display.

- **IMPL-5.1.1.3.1-01** `[HTML][CSS]` `[Main]` I controlli di una stessa sequenza operativa devono essere racchiusi in un contenitore semantico comune (es. `<fieldset class="control-group">`) posizionato adiacente ai display/valori associati in `#content-region`.

#### 5.1.1.3.1.1 – Multiple steps selected by a single control
*Sintesi (shall):* Quando più passi di una sequenza sono selezionati da un unico controllo, i passi devono essere ordinati secondo l'ordine di occorrenza per minimizzare i movimenti e impedire cicli inutili.

- **IMPL-5.1.1.3.1.1-01** `[JS]` `[Main]` Un controllo multi-step (es. `<select>`/stepper) gestito da `state.js` deve elencare le opzioni nell'ordine cronologico/operativo reale, senza possibilità di saltare o ripetere passi non necessari.

#### 5.1.1.3.1.2 – Cycling through ON/OFF position
*Sintesi (shall):* Il ciclo non necessario attraverso la posizione ON/OFF del controllo deve essere evitato.

- **IMPL-5.1.1.3.1.2-01** `[JS]` `[Main]` La logica dello stepper/controllo ciclico in `state.js` non deve attraversare uno stato ON/OFF intermedio quando si passa tra due valori operativi non correlati a tale stato.

#### 5.1.1.3.2 – Sequential operation
*Sintesi (shall):* Dove le operazioni sequenziali seguono uno schema fisso, i controlli devono essere disposti per facilitare l'operazione (es. schema sinistra-destra/alto-basso).

- **IMPL-5.1.1.3.2-01** `[CSS]` `[Main]` I controlli con uso sequenziale fisso devono essere disposti con `display: grid`/`flex` secondo l'ordine di lettura naturale (sinistra→destra, alto→basso), coerente con l'ordine del DOM per l'ordine di tabulazione.

#### 5.1.1.3.4 – Consistency
*Sintesi (shall):* La disposizione dei controlli primari funzionalmente simili/identici deve essere coerente tra i pannelli dell'intero sistema; il movimento di un controllo verso destra/sinistra deve corrispondere a un movimento dell'elemento visualizzato nella stessa direzione.

- **IMPL-5.1.1.3.4-01** `[CSS]` `[Globale]` Classi utility comuni (es. `.control-slider`, `.control-toggle`) devono definire la stessa disposizione e verso in tutte le viste dell'applicazione, documentate come pattern riutilizzabile.
- **IMPL-5.1.1.3.4-02** `[JS]` `[Main]` Per i controlli che manipolano un elemento visualizzato (slider, cursore), lo spostamento del controllo verso destra/sinistra deve produrre uno spostamento dell'elemento associato nella stessa direzione, gestito da `state.js`.

#### 5.1.1.4.1 – Consistent application
*Sintesi (shall):* Dove è usata una codifica per differenziare i controlli, l'applicazione del codice deve essere uniforme in tutto il sistema.

- **IMPL-5.1.1.4.1-01** `[CSS]` `[Globale]` Le codifiche (colore, icona, forma) usate per differenziare tipi di controllo devono essere definite una sola volta nei token `--color-*` (§2.4) e applicate uniformemente tramite classi condivise, senza varianti locali per vista.

#### 5.1.1.7.1 – Location and design
*Sintesi (shall):* I controlli devono essere progettati e collocati in modo da non essere soggetti ad attivazione accidentale/involontaria, in particolare i controlli critici la cui attivazione errata potrebbe causare danni, infortuni, degrado prestazionale o arresto di apparecchiature mission-critical.

- **IMPL-5.1.1.7.1-01** `[HTML][CSS]` `[Main]` I controlli critici (classe `.control-critical`) devono essere posizionati isolati da controlli ad uso frequente, con `--touch-gap-min` maggiorato (es. doppio del minimo) e sfondo/bordo distintivo per ridurre l'attivazione accidentale.
- **IMPL-5.1.1.7.1-02** `[JS]` `[Main]` L'attivazione di un controllo `.control-critical` deve richiedere conferma tramite `dialog.js` prima di eseguire l'azione (vedi anche IMPL-5.1.3.1.7-01).

#### 5.1.1.7.3 – Rapid operation
*Sintesi (shall):* Qualsiasi metodo di protezione da attivazione involontaria non deve impedire l'azionamento nel tempo richiesto.

- **IMPL-5.1.1.7.3-01** `[JS]` `[Main]` La conferma richiesta per i controlli critici (IMPL-5.1.1.7.1-02) deve essere eseguibile in un solo tocco aggiuntivo (es. pulsante "Conferma" nel dialog), senza passaggi multipli che ritardino oltre il tempo operativo richiesto (config. in `config.js`).

#### 5.1.1.9.2 – Consistency of use
*Sintesi (shall):* Un controllo usato per una funzione critica o di emergenza deve essere dedicato esclusivamente a quella funzione.

- **IMPL-5.1.1.9.2-01** `[HTML][JS]` `[Main]` I controlli con classe `.control-emergency` (es. arresto, allarme) non devono essere riutilizzati per altre funzioni in nessuna vista; `nav.js`/`state.js` devono mantenerne l'identità e il comportamento costanti ovunque compaiano.

#### 5.1.2.2.1 – Functional grouping
*Sintesi (shall):* I controlli e i display funzionalmente correlati devono essere posizionati vicini e organizzati in gruppi funzionali (es. alimentazione, stato, test).

- **IMPL-5.1.2.2.1-01** `[HTML][CSS]` `[Main]` `#content-region` deve organizzare controlli e display correlati in contenitori `<section class="control-group" aria-label="...">` adiacenti, raggruppati per funzione (es. alimentazione, stato, test).

#### 5.1.2.2.1.1 – Sequence
*Sintesi (shall):* I gruppi funzionali di controlli/display devono essere collocati per garantire un ordine d'uso sinistra-destra (preferito) o alto-basso, o entrambi.

- **IMPL-5.1.2.2.1.1-01** `[CSS]` `[Main]` La disposizione dei `.control-group` in `#content-region` deve seguire una griglia che rispetta l'ordine sinistra→destra come criterio primario, alto→basso come secondario, tramite `grid-auto-flow: column` o equivalente.

#### 5.1.2.2.1.3 – Access
*Sintesi (shall):* A parità di integrità del raggruppamento per funzione/sequenza, i gruppi più usati e più importanti devono essere collocati nelle aree di accesso più facile.

- **IMPL-5.1.2.2.1.3-01** `[CSS]` `[Main]` I `.control-group` marcati `data-priority="high"` devono essere posizionati nelle aree raggiungibili senza scorrimento (sopra la piega) e più vicine al pollice/centro dello schermo touch.

#### 5.1.2.2.1.5 – Consistency
*Sintesi (shall):* La posizione dei gruppi funzionali ricorrenti e dei singoli elementi deve essere coerente da pannello a pannello.

- **IMPL-5.1.2.2.1.5-01** `[CSS]` `[Globale]` Un layout di griglia comune (classi/token condivisi) deve posizionare i gruppi funzionali ricorrenti (es. azioni di conferma/annulla, stato) nelle stesse coordinate relative in tutte le viste di `#content-region`.

#### 5.1.2.2.1.5.2 – Display commonality
*Sintesi (shall):* Quando sono usati più display e più formati, nomenclatura e simbologia devono essere comuni su tutti i display, ove opportuno.

- **IMPL-5.1.2.2.1.5.2-01** `[JS]` `[Globale]` `i18n.js` deve fornire un'unica fonte di etichette/simboli/icone riutilizzata da tutte le viste, evitando sinonimi o simboli diversi per lo stesso concetto.

#### 5.1.2.2.1.5.3 – Text or readout fields
*Sintesi (shall):* I campi di testo/lettura comuni a tutti i display (es. avvisi di sistema) devono trovarsi in una posizione standard su tutti i pannelli e formati.

- **IMPL-5.1.2.2.1.5.3-01** `[HTML][CSS]` `[Footer]` Campi comuni come avvisi di sistema devono restare ancorati a `#status-line`/`#message-line` nel footer, in posizione fissa indipendentemente dalla vista corrente in `#content-region`.

#### 5.1.2.2.1.5.4 – Commonality and consistency across systems
*Sintesi (shall):* Ove possibile, i criteri di coerenza e comunanza dei display devono essere applicati anche agli altri sistemi utilizzati dall'utente.

- **IMPL-5.1.2.2.1.5.4-01** `[JS]` `[Globale]` Requisito di processo: le convenzioni di nomenclatura/simbologia/posizionamento (IMPL-5.1.2.2.1.5.2-01, -5.3-01) devono essere documentate come design system condiviso, riutilizzabile/verificabile anche in altre applicazioni della stessa famiglia di sistemi.

#### 5.1.2.2.3 – Arrangement within groups
*Sintesi (shall):* Controlli e display entro i gruppi funzionali devono essere collocati secondo la sequenza operativa e/o la funzione.

- **IMPL-5.1.2.2.3-01** `[CSS]` `[Main]` L'ordine DOM e visivo degli elementi dentro ciascun `.control-group` deve riflettere la sequenza operativa o la relazione funzionale tra i controlli, non un ordine arbitrario.

#### 5.1.2.2.3.1 – No specific sequence
*Sintesi (shall):* Se i controlli/display di un gruppo non seguono una sequenza operativa specifica, devono essere disposti per importanza o frequenza d'uso, con i più importanti/usati nelle posizioni più accessibili.

- **IMPL-5.1.2.2.3.1-01** `[CSS]` `[Main]` In assenza di sequenza fissa, l'attributo `data-priority`/`data-frequency` sull'elemento determina l'ordine `order` CSS/posizione nella griglia, privilegiando le posizioni più accessibili per i valori più alti.

#### 5.1.2.2.3.11 – Emergency use
*Sintesi (shall):* I display e i controlli di emergenza devono essere collocati dove possono essere visti e raggiunti senza ritardo (es. entro un cono di 30° rispetto alla linea di sguardo normale).

- **IMPL-5.1.2.2.3.11-01** `[HTML][CSS]` `[Header][Footer]` I controlli/indicatori di emergenza (`#alert-summary`, `.control-emergency`) devono restare sempre visibili in header/footer (mai in pannelli a comparsa nascosti), raggiungibili senza navigazione aggiuntiva, equivalente software del "cono di 30°".

#### 5.1.3.1.1.1 – High frequency use
*Sintesi (should):* Il touchscreen non dovrebbe essere l'unico mezzo di input se l'interfaccia richiede inserimento frequente di grandi quantità di dati.

- **IMPL-5.1.3.1.1.1-01** `[JS]` `[Globale]` Per le viste con inserimento dati massivo/ricorrente (es. form estesi, tabelle editabili), l'applicazione dovrebbe supportare anche input da tastiera fisica/scanner tramite Pointer Events e gestione `keydown`, non solo tocco su tastiera virtuale.

#### 5.1.3.1.1.2 – Moving or vibration environments
*Sintesi (should):* Il touchscreen non dovrebbe essere l'unico mezzo di input se movimento/vibrazione del sistema potrebbero degradare le prestazioni dell'utente.

- **IMPL-5.1.3.1.1.2-01** `[JS]` `[Globale]` `prefs.js` dovrebbe offrire una modalità "ambiente instabile" che aumenta `--touch-target-min` verso il massimo e abilita conferme vocali/aptiche (`navigator.vibrate`) tramite `feedback.js` come ridondanza al tocco.

#### 5.1.3.1.1.3 – Mission-critical and safety-critical applications
*Sintesi (shall):* Il touchscreen non deve essere l'unico dispositivo di input per interfacce mission-critical o safety-critical.

- **IMPL-5.1.3.1.1.3-01** `[JS]` `[Globale]` Le viste marcate `data-criticality="mission"`/`"safety"` devono garantire piena operatività anche da tastiera (focus, `Enter`/`Space`, scorciatoie) tramite `nav.js`, senza dipendere esclusivamente dal tocco.

#### 5.1.3.1.3 – Positive indication
*Sintesi (shall):* Un'indicazione positiva dell'attivazione touchscreen deve essere fornita per segnalare la risposta del sistema all'azione di controllo.

- **IMPL-5.1.3.1.3-01** `[CSS][JS]` `[Globale]` Ogni controllo deve applicare feedback visivo immediato su `pointerdown`/`pointerup` (stato `:active`, `feedback.js.acknowledge(el)`) ed eventualmente sonoro/aptico, entro ≤ 0,1 s (risposta al tocco, Tabella V).

#### 5.1.3.1.4 – Display response time (latency)
*Sintesi (should):* Il tempo di risposta del display non dovrebbe superare 100 ms; un tempo inferiore può essere richiesto da considerazioni operative.

- **IMPL-5.1.3.1.4-01** `[JS]` `[Globale]` `feedback.js` dovrebbe garantire che il riscontro visivo dell'attivazione (IMPL-5.1.3.1.3-01) sia renderizzato entro 100 ms dall'evento `pointerup`, misurabile con `performance.now()` in test automatici.

#### 5.1.3.1.5 – Impact on visual display
*Sintesi (shall):* Le caratteristiche dei dispositivi touch-interattivi non devono degradare la qualità del display visivo in modo da compromettere le prestazioni dell'utente.

- **IMPL-5.1.3.1.5-01** `[CSS]` `[Globale]` Gli overlay di feedback touch (es. ripple, evidenziazioni) devono usare opacità/colori che non riducano il contrasto testo/sfondo sotto la soglia `--contrast-*` definita (≥ 6:1).

#### 5.1.3.1.6 – Spatial resolution
*Sintesi (shall):* I dispositivi touch-interattivi devono fornire risoluzione spaziale sufficiente per le attività previste.

- **IMPL-5.1.3.1.6-01** `[CSS]` `[Globale]` Le dimensioni e la spaziatura dei target touch devono rispettare `--touch-target-min`/`--touch-gap-min` (vedi tabella in 3.4-5.1.3.1.17) affinché la risoluzione effettiva del tocco sia adeguata al compito.

#### 5.1.3.1.7 – Critical tasks
*Sintesi (shall):* Dove un controllo touchscreen è usato per un compito critico, la risposta del sistema deve richiedere un'azione di conferma aggiuntiva per assicurare l'intenzionalità dell'attivazione.

- **IMPL-5.1.3.1.7-01** `[JS]` `[Main]` I controlli `.control-critical` devono aprire un `<dialog>` di conferma tramite `dialog.js` dopo il `pointerup` iniziale, eseguendo l'azione solo dopo una seconda conferma esplicita dell'utente.

#### 5.1.3.1.7.1 – Confirmatory action impractical
*Sintesi (shall):* Dove l'azione di conferma è impraticabile per compiti critici, devono essere incorporate più attivazioni touch per minimizzare la possibilità di attivazione accidentale.

- **IMPL-5.1.3.1.7.1-01** `[JS]` `[Main]` In alternativa a IMPL-5.1.3.1.7-01 (quando un dialog è impraticabile), il controllo critico deve richiedere una sequenza di 2 tocchi distinti entro una finestra temporale configurabile in `config.js`, gestita da `feedback.js`.

#### 5.1.3.1.8 – Repeat function delay
*Sintesi (shall):* Un ritardo iniziale di 500-750 ms deve essere previsto quando è fornito un ritardo di ripetizione.

- **IMPL-5.1.3.1.8-01** `[JS]` `[Globale]` Per i controlli a pressione prolungata/ripetizione (es. stepper +/-), `feedback.js` deve applicare un `setTimeout` iniziale di 500-750 ms (valore di progetto in `config.js`) prima di avviare la ripetizione automatica su `pointerdown` mantenuto.

#### 5.1.3.1.9 – Sensitivity
*Sintesi (shall):* Il contatto della punta del dito con il touchscreen deve poter attivare il controllo o la funzione di input.

- **IMPL-5.1.3.1.9-01** `[CSS][JS]` `[Globale]` Tutti i controlli devono avere `touch-action: manipulation` e area di attuazione ≥ `--touch-target-min`, garantendo l'attivazione affidabile con un singolo contatto del polpastrello senza necessità di pressione prolungata o strumenti.

#### 5.1.3.1.14 – Sustained interaction
*Sintesi (shall):* I touchscreen non devono essere usati per azioni frequenti protratte nel tempo (es. digitazione su tastiera virtuale, selezione continua di target); non si applica a piccoli dispositivi portatili.

- **IMPL-5.1.3.1.14-01** `[JS]` `[Globale]` Per compiti di inserimento testo prolungato, `prefs.js` dovrebbe favorire tastiera fisica/dettatura vocale rispetto alla tastiera virtuale touch; l'interfaccia non deve richiedere digitazione touch continua come unico percorso (eccetto su dispositivi portatili piccoli, `data-form-factor="handheld"`).

#### 5.1.3.1.15 – Intermittent interaction
*Sintesi (may):* I touchscreen possono essere usati per completare azioni intermittenti come navigazione grossolana del cursore e selezione di pannelli di comunicazione.

- **IMPL-5.1.3.1.15-01** `[JS]` `[Main]` Le interazioni intermittenti (selezione voce di menu, navigazione a pannelli) possono essere implementate interamente via touch (Pointer Events) senza richiedere alternative, essendo coerenti con l'uso intermittente previsto.

#### 5.1.3.1.16 – Target shape and color
*Sintesi (shall):* Se è usata la codifica a colori, deve essere ridondante con un'altra forma di codifica.

- **IMPL-5.1.3.1.16-01** `[CSS][HTML]` `[Globale]` Ogni stato/target codificato a colore (`--color-critical`, `--color-alarm`, ecc.) deve essere accompagnato da icona, forma o testo distintivo (doppia codifica, vedi §2.4), non dal solo colore.

#### 5.19.3.3 – Data input (PED)
*Sintesi (should):* L'input dati su PED dovrebbe avvenire tramite tastiera/tastierino virtuale o fisico; salvo necessità specifiche, il riconoscimento calligrafico dovrebbe essere evitato.

- **IMPL-5.19.3.3-01** `[HTML][JS]` `[Main]` I campi di inserimento dati devono usare controlli standard (`<input>`, tastiera virtuale del sistema operativo o tastierino a schermo definito dall'app) e non devono richiedere riconoscimento calligrafico come unico metodo di input.

#### 5.19.3.4 – Direct input pointers
*Sintesi (shall):* I puntatori di input diretto (es. touchscreen, penna ottica) devono essere usati in preferenza a puntatori di input indiretto (es. trackball, mouse).

- **IMPL-5.19.3.4-01** `[JS]` `[Globale]` Pointer Events deve privilegiare il tocco diretto come modalità principale di interazione; mouse/trackball restano supportati come input secondari (vedi §2.2 Vincoli generali), non sostitutivi del tocco diretto.

#### 5.1.3.1.17 – Dimensions, resistance, and separation
*Sintesi (shall):* Le dimensioni e la separazione delle aree responsive dei touchscreen devono conformarsi alla tabella seguente (la resistenza/forza è una caratteristica hardware, omessa; le dimensioni fisiche vanno tradotte in CSS px tramite `--px-per-mm`).

| Categoria | A (area di attuazione) | S (separazione) | Token CSS equivalente |
|---|---|---|---|
| Tastiera alfanumerica/numerica – minimo | – | 0 | `--touch-gap-min-kb: 0` |
| Tastiera alfanumerica/numerica – preferito | 16 × 16 mm | 2 mm | `--touch-target-kb-preferred: calc(16 * var(--px-per-mm))`; `--touch-gap-kb-preferred: calc(2 * var(--px-per-mm))` |
| Tastiera alfanumerica/numerica – massimo | – | 6 mm | `--touch-gap-max: calc(6 * var(--px-per-mm))` |
| Altre applicazioni – minimo | 15 × 15 mm | 3 mm | `--touch-target-min: calc(15 * var(--px-per-mm))`; `--touch-gap-min: calc(3 * var(--px-per-mm))` |
| Altre applicazioni – massimo | 38 × 38 mm | 6 mm | `--touch-target-max: calc(38 * var(--px-per-mm))`; `--touch-gap-max: calc(6 * var(--px-per-mm))` |

Note: (1) con guanti antifiamma standard aggiungere 5 mm a ciascuna dimensione dell'area di attuazione; (2) con strategia "first contact" la separazione minima è 5 mm, con strategia "last contact" (adottata di default, §2.6) può scendere a 3 mm (non per tastiere alfanumeriche).

- **IMPL-5.1.3.1.17-01** `[CSS]` `[Globale]` Tutti i controlli non-tastiera devono rispettare `min-inline-size`/`min-block-size: var(--touch-target-min)` (15×15 mm) e non superare `var(--touch-target-max)` (38×38 mm) salvo necessità di layout, come già definiti in §2.4.
- **IMPL-5.1.3.1.17-02** `[CSS]` `[Main]` I tasti di tastiere a schermo (componenti `.key`) devono usare dimensione preferita `--touch-target-kb-preferred` (16×16 mm) con gap `--touch-gap-kb-preferred` (2 mm, minimo 0, massimo `--touch-gap-max` 6 mm).
- **IMPL-5.1.3.1.17-03** `[CSS]` `[Globale]` Lo spazio tra controlli non-tastiera adiacenti deve rispettare `gap: var(--touch-gap-min)` (3 mm, strategia last-contact di default) elevabile a 5 mm se `config.js` imposta la strategia "first contact".
- **IMPL-5.1.3.1.17-04** `[JS]` `[Globale]` `prefs.js` deve esporre un'opzione "modalità guanti" che aggiunge 5 mm (`calc(var(--touch-target-min) + 5 * var(--px-per-mm))`) alle dimensioni minime dei target, per utenti con guanti antifiamma.

### 3.5 Controllo interattivo e progettazione del dialogo (generale)

Criteri generali per i dialoghi interattivi: azioni di controllo, tempi di risposta, gerarchia dei menu, feedback, sistemi numerici e interazione grafica. I criteri duplicati in 5.17 (menu, form, linguaggio di comando, domanda-risposta, query) sono trattati nelle sezioni dedicate.

#### 5.1.3.12.1.1 – Azioni di controllo
*Sintesi (shall/should/may):* Le azioni di controllo devono essere minimizzate, coerenti, richiedere un carico mnemonico minimo ed essere sufficientemente flessibili da adattarsi a esigenze utente diverse.

- **IMPL-5.1.3.12.1.1-01** `[HTML][CSS]` `[Globale]` Definire un set unico e riutilizzabile di componenti di controllo (`.btn`, `.toggle`, `.field`) condiviso da tutte le viste in `#content-region`, evitando varianti ad hoc per vista.
- **IMPL-5.1.3.12.1.1-02** `[JS]` `[Globale]` `prefs.js` deve offrire una modalità `interactionMode` (`novice`/`expert`) che riduce i passaggi richiesti per una stessa azione, senza cambiarne il significato.
- **IMPL-5.1.3.12.1.1-03** `[JS]` `[Globale]` `config.js` deve centralizzare il mapping azione→controllo, affinché ogni azione ricorrente (conferma, annulla, avanti) usi sempre lo stesso controllo in tutta l'app.

#### 5.1.3.12.1.2 – Tipi di dialogo
*Sintesi:* I tipi di dialogo devono essere compatibili con i requisiti di compito previsti e con le competenze dell'utente.

- **IMPL-5.1.3.12.1.2-01** `[JS]` `[Globale]` `config.js` deve definire, per vista, il tipo di dialogo (`form`, `menu`, `command`); `nav.js` istanzia il componente coerente in `#content-region`.
- **IMPL-5.1.3.12.1.2-02** `[JS]` `[Globale]` `state.js` deve esporre `state.user.skillLevel`/`role`; `nav.js` sceglie dialoghi guidati a menu per utenti novizi e comandi diretti per utenti esperti.

#### 5.1.3.12.1.3 – Compatibilità dei tempi di risposta con l'utente
*Sintesi:* I tempi di risposta del sistema devono essere coerenti con i requisiti operativi e adeguati al tipo di dialogo e all'addestramento dell'utente.

- **IMPL-5.1.3.12.1.3-01** `[JS]` `[Globale]` `config.js` deve esporre `responseTimes` (Tabella V, 00-head §2.6) usati da `feedback.js`/`net.js` per timeout e soglie di progress coerenti col tipo di dialogo.
- **IMPL-5.1.3.12.1.3-02** `[JS]` `[Globale]` `log.js` deve registrare i tempi di risposta effettivi, per la verifica di conformità (test T) ai requisiti operativi.

#### 5.1.3.12.1.3.1 – Compatibilità con i tempi di risposta del sistema
*Sintesi:* I tempi di risposta richiesti all'utente devono essere compatibili con i tempi di risposta richiesti al sistema.

- **IMPL-5.1.3.12.1.3.1-01** `[JS]` `[Globale]` `config.js` deve derivare i timeout di input utente dagli stessi valori di `responseTimes`, evitando valori scoordinati tra campo UI e backend.

#### 5.1.3.12.1.3.2 – Coerenza con il carico di lavoro dell'utente
*Sintesi:* I tempi di risposta richiesti all'utente devono rientrare nei limiti imposti dal carico di lavoro totale previsto nell'ambiente operativo (vedi 5.1.2.1.6.4).

- **IMPL-5.1.3.12.1.3.2-01** `[JS]` `[Globale]` `config.js` deve rendere configurabile il tempo massimo di risposta per azione in base a `workloadProfile`, usato da `validate.js` per i timeout dei form.

#### 5.1.3.12.1.3.3 – Blocco del dispositivo di input per tempo di risposta
*Sintesi:* Se l'elaborazione richiede di ritardare input concorrenti e non è disponibile un buffer, deve verificarsi il blocco del dispositivo di input finché il sistema non è pronto ad accettare la transazione successiva.

- **IMPL-5.1.3.12.1.3.3-01** `[JS]` `[Globale]` `net.js`/`state.js` devono impostare `state.busy = true` e disabilitare (`disabled`/`aria-disabled="true"`) i controlli interattivi durante l'elaborazione, fino a risposta ricevuta.
- **IMPL-5.1.3.12.1.3.3-02** `[CSS]` `[Globale]` La classe `.is-busy` deve mostrare `cursor: wait` e stile disabilitato sui controlli bloccati.

#### 5.1.3.12.1.3.4 – Avviso di blocco
*Sintesi:* Deve essere mostrato un avviso che indichi all'utente che si è verificato un blocco.

- **IMPL-5.1.3.12.1.3.4-01** `[JS][HTML]` `[Footer]` `feedback.js` deve chiamare `setStatus("In elaborazione…", "busy")`, mostrato in `#status-line` (`role="status"`) quando si attiva `.is-busy`.

#### 5.1.3.12.1.3.5 – Ripristino del dispositivo di input
*Sintesi:* Quando il sistema è pronto a proseguire dopo un blocco, deve essere presentato un segnale che lo indichi (es. il cursore torna alla forma normale).

- **IMPL-5.1.3.12.1.3.5-01** `[CSS][JS]` `[Globale]` Al termine dell'elaborazione, `state.js` deve rimuovere `.is-busy`/`cursor: wait` e `feedback.js` ripristina lo stato normale di controlli e cursore.

#### 5.1.3.12.1.3.6 – Interruzione per terminare il blocco
*Sintesi:* In caso di blocco del dispositivo di input, l'utente deve poter annullare la transazione causa del blocco prolungato, con funzione tipo UNDO che interrompe l'elaborazione senza RESET del sistema.

- **IMPL-5.1.3.12.1.3.6-01** `[HTML][JS]` `[Footer]` Un controllo `#btn-cancel-request` in `#function-keys` deve restare attivo durante `.is-busy` e invocare `net.js:abortRequest()` (`AbortController`) senza reimpostare `state.js`.

#### 5.1.3.12.1.4 – Semplicità
*Sintesi:* Le relazioni controllo-display devono essere semplici ed esplicite.

- **IMPL-5.1.3.12.1.4-01** `[HTML][CSS]` `[Main]` Ogni controllo in `#content-region` deve essere posizionato vicino al display che modifica e associato tramite `aria-controls`/`for`, senza indirezioni implicite.

#### 5.1.3.12.1.5 – Azioni di controllo (semplici/dirette, conferma per azioni distruttive)
*Sintesi:* Le azioni di controllo devono essere semplici e dirette; le azioni potenzialmente distruttive devono richiedere attenzione prolungata (es. "Sei sicuro?").

- **IMPL-5.1.3.12.1.5-01** `[JS][HTML]` `[Globale]` `dialog.js` deve aprire un `<dialog>` di conferma (`role="alertdialog"`) per ogni azione `data-destructive="true"` prima dell'esecuzione.
- **IMPL-5.1.3.12.1.5-02** `[JS]` `[Globale]` Le azioni non distruttive devono eseguire direttamente al `pointerup` (strategia *last contact*), senza conferme superflue.

#### 5.1.3.12.1.6 – Attuazione accidentale
*Sintesi:* Deve essere previsto un meccanismo per evitare l'attuazione accidentale di azioni di controllo potenzialmente distruttive (es. cancellazione accidentale, dump di memoria).

- **IMPL-5.1.3.12.1.6-01** `[CSS]` `[Globale]` I controlli distruttivi devono rispettare `--touch-gap-min`/`--touch-gap-max` dai controlli adiacenti e non essere il target del focus iniziale.
- **IMPL-5.1.3.12.1.6-02** `[JS]` `[Globale]` Vedi `IMPL-5.1.3.12.1.5-01`: `dialog.js` deve richiedere conferma rinforzata (es. tenere premuto per un valore di progetto in `config.js`) per azioni irreversibili.

#### 5.1.3.12.1.7 – Compatibilità con la competenza dell'utente
*Sintesi:* I controlli devono essere compatibili con i livelli di competenza più bassi previsti; gli utenti esperti devono disporre di opzioni che abbrevino i passaggi richiesti agli inesperti.

- **IMPL-5.1.3.12.1.7-01** `[JS]` `[Globale]` `prefs.js` deve memorizzare `interactionMode` (`novice`|`expert`) in `localStorage`; `nav.js` abilita scorciatoie/comandi diretti solo in modalità esperta.
- **IMPL-5.1.3.12.1.7-02** `[HTML]` `[Main]` Il percorso guidato a menu completo deve restare sempre disponibile in `#nav-region` anche quando le scorciatoie esperte sono attive.

#### 5.1.3.12.1.8 – Disponibilità delle informazioni
*Sintesi:* Le informazioni necessarie per selezionare o immettere una specifica azione di controllo devono essere disponibili all'utente quando la selezione è appropriata.

- **IMPL-5.1.3.12.1.8-01** `[HTML][JS]` `[Main]` Ogni controllo deve esporre `aria-describedby` verso un testo di aiuto in linea (`#aux-region`) visibile quando il controllo diventa selezionabile.

#### 5.1.3.12.1.9 – Display concorrenti
*Sintesi:* I display concorrenti devono soddisfare i criteri di 5.1.3.12.1.9.1–.9.3 (alternative visibili prima della scelta, valore corrente, feedback positivo).

- **IMPL-5.1.3.12.1.9-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.3.12.1.9.1-01`, `IMPL-5.1.3.12.1.9.2-01`, `IMPL-5.1.3.12.1.9.3-01` per il dettaglio implementativo dei tre criteri.

#### 5.1.3.12.1.9.1 – Azioni di controllo (alternative visualizzate prima della selezione)
*Sintesi:* Le azioni di controllo scelte tra un insieme discreto di alternative devono avere tali alternative visualizzate prima della selezione.

- **IMPL-5.1.3.12.1.9.1-01** `[HTML]` `[Main]` I controlli a scelta discreta (`<select>`, `role="radiogroup"`, menu) devono elencare tutte le opzioni nel markup prima di qualunque interazione di selezione.

#### 5.1.3.12.1.9.2 – Valore corrente
*Sintesi:* Il valore corrente di qualunque parametro o variabile con cui l'utente sta interagendo deve essere visualizzato.

- **IMPL-5.1.3.12.1.9.2-01** `[HTML][JS]` `[Main]` Ogni controllo di parametro deve mostrare il valore corrente (es. `<output>`, `aria-valuenow`) aggiornato da `state.js` in tempo reale.

#### 5.1.3.12.1.9.3 – Input di controllo dell'utente
*Sintesi:* Gli input di controllo dell'utente devono produrre una risposta di feedback positivo visualizzata, a indicare l'esecuzione dell'azione richiesta.

- **IMPL-5.1.3.12.1.9.3-01** `[JS]` `[Globale]` `feedback.js:acknowledge(el)` deve applicare `.is-acknowledged` e, se richiesto, beep/vibrazione entro i tempi di Tabella V a ogni input di controllo.

#### 5.1.3.12.1.10 – Processo gerarchico
*Sintesi:* Il numero di livelli gerarchici per controllare un processo o sequenza deve essere minimizzato; non dovrebbero essere usati più di quattro livelli.

- **IMPL-5.1.3.12.1.10-01** `[JS]` `[Globale]` `nav.js` deve rispettare `config.js:maxHierarchyLevels = 4` come vincolo strutturale di `#nav-region`, verificabile per ispezione (I) sull'albero di navigazione.

#### 5.1.3.12.1.10.1 – Formati di display e input
*Sintesi:* I formati di display e input dovrebbero essere simili entro i livelli gerarchici.

- **IMPL-5.1.3.12.1.10.1-01** `[CSS][HTML]` `[Main]` Le viste allo stesso livello gerarchico devono condividere lo stesso template/classi di layout in `#content-region`, senza variazioni di formato tra viste pari-livello.

#### 5.1.3.12.1.10.2 – Posizione corrente
*Sintesi:* Il sistema deve indicare sempre la posizione corrente all'interno della sequenza.

- **IMPL-5.1.3.12.1.10.2-01** `[HTML][JS]` `[Main]` `#nav-region` deve includere un breadcrumb (`nav[aria-label="breadcrumb"]`) aggiornato da `nav.js` con `aria-current="page"` sull'ultimo elemento a ogni cambio vista.

#### 5.1.3.12.1.11 – Memorizzazione da parte dell'utente
*Sintesi:* Il sistema non deve richiedere all'utente di imparare mnemonici, codici, sequenze speciali o lunghe, o istruzioni speciali.

- **IMPL-5.1.3.12.1.11-01** `[HTML][JS]` `[Globale]` I comandi devono essere accessibili tramite controlli visibili con testo (bottoni/menu) in `#content-region`; eventuali scorciatoie da tastiera restano un'alternativa opzionale documentata nell'help (vedi §3.7).

#### 5.1.3.12.1.12 – Tipo di dialogo
*Sintesi:* La scelta del tipo di dialogo (form filling, menu, linguaggio di comando) per il controllo interattivo deve essere compatibile con le caratteristiche dell'utente e i requisiti del compito.

- **IMPL-5.1.3.12.1.12-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.3.12.1.2-01`/`-02`: stessa logica di selezione del tipo di dialogo in `config.js`/`nav.js` basata su `state.user.role`/`skillLevel`.

#### 5.1.3.12.1.13 – Sistemi numerici
*Sintesi:* I sistemi numerici devono soddisfare i criteri di 5.1.3.12.1.13.1–.13.2 (sistema decimale, chiarezza di eventuale sistema alternativo).

- **IMPL-5.1.3.12.1.13-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.3.12.1.13.1-01` e `IMPL-5.1.3.12.1.13.2-01`.

#### 5.1.3.12.1.13.1 – Sistema decimale
*Sintesi:* I dati numerici visualizzati o richiesti per l'input di controllo devono essere in formato decimale anziché binario/ottale/esadecimale, salvo necessità operativa.

- **IMPL-5.1.3.12.1.13.1-01** `[JS]` `[Globale]` `i18n.js` deve formattare di default tutti i valori numerici in base 10 (`Intl.NumberFormat`); campi non decimali richiedono opt-in esplicito in `config.js`.

#### 5.1.3.12.1.13.2 – Sistema numerico alternativo
*Sintesi:* Quando i dati numerici non sono in formato decimale per necessità operative, il sistema numerico alternativo usato deve essere chiaro all'utente.

- **IMPL-5.1.3.12.1.13.2-01** `[HTML]` `[Main]` I campi con base non decimale devono mostrare un'etichetta/suffisso esplicito (es. `0x`, badge "HEX") accanto al valore, definito in `i18n.js`.

#### 5.1.3.12.1.14 – Manipolazione dei dati
*Sintesi:* Il sistema deve consentire all'utente di manipolare i dati senza conoscenze specialistiche dei meccanismi interni di memorizzazione e recupero.

- **IMPL-5.1.3.12.1.14-01** `[JS]` `[Globale]` Le operazioni sui dati in `#content-region` devono passare da API applicative di `state.js`/`net.js` come azioni di dominio (es. "salva", "filtra"), senza esporre la struttura dati interna.

#### 5.1.3.12.1.15 – Vincoli di elaborazione del computer
*Sintesi:* La sequenza di selezione delle transazioni deve essere dettata dalle scelte dell'utente e non da vincoli interni di elaborazione.

- **IMPL-5.1.3.12.1.15-01** `[JS]` `[Globale]` `nav.js` non deve forzare un ordine di navigazione basato su stato di caricamento interno; i controlli restano selezionabili nell'ordine scelto dall'utente, con attese gestite da `net.js` in background.

#### 5.1.3.12.1.16 – Feedback per input corretto
*Sintesi:* Le risposte di feedback per un input corretto devono consistere in variazioni di stato/valore degli elementi di display controllati, presentate in forma attesa e logicamente naturale.

- **IMPL-5.1.3.12.1.16-01** `[JS]` `[Globale]` `feedback.js:acknowledge(el)` deve aggiornare direttamente lo stato/valore del display controllato (non un messaggio generico separato), in forma coerente con l'azione eseguita.

#### 5.1.3.12.1.19 – Visualizzazione dei dati di input di controllo
*Sintesi:* La presenza e la posizione dei dati immessi dall'utente devono essere chiaramente indicate; i dati non devono trarre in inganno riguardo a nomenclatura, unità di misura, sequenza dei passi o tempificazione.

- **IMPL-5.1.3.12.1.19-01** `[HTML]` `[Main]` Ogni campo deve mostrare etichetta, unità di misura (`aria-describedby`) e valore allineati senza ambiguità; `i18n.js` garantisce coerenza terminologica.
- **IMPL-5.1.3.12.1.19-02** `[CSS]` `[Main]` Lo stato "valore immesso dall'utente" deve essere distinguibile (es. `.field--dirty`) dal valore di default/calcolato dal sistema.

#### 5.1.3.12.2.2.3 – Selezione di menu per puntamento
*Sintesi:* Se la selezione di menu avviene per puntamento, devono essere previste due azioni distinte: la prima designa l'opzione, una seconda azione separata ne conferma la selezione per l'elaborazione.

- **IMPL-5.1.3.12.2.2.3-01** `[JS][HTML]` `[Main]` Le voci di menu devono distinguere designazione (`pointerdown`/evidenziazione, `aria-selected="true"`) da un evento di conferma separato (`pointerup` su azione "Conferma") prima dell'esecuzione.

#### 5.1.3.12.2.2.5 – Selezioni di menu in pila (stack)
*Sintesi:* Agli utenti deve essere fornita la capacità di accatastare selezioni di menu, effettuando più selezioni senza che ogni menu venga visualizzato singolarmente.

- **IMPL-5.1.3.12.2.2.5-01** `[JS]` `[Globale]` `nav.js` deve supportare una coda `commandStack` che accetta più selezioni in sequenza rapida (es. comando testuale o percorso digitato) senza mostrare ogni menu intermedio.

#### 5.1.3.12.2.9 – Chiamata di funzione diretta
*Sintesi:* Se sono previsti più livelli di menu gerarchici, deve essere fornita una capacità di chiamata diretta tale che l'utente esperto non debba attraversare più livelli di menu.

- **IMPL-5.1.3.12.2.9-01** `[JS]` `[Globale]` `nav.js` deve esporre comandi diretti/route interne (`#/goto/<viewId>`) o command palette per raggiungere qualunque vista senza passare dai menu intermedi, in modalità esperta (vedi `IMPL-5.1.3.12.1.7-01`).

#### 5.1.3.12.2.14 – Ritorno al livello superiore
*Sintesi:* Nei menu gerarchici, l'utente deve poter tornare al livello immediatamente superiore con una singola azione fino al menu/display iniziale; non dovrebbero essere usati più di tre livelli.

- **IMPL-5.1.3.12.2.14-01** `[HTML][JS]` `[Header]` `#btn-back` nell'header deve invocare `nav.js:goUp()` risalendo di un livello per azione singola; `config.js:maxMenuLevels = 3` vincola la profondità dei menu di `#nav-region`.

#### 5.1.3.12.2.15 – Ritorno al livello più alto
*Sintesi:* Deve essere fornita una funzione per richiamare direttamente il menu/display iniziale di livello più alto, senza attraversare la gerarchia.

- **IMPL-5.1.3.12.2.15-01** `[HTML][JS]` `[Header]` `#btn-home` nell'header deve invocare `nav.js:goHome()` per tornare direttamente alla vista top-level in un solo passaggio.

#### 5.1.3.12.3.8.2 – Movimento automatico
*Sintesi:* Il cursore deve spostarsi automaticamente al campo successivo quando viene raggiunta la fine del campo corrente.

- **IMPL-5.1.3.12.3.8.2-01** `[JS]` `[Main]` I campi dei form devono gestire l'evento `input` per spostare il focus al campo successivo al raggiungimento di `maxlength`, mantenendo coerente la navigazione da tastiera/tab.

#### 5.1.3.12.3.14 – Input utente ritardato
*Sintesi:* Se l'input utente sarà ritardato, l'utente deve poter designare il campo per indicare che il dato mancante è ritardato, non dimenticato.

- **IMPL-5.1.3.12.3.14-01** `[HTML][JS]` `[Main]` I campi devono offrire un controllo `data-deferred` (es. checkbox "Da completare in seguito") che applica `.field--deferred` ed esclude temporaneamente il campo dalla validazione obbligatoria di `validate.js`.

#### 5.1.3.12.5.5 – Abbreviazioni
*Sintesi:* L'utente deve poter immettere il nome completo o un'abbreviazione per qualunque comando di più di cinque caratteri.

- **IMPL-5.1.3.12.5.5-01** `[JS]` `[Main]` L'interprete di comandi deve risolvere abbreviazioni univoche tramite `nav.js:resolveCommand(input)` per ogni comando con nome > 5 caratteri.

#### 5.1.3.12.6.3 – Domande multiple
*Sintesi:* Non deve essere richiesto all'utente di rispondere a più domande contemporaneamente.

- **IMPL-5.1.3.12.6.3-01** `[HTML][JS]` `[Main]` I dialoghi domanda-risposta (`dialog.js`) devono presentare una domanda per volta, non più campi obbligatori simultanei in un unico prompt modale.

#### 5.1.3.12.8 – Interazione grafica
*Sintesi:* La progettazione dell'interazione grafica deve soddisfare i criteri di 5.1.3.12.8.1–.8.3 (uso, menu iconici, etichette testuali supplementari).

- **IMPL-5.1.3.12.8-01** `[JS]` `[Globale]` Vedi `IMPL-5.1.3.12.8.1-01`, `IMPL-5.1.3.12.8.2-01`, `IMPL-5.1.3.12.8.3-01`.

#### 5.1.3.12.8.1 – Uso
*Sintesi:* L'interazione grafica come dialogo può essere considerata per utenti occasionali, come ausilio grafico a supplemento di altri tipi di controllo interattivo.

- **IMPL-5.1.3.12.8.1-01** `[HTML]` `[Main]` `#content-region` può offrire controlli grafici supplementari (icone, mappe) in aggiunta, mai in sostituzione esclusiva, dei controlli testuali/menu standard.

#### 5.1.3.12.8.2 – Menu iconici
*Sintesi:* Con utenti di background linguistici diversi, possono essere usati menu grafici con icone per le opzioni di controllo; per uso da personale militare straniero, il design delle icone deve essere coerente con le variabili culturali ed etniche applicabili.

- **IMPL-5.1.3.12.8.2-01** `[JS]` `[Globale]` `i18n.js` deve permettere la selezione di set di icone localizzati/validati culturalmente in base a `state.user.locale`, configurabili in `config.js`.

#### 5.1.3.12.8.3 – Etichette testuali supplementari
*Sintesi:* Dove le icone rappresentano azioni di controllo nei menu, devono essere visualizzate etichette testuali, o rese disponibili per la visualizzazione, con ciascuna icona.

- **IMPL-5.1.3.12.8.3-01** `[HTML]` `[Main]` Ogni icona di menu deve avere testo visibile adiacente oppure `aria-label`/`title` accessibile anche al focus da tastiera, non solo al passaggio del mouse.

### 3.6 Tipi di dialogo: comandi, domanda-risposta, query, menu, form

Requisiti sui linguaggi di comando, i dialoghi domanda-risposta, le query, i menu (a tendina, a icone, gerarchici) e i form di immissione dati, incluse le convenzioni di gestione dei tasti funzione fissi e variabili.

#### 5.1.3.11.1.6 – Immissione di insiemi di dati ripetitivi
*Sintesi (shall):* Quando è richiesta l'immissione ripetitiva di insiemi di dati, la validazione di ciascun insieme deve essere completata prima che possa iniziare una nuova transazione.

- **IMPL-5.1.3.11.1.6-01** `[JS]` `[Main]` `validate.js` deve eseguire la validazione completa del data set corrente al submit del form in `#content-region` e impedire `form.requestSubmit()`/invio della transazione successiva finché tutti i campi non risultano validi (`aria-invalid="false"`).
- **IMPL-5.1.3.11.1.6-02** `[HTML][CSS]` `[Main]` Il form deve usare `<fieldset>` per ciascun insieme ripetitivo con uno stato visivo `.is-busy`/`[aria-disabled]` sul passaggio al set successivo finché la validazione non è conclusa.

#### 5.1.3.11.1.8 – Abbreviazioni, acronimi, mnemonici e codici
*Sintesi (shall):* Quando si usano abbreviazioni/acronimi/mnemonici/codici per abbreviare l'immissione dati, devono essere distintivi e avere una relazione intuitiva con il linguaggio naturale o la terminologia di settore.

- **IMPL-5.1.3.11.1.8-01** `[JS]` `[Globale]` `config.js` deve definire la tabella dei codici/abbreviazioni ammessi per campo, con mappatura a etichetta estesa usata da `i18n.js` per i suggerimenti.
- **IMPL-5.1.3.11.1.8-02** `[HTML]` `[Main]` I campi `<input>` che accettano codici devono avere `title` e `aria-describedby` che rimandano al significato esteso del codice/mnemonico.

#### 5.1.3.11.1.8.1 – Lunghezza
*Sintesi (shall):* Un'abbreviazione non deve essere più lunga del necessario per garantire un significato chiaro e univoco.

- **IMPL-5.1.3.11.1.8.1-01** `[JS]` `[Main]` `validate.js` deve verificare in `config.js` che ogni abbreviazione configurata non superi la lunghezza minima necessaria (valore di progetto, default 4 caratteri) e segnalare in fase di revisione i duplicati ambigui.

#### 5.1.3.11.1.8.2 – Riferimento
*Sintesi (shall):* Un riferimento facilmente accessibile dei significati delle abbreviazioni deve essere reso disponibile all'operatore.

- **IMPL-5.1.3.11.1.8.2-01** `[HTML][JS]` `[Footer][Main]` Deve esistere un pannello di aiuto (`#aux-region` o dialog da `#btn-help`) che elenchi tutte le abbreviazioni/codici attivi e i relativi significati, interrogabile da `i18n.js`.

#### 5.1.3.11.1.8.3 – Coerenza
*Sintesi (shall):* Abbreviazioni, acronimi, mnemonici e codici devono essere coerenti all'interno e tra i sistemi/applicazioni usati dalla stessa popolazione di utenti.

- **IMPL-5.1.3.11.1.8.3-01** `[JS]` `[Globale]` La tabella di codici in `config.js` è centralizzata e condivisa da tutti i moduli (`validate.js`, `i18n.js`, `nav.js`); nessun modulo deve definire codici locali alternativi per lo stesso concetto.

#### 5.1.3.11.1.11 – Modifica dei dati
*Sintesi (shall):* Quando un utente richiede la modifica (o cancellazione) di un dato non attualmente visualizzato, deve essere presentata l'opzione di visualizzare il vecchio valore prima di confermare la modifica.

- **IMPL-5.1.3.11.1.11-01** `[JS][HTML]` `[Main]` `dialog.js` deve aprire un `<dialog>` di conferma che mostri il valore precedente (`data-old-value`) accanto al nuovo valore prima di eseguire l'aggiornamento o la cancellazione.

#### 5.1.3.11.1.12 – Unico metodo di immissione dati
*Sintesi (shall):* I metodi di immissione dati e le visualizzazioni non devono richiedere all'utente di passare da un metodo di immissione a un altro.

- **IMPL-5.1.3.11.1.12-01** `[HTML][CSS]` `[Main]` Ogni form in `#content-region` deve usare un solo paradigma di immissione coerente (es. solo tastiera a schermo touch oppure solo controlli nativi) per tutti i campi dello stesso form; non alternare `<input type="text">` libero e tastierini numerici dedicati nello stesso task.

#### 5.1.3.11.1.13 – Visualizzazione dell'immissione dati
*Sintesi (shall):* Dove l'immissione dati su display elettronico è permessa solo in aree prescritte, deve essere fornita una chiara definizione visiva dei campi di immissione.

- **IMPL-5.1.3.11.1.13-01** `[CSS]` `[Main]` I campi editabili devono avere un bordo/sfondo distintivo (`border: 1px solid var(--color-fg)`, `background: var(--color-bg)` con contrasto dedicato) rispetto alle aree di sola lettura, applicato tramite classe `.field-editable`.

#### 5.1.3.11.3.3 – Lunghezza
*Sintesi (shall):* La lunghezza dei singoli dati non deve superare quanto necessario per svolgere il compito.

- **IMPL-5.1.3.11.3.3-01** `[HTML][JS]` `[Main]` Ogni `<input>`/`<textarea>` deve impostare `maxlength` al valore minimo necessario definito in `config.js` per il campo, verificato da `validate.js`.

#### 5.1.3.11.3.4 – Giustificazione
*Sintesi (shall):* Nell'immissione di dati tabulari l'utente non deve dover giustificare a destra/sinistra; il sistema deve giustificare automaticamente le colonne rispetto al punto decimale, al margine sinistro o destro, a seconda del tipo di dato.

- **IMPL-5.1.3.11.3.4-01** `[CSS]` `[Main]` Le celle tabellari numeriche devono usare `text-align: right` con allineamento al punto decimale (`font-variant-numeric: tabular-nums`); le celle testuali `text-align: left`, applicato automaticamente in base al tipo colonna, senza intervento dell'utente.
- **IMPL-5.1.3.11.3.4-02** `[JS]` `[Main]` `validate.js` deve formattare il valore immesso (padding/allineamento) alla perdita del focus, senza richiedere all'utente di digitare spazi di giustificazione.

#### 5.1.3.11.3.6 – Minimizzazione della digitazione
*Sintesi (shall):* La quantità di digitazione richiesta non deve superare quanto necessario per svolgere il compito.

- **IMPL-5.1.3.11.3.6-01** `[JS][HTML]` `[Main]` I form devono usare `<select>`, `autocomplete`, valori precompilati da `state.js` e default ragionevoli per ridurre al minimo la digitazione manuale, specialmente su schermo touch.

#### 5.1.3.11.3.7 – Minimizzazione dei tasti di shift
*Sintesi (shall):* L'uso delle funzioni di shift della tastiera deve essere minimizzato durante le transazioni di immissione dati.

- **IMPL-5.1.3.11.3.7-01** `[JS]` `[Main]` La tastiera touch virtuale gestita da `feedback.js`/componente input deve impostare il case (maiuscolo/minuscolo) e il set di caratteri più probabile per il campo (es. numerico, maiuscolo per codici) evitando cambi di layout/shift ripetuti.

#### 5.1.3.11.3.8 – Modifica dei dati
*Sintesi (shall):* Nell'immissione dati a tastiera devono essere forniti mezzi per consentire agli utenti di modificare le immissioni precedenti sovrascrivendo il testo esistente o inserendo nuovo testo in qualsiasi posizione del campo.

- **IMPL-5.1.3.11.3.8-01** `[HTML]` `[Main]` I campi `<input>`/`<textarea>` devono supportare nativamente sia la modalità overtype (tasto Ins) sia l'inserimento di testo in qualsiasi posizione del cursore, senza disabilitare la selezione/posizionamento del cursore.

#### 5.1.3.11.4.2 – Standardizzazione
*Sintesi (shall):* I tasti funzione fissi devono essere comuni in tutto il sistema.

- **IMPL-5.1.3.11.4.2-01** `[HTML][CSS]` `[Footer]` `#function-keys` deve usare lo stesso set di `<button class="fn-key" data-fn="...">` con etichette/posizioni coerenti in tutte le viste dell'applicazione, definito una sola volta in `nav.js`.

#### 5.1.3.11.4.3 – Coerenza funzionale
*Sintesi (shall):* Una volta assegnata una funzione a un tasto, questa non deve essere riassegnata a una funzione diversa per un dato utente.

- **IMPL-5.1.3.11.4.3-01** `[JS]` `[Footer]` `state.js`/`config.js` deve mantenere l'associazione tasto→funzione come costante per sessione utente; `nav.js` non deve rimappare dinamicamente lo stesso `data-fn` a comandi diversi.

#### 5.1.3.11.4.4 – Disponibilità
*Sintesi (shall):* I tasti funzione fissi devono essere scelti per controllare funzioni continuamente disponibili (il blocco dei tasti funzione fissi deve essere minimizzato).

- **IMPL-5.1.3.11.4.4-01** `[JS]` `[Footer]` `nav.js` deve mantenere abilitati (`[disabled]` assente) i tasti di `#function-keys` associati a funzioni sempre disponibili (home, help, back) in ogni stato dell'applicazione.

#### 5.1.3.11.4.4.1 – Tasti funzione non usati al passo corrente
*Sintesi (shall):* In ogni passo di una sequenza di transazione, i tasti funzione non usati per gli input correnti devono essere temporaneamente disabilitati sotto controllo computerizzato.

- **IMPL-5.1.3.11.4.4.1-01** `[JS][HTML]` `[Footer]` `nav.js` deve impostare `[disabled]`/`[aria-disabled="true"]` sui `.fn-key` non pertinenti al passo corrente della transazione in `#content-region`, riattivandoli automaticamente al cambio di passo.

#### 5.1.3.11.4.5 – Tasti non attivi
*Sintesi (shall):* I tasti funzione fissi non attivi devono essere sostituiti da un tasto vuoto o visualizzati in grigio (grayed-out).

- **IMPL-5.1.3.11.4.5-01** `[CSS]` `[Footer]` La classe `.fn-key[disabled]` deve applicare uno stile "grayed-out" (`opacity`, `color: var(--color-inactive)`) distinto dallo stato attivo, senza rimuovere l'etichetta.

#### 5.1.3.11.4.6 – Raggruppamento
*Sintesi (shall):* I tasti funzione fissi devono essere logicamente raggruppati e posizionati in luoghi distintivi sulla tastiera.

- **IMPL-5.1.3.11.4.6-01** `[HTML][CSS]` `[Footer]` `#function-keys` deve raggruppare i `.fn-key` in sotto-contenitori semantici (es. `<div class="fn-group" data-group="nav|edit|system">`) con separazione visiva (`gap`, bordo) per categoria funzionale.

#### 5.1.3.11.4.7 – Attuazione
*Sintesi (shall):* Salvo quando usato per commutare tra due stati opposti, un tasto funzione fisso deve richiedere una sola attuazione per compiere la sua funzione.

- **IMPL-5.1.3.11.4.7-01** `[JS]` `[Footer]` Il gestore `pointerup` dei `.fn-key` in `nav.js` deve eseguire l'azione con una singola attuazione; solo i tasti con `[aria-pressed]` (toggle) possono alternare stato ad ogni attuazione.

#### 5.1.3.11.4.8 – Feedback
*Sintesi (shall):* Quando l'attivazione di un tasto funzione fisso non produce una risposta naturale osservabile immediatamente, l'utente deve ricevere un'indicazione di riconoscimento del sistema.

- **IMPL-5.1.3.11.4.8-01** `[JS]` `[Footer]` `feedback.js.acknowledge(el)` deve essere invocato su ogni attuazione di `.fn-key` priva di effetto visivo immediato, mostrando un feedback (evidenziazione breve, `beep()`) entro i tempi di Tabella V (≤0,1 s), vedi `IMPL-5.1.3.11.1.6-01`.

#### 5.1.3.11.4.9 – Etichette dei tasti funzione fissi
*Sintesi (shall):* Le assegnazioni dei tasti funzione fissi devono sempre essere visualizzate.

- **IMPL-5.1.3.11.4.9-01** `[HTML]` `[Footer]` Ogni `.fn-key` deve contenere sempre un'etichetta testuale visibile (non solo icona) in `#function-keys`, mai vuota.

#### 5.1.3.11.4.9.1 – Marcatura diretta
*Sintesi (shall):* Le assegnazioni dei tasti funzione fissi devono essere visualizzate tramite marcatura diretta (direct marking).

- **IMPL-5.1.3.11.4.9.1-01** `[HTML][CSS]` `[Footer]` L'etichetta del `.fn-key` deve essere resa come testo/icona renderizzato direttamente sul tasto (contenuto del `<button>`), non tramite tooltip o legenda separata.

#### 5.1.3.11.4.9.2 – Abbreviazioni
*Sintesi (shall):* Dove sono necessarie abbreviazioni per i tasti funzione fissi, devono essere usate abbreviazioni standard.

- **IMPL-5.1.3.11.4.9.2-01** `[JS]` `[Footer]` Le etichette abbreviate dei `.fn-key` devono provenire dalla tabella standard di `i18n.js`/`config.js` (vedi `IMPL-5.1.3.11.1.8-01`), non da testo libero per-vista.

#### 5.1.3.11.4.10 – Pressione prolungata dei tasti funzione
*Sintesi (shall):* Eccetto per il tasto DELETE, la pressione prolungata dei tasti funzione non deve ripetere la funzione.

- **IMPL-5.1.3.11.4.10-01** `[JS]` `[Footer]` Il gestore eventi di `.fn-key` deve rispondere solo a `pointerup` (singola attuazione), senza listener `pointerdown`/intervallo ripetuto, eccetto per il tasto `data-fn="delete"` che può usare repeat controllato in `config.js`.

#### 5.1.3.11.5.2 – Azione critica
*Sintesi (shall):* Una singola immissione su un tasto funzione variabile non deve produrre un'azione critica.

- **IMPL-5.1.3.11.5.2-01** `[JS]` `[Footer]` I tasti funzione variabili (`.vfn-key`, definiti da `state.js` per vista) associati ad azioni critiche (`data-critical="true"`) devono sempre richiedere un passaggio di conferma gestito da `dialog.js`, mai eseguire l'azione alla prima attuazione.

#### 5.1.3.11.5.3 – Conferma azione critica
*Sintesi (shall):* Le azioni critiche devono richiedere conferma quando avviate con un tasto funzione variabile.

- **IMPL-5.1.3.11.5.3-01** `[JS][HTML]` `[Footer][Main]` `dialog.js` deve aprire un `<dialog>` di conferma (`Conferma`/`Annulla`) prima di eseguire qualsiasi azione con `data-critical="true"` originata da un tasto funzione variabile, vedi `IMPL-5.1.3.11.5.2-01`.

#### 5.1.3.11.5.4 – Visualizzazione dello stato
*Sintesi (shall):* Quando l'effetto di un tasto funzione varia, lo stato del tasto deve essere visualizzato.

- **IMPL-5.1.3.11.5.4-01** `[HTML][CSS]` `[Footer]` I `.vfn-key` il cui effetto cambia in base al contesto devono mostrare l'etichetta/icona corrente aggiornata da `state.js` e un attributo `[aria-pressed]`/`[data-state]` riflesso in CSS.

#### 5.1.3.11.5.5 – Funzioni predefinite riprogrammate o disattivate
*Sintesi (shall):* Quando i tasti con funzioni predefinite etichettate vengono riprogrammati o disattivati, un avviso visivo deve segnalare all'utente che la funzione standard non è attualmente accessibile da quel tasto.

- **IMPL-5.1.3.11.5.5-01** `[CSS][JS]` `[Footer]` Quando `nav.js` riassegna o disattiva un `.vfn-key` rispetto alla funzione standard, deve applicare una classe `.is-remapped`/badge visivo (`--color-warning`) e aggiornare `aria-label` di conseguenza.

#### 5.1.3.11.5.8 – Facile ritorno alle funzioni di base
*Sintesi (shall):* Dove le funzioni assegnate a un insieme di tasti funzione cambiano in seguito a selezione dell'utente, deve essere fornito un mezzo semplice per tornare alle funzioni iniziali di base.

- **IMPL-5.1.3.11.5.8-01** `[HTML][JS]` `[Footer]` `#function-keys` deve includere un tasto persistente `data-fn="base-level"` (es. "Base") che richiama `nav.js` per ripristinare l'assegnazione iniziale dei `.vfn-key` in un solo tocco.

#### 5.17.2.1.1 – Uso
*Sintesi (may):* Il linguaggio di comando può essere usato per compiti con un'ampia gamma di input utente e dove la familiarità con il linguaggio di comando sfrutta flessibilità e velocità di questa tecnica di controllo.

- **IMPL-5.17.2.1.1-01** `[JS]` `[Main]` Deve esistere una modalità a riga di comando opzionale (`command-palette`, componente in `#content-region`/`#aux-region`) gestita da un modulo dedicato (es. `cmd.js`/estensione di `nav.js`), abilitabile dall'utente esperto in `prefs.js`, senza sostituire i controlli grafici equivalenti.

#### 5.17.2.1.2 – Punto di vista dell'utente
*Sintesi (shall):* I comandi del linguaggio di comando devono essere logicamente correlati a ciò che l'utente si aspetta che il comando avvii.

- **IMPL-5.17.2.1.2-01** `[JS]` `[Globale]` I nomi dei comandi definiti in `config.js` devono corrispondere semanticamente all'azione risultante (es. `open`, `close`, `ack`) e seguire una convenzione di naming verbo-oggetto verificabile in revisione.

#### 5.17.2.1.3 – Distintività dei nomi
*Sintesi (shall):* I nomi dei comandi devono essere distinti l'uno dall'altro.

- **IMPL-5.17.2.1.3-01** `[JS]` `[Globale]` `validate.js`/strumento di build deve verificare che la tabella comandi in `config.js` non contenga nomi duplicati o ambigui (prefissi comuni risolvibili solo con troncamento).

#### 5.17.2.1.4 – Punteggiatura e caratteri speciali
*Sintesi (shall):* Il dialogo a linguaggio di comando deve minimizzare l'uso di punteggiatura e caratteri speciali.

- **IMPL-5.17.2.1.4-01** `[JS]` `[Main]` Il parser dei comandi deve accettare sintassi basata su parole semplici separate da spazio, evitando simboli come `;`, `|`, `&&` salvo operatori minimi indispensabili definiti in `config.js`.

#### 5.17.2.1.5 – Standardizzazione
*Sintesi (shall):* Tutti i comandi e le abbreviazioni corrispondenti devono essere identici tra sistemi e piattaforme.

- **IMPL-5.17.2.1.5-01** `[JS]` `[Globale]` La tabella comandi/abbreviazioni deve risiedere in `config.js` condiviso e non essere duplicata o ridefinita per piattaforma/vista, vedi `IMPL-5.1.3.11.1.8.3-01`.

#### 5.17.2.1.6 – Posizione standard
*Sintesi (shall):* I comandi devono essere immessi e visualizzati in una posizione standard sull'interfaccia utente del software.

- **IMPL-5.17.2.1.6-01** `[HTML][CSS]` `[Main]` La barra/campo comandi deve avere una posizione fissa (es. in testa a `#content-region` o in `#aux-region`), identica in tutte le viste in cui è disponibile.

#### 5.17.2.1.7 – Prompt dei comandi
*Sintesi (shall):* All'utente deve essere fornito un mezzo, come prompt o tooltip, per determinare i parametri richiesti o le opzioni disponibili per l'immissione del comando.

- **IMPL-5.17.2.1.7-01** `[HTML][JS]` `[Main]` Il campo comandi deve mostrare un suggerimento inline (`<datalist>`/popup di autocompletamento aggiornato da `cmd.js`) con parametri attesi e opzioni disponibili man mano che l'utente digita.

#### 5.17.2.1.8 – Complessità
*Sintesi (shall):* Il linguaggio di comando deve adattarsi ai livelli di abilità degli utenti, da principianti a esperti.

- **IMPL-5.17.2.1.8-01** `[JS]` `[Globale]` `prefs.js` deve esporre una preferenza "modalità esperto/guidata" che mostra/nasconde suggerimenti estesi e consente forme abbreviate dei comandi solo in modalità esperta.

#### 5.17.2.1.9 – Definizione utente di macro
*Sintesi (shall):* Il dialogo a comando non deve permettere all'utente di nominare le macro con nomi di comando esistenti ("parole chiave riservate").

- **IMPL-5.17.2.1.9-01** `[JS]` `[Main]` `validate.js` deve rifiutare (errore in `#message-line`) la creazione di una macro il cui nome coincide con una voce della tabella comandi riservati in `config.js`.

#### 5.17.2.1.10 – Tecniche standard per la modifica dei comandi
*Sintesi (shall):* Le immissioni di comandi e dati devono essere modificate con le stesse tecniche.

- **IMPL-5.17.2.1.10-01** `[HTML][JS]` `[Main]` Il campo di immissione comandi deve essere un `<input>` standard con le stesse modalità di editing (overtype/inserimento, selezione) degli altri campi dati, vedi `IMPL-5.1.3.11.3.8-01`.

#### 5.17.2.1.11 – Comandi distruttivi
*Sintesi (shall):* Se un'immissione di comando ha conseguenze distruttive, il sistema deve visualizzare le conseguenze del comando affinché l'utente possa rivedere e annullare o confermare prima dell'esecuzione.

- **IMPL-5.17.2.1.11-01** `[JS][HTML]` `[Main]` Prima di eseguire un comando marcato `data-destructive="true"`, `dialog.js` deve mostrare un riepilogo delle conseguenze in un `<dialog>` con azioni "Conferma"/"Annulla", vedi `IMPL-5.1.3.11.5.3-01`.

#### 5.17.2.2.1 – Uso
*Sintesi (may):* I dialoghi domanda-risposta possono essere usati per compiti di immissione dati di routine dove i dati sono noti, il loro ordine può essere vincolato e gli utenti avranno poco o nessun addestramento.

- **IMPL-5.17.2.2.1-01** `[HTML][JS]` `[Main]` Per task guidati verso utenti non addestrati, deve essere disponibile un componente wizard "domanda-risposta" (sequenza di `<fieldset>`/step in `#content-region` gestita da `nav.js`) in alternativa al form libero.

#### 5.17.2.2.2 – Domande visualizzate separatamente
*Sintesi (shall):* Per domande multiple in una dialog box, ogni domanda deve essere elencata separatamente.

- **IMPL-5.17.2.2.2-01** `[HTML][CSS]` `[Main]` Ogni domanda del wizard deve essere un elemento distinto (`<label>`/blocco) con separazione visiva (margine minimo `--touch-gap-min`) dalle altre, mai concatenata in un unico testo.

#### 5.17.2.2.3 – Area di risposta distinta
*Sintesi (shall):* Quando sono richieste risposte a una domanda, ogni domanda deve avere un'area di risposta separata e distinta.

- **IMPL-5.17.2.2.3-01** `[HTML][CSS]` `[Main]` Ogni domanda deve associare un unico campo di risposta tramite `<label for>`/`aria-labelledby`, con area di tocco dedicata ≥ `--touch-target-min` e non condivisa con altre domande.

#### 5.17.2.2.4 – Riepilogo delle risposte precedenti
*Sintesi (shall):* Quando una serie di domande poste dal sistema sono correlate, le risposte alle domande precedenti devono essere visualizzate se forniscono contesto utile per rispondere alla domanda corrente.

- **IMPL-5.17.2.2.4-01** `[JS][HTML]` `[Main]` Il wizard deve mostrare un riepilogo read-only delle risposte precedenti pertinenti (da `state.js`) sopra/accanto alla domanda corrente quando interrelate.

#### 5.17.2.2.5 – Capacità di documento sorgente
*Sintesi (shall):* Quando le domande richiedono l'immissione di dati da un documento sorgente, la sequenza delle domande deve corrispondere alla sequenza dei dati nel documento sorgente.

- **IMPL-5.17.2.2.5-01** `[JS]` `[Main]` L'ordine degli step del wizard configurato in `config.js` deve essere configurabile per rispecchiare la sequenza del documento sorgente di riferimento del task, documentata nella configurazione del form.

#### 5.17.2.3.1 – Organizzazione naturale dei dati
*Sintesi (should):* I linguaggi di query dovrebbero riflettere una struttura/organizzazione dei dati percepita dagli utenti come naturale, permettendo il recupero con una singola query anche se i dati risiedono in file diversi.

- **IMPL-5.17.2.3.1-01** `[JS]` `[Main]` L'interfaccia di query (`net.js`) deve esporre un modello dati unificato per topic (es. "allarmi", "sessioni") indipendentemente dalle sorgenti/endpoint sottostanti, permettendo una singola query utente per topic.

#### 5.17.2.3.2 – Rappresentazione coerente dell'organizzazione dei dati
*Sintesi (shall):* Deve essere stabilita un'unica rappresentazione dell'organizzazione dei dati per la formulazione delle query (l'utente non deve sapere se query diverse accedono a database diversi tramite percorsi diversi).

- **IMPL-5.17.2.3.2-01** `[JS]` `[Globale]` `net.js` deve incapsulare il routing verso endpoint/database multipli dietro un'unica API di query consumata dall'interfaccia, senza esporre dettagli di sorgente nella UI.

#### 5.17.2.3.3 – Formulazione orientata al compito
*Sintesi (shall):* Gli utenti devono poter specificare i dati richiesti in una query senza dover specificare dove sono memorizzati.

- **IMPL-5.17.2.3.3-01** `[HTML][JS]` `[Main]` Il form di query deve esporre solo campi semantici (es. "intervallo date", "tipo evento") e mai selettori di tabella/database fisico.

#### 5.17.2.3.4 – Logica per collegare le query
*Sintesi (should):* Il linguaggio di query dovrebbe includere elementi logici che permettano agli utenti di collegare (es. "e", "o") query sequenziali in un'unica immissione.

- **IMPL-5.17.2.3.4-01** `[HTML][JS]` `[Main]` Il builder di query deve offrire controlli per combinare condizioni con operatori "e"/"o" (es. `<select>` tra i filtri), tradotti da `i18n.js` in linguaggio naturale.

#### 5.17.2.3.5 – Conferma del recupero dati su larga scala
*Sintesi (shall):* Se la richiesta di query dell'utente recupererà una grande quantità di dati, all'utente devono essere fornite le opzioni di continuare, annullare o affinare la query durante l'elaborazione.

- **IMPL-5.17.2.3.5-01** `[JS][HTML]` `[Main]` Quando `net.js` stima un risultato oltre una soglia (`config.js`), `dialog.js` deve presentare un `<dialog>` con azioni "Continua"/"Annulla"/"Affina query" prima di proseguire.

#### 5.17.2.3.6 – Stima del tempo residuo
*Sintesi (shall):* Quando l'elaborazione richiede molto tempo, all'utente deve essere fornita un'indicazione dello stato che includa una stima della proporzione residua in termini di tempo o quantità.

- **IMPL-5.17.2.3.6-01** `[JS][HTML]` `[Footer]` `#progress-indicator` deve mostrare percentuale/tempo stimato residuo aggiornato da `net.js`/`feedback.js.showProgress()` per ogni query che supera 1 s (vedi Tabella V in 00-head.md §2.6).

#### 5.17.3.1.1 – Barre di menu a tendina
*Sintesi (shall):* I menu a tendina contengono una lista di comandi contestuali presentati in una barra di menu lungo il bordo superiore di una finestra. La barra di menu deve contenere un insieme di etichette di categoria (es. "Modifica") da cui si accede ai menu a tendina.

- **IMPL-5.17.3.1.1-01** `[HTML]` `[Header][Main]` La barra di menu principale deve essere realizzata come `<nav>`/`<ul role="menubar">` con voci `role="menuitem"` per categoria (es. "File", "Modifica"), collocata in `#nav-region` o `#app-header`.
- **IMPL-5.17.3.1.1-02** `[JS]` `[Main]` `nav.js` deve aprire il menu a tendina associato alla categoria selezionata (`aria-expanded="true"`), mostrando l'elenco dei comandi contestuali di quella categoria.

#### 5.17.3.1.1.1 – Uso
*Sintesi (should):* I menu a tendina e le relative barre dei menu dovrebbero essere usati quando ci sono più di tre comandi e categorie.

- **IMPL-5.17.3.1.1.1-01** `[JS]` `[Globale]` La scelta tra controlli diretti (pulsanti) e menu a tendina deve essere guidata da una soglia in `config.js` (default: >3 comandi/categorie → menu a tendina).

#### 5.17.3.1.1.2 – Finestre primarie
*Sintesi (should):* I menu a tendina e le barre dei menu dovrebbero essere usati quando i comandi compaiono in finestre primarie (cioè dialoghi popup).

- **IMPL-5.17.3.1.1.2-01** `[HTML]` `[Main]` I comandi presentati in viste primarie di `#content-region` (non in `<dialog>` secondari) devono preferire l'organizzazione a menu a tendina rispetto a controlli sparsi.

#### 5.17.3.1.2 – Submenu
*Sintesi (shall):* I submenu devono consistere in un gruppo di opzioni correlate a una voce di menu toolbar di livello superiore.

- **IMPL-5.17.3.1.2-01** `[HTML][JS]` `[Main]` Ogni submenu deve essere un `<ul role="menu">` annidato, le cui voci siano semanticamente correlate alla voce di menu padre, gestito da `nav.js`.

#### 5.17.3.1.2.1 – Opzioni di menu aggiuntive
*Sintesi (should):* I submenu dovrebbero permettere di visualizzare opzioni di menu aggiuntive senza aumentare la lunghezza del menu complessivo.

- **IMPL-5.17.3.1.2.1-01** `[CSS][JS]` `[Main]` Quando un menu supera la soglia di voci visibili (`config.js`), `nav.js` deve raggruppare le voci eccedenti in un submenu anziché allungare la lista principale.

#### 5.17.3.1.2.2 – Accorciare la lunghezza
*Sintesi (may):* I submenu possono essere usati per accorciare la lunghezza di un menu lungo quando una voce di livello superiore può descrivere diverse funzionalità correlate.

- **IMPL-5.17.3.1.2.2-01** `[JS]` `[Globale]` La struttura menu in `config.js` deve permettere di dichiarare una voce "contenitore" che raggruppa in submenu funzionalità correlate, riducendo la lunghezza del menu principale.

#### 5.17.3.1.2.3 – Funzioni necessarie di frequente
*Sintesi (should):* I submenu non dovrebbero essere usati per funzioni necessarie e accedute di frequente.

- **IMPL-5.17.3.1.2.3-01** `[JS]` `[Globale]` Le voci con alta frequenza d'uso (flag `data-frequent` in `config.js`) devono restare al primo livello del menu/toolbar, non annidate in submenu, vedi `IMPL-5.17.3.2.9-01`.

#### 5.17.3.1.2.4 – Indicazione
*Sintesi (should):* Un indicatore (es. un triangolo puntato a destra) dovrebbe essere usato per segnalare la presenza di un submenu su una voce di menu di livello superiore.

- **IMPL-5.17.3.1.2.4-01** `[HTML][CSS]` `[Main]` Le voci di menu con submenu devono includere un indicatore grafico (`::after` con glifo `▶`/icona SVG) accanto all'etichetta, gestito da token CSS condiviso.

#### 5.17.3.1.2.5 – Presentazione delle opzioni
*Sintesi (shall):* Quando l'opzione di livello superiore è selezionata, le opzioni devono essere presentate senza ulteriore input dell'utente.

- **IMPL-5.17.3.1.2.5-01** `[JS]` `[Main]` `nav.js` deve aprire automaticamente il submenu associato al primo tocco/attivazione della voce padre (`pointerup`), senza richiedere un secondo gesto di apertura.

#### 5.17.3.1.2.6 – Numero di livelli
*Sintesi (should):* I submenu dovrebbero essere limitati a tre livelli (Main>Sub1>Sub2) per garantire la scopribilità degli elementi annidati.

- **IMPL-5.17.3.1.2.6-01** `[JS]` `[Globale]` La struttura di navigazione in `config.js`/`nav.js` deve validare che la profondità dei menu non superi 3 livelli; in fase di revisione segnalare strutture più profonde.

#### 5.17.3.1.3 – Barra dei menu
*Sintesi (should):* Gli utenti dovrebbero interagire con le barre dei menu facendo click sinistro sulla categoria di menu per aprire la lista di comandi a tendina.

- **IMPL-5.17.3.1.3-01** `[HTML][JS]` `[Header][Main]` Le categorie della barra menu (`#nav-region` o `#app-header`) devono rispondere a `pointerup` (click sinistro/tap) per aprire il menu a tendina associato, coerente su mouse e touch.

#### 5.17.3.1.3.1 – Toolbar menu
*Sintesi (may):* Le toolbar possono essere usate per presentare graficamente azioni di comando per un accesso facile ed efficiente.

- **IMPL-5.17.3.1.3.1-01** `[HTML][CSS]` `[Main]` Una toolbar opzionale (`<div role="toolbar">` in `#nav-region`/`#content-region`) può presentare icone-comando per le azioni più frequenti, in aggiunta al menu a tendina.

#### 5.17.3.1.3.2 – Uso nelle finestre primarie
*Sintesi (should):* Le toolbar dovrebbero essere usate nelle finestre primarie per fornire un piccolo numero di icone come comandi immediati.

- **IMPL-5.17.3.1.3.2-01** `[HTML][CSS]` `[Main]` Nella vista principale (`#content-region` primaria, non in dialog) la toolbar deve limitarsi a un numero contenuto di icone (valore di progetto in `config.js`, default ≤7) per comandi immediati.

#### 5.17.3.1.3.3 – Uso nelle finestre secondarie
*Sintesi (should):* Le toolbar non dovrebbero essere usate nelle finestre secondarie, con comandi solo testuali, icone affollate e comandi non immediati.

- **IMPL-5.17.3.1.3.3-01** `[HTML]` `[Main]` I `<dialog>` secondari gestiti da `dialog.js` non devono includere toolbar con icone; le azioni vanno presentate come pulsanti testuali espliciti.

#### 5.17.3.1.3.4 – Opzioni della toolbar
*Sintesi (should):* Le opzioni fisse della toolbar dovrebbero limitarsi ai comandi usati più di frequente da una larga percentuale di utenti, oppure deve essere previsto un mezzo per la configurazione personalizzata.

- **IMPL-5.17.3.1.3.4-01** `[JS]` `[Main]` `prefs.js` deve permettere all'utente di personalizzare le voci della toolbar tra un sottoinsieme consentito definito in `config.js`, mantenendo di default solo i comandi più frequenti.

#### 5.17.3.1.3.5 – Etichette della toolbar
*Sintesi (shall):* Le toolbar con icone devono fornire etichette tramite tooltip (hover per mostrare l'etichetta).

- **IMPL-5.17.3.1.3.5-01** `[HTML]` `[Main]` Ogni pulsante icona della toolbar deve avere `title`/tooltip nativo e `aria-label` equivalente, poiché su touch l'hover non è disponibile (vedi `IMPL-5.17.3.1.3.6-01`).

#### 5.17.3.1.3.6 – Icone non standard
*Sintesi (should/shall):* Le icone non standard dovrebbero essere evitate ma, se presenti, devono fornire etichette tramite tooltip hover e dovrebbero includere etichette testuali fisse.

- **IMPL-5.17.3.1.3.6-01** `[HTML][CSS]` `[Main]` Per icone non appartenenti al set standard di `config.js`, la toolbar deve mostrare anche un'etichetta testuale fissa sotto/accanto all'icona, non solo il tooltip (requisito touch, vedi regola 5 del brief).

#### 5.17.3.1.3.7 – Selezione
*Sintesi (should):* I menu della toolbar dovrebbero essere selezionati con click sinistro per dispositivi come mouse e trackball.

- **IMPL-5.17.3.1.3.7-01** `[JS]` `[Main]` I pulsanti toolbar devono gestire la selezione tramite evento `pointerup` unificato (click sinistro su mouse, tap su touch), nessuna dipendenza da tasto destro.

#### 5.17.3.1.4 – Menu a nastro (ribbon)
*Sintesi (may):* I menu a nastro separano comandi dedicati al task all'interno di una finestra; un elenco di comandi correlati al task appare sotto una scheda attiva; possono essere usati con più categorie di menu con elementi sotto ciascuna.

- **IMPL-5.17.3.1.4-01** `[HTML][CSS][JS]` `[Main]` Se adottato, il ribbon deve essere realizzato con `<div role="tablist">`/`<button role="tab">` in `#nav-region` e pannelli `role="tabpanel"` in `#content-region`, gestiti da `nav.js` con `aria-selected`.

#### 5.17.3.1.5 – Menu contestuali (tasto destro)
*Sintesi (may):* I menu con tasto destro possono fornire opzioni e azioni contestualmente rilevanti relative solo all'elemento o al pannello selezionato.

- **IMPL-5.17.3.1.5-01** `[JS][HTML]` `[Main]` Un menu contestuale opzionale (`contextmenu`/`pointerdown` prolungato per touch) può essere offerto in `#content-region` per azioni specifiche dell'elemento selezionato, gestito da `nav.js`.

#### 5.17.3.1.5.1 – Metodo di esecuzione
*Sintesi (shall):* I menu con tasto destro non devono fornire l'unico metodo per eseguire un comando.

- **IMPL-5.17.3.1.5.1-01** `[JS]` `[Main]` Ogni azione disponibile nel menu contestuale deve essere raggiungibile anche tramite il menu principale/toolbar/tasto funzione, verificato in fase di revisione del codice.

#### 5.17.3.1.5.2 – Accessibilità
*Sintesi (shall):* I menu con tasto destro e contestuali non devono includere opzioni non accessibili altrove tramite un mezzo evidente.

- **IMPL-5.17.3.1.5.2-01** `[JS]` `[Main]` `nav.js` non deve registrare comandi esclusivi del menu contestuale; ogni voce deve avere un percorso alternativo documentato nel menu a tendina o nella toolbar.

#### 5.17.3.1.5.3 – Disponibilità
*Sintesi (should):* I menu con tasto destro dovrebbero essere disponibili per avviare comandi basilari e usati di frequente.

- **IMPL-5.17.3.1.5.3-01** `[JS]` `[Main]` Il menu contestuale, quando presente, deve limitarsi ai comandi di uso frequente sull'elemento selezionato (definiti per tipo elemento in `config.js`).

#### 5.17.3.1.5.4 – Non sostitutivo
*Sintesi (shall):* I menu con tasto destro non devono essere usati come sostituti dei menu primari (es. menu a tendina).

- **IMPL-5.17.3.1.5.4-01** `[JS]` `[Globale]` `nav.js` deve mantenere il menu a tendina/barra di navigazione come percorso primario per tutti i comandi; il menu contestuale resta un acceleratore opzionale, mai l'unico ingresso architetturale.

#### 5.17.3.2.1 – Uso della selezione interattiva da menu
*Sintesi (shall):* La selezione interattiva da menu deve essere usata per compiti con poca o nessuna immissione di dati arbitrari, utenti con poca formazione o un set di comandi troppo ampio da memorizzare.

- **IMPL-5.17.3.2.1-01** `[HTML][JS]` `[Main]` `#nav-region` deve offrire selezione a menu (non solo comando testuale) come percorso primario per tutti i task con insieme di comandi ampio o utenti non esperti, gestito da `nav.js`.

#### 5.17.3.2.2 – Metodo di selezione
*Sintesi (shall):* La selezione del dispositivo deve essere conforme ai criteri 5.17.3.2.2.1–5.17.3.2.2.4.

- **IMPL-5.17.3.2.2-01** `[JS]` `[Globale]` I gestori di selezione menu devono essere implementati con Pointer Events unificati, coprendo i requisiti delle sottosezioni seguenti.

#### 5.17.3.2.2.1 – Mouse
*Sintesi (shall):* Un mouse o altro dispositivo di puntamento (inclusa la tecnologia touch) deve essere un metodo disponibile per la selezione da menu.

- **IMPL-5.17.3.2.2.1-01** `[JS]` `[Globale]` Tutte le voci di menu devono gestire `pointerdown/up` generico, funzionante sia con mouse sia con touch, senza handler esclusivi `click`/`touchstart` separati.

#### 5.17.3.2.2.2 – Puntamento
*Sintesi (shall):* Se la selezione da menu avviene per puntamento, un'azione di selezione deve avviare il comando (click sinistro del mouse o tap per input touch).

- **IMPL-5.17.3.2.2.2-01** `[JS]` `[Main]` La selezione della voce di menu deve attivarsi al `pointerup` sul target (strategia *last contact*, vedi 00-head.md §2.6), non al semplice hover/attraversamento.

#### 5.17.3.2.2.3 – Submenu
*Sintesi (shall):* Ogni submenu deve avere un titolo che identifichi chiaramente lo scopo di quel menu.

- **IMPL-5.17.3.2.2.3-01** `[HTML]` `[Main]` Ogni `<ul role="menu">` di submenu deve avere un'intestazione/`aria-label` descrittiva dello scopo, non solo ereditare il nome della voce padre.

#### 5.17.3.2.2.4 – Pagine multiple
*Sintesi (shall):* I menu non devono consistere di più pagine di opzioni.

- **IMPL-5.17.3.2.2.4-01** `[CSS][JS]` `[Main]` I menu devono usare scroll verticale interno (`overflow-y: auto` su `max-block-size`) invece di paginazione; `nav.js` non deve implementare controlli "pagina successiva" nei menu.

#### 5.17.3.2.3 – Presentazione delle opzioni attive
*Sintesi (shall):* Le opzioni di menu non disponibili devono essere non presentate oppure visualizzate in grigio per chiarire che non sono disponibili.

- **IMPL-5.17.3.2.3-01** `[HTML][CSS]` `[Main]` Le voci di menu non disponibili devono avere `[disabled]`/`[aria-disabled="true"]` con stile grayed-out (`.menu-item[aria-disabled]`), oppure essere omesse dal DOM, mai mostrate attive ma non funzionanti.

#### 5.17.3.2.4 – Coerenza del formato
*Sintesi (shall):* I menu devono essere presentati in un formato coerente in tutto il sistema e tra sistemi correlati che l'utente dovrà utilizzare.

- **IMPL-5.17.3.2.4-01** `[CSS]` `[Globale]` Tutti i menu devono usare gli stessi token CSS (spaziatura, tipografia, stati) definiti in 00-head.md §2.4, senza varianti locali per vista.

#### 5.17.3.2.5 – Accessibile
*Sintesi (shall):* I menu devono essere prontamente accessibili in ogni momento.

- **IMPL-5.17.3.2.5-01** `[HTML][JS]` `[Header][Main]` Il punto di accesso al menu principale (`#nav-region` o toggle in `#app-header`) deve rimanere disponibile e non nascosto da nessuna vista applicativa o dialog modale bloccante per più del tempo minimo necessario.

#### 5.17.3.2.6 – Sequenza delle opzioni
*Sintesi (shall):* Le selezioni di menu devono essere elencate in un ordine logico, come alfabetico o per frequenza d'uso.

- **IMPL-5.17.3.2.6-01** `[JS]` `[Globale]` L'ordine delle voci menu in `config.js` deve essere esplicitamente alfabetico o per frequenza d'uso (dichiarato in configurazione), non casuale.

#### 5.17.3.2.7 – Menu semplici
*Sintesi (may/should):* Se le selezioni stanno in una pagina in non più di due colonne, può essere usato un menu singolo; se le opzioni superano due colonne, dovrebbe essere impiegata una struttura di menu gerarchica.

- **IMPL-5.17.3.2.7-01** `[CSS][JS]` `[Main]` `nav.js` deve passare automaticamente da menu piatto a struttura gerarchica (submenu) quando il conteggio voci supera la soglia di due colonne visualizzabili a `--touch-target-min`, calcolata a runtime in base al breakpoint.

#### 5.17.3.2.8 – Presentazione delle opzioni
*Sintesi (should):* Le opzioni di menu testuali dovrebbero essere presentate su righe separate per garantire che gli elementi siano distinguibili e selezionabili.

- **IMPL-5.17.3.2.8-01** `[CSS]` `[Main]` Le voci di menu devono essere elementi di lista (`<li>`) a riga singola (`display: block`/`flex`), mai testo in linea affiancato nella stessa riga.

#### 5.17.3.2.9 – Chiamata diretta di funzione
*Sintesi (shall):* Deve essere fornita una capacità di chiamata diretta di funzione per le opzioni usate di frequente.

- **IMPL-5.17.3.2.9-01** `[JS][HTML]` `[Footer]` Le opzioni di menu più frequenti devono essere duplicate come tasto funzione in `#function-keys` o scorciatoia diretta, per evitare la navigazione completa del menu.

#### 5.17.3.2.10 – Comandi di scelta rapida
*Sintesi (should):* Comandi di scelta rapida (es. "control C" per copiare) dovrebbero essere forniti per le azioni usate di frequente.

- **IMPL-5.17.3.2.10-01** `[JS]` `[Globale]` `nav.js` deve registrare scorciatoie da tastiera standard (`keydown` globale) per le azioni più frequenti, come alternativa per l'input secondario a tastiera.

#### 5.17.3.2.11 – Codifica delle opzioni
*Sintesi (shall):* Quando le selezioni possono essere fatte tramite scorciatoie da tastiera, le scorciatoie associate a ciascuna opzione alternativa devono essere incluse accanto all'opzione di menu.

- **IMPL-5.17.3.2.11-01** `[HTML][CSS]` `[Main]` Ogni voce di menu con scorciatoia deve mostrare la combinazione di tasti accanto all'etichetta (es. `<span class="menu-shortcut">Ctrl+C</span>`), allineata a destra nella voce.

#### 5.17.3.2.12 – Scorciatoie da tastiera
*Sintesi (shall):* Se le selezioni di menu avvengono tramite immissione alfanumerica di codici, le opzioni devono essere codificate con lettere associate al nome dell'opzione o scorciatoie standard, non con codici arbitrari.

- **IMPL-5.17.3.2.12-01** `[JS]` `[Globale]` I mnemonici tastiera (`accesskey`/gestione in `nav.js`) devono derivare dall'iniziale o da una lettera significativa del nome dell'opzione, mai da codici numerici arbitrari.

#### 5.17.3.2.13 – Duplicazione
*Sintesi (shall):* I codici a tasti non devono duplicare altri codici di funzione utente.

- **IMPL-5.17.3.2.13-01** `[JS]` `[Globale]` `validate.js`/strumento di build deve verificare l'unicità dei binding tastiera in `config.js` tra menu, tasti funzione e scorciatoie globali.

#### 5.17.3.2.14 – Posizione nella struttura
*Sintesi (shall):* Quando la selezione di menu attraversa più livelli, tutti i livelli devono restare visibili finché la selezione non è effettuata.

- **IMPL-5.17.3.2.14-01** `[CSS][JS]` `[Main]` La navigazione gerarchica deve mostrare contemporaneamente (breadcrumb o colonne a cascata) tutti i livelli di menu attraversati fino alla selezione finale, senza sostituire la vista del livello precedente.

#### 5.17.3.2.15 – Albero del menu
*Sintesi (shall):* Un albero del menu che mostri la gerarchia deve essere incluso nel manuale utente o nella sezione di Help online.

- **IMPL-5.17.3.2.15-01** `[HTML][JS]` `[Main]` La sezione Help online (apribile da `#btn-help`) deve includere una vista ad albero (`<ul>` annidati) che rispecchi l'intera gerarchia di `#nav-region`, generata da `nav.js`/`config.js`.

#### 5.17.3.2.16 – Tooltip
*Sintesi (should):* Un tooltip dovrebbe essere visualizzato sopra un elemento quando l'utente passa il puntatore senza cliccare sull'elemento.

- **IMPL-5.17.3.2.16-01** `[HTML][JS]` `[Main]` Le voci di menu devono avere `title`/tooltip su hover per input mouse; per touch, in assenza di hover, l'informazione equivalente deve essere sempre visibile come testo (vedi regola 5 del brief, nessuna funzione hover-only).

#### 5.17.3.2.17 – Icone
*Sintesi (may):* I menu possono usare icone per rappresentare le opzioni di controllo.

- **IMPL-5.17.3.2.17-01** `[HTML][CSS]` `[Main]` Le voci di menu possono includere un'icona SVG/`<img>` accanto all'etichetta testuale (mai icona da sola, vedi `IMPL-5.1.3.12.8.3` in sezione 5 del documento).

#### 5.17.3.2.17.1 – Attualmente in uso
*Sintesi (should):* Quando possibile, le icone dovrebbero essere coerenti con quelle attualmente usate in altri sistemi, familiari all'utente e usate in modo coerente all'interno dell'applicazione.

- **IMPL-5.17.3.2.17.1-01** `[CSS][JS]` `[Globale]` Il set di icone deve essere definito una sola volta (sprite SVG/libreria icone in `config.js`) e riutilizzato identicamente in tutti i menu, senza varianti locali.

#### 5.17.3.2.17.2 – Uso di menu grafici
*Sintesi (may):* I menu grafici possono usare icone per rappresentare le opzioni di controllo ed essere coerenti all'interno di un'applicazione.

- **IMPL-5.17.3.2.17.2-01** `[CSS]` `[Globale]` I menu grafici a icone (es. dashboard di navigazione rapida) devono condividere lo stesso set/stile icone di cui a `IMPL-5.17.3.2.17.1-01`.

#### 5.17.4.1 – Immissione dati
*Sintesi (shall):* Le funzioni di immissione dati devono essere progettate per garantire coerenza delle transazioni, minimizzare le azioni di input, minimizzare il carico di memoria sull'utente, assicurare compatibilità tra immissione e visualizzazione dei dati, e dare all'utente il controllo sull'immissione.

- **IMPL-5.17.4.1-01** `[JS][HTML]` `[Main]` Tutti i form devono condividere pattern comuni (stessi componenti `<input>`, stessa gestione errori di `validate.js`, stesso ordine label-campo) e il layout del form deve rispecchiare quello della visualizzazione dei dati corrispondente (stesso ordine campi), sotto controllo esplicito dell'utente (nessun autosubmit).

#### 5.17.4.2 – Ritmo controllato dall'utente
*Sintesi (shall):* Il ritmo dell'immissione dati deve essere controllato dall'utente, non dal sistema.

- **IMPL-5.17.4.2-01** `[JS]` `[Main]` Nessun form deve avanzare automaticamente al campo successivo o sottomettere dati per timeout; l'avanzamento avviene solo su azione esplicita dell'utente (vedi `IMPL-5.17.4.7-01`).

#### 5.17.4.3 – Feedback positivo
*Sintesi (shall):* Il sistema deve fornire feedback all'utente riguardo all'accettazione o al rifiuto di un dato immesso.

- **IMPL-5.17.4.3-01** `[JS]` `[Main]` `feedback.js.setStatus()`/`validate.js` deve mostrare esito (accettato/rifiutato) in `#message-line` o inline sul campo per ogni immissione completata.

#### 5.17.4.4 – Tempo di risposta
*Sintesi (shall):* I tempi di risposta del feedback devono essere conformi a 5.17.9.5.

- **IMPL-5.17.4.4-01** `[JS]` `[Globale]` `feedback.js` deve rispettare i tempi di Tabella V (00-head.md §2.6): eco carattere ≤0,2 s, errore ≤0,2 s, aggiornamento locale ≤0,5 s.

#### 5.17.4.5 – Ritardo di elaborazione
*Sintesi (shall):* Dove sovraccarico di sistema o altre condizioni causano un ritardo di elaborazione, il sistema deve confermare l'immissione dati e indicare il ritardo all'utente.

- **IMPL-5.17.4.5-01** `[JS][HTML]` `[Footer]` In caso di ritardo oltre 1 s, `net.js` deve confermare la ricezione dell'immissione (`#status-line`) e mostrare `#progress-indicator` con indicazione del ritardo, vedi `IMPL-5.17.2.3.6-01`.

#### 5.17.4.6 – Tempo di completamento
*Sintesi (should):* Il sistema dovrebbe fornire un'indicazione del tempo di completamento o della frazione di processo completata.

- **IMPL-5.17.4.6-01** `[JS]` `[Footer]` `#progress-indicator` deve esporre, quando disponibile, percentuale completata e/o tempo stimato residuo per le elaborazioni di immissione dati lunghe.

#### 5.17.4.7 – Azione esplicita
*Sintesi (shall):* Ogni immissione dati deve richiedere un'azione di completamento esplicita, come la pressione del tasto ENTER o di un altro tasto (es. TAB).

- **IMPL-5.17.4.7-01** `[HTML][JS]` `[Main]` Ogni campo deve confermare il valore solo su evento `change`/`keydown` (Enter/Tab) o pulsante esplicito "Conferma", mai su `input` continuo senza azione di completamento.

#### 5.17.4.8 – Validazione
*Sintesi (shall):* Le immissioni dati devono essere validate dal sistema per formato corretto, valore legale e intervallo di valori prima dell'elaborazione.

- **IMPL-5.17.4.8-01** `[JS]` `[Main]` `validate.js` deve controllare formato (regex/tipo), valore ammesso e range numerico di ogni campo prima di inoltrare i dati a `net.js`, impostando `aria-invalid` e messaggio in caso di esito negativo.

#### 5.17.4.9 – Dati già disponibili al software
*Sintesi (shall):* L'utente non deve essere tenuto a immettere dati già disponibili al software.

- **IMPL-5.17.4.9-01** `[JS]` `[Main]` I form devono precompilare automaticamente i campi il cui valore è già noto da `state.js`/`net.js` (es. utente corrente, stazione, data), rendendoli al più modificabili ma non vuoti.

#### 5.17.4.10 – Unità di immissione
*Sintesi (shall):* I dati devono essere immessi in unità familiari all'utente.

- **IMPL-5.17.4.10-01** `[JS][HTML]` `[Main]` `i18n.js`/`prefs.js` deve determinare l'unità di misura visualizzata/accettata per ciascun campo numerico in base alla preferenza utente/locale, con conversione interna trasparente.

#### 5.17.5.2 – Cursore distintivo
*Sintesi (shall):* Il cursore deve avere attributi visivi differenti che distinguono i vari usi, come la selezione di un oggetto o la modifica di testo.

- **IMPL-5.17.5.2-01** `[CSS]` `[Main]` Deve essere impostato `cursor: text|pointer|crosshair|move` in base alla modalità (editing testo, selezione oggetto, trascinamento) tramite classi di stato dedicate; equivalente touch tramite evidenziazione del target attivo.

#### 5.17.5.3 – Posizione distintiva del cursore
*Sintesi (shall):* La posizione corrente del cursore su display grafici/di elaborazione immagini che richiedono posizionamento preciso deve essere indicata con un simbolo distintivo che indichi un punto (es. una croce o un mirino la cui intersezione marchi una posizione con ragionevole precisione).

- **IMPL-5.17.5.3-01** `[CSS][JS]` `[Main]` Nelle viste grafiche/mappe che richiedono posizionamento preciso, deve essere renderizzato un cursore a croce/mirino SVG sincronizzato alla posizione del pointer, gestito dal componente grafico in `#content-region`.

#### 5.17.5.4 – Occultamento
*Sintesi (shall):* Il cursore non deve oscurare le entità visualizzate.

- **IMPL-5.17.5.4-01** `[CSS]` `[Main]` Il cursore/mirino grafico deve avere un offset o trasparenza tale da non coprire il punto/elemento sottostante indicato (es. centro vuoto del mirino).

#### 5.17.5.6 – Movimento del cursore con i tasti freccia
*Sintesi (shall):* Se il cursore è mosso premendo un tasto, il rilascio del tasto deve causare l'arresto del movimento.

- **IMPL-5.17.5.6-01** `[JS]` `[Main]` Il gestore `keydown`/`keyup` per lo spostamento del cursore grafico deve fermare il movimento su `keyup`, senza continuare oltre il rilascio del tasto.

#### 5.17.5.7 – Posizionamento preciso
*Sintesi (shall):* Dove l'immissione dati richiede un posizionamento esatto di elementi grafici, agli utenti deve essere fornita la capacità di espandere l'area di visualizzazione critica (es. zoom e pan) per rendere il compito di posizionamento più facile e preciso.

- **IMPL-5.17.5.7-01** `[JS][HTML]` `[Main]` I componenti grafici con posizionamento preciso devono offrire controlli di zoom/pan (pulsanti touch-friendly ≥ `--touch-target-min` e gesture pinch-to-zoom) tramite modulo dedicato.

#### 5.17.5.8 – Posizione home
*Sintesi (shall):* La posizione home del cursore deve essere coerente tra display di tipo simile.

- **IMPL-5.17.5.8-01** `[JS]` `[Globale]` Il componente di editing grafico deve definire una posizione home del cursore coerente (es. centro o origine) in `config.js`, applicata uniformemente a tutte le viste dello stesso tipo.

#### 5.17.5.9 – Attuazione esplicita
*Sintesi (shall):* Un'azione esplicita separata dalla posizione del cursore (es. premere un pulsante o commutare) deve essere richiesta per una selezione di controllo.

- **IMPL-5.17.5.9-01** `[JS]` `[Main]` La selezione di un controllo deve richiedere un evento `pointerup`/`click` distinto dal semplice posizionamento (`pointermove`/hover) del cursore.

#### 5.17.5.10 – Posizionamento incrementale del cursore
*Sintesi (shall):* Dove il posizionamento del cursore è incrementato a passi discreti, la dimensione del passo deve essere coerente in orizzontale (destra e sinistra) e in verticale (su e giù).

- **IMPL-5.17.5.10-01** `[JS]` `[Main]` Lo step di spostamento del cursore grafico via tastiera/pulsanti deve essere un valore unico configurato in `config.js`, identico nelle quattro direzioni.

#### 5.17.5.11.1 – Designazione della posizione
*Sintesi (shall):* Quando la designazione della posizione è richiesta in un compito che enfatizza l'immissione dati a tastiera, il controllo del cursore deve essere disponibile da tastiera, preferibilmente con i tasti freccia.

- **IMPL-5.17.5.11.1-01** `[JS]` `[Main]` Per i task di posizionamento cursore orientati a tastiera, i tasti freccia devono essere il metodo primario di spostamento, gestiti da listener `keydown` dedicato oltre al pointer.

#### 5.17.5.11.2 – Mezzo di controllo
*Sintesi (should):* Se vengono usati tasti diversi dai tasti freccia per il movimento (come con una tastiera abbreviata), il display dovrebbe indicare chiaramente il mezzo di controllo.

- **IMPL-5.17.5.11.2-01** `[HTML][CSS]` `[Main]` Se si usano tasti alternativi alle frecce per lo spostamento cursore, l'interfaccia deve mostrare un'indicazione visibile (legenda/tooltip) dei tasti attivi, specialmente su tastiera touch ridotta.

#### 5.17.5.12.1 – Aspettative dell'utente
*Sintesi (shall):* La risposta di un cursore all'input dell'utente deve essere coerente e compatibile con le aspettative dell'utente.

- **IMPL-5.17.5.12.1-01** `[JS]` `[Globale]` Il comportamento del cursore (velocità, accelerazione, direzione) deve essere implementato in modo uniforme da un unico modulo condiviso, senza variazioni non documentate tra viste.

#### 5.17.5.12.2 – Movimento nella direzione corrispondente
*Sintesi (shall):* Per il controllo del cursore tramite tasti, il cursore deve muoversi nella direzione corrispondente al tasto freccia premuto (sinistra→sinistra, destra→destra, su→su, giù→giù).

- **IMPL-5.17.5.12.2-01** `[JS]` `[Main]` Il gestore tasti freccia deve mappare `ArrowLeft/Right/Up/Down` esattamente alla direzione di movimento corrispondente del cursore grafico, senza inversioni.

#### 5.17.5.13 – Azione esplicita di cancellazione
*Sintesi (shall):* La cancellazione o l'annullamento dei dati deve richiedere un'azione esplicita, come la pressione di un tasto DELETE.

- **IMPL-5.17.5.13-01** `[JS][HTML]` `[Main]` La cancellazione di un valore/elemento deve richiedere un'attivazione esplicita (tasto Canc/pulsante "Elimina" dedicato), mai un effetto collaterale di altra azione.

#### 5.17.5.14 – Cancellazione permanente
*Sintesi (shall):* La cancellazione permanente (in assenza di una funzione "undo") di più di un carattere non deve essere consentita senza una risposta affermativa a una query di validazione.

- **IMPL-5.17.5.14-01** `[JS][HTML]` `[Main]` Quando non è disponibile `undo` e la cancellazione riguarda più di un carattere/elemento, `dialog.js` deve richiedere conferma esplicita prima di eseguire l'eliminazione, vedi `IMPL-5.1.3.11.5.3-01`.

#### 5.17.6.1 – Uso
*Sintesi (may):* Il controllo interattivo tramite compilazione di form può essere usato dove è necessaria una certa flessibilità nell'immissione dati.

- **IMPL-5.17.6.1-01** `[HTML]` `[Main]` I task con dati strutturati e relativa flessibilità d'ordine devono usare un `<form>` in `#content-region` come pattern di interazione, alternativo al wizard domanda-risposta (vedi `IMPL-5.17.2.2.1-01`).

#### 5.17.6.2 – Raggruppamento
*Sintesi (shall):* I form visualizzati devono essere organizzati raggruppando insieme gli elementi correlati.

- **IMPL-5.17.6.2-01** `[HTML]` `[Main]` Gli elementi correlati del form devono essere raggruppati in `<fieldset>` con `<legend>` descrittiva, non elencati come lista piatta indifferenziata.

#### 5.17.6.3 – Coerenza di formato e contenuto
*Sintesi (shall):* Formato e contenuto dei form visualizzati devono duplicare in ogni parametro principale il modulo cartaceo che intendono rappresentare.

- **IMPL-5.17.6.3-01** `[HTML][CSS]` `[Main]` Quando il form digitale rappresenta un modulo cartaceo di riferimento, l'ordine, il raggruppamento e le etichette dei campi devono rispecchiarne la struttura, documentato nella configurazione del form.

#### 5.17.6.4 – Avanzamento tra i campi del form
*Sintesi (shall):* Usando TAB o ENTER per avanzare tra i campi, il sistema deve richiedere una risposta per ogni campo di immissione dati; avanzare oltre un campo lasciato vuoto quando non si desidera immettere nulla deve richiedere un'azione esplicita (tasti TAB o ENTER).

- **IMPL-5.17.6.4-01** `[JS]` `[Main]` `validate.js` deve richiedere conferma esplicita (Tab/Enter) per lasciare vuoto un campo opzionale; nessun avanzamento automatico silenzioso che salti campi non compilati.

#### 5.17.6.5 – Separazione
*Sintesi (shall):* Campi o gruppi di campi devono essere separati da spazi, linee o altri segnali di delineazione.

- **IMPL-5.17.6.5-01** `[CSS]` `[Main]` I `<fieldset>`/gruppi di campi devono avere separazione visiva minima (`gap`/`border-block` ≥ `--touch-gap-min`) tra loro.

#### 5.17.6.6 – Distintività dei campi
*Sintesi (shall):* I campi obbligatori devono essere distinti dai campi opzionali.

- **IMPL-5.17.6.6-01** `[HTML][CSS]` `[Main]` I campi obbligatori devono avere `required`/`aria-required="true"` e un indicatore visivo coerente (es. asterisco, classe `.field-required`) distinto dai campi opzionali.

#### 5.17.6.7 – Etichette dei campi
*Sintesi (shall):* Le etichette dei campi devono essere presentate in modo distintivo così da poter essere distinte dallo spazio di immissione dati.

- **IMPL-5.17.6.7-01** `[HTML][CSS]` `[Main]` Le `<label>` devono avere stile tipografico/colore distinti (es. `font-weight`, `color: var(--color-fg)`) rispetto al controllo di immissione associato.

#### 5.17.6.8 – Indicazione aggiuntiva
*Sintesi (shall):* Le etichette per i campi di immissione dati devono incorporare indicazioni aggiuntive quando il formato del dato potrebbe logicamente assumere più di un formato (es. "DATA (MM/GG/AAAA): _ _ / _ _ / _ _ _ _").

- **IMPL-5.17.6.8-01** `[HTML]` `[Main]` Le `<label>` di campi con formato ambiguo devono includere il pattern atteso tra parentesi (es. "Data (GG/MM/AAAA)") e/o `placeholder` corrispondente, verificato da `validate.js`.

#### 5.17.6.9 – Etichette informative
*Sintesi (shall):* Nell'etichettare i campi dati deve essere impiegata una formulazione descrittiva.

- **IMPL-5.17.6.9-01** `[HTML]` `[Main]` Le etichette dei campi devono usare parole descrittive complete gestite da `i18n.js`, evitando sigle non spiegate.

#### 5.17.6.10 – Codici arbitrari
*Sintesi (shall):* L'uso di codici arbitrari deve essere evitato.

- **IMPL-5.17.6.10-01** `[JS]` `[Main]` I valori selezionabili nei form (es. `<select>`) devono usare etichette testuali leggibili come testo visibile, con eventuali codici interni solo come `value` non esposto, mai codici arbitrari mostrati all'utente.

#### 5.17.6.11 – Etichette allineate in alto
*Sintesi (shall):* Le etichette allineate in alto devono essere usate quando le etichette sono lunghe o il form richiede spazio orizzontale per raggruppare campi di immissione correlati.

- **IMPL-5.17.6.11-01** `[CSS]` `[Main]` La classe di layout form `.form-row--stacked` (label sopra il campo, `flex-direction: column`) deve essere usata per etichette lunghe o gruppi che richiedono spazio orizzontale.

#### 5.17.6.12 – Spazio verticale limitato
*Sintesi (shall):* Le etichette allineate in alto non devono essere usate se lo spazio verticale è limitato.

- **IMPL-5.17.6.12-01** `[CSS]` `[Main]` A breakpoint S (800×600) con viewport verticale ridotto, il layout form deve passare automaticamente da `.form-row--stacked` a `.form-row--inline` (etichetta a sinistra/destra) tramite media query, evitando l'allineamento in alto.

#### 5.17.6.13 – Etichette allineate a destra
*Sintesi (shall/may):* Le etichette allineate a destra devono essere usate per form che richiedono una scansione rapida delle etichette e possono essere usate quando lo spazio verticale è limitato.

- **IMPL-5.17.6.13-01** `[CSS]` `[Main]` La classe `.form-row--label-right` (`text-align: right` sulla label, stessa riga del campo) deve essere disponibile per i form a scansione rapida o a spazio verticale limitato.

#### 5.17.6.14 – Etichette allineate a sinistra
*Sintesi (shall):* Le etichette allineate a sinistra devono essere usate quando un form richiede che gli utenti considerino attentamente le etichette.

- **IMPL-5.17.6.14-01** `[CSS]` `[Main]` La classe `.form-row--label-left` (default, `text-align: left`) deve essere usata per i form che richiedono lettura attenta delle etichette (es. moduli legali/di conferma).

#### 5.17.6.15 – Spazio verticale limitato (etichette a sinistra)
*Sintesi (shall):* Le etichette allineate a sinistra non devono essere usate quando sono così lunghe da non consentire l'immissione dati nello stesso spazio verticale.

- **IMPL-5.17.6.15-01** `[CSS]` `[JS]` `[Main]` Quando la lunghezza dell'etichetta (misurata o configurata) eccede la soglia di `config.js`, il layout deve passare automaticamente a `.form-row--stacked` invece di `.form-row--label-left`.

#### 5.17.6.16 – Posizione predefinita del cursore
*Sintesi (shall):* Quando un form è visualizzato, un cursore visualizzato deve essere posizionato di default sul primo campo di immissione dati.

- **IMPL-5.17.6.16-01** `[JS]` `[Main]` Alla visualizzazione del form, `nav.js`/il componente form deve invocare `focus()` sul primo campo editabile, impostando il focus iniziale senza richiedere tocco dell'utente.

#### 5.17.6.16.1 – Avanzamento del cursore
*Sintesi (shall):* Il cursore deve avanzare al campo di immissione dati successivo tramite tasto TAB o ENTER quando l'utente ha completato l'immissione del campo corrente.

- **IMPL-5.17.6.16.1-01** `[JS]` `[Main]` Il gestore `keydown` del form deve intercettare `Tab`/`Enter` per spostare il focus al campo successivo nell'ordine DOM/`tabindex`, salvo eccezioni di `IMPL-5.17.6.16.2-01`.

#### 5.17.6.16.2 – Eccezioni
*Sintesi (shall):* Eccezioni all'uso del tasto ENTER per l'avanzamento si verificano quando alcuni campi richiedono o permettono più righe in input; in tal caso, solo il tasto TAB deve essere usato per avanzare il cursore.

- **IMPL-5.17.6.16.2-01** `[JS]` `[Main]` Per i campi `<textarea>` multi-riga, il gestore `keydown` non deve intercettare `Enter` per l'avanzamento (resta a capo); solo `Tab` deve spostare il focus al campo successivo.

#### 5.17.6.17 – Indicazione della lunghezza di immissione
*Sintesi (shall):* La lunghezza massima accettabile per i campi a lunghezza variabile deve essere visualizzata.

- **IMPL-5.17.6.17-01** `[HTML][JS]` `[Main]` I campi a lunghezza variabile devono mostrare un contatore `n/max` (aggiornato live da `validate.js`, `aria-live="polite"`) accanto al campo, basato su `maxlength`.

#### 5.17.6.18 – Numero massimo di caratteri immessi
*Sintesi (shall):* Deve essere fornita un'indicazione all'utente se è stato immesso il numero massimo di caratteri.

- **IMPL-5.17.6.18-01** `[JS][CSS]` `[Main]` Al raggiungimento di `maxlength`, il campo deve evidenziarsi (classe `.field-at-limit`) e il contatore di `IMPL-5.17.6.17-01` deve segnalarlo visivamente (es. colore `--color-warning`).

#### 5.17.6.19 – Sovrascrittura
*Sintesi (shall):* Caratteri diversi da quelli che indicano il formato e che devono essere sovrascritti non devono essere inseriti per default in campi dati non compilati.

- **IMPL-5.17.6.19-01** `[HTML][JS]` `[Main]` I campi con maschera di formato (es. data) devono mostrare solo i caratteri segnaposto del formato (`placeholder`/maschera in `validate.js`), senza precompilare valori di default non richiesti.

#### 5.17.6.20 – Unità dimensionali
*Sintesi (shall):* Quando un'unità dimensionale coerente è prevista come unità di immissione per un dato campo, l'unità dimensionale deve essere fornita dal computer a destra del campo.

- **IMPL-5.17.6.20-01** `[HTML][CSS]` `[Main]` I campi numerici con unità fissa devono mostrare l'unità come testo statico a destra del campo (es. `<span class="field-unit">mm</span>`), non editabile dall'utente.

#### 5.17.6.21 – Unità dimensionali variabili
*Sintesi (shall):* Quando l'unità dimensionale può variare per un dato campo, l'utente deve essere informato e deve essere fornito un campo separato con opzioni a destra del campo numerico per la scelta dell'unità da un elenco a discesa.

- **IMPL-5.17.6.21-01** `[HTML]` `[Main]` I campi numerici a unità variabile devono affiancare un `<select class="field-unit-select">` con le unità disponibili, posizionato a destra del campo numerico.

#### 5.17.6.22 – Omissioni dell'utente
*Sintesi (shall):* L'utente deve essere informato quando le immissioni dati richieste non sono state effettuate.

- **IMPL-5.17.6.22-01** `[JS][HTML]` `[Main]` Al submit, `validate.js` deve elencare in `#message-line`/accanto ai campi tutti i campi obbligatori non compilati, con `aria-invalid="true"` e focus sul primo mancante.

#### 5.17.6.23 – Aree non di immissione
*Sintesi (shall):* Le aree non di immissione (protette) del display devono essere visualizzate in modo da essere distinguibili dalle aree in cui i dati possono essere immessi.

- **IMPL-5.17.6.23-01** `[CSS]` `[Main]` Le aree protette/read-only devono usare uno stile distinto (`.field-readonly`, sfondo neutro, nessun bordo di campo editabile) rispetto ai campi immettibili, vedi `IMPL-5.1.3.11.1.13-01`.

#### 5.17.6.24 – Inaccessibili dal cursore
*Sintesi (shall):* Le aree non di immissione devono essere inaccessibili tramite il cursore.

- **IMPL-5.17.6.24-01** `[HTML]` `[Main]` Gli elementi read-only devono avere `readonly`/`disabled`/`tabindex="-1"` per essere esclusi dall'ordine di tabulazione e dal focus.

#### 5.17.6.25 – Immissione dati flessibile
*Sintesi (shall):* Quando vengono immessi più elementi di dati, l'utente deve poter reinserire, modificare o annullare qualsiasi elemento prima dell'invio.

- **IMPL-5.17.6.25-01** `[JS][HTML]` `[Main]` Prima del submit finale, ogni campo del form deve restare modificabile/cancellabile liberamente; deve essere presente un'azione "Annulla modifiche" per il singolo campo o l'intero form.

#### 5.17.6.26 – Ordine logico
*Sintesi (shall):* Dove non è coinvolto alcun documento sorgente, i form devono essere progettati in modo che gli elementi dati siano ordinati in una sequenza logica di immissione.

- **IMPL-5.17.6.26-01** `[HTML]` `[Main]` In assenza di documento sorgente di riferimento, l'ordine dei campi nel DOM deve seguire una sequenza logica task-oriented definita in fase di progettazione del form, documentata nel componente.

#### 5.17.6.27 – Dialog box per l'immissione di controlli
*Sintesi (may):* Le dialog box possono essere usate come ausilio per comporre immissioni di controllo complesse o per selezionare opzioni (es. per una richiesta di stampa, un form visualizzato potrebbe aiutare l'utente a invocare i vari controlli di formato disponibili).

- **IMPL-5.17.6.27-01** `[HTML][JS]` `[Main]` Per compiti di configurazione complessa (es. opzioni di stampa/esportazione), `dialog.js` deve offrire un `<dialog>` con form dedicato che guidi l'utente nella composizione dei parametri, invece di un'unica immissione in linea.

### 3.7 Gestione errori, guida all'utente, help e prompt

Prevenzione, rilevamento, messaggistica e recupero dagli errori, insieme a help e prompting.

#### 5.1.2.1.7.8 – Informazioni diagnostiche
*Sintesi (shall/should/may):* I messaggi di errore devono fornire esplicitamente tutte le informazioni diagnostiche e le indicazioni correttive inferibili in modo affidabile dalla condizione di errore.

- **IMPL-5.1.2.1.7.8-01** `[JS]` `[Main]` `notify.js`/`validate.js` devono generare messaggi d'errore con campo `detail` contenente la causa inferita e l'azione correttiva suggerita.
- **IMPL-5.1.2.1.7.8-02** `[HTML]` `[Main]` Il messaggio di errore deve essere reso con `role="alert"` associato al campo (`aria-describedby`) per il contenuto diagnostico.

#### 5.1.2.1.7.11.2 – Comandi corretti dal computer
*Sintesi:* I comandi, valori e ortografie corretti dal computer devono essere visualizzati ed evidenziati per la conferma dell'utente.

- **IMPL-5.1.2.1.7.11.2-01** `[HTML][JS]` `[Main]` Quando `validate.js` applica una correzione automatica, il valore corretto deve essere mostrato evidenziato (`.field--autocorrected`) in un dialogo di conferma (`dialog.js`) prima dell'accettazione definitiva.

#### 5.17.10.1 – Correzione degli errori
*Sintesi:* Deve essere fornito un mezzo semplice per correggere le voci errate dell'utente.

- **IMPL-5.17.10.1-01** `[HTML][JS]` `[Main]` Ogni campo in errore deve esporre correzione diretta in linea (focus automatico + modifica) senza richiedere la cancellazione dell'intero form.

#### 5.17.10.2 – Correzione parziale
*Sintesi:* Il sistema deve consentire la correzione parziale delle voci.

- **IMPL-5.17.10.2-01** `[JS]` `[Main]` `validate.js` deve permettere la modifica del singolo campo/carattere errato senza invalidare o richiedere il reinserimento degli altri campi già corretti del form.

#### 5.17.10.3 – Rilevamento precoce
*Sintesi:* Deve essere fornita una capacità di rilevare e correggere gli errori dopo la digitazione ma prima dell'immissione nel sistema.

- **IMPL-5.17.10.3-01** `[JS]` `[Main]` `validate.js` deve validare su `input`/`blur` prima della submit e mostrare l'errore immediatamente in linea, permettendo la correzione prima della conferma finale.

#### 5.17.10.4 – Interruzioni logiche dei dati
*Sintesi:* Il controllo degli errori deve avvenire alle interruzioni logiche di immissione (es. fine campo) piuttosto che per singolo carattere, per minimizzare le interruzioni.

- **IMPL-5.17.10.4-01** `[JS]` `[Main]` `validate.js` deve eseguire la validazione su `change`/`blur` (fine campo), non su ogni `keydown`, salvo controlli di formato carattere (es. solo cifre).

#### 5.17.10.5 – Controlli software interni
*Sintesi:* Il software deve fornire controlli sulle voci dell'utente per validità dell'elemento, sequenza di immissione, completezza e intervallo di valori.

- **IMPL-5.17.10.5-01** `[JS]` `[Main]` `validate.js` deve implementare regole configurabili (`type`, `required`, `min`/`max`, `pattern`, `order`) per campo, con messaggi specifici per tipo di violazione.

#### 5.17.10.6 – Voci critiche
*Sintesi:* Il sistema deve richiedere all'utente di confermare le voci con conseguenze irreversibili o potenzialmente distruttive prima della loro implementazione.

- **IMPL-5.17.10.6-01** `[JS][HTML]` `[Globale]` Vedi `IMPL-5.1.3.12.1.5-01`: `dialog.js` deve aprire conferma esplicita (`role="alertdialog"`) per ogni invio marcato `data-destructive="true"` prima dell'esecuzione.

#### 5.17.10.7.1 – Descrivere l'errore
*Sintesi:* I messaggi di errore devono descrivere l'errore in modo semplice e in termini applicativi piuttosto che di funzione di sistema (es. "troppi caratteri" vs "stack overflow").

- **IMPL-5.17.10.7.1-01** `[JS]` `[Main]` `i18n.js`/`notify.js` devono mappare i codici di errore interni a messaggi testuali in linguaggio applicativo, evitando terminologia tecnica interna.

#### 5.17.10.7.2 – Istruire l'utente
*Sintesi:* I messaggi di errore devono istruire l'utente su come recuperare dall'errore o uscire dalla situazione di errore.

- **IMPL-5.17.10.7.2-01** `[JS][HTML]` `[Main]` Ogni messaggio generato da `notify.js` deve includere un suggerimento di azione correttiva (testo/link con focus automatico sul campo da correggere).

#### 5.17.10.7.3 – Tono costruttivo e neutro
*Sintesi:* I messaggi di errore devono avere tono costruttivo e neutro, evitando frasi che suggeriscano un giudizio sul comportamento dell'utente.

- **IMPL-5.17.10.7.3-01** `[JS]` `[Globale]` Il dizionario testi di errore in `i18n.js` è soggetto a criterio di revisione (I) che vieta formulazioni colpevolizzanti o giudicanti.

#### 5.17.10.7.4 – Coerente con la comprensione dell'utente
*Sintesi:* Il messaggio di errore deve essere scritto in modo coerente con la comprensione e il livello di conoscenza atteso dell'utente.

- **IMPL-5.17.10.7.4-01** `[JS]` `[Globale]` `i18n.js` deve fornire varianti di testo errore per livello utente (`state.user.skillLevel`), con formulazione semplificata per utenti novizi.

#### 5.17.10.7.5 – Immissione e conferma della correzione
*Sintesi:* Quando l'utente immette la correzione di un errore, tale correzione deve essere implementata tramite un'azione esplicita dell'utente (es. tasto INVIO).

- **IMPL-5.17.10.7.5-01** `[HTML][JS]` `[Main]` Il form di correzione deve richiedere l'attivazione esplicita di un controllo "Conferma"/invio (`button[type=submit]` o Enter); nessuna applicazione automatica al solo cambio valore.

#### 5.17.10.7.6 – Conferma della correzione dell'errore
*Sintesi:* Il sistema deve riconoscere tutte le correzioni dell'utente, accettando la voce corretta o fornendo un messaggio di errore separato per una voce ancora errata.

- **IMPL-5.17.10.7.6-01** `[JS]` `[Main]` `validate.js` deve rivalutare il campo dopo ogni correzione e chiamare `feedback.js:acknowledge(el)` se valido, oppure aggiornare il messaggio d'errore se ancora non valido.

#### 5.17.10.7.7 – Errori in elementi non normalmente visibili
*Sintesi:* Non devono essere incluse referenze a errori di elementi non normalmente visibili agli utenti, a meno che l'utente non abbia esplicitamente richiesto informazioni diagnostiche dettagliate per troubleshooting a livello di programmatore.

- **IMPL-5.17.10.7.7-01** `[JS]` `[Main]` `notify.js` deve mostrare per default solo messaggi applicativi; dettagli tecnici/stack trace compaiono solo dietro azione esplicita "Mostra dettagli tecnici" (`<details>`).

#### 5.17.10.8 – Descrizione dell'errore
*Sintesi:* Se viene presentato un messaggio di errore semplice, agli utenti deve essere fornito l'accesso a una descrizione più dettagliata.

- **IMPL-5.17.10.8-01** `[HTML][JS]` `[Main]` Ogni messaggio in `#message-line`/toast deve includere un controllo "Dettagli" che espande (`<details>`/dialog) la descrizione completa tramite `notify.js`.

#### 5.17.10.9 – Tempistica dei messaggi
*Sintesi:* I messaggi di errore devono essere forniti entro 0,2 secondi dal rilevamento dell'errore.

- **IMPL-5.17.10.9-01** `[JS]` `[Globale]` `config.js` deve definire `errorMessageMaxDelayMs = 200` (Tabella V); `validate.js`/`notify.js` visualizzano il messaggio entro tale soglia, verificabile con test T.

#### 5.17.10.10 – Recupero dall'errore e cambio di processo (undo multilivello)
*Sintesi:* L'utente deve poter interrompere un processo di controllo in qualunque punto di una sequenza e tornare facilmente a livelli precedenti nei processi multi-passo, per annullare un errore o effettuare una modifica desiderata.

- **IMPL-5.17.10.10-01** `[JS][HTML]` `[Footer]` `#function-keys` deve includere un comando "Indietro/Annulla passo" che richiama `nav.js:undoStep()`/`state.js` per tornare al passo precedente del processo multi-step in qualunque momento.

#### 5.17.10.11 – Errori di ortografia
*Sintesi:* Errori di ortografia e altri errori comuni non devono produrre comandi di sistema validi o avviare transazioni.

- **IMPL-5.17.10.11-01** `[JS]` `[Main]` `nav.js:resolveCommand()` deve richiedere corrispondenza esatta o abbreviazione univoca registrata (vedi `IMPL-5.1.3.12.5.5-01`); input non riconosciuti non mappano mai per approssimazione silenziosa.

#### 5.17.10.12 – Riconoscere errori ortografici comuni
*Sintesi:* Il sistema deve riconoscere errori ortografici comuni dei comandi, informare l'utente delle voci non riconosciute, fornire una voce corretta simile e visualizzare la proposta per revisione e conferma dell'utente.

- **IMPL-5.17.10.12-01** `[JS][HTML]` `[Main]` `nav.js` deve implementare un confronto a distanza (es. Levenshtein) sui comandi non riconosciuti e proporre il comando più simile in un dialogo di conferma (`dialog.js`) prima dell'esecuzione.

#### 5.17.10.13 – Errori nei comandi in pila (stack)
*Sintesi:* Per richiedere correzioni di un errore in comandi accatastati, il sistema deve visualizzare la sequenza con gli errori evidenziati; ove possibile, deve essere fornita una procedura per correggere l'errore e salvare la pila.

- **IMPL-5.17.10.13-01** `[HTML][CSS]` `[Main]` La vista "coda comandi" (`nav.js:commandStack`, vedi `IMPL-5.1.3.12.2.2.5-01`) deve elencare i comandi accodati con quelli in errore evidenziati (`.cmd--error`).

#### 5.17.10.13.1 – Procedura di correzione dell'errore
*Sintesi:* Quando possibile, deve essere fornita una procedura per correggere l'errore e salvare la pila.

- **IMPL-5.17.10.13.1-01** `[JS]` `[Main]` `nav.js` deve esporre un'azione "Correggi e mantieni la coda" che modifica solo il comando errato nello stack senza rimuovere gli altri comandi accodati.

#### 5.17.10.13.2 – Esecuzione parziale
*Sintesi:* Se l'errore non è stato rilevato finché alcuni comandi della pila non sono stati eseguiti, il sistema deve informare l'utente su quali comandi sono stati eseguiti con successo.

- **IMPL-5.17.10.13.2-01** `[JS][HTML]` `[Main]` `notify.js` deve mostrare un riepilogo dei comandi eseguiti con successo prima dell'errore, distinto da quelli non eseguiti/falliti, nello stesso messaggio/dialogo.

#### 5.17.10.14 – Visualizzazione delle voci errate
*Sintesi:* Un messaggio di errore deve essere visualizzato continuamente finché l'errore non viene corretto o l'utente non lo chiude.

- **IMPL-5.17.10.14-01** `[JS][HTML]` `[Main]` Il messaggio d'errore non deve auto-nascondersi con timeout; resta visibile finché `validate.js` non rileva la correzione o l'utente attiva il controllo di chiusura (`aria-label="Chiudi errore"`).

#### 5.17.10.15 – Correzione automatica degli errori
*Sintesi:* La correzione automatica dovrebbe essere fornita se: a) l'errore ha soluzione univoca; b) la correzione è apparente all'utente; c) la funzionalità può essere disabilitata su richiesta dell'utente.

- **IMPL-5.17.10.15-01** `[JS]` `[Main]` `validate.js` deve applicare auto-correzione solo per errori con soluzione univoca deterministica (es. spazi superflui, maiuscole), sempre mostrata evidenziata per conferma (vedi `IMPL-5.1.2.1.7.11.2-01`).
- **IMPL-5.17.10.15-02** `[JS]` `[Globale]` `prefs.js` deve esporre `autoCorrect: boolean` disabilitabile su richiesta, memorizzato in `localStorage`.

#### 5.17.10.16 – Posizione dei messaggi di errore
*Sintesi:* I messaggi di errore devono comparire il più vicino possibile alla voce utente che ha causato il messaggio, senza occultare aree di controllo o display necessarie per il compito.

- **IMPL-5.17.10.16-01** `[CSS][HTML]` `[Main]` Il messaggio di errore in linea deve essere posizionato subito sotto/accanto al campo (`.field-error`), senza sovrapporsi a controlli adiacenti al breakpoint corrente.

#### 5.17.10.17 – Informazioni occultate
*Sintesi:* Se un messaggio di errore occulta informazioni rilevanti per il compito, deve essere fornito un mezzo per spostare il messaggio.

- **IMPL-5.17.10.17-01** `[JS][HTML]` `[Main]` I messaggi di errore in overlay/dialogo non modale devono essere trascinabili (Pointer Events) o riposizionabili con un controllo dedicato quando coprono `#content-region`.

#### 5.17.10.18 – Messaggi di errore multipli
*Sintesi:* Se l'utente ripete un errore di immissione, il secondo messaggio deve includere una variazione percepibile, così che l'utente sia certo che il tentativo di correzione sia stato elaborato.

- **IMPL-5.17.10.18-01** `[JS][CSS]` `[Main]` `notify.js` deve applicare una variazione percepibile (es. `.is-repeated`, testo aggiuntivo, breve animazione) quando lo stesso errore si ripete sullo stesso campo.

#### 5.17.12 – Guida (help)
*Sintesi:* Oltre ai requisiti qui previsti, indicazioni dettagliate e best practice possono essere reperite in ANSI/HFES 200.3.

- **IMPL-5.17.12-01** `[JS]` `[Globale]` Criterio di revisione (I): il modulo di help deve essere documentato internamente con riferimento alle linee guida adottate dal progetto, senza introdurre dipendenze esterne a runtime.

#### 5.17.12.1 – Accesso
*Sintesi:* Il sistema deve fornire guida integrata, consentendo agli utenti di accedere facilmente all'help tecnico incorporato.

- **IMPL-5.17.12.1-01** `[HTML][JS]` `[Header]` Un controllo `#btn-help` sempre visibile nell'header deve aprire l'help integrato (`dialog.js`/`#aux-region`) senza richiedere risorse esterne alla rete.

#### 5.17.12.2 – Ausili espliciti per la gestione degli errori
*Sintesi:* L'help deve essere disponibile tramite ausili espliciti (etichette, prompt, messaggi di avviso) e impliciti (cueing).

- **IMPL-5.17.12.2-01** `[HTML]` `[Main]` Ogni campo deve avere etichetta (`<label>`), prompt (`placeholder`/testo di aiuto) e messaggi di avviso (`aria-describedby`) come ausili espliciti, oltre a cueing implicito (`required`, icone di stato).

#### 5.17.12.3 – Azione standard per richiedere l'help
*Sintesi:* Deve essere sempre disponibile un'azione semplice e standard per l'utente per accedere all'help.

- **IMPL-5.17.12.3-01** `[JS][HTML]` `[Globale]` `#btn-help` deve essere presente in posizione fissa nell'header in ogni vista, con scorciatoia da tastiera standard (es. `F1`) gestita da `nav.js`.

#### 5.17.12.4 – Help integrato
*Sintesi:* Come minimo, l'help integrato deve includere definizioni delle opzioni consentite, delle capacità del sistema, delle procedure e degli intervalli di valori.

- **IMPL-5.17.12.4-01** `[JS][HTML]` `[Main]` Il contenuto dell'help in `#aux-region`/dialogo deve includere, per vista, sezioni "opzioni disponibili", "procedure" e "intervalli ammessi", popolate da `i18n.js`.

#### 5.17.12.5 – Help contestuale
*Sintesi:* L'help contestuale deve essere specifico per il contesto da cui è stato richiesto; dopo l'accesso iniziale, il sistema deve permettere all'utente di interrogare la documentazione di help.

- **IMPL-5.17.12.5-01** `[JS]` `[Globale]` `#btn-help` deve invocare `nav.js:openHelp(state.currentView)` passando l'id della vista/campo attivo; la finestra di help deve includere un campo di ricerca per query successive.

#### 5.17.12.6 – Minimizzare i rimandi incrociati
*Sintesi:* I singoli display di help integrato dovrebbero contenere informazioni sufficienti a rispondere alla query dell'utente, anche a costo di ridondanza nel sistema di help complessivo.

- **IMPL-5.17.12.6-01** `[JS]` `[Globale]` Criterio di redazione contenuti (I): ogni voce di help in `i18n.js` deve essere autoconsistente, senza richiedere di seguire più rimandi per rispondere alla domanda principale.

#### 5.17.12.7 – Definizioni
*Sintesi:* La sezione di help dovrebbe contenere un elenco di abbreviazioni e acronimi usati nell'applicazione principale.

- **IMPL-5.17.12.7-01** `[HTML][JS]` `[Main]` L'help deve includere una sotto-vista "Glossario/Acronimi" (`#help-glossary`) generata da una tabella di termini in `i18n.js`.

#### 5.17.12.8 – Help multilivello
*Sintesi:* Se un display di help iniziale fornisce solo sintesi, spiegazioni più dettagliate devono essere disponibili tramite interrogazione dell'utente o drill-down.

- **IMPL-5.17.12.8-01** `[HTML][JS]` `[Main]` Il contenuto di help deve strutturarsi in sintesi iniziale con controlli di espansione (`<details>`/link "Maggiori informazioni") che richiamano `nav.js:openHelp(topic, level="detail")`.

#### 5.17.12.9 – Terminologia di help coerente
*Sintesi:* Documentazione online, offline e istruzioni di help devono usare terminologia identica per descrivere o definire concetti o processi identici.

- **IMPL-5.17.12.9-01** `[JS]` `[Globale]` Tutti i testi (UI, messaggi di errore, help) devono attingere allo stesso dizionario terminologico centralizzato in `i18n.js`, senza sinonimi non controllati per lo stesso concetto.

#### 5.17.12.10 – Help avviato dal sistema
*Sintesi:* Per sistemi con utenti previsti novizi o poco frequenti, può essere usata una funzione di help avviata dal sistema; quando fornita, deve avere le caratteristiche di 5.17.12.10.1–.10.3.

- **IMPL-5.17.12.10-01** `[JS]` `[Globale]` `prefs.js` deve esporre `systemInitiatedHelp: boolean`, attivabile per profili utente novizi (`state.user.skillLevel === "novice"`), rispettando le sottosezioni seguenti.

#### 5.17.12.10.1 – Specifico
*Sintesi:* L'help avviato dal sistema deve fornire solo aiuto specifico per il compito o contesto corrente dell'utente.

- **IMPL-5.17.12.10.1-01** `[JS]` `[Main]` Il suggerimento proattivo (`notify.js:suggestHelp()`) deve usare `state.currentView`/`state.focusedField` per mostrare solo contenuto pertinente al contesto attivo, mai help generico.

#### 5.17.12.10.2 – Non intrusivo
*Sintesi:* L'help avviato dal sistema non deve interferire con o occultare alcuna area di controllo o display necessaria per il compito corrente.

- **IMPL-5.17.12.10.2-01** `[CSS][HTML]` `[Main]` I suggerimenti di help proattivo devono apparire come toast non modale in `#toast-region`/`#aux-region`, mai come overlay che copra `#content-region`.

#### 5.17.12.10.3 – Facile da abilitare e disabilitare
*Sintesi:* Un utente relativamente novizio deve poter disabilitare e abilitare la funzionalità di help avviata dal sistema con il minimo sforzo.

- **IMPL-5.17.12.10.3-01** `[HTML][JS]` `[Main]` Un controllo toggle accessibile (es. in pannello preferenze) deve attivare/disattivare `prefs.js:systemInitiatedHelp` con una singola interazione.

#### 5.17.13 – Prompt
*Sintesi:* I prompt devono essere conformi ai criteri di 5.17.13.1–.13.10.

- **IMPL-5.17.13-01** `[JS]` `[Globale]` `notify.js` deve centralizzare la generazione dei prompt applicando i criteri di tutte le sottosezioni seguenti in un unico componente riutilizzabile.

#### 5.17.13.1 – Uso
*Sintesi:* Prompt e istruzioni di help devono essere usati per spiegare comandi, messaggi di errore, capacità del sistema, formati di display, procedure e sequenze, oltre a fornire dati.

- **IMPL-5.17.13.1-01** `[HTML][JS]` `[Main]` I prompt in linea (`placeholder`, testo sopra/sotto il campo) devono coprire spiegazione del comando, formato atteso e procedura, generati da `i18n.js`.

#### 5.17.13.2 – Prompt per modalità speciali
*Sintesi:* Quando si opera in modalità speciali, il sistema deve visualizzare la designazione della modalità e il/i file in elaborazione.

- **IMPL-5.17.13.2-01** `[HTML][JS]` `[Header]` `#mode-indicator` nell'header deve mostrare sempre la modalità operativa corrente e, se pertinente, il nome del file/risorsa in elaborazione, aggiornato da `state.js`.

#### 5.17.13.3 – Conferma dell'utente
*Sintesi:* Prima di elaborare richieste utente che comportino modifiche estese o definitive ai dati esistenti, il sistema deve richiedere la conferma dell'utente.

- **IMPL-5.17.13.3-01** `[JS][HTML]` `[Globale]` Vedi `IMPL-5.1.3.12.1.5-01`/`IMPL-5.17.10.6-01`: `dialog.js` deve richiedere conferma esplicita per ogni azione marcata `data-extensive-change="true"`.

#### 5.17.13.4 – Confermare le operazioni di abort
*Sintesi:* Quando voci o modifiche ai dati saranno annullate da un'azione di abort, il sistema deve richiedere la conferma dell'utente.

- **IMPL-5.17.13.4-01** `[JS][HTML]` `[Main]` Il controllo "Annulla"/chiusura di un form con modifiche non salvate (`state.formDirty === true`) deve aprire un dialogo di conferma (`dialog.js`) prima di scartare i dati.

#### 5.17.13.5 – Prompt per dati mancanti
*Sintesi:* Quando vengono rilevati dati mancanti, il sistema deve richiedere all'utente di fornirli.

- **IMPL-5.17.13.5-01** `[JS][HTML]` `[Main]` `validate.js` deve evidenziare (`aria-invalid="true"`, `.field--missing`) e generare un prompt esplicito per ogni campo obbligatorio mancante al tentativo di invio.

#### 5.17.13.6 – Visualizzazione standard
*Sintesi:* I messaggi di prompt devono essere visualizzati in un'area standardizzata dei display.

- **IMPL-5.17.13.6-01** `[CSS][HTML]` `[Footer]` Tutti i prompt di sistema (non legati a un singolo campo) devono apparire in `#message-line`, area standard e coerente in ogni vista.

#### 5.17.13.7 – Prompt espliciti
*Sintesi:* I prompt e le istruzioni di help per il dialogo controllato dal sistema devono essere espliciti.

- **IMPL-5.17.13.7-01** `[JS]` `[Globale]` I testi dei prompt in `i18n.js` devono formulare esplicitamente l'azione richiesta (es. "Inserire la data nel formato GG/MM/AAAA"), non indicazioni generiche.

#### 5.17.13.8 – Memorizzazione
*Sintesi:* I prompt devono essere presentati in modo che l'utente non debba memorizzare sequenze lunghe o fare riferimento a procedure scritte secondarie per rispondere.

- **IMPL-5.17.13.8-01** `[HTML]` `[Main]` Il prompt deve riportare direttamente nel campo (o accanto) tutte le informazioni necessarie alla risposta (formato, esempio), senza rimandare a manuali esterni.

#### 5.17.13.9 – Chiarezza del prompt
*Sintesi:* I prompt devono essere chiari e comprensibili.

- **IMPL-5.17.13.9-01** `[JS]` `[Globale]` Criterio di revisione testi (I): i prompt in `i18n.js` devono essere validati per chiarezza (linguaggio semplice, frasi brevi) prima del rilascio.

#### 5.17.13.10 – Schemi di codifica
*Sintesi:* I prompt non devono richiedere il riferimento a schemi o convenzioni di codifica che potrebbero essere sconosciuti agli utenti occasionali.

- **IMPL-5.17.13.10-01** `[JS]` `[Main]` I prompt devono usare linguaggio naturale e valori decimali (vedi `IMPL-5.1.3.12.1.13.1-01`), evitando di richiedere la conoscenza di codici interni/enumerazioni per rispondere.

### 3.8 Notifiche, messaggi, allarmi e audio

Requisiti su notifiche, messaggistica (chat), allarmi/avvisi e segnali audio/vocali, gestiti principalmente da `notify.js` e `feedback.js` tramite `#alert-region`, `#alert-summary`, `#toast-region` e `#message-line` (vedi 00-head.md §2.2, §2.5).

#### 5.3.1.3 – Significato del segnale audio
*Sintesi (shall):* Ogni segnale audio deve avere un solo significato (l'orecchio distingue efficacemente toni periodici/complessi nel rumore di fondo).

- **IMPL-5.3.1.3-01** `[JS]` `[Globale]` `feedback.js` definisce in `config.js` una mappa univoca `beep(type)` → significato (es. `alarm`, `caution`, `confirm`, `error`, `message`), senza riutilizzare lo stesso suono per significati diversi.
- **IMPL-5.3.1.3-02** `[JS]` `[Globale]` `notify.js` verifica, in fase di accodamento del messaggio/allarme, che la categoria sia associata a un solo tipo di segnale sonoro definito nella tabella di `config.js`, impedendo sovrapposizioni.

#### 5.3.1.8 – Disattivazione manuale dei segnali non critici
*Sintesi (shall):* I segnali audio non critici devono poter essere disattivati a discrezione dell'utente.

- **IMPL-5.3.1.8-01** `[HTML][JS]` `[Footer]` Il pannello preferenze (richiamabile da `#user-menu`/`#btn-display-settings`) espone `<input type="checkbox" id="pref-audio-noncritical">` gestito da `prefs.js` per disattivare i segnali audio non critici in `feedback.js` (i segnali critici/di allarme restano sempre attivi).
- **IMPL-5.3.1.8-02** `[JS]` `[Globale]` La preferenza è salvata in `localStorage` da `prefs.js` e letta da `feedback.js.beep()` prima di emettere un suono non critico.

#### 5.3.1.9 – Indicazione visiva di audio disattivato
*Sintesi (shall):* Quando l'utente può disattivare i segnali audio non critici, deve essere fornita un'indicazione visiva che il segnale è spento.

- **IMPL-5.3.1.9-01** `[HTML][CSS]` `[Header]` Quando l'audio non critico è disattivato, `#alert-summary` mostra un'icona `.icon-audio-off` persistente con testo "Audio disattivato" (doppia codifica icona+testo).
- **IMPL-5.3.1.9-02** `[JS]` `[Globale]` `state.js` espone `state.audio.nonCriticalMuted`, aggiornato da `prefs.js`; l'icona in `#alert-summary` si lega a questo stato tramite `CustomEvent`.

#### 5.3.2.1.5 – Persistenza del segnale di allarme audio
*Sintesi (shall):* I segnali audio di warning devono persistere fino al ripristino delle condizioni normali o al riconoscimento (acknowledge) dell'allarme.

- **IMPL-5.3.2.1.5-01** `[JS]` `[Globale]` `feedback.js.beep('alarm')` ripete il segnale a intervallo configurabile in `config.js` finché `notify.js` non riceve l'`acknowledge()` dell'allarme o lo stato torna normale in `state.js`.
- **IMPL-5.3.2.1.5-02** `[HTML][JS]` `[Footer]` `#alert-summary` mantiene il conteggio degli allarmi non riconosciuti e il banner in `#alert-region` resta visibile finché l'utente non preme il pulsante "Riconosci".

#### 5.3.2.2.1 – Distinzione tra caution e warning
*Sintesi (shall):* I segnali di caution devono essere facilmente distinguibili dai segnali di warning.

- **IMPL-5.3.2.2.1-01** `[JS]` `[Globale]` `config.js` definisce pattern sonori distinti per `caution` e `warning` (frequenza/ritmo differenti) usati da `feedback.js.beep(type)`; vedi anche IMPL-5.7.3.5.1-01/IMPL-5.7.3.5.2-01 per la codifica visiva corrispondente.

#### 5.3.2.5 – Sistema di priorità dei messaggi
*Sintesi (shall):* In caso di possibile presentazione simultanea di messaggi automatici, un sistema di priorità deve far sì che il messaggio più critico prevalga sulla presentazione iniziale di quelli a priorità inferiore.

- **IMPL-5.3.2.5-01** `[JS]` `[Globale]` `notify.js` implementa una coda con priorità numerica (`warning` > `caution` > `advisory` > `info`) definita in `config.js`.
- **IMPL-5.3.2.5-02** `[JS]` `[Globale]` Le funzioni `notify.enqueue(msg, priority)` e `notify.presentNext()` gestiscono l'ordinamento e interrompono/posticipano la presentazione corrente quando arriva un messaggio di priorità superiore.

#### 5.3.2.6 – Ordine dei messaggi dopo il primo
*Sintesi (shall):* Dopo la presentazione del messaggio a priorità massima, gli altri messaggi devono seguire l'ordine di priorità; nessun messaggio di caution va presentato finché tutti i warning non sono terminati.

- **IMPL-5.3.2.6-01** `[JS]` `[Globale]` `notify.js` blocca la presentazione dei messaggi `caution` in coda finché esistono messaggi `warning` non terminati/riconosciuti, mantenendo l'ordinamento di IMPL-5.3.2.5-01.

#### 5.3.2.7 – Relazione con i display visivi
*Sintesi (shall):* I dispositivi di allarme audio, se usati insieme a display visivi, devono essere supplementari e servire a dirigere l'attenzione dell'utente verso il display visivo appropriato.

- **IMPL-5.3.2.7-01** `[JS]` `[Globale]` `feedback.js.beep(type)` è sempre invocato insieme a un aggiornamento visivo (evidenziazione in `#alert-region`/`#alert-summary`) tramite `notify.js`: nessun suono è emesso senza una corrispondente indicazione visiva.

#### 5.3.3.2.1 – Segnali avviati manualmente
*Sintesi (shall):* I segnali avviati manualmente devono essere terminati manualmente.

- **IMPL-5.3.3.2.1-01** `[HTML][JS]` `[Globale]` I segnali audio avviati con un'azione utente esplicita richiedono un controllo esplicito di stop (`feedback.js.stop(id)` collegato a un pulsante "Interrompi suono"); non si spengono automaticamente dopo un timeout.

#### 5.3.5.10.1 – Shutoff manuale
*Sintesi (shall):* Quando è previsto uno shutoff manuale, deve essere fornita un'indicazione visiva che il segnale di warning è stato disattivato.

- **IMPL-5.3.5.10.1-01** `[HTML][JS]` `[Header]` Quando l'utente usa lo shutoff manuale (pulsante "Silenzia" in `#alert-region`), compare l'indicazione visiva persistente descritta in IMPL-5.3.1.9-01 finché l'audio non è riattivato o l'allarme risolto.

#### 5.3.5.10.4 – Ridondanza visiva degli annunci sonori
*Sintesi (shall):* Tutte le annunciazioni aurali non verbali devono essere accompagnate da un'annunciazione visiva che definisce la condizione.

- **IMPL-5.3.5.10.4-01** `[HTML][JS]` `[Footer]` Ogni `feedback.js.beep('alarm'|'warning')` non verbale è accompagnato da un banner in `#alert-region` con `role="alert"`, icona e colore semantico che definisce la condizione, gestito da `notify.js`.

#### 5.3.5.11 – Controllo del volume
*Sintesi (shall, con sotto-criteri 5.3.5.11.1–.5):* I movimenti del controllo volume devono impedire di ridurre il suono a livello inudibile o di portarlo a un livello eccessivo.

- **IMPL-5.3.5.11-01** `[HTML][CSS]` `[Footer]` Il controllo volume è `<input type="range" id="pref-volume" min="20" max="100">` (valori minimo/massimo di progetto in `config.js`) nel pannello preferenze, con target touch ≥ `--touch-target-min`; non consente di impostare 0% per i segnali critici.

#### 5.3.5.11.1 – Controllo automatico o manuale
*Sintesi (shall):* Il volume del segnale di allarme deve poter essere controllato dall'utente, da un meccanismo automatico, o da entrambi, in base alla situazione operativa.

- **IMPL-5.3.5.11.1-01** `[JS]` `[Globale]` `feedback.js` calcola il volume effettivo combinando il valore manuale (`#pref-volume`, `prefs.js`) con un fattore automatico opzionale derivato da `state.js` (es. contesto operativo), secondo regole definite in `config.js`.

#### 5.3.9.1 – Funzione supportiva dell'audio
*Sintesi (shall):* I segnali audio usati insieme a display visivi devono essere supplementari ai segnali visivi.

- **IMPL-5.3.9.1-01** `[JS]` `[Globale]` Vedi IMPL-5.3.2.7-01: ogni segnale audio è supplementare e abbinato a un display visivo gestito da `notify.js`.

#### 5.3.9.2 – Direzionare l'attenzione dell'utente
*Sintesi (shall):* I segnali audio devono essere usati per allertare e dirigere l'attenzione dell'utente verso il display visivo appropriato.

- **IMPL-5.3.9.2-01** `[JS][CSS]` `[Globale]` `feedback.js.beep(type)` attiva in sincronia l'evidenziazione (classe `.is-alerting`, animazione CSS) dell'area del display coinvolto, per dirigere l'attenzione dell'utente.

#### 5.3.9.3 – Caratteristiche del segnale
*Sintesi (may):* I segnali possono essere singoli (one-time) o intermittenti.

- **IMPL-5.3.9.3-01** `[JS]` `[Globale]` `config.js` definisce per tipo di segnale se `mode: 'once'` o `mode: 'repeat'`; `feedback.js.beep(type)` rispetta la modalità configurata.

#### 5.3.9.4 – Terminazione dei segnali intermittenti
*Sintesi (shall):* I segnali intermittenti devono essere terminati automaticamente quando non più applicabili, o dal controllo dell'utente.

- **IMPL-5.3.9.4-01** `[JS]` `[Globale]` `feedback.js` interrompe automaticamente un segnale `repeat` quando `notify.js` rileva che la condizione non è più valida, oppure quando l'utente preme "Riconosci"/"Silenzia".

#### 5.3.9.5 – Stato delle impostazioni di allarme
*Sintesi (shall):* Quando gli allarmi sono definiti su logica utente, l'utente deve poter ottenere informazioni di stato sulle impostazioni correnti (variabili e valori critici).

- **IMPL-5.3.9.5-01** `[HTML][JS]` `[Main]` Una vista "Impostazioni allarmi" (`#content-region`, raggiunta via `nav.js`) mostra in sola lettura le soglie/variabili configurate in `prefs.js`/`config.js`.

#### 5.3.9.6 – Informazioni di stato allarme nel cambio turno
*Sintesi (shall):* Le informazioni di stato degli allarmi devono essere disponibili nelle situazioni di monitoraggio con cambio di responsabilità (es. cambio turno).

- **IMPL-5.3.9.6-01** `[JS][HTML]` `[Main]` La vista di IMPL-5.3.9.5-01 è accessibile a qualunque utente con ruolo abilitato (`auth.js`) e mostra `state.alarms` aggiornato in tempo reale, utile al passaggio di consegne tra operatori.

#### 5.3.10.5 – Controllo di priorità dei messaggi vocali
*Sintesi (shall):* Ove possano verificarsi messaggi simultanei, essi devono essere prioritizzati così che il messaggio più critico riceva priorità di trasmissione.

- **IMPL-5.3.10.5-01** `[JS]` `[Globale]` Vedi IMPL-5.3.2.5-01/-02: `notify.js` applica la stessa coda con priorità anche ai messaggi vocali (text-to-speech).

#### 5.3.10.7 – Capacità di annullamento del messaggio
*Sintesi (shall):* Deve essere fornita una capacità di annullamento manuale per tutti i display vocali dopo la presentazione iniziale.

- **IMPL-5.3.10.7-01** `[HTML][JS]` `[Footer]` Ogni messaggio vocale presentato da `notify.js` espone un pulsante "Annulla" (in `#message-line`/toast) che richiama `speechSynthesis.cancel()`/`feedback.js.stop()`.

#### 5.3.10.8 – Ripetizione del messaggio su comando
*Sintesi (shall):* La ripetizione del messaggio deve essere fornita su comando dell'utente.

- **IMPL-5.3.10.8-01** `[HTML][JS]` `[Footer]` Un pulsante "Ripeti" accanto a `#message-line` richiama `notify.repeatLast()` per rigenerare l'ultimo messaggio vocale su comando dell'utente.

#### 5.3.13.3 – Adattamento al contesto d'uso
*Sintesi (shall):* I sistemi di riconoscimento vocale devono adattarsi alla variabilità degli ambienti acustici, degli stili di eloquio e degli errori utente.

- **IMPL-5.3.13.3-01** `[JS]` `[Globale]` Se è implementato il riconoscimento vocale (Web Speech API), `config.js` consente di regolare soglie di sensibilità/rumore; `validate.js` gestisce gli errori ricorrenti, senza richiedere ricalibrazione manuale per ogni utente.

#### 5.3.13.4 – Addestramento minimo
*Sintesi (shall):* I sistemi di riconoscimento vocale devono richiedere un addestramento minimo.

- **IMPL-5.3.13.4-01** `[HTML][JS]` `[Main]` Il comando vocale, se presente, usa un vocabolario minimo documentato in una guida contestuale (`#btn-help`), senza fasi di training dedicate.

#### 5.3.13.5 – Feedback di comprensione
*Sintesi (shall):* I sistemi di riconoscimento vocale devono fornire feedback che confermi all'utente di essere stato compreso.

- **IMPL-5.3.13.5-01** `[JS][HTML]` `[Footer]` Ogni comando vocale riconosciuto produce un feedback visivo immediato in `#status-line` (testo riconosciuto) entro i tempi di Tabella V (≤ 0,2 s), tramite `feedback.js.setStatus()`.

#### 5.3.13.6 – Vocabolario minimizzato
*Sintesi (shall):* Il vocabolario di input deve essere minimizzato e fonologicamente distinto per eliminare interpretazioni errate.

- **IMPL-5.3.13.6-01** `[JS]` `[Globale]` Il set di comandi vocali riconosciuti è definito in `config.js` con termini fonologicamente distinti, per minimizzare le interpretazioni errate.

#### 5.3.13.7 – Assenza di pause tra parole
*Sintesi (shall):* Il riconoscimento vocale non deve richiedere pause tra le parole né pronuncia esagerata.

- **IMPL-5.3.13.7-01** `[JS]` `[Globale]` Il modulo di riconoscimento vocale (Web Speech API, `continuous: true`) non impone pause artificiali tra parole né pronuncia enfatizzata, secondo la configurazione di `config.js`.

#### 5.3.13.8 – Prompt vocale dal computer
*Sintesi (shall):* Il prompt vocale dal computer deve essere fornito quando conviene liberare l'utente dalla lettura di un display.

- **IMPL-5.3.13.8-01** `[JS]` `[Globale]` Quando una funzione richiede attenzione visiva altrove, `notify.js` può generare un prompt vocale (`SpeechSynthesisUtterance`) oltre al messaggio testuale.

#### 5.3.13.9 – Ripetizione in assenza di risposta
*Sintesi (shall):* L'assenza di risposta dell'utente al prompt deve risultare nella ripetizione del prompt.

- **IMPL-5.3.13.9-01** `[JS]` `[Globale]` `notify.js` ripete il prompt vocale/testuale a intervallo configurabile in `config.js` se non riceve risposta (input o acknowledge) entro il timeout stabilito.

#### 5.3.13.10 – Correzione di suoni involontari
*Sintesi (shall):* Deve essere fornita una capacità di rigettare suoni involontari (starnuti, tosse, parole non di comando).

- **IMPL-5.3.13.10-01** `[JS]` `[Globale]` Il riconoscimento vocale applica una soglia di confidenza minima (in `config.js`) e scarta eventi audio brevi/non consistenti prima di interpretarli come comando.

#### 5.3.14.1 – Non unico dispositivo di comando
*Sintesi (shall):* I dispositivi di riconoscimento vocale non devono essere l'unico dispositivo di comando.

- **IMPL-5.3.14.1-01** `[HTML][JS]` `[Globale]` Ogni funzione attivabile tramite comando vocale ha un equivalente attivabile tramite touch/tastiera sugli stessi elementi (`button`, `input`); `nav.js` garantisce la parità di funzioni.

#### 5.3.14.2 – Dispositivo di comando alternativo
*Sintesi (shall):* Deve essere fornito un dispositivo di comando alternativo in caso di degrado o guasto del riconoscimento vocale.

- **IMPL-5.3.14.2-01** `[JS]` `[Globale]` Se il riconoscimento vocale fallisce/non è disponibile, `state.js` imposta `state.voice.available=false` e l'interfaccia touch/tastiera resta sempre pienamente operativa senza richiedere conferma.

#### 5.7.1.2 – Sistema di allarme efficace
*Sintesi (should):* Un sistema di allarme efficace dovrebbe: a. non generare avvisi quando tutto è normale; b. ridurre il carico di assimilazione/memoria; c. minimizzare il tempo di rilevamento/valutazione dei guasti; d. fornire allerta visiva comprensibile in ogni condizione ambientale; e. essere standardizzato; f. consentire crescita del sistema senza nuovi componenti.

- **IMPL-5.7.1.2-01** `[JS]` `[Globale]` (a) `notify.js` non genera allarmi/avvisi audio o visivi quando `state.system.status==='normal'` per tutte le sottoparti monitorate: l'assenza di allarmi è lo stato di default silenzioso.
- **IMPL-5.7.1.2-02** `[JS][HTML]` `[Footer]` (b,c) `#alert-summary` e `#alert-region` aggregano e sintetizzano le condizioni di errore (conteggio, priorità) per ridurre il carico di memorizzazione e il tempo di rilevamento/valutazione.
- **IMPL-5.7.1.2-03** `[CSS]` `[Globale]` (d) Gli stati di allarme usano colori ad alto contrasto (`--color-critical`, `--color-alarm`) e doppia codifica (icona+testo), leggibili con i temi `theme-light/dark/high-contrast` in ogni condizione di luminosità ambientale simulata dalle preferenze utente.
- **IMPL-5.7.1.2-04** `[CSS][JS]` `[Globale]` (e) Stili e icone di allarme/caution/advisory sono standardizzati in `config.js` e nei token CSS (`--color-*`), riutilizzati identicamente in tutte le viste.
- **IMPL-5.7.1.2-05** `[JS]` `[Globale]` (f) `notify.js` tratta le categorie di allarme come dati configurabili (array in `config.js`), così da aggiungere nuovi tipi di allarme senza modificare markup o componenti esistenti.

#### 5.7.1.7 – Precedenza dei segnali
*Sintesi (shall):* Ciascuno dei segnali seguenti ha precedenza su quelli sottostanti (warning > caution > advisory).

- **IMPL-5.7.1.7-01** `[JS]` `[Globale]` `notify.js` definisce l'ordine di priorità `warning > caution > advisory` in `config.js` (vedi IMPL-5.3.2.5-01), applicato sia alla coda messaggi sia alla visualizzazione in `#alert-region`/`#alert-summary`.

#### 5.7.1.7.1 – Warning (allarme)
*Sintesi (def.):* Segnale che allerta l'utente di una condizione pericolosa che richiede azione immediata.

- **IMPL-5.7.1.7.1-01** `[CSS][HTML]` `[Globale]` La categoria `warning` usa `--color-critical`, icona dedicata e testo "Azione immediata richiesta" (i18n.js); vedi IMPL-5.7.3.5.1-01 per la codifica lampeggiante.

#### 5.7.1.7.2 – Caution (allerta)
*Sintesi (def.):* Segnale che allerta l'utente di una condizione pericolosa imminente che richiede attenzione ma non necessariamente azione immediata.

- **IMPL-5.7.1.7.2-01** `[CSS][HTML]` `[Globale]` La categoria `caution` usa `--color-warning` (giallo) e testo "Richiede attenzione"; vedi IMPL-5.7.3.5.2-01.

#### 5.7.1.7.3 – Advisory
*Sintesi (def.):* Segnale che indica configurazione/condizione sicura o normale, o un cambio di stato importante ma non urgente.

- **IMPL-5.7.1.7.3-01** `[CSS][HTML]` `[Globale]` La categoria `advisory` usa `--color-advisory` (ciano/blu) per configurazioni normali o cambi di stato non urgenti, mostrata in `#alert-region` con priorità più bassa.

#### 5.7.2.1.2 – Contenuto del messaggio di warning
*Sintesi (shall):* Le etichette/placard di warning devono descrivere esplicitamente il pericolo, come evitarlo e le conseguenze del mancato evitamento.

- **IMPL-5.7.2.1.2-01** `[HTML][JS]` `[Main]` I messaggi di warning a schermo (`dialog.js`, `role="alertdialog"`) descrivono esplicitamente la condizione pericolosa, l'azione da intraprendere e la conseguenza del mancato intervento, in tre campi testuali distinti del template.

#### 5.7.2.2 – Leggibilità
*Sintesi (shall):* I messaggi su etichette/placard di warning devono tenere conto del livello di alfabetizzazione e lettura della popolazione target.

- **IMPL-5.7.2.2-01** `[CSS][JS]` `[Globale]` I testi dei messaggi di warning usano `--fs-body`/`--fs-title` (mai sotto la dimensione minima di progetto) e un livello di linguaggio regolabile tramite `i18n.js` in base al profilo utente.

#### 5.7.2.2.1 – Popolazione target
*Sintesi (shall):* I messaggi devono essere scritti in modo che la popolazione target possa leggerli, comprenderli e rispettarli.

- **IMPL-5.7.2.2.1-01** `[JS]` `[Globale]` `i18n.js` fornisce traduzioni dei messaggi di warning nelle lingue configurate per la popolazione di utenti del sistema (`config.js`, elenco lingue supportate).

#### 5.7.2.2.2 – Stile delle frasi
*Sintesi (shall):* Le frasi devono essere brevi, dirette e in forma attiva per aumentare la leggibilità.

- **IMPL-5.7.2.2.2-01** `[JS]` `[Globale]` I template dei messaggi di warning/errore (`notify.js`, `validate.js`) sono redatti in frasi brevi, dirette e in forma attiva (criterio di revisione editoriale verificabile per ispezione dei testi in `i18n.js`).

#### 5.7.2.2.3 – Popolazioni multilingue o non lettrici
*Sintesi (consider):* Per popolazioni multilingue o non lettrici, considerare icone comunemente comprese, integrate da etichette scritte nelle lingue rilevanti.

- **IMPL-5.7.2.2.3-01** `[HTML][CSS]` `[Globale]` Ogni messaggio di warning/caution è accompagnato da un'icona standardizzata (SVG, `aria-hidden="true"`) oltre al testo tradotto (`i18n.js`), per utenti multilingue (doppia codifica icona+testo, vedi §2.4).

#### 5.7.2.5 – Parola di segnalazione
*Sintesi (shall):* I placard/etichette di warning devono mostrare la parola di segnalazione appropriata (DANGER, WARNING, CAUTION, NOTICE) secondo ANSI Z535.2/Z535.4.

- **IMPL-5.7.2.5-01** `[HTML][CSS]` `[Globale]` I dialoghi/banner di avviso mostrano un'etichetta normalizzata (`DANGER`/`WARNING`/`CAUTION`/`NOTICE`, tradotta da `i18n.js`) con stile coerente ad ANSI Z535 (colore, posizione in alto nel riquadro), gestita da `dialog.js`/`notify.js`.

#### 5.7.2.5.1 – DANGER
*Sintesi (shall):* DANGER va usato quando il mancato evitamento del pericolo comporterà quasi certamente lesioni gravi o morte; riservato ai pericoli più gravi.

- **IMPL-5.7.2.5.1-01** `[CSS][JS]` `[Globale]` L'etichetta "DANGER" è mappata in `config.js` alla massima priorità (`warning`, livello `danger`) con stile `--color-critical` massimamente evidente.

#### 5.7.2.5.2 – WARNING
*Sintesi (shall):* WARNING va usato quando il mancato evitamento del pericolo può causare lesioni gravi o morte.

- **IMPL-5.7.2.5.2-01** `[CSS][JS]` `[Globale]` L'etichetta "WARNING" è usata da `notify.js` per condizioni con rischio di lesioni gravi/morte, con stile `--color-critical` e priorità `warning` in `config.js`.

#### 5.7.2.5.3 – CAUTION
*Sintesi (shall):* CAUTION va usato quando il mancato evitamento del pericolo può causare lesioni minori o moderate.

- **IMPL-5.7.2.5.3-01** `[CSS][JS]` `[Globale]` L'etichetta "CAUTION" è usata per condizioni con rischio di lesioni minori/moderate, mappata a priorità `caution` e stile `--color-warning` (giallo) in `config.js`.

#### 5.7.2.5.4 – NOTICE
*Sintesi (should):* NOTICE dovrebbe essere usato per informazioni importanti non direttamente associate a un pericolo, o per rischio di danno al sistema.

- **IMPL-5.7.2.5.4-01** `[CSS][JS]` `[Globale]` L'etichetta "NOTICE" è usata per informazioni importanti non legate a un pericolo (es. rischio di danno al sistema), mappata a priorità `advisory`/`info` e stile `--color-advisory` in `config.js`.

#### 5.7.3.1 – Indicazione visiva generale
*Sintesi (shall):* I display visivi devono fornire all'utente un'indicazione chiara di warning per condizioni fuori norma dell'apparato/sistema.

- **IMPL-5.7.3.1-01** `[HTML][CSS]` `[Footer]` `#alert-region` e `#alert-summary` forniscono sempre un'indicazione visiva chiara (colore, icona, testo) di ogni condizione fuori norma, gestita da `notify.js`/`state.js`.

#### 5.7.3.2 – Display di warning e caution
*Sintesi (should):* Un display di warning/caution dovrebbe indicare la condizione scatenante, per aumentare la probabilità di rilevamento rispetto alla sola osservazione.

- **IMPL-5.7.3.2-01** `[HTML][JS]` `[Main]` Il banner in `#alert-region` indica esplicitamente la condizione scatenante (variabile/soglia superata) letta da `state.js`.

#### 5.7.3.2.1 – Uso dei segnali visivi di pericolo
*Sintesi (shall):* I segnali visivi di pericolo devono allertare l'utente che esiste una condizione specifica, fornendo informazioni su natura e priorità della condizione.

- **IMPL-5.7.3.2.1-01** `[HTML][JS]` `[Footer]` I segnali visivi di warning/caution in `#alert-region` includono natura (descrizione) e priorità (etichetta/colore) della condizione, generati da `notify.js` per ogni evento in coda.

#### 5.7.3.2.2 – Caratteristiche di visibilità
*Sintesi (shall):* I display di segnale di pericolo devono essere illuminati per essere visibili in tutte le condizioni di illuminazione previste.

- **IMPL-5.7.3.2.2-01** `[CSS]` `[Globale]` I temi `theme-light/dark/high-contrast` (`prefs.js`, `data-theme`) garantiscono rapporto di contrasto ≥ 6:1 (obiettivo ≥ 10:1, §2.4) per i segnali di pericolo in ogni condizione di luminosità ambientale simulata dall'utente.

#### 5.7.3.2.2.1 – Distinzione dall'illuminazione generale
*Sintesi (shall):* I display di segnale di pericolo devono essere visivamente distinti dall'illuminazione generale dell'area.

- **IMPL-5.7.3.2.2.1-01** `[CSS]` `[Globale]` Gli stili di allarme (`--color-critical`, animazione lampeggiante) sono distinti dagli stili neutri della UI (`--color-bg`, `--color-fg`) usati nel resto dell'interfaccia.

#### 5.7.3.2.2.2 – Significato specifico
*Sintesi (shall):* I display di segnale di pericolo devono avere significato specifico nell'area operativa in cui sono usati.

- **IMPL-5.7.3.2.2.2-01** `[JS]` `[Globale]` `config.js` associa in modo univoco ogni colore/icona di allarme a un solo significato operativo nell'intera applicazione, coerente con IMPL-5.3.1.3-01.

#### 5.7.3.4 – Integrazione dei segnali
*Sintesi (shall):* I warning, le cautions e gli advisory visivi devono essere integrati con quelli presentati in altre modalità sensoriali (uditiva, tattile).

- **IMPL-5.7.3.4-01** `[JS]` `[Globale]` `feedback.js` sincronizza segnale visivo (`.is-alerting`), audio (`beep(type)`) ed eventuale aptico (`navigator.vibrate`) per ogni evento di warning/caution/advisory in un'unica chiamata di `notify.js.raise()`.

#### 5.7.3.5 – Codifica di priorità
*Sintesi (shall):* Per stabilire la priorità dei segnali visivi devono essere usate caratteristiche discriminanti (lampeggio, colore, forma, simboli, contrasto, dimensione, posizione).

- **IMPL-5.7.3.5-01** `[CSS]` `[Globale]` Ogni livello di priorità combina almeno due caratteristiche discriminanti tra lampeggio, colore, forma/icona, dimensione e posizione (token `--color-*`, classi `.badge-warning/.badge-caution/.badge-advisory`), definite in `config.js`.

#### 5.7.3.5.1 – Segnali di warning: lampeggio rosso
*Sintesi (shall):* I segnali visivi di warning devono lampeggiare in rosso a 3–5 Hz con duty cycle 50%.

- **IMPL-5.7.3.5.1-01** `[CSS]` `[Globale]` I segnali di warning usano `--color-critical` con `@keyframes blink-warning` a frequenza 3–5 Hz e duty cycle 50% (durata esatta nel custom property `--blink-warning-ms` in `config.js`/CSS).

#### 5.7.3.5.1.1 – Testo lampeggiante leggibile
*Sintesi (should):* Se il testo di warning da leggere lampeggia, dovrebbe alternare intensità mantenendo un'intensità minima leggibile.

- **IMPL-5.7.3.5.1.1-01** `[CSS]` `[Globale]` L'animazione alterna opacità/colore tra 100% e un minimo (`--blink-min-opacity`, default 0.4) che mantiene il testo leggibile, mai opacity 0.

#### 5.7.3.5.1.2 – Sincronizzazione del lampeggio
*Sintesi (shall):* Il tasso di lampeggio per tutti i segnali di warning deve essere sincronizzato.

- **IMPL-5.7.3.5.1.2-01** `[CSS]` `[Globale]` Tutte le istanze dell'animazione `blink-warning` condividono `animation-delay: 0s` e la durata definita dal custom property `--blink-warning-ms` su `:root`.

#### 5.7.3.5.1.3 – Uso con segnali di caution
*Sintesi (shall):* Se usati insieme a segnali di caution, i segnali di warning devono essere codificati per essere facilmente distinti dai segnali di caution.

- **IMPL-5.7.3.5.1.3-01** `[CSS]` `[Globale]` L'animazione/colore `blink-warning`/`--color-critical` sono sempre distinti da `blink-caution`/`--color-warning` (frequenza e colore differenti) quando compaiono insieme in `#alert-region`.

#### 5.7.3.5.2 – Segnali di caution: lampeggio giallo
*Sintesi (shall):* Se le cautions assumono forma di testo lampeggiante, il testo deve lampeggiare a non più di 2 Hz con intervallo ON/OFF di circa 70% acceso, per garantire leggibilità.

- **IMPL-5.7.3.5.2-01** `[CSS]` `[Globale]` Il testo/elemento di caution lampeggiante usa `@keyframes blink-caution` a frequenza ≤ 2 Hz con intervallo ON ≈ 70% (`--blink-caution-ms`, `--blink-caution-on-ratio: 0.7` in `config.js`/CSS).

#### 5.7.3.5.2.1 – Colore giallo
*Sintesi (shall):* I segnali visivi di caution devono essere gialli.

- **IMPL-5.7.3.5.2.1-01** `[CSS]` `[Globale]` Tutti i segnali visivi di caution usano esclusivamente il token `--color-warning` (giallo) come colore di base.

#### 5.7.3.5.2.2 – Identificazione rapida
*Sintesi (shall):* Devono essere impiegate almeno due caratteristiche discriminanti per garantire identificazione rapida e interpretazione dei segnali di caution.

- **IMPL-5.7.3.5.2.2-01** `[CSS][HTML]` `[Globale]` I segnali di caution combinano almeno due caratteristiche discriminanti (colore `--color-warning` + icona `.icon-caution` + lampeggio), coerente con IMPL-5.7.3.5-01.

#### 5.7.3.6 – Altezza del testo
*Sintesi (shall):* Il testo dei segnali di warning/caution deve usare caratteri tra 8,7 e 17,4 milliradianti (30-60 primi d'arco) misurati dalla massima distanza di visualizzazione prevista, con dimensione maggiore in condizioni avverse.

- **IMPL-5.7.3.6-01** `[CSS]` `[Globale]` Il testo dei segnali di warning/caution usa `--fs-title` (dimensione maggiore della UI standard) con minimo garantito da `clamp()`, calibrato in `config.js` sulla distanza di lettura prevista (es. ≥ 1 m).

#### 5.7.3.7 – Raggruppamento in un'unica posizione
*Sintesi (shall):* I segnali di warning e caution e le informazioni per rispondervi devono essere raggruppati in un'unica posizione.

- **IMPL-5.7.3.7-01** `[HTML][CSS]` `[Footer]` Tutti i segnali di warning/caution attivi e le relative informazioni di risposta sono raggruppati in `#alert-region` (area unica, sempre visibile).

#### 5.7.3.7.1 – Raggruppamento separato
*Sintesi (shall):* Quando le informazioni testuali sulle condizioni di warning sono elencate in un'unica posizione, warning e caution devono essere raggruppati separatamente.

- **IMPL-5.7.3.7.1-01** `[HTML][CSS]` `[Footer]` All'interno di `#alert-region`, `notify.js` separa in due sotto-liste distinte (`.alert-list--warning`, `.alert-list--caution`) i messaggi di warning e di caution.

#### 5.7.3.7.2 – Opzioni di ordinamento
*Sintesi (shall):* L'utente deve avere la possibilità di ordinare i messaggi di warning/caution per priorità, ordine cronologico o recency.

- **IMPL-5.7.3.7.2-01** `[HTML][JS]` `[Footer]` Un controllo `<select id="alert-sort">` in `#alert-region` permette di ordinare i messaggi per priorità, ordine cronologico o recency, tramite `notify.js.sortAlerts(mode)`.

#### 5.7.3.8 – Indicatori master
*Sintesi (shall):* Gli indicatori master di warning/caution/advisory devono essere separati dagli indicatori di stato dei sottosistemi, salvo nei display di sola manutenzione.

- **IMPL-5.7.3.8-01** `[HTML][CSS]` `[Header]` `#alert-summary` funge da indicatore master (conteggio/colore aggregato) distinto dagli indicatori di stato dei singoli sottosistemi mostrati in `#content-region`, salvo nelle viste di sola manutenzione.

#### 5.7.3.9 – Warning aggiuntivi
*Sintesi (shall):* I warning aggiuntivi devono essere indicati con codifica ridondante.

- **IMPL-5.7.3.9-01** `[CSS][HTML]` `[Globale]` Le condizioni di warning aggiuntive/concorrenti sono segnalate con codifica ridondante (colore + icona + testo + eventuale lampeggio) in `#alert-region`.

#### 5.7.3.10 – Falsi allarmi e allarmi di disturbo
*Sintesi (should):* Il sistema dovrebbe minimizzare gli effetti di allarmi non rilevati, falsi o di disturbo.

- **IMPL-5.7.3.10-01** `[JS]` `[Globale]` `notify.js` applica isteresi/debounce (soglia e durata minima in `config.js`) prima di innalzare un allarme da una variazione di stato, per ridurre falsi positivi e allarmi di disturbo.

#### 5.17.7.1 – Uso dell'output vocale
*Sintesi (should):* Dove possibile, gli output vocali dovrebbero essere usati in applicazioni previste in concomitanza con un'altra attività che richiede attenzione visiva o risposta a mani libere.

- **IMPL-5.17.7.1-01** `[JS]` `[Globale]` Quando l'applicazione prevede l'uso simultaneo di altre attività visive o mani impegnate, `notify.js` offre in alternativa l'uscita vocale (`SpeechSynthesisUtterance`), attivabile da preferenza utente in `prefs.js`.

#### 5.17.7.1.1 – Conformità al design dei display vocali
*Sintesi (shall):* Il design dei display vocali deve conformarsi ai criteri di 5.3.10.

- **IMPL-5.17.7.1.1-01** `[JS]` `[Globale]` Vedi IMPL-5.3.10.5-01/IMPL-5.3.10.7-01/IMPL-5.3.10.8-01: i display vocali in `notify.js` seguono le stesse regole di priorità, annullamento e ripetizione.

#### 5.17.7.1.3 – Condizioni d'uso del riconoscimento vocale
*Sintesi (should):* Il riconoscimento vocale dovrebbe essere usato quando: a. le conseguenze di errori sono basse; b. identificare/correggere errori è facile; c. l'uso è previsto infrequente; d. esiste un metodo alternativo di input che consenta di disattivare il riconoscimento vocale.

- **IMPL-5.17.7.1.3-01** `[JS]` `[Globale]` (a-d) `config.js` abilita il riconoscimento vocale (Web Speech API) solo per funzioni a basso rischio di errore, facilmente correggibili (`validate.js`), di uso non frequente, e sempre con un metodo alternativo (touch/tastiera, vedi IMPL-5.3.14.2-01).

#### 5.17.7.2.1 – Condizioni d'uso della messaggistica testuale
*Sintesi (shall):* I display visivi di comunicazione (es. chat) devono essere forniti quando: a. il messaggio è complesso/lungo o va richiamato dopo; b. non richiede azione immediata; c. il sistema uditivo del ricevente è sovraccarico; d. l'ambiente è troppo rumoroso; e. il ricevente resta davanti ai display; f. serve comunicazione congiunta multilingue.

- **IMPL-5.17.7.2.1-01** `[HTML][JS]` `[Main]` (a-f) Un pannello chat integrato in `#aux-region` (gestito da `notify.js`) è mostrato quando il messaggio è lungo/complesso, non richiede azione immediata, l'utente è sovraccarico uditivamente, l'ambiente è rumoroso, l'utente resta davanti ai display, o serve comunicazione multilingue (`i18n.js`).

#### 5.17.7.2.2 – Comunicazione multimodale
*Sintesi (shall):* I display visivi di comunicazione non devono essere l'unico mezzo di comunicazione in tempo reale di informazioni critiche in situazioni di carico elevato.

- **IMPL-5.17.7.2.2-01** `[JS]` `[Globale]` In condizioni di carico elevato (`state.workload==='high'`), `notify.js` non usa il solo canale testuale/chat per informazioni critiche, ma lo affianca sempre a un segnale audio/visivo di allarme (vedi IMPL-5.3.2.7-01).

#### 5.17.7.2.3 – Notifiche audio programmabili
*Sintesi (should):* Le notifiche audio dovrebbero essere programmabili e disponibili per casi come arrivo di nuovo messaggio, messaggio da individui specifici o messaggi con parole chiave.

- **IMPL-5.17.7.2.3-01** `[HTML][JS]` `[Footer]` Il pannello preferenze (`prefs.js`) consente di configurare notifiche audio per nuovo messaggio, mittenti specifici e parole chiave (`<input>` liste dedicate), applicate da `notify.js`.

#### 5.17.7.2.4.1 – Salvataggio automatico del testo
*Sintesi (should):* Il testo dei messaggi dovrebbe essere salvato automaticamente per l'archiviazione a lungo termine, con un meccanismo anche per il salvataggio a breve termine durante la sessione.

- **IMPL-5.17.7.2.4.1-01** `[JS]` `[Globale]` `net.js` invia automaticamente il testo dei messaggi al back-end per l'archiviazione a lungo termine; una cache in `localStorage`/sessione mantiene il salvataggio a breve termine in caso di disconnessione.

#### 5.17.7.2.4.2 – Conferma di lettura
*Sintesi (should):* Una conferma che il messaggio è stato visto dovrebbe essere inviata al mittente durante discussioni bidirezionali in tempo reale.

- **IMPL-5.17.7.2.4.2-01** `[JS]` `[Main]` Quando un messaggio in tempo reale viene visualizzato (es. `IntersectionObserver` sul pannello chat aperto), `net.js` invia una conferma di lettura al mittente via WebSocket.

#### 5.17.7.2.4.3 – Efficienza di digitazione
*Sintesi (should):* Funzionalità come voice-to-text, auto-fill, hotkey e messaggi precomposti dovrebbero aumentare l'efficienza della chat.

- **IMPL-5.17.7.2.4.3-01** `[HTML][JS]` `[Main]` Il campo di composizione chat (`<textarea>`) offre voice-to-text (Web Speech API), autocomplete, scorciatoie da tastiera (`nav.js`) e `<select>` di messaggi precomposti.

#### 5.17.7.2.4.4 – Lettura vocale dei messaggi
*Sintesi (should):* Il contenuto del messaggio dovrebbe poter essere letto tramite sistema text-to-speech se l'utente non può vedere la finestra.

- **IMPL-5.17.7.2.4.4-01** `[HTML][JS]` `[Main]` Un pulsante "Ascolta" accanto a ogni messaggio chat richiama `SpeechSynthesisUtterance` per leggerne il contenuto, attivabile da `prefs.js`.

#### 5.17.7.2.4.5 – Integrazione della finestra chat
*Sintesi (should):* Le finestre chat dovrebbero essere integrate nei display primari che richiedono attenzione, per mantenere l'utente concentrato sui display.

- **IMPL-5.17.7.2.4.5-01** `[HTML][CSS]` `[Main]` La finestra di chat è un pannello integrato in `#aux-region` (non una finestra separata), posizionato accanto al `#content-region` principale.

#### 5.17.7.2.4.6 – Lessico standardizzato
*Sintesi (should):* Un lessico standardizzato dovrebbe essere fornito per garantire comprensione uniforme tra staff diversi.

- **IMPL-5.17.7.2.4.6-01** `[JS]` `[Globale]` `i18n.js` fornisce un glossario standardizzato (abbreviazioni, termini operativi) condiviso da tutti i moduli di messaggistica.

#### 5.17.7.2.5 – Uso dei display audio
*Sintesi (may):* I display audio possono essere usati quando: a. il display visivo è limitato da sovraccarico/mobilità ed è utile attirare/avvisare l'utente; b. serve feedback dopo azioni di controllo, inserimento dati o fine di cicli/sequenze.

- **IMPL-5.17.7.2.5-01** `[JS]` `[Globale]` (a,b) `feedback.js` usa segnali audio quando il display visivo è sovraccarico/l'utente ha mobilità limitata, o per dare feedback dopo un'azione di controllo, inserimento dati o fine timer/sequenza (`feedback.js.beep('confirm'|'timeout')`).

#### 5.17.7.2.6 – Funzione supportiva (messaggistica)
*Sintesi (shall):* I segnali audio usati con display visivi devono essere supplementari ai segnali visivi.

- **IMPL-5.17.7.2.6-01** `[JS]` `[Globale]` Vedi IMPL-5.3.9.1-01/IMPL-5.3.2.7-01: stesso principio di supplementarità applicato ai segnali audio della messaggistica.

#### 5.17.7.2.7 – Direzionare l'utente (messaggistica)
*Sintesi (shall):* I segnali audio devono dirigere l'utente verso il display visivo appropriato.

- **IMPL-5.17.7.2.7-01** `[JS][CSS]` `[Globale]` Vedi IMPL-5.3.9.2-01: i segnali audio dirigono l'attenzione verso il display visivo corrispondente (toast, pannello chat in `#aux-region`) tramite evidenziazione sincronizzata.

#### 5.17.7.2.8 – Caratteristiche del segnale (messaggistica)
*Sintesi (may):* I segnali possono essere singoli o intermittenti.

- **IMPL-5.17.7.2.8-01** `[JS]` `[Globale]` Vedi IMPL-5.3.9.3-01: `config.js` definisce modalità `once`/`repeat` anche per i segnali di notifica messaggi.

#### 5.17.7.2.9 – Terminazione dei segnali intermittenti (messaggistica)
*Sintesi (shall):* I segnali intermittenti devono terminare automaticamente o per controllo utente.

- **IMPL-5.17.7.2.9-01** `[JS]` `[Globale]` Vedi IMPL-5.3.9.4-01: terminazione automatica o su controllo utente dei segnali ripetuti di notifica messaggi.

#### 5.17.7.2.10 – Criteri di impostazione allarmi
*Sintesi (shall):* Le impostazioni di allarme devono conformarsi ai criteri di 5.17.7.2.10.1 e .2.

- **IMPL-5.17.7.2.10-01** `[JS]` `[Globale]` Vedi IMPL-5.3.9.5-01/IMPL-5.3.9.6-01 e le due sotto-voci seguenti: `notify.js` applica gli stessi criteri anche alle notifiche di messaggistica.

#### 5.17.7.2.10.1 – Impostazioni personalizzate
*Sintesi (shall):* Quando le impostazioni di allarme sono personalizzate dall'utente, questi deve poter vedere le informazioni di stato sulle impostazioni correnti stabilite come critiche.

- **IMPL-5.17.7.2.10.1-01** `[HTML][JS]` `[Main]` La vista impostazioni allarmi (IMPL-5.3.9.5-01) include anche le soglie/parole chiave personalizzate per le notifiche di messaggistica, in sola lettura per l'utente corrente.

#### 5.17.7.2.10.2 – Stato dell'allarme
*Sintesi (shall):* Le informazioni di stato dell'allarme devono essere fornite dove la responsabilità può passare da un utente a un altro (es. cambio turno).

- **IMPL-5.17.7.2.10.2-01** `[JS]` `[Globale]` Vedi IMPL-5.3.9.6-01: stato delle notifiche/allarmi di messaggistica disponibile in `state.alarms` per il passaggio di consegne.

#### 5.19.1.11.2 – Disattivazione degli avvisi sonori
*Sintesi (shall):* Gli utenti devono poter disattivare o silenziare gli avvisi/segnali sonori rispetto alla vicinanza ad ambienti ostili.

- **IMPL-5.19.1.11.2-01** `[HTML][JS]` `[Footer]` Un controllo rapido "Modalità silenziosa" (`#function-keys`/`#btn-display-settings`) richiama `prefs.js` per disattivare/silenziare tutti gli avvisi sonori non critici con un solo tocco.

#### 5.19.1.11.3 – Stato dell'avviso sonoro
*Sintesi (should):* Il dispositivo dovrebbe fornire un'indicazione dello stato di allerta, come un'icona di modalità silenziosa.

- **IMPL-5.19.1.11.3-01** `[HTML][CSS]` `[Header]` Quando la modalità silenziosa è attiva, un'icona `.icon-silent` compare in `#alert-summary`/`#mode-indicator` (doppia codifica icona+testo "Silenzioso"), aggiornata da `state.js`.

#### 5.19.4.5 – Tipo di allarme configurabile
*Sintesi (shall):* I dispositivi devono avere la capacità di impostare il tipo di allerta (es. audio, visiva, tattile).

- **IMPL-5.19.4.5-01** `[HTML][JS]` `[Footer]` Il pannello preferenze (`prefs.js`) consente di impostare il tipo di allarme per ciascuna categoria (`<select>` con opzioni "audio", "visivo", "aptico" o combinazioni), applicato da `feedback.js` (`navigator.vibrate` per l'aptico).

### 3.9 Finestre, portali e struttura della pagina

Requisiti specifici per portali e portlet web (5.17.8) e per display, titoli e frame, mappati sulle aree `#nav-region`, `#content-region`, `#aux-region` e sui portlet ospitati in `#content-region` (00-head.md §2.2), con il supporto di `nav.js` e `dialog.js`.

#### 5.12.3.4.8 – Posizione nello spazio dei dati
*Sintesi (shall):* Interfacce e ausili di navigazione devono fornire informazioni che consentano all'utente di sapere dove si trova nello spazio dei dati.

- **IMPL-5.12.3.4.8-01** `[HTML][JS]` `[Main]` `nav.js` mantiene e mostra un breadcrumb (`<nav aria-label="breadcrumb">` in `#nav-region`) e un indicatore di pagina (vedi IMPL-5.17.15.5-01) così l'utente sa sempre dove si trova nello spazio di navigazione.

#### 5.17.8.1 – Fonti diverse in un'unica pagina
*Sintesi (shall):* I portali web devono riunire informazioni da fonti diverse in un'unica posizione.

- **IMPL-5.17.8.1-01** `[HTML][CSS]` `[Main]` `#content-region` ospita più portlet (`<section class="portlet">`), ciascuno alimentato da una fonte dati diversa tramite `net.js`, disposti in griglia (`display: grid`) nella stessa pagina.

#### 5.17.8.2 – Area dedicata per portlet
*Sintesi (shall):* Ogni fonte informativa deve avere una propria area dedicata sulla pagina per visualizzare le informazioni in un portlet.

- **IMPL-5.17.8.2-01** `[HTML][CSS]` `[Main]` Ogni fonte informativa ha un proprio `<section class="portlet" id="portlet-<nome>">` con contenitore visivo distinto (bordo, ombra, `--color-bg`) separato dagli altri portlet nella griglia di `#content-region`.

#### 5.17.8.2.1 – Titolo del portlet
*Sintesi (should):* Il titolo di un portlet dovrebbe essere una frase breve e descrittiva che indichi chiaramente l'argomento del portlet (vedi figura 95 della fonte).

- **IMPL-5.17.8.2.1-01** `[HTML]` `[Main]` Ogni portlet ha un'intestazione `<h2 class="portlet-title">` con frase breve e descrittiva del contenuto, nella title bar del portlet.

#### 5.17.8.2.1.1 – Schermate multiple
*Sintesi (should):* Se il portlet presenta più schermate di contenuto, il nome della schermata dovrebbe essere incluso nel titolo del portlet o comparire come intestazione nell'area contenuto.

- **IMPL-5.17.8.2.1.1-01** `[HTML][JS]` `[Main]` Se il portlet gestisce più schermate (stato interno in `nav.js`), il nome della schermata corrente è incluso nel titolo o come `<h3>` nell'area contenuto del portlet.

#### 5.17.8.2.1.2 – Formato titolo-schermata
*Sintesi (should):* Quando il nome schermata fa parte del titolo, il formato dovrebbe essere "Nome Portlet – Nome Schermata".

- **IMPL-5.17.8.2.1.2-01** `[JS]` `[Main]` Quando il nome schermata fa parte del titolo, `nav.js` compone il testo con il formato "Nome Portlet – Nome Schermata" nell'elemento `.portlet-title`.

#### 5.17.8.2.1.3 – Non duplicare il nome schermata
*Sintesi (should):* Quando il nome schermata è incluso nel titolo del portlet, non dovrebbe essere duplicato come intestazione nell'area contenuto.

- **IMPL-5.17.8.2.1.3-01** `[HTML]` `[Main]` Quando il nome schermata è già nel titolo del portlet (IMPL-5.17.8.2.1.2-01), l'area contenuto del portlet non ripete lo stesso testo come intestazione aggiuntiva.

#### 5.17.8.2.2 – Barra del titolo: pulsanti di azione
*Sintesi (shall):* La title bar del portlet deve includere pulsanti d'azione per: a. Detach (finestra separata); b. Edit (modifica aspetto/contenuto); c. Help; d. Maximize; e. Open/close (toggle); f. Properties; g. Refresh; h. Remove; i. Restore down.

- **IMPL-5.17.8.2.2-01** `[HTML][CSS]` `[Main]` (a,d,e,i) La title bar del portlet (`.portlet-header`) include pulsanti icona ≥ `--touch-target-min` per Detach (`window.open()`), Maximize/Restore down (classe `.is-maximized` a tutta larghezza di `#content-region`) e Open/close (toggle `.is-collapsed`, mostra solo la title bar).
- **IMPL-5.17.8.2.2-02** `[HTML][JS]` `[Main]` (b,c,f,g,h) La title bar include inoltre pulsanti per Edit (apre `dialog.js` con form di configurazione), Help (guida contestuale via `nav.js`), Properties (`dialog.js` con versione/autore), Refresh (`net.js.refetch(portletId)`) e Remove (rimozione del nodo DOM del portlet, con conferma `dialog.js`).

#### 5.17.8.3 – Ordine coerente delle azioni
*Sintesi (shall):* I pulsanti d'azione del portlet devono essere presentati nello stesso ordine in ogni portlet di un portale.

- **IMPL-5.17.8.3-01** `[CSS][HTML]` `[Main]` L'ordine dei pulsanti in `.portlet-header` (Detach, Edit, Help, Maximize/Restore, Open/close, Properties, Refresh, Remove) è fisso, definito in un template HTML/CSS comune riutilizzato da ogni portlet.

#### 5.17.8.4 – Numero massimo di portlet
*Sintesi (should):* Una pagina non dovrebbe contenere più di sette portlet.

- **IMPL-5.17.8.4-01** `[JS][CSS]` `[Main]` `state.js`/`nav.js` limita a 7 il numero di portlet visualizzabili contemporaneamente in `#content-region` (valore in `config.js`); oltre tale numero i portlet aggiuntivi sono raggruppati in schede/pagine secondarie.

#### 5.17.8.5 – Altezza verticale massima
*Sintesi (should):* I portlet dovrebbero stare entro tre o meno altezze di schermo in verticale, per minimizzare lo scrolling.

- **IMPL-5.17.8.5-01** `[CSS]` `[Main]` Il layout a griglia di `#content-region` dimensiona i portlet (`grid-auto-rows`, `max-block-size`) in modo che l'insieme stia entro `300dvh`, minimizzando lo scroll verticale.

#### 5.17.8.6 – Supporto a task multi-pagina
*Sintesi (should):* Un portlet che supporta attività in una pagina dovrebbe essere disponibile in più pagine in cui gli utenti potrebbero dover supportare gli stessi task.

- **IMPL-5.17.8.6-01** `[JS]` `[Globale]` `nav.js` registra i portlet riutilizzabili in un registro condiviso (`config.js`) così lo stesso componente `<section class="portlet">` può essere incluso in più pagine/viste che richiedono lo stesso task.

#### 5.17.8.7 – Portlet per ruoli specifici
*Sintesi (should):* I portlet comuni a ruoli specifici dovrebbero essere controllati per privilegio; per utenti senza permesso, il controllo d'accesso deve essere nascosto e non recuperabile.

- **IMPL-5.17.8.7-01** `[JS][HTML]` `[Main]` `auth.js` determina, in base al ruolo utente (`state.user.role`), quali portlet/tab sono renderizzati; i controlli non accessibili sono rimossi dal DOM (non solo nascosti via CSS) per non essere recuperabili.

#### 5.17.8.8 – Portlet a larghezza fissa
*Sintesi (may):* Un portlet può essere a larghezza fissa o adattarsi alla larghezza della colonna; un portlet a larghezza fissa dovrebbe essere usato in layout a colonne fisse.

- **IMPL-5.17.8.8-01** `[CSS]` `[Main]` Un portlet può dichiarare `data-width="fixed"` con `inline-size` fissa in `rem`, da usare in colonne a larghezza fissa della griglia di `#content-region` (classe `.col-fixed`).

#### 5.17.8.9 – Portlet a larghezza variabile
*Sintesi (shall):* I portlet a larghezza variabile devono visualizzare correttamente tutto il contenuto indipendentemente dalla dimensione del portlet.

- **IMPL-5.17.8.9-01** `[CSS]` `[Main]` I portlet senza `data-width="fixed"` usano `inline-size: 100%`/`minmax(0, 1fr)` e layout interno fluido (flex/grid), visualizzando correttamente il contenuto a qualunque larghezza di colonna assegnata (da 800 px in su).

#### 5.17.8.10 – Accesso PKI/SSO
*Sintesi (should):* I portali dovrebbero supportare l'accesso basato su infrastruttura a chiave pubblica (PKI) in modo che l'utente effettui un unico login ed sia autenticato automaticamente su tutti i componenti/applicazioni cui ha accesso.

- **IMPL-5.17.8.10-01** `[JS]` `[Globale]` `auth.js` dovrebbe supportare un flusso di login unico (SSO/PKI, es. certificato client o token) che autentica l'utente per tutti i componenti/applicazioni collegate, senza richiedere nuovo login per ogni portlet.

#### 5.17.8.11 – Pagina pubblica
*Sintesi (shall):* Un portale deve includere una pagina pubblica accessibile prima del login.

- **IMPL-5.17.8.11-01** `[HTML][JS]` `[Main]` `nav.js` definisce una rotta pubblica (`#public-page`) accessibile senza autenticazione (`auth.js`), distinta dalle viste protette raggiungibili solo dopo login.

#### 5.17.8.12 – Contenuto della pagina pubblica
*Sintesi (should):* La pagina pubblica dovrebbe fornire informazioni organizzative, istruzioni per registrarsi/ottenere un account e un accesso diretto o link alla schermata di login.

- **IMPL-5.17.8.12-01** `[HTML]` `[Main]` La pagina pubblica (IMPL-5.17.8.11-01) include informazioni organizzative, istruzioni per richiedere un account/certificato e un pulsante o link "Accedi" verso la schermata di login.

#### 5.17.8.13 – Personalizzazione delle pagine
*Sintesi (should):* Gli utenti dovrebbero poter modificare pagine di portale non vincolate, o aggiungere/eliminare pagine predefinite e personalizzate, se consentito dal proprietario del portale.

- **IMPL-5.17.8.13-01** `[JS]` `[Main]` Quando consentito dal ruolo (`auth.js`), `prefs.js` permette di aggiungere/rimuovere/riordinare portlet in `#content-region`, salvando il layout personalizzato per utente in `localStorage`/back-end (`net.js`).

#### 5.17.8.14 – Aree di contenuto predefinite
*Sintesi (shall):* Le aree di contenuto modificabili devono essere predefinite e selezionabili.

- **IMPL-5.17.8.14-01** `[JS][HTML]` `[Main]` Le aree di contenuto modificabili sono predefinite in `config.js` come elenco selezionabile (non contenuto arbitrario libero), scelto dall'utente tramite un `dialog.js` di selezione portlet.

#### 5.17.15.4 – Messaggi critici in finestre di dialogo
*Sintesi (shall):* I messaggi critici o che richiedono conferma dell'utente (es. errori) devono comparire in proprie finestre di dialogo.

- **IMPL-5.17.15.4-01** `[HTML][JS]` `[Globale]` I messaggi critici o che richiedono conferma sono presentati da `dialog.js` in un `<dialog role="alertdialog">` modale ospitato in `#dialog-host`, mai inline in `#toast-region`/`#message-line`.

#### 5.17.15.5 – Numerazione delle pagine
*Sintesi (should):* Ogni pagina di un display multi-pagina dovrebbe essere etichettata con la pagina corrente e il totale (es. "Pagina 2 di 5").

- **IMPL-5.17.15.5-01** `[HTML][JS]` `[Main]` Per le viste multi-pagina, `nav.js` mostra un indicatore `#page-indicator` con testo "Pagina X di Y" (`i18n.js`) in `#nav-region` o nel footer della vista.

#### 5.17.15.6 – Titoli e intestazioni dei display
*Sintesi (shall):* Ogni display deve essere etichettato con un titolo o intestazione.

- **IMPL-5.17.15.6-01** `[HTML]` `[Header]` Ogni vista/display ha un `<h1 id="app-title">` o `<h2>` di sezione che etichetta la pagina/pannello, aggiornato da `nav.js` al cambio vista.

#### 5.17.15.6.1 – Unicità
*Sintesi (shall):* Il titolo o intestazione deve essere univoco nell'ambito del sistema.

- **IMPL-5.17.15.6.1-01** `[JS]` `[Globale]` `nav.js` verifica, tramite il registro delle rotte (`config.js`), che ogni titolo di vista sia univoco nell'intera applicazione, segnalando in fase di sviluppo eventuali duplicati.

#### 5.17.15.6.2 – Posizione
*Sintesi (should):* Il titolo o intestazione dovrebbe essere posizionato in alto a sinistra o centrato nella pagina.

- **IMPL-5.17.15.6.2-01** `[CSS]` `[Header]` `#app-title` è posizionato in alto a sinistra di `#app-header` o centrato (`justify-self: start|center`), coerente in tutte le viste.

#### 5.17.15.6.3 – Contenuto
*Sintesi (shall):* Il titolo o intestazione deve descrivere brevemente il contenuto o lo scopo della pagina.

- **IMPL-5.17.15.6.3-01** `[JS]` `[Header]` Il testo di `#app-title` descrive brevemente lo scopo della pagina corrente, impostato da `nav.js` ad ogni cambio di vista tramite `i18n.js`.

#### 5.17.15.6.4 – Significatività
*Sintesi (shall):* Il titolo o intestazione deve essere abbastanza significativo da poter essere appreso e ricordato facilmente.

- **IMPL-5.17.15.6.4-01** `[JS]` `[Globale]` I titoli delle viste (`config.js`, registro rotte) sono scelti per essere facilmente memorizzabili, criterio verificabile per ispezione in revisione editoriale.

#### 5.17.15.7 – Uso dei frame
*Sintesi (shall):* I frame devono delimitare la finestra con un bordo.

- **IMPL-5.17.15.7-01** `[CSS]` `[Main]` Ogni pannello (`#nav-region`, `#content-region`, `#aux-region`, portlet) ha un bordo visivo (`border`, token di progetto) che delimita chiaramente l'area del riquadro.

#### 5.17.15.7.1 – Posizione dell'identificazione
*Sintesi (shall):* L'identificazione del frame deve essere mostrata in modo prominente nell'angolo superiore sinistro del frame.

- **IMPL-5.17.15.7.1-01** `[HTML][CSS]` `[Main]` L'identificazione di ciascun riquadro/portlet (titolo, IMPL-5.17.8.2.1-01) è posizionata in modo prominente nell'angolo superiore sinistro del riquadro.

#### 5.17.15.7.2 – Dimensione
*Sintesi (shall):* L'identificazione del frame non deve superare la dimensione del frame quando ridotto alla dimensione minima.

- **IMPL-5.17.15.7.2-01** `[CSS]` `[Main]` Il titolo del riquadro usa `overflow: hidden; text-overflow: ellipsis; max-inline-size: 100%` per non superare mai le dimensioni del riquadro, anche ridotto al minimo.

#### 5.17.15.7.3 – Significatività
*Sintesi (shall):* L'identificazione del frame deve essere abbastanza significativa da essere appresa e ricordata facilmente.

- **IMPL-5.17.15.7.3-01** `[JS]` `[Main]` Vedi IMPL-5.17.15.6.4-01: la stessa regola di significatività si applica ai titoli di riquadro/portlet.

#### 5.17.15.7.4 – Contenuto
*Sintesi (shall):* L'identificazione del frame deve descrivere il contenuto presente nel frame.

- **IMPL-5.17.15.7.4-01** `[JS]` `[Main]` Il testo del titolo di riquadro/portlet descrive il contenuto effettivo del riquadro, impostato dinamicamente da `nav.js`/`i18n.js` in base alla fonte dati collegata.

#### 5.17.15.7.5 – Separazione
*Sintesi (shall):* Almeno una riga vuota deve separare l'identificazione del frame dal corpo del frame.

- **IMPL-5.17.15.7.5-01** `[CSS]` `[Main]` Un margine verticale minimo (`margin-block-end: 1em` o equivalente in `rem`) separa visivamente il titolo del riquadro/portlet dal corpo del contenuto sottostante.

#### 5.17.15.8.1 – Raggruppamento per attività
*Sintesi (shall):* Tutti i display necessari a supportare un'attività o sequenza di attività devono essere raggruppati insieme.

- **IMPL-5.17.15.8.1-01** `[HTML][JS]` `[Main]` `nav.js` raggruppa in una stessa vista/menu (`#nav-region`) tutti i display necessari a supportare una sequenza di attività dell'utente, evitando di disperderli tra pagine non correlate.

#### 5.17.15.8.2 – Frequenza d'uso
*Sintesi (shall):* I display usati più di frequente devono essere raggruppati insieme e collocati nella zona visiva ottimale.

- **IMPL-5.17.15.8.2-01** `[HTML][CSS]` `[Main]` I display/portlet usati più di frequente (dato di utilizzo in `state.js`) sono posizionati nella zona visiva ottimale di `#content-region` (in alto a sinistra in LTR), configurabile tramite `prefs.js`.

#### 5.17.15.8.3 – Importanza
*Sintesi (shall):* I display importanti o critici devono essere collocati nella zona visiva ottimale o altrimenti evidenziati.

- **IMPL-5.17.15.8.3-01** `[CSS][HTML]` `[Main]` I display critici/importanti sono posizionati nella zona visiva ottimale o evidenziati con stile dedicato (bordo, `--color-critical`) rispetto ai portlet secondari.

#### 5.17.15.8.4 – Funzione e sequenza
*Sintesi (shall):* I display devono essere disposti secondo la sequenza d'uso o le relazioni funzionali dei componenti rappresentati, con flusso di visione da sinistra a destra o dall'alto in basso; non si applica agli indicatori master warning/caution/advisory.

- **IMPL-5.17.15.8.4-01** `[CSS][HTML]` `[Main]` Il layout a griglia di `#content-region` dispone i portlet secondo l'ordine d'uso o la relazione funzionale, con flusso di lettura da sinistra a destra e dall'alto in basso (`grid-auto-flow: row`); gli indicatori master in `#alert-region` sono esenti da questo vincolo.

#### 5.17.15.8.5 – Coerenza
*Sintesi (shall):* La disposizione dei display all'interno di un sistema deve essere coerente in linea di principio da un'applicazione all'altra.

- **IMPL-5.17.15.8.5-01** `[JS][CSS]` `[Globale]` Il template di griglia e l'ordine delle aree (`#nav-region`/`#content-region`/`#aux-region`, portlet) sono definiti una volta in `config.js`/CSS condiviso e riutilizzati identicamente in tutte le viste/applicazioni dello stesso sistema.

### 3.10 Contenuto, formato e presentazione delle informazioni

Requisiti su quantità e precisione dell'informazione, abbreviazioni, dati quantitativi, coerenza di formati, raggruppamento, dimensioni dei caratteri, editing di testo, etichette, valori predefiniti, tabelle ed elenchi.

#### 5.17.14.1 – Quantità di informazione
*Sintesi (shall):* La quantità di informazione richiesta deve rispettare i criteri di 5.17.14.1.1-5.17.14.1.5.

- **IMPL-5.17.14.1-01** `[JS]` `[Globale]` state.js deve esporre solo i dati necessari alla vista corrente (view model minimale), applicando i criteri dei sotto-requisiti 5.17.14.1.1-5.17.14.1.5 (vedi voci seguenti).

#### 5.17.14.1.1 – Ambito
*Sintesi (shall):* L'informazione visualizzata deve essere sufficientemente chiara da permettere all'utente di svolgere il compito previsto.

- **IMPL-5.17.14.1.1-01** `[HTML][JS]` `[Main]` Ogni vista in `#content-region` deve mostrare solo campi/controlli necessari al task corrente, determinati da state.js in base a ruolo e modalità (`#mode-indicator`).
- **IMPL-5.17.14.1.1-02** `[JS]` `[Globale]` i18n.js deve fornire testi adeguati al livello di addestramento dell'utente, configurabili per ruolo in config.js.

#### 5.17.14.1.2 – Precisione
*Sintesi (shall):* L'informazione visualizzata deve essere limitata alla precisione richiesta per l'azione o la decisione specifica.

- **IMPL-5.17.14.1.2-01** `[JS]` `[Globale]` Le funzioni di formattazione numerica di i18n.js devono arrotondare ogni valore al numero di decimali definito per campo (`precision` per tipo di dato) in config.js, non alla precisione grezza della sorgente dati.

#### 5.17.14.1.3 – Contesto per i dati visualizzati
*Sintesi (shall):* Ogni display di dati deve fornire informazioni di contesto, inclusi dati di schermate precedenti ove necessario.

- **IMPL-5.17.14.1.3-01** `[HTML][JS]` `[Main]` Le viste in `#content-region` devono includere un'intestazione di contesto (`<h2>`, breadcrumb in `#nav-region`, filtri attivi) che richiami la selezione che ha originato la vista, mantenuta da nav.js/state.js.

#### 5.17.14.1.4 – Combinazione di informazioni operatore e manutentore
*Sintesi (shall):* Le informazioni per operatore e manutentore non devono essere combinate in un'unica vista salvo che contenuto, formato e tempestività soddisfino entrambi gli usi.

- **IMPL-5.17.14.1.4-01** `[JS]` `[Globale]` state.js/auth.js devono esporre viste distinte per ruolo (`operator`, `maintainer`) in config.js; nav.js non deve rendere raggiungibile da un ruolo una vista destinata esclusivamente all'altro, salvo vista combinata esplicitamente approvata.

#### 5.17.14.1.5 – Densità informativa
*Sintesi (shall):* L'informazione critica deve essere sufficientemente separata e distinta da quella non critica da risultare saliente.

- **IMPL-5.17.14.1.5-01** `[CSS]` `[Main]` `#alert-region` e gli elementi `.priority-critical` devono avere separazione visiva (margine ≥ `var(--touch-gap-min)`, bordo, contrasto di sfondo) rispetto ai contenuti non critici di `#content-region`.
- **IMPL-5.17.14.1.5-02** `[HTML][CSS]` `[Globale]` Gli elementi critici devono usare la classe `.priority-critical` con `--color-critical` più icona/testo ridondante (vedi IMPL-5.17.25.11-01), mai il solo posizionamento come indicatore.

#### 5.17.14.2 – Presentazione dell'informazione
*Sintesi (shall):* L'informazione deve essere presentata secondo i criteri 5.17.14.2.1-5.17.14.2.7.

- **IMPL-5.17.14.2-01** `[JS]` `[Globale]` Vedi IMPL-5.17.14.2.1-01 e seguenti per i criteri applicati da i18n.js e dalle viste.

#### 5.17.14.2.1 – Testo semplice
*Sintesi (shall):* L'informazione deve essere visualizzata in un linguaggio appropriato al livello di addestramento dell'utente e specifico per l'applicazione.

- **IMPL-5.17.14.2.1-01** `[JS]` `[Globale]` i18n.js deve fornire chiavi di traduzione specifiche per profilo utente/dominio applicativo (non testo generico), configurabili per ruolo in config.js.

#### 5.17.14.2.2 – Abbreviazioni
*Sintesi (should):* Le abbreviazioni dovrebbero essere conformi a JSSG-2010.

- **IMPL-5.17.14.2.2-01** `[JS]` `[Globale]` i18n.js deve mantenere un dizionario centrale delle abbreviazioni conforme a JSSG-2010 (o standard di progetto equivalente in config.js), riutilizzato da tutte le viste tramite chiavi comuni.

#### 5.17.14.2.3 – Nuove abbreviazioni
*Sintesi (shall):* Nuove abbreviazioni, se necessarie, devono essere sviluppate con regole logiche.

- **IMPL-5.17.14.2.3-01** `[JS]` `[Globale]` Le nuove voci del dizionario i18n.js devono seguire una regola documentata (es. troncamento a N caratteri) definita in config.js, verificabile per ispezione.

#### 5.17.14.2.4 – Distintive
*Sintesi (shall):* Le abbreviazioni devono essere distintive per evitare confusione.

- **IMPL-5.17.14.2.4-01** `[JS]` `[Globale]` Un test automatico su i18n.js deve verificare l'assenza di abbreviazioni duplicate/ambigue nel dizionario (stessa stringa per significati diversi).

#### 5.17.14.2.5 – Coerenti
*Sintesi (shall):* Le parole devono avere una sola abbreviazione coerente.

- **IMPL-5.17.14.2.5-01** `[JS]` `[Globale]` Il dizionario i18n.js deve mappare ogni termine a una sola abbreviazione; un test automatico verifica l'unicità parola→abbreviazione.

#### 5.17.14.2.6 – Punteggiatura
*Sintesi (shall):* Nessuna punteggiatura deve essere usata nelle abbreviazioni.

- **IMPL-5.17.14.2.6-01** `[JS]` `[Globale]` Un test automatico su i18n.js deve rifiutare abbreviazioni contenenti punteggiatura (`.`, `,`) nel dizionario, tramite espressione regolare di verifica.

#### 5.17.14.2.7 – Definizioni
*Sintesi (shall):* Le definizioni di tutte le abbreviazioni, mnemonici e codici devono essere accessibili all'interno del sistema.

- **IMPL-5.17.14.2.7-01** `[HTML][JS]` `[Globale]` Una vista "Glossario" raggiungibile da `#btn-help` deve elencare, tramite i18n.js, ogni abbreviazione con la definizione completa; in UI ogni abbreviazione può usare `<abbr title="...">`.

#### 5.17.14.3 – Informazione quantitativa
*Sintesi (shall):* L'informazione quantitativa deve essere visualizzata secondo le regole 5.17.14.3.1-5.17.14.3.5.

- **IMPL-5.17.14.3-01** `[JS]` `[Globale]` Vedi IMPL-5.17.14.3.1-01 e seguenti.

#### 5.17.14.3.1 – Display numerici digitali
*Sintesi (shall):* I display numerici digitali devono essere usati quando il compito richiede l'identificazione di una quantità numerica specifica.

- **IMPL-5.17.14.3.1-01** `[HTML]` `[Main]` I valori che richiedono identificazione esatta (es. quota, velocità puntuale) devono essere resi con elemento di testo numerico (`<output>`/`<span class="value-numeric">`), non solo in forma grafica.

#### 5.17.14.3.2 – Display a pattern
*Sintesi (shall):* Display analogici o grafici devono essere usati quando il confronto tra quantità è importante.

- **IMPL-5.17.14.3.2-01** `[HTML][CSS]` `[Main]` Per il confronto tra quantità (livelli, trend) deve essere presente un componente grafico/analogico (`<svg>`/`<canvas>`, classe `.gauge`/`.bar-chart`) accanto o al posto del solo valore numerico.

#### 5.17.14.3.3 – Confronto di quantità
*Sintesi (shall):* I display numerici digitali non devono essere l'unico mezzo di visualizzazione quando il confronto tra quantità è importante per un'interpretazione accurata.

- **IMPL-5.17.14.3.3-01** `[JS]` `[Main]` Le viste devono accoppiare ogni valore numerico "da confrontare" a un componente grafico (vedi IMPL-5.17.14.3.2-01); un criterio di revisione verifica l'assenza di soli numeri dove il confronto è richiesto.

#### 5.17.14.3.4 – Display ibridi
*Sintesi (shall):* Devono essere forniti display ibridi, che combinano visualizzazione digitale e analogica/grafica, quando un compito richiede sia l'identificazione di una quantità specifica sia la capacità di confrontare quantità.

- **IMPL-5.17.14.3.4-01** `[HTML][CSS]` `[Main]` Il componente `.gauge` deve includere sia la rappresentazione grafica (arco/barra) sia il valore numerico testuale nello stesso elemento (es. `<figure class="gauge"><svg>…</svg><span class="value-numeric">…</span></figure>`).

#### 5.17.14.3.5 – Unità
*Sintesi (shall):* I display di informazione quantitativa devono includere le unità di misura.

- **IMPL-5.17.14.3.5-01** `[HTML][JS]` `[Globale]` Ogni valore numerico in UI deve essere accompagnato dall'unità di misura localizzata da i18n.js (es. `<span class="value-numeric">120</span><span class="unit">km/h</span>`), mai il solo numero.

#### 5.17.15.1 – Dati critici
*Sintesi (shall):* I dati critici non devono essere nascosti da impaginazione o scorrimento.

- **IMPL-5.17.15.1-01** `[CSS]` `[Main]` `#alert-region` e gli elementi `.priority-critical` devono restare visibili (`position: sticky` o posizionamento fuori dal flusso scorrevole), mai coperti da paginazione o scroll interno di `#content-region`.

#### 5.17.15.2 – Forma direttamente utilizzabile
*Sintesi (shall):* L'informazione deve essere presentata in forma direttamente utilizzabile, senza richiedere all'utente di trasporre, calcolare, interpolare o tradurre mentalmente in altre unità, basi numeriche o lingue.

- **IMPL-5.17.15.2-01** `[JS]` `[Globale]` i18n.js deve convertire automaticamente unità, formati numerici e lingua secondo le preferenze utente (prefs.js); nessuna vista deve richiedere calcoli manuali di conversione.

#### 5.17.15.3.1 – Coerenza dei display in output
*Sintesi (shall):* Il contenuto dei display e i loro formati devono essere coerenti all'interno del sistema e tra sistemi usati dagli stessi utenti.

- **IMPL-5.17.15.3.1-01** `[CSS][JS]` `[Globale]` Un set comune di componenti/stili (token §2.4, config.js) deve essere riutilizzato in tutte le viste; un criterio di revisione verifica l'assenza di formati ad hoc per lo stesso tipo di dato.

#### 5.17.15.3.2 – Coerenza tra output e input
*Sintesi (shall):* Lo stesso formato deve essere usato per l'inserimento e la visualizzazione dei dati in output nello stesso compito (es. una data inserita come "mm/dd/yyyy" viene mostrata nella stessa forma).

- **IMPL-5.17.15.3.2-01** `[JS]` `[Globale]` i18n.js/validate.js devono usare lo stesso formato (`format`/`parse`) per i campi di input e per la visualizzazione dello stesso dato.

#### 5.17.15.3.3 – Etichette di campo dati ricorrenti
*Sintesi (shall):* I campi dati ricorrenti nel sistema e tra sistemi usati dagli stessi utenti devono avere etichette coerenti.

- **IMPL-5.17.15.3.3-01** `[JS]` `[Globale]` Le etichette dei campi ricorrenti devono provenire da una singola chiave i18n.js condivisa, mai da stringhe duplicate per vista.

#### 5.17.15.3.4 – Campi dati comparativi
*Sintesi (shall):* I campi dati da confrontare carattere per carattere devono essere posizionati uno sopra l'altro con allineamento dei caratteri da confrontare.

- **IMPL-5.17.15.3.4-01** `[CSS]` `[Main]` I campi da confrontare carattere per carattere devono essere disposti in colonna (`display: grid` con colonne allineate) con font monospazio o `font-variant-numeric: tabular-nums`.

#### 5.17.15.3.5 – Posizione dei campi dati ricorrenti
*Sintesi (shall):* I campi dati ricorrenti nel sistema e tra sistemi usati dagli stessi utenti devono occupare posizioni relative coerenti tra i display.

- **IMPL-5.17.15.3.5-01** `[CSS]` `[Globale]` I template di vista (griglia CSS in `#content-region`) devono riusare le stesse aree/posizioni per campi ricorrenti (es. id applicativo sempre in alto a sinistra), tramite classi di layout comuni.

#### 5.17.15.3.6 – Coerenza dei formati di inserimento dati
*Sintesi (should):* Quando i dati devono essere inseriti manualmente, ordine e formato dovrebbero essere il più coerenti possibile.

- **IMPL-5.17.15.3.6-01** `[HTML][JS]` `[Globale]` validate.js deve applicare lo stesso pattern di formato (`pattern`, maschera di input) a tutti i campi dello stesso tipo di dato in tutte le viste.

#### 5.17.17 – Raggruppamento in un display
*Sintesi (shall):* L'informazione deve essere ordinata secondo uno scopo o contesto specifico; quando non suggerisce un metodo, può essere raggruppata secondo le linee guida 5.17.17.1-5.17.17.4 in ordine di preferenza.

- **IMPL-5.17.17-01** `[JS]` `[Main]` Le viste devono definire esplicitamente un criterio di ordinamento/raggruppamento (configurabile in config.js: `cronologico|importanza|funzionale|frequenza`), applicato da state.js prima del render.

#### 5.17.17.1 – Ordine e sequenze
*Sintesi (should):* Quando i campi dati hanno un ordine naturale (cronologico o sequenziale), tale ordine dovrebbe riflettersi nell'organizzazione del formato dei campi.

- **IMPL-5.17.17.1-01** `[JS]` `[Main]` Le liste/tabelle con dati a ordine naturale (es. log eventi) devono essere ordinate di default cronologicamente da state.js, salvo ordinamento esplicito dell'utente.

#### 5.17.17.2 – Raggruppati per importanza
*Sintesi (may):* Gli elementi dati critici o che richiedono risposta immediata possono essere ordinati in cima all'elenco.

- **IMPL-5.17.17.2-01** `[JS][CSS]` `[Main]` notify.js deve ordinare `#alert-region` per priorità decrescente (critico in cima), applicando le classi `.priority-critical`/`.priority-alarm`/....

#### 5.17.17.3 – Ordinati per funzione
*Sintesi (may):* Gli insiemi di dati associati a domande specifiche o a funzioni particolari possono essere raggruppati insieme per segnalarne le relazioni funzionali.

- **IMPL-5.17.17.3-01** `[HTML][CSS]` `[Main]` I controlli/dati relativi alla stessa funzione devono essere racchiusi in un contenitore semantico comune (`<fieldset>`/`<section>` con `aria-labelledby`), distinto visivamente dagli altri gruppi.

#### 5.17.17.4 – Ordinati per frequenza
*Sintesi (may):* Gli elementi dati usati più frequentemente possono essere ordinati in cima all'elenco.

- **IMPL-5.17.17.4-01** `[JS]` `[Main]` prefs.js/state.js possono tracciare la frequenza d'uso delle voci (menu, `#function-keys`) e riordinarle di conseguenza, come opzione configurabile.

#### 5.17.18.2 – Dimensioni di caratteri alfanumerici e simboli
*Sintesi (shall/should):* Misurata dalla massima distanza di visione prevista, l'altezza di caratteri e simboli pittorici deve sottendere non meno di 2,9 milliradianti (10') e dovrebbe sottendere non meno di 4,5 milliradianti (15') di angolo visivo.

- **IMPL-5.17.18.2-01** `[CSS]` `[Globale]` `--fs-body` deve garantire, alla distanza di progetto (`--viewing-distance-mm` in config.js), un'altezza carattere derivata da ≥2,9 mrad (`h = 2 · d · tan(angolo/2)`); il default rispetta ≥16px a 800×600 (§2.3).
- **IMPL-5.17.18.2-02** `[CSS]` `[Globale]` I simboli pittorici (icone) devono avere `min-inline-size`/`min-block-size` coerenti con `--fs-title` o superiore, non inferiori al testo corrispondente.

#### 5.17.18.4 – Spessore del tratto dei caratteri
*Sintesi (shall):* Assumendo l'altezza carattere conforme a 5.17.18.2, lo spessore del tratto deve essere non meno di 0,0834 né più di 0,1667 del numero di pixel dell'altezza carattere.

- **IMPL-5.17.18.4-01** `[CSS]` `[Globale]` Il font di `--font-family-ui` deve avere un peso (font-weight) che produca uno spessore di tratto nell'intervallo 8,34%-16,67% dell'altezza carattere, verificabile per ispezione sul font scelto (es. Arial regular/medium).

#### 5.17.18.5 – Larghezza dei caratteri
*Sintesi (shall):* La larghezza dei caratteri deve essere approssimativamente 0,9 dell'altezza.

- **IMPL-5.17.18.5-01** `[CSS]` `[Globale]` Il font di `--font-family-ui` deve avere un rapporto larghezza/altezza ≈0,9 per i caratteri maiuscoli (criterio di selezione del font, verificabile per ispezione).

#### 5.17.18.6 – Dimensione dei simboli e qualità immagine per forme complesse
*Sintesi (shall):* La dimensione di un simbolo/grafico deve essere tale che testo o grafica incorporati (es. etichetta nel simbolo) sottendano non meno di 2,9 milliradianti dalla massima distanza di visione prevista.

- **IMPL-5.17.18.6-01** `[CSS]` `[Main]` Il testo incorporato in icone/simboli SVG complessi deve rispettare la stessa soglia minima di IMPL-5.17.18.2-01 (nessun testo-in-simbolo sotto `--fs-small`).

#### 5.17.18.7 – Caratteristiche del font
*Sintesi (shall):* Lo stile del font deve consentire la discriminazione di caratteri simili (es. lettera l/numero 1, lettera Z/numero 2).

- **IMPL-5.17.18.7-01** `[CSS]` `[Globale]` `--font-family-ui` deve usare un font che discrimini chiaramente l/1, O/0, Z/2 (criterio di selezione; verificabile per ispezione dei glifi).

#### 5.17.18.7.1 – Font standard
*Sintesi (shall):* Deve essere usato un font standard comune (es. Arial, Times New Roman, Courier, Verdana).

- **IMPL-5.17.18.7.1-01** `[CSS]` `[Globale]` `--font-family-ui: Arial, Verdana, Helvetica, sans-serif` (§2.4) deve essere l'unico font di interfaccia, senza font decorativi/custom non conformi.

#### 5.17.18.7.2 – Sans-serif
*Sintesi (shall):* Dove gli utenti devono leggere in condizioni avverse (es. scarsa illuminazione), deve essere usato uno stile sans-serif (es. Arial, Verdana, Helvetica).

- **IMPL-5.17.18.7.2-01** `[CSS]` `[Globale]` Vedi IMPL-5.17.18.7.1-01: `--font-family-ui` è già sans-serif per tutte le condizioni d'uso, incluso `theme-dark`/luce ambientale scarsa gestito da prefs.js.

#### 5.17.18.10.1 – Organizzazione alfanumerica
*Sintesi (shall):* Quando sono presentati cinque o più caratteri alfanumerici, devono essere separati in gruppi secondo l'organizzazione naturale e gli schemi di punteggiatura (es. "mm/dd/yyyy", "(123) 555-6789", "123-45-6789").

- **IMPL-5.17.18.10.1-01** `[JS]` `[Globale]` i18n.js deve formattare automaticamente date, telefoni, codici ecc. secondo il pattern naturale del tipo di dato (maschera in config.js), applicato sia in visualizzazione sia in validate.js per l'input.

#### 5.17.18.10.2 – Combinazione di lettere e cifre
*Sintesi (shall):* Quando un codice consiste sia di lettere che di cifre, i tipi di carattere comuni devono essere raggruppati insieme.

- **IMPL-5.17.18.10.2-01** `[JS]` `[Globale]` Le maschere di formattazione in config.js per codici misti devono raggruppare lettere e cifre in blocchi omogenei (es. `AAA-9999`), non alternati senza criterio.

#### 5.17.18.10.3 – Blocking
*Sintesi (shall):* Quando sono visualizzati cinque o più caratteri alfanumerici senza organizzazione naturale, devono essere raggruppati in blocchi di tre-cinque caratteri.

- **IMPL-5.17.18.10.3-01** `[JS]` `[Globale]` Per codici privi di formato naturale, i18n.js deve inserire separatori ogni 3-5 caratteri secondo la maschera definita in config.js.

#### 5.17.18.10.4 – Separazione dei gruppi
*Sintesi (shall):* Quando sono visualizzati cinque o più caratteri alfanumerici senza organizzazione naturale, i gruppi devono essere separati tra loro da almeno uno spazio vuoto.

- **IMPL-5.17.18.10.4-01** `[JS]` `[Globale]` La maschera di formattazione (vedi IMPL-5.17.18.10.3-01) deve usare spazio, trattino o `/` come separatore minimo tra i gruppi, mai gruppi adiacenti senza separatore.

#### 5.17.18.10.5 – Campi numerici lunghi
*Sintesi (shall):* Quando sono visualizzati cinque o più caratteri numerici senza organizzazione naturale, i campi numerici lunghi devono essere punteggiati con virgole, spazi o barre.

- **IMPL-5.17.18.10.5-01** `[JS]` `[Globale]` i18n.js deve formattare i numeri ≥5 cifre con separatore delle migliaia localizzato (`Intl.NumberFormat`), mai come stringa di cifre non punteggiata.

#### 5.17.18.10.6 – Zeri iniziali
*Sintesi (shall):* Gli zeri iniziali non devono essere usati nei dati numerici.

- **IMPL-5.17.18.10.6-01** `[JS]` `[Globale]` Le funzioni di formattazione numerica di i18n.js non devono produrre zeri non significativi iniziali, salvo formato a larghezza fissa esplicitamente richiesto (es. codici/orari) dichiarato in config.js.

#### 5.17.19 – Editing di testo e programma
*Sintesi (shall):* L'editing di testo e programma deve rispettare i criteri 5.17.19.1-5.17.19.12.

- **IMPL-5.17.19-01** `[JS]` `[Globale]` Vedi IMPL-5.17.19.1-01 e seguenti; le funzioni di editing avanzato si applicano solo alle viste con campi di testo estesi (note, log, configurazione), non ai form semplici.

#### 5.17.19.1 – Buffer
*Sintesi (shall):* Quando appropriato, nell'inserimento di caratteri/parole/frasi (editing), gli elementi da inserire devono essere raccolti in un'area buffer e visualizzati nell'area di inserimento prescritta per il successivo inserimento su comando utente.

- **IMPL-5.17.19.1-01** `[JS]` `[Main]` Per i campi di testo estesi con funzione "inserisci", l'editor deve mantenere un buffer visibile (`.insert-buffer`) prima della conferma di inserimento tramite comando esplicito.

#### 5.17.19.2 – Comandi di editing
*Sintesi (shall):* Devono essere forniti comandi di editing (MOVE, COPY, PASTE, INSERT, DELETE) per aggiungere, inserire o eliminare testo e segmenti di programma.

- **IMPL-5.17.19.2-01** `[HTML][JS]` `[Main]` Gli editor di testo estesi devono esporre controlli touch per Sposta/Copia/Incolla/Inserisci/Elimina (barra comandi dedicata, `min-block-size: var(--touch-target-min)`), oltre alle scorciatoie da tastiera standard.

#### 5.17.19.2.1 – Comandi di editing testo
*Sintesi (shall):* Nell'editing di testo, i comandi devono basarsi su carattere, parola, frase, paragrafo e segmenti di ordine superiore.

- **IMPL-5.17.19.2.1-01** `[JS]` `[Main]` I comandi di selezione nell'editor devono operare per granularità (carattere/parola/frase/paragrafo): doppio tap = parola, long-press = paragrafo.

#### 5.17.19.2.5 – Controlli di tabulazione
*Sintesi (shall):* Per l'editing di programmi o dati tabulari, devono essere forniti controlli di tabulazione del cursore o altre modalità per spostarsi agevolmente da campo a campo.

- **IMPL-5.17.19.2.5-01** `[HTML][JS]` `[Main]` I form e le tabelle editabili devono supportare la navigazione tra campi con Tab/Invio e pulsanti touch "campo successivo/precedente", gestiti da nav.js.

#### 5.17.19.2.6 – Inserimento dei comandi di editing da tastiera
*Sintesi (shall):* Dove i comandi di editing sono immessi digitando nel display, devono essere facilmente distinguibili dal materiale testuale visualizzato.

- **IMPL-5.17.19.2.6-01** `[CSS]` `[Main]` I comandi di editing testuale inseriti come sintassi speciale (se presenti) devono avere stile distinto (classe `.edit-command`: colore/monospace) rispetto al testo normale.

#### 5.17.19.3 – Testo evidenziato
*Sintesi (shall):* Dove il testo è oggetto di comandi di controllo (sottolineatura, grassetto, spostamento, copia, eliminazione), il segmento interessato deve essere evidenziato per indicarne i confini.

- **IMPL-5.17.19.3-01** `[CSS]` `[Main]` La selezione di testo nell'editor deve usare evidenziazione visibile (`::selection` con `background: var(--color-selected)`) che delimiti chiaramente i confini del segmento selezionato.

#### 5.17.19.4 – Ricerca di stringhe
*Sintesi (shall):* Deve essere fornita una capacità di ricerca di stringhe che consenta di specificare una stringa di testo e richiedere l'avanzamento (o arretramento) automatico del cursore all'occorrenza successiva (o precedente).

- **IMPL-5.17.19.4-01** `[HTML][JS]` `[Main]` Gli editor/visualizzatori di testo esteso devono fornire un campo di ricerca (`<input type="search">`) con pulsanti "successivo/precedente" che spostano focus/cursore all'occorrenza trovata.

#### 5.17.19.5 – Variabilità della ricerca di stringhe
*Sintesi (shall):* La capacità di ricerca deve offrire flessibilità nella scelta della variante di ricerca.

- **IMPL-5.17.19.5-01** `[HTML][JS]` `[Main]` Il campo di ricerca (vedi IMPL-5.17.19.4-01) deve offrire opzioni "maiuscole/minuscole", "parola intera", "espressione regolare" tramite toggle accanto all'input.

#### 5.17.19.6 – A capo automatico
*Sintesi (shall):* Deve essere fornito un a capo automatico quando il testo raggiunge il margine destro per l'inserimento o l'editing di testo non formattato.

- **IMPL-5.17.19.6-01** `[CSS]` `[Main]` I campi di testo multi-riga non formattato devono avere `white-space: pre-wrap; overflow-wrap: break-word` per l'a capo automatico al margine.

#### 5.17.19.7 – Esclusione dell'a capo automatico
*Sintesi (shall):* Deve essere fornita la possibilità per l'utente di disattivare l'a capo automatico.

- **IMPL-5.17.19.7-01** `[HTML][JS]` `[Main]` Gli editor di testo esteso devono offrire un'opzione (`#opt-wordwrap`) per disattivare l'a capo automatico, salvata da prefs.js.

#### 5.17.19.8 – Controllo del formato
*Sintesi (shall):* Deve essere fornito un mezzo per specificare le caratteristiche di controllo del formato richieste durante l'inserimento o l'editing di testo (margini, tabulazioni, interlinea).

- **IMPL-5.17.19.8-01** `[HTML][JS]` `[Main]` Gli editor di testo formattato devono esporre un pannello impostazioni (margini, tabulazioni, interlinea) in `#aux-region`, applicato al documento corrente.

#### 5.17.19.9 – Formati predefiniti
*Sintesi (shall):* Quando i formati di testo devono seguire standard predefiniti, il formato richiesto deve essere fornito automaticamente.

- **IMPL-5.17.19.9-01** `[JS]` `[Main]` Quando un campo/documento ha un formato standard obbligatorio (es. referto), validate.js/l'editor devono applicarlo automaticamente alla creazione, senza impostazione manuale.

#### 5.17.19.10 – Formati di testo come opzione utente
*Sintesi (shall):* Dove i formati di testo sono un'opzione dell'utente, deve essere fornito un mezzo comodo per specificare e memorizzare i formati generati per applicazioni particolari, per uso futuro.

- **IMPL-5.17.19.10-01** `[JS]` `[Main]` prefs.js deve permettere di salvare un insieme di impostazioni di formato testo come preset nominato, richiamabile in editing successivi.

#### 5.17.19.11 – Testo usato di frequente
*Sintesi (shall):* Deve essere fornita la capacità di etichettare e memorizzare segmenti di testo usati di frequente (firme, nomi organizzazione, nominativi, coordinate) e richiamarli in base all'etichetta assegnata.

- **IMPL-5.17.19.11-01** `[HTML][JS]` `[Main]` Deve esistere una libreria di frammenti di testo (snippet) etichettati, gestita da prefs.js/state.js, inseribile nel punto del cursore tramite menu/comando dedicato.

#### 5.17.19.12 – Inizio e fine del file
*Sintesi (shall):* Deve essere fornito un mezzo per spostare rapidamente il cursore all'inizio o alla fine del file.

- **IMPL-5.17.19.12-01** `[HTML][JS]` `[Main]` Gli editor di testo esteso devono fornire comandi/scorciatoie (Ctrl+Home/End, pulsanti touch `#btn-doc-start`/`#btn-doc-end`) per posizionare il cursore a inizio/fine documento.

#### 5.17.20.1 – Etichette
*Sintesi (shall):* Ogni elemento di un display deve essere etichettato, a meno che il suo scopo non sia intuitivamente ovvio per la popolazione di utenti prevista.

- **IMPL-5.17.20.1-01** `[HTML]` `[Globale]` Ogni controllo/campo interattivo deve avere etichetta visibile (`<label>`) o, in alternativa motivata, `aria-label`/`aria-labelledby`; un criterio di revisione verifica l'assenza di controlli privi di etichetta accessibile.

#### 5.17.20.1.1 – Uso
*Sintesi (should):* L'identificazione dell'elemento su richiesta dell'utente (es. tooltip) è accettabile dove i vincoli di spazio precludono l'uso di etichette.

- **IMPL-5.17.20.1.1-01** `[HTML][JS]` `[Globale]` Dove lo spazio non consente etichette visibili, i controlli devono fornire tooltip (`title`/popover accessibile) attivabile anche in touch (long-press o `#btn-help` contestuale), non solo `:hover`.

#### 5.17.20.1.2 – Caratteristiche delle etichette
*Sintesi (should):* Le etichette dovrebbero: a) relazionarsi univocamente all'elemento descritto; b) essere coerenti con le altre etichette; c) essere distinguibili e più salienti degli altri elementi; d) usare una tecnica di accentuazione diversa da quella per messaggi di emergenza/critici.

- **IMPL-5.17.20.1.2-01** `[HTML]` `[Globale]` Ogni `<label>` deve usare `for`/associazione implicita univoca con il relativo controllo (criterio a).
- **IMPL-5.17.20.1.2-02** `[CSS]` `[Globale]` Le etichette devono condividere uno stile comune (`.field-label`: font, peso, `--color-fg`) in tutta l'applicazione (criterio b) e risultare visivamente distinte dal valore del campo (criterio c).
- **IMPL-5.17.20.1.2-03** `[CSS]` `[Globale]` La tecnica di evidenziazione delle etichette deve differire da quella riservata a messaggi critici/di emergenza (`--color-critical` + icona), mai il solo maiuscolo per entrambi gli usi (criterio d).

#### 5.17.20.2 – Valori predefiniti
*Sintesi (shall):* I valori predefiniti devono rispettare i requisiti 5.17.20.2.1-5.17.20.2.6.

- **IMPL-5.17.20.2-01** `[JS]` `[Globale]` Vedi IMPL-5.17.20.2.1-01 e seguenti.

#### 5.17.20.2.1 – Uso quando fattibile
*Sintesi (shall):* Per ridurre il carico di lavoro dell'utente, i valori predefiniti devono essere usati dove fattibile.

- **IMPL-5.17.20.2.1-01** `[JS]` `[Main]` validate.js/state.js devono precompilare i campi con un valore predefinito (da config.js o dall'ultima transazione) ovunque esista un default ragionevole.

#### 5.17.20.2.2 – Visualizzazione automatica
*Sintesi (shall):* I valori predefiniti attualmente definiti devono essere visualizzati automaticamente nei campi appropriati all'avvio di una transazione di inserimento dati.

- **IMPL-5.17.20.2.2-01** `[HTML][JS]` `[Main]` All'apertura di un form, i campi con default devono mostrarlo già valorizzato (`value`/`defaultValue` da state.js), non vuoto con solo placeholder.

#### 5.17.20.2.3 – Accettazione
*Sintesi (shall):* L'utente deve poter indicare l'accettazione del valore predefinito con una singola pressione di tasto.

- **IMPL-5.17.20.2.3-01** `[JS]` `[Main]` Un singolo tap su "Conferma"/Invio sul campo con default deve accettarlo senza ulteriori azioni, gestito da validate.js.

#### 5.17.20.2.4 – Sostituzione del valore predefinito
*Sintesi (shall):* L'utente deve poter sostituire qualsiasi valore predefinito durante una data transazione senza modificare la definizione del default.

- **IMPL-5.17.20.2.4-01** `[JS]` `[Main]` La modifica di un campo precompilato deve aggiornare solo l'istanza corrente (stato della transazione in state.js), non il default in config.js.

#### 5.17.20.2.5 – Generazione di valori predefiniti
*Sintesi (shall):* L'utente deve avere la possibilità di generare valori predefiniti se il sistema non li ha forniti.

- **IMPL-5.17.20.2.5-01** `[HTML][JS]` `[Main]` Dove il sistema non fornisce un default, deve essere disponibile un comando "Imposta come predefinito" sul valore corrente, salvato tramite prefs.js/config.js.

#### 5.17.20.2.6 – Opzioni di default
*Sintesi (shall):* Dove è stata definita una serie di valori predefiniti per una sequenza di inserimento dati, l'utente deve poter accettare tali valori predefiniti.

- **IMPL-5.17.20.2.6-01** `[HTML][JS]` `[Main]` I form a più campi con default devono offrire un comando unico "Accetta tutti i predefiniti" che conferma l'intera sequenza in un'unica azione.

#### 5.17.20.3 – Dati tabulari
*Sintesi (shall):* I dati tabulari devono rispettare i requisiti 5.17.20.3.1-5.17.20.3.14.

- **IMPL-5.17.20.3-01** `[HTML]` `[Main]` Le tabelle devono usare marcatura semantica `<table>`/`<thead>`/`<tbody>`/`<th scope="col|row">`; vedi IMPL-5.17.20.3.1-01 e seguenti per i criteri di dettaglio.

#### 5.17.20.3.1 – Uso
*Sintesi (shall):* Le visualizzazioni tabulari devono essere usate per presentare dati riga-colonna a supporto del confronto dettagliato di insiemi di dati ordinati.

- **IMPL-5.17.20.3.1-01** `[HTML]` `[Main]` I dati che richiedono confronto riga-colonna devono essere resi con `<table>`, non con liste o griglie di card prive di allineamento colonnare.

#### 5.17.20.3.2 – Formati standard
*Sintesi (shall):* La posizione dei dati ricorrenti deve essere coerente tra tutte le visualizzazioni tabulari e comune in tutto il sistema.

- **IMPL-5.17.20.3.2-01** `[CSS]` `[Globale]` Un componente tabella comune (`.data-table`) con ordine colonne standard per tipo di dato deve essere riusato da tutte le viste tabellari, non implementazioni ad hoc.

#### 5.17.20.3.3 – Disposizione
*Sintesi (should):* Quando i dati tabulari sono ordinati entro le righe, dovrebbero essere in ordine crescente da sinistra a destra; i dati ordinati entro le colonne dovrebbero seguire un ordine dettato dal contesto.

- **IMPL-5.17.20.3.3-01** `[JS]` `[Main]` L'ordinamento di default applicato da state.js alle righe di `.data-table` deve essere crescente da sinistra a destra entro riga, salvo criterio di contesto per colonna (es. cronologico), configurabile per tabella.

#### 5.17.20.3.4 – Titoli
*Sintesi (shall):* Quando i dati tabulari sono divisi in classificazioni, i titoli di classificazione devono essere visualizzati.

- **IMPL-5.17.20.3.4-01** `[HTML]` `[Main]` Le tabelle con gruppi di righe classificati devono usare `<caption>`/righe di intestazione di gruppo (`<tr class="group-header">` con `colspan`) per mostrare il titolo della classificazione.

#### 5.17.20.3.5 – Estensione verticale – titoli
*Sintesi (shall):* Quando i dati tabulari si estendono su più di una pagina verticalmente, le colonne devono essere titolate identicamente in ogni pagina.

- **IMPL-5.17.20.3.5-01** `[CSS][JS]` `[Main]` Per tabelle paginate/scorrevoli, `<thead>` deve restare `position: sticky` in cima a `#content-region` o essere ripetuto identico su ogni pagina gestita da state.js.

#### 5.17.20.3.6 – Estensione orizzontale
*Sintesi (should):* Le visualizzazioni tabulari non dovrebbero estendersi su più di una pagina orizzontalmente.

- **IMPL-5.17.20.3.6-01** `[CSS]` `[Main]` `.data-table` deve adattare le colonne (colonne secondarie nascondibili, layout responsive) per restare entro la larghezza di `#content-region` a 800px, senza scroll orizzontale della pagina (§2.3).

#### 5.17.20.3.7 – Estensione orizzontale – titoli
*Sintesi (shall):* Se è richiesta l'estensione su più di una pagina orizzontalmente, le righe devono essere titolate identicamente in ogni pagina.

- **IMPL-5.17.20.3.7-01** `[CSS]` `[Main]` Se `.data-table` usa uno scroll orizzontale interno, la prima colonna (etichette di riga) deve restare `position: sticky` visibile su ogni scroll.

#### 5.17.20.3.8 – Etichette distintive e informative
*Sintesi (shall):* Righe e colonne devono essere etichettate in modo distintivo per guidare l'inserimento dati.

- **IMPL-5.17.20.3.8-01** `[HTML]` `[Main]` Ogni `<th>` di riga/colonna editabile deve avere testo univoco e descrittivo, associato ai relativi `<td>` tramite `scope`/`headers`.

#### 5.17.20.3.9 – Disposizione dell'inserimento numerico
*Sintesi (shall):* Gli utenti devono poter inserire valori numerici nelle tabelle senza preoccuparsi della giustificazione; il computer deve giustificare a destra gli interi o rispetto al punto decimale se presente.

- **IMPL-5.17.20.3.9-01** `[CSS][JS]` `[Main]` Le celle numeriche di `.data-table` devono avere `text-align: right` con allineamento al separatore decimale (`font-variant-numeric: tabular-nums`); validate.js non deve richiedere giustificazione manuale.

#### 5.17.20.3.10 – Etichettatura delle unità di misura
*Sintesi (shall):* Le unità dei dati visualizzati devono essere incluse in modo coerente nelle etichette di colonna e riga visualizzate.

- **IMPL-5.17.20.3.10-01** `[HTML]` `[Main]` Gli `<th>` di colonna/riga di `.data-table` devono includere l'unità di misura (es. "Velocità (km/h)"), localizzata da i18n.js, non ripetuta in ogni cella.

#### 5.17.20.3.11 – Spaziatura coerente delle colonne
*Sintesi (shall):* Le larghezze delle colonne contenenti gli stessi elementi dati devono essere uniformi e coerenti all'interno di una tabella e da una tabella all'altra.

- **IMPL-5.17.20.3.11-01** `[CSS]` `[Globale]` Le colonne dello stesso tipo di dato in `.data-table` devono condividere la stessa classe di larghezza (`.col-w-date`, `.col-w-numeric`) in tutte le tabelle dell'applicazione.

#### 5.17.20.3.12 – Spaziatura coerente delle righe
*Sintesi (shall):* Le altezze delle righe contenenti gli stessi elementi dati devono essere uniformi e coerenti all'interno di una tabella e da una tabella all'altra.

- **IMPL-5.17.20.3.12-01** `[CSS]` `[Globale]` `.data-table tr` deve avere `min-block-size` uniforme, coerente con `--touch-target-min` se la riga è selezionabile, riusata in tutte le tabelle.

#### 5.17.20.3.13 – Indicazioni di scansione per colonna
*Sintesi (shall):* Deve essere mantenuta una separazione tra colonne non inferiore a tre spazi.

- **IMPL-5.17.20.3.13-01** `[CSS]` `[Globale]` `.data-table th, .data-table td` deve avere `padding-inline` minimo equivalente ad almeno 3 caratteri (`ch`) per la separazione visiva tra colonne.

#### 5.17.20.3.14 – Indicazioni di scansione per riga
*Sintesi (shall):* Un gruppo di righe deve essere separato a intervalli regolari non superiori a cinque righe.

- **IMPL-5.17.20.3.14-01** `[CSS]` `[Main]` `.data-table` deve evidenziare un gruppo di righe ogni 5 (`:nth-child(5n)` con sfondo leggero alternato) per facilitare la scansione visiva.

#### 5.17.20.4.1 – Disposizione degli elenchi
*Sintesi (shall):* Gli elementi negli elenchi devono essere disposti in un ordine riconoscibile (cronologico, alfabetico, sequenziale, funzionale o di importanza).

- **IMPL-5.17.20.4.1-01** `[JS]` `[Main]` Ogni `<ul>/<ol>` di dati deve avere un criterio di ordinamento esplicito applicato da state.js, selezionabile dall'utente dove pertinente.

#### 5.17.20.4.2 – Righe dell'elenco
*Sintesi (shall):* Ogni elemento di un elenco deve iniziare su una nuova riga.

- **IMPL-5.17.20.4.2-01** `[HTML][CSS]` `[Main]` Gli elenchi devono usare `<li>` per ciascun elemento con `display: list-item`, mai elementi concatenati inline sulla stessa riga.

#### 5.17.20.4.3 – Estensione verticale
*Sintesi (shall):* Dove gli elenchi si estendono su più di una pagina di visualizzazione, l'ultima riga di una pagina deve essere la prima riga della pagina successiva.

- **IMPL-5.17.20.4.3-01** `[JS]` `[Main]` La paginazione/scroll infinito degli elenchi lunghi (state.js) non deve saltare né duplicare elementi tra una pagina e la successiva (continuità garantita da indice/cursore).

#### 5.17.20.4.4 – Marcatura degli elementi multi-riga in un elenco
*Sintesi (shall):* Dove un elemento continua per più di una riga, deve essere marcato (es. riga vuota, rientro) affinché la continuazione sia evidente.

- **IMPL-5.17.20.4.4-01** `[CSS]` `[Main]` Gli elementi di elenco su più righe devono usare `text-indent`/`padding-inline-start` per le righe successive alla prima, rendendo la continuazione visivamente distinguibile.

#### 5.17.20.4.5 – Ordinamento verticale in colonne multiple
*Sintesi (shall):* Dove gli elementi di un elenco sono visualizzati in più colonne, devono essere ordinati verticalmente entro ciascuna colonna.

- **IMPL-5.17.20.4.5-01** `[CSS]` `[Main]` Gli elenchi multi-colonna (`grid-auto-flow: column`) devono riempire verticalmente ciascuna colonna prima di passare alla successiva, non riga per riga.

#### 5.17.20.4.6 – Numeri arabi
*Sintesi (shall):* Quando gli elementi elencati devono essere numerati, devono essere usati numeri arabi anziché numeri romani.

- **IMPL-5.17.20.4.6-01** `[CSS]` `[Main]` Gli `<ol>` numerati devono usare `list-style-type: decimal`, mai `upper-roman`/`lower-roman`.

#### 5.17.20.4.7 – Struttura gerarchica per elenchi lunghi
*Sintesi (shall):* Dove gli elenchi sono lunghi e si estendono oltre una pagina, deve essere usata una struttura gerarchica per permettere il partizionamento logico in elenchi più brevi correlati.

- **IMPL-5.17.20.4.7-01** `[HTML][JS]` `[Main]` Gli elenchi lunghi devono essere organizzati in categorie/sottocategorie espandibili (`<details>`/albero con `aria-expanded`), gestite da nav.js, invece di un unico elenco piatto scorrevole.

### 3.11 Codifica delle informazioni (colore, luminosità, lampeggio, dimensione, pattern, simboli, forma)

Tecniche di codifica visiva e relativa traduzione in token/classi CSS; la Tabella XL (Appendice A) è tradotta nella mappatura `--color-*` di seguito.

#### 5.17.24.1 – Uso della codifica
*Sintesi (shall):* La codifica deve facilitare la discriminazione tra viste (modalità operative diverse), identificare display correlati, mostrare relazioni tra display, e identificare informazioni critiche, valori anomali, elementi modificati/da modificare, messaggi ad alta priorità, aree speciali, errori di inserimento, criticità del comando e bersagli.

- **IMPL-5.17.24.1-01** `[CSS][JS]` `[Globale]` L'applicazione deve definire e riusare un set comune di classi di codifica (`.priority-critical`/`.priority-alarm`/`.priority-warning`/`.priority-normal`/`.priority-advisory`/`.is-changed`/`.is-new`/`.has-error`) applicate da feedback.js/notify.js/validate.js per tutti i casi elencati.

#### 5.17.24.2 – Codifica significativa
*Sintesi (shall):* Devono essere usati codici significativi, non arbitrari.

- **IMPL-5.17.24.2-01** `[CSS]` `[Globale]` Ogni classe/token di codifica (colore, icona, pattern) deve avere un significato documentato in config.js (tabella `codeMeaning`), non valori scelti arbitrariamente per vista.

#### 5.17.24.3 – Convenzioni di codifica comuni
*Sintesi (should):* Dovrebbero essere usate convenzioni di codifica comunemente impiegate.

- **IMPL-5.17.24.3-01** `[CSS]` `[Globale]` La mappatura colore→significato deve seguire le convenzioni della Tabella XL (vedi IMPL-5.17.25.8-01), non convenzioni proprietarie in conflitto con l'uso comune (es. rosso = normale).

#### 5.17.24.4 – Impatto sulle prestazioni
*Sintesi (shall):* La codifica non deve ridurre la leggibilità né influire negativamente sulle prestazioni dell'utente o del sistema.

- **IMPL-5.17.24.4-01** `[CSS]` `[Globale]` I criteri di contrasto (`--contrast-*`, §2.4, ≥6:1) devono valere anche con colori/pattern di codifica attivi; un criterio di revisione verifica il contrasto minimo su ogni combinazione classe-di-codifica/sfondo.

#### 5.17.24.5 – Codifica coerente
*Sintesi (shall):* Tutta la codifica all'interno del sistema deve essere coerente.

- **IMPL-5.17.24.5-01** `[CSS][JS]` `[Globale]` Le classi/token di codifica definiti in config.js/CSS devono essere gli unici ammessi; un criterio di revisione verifica l'assenza di colori o pattern hardcoded in CSS locali alle viste.

#### 5.17.24.6 – Tecniche
*Sintesi (may):* Le seguenti caratteristiche visive possono essere usate per la codifica: colore, luminosità, lampeggio, dimensione, pattern, posizione, sottolineatura, simbolo o forma.

- **IMPL-5.17.24.6-01** `[CSS]` `[Globale]` config.js/CSS devono rendere disponibili tutte le tecniche come token/classi riusabili: `--color-*` (colore), `.brightness-high`/`.brightness-low` (luminosità, 5.17.26), `.is-flashing` (lampeggio, 5.17.27), `.size-s`/`.size-m`/`.size-l` (dimensione, 5.17.28), `.pattern-*` (pattern, 5.17.29), posizione (layout), `.is-underlined` (sottolineatura, 5.17.30), icone SVG (simbolo, 5.17.31), `.shape-*` (forma, 5.17.32).

#### 5.17.25.1 – Uso
*Sintesi (may):* La codifica a colori può essere impiegata per differenziare classi di informazione in display complessi, densi o critici, o per indicare similarità tra elementi (raggruppamento) lungo una dimensione significativa.

- **IMPL-5.17.25.1-01** `[CSS]` `[Globale]` Il colore come codifica deve essere riservato a differenziare classi di informazione o raggruppamenti significativi (classi `.color-*` di §2.4), mai usato come unico mezzo decorativo senza significato.

#### 5.17.25.2 – Solo visione foveale
*Sintesi (shall):* Il colore non deve essere usato per attirare l'attenzione al di fuori del campo visivo ottimale.

- **IMPL-5.17.25.2-01** `[CSS][JS]` `[Globale]` Gli avvisi che richiedono attenzione immediata (notify.js) non devono fare affidamento solo su un cambio di colore periferico; devono usare anche `#alert-summary`/`#alert-region` nell'area centrale di lettura, suono (`beep()`) o vibrazione.

#### 5.17.25.3 – Coerenza
*Sintesi (shall):* La codifica a colori deve essere usata in modo coerente all'interno di un display e, ove appropriato, tra display di altri sistemi usati dagli stessi utenti.

- **IMPL-5.17.25.3-01** `[CSS]` `[Globale]` I token `--color-critical`/`--color-alarm`/`--color-warning`/`--color-normal`/`--color-advisory`/`--color-inactive` (§2.4) devono essere gli unici colori semantici usati, con lo stesso significato in ogni vista.

#### 5.17.25.4 – Personalizzazione del colore
*Sintesi (shall):* La personalizzazione del colore da parte dell'utente deve essere consentita solo per informazioni non tatticamente significative.

- **IMPL-5.17.25.4-01** `[JS]` `[Globale]` prefs.js deve permettere la personalizzazione solo di colori non semantici (tema generale `theme-light`/`theme-dark`), non dei token `--color-critical`/`--color-alarm`/... che restano fissi per significato tattico/di sicurezza.

#### 5.17.25.5 – Riconoscimento del colore
*Sintesi (shall):* Quando l'utente deve riconoscere categorie di informazione (es. variabili diverse in un grafico), devono essere usati al massimo 11 colori nominabili per rappresentare informazioni categoricamente diverse.

- **IMPL-5.17.25.5-01** `[CSS][JS]` `[Main]` La palette di categorizzazione per grafici/mappe (`config.js: categoryColors`) non deve definire più di 11 colori nominabili distinti; un test automatico verifica il conteggio.

#### 5.17.25.6 – Differenziazione del colore
*Sintesi (should):* Quando il colore raggruppa elementi di un display senza significato oltre il raggruppamento, i colori dovrebbero essere discriminabili.

- **IMPL-5.17.25.6-01** `[CSS]` `[Globale]` I colori usati per il solo raggruppamento visivo (non semantico) devono rispettare la soglia minima di 20 ΔE (vedi IMPL-5.17.25.16.2-01), verificabile per ispezione dei valori nella palette `categoryColors`.

#### 5.17.25.7 – Codifica della saturazione
*Sintesi (may):* La saturazione può indicare l'intensità relativa (es. più caldo, più pericoloso): a) da più caldo a più freddo la saturazione varia da rosso a blu; b) la saturazione del rosso aumenta con il pericolo.

- **IMPL-5.17.25.7-01** `[CSS][JS]` `[Main]` Dove è rappresentata un'intensità relativa (temperatura, livello di pericolo), la scala colore deve variare da `--color-advisory` (freddo/basso) a `--color-critical` (caldo/pericolo) in modo monotono, definita come scala in config.js (criteri a, b).

#### 5.17.25.8 – Significati dei colori (Tabella XL)
*Sintesi (shall):* I colori devono essere associati ai significati comuni della Tabella XL.

- **IMPL-5.17.25.8-01** `[CSS]` `[Globale]` I token `--color-*` traducono la Tabella XL: `--color-critical` (rosso: critico, pericolo, emergenza, allarme, stop, guasto), `--color-alarm` (arancione: allarme/allerta, minaccia elevata), `--color-warning` (giallo: attenzione/cautela, stato anomalo), `--color-normal` (verde: normale, sicuro, pronto, acceso), `--color-advisory` (blu/ciano: consultivo, minaccia moderata, freddo), `--color-inactive` (grigio: inattivo/non disponibile).
- **IMPL-5.17.25.8-02** `[CSS]` `[Globale]` Per i significati della Tabella XL non coperti dai token principali, config.js deve definire: `--color-hazard` (magenta: allarme/rischio radiazioni), classe `.is-outline` (nero: bordo/contorno, non colore di stato) e `.is-neutral` (bianco: vuoto/azione in corso/personale medico), senza introdurre significati non previsti dalla tabella.
- **IMPL-5.17.25.8-03** `[JS]` `[Globale]` config.js deve esporre una tabella `colorMeaning` (id → {token, etichetta}) coerente con la Tabella XL, usata da notify.js/state.js per assegnare automaticamente la classe `.color-*` corretta in base al tipo di evento/stato, unica fonte di verità per la mappatura.
- **IMPL-5.17.25.8-04** `[HTML][CSS]` `[Globale]` Ogni elemento che usa un colore semantico della Tabella XL deve applicare la classe `.color-critical`/`.color-alarm`/`.color-warning`/`.color-normal`/`.color-advisory`/`.color-inactive` corrispondente più icona/testo ridondante (vedi IMPL-5.17.25.11-01), mai il colore come unico indicatore.

#### 5.17.25.8.1 – Colori freddi
*Sintesi (shall):* I colori freddi (lunghezza d'onda più corta, es. blu o verde) devono essere usati per visualizzare informazioni usate raramente e per comunicare lo stato di informazioni di sfondo.

- **IMPL-5.17.25.8.1-01** `[CSS]` `[Globale]` Le informazioni di sfondo/secondarie a bassa frequenza d'uso devono usare `--color-advisory`/`--color-normal` (blu/verde), mai colori caldi riservati all'azione.

#### 5.17.25.8.2 – Colori caldi
*Sintesi (shall):* I colori caldi (lunghezza d'onda più lunga, es. rosso o arancione) devono essere usati per comunicare un'azione o la necessità di una risposta.

- **IMPL-5.17.25.8.2-01** `[CSS]` `[Globale]` Gli elementi che richiedono un'azione o risposta dell'utente (conferme, allarmi) devono usare `--color-critical`/`--color-alarm` (rosso/arancione), coerentemente con IMPL-5.17.25.8-01.

#### 5.17.25.10 – Utenti daltonici
*Sintesi (should):* Se la popolazione di utenti include persone daltoniche, ogni sforzo dovrebbe essere fatto per usare colori non confondibili per la codifica.

- **IMPL-5.17.25.10-01** `[CSS][JS]` `[Globale]` `theme-high-contrast`/prefs.js deve offrire una palette alternativa sicura per daltonismo (deuteranopia/protanopia), verificata con uno strumento di simulazione; i token `--color-*` restano percettivamente distinguibili in entrambe le palette.

#### 5.17.25.11 – Codifica ridondante
*Sintesi (shall):* Se non è possibile usare colori non confondibili per utenti daltonici, deve essere usata una codifica ridondante.

- **IMPL-5.17.25.11-01** `[HTML][CSS]` `[Globale]` Ogni uso semantico del colore (`.color-*`) deve essere accompagnato da almeno un secondo canale (icona SVG, testo, pattern o forma, "doppia codifica" di §2.4); un criterio di revisione verifica l'assenza di informazione veicolata dal solo colore.

#### 5.17.25.13 – Dimensione dell'oggetto
*Sintesi (shall/should):* Quando è richiesta una percezione accurata del colore, la dimensione maggiore di simboli grandi isolati deve sottendere non meno di 8,7 milliradianti (30') e dovrebbe sottendere non meno di 13,1 milliradianti (45').

- **IMPL-5.17.25.13-01** `[CSS]` `[Main]` I simboli isolati colorati che richiedono riconoscimento di colore accurato (es. indicatori di stato grandi) devono avere dimensione minima calcolata da `--viewing-distance-mm`/`--px-per-mm` per soddisfare la soglia di 8,7 mrad, documentata in config.js.

#### 5.17.25.14 – Altezza dell'oggetto
*Sintesi (should):* Quando è richiesta una percezione accurata del colore, l'altezza di simboli e caratteri piccoli dovrebbe sottendere almeno 5,8 milliradianti (20') dalla massima distanza di visione prevista.

- **IMPL-5.17.25.14-01** `[CSS]` `[Main]` I simboli/caratteri piccoli colorati (es. badge di stato) devono rispettare un'altezza minima equivalente a 5,8 mrad alla distanza di progetto, coerente con `--fs-small` minimo definito in config.js.

#### 5.17.25.15 – Simboli pieni
*Sintesi (shall):* Per migliorare rilevabilità e discriminabilità, devono essere usati simboli colorati pieni (fill) invece di simboli solo contornati.

- **IMPL-5.17.25.15-01** `[CSS]` `[Globale]` Le icone di stato/indicatori semantici devono usare `fill` pieno (non solo `stroke`/contorno) con il colore `--color-*` corrispondente.

#### 5.17.25.16.3 – Abbinamenti testo/sfondo per luce diurna o artificiale
*Sintesi (should):* Il testo ≤14pt dovrebbe avere rapporto di contrasto >4,5:1; il testo >14pt dovrebbe avere rapporto ≥3:1; testo nero/scuro su sfondo chiaro.

- **IMPL-5.17.25.16.3-01** `[CSS]` `[Globale]` `theme-light` deve garantire rapporto di contrasto testo/sfondo >4,5:1 per `--fs-body`/`--fs-small` (≤14pt) e ≥3:1 per `--fs-title` (>14pt), con testo scuro su sfondo chiaro, verificabile con strumento di contrasto automatico.

#### 5.17.25.16.4 – Abbinamenti testo/sfondo per condizioni di buio
*Sintesi (should):* Testo bianco/chiaro dovrebbe essere su sfondo scuro.

- **IMPL-5.17.25.16.4-01** `[CSS]` `[Globale]` `theme-dark` (`data-theme="theme-dark"`, prefs.js) deve usare testo chiaro su sfondo scuro, con gli stessi rapporti di contrasto minimi di IMPL-5.17.25.16.3-01.

#### 5.17.26.1 – Uso
*Sintesi (should):* La codifica per intensità di luminosità dovrebbe essere impiegata solo per differenziare un elemento informativo da informazioni adiacenti.

- **IMPL-5.17.26.1-01** `[CSS]` `[Globale]` `.brightness-high`/`.brightness-low` deve essere usata solo per distinguere un elemento da quelli adiacenti nella stessa vista, non come codifica semantica primaria (riservata al colore/icona, vedi 5.17.25.8).

#### 5.17.26.2 – Livelli di intensità di luminosità
*Sintesi (shall):* Non devono essere usati più di due livelli di luminosità, e ciascun livello deve essere separato dal più vicino da non meno di un rapporto 2:1.

- **IMPL-5.17.26.2-01** `[CSS]` `[Globale]` config.js/CSS devono definire al massimo due livelli di luminosità (`--brightness-high`, `--brightness-low`) con rapporto di luminanza ≥2:1 tra loro; un test di accettazione verifica l'assenza di un terzo livello in uso.

#### 5.17.26.3 – Inversione di luminosità
*Sintesi (may/shall):* L'inversione di luminosità ("reverse video") può essere usata per evidenziare elementi critici; se usata a scopo di allerta, deve essere riservata esclusivamente a quello scopo e non per evidenziazione generale.

- **IMPL-5.17.26.3-01** `[CSS][JS]` `[Globale]` La classe `.reverse-video` deve essere riservata esclusivamente agli stati di allerta gestiti da notify.js/feedback.js (`.priority-critical`); non deve essere applicata per evidenziazioni generiche non di allerta.

#### 5.17.27.1 – Uso
*Sintesi (should):* La codifica a lampeggio dovrebbe essere impiegata per richiamare l'attenzione dell'utente solo su eventi critici per la missione.

- **IMPL-5.17.27.1-01** `[CSS][JS]` `[Globale]` La classe `.is-flashing` deve essere applicata da notify.js solo a eventi critici per la missione (`.priority-critical`/`.priority-alarm`), mai per enfasi decorativa o informazioni non critiche.

#### 5.17.27.2 – Duty cycle
*Sintesi (shall):* La percentuale di tempo "acceso" deve essere pari o non inferiore alla percentuale di tempo "spento"; è preferito un duty cycle del 50%.

- **IMPL-5.17.27.2-01** `[CSS]` `[Globale]` L'animazione `@keyframes flash` di `.is-flashing` deve avere un duty cycle acceso/spento del 50% (default, mai <50% acceso).

#### 5.17.27.3 – Frequenza di lampeggio
*Sintesi (shall):* Non devono essere usate più di due frequenze di lampeggio.

- **IMPL-5.17.27.3-01** `[CSS]` `[Globale]` CSS deve definire al massimo due varianti (`.is-flashing-fast`, `.is-flashing-slow`) come uniche frequenze di lampeggio ammesse nell'applicazione.

#### 5.17.27.3.1 – Differenze tra le frequenze di lampeggio
*Sintesi (shall):* Le due frequenze di lampeggio devono differire di non meno di 2,0 Hz.

- **IMPL-5.17.27.3.1-01** `[CSS]` `[Globale]` Le `animation-duration` di `.is-flashing-fast` e `.is-flashing-slow` devono corrispondere a frequenze che differiscono di ≥2,0 Hz, documentate in config.js.

#### 5.17.27.3.2 – Frequenza di lampeggio più alta
*Sintesi (shall):* La frequenza di lampeggio più alta non deve essere superiore a 5,0 Hz.

- **IMPL-5.17.27.3.2-01** `[CSS]` `[Globale]` `.is-flashing-fast` deve avere frequenza ≤5,0 Hz (`animation-duration ≥ 0,2s`).

#### 5.17.27.3.3 – Frequenza di lampeggio più lenta
*Sintesi (shall):* La frequenza di lampeggio più lenta non deve essere inferiore a 0,8 Hz.

- **IMPL-5.17.27.3.3-01** `[CSS]` `[Globale]` `.is-flashing-slow` deve avere frequenza ≥0,8 Hz (`animation-duration ≤ 1,25s`).

#### 5.17.27.3.4 – Informazione critica
*Sintesi (shall):* La frequenza di lampeggio più alta deve riflettere l'informazione più critica.

- **IMPL-5.17.27.3.4-01** `[CSS][JS]` `[Globale]` notify.js deve assegnare `.is-flashing-fast` esclusivamente a `.priority-critical` e `.is-flashing-slow` alle priorità inferiori che richiedono comunque lampeggio (`.priority-alarm`).

#### 5.17.27.4 – Sincronizzazione della frequenza di lampeggio
*Sintesi (shall):* Gli elementi che lampeggiano alla stessa frequenza devono essere sincronizzati.

- **IMPL-5.17.27.4-01** `[CSS]` `[Globale]` Tutte le istanze di `.is-flashing-fast`/`.is-flashing-slow` devono condividere la stessa `animation` con `animation-delay: 0s` e timeline comune, per restare sincronizzate.

#### 5.17.27.5 – Flash rate text
*Sintesi (shall/may):* I caratteri che devono essere letti non devono lampeggiare; un simbolo adiacente lampeggiante, uno sfondo lampeggiante o un'intensità alternata possono aggiungere enfasi al testo.

- **IMPL-5.17.27.5-01** `[CSS]` `[Globale]` `.is-flashing` non deve essere applicata direttamente al testo leggibile; l'enfasi va ottenuta lampeggiando un'icona/sfondo adiacente (`.is-flashing` sul contenitore/icona, testo sempre stabile).

#### 5.17.27.6 – Soppressione del lampeggio
*Sintesi (shall):* Deve essere fornito un controllo di riconoscimento dell'evento o di soppressione del lampeggio.

- **IMPL-5.17.27.6-01** `[HTML][JS]` `[Globale]` Ogni elemento `.is-flashing` deve essere abbinato a un comando "Riconosci" (`feedback.js: acknowledge(el)`) che rimuove la classe/ferma l'animazione dopo l'interazione dell'utente.

#### 5.17.27.7 – Flashing area
*Sintesi (should):* Solo una piccola area di un display dovrebbe lampeggiare in un dato momento.

- **IMPL-5.17.27.7-01** `[CSS][JS]` `[Globale]` notify.js deve limitare il numero di elementi `.is-flashing` simultanei (soglia configurabile in config.js, es. max 1-2 per vista) per evitare che un'ampia area dello schermo lampeggi contemporaneamente.

#### 5.17.28 – Codifica per dimensione
*Sintesi (shall):* Non devono essere usati più di tre livelli di dimensione; la dimensione maggiore deve essere non meno del 150% della dimensione maggiore della più piccola.

- **IMPL-5.17.28-01** `[CSS]` `[Globale]` config.js/CSS devono definire al massimo tre classi di dimensione (`.size-s`, `.size-m`, `.size-l`) per la codifica, con `.size-l` ≥150% di `.size-s` (basate su `--fs-small`/`--fs-body`/`--fs-title`).

#### 5.17.29 – Codifica per pattern e posizione
*Sintesi (may):* La codifica per pattern e posizione può essere usata per ridurre il tempo di ricerca dell'utente, limitando l'area da ricercare a segmenti prescritti.

- **IMPL-5.17.29-01** `[CSS]` `[Main]` Le aree funzionali (`#nav-region`, `#content-region`, `#aux-region`, `#alert-region`) devono mantenere posizione fissa e coerente tra le viste; classi `.pattern-*` (tratteggio, trama di sfondo) possono fungere da codifica secondaria ridondante al colore per categorie di dati.

#### 5.17.30 – Codifica per sottolineatura
*Sintesi (may):* La sottolineatura può essere impiegata per indicare valori anomali, errori di inserimento, elementi modificati o elementi da modificare.

- **IMPL-5.17.30-01** `[CSS][JS]` `[Globale]` La classe `.is-underlined` (abbinata a `.is-changed`/`.has-error`) gestita da validate.js/state.js può essere usata come codifica aggiuntiva (non esclusiva) per valori anomali, errori ed elementi modificati o da modificare.

#### 5.17.31.1 – Uso
*Sintesi (may):* La codifica a simboli può essere usata per migliorare l'assimilazione di informazioni dai display di dati.

- **IMPL-5.17.31.1-01** `[HTML][CSS]` `[Globale]` Le icone SVG comuni devono essere riusate come libreria condivisa per migliorare l'assimilazione delle informazioni nelle viste dati, invece di solo testo/colore.

#### 5.17.31.2 – Selezione del simbolo
*Sintesi (shall):* I simboli devono essere analoghi all'evento o elemento di sistema che rappresentano o di uso generale e ben noti agli utenti previsti.

- **IMPL-5.17.31.2-01** `[HTML]` `[Globale]` Le icone usate devono essere scelte da un set standard/convenzionale (es. icone ISO 7000, Material Symbols) analogico al significato rappresentato, non simboli arbitrari inventati.

#### 5.17.31.3 – Simboli speciali
*Sintesi (shall):* Quando sono usati simboli speciali per segnalare condizioni critiche, devono essere usati solo per quello scopo.

- **IMPL-5.17.31.3-01** `[HTML][CSS]` `[Globale]` Il simbolo riservato alle condizioni critiche (es. triangolo di allarme `--color-critical`) non deve essere riusato per altri significati in nessuna vista; un criterio di revisione verifica l'unicità d'uso.

#### 5.17.31.4 – Marcatori vicini alle parole marcate
*Sintesi (shall):* Quando un simbolo speciale è usato per marcare una parola, il simbolo deve essere separato dall'inizio della parola da uno spazio.

- **IMPL-5.17.31.4-01** `[CSS]` `[Globale]` I simboli/icone inline adiacenti a testo (es. icona di errore prima di un'etichetta) devono avere `margin-inline-end` ≥1 carattere tra l'icona e la parola marcata.

#### 5.17.32 – Codifica per forma
*Sintesi (may/should):* La codifica per forma può essere usata per compiti di ricerca e identificazione; i codici selezionati dovrebbero basarsi su standard consolidati o significati convenzionali.

- **IMPL-5.17.32-01** `[CSS]` `[Globale]` Le classi `.shape-circle`/`.shape-triangle`/`.shape-square`/`.shape-diamond` (badge/indicatori) devono seguire significati convenzionali consolidati (es. triangolo = attenzione, cerchio = stato, quadrato = stop/blocco), documentati in config.js, mai forme arbitrarie.

#### 5.17.25.16.1 – Contrasto di colore
*Sintesi (shall):* I simboli colorati devono differire dallo sfondo di non meno di 100 ΔE (CIE L*u*v*), calcolato con la formula ΔE(Yu'v') = [(155·ΔY/YM)² + (367·Δu')² + (167·Δv')²]^(1/2), con ΔY differenza di luminanza, YM luminanza massima del display, Δu'/Δv' differenze delle coordinate UCS.

- **IMPL-5.17.25.16.1-01** `[CSS]` `[Globale]` Ogni coppia simbolo/sfondo colorata nei token `--color-*` deve rispettare ΔE(Yu'v') ≥ 100 secondo la formula dello standard; la verifica è un test di accettazione per ispezione/strumento colorimetrico sui valori definiti in config.js.

#### 5.17.25.16.2 – Differenze di colore
*Sintesi (shall):* I colori di un insieme devono differire tra loro di non meno di 20 ΔE (CIE L*u*v*), con riferimento al bianco di visualizzazione (D6500K o 9300K+27MPCD), secondo la formula del documento.

- **IMPL-5.17.25.16.2-01** `[CSS]` `[Globale]` L'insieme dei colori semantici `--color-*` e la palette `categoryColors` (config.js) devono avere distanza ΔE(L*u*v*) ≥ 20 tra loro a due a due, verificato con strumento/test di accettazione in fase di definizione della palette.

### 3.12 Grafica, display dinamici, display geospaziali, stampa e trasmissione dati

Requisiti per interfacce grafiche generiche, grafici/diagrammi, aggiornamenti dati in tempo reale, display geospaziali, stampa e trasmissione di messaggi/dati; realizzati in `#content-region` con elementi `<canvas>`/SVG, tabelle e form, supportati da `state.js`, `net.js` e `notify.js`.

#### 5.2.1.1.3 – Movimento degli elementi grafici
*Sintesi (shall):* Gli elementi grafici animati non devono muoversi più velocemente di 60°/s di angolo visivo (preferibile ≤20°/s).

- **IMPL-5.2.1.1.3-01** `[JS]` `[Main]` Le animazioni in `#content-region` (es. rotazioni, traslazioni su `<canvas>`/SVG) devono limitare la velocità angolare a un valore di progetto in `config.js` (`maxAngularSpeedDegPerSec`, default 20, massimo 60), applicato tramite `requestAnimationFrame`.
- **IMPL-5.2.1.1.3-02** `[JS]` `[Globale]` `feedback.js` deve esporre `clampAnimationSpeed(deltaDeg, deltaTimeMs)` usato da tutti i moduli di visualizzazione dinamica per non superare la soglia configurata.

#### 5.2.1.1.4 – Orientamento degli oggetti
*Sintesi (shall):* I display devono includere riferimento a verticale/orizzontale (es. “Nord”), fornire indizi di orientamento relativo tra oggetti (fronte/retro, alto/basso) e indizi di dinamica (direzione/velocità).

- **IMPL-5.2.1.1.4-01** `[HTML][CSS]` `[Main]` Ogni display grafico orientabile deve includere un indicatore testuale/iconico di riferimento (`.orientation-ref`, es. “N↑”) ancorato con `position: sticky` all'angolo del `<canvas>`/SVG.
- **IMPL-5.2.1.1.4-02** `[JS]` `[Main]` `state.js` deve fornire per ogni oggetto grafico attributi `facing`, `zOrder` e `velocityVector` resi visivamente (frecce, ombreggiatura fronte/retro) dal modulo di rendering.
- **IMPL-5.2.1.1.4-03** `[JS]` `[Main]` La direzione e velocità di movimento degli oggetti devono essere codificate con vettori/frecce scalati in proporzione a `velocityVector`, ridisegnati ad ogni frame.

#### 5.2.1.1.5 – Flash coding
*Sintesi (shall):* La codifica a lampeggio deve rispettare i criteri di 5.17.27 (vedi requisiti dedicati al flash coding in altra sezione).

- **IMPL-5.2.1.1.5-01** `[CSS][JS]` `[Globale]` Gli elementi grafici lampeggianti devono usare la classe `.flash-code` comune (vedi `IMPL-5.17.27-*`), con frequenza e durata regolate da `config.js`, senza duplicare qui la logica.

#### 5.17.21.1 – Interfacce grafiche utente
*Sintesi:* Riferimento a ANSI/HFES 200 per ulteriori linee guida sulle GUI grafiche (nessun requisito diretto aggiuntivo).

- **IMPL-5.17.21.1-01** `[HTML][CSS]` `[Globale]` Le interfacce grafiche di `#content-region` devono riutilizzare i componenti/token comuni (§2.4, §2.6) invece di introdurre pattern visivi ad hoc, come criterio di revisione del codice.

#### 5.17.21.1.1 – Selezione degli attributi visualizzati
*Sintesi (shall):* Durante l'immissione di dati grafici, l'utente deve poter specificare attributi degli elementi (font, simbolo di tracciamento, tipo di linea).

- **IMPL-5.17.21.1.1-01** `[HTML]` `[Main]` Un pannello `#graphic-attrs-panel` in `#aux-region` deve esporre controlli (`<select>` per `font-family`, simbolo, `line-dash`) collegati all'elemento grafico selezionato.
- **IMPL-5.17.21.1.1-02** `[JS]` `[Main]` `validate.js` deve applicare le modifiche di attributo tramite evento `CustomEvent('graphic-attr-change')` gestito da `state.js`.

#### 5.17.21.1.2 – Esempi
*Sintesi (should):* La possibilità di specificare attributi dovrebbe includere esempi illustrativi delle opzioni disponibili.

- **IMPL-5.17.21.1.2-01** `[HTML][CSS]` `[Main]` Ogni `<select>` di `#graphic-attrs-panel` dovrebbe mostrare anteprime visive delle opzioni (es. swatch di linea, glifo campione) tramite `<option>` con icona di sfondo o `<datalist>` illustrata.

#### 5.17.21.1.3 – Selezione degli elementi grafici
*Sintesi (shall):* L'utente deve avere un mezzo per designare/selezionare elementi grafici visualizzati quando è richiesta la loro manipolazione (tipicamente puntatore/cursore).

- **IMPL-5.17.21.1.3-01** `[HTML][JS]` `[Main]` Gli elementi grafici selezionabili devono gestire `pointerdown`/`pointerup` (strategia *last contact*) impostando `aria-selected="true"` e `.is-selected`, con hit-area ≥ `--touch-target-min`.

#### 5.17.21.1.4 – Visualizzazione degli attributi correnti
*Sintesi (shall):* Gli attributi che saranno modificati da una variazione devono essere evidenziati.

- **IMPL-5.17.21.1.4-01** `[CSS][JS]` `[Main]` Quando un controllo attributo è a fuoco/in modifica, l'elemento grafico target deve ricevere `.is-highlighted` (outline distintivo) finché la modifica non è confermata o annullata.

#### 5.17.21.1.5 – Salvataggio e recupero semplici
*Sintesi (shall):* L'utente deve disporre di un mezzo per salvare e recuperare i display di dati grafici.

- **IMPL-5.17.21.1.5-01** `[HTML][JS]` `[Main]` I controlli `#btn-save-graphic`/`#btn-load-graphic` devono serializzare lo stato grafico (`state.js`) in JSON, salvato via `net.js` o `localStorage` tramite `prefs.js`.

#### 5.17.21.1.6 – Registrazione automatica dei dati
*Sintesi (shall):* Il sistema deve fornire registrazione/allineamento automatico dei dati grafici generati dal computer rispetto allo sfondo fisso o alla mappa dati, a qualunque scala.

- **IMPL-5.17.21.1.6-01** `[JS]` `[Main]` Il modulo di rendering deve applicare una trasformazione di coordinate condivisa (`state.js` → `viewTransform`) a overlay e mappa base, ricalcolata automaticamente ad ogni zoom/pan per mantenere l'allineamento.

#### 5.17.21.1.7 – Formati grafici predefiniti
*Sintesi (shall):* Dove i dati grafici devono essere tracciati in formati standard predefiniti (es. aree target su mappe, piani di volo), devono essere forniti template o display scheletro per facilitare l'inserimento dati.

- **IMPL-5.17.21.1.7-01** `[HTML][JS]` `[Main]` Deve essere disponibile un selettore `#template-select` che precarica forme/overlay scheletro predefiniti (da `config.js`) nell'area di disegno.

#### 5.17.21.1.8 – Modifica delle dimensioni
*Sintesi (shall):* Durante l'editing di dati grafici, l'utente deve poter cambiare la dimensione (scala) di un elemento selezionato invece di cancellarlo e ricrearlo.

- **IMPL-5.17.21.1.8-01** `[HTML][JS]` `[Main]` Gli elementi grafici selezionati devono esporre maniglie di ridimensionamento (`.resize-handle`, ≥ `--touch-target-min`) che aggiornano `scale`/`size` in `state.js` senza distruggere l'elemento.

#### 5.17.21.1.9 – Evidenziazione dei dati critici
*Sintesi (shall):* Quando l'attenzione dell'utente deve essere diretta a una porzione del display grafico con dati critici/anomali, tale elemento deve essere evidenziato con una codifica dati distintiva.

- **IMPL-5.17.21.1.9-01** `[CSS][JS]` `[Main]` Gli elementi grafici con dati critici devono ricevere la classe `.is-critical` (colore `--color-critical`, icona e, se pertinente, `.flash-code`), applicata da `notify.js` in base a soglie di `config.js`.

#### 5.17.21.1.10 – Derivazione computerizzata dei dati grafici
*Sintesi (should):* Calcoli comuni o complessi (es. curve di interpolazione, tracciamento di coordinate) dovrebbero essere eseguiti dal sistema su richiesta dell'utente.

- **IMPL-5.17.21.1.10-01** `[JS]` `[Main]` Un comando `#btn-compute-fit` dovrebbe invocare funzioni di calcolo (es. `computeCurveFit()`, `computePlotFromCoords()`) esposte dal modulo grafico, evitando calcoli manuali da parte dell'utente.

#### 5.17.21.1.11 – Orientamento normale delle etichette
*Sintesi (shall):* Il testo sui display grafici dinamici deve rimanere diritto quando l'immagine visualizzata ruota.

- **IMPL-5.17.21.1.11-01** `[CSS][JS]` `[Main]` Le etichette testuali devono applicare una contro-rotazione (`transform: rotate(calc(-1 * var(--map-rotation)))`) sincronizzata con la rotazione della mappa/immagine, così da restare sempre upright.

#### 5.17.21.1.12 – Visualizzazione della scala
*Sintesi (shall):* Se un display grafico/mappa ha funzionalità di zoom, il livello di zoom o la scala corrente devono essere visualizzati.

- **IMPL-5.17.21.1.12-01** `[HTML][JS]` `[Main]` Un indicatore `#zoom-scale-indicator` deve mostrare il livello di zoom o la scala corrente (es. “1:50 000”), aggiornato da `state.js` ad ogni variazione di zoom.

#### 5.17.21.1.13 – Precisione del display
*Sintesi (shall, se applicabile):* Se applicabile, la precisione corrente del display deve essere visualizzata.

- **IMPL-5.17.21.1.13-01** `[HTML][JS]` `[Main]` Dove rilevante, `#display-accuracy-indicator` deve mostrare la precisione corrente (es. ± m), valore fornito da `state.js`/sorgente dati.

#### 5.17.21.1.14 – Interpolazione
*Sintesi (shall):* Il sistema deve fornire informazioni numeriche accurate per qualsiasi punto del display grafico su richiesta dell'utente.

- **IMPL-5.17.21.1.14-01** `[JS]` `[Main]` Un gestore `pointerup` su richiesta esplicita (es. tasto “leggi valore”) deve invocare `interpolateValueAt(x, y)` e mostrare il risultato in un tooltip/`#aux-region`.

#### 5.17.21.1.15 – Griglie non invasive
*Sintesi (shall):* Le linee di griglia visualizzate devono essere non invasive.

- **IMPL-5.17.21.1.15-01** `[CSS]` `[Main]` Le linee di griglia devono usare `.grid-line` con basso contrasto/opacità (`opacity: 0.25`–`0.4`) rispetto ai dati, senza competere visivamente con essi.

#### 5.17.21.1.16 – Occultamento di elementi dati
*Sintesi (shall):* Le linee di griglia visualizzate non devono oscurare gli elementi dati.

- **IMPL-5.17.21.1.16-01** `[CSS]` `[Main]` Il livello z delle griglie (`.grid-line`) deve essere sempre inferiore (`z-index`/ordine di disegno) a quello degli elementi dati, così da non coprirli.

#### 5.17.21.1.17 – Soppressione delle linee di griglia
*Sintesi (shall):* L'utente deve poter sopprimere o visualizzare le linee di griglia.

- **IMPL-5.17.21.1.17-01** `[HTML][JS]` `[Main]` Un controllo `#toggle-grid` (`aria-pressed`) deve attivare/disattivare la visibilità di `.grid-line` tramite `prefs.js`, persistendo la scelta.

#### 5.17.21.2 – Grafici e diagrammi
*Sintesi (shall):* I grafici e diagrammi devono rispettare i criteri di 5.17.21.2.1–5.17.21.2.6 (introduzione, nessun requisito implementativo autonomo oltre ai successivi).

- **IMPL-5.17.21.2-01** `[Globale]` Nessun requisito implementativo dedicato; vedi `IMPL-5.17.21.2.1-01` … `IMPL-5.17.21.2.6-01` per i criteri specifici.

#### 5.17.21.2.1 – Assi dei grafici
*Sintesi (shall):* Gli assi dei grafici devono essere etichettati.

- **IMPL-5.17.21.2.1-01** `[HTML][CSS]` `[Main]` Ogni grafico in `#content-region` deve includere etichette `<text>`/`<figcaption>` per asse X e Y con unità di misura, classe `.chart-axis-label`.

#### 5.17.21.2.2 – Linee di tendenza
*Sintesi (should):* Quando più linee di tendenza devono essere confrontate, dovrebbero essere mostrate su un unico grafico.

- **IMPL-5.17.21.2.2-01** `[JS]` `[Main]` Il componente grafico dovrebbe supportare il rendering di più serie (`series[]`) sovrapposte in un singolo `<canvas>`/SVG, con legenda `.chart-legend` distintiva per colore/tratteggio.

#### 5.17.21.2.3 – Indice di riferimento
*Sintesi (shall):* Quando l'utente deve confrontare dati grafici con un livello o valore critico significativo, tale livello/valore deve essere incluso nel display.

- **IMPL-5.17.21.2.3-01** `[JS][CSS]` `[Main]` I grafici devono poter disegnare una linea di riferimento `.chart-threshold-line` (valore da `config.js` o dati) con stile distinto (tratteggio, colore `--color-warning`/`--color-critical`).

#### 5.17.21.2.4 – Annotazione dei dati
*Sintesi (shall):* Quando è richiesta una lettura precisa del display grafico, deve essere possibile integrare la rappresentazione grafica con i valori numerici rappresentati.

- **IMPL-5.17.21.2.4-01** `[HTML][JS]` `[Main]` Un controllo `#toggle-data-labels` (`aria-pressed`) deve mostrare/nascondere etichette numeriche `.chart-data-label` sui punti dati del grafico.

#### 5.17.21.2.5 – Scala coerente
*Sintesi (shall):* Quando l'utente deve confrontare dati grafici su una serie di diagrammi, deve essere usata la stessa scala per ogni diagramma.

- **IMPL-5.17.21.2.5-01** `[JS]` `[Main]` Il componente grafico multi-istanza deve calcolare e applicare un dominio/scala comune (`sharedScale` in `state.js`) a tutti i grafici confrontabili nella stessa vista.

#### 5.17.21.2.6 – Scala singola
*Sintesi (shall):* Nei grafici deve essere mostrata una sola scala per asse, non scale diverse per curve diverse nello stesso grafico.

- **IMPL-5.17.21.2.6-01** `[HTML][CSS]` `[Main]` Ogni asse del grafico deve rendere una sola etichetta di scala (`.chart-axis-label`); le serie con unità diverse richiedono grafici separati o assi secondari esplicitamente etichettati, mai scale ambigue sovrapposte.

#### 5.17.21.3.1 – Disegno di forme
*Sintesi (shall):* Quando è richiesto il disegno di forme, l'utente deve disporre di ausili per disegnarle.

- **IMPL-5.17.21.3.1-01** `[HTML][JS]` `[Main]` Una barra strumenti `#drawing-tools` deve offrire ausili per forme predefinite (rettangolo, cerchio, poligono) con snapping tramite `pointermove`/`pointerup`.

#### 5.17.21.3.2 – Disegno di linee
*Sintesi (shall):* Quando è richiesto il disegno di linee, l'utente deve disporre di ausili per disegnare segmenti di retta.

- **IMPL-5.17.21.3.2-01** `[HTML][JS]` `[Main]` `#drawing-tools` deve includere uno strumento linea retta con anteprima in tempo reale durante `pointermove` e conferma su `pointerup`.

#### 5.17.21.3.3 – Disegno di linee intersecanti
*Sintesi (should):* Quando l'utente disegna l'estremità di un segmento vicino a un elemento visualizzato, il sistema dovrebbe collegare automaticamente il segmento all'elemento.

- **IMPL-5.17.21.3.3-01** `[JS]` `[Main]` Durante il disegno, un algoritmo di snapping dovrebbe collegare automaticamente l'estremità del segmento a elementi entro una tolleranza (`config.js: snapToleranceMm` convertita con `--px-per-mm`).

#### 5.17.21.3.4 – Override
*Sintesi (shall):* L'utente deve disporre di un mezzo per disattivare la funzione di connessione automatica dei segmenti.

- **IMPL-5.17.21.3.4-01** `[HTML][JS]` `[Main]` Un controllo `#toggle-snap` (`aria-pressed`) deve permettere di disattivare temporaneamente lo snapping automatico di cui a `IMPL-5.17.21.3.3-01`.

#### 5.17.21.3.5 – Disegno di linee e figure con coordinate numeriche
*Sintesi (shall):* Quando linee/figure devono rappresentare coordinate numeriche, gli ausili informatici devono includere metodi per inserire le coordinate e selezionare le unità appropriate.

- **IMPL-5.17.21.3.5-01** `[HTML][JS]` `[Main]` Un form `#coord-entry-form` con campi numerici validati (`validate.js`) e `<select>` unità (gradi, metri, MGRS) deve permettere di creare/posizionare figure per coordinate esatte.

#### 5.17.22.1.1 – Valori che cambiano
*Sintesi (shall):* I valori alfanumerici che cambiano in tempo reale devono essere aggiornati secondo i criteri di 5.17.22.1.1.1–5.17.22.1.1.2 (introduzione).

- **IMPL-5.17.22.1.1-01** `[Globale]` Nessun requisito implementativo autonomo; vedi `IMPL-5.17.22.1.1.1-01` e `IMPL-5.17.22.1.1.2-01`.

#### 5.17.22.1.1.1 – Lettura affidabile
*Sintesi (shall):* I valori che l'utente deve leggere in modo affidabile non devono essere aggiornati più di una volta al secondo.

- **IMPL-5.17.22.1.1.1-01** `[JS]` `[Main]` I display di valori “da leggere” (classe `.value-readable`) devono limitare la frequenza di aggiornamento DOM a ≤1 Hz tramite throttling in `state.js`/`net.js`, anche se i dati sorgente arrivano più spesso.

#### 5.17.22.1.1.2 – Identificazione della velocità di variazione
*Sintesi (shall):* I valori usati per identificare la velocità di variazione o leggere valori grossolani devono essere aggiornati 3-4 volte al secondo.

- **IMPL-5.17.22.1.1.2-01** `[JS]` `[Main]` I display di tendenza (classe `.value-trend`) devono aggiornarsi a una frequenza di progetto `config.js: trendUpdateHz` (default 3,5 Hz, intervallo 3–4 Hz).

#### 5.17.22.1.2 – Frequenza di aggiornamento
*Sintesi (should):* La frequenza di aggiornamento dovrebbe essere conforme ai requisiti specifici del programma.

- **IMPL-5.17.22.1.2-01** `[JS]` `[Globale]` Tutte le frequenze di refresh dei display dinamici devono essere centralizzate in `config.js` (`refreshRates`), configurabili per programma/vista senza modifiche al codice.

#### 5.17.22.1.2.1 – Congelamento del display
*Sintesi (shall):* Deve essere fornita una modalità di congelamento (freeze) per visualizzare qualsiasi frame selezionato aggiornato/avanzato automaticamente dal sistema.

- **IMPL-5.17.22.1.2.1-01** `[HTML][JS]` `[Main]` Un controllo `#btn-freeze-display` (`aria-pressed`) deve sospendere l'aggiornamento automatico del display dinamico (`state.js: isFrozen`) mantenendo visibile il frame corrente.

#### 5.17.22.1.2.2 – Ripresa dal congelamento
*Sintesi (shall):* Deve essere fornita un'opzione a singola azione per riprendere dal punto di congelamento o dal punto in tempo reale corrente.

- **IMPL-5.17.22.1.2.2-01** `[HTML][JS]` `[Main]` Due controlli a singola azione, `#btn-resume-freeze-point` e `#btn-resume-realtime`, devono riprendere rispettivamente dal frame congelato o dal punto live corrente.

#### 5.17.22.1.2.3 – Divieto di congelamento del display
*Sintesi (shall):* L'utente deve essere impedito dall'entrare in modalità freeze quando ciò avrebbe un effetto negativo sulla missione svolta dal sistema.

- **IMPL-5.17.22.1.2.3-01** `[JS]` `[Main]` `#btn-freeze-display` deve essere disabilitato (`disabled`/`aria-disabled="true"`) quando `state.js` segnala una condizione operativa critica incompatibile con il freeze (valore di progetto in `config.js`).

#### 5.17.22.1.2.4 – Feedback di congelamento
*Sintesi (shall):* Un'etichetta deve ricordare all'utente quando il display è in modalità freeze.

- **IMPL-5.17.22.1.2.4-01** `[HTML][CSS]` `[Main]` Quando `state.js: isFrozen` è vero, deve apparire un'etichetta persistente `.freeze-label` (es. “CONGELATO”) sovrapposta al display interessato, con contrasto conforme a `IMPL-5.2.2.7-01`.

#### 5.17.22.1.2.5 – Tempestività
*Sintesi (shall):* I display che richiedono informazioni aggiornate (es. head-up display) devono essere aggiornati in modo sincrono.

- **IMPL-5.17.22.1.2.5-01** `[JS]` `[Main]` I display sincroni devono aggiornarsi tramite un unico ciclo `requestAnimationFrame`/timer centralizzato in `state.js`, evitando aggiornamenti asincroni indipendenti tra componenti correlati.

#### 5.17.22.1.2.6 – Avviso e allerta
*Sintesi (shall):* I dispositivi che visualizzano informazioni simultanee e integrate (es. display multifunzione) devono allertare/segnalare al personale operativo le informazioni prima che diventino critiche nel display.

- **IMPL-5.17.22.1.2.6-01** `[JS]` `[Main]` `notify.js` deve monitorare le soglie dei valori visualizzati e generare un avviso anticipato (classe `.is-advisory`/`--color-advisory`) prima che un valore raggiunga la soglia critica configurata in `config.js`.

#### 5.17.22.2.1 – Copia cartacea
*Sintesi (shall):* Il sistema deve fornire la capacità per l'utente di ottenere una copia cartacea del contenuto esatto del display alfanumerico o grafico digitale.

- **IMPL-5.17.22.2.1-01** `[HTML][JS]` `[Main]` Un controllo `#btn-print-display` deve invocare `window.print()` su un `@media print` dedicato che riproduce fedelmente il contenuto corrente del display (dati, non solo screenshot).

#### 5.17.22.2.2 – Stampa del display
*Sintesi (shall):* Il sistema deve fornire la capacità per l'utente di stampare una schermata con una semplice richiesta (es. PRINT-SCREEN).

- **IMPL-5.17.22.2.2-01** `[HTML][JS]` `[Main]` `#function-keys` deve includere un tasto funzione “Stampa schermata” a singola azione, mappato a `#btn-print-display` (vedi `IMPL-5.17.22.2.1-01`).

#### 5.17.22.2.3 – Stampa pagina
*Sintesi (shall):* Il sistema deve fornire la capacità di richiedere la stampa di una singola pagina o sequenza di pagine specificando i numeri di pagina, o di stampare tutte le pagine senza specificarli.

- **IMPL-5.17.22.2.3-01** `[HTML][JS]` `[Main]` La finestra di dialogo `#print-options-dialog` (su `<dialog>`, gestita da `dialog.js`) deve includere campi `page-range` (es. “1-3,5”) e opzione “Tutte le pagine”.

#### 5.17.22.2.4 – Testo visualizzato come stampato
*Sintesi (shall):* Il sistema deve permettere di visualizzare il testo come sarà stampato, incluse sottolineatura, grassetto, apice, pedice, caratteri/simboli speciali e stili/dimensioni diverse.

- **IMPL-5.17.22.2.4-01** `[CSS][JS]` `[Main]` Una vista “anteprima di stampa” (`.print-preview`) deve applicare gli stessi stili `@media print` usati in stampa (grassetto, corsivo, apice/pedice, dimensioni) prima dell'invio effettivo.

#### 5.17.22.2.5 – Opzioni di stampa flessibili
*Sintesi (shall):* Nella stampa del testo, il sistema deve permettere di scegliere tra i formati di output disponibili (interlinea, dimensione carattere, margini, intestazioni, piè di pagina) e specificare le pagine da stampare.

- **IMPL-5.17.22.2.5-01** `[HTML][JS]` `[Main]` `#print-options-dialog` deve includere controlli per interlinea, dimensione carattere, margini e intestazione/piè di pagina, applicati al foglio di stile `@media print` prima della stampa.

#### 5.17.22.3.1 – Integrazione funzionale
*Sintesi (shall):* Le funzioni di trasmissione dati devono essere integrate con le altre funzioni di gestione informazioni, cosicché l'utente trasmetta dati con lo stesso sistema/procedure usati per l'immissione, visualizzazione ed elaborazione generale.

- **IMPL-5.17.22.3.1-01** `[JS]` `[Globale]` La trasmissione messaggi (`net.js: sendMessage()`) deve riusare le stesse viste/form di immissione e visualizzazione dati dell'applicazione, senza un sottosistema separato con UI incoerente.

#### 5.17.22.3.2 – Procedure coerenti
*Sintesi (shall):* Le procedure per preparare, inviare e ricevere dati/messaggi devono essere coerenti da una transazione all'altra e con le procedure delle altre attività di gestione informazioni.

- **IMPL-5.17.22.3.2-01** `[HTML][CSS]` `[Main]` Il form di composizione messaggio (`#message-compose-form`) deve seguire gli stessi pattern di layout, validazione (`validate.js`) e conferma (`dialog.js`) degli altri form applicativi.

#### 5.17.22.3.3 – Carico di memoria minimo per l'utente
*Sintesi (shall):* Il sistema deve fornire ausili informatici per l'inserimento automatico di informazioni standard (es. intestazioni, liste di distribuzione) per minimizzare il carico di memoria dell'utente.

- **IMPL-5.17.22.3.3-01** `[JS]` `[Main]` `#message-compose-form` deve precompilare automaticamente intestazione, mittente e lista di distribuzione predefinita da `state.js`/`config.js`, editabili ma non da reinserire manualmente.

#### 5.17.22.3.4 – Messaggi
*Sintesi (shall):* I messaggi devono rispettare i criteri di 5.17.22.3.4.1–5.17.22.3.4.8 (introduzione).

- **IMPL-5.17.22.3.4-01** `[Globale]` Nessun requisito implementativo autonomo; vedi `IMPL-5.17.22.3.4.1-01` … `IMPL-5.17.22.3.4.8-01`.

#### 5.17.22.3.4.1 – Moduli di messaggio precaricati
*Sintesi (shall):* Dove i formati di messaggio sono conformi a uno standard o prevedibili, devono essere forniti moduli precaricati per facilitare la preparazione.

- **IMPL-5.17.22.3.4.1-01** `[HTML][JS]` `[Main]` Un selettore `#message-template-select` deve precaricare moduli standard (`config.js: messageTemplates`) in `#message-compose-form`.

#### 5.17.22.3.4.2 – Incorporare file esistenti
*Sintesi (shall):* L'utente deve poter incorporare un file di dati esistente in un messaggio, combinare più file in un unico messaggio e combinare dati memorizzati con nuovi dati.

- **IMPL-5.17.22.3.4.2-01** `[HTML][JS]` `[Main]` `#message-compose-form` deve includere un controllo di allegato (`<input type="file" multiple>`) che consente di combinare file esistenti con il testo del messaggio tramite `net.js`.

#### 5.17.22.3.4.3 – Non reinserire dati
*Sintesi (shall):* Non deve essere necessario reinserire dati già immessi per altri scopi.

- **IMPL-5.17.22.3.4.3-01** `[JS]` `[Main]` Il form di composizione deve leggere da `state.js` i campi già disponibili (es. coordinate, ID stazione) e popolarli automaticamente invece di richiederne la digitazione.

#### 5.17.22.3.4.4 – Interruzione
*Sintesi (shall):* L'utente deve poter interrompere la preparazione, revisione o disposizione del messaggio e riprendere da dove interrotto.

- **IMPL-5.17.22.3.4.4-01** `[JS]` `[Main]` Lo stato di bozza del messaggio deve essere salvato automaticamente (`state.js: draftMessage`, debounce) così che chiudendo e riaprendo `#message-compose-form` si riprenda dal punto di interruzione.

#### 5.17.22.3.4.5 – Etichette del gruppo dati
*Sintesi (shall):* Ogni gruppo di dati o messaggio deve contenere un titolo descrittivo, frase o parola che ne designi il contenuto.

- **IMPL-5.17.22.3.4.5-01** `[HTML]` `[Main]` Ogni messaggio/gruppo dati deve avere un campo obbligatorio `subject`/`title` (validato da `validate.js`) visualizzato come intestazione nelle liste messaggi.

#### 5.17.22.3.4.6 – Posizione delle etichette dei messaggi
*Sintesi (shall):* Le etichette devono essere posizionate in modo coerente adiacenti (preferibilmente sopra o a sinistra) al gruppo dati/messaggio che descrivono.

- **IMPL-5.17.22.3.4.6-01** `[CSS]` `[Main]` Le etichette dei campi messaggio devono usare un layout coerente `label { grid-column: 1; }` posizionate sopra o a sinistra del relativo controllo in tutta l'applicazione.

#### 5.17.22.3.4.7 – Messaggi di emergenza o critici
*Sintesi (shall):* Le etichette devono usare una tecnica di accentuazione diversa e facilmente distinguibile da quella usata per evidenziare/codificare messaggi di emergenza o critici.

- **IMPL-5.17.22.3.4.7-01** `[CSS]` `[Main]` Le etichette dei messaggi ordinari (`.message-label`) devono avere stile distinto (peso, colore) da `.message-label--critical` usata per messaggi di emergenza, evitando ambiguità visiva.

#### 5.17.22.3.4.8 – Identificazione del mittente
*Sintesi (shall, salvo broadcast):* Tranne per sistemi di comunicazione broadcast, il mittente di ogni messaggio deve essere automaticamente identificato, se possibile.

- **IMPL-5.17.22.3.4.8-01** `[JS]` `[Main]` `net.js` deve allegare automaticamente l'identità utente corrente (`state.js: currentUser`) a ogni messaggio inviato tramite `#message-compose-form`, salvo quando il tipo messaggio è marcato come broadcast.

#### 5.17.22.3.5 – Indirizzi
*Sintesi (shall):* Quando l'utente deve specificare l'indirizzo per i messaggi, deve essere fornito un prompting che lo guidi nel processo.

- **IMPL-5.17.22.3.5-01** `[HTML][JS]` `[Main]` Il campo destinatario deve usare `<input list="address-directory">`/combobox con suggerimenti in tempo reale (`validate.js`) man mano che l'utente digita.

#### 5.17.22.3.6 – Rubriche
*Sintesi (shall):* L'utente deve disporre di una rubrica integrata che mostri tutte le forme accettabili di indirizzamento messaggi per ogni destinazione nel sistema e per i collegamenti a sistemi esterni.

- **IMPL-5.17.22.3.6-01** `[HTML][JS]` `[Main]` Una vista `#address-directory` deve elencare tutte le destinazioni valide (interne ed esterne) con le relative forme di indirizzo accettate, alimentata da `net.js`.

#### 5.17.22.3.6.1 – Ausili informatici
*Sintesi (shall):* Devono essere forniti ausili informatici per cercare una rubrica specificando un nome completo o parziale.

- **IMPL-5.17.22.3.6.1-01** `[JS]` `[Main]` `#address-directory` deve offrire un campo di ricerca con filtro incrementale (nome completo/parziale) su `input`, senza submit esplicito.

#### 5.17.22.3.6.2 – Estrazione indirizzi
*Sintesi (shall):* L'utente deve poter estrarre indirizzi selezionati da una rubrica per l'inserimento diretto in un'intestazione, al fine di specificare le destinazioni di un messaggio.

- **IMPL-5.17.22.3.6.2-01** `[JS]` `[Main]` La selezione di una o più voci in `#address-directory` deve offrire un'azione “Aggiungi a destinatari” che inserisce l'indirizzo nel campo destinatario di `#message-compose-form`.

#### 5.17.22.4.1 – Uso
*Sintesi (shall):* I display geografici devono essere usati solo per dati georeferenziabili.

- **IMPL-5.17.22.4.1-01** `[JS]` `[Main]` Il componente mappa (`#geo-display`) deve essere renderizzato solo per dataset che includono coordinate geografiche valide (`validate.js`), come criterio architetturale verificabile per ispezione del codice.

#### 5.17.22.4.2 – Tipi
*Sintesi:* I display di coordinate geografiche editabili consentono l'inserimento in uno di più sistemi (decimali, MGRS, DMS); i display non editabili sono valori generati dal sistema nel formato di coordinate selezionato.

- **IMPL-5.17.22.4.2-01** `[HTML][JS]` `[Main]` Un campo coordinate editabile `#geo-coord-input` deve accettare e validare (`validate.js`) i formati decimale, MGRS e DMS, selezionabili via `<select name="coord-system">`.
- **IMPL-5.17.22.4.2-02** `[JS]` `[Main]` I display di sola lettura (`.geo-coord-readonly`) devono formattare i valori generati dal sistema nel formato di coordinate correntemente selezionato dall'utente, senza consentirne la modifica diretta.

#### 5.17.22.4.3 – Sistemi di coordinate geografiche predefiniti
*Sintesi (should):* I display geospaziali dovrebbero avere l'opzione di salvare un sistema di coordinate geografiche predefinito (es. decimale, MGRS, DMS).

- **IMPL-5.17.22.4.3-01** `[JS]` `[Main]` `prefs.js` dovrebbe memorizzare `defaultCoordSystem` scelto dall'utente e applicarlo come preselezione in `#geo-coord-input` e nei display di sola lettura.

#### 5.17.22.4.4 – Opzioni del display geospaziale
*Sintesi (should):* Per personalizzazione/decluttering, dovrebbe essere disponibile un elenco di opzioni sugli elementi visivi: a. visibilità commutabile; b. opacità; c. precedenza di impilamento; d. raggruppamento gerarchico.

- **IMPL-5.17.22.4.4-01** `[HTML][JS]` `[Main]` Un pannello `#geo-layers-panel` in `#aux-region` deve elencare gli overlay/elementi del display geospaziale con controlli `aria-pressed` per mostrare/nascondere (punto a).
- **IMPL-5.17.22.4.4-02** `[HTML][JS]` `[Main]` Ogni voce di `#geo-layers-panel` deve includere uno slider di opacità (`<input type="range">`) (punto b).
- **IMPL-5.17.22.4.4-03** `[HTML][JS]` `[Main]` `#geo-layers-panel` deve supportare il riordino per precedenza (drag o pulsanti su/giù) che aggiorna lo `z-order` di rendering (punto c).
- **IMPL-5.17.22.4.4-04** `[HTML][JS]` `[Main]` `#geo-layers-panel` deve supportare il raggruppamento gerarchico di elementi/livelli, applicando mostra/nascondi/opacità/precedenza anche al gruppo (punto d).

#### 5.17.22.4.5 – Disegni sul display geospaziale
*Sintesi (should):* Se l'utente può disegnare sul display geospaziale: a. manipolazione (spostare, ridimensionare con aspect ratio bloccabile, eliminare); b. zoom/pan durante il disegno; c. editing delle proprietà di disegno (colore, opacità, etichettatura) prima e dopo il disegno.

- **IMPL-5.17.22.4.5-01** `[JS]` `[Main]` I disegni su `#geo-display` dovrebbero essere spostabili, ridimensionabili (con opzione “blocca proporzioni”) ed eliminabili dopo il posizionamento (punto a), riusando `IMPL-5.17.21.1.8-01`.
- **IMPL-5.17.22.4.5-02** `[JS]` `[Main]` Durante il disegno su `#geo-display` deve restare possibile zoomare/pannare la mappa sottostante senza interrompere lo strumento di disegno attivo (punto b).
- **IMPL-5.17.22.4.5-03** `[HTML][JS]` `[Main]` Un pannello `#drawing-props-panel` dovrebbe permettere di modificare colore, opacità ed etichetta del disegno sia prima sia dopo la sua creazione (punto c).

#### 5.17.22.4.6 – Posizionamento relativo
*Sintesi (should):* I disegni dovrebbero poter essere allineati rispetto ad altri elementi visivi, come altri disegni o un overlay a griglia.

- **IMPL-5.17.22.4.6-01** `[JS]` `[Main]` Lo strumento di disegno su `#geo-display` dovrebbe offrire snapping di allineamento (bordi, centro, griglia) rispetto ad altri elementi visivi, con tolleranza configurabile in `config.js`.

### 3.13 Leggibilità del display, contrasto e adattamento a schermi piccoli

Requisiti sulla resa visiva dell'HMI web (contrasto, polarità, regolazioni utente del display, adattamento a schermi piccoli), applicati tramite i temi `data-theme` e i token `--contrast-*`, `--font-family-ui` di §2.4 e gestiti da `prefs.js`.

#### 5.2.1.2.3 – Controllo della luminanza
*Sintesi (shall):* La luminanza deve essere regolabile affinché il contenuto del display sia visibile e utilizzabile sull'intero intervallo di illuminazione ambientale previsto.

- **IMPL-5.2.1.2.3-01** `[HTML][JS]` `[Globale]` Un controllo `#brightness-control` (slider) nel pannello impostazioni display deve regolare una variabile CSS `--display-brightness` applicata come `filter: brightness()` sul documento, persistita da `prefs.js`.

#### 5.2.1.3 – Contrasto del display
*Sintesi (shall):* Il contrasto del display deve essere tale che il contenuto sia visibile sull'intero intervallo di illuminazione ambientale previsto.

- **IMPL-5.2.1.3-01** `[CSS][JS]` `[Globale]` Il tema `theme-high-contrast` (token `--contrast-*`) deve essere selezionabile da `#btn-display-settings` per garantire visibilità in condizioni di forte illuminazione ambientale o riflessi.

#### 5.2.2.6 – Polarità dell'immagine
*Sintesi (shall):* Per mantenere l'adattamento al buio, i display devono usare caratteri chiari su sfondo scuro quando l'illuminazione ambientale è < ~0,1 lx; altrimenti caratteri scuri su sfondo chiaro.

- **IMPL-5.2.2.6-01** `[CSS][JS]` `[Globale]` I temi `theme-light`/`theme-dark` (attributo `data-theme` su `<html>`) devono implementare rispettivamente polarità scura-su-chiara e chiara-su-scura; `prefs.js` deve permettere selezione manuale e, se disponibile un sensore di luce ambientale, proporre il tema `dark` sotto la soglia di 0,1 lx.

#### 5.2.2.7 – Rapporto di contrasto
*Sintesi (shall):* Il rapporto di contrasto tra caratteri e sfondo deve essere ≥6:1 (preferibile ≥10:1); c. deve essere fornito un controllo per variare il contrasto tra caratteri e sfondo.

- **IMPL-5.2.2.7-01** `[CSS]` `[Globale]` Tutte le combinazioni testo/sfondo definite nei token `--color-*` devono garantire un rapporto di contrasto ≥6:1 (obiettivo ≥10:1), verificato per ispezione/test automatico (contrast checker) su ogni tema.
- **IMPL-5.2.2.7-02** `[HTML][JS]` `[Globale]` Un controllo `#contrast-control` (slider o selezione predefinita) nel pannello impostazioni display deve permettere all'utente di variare il contrasto testo/sfondo, applicando la variabile `--contrast-level` e persistendo la scelta via `prefs.js` (punto c).

#### 5.2.2.10.2 – Adattamento della formattazione
*Sintesi (should):* Quando si converte un display da dimensione tradizionale a schermo piccolo, il design dovrebbe adattarsi (non semplicemente copiare) il design di sistema corrente.

- **IMPL-5.2.2.10.2-01** `[CSS]` `[Globale]` Al breakpoint **S** (800 px, §2.3) il layout deve riorganizzare i contenuti (colonna singola, pannelli a comparsa per `#aux-region`) invece di rimpicciolire proporzionalmente il layout desktop con `transform: scale()`.

#### 5.2.2.10.3 – Caratteristiche del font
*Sintesi (should/shall):* La dimensione del font dovrebbe essere sufficiente a discriminare caratteri simili (es. “I” e “1”, “Z” e “2”) e deve conformarsi ai requisiti di 5.17.18.2.

- **IMPL-5.2.2.10.3-01** `[CSS]` `[Globale]` Il font `--font-family-ui` deve garantire glifi distinguibili per caratteri simili (I/1, Z/2, O/0); la dimensione minima deve rispettare `--fs-body`/`--fs-small` come da `IMPL-5.17.18.2-*` (vedi sezione dedicata alla dimensione dei caratteri).

#### 5.2.2.10.3.1 – Font standard
*Sintesi (should):* Dovrebbe essere usato un font standard comune (es. Arial, Times New Roman, Courier o Verdana).

- **IMPL-5.2.2.10.3.1-01** `[CSS]` `[Globale]` `--font-family-ui` deve usare uno stack di font standard (`Arial, Verdana, Helvetica, sans-serif`), coerente in tutta l'applicazione senza eccezioni locali non giustificate.

#### 5.2.2.10.3.2 – Sans serif
*Sintesi (shall):* Dove gli utenti devono leggere rapidamente i display in condizioni avverse (es. scarsa illuminazione), deve essere usato uno stile sans serif (es. Arial, Verdana o Helvetica).

- **IMPL-5.2.2.10.3.2-01** `[CSS]` `[Globale]` `--font-family-ui` deve rimanere sans-serif (`Arial, Verdana, Helvetica`) per tutto il testo operativo dell'interfaccia, senza introdurre famiglie serif nei display primari.

#### 5.2.2.10.4 – Organizzazione del testo
*Sintesi (shall):* Per aumentare la capacità di scorrimento rapido del testo, sottotitoli e hypertext devono essere usati nei passaggi di testo più lunghi di una singola schermata.

- **IMPL-5.2.2.10.4-01** `[HTML][CSS]` `[Main]` I contenuti testuali più lunghi di una schermata (es. help, documentazione in `#aux-region`) devono usare sottotitoli (`<h2>`/`<h3>`) e collegamenti ipertestuali interni (`<a href="#...">`) per la navigazione rapida.

#### 5.2.2.10.4.1 – Righe di lunghezza variabile
*Sintesi (shall):* Le righe di lunghezza variabile devono essere evitate tramite sillabazione delle parole agli a-capo, per migliorare la leggibilità su schermi piccoli.

- **IMPL-5.2.2.10.4.1-01** `[CSS]` `[Globale]` I blocchi di testo devono applicare `hyphens: auto; overflow-wrap: break-word;` con `lang` impostato correttamente (per regole di sillabazione), specialmente ai breakpoint **S**/**M**.

#### 5.2.2.10.4.2 – Indicatori di scorrimento
*Sintesi (shall):* Quando il contenuto non può essere visualizzato in una sola schermata, devono essere forniti indicatori di scorrimento per far identificare all'utente la propria posizione nella pagina.

- **IMPL-5.2.2.10.4.2-01** `[CSS][JS]` `[Main]` Le aree scrollabili (`#main-panel`, `#aux-region`) devono mostrare indicatori di posizione di scorrimento (es. scrollbar persistente stilizzata, `.scroll-progress` o ombre di overflow) aggiornati dinamicamente durante lo scroll.

### 3.14 Automazione e controllo di supervisione

Si applica quando la web HMI supervisiona o controlla funzioni automatizzate. I requisiti di processo/principio generale sono tradotti in vincoli architetturali su `state.js` (macchina a stati del livello di automazione), `notify.js`/`feedback.js` (segnalazione cambi di stato e feedback) e `dialog.js` (conferme, override, spiegazioni degli ausili decisionali).

#### 5.12.3.1.2.1 – Miglioramento delle prestazioni
*Sintesi (shall):* Le funzioni devono essere automatizzate solo se migliorano le prestazioni, la sicurezza o il carico di lavoro dell'utente/sistema.

- **IMPL-5.12.3.1.2.1-01** `[JS]` `[Globale]` Ogni funzione di automazione registrata in `config.js` (`automationFeatures`) deve includere un campo `rationale` (motivazione prestazioni/sicurezza/carico) non vuoto; test automatico (T) verifica la presenza del campo per ogni feature attiva.

#### 5.12.3.1.2.2 – Centratura sull'utente
*Sintesi (shall):* L'automazione deve supportare l'utente dove appropriato (human-centered) e non essere introdotta solo perché disponibile (technology-centered).

- **IMPL-5.12.3.1.2.2-01** `[JS]` `[Globale]` `config.js` espone per ciascuna `automationFeature` un flag `humanCentered:boolean`; in revisione di progetto (criterio di accettazione D) si verifica che nessuna feature sia attivata senza giustificazione human-centered documentata.

#### 5.12.3.2.1 – Utente al comando
*Sintesi (shall):* I sistemi automatizzati non devono rimuovere l'utente dal ruolo di comando.

- **IMPL-5.12.3.2.1-01** `[JS]` `[Globale]` `state.js` mantiene `controlMode` (`manual`/`supervised`/`auto`); nessuna funzione primaria deve risultare eseguibile solo in `auto` senza un percorso manuale equivalente raggiungibile da `#function-keys`.
- **IMPL-5.12.3.2.1-02** `[HTML]` `[Footer]` Il controllo manuale corrispondente resta sempre presente nel DOM (non `display:none`) anche quando l'automazione è attiva, eventualmente disabilitato con `[aria-disabled]` e motivazione in tooltip.

#### 5.12.3.2.2 – Coinvolgimento dell'utente
*Sintesi (shall):* L'utente deve avere un ruolo attivo tramite compiti pertinenti e significativi, qualunque sia il livello di automazione.

- **IMPL-5.12.3.2.2-01** `[JS]` `[Main]` `#aux-region` presenta una lista di compiti di supervisione pendenti (conferme, verifiche) generata da `state.js`, aggiornata indipendentemente dal livello di automazione corrente.

#### 5.12.3.2.3 – Consapevolezza della situazione
*Sintesi (shall):* Il sistema deve fornire all'utente consapevolezza dello stato del sistema (posizione, stato), capacità (salute, prontezza) e, ove possibile, proiezione dello stato futuro in modo tempestivo.

- **IMPL-5.12.3.2.3-01** `[JS]` `[Header][Main]` `state.js` espone `systemState` (stato, capacità, prontezza); `#mode-indicator` e `#content-region` lo riflettono con aggiornamento entro i tempi di Tabella V (locale ≤0,5 s, da host ≤2,0 s) tramite `net.js`.
- **IMPL-5.12.3.2.3-02** `[JS]` `[Main]` Pannello di proiezione opzionale in `#aux-region` (vedi 5.12.3.2.3.3).

#### 5.12.3.2.3.1 – Parametri di stato del sistema
*Sintesi (shall):* Il sistema deve fornire parametri di stato appropriati al ruolo e al compito corrente dell'utente.

- **IMPL-5.12.3.2.3.1-01** `[JS]` `[Main]` `state.js` filtra l'insieme di parametri renderizzati in `#content-region` in base a `state.currentUser.role`, definito in `config.js` (`roleParameterSets`).

#### 5.12.3.2.3.2 – Situazione corrente
*Sintesi (shall):* Il sistema deve fornire la situazione corrente nel contesto funzionale e di missione appropriato al ruolo/compito.

- **IMPL-5.12.3.2.3.2-01** `[HTML][JS]` `[Main]` `#content-region` include un pannello contestuale legato a `state.missionContext`, aggiornato da `net.js`; contenuto scelto in base a ruolo e compito attivo.

#### 5.12.3.2.3.3 – Stati futuri del sistema
*Sintesi (should):* Il sistema dovrebbe fornire una proiezione degli stati futuri probabili basata sulla situazione corrente.

- **IMPL-5.12.3.2.3.3-01** `[JS]` `[Main]` Widget "Stato previsto" in `#aux-region`, attivabile da `config.js` (`enablePredictivePanel`), alimentato da `state.js`/`net.js`.

#### 5.12.3.2.4 – Prevedibilità
*Sintesi (shall):* I sistemi automatizzati devono comportarsi in modo prevedibile affinché l'utente comprenda lo scopo dell'automazione e l'effetto delle sue operazioni.

- **IMPL-5.12.3.2.4-01** `[JS]` `[Globale]` `notify.js` pubblica una notifica descrittiva prima/al momento di ogni azione automatica rilevante (cosa sta per accadere/è accaduto); le transizioni di stato sono deterministiche e coperte da test automatico (T).

#### 5.12.3.2.5 – Coerenza
*Sintesi (shall):* L'interazione dei sistemi automatizzati con gli utenti deve riflettere un alto grado di coerenza entro e tra i sistemi.

- **IMPL-5.12.3.2.5-01** `[CSS][JS]` `[Globale]` I controlli di automazione riusano gli stessi pattern (classi, token `--color-*`, componenti `dialog.js`) in tutte le viste; test di regressione (T) confronta markup/id tra le diverse viste con funzioni analoghe.

#### 5.12.3.2.6 – Intuitività
*Sintesi (should):* I sistemi automatizzati e i relativi display dovrebbero essere facili da comprendere, imparare, usare e soddisfare le aspettative dell'utente.

- **IMPL-5.12.3.2.6-01** `[HTML][CSS]` `[Main]` Icone e etichette standard (`i18n.js`) per i controlli di automazione, con tooltip/aiuto contestuale in `#aux-region`; criterio di accettazione tramite dimostrazione (D) con utenti rappresentativi.

#### 5.12.3.2.7 – Input e impostazione
*Sintesi (shall):* I sistemi automatizzati devono fornire un modo per verificare la configurazione dell'automazione e i dati usati come input.

- **IMPL-5.12.3.2.7-01** `[HTML][JS]` `[Main]` `dialog.js` apre una finestra "Verifica configurazione" che mostra i parametri/input correnti dell'automazione prima dell'attivazione, con azione di conferma esplicita.

#### 5.12.3.2.9 – Sistemi multipli
*Sintesi (shall):* I progetti con sistemi multipli devono soddisfare i criteri 5.12.3.2.9.1 e 5.12.3.2.9.2.

- **IMPL-5.12.3.2.9-01** `[JS]` `[Globale]` Vincolo architetturale: tutte le build/piattaforma condividono lo stesso set di componenti, token e dizionario (vedi `IMPL-5.12.3.2.9.1-01`, `IMPL-5.12.3.2.9.2-01`); test di configurazione verifica l'assenza di varianti locali non documentate.

#### 5.12.3.2.9.1 – Controlli e display
*Sintesi (shall):* Controlli e display devono essere standardizzati tra le diverse piattaforme controllate dagli stessi utenti, inclusi quelli riassegnati.

- **IMPL-5.12.3.2.9.1-01** `[HTML][CSS]` `[Globale]` Libreria di componenti condivisa (stessi `id`/classi/token `--touch-target-min`, `--color-*`) riusata su tutte le piattaforme; test di regressione visiva confronta il DOM tra build.

#### 5.12.3.2.9.2 – Terminologia
*Sintesi (shall):* La terminologia deve essere standardizzata tra le piattaforme controllate dagli stessi utenti.

- **IMPL-5.12.3.2.9.2-01** `[JS]` `[Globale]` Dizionario terminologico unico in `i18n.js` condiviso da tutte le build/piattaforme; step di build fallisce se una piattaforma definisce chiavi duplicate/divergenti.

#### 5.12.3.2.10.1 – Ripristino
*Sintesi (shall):* L'automazione deve mantenere un coinvolgimento sufficiente dell'utente per consentire un ripristino sicuro in emergenza o il funzionamento manuale in caso di guasto dell'automazione.

- **IMPL-5.12.3.2.10.1-01** `[HTML][JS]` `[Main]` `#alert-region` mostra banner "Automazione non disponibile – passare a manuale" con `role="alert"`; il percorso di comando manuale equivalente resta sempre disponibile in `#content-region`/`#function-keys`, gestito da `state.js` (fallback).

#### 5.12.3.2.10.3 – Capacità di override
*Sintesi (shall):* All'utente deve essere fornita la capacità di annullare, spegnere o neutralizzare il sistema automatizzato.

- **IMPL-5.12.3.2.10.3-01** `[HTML][CSS]` `[Footer]` Pulsante persistente "Override"/"Arresta automazione" in `#function-keys`, dimensione ≥ `--touch-target-min`, sempre visibile durante il controllo automatico.
- **IMPL-5.12.3.2.10.3-02** `[JS]` `[Globale]` Attivazione richiede conferma (`dialog.js`), produce `feedback.js.acknowledge()` e viene registrata da `log.js`.

#### 5.12.3.3.2 – Azioni/decisioni ad alto rischio
*Sintesi (shall):* Per compiti con maggiore incertezza/rischio, l'automazione non deve superare il livello di raccomandazione di una decisione/azione preferita.

- **IMPL-5.12.3.3.2-01** `[JS]` `[Main]` Per i compiti con `config.js riskLevel:'high'`, `state.js` vincola `automationLevel` al massimo a `'recommend'`; l'esecuzione richiede sempre conferma utente tramite `dialog.js`, mai avvio automatico.

#### 5.12.3.4.1 – Relazione con il compito dell'utente
*Sintesi (should):* Le relazioni tra display, controlli, ausili decisionali, struttura informativa e compiti dell'utente dovrebbero essere chiare.

- **IMPL-5.12.3.4.1-01** `[HTML]` `[Main]` Attributo `data-task` comune su controlli, indicatori e ausili decisionali correlati in `#content-region`/`#aux-region`, con collegamento visivo/testuale esplicito nell'aiuto contestuale.

#### 5.12.3.4.2 – Ruoli e responsabilità chiari
*Sintesi (shall):* Il sistema automatizzato deve indicare chiaramente se spetta all'utente o al sistema eseguire un compito in un dato momento.

- **IMPL-5.12.3.4.2-01** `[HTML][CSS]` `[Header]` `#mode-indicator` mostra per compito un badge testo+icona ("Manuale"/"Automatico") secondo `state.js taskOwner`, con doppia codifica colore/icona/testo.

#### 5.12.3.4.2.1 – Cambi di stato
*Sintesi (shall):* L'interfaccia deve indicare chiaramente tutti i cambi di stato e di livello di automazione, specialmente in caso di reversione di modalità per guasto o cambio operatore.

- **IMPL-5.12.3.4.2.1-01** `[JS]` `[Header][Main]` Ogni variazione di `state.automationLevel` genera un toast (`notify.js`), un banner in `#alert-region` se dovuta a guasto, segnale sonoro `feedback.js.beep('mode-change')` e voce in `log.js`.

#### 5.12.3.4.2.2 – Appropriato al livello/modalità di controllo
*Sintesi (shall):* Gli elementi informativi appropriati al livello/modalità di controllo devono essere resi salienti quando cambia l'allocazione di funzione o il livello di automazione.

- **IMPL-5.12.3.4.2.2-01** `[CSS][JS]` `[Main]` `#content-region` applica classi `.mode-manual`/`.mode-supervised`/`.mode-auto` che mostrano/enfatizzano (colore `--color-advisory`, ordine) i widget pertinenti al livello corrente.

#### 5.12.3.4.2.3 – Ritorno al controllo manuale
*Sintesi (should):* Quando l'automazione determina che l'operatore dovrebbe tornare al controllo manuale, il sistema dovrebbe fornire indicazioni chiare delle informazioni e azioni richieste.

- **IMPL-5.12.3.4.2.3-01** `[HTML][JS]` `[Main][Footer]` Banner `#alert-region` "Richiesta ripresa controllo manuale" con elenco puntato delle azioni richieste; pulsante dedicato "Assumi controllo" in `#function-keys`.

#### 5.12.3.4.3 – Feedback continuo
*Sintesi (should):* Il sistema dovrebbe fornire un livello di feedback appropriato su controllo, stato e output in condizioni normali e di malfunzionamento.

- **IMPL-5.12.3.4.3-01** `[JS]` `[Footer]` `#status-line` (`role="status"`) aggiornato continuamente da `feedback.js.setStatus()`; i malfunzionamenti generano notifica prioritaria via `notify.js` oltre al normale aggiornamento di stato.

#### 5.12.3.4.4 – Risposta del sistema
*Sintesi (shall):* Le risposte del sistema automatizzato ai comandi utente devono essere brevi e non ambigue, coerenti con i tempi di risposta previsti.

- **IMPL-5.12.3.4.4-01** `[JS]` `[Footer]` Risposte automatiche renderizzate in `#message-line` con testo breve (max ~80 caratteri) entro i tempi di Tabella V (eco ≤0,2 s, aggiornamento locale ≤0,5 s).

#### 5.12.3.4.5 – Visualizzare le conseguenze delle decisioni
*Sintesi (should):* Il sistema automatizzato dovrebbe fornire informazioni per visualizzare l'esito atteso di una decisione, umana o automatica.

- **IMPL-5.12.3.4.5-01** `[HTML][JS]` `[Main]` Pannello "Esito previsto" in `#aux-region`/`dialog.js` mostrato prima della conferma di un'azione decisionale, con sintesi dell'effetto atteso.

#### 5.12.3.4.6 – Coerenza
*Sintesi (should):* Il comportamento dei sistemi automatizzati dovrebbe essere coerente con le aspettative e la comprensione degli utenti.

- **IMPL-5.12.3.4.6-01** `[JS]` `[Globale]` Test di regressione comportamentale (T) confronta gli esiti delle stesse azioni di automazione in contesti diversi, verificando invarianza di comportamento a parità di input.

#### 5.12.3.4.7 – Organizzazione
*Sintesi (should):* Le interfacce di automazione dovrebbero essere organizzate per corrispondere alle aspettative, ai processi di compito e alle capacità del sistema.

- **IMPL-5.12.3.4.7-01** `[HTML][JS]` `[Main]` `#nav-region` raggruppa i controlli di automazione per compito (`nav.js taskGroups`), non per sottosistema tecnico.

#### 5.12.3.4.10 – Tempestività
*Sintesi (shall):* Le informazioni dinamiche devono essere presentate in tempo reale e su richiesta per garantire decisioni accurate e tempestive.

- **IMPL-5.12.3.4.10-01** `[JS]` `[Main]` `net.js` spinge aggiornamenti (WebSocket) a `#content-region` entro i tempi di Tabella V (locale ≤0,5 s, da host ≤2,0 s); pulsante "Aggiorna" per richiesta esplicita sempre disponibile.

#### 5.12.3.4.11 – Verifica degli errori
*Sintesi (should):* Display e dispositivi di immissione dati dovrebbero fornire un mezzo per verificare incrociatamente l'accuratezza dei dati inseriti manualmente.

- **IMPL-5.12.3.4.11-01** `[JS]` `[Main]` `validate.js` espone una funzione di cross-check che confronta il valore immesso con un intervallo atteso/fonte secondaria, impostando `[aria-invalid]` e messaggio in caso di discrepanza.

#### 5.12.3.4.12 – Modalità di automazione
*Sintesi (shall):* Le modalità devono soddisfare i criteri da 5.12.3.4.12.1 a 5.12.3.4.12.7.

- **IMPL-5.12.3.4.12-01** `[JS]` `[Globale]` `state.js` definisce un elenco chiuso ed enumerato di modalità ammesse (`config.js automationModes`); l'interfaccia non può generare/visualizzare modalità non presenti nell'elenco (vincolo verificabile per ispezione I).

#### 5.12.3.4.12.1 – Identificazione
*Sintesi (shall):* Al cambio di funzioni di controllo/display/automazione tra modalità, il sistema deve fornire un'indicazione chiara del cambiamento, modalità corrente, identificazione della funzione e stato.

- **IMPL-5.12.3.4.12.1-01** `[HTML][JS]` `[Header]` `#mode-indicator` con `aria-live="polite"` aggiorna testo+icona+nome funzione+stato ad ogni cambio di modalità.

#### 5.12.3.4.12.2 – Accessibilità
*Sintesi (should):* Le modalità usate più frequentemente dovrebbero essere più accessibili di quelle usate raramente.

- **IMPL-5.12.3.4.12.2-01** `[JS]` `[Footer]` `prefs.js` traccia la frequenza d'uso delle modalità e riordina i controlli rapidi in `#function-keys` di conseguenza.

#### 5.12.3.4.12.3 – Numero di modalità
*Sintesi (should):* Il numero di modalità diverse per un dato sistema dovrebbe essere minimizzato.

- **IMPL-5.12.3.4.12.3-01** `[JS]` `[Globale]` Vincolo di progetto in `config.js` (`automationModes.length` ≤ valore di progetto, es. 5); revisionato in fase di design (criterio I).

#### 5.12.3.4.12.4 – Commutazione
*Sintesi (shall):* L'utente deve poter commutare facilmente tra modalità.

- **IMPL-5.12.3.4.12.4-01** `[HTML][CSS]` `[Header]` Selettore di modalità (`<select id="mode-switch">` o gruppo di pulsanti) sempre visibile in `#app-header`, dimensione ≥ `--touch-target-min`, azione a passo singolo.

#### 5.12.3.4.12.5 – Coerente
*Sintesi (shall):* Le funzioni e caratteristiche comuni tra modalità di display devono essere coerenti.

- **IMPL-5.12.3.4.12.5-01** `[CSS][HTML]` `[Main]` I controlli comuni tra le varianti `.mode-*` condividono lo stesso markup/id/classi; solo lo stato/visibilità cambia tramite CSS, mai la struttura.

#### 5.12.3.4.12.6 – Allerte
*Sintesi (should):* Il sistema automatizzato dovrebbe allertare l'utente quando una modalità può essere potenzialmente pericolosa.

- **IMPL-5.12.3.4.12.6-01** `[JS]` `[Globale]` Modalità marcate `hazardous:true` in `config.js` generano notifica `notify.js` di priorità `warning` alla selezione, prima dell'attivazione.

#### 5.12.3.4.12.7 – Attivazione inavvertita
*Sintesi (should):* Il sistema automatizzato dovrebbe proteggere contro l'attivazione inavvertita di una modalità potenzialmente non sicura.

- **IMPL-5.12.3.4.12.7-01** `[JS]` `[Main]` La commutazione verso una modalità `hazardous:true` richiede conferma esplicita (`dialog.js`), coerente con il pattern anti-attivazione-inavvertita di `IMPL-5.19.1.9.1-01`.

#### 5.12.3.4.13 – Funzioni di monitoraggio
*Sintesi (should):* Il sistema dovrebbe coinvolgere attivamente gli utenti nel controllo e monitoraggio dei sistemi automatizzati, incluso lo stato e le tendenze.

- **IMPL-5.12.3.4.13-01** `[HTML][JS]` `[Main]` `#aux-region` include un pannello di stato/tendenza sempre renderizzato (non nascosto in modalità full-auto), alimentato da `state.js`.

#### 5.12.3.4.13.1 – Dati in cambiamento
*Sintesi (should):* I dati mutevoli da monitorare dovrebbero essere visualizzati in formato grafico.

- **IMPL-5.12.3.4.13.1-01** `[HTML][CSS]` `[Main]` Valori dinamici monitorati resi come sparkline/gauge SVG in `#content-region` anziché solo testo numerico.

#### 5.12.3.4.13.3 – Display multipli
*Sintesi (should):* Quando gli utenti devono monitorare più display, la notifica di eventi importanti dovrebbe avvenire sullo stesso display fisico, nella stessa posizione.

- **IMPL-5.12.3.4.13.3-01** `[CSS]` `[Header][Main]` `#alert-summary` e `#alert-region` sono posizione fissa e coerente (stessa area/coordinate) su tutte le viste tramite `position: sticky`/griglia comune, unico punto centralizzato di notifica.

#### 5.12.3.4.13.4 – Indicazione di monitoraggio
*Sintesi (should):* I sistemi automatizzati senza incidenti per lunghi periodi dovrebbero fornire un'indicazione che l'automazione sta ancora monitorando.

- **IMPL-5.12.3.4.13.4-01** `[JS]` `[Footer]` `#status-line` mostra indicatore "heartbeat" (punto pulsante + timestamp ultimo controllo) aggiornato periodicamente da `net.js`.

#### 5.12.3.4.13.5 – Interazioni utente
*Sintesi (should):* I sistemi automatizzati dovrebbero poter monitorare le interazioni dell'utente e avvisare in caso di errori.

- **IMPL-5.12.3.4.13.5-01** `[JS]` `[Globale]` `validate.js`/`log.js` tracciano pattern di errore ripetuti; `notify.js` avvisa l'utente oltre una soglia configurata in `config.js`.

#### 5.12.3.4.14.4 – Feedback
*Sintesi (shall):* Per favorire la consapevolezza situazionale, all'utente deve essere dato un feedback immediato agli ordini di comando e controllo.

- **IMPL-5.12.3.4.14.4-01** `[JS]` `[Globale]` Ogni comando inviato all'automazione invoca `feedback.js.acknowledge()` (visivo + aptico `navigator.vibrate`) entro ≤0,1 s (Tabella V).

#### 5.12.3.4.14.6 – Override
*Sintesi (should):* Quando l'utente potrebbe dover operare in condizioni fuori tolleranza, dovrebbe essere fornita un'azione di override deliberata.

- **IMPL-5.12.3.4.14.6-01** `[HTML][JS]` `[Main]` Controllo distinto "Forza override" (stile `--color-warning`), separato dall'override generale di `IMPL-5.12.3.2.10.3-01`, con conferma obbligatoria via `dialog.js`.

#### 5.12.3.4.15.1 – Dati incompleti
*Sintesi (shall):* Il sistema deve indicare quando i dati sono incompleti, mancanti, inaffidabili, non validi o quando si basa su dati di backup, con codifica colore ove visualizzato graficamente.

- **IMPL-5.12.3.4.15.1-01** `[HTML][CSS]` `[Main]` Celle/valori interessati ricevono classe `.data-stale`/`.data-invalid`, colore `--color-warning`/`--color-inactive`, icona e `aria-label="dato non affidabile"` (tripla codifica).

#### 5.12.3.4.15.2 – Stato accurato
*Sintesi (shall):* Le informazioni presentate devono riflettere accuratamente lo stato del sistema/ambiente affinché l'utente riconosca, comprenda e proietti rapidamente gli esiti rispetto agli obiettivi.

- **IMPL-5.12.3.4.15.2-01** `[JS]` `[Main]` `#content-region` è sempre vincolato a `state.js` come unica fonte di verità (nessun valore derivato cache non sincronizzato); timestamp dell'ultimo aggiornamento mostrato accanto al valore.

#### 5.12.3.4.15.3 – Presentazione delle informazioni
*Sintesi (should):* Sia il contenuto delle informazioni disponibili tramite automazione sia le modalità di presentazione dovrebbero essere coerenti con le priorità del compito.

- **IMPL-5.12.3.4.15.3-01** `[JS]` `[Main]` Ordine di layout di `#content-region` determinato da una lista di priorità per compito/ruolo in `config.js`.

#### 5.12.3.4.15.4 – Segnalazione delle informazioni importanti
*Sintesi (should):* Quando le informazioni devono essere aggiornate rapidamente, quelle più importanti dovrebbero essere segnalate per essere elaborate per prime dall'utente.

- **IMPL-5.12.3.4.15.4-01** `[JS]` `[Main]` `notify.js` ordina la coda per priorità, mostrando l'allerta più critica in cima con enfasi visiva e `aria-live="assertive"`.

#### 5.12.3.4.15.5 – Evidenziare i dati modificati
*Sintesi (should):* I cambiamenti di dati a seguito di aggiornamento automatico del display dovrebbero essere temporaneamente evidenziati.

- **IMPL-5.12.3.4.15.5-01** `[CSS][JS]` `[Main]` Classe transitoria `.flash-update` (animazione CSS) applicata dinamicamente da JS quando un valore monitorato cambia, rimossa dopo `config.js highlightDurationMs`.

#### 5.12.3.4.15.6 – Memorizzare e dare priorità alle informazioni
*Sintesi (should):* Elenchi lunghi di informazioni e compiti dovrebbero essere memorizzati e ordinati per priorità dall'ausilio automatizzato per ridurre le alternative decisionali e il carico visivo.

- **IMPL-5.12.3.4.15.6-01** `[JS]` `[Main]` Lista compiti/eventi in `#aux-region` ordinata per priorità calcolata in `state.js`, con paginazione/collasso per ridurre il carico visivo.

#### 5.12.3.4.15.7 – Cronologia delle informazioni
*Sintesi (should):* Dovrebbero essere previste disposizioni per visualizzare informazioni storiche che permettano di rivedere i cambiamenti alle informazioni critiche.

- **IMPL-5.12.3.4.15.7-01** `[HTML][JS]` `[Main]` Pulsante/scheda "Storico" in `#aux-region` apre vista timeline alimentata da `log.js` con le variazioni delle informazioni critiche.

#### 5.12.3.4.16.3 – Determinare l'uso dell'ausilio decisionale
*Sintesi (should):* Gli utenti dovrebbero poter determinare quando e come utilizzare l'ausilio decisionale.

- **IMPL-5.12.3.4.16.3-01** `[HTML][JS]` `[Main]` Interruttore "Ausilio decisionale: on/off" in pannello preferenze (`prefs.js`), applicabile per compito.

#### 5.12.3.4.16.4 – Termini e criteri
*Sintesi (shall):* Gli ausili decisionali devono usare terminologia e criteri appropriati al gruppo di utenti target.

- **IMPL-5.12.3.4.16.4-01** `[JS]` `[Globale]` `i18n.js` seleziona il glossario (`config.js userGroupGlossary`) in base al profilo/ruolo dell'utente per i testi generati dall'ausilio.

#### 5.12.3.4.16.6 – Assistere le decisioni dell'utente
*Sintesi (should):* Gli ausili decisionali dovrebbero assistere, non sostituire, il decisore umano fornendo dati per il giudizio anziché comandi da eseguire.

- **IMPL-5.12.3.4.16.6-01** `[HTML][JS]` `[Main]` La raccomandazione è presentata come scheda informativa con dati/motivazione e un pulsante di azione manuale; nessuna esecuzione automatica della decisione.

#### 5.12.3.4.16.8 – Interruzione del compito
*Sintesi (shall):* L'uso di ausili decisionali non deve richiedere l'annullamento dei compiti utente in corso.

- **IMPL-5.12.3.4.16.8-01** `[HTML]` `[Main]` L'ausilio decisionale si apre in `#aux-region` come pannello non modale (non `<dialog>` bloccante), lasciando il flusso di compito in `#content-region` ininterrotto.

#### 5.12.3.4.16.14 – Limite dell'ausilio decisionale
*Sintesi (should):* Gli ausili decisionali dovrebbero avvisare l'utente quando un problema/situazione supera la capacità dell'ausilio.

- **IMPL-5.12.3.4.16.14-01** `[JS]` `[Main]` `notify.js` emette avviso "Analisi oltre i limiti dell'ausilio" quando confidenza/intervallo superano la soglia di `config.js`; evento registrato da `log.js`.

#### 5.12.3.4.16.16 – Stima dell'incertezza
*Sintesi (should):* Gli ausili decisionali dovrebbero stimare e indicare la certezza dell'analisi e fornirne la motivazione.

- **IMPL-5.12.3.4.16.16-01** `[HTML]` `[Main]` La scheda di raccomandazione include percentuale/intervallo di confidenza e breve motivazione testuale.

#### 5.12.3.4.16.17 – Accessibilità dei dati e delle informazioni decisionali
*Sintesi (should):* Le informazioni procedurali, derivate o elaborate e i dati da cui derivano dovrebbero essere visibili o accessibili per la verifica della decisione.

- **IMPL-5.12.3.4.16.17-01** `[HTML][JS]` `[Main]` Sezione espandibile "Mostra dati di origine" nella scheda di raccomandazione, che rivela dati sorgente e passaggi procedurali.

#### 5.12.3.4.16.18 – Registrazione delle decisioni
*Sintesi (shall):* Il sistema deve registrare i dati dell'ausilio decisionale (display, informazioni procedurali, regole, dati, ipotesi testate, riepilogo), con configurabilità del livello di log.

- **IMPL-5.12.3.4.16.18-01** `[JS]` `[Globale]` `log.js` espone `logDecisionAid(snapshot)`; `prefs.js`/`config.js` definiscono `loggingLevel` (`all`/`some`/`none`) selezionabile dall'utente autorizzato.

#### 5.12.3.4.16.19 – Dettaglio della spiegazione della decisione
*Sintesi (should):* Quando il sistema fornisce spiegazioni, dovrebbe offrire inizialmente una spiegazione breve con possibilità di dettaglio su richiesta, incluso accesso a regole/base di conoscenza/soluzioni.

- **IMPL-5.12.3.4.16.19-01** `[HTML][JS]` `[Main]` Pulsante "Perché?" apre pannello (`dialog.js`) con testo breve e sezione "Dettagli" espandibile per regole/base di conoscenza.

#### 5.12.3.4.16.20 – Spiegazioni chiare all'utente
*Sintesi (shall):* Le spiegazioni devono usare termini familiari all'utente e mantenere coerenza con il compito immediato.

- **IMPL-5.12.3.4.16.20-01** `[JS]` `[Globale]` Il testo delle spiegazioni è generato tramite il glossario `i18n.js` comune, senza terminologia divergente dal resto dell'interfaccia.

#### 5.12.3.4.16.22 – Allerta su nuove informazioni disponibili
*Sintesi (shall):* Gli ausili decisionali devono allertare l'utente sui cambiamenti di stato di informazioni di sistema importanti, ad es. quando nuove informazioni critiche diventano disponibili durante l'uso.

- **IMPL-5.12.3.4.16.22-01** `[JS]` `[Main]` `notify.js` osserva le chiavi di stato rilevanti per l'ausilio decisionale e pubblica toast/alert quando cambiano mentre il pannello è aperto.

### 3.15 Sicurezza del software

Requisiti per interfacce utente *safety-critical* (5.7.19), applicabili quando l'HMI può avviare funzioni pericolose o presentare informazioni critiche per la sicurezza; realizzati principalmente tramite `notify.js`, `dialog.js`, `validate.js` e `state.js`.

#### 5.7.19.2.1 – Annullamento del processo corrente
*Sintesi (shall):* Il software deve annullare l'elaborazione corrente con una singola azione utente e far rientrare il sistema in uno stato sicuro predefinito.

- **IMPL-5.7.19.2.1-01** `[HTML][JS]` `[Globale]` Un controllo sempre accessibile `#btn-cancel-process` (o tasto funzione dedicato in `#function-keys`) deve annullare con una singola azione (`pointerup`) qualsiasi processo safety-critical in corso, invocando `state.js: revertToSafeState()`.

#### 5.7.19.2.2 – Tempo di reazione
*Sintesi (shall):* Quando il tempo di reazione dell'operatore non è sufficiente a prevenire un incidente, il software deve riportare il sistema a uno stato sicuro noto, segnalare il guasto e comunicare lo stato del sistema all'operatore.

- **IMPL-5.7.19.2.2-01** `[JS]` `[Globale]` `state.js` deve rilevare automaticamente il superamento di soglie temporali critiche (`config.js: maxReactionTimeMs`) e invocare `revertToSafeState()` senza attendere l'azione dell'operatore, seguito da una notifica via `notify.js` (guasto + stato) in `#alert-region`.

#### 5.7.19.3.1 – Due o più azioni
*Sintesi (shall):* Il sistema deve richiedere due o più azioni operatore univoche per avviare qualsiasi funzione o sequenza di funzioni potenzialmente pericolosa.

- **IMPL-5.7.19.3.1-01** `[HTML][JS]` `[Main]` I controlli che avviano funzioni pericolose devono richiedere una sequenza di due azioni distinte: `pointerup` sul controllo seguito da conferma esplicita su `<dialog>` gestito da `dialog.js` (es. “Arma”→“Conferma”).

#### 5.7.19.3.2 – Attuazione inavvertita
*Sintesi (shall):* Le azioni richieste devono minimizzare il potenziale di attuazione inavvertita e devono essere verificate per la sequenza corretta (vedi distanze di separazione 5.1.3/5.1.4).

- **IMPL-5.7.19.3.2-01** `[CSS][JS]` `[Main]` I controlli di funzioni pericolose devono rispettare `--touch-gap-min`/`--touch-gap-max` (separazione, vedi §2.4) e `dialog.js` deve verificare l'ordine corretto della sequenza di azioni, rifiutando conferme fuori sequenza.

#### 5.7.19.4 – Display safety-critical
*Sintesi (should):* I display operatore, le legende e le altre funzioni di interfaccia safety-critical dovrebbero essere chiari, concisi e non ambigui e, dove possibile, duplicati su dispositivi di visualizzazione separati.

- **IMPL-5.7.19.4-01** `[HTML][CSS]` `[Main]` Gli elementi `.safety-critical-display` devono usare testo conciso, etichette non ambigue e doppia codifica (colore+icona+testo, vedi §2.4); dove l'architettura hardware lo preveda, lo stesso stato deve poter essere duplicato su un display secondario tramite `net.js`/`state.js`.

#### 5.7.19.5.1 – Conferma positiva
*Sintesi (shall):* Il software deve fornire conferma positiva dell'immissione dati valida o delle azioni intraprese (feedback visivo o sonoro che l'operatore sappia che il sistema ha accettato l'azione e la sta elaborando).

- **IMPL-5.7.19.5.1-01** `[JS]` `[Globale]` Ogni azione/immissione valida deve invocare `feedback.js: acknowledge(el)` che fornisce feedback visivo (classe `.is-acknowledged`) e sonoro (`beep('confirm')`) entro i tempi di risposta di Tabella V (§2.6).

#### 5.7.19.5.2 – Indicazione in tempo reale
*Sintesi (shall):* Il sistema deve fornire un'indicazione in tempo reale che sta funzionando.

- **IMPL-5.7.19.5.2-01** `[HTML][JS]` `[Globale]` Un indicatore persistente `#system-heartbeat` in `#app-header`/`#status-line` deve mostrare, tramite `state.js`, che il sistema è operativo (es. animazione/pulsazione a intervallo regolare, non solo testo statico).

#### 5.7.19.5.3 – Indicazione di stato
*Sintesi (shall):* Le funzioni di elaborazione con ritardi ≥2 secondi devono richiedere un indicatore di stato per l'operatore durante l'elaborazione.

- **IMPL-5.7.19.5.3-01** `[HTML][JS]` `[Main]` `#progress-indicator` (footer, §2.2) deve attivarsi automaticamente per qualsiasi operazione la cui durata attesa sia ≥2 s, come da soglie di Tabella V gestite da `feedback.js: showProgress()`.

#### 5.7.19.5.4.1 – Immissioni operatore errate
*Sintesi (shall):* Il software deve essere in grado di rilevare immissioni o sequenze di immissioni/operazioni errate dell'operatore, per prevenire l'esecuzione di funzioni safety-critical.

- **IMPL-5.7.19.5.4.1-01** `[JS]` `[Globale]` `validate.js` deve verificare tipo, formato, intervallo e sequenza di ogni immissione/azione diretta a funzioni safety-critical, bloccando l'esecuzione in caso di anomalia (`aria-invalid="true"`).

#### 5.7.19.5.4.2 – Allertare l'operatore
*Sintesi (shall):* Il software deve allertare l'operatore dell'immissione o operazione erronea e indicare l'errore e l'azione correttiva.

- **IMPL-5.7.19.5.4.2-01** `[JS]` `[Main]` Quando `validate.js` rileva un'anomalia (vedi `IMPL-5.7.19.5.4.1-01`), `notify.js` deve mostrare in `#message-line`/`#toast-region` un messaggio che indica l'errore specifico e l'azione correttiva da intraprendere.

#### 5.7.19.6.1 – Distinzione dagli avvisi di routine
*Sintesi (shall):* Gli avvisi di routine devono essere codificati visivamente e/o acusticamente in modo diverso dagli avvisi safety-critical.

- **IMPL-5.7.19.6.1-01** `[CSS][JS]` `[Globale]` `notify.js` deve applicare classi/suoni distinti: `.alert-routine` (`--color-advisory`/`--color-normal`, beep breve) vs `.alert-safety-critical` (`--color-critical`, pattern sonoro distinto, eventuale `.flash-code`).

#### 5.7.19.6.2 – Eliminazione di un avviso safety-critical
*Sintesi (shall):* L'operatore non deve poter eliminare un avviso safety-critical senza intraprendere un'azione correttiva o eseguire le azioni successive richieste per completare l'operazione in corso.

- **IMPL-5.7.19.6.2-01** `[JS]` `[Main]` Gli avvisi `.alert-safety-critical` in `#alert-region` non devono esporre un controllo di chiusura semplice; `notify.js` deve rimuoverli solo dopo conferma dell'azione correttiva richiesta (tramite `dialog.js`) o completamento della sequenza operativa.

#### 5.7.19.6.3 – Avvisi di situazione non sicura
*Sintesi (shall):* Il sistema deve fornire segnali che allertino l'utente su situazioni non sicure tramite l'interfaccia utente.

- **IMPL-5.7.19.6.3-01** `[JS]` `[Globale]` `state.js` deve emettere un evento `CustomEvent('unsafe-condition')` intercettato da `notify.js` per generare immediatamente un avviso `.alert-safety-critical` in `#alert-region` ogni volta che viene rilevata una condizione non sicura.

#### 5.7.19.7.1 – Controllo computerizzato
*Sintesi (shall):* Le funzioni di temporizzazione safety-critical devono essere controllate dal computer piuttosto che affidarsi all'input umano.

- **IMPL-5.7.19.7.1-01** `[JS]` `[Globale]` Tutti i timer safety-critical (es. timeout di sicurezza, finestre di conferma) devono essere gestiti da `state.js`/`config.js` lato client (e validati lato server), mai dipendere dalla tempistica manuale dell'operatore.

#### 5.7.19.7.2 – Non modificabile dall'operatore
*Sintesi (shall):* Il sistema deve impedire all'operatore di modificare i valori di temporizzazione safety-critical dalle console di sistema, salvo quando specificamente richiesto dal progetto del sistema e in conformità ai protocolli di approvazione.

- **IMPL-5.7.19.7.2-01** `[HTML][JS]` `[Globale]` I campi di temporizzazione safety-critical in `#dialog-host`/pannelli di configurazione devono essere di sola lettura (`readonly`/`disabled`) per i ruoli utente standard in `auth.js`; la modifica deve essere consentita solo a un ruolo con privilegio esplicito e tracciata da `log.js`.

### 3.16 Uso su dispositivi mobili e portatili (web HMI responsive)

Requisiti a livello software/applicativo per applicazioni PED (*Portable Electronic Device*) quando la web HMI è utilizzata da telefono o tablet; i requisiti hardware/dispositivo (involucro, montaggio, alimentazione, temperatura) sono esclusi. **Target primario:** coerentemente con §2.1, il dispositivo di input primario resta lo **schermo touch**, con risoluzione minima **800 × 600 px** scalabile verso l'alto; non esiste un layout "mobile" separato. Gli aspetti PED di questa sezione (adattamento a schermi piccoli, orientamento, attenzione divisa, ecc.) sono realizzati **esclusivamente tramite il layout responsive** definito in §2.3 (stessi breakpoint S–XXL, stessa griglia header/main/footer, stessi componenti), non con un'applicazione o un codice separato per dispositivo.

#### 5.19.1.9.1 – Attivazione inavvertita
*Sintesi (shall):* L'attivazione accidentale nei PED deve essere evitata definendo sequenze di azione utente univoche richieste per l'attivazione.

- **IMPL-5.19.1.9.1-01** `[JS]` `[Main][Footer]` `config.js` elenca le azioni critiche che richiedono conferma; tali controlli invocano `dialog.js` (conferma) o un gesto a due passi (pressione prolungata + rilascio) anziché un singolo tocco, coerente con `IMPL-5.12.3.4.12.7-01`.

#### 5.19.2.5.2 – Indicazione dello stato di connessione
*Sintesi (should):* I PED dovrebbero fornire un'indicazione dello stato della connessione in termini di forza/affidabilità sia in modalità wireless sia cablata.

- **IMPL-5.19.2.5.2-01** `[HTML][CSS][JS]` `[Header]` `#connection-indicator` mostra icona a livelli (forza segnale) + testo, aggiornato da `net.js` (`pollConnectionQuality()`), con colore `--color-normal/--color-warning/--color-critical` e doppia codifica testo/icona.

#### 5.19.5.1 – Generale
*Sintesi (shall):* Oltre ai requisiti seguenti, le applicazioni PED devono essere progettate secondo i criteri applicabili di 5.1, 5.2 e 5.17.

- **IMPL-5.19.5.1-01** `[CSS][JS]` `[Globale]` Vincolo architetturale: lo stesso design system (token `--touch-target-min`, palette `--color-*`, moduli `feedback.js`/`validate.js`/`notify.js`) definito in §2 si applica senza eccezioni ai breakpoint stretti; nessun componente PED-specifico divergente (verifica per ispezione I).

#### 5.19.5.1.1 – Adattamento del design esistente ai PED
*Sintesi (shall):* Quando un'applicazione PED si basa su un progetto esistente, deve adattarlo, non replicarlo, calibrandolo per la dimensione dello schermo del PED.

- **IMPL-5.19.5.1.1-01** `[CSS]` `[Globale]` Un unico markup HTML e un'unica libreria di componenti vengono riorganizzati via `grid-template`/media query (breakpoint S 800 px) senza duplicare l'albero DOM per "versione mobile"; nessun secondo codebase.

#### 5.19.5.1.2 – Progettare intorno alle capacità e vincoli del PED
*Sintesi (should):* Le applicazioni PED dovrebbero essere progettate intorno alle capacità/vincoli del PED: (a) coerenza di aspetto/interazione tra dispositivi multipli; (b) focus sui compiti con sequenze/dialoghi logici (vedi 5.19.5.4).

- **IMPL-5.19.5.1.2-01** `[JS]` `[Globale]` `prefs.js` applica lo stesso tema/etichette/pattern di dialogo indipendentemente dal dispositivo (a).
- **IMPL-5.19.5.1.2-02** `[HTML][JS]` `[Main]` `nav.js` instrada per compito; a breakpoint S, `#nav-region` collassa in barra/hamburger mantenendo le stesse etichette e lo stesso ordine logico (b).

#### 5.19.5.1.3 – Contesti operativi dinamicamente mutevoli
*Sintesi (shall):* Il design deve supportare operazioni in contesti mutevoli (luminosità, rumore, meteo): (a) configurare l'output secondo preferenze (dimensione testo, luminosità); (b) cambiare attributi chiave con pochi passaggi; (c) fornire opzioni di accesso a funzioni/attributi chiave.

- **IMPL-5.19.5.1.3-01** `[HTML][JS]` `[Header]` `#btn-display-settings` apre dialogo `prefs.js` con cursori per dimensione testo/contrasto/tema (a); dato che la luminosità fisica dello schermo non è controllabile dal browser, si assume come equivalente software il tema (`theme-light/dark/high-contrast`) — assunzione dichiarata.
- **IMPL-5.19.5.1.3-02** `[JS]` `[Footer]` Un singolo tocco su `#function-keys` cicla tra i temi (cambio "luminosità" a passo minimo) (b).
- **IMPL-5.19.5.1.3-03** `[HTML]` `[Footer]` Scorciatoia alle impostazioni rapide raggiungibile in ≤2 tocchi da qualunque vista (c).

#### 5.19.5.1.4 – Attenzione limitata e divisa
*Sintesi (should):* Il design PED dovrebbe accomodare attenzione limitata/divisa tramite segnali udibili/visivi/tattili; l'uso del PED non dovrebbe distrarre l'utente da altri compiti nei casi (a) attenzione limitata su compito non-PED, (b) attenzione divisa PED/non-PED, (c) attenzione limitata con informazioni in arrivo su altro compito.

- **IMPL-5.19.5.1.4-01** `[JS]` `[Globale]` `feedback.js` usa segnali multimodali (visivo + `beep()` + `navigator.vibrate`) per gli avvisi, percepibili senza attenzione visiva sostenuta.
- **IMPL-5.19.5.1.4-02** `[HTML][CSS]` `[Main]` I toast non critici (`notify.js`) sono non modali, non rubano il focus e si auto-dismettono, per non interrompere il compito in corso.

#### 5.19.5.1.5 – Ottimizzazione dell'applicazione
*Sintesi (should):* Le applicazioni PED dovrebbero essere ottimizzate per le prestazioni (tempo di caricamento, riproduzione multimediale, salvataggio, connessione) e minimizzare il consumo energetico.

- **IMPL-5.19.5.1.5-01** `[JS]` `[Globale]` `config.js` definisce budget prestazionali allineati a Tabella V (cambio pagina ≤1,0 s, aggiornamento da host ≤2,0 s); `net.js` preferisce WebSocket/push a polling frequente per ridurre il consumo energetico; contenuti non visibili in `#aux-region` caricati lazy.

#### 5.19.5.1.6.1 – Uso delle linee guida di settore
*Sintesi (should):* Dovrebbero essere usate le linee guida di settore per funzionalità (es. fotocamera, giroscopio) e piattaforme (iOS, Android, Windows) PED, evitando interazioni che differiscono dal comportamento predefinito atteso.

- **IMPL-5.19.5.1.6.1-01** `[JS]` `[Globale]` Eventuali funzionalità hardware del PED sono accedute tramite API web standard (`MediaDevices`, `DeviceOrientation`) con UX di permesso nativa del browser, senza sovrascrivere le convenzioni di piattaforma (es. gesto/pulsante "indietro" standard preservato in `nav.js`).

#### 5.19.5.1.6.2 – Aspettative dell'utente
*Sintesi (should):* Il design dovrebbe permettere all'utente di applicare conoscenze pregresse e costruire rapidamente un modello mentale tramite oggetti familiari e coerenza, riducendo il carico mentale.

- **IMPL-5.19.5.1.6.2-01** `[HTML][CSS]` `[Main]` Uso di controlli HTML standard e pattern ARIA comuni (bottoni, select, checkbox) e icone convenzionali, anziché widget personalizzati non familiari.

#### 5.19.5.1.6.3 – Applicazioni multiple
*Sintesi (should):* Le applicazioni PED dovrebbero comportarsi in modo coerente con le altre applicazioni del PED con cui l'utente ha familiarità.

- **IMPL-5.19.5.1.6.3-01** `[JS]` `[Globale]` `nav.js` usa la Session History API in modo coerente con il comportamento di navigazione/back atteso del browser/OS, senza intercettazioni custom del gesto "indietro".

#### 5.19.5.1.6.4 – Progettazione per PED multipli
*Sintesi (should):* Se si progetta per più PED, dovrebbero essere seguite le convenzioni di design stabilite per ciascun PED, con aspetto/funzionalità coerenti tra PED.

- **IMPL-5.19.5.1.6.4-01** `[CSS]` `[Globale]` Un'unica base di codice responsive (breakpoint S–XXL di §2.3) garantisce lo stesso set di componenti/token su tutti i PED; test di regressione visiva (T) sui preset di viewport principali.

#### 5.19.5.1.7 – Facilitazione dell'azione prevista
*Sintesi (shall):* Le funzioni dell'applicazione PED devono supportare azioni utente critiche per il tempo.

- **IMPL-5.19.5.1.7-01** `[HTML][CSS]` `[Footer]` Le azioni primarie time-critical restano raggiungibili da `#function-keys` senza menu annidati, target ≥ `--touch-target-min` (vedi anche `IMPL-5.19.5.4.1-01`).

#### 5.19.5.1.8 – Informazioni testuali
*Sintesi (should):* Ove appropriato, la quantità di informazione testuale dovrebbe essere minimizzata per limitare la lettura richiesta, coerentemente con 5.17.18.

- **IMPL-5.19.5.1.8-01** `[HTML][JS]` `[Main]` `#content-region` privilegia icone/etichette brevi con `aria-label`/tooltip per il dettaglio; testi lunghi collassati dietro "Leggi di più" (`<details>`), testi gestiti da `i18n.js` in forma concisa.

#### 5.19.5.2 – Controllo degli errori
*Sintesi (shall):* Le applicazioni PED devono soddisfare i requisiti di controllo degli errori di 5.1.2.1.7.

- **IMPL-5.19.5.2-01** `[JS]` `[Main]` Il pattern di gestione errori di `validate.js` (messaggio inline + `[aria-invalid]` + suggerimento di correzione) è applicato identicamente a tutti i breakpoint, verificato a 800×600 portrait/landscape.

#### 5.19.5.3 – Feedback
*Sintesi (shall):* Il feedback delle applicazioni PED all'utente deve soddisfare i requisiti di feedback di 5.1.2.1.6.

- **IMPL-5.19.5.3-01** `[JS][CSS]` `[Globale]` I budget di tempo di `feedback.js` (Tabella V) non variano con la dimensione del viewport; l'area visiva del feedback scala comunque ≥ `--touch-target-min` per restare percepibile su schermo piccolo.

#### 5.19.5.4.1 – Informazioni critiche
*Sintesi (should):* Nella progettazione dei display PED, le informazioni critiche dovrebbero essere raggiungibili in non più di due azioni chiave (es. scorrimento e tocco).

- **IMPL-5.19.5.4.1-01** `[HTML][JS]` `[Main][Footer]` `nav.js` garantisce che ogni informazione critica sia raggiungibile con al massimo 2 azioni (es. `#btn-home` + 1 tocco); criterio di accettazione: dimostrazione (D) che conta i tocchi per ciascuna informazione critica.

#### 5.19.5.4.2 – Scorciatoie di comando
*Sintesi (should):* Dovrebbero essere fornite scorciatoie (tasti di scelta rapida, attivazione vocale, combinazioni di tasti) per i comandi PED usati frequentemente.

- **IMPL-5.19.5.4.2-01** `[JS]` `[Footer]` `#function-keys` configurabile con scorciatoie (`config.js shortcutMap`); supporto opzionale a comandi vocali tramite Web Speech API in `nav.js` e acceleratori da tastiera fisica come input secondario.

#### 5.19.5.4.3 – Controllo dell'applicazione
*Sintesi (should):* Le applicazioni PED dovrebbero essere progettate per velocità e ripristino: caricamento rapido, arresto/avvio/ripresa/cambio con sforzo minimo.

- **IMPL-5.19.5.4.3-01** `[JS]` `[Globale]` `state.js` persiste lo stato di sessione/compito e lo ripristina su evento `visibilitychange`/riavvio app; rendering iniziale con interfaccia scheletro per rispettare il tempo di cambio pagina ≤1,0 s di Tabella V.

#### 5.19.5.4.4 – Auto-rotazione dello schermo
*Sintesi (shall):* Le applicazioni PED devono supportare l'auto-rotazione dello schermo selezionabile dall'utente per adattare presentazione e immissione dati.

- **IMPL-5.19.5.4.4-01** `[JS]` `[Globale]` `prefs.js` espone l'impostazione `orientationLock` (`auto`/`portrait`/`landscape`) applicata tramite `screen.orientation` API.
- **IMPL-5.19.5.4.4-02** `[CSS]` `[Main]` `@media (orientation: landscape|portrait)` riorganizza `#nav-region`/`#content-region`/`#aux-region` tra tre colonne, due colonne e colonna singola come da §2.3.

#### 5.19.5.4.5 – Semplicità del design dell'applicazione
*Sintesi (should):* Il design dovrebbe essere semplice e non ingombro tramite: (a) annidamento gerarchico opzionale; (b) selezione di parole anziché immissione testo; (c) linguaggio semplice anziché gergo.

- **IMPL-5.19.5.4.5-01** `[HTML]` `[Main]` Uso di `<details>`/accordion per il drill-down gerarchico in `#content-region`, lasciando all'utente la scelta di espandere (a).
- **IMPL-5.19.5.4.5-02** `[HTML][JS]` `[Main]` Form che preferiscono `<select>`/chip di scelta a campi di testo libero, validati da `validate.js` (b).
- **IMPL-5.19.5.4.5-03** `[JS]` `[Globale]` `i18n.js` mantiene un glossario in linguaggio piano, evitando gergo salvo popolazione utente specialistica dichiarata (c).

#### 5.19.5.4.6 – Linguaggio
*Sintesi (shall):* L'uso del linguaggio deve essere coerente con 5.1.3.11.1.8.

- **IMPL-5.19.5.4.6-01** `[JS]` `[Globale]` Stesso dizionario/stile `i18n.js` usato su tutte le viste PED e non-PED, senza stringhe duplicate specifiche per PED.

#### 5.19.5.4.7 – Semplicità dell'immissione testo
*Sintesi (should):* L'immissione utente dovrebbe essere semplice e basata sul contesto operativo: (a) minimizzare l'immissione testo; (b) indicare chiaramente tipo/formato richiesto; (c) consentire la scelta per selezione anziché digitazione manuale; (d) mostrare dati precompilati nei form ove possibile.

- **IMPL-5.19.5.4.7-01** `[HTML]` `[Main]` Campi con `inputmode`, `pattern` e placeholder descrittivo gestiti da `validate.js` (b).
- **IMPL-5.19.5.4.7-02** `[HTML][JS]` `[Main]` Form che privilegiano `<select>`/liste con autocompletamento rispetto al testo libero (a, c).
- **IMPL-5.19.5.4.7-03** `[JS]` `[Main]` Campi precompilati da `state.js`/`prefs.js` quando il dato è già noto (d).

#### 5.19.5.4.8 – Navigazione dell'applicazione PED
*Sintesi (should):* (a) Le strutture di navigazione dovrebbero essere semplici e focalizzate su un compito specifico alla volta; (b) la navigazione tra campi nei form dovrebbe essere prevedibile.

- **IMPL-5.19.5.4.8-01** `[HTML][CSS]` `[Main]` `#nav-region` evidenzia un solo compito attivo (`.is-current`) per vista (a).
- **IMPL-5.19.5.4.8-02** `[HTML][JS]` `[Main]` Ordine di tabulazione naturale del DOM e `inputmode` coerenti tra tutti i form, gestiti da `validate.js` (b).

#### 5.19.5.4.9 – Chiusura del dialogo
*Sintesi (should):* I dialoghi, interazione finita tra utente e PED, dovrebbero fornire chiusura con inizio, metà e fine chiari della sequenza di azioni.

- **IMPL-5.19.5.4.9-01** `[HTML][JS]` `[Main]` I flussi modali di `dialog.js` mostrano indicatore di avanzamento ("Passo 2 di 3") e pulsanti espliciti "Conferma"/"Annulla"/"Fine" a marcare la chiusura.

#### 5.19.5.5.1 – Minimizzare il disordine
*Sintesi (should):* L'applicazione PED dovrebbe fornire dati e funzionalità essenziali per l'azione immediata, minimizzando elementi/disordine/informazioni non necessarie.

- **IMPL-5.19.5.5.1-01** `[CSS]` `[Main]` A breakpoint S (800 px), i widget non essenziali sono collassati in `#aux-region` come pannello a comparsa; la vista predefinita mostra solo i controlli critici per il compito.

#### 5.19.5.5.2 – Evidenziazione della selezione
*Sintesi (shall):* L'elemento selezionato su uno schermo PED deve essere chiaramente indicato, usando colore, sfondo, cambio di testo o altri meccanismi visivi.

- **IMPL-5.19.5.5.2-01** `[CSS][HTML]` `[Main]` Classe `.is-selected`/`[aria-selected="true"]` applica colore di sfondo `--color-selected`, bordo e icona di spunta più cambio testo, secondo la convenzione di doppia codifica di §2.6.

#### 5.19.5.5.3 – Disponibilità dei controlli di base
*Sintesi (shall):* I controlli software/hardware per la navigazione di base (es. "Torna alla home", "Torna ai risultati", "Indietro") devono essere sempre disponibili.

- **IMPL-5.19.5.5.3-01** `[HTML][CSS]` `[Footer]` `#function-keys` mantiene permanentemente visibili "Indietro"/"Home"/"Cerca" in tutte le viste e a tutti i breakpoint, gestiti da `nav.js` (`history.back()`, `goHome()`); mai nascosti o rimossi dal DOM.

#### 5.19.5.5.4 – Densità del contenuto
*Sintesi (should):* La densità del contenuto sullo schermo PED dovrebbe essere minimizzata: (a) non frammentare compiti/interazione su troppe schermate; (b) le schermate dovrebbero scorrere in una sola dimensione; (c) minimizzare il testo per ridurre la lettura in condizioni di movimento.

- **IMPL-5.19.5.5.4-01** `[JS]` `[Globale]` `nav.js` limita la profondità massima dei flussi di navigazione (`config.js maxNavDepth`) per evitare frammentazione (a).
- **IMPL-5.19.5.5.4-02** `[CSS]` `[Main]` `#content-region` ha `overflow-x: hidden` e scroll solo verticale, coerente con §2.3 (b).
- **IMPL-5.19.5.5.4-03** `[JS]` `[Globale]` `i18n.js` mantiene testo conciso, in linea con `IMPL-5.19.5.1.8-01` (c).

#### 5.19.5.5.5.1 – Capacità di scorrere rapidamente il testo
*Sintesi (shall):* Per aumentare la capacità di scorrere rapidamente il testo, sottotitoli e ipertesto devono essere usati nei passaggi di testo più lunghi di una schermata.

- **IMPL-5.19.5.5.5.1-01** `[HTML]` `[Main]` Blocchi di testo in `#content-region`/`#aux-region` più lunghi dell'altezza del viewport usano sottotitoli `<h2>`/`<h3>` e link di ancoraggio interni (ipertesto).

#### 5.19.5.5.5.2 – Evitare lunghezze di riga variabili
*Sintesi (shall):* Le lunghezze di riga variabili devono essere evitate sillabando le parole a fine riga per migliorare la leggibilità su schermi piccoli.

- **IMPL-5.19.5.5.5.2-01** `[CSS]` `[Main]` `hyphens: auto; overflow-wrap: break-word;` applicato ai blocchi di testo in `#content-region`/`#aux-region` ai breakpoint stretti.

#### 5.19.5.5.5.3 – Indicatori di scorrimento
*Sintesi (shall):* Devono essere forniti indicatori di scorrimento per permettere agli utenti di identificare la propria posizione nella pagina quando il contenuto non può essere mostrato in una sola schermata.

- **IMPL-5.19.5.5.5.3-01** `[CSS][JS]` `[Main]` Barra di scorrimento sempre visibile (non solo overlay) più un indicatore JS "X di Y"/mini-mappa sticky in `#content-region`, alimentato dalla posizione di scroll tracciata in `state.js`.

#### 5.19.5.5.6 – Leggibilità dello schermo
*Sintesi (should):* Dovrebbe essere selezionabile una gamma di colori di testo ad alto contrasto e caratteri tipografici per ottimizzare la leggibilità secondo l'ambiente operativo.

- **IMPL-5.19.5.5.6-01** `[CSS][JS]` `[Header]` `prefs.js` espone selettore tema includendo `theme-high-contrast` (token `--contrast-*` di §2.4) e font-family alternativa, applicati tramite `data-theme` su `<html>`.

#### 5.19.5.5.7 – Uso del colore
*Sintesi (shall):* (a) Il colore deve essere usato con attenzione e coerenza in tutto il design dei PED, coerentemente con 5.17.25; (b) la doppia codifica (forma o etichettatura) deve essere usata insieme al colore.

- **IMPL-5.19.5.5.7-01** `[CSS]` `[Globale]` I soli token semantici `--color-*` definiti in §2.4 sono ammessi per la codifica di stato, applicati tramite classi condivise (a).
- **IMPL-5.19.5.5.7-02** `[HTML][CSS]` `[Globale]` Ogni indicazione codificata a colore è sempre accompagnata da icona/forma e testo (doppia codifica); test di accessibilità (T) verifica l'assenza di indicatori solo-colore (b).
