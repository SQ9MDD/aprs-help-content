---
title: "Słownik pojęć i skrótów"
description: "Podstawowe terminy radiokomunikacyjne, APRS i transmisji pakietowej, wyjaśnione dla osób rozpoczynających pracę z APRS."
sidebar:
  order: 0
---

Dokumentacja APRS wykorzystuje pojęcia z radiokomunikacji, informatyki i transmisji pakietowej. Ten słownik wyjaśnia terminy spotykane podczas czytania artykułów, konfiguracji stacji i analizowania ruchu. Hasła podzielono tematycznie. Skróty zachowują oryginalne rozwinięcia, a definicje opisują ich znaczenie w praktyce.

## 1. Podstawy radiokomunikacji

**Antena** - element służący do wypromieniowywania i odbierania fal radiowych. Jej konstrukcja, umiejscowienie i charakterystyka wpływają na skuteczność łączności.

**CTCSS (Continuous Tone-Coded Squelch System)** - system selektywnego otwierania blokady szumów za pomocą ciągłego tonu o niskiej częstotliwości, przesyłanego wraz z sygnałem. Nie zapewnia poufności transmisji.

**Częstotliwość** - liczba cykli fali w ciągu sekundy, wyrażana w hercach (Hz). W Polsce klasyczny APRS na paśmie 2 m pracuje zwykle na częstotliwości 144,800 MHz.

**DCS (Digital-Coded Squelch)** - system selektywnego otwierania blokady szumów za pomocą przesyłanego kodu cyfrowego.

**Duplex** - sposób pracy pozwalający nadawać i odbierać dwoma oddzielnymi torami. Pełny duplex umożliwia jednoczesne nadawanie i odbiór; półduplex wymaga pracy naprzemiennej.

**FM (Frequency Modulation)** - modulacja częstotliwości, w której informacja zmienia chwilową częstotliwość fali nośnej. Stosowana w analogowych radiotelefonach, również podczas przesyłania sygnału AFSK.

**Kanał radiowy** - określona częstotliwość lub zestaw parametrów pracy radia, obejmujący na przykład częstotliwość, rodzaj emisji i ustawienia dodatkowe.

**Modulacja** - zmiana wybranego parametru sygnału nośnego w celu przeniesienia informacji.

**Moc nadajnika** - moc sygnału dostarczanego przez nadajnik, zwykle podawana w watach (W). Sama moc nie określa zasięgu stacji.

**Przemiennik radiowy** - stacja odbierająca i ponownie nadająca sygnał w celu rozszerzenia zasięgu. Przemienniki głosowe często wykorzystują osobne częstotliwości odbioru i nadawania.

**Propagacja** - rozchodzenie się fal radiowych. Na zasięg i sposób docierania sygnału wpływają między innymi częstotliwość, teren, anteny i warunki atmosferyczne. Przy sprzyjającej propagacji można odebrać odległe stacje APRS.

**PTT (Push To Talk)** - przycisk lub sygnał sterujący przełączający radiotelefon na nadawanie. W stacji komputerowej PTT może być realizowane przez interfejs sprzętowy.

**RF (Radio Frequency)** - częstotliwość radiowa. W opisach APRS określenie „sieć RF” oznacza radiową część systemu, w odróżnieniu od APRS-IS.

**RX (Receive)** - odbiór; oznaczenie odbiornika, toru odbiorczego lub operacji odbierania.

**Simplex** - w ścisłym znaczeniu transmisja jednokierunkowa. W praktyce krótkofalarskiej terminem „łączność simplex” nazywa się też bezpośrednią, naprzemienną komunikację na tej samej częstotliwości, bez przemiennika.

**Squelch** - blokada szumów wyciszająca wyjście audio odbiornika, gdy sygnał nie spełnia ustawionych warunków. Zbyt wysoki próg może utrudniać odbiór pakietów.

**TX (Transmit)** - nadawanie; oznaczenie nadajnika, toru nadawczego lub operacji wysyłania.

**UHF (Ultra High Frequency)** - zakres częstotliwości od 300 MHz do 3 GHz, obejmujący między innymi amatorskie pasmo 70 cm.

**VHF (Very High Frequency)** - zakres częstotliwości od 30 do 300 MHz, obejmujący między innymi amatorskie pasmo 2 m.

**VOX (Voice Operated Exchange)** - układ automatycznie uruchamiający nadawanie po wykryciu odpowiedniego sygnału audio. Przy transmisji danych wymaga uwzględnienia czasu reakcji.

**Zasięg radiowy** - obszar, w którym możliwy jest odbiór danej stacji. Zależy od anten, mocy, terenu, zakłóceń i warunków propagacyjnych.

## 2. Sprzęt i interfejsy

**CAT (Computer Aided Transceiver)** - sterowanie radiotelefonem z komputera, na przykład zmianą częstotliwości lub odczytem parametrów. Zakres funkcji zależy od radia.

**DTR (Data Terminal Ready)** - sygnał sterujący interfejsu szeregowego, który przez odpowiedni układ może służyć do sterowania PTT.

**GNSS (Global Navigation Satellite System)** - ogólna nazwa satelitarnych systemów nawigacyjnych, wykorzystywanych między innymi do określania pozycji trackera APRS.

**GPS (Global Positioning System)** - jeden z systemów GNSS. W języku potocznym określenie „GPS” bywa używane także wobec odbiorników korzystających z kilku systemów nawigacyjnych.

**Interfejs** - sposób lub układ połączenia współpracujących urządzeń. Interfejs radiowy może przenosić audio między komputerem i radiotelefonem oraz obsługiwać PTT.

**Karta dźwiękowa** - urządzenie przetwarzające sygnały między postacią analogową i cyfrową. W połączeniu z modemem programowym umożliwia nadawanie i odbiór AFSK.

**Modem (Modulator-Demodulator)** - urządzenie lub program zamieniający dane na sygnał odpowiedni dla medium transmisyjnego i odwrotnie. W klasycznym APRS modem AFSK przetwarza dane na sygnały audio i dekoduje odebrane audio.

**Port szeregowy** - interfejs przesyłający dane kolejno, bit po bicie. Może służyć do komunikacji z TNC, odbiornikiem GNSS lub układem sterowania.

**Radiotelefon** - urządzenie łączące nadajnik i odbiornik radiowy. Nie każdy radiotelefon ma wbudowany modem lub TNC.

**RTS (Request To Send)** - sygnał sterujący interfejsu szeregowego, często wykorzystywany przez odpowiedni układ do sterowania PTT.

**SDR (Software Defined Radio)** - radio programowe, w którym część funkcji odbiornika lub nadajnika realizuje oprogramowanie przetwarzające sygnał.

**Terminal** - program lub urządzenie umożliwiające wymianę danych z innym systemem. W radiokomunikacji pakietowej może współpracować z TNC.

**TNC (Terminal Node Controller)** - kontroler komunikacji pakietowej, sprzętowy lub programowy. W typowej konfiguracji AX.25 obsługuje ramki i współpracuje z modemem. Nie należy utożsamiać każdego modemu z kompletnym TNC.

**UART (Universal Asynchronous Receiver-Transmitter)** - układ realizujący asynchroniczną komunikację szeregową, często obecny w mikrokontrolerach.

**USB (Universal Serial Bus)** - interfejs służący między innymi do podłączania kart dźwiękowych, przejściówek szeregowych, odbiorników GNSS i radiotelefonów.

## 3. Podstawowe pojęcia APRS

**APRS (Automatic Packet Reporting System)** - system automatycznej wymiany informacji za pomocą transmisji pakietowej. Obsługuje między innymi pozycje, wiadomości, obiekty, pogodę i telemetrię.

**Beacon** - pakiet informacyjny nadawany zwykle automatycznie, na przykład z pozycją lub statusem stacji. Nie oznacza jednego, odrębnego typu ramki APRS.

**Biuletyn (Bulletin)** - komunikat APRS przeznaczony dla wielu odbiorców, przesyłany w formacie wiadomości APRS.

**Komentarz** - dodatkowy tekst dołączany do wybranych raportów APRS, na przykład raportu pozycji.

**Obiekt (Object)** - nazwana informacja APRS, zwykle opisująca punkt lub zdarzenie, publikowana przez inną stację. Może reprezentować na przykład przemiennik albo miejsce wydarzenia.

**Element (Item)** - uproszczony format nazwanej informacji APRS, odmienny od formatu obiektu.

**Pakiet** - porcja danych przesyłana w sieci. W rozmowach o APRS termin bywa używany zamiennie z „ramką”, choć technicznie znaczenie zależy od warstwy protokołu.

**Raport pozycji** - dane APRS zawierające współrzędne geograficzne, a zależnie od formatu także czas, symbol, kierunek, prędkość, wysokość lub komentarz.

**SSID (Secondary Station Identifier)** - dodatkowy identyfikator w adresie AX.25, przyjmujący wartości 0-15. Pozwala odróżnić stacje wykorzystujące ten sam znak wywoławczy. Nie każdy tekstowy sufiks spotykany w APRS-IS jest SSID AX.25.

**Stacja APRS** - urządzenie lub aplikacja uczestnicząca w wymianie informacji APRS, na przykład tracker, stacja domowa, DIGI lub IGate.

**Status** - tekstowa informacja o stanie lub aktywności stacji, przesyłana w przewidzianym do tego formacie APRS.

**Symbol APRS** - graficzne oznaczenie stacji lub obiektu, określone w danych APRS i prezentowane na mapach.

**Telemetria** - zdalnie przesyłane pomiary lub stany urządzenia, na przykład napięcie, temperatura lub sygnały cyfrowe.

**Tracker** - urządzenie lub aplikacja automatycznie publikująca pozycję, zwykle wyznaczaną przez odbiornik GNSS.

**Wiadomość APRS** - krótki komunikat tekstowy przesyłany w określonym formacie APRS. Wiadomości adresowane mogą korzystać z identyfikatorów i potwierdzeń odbioru.

**Znak wywoławczy (Callsign)** - identyfikator stacji radiowej przydzielany zgodnie z odpowiednimi przepisami; w amatorskim APRS stanowi podstawę adresowania.

## 4. Elementy sieci APRS

**APRS-IS (APRS Internet System)** - internetowa infrastruktura wymiany danych APRS między klientami, bramkami IGate i serwerami.

**DIGI (Digipeater, Digital Repeater)** - cyfrowa stacja retransmisyjna, która odbiera pakiety radiowe i ponownie nadaje je zgodnie z regułami ścieżki i konfiguracją.

**Duplikat pakietu** - kolejna kopia już odebranego pakietu. Może powstać, gdy tę samą transmisję odbierze i przekaże więcej niż jedna stacja.

**Hop** - pojedynczy etap przekazania pakietu. W radiowym APRS zwykle oznacza retransmisję przez jeden digipeater.

**IGate (Internet Gateway)** - bramka między radiową siecią APRS i APRS-IS. Przekazuje odebrane pakiety radiowe do internetu; bramka dwukierunkowa może także przekazywać wybrane dane z APRS-IS do RF.

**Klient APRS** - aplikacja lub urządzenie odbierające, prezentujące lub wysyłające dane APRS za pośrednictwem obsługiwanego medium.

**Retransmisja** - ponowne nadanie pakietu, na przykład przez digipeater, zgodnie z regułami przekazywania.

**Serwer APRS-IS** - serwer dystrybuujący dane APRS przez internet, obsługujący klientów i, zależnie od roli, połączenia z innymi serwerami.

**Ścieżka APRS (Path)** - pole adresowe wskazujące stacje lub aliasy uczestniczące w radiowym przekazywaniu pakietu.

**WIDE1-1** - popularny alias ścieżki, umożliwiający jedną retransmisję przez odpowiednio skonfigurowany digipeater.

**WIDE2-2** - alias WIDEn-N, którego początkowy licznik dopuszcza dwa etapy retransmisji przez zgodne digipeatery. Nie gwarantuje, że pakiet rzeczywiście zostanie dwukrotnie powtórzony.

## 5. Transmisja danych i protokoły

**AFSK (Audio Frequency-Shift Keying)** - reprezentacja danych przez zmianę częstotliwości sygnału audio. Klasyczny APRS 1200 baud wykorzystuje AFSK zgodny z Bell 202.

**ALOHA** - metoda dostępu do wspólnego medium, w której stacje podejmują transmisję bez centralnego przydziału czasu. W radiowym APRS współdzielenie kanału i brak gwarancji dostarczenia powodują możliwość kolizji.

**AX.25** - protokół transmisji pakietowej dla radiokomunikacji amatorskiej, definiujący między innymi adresowanie i strukturę ramek. APRS korzysta przede wszystkim z ramek UI.

**Baud** - jednostka szybkości modulacji oznaczająca liczbę symboli na sekundę. Nie zawsze odpowiada liczbie bitów na sekundę.

**Bit/s (bps)** - liczba bitów przesyłanych w ciągu sekundy.

**CRC (Cyclic Redundancy Check)** - metoda obliczania wartości kontrolnej umożliwiającej wykrywanie błędów w przesyłanych danych.

**DTI (Data Type Identifier)** - identyfikator typu danych, zwykle pierwszy znak pola informacyjnego APRS, wskazujący sposób interpretacji zawartości.

**FCS (Frame Check Sequence)** - sekwencja kontrolna ramki. W AX.25 wykorzystuje CRC do wykrywania błędów transmisji.

**FEC (Forward Error Correction)** - korekcja błędów na podstawie dodatkowych danych przesyłanych razem z informacją, bez konieczności ponownego nadawania.

**FSK (Frequency-Shift Keying)** - modulacja, w której symbole są reprezentowane przez różne częstotliwości sygnału.

**FX.25** - rozszerzenie AX.25 dodające mechanizm FEC. Pozwala odzyskać część uszkodzonych transmisji, jeśli odbiornik obsługuje FX.25.

**KISS (Keep It Simple, Stupid)** - prosty protokół komunikacji między aplikacją a TNC, służący do przesyłania ramek i wybranych poleceń sterujących. Sam interfejs KISS nie gwarantuje pełnej obsługi AX.25 przez urządzenie.

**Mic-E** - zwarty format APRS, w którym część informacji o pozycji i stanie jest kodowana również w polu adresowym AX.25.

**Payload** - dane użytkowe przenoszone w określonej warstwie protokołu. W opisie APRS często oznacza zawartość pola informacyjnego ramki.

**Ramka** - jednostka danych warstwy łącza. Ramka AX.25 zawiera między innymi adresy, pole sterujące, pole informacyjne i sekwencję kontrolną.

**TCP/IP** - rodzina protokołów sieciowych wykorzystywana między innymi do komunikacji klientów z APRS-IS.

**TOCALL** - potoczna nazwa pola adresu docelowego APRS, którego wartości często identyfikują oprogramowanie lub urządzenie nadające pakiet. Nie każda wartość adresu docelowego jest identyfikatorem produktu.

**UI Frame (Unnumbered Information Frame)** - ramka AX.25 służąca do przesyłania danych bez zestawiania połączenia i bez potwierdzania każdej ramki na poziomie łącza. Jest podstawą klasycznego APRS.

## 6. Obsługa i konfiguracja stacji

**APRS Passcode** - kod stosowany w tradycyjnym mechanizmie logowania do APRS-IS. Jest wyliczany ze znaku wywoławczego i nie stanowi silnego zabezpieczenia kryptograficznego.

**DCD (Data Carrier Detect)** - sygnał lub mechanizm wykrywania obecności transmisji danych, wykorzystywany między innymi do oceny zajętości kanału.

**Filtr APRS-IS** - zestaw reguł ograniczających dane dostarczane klientowi, na przykład według lokalizacji lub znaków wywoławczych.

**Host** - komputer lub urządzenie udostępniające usługę, na przykład serwer KISS TCP.

**KISS Serial** - przesyłanie ramek i poleceń KISS przez interfejs szeregowy.

**KISS TCP** - przesyłanie danych KISS przez połączenie TCP, umożliwiające współpracę aplikacji z TNC przez sieć komputerową.

**Port TCP** - numer identyfikujący usługę TCP na danym urządzeniu. Numer portu zależy od konfiguracji usługi.

**Proportional Pathing** - metoda nadawania kolejnych raportów pozycji z różnymi ścieżkami, w której dalsze retransmisje są wykorzystywane rzadziej, aby ograniczyć obciążenie sieci.

**q-construct** - specjalny element dodawany do tekstowej reprezentacji pakietu w APRS-IS. Wskazuje informacje o sposobie wprowadzenia lub przekazywania pakietu w sieci internetowej. Nie jest radiową ścieżką retransmisji AX.25.

**SmartBeaconing** - metoda dostosowywania częstości raportowania pozycji do ruchu stacji, zwłaszcza prędkości i zmian kierunku.

**TX Delay** - czas przeznaczony na przygotowanie nadajnika i toru odbiorczego drugiej stacji przed przesłaniem właściwych danych ramki. Dokładne znaczenie ustawienia zależy od modemu lub TNC.

**TX Tail** - dodatkowy czas utrzymywania nadawania po zakończeniu właściwych danych, jeśli jest przewidziany przez dany modem lub TNC.

## 7. Diagnostyka i eksploatacja

**Bufor** - obszar pamięci tymczasowo przechowujący dane przed dalszym przetwarzaniem lub transmisją.

**Duplikaty** - wielokrotne kopie tej samej informacji. W diagnostyce należy odróżniać wielokrotny odbiór jednej transmisji od ponownego nadania pakietu przez stację źródłową.

**Kolizja pakietów** - nakładanie się transmisji w sposób uniemożliwiający lub utrudniający poprawne dekodowanie.

**Kolejka** - mechanizm przechowujący pakiety oczekujące na przetworzenie albo nadawanie.

**Log** - chronologiczny zapis zdarzeń, wykorzystywany do analizy działania stacji i diagnozowania problemów.

**Monitor pakietów** - narzędzie prezentujące odbierane lub wysyłane ramki, ich adresy, ścieżki i zawartość.

**Opóźnienie (Latency)** - czas między określonymi etapami przetwarzania lub przesyłania pakietu.

**Poziom audio** - poziom sygnału dostarczanego do modemu lub nadajnika. Zbyt niski lub zbyt wysoki może powodować problemy z dekodowaniem.

**Przesterowanie** - zniekształcenie sygnału wskutek przekroczenia dopuszczalnego poziomu w torze przetwarzania.

**RSSI (Received Signal Strength Indicator)** - wskaźnik poziomu odbieranego sygnału radiowego. Jego skala i sposób pomiaru zależą od urządzenia.

**SNR (Signal-to-Noise Ratio)** - stosunek mocy sygnału użytecznego do mocy szumu, zwykle podawany w decybelach.

**Zajętość kanału** - stan, w którym kanał jest wykorzystywany przez trwającą transmisję. TNC może wykrywać zajętość przed rozpoczęciem nadawania.

## 8. Pojęcia, których nie należy mylić

**Modem i TNC** - modem przekształca sygnały na dane i odwrotnie. TNC obsługuje komunikację pakietową, na przykład ramki AX.25, i może zawierać modem lub z nim współpracować.

**DIGI i IGate** - DIGI retransmituje pakiety drogą radiową. IGate łączy sieć radiową z APRS-IS. Jedno urządzenie może pełnić obie funkcje, ale są to odrębne role.

**Baud i bit/s** - baud określa liczbę symboli na sekundę, a bit/s liczbę bitów na sekundę. Przy jednym bicie na symbol wartości liczbowe mogą być równe.

**GPS i GNSS** - GPS jest jednym z systemów GNSS. Odbiornik wielosystemowy może korzystać również z innych konstelacji satelitarnych.

**SSID i sufiks tekstowy** - SSID AX.25 ma zakres 0-15. Inne sufiksy występujące w tekstowych identyfikatorach internetowych nie stają się przez to SSID AX.25 i nie muszą nadawać się do przekazania na RF.

**APRS i APRS-IS** - APRS określa system wymiany informacji, który może korzystać z różnych mediów. APRS-IS jest jego internetową infrastrukturą dystrybucji danych.
