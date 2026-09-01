# Budgee - Documentazione sulla sicurezza

<div align="center">

[![English](https://img.shields.io/badge/Read_in_English-4A90E2?style=for-the-badge)](./SECURITY.md) &nbsp; [![Torna al README](https://img.shields.io/badge/Torna_al_README-009246?style=for-the-badge)](./README_IT.md)

</div>

---

Questo documento descrive le misure di sicurezza in vigore in Budgee. L'app gestisce informazioni finanziarie sensibili, quindi le scelte di sicurezza sono scritte in chiaro e tenute aggiornate.

---

## Indice

- [Autenticazione e sicurezza account](#autenticazione-e-sicurezza-account)
- [Protezione dei dati](#protezione-dei-dati)
- [Sicurezza del database](#sicurezza-del-database)
- [Sicurezza di rete](#sicurezza-di-rete)
- [Sicurezza applicativa](#sicurezza-applicativa)
- [Sicurezza offline](#sicurezza-offline)
- [Sicurezza delle dipendenze](#sicurezza-delle-dipendenze)
- [Privacy](#privacy)
- [Limitazioni note e roadmap](#limitazioni-note-e-roadmap)

---

## Autenticazione e sicurezza account

### Autenticazione email e password

Budgee usa Firebase Authentication per la gestione degli account.

- La verifica email è obbligatoria: i nuovi account devono verificare l'indirizzo prima di accedere alle funzionalità.
- Requisiti password:
  - Almeno 8 caratteri
  - Almeno una lettera maiuscola (A-Z)
  - Almeno una lettera minuscola (a-z)
  - Almeno una cifra (0-9)
- Il reset password è gestito da Firebase tramite link email firmati. Budgee non memorizza né trasmette password in chiaro.

### Gestione delle sessioni

- Le sessioni sono gestite da Firebase con token JWT.
- Le sessioni scadono in automatico secondo le policy Firebase.
- Il logout cancella i dati locali: token di sessione, dati in cache e preferenze utente.

### Eliminazione account

L'eliminazione passa da più conferme:

1. Finestra di conferma esplicita
2. Re-autenticazione richiesta: devi inserire di nuovo la password
3. Tutti i dati utente vengono eliminati dal database (spese, entrate, budget, investimenti, finanziamenti, obiettivi, impostazioni)
4. L'account Firebase Authentication viene rimosso
5. Tutti i dati in cache locale vengono cancellati

Firestore non cancella le subcollection insieme al documento padre. Senza una visita esplicita restano orfane e invisibili, quindi la cancellazione percorre un registro unico di tutto ciò che compone un account, lo stesso che legge l'esportazione. Il documento utente viene marcato prima di iniziare e cancellato per ultimo: un'interruzione lascia una traccia che l'accesso successivo riconosce e riprende.

### Protezione brute-force

Firebase Authentication blocca i tentativi di login ripetuti falliti con una risposta `too-many-requests`.

### Accettazione dei termini d'uso

Per registrarsi bisogna accettare i termini d'uso e l'informativa privacy. La casella non è mai già spuntata, e i due documenti sono collegati accanto al punto in cui si accetta, non solo in fondo alla pagina.

L'accettazione viene registrata, e il record è costruito perché nessuno lo possa alterare dopo:

- si crea e si legge, mai si aggiorna: le regole del database rifiutano `update`;
- un vincolo solo fa il lavoro: `acceptedAt == request.time`. Impone al client di usare l'orario del server, l'unico valore che soddisfa l'uguaglianza. Senza, una data scelta dal client verrebbe accettata e nulla sembrerebbe fuori posto;
- l'identificativo del documento è `<documento>-<versione>`, quindi riaccettare la stessa versione non produce un secondo record;
- le regole fissano l'insieme esatto delle chiavi, il nome del documento, il formato della versione e una lingua non vuota, così la forma salvata non può divergere da quella che il lettore si aspetta.

Cosa questo dimostra, detto senza gonfiarlo: che una registrazione autenticata ha accettato una versione precisa del testo, in un momento che non ha scelto, e che nessuno l'ha riscritta dopo. Non chi fosse alla tastiera, e non cosa abbia letto. È un click-wrap, non una firma.

---

## Protezione dei dati

### Crittografia

| Livello | Metodo | Dettagli |
|---------|--------|----------|
| In transito | TLS/HTTPS | Tutte le connessioni fra browser e Firebase sono cifrate. HSTS è attivo per 1 anno, sottodomini inclusi. |
| A riposo | AES-256 | Firestore cifra i dati a riposo con AES-256. L'impostazione è gestita dall'infrastruttura Google Cloud e non può essere disabilitata. |

### Isolamento dei dati

I dati di ogni utente sono isolati:

- Le regole di sicurezza del database impongono che ogni utente possa leggere e scrivere solo i propri dati.
- Non esiste un pannello di amministrazione che esponga i dati di altri utenti.
- Ogni operazione sul database verifica l'identità dell'utente autenticato prima di procedere.

### Gestione token sensibili

- I token OAuth di Google Drive sono in `sessionStorage` (cancellati alla chiusura della scheda), mai in memoria persistente.
- Le credenziali utente non vengono salvate localmente; viene mantenuto solo il token di sessione gestito da Firebase.
- Budgee non ha codice lato server proprio, quindi non esiste un posto dove nascondere un segreto: il client ID OAuth di Google viaggia nel frontend, come quel protocollo prevede, e il permesso di agire poggia sul consenso che concedi e sullo scope `drive.file`, non sulla segretezza dell'ID.
- La chiave API di Gemini che inserisci per la scansione degli scontrini resta nel tuo account ed è esclusa alla fonte tanto dall'esportazione dei dati quanto dal backup su Drive. Una chiave finita dentro un file di backup resta leggibile finché quel file esiste.
- L'integrazione con Drive usa lo scope `drive.file`: Budgee vede e tocca soltanto i file che ha creato lei. Il resto del tuo Drive le è invisibile.

---

## Sicurezza del database

### Regole di sicurezza Firestore

Budgee adotta un approccio default-deny: ogni percorso è negato salvo che una regola esplicita lo consenta.

Protezioni chiave:

- Accesso solo al proprietario: ogni lettura e scrittura richiede autenticazione e verifica che il richiedente sia il proprietario del documento.
- La validazione dei campi rifiuta dati che non rispettano tipi, range e dimensioni attesi:
  - Gli importi monetari devono essere positivi e inferiori a 10 milioni
  - Le stringhe (descrizioni, nomi) hanno un massimo di 200 caratteri
  - La valuta deve essere fra quelle ammesse (EUR, USD, GBP, PLN)
  - I tassi di interesse devono essere fra 0 e 100
  - Gli array hanno limiti di dimensione (per esempio 10.000 spese per utente)
- Limiti sulla dimensione dei documenti su numero di campi e dimensione complessiva.
- I documenti di configurazione sono validati campo per campo, non accettati in blocco. Quello dei buoni pasto, per esempio, ammette solo cinque chiavi note, una valuta fra quelle previste, un saldo di partenza pari o superiore a zero e una data `YYYY-MM-DD` con mese e giorno in intervallo: `2026-13-01` non è una data, e un confronto lessicale su di essa ordinerebbe in modo sbagliato.
- Le regole sono verificate contro l'emulatore Firestore da una suite dedicata (`npm run check:rules`), che prova sia i casi da accettare sia quelli da rifiutare. Un permesso troppo largo si vede solo se qualcuno prova a passarci.
- Il log degli errori dal client è in sola scrittura; non può essere riletto, riducendo il rischio di fughe di informazioni.
- Qualsiasi percorso non esplicitamente permesso è bloccato:

  ```
  match /{document=**} {
    allow read, write: if false;
  }
  ```

---

## Sicurezza di rete

### Header di sicurezza HTTP

Budgee imposta questi header su ogni risposta:

| Header | Valore | Scopo |
|--------|--------|-------|
| Strict-Transport-Security | `max-age=31556926; includeSubDomains; preload` | Forza HTTPS per 1 anno, sottodomini inclusi. Il valore è quello servito sul dominio `web.app`, che è già nella preload list dei browser; la configurazione del progetto ne dichiara uno equivalente |
| X-Content-Type-Options | `nosniff` | Impedisce il MIME sniffing |
| X-Frame-Options | `DENY` | Blocca l'inclusione in iframe (protezione clickjacking) |
| Referrer-Policy | `strict-origin-when-cross-origin` | Limita le informazioni di referrer verso terze parti |
| Permissions-Policy | `camera=(), microphone=(), geolocation=()` | Disattiva funzionalità browser non usate |

### Content Security Policy (CSP)

Una Content Security Policy controlla quali risorse il browser può caricare:

- Script: solo dal dominio dell'app e dalle origini di Google usate per Firebase, le API e l'accesso. Nessun `'unsafe-inline'`: gli handler scritti dentro il markup sono stati tutti rimossi
- Stili: solo dall'app e da Google Fonts
- Connessioni: solo verso Firebase, Google APIs e l'API dei tassi di cambio
- Plugin: completamente bloccati (`object-src 'none'`)
- URL base: vincolato al dominio dell'app (`base-uri 'self'`)

Chart.js e SheetJS non arrivano da una CDN: sono serviti dal dominio dell'app, quindi non c'è una terza parte che possa cambiarne il contenuto.

### Controllo cache

- Le pagine HTML vengono rivalidate a ogni richiesta (mai servite obsolete).
- JavaScript e CSS sono in cache per 24 ore; nomi file con hash garantiscono versioni aggiornate dopo gli update.
- Immagini e font sono in cache per 1 anno (asset immutabili con hash del contenuto).

---

## Sicurezza applicativa

### Prevenzione XSS

Tutti i dati forniti dall'utente (descrizioni, nomi categoria, note) vengono sanitizzati prima di essere mostrati:

- Escaping HTML: caratteri come `<`, `>`, `"`, `'`, `&` vengono convertiti negli equivalenti sicuri.
- Escaping degli attributi per i dati inseriti dentro attributi HTML.
- La Content Security Policy limita quali script possono essere eseguiti.

### Validazione input

Ogni input viene validato lato client e applicato lato server:

- Campi numerici: formato numerico valido, valore positivo, range ragionevole.
- Campi testuali: ripuliti dagli spazi, lunghezza minima e massima.
- Campi data: formato corretto.
- Campi valuta: corrispondenti alla lista ammessa.
- Imposizione lato server: anche se la validazione lato client viene aggirata, le regole Firestore rifiutano i dati non validi.

### Sicurezza event delegation

Budgee usa un sistema `EventDelegate` che centralizza la gestione degli eventi con allowlist esplicite sui selettori. Questo riduce la superficie di attacco rispetto ai gestori inline.

### Gestione errori

- Gli errori mostrati all'utente sono messaggi generici, mai stack trace o percorsi interni.
- Il log degli errori sul database è rate-limited (max 20 per sessione) e usa una whitelist rigorosa di campi; nessun dato sensibile, percorso file o codice sorgente viene mai salvato.
- Il log è scrivibile ma non leggibile dall'app: si consulta dalla console Firebase. Non esiste alcun sistema di notifica automatica sugli errori.

---

## Sicurezza offline

### Service Worker

Budgee funziona offline grazie a un Service Worker che cache le risorse essenziali:

- Strategia Network-First: si prova sempre prima la rete; la cache è usata solo quando si è offline.
- Cache con ambito per versione: ogni versione ha la propria cache; le cache vecchie vengono pulite all'aggiornamento.
- Esclusione API: le chiamate a Firebase e Google non vengono mai messe in cache e richiedono sempre la rete.

### Il passaggio di versione

Una versione nuova non prende più il controllo mentre la pagina aperta sta ancora eseguendo il codice vecchio. Quel codice chiede file che il rilascio corrente non pubblica più, e il risultato è un'app che si rompe a metà di un'operazione, senza spiegazioni. Ora la versione nuova resta in attesa e subentra solo quando l'utente accetta; se sono aperte più schede, il passaggio vale per tutte, perché una scheda lasciata sul codice vecchio è esattamente il caso che questo meccanismo evita.

### Sincronizzazione dati offline

- Le transazioni create offline finiscono in una coda di modifiche pendenti.
- Al rientro della connettività, le modifiche pendenti vengono sincronizzate con logica di retry (fino a 3 tentativi con backoff esponenziale).
- Se la sincronizzazione fallisce, l'utente viene avvisato e i dati restano locali fino alla prossima sincronizzazione riuscita.

---

## Sicurezza delle dipendenze

### Pacchetti npm

- Quasi tutte le dipendenze di sviluppo sono fissate alla versione esatta; tre (`vite`, `axe-core`, `@firebase/rules-unit-testing`) usano ancora l'intervallo `^`. Il lockfile è comunque committato, quindi ogni installazione riproduce l'albero verificato.
- `npm audit` viene eseguito prima del deploy per controllare vulnerabilità note.
- Le patch di sicurezza vengono applicate quando vengono divulgate vulnerabilità.

### Dipendenze CDN

- Gli script Firebase SDK caricati da CDN includono hash Subresource Integrity (SRI); il browser rifiuta file modificati.
- Uno script di audit dedicato controlla le dipendenze CDN contro il database vulnerabilità [OSV.dev](https://osv.dev/).
- Chart.js è ospitato localmente per eliminare la dipendenza esterna a runtime.

### Pipeline CI/CD

- A ogni push viene eseguito `npm audit --audit-level=high`; il deploy si blocca se compare una vulnerabilità ad alta gravità.
- Il check delle dipendenze CDN segnala librerie esterne con problemi noti.
- Le regole ESLint intercettano errori comuni con implicazioni di sicurezza (variabili inutilizzate, shadowing, uguaglianza non stretta).

---

## Privacy

- Nessuna analisi, nessun tracciamento: niente Google Analytics, niente Facebook Pixel, niente altri tracker.
- Nessuna pubblicità.
- Nessuna condivisione dati: i dati finanziari non vengono inviati a terze parti, eccetto i servizi che colleghi esplicitamente (per esempio Google Drive).
- Portabilità dei dati: puoi esportare l'intero account in qualsiasi momento, come ZIP con un JSON completo più un CSV per sezione. Viene letto dal database e non da quello che l'app ha in memoria, così non resta fuori niente in silenzio.
- Diritto alla cancellazione: puoi eliminare il tuo account e tutti i dati associati dalle impostazioni.
- La scansione degli scontrini è spenta finché non la accendi tu. Chiede un consenso a parte, distinto dall'uso di Budgee, perché l'immagine arriva a Google e uno scontrino può rivelare farmaci, visite mediche e abitudini. Il consenso è versionato: se l'informativa cambia nella sostanza la domanda torna, invece di riusare la vecchia risposta.
- Un'[informativa privacy](https://financial-management-by-bonn.web.app/src/pages/privacy.html) in italiano e inglese dice cosa viene raccolto e perché. Si raggiunge anche dalla schermata di accesso, prima di registrarsi: la raccolta comincia in quel momento, e l'art. 13 GDPR la vuole disponibile lì, non dopo.
- I [termini d'uso](https://financial-management-by-bonn.web.app/src/pages/terms.html) dicono cosa Budgee è e cosa non è. Le sezioni fiscali sono informative, le stime nascono dai dati che inserisci tu, e qualsiasi cosa abbia conseguenze fiscali va fatta controllare a un commercialista. Un avviso lo ripete dentro la procedura guidata, perché nessuno apre i termini prima di usare un calcolatore.
- Informativa e termini esistono solo nelle lingue in cui il testo è stato riletto da una persona. Una traduzione automatica di un documento che vincola non è una traduzione.
- Gli archivi che Budgee costruisce sanificano i nomi delle voci presi da Drive. Quei nomi non sono sotto il controllo di Budgee e possono contenere separatori di percorso o risalite di directory, e consegnare un archivio innocuo è responsabilità di chi lo produce.

---

## Limitazioni note e roadmap

La trasparenza è parte della sicurezza. Cosa Budgee oggi non fa e cosa è in programma:

| Limitazione | Stato | Note |
|------------|-------|------|
| Autenticazione a due fattori (2FA) | In programma | Per ora ci si appoggia a email/password più verifica email |
| Crittografia lato client | Non implementata | I dati sono cifrati a riposo da Firebase, ma non end-to-end |
| `'unsafe-inline'` sugli stili | Residuo | Gli script non ne hanno più bisogno. Resta sugli stili, per gli attributi `style` scritti nel markup: toglierlo richiede di spostarli tutti in classi |
| Due CDN nell'allowlist degli script | Da restringere | `cdn.jsdelivr.net` e `cdnjs.cloudflare.com` sono rimasti nella policy da quando Chart.js arrivava da lì. Oggi nessuno script viene caricato da quelle origini, quindi la policy è più larga di quanto serva |
| Rate limiting sulle letture del database | Non implementato | Firebase non offre rate limiting client-side sulle letture; il monitoraggio è server-side |

---

## Segnalare una vulnerabilità

Se trovi un problema di sicurezza, segnalalo in privato:

- Email: [andreabonacci95@protonmail.com](mailto:andreabonacci95@protonmail.com)
- Oggetto: `[SECURITY] Budgee - Breve descrizione`
- Includi i passaggi per riprodurre il problema e tutti i dettagli rilevanti

Le segnalazioni vengono lette e gestite il prima possibile. Per favore non divulgare pubblicamente il problema finché non è stato risolto.

---

<div align="center">

**© 2025-2026 Andrea Bonacci**

*Ultimo aggiornamento: settembre 2026*

</div>
