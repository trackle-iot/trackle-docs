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

# Prodotto

I dispositivi possono essere parte di un **Prodotto**. Un prodotto identifica un gruppo di dispositivi con lo stesso hardware e le stesse funzionalità. Immagina dei termostati intelligenti connessi: svolgono tutti una funzione simile e possiamo presumere che abbiano lo stesso hardware, firmware, GPIO, ecc... per questo vengono identificati dal Cloud attraverso un **productID** comune. In questo modo se si vogliono fare modifiche al firmware di tutti i dispositivi si rilascia un aggiornamento OTA di prodotto, aggiornando così l'intera flotta.

<figure><img src="../.gitbook/assets/Schermata 2023-09-04 alle 15.39.09.png" alt=""><figcaption></figcaption></figure>

Un prodotto appartiene ad un organizzazione ed ha un proprietario, colui che l'ha creato, che risulta l'amministratore di prodotto. Ogni prodotto può avere un **Team** di più utenti che possono accedere alle informazioni dei dispositivi limitando i permessi di accesso attraverso quattro ruoli: _amministratore, sviluppatore, manutentore_ e _sola lettura_.

{% hint style="info" %}
Un utente può anche essere parte del team di organizzazione e questo gli da visibilità su tutti i prodotti di quell'organizzazione.
{% endhint %}

I dispositivi che sono parte di un prodotto non devono avere per forza un proprietario per essere gestiti. E' possibile comunque associare un proprietario per es. uno sviluppatore del prodotto oppure rendere proprietario un _customer_ attraverso il processo di claim da una App.

Inoltre è possibile definire i prodotti come parte di un gruppo alla creazione del prodotto oppure in seguito, aggiungendo un dispositivo alla volta oppure aggiungendo un batch di dispositivi.
