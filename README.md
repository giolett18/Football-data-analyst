# Building a Decision-Based Digital Twin of Rodri

Questo progetto costruisce un **digital twin decisionale** semplice e interpretabile di Rodri usando StatsBomb Open Data.

L'obiettivo non è creare un modello predittivo black-box, ma capire e simulare come Rodri prende decisioni di passaggio in base al contesto di gioco.

Il digital twin si basa su quattro segnali principali:

- zona di campo
- pressione
- direzione del passaggio
- progressione del passaggio

## Panoramica del progetto

Rodri è uno dei centrocampisti più influenti del calcio moderno grazie alla sua capacità di controllare il possesso, far progredire il gioco e prendere decisioni di alta qualità anche sotto pressione.

Questo notebook analizza i suoi dati evento su 24 match selezionati e costruisce un modello probabilistico in grado di simulare comportamenti di passaggio plausibili in contesti diversi.

Il progetto risponde a domande come:

- Rodri passa più spesso in avanti nelle zone basse del campo?
- Come cambia la sua progressione in base alla zona?
- La pressione modifica il suo processo decisionale?
- Possiamo simulare una decisione di passaggio simile a Rodri dato un contesto di zona e pressione?

## Dataset

Il progetto utilizza **StatsBomb Open Data** in formato JSON.

Vengono caricati solo 24 match selezionati in cui è presente Rodri, invece di caricare l'intero dataset. Questo rende il notebook più leggero, veloce e meno esposto a problemi di memoria.

Principali feature evento utilizzate:

- player
- event type
- location
- pass end location
- under pressure
- match id

## Metodologia

Il workflow è strutturato così:

1. Caricamento dei file evento StatsBomb selezionati
2. Estrazione di nomi giocatore e tipo evento dai campi JSON annidati
3. Filtro degli eventi di Rodri
4. Estrazione delle coordinate iniziali e finali dei passaggi
5. Assegnazione di ogni passaggio a una zona di campo
6. Classificazione della direzione del passaggio
7. Calcolo della progressione
8. Analisi del comportamento sotto pressione
9. Costruzione di un modello decisionale probabilistico
10. Simulazione di decisioni di passaggio simili a Rodri

## Zone di campo

StatsBomb utilizza un sistema di coordinate 120x80.

I passaggi vengono assegnati a tre zone in base alla coordinata x di partenza:

- Zona difensiva: x < 40
- Zona centrale: 40 <= x < 80
- Zona offensiva: x >= 80

## Direzione del passaggio

La direzione del passaggio viene classificata usando la progressione sulla coordinata x:

- Forward: end_x - start_x > 5
- Backward: end_x - start_x < -5
- Lateral: tra -5 e +5

La soglia di 5 metri evita di interpretare piccoli spostamenti come passaggi realmente progressivi o regressivi.

## Risultati principali

Sul campione di 24 match selezionati:

- Eventi di Rodri: 5,245
- Passaggi validi analizzati: 1,695
- Progressione media dei passaggi: +3.65 metri

Distribuzione della direzione dei passaggi:

- Forward: 43.7%
- Lateral: 34.9%
- Backward: 21.4%

Quota di passaggi forward per zona:

- Zona difensiva: 53.1%
- Zona centrale: 42.1%
- Zona offensiva: 37.4%

Progressione media per zona:

- Zona difensiva: +6.18 metri
- Zona centrale: +3.33 metri
- Zona offensiva: +1.22 metri

Progressione in zona difensiva sotto pressione:

- Senza pressione: +5.88 metri
- Sotto pressione: +7.83 metri

## Interpretazione

I risultati suggeriscono che il comportamento di passaggio di Rodri dipende dal contesto.

Rodri fa progredire maggiormente il pallone dalle zone più basse, dove ha più campo da attaccare davanti a sé. Nelle zone offensive, la progressione media diminuisce naturalmente perché resta meno spazio da guadagnare.

Il risultato sulla pressione in zona difensiva è particolarmente interessante: Rodri guadagna più metri in media quando passa sotto pressione nella propria zona difensiva. Questo non significa necessariamente che la pressione renda il passaggio più semplice. Una lettura più realistica è che le situazioni di pressione spesso forzano o invitano passaggi verticali di uscita.

Questo supporta l'idea di un digital twin decisionale: il comportamento di Rodri va modellato in base al contesto, non solo attraverso medie globali.

## Simulazione del Digital Twin

Il modello di simulazione stima, per ogni combinazione di zona di campo e stato di pressione:

- probabilità di un passaggio forward, lateral o backward
- progressione media per ogni direzione di passaggio
- numerosità del campione su cui si basa la stima

Dato un contesto di zona e pressione, il modello può simulare decisioni di passaggio plausibili e simili al comportamento osservato di Rodri.

Esempio:

```python
simulate_player_behavior(
    model=model,
    zone="Defensive",
    under_pressure=True,
    n=20,
    random_state=7,
)
```

Il modello include una logica di fallback per le combinazioni mancanti, così non fallisce senza spiegazione quando un contesto specifico ha dati insufficienti.

## Struttura del notebook

Il notebook finale è organizzato così:

1. Introduzione
2. Dataset
3. Caricamento dei dati
4. Pulizia dei dati
5. Profilo decisionale
6. Analisi spaziale
7. Direzione dei passaggi
8. Analisi della progressione
9. Analisi della pressione
10. Simulazione del Digital Twin
11. Insight principali
12. Limiti
13. Sviluppi futuri

## Miglioramenti di qualità del codice

Il notebook è stato rifattorizzato per essere più pulito e adatto a un portfolio.

Miglioramenti principali:

- funzioni modulari
- sezioni markdown chiare
- filtri sicuri con `.copy()`
- nessun `SettingWithCopyWarning`
- nessun pattern rischioso `fillna(...).astype(bool)`
- caricamento solo dei match selezionati per ridurre l'uso di memoria
- gestione degli errori per file e colonne mancanti
- grafici leggibili con titoli, assi ed etichette sulle barre
- dipendenze semplici e adatte a un progetto da junior data analyst

## Tecnologie utilizzate

- Python
- pandas
- numpy
- matplotlib
- Jupyter Notebook
- StatsBomb Open Data

## Limiti

Questo è un progetto portfolio interpretabile, non un modello professionale basato su tracking data.

Limiti attuali:

- non usa tracking data
- la pressione è trattata come flag binario
- la posizione degli avversari non è modellata
- l'orientamento del corpo non è incluso
- la difficoltà del passaggio non è stimata direttamente
- la fase tattica è semplificata
- il modello simula lo stile decisionale, non il successo del passaggio o l'ottimalità tattica

## Sviluppi futuri

Possibili estensioni:

- includere l'esito del passaggio
- stimare la probabilità di completamento del passaggio
- confrontare Rodri con altri centrocampisti
- aggiungere lunghezza e angolo del passaggio
- separare gioco aperto e palle inattive
- modellare le zone di ricezione
- creare una dashboard
- spostare le funzioni in un modulo Python riutilizzabile

## Obiettivo del progetto

Lo scopo del progetto è mostrare come i dati evento calcistici possano essere trasformati in un modello comportamentale interpretabile.

Il lavoro combina football analysis, data cleaning, ragionamento spaziale e simulazione probabilistica in modo comprensibile, riproducibile e adatto a un portfolio di data analytics.
