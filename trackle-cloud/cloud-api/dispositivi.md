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

# Dispositivi

I **Dispositivi** sono associati al tuo account e possono essere parte di un **Prodotto**. Un Prodotto identifica un gruppo di dispositivi con lo stesso hardware e le stesse funzionalità. Ogni Prodotto ha la sua flotta di dispositivi associati.

Gli endpoint per i dispositivi possono essere usati anche per i dispositivi che sono parte di un prodotto sostituendo `/v1/devices` con `/v1/products/:productIdOrSlug/devices`.

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices" method="get" summary="Lista dispositivi" %}
{% swagger-description %}
Ottieni la lista dei dispositivi a cui l'utente autenticato ha accesso. Di default, la lista è ordinata per 

`last_heard`

 in ordine decrescente.
{% endswagger-description %}

{% swagger-response status="200" description="" %}
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
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/products/:productIdOrSlug/devices" method="get" summary="Lista dispositivi associati ad un prodotto" %}
{% swagger-description %}
Ottieni la lista dei dispositivi che sono parte di un Prodotto.
{% endswagger-description %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
ID prodotto o Slug
{% endswagger-parameter %}

{% swagger-parameter in="query" name="groups" type="string" %}
Lista di nomi di gruppo separati da virgola per filtrare i risultati mostrando solo i dispostivi appartenenti a quei gruppi
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
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
{% endswagger-response %}

{% swagger-response status="404" description="" %}
```
{
  "error": "Product not found",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/:deviceID" method="get" summary="Ottieni informazioni di un dispositivo" %}
{% swagger-description %}
Ottieni le informazioni di un singolo dispositivo, incluse le variabili e le funzioni esposte.
{% endswagger-description %}

{% swagger-parameter in="path" name="deviceID" type="string" %}
ID Dispositivo
{% endswagger-parameter %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
ID Prodotto o Slug. 

_Solo per endpoint Prodotto_
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
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
{% endswagger-response %}

{% swagger-response status="404" description="" %}
```
{
  "error": "No device found",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/:deviceID/:variableName" method="get" summary="Ottieni il valore di una variabile" %}
{% swagger-description %}
Ottieni il valore corrente di una variabile esposta dal dispsitivo. Le variabili possono essere lette da un dispositivo di cui l'utente è proprietario oppure da uno che è parte di un Prodotto di cui l'utente è nel team.
{% endswagger-description %}

{% swagger-parameter in="path" name="deviceID" type="string" %}
ID Dispositivo
{% endswagger-parameter %}

{% swagger-parameter in="path" name="variableName" type="string" %}
Nome variabile
{% endswagger-parameter %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
ID Prodotto o Slug. 

_Solo per endpoint Prodotto_
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "id": "e00fce68c6ea8328e3e3b0c7",
  "name": "ledStatus",
  "result": 1
}
```
{% endswagger-response %}

{% swagger-response status="404" description="" %}
```
{
  "error": "Variable not found",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/:deviceID/:functionName" method="post" summary="Chiama una funzione remota" %}
{% swagger-description %}
Chiama una funzione remota esposta dal dispositivo passando un parametro di tipo stringa. Le funzioni possono essere chiamate da un dispositivo di cui l'utente è proprietario oppure da uno che è parte di un Prodotto di cui l'utente è nel team.
{% endswagger-description %}

{% swagger-parameter in="path" name="deviceID" type="string" %}
ID Dispositivo
{% endswagger-parameter %}

{% swagger-parameter in="path" name="functionName" type="string" %}
Nome della funzione
{% endswagger-parameter %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
ID Prodotto o Slug. 

_Solo per endpoint Prodotto_
{% endswagger-parameter %}

{% swagger-parameter in="body" name="args" type="string" %}
Parametri della funzione con lunghezza massima di 622 caratteri
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "id": "e00fce68c6ea8328e3e3b0c7",
  "name": "led",
  "return_value": 1
}
```
{% endswagger-response %}

{% swagger-response status="404" description="" %}
```
{
  "error": "Function not found",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/:deviceID/ping" method="put" summary="Pinga un dispositivo" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="path" name="deviceID" type="string" %}
ID Dispositivo
{% endswagger-parameter %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
ID Prodotto or Slug. 

_Solo per endpoint Prodotto_
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "lastHeard": "2020-02-18T10:28:38.299Z",
  "online": true
}
```
{% endswagger-response %}

{% swagger-response status="400" description="" %}
```
{
  "error": "Could not get device for ID. Probably is not connected.",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/device_claims" method="post" summary="Genera un codice di claim" %}
{% swagger-description %}
Genera un codice di claim che permette di ad un utente di Trackle di diventare proprietario di un dispositivo e quindi avere il permesso di monitorarlo e controllarlo. Usando l'endpoint di Prodotto è possibile generare un codice di claim per un dispositivo parte di un Prodotto. Il codice deve essere generato usando l'access token dell'utente che vuole diventare proprietario del dispositivo.
{% endswagger-description %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
ID Prodotto o Slug. Solo per endpoint Prodotto
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "claim_code":"AAG9+vGe430aUxwQAx8xji/RQspya87h+qZSBikjpo6uGVl/lxH9xR+a3bXZNAx",
    "device_ids":[]
}
```
{% endswagger-response %}
{% endswagger %}
