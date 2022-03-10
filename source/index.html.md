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

# Wstęp

<aside class="warning">
UWAGA! API jest dostępne w wersji Sandbox - używane jest środowisko testowe platformy P1. Trwają prace nad udostępnieniem wersji Produkcyjnej.
</aside>

Witaj w dokumentacji [Proste Api NFZ](https://proste-api-nfz.com). Znajdziesz tu informacje jak korzystać z naszego API.

API które udostępnia NFZ to SOAP api i jest nieporęczne. Dlatego tworzymy nasze API abyś mógł wygodnie korzystać z systemu P1 NFZ np. wystawiając prawdziwą ereceptę. Dostępne moduły to e-Recepta, Ewus, Rejestru Leków.

## Wymagania

Aby móc korzystac z API musisz:

1. Założyć konto na [Proste Api NFZ](https://proste-api-nfz.com) . I zalogować się.
2. W zakładze API Tokeny wygenerować nowy TOKEN.

# Autoryzacja

>Zapisz swój API token wygenerowany w zakładce API Tokeny do zmiennej środowiskowej w terminalu.

```shell
TOKEN=twoj_api_token
```

>Aby sprawdzić czy poprawnie łączysz się z API skopiuj podane polecenie curl do terminala.

```shell
curl -X POST \
  -H "Authorization: ${TOKEN}" \
  https://proste-api-nfz.com/api/v1/ja

```

> Powinieneś otrzymać odpowiedź podobną do:

```json
{
  "srodowisko":"sandbox",
  "nazwa_firmy":"Twoja Firma",
  "typ_konta":"normalne_konto",
  "uzywane_certyfikaty":"wspolne"
}
```

> Brawo! Połączyłeś się z API.

# API e-Recepta

## Opis

Kluczową sprawą dla korzystania z tego modułu jest zrozumienie pojęć **pakiet recept** i **recepta**. **Recepta** = 1 lek. A grupa 1 do 5 recept może występowac razem w pakiecie recept. Czyli erecepta z języka potocznego to właśnie pakiet recept, a każdy lek w pakiecie recept to recepta.

## Zapis Pakietu Recept

Wystawienie erecepty odbywa się własnie za pomocą zapisu pakietu recept.

Ten endpoint zapisuje również w naszej bazie danych 4 cyfrowy kod pakietu recept

```shell
KONTEKST='{"id_biznesu_root":"2.16.840.1.113883.3.4424.2.3.1","id_biznesu_ext":"000000927012","id_uzytkownika_root":"2.16.840.1.113883.3.4424.1.6.2","id_uzytkownika_ext":"2179350","id_miejsca_pracy_root":"2.16.840.1.113883.3.4424.2.3.2","id_miejsca_pracy_ext":"4","rola_biznesowa":"LEKARZ_LEK_DENTYSTA_FELCZER"}'

OGOLNE='{"wezel_oid_uslugodawcy":"2.16.840.1.113883.3.4424.2.7.703","miasto_biznesu":"Warszawa","ulica_biznesu":"ul. Dubois","numer_domu_biznesu":"5A","regon_14_biznesu":"37442179500003","pesel_pacjenta":"70032816894","plec_pacjenta":"M","data_urodzenia_pacjenta":"1970-03-28","imie_pacjenta":"Anna","kraj_pacjenta":"Polska","kod_pocztowy_pacjenta":"03-134","miasto_pacjenta":"Warszawa","ulica_pacjenta":"Odkryta","numer_domu_pacjenta":"41","numer_mieszkania_pacjenta":"12","nazwisko_pacjenta":"Kasina","imie_lekarza":"Adam","nazwisko_lekarza":"Kowalski","nazwa_biznesu":"Poradnia POZ","numer_telefonu_biznesu":"22-1111123","kod_pocztowy_biznesu":"00-184"}'

RECEPTA_1='{"jak_dlugo_nalezy_lek_podawac_od":"2019-10-01","jak_dlugo_nalezy_lek_podawac_do":"2019-10-14","co_ile_godzin_podawac_lek":"12","ilosc_opakowan":"1.5","nie_zmieniac":true,"poziom_odplatnosci":"pelnoplatne","gtin_opakowania":"05909997196053","numer_zbioru_wersji_root":"2.16.840.1.113883.3.4424.2.7.703.2.2"}'

PARAMS='{"kontekst":'${KONTEKST}',"ogolne":'${OGOLNE}',"recepty":['${RECEPTA_1}']}'

curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d "${PARAMS}" \
  https://proste-api-nfz.com/api/v1/erecepta/zapis_pakietu_recept
```

> Powyższa komenda zwraca JSON podobny do:

```json
{
  "klucz_pakietu_recept":"KLUCZ_PAKIETU_RECEPT",
  "kod_pakietu_recept":"KOD_PAKIETU_RECEPT",
  "recepty":[
    {
      "kod_4_cyfrowy":"KOD_4_CYFROWY", # Na środowisku Sandbox to będzie zawsze 0000
      "klucz_recepty":"KLUCZ_RECEPTY"
    }
  ]
}
```

## Odczyt Pakietu Recept

Ten endpoint zapisuje również w naszej bazie danych aktualne dane o pakiecie recept i jego receptach.

```shell
KONTEKST='{"id_biznesu_root":"2.16.840.1.113883.3.4424.2.3.1","id_biznesu_ext":"000000927012","id_uzytkownika_root":"2.16.840.1.113883.3.4424.1.6.2","id_uzytkownika_ext":"2179350","id_miejsca_pracy_root":"2.16.840.1.113883.3.4424.2.3.2","id_miejsca_pracy_ext":"4","rola_biznesowa":"LEKARZ_LEK_DENTYSTA_FELCZER"}'

PARAMS='{"kontekst":'${KONTEKST}',"klucz_pakietu_recept":"KLUCZ_PAKIETU_RECEPT"}'

curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d "${PARAMS}" \
  https://proste-api-nfz.com/api/v1/erecepta/odczyt_pakietu_recept
```

> Powyższa komenda zwraca JSON podobny do:

```json
{
  "recepty":[
    {
      "status":"WYSTAWIONA", # Lub ANULOWANA
      "klucz_recepty":"KLUCZ_RECEPTY"
    }
  ]
}
```

## Pakiet Recept Html

Tuż przed wywołaniem tego endpointu należy wywołać Odczyt Pakietu Recept. Aby odświeżyć dane.

```shell
curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d '{"klucz_pakietu_recept":"KLUCZ_PAKIETU_RECEPT"}' \
  -o pakiet_recept.html \
  https://proste-api-nfz.com/api/v1/erecepta/pakiet_recept_html

firefox pakiet_recept.html
```

> Powyższa komenda curl zapisuje html do pliku.

# API Rejestr Leków

## Wyszukanie produktu leczniczego

```shell
curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d '{"q":"bact"}' \
  https://proste-api-nfz.com/api/v1/rejestr_lekow/wyszukaj_produkt_leczniczy
```

# API Ewus

Za pomocą integracji z Ewuś można sprawdzić, czy dana osoba jest ubezpieczona.

## Osoba ubezpieczona

```shell
curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d '{"pesel":"79060804378"}' \
  https://proste-api-nfz.com/api/v1/ewus/check
```

## Osoba nieubezpieczona

```shell
curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d '{"pesel":"55021562501"}' \
  https://proste-api-nfz.com/api/v1/ewus/check
```

## Niepoprawny pesel

```shell
curl -X POST \
  -H 'Content-Type: application/json' \
  -H "Authorization: ${TOKEN}" \
  -d '{"pesel":"niepoprawny"}' \
  https://proste-api-nfz.com/api/v1/ewus/check
```
