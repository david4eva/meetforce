# 📅 Salesforce + Google Meet Integration
> End-to-end Google Meet automation with Salesforce — built to showcase cloud integration, automation, and robust system design skills.

---

## 🚀 Overview

**Google Meet Integration for Salesforce** is a cloud-based project that connects Salesforce with Google Meet.  
It enables organizations to:

- Create & schedule Google Meet sessions directly from Salesforce (including Experience Cloud forms)
- Store meeting links, registrant details, and attendance within Salesforce custom objects
- Sync attendance and meeting data automatically (no manual intervention required)
- Provide reporting-ready data for admins, managers, and teams

*Goal:* Build a real-world, subscription-ready integration without costly middleware—leveraging Python, Google Cloud, and Salesforce.

---

## 💡 What Problem Does It Solve?

- **Manual work:** No more copy-pasting Google Meet links or tracking attendance by hand
- **Disconnected data:** Attendee, meeting, and event details all live in Salesforce
- **Expensive tools:** You don’t need costly third-party meeting or automation software

---

## ⚙️ Tech Stack

- **Salesforce Platform:** Apex, Flows, Experience Cloud, REST APIs, Custom Objects
- **Google Cloud Platform:** Cloud Run or App Engine (Python), Pub/Sub, Secret Manager
- **Python:** API integration logic (Google Meets, Salesforce)
- **Relational Database:** (e.g. PostgreSQL on Google Cloud SQL) — store attendee/meeting info
- **Scheduler:** Cron or Google Cloud Scheduler for auto-syncs
- **(Optional) AI/ML:** (Future roadmap) — AI-powered meeting summaries

---

## 🛠️ Key Features

- **Experience Cloud Registration Forms:** Collects Google Meet registrations using Salesforce Flows
- **Automated Meet Creation:** Creates Google Meet sessions and stores event/attendee info in Salesforce
- **Real-Time Attendance Sync:** Fetches attendance from Google Meet and updates Salesforce records
- **Relational Database Backup:** Stores all meeting/attendee data for audit, analytics, and troubleshooting
- **Dead Letter Handling:** Failed syncs are pushed to a “Dead Letter” Pub/Sub topic for manual review (robustness)
- **Secure Credential Storage:** Service account keys and secrets stored in Google Secret Manager

---

## 🏗️ Solution Architecture

```mermaid
graph TD;
    A[Salesforce Experience Cloud Form] -->|Submit Registration| B[Google Cloud Middleware (Python)]
    B -->|Create Meet + Register Attendees| C[Google Meet API]
    B -->|Store Data| D[Relational DB]
    C -->|Send Event Details| E[Salesforce Meeting/Attendee Objects]
    F[Google Cloud Scheduler] -->|Trigger Sync| G[Google Middleware]
    G -->|Get Attendance| C
    G -->|Update Attendance| E
    G -->|Failed Sync| H[Pub/Sub Dead Letter Topic]
