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

# Diagnostica

Trackle Library fornisce dei metodi per inviare lo stato di salute del dispositivo al Cloud. Questa funzionalità permette di monitorare gli eventuali problemi hardware o di rete che si possono presentate una volta che i dispositivi sono stati installati sul campo.&#x20;

I valori di **HealthCheck** disponibili sono raggruppati in 3 categorie:

**Power**: informazioni sulla sorgente di alimentazione:

* **SOURCE**: tipo di sorgente (_VIN_, _batteria_, ecc...) di tipo `power_source_t`
* **BATTERY\_CHARGE**: il valore della carica della batteria in percentuale di tipo `double`
* **BATTERY\_STATE**: lo stato di carica (_in carica_, _non in carica_, _caricata_ ecc...) di tipo `battery_state_t`

**System**: informazioni sul funzionamento del sistema:

* **UPTIME**: il tempo in secondi da quando è stato avviato il dispositivo
* **MEMORY\_TOTAL**: la memoria RAM totale a disposizione del dispostivo;
* **MEMORY\_USED**: la memoria RAM utilizzata dal dispositivo (configurabile anche via callback).

**Network**: informazioni sulla rete e sulla qualità della connessione:

* **CELLULAR\_MOBILE\_COUNTRY\_CODE:** valore decimale che identifica la nazionalità dell'operatore all'interno della rete PLMN (Public Land Mobile Network) GSM. Per l'Italia questo codice è 222.
* **CELLULAR\_MOBILE\_NETWORK\_CODE:** valore decimale che identifica univocamente l'operatore all'interno della PLMN nazionale. In Italia per esempio, vale 01 per Tim, 10 per Vodafone, 88 per Wind.
* **CELLULAR\_LOCATION\_AREA\_CODE:** valore decimale o esadecimale che identifica univocamente una Location Area entro una PLMN. La grandezza di quest'area non è fissa, ma dipende da come è progettata la rete.
* **CELLULAR\_CELL\_ID:** valore decimale o esadecimale che identifica la cella attualmente agganciata. Questo numero dipende dal gestore e dall'area.
* **SIGNAL\_STRENGTH\_V:** valore della potenza del segnale utile ricevuto. Varia, circa, da -30 (valore migliore) a -130 (valore peggiore).
* **SIGNAL\_RSSI:** misura stimata di quanto bene un dispositivo può sentire, rilevare e ricevere segnali da qualsiasi punto di accesso o da un router specifico.&#x20;
* **SIGNAL\_STRENGTH:** valore della potenza del segnale in percentuale; più si avvicina a 100, più alta è la qualità. Come regola generale, più il dispositivo è vicino a una torre o un router, maggiore sarà la potenza del segnale.
* **SIGNAL\_QUALITY\_V:** valore che indica il grado di qualità del segnale agganciato considerando gli RB (Resource Block) allocati, l'SNR ed influenzato dal carico della cella specifica agganciata. Varia da -1 (valore migliore) a -20 (valore peggiore). Il range "ottimale" si aggira dai -6 ai -9 e quello di "carico" dai -10 ai -13/-14.
* **SIGNAL\_QUALITY:** valore della qualità del segnale in percentuale; più si avvicina a 100, più alta è la qualità. Come regola generale, minore è il numero di dispositivi nelle immediate vicinanze che comunicano utilizzando frequenze radio simili, migliore sarà la qualità del segnale
* **SIGNAL\_AT:** tipo di tecnologia utilizzata (_GSM_, _LTE_, _WI-FI_, ecc...) di tipo `hal_net_access_tech_t`

Per valorizzare un parametro di diagnostica va chiamata la funzione relativa alla categoria (**Power, Network** o **System**); questa funzione riceve come parametri l'identificativo (costante) ed il valore a seconda del tipo.

{% tabs %}
{% tab title="C" %}
```c
iotreadyDiagnosticPower(c, BATTERY_STATE, BATTERY_STATE_CHARGED);
iotreadyDiagnosticNetwork(c, SIGNAL_RSSI, -70.5);
iotreadyDiagnosticSystem(c, MEMORY_TOTAL, 10000);
```
{% endtab %}

{% tab title="C++" %}
```cpp
iotready.diagnosticPower(BATTERY_STATE, BATTERY_STATE_CHARGED);
iotready.diagnosticNetwork(SIGNAL_RSSI, -70.5);
iotready.diagnosticSystem(MEMORY_TOTAL, 10000);
```
{% endtab %}
{% endtabs %}

Il parametro per la memoria utilizzata (_MEMORY\_USED_), oltre a come mostrato precedentemente, è aggiornabile anche attraverso una callback che viene automaticamente chiamata ogni 5 secondi.

{% tabs %}
{% tab title="C" %}
```c
int usedmemory_cb() {
    return 10000;
}

iotreadySetUsedMemoryCallback(c, usedmemory_cb);
```
{% endtab %}

{% tab title="C++" %}
```cpp
int usedmemory_cb() {
    return 10000;
}

iotready.setUsedMemoryCallback(usedmemory_cb);
```
{% endtab %}
{% endtabs %}

L'HealthCheck può essere inviato al Cloud in modo automatico o manuale. \
Per configurare l'invio automatico è necessario definire l'intervallo ti tempo tra due invii consecutivi attraverso la funzione `setPublishHealthCheckInterval(uint32_t interval)`.

L'invio manuale avviene chiamando funzione publishHealthCheck().

{% tabs %}
{% tab title="C" %}
```c
// invia la diagnostica ogni minuto
iotreadySetPublishHealthCheckInterval(c, 60*1000);

// invia la diagnostica
iotreadyPublishHealthCheck(c);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// invia la diagnostica ogni minuto
iotready.setPublishHealthCheckInterval(60*1000);

// invia la diagnostica
iotready.publishHealthCheck();
```
{% endtab %}
{% endtabs %}

