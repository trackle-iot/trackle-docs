# Client OAuth

Un Client OAuth solitamente rappresenta un App o un applicativo di terze parti a cui si vuol dare il permesso di controllare dei dispositivi o un Prodotto. Ognuno può creare i propri client. E' buona norma creare un client per ogni applicazione web e mobile che vuole fare richieste alle API REST.

{% hint style="danger" %}
**NON esporre mai il client secret al browser.**  Se, per esempio, hai un client che controlla un Prodotto e usi il client secret nel tuo front-end Javascript, un cliente finale che capisce qualcosa di programmazione potrebbe leggere il token attraverso gli strumenti per sviluppatori e utilizzarlo per avere accesso a tutti i dispositivi di quel prodotto.
{% endhint %}

Gli endpoint di Client OAuth possono essere usati anche come endpoint di Client OAuth per Prodotto sostituendo `/v1/clients` con `/v1/products/:productIdOrSlug/clients`.

## Lista dei Client OAuth

<mark style="color:blue;">`GET`</mark> `https://api.trackle.io/v1/clients`

Ottieni una lista dei Client OAuth generati dall'utente autenticato oppure associati ad certo Prodotto

#### Path Parameters

| Name            | Type   | Description                                      |
| --------------- | ------ | ------------------------------------------------ |
| productIdOrSlug | string | ID Prodotto o Slug. _Solo per endpoint Prodotto_ |

{% tabs %}
{% tab title="200 " %}
```
[
  {
    "id": "aXTUYF51s73TTEh1p4UBWd4k3YM2n6zR",
    "name": "myapp-0c27",
    "org_id": "5c23bd281402995be8561342",
    "scope": "create:customers",
    "secret": "mOfcyM4XaT2RZT4dC-IVBLZnvXpw2seA-QkdKLvGHv_WGBolbs_a7xx2Nz3YSMms",
    "type": "installed"
  }

```
{% endtab %}
{% endtabs %}
