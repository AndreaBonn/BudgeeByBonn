# Budgee - Gestione Finanze Personali

<div align="center">

**Tieni sotto controllo le tue finanze, senza stress**

[Apri l'app](https://financial-management-by-bonn.web.app) · [Leggi il manuale](./USER_GUIDE_IT.md) · Installabile come PWA · Sincronizzata sul cloud · Gratuita

[![English](https://img.shields.io/badge/Read_in_English-4A90E2?style=for-the-badge)](./README.md)

</div>

---

## Cos'è Budgee

Budgee è una Progressive Web App (PWA) per le finanze personali. Tracci spese ed entrate, imposti budget mensili, segui investimenti e finanziamenti, conservi documenti finanziari su Google Drive e gestisci obiettivi di risparmio - da qualsiasi dispositivo, con sincronizzazione cloud in tempo reale.

È pensata per chi tiene i conti in un quaderno o in un foglio Excel disordinato e vuole qualcosa di più semplice senza rinunciare al dettaglio.

![Dashboard principale di Budgee](./docs/screenshots/it/01-spese-dashboard.png)

*Riepilogo del mese: entrate, spese, tasso di risparmio e totali deducibili in un'unica pagina.*

---

## Guardalo in azione

Un giro completo: accesso, dashboard delle spese, aggiunta di una spesa, entrate, risparmi, aggiunta e svincolo di un investimento, budget.

[![Demo di Budgee](./docs/media/budgee-demo.gif)](./docs/media/budgee-demo.mp4)

*L'anteprima qui sopra è accelerata. Clicca sulla GIF per aprire il video completo ([budgee-demo.mp4](./docs/media/budgee-demo.mp4)).*

---

## Cosa ti offre

- Tutto in una sola app: spese, entrate, budget mensili, investimenti, finanziamenti, conti aperti, obiettivi di risparmio, spese deducibili e documenti
- Nessun abbonamento, nessuna versione premium, nessuna pubblicità
- Funziona su smartphone, tablet e desktop; la installi dal browser e si comporta come un'app nativa
- Sincronizzazione cloud via Firebase, gli stessi dati ti seguono fra dispositivi
- Funziona offline; ciò che inserisci senza connessione si sincronizza al rientro
- Pronta in pochi minuti: ti registri e sei dentro
- Fotografi uno scontrino e l'AI compila il form al posto tuo, se lo vuoi e solo dopo che l'hai detto
- Rilevamento di pattern sulle tue spese con suggerimenti pratici
- Un backup settimanale sul tuo Google Drive e l'esportazione dell'intero account in un click
- Un pacchetto guidato di documenti per il commercialista quando arriva la dichiarazione
- Multi-valuta: EUR, USD, GBP e PLN con conversione automatica

È la prima volta che la usi? Il [manuale utente](./USER_GUIDE_IT.md) accompagna sezione per sezione, passo dopo passo.

---

## Come iniziare

### 1. Apri l'app

Vai su [financial-management-by-bonn.web.app](https://financial-management-by-bonn.web.app) da qualsiasi browser (Chrome, Safari, Firefox, Edge).

### 2. Crea il tuo account

Tocca **Registrati** e inserisci email e password. Riceverai un'email di verifica; clicca sul link per attivare l'account.

### 3. Installa sul dispositivo (opzionale)

Su mobile, il browser ti proporrà "Aggiungi alla schermata Home". Accetta e Budgee comparirà come un'app nativa. Su desktop, cerca l'icona di installazione nella barra degli indirizzi.

### 4. Aggiungi le prime transazioni

Inizia aggiungendo qualche spesa o entrata del mese corrente. Ogni transazione richiede importo, categoria e data.

### 5. Imposta i budget

Apri la sezione **Budget** e imposta i limiti di spesa per le categorie che vuoi monitorare. Budgee terrà traccia dei progressi in tempo reale.

---

## Come usare Budgee

### Spese ed entrate

Il cuore dell'app. Per ogni transazione puoi impostare:

- Importo e valuta (EUR, USD, GBP, PLN)
- Categoria, scelta da categorie gerarchiche che puoi personalizzare
- Sottocategoria per maggiore dettaglio
- Descrizione libera
- Data e metodo di pagamento: contanti, carta o app, assegno, bonifico, crypto, addebito automatico per le spese, voucher per le entrate. Il campo è obbligatorio quando compili il form a mano e arriva già preselezionato con il metodo che usi di solito per quella categoria
- Collegamento a una rata di finanziamento o a un versamento su un investimento
- Flag deducibile per la dichiarazione di fine anno
- Frequenza ricorrente (giornaliera, settimanale, mensile, annuale)

Cose che puoi fare in più:

- Importare transazioni da un file `.xlsx` in blocco
- Esportare i dati in CSV per backup o uso esterno
- Cercare fra le transazioni per data, categoria, importo, parola chiave, metodo di pagamento o elemento collegato
- Leggere statistiche in tempo reale: media giornaliera, proiezione di fine mese, giorno con spesa più alta
- Aprire grafici di trend mensile, settimanale, annuale e distribuzione per categoria

![Calendario spese e trend mensile](./docs/media/features/it/spese.gif)

*Il calendario giornaliero usa l'intensità del colore per evidenziare i giorni con spesa più alta; il grafico del trend mostra come la spesa si accumula giorno per giorno.*

### Scansionare uno scontrino invece di scriverlo

Il pulsante con la fotocamera accanto ai form di spesa ed entrata apre la scansione AI: carichi una foto o un PDF e importo, data, descrizione e categoria arrivano nel form già compilati. Controlli e salvi, così non viene scritto nulla che tu non abbia visto.

![Scansione AI di uno scontrino](./docs/screenshots/it/17-scansione-ai.png)

*Carichi una foto o un PDF e il form torna compilato. L'ultima parola resta sempre tua.*

Tre cose da sapere prima di usarla:

- La scansione gira su Google Gemini con una chiave API che inserisci tu, nel profilo. Senza chiave la funzione resta spenta.
- L'immagine esce da Budgee, quindi la prima volta ti viene chiesto un consenso a parte, che puoi revocare quando vuoi dalla stessa schermata.
- Se scansioni una fattura che sembra un'entrata mentre è aperto il form delle spese, Budgee si ferma e ti chiede quale delle due sia.

Dal telefono puoi anche condividere una foto direttamente da galleria o fotocamera: Budgee compare fra le app di condivisione e si apre sulla scansione con l'immagine già caricata.

### Uscite in contanti

Il contante è la parte di un bilancio che sfugge più facilmente. La sezione Spese mostra quanta parte della spesa ci passa, mese per mese e categoria per categoria, e dice a voce alta quanta parte del totale resta fuori dalla misura perché il metodo di pagamento manca.

![Quota di contante per mese e per categoria](./docs/screenshots/it/19-quota-contante.png)

*La quota si misura sugli importi, non sul numero di voci: dieci caffè non pesano quanto un affitto.*

Se il tuo storico è precedente al campo metodo di pagamento, il profilo mostra la copertura e ti propone di completare i buchi in blocco, una categoria alla volta, suggerendo per ciascuna il metodo che usi più spesso.

### Budget

Imposti un limite di spesa mensile per ogni categoria. Budgee ti mostra:

- Quanto hai speso rispetto al limite, con barre di progresso
- Una proiezione di dove stai andando a fine mese
- Un click per copiare il budget del mese precedente, così non devi reinserire i limiti ogni volta

Le categorie di budget lavorano in gerarchia: prima la macro-categoria, poi le sotto-categorie. Decidi tu quanto scendere nel dettaglio.

![Budget con barre di progresso per categoria](./docs/media/features/it/budget.gif)

*Budget gerarchici per macro e sotto-categoria, con barre di progresso e avvisi di sforamento.*

### Risparmi

Una sezione dedicata che calcola i risparmi dalla differenza fra entrate e spese:

- Grafico mensile dei risparmi nel tempo
- Linea dei risparmi cumulati
- Saving rate (la quota di reddito che effettivamente tieni)
- Mesi migliori e peggiori a colpo d'occhio
- Rilevamento di pattern con suggerimenti brevi

![Analisi dei risparmi e trend mensile](./docs/screenshots/it/10-risparmi-trend.png)

*Trend dei risparmi mese per mese con totali dell'anno corrente e saving rate.*

### Investimenti

Il portafoglio in un unico posto:

- Tipi di asset: obbligazioni, conti deposito, azioni, fondi comuni, ETF, criptovalute, immobili e altro
- Dettagli per investimento: importo investito, data sottoscrizione, tasso di interesse, data scadenza, rendimento atteso lordo/netto, rendimento effettivo, note libere
- Barre di progresso temporali su quanto manca alla scadenza
- Collegamento di dividendi, interessi o affitti all'investimento giusto
- Collegamento di spese per tracciare versamenti di capitale aggiuntivi
- Uno storico dei movimenti per investimento: capitale versato, capitale svincolato, rendimenti incassati, ognuno modificabile o eliminabile anche dopo
- Rendimenti ricorrenti per ciò che rende a scadenza fissa, come una cedola o un affitto
- Totali di portafoglio: capitale investito, rendimenti attesi, rendimenti effettivi, tasso medio, prossima scadenza
- Ricerca con filtri multipli

![Portafoglio investimenti con progressi e rendimenti attesi](./docs/media/features/it/investimenti.gif)

*Ogni card mostra importo investito, rendimento atteso, tasso di interesse, progresso fino alla scadenza e azioni rapide per i movimenti di capitale.*

### Finanziamenti

Tieni traccia di ogni forma di debito:

- Tipologie: mutui casa, prestiti auto, prestiti personali, prestiti studenti, finanziamento telefono, riscatto laurea e altro
- Dettagli per finanziamento: importo, date di inizio e fine, rate totali, importo rata, tasso di interesse, rate pagate, totale pagato, saldo residuo, note
- Barre di progresso su quanto manca a chiudere il prestito
- Collegamento di spese alle rate per registrare i pagamenti reali
- Inserimento di rate pagate o importo cumulato
- Totali del portafoglio: totale prestato, totale da pagare (con interessi), già pagato, residuo, rata media mensile, progresso medio
- Ricerca e vista di dettaglio con storico pagamenti e piano di ammortamento

![Gestione finanziamenti con progresso pagamenti](./docs/screenshots/it/08-finanziamenti-lista.png)

*Mutui e prestiti con progresso delle rate, totale pagato, saldo residuo e tasso di interesse.*

### Documenti

Integrazione con Google Drive per tenere ordinati i documenti finanziari:

- 32 cartelle predefinite per buste paga, fatture incassate e pagate, spese detraibili, referti medici, documenti investimenti, documenti finanziamenti, contratti, assicurazioni, documenti fiscali, garanzie, estratti conto, carte di credito, bollette, tasse, immobili, veicoli, pensione, donazioni, spese scolastiche, criptovalute, spese condominiali, spese legali, spese veterinarie, giacenze medie e varie
- Cartelle organizzate per anno in automatico (2024, 2025, ...)
- Scegli quali cartelle visualizzare
- Link diretti che aprono le cartelle su Google Drive
- Autenticazione OAuth 2.0, senza condividere password
- Nomi delle cartelle tradotti in base alla lingua scelta
- Preferenze sulle cartelle salvate sul cloud e sincronizzate

### Backup sul tuo Drive

Una volta collegato Drive, ogni sette giorni Budgee scrive una copia dei tuoi dati in una cartella **Budgee Backup**. Tiene le ultime otto copie e cancella da sé le più vecchie.

Dietro non c'è un server, quindi il backup parte quando apri l'app e non a un orario fisso: se non apri Budgee per due settimane, la copia ti aspetta. L'app lo dice in chiaro invece di lasciartelo immaginare, e ti riporta quando è andato l'ultimo.

Su Drive, Budgee vede soltanto i file che ha creato lei. Il resto non lo può leggere.

### Documenti per il commercialista

Alla dichiarazione la parte difficile non sono i conti, è ricordarsi cosa consegnare. La procedura guidata **Documenti per il commercialista**, nel profilo, chiede che dichiarazione presenti, per quale anno e quali situazioni ti riguardano (casa, famiglia, spese sanitarie e così via). Poi ti accompagna solo nelle sezioni che contano davvero e per ognuna indica la cartella di Drive dove quei documenti stanno di solito.

![La procedura guidata chiede che dichiarazione presenti](./docs/screenshots/it/18-wizard-fiscale.png)

*Sette profili, dal dipendente che presenta il 730 all'impresa in contabilità ordinaria. Solo i capitoli che spunti diventano schermate.*

Quello che esce è un unico ZIP con i documenti che hai scelto, più ciò che Budgee sa già:

- Le spese detraibili dell'anno, raggruppate per categoria, con i totali
- L'elenco di cosa manca, con le voci obbligatorie tenute separate dalle facoltative, così il commercialista sa cosa chiederti
- Una segnalazione sulle spese detraibili pagate in contanti, che dal 2020 di norma perdono la detrazione del 19% salvo farmaci e strutture pubbliche
- L'elenco delle cose che in una cartella non ci vanno, come l'IBAN o i codici fiscali dei familiari a carico. Budgee le nomina senza contenerle: uno ZIP che viaggia per email non è il posto giusto.

La procedura segue la normativa fiscale italiana per l'anno d'imposta che scegli. Se dichiari altrove, l'elenco può non corrispondere a quello che ti serve.

### Transazioni ricorrenti

Automazione per spese ed entrate che si ripetono:

- Frequenze: giornaliera, settimanale (con giorno della settimana), mensile (primo giorno, ultimo giorno o giorno specifico), annuale
- Ogni occorrenza richiede conferma prima di finire nei record, così nulla viene aggiunto a tua insaputa
- Modifica importo, descrizione e metodo di pagamento; elimina una singola occorrenza o tutte le future; consulta lo storico delle conferme
- Sincronizzazione cloud via Firestore
- Usi tipici: affitto, stipendio, abbonamenti, bollette, assicurazioni, rate di finanziamento

### Analisi e report

La vista dei dati:

- Heatmap calendario con intensità di spesa per ogni giorno del mese
- Diagramma Sankey su dove vanno i tuoi soldi tra le categorie
- Grafici di trend cumulati per spese, entrate e risparmi nel tempo
- Rilevamento di pattern e anomalie nel comportamento di spesa
- Confronto fra il periodo corrente e quelli precedenti
- Suggerimenti calibrati sulle tue abitudini
- Ricerca con filtri su categoria, periodo, importo, parole chiave
- Report personalizzati per qualsiasi intervallo, con calcoli budget proporzionali

![Analisi categorie con macro e sotto-categoria](./docs/screenshots/it/11-categorie.png)

*L'analisi categorie ordina la spesa per macro-categoria, con drill-down nelle sotto-categorie e percentuale sul totale.*

La sezione Categorie confronta anche due anni interi, una categoria alla volta. Una categoria presente in un anno solo resta nella tabella con l'altro anno a zero, perché la riga che sparisce è di solito quella che vale la pena guardare.

![Confronto anno su anno per categoria](./docs/screenshots/it/20-confronto-anni.png)

*Differenza e variazione percentuale per categoria, con i due anni affiancati. Una valuta per volta: sommare valute diverse produrrebbe un numero senza significato.*

### Conti aperti

Traccia soldi che devi e soldi che ti devono:

- Tipi di conto: debito o credito
- Dettagli per conto: nome della persona o del fornitore, tipo, importo iniziale, saldo corrente, data di apertura, note
- Storico pagamenti con data e importo per ogni rata
- Conti automaticamente marcati chiusi quando il saldo arriva a zero
- Vista consolidata in un'unica lista o separata fra attivi e chiusi
- Transazioni collegate: ogni pagamento associato al conto
- Totali: crediti, debiti, saldo netto
- Ricerca per nome, tipo, importo o data
- Esportazione in CSV

### Obiettivi di risparmio

Imposta e segui obiettivi concreti:

- Tipi di obiettivo: una tantum con scadenza, oppure ricorrenti
- Dettagli per obiettivo: importo target, risparmiato finora, scadenza, percentuale di progresso
- Barre di progresso colorate e indicatori di completamento
- Quota del saldo liquido allocata agli obiettivi
- Crea, modifica, archivia o elimina un obiettivo in qualsiasi momento
- Calcolo automatico della quota mensile per raggiungere la scadenza
- Priorità per importanza e scadenza
- Conferma visiva al completamento
- Vista di archivio per gli obiettivi passati

### Spese deducibili

Una sezione dedicata alla stagione delle dichiarazioni:

- Marca le spese come deducibili mentre le registri
- Visualizza le deducibili raggruppate per anno fiscale
- Accesso rapido all'anno corrente e a quello precedente
- Drill-down su qualsiasi anno passato
- Aggiungi deducibili senza una spesa corrispondente (extra)
- Suddivisione per categoria, per vedere chi pesa di più
- Totale annuale
- Pronta per l'esportazione utile in dichiarazione
- Deducibili ricorrenti marcate in automatico
- Tracci anche versamenti di capitale deducibili

### Lingue

Italiano e inglese. Cambi lingua dalle impostazioni quando vuoi. Interfaccia, categorie, grafici, statistiche e nomi delle cartelle documenti seguono la lingua scelta.

### Tema chiaro e scuro

Scegli chiaro o scuro dall'header. La preferenza viene salvata e sincronizzata.

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

### Pensata per il mobile

Budgee è progettata mobile-first: ogni sezione si adatta agli schermi piccoli con navigazione a bottom-tab e layout compatti. Installala dal browser per lanciarla come un'app nativa.

<div align="center">
<img src="./docs/screenshots/it/14-mobile-spese.png" alt="Vista spese mobile" width="280">
&nbsp;&nbsp;&nbsp;
<img src="./docs/screenshots/it/15-mobile-calendario.png" alt="Calendario spese mobile" width="280">
</div>

### Tutorial interattivo

Alla prima apertura un tour guidato spiega le funzioni principali.

---

## Privacy e sicurezza

I tuoi dati finanziari sono sensibili e Budgee li tratta come tali:

- Ogni utente accede solo ai propri dati, garantito a livello di database
- Tutte le connessioni passano da HTTPS; i dati sono cifrati a riposo con AES-256
- Gli account devono verificare l'email prima dell'uso
- La password deve essere lunga almeno 8 caratteri, con maiuscole, minuscole e numeri
- Nessun tracciamento, nessuna pubblicità; Budgee non vende e non condivide i tuoi dati
- I dati in cache offline si sincronizzano al rientro su un canale cifrato

Nel profilo decidi anche cosa ne è dei tuoi dati:

![La schermata del profilo con copertura dei metodi e privacy](./docs/screenshots/it/21-profilo-privacy.png)

*Copertura dei metodi di pagamento, chiavi AI, informativa privacy, esportazione e cancellazione dell'account stanno tutte nella stessa schermata.*

- **Portati via tutto.** Un pulsante scarica l'intero account come ZIP: un JSON completo più un CSV per sezione. Viene letto dal database e non da ciò che l'app ha in memoria, così non resta indietro niente. L'unica cosa esclusa, di proposito, è la chiave API di Gemini.
- **Cancella l'account per davvero.** La cancellazione percorre ogni parte dell'account, non solo il documento principale, e chiede conferma con la password. Se si interrompe a metà, l'accesso successivo la riprende da dove si era fermata invece di lasciare dati orfani.
- **Leggi cosa viene raccolto.** L'[informativa privacy](https://financial-management-by-bonn.web.app/src/pages/privacy.html) è scritta in italiano e in inglese e si raggiunge dall'app stessa.
- **Di' no all'AI.** La scansione degli scontrini resta spenta finché non l'accendi tu, chiede un consenso a parte e si può revocare dalla stessa schermata.

Per l'inventario dettagliato delle misure di sicurezza, vai alla [documentazione sulla sicurezza](./SECURITY_IT.md).

---

## Sotto il cofano

Budgee è una Progressive Web App (PWA) basata su standard web:

| Componente | Tecnologia |
|-----------|-----------|
| Frontend | Vanilla JavaScript (moduli ES6+), HTML5, CSS3 con custom properties |
| Architettura | Modulare, organizzata per feature; event delegation; lifecycle esplicito |
| Grafici | Chart.js |
| Backend | Firebase (Firestore, Authentication, Cloud Functions, Hosting) |
| Documenti | Google Drive API con OAuth 2.0 |
| Scansione scontrini | Google Gemini, con chiave API fornita dall'utente |
| Notifiche | Telegram Bot API |
| Offline | Service Worker con caching Network-First, più la cache locale dell'SDK Firestore |
| Archiviazione | Transazioni divise in documenti mensili, così l'account continua a crescere oltre il limite di dimensione di un singolo record |
| Import/Export | SheetJS (xlsx) per Excel, fflate per gli archivi ZIP |
| Sicurezza | Content Security Policy, HTTPS forzato, sanitizzazione input, Firestore rules |

---

## Checklist delle funzionalità

- Tracciamento spese ed entrate con categorie, sottocategorie e metodi di pagamento
- Scansione AI degli scontrini, facoltativa, con la possibilità di condividere una foto direttamente dal telefono
- Quota di contante per mese e per categoria, più il completamento assistito dei metodi di pagamento mancanti
- Confronto anno su anno, una categoria alla volta
- Gestione budget con monitoraggio in tempo reale e avvisi
- Analisi dei risparmi con calcoli automatici e grafico di trend
- Portafoglio investimenti con rendimenti e date di scadenza
- Gestione finanziamenti con tracciamento pagamenti e visualizzazione del progresso
- Conti aperti per crediti e debiti
- Obiettivi di risparmio con progresso e scadenza
- Spese deducibili organizzate per anno
- Gestione documenti con integrazione Google Drive
- Backup settimanale sul tuo Drive, con le ultime otto copie conservate
- Pacchetto guidato di documenti per il commercialista, su sette profili fiscali
- Esportazione completa dell'account in ZIP e cancellazione che non lascia residui
- Transazioni ricorrenti con programmazione flessibile
- Ricerca su tutti i tipi di dato
- Insight sulle spese con rilevamento pattern
- Multi-valuta: EUR, USD, GBP, PLN
- Multi-lingua: italiano, inglese
- Tema chiaro e scuro
- Modalità offline con sincronizzazione automatica
- Installabile come PWA su qualsiasi dispositivo
- Import ed export Excel
- Sincronizzazione cloud
- Tutorial interattivo

---

## Licenza

Questo progetto è proprietario. L'app web è gratuita per uso personale. Per i dettagli vedi [LICENSE](./LICENSE).

---

## Feedback e supporto

Se hai feedback, suggerimenti o un bug da segnalare, scrivimi.

Leggi la [guida feedback](./FEEDBACK_IT.md) per sapere come:

- Segnalare bug in modo davvero utile
- Richiedere nuove funzionalità
- Condividere la tua esperienza
- Chiedere supporto

Contatto rapido: andreabonacci95@protonmail.com

---

## Autore

Creata da Andrea Bonacci - [github.com/AndreaBonn](https://github.com/AndreaBonn)

---

## Sostieni il progetto

Budgee è gratuita. Se ti aiuta a tenere sotto controllo le tue finanze e vuoi contribuire, puoi lasciare un'offerta tramite PayPal. L'importo lo scegli tu ed è del tutto facoltativo.

<div align="center">

[![Dona con PayPal](https://img.shields.io/badge/Dona-PayPal-00457C?logo=paypal&logoColor=white&style=for-the-badge)](https://paypal.me/AndreaBonacci19)

</div>

---

<div align="center">

*Il codice sorgente è privato, ma l'uso dell'app è completamente libero e gratuito.*

*Se Budgee ti è stata utile, lascia una stella al repository.*

**© 2025-2026 Andrea Bonacci**

</div>
