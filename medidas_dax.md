# DAX Measures — NexaCore Tickets 2025

All measures live in a dedicated table `[_Medidas]`. Grouped by theme.

---

## Volume

```dax
Total Tickets =
COUNTROWS(Tickets)
```

```dax
-- Tickets with a terminal status
Tickets Resolvidos =
CALCULATE([Total Tickets], Tickets[status] IN {"Resolved", "Closed"})
```

```dax
-- Tickets still active
Tickets Abertos =
CALCULATE([Total Tickets], Tickets[status] IN {"Open", "In Progress"})
```

```dax
Tickets Cancelados =
CALCULATE([Total Tickets], Tickets[status] = "Cancelled")
```

---

## SLA

```dax
-- Denominator for SLA rate — excludes tickets with no SLA evaluation
Tickets com SLA Avaliado =
CALCULATE([Total Tickets], Tickets[sla_breached] IN {"Sim", "Não"})
```

```dax
Tickets SLA Estourado =
CALCULATE([Total Tickets], Tickets[sla_breached] = "Sim")
```

```dax
% SLA Cumprido =
DIVIDE(
    CALCULATE([Total Tickets], Tickets[sla_breached] = "Não"),
    [Tickets com SLA Avaliado],
    BLANK()
)
```

```dax
% SLA Estourado =
1 - [% SLA Cumprido]
```

---

## Resolution time

```dax
-- Filters out zero and null to avoid skewing the average
Tempo Médio Resolução (h) =
AVERAGEX(
    FILTER(Tickets, Tickets[resolution_hours] > 0),
    Tickets[resolution_hours]
)
```

```dax
Tempo Mediano Resolução (h) =
MEDIANX(
    FILTER(Tickets, Tickets[resolution_hours] > 0),
    Tickets[resolution_hours]
)
```

---

## Satisfaction

```dax
-- Only tickets that received a rating
Satisfação Média =
AVERAGEX(
    FILTER(Tickets, NOT(ISBLANK(Tickets[satisfaction_score]))),
    Tickets[satisfaction_score]
)
```

```dax
Tickets com Avaliação =
CALCULATE([Total Tickets], NOT(ISBLANK(Tickets[satisfaction_score])))
```

```dax
-- Score >= 4 considered positive
% Avaliações Positivas =
DIVIDE(
    CALCULATE([Total Tickets],
        NOT(ISBLANK(Tickets[satisfaction_score])),
        Tickets[satisfaction_score] >= 4
    ),
    [Tickets com Avaliação],
    BLANK()
)
```

---

## Escalation & quality

```dax
Tickets Escalados =
CALCULATE([Total Tickets], Tickets[is_escalated] = "Sim")
```

```dax
% Escalação =
DIVIDE([Tickets Escalados], [Total Tickets], BLANK())
```

```dax
-- Tickets reopened at least once — proxy for resolution quality
Tickets Reabertos =
CALCULATE([Total Tickets], Tickets[reopen_count] > 0)
```

```dax
% Reabertos =
DIVIDE([Tickets Reabertos], [Total Tickets], BLANK())
```

```dax
Média Reaberturas =
AVERAGEX(
    FILTER(Tickets, Tickets[reopen_count] > 0),
    Tickets[reopen_count]
)
```

---

## Production / critical

```dax
Tickets Críticos em Produção =
CALCULATE(
    [Total Tickets],
    Tickets[priority] = "Critical",
    Tickets[environment] = "Production"
)
```

```dax
% Incidentes em Produção =
DIVIDE(
    CALCULATE([Total Tickets],
        Tickets[category] = "Incident",
        Tickets[environment] = "Production"
    ),
    [Total Tickets],
    BLANK()
)
```

---

## Month-over-month

```dax
Tickets Mês Anterior =
CALCULATE(
    [Total Tickets],
    DATEADD(CALENDAR(MIN(Tickets[created_at]), MAX(Tickets[created_at])), -1, MONTH)
)
-- Note: a dedicated date table makes time intelligence more robust
```

```dax
Variação MoM % =
DIVIDE(
    [Total Tickets] - [Tickets Mês Anterior],
    [Tickets Mês Anterior],
    BLANK()
)
```
