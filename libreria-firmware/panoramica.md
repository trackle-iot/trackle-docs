# Panoramica

La **libreria** di Trackle permette agli sviluppatori di connettere rapidamente e in tutta semplicità i dispositivi al Cloud. E' possibile creare soluzioni IoT innovative in maniera semplice e veloce utilizzando la piattaforma hardware preferita. Trackle ne supporta alcune out-of-the-box ma la libreria può funzionare su qualsiasi hardware.

La libreria si collega al Cloud attraverso il protocollo di comunicazione **CoAP** \(IETF RFC 7252\), acronimo di Constrained Application Protocol. CoAP è un protocollo leggero, appositamente progettato per dispositivi IoT con capacità di elaborazione limitate che comunicano su reti con banda disponibile ridotta. A differenza di altri protocolli, sebbene supporti i paradigmi publisher/subscriber ed observer, CoAP è un protocollo one-to-one molto simile ad HTTP. Questo permette di sfruttare la leggerezza del protocollo ma di avere comunque una comunicazione bidirezionale permanente.

CoAP utilizza **UDP** come protocollo di trasporto predefinito \(ogni messaggio CoAP viene inviato all'interno di un _datagram UDP_\) e implementa le funzionalità software per garantire l'invio, la ricezione e l'ordinamento dei pacchetti \(ACK e messageId\). La **sicurezza** della comunicazione è assicurata dall’implementazione dello standard **DTLS** \(_Datagram Transport Layer Security_\), un protocollo progettato per proteggere la privacy dei dati e prevenire intercettazioni e manomissioni.

{% hint style="info" %}
**DTLS** è il protocollo di sicurezza consigliato da **GSMA** per le applicazioni IoT che usano le reti mobile di tipo **LTE-M** e **NB-IoT**. [Link](https://www.gsma.com/iot/wp-content/uploads/2019/09/Security-Features-of-LTE-M-and-NB-IoT-Networks.pdf)
{% endhint %}



