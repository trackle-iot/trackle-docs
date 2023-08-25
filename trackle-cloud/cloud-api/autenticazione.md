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

# Autenticazione

I permessi per controllare e comunicare con i tuoi dispositivi sono gestiti con [OAuth 2.0](https://oauth.net/2/). Per poter accedere alle risorse protette OAuth 2.0 devi utilizzare gli **Access Token**. Un Access Token è una stringa che rappresenta la concessione del permesso. Le API Cloud di Trackle generano un Access Token nel formato [JSON Web Token (JWT)](https://jwt.io/).‌

Quando connetti un dispositivo al cloud per la prima volta, questo non avrà alcun **proprietario** quindi nessuno, eccetto nel caso di un dispositivo associato ad un Prodotto, avrà il permesso di controllarlo. Per poter associare un dispositivo ad un account e quindi poterlo controllare è necessario associarlo tramite la **procedura di claim**, direttamente dalla Console o tramite un codice di claim. Dopo questa operazione solo quell'account avrà il permesso di controllare il dispositivo con il suo access token.

Il permesso di accedere alle informazioni di un dispositivo può essere concesso anche ad App di terze parti tramite la creazione di speciali [Client OAuth](broken-reference) tramite i quali è possibile generare degli access token con i permessi per il monitoraggio e il controllo del dispositivo.

## Come inviare l'access token nelle richieste alle API <a href="#how-to-send-your-access-token" id="how-to-send-your-access-token"></a>

Ci sono 3 modi per inviare l'access token nelle richieste:

* tramite HTTP Authorization header (funziona per tutte le richieste)
* nell'URL come parametro query (funziona solo con le richieste di tipo GET)
* come request body (funziona solo con POST, PUT e DELETE quando il content-type è form encoded)

Per inviare un header custom usando curl basta aggiungere il flag `-H.` L'access token è chiamato token "Bearer" e viene scritto nell'HTTP `Authorization` header.

```
curl -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCI..." \
  https://...
```

La stringa dei parametri è la parte dell'URL dopo il punto di domanda `?`. Per inviare l'access token come parametro query basta aggiungere `access_token=38bb...`. L'intero URL deve essere racchiuso tra apici doppi, altrimenti curl pensa che il punto di domanda sia un carattere speciale.

```
curl "https://api.trackle.io/v1/devices?access_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCI..."
```

La request body è usata per inviare il contenuto delle form nel web. Usando curl, ogni parametro che viene inviato, incluso l'access token, è preceduto da un flag `-d`. Di default, se si aggiunge un flag `-d`, curl pensa che la richiesta sia una POST. Se si vuole fare una richiesta di un altro tipo è necessario aggiungere il flag `-X`, per esempio `-X PUT`.

```
curl -d access_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCI... \
  https://...
```

{% swagger baseUrl="https://api.trackle.io" path="/oauth/token" method="post" summary="Genera un access token" %}
{% swagger-description %}
Crea un access token che ti da accesso alle API Cloud. Devi inviare un OAuth Client ID e secret validi in HTTP Basic Auth oppure come parametri 

`client_id`

 e 

`client_secret`

. Questo endpoint accetta solo richieste di tipo 

_form encoded_

.
{% endswagger-description %}

{% swagger-parameter in="header" name="Authorization" type="string" required="false" %}
HTTP Basic Auth dove lo username è il OAuth client_id e la password è OAuth client_secret.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="client_id" type="string" required="false" %}
OAuth client_id. Richiesto solo se non si usa Authorization header
{% endswagger-parameter %}

{% swagger-parameter in="body" name="client_secret" type="string" %}
OAuth client_secret. Richiesto solo se non si usa Authorization header
{% endswagger-parameter %}

{% swagger-parameter in="body" name="grant_type" type="string" required="true" %}
OAuth grant type 

`authorization_code`

 o

`client_credentials`
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Ik...",
  "refresh_token": "Ogv4O0c84021LfH7051L9Z2QQVMfpZGh7im2t2zy9xETR",
  "scope": "offline_access",
  "expires_in": 604800,
  "token_type": "Bearer"
}
```
{% endswagger-response %}

{% swagger-response status="400" description="" %}
```
{
  "error": "{\"error\":\"invalid_grant\",\"error_description\":\"Wrong email or password.\"}",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/oauth/token" method="post" summary="Rigenera un access token (refresh)" %}
{% swagger-description %}
Rigenera un access token quando è scaduto.
{% endswagger-description %}

{% swagger-parameter in="header" name="Authorization" type="string" %}
HTTP Basic Auth dove lo username è OAuth client_id e la password è OAuth client_secret. Si può usare 

`iotready:iotready`
{% endswagger-parameter %}

{% swagger-parameter in="body" name="client_id" type="string" %}
OAuth client_id. Richiesto solo se non si usa Authorization header
{% endswagger-parameter %}

{% swagger-parameter in="body" name="client_secret" type="string" %}
OAuth client_secret. Richiesto solo se non si usa Authorization header
{% endswagger-parameter %}

{% swagger-parameter in="body" name="grant_type" type="string" required="true" %}
OAuth grant type. Usare 

`refresh_token`
{% endswagger-parameter %}

{% swagger-parameter in="body" name="refresh_token" type="string" required="true" %}
Il refresh token
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Ik...",
  "scope": "offline_access",
  "expires_in": 604800,
  "token_type": "Bearer"
}
```
{% endswagger-response %}

{% swagger-response status="400" description="" %}
```
{
  "error": "{\"error\":\"invalid_grant\",\"error_description\":\"Unknown or invalid refresh token.\"}",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}
