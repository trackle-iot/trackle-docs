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

# Utenti

Per iniziare ad utilizzare Trackle è necessario creare un account. Un account corrisponde ad un _utente_ ed è identificato da un indirizzo email. Generalmente un utente di Trackle può essere:

* uno sviluppatore firmware del dispositivo connesso
* uno sviluppatore software delle Applicazioni che interagiscono con il dispositivo
* un responsabile tecnico di un prodotto

## Customer

Un customer è generalmente un utente di un Applicazione, cioè parte di un'anagrafica esterna a Trackle, che però ha bisogno di avere dispositivi associati e poter eseguire azioni di controllo.

Un customer viene identificato in Trackle attraverso un codice univoco condiviso con l'Applicazione e può essere definito a livello di Prodotto oppure di Organizzazione. Le Applicazioni che devono ottenere un accesso per autorizzare un customer dovranno richiedere un token di autorizzazione attraverso un client OAuth predefinito a livello di prodotto o organizzazione.

<figure><img src="../.gitbook/assets/Schermata 2023-09-04 alle 15.36.14.png" alt=""><figcaption></figcaption></figure>



