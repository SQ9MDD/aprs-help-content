---

title: Jak dodać treść do APRS.help
description: Jak przygotować nowy artykuł i przesłać go do repozytorium APRS.help.
template: doc
tableOfContents: true
---------------------

APRS.help jest projektem otwartym i każdy może pomóc w rozbudowie bazy wiedzy.

Treści prezentowane na stronie są przechowywane w publicznym repozytorium GitHub:

`https://github.com/SQ9MDD/aprs-help-content`

Strona APRS.help synchronizuje swoją zawartość z tym repozytorium, dlatego dodanie lub poprawienie artykułu odbywa się poprzez zmianę odpowiednich plików Markdown i przesłanie zmian jako **Pull Request**.

## Wymagane wersje językowe

Każda nowa treść musi zostać przygotowana jednocześnie w czterech wersjach językowych:

* polskiej
* angielskiej
* hiszpańskiej
* niemieckiej

Dla jednego artykułu należy utworzyć cztery odpowiadające sobie pliki:

```text
nazwa-artykulu.pl.md
nazwa-artykulu.en.md
nazwa-artykulu.es.md
nazwa-artykulu.de.md
```

Przykład:

```text
digipeater.pl.md
digipeater.en.md
digipeater.es.md
digipeater.de.md
```

Nazwy plików powinny być identyczne i różnić się jedynie kodem języka.

Pull Request zawierający nowy artykuł powinien obejmować wszystkie cztery wersje językowe.

Brak którejkolwiek z wymaganych wersji językowych oznacza, że artykuł jest niekompletny i nie powinien zostać połączony z głównym repozytorium.

## Format dokumentów

Dokumenty przygotowujemy w formacie **Markdown** (`.md`).

Na początku każdego pliku powinny znaleźć się podstawowe informacje opisujące dokument:

```yaml
---
title: Tytuł artykułu
description: Krótki opis zawartości artykułu.
template: doc
tableOfContents: true
---
```

Pola `title` i `description` powinny być przetłumaczone odpowiednio dla każdej wersji językowej. Tytuł strony jest wyświetlany wyłącznie z pola `title`, dlatego na początku treści artykułu nie należy dodawać drugiego nagłówka poziomu 1 (`# Tytuł`).

Przykład polskiej wersji:

```markdown
---
title: Digipeater APRS
description: Podstawowe informacje o działaniu digipeaterów w sieci APRS.
template: doc
tableOfContents: true
---

Digipeater jest stacją retransmitującą pakiety APRS odebrane drogą radiową.

## Jak działa digipeater

Treść artykułu...
```

Odpowiadające sobie pliki w innych językach powinny zachowywać tę samą strukturę dokumentu.

## Gdzie umieścić pliki

Wszystkie cztery wersje językowe należy umieścić w tym samym katalogu odpowiadającym tematyce artykułu.

Przykład:

```text
4. sprzęt/
  modem-kiss.pl.md
  modem-kiss.en.md
  modem-kiss.es.md
  modem-kiss.de.md
```

Jeżeli nie masz pewności, do której sekcji pasuje nowy artykuł, wybierz najbardziej zbliżoną tematycznie. Lokalizacja może zostać skorygowana podczas przeglądu Pull Requesta.

## Obrazki, schematy i zrzuty ekranu

Grafiki związane z artykułem umieszczaj w katalogu `_img/` obok plików Markdown. Katalogi zaczynające się od `_` nie są wyświetlane w menu dokumentacji.

```text
4. sprzęt/
  modem-kiss.pl.md
  modem-kiss.en.md
  modem-kiss.es.md
  modem-kiss.de.md
  _img/
    modem-kiss-podlaczenie.png
```

W artykule użyj względnej ścieżki Markdown:

```markdown
![Schemat podłączenia modemu KISS](./_img/modem-kiss-podlaczenie.png)
```

Ten sam plik graficzny może być użyty we wszystkich wersjach językowych, ale tekst alternatywny w nawiasach kwadratowych należy przetłumaczyć. Używaj opisowych nazw plików; dla zdjęć i zrzutów ekranu wybieraj PNG, JPEG lub WebP, a dla prostych schematów — SVG, gdy jest dostępny.

## Jak przesłać nową treść

Najwygodniejszą metodą jest standardowy mechanizm GitHub:

1. Utwórz fork repozytorium `SQ9MDD/aprs-help-content`.
2. Przygotuj artykuł w formacie Markdown.
3. Utwórz cztery wersje językowe: `pl`, `en`, `es` i `de`.
4. Umieść wszystkie pliki w odpowiednim katalogu.
5. Zatwierdź zmiany w swoim repozytorium.
6. Utwórz **Pull Request** do głównego repozytorium APRS.help.
7. Krótko opisz, czego dotyczy dodana lub zmieniona treść.

Po przejrzeniu i zaakceptowaniu Pull Requesta zmiany zostaną połączone z głównym repozytorium.

Serwer APRS.help okresowo synchronizuje zawartość z repozytorium, dlatego zaakceptowana treść pojawi się następnie na stronie.

## Poprawki istniejących artykułów

Pull Request może również dotyczyć istniejącej treści, na przykład:

* poprawienia błędu technicznego,
* doprecyzowania opisu,
* dodania przykładu,
* poprawienia literówki,
* aktualizacji nieaktualnych informacji,
* rozszerzenia artykułu.

Jeżeli zmiana wpływa na znaczenie treści, należy zaktualizować wszystkie wersje językowe danego dokumentu.

## Kilka zasad

Przygotowując treść:

* pisz jasno i rzeczowo,
* rozdzielaj większe zagadnienia nagłówkami,
* używaj bloków kodu dla ramek APRS, konfiguracji i poleceń,
* zachowuj podobną strukturę we wszystkich wersjach językowych,
* tłumacz również tytuły, opisy i nagłówki,
* jeśli opisujesz zachowanie protokołu, warto wskazać źródło lub dokumentację,
* unikaj kopiowania dużych fragmentów cudzych materiałów,
* w przypadku tematów dyskusyjnych oddzielaj wymagania protokołu od dobrych praktyk i własnych doświadczeń.

APRS.help ma być wspólnym, praktycznym i technicznym źródłem wiedzy o APRS, rozwijanym przez społeczność krótkofalarską.

## Nie znasz GitHub?

To nie problem.

Do prostych zmian można wykorzystać edytor GitHub dostępny bezpośrednio w przeglądarce. Nie jest konieczne instalowanie Gita ani dodatkowego oprogramowania.

Najważniejsze jest przygotowanie wartościowej treści i wszystkich wymaganych wersji językowych. Mechanizm Pull Request pozwala przed publikacją wspólnie przejrzeć zmiany i w razie potrzeby je poprawić.
