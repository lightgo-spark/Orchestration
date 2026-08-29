# Assetflow Studio — an orchestration run, end to end

Three screens and the file they produced, captured on 2026-08-29 from
**Assetflow Studio** v4.1.10 — a data orchestrator written in Rust + egui
that runs as a desktop application rather than a web service.

This is not a mock-up. Every number below was read off a running build, and the
PDF in this folder is the artifact that run actually wrote.

한국어: [`README.ko.md`](README.ko.md)

## What is here

| File | What it shows |
|---|---|
| [`00.Assetflow_Overview.png`](00.Assetflow_Overview.png) | Deployment health — asset counts, run history, success rate, cost |
| [`01.Assetflow_Catalog.png`](01.Assetflow_Catalog.png) | The asset's detail page: what the last materialization produced, and its lineage |
| [`02.Assetflow_CodeEditor.png`](02.Assetflow_CodeEditor.png) | The code the asset runs, edited in place, with the run's own log beneath it |
| [`weather-seoul-2026-08-29.pdf`](weather-seoul-2026-08-29.pdf) | The output — one A4 page, written by that run |

## The run

One asset, `reporting/weather_daily`, in the `analytics_platform` code location.
It fetches today's forecast for Seoul and writes a one-page PDF to the desktop.

```
run 5088344e · manual · 19:16:35
  META source:url=https://open-meteo.com/
  ROWS=8
  EVENT ASSET_MATERIALIZATION — 8 rows (1.3s)
  EVENT RUN_SUCCESS — engine released
```

`ROWS=8` is not a constant. The script prints the number of hourly rows the
table on the page carries, so the asset's row count is a fact about the document
rather than a number somebody typed. The PDF has eight hourly rows in it.

The `META` lines are the same contract in the other direction: the asset detail
screen renders `path` as something to click, `high` and `low` as numbers because
they are tagged `:float`, and `source` as a link. That is why the screenshot
shows `28.300 / 23.000` in a metadata table rather than in a log line nobody
would read twice.

## Screen by screen

### Overview

Health of the `analytics_platform` deployment. The workspace's run history holds
**74 runs at 86% success**, `4h 46m` average duration, and **$0.19** attributed
from real step durations at the `python` compute kind's `$0.50/h`. The bar chart
is recent runs oldest-first — 32 succeeded, 7 failed, 1 other in the window
drawn — and the failures are named underneath rather than left as a count:
`reporting/finance_extract` (7) and `ml/model_metrics` (1), both from earlier
sessions in this same workspace.

### Asset detail

`reporting/weather_daily` after a successful materialization: **8 rows in 1.5s**,
owned by `bi@acme.io`, tagged `team=reporting` and `tier=gold`. It is a *source*
asset — no upstream, no downstream — so the lineage panel says so plainly instead
of drawing an empty graph.

The metadata below it is what the script emitted: the path of the PDF it wrote,
the place, the high and low, the conditions (`Light drizzle`), and the data
source. Auto-materialization is `Off`, and the condition reads `never (off)` —
this asset re-runs when somebody asks it to.

### Code editor

The same asset's command, which is a **422-line, 15,285-character Python
program**, syntax-highlighted with line numbers and a status line that counts
what the cursor is on. The highlighter follows the interpreter picked in the
dropdown, so choosing `python` colours it as Python.

Underneath is **Last run output** — status, row count, duration, and the log
tail — so the code and what it did are on one screen. `Save and run` writes the
command and launches it without leaving the page.

The program uses **the standard library only**, including for the PDF: no
`pip install` step, so the pipeline does not become a machine-setup problem on
the first machine that lacks a wheel. If the forecast cannot be fetched it exits
non-zero and the step goes red, because a report that quietly prints zeroes
would leave the screen saying `Materialized` with a row count that looked fine.

## Reproducing it

The product and the script both live in the project tree:

```
C:\Users\USER\OneDrive\Desktop\[2026-07-16] assetflow_studio
  target\release\assetflow_studio.exe    the application
  examples\samples\weather_pdf.py        the script in the screenshot
  examples\samples\README.md             what it does and how to configure it
```

Open the **Code Editor** screen, pick or create an asset, set `Runs as` to
`python`, paste the file, and press `▶ Save and run`.

Settings are read from the environment and all are optional — `WEATHER_LAT`,
`WEATHER_LON`, `WEATHER_PLACE`, `WEATHER_OUT_DIR`, and `WEATHER_API` /
`WEATHER_API_KEY` for the paid endpoint. Without them the report is Seoul,
written to the desktop, from the free endpoint.

The desktop is asked of Windows through `SHGetKnownFolderPath` rather than
assembled from the home directory. On this machine the shell's desktop is
`C:\Users\<user>\OneDrive\Desktop` because OneDrive redirects it, while
`C:\Users\<user>\Desktop` also exists and is empty — a guess would write the
report to a folder nobody opens while the run still reported success and printed
a path.

## Legal

**Weather data.** The forecast on that page is by
[Open-Meteo.com](https://open-meteo.com/), licensed
**[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)**. The PDF in this
folder names its source on the page; the licence and the link to it are supplied
here, for it and for any copy of it.

Open-Meteo's terms limit the **free** API — the one this run used — to
**non-commercial use**: *"You may only use the free API services for
non-commercial purposes."*
([terms](https://open-meteo.com/en/terms)) That restriction belongs to the
weather service the sample calls, not to Assetflow Studio. Running the same
sample as part of commercial work needs Open-Meteo's paid API: set `WEATHER_API`
to `https://customer-api.open-meteo.com/v1/forecast` and `WEATHER_API_KEY` to
the subscription key.

**Trademark.** "Dagster" is a trademark of Dagster Labs, Inc. Assetflow Studio is
an independent work that models the concepts Dagster popularised: **not**
produced, endorsed, sponsored by or affiliated with Dagster Labs, Inc., and not
the Dagster product. The name appears in the Asset Detail screenshot above
because naming the concept is the clearest way to describe it. Snowflake, Apache
Spark, Apache Kafka, PostgreSQL, DuckDB, MLflow, Slack and Kubernetes, where they
appear in the demo workspace, are the marks of their respective owners and are
used the same way.

**Screenshots.** They are of build v4.1.10 and show the demo owner address as
`bi@acme.io`. The seed data now uses `example.com`, which RFC 2606 reserves for
exactly this purpose; re-capturing the three screens is all that is left to bring
the images in line.
