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

# Eventi

I **Dispositivi** inviano eventi al Cloud ogni volta che si connettono e ogni volta che viene chiamato `Trackle.publish()`. E' possibile ricevere gli eventi dei dispositivi attraverso una tecnologia che sfrutta un flusso di dati HTTP chiamata [Server-Sent Events (SSEs)](https://www.w3.org/TR/eventsource/).

{% hint style="info" %}
Puoi filtrare gli eventi da ricevere specificando un _eventName._ Gli eventi ricevuti saranno limitati a quelli con il filtro specificato. Per esempio specificando il nome evento `temp` saranno ricevuti tutti gli eventi il cui nome inizia con `temp`.
{% endhint %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/events/[:eventName]" method="get" summary="Ottieni un flusso di eventi pubblici" %}
{% swagger-description %}
Open a stream of Server Sent Events for all public events.
{% endswagger-description %}

{% swagger-parameter in="path" name="eventPrefix" type="string" %}
Filters the stream to only events starting with the specified prefix. Omit to get all events.
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef0123456
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/events/[:eventName]" method="get" summary="Ottieni un flusso di eventi dei tuoi dispositivi" %}
{% swagger-description %}
Open a stream of Server Sent Events for all public and private events for your devices.
{% endswagger-description %}

{% swagger-parameter in="path" name="eventPrefix" type="string" %}
Filters the stream to only events starting with the specified prefix. Omit to get all events.
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef0123456
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/:deviceID/events/[:eventName]" method="get" summary="Ottieni un flusso di eventi per un dispositivo" %}
{% swagger-description %}
Open a stream of Server Sent Events for all public and private events for the specified device.
{% endswagger-description %}

{% swagger-parameter in="path" name="deviceID" type="string" %}
Device ID
{% endswagger-parameter %}

{% swagger-parameter in="path" name="eventPrefix" type="string" %}
Filters the stream to only events starting with the specified prefix. Omit to get all events.
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef01234567"}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/products/:productIdOrSlug/devices/events/[:eventName]" method="get" summary="Ottieni un flusso di eventi di un prodotto" %}
{% swagger-description %}
Open a stream of Server Sent Events for all public and private events for a product.
{% endswagger-description %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
Product ID or Slug
{% endswagger-parameter %}

{% swagger-parameter in="path" name="eventPrefix" type="string" %}
Filters the stream to only events starting with the specified prefix. Omit to get all events
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef01234567"}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/products/:productIdOrSlug/devices/:deviceID/events/[:eventName]" method="get" summary="Ottieni un flusso di eventi di un dispositivo parte di un prodotto" %}
{% swagger-description %}
Open a stream of Server Sent Events scoped to a particular device in a product
{% endswagger-description %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
Product ID or Slug
{% endswagger-parameter %}

{% swagger-parameter in="path" name="deviceID" type="string" %}
Device ID
{% endswagger-parameter %}

{% swagger-parameter in="path" name="eventPrefix" type="string" %}
Filters the stream to only events starting with the specified prefix. Omit to get all events
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef01234567"}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/devices/events" method="post" summary="Pubblica un evento" %}
{% swagger-description %}
Publish an event to your devices stream.
{% endswagger-description %}

{% swagger-parameter in="body" name="name" type="string" %}
Event name
{% endswagger-parameter %}

{% swagger-parameter in="body" name="data" type="string" %}
Event data. Limited to a maximum of 622 bytes
{% endswagger-parameter %}

{% swagger-parameter in="body" name="private" type="boolean" %}
Private or public
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "ok": true
}
```
{% endswagger-response %}

{% swagger-response status="400" description="" %}
```
{
  "error": "name not provided",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://api.trackle.io" path="/v1/products/:productIdOrSlug/events" method="post" summary="Pubblica un evento per un prodotto" %}
{% swagger-description %}
Publish an event that is sent to the product's event stream.
{% endswagger-description %}

{% swagger-parameter in="path" name="productIdOrSlug" type="string" %}
Product ID or Slug
{% endswagger-parameter %}

{% swagger-parameter in="body" name="name" type="string" %}
Event name
{% endswagger-parameter %}

{% swagger-parameter in="body" name="data" type="string" %}
Event data. Limited to a maximum of 622 bytes
{% endswagger-parameter %}

{% swagger-parameter in="body" name="private" type="boolean" %}
Private or public
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "ok": true
}
```
{% endswagger-response %}

{% swagger-response status="400" description="" %}
```
{
  "error": "name not provided",
  "ok": false
}
```
{% endswagger-response %}

{% swagger-response status="404" description="" %}
```
{
  "error": "Product does not exist",
  "ok": false
}
```
{% endswagger-response %}
{% endswagger %}
