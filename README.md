# 🤝 Google Meet x Salesforce Integration

A real-world, scalable integration that connects **Salesforce Experience Cloud** with **Google Meet** using Python middleware on Google Cloud.  
This project allows seamless event registration, meeting management, and attendance syncing between Salesforce and Google Meet.

---

## ✨ What It Does

- Collects event registrations from users via Salesforce Experience Cloud.
- Uses Python middleware (Google Cloud Functions or Cloud Run) to create Google Meet meetings, manage registrants, and store meeting info in both Salesforce and a cloud database.
- Updates attendance in Salesforce automatically after meetings, using scheduled or event-driven syncs.
- Handles failures robustly with Google Pub/Sub dead-letter topics for manual review.

---

## 🏗️ Solution Architecture

<details>
<summary>Click to expand Mermaid Architecture Diagram</summary>

```mermaid
flowchart LR
    A[User Submits Registration Form (Salesforce Experience Cloud)] --> B[Google Cloud Middleware (Python)]
    B --> C[Google Meet API: Create Registrant/Meeting]
    C --> D[(Relational Database: Store Registrant/Meeting Info)]
    B --> E[Salesforce: Update Registrant/Meeting Records]
    D --> F[Scheduler: Trigger Attendance Sync (Cloud Scheduler or Pub/Sub)]
    F --> G[Google Meet API: Fetch Attendance]
    G --> H[(Relational Database: Store Attendance)]
    H --> I[Salesforce: Update Attendance on Meeting/Registrant Records]
    F -.-> J[Dead Letter Topic: Failed Syncs for Manual Review]
