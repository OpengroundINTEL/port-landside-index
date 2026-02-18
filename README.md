# port-landside-index
Multi-source public traffic data pipeline for port landside congestion indexing


Absolutely — here’s a **fully written English README plan** for your project, designed to be realistic and technically clear for building the **Port Landside Congestion Index** with Python and *public data sources* like 511 APIs and heuristics — no Kafka/Flink complexity required right now.

---

```markdown
# 📦 landside-congestion-index

**Port Landside Congestion Index (PLCI)**  
A Python-based multi-source data pipeline and API to compute and expose *real-world congestion indices* for truck traffic around major U.S. ports.

---

## 🚀 Project Summary

This project builds a **Landside Congestion Index** for ports using publicly available traffic data (such as 511 traffic event APIs), probe-based slowdown signals (Waze/other sources), and optional crowdsourced truck reports.

The goal is to provide:

- A normalized congestion score per port
- Historical trend data
- Confidence indicators
- REST API for clients (apps, dashboards, research)

This index is useful for logistics planning, NIW research, and real-time freight routing.

---

## 📌 Why This Matters

Existing congestion indices tend to focus on:

✔ Port vessel queues  
✔ Terminal throughput  
✔ Roadway traffic averages

BUT — there is **no unified, truck-oriented landside congestion metric** used in logistics. This project fills that gap using available open data sources and scalable Python tooling.

---

## 📊 Architecture Overview

```

Traffic Data Sources
↓
Adapters (Python)
↓
Normalization & Baseline Model
↓
Congestion Index Engine
↓
Storage (Timeseries DB)
↓
REST API (FastAPI)

````

---

## 🧠 Data Sources

### 🚦 511 Open Traffic APIs

Many U.S. states publish 511 data with traffic events, closures, and sometimes speeds → e.g.:

- 511 SF Bay Area traffic events & work zones :contentReference[oaicite:0]{index=0}  
- 511 NY with real-time traffic, cameras, truck parking, events :contentReference[oaicite:1]{index=1}  
- 511 WI with travel time and alerts :contentReference[oaicite:2]{index=2}  
- 511 AZ/LA/GA with cameras, message signs, events, alerts :contentReference[oaicite:3]{index=3}

Each API typically requires a developer key and returns JSON / structured data about:

- Roadwork  
- Incidents / closures  
- Alerts  
- Camera states  
- (Sometimes) travel times

These can be **normalized into a traffic events index** for ports.

---

### 🚘 Optional Probe-Based Real-Time Signals

Public/third-party signals like:

✔ Waze slowdown events (after approval)  
✔ INRIX / StreetLight (paid but consistent coverage) :contentReference[oaicite:4]{index=4}

These can be used as optional real-time congestion signals to strengthen accuracy.

---

### 🧑‍🤝‍🧑 Crowd Reports (Optional)

End users (truckers) can submit:

- Gate wait estimates  
- Approach delays  
- Terminal / gate identifiers

These help *correct and refine* index values and confidence levels.

---

## 📈 Congestion Index Concept

The index is a fused score from:

| Component            | Description |
|---------------------|-------------|
| Traffic Events      | Active incidents/closures affecting approach routes |
| Travel Deviation    | Current vs historical baseline deviation |
| Probe Slowdowns     | Optional real-time slowdown signals |
| Crowd Reports       | User-submitted delays |
| Confidence Metrics  | Based on sample size & data freshness |

All components are normalized and combined into a single port score:  
**0 (free flow) → 100 (severe congestion)**

---

## 🚧 Installation

```bash
git clone https://github.com/<org>/landside-congestion-index
cd landside-congestion-index

python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
````

---

## ⚙️ Configuration

Copy `.env.example` → `.env` and fill:

```
PORT_ENV=development
511_API_KEYS={ state: key, ... }
WAZE_API_KEY=your_key
DATABASE_URL=postgres://...
```

---

## 📦 Quick Start (Local)

Start DB + migrations:

```bash
alembic upgrade head
```

Run ingestion workers (cron every X minutes):

```bash
python ingestion/run.py
```

Run API server:

```bash
uvicorn app.main:app --reload
```

---

## 🔌 API Endpoints

```
GET /ports                # list supported ports
GET /ports/{id}/index     # current score + confidence
GET /ports/{id}/history   # historical time series
POST /reports             # add crowd report
```

**Example:**

```bash
curl https://yourdomain.com/ports/los-angeles/index
```

Response:

```json
{
  "index": 76,
  "confidence": "medium",
  "components": {
    "traffic": 0.42,
    "slowdown": 0.31,
    "crowd": 0.17,
    "baseline_deviation": 0.10
  }
}
```

---

## 🧪 Testing

Use pytest with fixtures:

```bash
pytest
```

511 API responses can be mocked offline to avoid rate limits.

---

## ☁️ Deployment Plan

* Docker + Kubernetes (optional)
* Cloud DB (TimescaleDB / Postgres)
* CI/CD with GitHub Actions

---

## 📚 Future Work

✔ Nationwide port adapter registry
✔ Optional premium data integrations (INRIX, StreetLight)
✔ Visualization dashboards
✔ Satellite / AIS hybrid enhancement


## 📌 Notes

* 511 data across states is not uniform but provides *useful event/alert signals* that can be normalized into congestion features. ([511.org][1])
* This project aims for realistic coverage using *available open APIs* and *optional probes*.

[1]: https://511.org/open-data?utm_source=chatgpt.com "511 Open Data"
