# Changelog

Tutte le modifiche rilevanti a questo plugin sono documentate in questo file.

Il formato è basato su [Keep a Changelog](https://keepachangelog.com/it/1.1.0/)
e questo progetto aderisce al [Semantic Versioning](https://semver.org/lang/it/).

## 1.2.2

- Fix: gli endpoint AJAX pubblici di log e report ignorano parametri non scalari (rispondono 400 invece di warning che rompono il JSON).

## [1.2.1] - 2026-10-26

### Fixed
- Pagina *Cookie rilevati*: "1 volta" invece di "1 volte" (plurale corretto, anche in inglese).
- Campo *Colore principale* più largo: il valore predefinito `#3e287c` non veniva più tagliato.
- Estratti di articoli e pagine (home, archivi, ricerca): il segnaposto dei video bloccati non finisce più nel testo
  dell'estratto ("Per vedere questo contenuto servono i cookie di marketing. Accetta e mostra").

## [1.2.0] - 2026-10-26

Stesse funzioni del modulo Magento `m2-cookie-banner` 1.1.0 – 1.3.0.

### Added
- **Scansione automatica dei cookie**, attiva di default. Un evento WP-Cron (default ogni notte alle 02:30, ora del sito)
  visita la home e le pagine indicate come farebbe un visitatore nuovo e legge i cookie impostati dal server,
  compresi quelli HttpOnly, seguendo i reindirizzamenti dello stesso sito.
- **Segnalazione dal browser**: il banner invia i soli nomi dei cookie presenti nel browser (mai i valori), così si
  vedono anche quelli impostati da JavaScript (per esempio Google Analytics). Endpoint pubblico
  `admin-ajax.php?action=tc_cookie_report` con input validato, 20 richieste l'ora per IP e tetto di 400 righe.
- **Dizionario dei cookie comuni** (WordPress, WooCommerce, WPML, Polylang, Google, Meta, Microsoft, TikTok, LinkedIn,
  YouTube…) che assegna categoria, durata e finalità. I cookie non riconosciuti restano "da classificare".
- Pagina **Impostazioni → Cookie rilevati**: corregge categoria, durata e finalità, sceglie cosa pubblicare o ignorare e
  lancia la scansione al volo. Le righe modificate a mano non vengono più sovrascritte né cancellate.
- **Dettaglio dei cookie** nel pannello *Personalizza*: elenco manuale (nuovo campo *Elenco cookie*, ha la precedenza)
  più i cookie pubblicati dalla scansione; il cookie di consenso è sempre tra i necessari.
- **Registro dei consensi** (opzionale, spento di default): ogni scelta, revoche comprese, nella tabella
  `tc_cookie_consent_log` per poter dimostrare il consenso (art. 7.1 GDPR). Pagina **Impostazioni → Registro consensi**
  con filtri, ricerca ed esportazione CSV. Conservazione in giorni con pulizia notturna; impronta dell'IP (HMAC) e
  utente loggato facoltativi. Endpoint `admin-ajax.php?action=tc_cookie_log` con input validato e 10 scritture l'ora per ID.
- Ogni browser riceve un ID casuale (UUID) salvato nel cookie di consenso; è anche in `event.detail` dell'evento
  `tc:cookie-consent`.
- Impostazioni **Nome cookie di consenso**, **Durata del consenso (giorni)** e **Versione del consenso**, prima fissi
  nel codice. Il Consent Mode v2 legge nome e versione configurati.

### Notes
- Le tabelle `tc_cookie_consent_log` e `tc_cookie_scan` si creano da sole al primo caricamento dopo l'aggiornamento.
- Alla disinstallazione la tabella del registro dei consensi resta (prova da conservare); quella dei cookie rilevati
  viene eliminata.
- Aggiornare la privacy policy e la cookie policy se si attiva il registro dei consensi.

## [1.1.2] - 2026-10-22

### Fixed
- Rimosso `p-cookie-banner.php`, un file vuoto finito per errore nella 1.1.1. Nessun cambio di comportamento.

## [1.1.1] - 2026-10-22

### Changed
- Elenco predefinito degli embed bloccati: aggiunti i video di Facebook (`facebook.com/plugins`), Instagram, TikTok,
  Twitch, Spotify e SoundCloud, oltre a YouTube, Vimeo e Google Maps. Resta modificabile con il filtro
  `wp_cookie_banner_embed_hosts`.

## [1.1.0] - 2026-10-22

### Added
- Opzione **Pagina Cookie Policy** (select di pagine): ha la precedenza sull'URL manuale e viene tradotta nella
  lingua del visitatore con WPML (`wpml_object_id`) o Polylang (`pll_get_post`), come la privacy policy di
  WordPress. Con la pagina scelta, il banner mostra "Consulta la Cookie Policy e la Privacy Policy".
- **Blocco degli embed di terze parti**: gli iframe di YouTube, YouTube Nocookie, Vimeo e Google Maps nei contenuti
  (`the_content`, `embed_oembed_html`, `render_block`) diventano un segnaposto accessibile con il pulsante
  "Accetta e mostra"; l'iframe, con gli stessi attributi, torna solo con il consenso marketing, già salvato o
  concesso in quel momento. Il pulsante concede soltanto il marketing e lascia invariata la scelta sulla statistica.
  Opzione per disattivare il blocco, filtro `wp_cookie_banner_embed_hosts` per l'elenco dei domini, filtro
  `wp_cookie_banner_block_embeds` per filtrare a mano l'HTML che WordPress non filtra da solo.
- Test: `EmbedsTest` (PHPUnit) e 8 test jsdom sul ripristino degli embed.

### Changed
- Il blocco degli embed è **attivo di default** anche per chi aggiorna dalla 1.0.0: gli iframe di YouTube, Vimeo e
  Google Maps nei contenuti mostrano il segnaposto finché non c'è il consenso marketing.

## [1.0.0] - 2026-10-21

### Added
- Banner di consenso cookie GDPR con categorie Necessari, Statistica e Marketing, portato da `m2-cookie-banner`:
  cookie `tc_cookie_consent`, blocco preventivo degli script (`type="text/plain" data-cookiecategory`), evento
  `tc:cookie-consent`, API `window.TcCookieConsent`, variabili CSS per i colori.
- Pagina *Impostazioni → Cookie Banner*: attivazione, URL privacy (default pagina privacy di WordPress) e cookie
  policy, colore principale validato, posizione (a tutta larghezza, a sinistra, a destra).
- Shortcode `[tc_cookie_settings]` per riaprire le preferenze.
- Google Consent Mode v2 facoltativo, con default negato e aggiornamento alla scelta.
- Banner compatibile con la cache di pagina, accessibile (dialog, focus, `Esc`) e traducibile; traduzione inglese inclusa.
- Dopo la revoca di una categoria già concessa la pagina si ricarica, così gli script bloccati restano tali.
