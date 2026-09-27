---
title: "IGate i wymiana danych z APRS-IS"
description: Rola IGate, kierunki przekazywania pakietów, q construct i mechanizmy dostarczania ruchu APRS-IS niezależnie od filtrów.
---

IGate (Internet Gateway) łączy radiową sieć APRS z APRS-IS. Odbiera pakiety z kanału radiowego i przekazuje je do sieci internetowej. Dwukierunkowy IGate może również nadawać wybrane pakiety odebrane z APRS-IS na RF. Nie jest jednak przezroczystym mostem: w obu kierunkach obowiązują odrębne zasady przekazywania, a po stronie internetowej istotną rolę odgrywają serwery APRS-IS.

## Kierunek RF do APRS-IS

Podstawowym zadaniem IGate jest udostępnianie w APRS-IS pakietów odebranych drogą radiową. Dotyczy to między innymi pozycji, wiadomości, obiektów, telemetrii i danych pogodowych. IGate przekazuje poprawne pakiety z zachowaniem ich zawartości i radiowej ścieżki, z uwzględnieniem reguł zapobiegających ponownemu wprowadzaniu tych samych danych do sieci.

Zgodnie z zasadami IGate do APRS-IS nie należy przekazywać między innymi:

- ramek AX.25 bez właściwych pól sterujących UI (`0x03`) i PID (`0xF0`);
- ramek przyjętych w trybie `PASSALL`, który nie gwarantuje poprawności danych;
- ogólnych zapytań APRS rozpoczynających się od `?`;
- pakietów zawierających w ścieżce `TCPIP` lub `TCPXX`, a także, zgodnie z przyjętymi regułami, `NOGATE` lub `RFONLY`;
- pakietów w formacie third-party zawierających `TCPIP` lub `TCPXX` w wewnętrznym nagłówku.

Pakiet third-party, który nie zawiera takich znaczników, wymaga odpowiedniego usunięcia radiowego nagłówka zewnętrznego i znacznika third-party przed przekazaniem do APRS-IS.

IGate nie powinien dowolnie przepisywać ścieżki pakietu. Informację o wejściu pakietu z RF do APRS-IS umieszcza w przeznaczonym do tego miejscu.

## q construct: identyfikacja pochodzenia pakietu

`q construct` to mechanizm nagłówka stosowany **wyłącznie w APRS-IS**. Pozwala rozpoznać sposób wprowadzenia pakietu do sieci, wskazać punkt wejścia i wspomagać wykrywanie pętli. Nie jest częścią radiowej ścieżki AX.25 i nie wolno go nadawać na RF.

Przykład pakietu odebranego na RF:

```text
SQ9ABC>APRS,WIDE1-1:!5000.00N/01900.00E-
```

Po przekazaniu przez dwukierunkowy IGate `SQ9MDD-4` jego nagłówek w APRS-IS może wyglądać tak:

```text
SQ9ABC>APRS,WIDE1-1,qAR,SQ9MDD-4:!5000.00N/01900.00E-
```

`qAR` wskazuje pakiet przekazany z RF przez IGate, który może obsługiwać przekazywanie wiadomości do danej stacji. `SQ9MDD-4` jest identyfikatorem punktu wejścia. Sam zapis nie potwierdza jednak, że wiadomość zostanie rzeczywiście nadana na RF: zależy to również od zasad działania IGate i sytuacji w sieci.

Najważniejsze konstrukcje spotykane w APRS-IS:

| Konstrukcja | Znaczenie |
| --- | --- |
| `qAR` | Pakiet przekazany z RF przez IGate deklarujący możliwość przekazywania wiadomości do danej stacji. |
| `qAO` | Pakiet przekazany z RF bez możliwości przekazania wiadomości do danej stacji, w szczególności przez IGate tylko odbiorczy. |
| `qAC` | Pakiet pochodzący bezpośrednio od klienta z poprawnie zweryfikowanym logowaniem, oznaczony przez serwer. |
| `qAS` | Pakiet otrzymany przez serwer bez istniejącego q construct lub wygenerowany przez serwer. |
| `qAU` | Pakiet otrzymany bezpośrednio przez UDP. |
| `qAI` | Konstrukcja śledzenia drogi pakietu przez serwery APRS-IS. |

W dokumentacji występują także konstrukcje związane ze starszymi sposobami przekazywania pakietów, między innymi `qAr` i `qAo`, oraz wycofane mechanizmy niezaufanego logowania. Wielkość liter w q construct ma znaczenie.

Nie należy utożsamiać `qAR` z dowodem, że IGate ma fizycznie działający nadajnik ani `qAO` wyłącznie z brakiem nadajnika. Dwukierunkowy IGate również może użyć `qAO` dla stacji, do której nie będzie przekazywać wiadomości.

## Kierunek APRS-IS do RF

Przekazywanie danych z Internetu na kanał radiowy jest znacznie bardziej selektywne. Nie należy retransmitować całego strumienia APRS-IS: spowodowałoby to szybkie przeciążenie lokalnego kanału.

Podstawowym zastosowaniem dwukierunkowego IGate jest dostarczenie wiadomości do stacji znajdujących się w jego zasięgu radiowym. Zgodnie z podstawowymi kryteriami IGate przekazuje wiadomość i powiązane z nią pakiety pozycyjne, gdy spełnione są odpowiednie warunki:

- adresat był słyszany na RF w zdefiniowanym przedziale czasu i mieści się w przyjętym zakresie obsługi IGate, określanym na przykład liczbą przeskoków DIGI lub odległością;
- nadawca wiadomości nie był niedawno słyszany lokalnie na RF;
- pakiet nadawcy nie zawiera znaczników blokujących takie przekazanie, w szczególności `TCPXX`, `NOGATE` lub `RFONLY`;
- adresat nie był niedawno widziany jako stacja dostępna bezpośrednio przez Internet.

Szczegółowe czasy, zakres radiowy i dodatkowe ograniczenia zależą od konfiguracji IGate. Operator może ponadto zdefiniować własne kryteria przekazywania wybranych pakietów, na przykład określonych obiektów. Sam fakt otrzymania pakietu przez IGate z APRS-IS **nie oznacza** automatycznie nadania go na RF.

## Filtr APRS-IS a ruch dostarczany automatycznie

IGate często łączy się z filtrowanym portem APRS-IS `14580`. Filtr serwerowy określa **dodatkowy strumień danych**, który klient chce otrzymywać. Nie zastępuje podstawowych mechanizmów serwera odpowiedzialnych za komunikację ze stacjami obsługiwanymi przez IGate.

Przykładowy filtr:

```text
filter m/10
```

`m/10` wyznacza obszar o promieniu 10 km wokół ostatniej znanej pozycji znaku, którym klient zalogował się do APRS-IS. Nie jest to promień odbioru radiowego IGate, obszar wokół wszystkich usłyszanych stacji ani bezwzględne ograniczenie danych dostarczanych przez serwer. Jeżeli pozycja znaku logowania nie jest znana serwerowi, filtr nie ma ustalonego punktu odniesienia.

Odpowiednikiem filtra o stałym środku jest na przykład:

```text
filter r/50/19/50
```

Obejmuje on pozycje i obiekty w promieniu 50 km od punktu 50°N, 19°E, a także wiadomości adresowane do stacji znajdujących się w tym obszarze. W obu przypadkach dodatkowa subskrypcja działa **obok** podstawowego strumienia serwera, a nie zamiast niego.

### Co serwer dostarcza niezależnie od dodatkowego filtra?

Dokumentacja filtrowania APRS-IS wskazuje trzy istotne kategorie ruchu dostępne domyślnie na porcie filtrowanym:

- **Wiadomości APRS** adresowane do zalogowanego klienta oraz stacji, których pakiety ten klient przekazał z RF do APRS-IS.
- **Powiązane pozycje nadawców wiadomości**: następny dostępny raport pozycyjny stacji, która wysłała taką wiadomość. Nie oznacza to automatycznego dostarczania całej historii jej pozycji.
- **Pakiety `TCPIP` od stacji przekazanych przez klienta**: ten mechanizm nie wymaga wcześniejszej wiadomości. Może powodować odbiór kolejnych pakietów stacji, której ruch IGate wcześniej wprowadził do APRS-IS, nawet jeśli stacja znajduje się poza promieniem filtra.

Ostatni punkt jest szczególnie istotny przy interpretacji rzeczywistych logów. Nie każdy pakiet odebrany spoza `m/10` jest wiadomością lub pozycją jej nadawcy. Serwer może dostarczać także ruch internetowy stacji powiązanych z danym IGate przez wcześniejsze przekazanie pakietu radiowego.

Filtry włączające są sumowane: pakiet pasujący do dowolnej aktywnej reguły może zostać dostarczony. Filtry wykluczające ograniczają dodatkowe subskrypcje, ale nie wyłączają standardowej obsługi wiadomości. Filtrowanie dotyczy strumienia **od serwera do klienta**. Nie ogranicza pakietów, które IGate wysyła do APRS-IS.

## Jak daleki odbiór radiowy wpływa na strumień APRS-IS?

Zasięg radiowy IGate jest zmienny. Przy podwyższonych warunkach propagacyjnych może on odebrać stację oddaloną o setki kilometrów, bezpośrednio albo za pośrednictwem digipeaterów. W obu przypadkach pakiet może zostać prawidłowo przekazany do APRS-IS. Promień ustawiony w filtrze połączenia internetowego nie ogranicza tego procesu.

Rozważmy IGate z filtrem `m/10`:

1. IGate odbiera na RF pakiet odległej stacji, na przykład dzięki propagacji troposferycznej lub trasie przez DIGI, i przekazuje go do APRS-IS.
2. Serwer uwzględnia tę stację w mechanizmach obsługi ruchu przekazanego przez klienta.
3. Jeśli stacja nada następne pakiety bezpośrednio do APRS-IS, z internetową ścieżką `TCPIP`, serwer może dostarczyć je również temu IGate niezależnie od `m/10`. Nie potrzeba do tego żadnej wiadomości APRS.
4. Jeśli inna stacja wyśle wiadomość do stacji wcześniej przekazanej przez IGate, serwer dostarczy także tę wiadomość oraz następny dostępny raport pozycyjny jej nadawcy. Nadawca wiadomości również może znajdować się daleko poza filtrem.
5. Otrzymanie tych danych z APRS-IS nie oznacza, że IGate automatycznie wyśle je na RF. Kierunek IS do RF podlega odrębnym zasadom.

W efekcie niewielki filtr geograficzny może współistnieć z okresowym napływem pakietów od znacznie dalszych stacji. Nie jest to rozszerzenie promienia `m/10`, lecz równoległe działanie podstawowych funkcji serwera APRS-IS. Zjawisko może być bardziej zauważalne po okresie dalekich odbiorów radiowych, gdy IGate przekaże pakiety stacji, których zwykle nie słyszy.

### Dwa różne przypadki, które łatwo pomylić

**Pakiety stacji wcześniej przekazanej przez IGate.** IGate odbiera na RF stację `SQ9ABC` i przekazuje jej pakiet do APRS-IS. Jeśli `SQ9ABC` następnie wyśle własny pakiet bezpośrednio do APRS-IS jako `TCPIP`, może on wrócić do strumienia tego IGate poza jego filtrem geograficznym. Nie jest do tego potrzebna korespondencja z inną stacją.

**Pakiety związane z wiadomością do stacji wcześniej przekazanej.** IGate odbiera `SQ9ABC` na RF. Stacja `EA1XYZ` wysyła przez APRS-IS wiadomość adresowaną do `SQ9ABC`. Serwer dostarcza wiadomość do IGate i przekazuje mu następny dostępny pakiet pozycyjny `EA1XYZ`, mimo że nadawca wiadomości może znajdować się daleko poza obszarem filtra.

Pierwszy mechanizm dotyczy pakietów `TCPIP` stacji wcześniej przekazanej przez klienta. Drugi dotyczy wiadomości do tej stacji oraz pozycji **nadawcy wiadomości**. Rozróżnienie wyjaśnia, dlaczego poza filtrem mogą pojawiać się również pakiety, których nie poprzedza widoczna korespondencja APRS.

### Jak interpretować takie pakiety w praktyce?

W strumieniu APRS-IS mogą jednocześnie występować pakiety dobrane przez `m/10`, pakiety `TCPIP` stacji wcześniej przekazanych przez IGate, wiadomości wraz z powiązanymi pozycjami oraz dane dopuszczone przez inne aktywne reguły. Te mechanizmy działają równolegle.

Nagłówek w rodzaju:

```text
SQ9ABC>APRS,TCPIP*,qAC,T2SERVER:!5000.00N/01900.00E-
```

informuje o sposobie wejścia pakietu do APRS-IS. Sam `qAC` **nie wskazuje**, dlaczego konkretny serwer dostarczył go konkretnemu klientowi. Nie pozwala też na podstawie pojedynczego wpisu rozstrzygnąć, czy pakiet trafił do klienta dzięki filtrowi geograficznemu, wcześniejszemu przekazaniu stacji przez IGate, czy innej regule.

Jeżeli w logu obok odległych pozycji występują również statusy, telemetria i obiekty, nie należy automatycznie przypisywać ich wszystkich obsłudze wiadomości. Mogą to być między innymi pakiety `TCPIP` stacji przekazanych wcześniej przez IGate lub dane dopuszczone przez pozostałe aktywne filtry. Dokumentacja nie daje podstaw do twierdzenia, że samo jednorazowe usłyszenie stacji powoduje bezwarunkowe przesyłanie całego ruchu jej otoczenia przez określony czas.

Z punktu widzenia operatora najważniejsza zasada jest prosta: **filtr geograficzny określa dodatkowo zamówiony ruch, natomiast serwer nadal obsługuje stacje, których pakiety IGate wprowadził do APRS-IS**. Właśnie dlatego odbieranie pakietów spoza `m/10` może być prawidłowym zachowaniem sieci.

## Format third-party przy przekazywaniu na RF

Pakiet odebrany z APRS-IS nie może zostać nadany na RF wraz z internetową ścieżką i q construct. IGate stosuje format third-party, czyli zewnętrzną ramkę radiową zawierającą oryginalny pakiet jako dane.

Schemat:

```text
IGATECALL>APRS,GATEPATH:}FROMCALL>TOCALL,TCPIP,IGATECALL*:oryginalne_dane
```

Wewnętrzny nagłówek `TCPIP,IGATECALL*` identyfikuje pochodzenie pakietu. Przed nadaniem należy usunąć z niego ścieżkę APRS-IS. Dzięki temu inny IGate, który odbierze tę transmisję radiową, rozpozna pakiet pochodzący z Internetu i nie wprowadzi go ponownie do APRS-IS.

Znacznik `}` rozpoczyna wewnętrzny pakiet third-party. Nie należy mylić go z q construct, który istnieje wyłącznie po stronie internetowej.

## Dokumentacja

- [APRS-IS: IGate Details](https://www.aprs-is.net/IGateDetails.aspx) - kryteria przekazywania pakietów i format third-party.
- [APRS-IS: q Construct](https://www.aprs-is.net/q.aspx) - znaczenie i zastosowanie konstrukcji q.
- [APRS-IS: Server Design](https://www.aprs-is.net/ServerDesign.aspx) - podstawowe zasady serwera, w tym obowiązkowa obsługa wiadomości.
- [APRS-IS: Server-side Filter Commands](https://www.aprs-is.net/javAPRSFilter.aspx) - działanie filtrów i pakiety dostarczane niezależnie od nich.
