# Integrazioni

Le integrazioni consentono agli sviluppatori di estendere l'efficacia di Trackle nei loro sistemi esistenti. Forniscono un modo semplice e sicuro per interagire con un servizio basato su Internet.&#x20;

Le integrazioni di Trackle sfruttano l'autenticazione e la crittografia già fornite con la connessione cloud, utilizzano una quantità di dati notevolmente inferiore rispetto alla connessione diretta e consentono di risparmiare spazio sul codice del firmware o dell'applicazione.

Trackle implementa, oltre alle API REST, due tipi di integrazioni a disposizione degli sviluppatori: Webhook e Server-Sent-Events (SSE).

## Webhook

I webhook rappresentano un modo semplice e flessibile per inviare dati dai tuoi dispositivi ad altre app e servizi su Internet. I webhook colmano il divario tra il mondo fisico e quello digitale, aiutandoti a portare i tuoi dati dove ne hai bisogno.

Potresti utilizzare un webhook per salvare per archiviare dati in un database time series in cloud, aggiornare valori di una dashboard, inviare le previsioni meteo ai tuoi dispositivi, attivare un pagamento, inviare un messaggio di testo e molto altro ancora.

Il flusso tipico prevede che il tuo dispositivo esegua `Trackle.publish()` di uno specifico evento che scatena un webhook. È anche possibile che l'integrazione restituisca i dati al dispositivo. È possibile effettuare chiamate API REST standard (GET, POST, PUT e così via).&#x20;

Inoltre, puoi eseguire semplici manipolazioni dei dati utilizzando i modelli Moustache attraverso il toolkit [Handlebars](https://handlebarsjs.com/): puoi modificare l'URL della richiesta, i dati della richiesta e i dati della risposta. Se lo si desidera, il webhook può anche aggiungere header HTTP, argomenti di query, form, autenticazione Basic e molto altro.

## Server-Sent-Events (SSE) <a href="#server-sent-events-sse" id="server-sent-events-sse"></a>

Tradizionalmente, una pagina web deve inviare una richiesta al server per ricevere nuovi dati; cioè, la pagina richiede dati dal server. Con i [_Server-Sent-Events_](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events), è possibile per un server inviare nuovi dati a una pagina web in qualsiasi momento, inviando messaggi alla pagina web. Questi messaggi in arrivo possono essere trattati come un flusso di eventi.

Il flusso di eventi SSE funziona facendo in modo che il tuo client o server effettui una connessione https (crittografata) in uscita al servizio API REST di Trackle. Questa connessione viene mantenuta aperta e, se arrivano nuovi eventi, vengono immediatamente trasmessi su questo flusso.

Con questo tipo di integrazione gli sviluppatori possono ricevere tutti gli eventi di prodotto (o un sottoinsieme usando un filtro basato sul prefisso) stabilendo una sola connessione, utilizzando la stessa autenticazione (token) delle API REST senza applicare modifiche alla rete poiché funziona anche dietro firewall o NAT senza richiedere inoltro delle porte.&#x20;

Uno sviluppatore dovrà scegliere tra webhook e SSE quale tecnologia si la migliore per la propria Applicazione. Di seguito proponiamo una tabella con le differenze tra le due integrazioni.

### Tabella comparativa

| Webhook                                                       | SSE                                         |
| ------------------------------------------------------------- | ------------------------------------------- |
| Richiede un indirizzo IP pubblico                             | Funziona dietro un firewall o NAT           |
| Richiede un certificato SSL affinché il server supporti https | Flusso crittografato senza certificato SSL  |
| La consegna degli eventi è più affidabile                     | Meglio se gli eventi persi non sono critici |
| Può utilizzare load balancer e server ridondanti              | Consente solo un singolo server             |

