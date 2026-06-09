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

### Protezione brute-force

Firebase Authentication blocca i tentativi di login ripetuti falliti con una risposta `too-many-requests`.

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
- Le chiavi API per i servizi esterni (Telegram, Google) sono conservate lato server nella configurazione delle Firebase Cloud Functions, non nel codice frontend.

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
| Strict-Transport-Security | `max-age=31536000; includeSubDomains` | Forza HTTPS per 1 anno, sottodomini inclusi |
| X-Content-Type-Options | `nosniff` | Impedisce il MIME sniffing |
| X-Frame-Options | `DENY` | Blocca l'inclusione in iframe (protezione clickjacking) |
| Referrer-Policy | `strict-origin-when-cross-origin` | Limita le informazioni di referrer verso terze parti |
| Permissions-Policy | `camera=(), microphone=(), geolocation=()` | Disattiva funzionalità browser non usate |

### Content Security Policy (CSP)

Una Content Security Policy controlla quali risorse il browser può caricare:

- Script: solo dal dominio dell'app e da fonti fidate (Firebase, Google APIs, CDN Chart.js)
- Stili: solo dall'app e da Google Fonts
- Connessioni: solo verso Firebase, Google APIs, Telegram API e l'API dei tassi di cambio
- Plugin: completamente bloccati (`object-src 'none'`)
- URL base: vincolato al dominio dell'app (`base-uri 'self'`)

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
- Errori ripetuti generano notifiche su un canale Telegram privato.

---

## Sicurezza offline

### Service Worker

Budgee funziona offline grazie a un Service Worker che cache le risorse essenziali:

- Strategia Network-First: si prova sempre prima la rete; la cache è usata solo quando si è offline.
- Cache con ambito per versione: ogni versione ha la propria cache; le cache vecchie vengono pulite all'aggiornamento.
- Esclusione API: le chiamate a Firebase, Google e Telegram non vengono mai messe in cache e richiedono sempre la rete.

### Sincronizzazione dati offline

- Le transazioni create offline finiscono in una coda di modifiche pendenti.
- Al rientro della connettività, le modifiche pendenti vengono sincronizzate con logica di retry (fino a 3 tentativi con backoff esponenziale).
- Se la sincronizzazione fallisce, l'utente viene avvisato e i dati restano locali fino alla prossima sincronizzazione riuscita.

---

## Sicurezza delle dipendenze

### Pacchetti npm

- Tutte le dipendenze usano versioni fissate (`==`) per evitare aggiornamenti imprevisti.
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
- Portabilità dei dati: puoi esportare tutto in CSV in qualsiasi momento.
- Diritto alla cancellazione: puoi eliminare il tuo account e tutti i dati associati dalle impostazioni.

---

## Limitazioni note e roadmap

La trasparenza è parte della sicurezza. Cosa Budgee oggi non fa e cosa è in programma:

| Limitazione | Stato | Note |
|------------|-------|------|
| Autenticazione a due fattori (2FA) | In programma | Per ora ci si appoggia a email/password più verifica email |
| Crittografia lato client | Non implementata | I dati sono cifrati a riposo da Firebase, ma non end-to-end |
| Direttiva CSP `'unsafe-inline'` | In rimozione | Servita per codice legacy; il refactor con EventDelegate la sta sostituendo |
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

*Ultimo aggiornamento: aprile 2026*

</div>
