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

# Organizzazione

Un'Organizzazione rappresenta un'entità di livello superiore che può possedere molti prodotti, avere un team di utenti e dei _customer_ che possono avere accesso a dispositivi di prodotti diversi, per es. nel caso di utenti di un'App multi prodotto.

<figure><img src="../.gitbook/assets/Schermata 2023-09-04 alle 15.48.00.png" alt=""><figcaption></figcaption></figure>

Ciascuna Organizzazione dispone di un team e ciò è molto utile per i membri della tua azienda che dovrebbero avere visibilità su tutte le attività IoT della tua organizzazione. Ogni utente nel team di organizzazione avrà accesso a tutti i Prodotti ereditando il ruolo assegnato nel team di organizzazione. Con una singola azione puoi autorizzare un utente a gestire molti Prodotti.

Anche per quanto l'autenticazione e autorizzazione alle API REST di Prodotto, creando un client OAuth a livello di Organizzazione, un software esterno può ottenere un token di organizzazione che lo autorizza ad accedere alla risorse di tutti i Prodotti dell'organizzazione.
