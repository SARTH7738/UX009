# UX009
# UX Tester - Comprehensive Documentation

**Version:** 1.0.0
**Date:** 2025-12-28
**Author:** Sarth


---

## Table of Contents

1.  [System Overview](#1-system-overview)
2.  [System Architecture](#2-system-architecture)
3.  [User Guide](#3-user-guide)
    *   [Dashboard & Scanning](#dashboard--scanning)
    *   [Analytics](#analytics)
    *   [Bulk Scanning](#bulk-scanning)
    *   [Real-Time Monitoring](#real-time-monitoring)
4.  [Developer Guide](#4-developer-guide)
    *   [Folder Structure](#folder-structure)
    *   [Dependencies](#dependencies)
    *   [Extending the Application](#extending-the-application)
5.  [API Reference](#5-api-reference)

---

## 1. System Overview

**UX Tester** is an enterprise-grade automated auditing platform. It leverages Google's Gemini AI to simulate expert user behavior, analyzing websites for cognitive friction, accessibility barriers, and performance bottlenecks.

Unlike standard linters (like Lighthouse), UX Tester provides **qualitative insights** and **generative code fixes**.

---

## 2. System Architecture

The application follows a monolithic MVC-style architecture using Flask, with lightweight client-side routing for a snappy SPA (Single Page Application) feel.

```mermaid
graph TD
    User[User / Browser] -->|HTTP/HTTPS| Flask[Flask Server (app.py)]
    Flask -->|Gemini API| AI[Google Gemini Pro]
    Flask -->|APScheduler| Cron[Background Monitor]
    Cron -->|Poll| Sites[External Websites]
    Flask -->|Jinja2| Templates[HTML Templates]
    User -->|Local Storage| History[Browser DB]
    
    subgraph "Backend Logic"
        Flask
        Cron
    end
    
    subgraph "External Services"
        AI
        Sites
    end
```

### Core Components
*   **Flask Server**: Handles routing, API requests, and serves static assets.
*   **Generative Engine**: `google.generativeai` module communicates with Gemini Pro to audit HTML/DOM structures.
*   **Scheduler**: `APScheduler` runs background threads to monitor URLs every 30 seconds.
*   **Frontend**: pure HTML/JS with Tailwind CSS. State is managed via vanilla JS and persisted in `localStorage`.

---

## 3. User Guide

### Dashboard & Scanning
The **Dashboard** is the entry point.
1.  Enter a URL (e.g., `https://example.com`) in the central input.
2.  Click **"Run UX Audit"**.
3.  Wait for the AI analysis (approx. 5-10 seconds).
4.  **Results**:
    *   **Score**: 0-100 rating.
    *   **Radar Chart**: Visual breakdown of Performance, Accessibility, Best Practices, and SEO.
    *   **Fixes**: Click "Auto-Fix" to see generated CSS code.

### Analytics
Navigate to the **Analytics** tab to track progress.
*   **Trend Chart**: Shows the "Overall Score" history for every scan performed.
*   **Category Radar**: Aggregates the average strengths and weaknesses of all your scans.
*   **KPI Cards**: Quick stats on Total Scans and Average Score.

### Bulk Scanning
Designed for QAs and Agencies.
1.  Navigate to **Bulk Scan**.
2.  Paste a list of URLs (one per line).
3.  Click **Start Bulk Scan**.
4.  Watch the progress bar as the system audits each site sequentially.
5.  **Export**: Download a **CSV** summary or a detailed **PDF** report.

### Real-Time Monitoring
The "Watchdog" feature.
1.  Navigate to **Monitor**.
2.  Add a generic URL.
3.  The system will automatically ping this URL every 30 seconds.
4.  **Status Indicators**:
    *    **Healthy**: Score > 70
    *    **Warning**: Score 50-70
    *    **Critical**: Score < 50

---

## 4. Developer Guide

### Folder Structure
 
### Dependencies
All dependencies are frozen in `requirements.txt`.
*   `flask`: Web Framework.
*   `google-generativeai`: AI Integration.
*   `APScheduler`: Background task management.
*   `python-dotenv`: Environment variable management.

### Extending the Application
*   **Adding a new View**:
    1.  Add a new `<div id="new-view" class="view-section hidden">` in `index.html`.
    2.  Add a corresponding link in the Sidebar.
    3.  Update the `switchView()` function in `index.html` to handle the new ID.
*   **Adding a Background Task**:
    1.  Define a python function in `app.py`.
    2.  Register it with `scheduler.add_job()`.

---

## 5. API Reference

### Authentication
Include the `x-api-key` header in all requests.
*   *For Testing*: Generate a key at `/api-docs`.

### specific Endpoints

#### `POST /api/v1/scan`
Initiates a single URL audit.

**Request Body:**
```json
{
  "url": "https://example.com"
}
```

**Response:**
```json
{
  "score": 85,
  "categories": {
    "performance": 90,
    "accessibility": 80,
    "best_practices": 85,
    "seo": 88
  },
  "strengths": ["Fast LCP", "Good Contrast"],
  "weaknesses": ["Missing Alt tags"]
}
```

#### `GET /api/monitor`
Returns the list of currently monitored websites and their status.

**Response:**
```json
[
  {
    "url": "https://example.com",
    "status": "Healthy",
    "score": 92,
    "last_check": "2025-12-28 15:30:00"
  }
]
```


