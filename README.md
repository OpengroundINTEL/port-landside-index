# port-landside-index



Port Landside Congestion Index (PLCI)

A lightweight Python service that computes a congestion score for major U.S. ports using public traffic data and optional crowd reports.

---

## Overview

This project calculates a landside congestion index (0–100) for port approach corridors using:

- 511 state traffic APIs (incidents, closures, alerts)
- Optional slowdown/probe signals
- Optional crowd-reported wait times
- Historical baseline deviation

The goal is a simple, maintainable, minutes-level index — not millisecond streaming.

---

## Scope (MVP)

- Support 5–10 major U.S. ports
- Poll traffic APIs every 5 minutes
- Normalize events into congestion signals
- Compute index per port
- Expose REST API

No Kafka. No Flink. No distributed streaming.

---

## Tech Stack

- Python 3.10+
- FastAPI
- SQLAlchemy
- PostgreSQL (or TimescaleDB)
- Requests / HTTPX
- Pandas (for normalization logic)

---

## Architecture

```
Traffic APIs → Adapter Layer → Normalization → Index Engine → Database → REST API
```

---

## Directory Structure

```
app/
├── adapters/
├── ingestion/
├── core/
├── db/
├── api/
└── main.py
```

---

## Index Model

Index = weighted sum of:

- Active traffic events near port
- Road severity score
- Deviation from historical baseline
- Optional crowd correction

Output: 0–100 score + confidence level

---

## API

```
GET  /ports
GET  /ports/{id}/index
GET  /ports/{id}/history
POST /reports
```

---

## Run Locally

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

---

## Documentation

| Document | Description |
|---|---|
| [METHODOLOGY.md](./METHODOLOGY.md) | Index definition, baseline model, confidence scoring, limitations |
| [VALIDATION.md](./VALIDATION.md) | Backtesting against historical congestion events |
| [DATA_SOURCES.md](./DATA_SOURCES.md) | 511 APIs by state, coverage table, fallback sources |

---

## Roadmap

- Add more ports
- Improve baseline model
- Add confidence scoring
- Optional probe integration

---

## License

MIT
