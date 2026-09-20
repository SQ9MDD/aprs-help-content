---
title: Typy danych i DTI
description: Jak zbudowane jest pole Information w APRS, jakie główne typy danych definiuje protokół, czym jest Data Type Identifier i dlaczego DTI nie jest tym samym co typ danych.
template: doc
tableOfContents: true
---

Właściwe dane APRS są przenoszone w polu **Information** ramki AX.25 UI.

W tekstowym zapisie monitorowym, np. TNC2, pole Information jest częścią znajdującą się po separatorze `:`:

```text
SQ9MDD-4>APBOX0,WIDE1-1:!5213.45N/02100.12E...
                            ^^^^^^^^^^^^^^^^^^^^^^
                            pole Information
```

Sam separator `:` nie jest dodatkowym bajtem pola Information. Należy do tekstowej reprezentacji ramki.

APRS nie definiuje jednego uniwersalnego formatu dla całego pola Information. Inaczej zapisuje się pozycję, inaczej wiadomość, pogodę, telemetrię czy obiekt. Dlatego przed interpretacją danych odbiornik musi ustalić, z jakim rodzajem i wariantem danych ma do czynienia.

Podstawą tego mechanizmu jest **Data Type Identifier**, w skrócie **DTI**.

## Ogólny model pola Information

APRS Protocol Reference 1.0.1 opisuje ogólny model pola Information jako:

```text
DTI | APRS Data | APRS Data Extension | Comment
```

Nie oznacza to, że każdy pakiet musi zawierać wszystkie cztery części.

W klasycznym modelu:

- `DTI` ma 1 bajt,
- `APRS Data` ma długość zależną od formatu,
- klasyczne `APRS Data Extension`, jeżeli występuje, ma 7 bajtów,
- `Comment` jest opcjonalnym polem o długości zależnej od danego formatu.

`APRS Data Extension` jest pojęciem z podstawowej specyfikacji i obejmuje między innymi klasyczne rozszerzenia `course/speed`, `PHG`, `RNG` czy sygnał DF. Nie należy jednak traktować tych 7 bajtów jako jedynego miejsca na wszystkie późniejsze rozszerzenia APRS. W kolejnych latach pojawiły się również rozszerzenia kodowane w komentarzu, np. `!DAO!` czy Base91 Comment Telemetry.

Komentarz również nie jest po prostu "resztą tekstu" w każdym możliwym pakiecie. W niektórych formatach może zawierać dalsze dane o ustalonej składni.

## Główne typy danych APRS

APRS Protocol Reference wyróżnia **10 głównych typów danych APRS**. Jest to klasyfikacja semantyczna, czyli odpowiada przede wszystkim na pytanie:

**co dany pakiet przekazuje?**

Nie jest to lista DTI. Jeden typ danych może korzystać z kilku różnych DTI, a niektóre DTI mogą być przeciążone historycznymi znaczeniami.

| Typ danych APRS | Typowe DTI i formaty | Znaczenie |
|---|---|---|
| **Position** | `!`, `=`, `/`, `@`, Mic-E `` ` `` i `'`, surowe NMEA `$` | Pozycja stacji. Cztery podstawowe DTI pozycji rozróżniają timestamp oraz obsługę wiadomości. Mic-E ma własny sposób kodowania, a `$` może przenosić surowe zdania NMEA. |
| **Direction Finding** | zwykle `!`, `=`, `/`, `@`; historycznie także `%` | Standardowy raport DF jest wariantem raportu pozycji z odpowiednim symbolem i danymi DF, np. `/BRG/NRQ`. `%` był używany przez format Agrelo DFJr / MicroFinder. |
| **Objects and Items** | `;` dla Object, `)` dla Item | Opis elementu innego niż stacja źródłowa. Object i Item mają osobne DTI i różnią się składnią. |
| **Weather** | `_`, a także `!`, `=`, `/`, `@`; historycznie `!`, `#`, `$`, `*` dla surowych formatów WX | `_` oznacza raport pogodowy bez pozycji. Pogoda może być również dołączona do raportu pozycji. Stare surowe formaty stacji pogodowych nadal występują w dokumentacji, ale APRS 1.1 nie zaleca ich w nowych implementacjach. |
| **Telemetry** | `T` w klasycznym formacie | Klasyczna telemetria używa pakietów `T#...`. Telemetria może jednak występować także w innych miejscach, np. historycznie w Mic-E lub jako późniejsze Base91 Comment Telemetry w komentarzu raportu pozycji. |
| **Messages, Bulletins and Announcements** | `:` | Wszystkie korzystają z tego samego DTI. O tym, czy dane są zwykłą wiadomością, bulletinem czy announcement, decyduje dalsza składnia pola. |
| **Queries** | `?` dla zapytań ogólnych; `:` dla zapytań kierowanych | Zapytania ogólne rozpoczynają się od `?`. Zapytanie do konkretnej stacji jest kodowane jako wiadomość APRS i dlatego korzysta z DTI `:`. |
| **Responses** | brak jednego własnego DTI | Odpowiedź używa formatu właściwego dla zwracanych danych, np. pozycji, obiektu, pogody, statusu lub wiadomości. Odpowiedź Station Capabilities używa DTI `<`. |
| **Status** | `>` | Jednowierszowy status stacji. Może zawierać zwykły tekst, a w określonych formatach również dodatkowe informacje. |
| **Other** | m.in. `{`, `}`, `,` | Inne mechanizmy, np. User-Defined Format, third-party traffic oraz dane oznaczone jako invalid/test. |

Już z tej tabeli widać najważniejszą cechę APRS:

**typ danych i DTI nie tworzą relacji jeden do jednego.**

## Czym jest DTI?

**Data Type Identifier** jest pierwszym bajtem pola Information i określa sposób interpretacji dalszej zawartości.

W praktyce DTI działa jak pierwszy selektor parsera.

Przykładowo:

```text
!5213.45N/02100.12E...
```

zaczyna się od `!`, więc parser oczekuje formatu pozycji bez timestampu i bez deklaracji obsługi APRS messaging.

```text
>QRV 145.500 MHz
```

zaczyna się od `>`, więc dalsze dane są interpretowane jako status.

```text
;REPEATER*...
```

zaczyna się od `;`, więc dalsza część ma format obiektu APRS.

```text
:SP9XYZ  :Test
```

zaczyna się od `:`, więc jest interpretowana zgodnie z formatem wiadomości APRS.

DTI nie jest więc po prostu nazwą typu danych. Określa konkretny wariant składni, a czasami przekazuje przy okazji dodatkową informację.

### DTI powinien być pierwszym bajtem

Oryginalne APRS 1.0.1 zawierało wyjątek dla DTI `!`, związany ze starymi digipeaterami X1J. Zgodnie z tamtym zapisem `!` mógł znajdować się nawet do 40. pozycji pola Information.

Późniejszy APRS 1.1 wycofał ten wyjątek. Współcześnie `!`, podobnie jak pozostałe DTI, powinien znajdować się na początku pola Information.

Nowe implementacje nie powinny generować historycznego wariantu X1J.

## Jeden typ danych, kilka DTI

Najbardziej czytelnym przykładem jest **Position**.

APRS ma cztery podstawowe DTI dla normalnych raportów pozycji:

| DTI | Timestamp | APRS messaging |
|---|---|---|
| `!` | nie | nie |
| `=` | nie | tak |
| `/` | tak | nie |
| `@` | tak | tak |

Wszystkie cztery oznaczają ten sam główny typ danych:

```text
Position
```

ale wybierają inny wariant formatu.

Można to przedstawić jako macierz:

| | Bez timestampu | Z timestampem |
|---|---|---|
| Bez APRS messaging | `!` | `/` |
| Z APRS messaging | `=` | `@` |

DTI przekazuje tu więc jednocześnie:

1. że pakiet jest raportem pozycji,
2. czy w formacie występuje timestamp,
3. czy stacja deklaruje obsługę APRS messaging.

To jest dobry przykład, dlaczego określenie "typ ramki" dla pojedynczego DTI jest zbyt dużym uproszczeniem.

## Capabilities - dwa różne pojęcia

W APRS łatwo pomylić dwa mechanizmy związane z możliwościami stacji.

### Messaging capability w DTI pozycji

DTI `=` i `@` oznaczają raport pozycji **with APRS messaging**.

DTI `!` i `/` oznaczają odpowiednio warianty **no APRS messaging**.

Ta informacja jest częścią znaczenia konkretnego DTI pozycji. Nie jest osobnym blokiem danych dołączonym do raportu.

### Station Capabilities i DTI `<`

Osobnym mechanizmem jest formalny pakiet **Station Capabilities**, którego DTI wynosi:

```text
<
```

APRS101 opisuje capabilities jako listę tokenów:

```text
TOKEN
```

lub:

```text
TOKEN=VALUE
```

Rozdzielonych przecinkami.

Klasyczny przykład:

```text
<IGATE,MSG_CNT=43,LOC_CNT=14
```

W APRS101 taki pakiet jest odpowiedzią na zapytanie `?IGATE?`.

Nie należy więc utożsamiać:

```text
= lub @
```

czyli informacji o możliwości APRS messaging zakodowanej w wariancie raportu pozycji,

z:

```text
<
```

czyli osobnym formatem Station Capabilities.

Mic-E ma jeszcze własne kody typu i identyfikacji urządzeń. To kolejny niezależny mechanizm i nie należy go mieszać ani z DTI `<`, ani z messaging capability raportów pozycji.

## DTI nie zawsze wystarcza do określenia pełnej semantyki

DTI jest podstawowym punktem wejścia do parsera, ale nie oznacza to, że jeden znak zawsze jednoznacznie określa wszystko.

Przykłady:

- `:` oznacza format wiadomości, ale dopiero dalsze pola rozróżniają zwykłą wiadomość, bulletin, announcement czy kierowane query,
- `!`, `=`, `/` i `@` mogą przenosić zwykłą pozycję, dane DF albo pogodę dołączoną do pozycji,
- `!` i `$` mają również stare, przeciążone znaczenia związane z surowymi formatami stacji pogodowych,
- odpowiedź na query nie ma jednego wspólnego DTI, bo odpowiedzią może być np. pozycja, status, pogoda, obiekt albo Station Capabilities,
- Mic-E wykorzystuje jednocześnie DTI, pole Information oraz zakodowane dane w Destination Address.

Dlatego poprawny parser APRS nie może kończyć analizy na prostym `switch(DTI)`. DTI wybiera rodzinę reguł, ale konkretny format może wymagać dalszego rozpoznania.

## Pełna tabela DTI z APRS101

Poniższa tabela pokazuje znaczenia zdefiniowane w APRS Protocol Reference 1.0.1 wraz z najważniejszymi współczesnymi uwagami.

| DTI | Znaczenie | Uwagi |
|---|---|---|
| `0x1c` | Current Mic-E Data, Rev 0 beta | Bardzo stary format beta. |
| `0x1d` | Old Mic-E Data, Rev 0 beta | Bardzo stary format beta. |
| `!` | Position bez timestampu, no APRS messaging | Historycznie także Ultimeter 2000 WX. Stary wyjątek X1J pozwalający na `!` później niż na początku pola został wycofany. |
| `"` | Unused | Nie używać jako nowego DTI. |
| `#` | Peet Bros U-II Weather Station | Surowy, historyczny format WX. Nowe implementacje nie powinny go wybierać. |
| `$` | Raw GPS data lub Ultimeter 2000 | Dla GPS typowo surowe zdania NMEA. DTI historycznie przeciążony przez format WX. |
| `%` | Agrelo DFJr / MicroFinder | Historyczny, specjalizowany format direction finding. |
| `&` | Reserved - Map Feature | Zarezerwowany w APRS101. |
| `'` | Mic-E | APRS101 opisuje go jako Old Mic-E, ale Kenwood TM-D700 używał go dla aktualnego Mic-E. W praktyce należy obsługiwać zgodnie z regułami Mic-E. |
| `(` | Unused | Nie używać jako nowego DTI. |
| `)` | Item | Format Item jest zdefiniowany, ale APRS 1.1 nie zaleca jego stosowania na RF z powodu problemów kompatybilności. |
| `*` | Peet Bros U-II Weather Station | Surowy, historyczny format WX. |
| `+` | Reserved - Shelter data with time | Zarezerwowany w APRS101. |
| `,` | Invalid data lub test data | Pakiet świadomie oznaczony jako dane niezgodne ze standardowym formatem lub dane testowe. |
| `-` | Unused | Nie używać jako nowego DTI. |
| `.` | Reserved - Space weather | Zarezerwowany w APRS101. |
| `/` | Position z timestampem, no APRS messaging | Jeden z czterech podstawowych DTI pozycji. |
| `0`-`9` | Do not use | Nie są przeznaczone na DTI. |
| `:` | Message | Ten sam DTI obsługuje wiadomości, bulletiny, announcements i kierowane queries. |
| `;` | Object | Raport obiektu APRS. |
| `<` | Station Capabilities | Format tokenów capabilities, np. `<IGATE,MSG_CNT=43,LOC_CNT=14`. |
| `=` | Position bez timestampu, with APRS messaging | Jeden z czterech podstawowych DTI pozycji. |
| `>` | Status | Raport statusu. |
| `?` | Query | General query. Directed query korzysta z formatu wiadomości `:`. |
| `@` | Position z timestampem, with APRS messaging | Jeden z czterech podstawowych DTI pozycji. |
| `A`-`S` | Do not use | Zakres zarezerwowany jako niedozwolony dla nowych DTI. |
| `T` | Telemetry data | Klasyczny format telemetryczny `T#...`. |
| `U`-`Z` | Do not use | Zakres zarezerwowany jako niedozwolony dla nowych DTI. |
| `[` | Maidenhead grid locator beacon | Format oznaczony w APRS101 jako obsolete. |
| `\` | Unused | Nie używać jako nowego DTI. |
| `]` | Unused | Nie używać jako nowego DTI. |
| `^` | Unused | Nie używać jako nowego DTI. |
| `_` | Weather Report bez pozycji | Standardowy positionless weather report. |
| `` ` `` | Current Mic-E Data | Standardowo używany DTI Mic-E. APRS101 zaznacza wyjątek związany z TM-D700. |
| `a`-`z` | Do not use | Nie są przeznaczone na DTI. |
| `{` | User-Defined APRS packet format | Zarezerwowany mechanizm dla formatów definiowanych przez autorów aplikacji. |
| `|` | Do not use jako DTI | W APRS101 znak był związany z historycznym TNC stream switching. Późniejsze rozszerzenia mogą używać `|` wewnątrz danych, np. Base91 Comment Telemetry. |
| `}` | Third-party traffic | Opakowanie pakietu APRS przeniesionego przez sieć pośrednią. Wewnątrz znajduje się oryginalny nagłówek i jego własne dane APRS. |
| `~` | Do not use jako DTI | Historycznie związany z TNC stream switching. |

Warto zwrócić uwagę, że tabela DTI APRS101 odzwierciedla stan protokołu z 2000 roku. Późniejsze addenda korygują część zaleceń i oznaczają niektóre stare mechanizmy jako przestarzałe lub niezalecane.

## Weather pokazuje, dlaczego sama tabela DTI nie wystarcza

Pogoda jest szczególnie dobrym przykładem wielowarstwowej natury APRS.

Raport bez pozycji może zaczynać się od:

```text
_
```

Natomiast dane pogodowe mogą być dołączone do raportu pozycji, który zaczyna się od:

```text
!
=
/
@
```

Dodatkowo APRS101 definiował surowe formaty konkretnych stacji pogodowych wykorzystujące:

```text
!
#
$
*
```

APRS 1.1 zaleca, aby nowe urządzenia nie wysyłały surowych formatów weather station, tylko konwertowały dane do standardowych formatów APRS.

Oznacza to, że sama wartość DTI może wymagać spojrzenia na dalszą zawartość pakietu oraz znajomości historycznych wariantów.

## Telemetria też nie zawsze ma DTI `T`

Klasyczna telemetria APRS ma format:

```text
T#001,123,045,255,010,100,10110011
```

i używa DTI:

```text
T
```

Nie oznacza to jednak, że wszystkie dane telemetryczne w APRS muszą występować w pakiecie `T`.

Historyczna telemetria Mic-E korzystała z mechanizmu Mic-E i została później uznana za przestarzałą w związku z wprowadzeniem Mic-E Type Codes.

Późniejsze **Base91 Comment Telemetry** pozwala umieścić dane telemetryczne bezpośrednio w komentarzu raportu pozycji, np. pomiędzy znakami `|...|`. W takim przypadku DTI całego pakietu pozostaje DTI pozycji, np. `!`, `=` albo Mic-E, a telemetria jest rozszerzeniem znajdującym się głębiej w danych.

To kolejny przykład pokazujący, że:

**typ danych APRS nie jest tym samym co DTI pierwszego bajtu.**

## User-Defined APRS packet format

DTI:

```text
{
```

jest formalnie przeznaczony dla **User-Defined APRS packet format**.

APRS101 definiuje początek takiego pakietu jako:

```text
{UX...
```

gdzie:

- `{` jest DTI,
- `U` jest jednobajtowym User ID,
- `X` jest jednobajtowym identyfikatorem konkretnego typu pakietu danego autora,
- po nich następują dane formatu zdefiniowanego przez autora.

Przykład z APRS101:

```text
{Q1qwerty
```

oznacza:

```text
DTI:         {
User ID:     Q
Packet Type: 1
Data:        qwerty
```

Dla eksperymentów przewidziano User ID równy `{`, więc pakiet eksperymentalny może zaczynać się od:

```text
{{
```

Przykład:

```text
{{zasdfg
```

W późniejszym rejestrze eksperymentalnych formatów pojawiły się między innymi nagłówki:

```text
{BT
{BP
{Dx
{KY
{Q1
{Q2
{Sx
```

User-Defined Format nie oznacza, że każdy program APRS musi taki pakiet rozumieć. APRS101 wprost zakłada, że formaty te są opcjonalne, a aplikacja, która nie zna danego User ID i Packet Type, powinna je bezpiecznie zignorować.

Mechanizm `{` jest więc kontrolowanym sposobem rozszerzania APRS bez udawania, że niestandardowe dane są którymś ze standardowych formatów.

## Third-party traffic i DTI `}`

DTI:

```text
}
```

oznacza **third-party traffic**.

W tym formacie pole Information zawiera opakowany oryginalny pakiet APRS wraz z jego nagłówkiem i danymi.

Schematycznie:

```text
}SOURCE>DEST,PATH:DTI...
```

Pierwszy DTI zewnętrznego pakietu to `}`, ale wewnętrzny pakiet ma własny DTI i musi zostać ponownie zinterpretowany zgodnie z jego formatem.

To szczególny przypadek, w którym parser po rozpoznaniu `}` wraca do analizy kompletnego pakietu APRS znajdującego się wewnątrz danych.

## Znaki `|` i `~` - historyczne ograniczenie

APRS101 oznacza `|` i `~` jako znaki, których nie należy używać jako DTI, ponieważ stare TNC wykorzystywały je do przełączania strumieni.

W oryginalnej specyfikacji ograniczenie to było również traktowane szerzej przy komentarzach.

Późniejsza specyfikacja Base91 Comment Telemetry celowo wykorzystuje jednak:

```text
|...|
```

jako delimitery danych telemetrycznych w komentarzu.

Autorzy tego rozszerzenia zwracają uwagę, że ograniczenie wynikało z bardzo starego trybu CONVerse klasycznych TNC, natomiast w praktyce znak `|` przechodzi poprawnie przez współczesne sieci APRS i APRS-IS.

Dlatego nowoczesny parser nie powinien stosować starej zasady "znak `|` nigdy nie może wystąpić w danych APRS". Nadal nie jest on standardowym DTI, ale może mieć zdefiniowane znaczenie wewnątrz konkretnego formatu lub rozszerzenia.

## Reguła praktyczna

Najbezpieczniejszy sposób interpretacji pola Information jest następujący:

1. odczytaj pierwszy bajt jako DTI,
2. wybierz rodzinę formatu wynikającą z DTI,
3. uwzględnij, że jeden główny typ danych może mieć kilka DTI,
4. uwzględnij dodatkową semantykę DTI, np. timestamp i APRS messaging dla pozycji,
5. jeżeli format jest przeciążony lub historyczny, sprawdź dalszą składnię,
6. parsuj klasyczne Data Extension tylko tam, gdzie dany format je przewiduje,
7. po danych głównych uwzględnij rozszerzenia i informacje zapisane w komentarzu,
8. dla `{` zastosuj reguły konkretnego User ID i Packet Type,
9. dla `}` rozpakuj wewnętrzny pakiet i rozpocznij interpretację jego DTI od nowa,
10. nie zakładaj, że nierozpoznany DTI można bezpiecznie zinterpretować jako tekst.

DTI jest więc pierwszym kluczem do dekodowania pola Information, ale pełne znaczenie pakietu wynika z połączenia:

```text
głównego typu danych
+
DTI
+
wariantu formatu
+
ewentualnych capabilities
+
rozszerzeń
+
komentarza
```

## Źródła

Podstawą tego artykułu są:

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [APRS 1.2 Addendum Proposals](https://www.aprs.org/aprs12.html)
- [APRS User Defined Data Formats List](https://www.aprs.org/aprs11/expfmts.txt)
- [APRS Documentation Project - APRS 1.2 compilation](https://github.com/wb2osz/aprsspec)
- [Base91 Comment Telemetry specification](https://github.com/hessu/aprs-specs/blob/master/aprs-base91-comment-telemetry.txt)
