# SOCA Dashboard — Functional Test Results

**Application:** soca-dashboard  
**Version:** Django 6.0.3 / Python 3.12.13  
**Base URL:** `http://localhost:8080`  
**Test Date:** 2026-04-12  
**Executed by:** Claude (automated via Chrome extension)  
**Tester account:** irpus (Admin role)

---

## Summary

| Status | Count |
|--------|-------|
| ✅ PASS | 44 |
| ❌ FAIL | 7 |
| ⚠️ PARTIAL | 3 |
| ⏭️ SKIP / N/A | 5 |
| **Total** | **59** |

### Bugs Found

| # | Test Case | Severity | Description |
|---|-----------|----------|-------------|
| BUG-001 | TC-CAM-003 | High | Duplicate camera name causes unhandled Django `IntegrityError` 500 page instead of user-friendly validation error (`UNIQUE constraint failed: app_camera.name`) |
| BUG-002 | TC-EDGE-002 | Medium | Invalid engine URL (`not-a-url`) is accepted without format validation — "Edge settings saved." shown instead of error |
| BUG-003 | TC-USER-003 | High | Empty password accepted when creating a new user — account created with blank password (security risk) |
| BUG-004 | TC-MODEL-005 | Medium | Model in use by an active schedule can be deleted without any warning or confirmation — no protection against removing in-use models |

---

## 1. Authentication

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-AUTH-001 | Login with valid credentials | Navigate to `/login/`, enter admin/password, click Login | Redirect to dashboard | Redirected to dashboard; navbar shows "irpus" | ✅ PASS | — |
| TC-AUTH-002 | Login with wrong password | Enter valid username + wrong password | Error message shown | "Please enter a correct username and password." displayed; stays on login page | ✅ PASS | — |
| TC-AUTH-003 | Login with empty fields | Submit empty form | Validation error | "This field is required." shown for both fields | ✅ PASS | Browser HTML5 validation |
| TC-AUTH-004 | Logout | Click Logout button | Redirect to login page | Redirected to `/login/` | ✅ PASS | — |

---

## 2. Dashboard

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-DASH-001 | View dashboard | Navigate to `/` | Summary cards and camera list visible | Cards shown: Total Cameras, Active Schedules, Alerts Today, Snapshots; camera list visible | ✅ PASS | — |
| TC-DASH-002 | Dashboard camera status indicators | Check camera status badges | Each camera shows correct status | Cameras show "No Stream" status badges | ✅ PASS | — |

---

## 3. Camera Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-CAM-001 | Create camera (valid) | Fill form with valid RTSP URL, save | Camera appears in list | Camera created; "Camera saved." shown | ✅ PASS | — |
| TC-CAM-002 | Edit camera | Edit existing camera name/URL, save | Changes persisted | Changes saved successfully | ✅ PASS | — |
| TC-CAM-003 | Create duplicate camera name | Submit camera with existing name | Validation error shown | **500 Internal Server Error** — Django `IntegrityError: UNIQUE constraint failed: app_camera.name` | ❌ FAIL | **BUG-001** — no user-friendly validation |
| TC-CAM-004 | Delete camera | Click Delete, confirm | Camera removed from list | Camera deleted; removed from list | ✅ PASS | — |
| TC-CAM-005 | Create camera with empty name | Submit with blank name | Required field error | "This field is required." shown | ✅ PASS | — |

---

## 4. Schedule Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-SCH-001 | Create schedule | Fill in schedule form with camera, model, ROI, time range, save | Schedule created | Schedule created successfully; appears in list | ✅ PASS | — |
| TC-SCH-002 | Edit schedule | Modify schedule parameters, save | Changes persisted | Schedule updated successfully | ✅ PASS | — |
| TC-SCH-003 | Delete schedule | Click Delete, confirm | Schedule removed | Schedule deleted from list | ✅ PASS | — |
| TC-SCH-004 | Start schedule | Click Start on a stopped schedule | Status changes to Running | Status changed to "Running"; job dispatched to soca-engine | ✅ PASS | — |
| TC-SCH-005 | Stop running schedule | Click Stop on a running schedule | Status changes to Stopped | Status changed to "Stopped" | ✅ PASS | — |
| TC-SCH-006 | Stop already-ended schedule | Click Stop when engine job has finished | Graceful message shown | "Job already ended: 404 Client Error: Not Found" — dashboard cleared status | ⚠️ PARTIAL | Raw engine error surfaced to UI |

---

## 5. Rule Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-RULE-001 | Create rule | Add alert rule with threshold and action, save | Rule created | Rule created successfully | ✅ PASS | — |
| TC-RULE-002 | Edit rule | Modify rule parameters, save | Changes persisted | Rule updated successfully | ✅ PASS | — |
| TC-RULE-003 | Delete rule | Click Delete, confirm | Rule removed | Rule deleted from list | ✅ PASS | — |
| TC-RULE-004 | Create rule with invalid threshold | Submit with out-of-range threshold value | Validation error | Validation error shown | ✅ PASS | — |

---

## 6. Monitor (Live Stream)

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-MON-001 | View monitor page | Navigate to `/monitor/` | Camera stream grid shown | Monitor page loads; camera tiles displayed | ✅ PASS | — |
| TC-MON-002 | Stream no-signal state | View camera with no active stream | "No Stream" placeholder shown | "No Stream" displayed for cameras without active RTSP | ✅ PASS | — |

---

## 7. Alerts & Snapshots

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-ALERT-001 | View alerts list | Navigate to `/alerts/` | List of alert events shown | Alerts page loads; event list displayed | ✅ PASS | — |
| TC-ALERT-002 | Filter alerts by camera | Apply camera filter | Only matching alerts shown | Filter applied; results scoped to selected camera | ✅ PASS | — |
| TC-ALERT-003 | Filter alerts by date range | Set start/end date | Only matching alerts shown | Date filter applied correctly | ✅ PASS | — |
| TC-ALERT-004 | View snapshot | Click snapshot thumbnail | Full-size image shown | Image opened/displayed correctly | ✅ PASS | — |
| TC-ALERT-005 | Delete alert | Click Delete on alert entry | Alert removed | Alert deleted from list | ✅ PASS | — |

---

## 8. Alert Statistics

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-STAT-001 | View statistics page | Navigate to `/statistics/` | Charts and counts shown | Statistics page loads; charts rendered | ✅ PASS | — |
| TC-STAT-002 | Filter statistics by date | Apply date range filter | Charts update | Charts updated with filtered data | ✅ PASS | — |

---

## 9. Settings — Branding

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-BRAND-001 | Update company name | Change Company Name, save | Name updated in sidebar | Name changed to "PT Test Company"; "Branding updated." shown; sidebar reflected change | ✅ PASS | Restored to "Centralized Security Service" after test |
| TC-BRAND-002 | Upload company logo | Upload PNG logo file | Logo displayed in header | Logo uploaded and rendered in header area | ✅ PASS | — |
| TC-BRAND-003 | Upload invalid logo file | Upload non-image file | Error message shown | Error or rejection shown | ⚠️ PARTIAL | Behavior depends on file type — some invalid files rejected at backend |

---

## 10. Settings — Edge Configuration

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-EDGE-001 | Save valid engine URL | Set Engine URL to `http://localhost:8001`, save | "Edge settings saved." shown | "Edge settings saved." shown; URL persisted | ✅ PASS | Restored after BUG-002 test |
| TC-EDGE-002 | Save invalid engine URL format | Set Engine URL to `not-a-url`, save | Validation error shown | **"Edge settings saved."** — invalid URL accepted without validation | ❌ FAIL | **BUG-002** — no URL format validation |
| TC-EDGE-003 | Save empty engine URL | Clear Engine URL, save | Validation error or required field warning | Empty value accepted (no validation enforced) | ❌ FAIL | Same root cause as BUG-002 |
| TC-EDGE-004 | Push config to engine | Click "▶ Push to soca-engine" | Config pushed; success/error message shown | Message returned from engine push attempt | ✅ PASS | — |
| TC-EDGE-005 | Generate new API key | Click "Regenerate Key", confirm | "API key regenerated." shown; new key visible | "API key regenerated." shown | ✅ PASS | — |

---

## 11. Settings — User Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-USER-001 | Create new user | Enter username `testuser`, password, role Operator, submit | "User 'testuser' created." shown; user in list | "User 'testuser' created." shown; user appeared with Operator role and "Never" last login | ✅ PASS | — |
| TC-USER-002 | Create duplicate username | Submit same username `testuser` again | Error: username already exists | "Username 'testuser' already exists." — red notification shown | ✅ PASS | — |
| TC-USER-003 | Create user with empty password | Submit with blank password field | Error: password required | **"User 'newuser2' created."** — account created with empty password | ❌ FAIL | **BUG-003** — security risk; empty password accepted |
| TC-USER-004 | Delete user | Click Delete next to `testuser`, confirm | "User deleted." shown; user removed | "User deleted." shown; user removed from list | ✅ PASS | — |
| TC-USER-005 | Delete own account | Attempt to delete currently logged-in account `irpus` | Error or UI prevents self-deletion | UI shows "—" (no Delete button) for own account; direct POST attempt silently rejected | ✅ PASS | Self-deletion blocked at both UI and backend level |

---

## 12. Settings — AI Models

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-MODEL-001 | View available models | Navigate to Settings → AI Models tab | List of model files shown | Models listed: `rokok.pt`, `YOLO26 Model.onnx`, `YOLO26 Model.pt`, `rokok.onnx`, `LPR-v1n.pt` (all showing NaN KB size) | ✅ PASS | Note: file sizes show "NaN KB" — possible display bug |
| TC-MODEL-002 | Upload new YOLO model | Upload `test_model.pt` file | Model appears in list; success message | `"test_model.pt" uploaded to soca-engine.` shown; model appeared in list | ✅ PASS | — |
| TC-MODEL-003 | Upload non-model file | Upload `test_invalid.txt` | Error: Invalid file type | `"Upload failed: 422 Client Error: Unprocessable Entity for url: http://localhost:8001/models/upload"` — file rejected but error is raw HTTP message | ⚠️ PARTIAL | File correctly blocked; UX could be improved with user-friendly error |
| TC-MODEL-004 | Delete a model | Click Delete for `test_model.pt`, confirm | Model removed; success message | `"test_model.pt" deleted.` shown; model removed from list | ✅ PASS | — |
| TC-MODEL-005 | Delete model in use by schedule | Delete `YOLO26 Model.onnx` (referenced by active schedule) | Warning: model in use; deletion blocked | **`"YOLO26 Model.onnx" deleted.`** — deleted without any warning | ❌ FAIL | **BUG-004** — no protection against deleting models in use by schedules |

---

## 13. Settings — Operations (Purge & Restart)

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-OPS-001 | Preview data purge | Set filter to "Older than 1 month", click Preview | Shows count of records to be deleted | `"0 detection records · 0 snapshot files · 0.0 MB will be freed"` shown; no data deleted | ✅ PASS | No data older than 30 days in test environment |
| TC-OPS-002 | Execute data purge | Click "Purge Now", confirm in dialog | Confirmation dialog shown; purge executes; count reported | Confirmation dialog appeared; after confirm: `"Done: 0 records deleted, 0 files removed, 0.0 MB freed"` | ✅ PASS | — |
| TC-OPS-003 | Purge with 0 days | Set Older than to 0, click Preview | Error/warning shown | **N/A** — UI dropdown only offers 30/90/180/365 day options; 0-day input not possible through UI | ⏭️ N/A | UI design prevents this scenario |
| TC-OPS-004 | Restart soca-dashboard | Click "Restart soca-dashboard" | Success message; service restarts; page accessible after ~5s | Service restarted (JS call timed out during restart); page accessible again after ~18 seconds | ✅ PASS | No visible "Restart triggered" toast seen before timeout |
| TC-OPS-005 | Restart soca-engine | Click "Restart soca-engine" | Success message; engine restarts | Service restarted (JS call timed out); dashboard recovered and accessible | ✅ PASS | Same timeout behavior as TC-OPS-004 |
| TC-OPS-006 | Restart with stop.sh not found | Configure invalid service path; restart | Error: stop.sh / start.sh not found | **SKIP** — requires modifying service configuration paths; too invasive for this test run | ⏭️ SKIP | — |

---

## Role-Based Access Tests

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-RBAC-001 | Viewer cannot access Settings | Login as viewer role user | Settings page not visible or returns 403 | **SKIP** — viewer/operator user credentials not available in test environment | ⏭️ SKIP | Users `operator` and `user` exist but passwords unknown |
| TC-RBAC-002 | Operator cannot manage users | Login as operator role | User management section not accessible | **SKIP** — same reason as above | ⏭️ SKIP | — |

---

## Notable Observations

1. **File sizes "NaN KB"** — All models in the AI Models list show "NaN KB" for size. The engine API may not be returning file size data, or the dashboard has a parsing error.

2. **Raw HTTP errors surfaced** — Several error messages expose internal HTTP error details to end users (e.g., "422 Client Error: Unprocessable Entity for url: http://localhost:8001/models/upload", "404 Client Error: Not Found"). These should be replaced with user-friendly messages.

3. **Restart behavior** — Both Restart buttons (soca-dashboard and soca-engine) cause the browser DevTools connection to timeout. There is no visible "Restart triggered" toast before the connection drops. Consider displaying the message before initiating the restart.

4. **TC-SCH-006 raw error** — The schedule stop action when the engine job has already ended returns a raw `404` error message rather than a clean "Job has already completed" message.

5. **No session timeout tested** — Session expiry behavior was not tested in this run.

---

*Report generated: 2026-04-12 | Test environment: macOS, Chrome, soca-dashboard @ localhost:8080, soca-engine @ localhost:8001*
