---
title: Obiekty częstotliwości
description: Obiekty APRS publikujące lokalne częstotliwości radiowe.
---

Obiekty częstotliwości publikują na mapie APRS przydatne lokalne zasoby radiowe: polecany przemiennik głosowy, węzeł EchoLink lub IRLP, bramkę Winlink albo aktywną sieć. Są zwykłymi obiektami APRS ze sformatowaną nazwą lub komentarzem dotyczącym częstotliwości.

## Informacja lokalna

Taki obiekt ma wartość tylko tam, gdzie podróżujący może z niego skorzystać. Nadawaj go oszczędnie i zgodnie z lokalnymi zasadami pracy RF; zbędne retransmisje zwiększają obciążenie wspólnego kanału.

## Częstotliwość w komentarzu

Czytelna forma zawiera częstotliwość i `MHz`, opcjonalnie ton, przesunięcie oraz zasięg:

```text
146.805MHz T107 R25m
```

`T107` opisuje ton 107.x Hz, a `R25m` nominalny zasięg 25 mil. Pełna konwencja określa również CTCSS/DCS, modulację, przesunięcie i kilometry. To zapis dla zgodnych klientów APRS, a nie zamiennik lokalnego planu częstotliwości.

## Częstotliwość jako nazwa obiektu

Dla przemiennika częstotliwość może znaleźć się w nazwie obiektu, a dodatkowe znaki zapewniają jej unikalność. Komentarz przenosi wtedy ton, przesunięcie, zasięg i krótki opis.

```text
;147.105md*111111zDDMM.hhN/DDDMM.hhWrT107 R25m Lokalny przemiennik
```

To wyłącznie przykładowy układ. Rzeczywiste współrzędne, symbol, końcówkę nazwy i harmonogram należy dobrać do danej stacji.

## Usługi specjalne

EchoLink, IRLP, WiRES i Winlink mogą korzystać z tego samego mechanizmu obiektów. Ich konwencje nazewnicze pomagają klientom mobilnym rozpoznać usługę, ale dostępność i stan powinny pochodzić od operatora lub wiarygodnego źródła lokalnego.

## Źródła

- [freqspec.txt — formaty częstotliwości i konwencje obiektów usług](/APRS-SPEC/freqspec.txt)
- [PROTOCOL.TXT — ramka obiektu APRS](/APRS-SPEC/PROTOCOL.TXT)
