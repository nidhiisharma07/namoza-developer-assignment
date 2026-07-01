# Task 3 - Integration Design

## End-to-End Integration Architecture

When a patient submits the consultation form on the OrthoNow landing page, the form data is first validated on the client side and then sent securely to a backend API. The backend acts as the integration layer between the landing page and all third-party services.

### Integration Flow

1. The patient fills in the consultation form with:
   - Full Name
   - Phone Number
   - Preferred Clinic

2. The landing page sends a POST request to the backend API.

3. The backend checks HubSpot using the **Phone Number** to determine whether the patient already exists. HubSpot's default deduplication works on email, but since this landing page does not collect email addresses, checking the phone number manually prevents duplicate patient records.

4. If the phone number already exists:
   - Update the existing HubSpot contact.

   Otherwise:
   - Create a new contact.

   The contact will contain:

   - Name
   - Phone Number
   - Preferred Clinic
   - Source = "Google Ads - Consultation Landing Page"
   - Lead Status = "New Enquiry"

5. After the contact is successfully created or updated in HubSpot, the backend calls the Karix WhatsApp Business API to send a confirmation message to the patient.

6. Once the backend confirms successful processing, the frontend pushes a GTM event using:

```javascript
window.dataLayer.push({
  event: "consultation_form_submitted"
});
```

Google Tag Manager captures this event and forwards it to Google Analytics 4. The same event is imported into Google Ads as a conversion to optimize advertising campaigns.

---

# Architecture Diagram

```
Patient
    │
    ▼
Landing Page
    │
    ▼
Backend API
    │
    ├────────────► HubSpot CRM
    │                 │
    │                 └── Create / Update Contact
    │
    ├────────────► Karix WhatsApp API
    │                 │
    │                 └── Send Confirmation Message
    │
    └────────────► dataLayer.push()
                          │
                          ▼
                   Google Tag Manager
                          │
                          ▼
                 Google Analytics 4
                          │
                          ▼
              Google Ads Conversion Tracking
```

---

# Biggest Failure Point and Fallback Strategy

The biggest failure point is communication with external APIs such as HubSpot and Karix. Network failures, API downtime, server errors, or rate limits can prevent successful processing of patient enquiries.

To improve reliability, the backend should implement automatic retry logic with exponential backoff. Failed requests should be stored in a queue or temporary database instead of being discarded.

If HubSpot is unavailable, the patient enquiry should be saved locally and synchronized once the CRM becomes available.

If the Karix WhatsApp API fails, the backend should retry sending the confirmation message until it succeeds or reaches the retry limit.

All failures should be logged with detailed error information for debugging and monitoring.

---

# WhatsApp SLA Monitoring

The WhatsApp confirmation message must be delivered within two minutes after the consultation form is submitted.

Possible reasons for missing this SLA include:

- HubSpot API downtime
- Karix API downtime
- Network latency
- Backend server failures
- High traffic causing request queues

To monitor this SLA, every request should store:

- Form submission timestamp
- WhatsApp API response timestamp

These timestamps can be monitored using logging tools and dashboards such as Grafana, Datadog, or AWS CloudWatch.

If message delivery exceeds two minutes, an automatic alert should notify the engineering team so the issue can be investigated immediately.

---

# Technology Stack

- Frontend: HTML, CSS, JavaScript
- Backend: Node.js with Express
- CRM: HubSpot Contacts API
- Messaging: Karix WhatsApp Business API
- Analytics: Google Tag Manager (GTM)
- Reporting: Google Analytics 4 (GA4)
- Advertising: Google Ads Conversion Tracking

---

# Why This Architecture?

This architecture separates the frontend from external services, keeps API credentials secure on the backend, prevents duplicate patient records by checking phone numbers, supports reliable retries for failed API calls, and provides complete conversion tracking using Google Tag Manager, Google Analytics 4, and Google Ads.