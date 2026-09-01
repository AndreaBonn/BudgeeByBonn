# Changelog Budgee

Questo documento riassume gli avanzamenti significativi del progetto dall'inizio (gennaio 2026) a oggi, raggruppati per **ere di valore** invece che per singola sessione. Sono stati esclusi i commit puramente cosmetici (formattazione, rename di variabili, allineamenti UI senza cambio di comportamento), i commit di pura pulizia interna senza impatto (rimozione `console.log`, fix di selettori CSS inutilizzati) e le sessioni vuote.

Arco temporale coperto: **gennaio 2026 → settembre 2026**, 128 sessioni di lavoro.

---

## Era 1 - Fondamenta funzionali (gennaio - febbraio 2026)

**Valore aggiunto**: le funzionalità che hanno definito cosa Budgee fa per l'utente. In questa era nascono le feature distintive dell'app, quelle che differenziano Budgee da un semplice tracker spese.

### Sistema "Liquidità disponibile" (sessioni #4 → #11)

L'app non si limita a sommare entrate e sottrarre uscite. Tiene conto separato di tre dimensioni:

- **Obiettivi di risparmio**: ogni obiettivo ha nome, importo target, deadline (o "obiettivo continuo"), priorità e colore. Calcolo automatico del risparmio mensile necessario, barre di progresso colorate, badge con il numero di obiettivi attivi.
- **Accantonamenti**: bottone "Aggiungi Risparmio" su ogni obiettivo, con shortcut rapidi (+50, +100, +200, Completa). La card liquidità mostra `1.000 € (+500 € accantonati)`, dove i 1.000 sono spendibili liberamente.
- **Investimenti dalla liquidità**: ogni investimento dichiara l'origine fondi (esterni, totalmente da liquidità, parzialmente da liquidità). Possibili nuovi versamenti (bottone "Investi") e prelievi (bottone "Svincola") con tracciamento del flusso.

Bug critico risolto (sessione #11): il calcolo della liquidità disponibile sottraeva due volte gli investimenti effettuati prima del set manuale del saldo. Ora sono considerati solo gli investimenti successivi a quella data.

### Tab "Conti Aperti" (sessione #8)

Gestione di debiti e crediti verso persone o fornitori (es. "Dentista Dr. Rossi"). Ogni conto ha tipo (debito/credito), categoria, barra di progresso. Bottoni: Paga/Incassa (crea automaticamente una spesa o entrata collegata), + Importo (nuova fattura sullo stesso conto), Modifica, Chiudi.

### Spese deducibili interattive (sessione #1)

Cliccando "Totale Spese" o "Totale Entrate" si apre il riepilogo per mese. Cliccando su un mese si vedono tutte le transazioni di quel mese (data, descrizione, categoria, importo), con icona distintiva per spese deducibili e ricorrenti.

### Analisi e confronti

- **Ripartizione spese per valuta** (sessione #14): tab Spese mostra quanto è stato speso in EUR, PLN, USD, GBP, con valore originale + conversione. Barre percentuali sul totale.
- **Confronto Entrate vs Uscite** (sessione #15): tab Categorie con selezione libera di macro-categorie (entrate vs uscite) tramite "pillole" cliccabili. Secondo livello sotto-categorie per analisi più fine. Click su categoria apre popup con tutte le transazioni.
- **Riepilogo periodo** (sessioni #24-#25): card riepilogativa su Spese/Entrate/Risparmi con saldo (verde positivo / rosso negativo), totale entrate, totale spese, previsione ricorrenze. Card cliccabili che aprono il dettaglio transazioni.

### Internazionalizzazione e categorie

- **i18n popup ricorrenti** (sessione #13): traduzione completa IT/EN di titoli, etichette, dropdown (frequenze, giorni settimana, metodi pagamento, tipi ricorrenza), messaggi.
- **Categorie crypto** (sessione #12): "Criptovalute" sotto "Finanza & Risparmi", allineata fra spese ed entrate. Suggerimento automatico su descrizioni crypto.
- **Etichette descrittive** (sessione #12b): tutte le voci generiche "Altro" sostituite con nomi specifici ("Altre Spese di Trasporto", "Altre Spese Sanitarie") in italiano e inglese.

---

## Era 2 - Sicurezza e protezione dei dati (febbraio → giugno 2026)

**Valore aggiunto**: ogni step di crescita dell'app è stato accompagnato da un audit di sicurezza. Le sessioni di security non hanno aggiunto funzionalità visibili ma hanno chiuso vettori di attacco specifici prima che potessero essere sfruttati.

### Protezione XSS

- **#18, #32**: protezione XSS su tutti i campi input utente (descrizioni, categorie). Caratteri `&`, `<`, `>` vengono mostrati come testo, non interpretati come HTML. La funzione di ricerca continua a evidenziare correttamente i risultati.
- **#29, #33**: sanitizzazione di 126 punti `innerHTML`, 16 file toccati dalla XSS prevention.
- **#52, #75**: audit approfondito, eliminazione di tutti i vettori XSS residui, vulnerabilità corretta sulla ricerca con caratteri speciali nei metodi di pagamento.
- **#80**: XSS in goals modal (targetAmount/savedAmount/deadline non escaped). Fix con `escapeHtml(String(...))`.
- **#83**: `escapeAttr` non importato in `investments-modals-popups.js`, mascherato dal mock globale nei test.

### Protezione CSV injection

- **#63**: vulnerabilità scoperta sull'export finanziamenti: nomi con `=HYPERLINK(...)` interpretati da Excel come formule. Fix con prefisso apostrofo sui caratteri pericolosi.
- **#65**: stessa protezione estesa agli investimenti.
- **#80**: helper `escapeCsvCell` con quote-doubling RFC 4180 + prefisso `'` su `=`, `+`, `-`, `@`, `\t`, `\r` per l'export conti aperti.

### Hardening database e auth

- **#33**: validazione dati nel database cloud (Firestore rules più restrittive), headers HTTP di protezione, gestione robusta sync offline.
- **#36**: regole Firestore rafforzate per accettare solo campi previsti, errori non espongono più informazioni tecniche sensibili, notifiche Telegram senza stack trace.
- **#38**: migrazione a UUID (`crypto.randomUUID()`) per tutti gli identificativi (spese, entrate, investimenti, ...). Elimina il rischio che doppi click in rapida successione sovrascrivano silenziosamente record diversi.
- **#57**: password con regole di complessità (8+ caratteri, maiuscole + minuscole + numeri), auto-recovery delle operazioni Firestore in caso di errore rete, messaggi Telegram protetti contro injection, rules cloud più restrittive con limiti sulle dimensioni dati.
- **#34**: sistema di backup manuale del database (`scripts/firestore-backup.sh`), tracking centralizzato degli errori, timeout di auto-logout dopo inattività.
- **#82**: validazione anno fiscale (parseInt overflow `9999999999` rifiutato, range `[1900, 2200]`).

### Silent failures resi visibili

L'app aveva diversi punti dove un errore di rete o di stato veniva ingoiato senza informare l'utente. Fix sistematico nelle sessioni #66, #70, #80:

- `loadFinancings` / `loadOpenAccounts`: ora mostrano `error-loading`.
- `removePaymentFromAccount` / `removeReceiptFromAccount`: ora mostrano `error-updating`.
- `saveRecurringExpenses` / `saveRecurringIncome`: notifica `sync-error`.
- `recordPayment` con template vuoto: fallback su `payment-exceeds-remaining`.
- `handleEditSubmit` su account-not-found: ora mostra `account-not-found`.
- Salvataggio errato su cloud per obiettivi di risparmio (non segnalato), caricamento errato che cancellava dati in memoria (ora preservati), aggiunta risparmio su obiettivo cancellato (ora con feedback).
- 3 log diagnostici aggiunti su pattern che fallivano silenziosamente: popup non riaperto, dropdown non popolato, azione utente sconosciuta.

---

## Era 3 - Performance e ottimizzazione caricamento (febbraio → marzo 2026)

**Valore aggiunto**: i tempi di caricamento sono passati da inaccettabili a competitivi. Ogni intervento è stato misurato.

| Intervento                                       | Sessione | Risultato misurato                         |
| ------------------------------------------------ | -------- | ------------------------------------------ |
| Code-splitting JavaScript on-demand              | #17      | Bundle iniziale 1.73 MB → 900 KB (-48%)    |
| Conversione immagini PNG → WebP                  | #23      | Asset immagini 6.2 MB → 340 KB (-95%)      |
| Cache offline completa                           | #22      | App pienamente operativa offline           |
| Code-splitting bundle main                       | #43      | `main.js` 668 KB → 383 KB (-43%)           |
| Migrazione ES Modules + Vite build               | #42      | 56 file separati → 1 bundle ottimizzato    |
| Build system con minify CSS+JS                   | #29      | CSS bundle 362 KB → 46 KB gzip (-87%)      |
| Memory leak event listeners                      | #40      | 240 `addEventListener` vs 5 `removeEventListener`: rapporto 11:1 ridotto con event delegation centralizzato |
| Service Worker allineato a nuova struttura       | #41      | Cache attiva, no errori in console         |

**Bug specifico risolto** (#43): i popup degli investimenti non si chiudevano correttamente con X / clic fuori / Escape. Errore "Investimento non trovato" su Modifica investimento. Entrambi corretti.

---

## Era 4 - Modernizzazione architetturale (marzo 2026)

**Valore aggiunto**: il codebase passa da uno script monolitico a un'architettura modulare. Le fondamenta che hanno reso possibile tutto il lavoro successivo di test e refactor.

### Riorganizzazione strutturale

- **#39**: struttura del progetto riordinata. Da 60+ file in cartella root a sottodirectory tematiche in `src/` (15 sottodirectory).
- **#37**: `app.js` ridotto del 61% (da 4.452 a 1.734 righe). Estratti 6 file specializzati.
- **#30**: `app.js` ridotto del 23.7% (da 13.150 a 10.030 righe). Il sistema di personalizzazione sezioni (visibilità, ordine, drag-drop) era duplicato 8 volte → ora `SectionsManager` unico riutilizzabile.
- **#54**: file investimenti diviso in 4 file più piccoli (era 2.829 righe).
- **#44**: CSS principale da 7.982 righe → 16 file modulari (max 1.492 righe ciascuno), organizzati per area funzionale.

### Nuovi sistemi interni

- **#34**: 5 nuovi servizi dedicati (backup, tracking errori, gestione storage, ...). Auto-logout dopo inattività.
- **#35**: `localStorage` centralizzato in StorageService (87% migrati), navigazione sezioni con sistema event-driven.
- **#36**: 61/63 `onclick` migrati a event delegation (97%).
- **#40**: `event-delegate.js` e `lifecycle-mixin.js` per prevenire memory leak.
- **#42**: 67 file JS migrati a ES modules, 120+ check `typeof` rimossi, 55+ assegnazioni `window.X` eliminate.
- **#46**: ~540 inline styles → ~85 residui (riduzione 84%); colori e dimensioni in CSS classes per supportare tema scuro.
- **#47**: ESLint flat config + correzione di 131 problemi pre-esistenti nascosti dalla vecchia configurazione (confronti imprecisi, traduzioni duplicate, ...).
- **#50**: lista parole chiave per suggerimento automatico categoria estratta in JSON dedicato.
- **#55-#56**: rimozione di tutti gli handler `onclick` inline per conformità CSP.

### Refactor architetturale di seconda generazione (giugno 2026)

Le sessioni #58-#83 hanno completato il lavoro su tutte le god-class residue.

| Area funzionale                       | File principale                              | Moduli puri estratti                                                                                                              |
| ------------------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Investimenti** (modali e popup)     | `investments-modals.js` (1.439 → 41)         | edit, transactions (5 modal), search filter, popup di portafoglio (6 popup aggregati)                                             |
| **Documenti / Google Drive**          | `documents.js`                               | token-state OAuth, session-data, folder-preferences, script-loader con polling e retry                                            |
| **Obiettivi di risparmio**            | `goals-manager.js`                           | form-templates (HTML), form-routing (dispatch azioni, parsing form)                                                               |
| **Finanziamenti**                     | `financing.js`, `financing-details-modal.js` | form-parsing, action-routing (7 azioni), grouping per tipo, calcoli importi/progress/interessi                                    |
| **Conti aperti**                      | `open-accounts.js`, `open-accounts-modals.js` | form-parsing + 3 validator, record-builders immutabili (apply/remove transazioni, chiusura/riapertura account), CSV export, filtering multi-criterio, action-routing 12 azioni |
| **Spese ricorrenti**                  | `recurring-expenses.js`, `recurring-modals.js` | action-routing (lock, dispatcher sicuro), reject-flow descriptor, recurrence builder, type-i18n bundle frozen                |
| **Insight avanzati**                  | `advanced-insights-ui.js`                    | data processors (sort, percentuale safe, classi colore risparmio, benchmark IT/EN), routing fonti (savings rate, impulse spending, heatmap) |
| **Budget mensili**                    | `budget-manager.js`                          | messages, sort helpers, routing                                                                                                   |
| **Entrate**                           | `income-manager.js`                          | list-item template, view helpers, messages, routing                                                                               |
| **Spese deducibili**                  | `deductible-manager.js`                      | reopen-plan (unifica 7 callsite duplicati), subcategory-dropdown (deduplica 3 implementazioni), popup action-routing (9 cases) |
| **Autenticazione**                    | `auth-manager.js`                            | messages, payload documento utente Firestore, error routing, form-state                                                           |
| **Popup totali / Edit popup / Filtri periodo / Report / Export** | più file                  | routing + form-state + aggregation, totale ~30 helper estratti                                                                    |

Riferimento: vedi anche il bilancio coverage nell'Era 7.

---

## Era 5 - Bug fix critici

**Valore aggiunto**: bug che, una volta scoperti, non sarebbero mai dovuti rimanere in produzione. Risolti in priorità.

- **#49 Fuso orario italiano**: tra mezzanotte e le 2 di notte, l'app mostrava le transazioni del mese precedente invece di quello corrente (usava UTC invece di Europe/Rome). Risolto su tutte le date dell'app.
- **#45 Console errors**: sistema cache offline su immagini mancanti, init spese ricorrenti, scrollbar Firefox.
- **#16b Permessi obiettivi**: regola Firestore mancante per obiettivi di risparmio; report button rimosso dove non più presente.
- **#11 Doppio conteggio liquidità**: investimenti pre-saldo sottratti due volte.
- **#72 Recurring binding**: tasti "Modifica" ed "Elimina" su spese ricorrenti non collegati correttamente.
- **#73 Recurring investimenti**: titoli "Spesa Ricorrente" invece di "Investimento Ricorrente"; campo "Finanziamento Collegato" mostrato erroneamente per investimenti.
- **#74 Spese deducibili - bottoni**: aggiunti pulsanti "Non Deducibile" e "Elimina" alle spese normali marcate come deducibili.
- **#76**: documentato nella sessione corrente.

### Bug latenti scoperti dai test (sessioni #61-#75)

Il refactor con TDD ha portato a galla bug pre-esistenti che il sistema riusciva a mascherare:

- Filtro "Ultimi 12 mesi" Risparmi non funzionante da gennaio a novembre (date generate male a inizio anno).
- "undefined 0.00" in stat card risparmi quando il simbolo valuta non era caricato.
- "Invalid Date" come etichetta asse X nei grafici risparmi su mesi mal formati dal cloud.
- "Rendimento: 0" invece del valore negativo reale per investimenti in perdita.
- Importi mancanti nei CSV come "undefined".
- Finanziamenti corrotti (rate totali = 0 o assenti) mostrati come "chiusi" quando dovevano essere "aperti".
- Errori di caricamento investimenti mostrati come lista vuota indistinguibile.
- "Investi dalla liquidità" senza saldo impostato mostrava liquidità fittizia da zero.
- Date deducibili "2024-13-45" non bloccate dalla validazione.
- "NaN" e "Invalid Date" visibili nelle categorie su dati anomali.
- Bottone "Converti in normale" deducibili che puntava a un metodo inesistente.
- Bug di fuso orario che assegnava una spesa al mese sbagliato.
- "Investimento Ricorrente" mostrato come "Spesa Ricorrente".
- NaN propagation in `computeComparisonTotals` su dati numerici particolari.
- Bug i18n (frasi non tradotte, categoria "Criptovalute" mancante in IT).

---

## Era 6 - UI/UX maturo (marzo 2026)

**Valore aggiunto**: l'aspetto dell'app passa da "generata da AI" a "progettata con intenzione" (riferimenti dichiarati: Apple, Linear).

### Refresh visivo

- **#27**: aspetto rinnovato: colori titoli puliti senza effetti sfumati, card più raffinate, animazioni ridotte a quelle funzionali, font Figtree per lettura piacevole. Accessibilità tastiera con indicatori focus ripristinati. Sistema notifiche con elemento `<dialog>` nativo del browser.
- **#28**: colori di sfondo più puliti e piatti (stile Apple/Linear), ombre neutre e sottili, feedback touch immediato. Supporto notch (iPhone X+, Pixel) via safe-area inset.
- **#29**: cambi sezione animati, errori form vicino al campo, navigazione col tasto "indietro" del browser, swipe gesture tra sezioni su mobile, bottone "Salta" per il tutorial. `rgba()` hardcodati 388 → 12 (-97%).
- **#26**: animazioni più fluide su smartphone, layout robusto su schermi piccoli, navigazione tastiera/screen reader migliorata, sezioni vuote con suggerimento azione (era "punteggio UI stimato 5.5/10 → 8/10").
- **#31**: animazioni standardizzate, sistema più sicuro per click sui pulsanti delle liste, loading state durante download dati.
- **#46**: tema chiaro/scuro funziona meglio (colori non più hardcoded), tema unico da cambiare in un punto.

### Bottoni stato vuoto

- **#27**: ogni sezione senza dati mostra un messaggio chiaro con bottone verde "Aggiungi" che apre direttamente il form.

### A11y dark mode

- **#35**: 6/6 WCAG AA failures corretti (contrasto colori in modalità scura).
- **#20**: 3 file CSS "rattoppo" eliminati, problemi di colore testo (scuro su sfondo verde) risolti alla radice.

---

## Era 7 - Test coverage massiccio (marzo → giugno 2026)

**Valore aggiunto**: Budgee parte praticamente senza test. In sei mesi diventa un progetto con copertura del 65% globale e oltre 5.500 test comportamentali. Ogni regressione futura sarà intercettata prima del deploy.

### Crescita della copertura

| Periodo                  | Test totali | Coverage Lines | Sessione di riferimento |
| ------------------------ | ----------- | -------------- | ----------------------- |
| Inizio progetto          | 0           | 0%             | -                       |
| Marzo (#48)              | 74          | n.d.           | #48 - test calcoli      |
| Marzo (#53)              | 105         | n.d.           | #53 - test Firestore    |
| Maggio (#58)             | 1.186       | n.d.           | #58 - god-class wave 1  |
| Maggio (#60)             | 1.439       | n.d.           | #60 - 5 god-class       |
| Maggio (#61)             | 1.568       | n.d.           |                         |
| Maggio (#62)             | 1.703       | 34.17%         | #62 - +4 god-class      |
| Maggio (#63)             | 1.875       | 38.61%         | #63 - +3 god-class      |
| Maggio (#67)             | 2.699       | 47.23%         | #67 - +13.06 pp d'urto  |
| Maggio (#68)             | 2.893       | 47.54%         |                         |
| Maggio (#69)             | 3.100       | n.d.           |                         |
| Maggio (#70)             | 3.439       | 49.68%         |                         |
| Maggio (#71)             | 3.813       | 51.57%         |                         |
| Giugno (#75)             | 4.019       | 51.93%         |                         |
| Giugno (#78)             | 4.618       | 55.03%         | #78 - 10 god-class      |
| Giugno (#79)             | 4.885       | 55.85%         |                         |
| Giugno (#80)             | 5.132       | 56.73%         | #80 - 4 god + 8 moduli  |
| Giugno (#81)             | 5.232       | n.d.           | #81 - Google Drive      |
| Giugno (#82)             | 5.376       | 61.33%         | #82 - 3 god, audit      |
| Giugno (#83) - **stato attuale** | **5.510** | **65.37%** | #83 - investments+auth  |

### Coverage di file critici (prima → dopo)

| File                       | Prima         | Dopo          |
| -------------------------- | ------------- | ------------- |
| `auth-manager.js`          | **0%**        | **97.51%**    |
| `budget-manager.js`        | 0%            | **93.6%**     |
| `income-manager.js`        | 0%            | **89.57%**    |
| `deductible-manager.js`    | 0.6%          | **32%** (manager) + 3 moduli puri al 100% |
| `investments-modals.js`    | 6.89%         | **100%** (barrel) + 4 moduli puri al 91-100% |
| `documents.*` pure modules | inesistenti   | **100%**      |

### Filosofia dei test

I test sono **comportamentali**, non tautologici. Ogni nuovo modulo passa per un **red-check** (rottura intenzionale per verificare che il test cattura la regressione). Red-check confermati durante il lavoro:

`buildLiquiditySummaryHtml` (goals templates), `buildIncomeListItemHtml` con XSS escape rotto, `isInvestmentTransactionEditable`, `buildRecurrenceData` monthly-specific, `validateOpenAccountForm`, `buildRejectFlowDescriptor`, `getInterestVisuals`, heatmap source resolution, set-view boolean nel routing conti aperti, `mapRegisterValidationError`, `resolvePasswordToggle`, e altri ~30 punti critici.

---

## Era 8 - Trasparenza per l'utente

**Valore aggiunto**: rendere il progetto verificabile da chiunque lo usi.

- **README di presentazione** (sessione #32): tre README pubblici (originali oltre 290 righe tecniche ciascuno) riscritti per chi vuole capire cosa fa Budgee in 60 secondi. Struttura identica in italiano e inglese.
- **Link al repository nell'header** (giugno 2026): il titolo "Budgee by Bonn" nell'header dell'app e un link diretto al repository GitHub.

---

## Era 9 - AI, diritti sui dati e spazio per crescere (giugno → agosto 2026)

**Valore aggiunto**: l'app ha smesso di chiedere di scrivere ciò che una fotocamera sa leggere, ha dato un modo per portarsi via i propri dati o distruggerli, e ha spostato l'archiviazione da un tetto verso cui stava andando dritta.

### Scansione degli scontrini

- **Scansione AI per spese ed entrate** (giugno): fotografi uno scontrino o carichi un PDF e importo, data, descrizione e categoria arrivano nel form. La scansione gira su Google Gemini con una chiave API fornita dall'utente; se ne possono salvare più di una, e quella che raggiunge il limite passa la mano alla successiva.
- **Una guardia sull'unico errore silenzioso** (giugno): scansionare qualcosa che sembra un'entrata mentre è aperto il form delle spese ferma il flusso e chiede. Registrare un rimborso come un acquisto è l'errore che nessuno nota finché i totali non smettono di avere senso.
- **Condivisione dal sistema** (agosto): con Budgee installata, il menu di condivisione del telefono la elenca fra le destinazioni per un'immagine. Il service worker trattiene il file finché l'app non si apre, e la scansione parte con la foto già caricata.

### Il consenso, e cosa esce dall'app

- **Un consenso a parte per l'analisi AI** (agosto): uno scontrino può rivelare farmaci, visite mediche, abitudini. Mandarlo a un terzo è una decisione distinta dall'uso di Budgee, quindi viene chiesta separatamente, è versionata e si revoca dal profilo. Se l'informativa cambia nella sostanza, la vecchia risposta non copre più il nuovo trattamento e la domanda torna.
- **Informativa privacy in italiano e inglese** (agosto): una pagina propria, raggiungibile dall'app.
- **La chiave API non esce mai** (agosto): `settings/ai` è escluso alla fonte tanto dall'esportazione quanto dal backup su Drive. Una chiave finita in un backup resta leggibile per sempre.

### Portarsi via i dati, o distruggerli

- **Un registro unico di cosa compone un account** (agosto): esportazione e cancellazione percorrevano due elenchi scritti a mano che avevano divergito. La cancellazione puntava a tre subcollection dismesse e lasciava intatte le sette reali; l'esportazione ne copriva quattro su dieci. Ora leggono lo stesso registro.
- **Esportazione completa in ZIP** (agosto): un JSON completo più un CSV per sezione, letto da Firestore e non da ciò che l'app ha in memoria, che conteneva solo spese, entrate e budget.
- **Una cancellazione che sopravvive a un'interruzione** (agosto): Firestore non cancella le subcollection insieme al documento padre. Senza una visita esplicita restano orfane e invisibili, e l'account risulta cancellato mentre tutti i dati finanziari sono ancora lì. Il documento utente viene marcato prima di iniziare e cancellato per ultimo, così un'interruzione lascia una traccia che l'accesso successivo riconosce e riprende.

### Metodi di pagamento, e il contante che non si vede

- **Un elenco canonico solo** (agosto): i codici erano duplicati in sei punti e le copie avevano divergito. La select di modifica delle entrate ricorrenti ometteva `voucher`, così un'entrata ricorrente pagata con voucher perdeva il metodo al salvataggio.
- **Obbligatorio dove è una persona a compilare** (agosto), con preselezione del metodo che l'utente usa di solito in quella categoria, e solo quando il campo è ancora vuoto.
- **Copertura e completamento in blocco** (agosto): il profilo mostra quanta parte dello storico ha il campo compilato e propone di riempire i buchi per categoria, suggerendo per ognuna il metodo più usato.
- **Quota di contante** (agosto): quanta parte della spesa passa dal contante, per mese e per categoria, misurata sugli importi e non sul numero di voci. Ciò di cui non si conosce il metodo viene riportato a parte, invece di diluire la percentuale in silenzio.

### Leggere due anni insieme

- **Confronto anno su anno per categoria** (agosto): differenza e variazione percentuale, come tabella e come barre affiancate. Una categoria presente in un anno solo resta nel confronto con l'altro a zero, perché la riga che sparisce è di solito quella interessante. Una valuta per volta.

### Backup sul Drive dell'utente

- **Copia settimanale, otto conservate** (agosto): caricamento, retention e pulizia su Drive, con lo scope `drive.file`, così Budgee tocca soltanto i file che ha creato lei.
- **Detto a voce alta** (agosto): senza Cloud Function non esiste uno scheduler. Il backup parte all'apertura dell'app, e l'avviso lo dice esattamente così. "Backup settimanale" prometterebbe altrimenti qualcosa che accade da solo ogni sette giorni.

### Il pacchetto fiscale

- **Una procedura guidata per il commercialista** (agosto): sette profili, dal dipendente che presenta il 730 al socio di una società o all'impresa in contabilità ordinaria. Un filtro per capitoli riduce la ventina di sezioni per profilo alle sole schermate che riguardano chi ha davanti.
- **Nell'archivio va anche quello che Budgee sa già** (agosto): spese detraibili raggruppate per categoria, entrate, totali.
- **Quello che manca viene nominato** (agosto): una sezione vuota non distingue "non ce l'ho" da "me ne sono dimenticato". L'elenco viene generato sempre, anche quando non manca nulla, perché un archivio senza lascia il dubbio che il controllo non sia stato fatto.
- **Il contante e la detrazione del 19%** (agosto): dal 2020 la detrazione spetta di norma solo con pagamento tracciabile, salvo farmaci e strutture pubbliche. Budgee non registra chi ha erogato una prestazione, quindi segnala e non sentenzia.
- **Quello che non è un file** (agosto): IBAN, codici fiscali dei familiari a carico, dati del sostituto d'imposta. L'archivio li nomina senza contenerli: uno ZIP che viaggia per email non è il posto giusto.
- **Nomi di file innocui** (agosto): i nomi che arrivano da Drive non sono sotto il controllo di Budgee e possono contenere separatori di percorso o risalite di directory. Sanificarli è responsabilità di chi produce l'archivio.

### Spazio per crescere

L'account viveva in due array dentro un unico documento utente, che Firestore limita a 1 MiB. Quel tetto era una questione di tempo.

- **Documenti mensili** (agosto): transazioni divise per mese e ricomposte, con una regola che governa tutto: nessun record può sparire. Una data illeggibile non è un motivo per buttare via una spesa, quindi quei record finiscono in un secchio a parte, che chi migra deve guardare.
- **Una migrazione ispezionabile** (agosto): backup pre-migrazione, un piano, verifica prima e dopo la scrittura, un referto conservato su Firestore, e un motivo scritto ogni volta che non avanza.
- **Sincronizzazione in tempo reale** (agosto): due schede sullo stesso account restano allineate, con gli ascolti che si aprono solo dopo che la lettura di avvio si è chiusa, così la decisione su dove leggere non viene presa su dati che si sono mossi sotto.
- **La memoria offline dell'SDK** (agosto): l'accensione c'era già dentro `firebase-config.js`, con i due rami di errore vuoti. Una persistenza che non si era mai accesa aveva lo stesso aspetto di una accesa.
- **Caricamento pigro dei mesi** (agosto): solo i mesi che la vista corrente chiede, ricavati dal filtro di periodo invece che duplicati da esso.

### Investimenti e transazioni

- **Movimenti per investimento** (giugno): capitale versato, capitale svincolato, rendimenti incassati, tutti modificabili o eliminabili anche dopo, più i rendimenti ricorrenti per ciò che rende a scadenza fissa.
- **Modifica ed eliminazione dentro ogni modale di dettaglio** (giugno), comprese quelle aperte dalle analisi, così una voce sbagliata si corregge dove la si è notata.

### Accessibilità

- **Contrasto portato alla soglia AA** in entrambi i temi su obiettivi, ricerca, conti aperti, pulsante di rimozione e titoli delle sezioni del profilo (giugno → agosto).
- **Tastiera trattenuta dentro le modali** (agosto), così il focus non finisce dietro a una modale aperta.
- **Nomi accessibili** sui controlli che non ne avevano, come la select della valuta di un obiettivo.
- **Emoji decorative rimosse** dove il testo diceva già la stessa cosa.
- **Colori del testo semantici** che seguono il tema, invece di valori ripetuti per componente.
- **axe-core installato in locale** (agosto), così il gate di accessibilità smette di dipendere dalla rete.

### Mostrare l'app, e spiegarla

- **Pipeline demo riproducibile** (luglio): emulatori Firebase, dati finti generati, registrazione con Playwright, conversione con ffmpeg. Non viene toccato nulla di reale, e lo stesso seed produce sempre lo stesso video.
- **GIF per singola feature** (luglio) in italiano e inglese, tagliate da un unico tour continuo.
- **Screenshot delle feature recenti** (agosto), sulla stessa infrastruttura.
- **Un manuale utente** (agosto), in italiano e in inglese, che accompagna sezione per sezione.

### Togliere un backend che non c'era

Budgee gira sul piano gratuito di Firebase, dove le Cloud Functions non esistono,
eppure il repository ne conteneva ancora quattro e il client continuava a scrivere
sul database per alimentarle. Un timer aggiornava un indicatore di attività ogni
cinque minuti per una funzione che non esisteva in nessun file, e ogni richiesta
di reset archiviava l'indirizzo dell'utente in una collezione che nessuno poteva
leggere e nessuno poteva svuotare.

È stato tolto tutto (agosto). Resta il log degli errori, che si consulta dalla
console Firebase. L'app non ha più codice lato server proprio: la logica gira nel
browser e le regole del database sono l'unico confine di fiducia.

Lo stesso passaggio ha corretto un difetto che a est di Greenwich non si sarebbe
mai visto. Le date delle transazioni viaggiano come stringhe, che JavaScript legge
come mezzanotte a Greenwich: esportare un CSV dalla California trasformava una
spesa del primo giugno in una del trentuno maggio. Ora le date sono fissate alla
mezzanotte locale, e i test girano verdi a Los Angeles, a Roma e ad Auckland.

---

## Era 10 - Le regole del gioco, dette per iscritto (agosto → settembre 2026)

**Valore aggiunto**: l'app ha smesso di dare per scontate tre cose che nessuno le aveva detto. Quante lingue può parlare, a cosa acconsente chi si registra, e quando può cambiare versione sotto le mani di chi la sta usando. In coda, una funzione nuova per chi viene pagato anche in buoni pasto.

### Le lingue smettono di essere due

Ogni scelta legata alla lingua era scritta come `lang === 'it' ? x : y`, sparsa nel codice. Aggiungerne una terza avrebbe significato ritrovarle tutte.

- **Un registro unico** (agosto) dichiara quali lingue esistono e come si comportano: tag per date e numeri, nome nella lingua stessa, separatore decimale per i file importati, lingua a cui ripiegare quando una chiave manca, e se la lingua va offerta o resta nascosta finché la traduzione non è completa. Aggiungerne una è una voce in un elenco.
- **Un selettore al posto delle bandiere** (agosto): una fila di bandiere non scala, e una bandiera non è una lingua. L'inglese non è americano, l'italiano si parla anche in Svizzera, e le emoji regionali non compaiono su tutte le piattaforme. Il controllo è un menu a tendina nativo, quindi tastiera, Esc, ricerca per lettera e lettore di schermo li gestisce il browser.
- **Dizionari separati per lingua** (agosto), invece di un unico file con le coppie IT/EN affiancate.
- **Le pagine legali non ripiegano** (agosto): l'interfaccia può mostrare una stringa in un'altra lingua senza danno, un'informativa privacy no. Compaiono solo dove il testo è stato riletto da una persona.
- **Categorie confrontate in forma canonica** (agosto): la stessa categoria scritta in due lingue non è più due categorie.

### Termini d'uso, e un'accettazione che regge

L'app distribuisce una guida fiscale con calcoli che una persona potrebbe riportare in dichiarazione. Senza un documento che dica cosa quella guida è, l'app stava offrendo consulenza fiscale a sconosciuti.

- **Una pagina di termini in italiano e inglese** (agosto). Il testo definisce l'oggetto del servizio invece di escludere responsabilità: la Cassazione (ord. 20945/2026) chiede l'approvazione specifica per una clausola limitativa anche online, e il Codice del consumo (art. 33) la presume vessatoria verso i consumatori. Una clausola nulla si porta dietro la credibilità del resto del documento, quindi il testo dice cosa Budgee è e cosa non è, che le sezioni fiscali sono informative e che le stime nascono dai dati inseriti dall'utente.
- **Accettazione obbligatoria alla registrazione** (agosto), con la casella mai già spuntata e i due documenti collegati accanto al punto in cui si accetta.
- **Un'accettazione che nessuno può riscrivere** (agosto): il record si crea e si legge, non si aggiorna, e un vincolo solo fa il lavoro: `acceptedAt == request.time`. Costringe il client a usare l'orario del server, l'unico che non sceglie lui. Senza, una data del 2020 sarebbe stata accettata senza che nulla sembrasse storto. Cosa dimostra, detto senza gonfiarlo: che una registrazione autenticata ha accettato una versione precisa in un momento che non ha scelto. Non chi fosse alla tastiera.
- **L'informativa raggiungibile prima di registrarsi** (agosto): stava dentro l'app, cioè dopo. La raccolta dei dati comincia con la registrazione, e l'art. 13 GDPR la vuole disponibile in quel momento.
- **L'avviso dentro il wizard fiscale** (agosto): i termini dicono che le sezioni fiscali sono informative, ma nessuno apre i termini prima di usare un calcolatore. L'avviso resta in cima alla procedura per tutta la sua durata, non lampeggia alla prima schermata.
- **Errori ancorati al campo** (agosto) su registrazione, obiettivi, investimenti, finanziamenti, conti aperti e budget. Un solo messaggio per volta obbligava a scoprire i problemi uno alla volta, un tentativo per ciascuno.

### La PWA fa quello che dichiara

- **Il service worker non si registrava** (agosto). `main.js` aspettava l'evento `load`, che in produzione era già passato quando quel codice veniva eseguito: misurato in Chromium, evento a 147,6 ms e ascoltatore aggiunto a 167,7 ms. Nessuna registrazione, quindi nessuna cache offline e nessun aggiornamento da annunciare.
- **Icone della dimensione che dichiaravano** (agosto): `icon-512.png` era 394x340 e non quadrata, ma manifest e pagina la annunciavano 512x512. I browser prendono la dichiarazione per buona e ridisegnano, quindi il difetto si vedeva solo a app installata. Rigenerate tutte da un'unica sorgente, più il 32 e il 16 che servono alla scheda del browser: 243 KB complessivi diventano 100 KB.
- **Un avviso quando esce una versione nuova** (agosto): la versione in attesa non subentra più in silenzio mentre la pagina aperta esegue ancora il codice vecchio. Compare un avviso con "Aggiorna" e "Più tardi", l'aggiornamento parte solo se accetti, e accettare in una scheda aggiorna anche le altre.
- **Un tentativo tolto perché costava troppo** (agosto): la barra delle sezioni era stata fissata in fondo allo schermo su mobile, poi rimessa nel flusso. Nove pulsanti su due colonne misurano 226 px su uno schermo da 375: un terzo dello schermo occupato mentre si scorre una lista di transazioni, che è l'unica cosa che quello schermo deve mostrare.

### Cose piccole che si notano ogni giorno

- **L'import Excel nell'intestazione di sezione** (agosto), accanto alla fotocamera, al posto del pulsante dentro il modulo richiuso. Lo spostamento ha fatto emergere che in produzione quel pulsante scaricava il pacchetto xlsx e si fermava lì: il gestore non veniva mai costruito, e il selezionatore di file non si apriva. Regressione della migrazione a moduli ES, mai notata perché non produce errori.
- **Un caricamento fallito adesso lo dice** (agosto): i due caricatori a richiesta non avevano un ramo di errore. Senza rete il pulsante restava cliccabile e inerte, senza messaggi.
- **La valuta resta quella dell'ultima volta** (agosto), con una memoria per ogni form: le spese possono restare in zloty mentre lo stipendio continua ad arrivare in euro. I form che modificano un record già salvato sono esclusi, perché precompilarli falsificherebbe il contenuto.

### I buoni pasto (settembre 2026)

Chi li riceve ha in tasca una seconda moneta. Sono soldi veri, ma si spendono solo in certi posti, e trattarli come contante è il modo più semplice per credersi più liquidi di quanto si sia.

- **Un codice nuovo, non uno riciclato**: `voucher` esisteva già, ma è il buono regalo, e vietato sulle spese. Rietichettarlo avrebbe fatto entrare le gift card già registrate nel saldo dei buoni pasto. Il codice `meal-voucher` è l'unico ammesso sia in entrata sia in uscita, perché un buono prima si incassa e poi si spende.
- **Un saldo derivato, non memorizzato**: parte da una cifra dichiarata dall'utente e datata, e da lì segue i movimenti. Non viene mai scritto da nessuna parte, quindi non può divergere da essi.
- **La stessa aritmetica del saldo liquido**: il saldo dei buoni pasto è la funzione della liquidità applicata ai soli movimenti pagati in buoni. Una seconda funzione di calcolo sarebbe stata più corta da leggere e più lunga da mantenere, perché due funzioni che devono restare d'accordo per sempre finiscono per non esserlo, e la divergenza si vede come due saldi che non tornano mentre nessun test fallisce.
- **Fuori dalla liquidità, dentro tutto il resto**: nella card del saldo attuale stanno su una riga a parte e non entrano nel totale né nella proiezione del mese. Nei totali di periodo, nei budget e nei risparmi contano come ogni altro movimento.
- **Nessuno stato mostra un numero falso**: senza saldo di partenza il pannello invita a impostarlo invece di mostrare uno zero, che direbbe "non ne hai" a chi ne ha in tasca; un saldo negativo si vede con il segno, perché è l'unico segnale che l'apertura è sbagliata o che manca un accredito.
- **Spegnere nasconde, non cancella**: i movimenti storici restano in lista con la loro etichetta e il saldo di partenza resta scritto.
- **Un bug preesistente trovato per strada**: il calcolo della liquidità controllava ogni entrata e ogni spesa, ma non il saldo di partenza. Un importo corrotto trasformava l'intero saldo in `NaN`, e l'interfaccia lo stampava come se fosse una cifra.
- **Due buchi trovati dalla verifica, non dal codice**: i form di inserimento hanno le opzioni scritte nel markup, che nessuno ricostruisce all'apertura, quindi chi non aveva mai acceso la funzione si vedeva comunque offrire "Buono Pasto" a ogni spesa; e il completamento assistito dei metodi elencava tutti i codici, quindi avrebbe attribuito spese passate a un canale mai posseduto.
- **Un difetto che solo l'esecuzione ha trovato**: registrando una spesa in buoni pasto, il saldo restava sulla cifra precedente fino al ricaricamento della pagina. È derivato, quindi cambia solo quando qualcosa lo ridisegna, e non lo faceva nessuno.

---

## Metriche aggregate sull'intera storia del progetto

| Metrica                                          | Valore                                          |
| ------------------------------------------------ | ----------------------------------------------- |
| Sessioni di lavoro                               | 128 (gen 2026 → set 2026)                       |
| Funzionalità user-facing introdotte              | 30+                                             |
| Vulnerabilità di sicurezza chiuse                | XSS multipli + 3 CSV injection + 5 HIGH recenti |
| Silent failures resi visibili                    | 15+                                             |
| Test totali al 1 settembre 2026                  | **10.282** su 479 suite, tutti verdi            |
| Coverage globale Lines                           | 0% → **91.99%** (misurata il 1 settembre 2026)  |
| Bundle iniziale                                  | 1.73 MB → 900 KB (-48%)                         |
| Asset immagini                                   | 6.2 MB → 340 KB (-95%)                          |
| Riduzione `app.js`                               | 13.150 → 1.734 righe                            |
| Riduzione `investments-modals.js`                | 1.439 → 41 righe (barrel)                       |
| Inline styles eliminati                          | ~540 → ~85 (-84%)                               |
| `rgba()` hardcodati                              | 388 → 12 (-97%)                                 |
| Nuovi moduli puri estratti                       | 75+                                             |

---

## Cosa significa per chi usa Budgee oggi

In nove mesi Budgee è passata da un'app funzionante ma fragile a un prodotto:

1. **Veloce**: bundle iniziale dimezzato, immagini ridotte del 95%, build minificato, offline completo.
2. **Sicuro**: tutti i vettori XSS noti chiusi, CSV injection bloccata, password con regole di complessità, UUID per gli ID, regole Firestore restrittive, auto-logout per inattività.
3. **Affidabile**: gli errori che prima sparivano silenziosamente ora sono visibili. Un pulsante che non funziona spiega il perché.
4. **Verificabile**: 10.282 test su 479 suite. Le funzioni critiche (login, budget, investimenti, esportazioni) sono coperte oltre il 90%. Una modifica futura che rompe qualcosa viene intercettata prima del deploy.
5. **Trasparente**: il codice è linkato dall'header dell'app. Chiunque può verificare cosa fa Budgee con i propri dati.
6. **Tuo**: l'intero account si scarica in un unico archivio, la cancellazione non lascia residui, e l'unica funzione che manda dati a un terzo lo chiede prima e si può spegnere.
7. **Senza un muro davanti**: le transazioni vivono in documenti mensili, quindi lo storico può continuare a crescere oltre il limite di dimensione di un singolo record.
8. **Con le regole scritte**: termini d'uso e informativa privacy si leggono prima di registrarsi, l'accettazione resta agganciata alla versione del testo, e una versione nuova dell'app non subentra finché non sei tu a dire di sì.

Il prossimo ciclo di sviluppo parte da una base molto più protetta da test e da audit di sicurezza rispetto a nove mesi fa, non ha più un tetto di archiviazione all'orizzonte, e può aggiungere una lingua senza rimettere mano al codice.
