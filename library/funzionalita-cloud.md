# Funzionalità cloud

## Trackle.get()

Espone una _variabile_ alle API Cloud in modo che possa essere letta con  `GET /v1/devices/{DEVICE_ID}/{VARIABLE}` . Ritorna il valore `true`quando la variabile è stata registrata.

#### C / C++

Puoi chiamare `Trackle.get` una solo volta per variabile, passando il riferimento ad una variabile globale. Puoi cambiare il valore della suddetta variabile ogni volta che vuoi; il valore della stessa viene letto solo quando viene richiesta, quindi _cambiare il valore della variabile non comporta trasferimento di alcun dato_.

{% tabs %}
{% tab title="C" %}
```c
double tempC = 0;
bool success = trackleGet(c, "temp", tempC, VAR_DOUBLE);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
double tempC = 0;
Trackle.get("temp", tempC);
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Non devi chiamare `Trackle.get` quando cambi il valore della variabile.
{% endhint %}

Ci sono quattro tipi di dato supportati:

* `BOOL`
* `INT`
* `DOUBLE`
* `STRING` (la massima lunghezza è di 622 bytes)

#### Typescript

Puoi chiamare `Trackle.get` una solo volta per variabile, passando la callback da chiamare per restituire il valore corrente della variabile.

```typescript
const getTemperature = (): number => {
  return 23.32;
}

boolean success = Trackle.get('temp', 'double', () => getTemperature());
```

Ci sono cinque tipi di dato supportati:

* `boolean`
* `int`
* `double`
* `string` (la massima lunghezza è di 1200 bytes)
* `json` (la massima lunghezza è di 1200 bytes)

## Trackle.getFn()

Espone una _funzione che ritorna un valore_ alle API Cloud che può essere chiamata attraverso una richiesta  `GET /v1/devices/{DEVICE_ID}/{REQUEST}` . Ritorna il valore `true`quando la funzione è stata registrata.

`Trackle.getFn` permette di eseguire codice sul dispositivo e ottenere una risposta attraverso una richiesta alle API Cloud. Tipicamente si utilizza questa funzionalità quando si vuole ottenere un valore che deve essere calcolato al momento della richiesta in base ad un parametro fornito, per es. interrogare un certo slave su bus per ritornare uno specifico valore.

{% tabs %}
{% tab title="C" %}
```c
```
{% endtab %}

{% tab title="C ++" %}
```cpp
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
```
{% endtab %}
{% endtabs %}

Per registrare una funzione, l'utente deve fornire una chiave `getFuncKey`, che è il nome da utilizzare per effettuara la richiesta GET e un `getFuncName`, che è il reale nome della funzione che sarà utilizzata dalla tua applicazione. La richiesta può ritornare uno tra i quattro tipi di dato supportati:

* `BOOL`
* `INT`
* `DOUBLE`
* `STRING` (la massima lunghezza è di 622 bytes)

Possono essere registrate fino a 20 tra variabili _get_ e funzioni _getFn_ ed il nome di ognuna ha il limite massimo di 64 caratteri.

## Trackle.post()

Espone una _funzione_ alle API Cloud che può essere chiamata attraverso una richiesta [`POST /v1/devices/{DEVICE_ID}/{FUNCTION}`](broken-reference). Ritorna il valore `true`quando la funzione è stata registrata.

`Trackle.post` permette di eseguire codice sul dispositivo da una chiamata alle API Cloud. Tipicamente si utilizza questa funzionalità quando si vuole controllare qualcosa sul dispositivo per es. accendere un LED, far suonare un buzzer o controllare una funzione del firmware da Cloud.

{% tabs %}
{% tab title="C" %}
```c
bool success = TracklePost(c, "funcKey", funcName, ALL_USERS);

int funcName(String extra) {
  return 1;
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
bool success = Trackle.post("funcKey", funcName);

int funcName(String extra) {
  return 1;
}
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
const log = (args: string) => { console.log(args); return 1; }

boolean success = Trackle.post('funcKey', log);
```
{% endtab %}
{% endtabs %}

Per registrare una funzione, l'utente deve fornire una chiave `funcKey`, che è il nome da utilizzare per effettuara la chiamata POST e un`funcName`, che è il reale nome della funzione che sarà chiamata dalla tua applicazione. Una funzione ritorna un numero intero; `-1`è solitamente utilizzato per ritornare un errore.

La funzione esposta accetta come parametro una stringa. Questa ha una lunghezza massima limitata a 622 caratteri ed è codificata in UTF-8.

Possono essere registrate fino a 10 funzioni, ognuna delle quali ha un nome di massimo 64 caratteri.

_`OWNER_ONLY` flag_

E' possibile **limitare l'accesso** ad una o più funzioni esposte tramite `Trackle.post()` al solo proprietario del dispositivo. In questo modo un dispositivo parte di un Prodotto può avere delle funzioni esclusive per il proprietario del dispositivo che non possono essere chiamate dal manutentore o da altri soggetti a cui è stato concesso il permesso di monitorare i dispositivi di Prodotto.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
TracklePost(Iotready* v, const char *funcKey, user_function_int_char_t* funcName, Function_Flags flags);

// ESEMPIO
bool success = TracklePost(c, "funcKey", funcName, OWNER_ONLY);

int funcName(String extra) {
  return 1;
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
Trackle.post(const char *funcKey, user_function_int_char_t* funcName, FunctionFlags flags)

// ESEMPIO
bool success = Trackle.function("funcKey", funcName, OWNER_ONLY);

int funcName(String extra) {
  return 1;
}
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.post(funcKey: string, funcName: (args: string) => number | Promise<number>, functionFlags?: FunctionFlags);

// ESEMPIO
const log = (args: string) => { console.log(args); return 1; }

boolean success = Trackle.post('funcKey', log, "OWNER_ONLY");
```
{% endtab %}
{% endtabs %}

## Trackle.publish()

Pubblica un evento sul Cloud che verrà inoltrato a tutti gli ascoltatori registrati, come _proprietà_, _notifiche_, _webhooks,_ stream sottoscritti di tipo SSE e altri dispositivi in ascolto via `Trackle.subscribe()`. Ritorna il valore `true`quando l'evento è stato inviato al Cloud.

Questa funzionalità permette al Dispositivo di inviare un evento basato su una condizione. Per esempio puoi collegare un sensore di movimento e generare un messaggio quando viene rilevato un movimento.

Un evento Cloud ha le seguenti proprietà:

* nome (1–64 caratteri ASCII)
* PUBBLICO / PRIVATO
* dati opzionali fino a 622 caratteri

Solo il proprietario di un dispositivo può sottoscriversi agli eventi privati. I dispositivi di un prodotto possono sottoscriversi ai messaggi pubblici di altri dispositivi di quel prodotto.

Un dispositivo non deve pubblicare eventi che inizino con la parola case-insensitive "trackle" o "iotready". Questi eventi sono riservati ai dati ufficialmente originati dal Cloud.

Chiamare `Trackle.publish()` quando la connessione al Cloud non è stabilita non pubblicherà l'evento. Questo sarà indicato dal codice di ritorno `false` della funzione.

Le variabili di tipo String devono essere UTF-8 encoded. Non puoi inviare data binari o altre tipologie di caratteri tipo ISO-8859-1. Se hai la necessità di inviare dati binari, puoi codificarli un un formato text-based tipo [Base64](https://github.com/rickkas7/Base64RK).

{% hint style="info" %}
Al momento un dispositivo può pubblicare con un rate di circa 1 evento / sec, con picchi fino a 4 messaggi al secondo. Recuperare l'invio di 4 messagi impiegherà 4 secondi**.**
{% endhint %}

{% hint style="warning" %}
I piani di utilizzo di Trackle comprendo un tot di messaggi / mese per dispositivo quindi è bene stare attenti a quanti eventi vengono inviati dal dispositivo per non subire blocchi o aumenti di costo rispetto al piano sottoscritto.
{% endhint %}

Pubblicare un evento privato con un dato nome ma nessun dato.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
TracklePublish(c, const char *eventName, PublishFlags flags);
TracklePublish(c, String eventName, PublishFlags flags);

// ESEMPIO
TracklePublish(c, "front-door-unlocked", PRIVATE);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
Trackle.publish(const char *eventName, PublishFlags flags);
Trackle.publish(String eventName, PublishFlags flags);

// ESEMPIO
Trackle.publish("front-door-unlocked", PRIVATE);
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.publish(eventName: string, null, eventType: EventType);

// ESEMPIO
Trackle.publish("front-door-unlocked", null, "PRIVATE");
```
{% endtab %}
{% endtabs %}

**Ritorno:** Un `bool` indica il successo: (true o false)

{% tabs %}
{% tab title="C" %}
```c
// ESEMPIO
bool success = TracklePublish(c, "motion-detected", PRIVATE);
if (!success) {
  // get here if event publish did not work
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// ESEMPIO
bool success = Trackle.publish("motion-detected", PRIVATE);
if (!success) {
  // get here if event publish did not work
}
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// ESEMPIO
const success = Trackle.publish("motion-detected", null, "PRIVATE");
if (!success) {
  // get here if event publish did not work
}
```
{% endtab %}
{% endtabs %}

Pubblicare un evento privato con un nome e un contenuto

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
TracklePublish(c, const char *eventName, const char *data, PublishFlags flags);
TracklePublish(c, String eventName, String data, PublishFlags flags);

// ESEMPIO
TracklePublish(c, "temperature", "19 F", PRIVATE);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
Trackle.publish(const char *eventName, const char *data, PublishFlags flags);
Trackle.publish(String eventName, String data, PublishFlags flags);

// ESEMPIO
Trackle.publish("temperature", "19 F", PRIVATE);
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.publish(eventName: string, data: string, eventType: EventType);

// ESEMPIO
Trackle.publish("temperature", "19 F", "PRIVATE");
```
{% endtab %}
{% endtabs %}

Pubblicare un evento pubblico con un nome

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
TracklePublish(c, const char *eventName);
TracklePublish(c, String eventName);

// ESEMPIO
TracklePublish(c, "motion-detected");
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
Trackle.publish(const char *eventName);
Trackle.publish(String eventName);

// ESEMPIO
Trackle.publish("front-door-unlocked");
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.publish(eventName: string);

// ESEMPIO
Trackle.publish("front-door-unlocked");
```
{% endtab %}
{% endtabs %}

Sottoscrivere a un Server-Sent Events con [Cloud API](broken-reference) per un evento pubblico

```
# ESEMPIO
curl -H "Authorization: Bearer {ACCESS_TOKEN_GOES_HERE}" \
    https://api.trackle.io/v1/events/motion-detected

# Will return a stream that echoes text when your event is published
event: motion-detected
data: {"data":"23:23:44","ttl":"60","published_at":"2014-05-28T19:20:34.638Z","deviceid":"0123456789abcdef"}
```

_`NO_ACK` flag_

A meno che non sia specificato, un evento viene inviato al cloud come messaggio affidabile. Il Dispositivo aspetta per un acknowledgement dal cloud che il messaggio sia stato ricevuto, ed effettua il reinvio del messaggio fino a 3 volte in background prima di lasciar perdere.

`NO_ACK` flag disabilita questo comportamento di acknowledge/retry ed invia il messaggio una sola volta. Questo riduce il consumo di dati per evento, ma introduce la possibilità che questo non raggiunga mai il cloud.

Per esempio, il `NO_ACK` flag potrebbe essere utile per inviare dei valori, come ad esempio la lettura di sensori, per cui la perdita occasione le di un dato sia tollerabile.

{% tabs %}
{% tab title="C" %}
```c
// ESEMPIO
float temperature = sensor.readTemperature();  // by way of example, not part of the API
TracklePublish(c, "t", String::format("%.2f",temperature), NO_ACK);  // make sure to convert to const char * or String
TracklePublish(c, "t", String::format("%.2f",temperature), PRIVATE, NO_ACK);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// ESEMPIO
float temperature = sensor.readTemperature();  // by way of example, not part of the API
Trackle.publish("t", String::format("%.2f",temperature), NO_ACK);  // make sure to convert to const char * or String
Trackle.publish("t", String::format("%.2f",temperature), PRIVATE, NO_ACK);
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.publish(eventName: string, data: string, eventType: EventType, eventFlags: EventFlags);

// ESEMPIO
const temperature = sensor.readTemperature();

Trackle.publish("t", temperature.toString(), null, "NO_ACK");
Trackle.publish("t", temperature.toString(), "PRIVATE", "NO_ACK");
```
{% endtab %}
{% endtabs %}

_`WITH_ACK` flag_

Questo flag fa sì che `Trackle.publish()` ritorni solo dopo aver ricevuto l'acknowledgement che l'evento pubblicato sia stato ricevuto dal Cloud.

{% tabs %}
{% tab title="C" %}
```c
// SYNTAX

TracklePublish(c, "motion-detected", NULL, WITH_ACK);
TracklePublish(c, "motion-detected", NULL, PRIVATE, WITH_ACK);             
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SYNTAX

Trackle.publish("motion-detected", NULL, WITH_ACK);
Trackle.publish("motion-detected", NULL, PRIVATE, WITH_ACK);
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.publish(eventName: string, data: string, eventType: EventType, eventFlags: EventFlags);

// ESEMPIO
Trackle.publish("motion-detected", null, null, "WITH_ACK");
Trackle.publish("motion-detected", null, "PRIVATE", "WITH_ACK");
```
{% endtab %}
{% endtabs %}

Pubblicare un evento privato affidabile con un nome, un contenuto e un codice univoco.

{% tabs %}
{% tab title="C" %}
```c
// ESEMPIO
float temperature = sensor.readTemperature();

TracklePublish(c, "t", String::format("%.2f",temperature), PRIVATE, WITH_ACK, "32h4bhj43");
```
{% endtab %}

{% tab title="C++" %}
```cpp
// ESEMPIO
float temperature = sensor.readTemperature();

Trackle.publish("t", String::format("%.2f",temperature), PRIVATE, WITH_ACK, "32h4bhj43");
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.publish(eventName: string, data: string, eventType: EventType, withAck: boolean, messageID: string);

// ESEMPIO
const temperature = sensor.readTemperature();
Trackle.publish("t", temperature.toString(), "PRIVATE", "WITH_ACK", "32h4bhj434");
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Diversamente da `Trackle.get` e `Trackle.post,`devi chiamare `Trackle.publish` dal loop() (o da una funzione chiamata dal loop).
{% endhint %}

## Trackle.subscribe()

Sottoscrive ad un evento pubblicato da un dispositivo. Ritorna un valore `booleano` indicante il successo della sottoscrizione.

Questa funzionalità permette ai dispositivo di parlarsi tra loro. Ad esempio un dispositivo può pubblicare un evento di un sensore ed un altro può generare un allarme o rispondere pubblicando un altro.

{% tabs %}
{% tab title="C" %}
```c
int i = 0;

void myHandler(const char *event, const char *data)
{
  i++;
  Serial.print(i);
  Serial.print(event);
  Serial.print(", data: ");
  if (data)
    Serial.println(data);
  else
    Serial.println("NULL");
}

int main()
{
  TrackleSubscribe(c, "temperature", myHandler, ALL_DEVICES);
  while(1) {
    TrackleLoop();
  }
  return 0;
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
int i = 0;

void myHandler(const char *event, const char *data)
{
  i++;
  Serial.print(i);
  Serial.print(event);
  Serial.print(", data: ");
  if (data)
    Serial.println(data);
  else
    Serial.println("NULL");
}

int main()
{
  Trackle.subscribe("temperature", myHandler, ALL_DEVICES);
  while(1) {
    Trackle.loop();
  }
  return 0;
}
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
const myHandler = (eventName: string, data: string) => {
    console.log('eventName '+eventName);
    console.log('data '+data);
}

Trackle.subscribe("temperature", myHandler, "ALL_DEVICES");
```
{% endtab %}
{% endtabs %}

Puoi metterti in ascolto sugli eventi privati pubblicati dai tuoi dispositivi utilizzando `MY_DEVICES`.&#x20;

{% tabs %}
{% tab title="C" %}
```c
// only private events from my devices
TrackleSubscribe(c, "the_event_prefix", myHandler, MY_DEVICES);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// only private events from my devices
Trackle.subscribe("the_event_prefix", myHandler, MY_DEVICES);
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// only private events from my devices
Trackle.subscribe("the_event_prefix", myHandler, "MY_DEVICES");
```
{% endtab %}
{% endtabs %}

* Specificando MY\_DEVICES si ricevono solo eventi PRIVATI.&#x20;
* Specificando ALL\_DEVICES o omettendo il terzo parametro si ricevolo solo eventi PUBBLICI (solo per dispositivi che fanno parte di un prodotto).

Una sottoscrizione è come un filtro su un prefisso. Se ti sottoscrivi a "evento", riceverati tutti gli eventi che iniziano con "evento", incluso "evento", "evento1", "evento/prova" ecc...

{% hint style="warning" %}
Un dispositivo può registrare fino a 4 sottoscrizioni. Dalla quinta registrazione ad un evento, la funzione subscribe ritornerà `false`
{% endhint %}

Gli eventi ricevuti vengono passati alla callback registrata; questa deve essere di tipo void ed accettare 2 parametri:

* Il primo parametro è il nome completo dell'evento pubblicato
* Il secondo parametro è il corpo dell'evento, che può essere NULL.

`Trackle.subscribe()` ritorna un `bool` che indica se l'evento è stato sottoscritto con successo. La libreria invierà la sottoscrizione al Cloud non appena si connetterà allo stesso.

{% hint style="info" %}
A differenza delle `Trackle.get` e delle`Trackle.post`, puoi chiamare `Trackle.subscribe` sia prima di effettuare la connessione al Cloud che durante il loop.
{% endhint %}

## Trackle.unsubscribe()

Rimuove tutto le sottoscrizioni precedentemente registrate con`Trackle.subscribe()`.&#x20;

## Trackle.prop()

Registra una variabile come _prop._ Una prop è una variabile che può essere sincronizzata con il cloud in modo che le applicazioni possano conoscerne l'ultimo valore senza interrogare il dispositivo e possano aggiornarla da remoto attraverso una richiesta  `PUT /v1/devices/{DEVICE_ID}/{PROP}` .

Pensa ad una _prop_ come ad una proprietà con la quale il cloud può interagire senza dover implementare delle _get,_ delle _post_ o dei _publish_ ad hoc, con il vantaggio di conoscerne sempre l'ultimo valore sincronizzato, anche se il dispositivo è offline.

Una _prop_ può essere configurata in modalità `sync` per essere inviata al cloud ad ogni modifica oppure può essere sincronizzata manualmente al bisogno_._

Una _prop_ può essere aggiornata dal cloud se configurata come `writable` . Se vengono registrate delle _prop_ che possono essere aggiornate da cloud è necessario implementare il metodo setUpdatePropCallback (link a doc firmware).

{% tabs %}
{% tab title="C" %}
```c
```
{% endtab %}

{% tab title="C ++" %}
```cpp
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
// SINTASSI
Trackle.prop(name: string, value: number, sync: boolean, writable: boolean)
Trackle.updatePropValue(name: string, value: number)
Trackle.syncProps(props?: string[])
```
{% endtab %}
{% endtabs %}

Le prop supportano solo i tipi di dato numerici:

* `INT`
* `DOUBLE`

Configurare una prop sincronizzata e scrivibile da cloud

{% tabs %}
{% tab title="C" %}
```c
```
{% endtab %}

{% tab title="C ++" %}
```cpp
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
Trackle.prop("temp", 20.0, true, true);
Trackle.updatePropValue("temp", 21.0); // sent to cloud

Trackle.setUpdatePropCallback((propName: string, value: number, caller?: string) => {
    // validation / other logic
    
    // must return success or fail
    return 1;
});
```
{% endtab %}
{% endtabs %}

Configurare una prop non sincronizzata e inviarla manualmente al bisogno

{% tabs %}
{% tab title="C" %}
```c
```
{% endtab %}

{% tab title="C ++" %}
```cpp
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
Trackle.prop("level", 1, false, false);
Trackle.updatePropValue("level", 2); // not sent to cloud

Trackle.syncProps(["level"]); // sync level with cloud
Trackle.syncProps([]); // sync nothing
Trackle.syncProps(); // sync all props
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Le modifiche alle _prop_ vengono sincronizzate al massimo 1 volta / secondo.
{% endhint %}

{% hint style="warning" %}
Le _prop_ sincronizzate generano traffico ad ogni modifica quindi è bene stare attenti a quanto frequentemente vengono modificate le _prop_ per non subire blocchi o aumenti di costo rispetto al piano sottoscritto.
{% endhint %}

**Scenario applicativo di utilizzo**

\-Prodotto con Applicazione Web di gestione&#x20;

Utilizzando le _prop_ in modalità `sync` e `writable` un'Applicazione Web potrebbe:

* mostrare gli ultimi valori di ogni dispositivo già nella lista dei dispositivi associati oltre che nella pagina di dettaglio
* sfruttare lo stream degli eventi di prodotto di Trackle per aggiornare l'interfaccia in tempo reale ad ogni modifica delle _prop_ migliorando l'esperienza d'uso
* permettere all'utente di comandare il dispositivo da remoto modificando il valore delle _prop_

Lo stesso risultato si potrebbe ottenere senza utilizzare le _prop_ in questo modo:

* ottenendo la lista dispositivi senza poter mostrare gli ultimi valori
* creando una _get_ che restituisca i valori al momento della richiesta per popolare la pagina di dettaglio
* inviando un _publish_ ad ogni modifica di ogni proprietà per avere gli aggiornamenti in tempo reale
* creando una _post_ che scrive le proprietà per comandare il dispositivo da remoto

## Trackle.connect()

Connette il Dispositivo al Cloud. Restituisce il valore `0` se non ci sono errori, un valore `<0` in caso di errore.

{% tabs %}
{% tab title="C" %}
```c
int main()
{
  while(1) {
    if (TrackleConnected(c) == false) {
      TrackleConnect(c);
    }
    TrackleLoop(c);
  }
  return 0;
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
int main()
{
  while(1) {
    if (Trackle.connected() == false) {
      Trackle.connect();
    }
    Trackle.loop();
  }
  return 0;
}
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
try {
  Trackle.connect();
} catch (err) {
  console.error(err.message);
}
```
{% endtab %}
{% endtabs %}

## Trackle.connectionCompleted()

Questo metodo va chiamato quando il socket è stato creato correttamente, in modo da consentire alla libreria di continuare la procedura di collegamento al Cloud.

{% tabs %}
{% tab title="C" %}
```c
int connect_cb_udp(const char* server_address, int server_port) {
    
    .....
    
    int r = setsockopt(cloud_socket, SOL_SOCKET, SO_RCVTIMEO,(struct timeval *)&socket_timeout, sizeof(struct timeval));

    // connect to server
    if(connect(cloud_socket, (struct sockaddr *)&servaddr, sizeof(servaddr)) < 0)
    {
      printf("\n Error : Connect Failed \n");
    }
    
    TrackleConnectionCompleted(c);
    return 1;
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
int connect_cb_udp(const char* server_address, int server_port) {
    
    .....
    
    int r = setsockopt(cloud_socket, SOL_SOCKET, SO_RCVTIMEO,(struct timeval *)&socket_timeout, sizeof(struct timeval));

    // connect to server
    if(connect(cloud_socket, (struct sockaddr *)&servaddr, sizeof(servaddr)) < 0)
    {
      printf("\n Error : Connect Failed \n");
    }
    
    Trackle.connectionCompleted();
    return 1;
}
```
{% endtab %}
{% endtabs %}

## Trackle.disconnect()

Disconnette il Dispositivo dal Cloud.&#x20;

{% tabs %}
{% tab title="C" %}
```c
int counter = 10000;

bool needConnection() {
  --counter;
  if (0 == counter)
    counter = 10000;
  return (2000 > counter);
}

int main() {
  while(1) {
    if (needConnection()) {
      if (!TrackleConnected(c))
        TrackleConnect(c);
    } else {
      if (TrackleConnected(c))
        TrackleDisconnect(c);
    }
    TrackleLoop(c);
  }
  return 0;
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
int counter = 10000;

bool needConnection() {
  --counter;
  if (0 == counter)
    counter = 10000;
  return (2000 > counter);
}

int main() {
  while(1) {
    if (needConnection()) {
      if (!Trackle.connected())
        Trackle.connect();
    } else {
      if (Trackle.connected())
        Trackle.disconnect();
    }
    Trackle.loop();
  }
  returno 0;
}
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
const counter = 100;

const needConnection = () => {
  counter-=1;
  if (counter === 0)
    counter = 100;
  return (counter < 20);
}

setInterval(() => {
  if (needConnection()) {
    if (!Trackle.connected())
        Trackle.connect();
  } else {
    if (Trackle.connected())
        Trackle.disconnect();
  }
}, 1000);
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Quando il dispositivo non è connesso, diverse funzionalità come gli aggiornamenti OTA, leggere `Trackle.get` e chiamare `Trackle.post` non sono disponibili.
{% endhint %}

## Trackle.connected()

Ritorno `true` se il Dispositivo è connasso al Cloud, altrimenti `false`&#x20;

## Trackle.setKeepalive()

Imposta la durate tra i messaggi keepalive. Questi messaggi sono utilizzati per mantenere attiva la connessione con il Cloud.

{% tabs %}
{% tab title="C" %}
```c
TrackleSetKeepalive(c, 1 * 30);    // send a ping every 30 seconds
```
{% endtab %}

{% tab title="C ++" %}
```cpp
Trackle.setKeepAlive(1 * 30);    // send a ping every 30 seconds
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
Trackle.setKeepalive(1 * 30); // send a ping every 30 seconds
```
{% endtab %}
{% endtabs %}

In UDP, il keepalive è utilizzato per implementare "UDP hole punching", ovvero aiuta a mantenera aperta la connessione tra Dispositivo e Cloud. Quando due dispositivi comunicano tra loro, viene creato un port forwarding termporaneo per permettere il passaggio di pacchetti in entrambe le direzioni. Essendo le risorse di rete finite, tutti i canali non utilizzati vengono chiusi e cancellati. Questo farebbe si che un dispositivo non sia più raggiungibile dal Cloud dopo circa 30 secondi.

## Trackle.setClaimCode()

Imposta il [**codice di claim**](broken-reference) precedentemente generato tramite le API Cloud. Se il codice è presente nel momento in cui il Dispositivo si connette al Cloud viene inviato un evento di tipo `trackle/device/claim/code` che serve ad associare il Dispositivo al suo proprietario.

{% tabs %}
{% tab title="C" %}
```c
TrackleSetClaimCode(c, "qSHVg4T111RCJ03i0N.....");
```
{% endtab %}

{% tab title="C++" %}
```cpp
Trackle.setClaimCode("qSHVg4T111RCJ03i0N....."); 
```
{% endtab %}

{% tab title="Typescript" %}
```typescript
Trackle.setClaimCode("qSHVg4T111RCJ03i0N.....");
```
{% endtab %}
{% endtabs %}

## Trackle.loop()

Esegue il  background loop. Solo per C / C++.

`Trackle.loop()` controlla se ci sono messaggi dal Cloud e nel caso li processa. Gestisce anche l'invio dei keep-alive al cloud; se non viene chiamata abbastanza frequentemente, la connessione con il Cloud verrà persa.

{% tabs %}
{% tab title="C " %}
```c
int main() {
  while (1) {
    TrackleLoop(c);
    redundantLoop();
  }
}

void redundantLoop() {
  Serial.println("Well that was unnecessary.");
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
int main() {
  while (1) {
    Trackle.loop();
    applicationLoop();
  }
}

void applicationLoop() {
  ...
}
```
{% endtab %}
{% endtabs %}

`Trackle.loop()` è una funzione che blocca l'esecuzione del firmware per alcuni millisecondi. Più frequentemente viene chiamata, più il tempo di esecuzione si riduce e più il dispositivo risponde con velocità e la connessione con il Cloud resta aperta.

## Trackle.forceTcpProtocol()

Cambia il protocollo di comunicazione passando da UDP con DTLS a TCP con cifratura dei pacchetti. \
Solo per NodeJS.

Utile in situazioni in cui è preferibile avere una connessione socket sempre attiva. Non preferibile quando si utilizzano reti mobile perché viene generato molto più traffico.&#x20;

{% hint style="warning" %}
La chiave privata del dispositivo e la chiave pubblica del server devono essere cambiate per poter connettere un dispositivo tramite protocollo TCP.
{% endhint %}
