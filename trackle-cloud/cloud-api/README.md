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

# Cloud API

Le API Cloud di Trackle sono di tipo [REST](http://en.wikipedia.org/wiki/Representational\_State\_Transfer). REST significa che usiamo l'URL nel modo per cui è stato creato ovvero come "Uniform Resource Locator".&#x20;

La risorsa in questione è il **Dispositivo**. Ogni dispositivo diventa un endpoint, che può essere usato per fare una richiesta `GET` e ottenere un valore, o una `POST` per chiamare una funzione remota o una `PUT` per aggiornare il firmware.&#x20;

Tutte le richieste ai dispositivi passano attraverso il nostro API Gateway con sicurezza **TLS**.

```bash
INDIRIZZO E PROTOCOLLO
"https://api.trackle.io"
```

{% hint style="info" %}
Quando scriviamo un nome preceduto da `:`, quel nome deve essere sostituito con un informazione di tua proprietà. Per esempio quando trovi un URL come `/v1/devices/:deviceId` devi sostituirlo con qualcosa tipo `/v1/devices/e00fce68166c3bbe15df4c6b`
{% endhint %}

## Formato

Le API REST accettano richieste in [JSON](https://www.w3schools.com/js/js\_json\_intro.asp) (content type `application/json`) e in [form encoded format](https://en.wikipedia.org/wiki/POST\_\(HTTP\)) (content type `application/x-www-form-urlencoded`). Rispondono sempre con un JSON (content type `application/json`).

```aspnet
# Esempio in form encoded format
curl https://api.trackle.io/v1/devices/e00fce68166c3bbe15df4c6b/action \
     -d args=open \
     -d access_token=...

# Esempio con JSON
curl "https://api.trackle.io/v1/devices/e00fce68166c3bbe15df4c6b/action?access_token=..." \
     -H "Content-Type: application/json" \
     -d "{\"args\": \"open\"}"
```

In questa documentazione trovi esempi di chiamate alle API utilizzando un programma da terminale chiamato [curl](https://curl.haxx.se/) che dovrebbe essere già installato sul tuo PC.

Gli esempi usano _form encoded data_ cosi da essere più semplici da leggere e da capire ma tutti gli endpoint accettano anche oggetti JSON come parametri.

## Errori

Le API Cloud di Trackle usano i codici di risposta HTTP convenzionali per indicare il successo o il fallimento di una richiesta alle API.  In generale: i codici `2xx` indicano successo. I codici `4xx`indicano un errore che riguarda le informazioni fornite (es., un parametro richiesto è stato omesso, etc.).
