# Aggiornamenti firmware OTA

Gli aggiornamenti firmware _over-the-air_ (OTA) sono una componente fondamentale e critica di qualsiasi sistema IoT. Trackle permette agli sviluppatori di eseguire aggiornare OTA sia per un dispositivo singolo che per l'intera flotta di prodotto direttamente dalla console o dalle API REST.

Il valore di incorporare le funzionalità di aggiornamento OTA in un prodotto connesso include:

* La possibilità di implementare nuove funzionalità software in un prodotto anche dopo che un dispositivo è stato distribuito sul campo.
* &#x20;L'opportunità di rispondere rapidamente a bug e vulnerabilità della sicurezza senza la necessità di richiami fisici dei dispositivi.
* Garantire che gli sviluppatori embedded possano prototipare rapidamente e implementare senza problemi nuove versioni del firmware del dispositivo, accelerando i cicli di innovazione.

Un aggiornamento OTA di successo richiede un coordinamento complesso tra hardware, firmware del dispositivo, connettività e cloud. Trackle offre un sistema integrato per permetterti di aggiornare il software dei tuoi dispositivi in modo sicuro e affidabile.

## Affidabilità

L'invio di un aggiornamento OTA è probabilmente una delle azioni più rischiose che puoi intraprendere su un dispositivo connesso. Una gestione errata degli aggiornamenti OTA potrebbe come minimo causare interruzioni temporanee o, nel peggiore dei casi, forzare il dispositivo in uno stato irrecuperabile.

Attraverso Trackle Library lo sviluppatore ha tutti gli strumenti già integrati con il cloud per poter ricevere un nuovo firmware verificato ed eseguire in completa sicurezza l'aggiornamento senza interrompere la versione corrente e con la possibilità di ripristinare la versione precedente in caso di problemi di connettività.

## Sicurezza

Dato il rischio associato a un aggiornamento OTA, è fondamentale che gli aggiornamenti vengano eseguiti in modo sicuro e protetto.

### Comunicazioni crittografate

Tutti i messaggi tra i dispositivi e il cloud sono sempre crittografati, inclusi i file del firmware. Ciò elimina potenziali attacchi _man-in-the-midd&#x6C;_&#x65; che cercano di inviare firmware fraudolento al dispositivo.

### **Verifica del mittente**

Ogni tentativo di aggiornamento OTA viene prima verificato per garantire che l'identità del mittente sia un gestore dispositivi approvato.

## Scalabilità

Trackle offre diversi strumenti OTA adatti alle diverse fasi di crescita della flotta.

### Prototipazione

Durante la prototipazione Trackle consente al tuo team di sviluppo di innovare rapidamente. Gli aggiornamenti OTA possono essere inviati su un singolo dispositivo attraverso la Console o tramite le nostre API REST.

### Produzione&#x20;

Passando alla produzione nasce la necessità di distribuire un aggiornamento ad un numero elevato di dispositivi, quindi è fondamentale avere la possibilità di eseguire in batch in modo sicuro gli aggiornamenti OTA su più dispositivi contemporaneamente. Questo è ciò che ti consente di implementare nuove funzionalità software, correggere bug o correggere buchi di sicurezza nella tua flotta.

A questo scopo, Trackle offre aggiornamenti OTA a livello di flotta, anche in questo caso attraverso la Console o tramite le nostre API REST, in due modalità di rilascio:

* Standard: i dispositivi saranno notificati del nuovo rilascio solo alla prossima riconnessione.
* Veloce: invece di attendere che i dispositivi si riconnettano per ricevere un aggiornamento, invia un aggiornamento a tutto il parco dispositivi il più rapidamente possibile pur consentendo al dispositivo di controllare il momento appropriato per l'aggiornamento.

Attraverso la Console di Trackle è possibile sapere se e quando un dispositivi ha ricevuto un aggiornamento e in caso di problemi avere l'evidenza dell'errore.

