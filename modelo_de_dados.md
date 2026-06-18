# Data Model — NexaCore Tickets 2025

Single table: `Tickets` (loaded from `nexacore_tickets_2025.csv`).

## Columns

| Column | Type | Description |
|---|---|---|
| ticket_id | Text | Unique identifier — format `TKT-XXXXX` |
| created_at | DateTime | Ticket creation timestamp |
| resolved_at | DateTime (nullable) | Resolution timestamp — null if still open |
| status | Text | `Open` · `In Progress` · `Resolved` · `Closed` · `Cancelled` |
| priority | Text | `Low` · `Medium` · `High` · `Critical` |
| category | Text | `Bug` · `Feature Request` · `Incident` · `Change Request` · `Task` · `Security` · `Performance` |
| environment | Text | `Production` · `Staging` · `Development` · `Homologation` |
| assignee | Text | Analyst responsible for the ticket |
| reporter | Text | Collaborator who opened the ticket |
| team | Text | `Squad Alpha/Beta/Gamma/Delta/Omega` · `Infra` · `QA` · `DevOps` |
| department_requester | Text | Requesting department |
| tag | Text | Technical tag (frontend, api, database…) — nullable |
| sla_target_hours | Integer | Resolution target in hours by priority |
| resolution_hours | Decimal | Actual hours to resolution — null if unresolved |
| sla_breached | Text | `Sim` / `Não` — whether SLA was breached |
| satisfaction_score | Integer | Requester rating 1–5 — sparse (only when submitted) |
| story_points | Integer | Effort estimate — nullable |
| reopen_count | Integer | Number of times the ticket was reopened |
| is_escalated | Text | `Sim` / `Não` — escalated to a higher level |
| sprint | Text | Associated sprint (`Sprint 1` to `Sprint 24`) — nullable |

## SLA targets by priority

| Priority | Target |
|---|---|
| Critical | 4h |
| High | 8h |
| Medium | 24h |
| Low | 72h |

## Computed columns (added in Power Query)

| Column | Description |
|---|---|
| mes_ano | Integer `YYYYMM` — used as the timeline axis |
| trimestre | `Q1` to `Q4` |
| sla_status | Human-readable label: `Cumprido` / `Estourado` / `N/A` |

## Data quality issues treated

- **Mixed date formats** — ISO (`YYYY-MM-DD`), BR (`DD/MM/YYYY`), and US (`MM/DD/YYYY`) all present; normalized in Power Query
- **Inconsistent casing** — status and priority had typos and mixed case; standardized via lookup logic
- **Negative resolution_hours** — ~handful of rows with negative values; converted to absolute value
- **Duplicate ticket IDs** — ~30 duplicate rows removed via `Table.Distinct`
- **Whitespace** — leading/trailing spaces in `assignee` removed with `Text.Trim`
