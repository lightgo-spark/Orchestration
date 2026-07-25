# Orchestration Studio — DAG Builder & Node Editor

> Visual DAG builder and full-screen node editor. Draw workflows as graphs, generate `.py` code, and run locally.

## DAG Builder

**Graph canvas editor** — build DAGs by placing nodes and connecting ports.

| Feature | Detail |
|---|---|
| Operators | 22 types (Bash, Python, K8s, MySQL, S3, Spark, Slack, dbt, BigQuery, Snowflake, etc.) |
| Execution | `Run` (actual, max 1~32 parallel) / `Sim` (dry-run, 1x/2x/4x speed) |
| Validation | Static check — cycles (Kahn), dup names, orphans, empty code, schedule checks |
| Layout | Kahn topological sort + barycenter crossing minimization, 4 sweeps |
| I/O | Import/export JSON, `.py`; Airflow 2.x/3.x + TaskFlow/Classic code generation |
| Undo/Redo | Canvas snapshot stack |
| AI | Claude / OpenAI / DeepSeek prompt assistant, failure diagnosis |
| Perf | Metrics overlay (nodes, edges, validate/layout/codegen timings) |

**Canvas controls**: Click to select, Shift+click multi-select, drag to move/pan, mouse wheel zoom (0.2~4.0), right-click context menu, rubber-band selection, minimap.

**Inline node panel** (right sidebar, 240~440px): Quick property edits + code editor with syntax highlighting, Find/Replace, Jinja preview, operator-specific snippets.

## Node Editor

**Full-screen IDE-style editor** for focused editing of a single node.

| Feature | Detail |
|---|---|
| Properties | Name, Op, Retries, Delay, Pool, Queue, Trigger Rule, Priority, Dependencies |
| Code editor | Syntax highlighting (Python/SQL/Bash), line-number gutter, minimap |
| Virtualization | Viewport windowed rendering for 400+ line code, document-level undo (64 steps) |
| Pro toolbar | Indent/outdent, comment toggle, sort lines, deduplicate, case transform, trim |
| Find/Replace | Case-insensitive, word-boundary, Replace All / Delete All |
| Font | 9~24px adjustable |
| Templates | Jinja variable insertion ({{ ds }}, {{ ti.xcom_pull(...) }}, etc.), op-specific snippets |
| Diff | Buffer vs. Node side-by-side comparison |

## Key Notes

- **`Apply` ≠ `Save`** — Apply writes buffer to in-memory node; only `Save` persists to disk.
- **No `Ctrl+S`** — save exclusively via the DAG Builder toolbar.
- **Two editors, synced** — inline panel (quick edits) and Node Editor tab (deep edits) sync on `Apply`.
- `Timeout`, `Exp backoff`, `Map .expand` only in inline panel; `Queue`, `Trigger`, `Prio` only in Node Editor.
- `▶ Sim` runs no real code; `▶ Run` executes actually.
- Sticky notes are workspace-global, not per DAG.