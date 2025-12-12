# Configurazione

La configurazione minima per implementare un client Trackle è la seguente:

{% tabs %}
{% tab title="C" %}
```c
#include <trackle_interface.h>

int main(int argc, char *argv[]) {
    Trackle *trackle_s = newTrackle();
    // Inizializzazione
    trackleInit(trackle_s);
    trackleSetMillis(trackle_s, get_millis_cb);
    // Autenticazione
    trackleSetDeviceId(trackle_s, DEVICE_ID);
    trackleSetKeys(trackle_s, PRIVATE_KEY);
    // Configurazione del socket di comunicazione 
    trackleSetSendCallback(trackle_s, send_cb_udp);
    trackleSetReceiveCallback(trackle_s, receive_cb_udp);
    trackleSetConnectCallback(trackle_s, connect_cb_udp);
    trackleSetDisconnectCallback(trackle_s, disconnect_cb);
    // Connessione
    trackleConnect(trackle_s);
    // Loop
    while (1)
    {
        trackleLoop(trackle_s);
        ...
        // Application Loop
        ...
	usleep(20 * 1000);
    }
    return 0;
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
#include <trackle.h>

int main(int argc, char *argv[]) {
    Trackle trackle;
    // Inizializzazione
    trackle.setMillis(get_millis_cb);
    // Autenticazione
    trackle.setDeviceId(DEVICE_ID);
    trackle.setKeys(PRIVATE_KEY);
    // Configurazione del socket di comunicazione 
    trackle.setSendCallback(send_cb_udp);
    trackle.setReceiveCallback(receive_cb_udp);
    trackle.setConnectCallback(connect_cb_udp);
    trackle.setDisconnectCallback(disconnect_cb);
    // Connessione
    trackle.connect();
    // Loop
    while (1)
    {
        trackle.loop();
        ...
        // Application Loop
        ...
        usleep(20 * 1000);
    }
    return 0;
}
```
{% endtab %}
{% endtabs %}

## Inizializzazione

### Trackle.setMillis()

Imposta una callback che ritorna il numero di millisecondi da cui il software è in esecuzione.

{% tabs %}
{% tab title="C" %}
```c
static system_tick_t get_millis_cb(void) {
    struct timeval tp;
    gettimeofday(&tp, NULL);
    long int ms = tp.tv_sec * 1000 + tp.tv_usec / 1000;
    return (uint32_t)ms;
}

trackleSetMillis(trackle_s, get_millis_cb);
```
{% endtab %}

{% tab title="C++" %}
```cpp
static system_tick_t get_millis_cb(void) {
    struct timeval tp;
    gettimeofday(&tp, NULL);
    long int ms = tp.tv_sec * 1000 + tp.tv_usec / 1000;
    return (uint32_t)ms;
}

trackle.setMillis(get_millis_cb);
```
{% endtab %}
{% endtabs %}

## Autenticazione &#x20;

Per ogni dispositivo che si vuole connettere a Trackle è necessario possedere un **ID Dispositivo** e una **chiave privata**.&#x20;

Per ottenere un ID dispositivo e una chiave privata puoi seguire questi passaggi:

* Crea un account su Trackle Cloud attraverso la _Console_ ([https://trackle.cloud/](https://trackle.cloud/))
* Dalla pagina "Dispositivi" clicca sul bottone "Claim di un dispositivo"
* Clicca sul link "Non hai un ID dispositivo?", poi su continua
* L'ID Dispositivo verrà mostrato sullo schermo e il file della chiave privata verrà scaricato automaticamente dal browser con il nome \<id\_dispositivo>.der&#x20;

<figure><img src="../.gitbook/assets/Schermata 2023-08-25 alle 17.49.02 (1).png" alt="" width="563"><figcaption></figcaption></figure>

La connessione verso Trackle Cloud è **reciprocamente autenticata** utilizzando coppie di chiavi pubbliche / private in formato **RPK** (_Raw Public Key_).

La chiave privata del dispositivo ottenuta al passaggio precedente deve essere memorizzata sul dispositivo e deve essere essere mantenuta segreta. Trackle Cloud memorizza la chiave pubblica di ogni dispositivo.&#x20;

Per quanto riguarda il cloud, la chiave privata del cloud viene mantenuta segreta, mentre tutti i dispositivi conoscono la chiave pubblica del cloud. La chiave pubblica del cloud non è un segreto.

### Trackle.setDeviceId()

Configura l'ID univoco del dispositivo (_DeviceID_) ottenuto dalla Console. L'ID dispositivo **deve** essere un numero di 12 byte che identifica univocamente il dispositivo.

### Trackle.setKeys()

Configura la _chiave privata_ per questo dispositivo. Non è necessario impostare la chiave pubblica del cloud in quanto è già codificata nella libreria.

{% hint style="info" %}
Per ottenere l'array esadecimale dal file .der della chiave privata scaricata dalla Console è possibile usare questo comando su sistemi unix:

`cat private_key.der | xxd -i`
{% endhint %}

{% tabs %}
{% tab title="C" %}
```cpp
// ID univoco del dispositivo
char DEVICE_ID[13] = {0xd1, 0xaf, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 0x06};

// chiave privata del dispositivo
const uint8_t PRIVATE_KEY[PRIVATE_KEY_LENGTH] =
  {0x30, 0x77, 0x02, 0x01, 0x01, 0x04, ... };
  
trackleSetDeviceId(trackle_s, DEVICE_ID);
trackleSetKeys(trackle_s, PRIVATE_KEY);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// ID univoco del dispositivo
char DEVICE_ID[13] = {0xd1, 0xaf, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 0x06};

// chiave privata del dispositivo
const uint8_t PRIVATE_KEY[PRIVATE_KEY_LENGTH] =
  {0x30, 0x77, 0x02, 0x01, 0x01, 0x04, ... };
  
trackle.setDeviceId(DEVICE_ID);
trackle.setKeys(PRIVATE_KEY);
```
{% endtab %}
{% endtabs %}

## Comunicazione

Trackle Library si collega al Cloud attraverso il protocollo di comunicazione **CoAP** (IETF [RFC 7252](https://tools.ietf.org/html/rfc7252)), acronimo di _Constrained Application Protocol._ CoAP è un protocollo leggero, appositamente progettato per dispositivi IoT con capacità di elaborazione limitate che comunicano su reti con banda disponibile ridotta.&#x20;

CoAP utilizza **UDP** come protocollo di trasporto predefinito (ogni messaggio CoAP viene inviato all'interno di un _datagram UDP_) e implementa le funzionalità software per garantire l'invio, la ricezione e l'ordinamento dei pacchetti (ACK e messageId). La **sicurezza** della comunicazione è assicurata dall’implementazione dello standard **DTLS** (_Datagram Transport Layer Security_), un protocollo progettato per proteggere la privacy dei dati e prevenire intercettazioni e manomissioni.

Per permettere ad un client di comunicare con il cloud è necessario implementare le callback che definiscono come creare e distruggere un socket UDP e come inviare e ricevere dati su quel socket UDP. La libreria si occupa di cifrare la comunicazione e di mantenere il canale sempre attivo.

### Trackle.**setConnectCallback**()

Imposta una callback che crea il socket UDP verso il cloud e ritorna il risultato.

{% tabs %}
{% tab title="C" %}
```c
struct sockaddr_in servaddr;
int cloud_socket;

int connect_cb_udp(const char *address, int port)
{
    printf("Connecting socket");
    int addr_family;
    int ip_protocol;
    char addr_str[128];

    struct hostent *res = gethostbyname(address);
    if (res)
    {
        printf("Dns address %s resolved", address);
    }
    else
    {
        printf("error resolving gethostbyname %s resolved", address);
        return -1;
    }

    memcpy(&cloud_addr.sin_addr.s_addr, res->h_addr, sizeof(cloud_addr.sin_addr.s_addr));

    cloud_addr.sin_family = AF_INET;
    cloud_addr.sin_port = htons(port);
    addr_family = AF_INET;
    ip_protocol = IPPROTO_IP;
    inet_ntoa_r(cloud_addr.sin_addr, addr_str, sizeof(addr_str) - 1);

    cloud_socket = socket(addr_family, SOCK_DGRAM, ip_protocol);
    if (cloud_socket < 0)
    {
        printf("Unable to create socket: errno %d", errno);
    }
    printf("Socket created, sending to %s:%d", address, port);

    // setto i timeout di lettura/scrittura del socket
    struct timeval socket_timeout;
    socket_timeout.tv_sec = 0;
    socket_timeout.tv_usec = 1000; // 1ms
    setsockopt(cloud_socket, SOL_SOCKET, SO_RCVTIMEO, (struct timeval *)&socket_timeout, sizeof(struct timeval));

    return 1;
}

trackleSetConnectCallback(trackle_s, connect_cb_udp);
```
{% endtab %}

{% tab title="C++" %}
```cpp
struct sockaddr_in servaddr;
int cloud_socket;

int connect_cb_udp(const char *address, int port)
{
    printf("Connecting socket");
    int addr_family;
    int ip_protocol;
    char addr_str[128];

    struct hostent *res = gethostbyname(address);
    if (res)
    {
        printf("Dns address %s resolved", address);
    }
    else
    {
        printf("error resolving gethostbyname %s resolved", address);
        return -1;
    }

    memcpy(&cloud_addr.sin_addr.s_addr, res->h_addr, sizeof(cloud_addr.sin_addr.s_addr));

    cloud_addr.sin_family = AF_INET;
    cloud_addr.sin_port = htons(port);
    addr_family = AF_INET;
    ip_protocol = IPPROTO_IP;
    inet_ntoa_r(cloud_addr.sin_addr, addr_str, sizeof(addr_str) - 1);

    cloud_socket = socket(addr_family, SOCK_DGRAM, ip_protocol);
    if (cloud_socket < 0)
    {
        printf("Unable to create socket: errno %d", errno);
    }
    printf("Socket created, sending to %s:%d", address, port);

    // setto i timeout di lettura/scrittura del socket
    struct timeval socket_timeout;
    socket_timeout.tv_sec = 0;
    socket_timeout.tv_usec = 1000; // 1ms
    setsockopt(cloud_socket, SOL_SOCKET, SO_RCVTIMEO, (struct timeval *)&socket_timeout, sizeof(struct timeval));

    return 1;
}

trackle.setConnectCallback(connect_cb_udp);
```
{% endtab %}
{% endtabs %}

### Trackle.**setDisconnectCallback**()

Imposta una callback che esegue la chiusura del socket UDP e ritorna il risultato.

{% tabs %}
{% tab title="C" %}
```c
int disconnect_cb()
{
    if (cloud_socket)
        close(cloud_socket);
    return 1;
}

trackleSetDisconnectCallback(trackle_s, disconnect_cb);
```
{% endtab %}

{% tab title="C++" %}
```cpp
int disconnect_cb()
{
    if (cloud_socket)
        close(cloud_socket);
    return 1;
}

trackle.setDisconnectCallback(disconnect_cb);
```
{% endtab %}
{% endtabs %}

### Trackle.setSendCallback()

Imposta una callback che esegue la scrittura di un buffer dati sul socket UDP e ritorna il numero di byte inviati.

{% tabs %}
{% tab title="C" %}
```c
int send_cb_udp(const unsigned char *buf, uint32_t buflen, void *tmp)
{
    size_t sent = sendto(cloud_socket, (const char *)buf, buflen, 0, (struct sockaddr *)&cloud_addr, sizeof(cloud_addr));
    return (int)sent;
}

trackleSetSendCallback(trackle_s, send_cb_udp);
```
{% endtab %}

{% tab title="C++" %}
```cpp
int send_cb_udp(const unsigned char *buf, uint32_t buflen, void *tmp)
{
    size_t sent = sendto(cloud_socket, (const char *)buf, buflen, 0, (struct sockaddr *)&cloud_addr, sizeof(cloud_addr));
    return (int)sent;
}

trackle.setSendCallback(send_cb_udp);
```
{% endtab %}
{% endtabs %}

### Trackle.**setReceiveCallback**()

Imposta una callback che legge il socket UDP e ritorna il numero di byte ricevuti.

{% tabs %}
{% tab title="C" %}
```c
int receive_cb_udp(unsigned char *buf, uint32_t buflen, void *tmp)
{
    size_t res = recvfrom(cloud_socket, (char *)buf, buflen, 0, (struct sockaddr *)NULL, NULL);

    // on timeout error, set bytes received to 0
    if ((int)res < 0 && errno == 11)
    {
        res = 0;
    }

    return (int)res;
}

trackleSetReceiveCallback(trackle_s, receive_cb_udp);
```
{% endtab %}

{% tab title="C++" %}
```cpp
int receive_cb_udp(unsigned char *buf, uint32_t buflen, void *tmp)
{
    size_t res = recvfrom(cloud_socket, (char *)buf, buflen, 0, (struct sockaddr *)NULL, NULL);

    // on timeout error, set bytes received to 0
    if ((int)res < 0 && errno == 11)
    {
        res = 0;
    }

    return (int)res;
}

trackle.setReceiveCallback(receive_cb_udp);
```
{% endtab %}
{% endtabs %}

### **Trackle.setRandomCallback**

Imposta una callback per la generazione di un numero random. Se non definita viene utilizzata la funziona rand() che ritorna un numero pseudo-casuale.

{% tabs %}
{% tab title="C" %}
```c
// SINTASSI
typedef uint32_t(randomNumberCallback)(void);
void trackleSetRandomCallback(Trackle *v, randomNumberCallback *random);

// ESEMPIO
uint32_t random_callback() {
    return rand();
}

trackleSetRandomCallback(c, random_callback);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef uint32_t(randomNumberCallback)(void);
void setRandomCallback(randomNumberCallback *random);

// ESEMPIO
uint32_t random_callback() {
    return rand();
}

Trackle.setRandomCallback(random_callback);
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Se l'hardware utilizzato dispone di metodi più sicuri per la generazione di un numero random, è possibile configurarli attraverso questa callback.
{% endhint %}

## Connessione

Dopo aver implementato la gestione del socket il client può tentare la connessione al cloud.

### Trackle.c**onnect**()

Tenta la connessione al cloud. Restituisce il valore `0` se non ci sono errori, un valore `<0` in caso di errore.

### Trackle.connected()

Ritorna `true` se il dispositivo è connesso al cloud, altrimenti `false` .

### Trackle.**loop**()

Esegue il background loop che si occupa della comunicazione bidirezionale tra dispositivo e cloud di mantenere il canale attivo. Se non viene chiamata abbastanza frequentemente, la connessione con il Cloud verrà persa.

{% hint style="warning" %}
`Trackle.loop()` è una funzione che blocca l'esecuzione del firmware per alcuni millisecondi. Più frequentemente viene chiamata, più il tempo di esecuzione si riduce e più il dispositivo risponde con velocità.
{% endhint %}

{% tabs %}
{% tab title="C " %}
```c
int main() {
    trackleConnect(trackle_s);
    while (1)
    {
        trackleLoop(trackle_s);
        ...
        // Application Loop
        ...
        usleep(20 * 1000);
    }
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
int main() {
    trackle.connect();
    while (1)
    {
        trackle.loop();
        ...
        // Application Loop
        ...
        usleep(20 * 1000);
    }
}
```
{% endtab %}
{% endtabs %}

### Trackle.disconnect()

Tenta di disconnettere il dispositivo dal cloud.&#x20;

{% tabs %}
{% tab title="C" %}
<pre class="language-c"><code class="lang-c">int counter = 10000;

bool needConnection() {
  --counter;
  if (0 == counter)
    counter = 10000;
  return (2000 > counter);
}

int main() {
  while(1) {
    if (needConnection()) {
<strong>      if (!trackleConnected(trackle_s))
</strong>        trackleConnect(trackle_s);
    } else {
      if (trackleConnected(trackle_s))
        trackleDisconnect(trackle_s);
    }
    trackleLoop(trackle_s);
    ...
    // Application Loop
    ...
    usleep(20 * 1000);
  }
  return 0;
}
</code></pre>
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
      if (!trackle.connected())
        trackle.connect();
    } else {
      if (trackle.connected())
        trackle.disconnect();
    }
    trackle.loop();
    ...
    // Application Loop
    ...
    usleep(20 * 1000);
  }
  return 0;
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Quando il dispositivo non è connesso, diverse funzionalità come gli aggiornamenti OTA,  `Trackle.get` e `Trackle.post` non sono disponibili.
{% endhint %}

## Prodotti

Per i dispositivi che fanno parte di un prodotto e' necessario specificare anche un **ID Prodotto** e una **versione del firmware**.&#x20;

### Trackle.setProductId()

Configura l'ID prodotto di cui è parte il dispositivo.

### Trackle.setFirmwareVersion()

Configura la versione firmware del prodotto.

{% tabs %}
{% tab title="C" %}
```c
const uint_16_t PRODUCT_ID = 20;
const uint_16_t PRODUCT_FIRMWARE_VERSION = 1;

trackleSetProductId(trackle_s, PRODUCT_ID);
trackleSetFirmwareVersion(trackle_s, PRODUCT_FIRMWARE_VERSION);
```
{% endtab %}

{% tab title="C++" %}
```cpp
const uint_16_t PRODUCT_ID = 20;
const uint_16_t PRODUCT_FIRMWARE_VERSION = 1;

trackle.setProductId(PRODUCT_ID);
trackle.setFirmwareVersion(PRODUCT_FIRMWARE_VERSION);
```
{% endtab %}
{% endtabs %}

### Trackle.setComponentsList()

Permette di specificare una lista di componenti utilizzati ed inviarli al cloud. Questa informazione può essere utile per monitorare in modo più dettagliato le risorse e le funzionalità disponibili sul dispositivo.

{% tabs %}
{% tab title="C" %}
```c
trackleSetComponentsList(trackle_s, "trackle-library-esp-idf:v2.2.1");
```
{% endtab %}

{% tab title="C++" %}
```cpp
Trackle.setComponentsList("trackle-library-esp-idf:v2.2.1");
```
{% endtab %}
{% endtabs %}

## Blockwise

Il Blockwise Transfer (RFC 7959) permette di suddividere un messaggio CoAP in blocchi numerati, rendendo possibile la gestione di payload molto grandi durante operazioni di Publish e Get senza superare i limiti del protocollo o della memoria disponibile.

La Trackle Library usa un sistema di buffer per memorizzare i blocchi:

* buffer **interno**, gestito automaticamente
* buffer **esterno**, gestito dall’utente (consigliato quando si ha poca RAM o si vuole ottimizzare l’allocazione)

La scelta tra buffer interno ed esterno dipende dai requisiti di memoria del dispositivo.

### Configurazione

La dimensione e la capacità del sistema blockwise vengono configurate via macro di compilazione:

#### **TRACKLE\_BLOCKS\_NUMBER**

Numero massimo di blocchi che la libreria può memorizzare internamente.\
Più blocchi = messaggi più grandi gestibili, ma maggiore consumo di RAM.

Valori possibili: da 1 a 32 (default 4)

```c
#define TRACKLE_BLOCKS_NUMBER 16
```

#### **TRACKLE\_CONCURRENT\_MESSAGES**

Numero massimo di messaggi Blockwise che possono essere gestiti contemporaneamente.

Valori possibili: da 1 a 4 (default 4)

```c
#define TRACKLE_CONCURRENT_MESSAGES 2
```

### Buffer interno vs buffer esterno

#### **Buffer interno (default)**

* Allocato dalla libreria.
* Semplice da usare, nessuna configurazione aggiuntiva.
* Occupa RAM interna alla Trackle Library.
* Non permette un controllo preciso del consumo di memoria.

#### **Buffer esterno**

* Allocato dall’utente.
* Permette di utilizzare:
  * RAM esterna,
  * RAM statica,
  * memoria più efficiente.
* Utile in sistemi embedded a bassa RAM.
* Si abilita tramite la funzione **trackleSetExternalBuffer()**.

{% tabs %}
{% tab title="C" %}
```c
trackleSetExternalBuffer(Trackle *v, uint8_t *extBuffer, size_t size);
```
{% endtab %}

{% tab title="C++" %}
```cpp
boo Trackle.setExternalBuffer(uint8_t *extBuffer, size_t size);
```
{% endtab %}
{% endtabs %}

#### Parametri

* **extBuffer** – puntatore al buffer esterno allocato dall’utente
* **size** – dimensione totale del buffer

#### Valore ritornato

* `true` se il buffer è stato impostato correttamente
* `false` se la dimensione è insufficiente

```c
#define EXTERNAL_BUFFER_SIZE 4096 * 4 

uint8_t external_blockwise_buffer[EXTERNAL_BUFFER_SIZE];

// Imposto un buffer esterno per la gestione dei blocchi
bool ok = trackleSetExternalBuffer(
    trackle_s,
    external_blockwise_buffer,
    EXTERNAL_BUFFER_SIZE
);

if (!ok) {
    printf("Errore: buffer esterno troppo piccolo!\n");
}
```

#### Note importanti

* Il buffer deve essere **statico o globale**, non locale (sullo stack).
*   La dimensione deve essere sufficiente per contenere:

    ```
    TRACKLE_BLOCKS_NUMBER × TRACKLE_CONCURRENT_MESSAGES * 1024
    ```
* Se troppo piccolo, la libreria segnala errore.
