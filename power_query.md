# Power Query Transformations — NexaCore Tickets 2025

Applied in the Advanced Editor after importing `nexacore_tickets_2025.csv`.

---

1. **Import CSV** — UTF-8, comma delimiter, 20 columns, headers promoted.

2. **Remove duplicates** — `Table.Distinct` on `ticket_id`. ~30 duplicate rows were present in the raw export.

3. **Trim text columns** — leading/trailing whitespace removed from `assignee`, `status`, and `priority`. The `assignee` column had consistent extra spaces from the source system.

4. **Standardize status** — values uppercased and matched to canonical labels (`Open`, `In Progress`, `Resolved`, `Closed`, `Cancelled`). The raw data included Portuguese variants (`Cancelado`) and inconsistent casing.

5. **Standardize priority** — same approach; corrected typos found in the raw file (`MEDUM` → `Medium`, `HIG` → `High`, `CRITCAL` → `Critical`).

6. **Normalize dates** — `created_at` and `resolved_at` arrived in three formats (ISO, BR, US). A custom M function detects the format by checking if the first segment exceeds 12 (unambiguous day-first) and parses accordingly. Ambiguous cases default to US format.

7. **Fix negative resolution_hours** — a small number of rows had negative values, likely from a data entry bug. Converted to absolute value with `Number.Abs`.

8. **Cast numeric columns** — `sla_target_hours`, `satisfaction_score`, `reopen_count`, and `story_points` cast to `Int64`.

9. **Replace empty strings** — `tag` → `"Não informado"`, `sprint` → `"Sem Sprint"`. Keeps slicers clean.

10. **Add mes_ano** — integer `YYYYMM` computed from `created_at`. Used as the x-axis on the monthly volume chart.

11. **Add trimestre** — `"Q" & QuarterOfYear` string column for quarterly filtering.

12. **Add sla_status** — human-readable label derived from `sla_breached`: `Cumprido`, `Estourado`, or `N/A`.

13. **Final type enforcement** — all text and datetime columns explicitly typed to prevent Power BI from inferring incorrect types on refresh.
