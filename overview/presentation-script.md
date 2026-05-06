# SOCA Platform — Presentation Script
**For AI-Generated Presentation (Gamma / Similar Tool)**

> **Instructions for GenAI:** Use this script as the exact content baseline.  
> Tone: professional, confident, enterprise-grade. Not casual.  
> Color palette suggestion: deep navy (`#0F172A`), electric blue (`#3B82F6`), white, subtle green accent (`#22C55E`).  
> Include diagrams, icons, and data visualizations where noted. Avoid stock photography of people.  
> Total target slides: ~22–26 slides.

---

## SECTION 1 — INTRODUCTION & ECOSYSTEM

---

### SLIDE 1 — Cover Slide

**Title:** SOCA
**Subtitle:** Security Operations & Camera Analytics
**Tagline:** *Turn Your Existing CCTV Into an Intelligent AI Guard*

**Visual:** Full-bleed dark background. CCTV camera silhouette with AI detection bounding boxes overlaid in blue. SOCA logo centered.

**Speaker note:**
Welcome. Today we are presenting SOCA — Security Operations and Camera Analytics. SOCA is an AI-powered video analytics platform that transforms the CCTV infrastructure you already own into an intelligent, real-time monitoring system. We will walk you through the ecosystem, the architecture, the capabilities, and where this technology is going.

---

### SLIDE 2 — The Problem

**Title:** The Gap in Traditional CCTV

**Three columns layout:**

| Column 1 | Column 2 | Column 3 |
|---|---|---|
| 👁 **Human Fatigue** | ⏱ **Reactive, Not Proactive** | 📂 **No Actionable Data** |
| Security operators cannot watch 20+ camera feeds continuously for 12-hour shifts without missing events | Incidents are discovered after they happen — no real-time alerting based on what is actually happening in the frame | Hours of footage exist but produce no statistical insights on patterns, compliance, or risk |

**Speaker note:**
The challenge with traditional CCTV is simple: cameras see everything, but humans cannot process it all. Security teams are overloaded, events get missed, and terabytes of footage go unanalyzed. The camera is there — the intelligence is not. SOCA closes that gap.

---

### SLIDE 3 — What is SOCA?

**Title:** SOCA — The AI Layer on Top of Your Cameras

**Visual:** Split layout — left: traditional CCTV camera → right: camera + SOCA logo + detection overlays + alert notification on phone.

**Key points (3 bullets):**
- **Real-time AI detection** — YOLO deep learning models analyze live video streams at the edge, frame by frame
- **Configurable intelligence** — define zones, rules, and thresholds for your specific environment — no coding required
- **Centralized visibility** — manage every camera across every site from a single dashboard, with unified reporting and alerting

**Speaker note:**
SOCA sits as a software layer between your existing CCTV cameras and your security team. It continuously watches the video feed, applies AI detection, and only surfaces events that matter — based on rules you define. The result: your cameras become proactive, always-on intelligent sensors.

---

### SLIDE 4 — The SOCA Ecosystem

**Title:** Five Services, One Intelligent Platform

**Visual:** Ecosystem diagram — two zones:

```
┌──────────────────────────────────┐    ┌──────────────────────────────────┐
│          EDGE DEVICE             │    │         CLOUD / SERVER           │
│                                  │    │                                  │
│  ┌─────────────┐  ┌───────────┐  │    │  ┌────────────┐  ┌───────────┐  │
│  │  soca-      │  │  soca-    │  │───►│  │  soca-     │  │  soca-    │  │
│  │  dashboard  │  │  engine   │  │    │  │  service   │  │  control  │  │
│  │  (Config UI)│  │  (AI Core)│  │    │  │  (Ingest)  │  │  (Fleet)  │  │
│  └─────────────┘  └───────────┘  │    │  └────────────┘  └───────────┘  │
└──────────────────────────────────┘    └──────────────────────────────────┘
                                                              │
                                              ┌───────────────┴──────────┐
                                              │     soca-entitlement     │
                                              │     (License Server)     │
                                              └──────────────────────────┘
```

**Five service cards (icon + name + one-line description):**
1. 🧠 **soca-engine** — YOLO AI inference engine; processes RTSP video streams in real-time
2. ⚙️ **soca-dashboard** — Per-site web UI; manage cameras, schedules, detection rules, and settings
3. 📡 **soca-service** — Alert consumer microservice; bridges edge data to the central server
4. 🖥 **soca-control** — Fleet management dashboard; unified view of all sites, reports, and analytics
5. 🔑 **soca-entitlement** — License server; controls access tier and feature limits per deployment

**Speaker note:**
SOCA is a microservices ecosystem. On each edge device, two services run: soca-dashboard handles configuration and the soca-engine runs the actual AI inference. On the server side, soca-control is your command center — a single dashboard for your entire camera fleet. soca-service is the data pipeline that bridges the two. And soca-entitlement manages licensing, ensuring deployments operate within their authorized capacity.

---

### SLIDE 5 — Version & Licensing Model

**Title:** Built for Enterprise Scale — Flexible Licensing

**Two columns:**

**Left — Version:**
- Current platform version: **v1.2**
- Production-ready, deployed on edge hardware
- Django + FastAPI microservices stack
- YOLO11 / YOLOv8 AI models

**Right — License Tiers:**

| Tier | Cameras | AI Models | Support |
|---|---|---|---|
| **Free** | 1 | 1 | Community |
| **Starter** | Up to 5 | Up to 2 | Email |
| **Professional** | Up to 20 | Up to 8 | Priority |
| **Enterprise** | Unlimited | Unlimited | Dedicated SLA |

**Speaker note:**
SOCA version 1.2 is in active production. Licensing is handled through the soca-entitlement service — a UUID-based key is issued to each edge device and activates its camera and AI model limits. Free tier runs with one camera and one model, allowing customers to evaluate the platform before committing.

---

## SECTION 2 — ARCHITECTURE

---

### SLIDE 6 — Architecture Philosophy

**Title:** Edge-First. Cloud-Optional.

**Three principles (large icon + heading + 2-line description):**

🏗 **Edge Computing**
AI inference happens on the device where the cameras are. No video stream leaves the premises. Only alerts and snapshots are transmitted.

🌐 **Centralized Management**
All edges — regardless of location — report to a single soca-control dashboard. One login. Complete visibility.

🔄 **Transport Flexibility**
Choose Redis Stream for on-premises LAN deployments, or Google Pub/Sub for cloud and multi-region setups. Both work seamlessly.

**Speaker note:**
SOCA is designed edge-first by philosophy. Processing happens where the cameras are, which means low latency, high privacy, and resilience — the system keeps working even if the internet goes down. Cloud connectivity is optional, not mandatory.

---

### SLIDE 7 — The Data Flow

**Title:** From Camera Frame to Actionable Alert

**Visual:** Horizontal flow diagram with 6 numbered steps:

```
[Camera] ──1──► [MediaMTX Relay] ──2──► [soca-engine YOLO]
                                               │
                                        3. Rule fires
                                               │
                              ┌────────────────┴────────────────┐
                              ▼                                  ▼
                     4. Snapshot saved                  5. Alert published
                     (local / GCS)                (Redis Stream / Pub/Sub)
                                                         │
                                                  6. soca-control
                                                  stores + displays
```

**Steps list:**
1. **RTSP Capture** — Camera streams to MediaMTX relay; soca-engine reads at `rtsp://<edge-ip>:8554/<camera>`
2. **YOLO Inference** — Frame analyzed: object detection + ROI filtering + tracking
3. **Rule Evaluation** — Detection matched against configured rules (zone, threshold, schedule)
4. **Snapshot** — Incident frame saved locally and synced to Google Cloud Storage (10-second async)
5. **Alert Published** — Event pushed to Redis stream or Google Pub/Sub
6. **soca-control** — Alert stored in database; appears in dashboard, reports, and live feed

**Speaker note:**
This is the heartbeat of SOCA. A camera frame enters, YOLO processes it in milliseconds, the rule engine decides if it's noteworthy, and an alert — with a snapshot — is published. From event to visible alert in soca-control takes under 2 seconds on standard hardware.

---

### SLIDE 8 — Edge Device Deep Dive

**Title:** What Lives on the Edge Device

**Visual:** Edge device illustration (small PC / industrial computer) with service labels.

**Two-column layout:**

**soca-dashboard (Config Layer)**
- Web UI accessible from local network
- Manage cameras (RTSP URLs, credentials)
- Configure detection schedules and zones
- Sync settings to soca-engine via `.env`
- License activation and management
- Role-based access: Admin / Operator / Viewer

**soca-engine (AI Layer)**
- YOLO11 / YOLOv8 inference (`.pt` or `.onnx`)
- MediaMTX RTSP relay integration
- ROI: Polygon, Rectangle, Line Crossing
- Multi-rule evaluation per schedule
- GCS snapshot sync (background async)
- Job management API (start / stop / monitor)

**Speaker note:**
Each edge device is self-contained. soca-dashboard handles all configuration — you add cameras, draw detection zones, set rules, and it writes the configuration to soca-engine. The engine then runs continuously, processing video streams, making decisions, and publishing events — independently of the central server.

---

### SLIDE 9 — Transport & Storage Options

**Title:** Flexible by Design — Fits Your Infrastructure

**Two rows of option cards:**

**Alert Transport:**

| Option | Best For | How It Works |
|---|---|---|
| 🔴 **Redis Stream** | On-premises, LAN | Engine pushes to Redis → soca-service consumes → forwards to soca-control |
| ☁️ **Google Pub/Sub** | Cloud, multi-region | Engine publishes to GCP topic → soca-control subscribes directly |

**Snapshot Storage:**

| Option | Best For | How It Works |
|---|---|---|
| 💾 **Edge Local** | Air-gapped, LAN | Snapshots served via soca-dashboard proxy URL |
| ☁️ **Google Cloud Storage** | Cloud, multi-edge | Async background sync to `gs://bucket/edge-prefix/snapshots/` |

**Speaker note:**
We did not lock customers into a single infrastructure choice. If you are running fully on-premises, Redis is simple and fast. If you are cloud-native or geographically distributed, Pub/Sub scales without any infrastructure to manage. The same is true for snapshots — local or GCS, your choice.

---

### SLIDE 10 — Security Architecture

**Title:** Enterprise-Grade Security Model

**Visual:** Lock icon centered, four cards around it.

**Four security cards:**

🔑 **API Key Authentication**
Inter-service communication protected by bearer tokens. Ingest keys generated per deployment and stored securely.

🛡 **Role-Based Access Control**
Three roles — Admin, Operator, Viewer — with granular permissions across soca-dashboard and soca-control.

🔐 **License Binding**
Each license key is bound to an edge device by MAC address. Keys cannot be reused across machines.

📁 **Credential Isolation**
Camera RTSP credentials never leave the edge. Detection jobs use MediaMTX relay URLs, not raw camera addresses.

**Speaker note:**
Security is not an afterthought. Credentials are isolated at the edge, inter-service APIs are authenticated, and the licensing system adds a hardware binding layer so licenses cannot be shared across deployments.

---

## SECTION 3 — FEATURES & CAPABILITIES

---

### SLIDE 11 — AI Detection Modules

**Title:** Six Detection Capabilities, Out of the Box

**Visual:** 2×3 grid of feature cards with icon + title + one-line description.

| | | |
|---|---|---|
| 🚧 **Intrusion Detection** | 🦺 **PPE Compliance** | 🔍 **Object Detection** |
| Virtual zone-based alerts when unauthorized persons enter restricted areas | Automated detection of missing safety equipment: helmets, vests, goggles, masks | Detect, classify, and track any COCO object class in real time |
| 👥 **People Counting** | 🔴 **Crowd Detection** | 🚗 **License Plate Recognition** |
| Count IN/OUT crossings across a virtual line with directional filtering | Alert when crowd density in a zone exceeds a configurable threshold | OCR-based plate number capture with confidence scoring and expiry tracking |

**Speaker note:**
Six detection modules, all configurable without writing code. Each module maps to a distinct report category in soca-control, making it easy to segment analytics by use case. Customers typically start with one or two modules and expand as they see value.

---

### SLIDE 12 — Detection Intelligence

**Title:** Configurable Intelligence — Not Just Motion Detection

**Three capability sections:**

**Region of Interest (ROI)**
Draw exactly where detection applies:
- Polygon zones — complex irregular shapes
- Rectangle zones — simple boundary areas
- Crossing lines — directional people/vehicle counting

**Rule Engine**
- Multiple rules per detection schedule
- Each rule: define object classes, trigger condition, cooldown, cron schedule
- Actions per rule: save snapshot, publish alert, send Telegram notification, webhook

**Alert Categories & Routing**
- Every alert tagged with a category: `Intrusion`, `PPE`, `Detection`, `Counting`, `Crowd`, `LPR`
- Automatically routed to corresponding report pages in soca-control
- Filter, search, export — CSV and PDF

**Speaker note:**
What separates SOCA from basic motion detection is the rule engine. You define what matters in your environment — a person entering a polygon zone after hours, more than 3 people in a waiting area, a car without a valid plate approaching a gate — and SOCA monitors for exactly that, continuously, with no operator fatigue.

---

### SLIDE 13 — Analytics & Reporting

**Title:** From Raw Alerts to Business Intelligence

**Visual:** Dashboard screenshot mockup showing report cards and heatmap.

**Six report types (two rows of three):**

📌 **Intrusion Report** — Timeline, camera distribution, severity breakdown, peak hours

🦺 **PPE Violations** — Compliance rate by shift, class-level breakdown (helmet vs vest vs mask), trend over 30 days

🔍 **Object Detection** — Alert volume by class, camera hotspot matrix, hourly patterns

👥 **People Counting** — IN/OUT flow by time period, camera-level comparisons

🔴 **Crowd Detection** — Density events by location and time, peak crowd periods

🚗 **LPR Report** — Plate log with confidence scores, searchable by plate number

**All reports support:**
- Date range filtering
- Per-edge and per-camera filtering
- Export to CSV and PDF

**Speaker note:**
Every alert SOCA generates feeds into a structured reporting layer. Security teams get incident timelines. Safety managers get compliance trends. Operations leaders get traffic flow data. All from the same platform — no third-party BI tool required.

---

### SLIDE 14 — Live Monitoring & Real-Time View

**Title:** Operational Awareness, Right Now

**Visual:** Monitor page mockup with multiple MJPEG streams and bounding boxes.

**Three live capabilities:**

📹 **Live Stream Monitor**
Watch active inference jobs in real time — MJPEG stream with AI bounding boxes overlaid. Open individual streams in resizable popup windows.

📸 **Instant Snapshots**
Click any camera in the Assets page to grab a current frame. Confirm camera connectivity and framing before starting a detection job.

🌐 **HLS Stream Viewer**
MediaMTX provides a browser-accessible HLS video player for each camera at `http://edge-host:8888/<camera>` — no VPN or special client required.

**Speaker note:**
SOCA is not just a reporting tool — operators can see what is happening right now. The live monitor page shows active detection jobs with real-time bounding boxes. The HLS player makes it accessible from any browser on the local network without specialized software.

---

### SLIDE 15 — Fleet Management (soca-control)

**Title:** One Dashboard. Every Site. Complete Visibility.

**Visual:** soca-control dashboard mockup — sidebar with edges listed, main panel showing fleet health grid.

**Four fleet management pillars:**

🗺 **Multi-Edge Overview**
All registered edge devices shown on one screen. Online/offline status, last-seen timestamp, active job count.

💓 **Health Monitoring**
Per-edge system metrics: CPU, RAM, disk usage, active detection jobs, network latency. Identify overloaded or failing edges instantly.

🎯 **Unified Alert Inbox**
All detection events from all edges, searchable and filterable by date, camera, category, and edge. Complete incident history.

👤 **User Management**
Role-based access control — Admin, Operator, Viewer — configurable per user across the entire fleet.

**Speaker note:**
soca-control is the command center for your entire deployment. Whether you have 2 edge devices or 200, it is all in one place. Security teams see the incident feed. IT sees system health. Management sees reports. Each role gets exactly what it needs.

---

## SECTION 4 — REAL-WORLD USE CASES

---

### SLIDE 16 — Use Case Overview Map

**Title:** SOCA Across Industries

**Visual:** Industry hexagon grid or world map with industry icons placed on sectors.

**Eight industries:**

| Icon | Industry | Primary Module |
|---|---|---|
| 🏭 | **Manufacturing & Industrial** | PPE Compliance + Intrusion Detection |
| 🏗 | **Construction** | PPE Compliance + Zone Safety |
| 🏪 | **Retail & Hospitality** | People Counting + Crowd Detection |
| 🏢 | **Corporate & Office** | Intrusion Detection + Access Control |
| 🏙 | **Smart City & Public Safety** | Crowd Detection + LPR |
| 🚛 | **Logistics & Warehousing** | Loading Dock Security + LPR |
| 🏥 | **Healthcare** | Restricted Area Monitoring |
| 🎓 | **Education** | Campus Security + After-Hours |

**Speaker note:**
SOCA is purpose-built for any environment where cameras already exist but intelligence does not. The following slides show four specific real-world applications in depth.

---

### SLIDE 17 — Use Case 1: Industrial PPE Compliance

**Title:** Manufacturing — Zero Tolerance for PPE Violations

**Visual:** Construction/factory scene with bounding boxes on workers — red box on worker without helmet, green on compliant worker.

**Problem → Solution → Result layout:**

❌ **The Problem**
Workers on production floors and construction sites routinely remove helmets, vests, or goggles. Manual spot-checks are inconsistent and supervisors cannot be everywhere. A single violation can result in serious injury and regulatory fines.

✅ **How SOCA Solves It**
- PPE detection model runs on every camera in the work zone
- Alerts fire the moment a non-compliant worker is detected — with a snapshot
- 30-day compliance trend report shows which shifts, cameras, and time periods have the most violations
- Supervisors receive Telegram notifications in real time

📊 **Business Value**
- Consistent 24/7 compliance monitoring — not periodic spot checks
- Data-driven safety training: target the specific shift, camera, or time with highest violation rates
- Auditable compliance records for OSHA and local regulator inspections

**Speaker note:**
PPE compliance is one of SOCA's highest-impact use cases. The cost of a single lost-time injury far exceeds the cost of the entire platform deployment. SOCA makes compliance monitoring continuous, objective, and evidenced.

---

### SLIDE 18 — Use Case 2: Retail Traffic & Queue Intelligence

**Title:** Retail — From Guesswork to Data-Driven Operations

**Visual:** Overhead view of retail entrance with crossing line drawn across the doorway and counter visible.

**Problem → Solution → Result layout:**

❌ **The Problem**
Store managers schedule staff based on experience and intuition — not actual foot traffic data. Peak hours are understaffed; quiet periods are overstaffed. Queue buildup causes customer walkouts.

✅ **How SOCA Solves It**
- Crossing-line counting at all entrances measures exact IN/OUT counts by hour and day
- Crowd detection in checkout areas triggers alerts when queue length exceeds threshold
- Daily and weekly traffic reports enable precise staff scheduling decisions

📊 **Business Value**
- Labor cost optimization — schedule the right number of staff at the right times
- Measurable ROI on promotions — correlate traffic spikes with marketing events
- Customer experience improvement — proactive queue management before it becomes a problem

**Speaker note:**
Retail customers often discover that their highest-traffic days are not the days they assumed. SOCA gives you the actual number — not a guess — and the trend over time to staff accordingly.

---

### SLIDE 19 — Use Case 3: Smart City & Public Safety

**Title:** Smart City — Proactive Crowd & Vehicle Intelligence

**Visual:** Bird's-eye view of a public square or transportation hub with zone overlays and vehicle bounding boxes.

**Problem → Solution → Result layout:**

❌ **The Problem**
City operators react to crowding incidents after they escalate — no early warning. Vehicle access to restricted zones relies entirely on human guards. Parking enforcement is manual and inconsistent.

✅ **How SOCA Solves It**
- Crowd density detection in public squares and transit hubs triggers operator alerts before dangerous conditions develop
- LPR captures and logs every vehicle at entry points — unauthorized plates flagged immediately
- Heatmap analytics show which areas attract crowds at which times, informing urban planning decisions

📊 **Business Value**
- Prevention over reaction — intervene before a crowd becomes a safety hazard
- Automated vehicle access enforcement — scalable without increasing staffing
- Evidence-based infrastructure planning decisions

**Speaker note:**
Smart city applications require scale. SOCA's multi-edge architecture means you can deploy dozens of nodes across a city and manage them all from one soca-control instance — whether they connect via the city's LAN or Google Pub/Sub over the internet.

---

### SLIDE 20 — Use Case 4: Corporate Campus Security

**Title:** Corporate — After-Hours Security Without Overtime Costs

**Visual:** Corporate building exterior at night with intrusion detection zone highlighted in red.

**Problem → Solution → Result layout:**

❌ **The Problem**
After-hours security relies on on-site guards — expensive, prone to inattention, and unable to monitor all areas simultaneously. Incidents are discovered the next morning, not in real time.

✅ **How SOCA Solves It**
- Intrusion detection activates automatically after business hours via cron-based rule scheduling
- Any person detected in restricted zones triggers an instant alert with snapshot evidence
- Crossing-line rules monitor entry points — detect tailgating that badge systems miss
- Multi-camera correlation tracks movement through the facility

📊 **Business Value**
- Significant reduction in on-site guard hours required
- Response time measured in seconds, not minutes
- Complete photographic incident record for insurance and investigations

**Speaker note:**
The cron-based rule scheduling is important here — you configure SOCA once, and it automatically activates the after-hours detection rules at the right time. No human needs to toggle the system every day.

---

## SECTION 5 — FUTURE ROADMAP

---

### SLIDE 21 — Roadmap Overview

**Title:** What's Coming — The SOCA Roadmap

**Visual:** Three-column timeline: Near-Term | Mid-Term | Long-Term

**Near-Term (v1.x)**
- 📱 Mobile push notifications — iOS and Android alert delivery
- 📧 Email / SMS alerting — direct notification without third-party integration
- 🗺 GIS map view in soca-control — see all edges plotted on a live map
- 🤖 Pre-built industry models — construction, retail, healthcare-specific YOLO weights

**Mid-Term**
- 🚶 Behavioral analytics — loitering detection, fall detection, wrong-direction alerts
- 🔗 Access control integration — trigger door locks/unlocks on detection events
- 📊 Custom dashboard widgets — role-specific KPI widgets per user
- 🔊 Two-way audio — trigger PA announcements from detection events

**Long-Term Vision**
- 🧭 Multi-camera object tracking — follow an individual across camera views
- 🔍 Video search — natural language queries ("show all red shirts yesterday between 2–4pm")
- 🌐 Edge-to-edge coordination — automated tracking handoff between adjacent edges
- 🔮 Predictive analytics — ML forecasting of incident probability by location and time

**Speaker note:**
The roadmap is customer-driven. The near-term items address the most common feedback we receive: notifications without complex integrations, and industry-tuned models that work out of the box. The long-term vision moves SOCA from reactive analytics to predictive, coordinated intelligence.

---

### SLIDE 22 — Technology Trajectory

**Title:** AI at the Edge — A Platform That Evolves

**Visual:** Ascending capability curve with milestone markers.

**Three trajectory stages:**

**Today — v1.2: Detect & Alert**
- Real-time YOLO detection across 6 use cases
- Rule-based alerting with snapshot evidence
- Fleet management with unified reporting
- Edge-first, cloud-optional architecture

**Near Future — v2.x: Understand & Predict**
- Behavioral AI: intent recognition, anomaly detection
- Predictive risk scoring by location and time pattern
- Automated model retraining from flagged incidents
- Mobile-first operator experience

**Far Future — v3.x: Coordinate & Act**
- Multi-camera object identity tracking across entire sites
- Natural language video search and querying
- Autonomous response triggers (access control, audio, lighting)
- Edge-to-cloud federated learning — models improve from all deployments

**Speaker note:**
We are at version 1.2. The platform is production-ready and deployed. The roadmap takes SOCA from detection and alerting today, to understanding context and predicting risk in the near term, to fully coordinated autonomous response in the longer horizon. Each stage builds on real customer deployments and feedback.

---

### SLIDE 23 — Competitive Positioning

**Title:** Why SOCA — The Differentiators

**Visual:** Comparison table with SOCA column highlighted.

| Feature | SOCA | Traditional VMS | Cloud-Only AI |
|---|:---:|:---:|:---:|
| Edge AI Processing | ✅ | ❌ | ❌ |
| Works Offline | ✅ | ✅ | ❌ |
| Bandwidth Efficient | ✅ | ❌ | ❌ |
| Real-Time Alerts | ✅ | Limited | Latency |
| Custom ROI Zones | ✅ | Limited | ✅ |
| Multi-Site Fleet Management | ✅ | Expensive | ✅ |
| PPE + LPR + Crowd (same platform) | ✅ | ❌ | Separate add-ons |
| Open API (Redis / Pub/Sub) | ✅ | Proprietary | ✅ |
| Self-Hosted Option | ✅ | ✅ | ❌ |
| License Flexibility | ✅ | Per-seat, expensive | Subscription only |

**Speaker note:**
The key differentiators are edge processing and the breadth of detection in one platform. Cloud-only AI solutions require your video to leave the premises and are vulnerable to internet outages. Traditional VMS systems do not include AI detection — it is always an expensive add-on. SOCA is purpose-built for AI at the edge, with the fleet management layer built in from day one.

---

### SLIDE 24 — Deployment Model

**Title:** Up and Running in One Day

**Visual:** Four-step timeline with icons.

**Step 1 — Install**
Deploy soca-engine + soca-dashboard on an edge device (Linux bare-metal, Docker, or cloud VM). Start services with a single shell script.

**Step 2 — Connect**
Add camera RTSP URLs in soca-dashboard. Draw detection zones. Configure rules. No coding required.

**Step 3 — Activate**
Register the edge in soca-control. Activate license key via Settings → License. System is live.

**Step 4 — Operate**
Monitor live, respond to alerts, review reports. Add additional edges by repeating steps 1–3.

**Speaker note:**
Deployment is designed to be fast. A single-site pilot can be live in under four hours. Adding more edges does not require any changes to the central server — each edge is independent and self-registering.

---

### SLIDE 25 — Closing / Call to Action

**Title:** See It Working on Your Cameras

**Visual:** Clean dark slide with a single bold CTA and contact information.

**Three options:**

📋 **30-Day Pilot Program**
Deploy SOCA on one site. We measure baseline incident rates and compliance levels. At day 30, you receive a quantified improvement report.

🎯 **Live Demo**
Schedule a demonstration using your actual camera feeds or our reference environment.

📩 **Contact**
[contact information / email / website placeholder]

**Three proof points (bottom bar):**
- ⚡ Sub-2-second alert latency from detection to notification
- 🔋 Runs on existing CCTV infrastructure — no camera replacement
- 🛡 On-premises processing — your video never leaves your site

**Speaker note:**
The best way to evaluate SOCA is to run it on your own cameras, against your actual security or safety challenges. The 30-day pilot is zero-risk — you measure improvement with your own data. Let us show you what your cameras have been seeing all along.

---

## APPENDIX

---

### SLIDE 26 — Technical Specifications

**Title:** Platform Technical Reference

**Three-column specs table:**

**Infrastructure:**
- soca-engine: FastAPI / Python 3.11+
- soca-dashboard: Django 5.0+ / Python 3.11+
- soca-control: Django 5.0+ / Python 3.11+
- Database: SQLite (dev) / PostgreSQL (prod)
- RTSP relay: MediaMTX
- Message queue: Redis / Google Pub/Sub

**AI Models:**
- Framework: Ultralytics YOLO (v8 / v11)
- Formats: PyTorch `.pt` (with tracking), ONNX `.onnx`
- Device: CPU / CUDA GPU (configurable)
- Resolution: 640px default (configurable)
- FP16 half-precision: optional

**Ports (default):**
- soca-engine: 8001
- soca-dashboard: 8080
- soca-control: 8000
- soca-service: 8010
- MediaMTX RTSP: 8554
- MediaMTX HLS: 8888

---

### SLIDE 27 — Glossary

**Title:** Terminology Reference

| Term | Definition |
|---|---|
| **Edge Device** | The on-premises machine running soca-dashboard and soca-engine, co-located with CCTV cameras |
| **ROI** | Region of Interest — the polygon, rectangle, or line drawn in the camera frame where detection applies |
| **Schedule** | A configuration binding one camera to one AI model with a specific ROI and set of detection rules |
| **Job** | A running inference process in soca-engine — one job per active schedule |
| **Rule** | A detection trigger condition: object class, threshold, timing, and action (alert/snapshot/Telegram) |
| **soca-entitlement** | The license server that issues and validates UUID keys controlling camera and model limits |
| **Free Tier** | Default mode with no license — limited to 1 active camera and 1 concurrent AI model |
| **MediaMTX** | RTSP/HLS relay server on each edge device; reduces camera connection slots and hides credentials |

---

*Document Version: 1.0 — SOCA Platform v1.2*
*Prepared: May 2026*
*Classification: Internal — For GenAI Presentation Development*
