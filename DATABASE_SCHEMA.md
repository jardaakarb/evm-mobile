# ERA Hackathon 2026 – Relační databáze (SQL Schema & Architektura)

Tento repozitář obsahuje kompletní datovou základnu pro řízení a vyhodnocování projektů metodou Earned Value Management (EVM).

Data jsou normalizována z primárních zdrojů (**ERA Database.xlsx**, **Cost Sheet - SYN-*.xlsx** a **Project Plan - SYN-*.pdf**) a uložena v SQLite relační databázi a SQL dump souboru.

---

## 🌐 Online zhlédnutí a interaktivní SQL konzole
- **Živé online SQL Studio na GitHub Pages:** [https://jardaakarb.github.io/evm-mobile/database.html](https://jardaakarb.github.io/evm-mobile/database.html)
- **Kompletní SQL Dump soubor:** [era_hackathon_dump.sql](era_hackathon_dump.sql)
- **Mobilní EVM aplikace:** [https://jardaakarb.github.io/evm-mobile/](https://jardaakarb.github.io/evm-mobile/)

---

## 🗄️ Entitně-Relační Diagram (ERD)

```mermaid
erDiagram
    era_db_info ||--o{ era_db_phase_hours : "project_code"
    era_db_info ||--|| cost_sheet_summary : "project_code"
    era_db_info ||--o{ cost_sheet_labor_phases : "project_code"
    era_db_info ||--o{ project_plan_phases : "project_code"
    era_db_info ||--o{ project_plan_subtasks : "project_code"
    era_db_info ||--o{ hitl_review_queue : "project_code"
    era_db_info ||--|| evm_calculated_metrics : "project_code"
    era_db_info ||--o{ audit_log : "project_code"

    era_db_info {
        string project_code PK
        string project_name
        string project_manager
        string customer
        real contract_value
        real total_planned
        real total_actual
        real planned_hours
        real actual_hours
        string status_date
        string project_status
    }

    cost_sheet_summary {
        string project_code PK
        real bac_czk
        real labor_hours
        real labor_czk
        real equipment_czk
        real subcontract_czk
        real travel_czk
        real contingency_czk
    }

    cost_sheet_labor_phases {
        int id PK
        string project_code FK
        string phase_code
        string phase_name
        string applicability
        real hours
        real czk_per_hour
        real budget_czk
    }

    era_db_phase_hours {
        int id PK
        string project_code FK
        string phase_code
        string applicability
        real actual_hours
    }

    project_plan_phases {
        int id PK
        string project_code FK
        string phase_code
        string phase_name
        string start_date
        string finish_date
        real hours
        real plan_pct
        real earned_pct
    }

    hitl_review_queue {
        int id PK
        string project_code FK
        string category
        string field_name
        string epistemic_tier
        string status
        string recommendation
    }

    evm_calculated_metrics {
        string project_code PK
        real bac_labor_hours
        real bac_labor_czk
        real ac_labor_czk
        real pv_labor_czk
        real ev_labor_czk
        real labor_cpi
        real labor_spi
        real labor_cv
        real labor_sv
        real labor_eac_czk
    }
```

---

## 📋 Přehled tabulek

1. **`era_db_info` (5 řádků):** Hlavní projektová data z účetního systému (kontrakty, celkové náklady, datum stavu, manažer).
2. **`cost_sheet_summary` (5 řádků):** Rozpad rozpočtu dle dodavatelského listu (Delivery BAC) na práci, materiál, subdodávky a rezervy.
3. **`evm_calculated_metrics` (5 řádků):** Exaktně spočtené indexy pracnosti (Labor CPI, SPI, EAC, CV, SV) a brány celoprojektového EVM.
4. **`era_db_phase_hours` (130 řádků):** Skutečně odpracované hodiny zaúčtované pro všech 26 etap (B0–A0).
5. **`cost_sheet_labor_phases` (130 řádků):** Plánované baseline hodiny a hodinové sazby pro 26 etap.
6. **`project_plan_phases` (130 řádků):** Harmonogramové fáze z primárních plánů (termíny, % dokončení).
7. **`project_plan_subtasks` (100 řádků):** Rozpad WBS na detailní subtasky.
8. **`hitl_review_queue` (20 řádků):** Fronta neshod k lidskému posouzení (kategorie, odchylka, doporučení).
9. **`audit_log` (16 řádků):** Záznamy o validaci a triangulaci dat.
