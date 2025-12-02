# 💰 Budgee (PWA)

<div align="center">

**Progressive Web App completa per la gestione delle finanze personali**

🌐 [**Prova l'App**](https://financial-management-by-bonn.web.app) • 📱 Installabile • ☁️ Sincronizzazione Cloud

</div>

---

## ℹ️ Descrizione

**Budgee** è un'applicazione web progressiva completa per la gestione delle finanze personali. Sviluppata con **HTML5, CSS3 e JavaScript vanilla**, utilizza **Firebase** per il backend e **Chart.js** per le visualizzazioni interattive.

L'app offre un'esperienza utente moderna e intuitiva, con focus su **performance**, **design responsivo** e **funzionalità offline-first**.

---

## ✨ Caratteristiche Principali

### 💸 Gestione Completa delle Finanze
- **Tracciamento Spese ed Entrate** - Registra tutte le tue transazioni con categorie personalizzabili
- **Multi-Valuta** - Supporto per EUR, PLN con conversione automatica
- **Importazione Excel** - Carica massivamente i tuoi dati da file .xlsx/.xls
- **Esportazione CSV** - Backup completo dei tuoi dati

### 📊 Visualizzazioni Interattive

#### Heatmap in formato calendario
Analizza la distribuzione delle tue spese ed entrate per giorno. Clicca su ogni sezione per vedere i dettagli delle transazioni.

#### Grafici Trend Cumulativi
Monitora l'andamento mensile e annuale delle tue finanze con grafici a linee che mostrano la crescita cumulativa.

#### Grafico Combinato
Confronta entrate vs spese nel tempo. Clicca su un punto per vedere tutte le transazioni di quel giorno con:
- 💰 Totale entrate
- 💸 Totale spese
- 📊 Bilancio giornaliero
- 📝 Lista dettagliata delle transazioni

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
- **Gestione Completa** - Modifica importo, descrizione e metodo di pagamento delle ricorrenze future
- **Eliminazione Intelligente** - Salta singoli pagamenti o elimina tutte le occorrenze future mantenendo lo storico
- **Modal Eleganti** - Interfaccia premium senza alert nativi, con animazioni fluide e design moderno

### 🤖 Insights Intelligenti

Il motore di analisi automatica ti aiuta a:
- 📈 Identificare pattern nelle tue abitudini di spesa
- 🔍 Scoprire le categorie più utilizzate
- 📅 Confrontare periodi diversi (mese corrente vs precedente)
- 📊 **Report Periodi Personalizzati** - Genera report per range di date specifici (es: 20 giorni di ottobre + 10 di novembre)
- 💸 **Flusso di Denaro (Sankey Diagram)** - Visualizza come si muovono i tuoi soldi tra entrate e spese
- 💡 Ricevere suggerimenti personalizzati per ottimizzare le finanze

### 🌍 Multilingua

Interfaccia completamente tradotta in:
- 🇮🇹 **Italiano**
- 🇬🇧 **English**

Switch istantaneo tra le lingue con traduzione automatica di tutte le categorie e messaggi.

### 📱 Progressive Web App

- **Installabile** - Aggiungi alla home screen come app nativa
- **Responsive Design** - Ottimizzata per mobile, tablet e desktop
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
- **Responsive Design** - Mobile-first approach

---

## 🚀 Come Funziona

### 1. Registrazione e Login
Crea un account con email e password. Verifica la tua email per accedere.

### 2. Crea il Tuo Profilo
Imposta nome e valuta predefinita.

### 3. Registra le Transazioni
Aggiungi spese ed entrate con pochi click. Ogni transazione include:
- Importo
- Categoria
- Descrizione (opzionale)
- Data
- Valuta

### 4. Visualizza i Grafici
I grafici si aggiornano automaticamente ad ogni nuova transazione. Clicca su qualsiasi elemento per vedere i dettagli.

### 5. Imposta i Budget
Definisci limiti mensili per ogni categoria e monitora l'utilizzo in tempo reale. Usa la funzione "Riprendi Budget Mese Precedente" per copiare rapidamente i budget dal mese scorso.

### 6. Genera Report Personalizzati
Crea report per periodi specifici (es: dal 15 ottobre al 20 novembre). Il sistema calcola automaticamente i budget proporzionali in base ai giorni effettivi di ogni mese inclusi nel periodo.

### 7. Configura Transazioni Ricorrenti
Programma spese ed entrate che si ripetono:
- **Crea una ricorrenza**: Spunta "🔄 Spesa/Entrata Ricorrente" e scegli la frequenza
- **Conferma pagamenti**: Ricevi notifiche quando è il momento di confermare una transazione ricorrente
- **Gestisci ricorrenze**: Clicca su "📋 Gestisci Ricorrenti" per modificare o eliminare le programmazioni future

**Esempi pratici:**
- 💰 Stipendio mensile (ultimo giorno del mese)
- 🏠 Affitto (primo giorno del mese)
- 📺 Netflix (giorno 15 di ogni mese)
- 🏋️ Palestra (ogni lunedì)
- 💼 Cliente freelance fisso (primo del mese)

### 8. Ricevi Insights
Il sistema analizza automaticamente le tue abitudini e ti fornisce suggerimenti personalizzati. Visualizza il flusso di denaro con il diagramma Sankey per capire dove vanno i tuoi soldi.


---

## 🎯 Prova Subito l'App!

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
