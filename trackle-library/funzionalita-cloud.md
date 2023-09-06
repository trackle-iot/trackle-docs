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

## Trackle.get()

Espone una _funzione che ritorna un valore_ alle API Cloud che può essere chiamata attraverso una richiesta  `GET /v1/devices/{DEVICE_ID}/{REQUEST}` . Ritorna il valore `true`quando la funzione è stata registrata.

`Trackle.get` permette di eseguire codice sul dispositivo e ottenere una risposta attraverso una richiesta alle API Cloud. Si utilizza questa funzionalità quando si vuole ottenere un valore raw oppure un valore che deve essere calcolato al momento della richiesta in base ad un parametro fornito, per es. interrogare un certo slave su bus per ritornare uno specifico valore.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool trackleGet(Trackle *v, const char *varKey, void *(*fn)(const char *), Data_TypeDef type) DYNLIB;

// ESEMPIO
void *myCallbackFunction(const char *args) {
    sprintf(cloudNumberMessage, "The number is %d !", cloudNumber);
    return cloudNumberMessage;
}

bool success = trackleGet(trackle_s, "temperatura", myCallbackFunction, VAR_FLOAT);

```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool get(const char *varKey, user_variable_bool_cb_t fn);
bool get(const char *varKey, user_variable_int_cb_t fn);
bool get(const char *varKey, user_variable_double_cb_t fn);
bool get(const char *varKey, user_variable_char_cb_t fn);
bool get(const char *varKey, void *(*fn)(const char *), Data_TypeDef type);

// ESEMPIO
void *myCallbackFunction(const char *args) {
    sprintf(cloudNumberMessage, "The number is %d !", cloudNumber);
    return cloudNumberMessage;
}

bool success = Trackle.get("temperatura", myCallbackFunction, VAR_FLOAT);
```
{% endtab %}
{% endtabs %}

Per registrare una funzione, l'utente deve fornire una chiave `getFuncKey`, che è il nome da utilizzare per effettuara la richiesta GET e un `getFuncName`, che è il reale nome della funzione che sarà utilizzata dalla tua applicazione. La richiesta può ritornare uno tra i quattro tipi di dato supportati:

* `BOOL`
* `INT`
* `DOUBLE`
* `STRING` (la massima lunghezza è di 1024 bytes)

Possono essere registrate fino a 20 richieste di dati ed il nome di ognuna ha il limite massimo di 32 caratteri.

## Trackle.post()

Espone una _funzione_ alle API Cloud che può essere chiamata attraverso una richiesta [`POST /v1/devices/{DEVICE_ID}/{FUNCTION}`](broken-reference). Ritorna il valore `true`quando la funzione è stata registrata.

`Trackle.post` permette di eseguire codice sul dispositivo da una chiamata alle API Cloud. Tipicamente si utilizza questa funzionalità quando si vuole controllare qualcosa sul dispositivo per es. accendere un LED, far suonare un buzzer o controllare una funzione del firmware da Cloud.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePost(Trackle *v, const char *funcKey, user_function_int_char_t *func, Function_PermissionDef permission) DYNLIB;

// ESEMPIO
int funcName(String extra) {
  return 1;
}

bool success = TracklePost(trackle_s, "funcKey", funcName, ALL_USERS);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool post(const char *funcKey, user_function_int_char_t *func, Function_PermissionDef permission = ALL_USERS);

// ESEMPIO
int funcName(String extra) {
  return 1;
}

bool success = Trackle.post("funcKey", funcName, ALL_USERS);
```
{% endtab %}
{% endtabs %}

Per registrare una funzione, l'utente deve fornire una chiave `funcKey`, che è il nome da utilizzare per effettuara la chiamata POST e un`funcName`, che è il reale nome della funzione che sarà chiamata dalla tua applicazione. Una funzione ritorna un numero intero; `-1`è solitamente utilizzato per ritornare un errore.

La funzione esposta accetta come parametro una stringa. Questa ha una lunghezza massima limitata a 1024 caratteri ed è codificata in UTF-8.

Possono essere registrate fino a 20 funzioni, ognuna delle quali ha un nome di massimo 32 caratteri.

_`OWNER_ONLY` flag_

E' possibile **limitare l'accesso** ad una o più funzioni esposte tramite `Trackle.post()` al solo proprietario del dispositivo. In questo modo un dispositivo parte di un Prodotto può avere delle funzioni esclusive per il proprietario del dispositivo che non possono essere chiamate dal manutentore o da altri soggetti a cui è stato concesso il permesso di monitorare i dispositivi di Prodotto.

## Trackle.publish()

Pubblica un evento sul Cloud che verrà inoltrato a tutti gli ascoltatori registrati, come _proprietà_, _notifiche_, _webhooks,_ stream sottoscritti di tipo SSE e altri dispositivi in ascolto via `Trackle.subscribe()`. Ritorna il valore `true`quando l'evento è stato inviato al Cloud.

Questa funzionalità permette al Dispositivo di inviare un evento basato su una condizione. Per esempio puoi collegare un sensore di movimento e generare un messaggio quando viene rilevato un movimento.

Un evento Cloud ha le seguenti proprietà:

* nome (1–32 caratteri ASCII)
* PUBBLICO / PRIVATO
* dati opzionali fino a 4096 caratteri

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

Pubblicare un evento privato con un dato nome ma nessun dato.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool tracklePublish(Trackle *v, const char *eventName, const char *data, int ttl, Event_Type eventType, Event_Flags eventFlag, uint32_t msg_key);

// ESEMPIO
tracklePublish(trackle_s, "front-door", "unlocked", 30, PRIVATE, WITH_ACK, 1);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
// SINTASSI
bool publish(const char *eventName, const char *data, int ttl = DEFAULT_TTL, Event_Type eventType = PUBLIC, Event_Flags eventFlag = EMPTY_FLAGS, uint32_t msg_key = 0);
bool publish(string eventName, const char *data, int ttl = DEFAULT_TTL, Event_Type eventType = PUBLIC, Event_Flags eventFlag = EMPTY_FLAGS, uint32_t msg_key = 0);
bool publish(const char *eventName, const char *data, Event_Type eventType, Event_Flags eventFlag = EMPTY_FLAGS, uint32_t msg_key = 0);
bool publish(string eventName, const char *data, Event_Type eventType, Event_Flags eventFlag = EMPTY_FLAGS, uint32_t msg_key = 0);
bool publish(const char *eventName);
bool publish(string eventName);

// ESEMPI
Trackle.publish("front-door", "unlocked", 30, PRIVATE, WITH_ACK, 1);
Trackle.publish("front-door", "unlocked", PRIVATE, WITH_ACK, 1);
Trackle.publish("front-door", "unlocked");
```
{% endtab %}
{% endtabs %}

**Ritorno:** Un `bool` indica il successo: (true o false)

{% tabs %}
{% tab title="C" %}
```c
bool success = tracklePublish(trackle_s, "motion-detected", PRIVATE);
if (!success) {
  // get here if event publish did not work
}
```
{% endtab %}

{% tab title="C ++" %}
```cpp
bool success = Trackle.publish("motion-detected", PRIVATE);
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
tracklePublish(trackle_s, "temperature", "19 F", PRIVATE);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
Trackle.publish("temperature", "19 F", PRIVATE);
```
{% endtab %}
{% endtabs %}

Pubblicare un evento pubblico con un nome

{% tabs %}
{% tab title="C" %}
```c
tracklePublish(trackle_s, "motion-detected");
```
{% endtab %}

{% tab title="C ++" %}
```cpp
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
float temperature = sensor.readTemperature();  // by way of example, not part of the API
TracklePublish(trackle_s, "t", String::format("%.2f",temperature), NO_ACK);  // make sure to convert to const char * or String
TracklePublish(trackle_s, "t", String::format("%.2f",temperature), PRIVATE, NO_ACK);
```
{% endtab %}

{% tab title="C ++" %}
```cpp
float temperature = sensor.readTemperature();  // by way of example, not part of the API
Trackle.publish("t", String::format("%.2f",temperature), NO_ACK);  // make sure to convert to const char * or String
Trackle.publish("t", String::format("%.2f",temperature), PRIVATE, NO_ACK);
```
{% endtab %}
{% endtabs %}

_`WITH_ACK` flag_

Questo flag fa sì che `Trackle.publish()` ritorni solo dopo aver ricevuto l'acknowledgement che l'evento pubblicato sia stato ricevuto dal Cloud.

{% tabs %}
{% tab title="C" %}
```c
TracklePublish(trackle_s, "motion-detected", NULL, WITH_ACK);
TracklePublish(trackle_s, "motion-detected", NULL, PRIVATE, WITH_ACK);             
```
{% endtab %}

{% tab title="C ++" %}
```cpp
Trackle.publish("motion-detected", NULL, WITH_ACK);
Trackle.publish("motion-detected", NULL, PRIVATE, WITH_ACK);
```
{% endtab %}
{% endtabs %}

Pubblicare un evento privato affidabile con un nome, un contenuto e un codice univoco.

{% tabs %}
{% tab title="C" %}
```c
float temperature = sensor.readTemperature();
TracklePublish(trackle_s, "t", String::format("%.2f",temperature), PRIVATE, WITH_ACK, "32h4bhj43");
```
{% endtab %}

{% tab title="C++" %}
```cpp
float temperature = sensor.readTemperature();
Trackle.publish("t", String::format("%.2f",temperature), PRIVATE, WITH_ACK, "32h4bhj43");
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Diversamente da `Trackle.get` e `Trackle.post,`devi chiamare `Trackle.publish` dal loop() (o da una funzione chiamata dal loop).
{% endhint %}

## Trackle.subscribe()

Sottoscrive ad un evento pubblicato da un dispositivo. Ritorna un valore `boolean` indicante il successo della sottoscrizione.

Questa funzionalità permette ai dispositivo di parlarsi tra loro. Ad esempio un dispositivo può pubblicare un evento di un sensore ed un altro può generare un allarme o rispondere pubblicando un altro.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
bool trackleSubscribe(Trackle *v, const char *eventName, EventHandler handler, Subscription_Scope_Type scope, const char *deviceID)

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
  trackleSubscribe(trackle_s, "temperature", myHandler, ALL_DEVICES, "");
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
  Trackle.subscribe("temperature", myHandler);
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

chiamata ogni volta in cui viene eseguito un [**publish**](broken-reference). Come parametri, oltre al nome dell'evento e al contenuto, viene passata una chiave (che può essere usata come codice del messaggio) e un booleano che specifica se il messaggio è stato effettivamente inviato;

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef void(publishSendCallback)(const char *eventName, const char *data, uint32_t msg_key, bool published);
void trackleSetSendPublishCallback(Trackle *v, publishSendCallback *publish);

// ESEMPIO
void callback_send_publish(const char *eventName, const char *data, int ttl, string key, bool published) {
    printf("Event: %s, key: %s", (published ? "cache" : "republish"), key.c_str());
    
    if(offline_db) {
        printf("Saving event in db %s, key: %s", (published ? "cache" : "republish"), key.c_str());

        string name(eventName);
        string content(data);
        string db_data ="{\"event\": \"" + name + "\", \"data\": " + content + ", \"key\": \"" + key + "\" , \"ttl\": " + to_string(ttl) + " }";

        if(published) // salvo in cahce
            offline_db->save(0, key, db_data);
        else // salvo in db offline per reinviare
            offline_db->save(1, key, db_data);
    }
}

trackleSetSendPublishCallback(c, callback_send_publish);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef void(publishSendCallback)(const char *eventName, const char *data, uint32_t msg_key, bool published);
void setSendPublishCallback(publishSendCallback *publish);

// ESEMPIO
void callback_send_publish(const char *eventName, const char *data, int ttl, string key, bool published) {
    printf("Event: %s, key: %s", (published ? "cache" : "republish"), key.c_str());
    
    if(offline_db) {
        printf("Saving event in db %s, key: %s", (published ? "cache" : "republish"), key.c_str());

        string name(eventName);
        string content(data);
        string db_data ="{\"event\": \"" + name + "\", \"data\": " + content + ", \"key\": \"" + key + "\" , \"ttl\": " + to_string(ttl) + " }";

        if(published) // salvo in cahce
            offline_db->save(0, key, db_data);
        else // salvo in db offline per reinviare
            offline_db->save(1, key, db_data);
    }
}

Trackle.setSendPublishCallback(callback_send_publish);
```
{% endtab %}
{% endtabs %}

### Trackle.**completedPublishCallback**

chiamata alla ricezione dell'ack al publish o se il publish fallisce per timeout;

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef void(publishCompletionCallback)(int error, const void *data, void *callbackData, void *reserved);
void trackleSetCompletedPublishCallback(Trackle *v, publishCompletionCallback *publish);

// ESEMPIO
void callback_complete_publish(int error, const void* data, void* callbackData, void* reserved) {
    uint32_t *b = (uint32_t*)callbackData;
    char str[20]; snprintf(str, sizeof str, "%lu", (unsigned long)b);
    string del_key(str);

    if(error == 0){
        printf("ACK received from cloud: %s", del_key.c_str());
        ...
    } else {
        printf("ACK not received from cloud: %s", del_key.c_str());
        if(error == SYSTEM_ERROR_TIMEOUT) {
            printf("Publish TIMEOUT: %s", del_key.c_str());
        } else {
            printf("Publish UNDEFINED ERROR: %s", del_key.c_str());
        }
    }
}

trackleSetCompletedPublishCallback(c, callback_complete_publish);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef void(publishCompletionCallback)(int error, const void *data, void *callbackData, void *reserved);
void setCompletedPublishCallback(publishCompletionCallback *publish);

// ESEMPIO
void callback_complete_publish(int error, const void* data, void* callbackData, void* reserved) {
    uint32_t *b = (uint32_t*)callbackData;
    char str[20]; snprintf(str, sizeof str, "%lu", (unsigned long)b);
    string del_key(str);

    if(error == 0){
        printf("ACK received from cloud: %s", del_key.c_str());
        ...
    } else {
        printf("ACK not received from cloud: %s", del_key.c_str());
        if(error == SYSTEM_ERROR_TIMEOUT) {
            printf("Publish TIMEOUT: %s", del_key.c_str());
        } else {
            printf(Publish UNDEFINED ERROR: %s", del_key.c_str());
        }
    }
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

trackleSetSignalCallback(c, signal_cb);
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

trackleSetSystemTimeCallback(c, system_time_cb);
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
