---
title: Struktura pakietu APRS
description: Krótkie wprowadzenie do części pakietu APRS.
---

Pakiet APRS zawiera stację źródłową, identyfikator docelowy, opcjonalną ścieżkę i pole informacji.

```text
SOURCE>DESTINATION,PATH:information field
```

Pole informacji informuje odbierające oprogramowanie, czy pakiet zawiera pozycję, wiadomość, obiekt, telemetrię czy inny typ danych APRS.

## Dlaczego to ważne

Rozdzielne odczytanie poszczególnych części to najszybszy sposób na zdiagnozowanie pakietu, który nie wyświetla się zgodnie z oczekiwaniami.
