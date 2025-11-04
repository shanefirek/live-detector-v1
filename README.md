# Live Detector v1

A FastAPI-based enrichment microservice that identifies active SaaS vendor technology in real-time by analyzing HTML signatures. Powers Clay tables, n8n automations, and CRM signals for outbound segmentation.

## Overview

Live Detector v1 scrapes target domains and detects which service management platforms they're using through regex pattern matching. Currently supports detection of:

- **ServiceTitan** — Field service management software
- **HouseCall Pro** — Home service business management
- **Jobber** — Field service CRM and operations platform

## Features

- 🚀 Fast, lightweight FastAPI endpoint
- 🔍 Regex-based HTML signature detection
- 🎯 High-confidence scoring (95% when detected)
- 🧹 Smart ignore patterns to reduce false positives
- 📊 Returns structured JSON with match details
- ⚡ Deployed on Railway for instant scalability

## API Usage

### Endpoint

```
POST /classify
```

### Request Body

```json
{
  "domain": "example.com"
}
```

### Response

```json
{
  "domain": "example.com",
  "uses_servicetitan": true,
  "uses_housecallpro": false,
  "uses_jobber": false,
  "confidence": 0.95,
  "matches": {
    "servicetitan": "servicetitan.com"
  }
}
```

## Local Development

### Prerequisites

- Python 3.8+
- pip

### Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Run locally with hot reload
hypercorn src/main:app --reload
```

The service will be available at `http://localhost:8000`

### Test the Endpoint

```bash
curl -X POST http://localhost:8000/classify \
  -H "Content-Type: application/json" \
  -d '{"domain": "example.com"}'
```

## Deployment

This service is configured for one-click deployment on Railway.

1. Push to GitHub
2. Connect repo to Railway
3. Railway auto-detects FastAPI and deploys
4. Use the generated URL as your webhook endpoint in Clay or n8n

See `infra/railway.json` for deployment configuration.

## Project Structure

```
live-detector-v1/
├── src/
│   └── main.py              # FastAPI app with detection logic
├── examples/
│   └── sample_payload.json  # Example request payload
├── infra/
│   └── railway.json         # Railway deployment config
├── requirements.txt         # Python dependencies
├── .gitignore
└── README.md
```

## Use Cases

### Clay Enrichment Tables
Add a webhook enrichment column that posts domain data to this endpoint. Use the response to segment leads by their tech stack.

### n8n Workflow Automation
Integrate as a webhook node to enrich prospect data in real-time before sending to CRM or outreach tools.

### ICP Segmentation
Identify companies using specific software to build targeted outbound lists for GTM teams.

## How It Works

1. Receives domain via POST request
2. Fetches the HTML from the target domain
3. Strips out common false-positive patterns
4. Runs regex patterns for each vendor
5. Returns detection results with confidence score

## Detection Patterns

The service uses carefully crafted regex patterns to identify vendor-specific signatures in HTML:

- CDN references (`cdn.servicetitan.com`, `st-cdn.net`)
- API endpoint patterns (`api.getjobber.com`)
- Widget loaders (`stwidget-*`, `hcp.run`)
- App domains (`app.housecallpro.com`, `clienthub.app`)

## Error Handling

- **400**: Invalid or missing domain
- **502**: Failed to fetch domain (timeout, DNS error, etc.)

---

### Author

**Shane Firek** — GTM Engineer / Workflow Architect
- [LinkedIn](https://linkedin.com/in/shanefirek)
- [Twitter](https://twitter.com/shanefgtm)
- [Website](https://shanefirek.com)
