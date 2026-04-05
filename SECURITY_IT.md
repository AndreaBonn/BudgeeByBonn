# 🔐 Budgee — Documentazione sulla Sicurezza

<div align="center">

[![English](https://img.shields.io/badge/🇬🇧_Read_in_English-4A90E2?style=for-the-badge)](./SECURITY.md) &nbsp; [![Torna al README](https://img.shields.io/badge/📖_Torna_al_README-009246?style=for-the-badge)](./README_IT.md)

</div>

---

Questo documento descrive le misure di sicurezza implementate in Budgee per proteggere i tuoi dati e garantire un'esperienza d'uso sicura. Budgee gestisce informazioni finanziarie sensibili, e la sicurezza è parte integrante del progetto — non un'aggiunta a posteriori.

---

## 📑 Indice

- [Autenticazione e Sicurezza Account](#-autenticazione-e-sicurezza-account)
- [Protezione dei Dati](#️-protezione-dei-dati)
- [Sicurezza del Database](#️-sicurezza-del-database)
- [Sicurezza di Rete](#-sicurezza-di-rete)
- [Sicurezza Applicativa](#-sicurezza-applicativa)
- [Sicurezza Offline](#-sicurezza-offline)
- [Sicurezza delle Dipendenze](#-sicurezza-delle-dipendenze)
- [Privacy](#-privacy)
- [Limitazioni Note e Roadmap](#️-limitazioni-note-e-roadmap)

---

## 🔑 Autenticazione e Sicurezza Account

### Autenticazione Email e Password

Budgee utilizza **Firebase Authentication**, una piattaforma di identità consolidata di Google, per la gestione di tutti gli account.

- La **verifica email** è obbligatoria — i nuovi account devono verificare il proprio indirizzo email prima di accedere a qualsiasi funzionalità
- I **requisiti password** impongono uno standard minimo di complessità:
  - Almeno 8 caratteri
  - Almeno una lettera maiuscola (A-Z)
  - Almeno una lettera minuscola (a-z)
  - Almeno un numero (0-9)
- Il **reset della password** è gestito interamente da Firebase tramite link email sicuri — Budgee non memorizza né trasmette mai le password in chiaro

### Gestione delle Sessioni

- Le sessioni di autenticazione sono gestite da Firebase con **token JWT**
- Le sessioni scadono automaticamente secondo le politiche di sicurezza di Firebase
- Il **logout** cancella tutti i dati locali — token di sessione, dati in cache e preferenze utente

### Eliminazione Account

L'eliminazione dell'account è un processo a più passaggi con garanzie di sicurezza:

1. Conferma esplicita tramite finestra di dialogo
2. **Re-autenticazione richiesta** — devi inserire nuovamente la password per dimostrare la tua identità
3. Tutti i dati utente vengono eliminati dal database (spese, entrate, budget, investimenti, finanziamenti, obiettivi, impostazioni)
4. L'account Firebase Authentication viene rimosso permanentemente
5. Tutti i dati in cache locale vengono cancellati

### Protezione Brute-Force

Firebase Authentication include una protezione integrata contro tentativi di accesso brute-force. Dopo diversi tentativi di login falliti, il sistema blocca temporaneamente ulteriori tentativi con una risposta `too-many-requests`.

---

## 🛡️ Protezione dei Dati

### Crittografia

| Livello | Metodo | Dettagli |
|---------|--------|----------|
| **In transito** | TLS/HTTPS | Tutte le connessioni tra il browser e Firebase sono crittografate. HSTS (HTTP Strict Transport Security) è attivo con durata di 1 anno, inclusi i sottodomini. |
| **A riposo** | AES-256 | Firebase Firestore crittografa automaticamente tutti i dati salvati usando crittografia AES-256, gestita dall'infrastruttura Google Cloud. |

### Isolamento dei Dati

I dati di ogni utente sono completamente isolati:

- Le regole di sicurezza del database impongono che **puoi leggere e scrivere solo i tuoi dati**
- Non esiste un pannello di amministrazione o dati condivisi — nemmeno lo sviluppatore può accedere ai dati dei singoli utenti tramite l'app
- Ogni operazione sul database verifica l'identità dell'utente autenticato prima di procedere

### Gestione Token Sensibili

- I **token OAuth di Google Drive** sono salvati in `sessionStorage` (cancellati automaticamente alla chiusura della scheda del browser), mai in memoria persistente
- Le **credenziali utente** non vengono mai salvate localmente — solo il token di sessione gestito da Firebase
- Le **chiavi API** per i servizi esterni (Telegram, Google) sono conservate lato server nella configurazione di Firebase Cloud Functions, mai esposte nel codice frontend

---

## 🗄️ Sicurezza del Database

### Regole di Sicurezza Firestore

Budgee utilizza un **approccio a whitelist** per la sicurezza del database — tutto è negato per impostazione predefinita, e solo operazioni specifiche sono esplicitamente permesse.

**Protezioni chiave:**

- **Accesso solo al proprietario** — ogni operazione di lettura e scrittura richiede autenticazione e verifica che l'utente richiedente sia il proprietario del documento
- **Validazione dei campi** — il database rifiuta dati che non corrispondono a tipi, intervalli e dimensioni attesi:
  - Gli importi monetari devono essere numeri positivi inferiori a un massimo ragionevole (10 milioni)
  - Le stringhe (descrizioni, nomi) hanno limiti massimi di lunghezza (200 caratteri)
  - I valori di valuta devono essere tra quelli approvati (EUR, USD, GBP, PLN)
  - I tassi di interesse devono essere tra 0% e 100%
  - Gli array hanno limiti di dimensione (es. max 10.000 spese per utente)
- **Limiti dimensione documenti** — previene abusi limitando il numero di campi e la dimensione complessiva dei documenti
- **Log errori in sola scrittura** — i report di errore possono essere creati ma mai letti o modificati dal client, prevenendo fughe di informazioni
- **Deny di default** — qualsiasi percorso del database non esplicitamente permesso è automaticamente bloccato:
  ```
  match /{document=**} {
    allow read, write: if false;
  }
  ```

---

## 🌐 Sicurezza di Rete

### Header di Sicurezza HTTP

Budgee configura i seguenti header di sicurezza su ogni risposta:

| Header | Valore | Scopo |
|--------|--------|-------|
| **Strict-Transport-Security** | `max-age=31536000; includeSubDomains` | Forza HTTPS per 1 anno, inclusi i sottodomini |
| **X-Content-Type-Options** | `nosniff` | Impedisce al browser di indovinare il tipo di file (attacchi MIME sniffing) |
| **X-Frame-Options** | `DENY` | Impedisce che l'app venga incorporata in iframe (protezione clickjacking) |
| **Referrer-Policy** | `strict-origin-when-cross-origin` | Limita le informazioni di referrer inviate a terze parti |
| **Permissions-Policy** | `camera=(), microphone=(), geolocation=()` | Disabilita funzionalità del browser non necessarie (fotocamera, microfono, geolocalizzazione) |

### Content Security Policy (CSP)

Una Content Security Policy controlla quali risorse il browser può caricare:

- **Script**: solo dal dominio dell'app e da fonti fidate (Firebase, Google APIs, CDN Chart.js)
- **Stili**: solo dall'app e da Google Fonts
- **Connessioni**: solo verso Firebase, Google APIs, Telegram API e l'API dei tassi di cambio
- **Plugin**: completamente bloccati (`object-src 'none'`)
- **URL base**: vincolato al dominio dell'app (`base-uri 'self'`)

### Controllo Cache

- **Pagine HTML**: sempre rivalidate con il server (mai servite se obsolete)
- **JavaScript e CSS**: in cache per 24 ore (i nomi dei file includono hash del contenuto che garantiscono versioni aggiornate dopo gli update)
- **Immagini e font**: in cache per 1 anno (asset immutabili con hash del contenuto)

---

## 🔒 Sicurezza Applicativa

### Prevenzione XSS (Cross-Site Scripting)

Tutti i dati forniti dall'utente (descrizioni, nomi di categorie, note) vengono **sanitizzati prima di essere visualizzati** nell'interfaccia:

- **Escaping HTML** — caratteri come `<`, `>`, `"`, `'`, `&` vengono convertiti in equivalenti sicuri prima del rendering
- **Escaping attributi** — protezione aggiuntiva per i dati inseriti negli attributi HTML
- **Content Security Policy** — limita quali script possono essere eseguiti nel browser

### Validazione Input

Ogni input dell'utente viene validato sia lato client sia imposto lato server:

- **Campi numerici**: verificato formato numerico valido, valore positivo e intervallo ragionevole
- **Campi testuali**: ripuliti dagli spazi, verificata lunghezza minima/massima
- **Campi data**: validato il formato corretto
- **Campi valuta**: devono corrispondere alla lista delle valute approvate
- **Imposizione lato server**: anche se la validazione lato client viene aggirata, le regole di sicurezza Firestore rifiutano i dati non validi

### Sicurezza Event Delegation

Budgee utilizza un sistema `EventDelegate` che centralizza la gestione degli eventi con allowlist esplicite basate su selettori CSS, riducendo la superficie di attacco rispetto ai gestori di eventi inline.

### Gestione Errori

- Gli **errori mostrati all'utente** presentano messaggi generici e utili — mai dettagli tecnici, stack trace o percorsi interni
- Il **log degli errori** nel database è limitato (max 20 per sessione) e utilizza una whitelist rigorosa di campi — nessun dato sensibile, percorso di file o codice sorgente viene mai memorizzato
- **Monitoraggio errori critici** — il sistema rileva errori ripetuti e invia avvisi tramite un canale Telegram privato

---

## 📴 Sicurezza Offline

### Service Worker

Budgee funziona offline tramite un Service Worker che memorizza in cache le risorse essenziali:

- **Strategia Network-First** — prova sempre a recuperare dati aggiornati dal server; usa la cache solo quando è offline
- **Cache con ambito per versione** — ogni versione dell'app ha la propria cache; le cache vecchie vengono pulite automaticamente all'aggiornamento
- **Esclusione API** — le chiamate a Firebase, Google e Telegram non vengono mai memorizzate in cache (richiedono sempre la rete)

### Sincronizzazione Dati Offline

- Le transazioni create offline vengono salvate in una **coda di modifiche pendenti**
- Quando la connettività ritorna, le modifiche pendenti vengono sincronizzate con il server con **logica di retry** (fino a 3 tentativi con backoff esponenziale)
- Se la sincronizzazione fallisce, l'utente viene notificato e i dati vengono preservati localmente fino alla prossima sincronizzazione riuscita

---

## 📦 Sicurezza delle Dipendenze

### Pacchetti npm

- Tutte le dipendenze utilizzano **versioni fissate** (`==`) per prevenire aggiornamenti imprevisti
- **`npm audit`** viene eseguito per verificare vulnerabilità note prima del deployment
- Le patch di sicurezza vengono applicate tempestivamente quando vengono divulgate vulnerabilità

### Dipendenze CDN

- Gli script Firebase SDK caricati da CDN includono **hash di Subresource Integrity (SRI)** — il browser verifica che il file scaricato non sia stato manomesso
- Uno script di audit dedicato verifica le dipendenze CDN rispetto al database di vulnerabilità [OSV.dev](https://osv.dev/)
- Chart.js è ospitato localmente (non caricato da CDN) per eliminare la dipendenza esterna a runtime

### Pipeline CI/CD

- **Audit di sicurezza automatico** ad ogni push — `npm audit --audit-level=high` blocca il deployment se vengono trovate vulnerabilità ad alta gravità
- **Verifica dipendenze CDN** — avvisa se una libreria caricata esternamente ha problemi noti
- **ESLint** applica regole di qualità del codice che intercettano potenziali problemi di sicurezza (variabili inutilizzate, shadowing di variabili, uguaglianza stretta)

---

## 🔏 Privacy

- **Nessuna analisi o tracciamento** — Budgee non utilizza Google Analytics, Facebook Pixel o alcun servizio di tracciamento
- **Nessuna pubblicità** — l'app è completamente priva di pubblicità
- **Nessuna condivisione dati** — i tuoi dati finanziari non vengono mai inviati a terze parti (eccetto i servizi che colleghi esplicitamente, come Google Drive)
- **Portabilità dei dati** — puoi esportare tutti i tuoi dati in CSV in qualsiasi momento
- **Diritto alla cancellazione** — elimina il tuo account e tutti i dati associati in modo permanente dalle impostazioni

---

## ⚠️ Limitazioni Note e Roadmap

La trasparenza è parte della sicurezza. Ecco cosa Budgee attualmente **non** fa, e cosa è in programma:

| Limitazione | Stato | Note |
|------------|-------|------|
| Autenticazione a due fattori (2FA) | In programma | Attualmente si basa su email/password + verifica email |
| Crittografia lato client | Non implementata | I dati sono crittografati a riposo da Firebase, ma non con crittografia end-to-end |
| Direttiva CSP `'unsafe-inline'` | In fase di eliminazione | Necessaria per codice legacy; in sostituzione attiva con EventDelegate |
| Rate limiting sulle letture del database | Non implementato | Firebase non offre rate limiting sulle letture lato client; monitorato lato server |

---

## 📬 Segnalare una Vulnerabilità

Se scopri un problema di sicurezza, ti chiediamo di segnalarlo in modo responsabile:

- **Email**: [andreabonacci95@protonmail.com](mailto:andreabonacci95@protonmail.com)
- **Oggetto**: `[SECURITY] Budgee — Breve descrizione`
- Includi i passaggi per riprodurre il problema e tutti i dettagli rilevanti

Prendo tutte le segnalazioni seriamente e risponderò il prima possibile. Ti chiedo di non divulgare il problema pubblicamente finché non sarà stato risolto.

---

<div align="center">

**© 2025-2026 Andrea Bonacci**

*Ultimo aggiornamento: Aprile 2026*

</div>
