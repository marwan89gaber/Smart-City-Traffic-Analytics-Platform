# Business Problem & Project Objectives
# Smart City Traffic Analytics Platform

---

## 1. Executive Summary

Urban traffic congestion costs cities billions of dollars annually in lost productivity,
wasted fuel, and delayed emergency response. Today's traffic management systems are
almost entirely reactive — operators watch camera feeds and respond after congestion
has already formed. The Smart City Traffic Analytics Platform replaces this reactive
model with a real-time AI-driven platform that predicts congestion before it forms,
detects incidents automatically within 60 seconds, and delivers actionable intelligence
to traffic operations centers through a live dashboard.

---

## 2. Business Problem Statement

### 2.1 The Core Problem

Urban traffic management operates on a fundamental mismatch: the volume of road events
requiring attention grows continuously, but the human capacity to monitor them does not.

A city with 500 camera feeds cannot be effectively monitored by a team of operators.
Congestion cascades across intersections faster than manual intervention can halt it.
Accidents go undetected for 10 to 20 minutes in off-peak or low-visibility conditions.
And the mountains of historical traffic data collected by cities every day are almost
never used to build predictive models or optimize signal timing.

The result is that traffic management remains one of the least data-driven operations
in city government — despite having more raw data available than most domains.

### 2.2 Operational Challenges in Detail

**Challenge 1: Reactive-only monitoring**
Traffic operations centers see congestion only after it has formed and spread.
There is no system that ingests real-time signals and forecasts whether an
intersection will become congested 10 to 15 minutes from now.

**Challenge 2: Manual camera monitoring at scale**
Human operators cannot simultaneously monitor hundreds of feeds with consistent
attention. Incidents are frequently missed or reported late, especially at night
and on lower-priority roads.

**Challenge 3: Slow incident detection**
When a vehicle breaks down or an accident occurs, the average detection time via
manual monitoring is 10 to 20 minutes. Every minute of undetected blockage
extends downstream congestion across the network.

**Challenge 4: Disconnected data systems**
Vehicle counts, speed estimates, weather data, signal timing logs, and incident
reports exist in separate systems with no unified analytics layer. City planners
cannot easily answer basic questions like: "Which 10 intersections are most
chronically congested between 7am and 9am on weekdays?"

**Challenge 5: No scalable prediction infrastructure**
Even when cities want to build predictive models, they lack the streaming
infrastructure to feed real-time features into a model and serve predictions
at low latency across all intersections simultaneously.

### 2.3 Financial and Business Impact

The costs of unmanaged congestion are well documented:

- Urban congestion costs the United States approximately $87 billion per year
  in wasted fuel and lost productivity (INRIX Global Traffic Scorecard).
- The average driver in a major city loses 54 hours per year to traffic delays.
- Emergency response times increase by an average of 23% when responders
  navigate through congested corridors.
- Studies show that optimized signal timing alone can reduce intersection
  delay by 10 to 15 percent — but optimization requires real-time data.
- Poor infrastructure investment decisions, made without reliable traffic data,
  direct billions of dollars toward roads that are not the actual bottlenecks.

### 2.4 Stakeholders Affected

| Stakeholder | Pain Point | What They Need |
|---|---|---|
| Traffic Operations Center | Cannot monitor all feeds manually; slow incident response | Real-time alerts, live dashboard, automated anomaly flagging |
| Emergency Services (Police, Fire, EMS) | Congestion slows response routes | Live congestion map, fastest-route data |
| City Planning Department | Cannot identify chronic bottlenecks from raw data | Historical analytics, hotspot reports, trend visualizations |
| Municipal Government | Accountable for city efficiency and resident satisfaction | KPI dashboards, evidence for infrastructure investment |
| Commuters | Experience delays and unreliable journey times | (Indirect) Reduced delays from better traffic management |
| Road Maintenance Teams | Cannot prioritize without usage data | High-load intersection reports |

---

## 3. Project Objectives

### 3.1 Technical Objectives

**OBJ-T1: Real-Time Data Ingestion Pipeline**
Build a Kafka-based streaming pipeline that ingests simulated traffic camera events
from 12 intersections simultaneously, with each intersection publishing to a
dedicated Kafka topic at sub-second event frequency.

Success Criterion: Pipeline handles 10,000+ events per minute without consumer lag
exceeding 5 seconds under sustained load.

---

**OBJ-T2: Distributed Stream Processing**
Implement Apache Spark Structured Streaming to consume Kafka topics and compute
windowed aggregations (5-minute and 15-minute windows) for vehicle count, average
speed, lane occupancy rate, and congestion score per intersection.

Success Criterion: End-to-end latency from event generation to aggregated result
in PostgreSQL is under 10 seconds.

---

**OBJ-T3: Computer Vision — Vehicle Detection and Counting**
Integrate YOLOv8 to process simulated video frames and detect vehicles by class
(car, truck, bus, motorcycle). Implement frame-displacement speed estimation using
OpenCV to approximate vehicle speed without ground-truth calibration.

Success Criterion: Detection runs at minimum 15 FPS on CPU in batch mode;
speed estimation error within +/- 15 km/h of simulated ground truth.

---

**OBJ-T4: Congestion Prediction Model**
Engineer features from real-time aggregations and train a multi-class classifier
to predict congestion level (Free Flow / Moderate / Heavy / Gridlock) per
intersection per 5-minute window. Compare XGBoost, LightGBM, and Random Forest.
Select the best model based on weighted F1-score on a held-out test set.

Success Criterion: Weighted F1-score >= 0.88; inference latency < 20ms per prediction.

---

**OBJ-T5: Anomaly Detection — Incident Flagging**
Train an unsupervised Isolation Forest model on normal traffic patterns to flag
statistically abnormal readings (sudden vehicle count drop, near-zero speeds,
unusual density spikes) that signal accidents, road blockages, or sensor failures.

Success Criterion: Detects simulated incident events with precision >= 0.85 and
average detection lag under 60 seconds.

---

**OBJ-T6: Real-Time Alert System**
Build an alert engine that fires when congestion prediction crosses a configured
threshold or anomaly detection flags an incident. Alerts are routed to a Kafka
alerts topic, written to PostgreSQL, and surfaced via the API within 5 seconds
of the triggering event.

Success Criterion: Alert end-to-end latency from trigger to API availability < 5s.

---

**OBJ-T7: FastAPI Backend**
Develop a production-ready FastAPI application with documented REST endpoints for:
live traffic status, congestion predictions, alert feeds, and historical analytics.
Include Swagger documentation and Pydantic schema validation on all inputs.

Success Criterion: All endpoints respond in < 200ms at 50 concurrent requests
on standard hardware.

---

**OBJ-T8: Power BI Dashboard**
Build a Power BI dashboard connected to PostgreSQL displaying real-time KPIs,
a congestion heatmap, alert history, prediction accuracy trends, and peak-hour
traffic patterns. Dashboard must refresh on a 1-minute schedule.

Success Criterion: Dashboard renders all KPIs correctly from live PostgreSQL data
with no manual refresh required.

---

**OBJ-T9: Full Docker Compose Deployment**
Containerize all services (Zookeeper, Kafka, PostgreSQL, Spark, FastAPI) and
write a docker-compose.yml that starts the entire platform with a single command
on any machine with Docker installed.

Success Criterion: `docker-compose up` produces a fully running platform in
under 3 minutes on a machine with 8GB RAM.

---

### 3.2 Business Objectives

**OBJ-B1: Reduce incident detection time**
Target: Automated detection within 60 seconds vs. 10–20 minutes manual baseline.

**OBJ-B2: Enable proactive congestion management**
Target: Predict congestion 10–15 minutes before formation using windowed features.

**OBJ-B3: Automate multi-intersection monitoring**
Target: Monitor 12 simulated intersections simultaneously without human intervention,
establishing an architecture that scales to 500+ with Kafka partition expansion.

**OBJ-B4: Provide historical traffic intelligence**
Target: Support city planner queries on peak hours, chronic hotspots, and seasonal
patterns through the analytics API and Power BI dashboard.

**OBJ-B5: Demonstrate production-deployable system**
Target: Deliver a fully containerized, documented platform that could be extended
to a real city deployment with cloud infrastructure (AWS MSK, EMR, RDS).

---

## 4. Scope and Constraints

### In Scope
- Simulated traffic data (no real camera hardware required)
- 12 virtual intersections
- Congestion prediction and anomaly detection
- FastAPI backend and Power BI dashboard
- Full Docker Compose deployment

### Out of Scope (documented as Future Work)
- Real camera hardware integration
- Kubernetes scaling (documented as next step)
- Live weather API integration (feature placeholder included)
- Route optimization engine (architecture placeholder included)
- Cloud deployment (documented as next step with AWS architecture)

### Technical Constraints
- Platform must run on a single machine with 8GB RAM for demo purposes
- All data is simulated — no real PII or sensitive municipal data
- YOLOv8 runs on CPU (GPU acceleration documented as an improvement)

---

## 5. Success Metrics Summary

| Objective | Metric | Target |
|---|---|---|
| Streaming throughput | Events/minute without consumer lag | 10,000+ |
| End-to-end latency | Event to DB write | < 10 seconds |
| Congestion model accuracy | Weighted F1-score | >= 0.88 |
| Incident detection speed | Time from event to alert | < 60 seconds |
| API response time | P95 at 50 concurrent users | < 200ms |
| Deployment | Full stack startup time | < 3 minutes |