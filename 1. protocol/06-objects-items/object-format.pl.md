---
title: Obiekty APRS
description: Sposób prezentowania nazwanego obiektu na mapie APRS.
---

Obiekt APRS to nazwany raport mapowy wysyłany przez stację w imieniu czegoś innego: przemiennika, czujnika pogody, punktu wydarzenia, pojazdu lub innego zasobu. Wykorzystuje dane pozycji takie jak zwykły raport pozycyjny, poprzedzone dziewięcioznakową nazwą.

## Podstawowa postać

```text
;OBJECT___*DDHHMMzDDMM.hhN/DDDMM.hhW$komentarz
```

- `;` oznacza raport obiektu.
- `OBJECT___` to stała, dziewięcioznakowa nazwa; krótsze nazwy uzupełnia się spacjami.
- `*` oznacza obiekt aktywny.
- Pozostała część pozycji podlega zwykłym zasadom APRS.

W nazwach rozróżniana jest wielkość liter. Wybierz czytelną, stałą nazwę, aby odbiorniki rozpoznały kolejne raporty jako aktualizację tego samego obiektu.

## Aktualizacja i usuwanie

Wyślij nowy raport z tą samą nazwą, aby zmienić pozycję lub komentarz. Obiekt można oznaczyć jako usunięty, zastępując znacznik aktywności znakiem `_`. Odbiorniki powinny przestać go wyświetlać, zachowując raport w historii, jeśli ich oprogramowanie to obsługuje.

Obiekt powinna usuwać wyłącznie stacja, która za niego odpowiada. Ogranicza to ryzyko konfliktu raportów, przez który aktywny obiekt znika i pojawia się na przemian.

## Obiekt czy item?

Wybierz **obiekt**, gdy nazwa ma dokładnie dziewięć znaków i przydatny jest znacznik czasu. APRS **item** ma nazwę o zmiennej długości i nie zawiera czasu, dlatego pasuje do prostszych, krótkotrwałych raportów.

## Źródła

- [PROTOCOL.TXT — format, zastępowanie i usuwanie obiektów](/APRS-SPEC/PROTOCOL.TXT)
- [Objects.txt — działanie i prezentacja obiektów](/APRS-SPEC/Objects.txt)
