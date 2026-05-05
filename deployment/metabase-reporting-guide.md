# SOCA — Metabase Reporting Guide

Complete database schema and SQL queries for building Metabase dashboards covering
Object Detection, People Counting, Intrusion, Crowd, and LPR reports.

**Database: PostgreSQL**

---

## Database Overview

soca-control uses **PostgreSQL** in production.
Connect Metabase to the same PostgreSQL instance that soca-control uses.

> **Timezone note:** All timestamps stored in UTC. Set Metabase report timezone to `Asia/Jakarta` (GMT+7)
> in Admin → Settings → Localization → Report Timezone, **or** use `AT TIME ZONE 'Asia/Jakarta'` in queries.

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

## Connecting Metabase to PostgreSQL

1. Metabase → Admin → Databases → Add Database → **PostgreSQL**
2. Fill in: host, port (`5432`), database name, user, password
3. Save — Metabase syncs the schema automatically

---

## Shared Utility Queries

### Q0 — Daily Alert Summary (all categories)

```sql
SELECT
    timestamp::date                AS date,
    alert_category,
    COUNT(*)                       AS alert_count,
    SUM(in_roi_count)              AS total_in_roi,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date, alert_category
ORDER BY date DESC, alert_count DESC
```

---

## 1. Object Detection Report

**alert_category = 'Detection'**

### 1-A — Daily trend

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS alerts,
    SUM(detection_count)           AS total_objects,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

### 1-B — Alerts by camera

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS alert_count,
    SUM(detection_count)           AS total_objects,
    MAX(detection_count)           AS peak_count
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY alert_count DESC
LIMIT 20
```

### 1-C — Alerts by rule name

```sql
SELECT
    rule_name,
    COUNT(*)                       AS alert_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi,
    MAX(detection_count)           AS peak_objects
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY rule_name
ORDER BY alert_count DESC
```

### 1-D — Hourly distribution (heatmap source)

```sql
SELECT
    EXTRACT(HOUR FROM timestamp)::integer      AS hour_of_day,
    EXTRACT(DOW  FROM timestamp)::integer      AS day_of_week,  -- 0=Sun, 6=Sat
    COUNT(*)                                   AS alert_count
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY hour_of_day, day_of_week
ORDER BY day_of_week, hour_of_day
```

### 1-E — Top detected classes

```sql
SELECT
    kv.key                         AS class_name,
    SUM((kv.value)::integer)       AS total_detections
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE a.alert_category = 'Detection'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY kv.key
ORDER BY total_detections DESC
```

### 1-F — Recent alerts list

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
WHERE alert_category = 'Detection'
ORDER BY timestamp DESC
LIMIT 200
```

---

## 2. Intrusion Detection Report

**alert_category = 'Intrusion'**

### 2-A — Daily intrusion trend

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS intrusions,
    SUM(in_roi_count)              AS total_intruders
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

### 2-B — Most triggered cameras

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS intrusion_count,
    MIN(timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY intrusion_count DESC
```

### 2-C — Peak intrusion hours

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    COUNT(*)                       AS intrusion_count
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY hour
ORDER BY hour
```

### 2-D — Intrusion events detail

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    in_roi_count                               AS intruders_in_roi,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Intrusion'
ORDER BY timestamp DESC
LIMIT 500
```

### 2-E — Time gap between consecutive alerts per camera

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
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
ORDER BY camera_id, timestamp
```

---

## 3. PPE Violations Report

**alert_category = 'PPE'**

### 3-A — Daily PPE violation trend

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS violations,
    SUM(in_roi_count)              AS total_non_compliant
FROM app_alert
WHERE alert_category = 'PPE'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

### 3-B — Violations by camera / zone

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    COUNT(*)                       AS violation_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_non_compliant
FROM app_alert
WHERE alert_category = 'PPE'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY violation_count DESC
```

### 3-C — Non-compliant item breakdown

```sql
SELECT
    kv.key                         AS ppe_item,
    SUM((kv.value)::integer)       AS total_detected,
    COUNT(DISTINCT a.id)           AS alert_count
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE a.alert_category = 'PPE'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY kv.key
ORDER BY total_detected DESC
```

### 3-D — Recent PPE violations

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    rule_name,
    in_roi_count                               AS non_compliant_count,
    snapshot_message
FROM app_alert
WHERE alert_category = 'PPE'
ORDER BY timestamp DESC
LIMIT 500
```

---

## 4. People Counting Report

**alert_category = 'Counting'**

### 4-A — Cumulative crossings by camera and rule

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
WHERE a.alert_category = 'Counting'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY a.camera_id, a.camera_name, a.edge_name, rule.key
ORDER BY total_in DESC
```

### 4-B — Hourly crossing volume

```sql
SELECT
    timestamp::date                    AS date,
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'in')::integer, 0)
    )                                  AS crossings_in,
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'out')::integer, 0)
    )                                  AS crossings_out
FROM app_alert a,
     jsonb_object_keys(a.crossing_counts) rule
WHERE a.alert_category = 'Counting'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date, hour
ORDER BY date, hour
```

### 4-C — Daily entry/exit balance per camera

```sql
SELECT
    a.timestamp::date                  AS date,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    SUM((rule.value ->> 'in')::integer)  AS entries,
    SUM((rule.value ->> 'out')::integer) AS exits,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS occupancy_change
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE a.alert_category = 'Counting'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY a.timestamp::date, a.camera_id, a.camera_name
ORDER BY date, camera
```

### 4-D — People count events list

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Counting'
ORDER BY timestamp DESC
LIMIT 500
```

---

## 5. Crowd Detection Report

**alert_category = 'Crowd'**

### 5-A — Daily crowd alerts and peak count

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd,
    SUM(crowd_count)               AS total_people_detected
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

### 5-B — Crowd level by camera

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd,
    MIN(crowd_count)               AS min_crowd
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY peak_crowd DESC
```

### 5-C — Hourly crowd distribution

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd_count,
    MAX(crowd_count)               AS peak_crowd_count,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY hour
ORDER BY hour
```

### 5-D — Crowd events exceeding threshold

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    crowd_count,
    rule_name,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Crowd'
  AND crowd_count >= 10            -- adjust threshold
ORDER BY crowd_count DESC, timestamp DESC
LIMIT 200
```

### 5-E — Crowd density in 15-minute buckets

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 15) * INTERVAL '15 minutes') AS time_bucket,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '7 days'
GROUP BY time_bucket, camera_id, camera_name
ORDER BY time_bucket
```

---

## 6. LPR (License Plate Recognition) Report

**alert_category = 'LPR'**

### 6-A — Daily LPR detections

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS detection_events
FROM app_alert
WHERE alert_category = 'LPR'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

### 6-B — All detected plates

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
WHERE a.alert_category = 'LPR'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
  AND (plate ->> 'plate_number') IS NOT NULL
  AND (plate ->> 'plate_number') <> ''
ORDER BY a.timestamp DESC
LIMIT 1000
```

### 6-C — Plate frequency (most seen plates)

```sql
SELECT
    plate ->> 'plate_number'                   AS plate_number,
    COUNT(*)                                   AS appearances,
    MIN(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen,
    COUNT(DISTINCT a.camera_id)                AS camera_count
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE a.alert_category = 'LPR'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
  AND (plate ->> 'plate_number') <> ''
GROUP BY plate ->> 'plate_number'
ORDER BY appearances DESC
LIMIT 50
```

### 6-D — Plates by camera

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    COUNT(DISTINCT plate ->> 'plate_number')   AS unique_plates,
    COUNT(*)                                   AS total_reads
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE a.alert_category = 'LPR'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
  AND (plate ->> 'plate_number') <> ''
GROUP BY a.camera_id, a.camera_name, a.edge_name
ORDER BY total_reads DESC
```

### 6-E — Plate search (use `{{plate_search}}` as Metabase text variable)

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
WHERE a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') ILIKE '%' || {{plate_search}} || '%'
ORDER BY a.timestamp DESC
LIMIT 200
```

---

## 7. Cross-Category Dashboard Queries

### 7-A — Fleet overview — all categories today

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

### 7-B — Alert volume by edge and category

```sql
SELECT
    edge_name,
    alert_category,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '30 days'
GROUP BY edge_name, alert_category
ORDER BY edge_name, alert_count DESC
```

### 7-C — Top 10 busiest cameras (all categories, last 7 days)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS total_alerts,
    COUNT(DISTINCT alert_category) AS category_count
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '7 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY total_alerts DESC
LIMIT 10
```

### 7-D — 30-minute alert heatmap (last 24 hours)

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 30) * INTERVAL '30 minutes') AS time_bucket,
    alert_category,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '24 hours'
GROUP BY time_bucket, alert_category
ORDER BY time_bucket
```

### 7-E — Alerts this week vs last week

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

---

## 8. Metabase Dashboard Layout Suggestions

### Dashboard 1: Fleet Operations Overview

| Card | Type | Query |
|------|------|-------|
| Total alerts today | Number | Q7-A `SUM(today_count)` |
| Alert trend (30 days) | Line chart | Q0 grouped by date |
| Alerts by category today | Bar chart | Q7-A |
| Top cameras (7 days) | Table | Q7-C |
| Alerts by edge | Stacked bar | Q7-B |
| 30-min heatmap | Bar chart | Q7-D |
| This week vs last week | Row chart | Q7-E |

### Dashboard 2: Object Detection & Intrusion

| Card | Type | Query |
|------|------|-------|
| Detection trend | Line chart | Q1-A |
| Intrusion trend | Line chart | Q2-A |
| Top detection cameras | Bar chart | Q1-B |
| Peak intrusion hours | Bar chart | Q2-C |
| Top detected classes | Pie / Bar | Q1-E |
| Recent alerts | Table | Q1-F |

### Dashboard 3: People Counting & Crowd

| Card | Type | Query |
|------|------|-------|
| Daily entries / exits | Line chart | Q4-C |
| Crossings by camera | Bar chart | Q4-A |
| Daily crowd alerts | Line chart | Q5-A |
| Peak crowd by camera | Bar chart | Q5-B |
| Hourly crowd distribution | Bar chart | Q5-C |
| Crowd density (15 min) | Line chart | Q5-E |
| Crowd events > threshold | Table | Q5-D |

### Dashboard 4: PPE & Safety Compliance

| Card | Type | Query |
|------|------|-------|
| Daily violations | Line chart | Q3-A |
| Violations by zone | Bar chart | Q3-B |
| Missing PPE items | Pie chart | Q3-C |
| Recent violations | Table | Q3-D |

### Dashboard 5: LPR — License Plate

| Card | Type | Query |
|------|------|-------|
| Daily plate reads | Line chart | Q6-A |
| Plates by camera | Bar chart | Q6-D |
| Most frequent plates | Table | Q6-C |
| All plate reads | Table | Q6-B |
| Plate search | Table + text filter | Q6-E |

---

## 9. Metabase Variables & Filters

Add as **dashboard filters** and link to multiple cards:

| Filter name | Type | Column |
|------------|------|--------|
| Date range | Date range | `timestamp` |
| Edge | Text | `edge_name` |
| Camera | Text | `camera_id` |
| Alert category | Category | `alert_category` |
| Plate number | Text | Q6-E `{{plate_search}}` |

---

## 10. Recommended Indexes

Run once in PostgreSQL to speed up Metabase queries:

```sql
-- Already created by Django migrations:
-- CREATE INDEX app_alert_edge_name_timestamp ON app_alert (edge_name, timestamp DESC);
-- CREATE INDEX app_alert_alert_category_timestamp ON app_alert (alert_category, timestamp DESC);
-- CREATE INDEX app_alert_camera_id ON app_alert (camera_id);

-- Additional indexes for reporting:
CREATE INDEX IF NOT EXISTS idx_alert_ts_date
    ON app_alert (timestamp DESC);

CREATE INDEX IF NOT EXISTS idx_alert_cat_cam_ts
    ON app_alert (alert_category, camera_id, timestamp DESC);

-- Partial index for LPR (speeds up plate queries on large tables)
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
