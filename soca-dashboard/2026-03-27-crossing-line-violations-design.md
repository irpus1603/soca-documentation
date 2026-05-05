# Design: Crossing Line Fix + Violation CLS Summary
Date: 2026-03-27

## Goals
1. Fix crossing line not shown in live stream / snapshot when ROI type is LINE
2. Record PPE/People Detection violations by CLS name, display as one-line summary (e.g. "3 no-vest, 2 no-gloves")

## Principles
- Minimal changes only — no new abstractions, no extra helpers
- Flat, readable code
- Follow existing patterns in each project

---

## Fix 1: Crossing Line Not Shown

**Root cause**: `_draw_roi()` returns early for LINE type, expecting `_draw_crossing_lines()` to handle it. But `_draw_crossing_lines()` only draws for `people_count` rules. If the user uses `detection` mode rules (PPE, intrusion, etc.) with a LINE ROI, the line never appears.

**Fix** — `soca-engine/core/monitor_stream.py`:
- In `_draw_roi()`: when `roi.type == 'LINE'`, draw dashed amber line + endpoint circles directly from `roi.points`, then return
- `_draw_crossing_lines()` unchanged — still overlays direction/counts for `people_count` rules on top

One file, 6 lines changed.

---

## Fix 2: Violation CLS Name Summary

**Data flow**: engine detects → stores cls_name_summary → publishes to queue → soca-service forwards → soca-control stores → displayed in UI

### soca-engine (4 files)

| File | Change |
|------|--------|
| `models/db.py` | Add `cls_name_summary` JSON column to `DBEvent` + `ALTER TABLE` migration (same pattern as existing migrations) |
| `workers/detection_worker.py` | Build `cls_name_summary = {cls_name: count}` from in_roi detections; pass to DBEvent |
| `core/action_dispatcher.py` | Add `{violations}` to `_format_message()` — formatted as `"3 no-vest, 2 no-gloves"` from in_roi detections |
| `core/output_publisher.py` | Add `cls_name_summary` to `roi_summary` in published payload |

### soca-dashboard (3 files)

| File | Change |
|------|--------|
| `app/views.py` | Add `e.cls_name_summary` to alerts SQL SELECT; parse into event dict |
| `templates/alerts/list.html` | Add "Violations" column — show cls_name_summary as compact badges |
| `templates/schedules/_rule_fields.html` | Add `{violations}` badge to message placeholder list |

### soca-control (4 files)

| File | Change |
|------|--------|
| `app/models.py` | Add `cls_name_summary = JSONField(default=dict, blank=True)` to Alert |
| `app/migrations/0012_add_cls_name_summary.py` | Django migration |
| `app/management/commands/message_parser.py` | Parse `cls_name_summary` from `roi_summary` in payload |
| `app/templates/reports/ppe.html` | Replace/supplement "Message" column with "Violations" compact badges |

---

## Violation Format
`cls_name_summary` = `{"no-vest": 3, "no-gloves": 2}` (in_roi detections only)

Display: `3 no-vest  2 no-gloves` (flat badge per class, sorted by count desc)

`{violations}` template: `"3 no-vest, 2 no-gloves"` — one-line string

---

## Out of Scope
- No changes to soca-service (it just forwards payloads, no parsing)
- No new views or URLs
- No CSS frameworks beyond existing DaisyUI
