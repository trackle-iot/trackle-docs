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

spiegare bene cosa è e casi applicativi



writeStore(json)

fa una scrittura in patch dello store cloud di un json di massimo 4KB

readStore(prop1, prop2, prop3)

fa una lettura delle prorietà passate come parametri di massimo 1KB

quindi il firmwarista a secondo del caso d'uso può creare un tipo di store diverso, fare esempi

avvertire che le chiamate possibili di write e read si sommano ai publish per calcolare il limite del rate di picco di 4 messaggi / secondo

changedStoreCallbak&#x20;

invia il nome delle proprietà modificate nello store dalle api cloud&#x20;

se il dispositivo deve recepire un comando o un valore in tempo reale il softwarista userà le post.

se il softwarista fa una modifica allo store se il dispositivo è online riceve una notifica di change con il nome della proprietà e potrà decidere se leggerla o no in quel momento dallo store

se il device è offline alla prima riconnessione riceverà la lista delle proprietà che sono cambiate nel periodo di disconnessione&#x20;

