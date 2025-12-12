# Dispositivi

I **Dispositivi** sono associati al tuo account e possono essere parte di un **Prodotto**. Un Prodotto identifica un gruppo di dispositivi con lo stesso hardware e le stesse funzionalità. Ogni Prodotto ha la sua flotta di dispositivi associati.

Gli endpoint per i dispositivi possono essere usati anche per i dispositivi che sono parte di un prodotto sostituendo `/v1/devices` con `/v1/products/:productIdOrSlug/devices`.

## Lista dispositivi

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/devices`

Ottieni la lista dei dispositivi a cui l'utente autenticato ha accesso. Di default, la lista è ordinata per `last_heard` in ordine decrescente.

{% tabs %}
{% tab title="200 " %}
```
[
  {
    "cellular": true,
    "connected": true,
    "firmware_product_id": 200,
    "firmware_updates_enabled": true,
    "firmware_updates_forced": true,
    "firmware_version": 4,
    "functions": [
      "led",
    ],
    "iccid": "8944502009199676656",
    "id": "e00fce68c6ea8328e3e3b0c7",
    "imei": "357520078513989",
    "last_heard": "2020-02-17T18:00:00.185Z",
    "last_ip_address": "5.35.166.154",
    "name": "Lamp",
    "platform_id": 13,
    "product_id": 200,
    "status": "normal",
    "subscriptions": [],
    "system_firmware_version": "1406",
    "variables": {
      "ledStatus": "int32"
    }
  }
]
```
{% endtab %}
{% endtabs %}

## Lista dispositivi associati ad un prodotto

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/products/:productIdOrSlug/devices`

Ottieni la lista dei dispositivi che sono parte di un Prodotto.

#### Path Parameters

| Name            | Type   | Description        |
| --------------- | ------ | ------------------ |
| productIdOrSlug | string | ID prodotto o Slug |

#### Query Parameters

| Name   | Type   | Description                                                                                                                 |
| ------ | ------ | --------------------------------------------------------------------------------------------------------------------------- |
| groups | string | Lista di nomi di gruppo separati da virgola per filtrare i risultati mostrando solo i dispostivi appartenenti a quei gruppi |

{% tabs %}
{% tab title="200 " %}
```
{
  "customers": [],
  "devices": [
    {
      "cellular": false,
      "connected": true,
      "desired_firmware_version": null,
      "firmware_product_id": 100,
      "firmware_updates_enabled": true,
      "firmware_updates_forced": false,
      "firmware_version": 3,
      "functions": [
        "action"
      ],
      "groups": ["bergamo", "milano"],
      "id": "0fd152b041ec15f00cd6ab96",
      "last_heard": "2020-02-18T13:30:23.229Z",
      "last_ip_address": "78.6.28.126",
      "name": "Fancoil",
      "owner": null,
      "plan": "SF2019",
      "product_id": 100,
      "status": "normal",
      "user_id": "5a0e06b7a9829d48f6e2fb3b",
      "variables": {
        "temp": "int32",
        "status": "boolean"
      }
    } 
  ]
}
```
{% endtab %}

{% tab title="404 " %}
```
{
  "error": "Product not found",
  "ok": false
}
```
{% endtab %}
{% endtabs %}

## Ottieni informazioni di un dispositivo

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/devices/:deviceID`

Ottieni le informazioni di un singolo dispositivo, incluse le variabili e le funzioni esposte.

#### Path Parameters

| Name            | Type   | Description                                      |
| --------------- | ------ | ------------------------------------------------ |
| deviceID        | string | ID Dispositivo                                   |
| productIdOrSlug | string | ID Prodotto o Slug. _Solo per endpoint Prodotto_ |

{% tabs %}
{% tab title="200 " %}
```
{
  "cellular": true,
  "connected": true,
  "firmware_product_id": 200,
  "firmware_updates_enabled": true,
  "firmware_updates_forced": false,
  "firmware_version": 4,
  "functions": [
    "led",
  ],
  "groups": [], // Product endpoint only
  "iccid": "8944502009199676656",
  "id": "e00fce68c6ea8328e3e3b0c7",
  "imei": "357520078513989",
  "last_heard": "2020-02-17T18:00:00.185Z",
  "last_ip_address": "5.35.166.154",
  "name": "Lamp",
  "platform_id": 13,
  "product_id": 200,
  "status": "normal",
  "subscriptions": [],
  "system_firmware_version": "1406",
  "variables": {
    "ledStatus": "int32"
  }
}
```
{% endtab %}

{% tab title="404 " %}
```
{
  "error": "No device found",
  "ok": false
}
```
{% endtab %}
{% endtabs %}

## Ottieni il valore di una variabile

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/devices/:deviceID/:variableName`

Ottieni il valore corrente di una variabile esposta dal dispsitivo. Le variabili possono essere lette da un dispositivo di cui l'utente è proprietario oppure da uno che è parte di un Prodotto di cui l'utente è nel team.

#### Path Parameters

| Name            | Type   | Description                                      |
| --------------- | ------ | ------------------------------------------------ |
| deviceID        | string | ID Dispositivo                                   |
| variableName    | string | Nome variabile                                   |
| productIdOrSlug | string | ID Prodotto o Slug. _Solo per endpoint Prodotto_ |

{% tabs %}
{% tab title="200 " %}
```
{
  "id": "e00fce68c6ea8328e3e3b0c7",
  "name": "ledStatus",
  "result": 1
}
```
{% endtab %}

{% tab title="404 " %}
```
{
  "error": "Variable not found",
  "ok": false
}
```
{% endtab %}
{% endtabs %}

## Chiama una funzione remota

<mark style="color:green;">`POST`</mark> `https://api.trackle.io/v1/devices/:deviceID/:functionName`

Chiama una funzione remota esposta dal dispositivo passando un parametro di tipo stringa. Le funzioni possono essere chiamate da un dispositivo di cui l'utente è proprietario oppure da uno che è parte di un Prodotto di cui l'utente è nel team.

#### Path Parameters

| Name            | Type   | Description                                      |
| --------------- | ------ | ------------------------------------------------ |
| deviceID        | string | ID Dispositivo                                   |
| functionName    | string | Nome della funzione                              |
| productIdOrSlug | string | ID Prodotto o Slug. _Solo per endpoint Prodotto_ |

#### Request Body

| Name | Type   | Description                                                     |
| ---- | ------ | --------------------------------------------------------------- |
| args | string | Parametri della funzione con lunghezza massima di 622 caratteri |

{% tabs %}
{% tab title="200 " %}
```
{
  "id": "e00fce68c6ea8328e3e3b0c7",
  "name": "led",
  "return_value": 1
}
```
{% endtab %}

{% tab title="404 " %}
```
{
  "error": "Function not found",
  "ok": false
}
```
{% endtab %}
{% endtabs %}

## Pinga un dispositivo

<mark style="color:orange;">`PUT`</mark> `https://api.trackle.io/v1/devices/:deviceID/ping`

#### Path Parameters

| Name            | Type   | Description                                       |
| --------------- | ------ | ------------------------------------------------- |
| deviceID        | string | ID Dispositivo                                    |
| productIdOrSlug | string | ID Prodotto or Slug. _Solo per endpoint Prodotto_ |

{% tabs %}
{% tab title="200 " %}
```
{
  "lastHeard": "2020-02-18T10:28:38.299Z",
  "online": true
}
```
{% endtab %}

{% tab title="400 " %}
```
{
  "error": "Could not get device for ID. Probably is not connected.",
  "ok": false
}
```
{% endtab %}
{% endtabs %}

## Genera un codice di claim

<mark style="color:green;">`POST`</mark> `https://api.trackle.io/v1/device_claims`

Genera un codice di claim che permette di ad un utente di Trackle di diventare proprietario di un dispositivo e quindi avere il permesso di monitorarlo e controllarlo. Usando l'endpoint di Prodotto è possibile generare un codice di claim per un dispositivo parte di un Prodotto. Il codice deve essere generato usando l'access token dell'utente che vuole diventare proprietario del dispositivo.

#### Path Parameters

| Name            | Type   | Description                                    |
| --------------- | ------ | ---------------------------------------------- |
| productIdOrSlug | string | ID Prodotto o Slug. Solo per endpoint Prodotto |

{% tabs %}
{% tab title="200 " %}
```
{
    "claim_code":"AAG9+vGe430aUxwQAx8xji/RQspya87h+qZSBikjpo6uGVl/lxH9xR+a3bXZNAx",
    "device_ids":[]
}
```
{% endtab %}
{% endtabs %}
