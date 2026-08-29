# Rustflow Studio 

Screenshot documentation for **Rustflow Studio**, a real local workflow
orchestrator built with Rust + egui, featuring an in-app flow editor. Every
screenshot below is a live session running the `calendar-pdf` demo flow on the
`default-agent-pool` work pool, captured 2026-08-28 (UTC), signed in as `admin`.

| # | File | Screen |
|---|------|--------|
| 1 | `00.Rustflow_Dashboard.png` | Dashboard |
| 2 | `01.Rustflow_Flow Runs.png` | Flow Runs |
| 3 | `02.Rustflow_Flows.png` | Flows |
| 4 | `03.Rustflow_Flows_Eidtor.png` | Flows — Editor |

---

## 1. Dashboard — `00.Rustflow_Dashboard.png`

The home screen: real-time orchestration at a glance.

- **Top bar** — scheduler toggle (`Resume scheduler`), `Quick Run` button,
  UTC clock (`08-28 23:38:48 UTC`)
- **KPIs** — Running, Scheduled, Active now, success rate (94%)
- **Charts** — Completed (total), Throughput (cumulative)
- **Recent flow runs** — the latest runs with state and age
  (two `rapid-ibis calendar-pdf` runs completed)
- **Flow run states legend** — Running / Scheduled / Pending / Retrying /
  Paused / Cancelling / Completed / Failed / Crashed / Cancelled / Skipped
- **Sidebar navigation** —
  - Overview: Dashboard, Flow Runs, Flows, Deployments
  - Infrastructure: Work Pools, Work Queues
  - Configuration: Blocks, Variables, Concurrency
  - CI/CD
  - Events & Alerts: Automations, Sensors, Notifications, Event Feed, Artifacts
  - Governance: Users

## 2. Flow Runs — `01.Rustflow_Flow Runs.png`

Every scheduled, active and finished run.

- **Filter bar** — All / Late / Pending / Running / Retrying / Paused /
  Cancelling / Completed / Started / Failed / Crashed / Cancelled / Skipped
- **Run table** — State, Run, Deployment, Work pool
- **Shown here** — two `calendar-pdf` runs, both `Completed` with **14/14**
  tasks on `default-agent-pool`
- **Controls** — Resume scheduler, Quick Run, Abort

## 3. Flows — `02.Rustflow_Flows.png`

Registered flow definitions and their task graphs.

- **Flow card** — `calendar-pdf`: "Save printable year calendar as a PDF on
  the desktop"
- **Task graph** — `plan` → `PDF` (both `tries=0`), source
  `flows/calendar_pdf.py`
- **Actions** — `+ New script`, `Open script`, `+ Register script`,
  `Run`, `Edit script`
- **Deployments panel** — none registered at capture time

## 4. Flows Editor — `03.Rustflow_Flows_Eidtor.png`

The in-app editor with `flows/calendar_pdf.py` open.

- **Flow** — `calendar-pdf`, description "PDF to desktop",
  **577 lines, 2 tasks detected** (`plan`, `write`)
- **Parameter form** — year, months, week_start, dest, out, overwrite,
  paper, orientation, events, week_numbers, highlight_today, compress
- **Buttons** — `Save`, `Save & register`
- **Script body** — a printable month calendar written as a real PDF, byte
  by byte, **stdlib only**: no third-party packages are imported, and the
  only fonts used are two of the fourteen every PDF reader is required to
  have. The file lands on the desktop by default (or `results`, `home`, or
  any directory path via `dest`).
