# pr3-vehicle-registrations-dwh-datamart

ETL with **Pentaho PDI (Spoon/Kettle)** to build a **dimensional Data Warehouse in PostgreSQL** and replicate it to a **SQL Server Data Mart**, including validation queries and execution evidence.

---

## Project context (PR3 — Analytical Databases)
This repository contains the full deliverable for **PR3**. The objective is to design and implement an end-to-end ETL process that:

1. Loads a **PostgreSQL Data Warehouse (DW)** following a **star schema** (dimensions + fact table).
2. Extends the DW with a **municipality dimension** and ensures referential integrity.
3. Orchestrates the ETL sequentially via a **Pentaho Job (JOB_LOAD)**.
4. Replicates the curated DW into a **SQL Server Data Mart (DM)** (DW → DM copy phase).
5. Provides **evidence** (screenshots/logs) and **SQL validations** (row counts, FK checks, etc.).

---

## Architecture overview
**Source/Staging (PostgreSQL)**  
→ **Dimensional DW (PostgreSQL)**  
→ **Data Mart (SQL Server)**

Implemented using **Pentaho PDI** transformations (`.ktr`) and jobs (`.kjb`), plus database-side SQL (DDL and validations).

---

## Data model (PostgreSQL DW)
The DW follows a star-schema approach with these core tables:

### Dimensions
- `dbo.dim_dgt_type`
- `dbo.dim_date`
- `dbo.dim_vehicle`
- `dbo.dim_municipalities`

### Fact
- `dbo.fact_vehicle_registration`

The ETL guarantees:
- Consistent lookups from dimensions into the fact table.
- Referential integrity (validated via FK-null checks).
- Reproducible loads (controlled cleanup/truncation as part of the job flow when required).

---

## ETL pipeline (Pentaho PDI)
### Main job
- **`JOB_LOAD`** executes the ETL sequentially:
  1. Set variables (paths/config)
  2. SQL step (cleanup/truncate DW tables)
  3. Load dimensions
  4. Load fact table
  5. Success / end

### Core transformations
- `DIM_DGT_TYPE.ktr`
- `DIM_DATE.ktr`
- `DIM_VEHICLE.ktr`
- `DIM_MUNICIPALITY.ktr`
- `FACT_VEHICLE_REGISTRATIONS.ktr`

---

## Validation queries (examples)
### Fact row count
```sql
SELECT COUNT(*)::bigint AS n_fact_rows
FROM dbo.fact_vehicle_registration;
````

### FK nulls (must be 0)

```sql
SELECT COUNT(*)::bigint AS n_fk_nulls
FROM dbo.fact_vehicle_registration
WHERE registration_date IS NULL
   OR vehicle_id IS NULL
   OR id_dgt_type IS NULL
   OR municipalities_id IS NULL;
```

Additional checks (dimension row counts, samples, job execution logs/metrics) are included as evidence in the final report.

---

## Repository contents

* `BDA_PR3_Suesta_Arribas_Victor.pdf` — Final submission report (includes evidence and results).
* `M2.888_PR3_Enunciado.pdf` — Official assignment statement.
* `LICENSE` — MIT License.
* `README.md` — This document.

---

## How to run (high-level)

1. Ensure **PostgreSQL (DW)** and **SQL Server (DM)** are available and reachable.
2. Open **Pentaho Data Integration (Spoon)**.
3. Configure the database connections used by the job/transformations.
4. Execute **`JOB_LOAD`** to load the DW end-to-end.
5. Execute the DW → DM replication phase (as defined in the PR3 workflow).
6. Run the validation SQL queries and verify results against the evidence in the report.

---

## Tech stack

* **ETL**: Pentaho Data Integration (Spoon/Kettle)
* **DW**: PostgreSQL
* **DM**: Microsoft SQL Server
* **SQL**: DDL + validation queries

---

## Author

**Víctor Suesta Arribas**

