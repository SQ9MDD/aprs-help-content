---
title: "FoxTrak-M"
slug: "foxtrak-m"
type: "hardware"
category: "tracker"
manufacturer: "Fox Delta"
status: "legacy"
protocols:
  - "APRS"
  - "AX.25"
  - "NMEA 0183"
tags:
  - "tracker"
  - "GPS"
  - "1200-baud"
  - "Bell-202"
  - "PIC"
source:
  - "https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf"
---

## 1. Podstawowe informacje

**FoxTrak-M** to kompaktowy moduł trackera APRS firmy Fox Delta. Został zaprojektowany jako niewielki moduł do wbudowania we własne urządzenie lub projekt, zamiast jako kompletne urządzenie w osobnej obudowie.

Konstrukcja bazuje na mikrokontrolerach **PIC16F84A lub PIC16F628A**. Płytka ma około **7 × 4 cm** i wykorzystuje złącza goldpin 0,1 cala.

FoxTrak-M jest odmianą wcześniejszego trackera FoxTrak. Główna różnica konstrukcyjna polega na zastąpieniu złączy D-Sub złączami pinowymi, dzięki czemu moduł łatwiej zintegrować z innym urządzeniem.

## 2. Przeznaczenie

FoxTrak-M jest przeznaczony przede wszystkim do:

- automatycznego wysyłania pozycji stacji przez APRS,
- budowy mobilnego trackera GPS,
- integracji trackera APRS jako modułu we własnym urządzeniu,
- współpracy z terminalem FoxView-2,
- eksperymentów z własnym firmware dla mikrokontrolera PIC.

Urządzenie jest trackerem nadawczym. Nie jest pełnym terminalem APRS i nie dekoduje odebranych ramek APRS.

## 3. Sposób działania

Tracker odbiera z GPS dane pozycyjne w formacie **NMEA**, w szczególności zdania:

```text
$GPRMC
```

Na ich podstawie generuje sygnał packet radio **1200 bps**, który może zostać podany bezpośrednio na wejście mikrofonowe nadajnika.

Do podstawowej pracy nie jest wymagany zewnętrzny TNC.

Automatyczne beacony są wysyłane tylko wtedy, gdy urządzenie otrzymuje z GPS poprawne dane pozycyjne.

## 4. APRS i obsługiwane funkcje

FoxTrak-M realizuje podstawową funkcję trackera APRS, czyli okresowe nadawanie raportu pozycji.

Firmware DK7IN obsługuje między innymi:

- stały interwał beaconów,
- zmianę interwału beaconów zależnie od prędkości,
- dodatkowy beacon po dużej zmianie kierunku jazdy,
- konfigurowalny TX Delay,
- konfigurację znaku wywoławczego i innych parametrów APRS,
- generowanie tonów testowych do regulacji toru audio.

Urządzenie nie odbiera ani nie interpretuje ruchu APRS z kanału radiowego.

## 5. Smart Beaconing

FoxTrak-M implementuje wczesną formę adaptacyjnego beaconingu. Częstotliwość wysyłania pozycji może zmieniać się zależnie od prędkości.

Dostępne są dwa zestawy parametrów, wybierane przełącznikiem.

| Prędkość | Profil szybszy | Profil wolniejszy |
|---|---:|---:|
| < 4 km/h | 25 min | 30 min |
| > 4 km/h | 4 min | 5 min |
| > 11 km/h | 120 s | 160 s |
| > 24 km/h | 60 s | 80 s |
| > 50 km/h | 30 s | 40 s |
| > 100 km/h | 20 s | 30 s |
| > 150 km/h | 10 s | 20 s |

Pod uwagę brana jest maksymalna prędkość od czasu wysłania poprzedniego beaconu.

Jeżeli prędkość przekracza **15 km/h**, urządzenie może również wysłać beacon po znaczącej zmianie kierunku:

- ponad **40°** w profilu szybszym,
- ponad **60°** w profilu wolniejszym.

Jeśli poprzednia transmisja nastąpiła mniej niż 10 sekund wcześniej, kolejny beacon zostanie opóźniony.

## 6. Interfejs GPS i konfiguracja

Ten sam interfejs szeregowy służy do:

- odbierania danych z odbiornika GPS,
- konfiguracji parametrów zapisanych w pamięci PIC.

Dokumentacja opisuje konfiguratory dla:

- DOS,
- Windows,
- Linux.

Możliwe było również użycie programu konfiguracyjnego TinyTrak.

Przykładowa wersja firmware identyfikowała się jako:

```text
DK7IN V.1.6
```

Stały interwał beaconów można ustawiać z krokiem 10 sekund, a TX Delay z rozdzielczością około 6,6 ms.

## 7. Tor radiowy

FoxTrak-M generuje modulację AFSK zgodną z używanym w packet radio systemem **Bell 202**.

Nominalne częstotliwości tonów to:

- **1200 Hz**
- **2200 Hz**

Do regulacji i diagnostyki urządzenie może generować tony testowe.

Przykładowe polecenia terminalowe:

```text
ESC T 0
```

generuje ton 1200 Hz.

```text
ESC T 1
```

generuje ton 2200 Hz.

```text
ESC T M
```

generuje sygnał mieszany 1200/2200 Hz.

Komunikacja konfiguracyjna opisana w instrukcji używa parametrów:

```text
4800 baud, 8N1
```

## 8. Połączenie z radiem

Złącze radiowe udostępnia między innymi:

| Sygnał | Funkcja |
|---|---|
| Audio In | audio z odbiornika do trackera |
| CD | Carrier Detect, jeśli dostępny |
| GND | masa |
| SW1 | wejście przycisku natychmiastowej transmisji |
| Audio Out | audio trackera do wejścia mikrofonowego radia |
| +5V | zasilanie modułu |

Moduł posiada regulację poziomu audio do i z radiotelefonu.

Dostępna jest również opcja dostosowania sterowania PTT do niektórych radiotelefonów przenośnych przez dołączenie rezystora 2,2 kΩ.

## 9. Złącze GPS / PC

Drugie 8-pinowe złącze 0,1 cala służy do komunikacji z GPS lub komputerem.

Dokumentacja wymienia następujące sygnały:

| Sygnał | Funkcja |
|---|---|
| GND | masa |
| S0 | dane szeregowe OUT |
| SI | dane szeregowe IN |
| PI | PTT IN |
| PO | PTT OUT |
| A | niewykorzystywane w FoxTrak-M |
| B | niewykorzystywane w FoxTrak-M |

## 10. Sygnalizacja i elementy sterujące

FoxTrak-M posiada diody LED sygnalizujące stan urządzenia.

**CD**

Świeci, gdy wykrywana jest transmisja innej stacji.

**GPS**

Świeci przy poprawnych danych pozycyjnych z GPS. Miga, jeśli dane GPS są odbierane, ale pozycja nie jest prawidłowa.

**PTT**

Świeci podczas załączenia nadajnika.

Podczas uruchamiania diody wykonują sekwencję inicjalizacyjną, po której może zostać wysłany beacon.

## 11. Zasilanie

FoxTrak-M wymaga zewnętrznego zasilania:

```text
+5 V DC
```

Moduł nie posiada własnego stabilizatora 5 V.

Nie zapewnia również zasilania dla odbiornika GPS, dlatego GPS musi mieć osobne źródło zasilania.

## 12. Firmware

Podstawowe firmware opisane w dokumentacji zostało opracowane przez **DK7IN**.

Konstrukcja sprzętowa pozwala na zmianę firmware, a w razie potrzeby także zastąpienie PIC16F84A mikrokontrolerem PIC16F628A.

FoxTrak-M był więc projektowany nie tylko jako gotowy tracker, ale również jako platforma do eksperymentów z własnym oprogramowaniem APRS.

## 13. Ograniczenia

Najważniejsze ograniczenia wynikające z dokumentacji:

- brak dekodowania odebranych ramek APRS,
- brak funkcji pełnego TNC,
- urządzenie koncentruje się na transmisji pozycji GPS,
- wymagane zewnętrzne zasilanie 5 V,
- GPS nie jest zasilany z modułu,
- konstrukcja bazuje na starszych mikrokontrolerach PIC.

## 14. Zastosowania

Typowe zastosowania FoxTrak-M:

- samochodowy tracker APRS,
- tracker wbudowany w inne urządzenie,
- mobilna stacja GPS/APRS,
- eksperymentalna platforma APRS,
- projekt edukacyjny związany z AFSK, AX.25 i APRS.

## 15. Klasyfikacja

| Cecha | Wartość |
|---|---|
| Typ | sprzęt |
| Klasa | tracker APRS |
| Kierunek pracy | TX |
| Pozycja | GPS |
| Dane GPS | NMEA, GPRMC |
| Warstwa radiowa | AFSK 1200 bps |
| Modulacja | Bell 202 |
| Mikrokontroler | PIC16F84A / PIC16F628A |
| Zasilanie | 5 V DC |
| Interfejs do radia | audio + PTT |
| Odbiór APRS | nie |
| Smart Beaconing | tak |
| Konfiguracja z PC | tak |

## 16. Znaczenie historyczne

FoxTrak-M reprezentuje klasę prostych, sprzętowych trackerów APRS, które pozwalały zbudować mobilną stację pozycyjną bez stosowania pełnego TNC i komputera.

Jego konstrukcja dobrze pokazuje klasyczny model trackera APRS:

```text
GPS
  ↓
NMEA
  ↓
mikrokontroler
  ↓
APRS / AX.25
  ↓
AFSK 1200 bps
  ↓
radio
```

## 17. Źródła

Podstawą tego opisu jest dokumentacja producenta:

**Fox Delta, FD - FoxTrak-M, Technical information: Compact PIC 16F84A/628A APRS Tracker Module**

https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf

Dokument datowany jest na 8 czerwca 2008 r.
