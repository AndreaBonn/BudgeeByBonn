# 💰 Budgee (PWA)

<div align="center">

**App Web per la Gestione delle Finanze Personali**

🌐 [**Prova Live**](https://financial-management-by-bonn.web.app) • 📱 Installabile • ☁️ Sincronizzazione Cloud

</div>

---

## ℹ️ Descrizione

**Budgee** è un'applicazione web progressiva completa per la gestione delle finanze personali. Costruita con **HTML5, CSS3 e JavaScript vanilla**, utilizza **Firebase** per il backend e **Chart.js** per visualizzazioni interattive.

L'app offre un'esperienza utente moderna e intuitiva, con focus su **performance**, **design responsive** e **funzionalità offline-first**.

---

## ✨ Funzionalità Principali

### 💸 Gestione Completa delle Finanze
- **Tracciamento Spese ed Entrate** - Registra tutte le tue transazioni con categorie personalizzabili
- **Analisi Risparmi Dedicata** - Tab completo per monitorare i tuoi risparmi nel tempo
- **Multi-Valuta** - Supporto per EUR, PLN con conversione automatica
- **Importazione Excel** - Carica massivamente i tuoi dati da file .xlsx/.xls
- **Esportazione CSV** - Backup completo dei tuoi dati

### 📊 Visualizzazioni Interattive

#### Calendario Heatmap
Analizza la distribuzione delle tue spese ed entrate per giorno. Clicca su ogni sezione per vedere i dettagli delle transazioni.

#### Grafici Trend Cumulativi
Monitora l'andamento mensile e annuale delle tue finanze con grafici a linea che mostrano la crescita cumulativa.

#### Grafico Combinato
Confronta entrate vs spese nel tempo. Clicca su un punto per vedere tutte le transazioni di quel giorno con:
- 💰 Totale entrate
- 💸 Totale spese
- 📊 Bilancio giornaliero
- 📝 Lista dettagliata transazioni

#### Tab Risparmi Completo
Un'intera sezione dedicata all'analisi dei tuoi risparmi:

**Grafici Avanzati:**
- **Trend Mensile Risparmi** - Visualizza entrate, spese e risparmi per ogni mese con grafico combinato (linee + barre)
- **Risparmi Cumulati** - Monitora la crescita progressiva del tuo patrimonio con area riempita dinamica (verde/rosso)
- **Trend Saving Rate** - Analizza la percentuale di risparmio mensile con colori dinamici basati sulla performance

**Statistiche Interattive:**
- 💰 **Totale Risparmiato** (cliccabile) - Mostra il breakdown completo di entrate totali vs spese totali
- 📊 **Tasso Medio di Risparmio** - Percentuale media di risparmio rispetto alle entrate del periodo
- 🏆 **Miglior Mese** (cliccabile) - Identifica il mese con maggior risparmio e visualizza dettagli completi
- ⚠️ **Peggior Mese** (cliccabile) - Identifica il mese con minor risparmio per capire dove migliorare

**Insights Intelligenti:**
- Analisi automatica dei pattern di risparmio
- Valutazione del tasso di risparmio (Eccellente ≥20%, Buono ≥10%, Basso ≥0%)
- Confronto trend tra mesi consecutivi
- Suggerimenti personalizzati per ottimizzare i risparmi

**Filtri Temporali Flessibili:**
- Mese corrente
- Ultimi 3, 6 o 12 mesi
- Dall'inizio dell'anno (default)
- Tutto il periodo disponibile
- Range personalizzato con date specifiche

### 🎯 Budget e Monitoraggio

- **Budget Mensili** - Imposta limiti di spesa per ogni categoria
- **Copia Budget Mese Precedente** - Riprendi rapidamente i budget dal mese precedente con selezione selettiva e gestione conflitti
- **Monitoraggio Real-time** - Visualizza percentuali di utilizzo con barre colorate
- **Avvisi Automatici** - Notifiche quando superi il budget
- **Previsioni Intelligenti** - Stima la spesa a fine mese basata sui trend attuali
- **Budget Proporzionali** - Calcolo automatico per periodi personalizzati basato sui giorni effettivi

### 🔄 Transazioni Ricorrenti

- **Spese Ricorrenti** - Programma spese che si ripetono (abbonamenti Netflix, affitto, bollette, palestra)
- **Entrate Ricorrenti** - Programma entrate che si ripetono (stipendio, freelance, affitti ricevuti)
- **Frequenze Flessibili** - Giornaliera, settimanale (scegli il giorno), mensile (primo/ultimo/giorno specifico), annuale
- **Conferma Manuale** - Ogni transazione ricorrente richiede la tua conferma prima di essere registrata
- **Gestione Completa** - Modifica importo, descrizione e metodo di pagamento per le occorrenze future
- **Eliminazione Intelligente** - Salta singoli pagamenti o elimina tutte le occorrenze future mantenendo lo storico
- **Modal Eleganti** - Interfaccia premium senza alert nativi, con animazioni fluide e design moderno

### 🤖 Insights Intelligenti

Il motore di analisi automatica ti aiuta a:
- 📈 Identificare pattern nelle tue abitudini di spesa e risparmio
- 🔍 Scoprire le categorie più utilizzate
- 📅 Confrontare periodi diversi (mese corrente vs precedente)
- 📊 **Report Periodi Personalizzati** - Genera report per range di date specifici (es: 20 giorni di ottobre + 10 giorni di novembre)
- 💸 **Flusso di Denaro (Diagramma Sankey)** - Visualizza come si muovono i tuoi soldi tra entrate e spese
- 💡 Ricevere suggerimenti personalizzati per ottimizzare le finanze

### 🌍 Multilingua

Interfaccia completamente tradotta in:
- 🇮🇹 **Italiano**
- 🇬🇧 **English**

Cambio istantaneo della lingua con traduzione automatica di tutte le categorie, grafici e messaggi.

### 📱 Progressive Web App

- **Installabile** - Aggiungi alla home screen come app nativa
- **Design Responsive** - Ottimizzato per mobile, tablet e desktop
- **Performance** - Caricamento veloce e animazioni fluide

---

## 🛠️ Tecnologie Utilizzate

### Frontend
- **HTML5, CSS3, JavaScript** - Vanilla JS per massime performance
- **Chart.js** - Libreria per grafici interattivi e responsive
- **PWA APIs** - Service Worker, Web App Manifest, Cache API

### Backend & Storage
- **Firebase Firestore** - Database NoSQL real-time
- **Firebase Authentication** - Sistema di autenticazione sicuro
- **Firebase Hosting** - Hosting veloce e affidabile con HTTPS

### Integrazioni
- **SheetJS (xlsx)** - Importazione file Excel
- **CSV Export** - Esportazione dati

### Architettura
- **Single Page Application** - Navigazione fluida senza reload
- **Offline First** - Persistenza locale con sincronizzazione cloud
- **Responsive Design** - Approccio mobile-first

---

## 🚀 Come Funziona

### 1. Registrazione e Login
Crea un account con email e password. Verifica la tua email per accedere.

### 2. Crea il Tuo Profilo
Imposta il tuo nome e la valuta predefinita.

### 3. Registra Transazioni
Aggiungi spese ed entrate con pochi click. Ogni transazione include:
- Importo
- Categoria
- Descrizione (opzionale)
- Data
- Valuta

### 4. Visualizza i Grafici
I grafici si aggiornano automaticamente ad ogni nuova transazione. Clicca su qualsiasi elemento per vedere i dettagli.

### 5. Analizza i Risparmi
Vai al tab "Risparmi" per:
- Visualizzare il trend mensile di entrate, spese e risparmi
- Monitorare la crescita cumulativa del patrimonio
- Analizzare la percentuale di risparmio mensile
- Cliccare sulle statistiche per dettagli approfonditi
- Ricevere insights automatici sui tuoi pattern di risparmio

### 6. Imposta Budget
Definisci limiti mensili per ogni categoria e monitora l'utilizzo in tempo reale. Usa la funzione "Copia Budget Mese Precedente" per copiare rapidamente i budget dal mese scorso.

### 7. Genera Report Personalizzati
Crea report per periodi specifici (es: dal 15 ottobre al 20 novembre). Il sistema calcola automaticamente i budget proporzionali in base ai giorni effettivi di ogni mese incluso nel periodo.

### 8. Configura Transazioni Ricorrenti
Programma spese ed entrate che si ripetono:
- **Crea una ricorrenza**: Spunta "🔄 Spesa/Entrata Ricorrente" e scegli la frequenza
- **Conferma pagamenti**: Ricevi notifiche quando è il momento di confermare una transazione ricorrente
- **Gestisci ricorrenze**: Clicca "📋 Gestisci Ricorrenti" per modificare o eliminare programmazioni future

**Esempi pratici:**
- 💰 Stipendio mensile (ultimo giorno del mese)
- 🏠 Affitto (primo giorno del mese)
- 📺 Netflix (15 di ogni mese)
- 🏋️ Palestra (ogni lunedì)
- 💼 Cliente fisso freelance (primo del mese)

### 9. Ricevi Insights
Il sistema analizza automaticamente le tue abitudini e fornisce suggerimenti personalizzati. Visualizza il flusso di denaro con il diagramma Sankey per capire dove vanno i tuoi soldi.

---

## 🎯 Prova l'App Ora!

<div align="center">

### 👉 [**APRI BUDGEE**](https://financial-management-by-bonn.web.app) 👈

**Nessuna installazione richiesta • Funziona su tutti i dispositivi • Dati sincronizzati nel cloud**

[![Prova Ora](https://img.shields.io/badge/🚀_INIZIA_GRATIS-4285F4?style=for-the-badge&logo=google-chrome&logoColor=white&labelColor=1a73e8)](https://financial-management-by-bonn.web.app)

</div>

---

## 📬 Contatti

Per maggiori informazioni, collaborazioni o feedback:

📧 **Email:** [andreabonacci95@protonmail.com](mailto:andreabonacci95@protonmail.com)

---

<div align="center">

**Il codice sorgente è privato, ma puoi testare l'applicazione tramite il link sopra.**

**© 2025 Andrea Bonacci - Tutti i diritti riservati**

</div>
