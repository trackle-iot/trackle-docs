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

# Data Store

Trackle mette a disposizione di ogni dispositivo un **contenitore di dati in cloud** di tipo JSON. Pensa al Data Store come un **database chiave-valore condiviso** tra il firmware e le App, utile per sapere l'ultimo stato conosciuto di un dispositivo, per salvare le sue configurazioni oppure per aggregare informazioni di diversi dispositivi per esempio in un'applicazione di prodotto.

Il Data Store può essere scritto dalle App, attraverso le API REST, anche quando il dispositivo è offline. Per ogni modifica Trackle invia una notifica al dispositivo, immediata se è subito raggiungibile oppure nel momento in cui torna online. Questo meccanismo permette di costruire potenti logiche di sincronizzazione.

Un Data Store è composto da una o più chiavi, che chiamiamo _proprietà,_ definite a livello di Prodotto. Ogni dispositivo può leggere e scrivere solo le chiavi configurate nel Data Store attraverso la Console o tramite le API REST.

## Trackle.writeStore()

write store(json, REPLACE | MERGE)

Il parametro della funzione deve essere un file json in cui ogni voce deve essere definita come <mark style="color:purple;">proprietà nel prodotto trackle cloud.</mark>

La dimensione massima di una scrittura in put (mode REPLACE) o in patch (mode MERGE) dello store è di 4Kb.

{% tabs %}
{% tab title="C" %}
<pre class="language-c"><code class="lang-c"><strong>// SINTASSI
</strong>typedef enum
{
    STORE_WRITE_REPLACE = 0, // sostituisci lo store corrente
    STORE_WRITE_MERGE        // aggiorna una o più attibuiti dello store
} Store_Write_Type

<strong>void trackleWriteStore(Trackle * v, const char* data, Store_Write_Type type) {
</strong>
// ESEMPIO
trackleWriteStore(c, "{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\"}", STORE_WRITE_MERGE);
trackleWriteStore(c, "{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\", \"data2\":\"Morbi auctor tincidunt ipsum, et egestas felis posuere a\"}", STORE_WRITE_REPLACE);
</code></pre>
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
typedef enum
{
    STORE_WRITE_REPLACE = 0, // sostituisci lo store corrente
    STORE_WRITE_MERGE        // aggiorna una o più attibuiti dello store
} Store_Write_Type

void writeStore(const char* data, Store_Write_Type type) {

// ESEMPIO
Trackle.writeStore("{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\"}", STORE_WRITE_MERGE);
Trackle.writeStore("{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\", \"data2\":\"Morbi auctor tincidunt ipsum, et egestas felis posuere a\"}", STORE_WRITE_REPLACE);
```
{% endtab %}
{% endtabs %}

## Trackle.readStore()

Richiede il valore di una proprietà dsllo store. È necessario passare come argomento la chiave della proprietà che si desidera leggere.&#x20;

La dimensione massima della proprietà che è possibile leggere è di 1KB.&#x20;

{% hint style="info" %}
I messaggi di lettura e scrittura dello store rientrano nel rate di invio degli eventi, di circa 1 evento / sec, con picchi fino a 4 messaggi al secondo.
{% endhint %}

## Trackle.readStoreCallback()

La callback viene chiamata quando viene ricevuta una proprietà dal cloud, richiesta con il metodo readStore:

* key: la chiave della proprietà
* data: il valore della proprietà

{% tabs %}
{% tab title="C" %}
```c
void read_store_callback(const char* key, const char* data) {
    // handle data
}

trackleSetReadStoreCallback(c, read_store_callback);
```
{% endtab %}

{% tab title="C++" %}
```cpp
void read_store_callback(cons char* data) {
  // handle json data
}

Trackle.setReadStoreCallback(read_store_callback);
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Come documentato nel paragrafo precedente, la dimensione massima del valore della proprietà (data) è di 1KB
{% endhint %}

## Trackle.changedStoreCallback()

Questa callback viene chiamata quando il valore di una o più proprietà sullo store è stato aggiornato sul Cloud ed è quindi necessario che il firmware legga il nuovo valore della proprietà:

* keys: elenco delle chiavi delle proprietà modificate, separate da virgola

{% tabs %}
{% tab title="C" %}
```c
void changed_store_callback(const char* keys) {
    // handle data
}

trackleSetChangedStoreCallback(c, changed_store_callback);
```
{% endtab %}

{% tab title="C++" %}
```cpp
void changed_store_callback(const char* keys) {
    // handle data
}

Trackle.setChangedStoreCallback(changed_store_callback);
```
{% endtab %}
{% endtabs %}

Se un software, attraverso le API REST, effettua una modifica allo store, se il dispositivo è online riceverà subito una notifica di change con il nome della proprietà e potrà decidere se eseguire una lettura con la funzione `readStore()` o ignorarla.

Se invece il device è offline, alla prima riconnessione riceverà la lista delle proprietà che sono state modificate nel periodo di disconnessione.

Se il dispositivo successivamente effettua una read dello store o una proprietà, oppure fa una scrittura di una o più proprietà, quelle proprietà non verranno più notificate ai successivi riavvii (in quanto risulteranno _sincronizzate_ con il dispositivo)

<mark style="color:purple;">fare diagrammi di sequenza per spiegare il meccanismo di synch.</mark>

{% hint style="info" %}
Se è necessario che il dispositivo riceva un comando in tempo reale, solamente se è online, è consigliabile utilizzare una Trackle POST.
{% endhint %}

## Modalità di utilizzo dello store

In funzione delle necessità, è possibile utilizzare lo in diverse modalità:

* SOLA SCRITTURA: se lo store è utilizzato solamente per rendere disponibili dei dati in cloud attraverso le API REST ma non è necessario leggerli / riceverli dal dispositivo, è possibile creare e scrivere singolarmente le proprietà. In questo modo ogni proprietà può avere una dimensione massima di 4KB ed è possibile gestire store di grandi dimensioni.
* SOLA LETTURA o LETTURA E SCRITTURA: nel caso in cui il dispositivo abbia la necessità di leggere lo store, è imporante ricordare che la dimensione massima di una proprietà in lettura è di 1KB. Potrebbe quindi essere possibile scrivere più chiavi con una singola write, in quanto la dimensione della singola scrittura può arrivare a 4KB, prestando sempre attenzione a non superare la dimensione di 1KB per la singola proprietà.



