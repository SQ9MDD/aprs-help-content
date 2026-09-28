---
title: "Mic-E: historia, zastosowania i format danych"
description: "Kodowanie pozycji APRS w adresie docelowym i polu informacyjnym AX.25, historia Mic-E, rozszerzenia i zasady dekodowania."
---


**Mic-E (Microphone Encoder)** jest formatem krótkich raportów pozycyjnych APRS. Jego charakterystyczną cechą jest wykorzystanie nie tylko pola informacyjnego ramki AX.25, lecz również adresu docelowego. Sześć znaków tego adresu przenosi zakodowaną szerokość geograficzną oraz dodatkowe informacje, a pole informacyjne uzupełnia raport o długość geograficzną, kurs, prędkość i symbol. Mic-E nie jest odrębnym protokołem łącza ani odmianą formatu *Compressed Position* wykorzystującego Base-91.

Format pozwala przekazać znaczną ilość danych w krótkiej ramce. Według *APRS Protocol Reference* minimalna kompletna ramka Mic-E, bez adresów digipeaterów i bez danych opcjonalnych, zajmuje 25 bajtów, nie licząc FCS i flag AX.25. Ta wartość dotyczy całej ramki, a nie samego pola informacyjnego.

## Historia

Mic-E opracowali Bob Bruninga, WB4APR, i N3MIM w 1994 roku. Pierwotnie był to sprzętowy koder mikrofonowy, umożliwiający przekazywanie danych APRS przy użyciu radiotelefonu. Po prototypie pojawił się zestaw TAPR Mic-E; format stosowano także w urządzeniach PIC Encoder.

Podczas przygotowywania formalnej specyfikacji APRS rozwijano już radiotelefony Kenwood TH-D7 i TM-D700. W związku z tym przewidziano identyfikatory typu urządzenia w opcjonalnym polu tekstowym: `>` dla TH-D7 i `]` dla TM-D700. Specyfikacja APRS 1.0, opublikowana w 2000 roku, dokumentowała kodowanie pozycji, wiadomości Mic-E, telemetrię i tekst statusowy.

Rozwój kolejnych urządzeń ujawnił ograniczenia pierwotnego sposobu identyfikacji. TM-D710 zachował identyfikator rodziny `]`, a do rozróżnienia modelu dodano końcowy znak `=`. W latach 2007–2008 rozszerzono identyfikację o kategorie innych urządzeń: znak `` ` `` oznaczał urządzenie obsługujące wiadomości, a `'` jednokierunkowy tracker. Następnie wprowadzono końcową parę znaków producenta i wersji (`Mv`). Dodatkowe informacje umieszczano w tekście, bez przebudowy zasadniczego kodowania pozycji, aby zachować zgodność ze starszymi odbiornikami.

Dokument *Mic-E TYPE CODES* opisuje dalsze identyfikatory i urządzenia, z aktualizacjami do 4 czerwca 2019 roku. Jest to historyczny wykaz, a nie gwarancja kompletnej identyfikacji wszystkich późniejszych modeli.

## Zastosowania

Mic-E stosowano w koderach mikrofonowych, samodzielnych trackerach i radiotelefonach APRS. Krótki raport dobrze odpowiada potrzebom stacji ruchomych, które regularnie transmitują pozycję, kurs i prędkość. Kod wiadomości Mic-E pozwala dodatkowo przekazać jeden z predefiniowanych stanów lub stan własny.

Odbiornik, digipeater i IGate nadal pracują z ramką AX.25. Oprogramowanie APRS musi jednak wiedzieć, że pozorny adres docelowy zawiera dane, a nie zwykły identyfikator aplikacji TOCALL. Nie należy zatem przypisywać temu adresowi znaczenia właściwego zwykłym ramkom APRS.

## Rozmieszczenie danych w ramce

Mic-E korzysta ze standardowej ramki AX.25 UI. Dane dzielą się następująco:

| Pole | Informacje Mic-E |
| --- | --- |
| Adres docelowy, znaki 1–3 | Pierwsze trzy cyfry szerokości geograficznej i bity wiadomości A/B/C |
| Adres docelowy, znak 4 | Czwarta cyfra szerokości i wskaźnik N/S |
| Adres docelowy, znak 5 | Piąta cyfra szerokości i przesunięcie długości geograficznej |
| Adres docelowy, znak 6 | Szósta cyfra szerokości i wskaźnik E/W |
| SSID adresu docelowego | Historyczny kod ścieżki APRS |
| Pole informacyjne, bajt 1 | Identyfikator typu danych Mic-E (DTI) |
| Pole informacyjne, bajty 2–4 | Długość geograficzna |
| Pole informacyjne, bajty 5–7 | Prędkość i kurs |
| Pole informacyjne, bajty 8–9 | Kod symbolu i identyfikator tablicy symboli |
| Dalsze bajty | Opcjonalna telemetria albo tekst statusowy z rozszerzeniami |

Sześć znaków adresu docelowego jest kodowanych jako prawidłowe znaki adresu AX.25. Nie oznacza to, że reprezentują znak wywoławczy stacji docelowej. Adres źródłowy pozostaje identyfikatorem stacji nadającej.

## Szerokość geograficzna w adresie docelowym

Sześć cyfr szerokości geograficznej odpowiada zapisowi `DDMM.hh`: dwie cyfry stopni, dwie minut i dwie setnych części minuty. Każdy znak adresu koduje jedną cyfrę i, zależnie od swojej pozycji, dodatkową informację.

| Zakres znaków | Cyfra szerokości | Dodatkowa informacja |
| --- | --- | --- |
| `0`–`9` | `0`–`9` | Bit `0`; na pozycjach 4–6 odpowiednio S, offset `+0`, E |
| `A`–`J` | `0`–`9` | Bit wiadomości własnej `1` na pozycjach 1–3 |
| `P`–`Y` | `0`–`9` | Bit wiadomości standardowej `1`; na pozycjach 4–6 odpowiednio N, offset `+100`, W |
| `K`, `L`, `Z` | Brak cyfry | Specjalne wartości związane z niejednoznacznością pozycji |

Na pozycjach 4–6 nie używa się `A`–`K`. Szczegółowe znaczenie znaków specjalnych zależy od pozycji. Dla przykładu adres `S32U6T` koduje szerokość **33°25,64′ N**, bity wiadomości `100` (standardowy stan *Returning*), przesunięcie długości `+0` i półkulę zachodnią. To przykład bezpośrednio z rozdziału 10 specyfikacji.

### Kod wiadomości Mic-E

Pierwsze trzy znaki adresu zawierają bity A, B i C. Dla niezerowych kombinacji można przekazać wiadomość standardową lub własną. Wszystkie bity równe zero oznaczają *Emergency*.

| A | B | C | Standard | Własny |
| --- | --- | --- | --- | --- |
| 1 | 1 | 1 | M0: Off Duty | C0: Custom-0 |
| 1 | 1 | 0 | M1: En Route | C1: Custom-1 |
| 1 | 0 | 1 | M2: In Service | C2: Custom-2 |
| 1 | 0 | 0 | M3: Returning | C3: Custom-3 |
| 0 | 1 | 1 | M4: Committed | C4: Custom-4 |
| 0 | 1 | 0 | M5: Special | C5: Custom-5 |
| 0 | 0 | 1 | M6: Priority | C6: Custom-6 |
| 0 | 0 | 0 | Emergency | Emergency |

Dla wiadomości standardowych wszystkie ustawione bity muszą być kodowane jako standardowe, a dla własnych jako własne. Mieszane kodowanie ustawionych bitów daje nieznany typ wiadomości. Obsługa wiadomości własnych jest opcjonalna; oryginalne urządzenia Mic-E jej nie zapewniały. Kod wiadomości Mic-E nie jest wiadomością tekstową APRS z mechanizmem potwierdzeń.

### SSID adresu docelowego

Pierwotna specyfikacja przewiduje kodowanie konwencjonalnej lub jednej z historycznych, ogólnych ścieżek APRS za pomocą SSID adresu docelowego. Nie należy utożsamiać tego pola z identyfikatorem aplikacji ani traktować historycznego kodowania ścieżek jako zalecenia konfiguracji współczesnej sieci. Faktyczna lista adresów digipeaterów, jeśli występuje, pozostaje osobnym polem ramki AX.25.

## Pole informacyjne

Podstawowa część pola informacyjnego Mic-E ma dziewięć bajtów:

```text
DTI | d+28 | m+28 | h+28 | SP+28 | DC+28 | SE+28 | SYMBOL | TABLE
 1  |   2  |   3  |   4  |   5   |   6   |   7   |    8   |   9
```

Bajty po tej części są opcjonalne. Nie wszystkie bajty podstawowej części muszą być drukowalnymi znakami ASCII. Jeżeli transmisja lub konwersja tekstowa usunie znaki sterujące, pozycje pozostałych bajtów się przesuną, a dekodowanie stanie się błędne. Specyfikacja nakazuje odrzucić pakiet, którego pole informacyjne ma mniej niż dziewięć bajtów.

### Identyfikator DTI i historyczna niezgodność

Pierwotnie `` ` `` oznaczał aktualną pozycję GPS, a `'` starą pozycję. W radiotelefonie Kenwood TM-D700 aktualną pozycję oznaczano jednak apostrofem. Specyfikacja odnotowuje tę niezgodność i wskazuje możliwość rozpoznania TM-D700 po identyfikatorze `]` na początku tekstu statusowego. Wczesne jednostki beta używały także wartości `0x1c` i `0x1d`.

Nie wolno mylić **pierwszego bajtu pola informacyjnego (DTI)** z **identyfikatorem urządzenia**, który może występować dopiero po dziewięciobajtowej części podstawowej. Oba miejsca mogą zawierać znaki `` ` `` lub `'`, ale mają odmienne znaczenie.

### Długość geograficzna

Długość geograficzną przenoszą bajty `d+28`, `m+28` i `h+28`. Półkulę E/W i przesunięcie `+0` lub `+100` należy odczytać z adresu docelowego.

Dekodowanie stopni:

1. `d = wartość_bajtu_2 - 28`.
2. Jeżeli adres wskazuje przesunięcie `+100`, dodaj `100`.
3. Jeżeli wynik mieści się w przedziale `180–189`, odejmij `80`.
4. Jeżeli wynik mieści się w przedziale `190–199`, odejmij `190`.

Nietypowe korekty są konieczne ze względu na sposób zakodowania zakresów `0–9` i `100–109` stopni. Prawidłowy bajt stopni mieści się w zakresie dziesiętnym `38–127`.

Dekodowanie minut:

1. `m = wartość_bajtu_3 - 28`.
2. Jeśli `m >= 60`, odejmij `60`.

Dekodowanie setnych części minuty: `h = wartość_bajtu_4 - 28`. Wynik stanowi setne części minuty. Należy zachować oryginalne wartości bajtów i walidować uzyskane zakresy.

### Prędkość i kurs

Trzy kolejne bajty kodują prędkość w węzłach i kurs w stopniach. Specyfikacja przewiduje prędkości `0–799` węzłów oraz kurs `0–360°`; `0` oznacza kurs nieznany lub nieokreślony, a `360` kierunek północny.

Dekoder odtwarza wartości po odjęciu `28` od każdego z trzech bajtów:

```text
sp = bajt_5 - 28
dc = bajt_6 - 28
se = bajt_7 - 28

speed  = sp * 10 + floor(dc / 10)
course = (dc % 10) * 100 + se

if speed >= 800: speed -= 800
if course >= 400: course -= 400
```

W praktyce należy uwzględnić dwa historyczne sposoby kodowania części wartości `SP+28` i `DC+28`, opisane w tabelach specyfikacji. Powyższy algorytm dekodowania uwzględnia oba warianty dzięki końcowym korektom. Wartości spoza dozwolonych zakresów wymagają odrzucenia albo oznaczenia jako błędne, a nie automatycznego przedstawienia jako poprawnej pozycji.

Specyfikacja podaje przykład **86 węzłów i kurs 194°**: `SP+28` może przyjąć `t` lub `$`, `DC+28` może przyjąć `]` lub `Y`, a `SE+28` ma wartość `z`.

### Symbol i tablica symboli

Bajt ósmy jest kodem symbolu, a dziewiąty identyfikatorem tablicy symboli lub nakładki. Ich kolejność jest odwrotna do często spotykanego tekstowego zapisu symbolu APRS, w którym najpierw podaje się tablicę, a potem kod symbolu. Dokument opisujący ewolucję Mic-E wiąże to z późniejszym dodaniem alternatywnej tablicy symboli do pierwotnego formatu.

## Niejednoznaczność pozycji

Mic-E obsługuje celowe ograniczenie dokładności raportowanej pozycji. Niejednoznaczność jest sygnalizowana w sześciu znakach szerokości geograficznej, a ten sam stopień niejednoznaczności należy zastosować do długości geograficznej. Przykład specyfikacji `T4SQZZ` wskazuje, że dwie ostatnie cyfry szerokości są niejednoznaczne; przy dekodowaniu długości należy wówczas pominąć odpowiadające im dwie ostatnie cyfry precyzji. Oprogramowanie nie powinno przedstawiać takich danych jako dokładnych współrzędnych.

## Opcjonalna telemetria i tekst statusowy

Po dziewięciu bajtach podstawowych może wystąpić **telemetria Mic-E albo tekst statusowy**. Rozpoznanie opiera się na pierwszym bajcie części opcjonalnej.

| Flaga | Znaczenie według APRS 1.0 |
| --- | --- |
| `` ` `` | Dwie wartości telemetryczne zapisane drukowalnie w systemie szesnastkowym; kanały 1 i 3 |
| `'` | Pięć wartości telemetrycznych zapisanych drukowalnie w systemie szesnastkowym |
| `0x1d` | Pięć binarnych wartości telemetrycznych; historyczne jednostki beta |

W wariancie drukowalnym każda wartość `0–255` zajmuje dwa znaki szesnastkowe. Przykład specyfikacji `'7200007100` odpowiada pięciu wartościom: `114`, `0`, `0`, `113`, `0`.

Jeśli część opcjonalna jest tekstem statusowym, nie może zaczynać się od znaków zarezerwowanych jako flagi telemetrii, gdyż spowodowałoby to błędną interpretację. Tekst może zawierać komentarz, lokator Maidenhead i wysokość. Specyfikacja dopuszcza również standardowo sformatowaną pozycję APRS wewnątrz tekstu Mic-E; odbiornik może użyć jej zamiast pozycji zakodowanej w Mic-E. Jest to historycznie przydatne między innymi przy pracy kodera bez odbiornika GPS.

### Lokator Maidenhead

Lokator może występować w tekście statusowym. Gdy po nim znajduje się komentarz, specyfikacja wymaga odpowiedniego oddzielenia tekstu. Rozpoznawanie lokatora powinno uwzględniać ewentualny poprzedzający identyfikator urządzenia.

### Wysokość w formacie `aaa}`

Wysokość Mic-E jest kodowana trzema znakami Base-91 i zamykającym `}`. Wartość odnosi się do poziomu odniesienia położonego 10 000 m poniżej średniego poziomu morza. Dla kodowania wysokości w metrach:

```text
v = wysokość_m + 10000
znak_1 = floor(v / (91 * 91)) + 33
znak_2 = floor(v / 91) % 91 + 33
znak_3 = v % 91 + 33
```

Dekodowanie odwraca tę operację i odejmuje `10000`. Specyfikacja podaje przykład wysokości 200 stóp (około 61 m), zakodowanej jako `"4T}`. W późniejszych zaleceniach opcjonalne `aaa}` umieszcza się bezpośrednio po identyfikatorze typu urządzenia, przed pozostałym tekstem.

## Ewolucja pola tekstowego

Pierwotne urządzenia przesyłały tekst statusowy bez rozbudowanego identyfikatora. Późniejsze rozszerzenia umieszczano w tym samym obszarze, aby starsze odbiorniki nadal potrafiły odczytać podstawową pozycję. W dokumentach WB4APR występują następujące elementy:

| Element | Znaczenie | Położenie |
| --- | --- | --- |
| `T` | Opcjonalny identyfikator typu urządzenia | Początek tekstu statusowego |
| `aaa}` | Opcjonalna wysokość Base-91 | Bezpośrednio po `T`, jeśli występuje |
| `FFF.FFFMHz` | Opcjonalna częstotliwość | Początek właściwego tekstu, po ewentualnej wysokości |
| Dowolny tekst | Komentarz stacji | Po polach wymagających określonego położenia |
| `/A=xxxxxx` | Starszy zapis wysokości | W dalszej części komentarza, jeśli używany |
| `!DAO!` | Rozszerzona precyzja pozycji i datum | Zalecane umieszczenie pod koniec tekstu |
| `Mv` | Identyfikator producenta i wersji | Koniec tekstu w nowszym systemie identyfikacji |

To zestawienie łączy elementy z różnych etapów rozwoju Mic-E. Nie wszystkie są wymagane ani obecne jednocześnie. Zapis `!DAO!` oznacza tutaj rodzinę rozszerzeń opisywaną w dokumentacji APRS 1.2, a nie dosłowną wartość danych do nadania. Szczegółowe znaczenie i walidację `!DAO!` należy realizować zgodnie z dokumentacją tego rozszerzenia.

Przy jednoczesnym występowaniu wysokości i częstotliwości zalecana kolejność to `T`, `aaa}`, `FFF.FFFMHz`, następnie tekst i dalsze dodatki. Dokument z 2008 roku zaleca też odstęp po częstotliwości ze względu na zgodność ze starszymi urządzeniami Yaesu FTM-350.

## Identyfikacja urządzenia

Identyfikator urządzenia jest elementem **opcjonalnego tekstu**, a nie adresem docelowym i nie identyfikatorem DTI. Pierwotna specyfikacja przewidziała między innymi `>` dla Kenwood TH-D7 i `]` dla TM-D700. Późniejsze ustalenia rozszerzyły te identyfikatory:

| Postać | Interpretacja w dokumentacji WB4APR |
| --- | --- |
| `>...` | Rodzina Kenwood TH-D7 |
| `]...` | Kenwood TM-D700 |
| `]...=` | Kenwood TM-D710 |
| `>...=` | Kenwood TH-D72 |
| `>...^` | Kenwood TH-D74 |
| `` `...Mv `` | Inne urządzenie Mic-E obsługujące wiadomości |
| `'...Mv` | Inny jednokierunkowy tracker Mic-E |

W zapisie tabelarycznym wielokropek oznacza tekst o zmiennej długości, nie konkretną liczbę bajtów. Dla późniejszych urządzeń kombinacja znaku początkowego oraz końcowego `Mv` może określać producenta i model. Dokument *Mic-E TYPE CODES* wymienia między innymi modele Yaesu, Byonics, AnyTone i SCS, ale jego ostatnia aktualizacja pochodzi z 2019 roku. Identyfikatory spoza znanej tabeli nie powinny prowadzić do odrzucenia poprawnej pozycji Mic-E.

Ważne jest również zachowanie zgodności przy wyświetlaniu tekstu: rozpoznany identyfikator typu można usunąć z komentarza widocznego dla operatora i przedstawić osobno jako informację o urządzeniu. Nierozpoznanych końcowych znaków nie należy bezwarunkowo usuwać, ponieważ mogą być częścią komentarza.

## Przykłady dekodowania

### Adres docelowy `S32U6T`

Przykład z APRS Protocol Reference:

- Sześć cyfr: `332564`, czyli **33°25,64′**.
- Czwarty znak `U` wskazuje półkulę północną.
- Piąty znak `6` wskazuje przesunięcie długości `+0`.
- Szósty znak `T` wskazuje półkulę zachodnią.
- Bity wiadomości: `100`, standardowy stan **M3: Returning**.

Adres sam w sobie nie zawiera pełnej długości geograficznej: jej wartości liczbowe trzeba pobrać z pola informacyjnego.

### Długość geograficzna z pola informacyjnego

W przykładzie specyfikacji bajty `(`, `_`, `f` mają wartości dziesiętne `40`, `95` i `102`. Przy przesunięciu `+100` oraz wskaźniku W dekoder otrzymuje:

```text
stopnie: 40 - 28 + 100 = 112
minuty: 95 - 28 - 60 = 7
setne:  102 - 28 = 74
wynik:  112°07,74′ W
```

### Prędkość i kurs z pola informacyjnego

Ten sam przykład wykorzystuje bajty `n`, `"`, `O`:

```text
sp = 110 - 28 = 82
dc =  34 - 28 = 6
se =  79 - 28 = 51

speed  = 82 * 10 + 0 = 820; 820 - 800 = 20 węzłów
course = 6 * 100 + 51 = 651; 651 - 400 = 251°
```

Wynik: **20 węzłów i kurs 251°**. Końcowe bajty `j/` określają symbol pojazdu terenowego z podstawowej tablicy symboli. Przykłady ilustrują odczyt poszczególnych części formatu; nie należy łączyć ich w jedną nową ramkę bez sprawdzenia wszystkich pól.

## Wskazówki dla implementacji

Dekoder Mic-E powinien przetwarzać dane jako bajty, a nie zakładać, że całe pole informacyjne jest zwykłym tekstem. Zalecana kolejność przetwarzania:

1. Zweryfikować poprawność ramki AX.25 i długość pola informacyjnego (co najmniej dziewięć bajtów).
2. Rozpoznać DTI Mic-E i zdekodować sześć znaków adresu docelowego, zachowując rozróżnienie między wiadomościami standardowymi, własnymi i nieznanymi.
3. Odtworzyć współrzędne z obu pól i uwzględnić niejednoznaczność pozycji.
4. Zdekodować prędkość, kurs, symbol i tablicę symboli.
5. Rozpoznać opcjonalną telemetrię albo tekst statusowy, nie myląc ich flag z identyfikatorem DTI.
6. W tekście statusowym rozpoznać tylko te rozszerzenia i identyfikatory urządzeń, których składnia jest znana; zachować nierozpoznane dane.
7. Nie modyfikować zakodowanego adresu docelowego przy przekazywaniu pakietu. Zmiana jego znaków może zmienić współrzędne, kod wiadomości lub wskaźniki półkul.

Przy konwersji danych na postać tekstową należy szczególnie uważać na niedrukowalne bajty Mic-E. Utrata choćby jednego bajtu może uniemożliwić poprawne odczytanie całego raportu. Oprogramowanie powinno również uwzględniać historyczne odstępstwa niektórych urządzeń, zamiast przyjmować, że sam znak DTI zawsze jednoznacznie określa aktualność pozycji GPS.

## Źródła i zakres opracowania

- *APRS Protocol Reference*, APRS Protocol Version 1.0, Document Version 1.0.1, 29 sierpnia 2000, rozdział 10: **Mic-E Data Format**. Podstawowe kodowanie adresu docelowego, pola informacyjnego, wiadomości, niejednoznaczności pozycji, telemetrii i tekstu.
- Bob Bruninga, WB4APR, [*Mic-E TEST EXAMPLES*](https://www.aprs.org/aprs12/mic-e-examples.txt), 10 grudnia 2008. Ewolucja pola tekstowego, kolejność rozszerzeń i przykłady zgodności wstecznej.
- Bob Bruninga, WB4APR, [*Mic-E TYPE CODES*](https://www.aprs.org/aprs12/mic-e-types.txt), aktualizacja 4 czerwca 2019. Historyczne i późniejsze identyfikatory urządzeń.

Dokumenty z 2008 i 2019 roku rozwijają zagadnienia, których pierwotna specyfikacja z 2000 roku nie opisuje w całości. Opis identyfikatorów urządzeń nie jest kompletnym rejestrem wszystkich współczesnych implementacji Mic-E. Ogólne reguły rozszerzeń APRS, takie jak pełna składnia `!DAO!` i uniwersalny zapis częstotliwości, są osobnymi zagadnieniami; tutaj omówiono ich umiejscowienie w polu tekstowym Mic-E.
