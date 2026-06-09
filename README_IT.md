# 💰 Budgee — Gestione Finanze Personali

<div align="center">

**Tieni sotto controllo le tue finanze, senza stress**

🌐 [**Apri l'App**](https://financial-management-by-bonn.web.app) &nbsp;•&nbsp; 📱 Installabile &nbsp;•&nbsp; ☁️ Sincronizzazione Cloud &nbsp;•&nbsp; 🆓 Gratuita

[![English](https://img.shields.io/badge/🇬🇧_Read_in_English-4A90E2?style=for-the-badge)](./README.md)

</div>

---

## 💡 Cos'è Budgee

Budgee è una Progressive Web App (PWA) completa per la gestione delle finanze personali. Traccia spese ed entrate, imposta budget, gestisci investimenti e finanziamenti, organizza documenti finanziari e raggiungi i tuoi obiettivi di risparmio — tutto in un unico posto, accessibile da qualsiasi dispositivo con sincronizzazione cloud in tempo reale.

Non è un foglio Excel. Non è un'app complicata. È uno strumento pensato per chi vuole il controllo completo delle proprie finanze senza la complessità.

![Dashboard principale di Budgee](./docs/screenshots/it/01-spese-dashboard.png)

*Riepilogo del mese: entrate, spese, tasso di risparmio e totali deducibili a colpo d'occhio.*

---

## 🎯 Perché usare Budgee

- **Gestione finanziaria completa** — spese, entrate, budget, investimenti, finanziamenti, conti aperti, obiettivi di risparmio, spese deducibili e documenti in un'unica app
- **Gratis, per sempre** — nessun abbonamento, nessuna versione premium nascosta, nessuna pubblicità
- **Funziona ovunque** — smartphone, tablet, desktop; installabile come app nativa (PWA) sulla home screen
- **I dati sono tuoi** — sincronizzazione cloud sicura con Firebase su tutti i tuoi dispositivi
- **Funziona anche offline** — puoi registrare transazioni anche senza connessione; tutto si sincronizza automaticamente quando torni online
- **Pronta all'uso in 2 minuti** — crea un account e inizia subito, senza configurazioni complesse
- **Insights intelligenti** — analisi automatica dei tuoi pattern di spesa con suggerimenti pratici
- **Supporto multi-valuta** — gestisci finanze in EUR, USD, GBP e PLN con conversione automatica

---

## 🚀 Come iniziare

### 1. Apri l'app

Vai su [financial-management-by-bonn.web.app](https://financial-management-by-bonn.web.app) da qualsiasi browser — Chrome, Safari, Firefox, Edge.

### 2. Crea il tuo account

Tocca **Registrati** e inserisci email e password. Riceverai un'email di verifica — clicca il link per attivare il tuo account. Fatto.

### 3. Installa sul dispositivo (opzionale)

Su mobile, il browser ti proporrà "Aggiungi alla schermata Home". Accetta, e Budgee comparirà come un'app nativa con la sua icona. Su desktop, cerca l'icona di installazione nella barra degli indirizzi.

### 4. Aggiungi le prime transazioni

Ci sei! Inizia aggiungendo qualche spesa o entrata di questo mese. Ogni transazione richiede un importo, una categoria e una data.

### 5. Imposta i budget

Vai alla sezione **Budget** e imposta i limiti di spesa per le categorie che vuoi monitorare. Budgee terrà traccia dei tuoi progressi in tempo reale.

---

## 📋 Come usare Budgee

### 💸 Spese ed Entrate

Il cuore dell'app. Registra ogni transazione con:
- **Importo** e **valuta** (EUR, USD, GBP, PLN supportate)
- **Categoria** — scegli tra categorie gerarchiche predefinite o creane di personalizzate
- **Sottocategorie** — organizza le spese con sottocategorie dettagliate
- **Descrizione** — note opzionali per il contesto
- **Data** e **metodo di pagamento** (contanti, digitale, bonifico, crypto, addebito automatico, assegni)
- **Collegamento finanziamenti** — associa i pagamenti delle spese alle rate dei prestiti
- **Collegamento investimenti** — traccia i versamenti di capitale nei tuoi investimenti
- **Flag deducibile** — marca le spese come deducibili ai fini fiscali
- **Transazioni ricorrenti** — automatizza spese ed entrate che si ripetono (giornaliere, settimanali, mensili, annuali)

Puoi anche:
- **Importare da Excel** — carica un file `.xlsx` per aggiungere transazioni in blocco
- **Esportare in CSV** — scarica i tuoi dati per uso esterno o backup
- **Ricerca avanzata** — trova qualsiasi transazione per periodo, categoria, importo, parola chiave, metodo di pagamento o elemento collegato
- **Statistiche in tempo reale** — media giornaliera, proiezione fine mese, giorno con spesa massima
- **Grafici interattivi** — trend mensili, pattern settimanali/mensili/annuali, distribuzione per categoria

![Calendario spese e trend mensile](./docs/screenshots/it/02-calendario-trend.png)

*La heatmap giornaliera evidenzia i giorni con spesa più alta; il trend mensile mostra l'evoluzione giorno per giorno.*

### 🎯 Budget

Imposta un limite di spesa mensile per ogni categoria. Budgee ti mostra:
- Quanto hai speso rispetto al limite (con barre di progresso visive)
- Una previsione della spesa totale a fine mese
- **Copia il budget del mese precedente** in un click — non serve reinserire i limiti ogni mese

Le categorie di budget supportano una gerarchia con macro-categorie e sotto-categorie, così puoi monitorare la spesa al livello di dettaglio che preferisci.

![Budget con barre di progresso per categoria](./docs/screenshots/it/04-budget-progress.png)

*Budget gerarchici per macro e sotto-categoria, con barre di progresso in tempo reale e avvisi di sforamento.*

### 📊 Risparmi

Una sezione dedicata che calcola automaticamente i tuoi risparmi dalla differenza tra entrate e spese:
- **Grafico trend mensile** — vedi come evolvono i tuoi risparmi nel tempo
- **Risparmi cumulati** — monitora la crescita totale dei tuoi risparmi
- **Saving rate** — che percentuale del tuo reddito stai effettivamente risparmiando
- **Mesi migliori e peggiori** — capisci i tuoi pattern a colpo d'occhio
- **Insights automatici** — Budgee rileva pattern e ti dà suggerimenti

![Analisi dei risparmi e trend mensile](./docs/screenshots/it/10-risparmi-trend.png)

*Trend dei risparmi mese per mese con totali dell'anno corrente e saving rate.*

### 📈 Investimenti

Tieni traccia dell'intero portafoglio in un unico posto:
- **Tipi di asset**: obbligazioni, conti deposito, azioni, fondi comuni, ETF, criptovalute, immobili e altro
- **Tracciamento dettagliato**: importo investito, data sottoscrizione, tasso di interesse, data scadenza, rendimenti attesi lordi/netti, rendimenti effettivi, note personalizzate
- **Visualizzazione progresso**: barre di progresso temporali che mostrano il tempo trascorso fino alla scadenza
- **Collegamento entrate**: collega automaticamente dividendi, interessi o affitti a investimenti specifici
- **Tracciamento capitale**: collega pagamenti di spese per tracciare versamenti di capitale aggiuntivi
- **Statistiche riepilogative**: totale investito, rendimenti attesi totali, rendimenti effettivi totali, tasso di interesse medio, prossima scadenza
- **Ricerca avanzata**: trova investimenti rapidamente con filtri multipli

![Portafoglio investimenti con progressi e rendimenti attesi](./docs/screenshots/it/06-investimenti-lista.png)

*Ogni asset mostra importo investito, rendimento atteso, tasso di interesse, progresso alla scadenza e azioni rapide per i versamenti.*

### 💳 Finanziamenti

Gestisci tutti i tuoi debiti con tracciamento completo:
- **Tipologie**: mutui casa, prestiti auto, prestiti personali, prestiti studenti, finanziamenti telefono, riscatto laurea e altro
- **Informazioni dettagliate**: importo prestito, date inizio/fine, rate totali, importo rata, tasso di interesse, rate pagate, totale pagato, saldo residuo, note personalizzate
- **Visualizzazione progresso**: barre di progresso visive che mostrano la percentuale di completamento del pagamento
- **Collegamento spese**: collega automaticamente i pagamenti delle rate per tracciare gli importi pagati
- **Gestione pagamenti**: inserisci rate pagate o importo totale pagato
- **Statistiche riepilogative**: totale prestato, totale da pagare (con interessi), totale già pagato, totale rimanente, rata mensile media, progresso medio
- **Ricerca avanzata**: trova prestiti rapidamente con filtri multipli
- **Modal dettagliato**: visualizza informazioni complete sul prestito con storico pagamenti e piano di ammortamento

![Gestione finanziamenti con progresso pagamenti](./docs/screenshots/it/08-finanziamenti-lista.png)

*Mutui e prestiti con progresso delle rate, totale pagato, saldo residuo e tasso di interesse.*

### 📁 Documenti

Integrazione perfetta con Google Drive per la gestione dei documenti finanziari:
- **27+ cartelle predefinite**: buste paga, fatture (incassate/pagate), spese detraibili, referti medici, documenti investimenti, documenti finanziamenti, contratti, assicurazioni, documenti fiscali, garanzie, estratti conto bancari, carte di credito, bollette e utenze, tasse e tributi, patrimonio immobiliare, veicoli, pensione, donazioni, spese scolastiche, criptovalute, spese condominiali, spese legali, spese veterinarie, giacenze medie e varie
- **Organizzazione automatica per anno**: cartelle organizzate automaticamente per anno (2024, 2025, ecc.)
- **Vista personalizzabile**: scegli quali cartelle visualizzare in base alle tue esigenze
- **Accesso diretto**: link rapidi per aprire le cartelle direttamente su Google Drive
- **OAuth 2.0 sicuro**: autenticazione sicura senza condividere password
- **Multi-lingua**: nomi cartelle tradotti automaticamente in base alla lingua preferita
- **Sincronizzazione cloud**: preferenze salvate e sincronizzate tra dispositivi

### 🔄 Transazioni Ricorrenti

Sistema completo di automazione per spese ed entrate che si ripetono:
- **Frequenze flessibili**: giornaliera, settimanale (scegli il giorno), mensile (primo giorno, ultimo giorno o giorno specifico), annuale
- **Conferma manuale**: ogni occorrenza richiede conferma prima della registrazione — controllo completo su cosa viene aggiunto
- **Gestione completa**: modifica importo, descrizione e metodo di pagamento; elimina singole occorrenze o tutte le future; visualizza storico conferme
- **Notifiche intelligenti**: modal eleganti per un'esperienza premium
- **Sincronizzazione cloud**: transazioni ricorrenti salvate su Firestore e sincronizzate tra dispositivi
- **Esempi**: affitto, stipendio, abbonamenti, bollette, premi assicurativi, rate prestiti

### 🤖 Insights e Report

Budgee analizza i tuoi dati e li presenta visivamente:
- **Heatmap calendario** — vedi l'intensità delle tue spese per ogni giorno del mese
- **Diagramma Sankey** — visualizza dove vanno i tuoi soldi tra le categorie
- **Grafici trend cumulativi** — monitora spese, entrate e risparmi nel tempo
- **Rilevamento pattern** — identificazione automatica di pattern di spesa e anomalie
- **Analisi comparativa** — confronta il periodo corrente con periodi precedenti
- **Suggerimenti di ottimizzazione** — raccomandazioni personalizzate basate sul tuo comportamento finanziario
- **Ricerca avanzata** — filtra per categoria, periodo, range di importo, parole chiave
- **Report personalizzati** — genera report per qualsiasi periodo con calcoli budget proporzionali
- **Insights automatici** — pattern, anomalie e suggerimenti basati sui tuoi dati

![Analisi categorie con macro e sotto-categoria](./docs/screenshots/it/11-categorie.png)

*L'analisi categorie ordina le spese per macro-categoria con drill-down nelle sotto-categorie e percentuale sul totale.*

### 🏦 Conti Aperti

Traccia crediti e debiti con persone o aziende:
- **Tipi di conto**: traccia soldi che devi (debiti) o soldi che ti devono (crediti)
- **Informazioni dettagliate**: nome persona/fornitore, tipo conto, importo iniziale, saldo corrente, data apertura, note
- **Storico transazioni**: registra pagamenti effettuati o ricevuti con date e importi
- **Tracciamento stato**: conti automaticamente marcati come chiusi quando il saldo raggiunge zero
- **Vista consolidata**: vedi tutti i conti in un'unica lista o separati per attivi/chiusi
- **Transazioni collegate**: visualizza tutti i pagamenti e incassi associati a ogni conto
- **Statistiche riepilogative**: crediti totali, debiti totali, saldo netto
- **Ricerca avanzata**: trova conti per nome, tipo, range importo o data
- **Esportazione**: scarica dati conti in CSV per analisi esterna

### 🎯 Obiettivi di Risparmio

Imposta e traccia i tuoi obiettivi finanziari:
- **Tipi di obiettivo**: obiettivi una tantum con scadenza o target di risparmio continui
- **Tracciamento dettagliato**: importo target, importo risparmiato, scadenza, percentuale di progresso
- **Progresso visivo**: barre di progresso con codici colore e indicatori di completamento
- **Allocazione intelligente**: traccia quanto del tuo saldo liquido è allocato agli obiettivi
- **Gestione obiettivi**: crea, modifica, archivia o elimina obiettivi secondo necessità
- **Calcoli automatici**: vedi quanto devi risparmiare mensilmente per raggiungere l'obiettivo
- **Sistema di priorità**: organizza obiettivi per importanza e scadenza
- **Celebrazione completamento**: feedback visivo quando gli obiettivi sono raggiunti
- **Tracciamento storico**: visualizza obiettivi archiviati e traguardi passati

### 📝 Spese Deducibili

Semplifica la stagione fiscale con il tracciamento dedicato delle spese deducibili:
- **Marcatura automatica**: marca le spese come deducibili quando le registri
- **Vista anno per anno**: vedi le spese deducibili organizzate per anno fiscale
- **Anno corrente e precedente**: accesso rapido ai due anni più rilevanti
- **Dati storici**: visualizza spese deducibili di qualsiasi anno passato
- **Deducibili extra**: aggiungi spese deducibili non tracciate come spese regolari
- **Suddivisione per categoria**: vedi quali categorie contribuiscono di più alle detrazioni
- **Calcoli totali**: somma automatica degli importi deducibili per anno
- **Pronto per l'esportazione**: scarica spese deducibili per la preparazione fiscale
- **Deducibili ricorrenti**: marca automaticamente spese ricorrenti come deducibili
- **Contributi investimenti**: traccia contributi investimenti deducibili

### 🌐 Supporto Multi-lingua

Budgee è disponibile in **Italiano** e **Inglese**. Cambia lingua in qualsiasi momento dalle impostazioni. Tutti gli elementi dell'interfaccia, categorie, grafici, statistiche e nomi cartelle documenti sono tradotti automaticamente.

### 🎨 Supporto Temi

Scegli tra modalità chiara e scura per una visualizzazione confortevole in qualsiasi condizione di illuminazione. La tua preferenza viene salvata e sincronizzata tra dispositivi.

<div align="center">
<table>
<tr>
<td><img src="./docs/screenshots/it/01-spese-dashboard.png" alt="Tema chiaro" width="420"></td>
<td><img src="./docs/screenshots/it/16-dark-mode.png" alt="Tema scuro" width="420"></td>
</tr>
<tr>
<td align="center"><em>Tema chiaro</em></td>
<td align="center"><em>Tema scuro</em></td>
</tr>
</table>
</div>

### 📱 Pensata per il Mobile

Budgee è mobile-first: ogni sezione si adatta agli schermi piccoli con navigazione a bottom-tab e layout compatti. Installala dal browser per lanciarla come un'app nativa.

<div align="center">
<img src="./docs/screenshots/it/14-mobile-spese.png" alt="Vista spese mobile" width="280">
&nbsp;&nbsp;&nbsp;
<img src="./docs/screenshots/it/15-mobile-calendario.png" alt="Calendario spese mobile" width="280">
</div>

### 📊 Tutorial Interattivo

Gli utenti alla prima esperienza sono guidati attraverso l'app con un tutorial interattivo che spiega tutte le funzionalità principali e come usarle efficacemente.

---

## 🔐 Privacy e Sicurezza

I tuoi dati finanziari sono sensibili. Budgee prende la sicurezza sul serio:

- **I tuoi dati restano tuoi** — ogni utente può accedere solo ai propri dati, garantito a livello di database
- **Crittografia in transito e a riposo** — tutte le connessioni usano HTTPS; i dati sono conservati con crittografia AES-256
- **Verifica email obbligatoria** — gli account devono essere verificati prima dell'uso
- **Policy password robusta** — minimo 8 caratteri con maiuscole, minuscole e numeri
- **Nessun tracciamento, nessuna pubblicità** — Budgee non vende né condivide i tuoi dati con nessuno
- **Sicuro anche offline** — i dati in cache locale vengono sincronizzati in modo sicuro quando ti riconnetti

Per una panoramica dettagliata di tutte le misure di sicurezza, consulta la [**Documentazione sulla Sicurezza**](./SECURITY_IT.md).

---

## 🛠️ Sotto il cofano

Budgee è una Progressive Web App (PWA) costruita con tecnologie web moderne:

| Componente | Tecnologia |
|-----------|-----------|
| **Frontend** | Vanilla JavaScript (moduli ES6+), HTML5, CSS3 con proprietà personalizzate CSS |
| **Architettura** | Design modulare con organizzazione basata su funzionalità, event delegation, gestione lifecycle |
| **Grafici** | Chart.js per visualizzazione dati interattiva |
| **Backend** | Firebase (Firestore, Authentication, Cloud Functions, Hosting) |
| **Documenti** | Google Drive API con OAuth 2.0 |
| **Notifiche** | Telegram Bot API per avvisi e report |
| **Offline** | Service Worker con strategia di caching Network-First |
| **Import/Export Dati** | SheetJS (xlsx) per Excel, JSZip per export compressi |
| **Sicurezza** | Content Security Policy, imposizione HTTPS, sanitizzazione input, regole sicurezza Firestore |

---

## 🚀 Riepilogo Funzionalità Principali

✅ **Tracciamento Spese ed Entrate** con categorie, sottocategorie e metodi di pagamento  
✅ **Gestione Budget** con monitoraggio in tempo reale e avvisi  
✅ **Analisi Risparmi** con calcoli automatici e visualizzazione trend  
✅ **Portafoglio Investimenti** con tracciamento rendimenti e date scadenza  
✅ **Gestione Finanziamenti** con tracciamento pagamenti e visualizzazione progresso  
✅ **Conti Aperti** per tracciare crediti e debiti  
✅ **Obiettivi di Risparmio** con tracciamento progresso e gestione scadenze  
✅ **Spese Deducibili** organizzate per anno  
✅ **Gestione Documenti** con integrazione Google Drive  
✅ **Transazioni Ricorrenti** con programmazione flessibile  
✅ **Ricerca Avanzata** su tutti i tipi di dati  
✅ **Insights Intelligenti** con rilevamento pattern e suggerimenti  
✅ **Multi-Valuta** (EUR, USD, GBP, PLN)  
✅ **Multi-Lingua** (Italiano, Inglese)  
✅ **Tema Scuro/Chiaro**  
✅ **Modalità Offline** con sincronizzazione automatica  
✅ **PWA** installabile su tutti i dispositivi  
✅ **Import/Export Excel**  
✅ **Sincronizzazione Cloud** tra dispositivi  
✅ **Tutorial Interattivo**

---

## 📄 Licenza

Questo progetto è proprietario. L'applicazione web è **gratuita per uso personale**. Vedi [LICENSE](./LICENSE) per i dettagli.

---

## 💬 Feedback e Supporto

Hai feedback, suggerimenti o hai trovato un bug? Mi piacerebbe sentirti!

👉 **[Leggi la Guida Feedback](./FEEDBACK_IT.md)** per scoprire come:
- Segnalare bug efficacemente
- Richiedere nuove funzionalità
- Condividere la tua esperienza
- Ottenere supporto

**Contatto rapido:** andreabonacci95@protonmail.com

---

## 👤 Autore

Creata da **Andrea Bonacci** — [github.com/AndreaBonn](https://github.com/AndreaBonn)

---

<div align="center">

*Il codice sorgente è privato, ma l'uso dell'app è completamente libero e gratuito.*

*Se Budgee ti è piaciuta o ti è stata utile, lascia una ⭐ al repository!*

**© 2025-2026 Andrea Bonacci**

</div>
