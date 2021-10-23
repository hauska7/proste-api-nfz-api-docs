---
title: API Docs

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Dokumentacja dla Proste Api NFZ
---

# Introduction

<aside class="warning">
UWAGA! API jeszcze nie jest dostępne. Jest w trakcie developmentu.
</aside>

Witaj w dokumentacji api Proste Api NFZ. Znajdziesz tu informacje jak korzystać z naszego API.

API które udostępnia NFZ to SOAP api i jest nieporęczne. Dlatego tworzymy nasze api abyś mógł wygodnie korzystać z systemu P1 NFZ. Modułu e-Recepta.

Przykłady korzystania z API używają narzędzia curl.

# Authentication

> Aby się autoryzować użyj tego kodu:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

> Upewnij się. ze podmienisz `meowmeowmeow` na Twój token API.

API używa tokenów do autoryzacji. Możesz zarejestrować nowy token w [ustawieniach](#).

API oczekuje, że token będzie zawarty w każdym requeście w nagłówku. Przykład:

`Authorization: meowmeowmeow`

<aside class="notice">
Musisz podmienić <code>meowmeowmeow</code> na twój API token.
</aside>

# API

## Odczyt Recepty

```shell
curl "https://proste-api-nfz.com/api/v1/odczyt_recepty?klucz_recepty=KLUCZ_RECEPTY" \
  -H "Authorization: meowmeowmeow"
```

> Powyższa komenda zwraca JSON podobny do:

```json
{
  "recepta": {
    "status":  "WYSTAWIONA",
    "tresc": "Base64-zakodowana-treść-recepty"
  }
}
```

Ten endpoint zwraca pojedynczą recepte.

### HTTP Request

`GET https://proste-api-nfz.com/api/v1/odczyt_recepty`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
klucz_recepty |  | klucz identyfikujący receptę

## Odczyt Pakietu Recept

```shell
curl "https://proste-api-nfz.com/api/v1/odczyt_pakietu_recept?klucz_pakietu_recept=KLUCZ_PAKIETU_RECEPT" \
  -H "Authorization: meowmeowmeow"
```

> Powyższa komenda zwraca JSON podobny do:

```json
{
  "info_recept": [
    { "status_recepty": "WYSTAWIONA" }, // recepta nr 1
    { "status_recepty": "ANULOWANA" }   // recepta nr 2
  ],
  "tresc_recept": [
    "Base64-zakodowana-tresc-recepty", // recepta nr 1
    "Base64-zakodowana-tresc-recepty"  // recepta nr 2
  ],
}
```

Ten endpoint zwraca jeden pakiet recept.

### HTTP Request

`GET https://proste-api-nfz.com/api/v1/odchyt_pakietu_recept`

### URL Parameters

Parameter | Description
--------- | -----------
klucz_pakietu_recept | Klucz identyfikujący pakiet recept

## Zapis Pakietu Recept

```shell
curl "https://proste-api-nfs.com/api/v1/zapis_pakietu_recept" \
  -X POST \
  -H "Authorization: meowmeowmeow"
```

> Powyższa komenda zwraca JSON podobny do:

   expect(result["klucz_pakietu_recept"]).to be_present
      expect(result["kod_pakietu_recept"]).to be_present
```json
{
  "klucz_pakietu_recept": "KLUCZ_PAKIETU_RECEPT",
  "kod_pakietu_recept": "KOD_PAKIETU_RECEPT"
}
```

Ten endpoint zapisuje pakiet recept

### HTTP Request

`POST https://proste-api-nfz.com/api/v1/zapis_pakietu_recept`

### URL Parameters
