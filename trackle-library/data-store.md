---
hidden: true
noIndex: true
---

# Data Store

Trackle mette a disposizione di ogni dispositivo un **contenitore di dati in cloud** di tipo JSON. Pensa al Data Store come un **database chiave-valore condiviso** tra il firmware e le App, utile per sapere l'ultimo stato conosciuto di un dispositivo, per salvare le sue configurazioni oppure per aggregare informazioni di diversi dispositivi per esempio in un'applicazione di prodotto.

Il Data Store può essere scritto dalle App, attraverso le API REST, anche quando il dispositivo è offline. Per ogni modifica Trackle invia una notifica al dispositivo, immediata se è subito raggiungibile oppure nel momento in cui torna online. Questo meccanismo permette di costruire potenti logiche di sincronizzazione.

Un Data Store è composto da una o più chiavi, che chiamiamo _proprietà,_ definite a livello di Prodotto. Ogni proprietà viene definita attraverso:

* il nome della chiave
* il tipo di dato (_Integer, Double, String, Boolean, Object, Array_)&#x20;
* il tipo di proprietà:
  * **Device only**_:_ La proprietà può essere scritta solo dal dispositivo.
  * **Cloud only**: La proprietà può essere scritta solo tramite le API Rest.
  * **Sync**: Le modifiche alla proprietà vengono sincronizzate con il dispositivo. Se offline, il dispositivo riceverà la modifica al prossimo handshake se effettuato entro il TTL impostato. Lasciare il TTL vuoto per validità infinita.
* la validità TTL in secondi (se tipo di proprietà _Sync_**)**

Ogni dispositivo può aggiornare il valore del datastore per le proprietà di tipo Device only e/o Sync e può ricevere il valore aggiornato dal cloud per le proprietà ti tipo Sync.

## Trackle.syncState()

Aggiorna il data store in cloud attraverso l'invio di una stringa JSON dove ogni chiave deve essere una proprietà definita a livello di prodotto. Il dispositivo può aggiornare solo le proprietà di tipo Device only e/o Sync. Il dispositivo può aggiornare solo le chiavi necessarie.

La dimensione massima di invio è di 4Kb.

{% tabs %}
{% tab title="C" %}
<pre class="language-c"><code class="lang-c"><strong>// SINTASSI
</strong><strong>void trackleSyncState(Trackle * v, const char* data)
</strong>
// ESEMPIO
trackleSyncState(c, "{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\"}");
</code></pre>
{% endtab %}

{% tab title="C++" %}
```cpp
// SINTASSI
void syncState(const char* data)

// ESEMPIO
Trackle.syncState("{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\"}");
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Attenzione: il tipo di dato nel JSON inviato deve corrispondere al tipo di dato definito nel Data Store per quella proprietà altrimenti Trackle ignorerà l'aggiornamento
{% endhint %}

### Proprietà di tipo Object

Per le proprietà di tipo _Object_ è possibile eseguire un aggiornamento parziale dell'oggetto del Data Store attraverso l'utilizzo nel JSON di chiavi del tipo "chiave.attributo". Per es. se nel Data Store ho specificato una proprietà di tipo Object con chiave _position_ che tra i suoi attributi ha _lat, long, city, country_ potrei voler aggiornare solo lat e long. In questo caso il dispositivo dovrebbe inviare un JSON cosi composto:

{% tabs %}
{% tab title="C" %}
```c
// ESEMPIO
trackleSyncState(c, "{\"position.lat\": 42.45, \"position.long\": 9.81 }");
```
{% endtab %}

{% tab title="C++" %}
```cpp
// ESEMPIO
Trackle.syncState("{\"data\":\"Lorem ipsum dolor sit amet, consectetur adipiscing elit.\"}");
```
{% endtab %}
{% endtabs %}

## Trackle.setUpdateStateCallback()

La callback viene chiamata quando viene ricevuta un aggiornamento dello store dal cloud per le proprietà ti tipo Sync.

* key: la chiave della proprietà
* value: il valore della proprietà

{% tabs %}
{% tab title="C" %}
```c
void update_state_callback(const char* key, const char *value) {
    // handle data(key, value)
}

trackleSetUpdateStateCallback(c, update_state_callback);
```
{% endtab %}

{% tab title="C++" %}
```cpp
void update_state_callback(const char* key, const char *value) {
    // handle data(key, value)
}

Trackle.setUpdateStateCallback(update_state_callback);
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Come documentato nel paragrafo precedente, la dimensione massima del valore della proprietà è di 1K
{% endhint %}

Se un software, attraverso le API REST, effettua una modifica allo store di una proprietà di tipo **Sync**, se il dispositivo è online riceverà subito l'aggiornamento, mentre se il device è offline, riceverà l'aggiornamento alla prima riconnessione se il TTL è valido.

{% hint style="info" %}
Se è necessario che il dispositivo riceva un comando in tempo reale, solamente se è online, è consigliabile utilizzare una Trackle POST.
{% endhint %}



