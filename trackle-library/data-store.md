---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Data Store

Trackle mette a disposizione di ogni dispositivo un **contenitore di dati in cloud** di tipo JSON. Pensa al Data Store come un **database chiave-valore condiviso** tra il firmware e le App, utile per sapere l'ultimo stato conosciuto di un dispositivo, per salvare le sue configurazioni oppure per aggregare informazioni di diversi dispositivi per esempio in un'applicazione di prodotto.

Il Data Store può essere scritto dalle App, attraverso le API REST, anche quando il dispositivo è offline. Per ogni modifica Trackle invia una notifica al dispositivo, immediata se è subito raggiungibile oppure nel momento in cui torna online. Questo meccanismo permette di costruire potenti logiche di sincronizzazione.

Un Data Store è composto da una o più chiavi, che chiamiamo _proprietà,_ definite a livello di Prodotto. Ogni dispositivo può leggere e scrivere solo le chiavi configurate nel Data Store attraverso la Console o tramite le API REST.

## Trackle.writeStore()

write store(json, REPLACE | MERGE)

the structure of the store must be a json file in which every entry must be defined as a property in trackle cloud product&#x20;

/\*\* \* Mode of operation of the `set()` method. \*/ enum SetMode { REPLACE, ///< Replace the current ledger data. MERGE ///< Update some of the entries of the ledger data. };

fa una scrittura in put (mode replace) o in patch (mode merge) dello store cloud di un json di massimo 4KB

## Trackle.readStore()

read from store() optionally ask for a key entry prop1

richiede una lettura di una prorietà passata come parametro di massimo 1KB

quindi il firmwarista a secondo del caso d'uso può creare un tipo di store diverso, fare esempi

avvertire che le chiamate possibili di write e read si sommano ai publish per calcolare il limite del rate di picco di 4 messaggi / secondo

## Trackle.readStoreCallback()

da implementare per ricevere il valore di una proprietà dello store

## Trackle.changedStoreCallback()

invia un notifica contenente un array del nome delle proprietà modificate nello store dalle api cloud.

se un software fa una modifica allo store, se il dispositivo è online riceverà una notifica di change con il nome della proprietà e potrà decidere se leggerla o no in quel momento dallo store.

se il device è offline alla prima riconnessione riceverà la lista delle proprietà che sono cambiate nel periodo di disconnessione&#x20;

&#x20;se il dispositivo successivamente effettua una read dello store o una o più read di una singola proprietà, oppure fa una scrittura di una o più proprietà, quelle proprietà non verranno più notificate ai successivi riavvii. (synched)

fare diagrammi di sequenza per spiegare il meccanismo di synch.

se il dispositivo deve recepire un comando o un valore in tempo reale il softwarista userà le post.



