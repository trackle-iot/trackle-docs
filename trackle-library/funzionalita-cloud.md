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

# Funzionalità cloud

## Trackle.get

Espone una _funzione che ritorna un valore_ alle API Cloud che può essere chiamata attraverso una richiesta  `GET /v1/devices/{DEVICE_ID}/{REQUEST}` . Ritorna il valore `true`quando la funzione è stata registrata.

`Trackle.get` permette di eseguire codice sul dispositivo e ottenere una risposta attraverso una richiesta alle API Cloud. Si utilizza questa funzionalità quando si vuole ottenere un valore raw oppure un valore che deve essere calcolato al momento della richiesta in base ad un parametro fornito, per es. interrogare un certo slave su bus per ritornare uno specifico valore.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef bool (*user_variable_bool_cb_t)(const char *paramString);
typedef int (*user_variable_int32_cb_t)(const char *paramString);
typedef double (*user_variable_double_cb_t)(const char *paramString);
typedef const char *(*user_variable_char_cb_t)(const char *paramString);

bool trackleGet(Trackle *v, const char *varKey, void *(*varCb)(const char *), Data_TypeDef type);

// ESEMPI
bool myBoolCb(const char *args) {
    return true;
}

int32_t myIntCb(const char *args) {
    return 42;
}

double myDoubleCb(const char *args) {
    return 1.21;
}

const char* myStringCb(const char *args) {
    return "Hasta la vista, baby.";
}

const char* myJsonCb(const char *args) {
    return "{"
        "\"title\": \"The Hangover\","
        "\"director\": \"Todd Phillips\","
        "\"year\": \"2009\","
        "\"genre\": \"Comedy\""
    "}";
}

bool success = trackleGet(trackle_s, "showMeYouLearnedKungFu", myBoolCb, VAR_BOOL);
bool success = trackleGet(trackle_s, "answerTofundamentalQuestion", myIntCb, VAR_INT);
bool success = trackleGet(trackle_s, "howManyGigawatts", myDoubleCb, VAR_DOUBLE);
bool success = trackleGet(trackle_s, "seeYouLater", myStringCb, VAR_STRING);
bool success = trackleGet(trackle_s, "suggestMeMovie", myJsonCb, VAR_JSON);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
typedef bool (*user_variable_bool_cb_t)(const char *paramString);
typedef int (*user_variable_int32_cb_t)(const char *paramString);
typedef double (*user_variable_double_cb_t)(const char *paramString);
typedef const char *(*user_variable_char_cb_t)(const char *paramString);

bool get(const char *varKey, user_variable_bool_cb_t varCb);
bool get(const char *varKey, user_variable_int_cb_t varCb);
bool get(const char *varKey, user_variable_double_cb_t varCb);
bool get(const char *varKey, user_variable_char_cb_t var);
bool get(const char *varKey, void *(*varCb)(const char *), Data_TypeDef type);

// ESEMPI
bool myBoolCb(const char *args) {
    return true;
}

int32_t myIntCb(const char *args) {
    return 42;
}

double myDoubleCb(const char *args) {
    return 1.21;
}

const char* myStringCb(const char *args) {
    return "Hasta la vista, baby.";
}

const char* myJsonCb(const char *args) {
    return "{"
        "\"title\": \"The Hangover\","
        "\"director\": \"Todd Phillips\","
        "\"year\": \"2009\","
        "\"genre\": \"Comedy\""
    "}";
}

bool success = Trackle.get("showMeYouLearnedKungFu", myBoolCb);
bool success = Trackle.get("answerTofundamentalQuestion", myIntCb);
bool success = Trackle.get("howManyGigawatts", myDoubleCb);
bool success = Trackle.get("seeYouLater", myStringCb);
bool success = Trackle.get("suggestMeMovie", myJsonCb, VAR_JSON);
```
{% endtab %}
{% endtabs %}

Per registrare una GET, l'utente deve fornire una chiave `varKey` che è il nome da utilizzare per effettuara la richiesta GET e una `varCb` che è la callback implementata dalla sua applicazione. La richiesta può ritornare uno tra i cinque tipi di dato supportati:

* `BOOL`
* `INT` (intero con segno a 32 bit)
* `DOUBLE`
* `STRING` (la massima lunghezza è di 1024 bytes)
* `JSON` (la massima lunghezza è di 1024 bytes)

Possono essere registrate fino a 20 richieste di dati GET ed il nome di ognuna ha il limite massimo di 32 caratteri.

## Trackle.post()

Espone una _funzione_ alle API Cloud che può essere chiamata attraverso una richiesta `POST /v1/devices/{DEVICE_ID}/{FUNCTION}.` Ritorna il valore `true`quando la funzione è stata registrata.

`Trackle.post` permette di eseguire codice sul dispositivo da una chiamata alle API Cloud. Tipicamente si utilizza questa funzionalità quando si vuole controllare qualcosa sul dispositivo per es. accendere un LED, far suonare un buzzer o controllare una funzione del firmware da Cloud.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePost(Trackle *v, const char *funcKey, user_function_int_char_t *funcCb, Function_PermissionDef permission);

// ESEMPIO
int startHack(const char* arg) {
  .....
  return -1;
}

int sendVirus(const char* arg) {
  .....
  return 1;
}

bool success = TracklePost(trackle_s, "hackNORAD", startHack, OWNER_ONLY);
bool success = TracklePost(trackle_s, "sendVirusToAlienShuttle", sendVirus, ALL_USERS);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool post(const char *funcKey, user_function_int_char_t *funcCb, Function_PermissionDef permission = ALL_USERS);

// ESEMPIO
int startHack(const char* arg) {
  .....
  return 1;
}

int sendVirus(const char* arg) {
  .....
  return 1;
}

bool success = Trackle.post("hackNORAD", startHack, OWNER_ONLY);
bool success = Trackle.post("sendVirusToAlienShuttle", sendVirus, ALL_USERS);
```
{% endtab %}
{% endtabs %}

Per registrare una POST, l'utente deve fornire una chiave `funcKey`, che è il nome da utilizzare per effettuara la chiamata POST e una`funcCb`, che è la callback implementata dalla tua applicazione. Una POST ritorna un numero intero; `-1`è solitamente utilizzato per ritornare un errore.

La POST accetta come parametro una stringa. Questa ha una lunghezza massima limitata a 1024 caratteri ed è codificata in UTF-8.

Possono essere registrate fino a 20 POST, ognuna delle quali ha un nome di massimo 32 caratteri.

_`OWNER_ONLY` flag_

E' possibile **limitare l'accesso** ad una o più funzioni esposte tramite `Trackle.post()` al solo proprietario del dispositivo. In questo modo un dispositivo parte di un Prodotto può avere delle funzioni esclusive per il proprietario del dispositivo che non possono essere chiamate dal manutentore o da altri soggetti a cui è stato concesso il permesso di monitorare i dispositivi di Prodotto.

## Trackle.publish()

Pubblica un evento sul Cloud che verrà inoltrato a tutti gli ascoltatori registrati, come _proprietà_, _notifiche_, _webhooks,_ stream sottoscritti di tipo SSE e altri dispositivi in ascolto via `Trackle.subscribe()`. Ritorna il valore `true`quando l'evento è stato inviato al Cloud.

Questa funzionalità permette al Dispositivo di inviare un evento basato su una condizione. Per esempio puoi collegare un sensore di movimento e generare un messaggio quando viene rilevato un movimento.

Un evento Cloud ha le seguenti proprietà:

* nome (1–32 caratteri ASCII)
* dati fino a 4096 caratteri
* ttl default 30 secondi
* Event\_Type PUBBLICO / PRIVATO
* Event\_Flags con o senza ACK
* msg\_key una chiave numerica opzionale

Le variabili di tipo String devono essere UTF-8 encoded. Non puoi inviare data binari o altre tipologie di caratteri tipo ISO-8859-1. Se hai la necessità di inviare dati binari, puoi codificarli un un formato text-based tipo [Base64](https://github.com/rickkas7/Base64RK).

{% hint style="info" %}
Al momento un dispositivo può pubblicare con un rate di circa 1 evento / sec, con picchi fino a 4 messaggi al secondo. Recuperare l'invio di 4 messaggi impiegherà 4 secondi**.**
{% endhint %}

{% hint style="warning" %}
I piani di utilizzo di Trackle comprendo un tot di messaggi / mese per dispositivo quindi è bene stare attenti a quanti eventi vengono inviati dal dispositivo per non subire blocchi o aumenti di costo rispetto al piano sottoscritto.
{% endhint %}

La chiamata a`Trackle.publish()` ritorna `false` quando:

* il dispositivo non è connesso al Cloud
* il nome dell'evento inizia con `trackle` o `iotready`, questi eventi sono riservati ai dati ufficialmente originati dal Cloud.
* la dimensione dei dati opzionali è maggiore di 4096 caratteri
* è stato superato il rate limit di invio
* si verifica un errore di rete

_`NO_ACK` flag_

A meno che non sia specificato, un evento viene inviato al cloud come messaggio affidabile. Il Dispositivo aspetta per un acknowledgement dal cloud che il messaggio sia stato ricevuto, ed effettua il reinvio del messaggio fino a 3 volte in background prima di lasciar perdere.

`NO_ACK` flag disabilita questo comportamento di acknowledge/retry ed invia il messaggio una sola volta. Questo riduce il consumo di dati per evento, ma introduce la possibilità che questo non raggiunga mai il cloud.

Per esempio, il `NO_ACK` flag potrebbe essere utile per inviare dei valori, come ad esempio la lettura di sensori, per cui la perdita occasione le di un dato sia tollerabile.

_`WITH_ACK` flag_

Questo flag fa sì che per l'evento inviato sia atteso l'acknowledgement dal Cloud per verificarne l'effettiva ricezione. In caso di mancata ricezione dell'ack entro un timeout prestabilito, se configurata la callback, verrà notificato l'errore.

_MSG\_KEY_

È possibile specificare un parametro numerico aggiuntivo come ultimo argomento. Questo valore numerico verrà inoltrato come parametro alle funzioni di callback `completedPublishCallback` e `sendPublishCallback`. Questo parametro può essere utilizzato per tracciare e identificare i messaggi pubblicati nel cloud, verificando la loro effettiva ricezione e di gestire manualmente la ripubblicazione in caso di errori. Nel caso in cui non sia specificata (o sia passsato il valore `0`), la msg\_key viene generata dalla libreria.

{% hint style="info" %}
Diversamente da `Trackle.get` e `Trackle.post,`devi chiamare `Trackle.publish` dal loop() (o da una funzione chiamata dal loop).
{% endhint %}

Pubblicare un evento pubblico, senza ack, con un nome ma nessun contenuto e msg\_key

**Ritorno:** Un `bool` indica il successo: (true o false)

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePublish(Trackle *v, const char *eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = tracklePublish(trackle_s, "Black_Pearl", "", 30, PUBLIC, NO_ACK, 0);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool publish(const char *eventName);
bool publish(string eventName);

// ESEMPIO
bool success = Trackle.publish("Black_Pearl");
```
{% endtab %}
{% endtabs %}

Pubblicare un evento privato, con ack, con un nome, un contenuto e msg\_key

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePublish(Trackle *v, const char *eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = tracklePublish(trackle_s, "Taxi_driver", "You talkin' to me?", 30, PRIVATE, WITH_ACK, 1);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool publish(const char *eventName, const char *data, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);
bool publish(string eventName, const char *data, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = Trackle.publish("Taxi_driver", "You talkin' to me?", PRIVATE, WITH_ACK, 1);
```
{% endtab %}
{% endtabs %}

Pubblicare un evento privato, senza ack, con un nome, un contenuto ma senza msg\_key

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePublish(Trackle *v, const char *eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = tracklePublish(trackle_s, "The_Godfather", "I'll make him an offer he can't refuse.", 30, PRIVATE, NO_ACK, 0);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool publish(const char *eventName, const char *data, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);
bool publish(string eventName, const char *data, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = Trackle.publish("The_Godfather", "I'll make him an offer he can't refuse.", PRIVATE, NO_ACK, 0);
```
{% endtab %}
{% endtabs %}

Pubblicare un evento publico, senza ack, con un nome, un contenuto, il ttl e una msg\_key

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePublish(Trackle *v, const char *eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = tracklePublish(trackle_s, "McClane", "Yippee-Ki-Yay, Motherf*cker!", 60, PUBLIC, NO_ACK, 11);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool publish(const char *eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);
bool publish(string eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
bool success = Trackle.publish("McClane", "Yippee-Ki-Yay, Motherf*cker!", 60, PUBLIC, NO_ACK, 11);
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

## Trackle.subscribe()

Sottoscrive ad un evento pubblicato da un dispositivo. Ritorna un valore `boolean` indicante il successo della sottoscrizione.

Questa funzionalità permette ai dispositivo di parlarsi tra loro. Ad esempio un dispositivo può pubblicare un evento di un sensore ed un altro può generare un allarme o rispondere pubblicando un altro.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool trackleSubscribe(Trackle *v, const char *eventName, EventHandler handler, Subscription_Scope_Type scope, const char *deviceID);

// ESEMPIO
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
  trackleSubscribe(trackle_s, "inception", myHandler, ALL_DEVICES, "");
  while(1) {
    trackleLoop();
  }
  return 0;
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
Trackle.subscribe(const char *eventName, EventHandler handler);
Trackle.subscribe(const char *eventName, EventHandler handler, Subscription_Scope_Type scope);
Trackle.subscribe(const char *eventName, EventHandler handler, const char *deviceID);
Trackle.subscribe(const char *eventName, EventHandler handler, Subscription_Scope_Type scope, const char *deviceId);

// ESEMPIO
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
  Trackle.subscribe("inception", myHandler);
  while(1) {
    Trackle.loop();
  }
  return 0;
}
```
{% endtab %}
{% endtabs %}

Puoi metterti in ascolto sugli eventi privati pubblicati dai tuoi dispositivi utilizzando `MY_DEVICES`.&#x20;

* Specificando MY\_DEVICES si ricevono solo eventi PRIVATI.&#x20;
* Specificando ALL\_DEVICES o omettendo il terzo parametro si ricevono solo eventi PUBBLICI (solo per dispositivi che fanno parte di un prodotto).

Una sottoscrizione è come un filtro su un prefisso. Se ti sottoscrivi a "evento", riceverai tutti gli eventi che iniziano con "evento", incluso "evento", "evento1", "evento/prova" ecc...

{% hint style="warning" %}
Un dispositivo può registrare fino a 4 sottoscrizioni. Dalla quinta registrazione ad un evento, la funzione subscribe ritornerà `false`
{% endhint %}

Gli eventi ricevuti vengono passati alla callback registrata; questa deve essere di tipo void ed accettare 2 parametri:

* Il primo parametro è il nome completo dell'evento pubblicato
* Il secondo parametro è il corpo dell'evento, che può essere NULL.

`Trackle.subscribe()` ritorna un `bool` che indica se l'evento è stato sottoscritto con successo. La libreria invierà la sottoscrizione al Cloud non appena si connetterà allo stesso.

Puoi indicare come ultimo parametro il _Device ID_ del dispositivo a cui vuoi sottoscriverti, nel caso in cui tu sia interessato ai dati di un singolo dispositivo

{% hint style="info" %}
A differenza delle `Trackle.get` e delle`Trackle.post`, puoi chiamare `Trackle.subscribe` sia prima di effettuare la connessione al Cloud che durante il loop.
{% endhint %}

## Trackle.unsubscribe()

Rimuove tutto le sottoscrizioni precedentemente registrate con`Trackle.subscribe()`.&#x20;

## Trackle.setClaimCode()

Imposta il **codice di claim** precedentemente generato tramite le API Cloud. Se il codice è presente nel momento in cui il Dispositivo si connette al Cloud viene inviato un evento di tipo `trackle/device/claim/code` che serve ad associare il Dispositivo al suo proprietario.

{% tabs %}
{% tab title="C" %}
```c
trackleSetClaimCode(trackle_s, "qSHVg4T111RCJ03i0N.....");
```
{% endtab %}

{% tab title="C++" %}
```cpp
Trackle.setClaimCode("qSHVg4T111RCJ03i0N....."); 
```
{% endtab %}
{% endtabs %}

## Eventi

La libreria permette agli sviluppatori di definire il comportamento del firmware allo scatenarsi di specifici eventi (per es. dopo aver inviato un messaggio al cloud) attraverso l'implementazione di callback, in particolare:

### Trackle.**sendPublishCallback**

chiamata ogni volta in cui viene eseguito un [**publish**](broken-reference). Come parametri, oltre al nome dell'evento e al contenuto, viene passata la msg\_key (l'ultimo parametro della funzione publish), che può essere usata come codice del messaggio. e un booleano che specifica se il messaggio è stato effettivamente inviato;

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef void(publishSendCallback)(const char *eventName, const char *data, uint32_t msg_key, bool published);
void trackleSetSendPublishCallback(Trackle *v, publishSendCallback *publish);

// ESEMPIO
void callback_send_publish(const char *eventName, const char *data, uint32_t msg_key, bool published) {
    printf("Event: %s, key: %d", (published ? "cache" : "republish"), msg_key);
    ...
}

trackleSetSendPublishCallback(trackle_s, callback_send_publish);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef void(publishSendCallback)(const char *eventName, const char *data, uint32_t msg_key, bool published);
void setSendPublishCallback(publishSendCallback *publish);

// ESEMPIO
void callback_send_publish(const char *eventName, const char *data, uint32_t msg_key, bool published) {
    printf("Event: %s, key: %d", (published ? "cache" : "republish"), msg_key);
    ...
}

Trackle.setSendPublishCallback(callback_send_publish);
```
{% endtab %}
{% endtabs %}

### Trackle.**completedPublishCallback**

Chiamata alla ricezione dell'ack al publish o se il publish fallisce per timeout.&#x20;

* Se error è uguale a 0, significa che il publish è stato eseguito con successo ed è stato ricevuto l'ack dal server, in caso contrario si è verificato un errore ed il publish non è andato a buon fine;
* il puntatore a callbackData contiene la msg\_key, ovvero l'ultimo parametro passato alla funzione publish().

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef void(publishCompletionCallback)(int error, const void *data, void *callbackData, void *reserved);
void trackleSetCompletedPublishCallback(Trackle *v, publishCompletionCallback *publish);

// ESEMPIO
void callback_complete_publish(int error, const void* data, void* callbackData, void* reserved) {
    uint32_t *msg_key = (uint32_t*)callbackData;
    ...
}

trackleSetCompletedPublishCallback(trackle_s, callback_complete_publish);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef void(publishCompletionCallback)(int error, const void *data, void *callbackData, void *reserved);
void setCompletedPublishCallback(publishCompletionCallback *publish);

// ESEMPIO
void callback_complete_publish(int error, const void* data, void* callbackData, void* reserved) {
    uint32_t *msg_key = (uint32_t*)callbackData;
    ...
}

Trackle.setCompletedPublishCallback(callback_complete_publish);
```
{% endtab %}
{% endtabs %}

### **Trackle.signalCallback**

chiamata quando è ricevuto un messaggio di tipo **signal** dal Cloud. Questa funzione è utile per identificare un dispositivo, per esempio, attraverso l'accensione di un led;

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef void(signalCallback)(bool on, unsigned int param, void *reserved);
void trackleSetSignalCallback(Trackle *v, signalCallback *signal);

// ESEMPIO
void signal_cb(bool on, unsigned int param, void* reserved) {
    printf("signal_cb: %d %d\n", on, param);
}

trackleSetSignalCallback(trackle_s, signal_cb);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef void(signalCallback)(bool on, unsigned int param, void *reserved);
void setSignalCallback(signalCallback *signal);

// ESEMPIO
void signal_cb(bool on, unsigned int param, void* reserved) {
    printf("signal_cb: %d %d\n", on, param);
}

Trackle.setSignalCallback(signal_cb);
```
{% endtab %}
{% endtabs %}

### **Trackle.systemTimeCallback**

chiamata quando viene ricevuto un messaggio di tipo system time dal Cloud (comunicazione orario del server).

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef void(timeCallback)(time_t time, unsigned int param, void *);
void trackleSetSystemTimeCallback(Trackle *v, timeCallback *time);

// ESEMPIO

void system_time_cb(time_t time, unsigned int param, void*)
{
    printf("Server time is %lld\n", (long long)time);
}

trackleSetSystemTimeCallback(trackle_s, system_time_cb);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef void(timeCallback)(time_t time, unsigned int param, void *);
void setSystemTimeCallback(timeCallback *signal);

// ESEMPIO
void system_time_cb(time_t time, unsigned int param, void*)
{
    printf("Server time is %lld\n", (long long)time);
}

Trackle.setSystemTimeCallback(system_time_cb);
```
{% endtab %}
{% endtabs %}
