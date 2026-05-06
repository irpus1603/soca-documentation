# SOCA — Grafana Reporting Guide

Complete database schema and SQL queries for building Grafana dashboards covering
Object Detection, People Counting, Intrusion, Crowd, and LPR reports.

**Database: PostgreSQL**

---

## Database Overview

soca-control uses **PostgreSQL** in production.
Add it as a Grafana data source — the same PostgreSQL instance used by soca-control.

> **Timezone note:** All timestamps stored in UTC. Set Grafana's timezone to `Asia/Jakarta (GMT+7)`
> in Dashboard Settings → Time options → Timezone, **or** apply `AT TIME ZONE 'Asia/Jakarta'` in queries.

---

## Table: `app_alert` — Core Schema

```
Column              Type        Notes
──────────────────────────────────────────────────────────────────
id                  SERIAL      Primary key
edge_id             INTEGER     FK → app_edge.id (nullable)
edge_name           TEXT        Denormalized edge identifier, e.g. "edge-mac-mini-bekasi"
camera_id           TEXT        Raw camera identifier
camera_name         TEXT        Human-readable camera name
job_id              TEXT        UUID of the soca-engine job
frame_id            TEXT        Unique frame identifier
timestamp           TIMESTAMPTZ UTC — when the detection occurred
alert_category      TEXT        'Intrusion' | 'PPE' | 'Detection' | 'Counting' | 'Crowd' | 'LPR'
rule_name           TEXT        Name of the rule that fired
snapshot_message    TEXT        Formatted alert message
snapshot_path       TEXT        Local/GCS path to snapshot JPG
detection_count     INTEGER     Total detections in frame
in_roi_count        INTEGER     Detections inside ROI
cls_summary         JSONB       Class ID counts: {"0": 2, "2": 1}
cls_name_summary    JSONB       Class name counts: {"person": 2, "car": 1}
crossing_counts     JSONB       Line crossing: {"rule_name": {"in": 5, "out": 3}}
crowd_count         INTEGER     In-ROI person count (crowd mode)
lpr_plates          JSONB       [{plate_number, plate_confidence, plate_expiry}]
raw                 JSONB       Full raw payload
created_at          TIMESTAMPTZ UTC — when soca-control received the alert
```

**Indexes:** `(edge_name, timestamp)`, `(alert_category, timestamp)`, `(camera_id)`

---

## Table: `app_edge` — Edge Devices

```
Column              Type        Notes
──────────────────────────────────────────────────────────────────
id                  SERIAL      Primary key
name                TEXT        Unique edge name, e.g. "edge-mac-mini-bekasi"
url                 TEXT        soca-dashboard base URL
engine_url          TEXT        soca-engine base URL
location            TEXT        Physical location description
active              BOOLEAN     true = active, false = deactivated
gcs_path_prefix     TEXT        GCS path prefix, e.g. "edge-mac-mini-bekasi"
created_at          TIMESTAMPTZ
```

---

## Connecting Grafana to PostgreSQL

1. Grafana → **Connections → Data sources → Add data source → PostgreSQL**
2. Fill in:
   - **Host URL**: `<soca-control-ip>:5432`
   - **Database**: your soca-control database name
   - **User / Password**: PostgreSQL credentials
   - **TLS/SSL Mode**: `disable` (for local) or `require` (for remote)
3. **Min time interval**: `1m`
4. **PostgreSQL version**: select your version (14+)
5. Click **Save & Test** — Grafana validates the connection

---

## Grafana SQL Macros

Grafana's PostgreSQL plugin provides time macros for all time-series panels:

| Macro | Expands to | Use for |
|-------|-----------|---------|
| `$__timeFilter(col)` | `col BETWEEN $from AND $to` | WHERE clause — respects dashboard time picker |
| `$__timeGroup(col,'$__interval')` | `date_trunc(...)` bucket | GROUP BY in time series |
| `$__timeGroupAlias(col,'$__interval')` | Same + aliased as `time` | Required alias for Time series panels |
| `$__interval` | Auto-calculated interval string | Passed to `$__timeGroup` |
| `$__timeFrom()` | Start of selected time range | Manual date comparisons |
| `$__timeTo()` | End of selected time range | Manual date comparisons |

> All queries below use `$__timeFilter(timestamp)` in place of a fixed `30 days` window,
> so they automatically respond to the Grafana time range picker.

---

## Template Variables

Create these as **Dashboard Variables** (Dashboard Settings → Variables → Add variable):

### `$edge` — Edge filter

| Field | Value |
|-------|-------|
| Type | Query |
| Data source | your PostgreSQL |
| Query | `SELECT DISTINCT edge_name FROM app_alert WHERE edge_name <> '' ORDER BY 1` |
| Multi-value | ✅ |
| Include All | ✅ |
| All value | `.*` |

Use in queries: `AND edge_name ~ '$edge'` (when All = `.*`) or `AND edge_name IN ($edge)` for multi-select.

### `$camera` — Camera filter

| Field | Value |
|-------|-------|
| Type | Query |
| Query | `SELECT DISTINCT COALESCE(NULLIF(camera_name,''), camera_id) FROM app_alert ORDER BY 1` |
| Multi-value | ✅ |
| Include All | ✅ |

### `$category` — Alert category filter

| Field | Value |
|-------|-------|
| Type | Query |
| Query | `SELECT DISTINCT alert_category FROM app_alert ORDER BY 1` |
| Multi-value | ✅ |
| Include All | ✅ |

### `$plate_search` — Plate number text filter

| Field | Value |
|-------|-------|
| Type | Text box |
| Default | _(empty)_ |

Use in query: `AND (plate ->> 'plate_number') ILIKE '%${plate_search}%'`

---

## Shared Utility Queries

### Q0 — Daily Alert Summary (all categories) — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    alert_category,
    COUNT(*)  AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY 1, alert_category
ORDER BY 1
```

> Enable **Format as: Time series** and set **Fill mode** to `0` for missing buckets.

---

## 1. Object Detection Report

**alert_category = 'Detection'**

### 1-A — Daily trend — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)                    AS alerts,
    SUM(detection_count)        AS total_objects,
    ROUND(AVG(in_roi_count), 1) AS avg_in_roi
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY 1
ORDER BY 1
```

### 1-B — Alerts by camera — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS alert_count,
    SUM(detection_count)           AS total_objects,A
    MAX(detection_count)           AS peak_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY camera_id, camera_name, edge_name
ORDER BY alert_count DESC
LIMIT 20
```

### 1-C — Alerts by rule name — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    rule_name,
    COUNT(*)                       AS alert_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi,
    MAX(detection_count)           AS peak_objects
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY rule_name
ORDER BY alert_count DESC
```

### 1-D — Hourly distribution — Heatmap

**Panel type:** Heatmap
> X-axis = hour of day (0–23), Y-axis = day of week

```sql
SELECT
    EXTRACT(HOUR FROM timestamp)::integer      AS hour_of_day,
    EXTRACT(DOW  FROM timestamp)::integer      AS day_of_week,
    COUNT(*)                                   AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY hour_of_day, day_of_week
ORDER BY day_of_week, hour_of_day
```

### 1-E — Top detected classes — Pie chart

**Panel type:** Pie chart

```sql
SELECT
    kv.key                         AS class_name,
    SUM((kv.value)::integer)       AS total_detections
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Detection'
GROUP BY kv.key
ORDER BY total_detections DESC
```

### 1-F — Recent alerts list — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    in_roi_count,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
ORDER BY timestamp DESC
LIMIT 200
```

---

## 2. Intrusion Detection Report

**alert_category = 'Intrusion'**

### 2-A — Daily intrusion trend — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)            AS intrusions,
    SUM(in_roi_count)   AS total_intruders
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
GROUP BY 1
ORDER BY 1
```

### 2-B — Most triggered cameras — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS intrusion_count,
    MIN(timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
GROUP BY camera_id, camera_name, edge_name
ORDER BY intrusion_count DESC
```

### 2-C — Peak intrusion hours — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    COUNT(*)                       AS intrusion_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
GROUP BY hour
ORDER BY hour
```

### 2-D — Intrusion events detail — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    in_roi_count                               AS intruders_in_roi,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
ORDER BY timestamp DESC
LIMIT 500
```

### 2-E — Time gap between consecutive alerts per camera — Table

**Panel type:** Table

```sql
SELECT
    camera_id,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    ROUND(
        EXTRACT(EPOCH FROM (
            timestamp - LAG(timestamp) OVER (PARTITION BY camera_id ORDER BY timestamp)
        ))
    )                                          AS seconds_since_last_alert
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
ORDER BY camera_id, timestamp
```

---

## 3. PPE Violations Report

**alert_category = 'PPE'**

### 3-A — Daily PPE violation trend — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)                AS violations,
    SUM(in_roi_count)       AS total_non_compliant
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'PPE'
GROUP BY 1
ORDER BY 1
```

### 3-B — Violations by camera / zone — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    COUNT(*)                       AS violation_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_non_compliant
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'PPE'
GROUP BY camera_id, camera_name, edge_name
ORDER BY violation_count DESC
```

### 3-C — Non-compliant item breakdown — Pie chart

**Panel type:** Pie chart

```sql
SELECT
    kv.key                         AS ppe_item,
    SUM((kv.value)::integer)       AS total_detected,
    COUNT(DISTINCT a.id)           AS alert_count
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'PPE'
GROUP BY kv.key
ORDER BY total_detected DESC
```

### 3-D — Recent PPE violations — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    rule_name,
    in_roi_count                               AS non_compliant_count,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'PPE'
ORDER BY timestamp DESC
LIMIT 500
```

---

## 4. People Counting Report

**alert_category = 'Counting'**

### 4-A — Cumulative crossings by camera and rule — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    rule.key                           AS rule_name,
    SUM((rule.value ->> 'in')::integer)  AS total_in,
    SUM((rule.value ->> 'out')::integer) AS total_out,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS net_count
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Counting'
GROUP BY a.camera_id, a.camera_name, a.edge_name, rule.key
ORDER BY total_in DESC
```

### 4-B — Hourly crossing volume — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'in')::integer, 0)
    )                                  AS crossings_in,
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'out')::integer, 0)
    )                                  AS crossings_out
FROM app_alert a,
     jsonb_object_keys(a.crossing_counts) rule
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Counting'
GROUP BY 1
ORDER BY 1
```

### 4-C — Daily entry/exit balance per camera — Time series

**Panel type:** Time series (or Bar chart grouped by camera)

```sql
SELECT
    $__timeGroupAlias(a.timestamp, $__interval),
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    SUM((rule.value ->> 'in')::integer)  AS entries,
    SUM((rule.value ->> 'out')::integer) AS exits,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS occupancy_change
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Counting'
GROUP BY 1, a.camera_id, a.camera_name
ORDER BY 1
```

### 4-D — People count events list — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Counting'
ORDER BY timestamp DESC
LIMIT 500
```

---

## 5. Crowd Detection Report

**alert_category = 'Crowd'**

### 5-A — Daily crowd alerts and peak count — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY 1
ORDER BY 1
```

### 5-B — Crowd level by camera — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd,
    MIN(crowd_count)               AS min_crowd
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY camera_id, camera_name, edge_name
ORDER BY peak_crowd DESC
```

### 5-C — Hourly crowd distribution — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd_count,
    MAX(crowd_count)               AS peak_crowd_count,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY hour
ORDER BY hour
```

### 5-D — Crowd events exceeding threshold — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    crowd_count,
    rule_name,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
  AND crowd_count >= 10            -- adjust threshold
ORDER BY crowd_count DESC, timestamp DESC
LIMIT 200
```

### 5-E — Crowd density in 15-minute buckets — Time series

**Panel type:** Time series

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 15) * INTERVAL '15 minutes') AS time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY 1, camera_id, camera_name
ORDER BY 1
```

> Note: the aliased column must be named `time` for Grafana time series format.

---

## 6. LPR (License Plate Recognition) Report

**alert_category = 'LPR'**

### 6-A — Daily LPR detections — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*) AS detection_events
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'LPR'
GROUP BY 1
ORDER BY 1
```

### 6-B — All detected plates — Table

**Panel type:** Table

```sql
SELECT
    (a.timestamp AT TIME ZONE 'Asia/Jakarta')  AS local_time,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    plate ->> 'plate_number'                   AS plate_number,
    plate ->> 'plate_expiry'                   AS expiry,
    ROUND((plate ->> 'plate_confidence')::numeric * 100, 1) AS confidence_pct
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') IS NOT NULL
  AND (plate ->> 'plate_number') <> ''
ORDER BY a.timestamp DESC
LIMIT 1000
```

### 6-C — Plate frequency (most seen plates) — Table

**Panel type:** Table

```sql
SELECT
    plate ->> 'plate_number'                   AS plate_number,
    COUNT(*)                                   AS appearances,
    MIN(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen,
    COUNT(DISTINCT a.camera_id)                AS camera_count
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') <> ''
GROUP BY plate ->> 'plate_number'
ORDER BY appearances DESC
LIMIT 50
```

### 6-D — Plates by camera — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    COUNT(DISTINCT plate ->> 'plate_number')   AS unique_plates,
    COUNT(*)                                   AS total_reads
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') <> ''
GROUP BY a.camera_id, a.camera_name, a.edge_name
ORDER BY total_reads DESC
```

### 6-E — Plate search (uses `$plate_search` variable) — Table

**Panel type:** Table

```sql
SELECT
    (a.timestamp AT TIME ZONE 'Asia/Jakarta')  AS local_time,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    plate ->> 'plate_number'                   AS plate_number,
    plate ->> 'plate_expiry'                   AS expiry,
    ROUND((plate ->> 'plate_confidence')::numeric * 100, 1) AS confidence_pct
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') ILIKE '%${plate_search}%'
ORDER BY a.timestamp DESC
LIMIT 200
```

---

## 7. Cross-Category Dashboard Queries

### 7-A — Fleet overview — alerts today — Stat panels

**Panel type:** Stat (one panel per category, or use repeat)

```sql
SELECT
    alert_category,
    COUNT(*)                       AS today_count,
    COUNT(DISTINCT camera_id)      AS cameras_affected,
    COUNT(DISTINCT edge_name)      AS edges_affected
FROM app_alert
WHERE timestamp::date = CURRENT_DATE
GROUP BY alert_category
ORDER BY today_count DESC
```

> Use **Panel repeat** with the `$category` variable to generate one Stat card per category automatically.

### 7-B — Alert volume by edge and category — Time series / Bar chart

**Panel type:** Bar chart (stacked)

```sql
SELECT
    edge_name,
    alert_category,
    COUNT(*) AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY edge_name, alert_category
ORDER BY edge_name, alert_count DESC
```

### 7-C — Top 10 busiest cameras — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS total_alerts,
    COUNT(DISTINCT alert_category) AS category_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY camera_id, camera_name, edge_name
ORDER BY total_alerts DESC
LIMIT 10
```

### 7-D — 30-minute alert heatmap — Time series

**Panel type:** Time series (or Heatmap)

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 30) * INTERVAL '30 minutes') AS time,
    alert_category,
    COUNT(*) AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY 1, alert_category
ORDER BY 1
```

### 7-E — Alerts this week vs last week — Bar chart (grouped)

**Panel type:** Bar chart

```sql
SELECT
    alert_category,
    COUNT(*) FILTER (WHERE timestamp >= date_trunc('week', NOW()))             AS this_week,
    COUNT(*) FILTER (WHERE timestamp >= date_trunc('week', NOW()) - INTERVAL '7 days'
                       AND timestamp <  date_trunc('week', NOW()))             AS last_week
FROM app_alert
WHERE timestamp >= date_trunc('week', NOW()) - INTERVAL '7 days'
GROUP BY alert_category
ORDER BY this_week DESC
```

### 7-F — Live alert feed — Table with auto-refresh

**Panel type:** Table (set dashboard refresh to `30s` or `1m`)

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    alert_category,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    snapshot_message
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '2 hours'
ORDER BY timestamp DESC
LIMIT 100
```

---

## 8. Grafana Dashboard Layout Suggestions

### Dashboard 1: Fleet Operations Overview

| Panel | Type | Query | Size |
|-------|------|-------|------|
| Total alerts today | Stat | Q7-A `SUM(today_count)` | 2×2 |
| Active cameras | Stat | `COUNT(DISTINCT camera_id) WHERE timestamp::date = CURRENT_DATE` | 2×2 |
| Active edges | Stat | `COUNT(DISTINCT edge_name) WHERE timestamp::date = CURRENT_DATE` | 2×2 |
| Alert trend (time range) | Time series | Q0 | 12×6 |
| Alerts by category today | Bar chart | Q7-A | 6×5 |
| Top cameras | Bar chart (horizontal) | Q7-C | 6×5 |
| Alerts by edge (stacked) | Bar chart | Q7-B | 12×5 |
| 30-min heatmap | Time series | Q7-D | 12×5 |
| This week vs last week | Bar chart | Q7-E | 8×5 |
| Live alert feed | Table | Q7-F | 12×6 |

### Dashboard 2: Object Detection & Intrusion

| Panel | Type | Query |
|-------|------|-------|
| Detection trend | Time series | Q1-A |
| Intrusion trend | Time series | Q2-A |
| Top detection cameras | Bar chart | Q1-B |
| Peak intrusion hours | Bar chart | Q2-C |
| Top detected classes | Pie chart | Q1-E |
| Hourly detection heatmap | Heatmap | Q1-D |
| Most triggered cameras | Bar chart | Q2-B |
| Recent alerts | Table | Q1-F |

### Dashboard 3: People Counting & Crowd

| Panel | Type | Query |
|-------|------|-------|
| Crossings IN time series | Time series | Q4-B |
| Crossings by camera/rule | Bar chart | Q4-A |
| Daily entry/exit balance | Time series | Q4-C |
| Daily crowd alerts | Time series | Q5-A |
| Peak crowd by camera | Bar chart | Q5-B |
| Hourly crowd distribution | Bar chart | Q5-C |
| Crowd density (15 min) | Time series | Q5-E |
| Crowd events > threshold | Table | Q5-D |

### Dashboard 4: PPE & Safety Compliance

| Panel | Type | Query |
|-------|------|-------|
| Daily violations | Time series | Q3-A |
| Violations by zone | Bar chart | Q3-B |
| Missing PPE items | Pie chart | Q3-C |
| Recent violations | Table | Q3-D |

### Dashboard 5: LPR — License Plate

| Panel | Type | Query |
|-------|------|-------|
| Daily plate reads | Time series | Q6-A |
| Plates by camera | Bar chart | Q6-D |
| Most frequent plates | Table | Q6-C |
| All plate reads | Table | Q6-B |
| Plate search | Table | Q6-E (uses `$plate_search`) |

---

## 9. Grafana Variables & Dashboard Filters

All variables defined above appear as dropdowns at the top of each dashboard.
Link them to panels by adding `AND` clauses:

```sql
-- Edge filter (multi-value — wrap in array)
AND edge_name = ANY(ARRAY[$edge]::text[])

-- Category filter
AND alert_category = ANY(ARRAY[$category]::text[])

-- Camera filter (matches camera_name or camera_id)
AND (
    COALESCE(NULLIF(camera_name, ''), camera_id)
    = ANY(ARRAY[$camera]::text[])
)
```

> When **Include All** is enabled and **All value** is set to `.*`, use `~ '$edge'` (regex match) as an
> alternative. The `ANY(ARRAY[...])` pattern is more reliable for multi-select.

---

## 10. Recommended Indexes

Run once in PostgreSQL (same as Metabase — shared database):

```sql
-- Already created by Django migrations:
-- CREATE INDEX app_alert_edge_name_timestamp ON app_alert (edge_name, timestamp DESC);
-- CREATE INDEX app_alert_alert_category_timestamp ON app_alert (alert_category, timestamp DESC);
-- CREATE INDEX app_alert_camera_id ON app_alert (camera_id);

-- Additional indexes for time-range queries:
CREATE INDEX IF NOT EXISTS idx_alert_ts_date
    ON app_alert (timestamp DESC);

CREATE INDEX IF NOT EXISTS idx_alert_cat_cam_ts
    ON app_alert (alert_category, camera_id, timestamp DESC);

-- Partial index for LPR
CREATE INDEX IF NOT EXISTS idx_alert_lpr
    ON app_alert (timestamp DESC)
    WHERE alert_category = 'LPR';

-- Partial index for crowd_count queries
CREATE INDEX IF NOT EXISTS idx_alert_crowd
    ON app_alert (crowd_count DESC, timestamp DESC)
    WHERE alert_category = 'Crowd';
```

---

## 11. JSON Field Notes

All JSON columns (`cls_name_summary`, `crossing_counts`, `lpr_plates`) are stored as **JSONB** in PostgreSQL.

| Operation | Syntax |
|-----------|--------|
| Get key value | `col ->> 'key'` → text |
| Get nested object | `col -> 'key'` → jsonb |
| Iterate object keys+values | `jsonb_each(col)` → `(key text, value jsonb)` |
| Iterate array elements | `jsonb_array_elements(col)` → `jsonb` |
| Cast to number | `(col ->> 'key')::integer` or `::numeric` |
| Case-insensitive search | `col ->> 'key' ILIKE '%search%'` |

---

## 12. Grafana Tips

### Time series panels

- Set **Format as: Time series** in the query options
- Enable **Fill value: 0** so gaps show as zero instead of null (avoids broken lines)
- Use `GROUP BY 1` (not column name) when the first column is a `$__timeGroupAlias` macro result

### Stat panels for KPIs

```sql
-- Total alerts in selected time range
SELECT COUNT(*) AS value
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
```

Set **Reduce: Last** and enable **Show: Calculation** in the panel options.

### Snapshot images in Table panels

Add the `snapshot_path` column to Table panels. In **Override** settings, apply **Data links**:

- Title: `View Snapshot`
- URL: `https://storage.googleapis.com/soca-snapshot-bucket/${__value.text}` (for GCS)

This makes each row clickable to open the snapshot image.

### Auto-refresh for live monitoring

In Dashboard Settings → Time options:
- Set **Auto-refresh** options to include `10s`, `30s`, `1m`
- Set default refresh on the live feed dashboard to `30s`

### Alerting

Grafana supports native alerts on any panel. Recommended alert rules:

| Alert | Query | Condition |
|-------|-------|-----------|
| Intrusion spike | Q2-A | `intrusions > 5` in last 5 min |
| Crowd threshold exceeded | Q5-A | `peak_crowd > 20` in last 5 min |
| High PPE violation rate | Q3-A | `violations > 10` in last 15 min |
| LPR camera offline | Q6-A | `detection_events = 0` for 30 min |
