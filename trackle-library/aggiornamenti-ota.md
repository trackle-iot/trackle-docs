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

# Aggiornamenti OTA

Una delle funzionalità più importanti di una piattaforma IoT è quella di permettere ai produttori di eseguire aggiornamenti firmware _over-the-air_ ai loro dispositivi connessi. Potrebbe essere necessario aggiornare il firmware ad esempio per aggiungere nuove funzionalità o applicare patch di sicurezza.&#x20;

## Inizializzazione

Trackle Library supporta la funzionalità di aggiornamento OTA in due modi:

1. occupandosi dell'invio del firmware verso i dispositivi connessi garantendo la consegna e l'integrità del file (modalità _push_)
2. inviando al dispositivo le informazioni per eseguire il download del firmware (modalità _sendUrl_)

### Trackle.setOtaMethod

Imposta la modalità di aggiornamento OTA

{% tabs %}
{% tab title="C" %}
<pre class="language-c"><code class="lang-c"><strong>// SINTASSI
</strong><strong>void trackleSetOtaMethod(Trackle * v, Ota_Method method) {
</strong>
// ESEMPIO
trackleSetOtaMethod(trackle_s, PUSH); // Modalità push
trackleSetOtaMethod(trackle_s, SEND_URL); // Modalità sendUrl
</code></pre>
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
void Trackle.setOtaMethod(Ota_Method method) {

// ESEMPIO
trackle.setOtaMethod(PUSH); // Modalità push
trackle.setOtaMethod(SEND_URL); // Modalità sendUrl
```
{% endtab %}
{% endtabs %}

## Modalità _PUSH_

Per implementare la modalità PUSH la libreria fornisce allo sviluppatore 3 callback: `prepareForFirmwareUpdateCallback()`,  `saveFirmwareChunkCallback()` e `finishFirmwareUpdateCallback()`.&#x20;

L'implementazione delle prime 2 non è obbligatoria; nel caso in cui lo sviluppatore decida di implementare solo la terza callback, all'avvio di un firmware update verrà creata una variabile in RAM della dimensione del firmware, verrà aggiornata alla ricezione di ogni chunk ed al termine verrà passata come parametro alla callback `finishFirmwareUpdateCallback()`. Se il dispositivo dispone di RAM limitata è vivamente consigliato implementare anche le prime due callback e scrivere i chunk ricevuti in flash.

### Trackle.**prepareForFirmwareUpdateCallback**

chiamata quando dal Cloud viene avviato un aggiornamento firmware e il dispositivo è pronto per riceverlo.  Riceve un parametro di tipo Chunk contentente:

* file\_length: dimensione totale del firmware;
* chunk\_size: dimensione di ogni singolo chunk;
* chunk\_count: numero totale di chunk;

{% tabs %}
{% tab title="C" %}
```c
void prepare_firmware_update(Chunk data, uint32_t flags, void* reserved) {
    printf("firmware size %d\n", data.file_length);
    printf("chunk size %d\n", data.chunk_size);
    printf("chunk count %d\n", data.chunk_count);
}

trackleSetPrepareForFirmwareUpdateCallback(c, prepare_firmware_update);
```
{% endtab %}

{% tab title="C++" %}
```cpp
void prepare_firmware_update(Chunk data, uint32_t flags, void* reserved) {
    printf("firmware size %d\n", data.file_length);
    printf("chunk size %d\n", data.chunk_size);
    printf("chunk count %d\n", data.chunk_count);
}

Trackle.setPrepareForFirmwareUpdateCallback(prepare_firmware_update);
```
{% endtab %}
{% endtabs %}

### Trackle.**saveFirmwareChunkCallback**

chiamata alla ricezione di ogni chunk durante un firmware update. Riceve un primo parametro di tipo Chunk contenente:

* file\_length: dimensione totale del firmware;
* chunk\_size: dimensione del chunk;
* chunk\_number: numero del chunk;
* chunk\_offset: posizione di memoria in cui scrivere la porzione di firmware.

Come secondo parametro riceve un `unsigned char*` contenente la porzione di firmware.

{% tabs %}
{% tab title="C" %}
```c
void save_firmware_chunk(struct Chunk data, const unsigned char* chunk, void* reserved) {
    printf("received chunk number %d\n", data.chunk_count);
}

trackleSetSaveFirmwareChunkCallback(c, save_firmware_chunk);
```
{% endtab %}

{% tab title="C++" %}
```cpp
void save_firmware_chunk(struct Chunk data, const unsigned char* chunk, void* reserved) {
    printf("received chunk number %d\n", data.chunk_count);
}

Trackle.setSaveFirmwareChunkCallback(save_firmware_chunk);
```
{% endtab %}
{% endtabs %}

### Trackle.**finishFirmwareUpdateCallback**

chiamata al termine della ricezione del firmware update. &#x20;

* Nel caso in cui non siano state implementate le 2 callback precedenti, riceve come primo parametro un `unsigned char*` contenente il firmware completo e come secondo parametro un `uint32_t` indicante la dimensione totale del firmware.
* Nel caso in cui siano state implementate le 2 callback precedenti, riceve come primo parametro un `unsigned char*` vuoto e come secondo parametro un `uint32_t` indicante la dimensione totale del firmware.

{% tabs %}
{% tab title="C" %}
```cpp
void finish_update(char *data, uint32_t fileSize) {
    printf("Finished fw update, received fw %d\n", fileSize);
}

trackleSetFinishFirmwareUpdateCallback(c, finish_update);
```
{% endtab %}

{% tab title="C++" %}
```cpp
void finish_update(char *data, uint32_t fileSize) {
    printf("Finished fw update, received fw %d\n", fileSize);
}

Trackle.setFinishFirmwareUpdateCallback(finish_update);
```
{% endtab %}
{% endtabs %}

## Modalità _SEND\_URL_

Trackle Library fornisce la possibilità di eseguire l'aggiornamento del firmware Over The Air (OTA) in modalità "send\_url". In questa modalità, il firmware viene notificato di un aggiornamento disponibile e vengono fornite le informazioni necessarie per scaricarlo da un URL e validarlo.

### Trackle.otaUpdateCallback

Questa callback viene chiamata dal Cloud quando è disponibile un aggiornamento firmware con il metodo sendUrl, contiene come parametri:

* url: L'URL da cui scaricare il firmware.
* crc: Il valore CRC32 del firmware. Può essere utilizzato per verificare l'integrità del firmware scaricato.

{% tabs %}
{% tab title="C" %}
```cpp
bool firmware_ota_url(const char *url, uint32_t crc) {
    // configure and start update task
    if (..... ) 
        return true; // if firmware download i
    else
        return false; // if error occured
}

trackleSetOtaUpdateCallback(c, firmware_ota_url);
```
{% endtab %}

{% tab title="C++" %}
```cpp
bool firmware_ota_url(const char *url, uint32_t crc) {
    // configure and start update task
    if (..... ) 
        return true; // if firmware download i
    else
        return false; // if error occured
}

Trackle.setOtaUpdateCallback(c, firmware_ota_url);
```
{% endtab %}
{% endtabs %}

La callback deve ritornare `true` se l'aggiornamento è stato iniziato con successo, `false` in caso di errore.

{% hint style="warning" %}
Questa callback è **bloccante** per l'esecuzione della libreria e deve ritornare un valore `boolean` il prima possibile.

Va utilizzata come notifica di avvio dell'aggiornamento OTA, ma il download e la scrittura del firmware devono essere eseguiti in un altro task non bloccante.
{% endhint %}

È possibile, per valutare l'integrità del file scaricato, confrontare il `crc` del file scaricato con quello contenuto nell'argomento della callback. Il crc (Cyclic Redundancy Check) utilizzato è di tipo CRC32 `Little Endian`.

Al termine dell'aggiornamento deve essere chiamato il metodo `setOtaUpdateDone`, che accetta il parametro `error_code`. Impostare `error_code` a 0 per indicare che l'aggiornamento è stato completato con successo. In caso di errore, impostare `error_code` con il valore corrispondente all'errore che si è verificato.

{% tabs %}
{% tab title="C" %}
```cpp
trackleSetOtaUpdateDone(c, 0);
trackleSetOtaUpdateDone(c, 3);
```
{% endtab %}

{% tab title="C++" %}
```cpp
Trackle.setOtaUpdateDone(0);
Trackle.setOtaUpdateDone(3);
```
{% endtab %}
{% endtabs %}

La mancata chiamata del metodo `setOtaUpdateDone` comporterà, lato Cloud, il fallimento dell'aggiornamento ota per timeout.

## Configurazione OTA

Trackle Library permette allo sviluppatore di poter controllare e far eseguire un aggiornamento firmware solo quando il dispositivo è disponibile a farlo.

Di default gli aggiornamenti sono attivi; lo sviluppatore può scegliere di disabilitare gli aggiornamenti in qualsiasi momento chiamando la funzione `Trackle.disableUpdates()` e riabilitarli nuovamente con `Trackle.enableUpdates()`.

Quando dal Cloud si lancia un aggiornamento in modalità "Fast", questo viene immediatamente eseguito non appena viene chiamato `Trackle.enableUpdates()` mentre in modalità "Standard" l'aggiornamento viene eseguito alla prima riconnessione del dispositivo al Cloud.

{% hint style="info" %}
La chiamata delle funzioni disableUpdates() ed enableUpdates()  genera un messaggio dal dispositivo al Cloud, comportando quindi l'utilizzo di una piccola quantità di dati.
{% endhint %}

### Trackle.disableUpdates()

Disabilita gli aggiornamenti OTA sul dispositivo. Se, dopo aver utilizzato questa funzione, si cerca di eseguire un aggiornamento dal Cloud, il dispositivo lo blocca ed invia un errore. Non sarà più possibile effettuare aggiornamenti OTA a meno di [abilitare la forzatura.](broken-reference)

Mentre gli aggiornamenti sono disabilitati se si tenta di inviare un aggiornamento firmware al dispositivo questo salverà l'informazione del fatto che c'è un aggiornamento in sospeso. Da quel momento la funzione `Trackle.updatesPending()` ritornerà `true`.

### Trackle.enableUpdates()

Abilita gli aggiornamenti OTA sul dispositivo. All'avvio del dispositivo gli aggiornamenti sono sempre abilitati.

Utilizzando questa funzione, se gli aggiornamenti non erano già attivi in precedenza, il dispositivo notificherà al Cloud la sua disponibilità.  Il Cloud a questo punto, se ha degli aggiornamenti da fare, li invierà al dipositivo.&#x20;

### Trackle.updatesEnabled()

Ritorna `true` all'avvio e dopo aver chiamato la funzione`System.enableUpdates()`. Ritorna `false` dopo aver chiamato la funzione `System.disableUpdates()`.

### Trackle.updatesPending()&#x20;

Ritorna `true` se per il dispositivo ci sono aggiornamenti firmware in sospeso. Nel caso in cui gli aggiornamenti siano disabilitati, è possibile effettuare l'aggiornamento o chiamando la funzione `Trackle.enableUpdates()` o inviando un update forzato dal Cloud.

### Trackle.updatesForced()

Quando il dispositivo non è disponibile per gli aggiornamenti, l'aggiornamento del firmware in sospeso non viene normalmente consegnato al dispositivo. Gli aggiornamenti possono essere forzati nel Cloud per ignorare l'impostazione locale sul dispositivo. In questo caso la funzione `Trackle.updatesForced()` ritorna `true`.

In questo modo gli aggiornamenti del firmware vengono consegnati anche quando `System.disableUpdates()` è stato chiamato dall'applicazione del dispositivo.&#x20;

