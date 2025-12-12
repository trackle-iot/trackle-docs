# Eventi

I **Dispositivi** inviano eventi al Cloud ogni volta che si connettono e ogni volta che viene chiamato `Trackle.publish()`. E' possibile ricevere gli eventi dei dispositivi attraverso una tecnologia che sfrutta un flusso di dati HTTP chiamata [Server-Sent Events (SSEs)](https://www.w3.org/TR/eventsource/).

{% hint style="info" %}
Puoi filtrare gli eventi da ricevere specificando un _eventName._ Gli eventi ricevuti saranno limitati a quelli con il filtro specificato. Per esempio specificando il nome evento `temp` saranno ricevuti tutti gli eventi il cui nome inizia con `temp`.
{% endhint %}

## Ottieni un flusso di eventi pubblici

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/events/[:eventName]`

Open a stream of Server Sent Events for all public events.

#### Path Parameters

| Name        | Type   | Description                                                                                   |
| ----------- | ------ | --------------------------------------------------------------------------------------------- |
| eventPrefix | string | Filters the stream to only events starting with the specified prefix. Omit to get all events. |

{% tabs %}
{% tab title="200 " %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef0123456
```
{% endtab %}
{% endtabs %}

## Ottieni un flusso di eventi dei tuoi dispositivi

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/devices/events/[:eventName]`

Open a stream of Server Sent Events for all public and private events for your devices.

#### Path Parameters

| Name        | Type   | Description                                                                                   |
| ----------- | ------ | --------------------------------------------------------------------------------------------- |
| eventPrefix | string | Filters the stream to only events starting with the specified prefix. Omit to get all events. |

{% tabs %}
{% tab title="200 " %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef0123456
```
{% endtab %}
{% endtabs %}

## Ottieni un flusso di eventi per un dispositivo

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/devices/:deviceID/events/[:eventName]`

Open a stream of Server Sent Events for all public and private events for the specified device.

#### Path Parameters

| Name        | Type   | Description                                                                                   |
| ----------- | ------ | --------------------------------------------------------------------------------------------- |
| deviceID    | string | Device ID                                                                                     |
| eventPrefix | string | Filters the stream to only events starting with the specified prefix. Omit to get all events. |

{% tabs %}
{% tab title="200 " %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef01234567"}
```
{% endtab %}
{% endtabs %}

## Ottieni un flusso di eventi di un prodotto

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/products/:productIdOrSlug/devices/events/[:eventName]`

Open a stream of Server Sent Events for all public and private events for a product.

#### Path Parameters

| Name            | Type   | Description                                                                                  |
| --------------- | ------ | -------------------------------------------------------------------------------------------- |
| productIdOrSlug | string | Product ID or Slug                                                                           |
| eventPrefix     | string | Filters the stream to only events starting with the specified prefix. Omit to get all events |

{% tabs %}
{% tab title="200 " %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef01234567"}
```
{% endtab %}
{% endtabs %}

## Ottieni un flusso di eventi di un dispositivo parte di un prodotto

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/products/:productIdOrSlug/devices/:deviceID/events/[:eventName]`

Open a stream of Server Sent Events scoped to a particular device in a product

#### Path Parameters

| Name            | Type   | Description                                                                                  |
| --------------- | ------ | -------------------------------------------------------------------------------------------- |
| productIdOrSlug | string | Product ID or Slug                                                                           |
| deviceID        | string | Device ID                                                                                    |
| eventPrefix     | string | Filters the stream to only events starting with the specified prefix. Omit to get all events |

{% tabs %}
{% tab title="200 " %}
```
:ok

event: temperature
data: {"data":"25.34","ttl":"60","published_at":"2015-07-18T00:12:18.174Z","coreid":"0123456789abcdef01234567"}
```
{% endtab %}
{% endtabs %}

## Pubblica un evento

<mark style="color:green;">`POST`</mark> `https://api.trackle.io/v1/devices/events`

Publish an event to your devices stream.

#### Request Body

| Name    | Type    | Description                                   |
| ------- | ------- | --------------------------------------------- |
| name    | string  | Event name                                    |
| data    | string  | Event data. Limited to a maximum of 622 bytes |
| private | boolean | Private or public                             |

{% tabs %}
{% tab title="200 " %}
```
{
  "ok": true
}
```
{% endtab %}

{% tab title="400 " %}
```
{
  "error": "name not provided",
  "ok": false
}
```
{% endtab %}
{% endtabs %}

## Pubblica un evento per un prodotto

<mark style="color:green;">`POST`</mark> `https://api.trackle.io/v1/products/:productIdOrSlug/events`

Publish an event that is sent to the product's event stream.

#### Path Parameters

| Name            | Type   | Description        |
| --------------- | ------ | ------------------ |
| productIdOrSlug | string | Product ID or Slug |

#### Request Body

| Name    | Type    | Description                                   |
| ------- | ------- | --------------------------------------------- |
| name    | string  | Event name                                    |
| data    | string  | Event data. Limited to a maximum of 622 bytes |
| private | boolean | Private or public                             |

{% tabs %}
{% tab title="200 " %}
```
{
  "ok": true
}
```
{% endtab %}

{% tab title="400 " %}
```
{
  "error": "name not provided",
  "ok": false
}
```
{% endtab %}

{% tab title="404 " %}
```
{
  "error": "Product does not exist",
  "ok": false
}
```
{% endtab %}
{% endtabs %}
