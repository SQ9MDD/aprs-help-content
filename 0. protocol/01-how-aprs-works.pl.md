---
title: Jak działa APRS?
description: Jak pakiety APRS rozchodzą się przez radio, digipeatery, IGate i APRS-IS.
template: doc
tableOfContents: true
---

APRS jest siecią rozgłoszeniową. Stacja nadaje pakiet w eter, a każdy odbiornik w jej zasięgu może go odebrać i wykorzystać niezależnie. Nie ma centralnego węzła ani obowiązkowej trasy: pakiet może pozostać lokalny, zostać retransmitowany przez digipeater, trafić do Internetu przez IGate — albo zrobić kilka z tych rzeczy równocześnie.

## Od stacji do stacji: APRS działa przez radio

Najprostszy przypadek nie wymaga ani Internetu, ani infrastruktury. Stacja mobilna nadaje pakiet, a stacja domowa odbiera go bezpośrednio przez RF.

![Bezpośredni odbiór pakietu APRS przez radio](./_img/diagram1.png)

Odebrany pakiet może zawierać na przykład pozycję, status, wiadomość, dane pogodowe lub telemetrię. Jeśli odbiornik rozumie jego format, informacja jest od razu użyteczna. To kompletny, poprawnie działający APRS.

W warstwie radiowej dane APRS są zwykle przenoszone w ramce **AX.25 UI** (*Unnumbered Information*). Jej zapis tekstowy może wyglądać tak:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Znak źródłowy, adres docelowy, ścieżka i pole informacji opisują ramkę oraz zawartość APRS. Budowę pakietu omawiają dalsze artykuły; tutaj istotne jest, co dzieje się z nim po nadaniu.

## Jedna transmisja, wielu odbiorców

RF jest medium współdzielonym. Ta sama emisja może być odebrana jednocześnie przez stację użytkownika, digipeater i IGate.

![Jedna transmisja APRS odbierana przez stację, digipeater i IGate](./_img/diagram2.png)

Odbiór nie tworzy kolejki ani łańcucha przekazywania. Każdy odbiorca podejmuje własną decyzję: wyświetla dane, retransmituje pakiet przez radio lub przekazuje go do APRS-IS. To właśnie dlatego w APRS normalne są różne drogi dojścia tej samej informacji.

## Digipeater: zasięg po stronie RF

**Digipeater** odbiera pakiet radiowy i — jeśli pozwala na to ścieżka oraz jego konfiguracja — nadaje go ponownie. Dzięki temu informacja może dotrzeć poza bezpośredni zasięg stacji źródłowej.

![Retransmisja pakietu APRS przez digipeater](./_img/diagram3.png)

Digipeater nie powinien powtarzać wszystkiego. Decyzja zależy m.in. od adresów w ścieżce, lokalnej polityki pracy sieci oraz ochrony przed duplikatami. W praktyce stosuje się ścieżki takie jak `WIDE1-1` i `WIDE2-n`; ich semantyka i zasady konfiguracji są opisane osobno.

Funkcję digipeatera można streścić jako:

```text
RF → RF
```

Nie oznacza ona automatycznie dostępu do Internetu.

## IGate i APRS-IS: granica między RF a Internetem

**IGate** (*Internet Gateway*) słyszy lokalny ruch RF i przekazuje wybrane pakiety do **APRS-IS** — globalnej sieci serwerów dystrybuującej dane APRS. Dzięki temu pakiety odebrane lokalnie są dostępne dla aplikacji, map i usług monitorujących.

![Przekazanie danych z sieci RF przez IGate do APRS-IS](./_img/diagram4.png)

Podstawowy kierunek pracy IGate to:

```text
RF → APRS-IS
```

IGate może działać bez funkcji digipeatera, a digipeater bez IGate. Jedna stacja może oczywiście realizować obie funkcje, lecz są to dwa niezależne zadania:

| Element | Zadanie | Kierunek podstawowy |
| --- | --- | --- |
| Digipeater | Zwiększa zasięg lokalnej sieci radiowej | `RF → RF` |
| IGate | Łączy lokalne RF z APRS-IS | `RF → APRS-IS` |
| APRS-IS | Dystrybuuje pakiety przez Internet | Internet |

APRS-IS rozszerza zasięg informacji, ale nie zastępuje kanału radiowego. Jeżeli pakiet nie pojawi się w serwisie internetowym, mógł mimo to zostać poprawnie odebrany i wykorzystany lokalnie.

## Dlaczego ta sama ramka pojawia się kilka razy

W realnej sieci pierwotną transmisję i jej retransmisję może usłyszeć wiele IGate. Każdy z nich może przekazać ramkę do APRS-IS.

![Wiele dróg odbioru tej samej transmisji APRS](./_img/diagram5.png)

Nie jest to błąd transmisji, tylko konsekwencja rozgłoszeniowego charakteru RF. Digipeatery, IGate i serwery APRS-IS stosują rozpoznawanie duplikatów, aby nie mnożyć dalej tej samej ramki. Szczegóły zależą od implementacji i konfiguracji węzła.

## Z Internetu z powrotem na radio

Kierunek `APRS-IS → RF` jest celowo ograniczany. Kanał radiowy ma małą przepustowość i jest wspólny dla wszystkich stacji, dlatego IGate nie może traktować go jako pełnej kopii APRS-IS.

Typowy, kontrolowany przypadek to wiadomość adresowana do lokalnej stacji, którą IGate niedawno słyszał na RF. Przy przekazywaniu określonego ruchu z APRS-IS na radio może być używany mechanizm **third-party traffic**, zachowujący informację o pochodzeniu pakietu. Reguły gatingu, q-constructs i format third-party traffic wymagają osobnego omówienia.

## Pełny obraz

Poniższy schemat pokazuje współistnienie wszystkich ról. Połączenia RF rozchodzą się lokalnie; IGate przekazuje dane między lokalnym radiem a APRS-IS; aplikacje i usługi korzystają z danych dostępnych w Internecie.

![Przepływ danych między stacjami RF, digipeaterami, IGate i APRS-IS](./_img/diagram6.png)

Z perspektywy pojedynczego pakietu możliwe są więc równolegle trzy efekty:

- lokalny odbiór przez inne stacje,
- zwiększenie zasięgu przez retransmisję digi,
- publikacja do APRS-IS przez jeden lub więcej IGate.

Żaden z nich nie jest wymagany, aby zaistniał pozostały. APRS lokalne może działać bez Internetu, a IGate może przekazać pakiet do APRS-IS bez udziału digipeatera.

## Najważniejsze wnioski

- APRS nie jest pojedynczą trasą `stacja → digipeater → IGate → Internet`.
- Jedna transmisja RF może być użyteczna dla wielu odbiorców i dotrzeć do nich różnymi drogami.
- Digipeater retransmituje ruch radiowy; IGate łączy RF z APRS-IS.
- Duplikaty są naturalne w sieci rozgłoszeniowej i są odfiltrowywane przez jej elementy.
- Internet zwiększa dostępność danych, lecz nie jest warunkiem działania lokalnego APRS.
- Ruch z Internetu na RF musi być selekcjonowany, aby nie obciążać wspólnego kanału.

## Dalej

Kolejnym krokiem jest poznanie budowy ramki AX.25 i pakietu APRS, adresów źródłowych oraz SSID, pola destination/TOCALL, ścieżek digipeaterów i różnic między ruchem RF a APRS-IS.
