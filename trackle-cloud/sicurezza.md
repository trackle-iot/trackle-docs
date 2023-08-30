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

# Sicurezza

## Autenticazione e comunicazione

Trackle Cloud implementa l'**autenticazione reciproca** utilizzando coppie di chiavi pubbliche / private in formato **RPK** (_Raw Public Key_) per assicurarsi che il tuo dispositivo **sia il tuo dispositivo** e non un _imitatore_ e che il cloud **sia veramente Trackle Cloud** e non un _impostore_ man-in-the-middle. In questo modo entrambe le parti possono essere sicure che l’altro sia chi dice di essere.

Il processo di handshake iniziale crea una **sessione crittografata utilizzando DTLS** su UDP (datagram TLS). Ciò garantisce che i tuoi dati non possano essere monitorati o manomessi durante il trasporto.

La connessione al cloud utilizza il protocollo **CoAP** (_Costrained Application Protocol)_ su **DTLS**. Tutte le funzionalità come _get_, _post_, _publish_, _subscribe_, e aggiornamenti del firmware OTA avvengono su un'unica connessione CoAP.

Effettuando tutte le connessioni dal dispositivo al cloud, nella maggior parte dei casi è possibile utilizzare i dispositivi sulle reti Wi-Fi senza dover apportare modifiche personalizzate al _port forwarding_ o al _firewall_.

## Infrastruttura Cloud

Trackle adotta le _best practice_ e azioni automatiche per ridurre al minimo la superficie di attacco sia a livello di servizio che di dati.

Trackle Cloud usa le migliori piattaforme di hosting della categoria con sicurezza fisica e gestione del rischio ISO 27001, 27017 e 27018.

L'infrastruttura di Trackle è monitorata e testata regolarmente per potenziali vulnerabilità con test di penetrazione e revisioni della sicurezza.

Il traffico verso le API REST di Trackle viene bilanciato in modo intelligente per mitigare gli attacchi di forza bruta. Inoltre, ogni nodo o server di Trackle si trova dietro una rete privata rigorosamente protetta da firewall con rilevamento delle intrusioni per evitare accessi non autorizzati.

## Protezione dei dati personali

Trackle limita intenzionalmente l'ambito dei dati utente archiviati nel cloud.

I flussi di informazioni sensibili transitano da Trackle Cloud che protegge i dati ma non li memorizza. Non memorizziamo informazioni o dati di identificazione personale (mascheriamo l'indirizzo IP) che potrebbero essere utilizzati per compromettere prodotti o clienti.
