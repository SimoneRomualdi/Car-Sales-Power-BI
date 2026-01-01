# 📊 Formule DAX - Dashboard Vendita Automobili

Questo documento contiene tutte le formule DAX utilizzate nel progetto Power BI per l'analisi delle vendite di automobili.

---

## 📅 Tabella Calendario

### Creazione Tabella Calendario
```DAX
calendario = CALENDAR(MIN(car_data[Data]), MAX(car_data[Data]))
```

### Colonna Anno
```DAX
Anno = YEAR(calendario[Date])
```

### Colonna Mese
```DAX
Mese = FORMAT(calendario[Date], "MMMM")
```

### Colonna Settimana
```DAX
Settimana = WEEKNUM(calendario[Date])
```

---

## 💰 Metriche Base

### Prezzo Medio
```DAX
Prezzo Medio = SUM(car_data[Prezzo (€)]) / COUNT(car_data[Car_id])
```

### Vendite Totali
```DAX
Vendite Totali = SUM(car_data[Prezzo (€)])
```

### Auto Vendute
```DAX
Auto Vendute = COUNT(car_data[Car_id])
```

---

## 📈 Metriche YTD (Year-to-Date)

### YTD Vendite Totali
```DAX
YTD Vendite Totali = 
TOTALYTD(
    SUM(car_data[Prezzo (€)]),
    calendario[Date]
)
```

### YTD Prezzo Medio
```DAX
YTD Prezzo Medio = 
TOTALYTD(
    [Prezzo Medio],
    calendario[Date]
)
```

### YTD Auto Vendute
```DAX
YTD Auto Vendute = 
TOTALYTD(
    COUNT(car_data[Car_id]),
    calendario[Date]
)
```

---

## 📆 Metriche MTD (Month-to-Date)

### MYTD Vendite Totali
```DAX
MYTD Vendite Totali = 
TOTALMTD(
    SUM(car_data[Prezzo (€)]), 
    calendario[Date]
)
```

### MTD Prezzo Medio
```DAX
MTD Prezzo Medio = 
TOTALMTD(
    [Prezzo Medio], 
    calendario[Date]
)
```

### MTD Auto Vendute
```DAX
MTD Auto Vendute = 
TOTALMTD(
    COUNT(car_data[Car_id]), 
    calendario[Date]
)
```

---

## ⏪ Metriche PYTD (Previous Year-to-Date)

### PYTD Vendite Totali
```DAX
PYTD Vendite Totali = 
CALCULATE(
    [YTD Vendite Totali],
    SAMEPERIODLASTYEAR(calendario[Date])
)
```

### PYTD Prezzo Medio
```DAX
PYTD Prezzo Medio = 
CALCULATE(
    [YTD Prezzo Medio],
    SAMEPERIODLASTYEAR(calendario[Date])
)
```

### PYTD Auto Vendute
```DAX
PYTD Auto Vendute = 
CALCULATE(
    [YTD Auto Vendute],
    SAMEPERIODLASTYEAR(calendario[Date])
)
```

---

## 📊 Calcolo Differenze

### Differenza Vendite
```DAX
Differenza Vendite = [YTD Vendite Totali] - [PYTD Vendite Totali]
```

### Prezzo Medio Differenza
```DAX
Prezzo Medio Differenza = [YTD Prezzo Medio] - [PYTD Prezzo Medio]
```

### Auto Vendute Differenza
```DAX
Auto Vendute Differenza = [YTD Auto Vendute] - [PYTD Auto Vendute]
```

---

## 📈 Crescita YoY (Year-over-Year)

### YoY Crescita Vendite
```DAX
YoY Crescita = [Differenza Vendite] / [PYTD Vendite Totali]
```

### YoY Prezzo Medio
```DAX
YoY Prezzo Medio = [Prezzo Medio Differenza] / [PYTD Prezzo Medio]
```

### YoY Auto Vendute
```DAX
YoY Auto Vendute = [Auto Vendute Differenza] / [PYTD Auto Vendute]
```

---

## 🏷️ Formattazione Testo per Card

### Testo MYTD Vendite
```DAX
Testo MYTD = 
VAR Milioni = FORMAT([MYTD Vendite Totali] / 1000000, "0.00")
RETURN
"MTD Vendite Totali: €" & Milioni & "M"
```

### Testo MTD Prezzo Medio
```DAX
Testo MTD Prezzo Medio = 
VAR prezzo_medio = FORMAT([Prezzo Medio] / 1000, "0.00")
RETURN
"MTD Prezzo Medio: €" & prezzo_medio & "K"
```

### Testo MTD Auto Vendute
```DAX
Testo MTD Auto Vendute = 
VAR auto_vendute = FORMAT([MTD Auto Vendute] / 1000, "0.00")
RETURN
"MTD Auto Vendute: " & auto_vendute & "K"
```

---

## 🎨 Formattazione Condizionale

### Colore Testo (Verde/Rosso)
```DAX
Colore testo = 
IF([Prezzo Medio Differenza] > 0, "Green", "Red")
```

> **Nota**: Questa formula può essere adattata per qualsiasi metrica di differenza sostituendo `[Prezzo Medio Differenza]` con la metrica desiderata.

---

## 📍 Visualizzazioni Avanzate

### Picco Massimo Grafico Area
```DAX
Picco Massimo Grafico Area = 
VAR MaxVendite = 
    MAXX(
        ALLSELECTED(calendario[Settimana]),
        [Vendite Totali]
    )
RETURN
    IF(
        [Vendite Totali] = MaxVendite,
        MaxVendite,
        BLANK()
    )
```

> **Utilizzo**: Questa misura evidenzia il picco massimo nel grafico ad area delle vendite settimanali, mostrando solo il valore più alto e lasciando vuoti gli altri punti.

---

## 📝 Note Tecniche

### Convenzioni Utilizzate
- **YTD** (Year-to-Date): Dall'inizio dell'anno fino alla data corrente
- **MTD** (Month-to-Date): Dall'inizio del mese fino alla data corrente
- **PYTD** (Previous Year-to-Date): Stesso periodo dell'anno precedente
- **YoY** (Year-over-Year): Crescita percentuale rispetto all'anno precedente

### Formattazione Numeri
- **M** = Milioni (diviso per 1.000.000)
- **K** = Migliaia (diviso per 1.000)
- Formato: `"0.00"` garantisce almeno 2 decimali

### Funzioni DAX Chiave
- `TOTALYTD()`: Calcola il totale dall'inizio dell'anno
- `TOTALMTD()`: Calcola il totale dall'inizio del mese
- `SAMEPERIODLASTYEAR()`: Confronta con lo stesso periodo dell'anno precedente
- `CALCULATE()`: Modifica il contesto di filtro
- `MAXX()`: Trova il valore massimo in una tabella
- `ALLSELECTED()`: Mantiene i filtri esterni ma ignora quelli interni
