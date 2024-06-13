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

Trackle Library offre un set di metodi per inviare le informazioni relative allo stato di salute del dispositivo al Cloud. Questa funzionalità è essenziale per il monitoraggio dei potenziali problemi hardware o di rete che possono emergere dopo l'installazione dei dispositivi sul campo.

Le informazioni di diagnostica (HealthCheck) sono suddivise in tre categorie: **Cloud**, **System e Network.**  La diagnostica Cloud (numero di connessioni, disconnessioni, roundtrip time, ecc...) viene gestita in maniera autonoma dalla Trackle Library.

Per configurare, valorizzare o aggiornare un dato di diagnostica deve essere chiamata la funzione specifica della categoria (**System** o **Network**), indicando la "chiave" del parametro (elencate sotto) ed il relativo valore.&#x20;

Ad esempio, per aggiornare il numero di tentativi di connessione alla rete e specificare la memoria ram totale del dispositivo, devono essere utilizzati i seguenti comandi:

{% tabs %}
{% tab title="C" %}
```c
trackleDiagnosticNetwork(trackle_s, NETWORK_CONNECTION_ATTEMPTS, 10);
trackleDiagnosticSystem(trackle_s, SYSTEM_TOTAL_RAM, 10000);
```
{% endtab %}

{% tab title="C++" %}
```cpp
Trackle.diagnosticNetwork(NETWORK_CONNECTION_ATTEMPTS, 10);
Tracklr.diagnosticSystem(SYSTEM_TOTAL_RAM, 10000);
```
{% endtab %}
{% endtabs %}

**System**: informazioni sul funzionamento del sistema:

* **SYSTEM\_LAST\_RESET\_REASON**: Fornisce il motivo dell'ultimo reset del sistema. Può aiutare a identificare la causa di riavvii o reset del dispositivo.
* **SYSTEM\_FREE\_MEMORY**: Restituisce la quantità di memoria libera disponibile nel sistema. Questa chiave può essere utilizzata per monitorare l'utilizzo della memoria.
* **SYSTEM\_BATTERY\_CHARGE**: Rappresenta lo stato di carica della batteria del dispositivo in percentuale. Questa chiave fornisce informazioni sulla carica residua della batteria.
* **SYSTEM\_SYSTEM\_LOOPS**: Fornisce il numero di cicli di loop del sistema. Questa chiave può essere utilizzata per tenere traccia del numero di iterazioni del ciclo principale del sistema.
* **SYSTEM\_APPLICATION\_LOOPS**: Restituisce il numero di cicli di loop dell'applicazione. Questa chiave può essere utilizzata per monitorare il numero di iterazioni del ciclo dell'applicazione.
* **SYSTEM\_UPTIME**: Rappresenta il tempo trascorso (in secondi) dall'ultima accensione o riavvio del dispositivo.&#x20;
* **SYSTEM\_BATTERY\_STATE**: Lo stato di carica attuale della batteria. I possibili valori sono _unknown, not\_charging, charging, charged, discharging, fault, disconnected._
* **SYSTEM\_POWER\_SOURCE**: Un'enumerazione che descrive la fonte di energia attuale. I valori possibili sono _unknown, VIN, USB host, USB adapter, USB otg._
* **SYSTEM\_TOTAL\_RAM**:  la memoria RAM totale a disposizione del dispostivo.
* **SYSTEM\_USED\_RAM**: la memoria RAM utilizzata dal dispositivo.

**Network**: informazioni sulla rete e sulla qualità della connessione:

* **NETWORK\_CONNECTION\_STATUS**: Stato della connessione di rete. Quando si riceve un evento vitale attraverso il Cloud è necessario che sia sempre connesso. I valori possibili sono _turned\_off, turning\_on, disconnected, connecting, connected, disconnecting, turning\_off._
* **NETWORK\_CONNECTION\_ERROR\_CODE:** Un codice di errore specifico della piattaforma restituito dalla funzione di basso livello del sistema operativo del dispositivo per l'evento di connettività di rete più recente.
* **NETWORK\_DISCONNECTS:** Conteggio delle disconnessioni di rete .
* **NETWORK\_CONNECTION\_ATTEMPTS**: Numero di tentativi necessari per stabilire una connessione di rete .
* **NETWORK\_DISCONNECTION\_REASON**: Ultimo motivo per cui la rete si è disconnessa. I valori possibili sono _none, error, user, network\_off, listening, sleep, reset._
* **NETWORK\_IPV4\_ADDRESS**: Rappresenta l'indirizzo IPv4 locale del dispositivo. Questo indirizzo è utile per identificare il dispositivo sulla rete.
* **NETWORK\_IPV4\_GATEWAY**: Restituisce l'indirizzo del gateway IPv4 utilizzato dal dispositivo per comunicare con la rete.
* **NETWORK\_RSSI:** misura stimata di quanto bene un dispositivo può sentire, rilevare e ricevere segnali da qualsiasi punto di accesso o da un router specifico.&#x20;
* **NETWORK\_SIGNAL\_STRENGTH\_VALUE:** valore della potenza del segnale utile ricevuto. Varia, circa, da -30 (valore migliore) a -130 (valore peggiore).
* **NETWORK\_SIGNAL\_STRENGTH:** valore della potenza del segnale in percentuale; più si avvicina a 100, più alta è la qualità. Come regola generale, più il dispositivo è vicino a una torre o un router, maggiore sarà la potenza del segnale.
* **NETWORK\_SIGNAL\_QUALITY:** valore della qualità del segnale in percentuale; più si avvicina a 100, più alta è la qualità. Come regola generale, minore è il numero di dispositivi nelle immediate vicinanze che comunicano utilizzando frequenze radio simili, migliore sarà la qualità del segnale
* **NETWORK\_SIGNAL\_QUALITY\_VALUE:** valore che indica il grado di qualità del segnale agganciato considerando gli RB (Resource Block) allocati, l'SNR ed influenzato dal carico della cella specifica agganciata. Varia da -1 (valore migliore) a -20 (valore peggiore). Il range "ottimale" si aggira dai -6 ai -9 e quello di "carico" dai -10 ai -13/-14.
* **NETWORK\_ACCESS\_TECNHOLOGY:** tipo di tecnologia utilizzata (_GSM_, _LTE_, _WI-FI_, ecc...) di tipo `hal_net_access_tech_t`
* **NETWORK\_CELLULAR\_CELL\_GLOBAL\_IDENTITY\_MOBILE\_COUNTRY\_CODE:** valore decimale che identifica la nazionalità dell'operatore all'interno della rete PLMN (Public Land Mobile Network) GSM. Per l'Italia questo codice è 222.
* **NETWORK\_CELLULAR\_CELL\_GLOBAL\_IDENTITY\_MOBILE\_NETWORK\_CODE:** valore decimale che identifica univocamente l'operatore all'interno della PLMN nazionale. In Italia per esempio, vale 01 per Tim, 10 per Vodafone, 88 per Wind.
* **NETWORK\_CELLULAR\_CELL\_GLOBAL\_IDENTITY\_LOCATION\_AREA\_CODE:** valore che identifica univocamente una Location Area entro una PLMN. La grandezza di quest'area non è fissa, ma dipende da come è progettata la rete.
* **NETWORK\_CELLULAR\_CELL\_GLOBAL\_IDENTITY\_CELL\_ID:** valore che identifica la cella attualmente agganciata. Questo numero dipende dal gestore e dall'area.
* **NETWORK\_MAC\_ADDRESS\_OUI**: Restituisce il primo componente dell'indirizzo MAC (Organizationally Unique Identifier) associato al dispositivo.
* **NETWORK\_MAC\_ADDRESS\_NIC**: Restituisce il secondo componente dell'indirizzo MAC (Network Interface Card) associato al dispositivo.

La diaagnostica del dispositivo può essere inviata al Cloud in modo automatico o manuale. \
Per configurare l'invio automatico è necessario definire l'intervallo di tempo, in millisecondi, tra due invii consecutivi attraverso la funzione `setPublishHealthCheckInterval(uint32_t interval)`.

L'invio manuale avviene chiamando funzione `publishHealthCheck().`

{% tabs %}
{% tab title="C" %}
```c
// invia la diagnostica ogni minuto
trackleSetPublishHealthCheckInterval(trackle_s, 60*1000);

// invia la diagnostica
tracklePublishHealthCheck(trackle_s);
```
{% endtab %}

{% tab title="C++" %}
```cpp
// invia la diagnostica ogni minuto
Trackle.setPublishHealthCheckInterval(60*1000);

// invia la diagnostica
Trackle.publishHealthCheck();
```
{% endtab %}
{% endtabs %}

