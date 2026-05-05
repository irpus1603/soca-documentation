# SOCA Dashboard — Functional Test Scenarios

**Application:** soca-dashboard  
**Test type:** Functional (browser-based, user perspective)  
**Executed by:** Claude Code with Chrome DevTools connector  
**Base URL:** `http://localhost:8000`

---

## Table of Contents

1. [Authentication](#1-authentication)
2. [Dashboard](#2-dashboard)
3. [Camera Management](#3-camera-management)
4. [Schedule Management](#4-schedule-management)
5. [Rule Management](#5-rule-management)
6. [Monitor (Live Stream)](#6-monitor-live-stream)
7. [Alerts & Snapshots](#7-alerts--snapshots)
8. [Alert Statistics](#8-alert-statistics)
9. [Settings — Branding](#9-settings--branding)
10. [Settings — Edge Configuration](#10-settings--edge-configuration)
11. [Settings — User Management](#11-settings--user-management)
12. [Settings — AI Models](#12-settings--ai-models)
13. [Settings — Operations (Purge & Restart)](#13-settings--operations-purge--restart)

---

## 1. Authentication

### TC-AUTH-001 — Login with valid credentials (Positive)
**Steps:**
1. Navigate to `http://localhost:8000/login/`
2. Enter valid username and password
3. Click **Login**

**Expected:** Redirected to dashboard (`/`). Navbar shows username and role.

---

### TC-AUTH-002 — Login with wrong password (Negative)
**Steps:**
1. Navigate to `http://localhost:8000/login/`
2. Enter valid username, wrong password
3. Click **Login**

**Expected:** Stay on login page. Error message shown ("Please enter a correct username and password").

---

### TC-AUTH-003 — Login with empty fields (Negative)
**Steps:**
1. Navigate to `http://localhost:8000/login/`
2. Leave username and password blank
3. Click **Login**

**Expected:** Stay on login page. Validation error shown for required fields.

---

### TC-AUTH-004 — Access protected page without login (Negative)
**Steps:**
1. Without logging in, navigate directly to `http://localhost:8000/cameras/`

**Expected:** Redirected to `/login/?next=/cameras/`. Dashboard not accessible.

---

### TC-AUTH-005 — Logout (Positive)
**Steps:**
1. Log in with valid credentials
2. Click **Logout** in the navbar

**Expected:** Redirected to login page. Session cleared — navigating to `/` redirects back to login.

---

## 2. Dashboard

### TC-DASH-001 — Dashboard loads with engine connected (Positive)
**Steps:**
1. Log in
2. Navigate to `/` (dashboard)

**Expected:**
- Page title shows app name
- System health section visible (CPU, memory, or engine status)
- Active cameras count displayed
- Running schedules count displayed

---

### TC-DASH-002 — Dashboard with engine unreachable (Negative)
**Steps:**
1. Log in with soca-engine offline or wrong engine URL configured
2. Navigate to `/`

**Expected:** Dashboard loads without crashing. Engine health section shows error/unavailable status. No unhandled exception.

---

### TC-DASH-003 — Viewer role cannot access settings (Negative)
**Steps:**
1. Log in as a user with **viewer** role
2. Click **Settings** in the navbar (if visible)

**Expected:** Settings menu not visible, or if accessed directly via URL returns 403 / redirect.

---

## 3. Camera Management

### TC-CAM-001 — View camera list (Positive)
**Steps:**
1. Log in as admin
2. Navigate to `/cameras/`

**Expected:** Table lists all cameras with name, site, floor, location, and active status.

---

### TC-CAM-002 — Create a new camera (Positive)
**Steps:**
1. Navigate to `/cameras/new/`
2. Fill in:
   - Name: `test-cam-01`
   - RTSP URL: `rtsp://192.168.1.100:554/stream`
   - Site Name: `HQ`
   - Floor: `1`
   - Location: `Lobby`
   - Username / Password (optional)
   - Is Active: checked
3. Click **Save**

**Expected:**
- Redirected to camera list
- `test-cam-01` appears in the table
- Entry added to `mediamtx.yml` (if MediaMTX path is configured)

---

### TC-CAM-003 — Create camera with duplicate name (Negative)
**Steps:**
1. Create a camera named `test-cam-01`
2. Try to create another camera with the same name `test-cam-01`

**Expected:** Form shows validation error ("Camera with this name already exists"). Second camera not created.

---

### TC-CAM-004 — Create camera with empty name (Negative)
**Steps:**
1. Navigate to `/cameras/new/`
2. Leave **Name** blank, fill other fields
3. Click **Save**

**Expected:** Form validation error for required name field. Camera not created.

---

### TC-CAM-005 — Edit camera metadata only (floor, location) (Positive)
**Steps:**
1. Open edit page for `test-cam-01`
2. Change **Floor** from `1` to `2` and **Location** from `Lobby` to `Entrance`
3. Keep RTSP URL and name unchanged
4. Click **Save**

**Expected:**
- Redirected to camera list with success message
- Updated floor/location visible in list
- `mediamtx.yml` **NOT modified** (MediaMTX stays running)

---

### TC-CAM-006 — Edit camera RTSP URL (Positive)
**Steps:**
1. Open edit page for `test-cam-01`
2. Change **RTSP URL** to `rtsp://192.168.1.101:554/stream`
3. Click **Save**

**Expected:**
- Success message shown
- `mediamtx.yml` updated with new source URL
- Camera list shows updated data

---

### TC-CAM-007 — Delete a camera (Positive)
**Steps:**
1. Navigate to `/cameras/`
2. Click **Delete** for `test-cam-01`
3. Confirm deletion

**Expected:**
- Camera removed from list
- Entry removed from `mediamtx.yml`
- Success message shown

---

### TC-CAM-008 — Delete camera that has active schedules (Negative)
**Steps:**
1. Create a camera and assign it to a running schedule
2. Try to delete that camera

**Expected:** Error message shown ("Cannot delete camera with active schedules") or cascading delete handled gracefully. No orphaned schedule records.

---

### TC-CAM-009 — Grab live snapshot from camera (Positive)
**Steps:**
1. Navigate to `/cameras/`
2. Click **Snapshot** button for a camera with a reachable RTSP stream

**Expected:** JPEG image returned and displayed in browser or downloaded.

---

### TC-CAM-010 — Grab snapshot from unreachable camera (Negative)
**Steps:**
1. Click **Snapshot** for a camera with an unreachable RTSP URL

**Expected:** 404 or error response returned. No crash. Graceful error message shown.

---

## 4. Schedule Management

### TC-SCH-001 — View schedule list (Positive)
**Steps:**
1. Navigate to `/schedules/`

**Expected:** Table lists all schedules with name, camera, model, and current status (running/stopped).

---

### TC-SCH-002 — Create a new schedule (Positive)
**Steps:**
1. Navigate to `/schedules/new/`
2. Fill in:
   - Name: `schedule-lobby`
   - Camera: select `test-cam-01`
   - Model: `yolo/yolo11n.pt`
   - Confidence: `0.5`
   - Frame Interval: `1000` ms
   - Alert Category: `Detection`
   - Save Snapshot: checked
3. Click **Save**

**Expected:**
- Redirected to schedule list
- `schedule-lobby` appears with status **Stopped**

---

### TC-SCH-003 — Create schedule with no camera selected (Negative)
**Steps:**
1. Navigate to `/schedules/new/`
2. Leave **Camera** unselected
3. Fill all other required fields
4. Click **Save**

**Expected:** Validation error for required camera field. Schedule not created.

---

### TC-SCH-004 — Start a schedule (Positive)
**Steps:**
1. Navigate to `/schedules/`
2. Click **Start** for `schedule-lobby`

**Expected:**
- Status changes to **Running**
- `current_job_id` populated
- soca-engine starts the job

---

### TC-SCH-005 — Start a schedule with engine unreachable (Negative)
**Steps:**
1. Set engine URL to an invalid address
2. Click **Start** for a stopped schedule

**Expected:** Error message shown ("Failed to start job" or connection error). Schedule status remains **Stopped**.

---

### TC-SCH-006 — Stop a running schedule (Positive)
**Steps:**
1. Start a schedule
2. Click **Stop**

**Expected:**
- Status changes to **Stopped**
- `current_job_id` cleared

---

### TC-SCH-007 — Edit a running schedule (Negative)
**Steps:**
1. Start a schedule
2. Navigate to its edit page
3. Change the model and save

**Expected:** Warning shown that job is currently running, or schedule stops/updates are handled safely. No engine crash.

---

### TC-SCH-008 — Delete a stopped schedule (Positive)
**Steps:**
1. Ensure schedule is stopped
2. Click **Delete**
3. Confirm

**Expected:** Schedule removed from list. Success message shown.

---

### TC-SCH-009 — Delete a running schedule (Negative)
**Steps:**
1. Start a schedule
2. Try to delete it

**Expected:** Schedule is stopped first, then deleted. Or error message requiring stop before delete.

---

### TC-SCH-010 — Create schedule with invalid confidence threshold (Negative)
**Steps:**
1. Navigate to `/schedules/new/`
2. Set **Confidence** to `1.5` (out of 0–1 range)
3. Click **Save**

**Expected:** Validation error shown. Schedule not created.

---

## 5. Rule Management

### TC-RULE-001 — Add a rule to a schedule (Positive)
**Steps:**
1. Open edit page for `schedule-lobby`
2. Click **Add Rule**
3. Fill in:
   - Name: `intrusion-rule`
   - Category: `Intrusion`
   - Processing: `in_roi`
   - Trigger: `present`
   - Duration: `immediate`
   - Cooldown: `60` seconds
   - Actions: Snapshot checked
4. Click **Save Rule**

**Expected:** Rule `intrusion-rule` appears in the rules list for this schedule.

---

### TC-RULE-002 — Add rule with empty name (Negative)
**Steps:**
1. Open schedule edit page
2. Click **Add Rule**
3. Leave **Name** blank
4. Click **Save Rule**

**Expected:** Validation error for required name. Rule not created.

---

### TC-RULE-003 — Edit existing rule (Positive)
**Steps:**
1. Open rule edit for `intrusion-rule`
2. Change cooldown from `60` to `120`
3. Save

**Expected:** Rule updated. Cooldown shows `120` seconds.

---

### TC-RULE-004 — Delete a rule (Positive)
**Steps:**
1. Click **Delete** on `intrusion-rule`
2. Confirm

**Expected:** Rule removed from schedule. Success message shown.

---

### TC-RULE-005 — Add People Counting rule (Positive)
**Steps:**
1. Add rule with:
   - Mode: `people_count`
   - Direction: `left_to_right`
   - Count Threshold: `5`
   - Category: `Counting`

**Expected:** Rule saved with crossing direction and count threshold fields.

---

## 6. Monitor (Live Stream)

### TC-MON-001 — Monitor page loads with running jobs (Positive)
**Steps:**
1. Start a schedule with **Enable Monitor** checked
2. Navigate to `/monitor/`

**Expected:** Live MJPEG stream visible for the running job. Camera name displayed above stream.

---

### TC-MON-002 — Monitor page with no running jobs (Negative)
**Steps:**
1. Stop all running schedules
2. Navigate to `/monitor/`

**Expected:** Empty state message shown ("No active streams"). Page does not crash.

---

### TC-MON-003 — Monitor page with engine unreachable (Negative)
**Steps:**
1. Set engine URL to unreachable address
2. Navigate to `/monitor/`

**Expected:** Error state shown. Page loads without crashing.

---

## 7. Alerts & Snapshots

### TC-ALERT-001 — View alerts list (Positive)
**Steps:**
1. Navigate to `/alerts/`

**Expected:** Paginated table of detection events with timestamp, rule name, camera, count, category, snapshot thumbnail.

---

### TC-ALERT-002 — Filter alerts by camera (Positive)
**Steps:**
1. Navigate to `/alerts/`
2. Select a specific camera from the **Camera** filter
3. Apply filter

**Expected:** Only events from selected camera shown. Pagination recalculates.

---

### TC-ALERT-003 — Filter alerts by date range (Positive)
**Steps:**
1. Set **Date From** and **Date To** to a valid range
2. Apply filter

**Expected:** Only events within date range shown. Events outside range excluded.

---

### TC-ALERT-004 — Filter alerts by date range where To < From (Negative)
**Steps:**
1. Set **Date From** to today, **Date To** to yesterday
2. Apply filter

**Expected:** Zero results shown, or validation message. No crash.

---

### TC-ALERT-005 — Filter by alert category (Positive)
**Steps:**
1. Enter `Intrusion` in the **Category** filter
2. Apply

**Expected:** Only intrusion alerts shown.

---

### TC-ALERT-006 — Filter by LPR plate number (Positive)
**Steps:**
1. Enter a known plate number in **Plate** filter
2. Apply

**Expected:** Only alerts matching that plate shown.

---

### TC-ALERT-007 — View snapshot thumbnail (Positive)
**Steps:**
1. Open `/alerts/`
2. Verify snapshot thumbnails load for events with GCS storage configured

**Expected:** Thumbnail images load (`/alerts/snapshot/?b=...` returns 302 → GCS signed URL → image).

---

### TC-ALERT-008 — Click thumbnail to open full image (Positive)
**Steps:**
1. Click a thumbnail image

**Expected:** Popup window opens with full-size snapshot image from GCS.

---

### TC-ALERT-009 — Alerts with engine DB not configured (Negative)
**Steps:**
1. Clear the **Engine DB Path** in settings
2. Navigate to `/alerts/`

**Expected:** Empty state or info message ("Engine DB not configured"). No exception shown to user.

---

### TC-ALERT-010 — Pagination works correctly (Positive)
**Steps:**
1. Navigate to `/alerts/` with more than 20 events
2. Click **Next page**

**Expected:** Next 20 events loaded. Page number updates. Active filters preserved across pages.

---

## 8. Alert Statistics

### TC-STATS-001 — Statistics page loads (Positive)
**Steps:**
1. Navigate to `/alerts/stats/`

**Expected:**
- Daily detection trend chart rendered
- Hourly distribution chart rendered
- Top categories chart rendered
- Top cameras chart rendered
- Summary counters (today, week, month) visible

---

### TC-STATS-002 — Statistics with camera filter (Positive)
**Steps:**
1. Navigate to `/alerts/stats/?camera=test-cam-01`

**Expected:** All charts filtered to show data only for `test-cam-01`.

---

### TC-STATS-003 — Statistics with no data (Negative)
**Steps:**
1. Apply date filter with a range that has no events

**Expected:** Charts render empty (zero data). No crash. Counters show `0`.

---

## 9. Settings — Branding

### TC-BRAND-001 — Update company name (Positive)
**Steps:**
1. Navigate to `/settings/`
2. In the **Branding** section, change company name to `PT Test Company`
3. Click **Save**

**Expected:** Success message shown. Navbar/header reflects new company name on page reload.

---

### TC-BRAND-002 — Upload company logo (Positive)
**Steps:**
1. Navigate to `/settings/`
2. Upload a PNG or JPG file as company logo
3. Set logo height to `50`
4. Click **Save**

**Expected:** Logo visible in navbar. Height applied correctly.

---

### TC-BRAND-003 — Upload non-image file as logo (Negative)
**Steps:**
1. Try to upload a `.pdf` or `.txt` file as company logo

**Expected:** Validation error shown. Logo not changed.

---

### TC-BRAND-004 — Set logo height below minimum (Negative)
**Steps:**
1. Set logo height to `5` (below 16px minimum)
2. Save

**Expected:** Validation error ("Ensure this value is greater than or equal to 16"). Logo height not changed.

---

## 10. Settings — Edge Configuration

### TC-EDGE-001 — Save valid engine URL (Positive)
**Steps:**
1. Navigate to `/settings/`
2. In **Edge** section, set Engine URL to `http://localhost:8001`
3. Click **Save**

**Expected:** Success message. Engine URL updated.

---

### TC-EDGE-002 — Save invalid engine URL format (Negative)
**Steps:**
1. Set Engine URL to `not-a-url`
2. Click **Save**

**Expected:** Validation error shown. URL not saved.

---

### TC-EDGE-003 — Push config to soca-engine (Positive)
**Steps:**
1. Ensure soca-engine is running and API key matches
2. Click **Push to Engine**

**Expected:** Success message. Last push timestamp updated. `last_engine_push_ok = true`.

---

### TC-EDGE-004 — Push config to engine with wrong API key (Negative)
**Steps:**
1. Set an incorrect engine API key
2. Click **Push to Engine**

**Expected:** Error message ("Push failed" or "401 Unauthorized"). `last_engine_push_ok = false`.

---

### TC-EDGE-005 — Generate new API key (Positive)
**Steps:**
1. Navigate to `/settings/`
2. Click **Generate API Key**

**Expected:** New 64-character hex key displayed. Old key replaced.

---

### TC-EDGE-006 — Save GCS credentials file (Positive)
**Steps:**
1. Upload a valid GCS service account JSON file
2. Click **Save**

**Expected:** File saved to `credentials/` folder. Path stored in settings.

---

### TC-EDGE-007 — Save Telegram credentials (Positive)
**Steps:**
1. Enter a valid Telegram bot token and chat ID
2. Click **Save**

**Expected:** Credentials saved. No error shown.

---

## 11. Settings — User Management

### TC-USER-001 — Create a new user (Positive)
**Steps:**
1. Navigate to `/settings/`
2. In **Users** section, fill:
   - Username: `testuser`
   - Password: `Test@12345`
   - Role: `operator`
3. Click **Create User**

**Expected:** `testuser` appears in the users table with role `operator`.

---

### TC-USER-002 — Create user with duplicate username (Negative)
**Steps:**
1. Try to create a user with the same username as an existing user

**Expected:** Error message ("A user with that username already exists"). User not created.

---

### TC-USER-003 — Create user with empty password (Negative)
**Steps:**
1. Leave password blank
2. Click **Create User**

**Expected:** Validation error for required password. User not created.

---

### TC-USER-004 — Delete a user (Positive)
**Steps:**
1. Click **Delete** for `testuser`
2. Confirm

**Expected:** `testuser` removed from users table.

---

### TC-USER-005 — Delete own account (Negative)
**Steps:**
1. Log in as `admin`
2. Try to delete your own account from settings

**Expected:** Error message ("Cannot delete your own account"). Admin account not deleted.

---

### TC-USER-006 — Operator cannot access user management (Negative)
**Steps:**
1. Log in as operator role user
2. Navigate to `/settings/`

**Expected:** User management section not visible, or action returns 403.

---

## 12. Settings — AI Models

### TC-MODEL-001 — View available models (Positive)
**Steps:**
1. Navigate to `/settings/` → **AI Models** tab

**Expected:** List of model files from soca-engine shown (e.g. `yolo11n.pt`).

---

### TC-MODEL-002 — Upload a new YOLO model (Positive)
**Steps:**
1. Click **Upload Model**
2. Select a `.pt` or `.onnx` file
3. Confirm upload

**Expected:** New model appears in models list. Success message shown.

---

### TC-MODEL-003 — Upload non-model file (Negative)
**Steps:**
1. Try to upload a `.txt` or `.jpg` file as a model

**Expected:** Error message ("Invalid file type"). File not uploaded.

---

### TC-MODEL-004 — Delete a model (Positive)
**Steps:**
1. Click **Delete** for an unused model
2. Confirm

**Expected:** Model removed from list. Success message shown.

---

### TC-MODEL-005 — Delete model currently used by a schedule (Negative)
**Steps:**
1. Create a schedule using `yolo11n.pt`
2. Try to delete `yolo11n.pt` from the models list

**Expected:** Error or warning shown ("Model in use by active schedule"). Model not deleted, or warning displayed.

---

## 13. Settings — Operations (Purge & Restart)

### TC-OPS-001 — Preview data purge (Positive)
**Steps:**
1. Navigate to `/settings/` → **Operations** tab
2. Set **Older than** to `30` days
3. Click **Preview**

**Expected:** Shows count of events and estimated storage to be deleted. No data deleted yet.

---

### TC-OPS-002 — Execute data purge (Positive)
**Steps:**
1. Preview purge for 30 days (verify count > 0)
2. Click **Execute Purge**
3. Confirm

**Expected:** Events older than 30 days deleted. Success message with count shown. Snapshot files removed.

---

### TC-OPS-003 — Purge with zero days (Negative)
**Steps:**
1. Set **Older than** to `0`
2. Click **Preview**

**Expected:** Error or warning ("Value must be greater than 0"). No purge executed.

---

### TC-OPS-004 — Restart soca-dashboard service (Positive)
**Steps:**
1. Navigate to `/settings/` → **Operations** tab
2. Click **Restart soca-dashboard**
3. Confirm

**Expected:** Success message ("Restart triggered"). Service restarts in background. After ~5 seconds, page reloads and is accessible again.

---

### TC-OPS-005 — Restart soca-engine service (Positive)
**Steps:**
1. Click **Restart soca-engine**
2. Confirm

**Expected:** Success message. soca-engine restarts. Running schedules resume after restart.

---

### TC-OPS-006 — Restart with stop.sh not found (Negative)
**Steps:**
1. Configure a service path that does not contain `stop.sh`
2. Click **Restart**

**Expected:** Error message ("stop.sh / start.sh not found"). Service not restarted. Error details shown.

---

## Test Execution Notes

### Prerequisites
- soca-dashboard running at `http://localhost:8000`
- At least one admin user exists
- soca-engine running at configured URL (for engine-dependent tests)
- GCS bucket configured (for snapshot/thumbnail tests)
- MediaMTX running (for camera relay tests)

### Test Accounts Needed
| Username | Password | Role |
|----------|----------|------|
| admin | (configured) | admin |
| operator | (configured) | operator |
| viewer | (configured) | viewer |

### Scope Exclusions
- soca-engine internal detection logic (tested separately)
- GCS bucket permissions (infrastructure level)
- Telegram delivery (external dependency)
- Redis stream consumption (soca-service scope)
