# NexaCore — Ticket Analytics

**IT Support Operational Performance**

## Context

NexaCore Solutions is a fictional SaaS B2B company. The dataset simulates an internal ticket management system covering all support requests across departments throughout 2025 — 7,000 records, 20 columns, tracking the full lifecycle of each ticket from creation to resolution.

The dashboard was built to give leadership a single-page view of team performance, SLA compliance, and resolution bottlenecks — without needing to query the raw data.

## Dataset

- **Source:** Simulated CSV (`nexacore_tickets_2025.csv`)
- **Volume:** ~7,000 rows, 20 columns
- **Period:** January–December 2025
- **Key dimensions:** status, priority, category, environment, team, department
- **Key metrics:** resolution time, SLA breach, satisfaction score, escalation rate

The raw data had quality issues treated in Power Query: mixed date formats (ISO/BR/US), inconsistent casing in status and priority fields, negative resolution times, and ~30 duplicate ticket IDs.

## Dashboard

Published via Power BI Service. Single-page layout with:

- **5 KPI cards:** Total Tickets · % SLA Compliance · Avg Resolution Time · Avg Satisfaction · Escalation Rate
- **Volume trend:** monthly ticket volume line chart
- **Status breakdown:** bar chart by ticket status
- **SLA by team:** which squads breach SLA most
- **Satisfaction by team:** donut chart
- **Resolution time vs SLA target:** grouped bar by priority
- **Slicers:** month, priority, category, environment, team

## Repo structure

```
nexacore-ticket-analytics/
├── README.md
├── medidas_dax.md       — all DAX measures with comments
├── modelo_de_dados.md   — column reference and data quality notes
├── power_query.md       — transformation steps applied to the CSV
├── dataset/
│   └── nexacore_tickets_2025.csv
└── theme/
    └── nexacore_theme.json
```
