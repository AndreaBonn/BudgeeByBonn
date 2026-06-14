# Changelog Budgee

Questo documento riassume gli avanzamenti significativi del progetto dall'inizio (gennaio 2026) a oggi, raggruppati per **ere di valore** invece che per singola sessione. Sono stati esclusi i commit puramente cosmetici (formattazione, rename di variabili, allineamenti UI senza cambio di comportamento), i commit di pura pulizia interna senza impatto (rimozione `console.log`, fix di selettori CSS inutilizzati) e le sessioni vuote.

Arco temporale coperto: **gennaio 2026 → giugno 2026**, 83 sessioni di lavoro.

---

## Era 1 - Fondamenta funzionali (gennaio - febbraio 2026)

**Valore aggiunto**: le funzionalita che hanno definito cosa Budgee fa per l'utente. In questa era nascono le feature distintive dell'app, quelle che differenziano Budgee da un semplice tracker spese.

### Sistema "Liquidita disponibile" (sessioni #4 → #11)

L'app non si limita a sommare entrate e sottrarre uscite. Tiene conto separato di tre dimensioni:

- **Obiettivi di risparmio**: ogni obiettivo ha nome, importo target, deadline (o "obiettivo continuo"), priorita e colore. Calcolo automatico del risparmio mensile necessario, barre di progresso colorate, badge con il numero di obiettivi attivi.
- **Accantonamenti**: bottone "Aggiungi Risparmio" su ogni obiettivo, con shortcut rapidi (+50, +100, +200, Completa). La card liquidita mostra `1.000 € (+500 € accantonati)`, dove i 1.000 sono spendibili liberamente.
- **Investimenti dalla liquidita**: ogni investimento dichiara l'origine fondi (esterni, totalmente da liquidita, parzialmente da liquidita). Possibili nuovi versamenti (bottone "Investi") e prelievi (bottone "Svincola") con tracciamento del flusso.

Bug critico risolto (sessione #11): il calcolo della liquidita disponibile sottraeva due volte gli investimenti effettuati prima del set manuale del saldo. Ora sono considerati solo gli investimenti successivi a quella data.

### Tab "Conti Aperti" (sessione #8)

Gestione di debiti e crediti verso persone o fornitori (es. "Dentista Dr. Rossi"). Ogni conto ha tipo (debito/credito), categoria, barra di progresso. Bottoni: Paga/Incassa (crea automaticamente una spesa o entrata collegata), + Importo (nuova fattura sullo stesso conto), Modifica, Chiudi.

### Spese deducibili interattive (sessione #1)

Cliccando "Totale Spese" o "Totale Entrate" si apre il riepilogo per mese. Cliccando su un mese si vedono tutte le transazioni di quel mese (data, descrizione, categoria, importo), con icona distintiva per spese deducibili e ricorrenti.

### Analisi e confronti

- **Ripartizione spese per valuta** (sessione #14): tab Spese mostra quanto e stato speso in EUR, PLN, USD, GBP, con valore originale + conversione. Barre percentuali sul totale.
- **Confronto Entrate vs Uscite** (sessione #15): tab Categorie con selezione libera di macro-categorie (entrate vs uscite) tramite "pillole" cliccabili. Secondo livello sotto-categorie per analisi piu fine. Click su categoria apre popup con tutte le transazioni.
- **Riepilogo periodo** (sessioni #24-#25): card riepilogativa su Spese/Entrate/Risparmi con saldo (verde positivo / rosso negativo), totale entrate, totale spese, previsione ricorrenze. Card cliccabili che aprono il dettaglio transazioni.

### Internazionalizzazione e categorie

- **i18n popup ricorrenti** (sessione #13): traduzione completa IT/EN di titoli, etichette, dropdown (frequenze, giorni settimana, metodi pagamento, tipi ricorrenza), messaggi.
- **Categorie crypto** (sessione #12): "Criptovalute" sotto "Finanza & Risparmi", allineata fra spese ed entrate. Suggerimento automatico su descrizioni crypto.
- **Etichette descrittive** (sessione #12b): tutte le voci generiche "Altro" sostituite con nomi specifici ("Altre Spese di Trasporto", "Altre Spese Sanitarie") in italiano e inglese.

---

## Era 2 - Sicurezza e protezione dei dati (febbraio → giugno 2026)

**Valore aggiunto**: ogni step di crescita dell'app e stato accompagnato da un audit di sicurezza. Le sessioni di security non hanno aggiunto funzionalita visibili ma hanno chiuso vettori di attacco specifici prima che potessero essere sfruttati.

### Protezione XSS

- **#18, #32**: protezione XSS su tutti i campi input utente (descrizioni, categorie). Caratteri `&`, `<`, `>` vengono mostrati come testo, non interpretati come HTML. La funzione di ricerca continua a evidenziare correttamente i risultati.
- **#29, #33**: sanitizzazione di 126 punti `innerHTML`, 16 file toccati dalla XSS prevention.
- **#52, #75**: audit approfondito, eliminazione di tutti i vettori XSS residui, vulnerabilita corretta sulla ricerca con caratteri speciali nei metodi di pagamento.
- **#80**: XSS in goals modal (targetAmount/savedAmount/deadline non escaped). Fix con `escapeHtml(String(...))`.
- **#83**: `escapeAttr` non importato in `investments-modals-popups.js`, mascherato dal mock globale nei test.

### Protezione CSV injection

- **#63**: vulnerabilita scoperta sull'export finanziamenti — nomi con `=HYPERLINK(...)` interpretati da Excel come formule. Fix con prefisso apostrofo sui caratteri pericolosi.
- **#65**: stessa protezione estesa agli investimenti.
- **#80**: helper `escapeCsvCell` con quote-doubling RFC 4180 + prefisso `'` su `=`, `+`, `-`, `@`, `\t`, `\r` per l'export conti aperti.

### Hardening database e auth

- **#33**: validazione dati nel database cloud (Firestore rules piu restrittive), headers HTTP di protezione, gestione robusta sync offline.
- **#36**: regole Firestore rafforzate per accettare solo campi previsti, errori non espongono piu informazioni tecniche sensibili, notifiche Telegram senza stack trace.
- **#38**: migrazione a UUID (`crypto.randomUUID()`) per tutti gli identificativi (spese, entrate, investimenti, ...). Elimina il rischio che doppi click in rapida successione sovrascrivano silenziosamente record diversi.
- **#57**: password con regole di complessita (8+ caratteri, maiuscole + minuscole + numeri), auto-recovery delle operazioni Firestore in caso di errore rete, messaggi Telegram protetti contro injection, rules cloud piu restrittive con limiti sulle dimensioni dati.
- **#34**: sistema di backup manuale del database (`scripts/firestore-backup.sh`), tracking centralizzato degli errori, timeout di auto-logout dopo inattivita.
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

**Valore aggiunto**: i tempi di caricamento sono passati da inaccettabili a competitivi. Ogni intervento e stato misurato.

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
- **#30**: `app.js` ridotto del 23.7% (da 13.150 a 10.030 righe). Il sistema di personalizzazione sezioni (visibilita, ordine, drag-drop) era duplicato 8 volte → ora `SectionsManager` unico riutilizzabile.
- **#54**: file investimenti diviso in 4 file piu piccoli (era 2.829 righe).
- **#44**: CSS principale da 7.982 righe → 16 file modulari (max 1.492 righe ciascuno), organizzati per area funzionale.

### Nuovi sistemi interni

- **#34**: 5 nuovi servizi dedicati (backup, tracking errori, gestione storage, ...). Auto-logout dopo inattivita.
- **#35**: `localStorage` centralizzato in StorageService (87% migrati), navigazione sezioni con sistema event-driven.
- **#36**: 61/63 `onclick` migrati a event delegation (97%).
- **#40**: `event-delegate.js` e `lifecycle-mixin.js` per prevenire memory leak.
- **#42**: 67 file JS migrati a ES modules, 120+ check `typeof` rimossi, 55+ assegnazioni `window.X` eliminate.
- **#46**: ~540 inline styles → ~85 residui (riduzione 84%); colori e dimensioni in CSS classes per supportare tema scuro.
- **#47**: ESLint flat config + correzione di 131 problemi pre-esistenti nascosti dalla vecchia configurazione (confronti imprecisi, traduzioni duplicate, ...).
- **#50**: lista parole chiave per suggerimento automatico categoria estratta in JSON dedicato.
- **#55-#56**: rimozione di tutti gli handler `onclick` inline per conformita CSP.

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
| **Popup totali / Edit popup / Filtri periodo / Report / Export** | piu file                  | routing + form-state + aggregation, totale ~30 helper estratti                                                                    |

Riferimento: vedi anche il bilancio coverage nell'Era 7.

---

## Era 5 - Bug fix critici

**Valore aggiunto**: bug che, una volta scoperti, non sarebbero mai dovuti rimanere in produzione. Risolti in priorita.

- **#49 Fuso orario italiano**: tra mezzanotte e le 2 di notte, l'app mostrava le transazioni del mese precedente invece di quello corrente (usava UTC invece di Europe/Rome). Risolto su tutte le date dell'app.
- **#45 Console errors**: sistema cache offline su immagini mancanti, init spese ricorrenti, scrollbar Firefox.
- **#16b Permessi obiettivi**: regola Firestore mancante per obiettivi di risparmio; report button rimosso dove non piu presente.
- **#11 Doppio conteggio liquidita**: investimenti pre-saldo sottratti due volte.
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
- "Investi dalla liquidita" senza saldo impostato mostrava liquidita fittizia da zero.
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

- **#27**: aspetto rinnovato — colori titoli puliti senza effetti sfumati, card piu raffinate, animazioni ridotte a quelle funzionali, font Figtree per lettura piacevole. Accessibilita tastiera con indicatori focus ripristinati. Sistema notifiche con elemento `<dialog>` nativo del browser.
- **#28**: colori di sfondo piu puliti e piatti (stile Apple/Linear), ombre neutre e sottili, feedback touch immediato. Supporto notch (iPhone X+, Pixel) via safe-area inset.
- **#29**: cambi sezione animati, errori form vicino al campo, navigazione col tasto "indietro" del browser, swipe gesture tra sezioni su mobile, bottone "Salta" per il tutorial. `rgba()` hardcodati 388 → 12 (-97%).
- **#26**: animazioni piu fluide su smartphone, layout robusto su schermi piccoli, navigazione tastiera/screen reader migliorata, sezioni vuote con suggerimento azione (era "punteggio UI stimato 5.5/10 → 8/10").
- **#31**: animazioni standardizzate, sistema piu sicuro per click sui pulsanti delle liste, loading state durante download dati.
- **#46**: tema chiaro/scuro funziona meglio (colori non piu hardcoded), tema unico da cambiare in un punto.

### Bottoni stato vuoto

- **#27**: ogni sezione senza dati mostra un messaggio chiaro con bottone verde "Aggiungi" che apre direttamente il form.

### A11y dark mode

- **#35**: 6/6 WCAG AA failures corretti (contrasto colori in modalita scura).
- **#20**: 3 file CSS "rattoppo" eliminati, problemi di colore testo (scuro su sfondo verde) risolti alla radice.

---

## Era 7 - Test coverage massiccio (marzo → giugno 2026)

**Valore aggiunto**: Budgee parte praticamente senza test. In sei mesi diventa un progetto con copertura del 65% globale e oltre 5.500 test comportamentali. Ogni regressione futura sara intercettata prima del deploy.

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

## Metriche aggregate sull'intera storia del progetto

| Metrica                                          | Valore                                          |
| ------------------------------------------------ | ----------------------------------------------- |
| Sessioni di lavoro                               | 83 (gen 2026 → giu 2026)                        |
| Funzionalita user-facing introdotte              | 15+                                             |
| Vulnerabilita di sicurezza chiuse                | XSS multipli + 3 CSV injection + 5 HIGH recenti |
| Silent failures resi visibili                    | 15+                                             |
| Test totali al 9 giugno 2026                     | **5.510**                                       |
| Coverage globale Lines                           | 0% → **65.37%**                                 |
| Bundle iniziale                                  | 1.73 MB → 900 KB (-48%)                         |
| Asset immagini                                   | 6.2 MB → 340 KB (-95%)                          |
| Riduzione `app.js`                               | 13.150 → 1.734 righe                            |
| Riduzione `investments-modals.js`                | 1.439 → 41 righe (barrel)                       |
| Inline styles eliminati                          | ~540 → ~85 (-84%)                               |
| `rgba()` hardcodati                              | 388 → 12 (-97%)                                 |
| Nuovi moduli puri estratti                       | 75+                                             |

---

## Cosa significa per chi usa Budgee oggi

In sei mesi Budgee e passato da un'app funzionante ma fragile a un prodotto:

1. **Veloce**: bundle iniziale dimezzato, immagini ridotte del 95%, build minificato, offline completo.
2. **Sicuro**: tutti i vettori XSS noti chiusi, CSV injection bloccata, password con regole di complessita, UUID per gli ID, regole Firestore restrittive, auto-logout per inattivita.
3. **Affidabile**: gli errori che prima sparivano silenziosamente sono ora visibili. Un pulsante che non funziona spiega il perche.
4. **Verificabile**: 5.510 test coprono il 65% del codice. Le funzioni critiche (login, budget, investimenti, esportazioni) sono coperte oltre il 90%. Una modifica futura che rompe qualcosa viene intercettata prima del deploy.
5. **Trasparente**: il codice e linkato dall'header dell'app. Chiunque puo verificare cosa fa Budgee con i propri dati.

Il prossimo ciclo di sviluppo introduce nuove funzionalita su una base che e drasticamente piu protetta da test e da audit di sicurezza rispetto a sei mesi fa.
