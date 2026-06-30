# Task 1 – GTM Event Schema for OrthoNow

## Objective

The objective of this implementation is to establish a complete analytics tracking framework for the OrthoNow website. By implementing Google Tag Manager (GTM) and Google Analytics 4 (GA4) event tracking, the marketing team will be able to monitor user interactions, analyze appointment booking behaviour, measure campaign performance, identify conversion bottlenecks, and optimize digital marketing efforts.

---

# Tracking Architecture

```text
User Action
      │
      ▼
Frontend Website
      │
      ▼
window.dataLayer.push()
      │
      ▼
Google Tag Manager (GTM)
      │
      ▼
Google Analytics 4 (GA4)
      │
      ▼
Reports & Audiences
      │
      ▼
Google Ads Conversion
```

---

# 1. GTM Event Tracking Schema

| Event Name | Business Purpose | Trigger Type | Key Parameters (Minimum 3) | GA4 Report / Audience |
|------------|-----------------|--------------|----------------------------|----------------------|
| booking_step_complete | Track appointment booking progress | Custom Event (dataLayer) | step_number, clinic_location, specialty | Funnel Exploration |
| booking_form_submit | Measure completed appointment bookings | Form Submission | clinic_location, specialty, preferred_date | Conversions Report |
| booking_form_error | Identify validation issues | Form Error | error_type, step_number, field_name | Error Analysis |
| call_now_click | Measure call intent | Click Trigger (tel:) | page_location, clinic_location, button_position | Lead Attribution Report |
| whatsapp_click | Measure WhatsApp engagement | Click Trigger (wa.me) | page_location, clinic_location, device_type | Engagement Audience |
| patient_guide_form_submit | Track lead generation | Form Submission | patient_name, page_location, guide_name | Lead Generation Report |
| patient_guide_download | Track PDF downloads | Link Click | file_name, page_location, clinic_location | Downloads Report |
| clinic_page_view | Measure clinic popularity | Page View | clinic_name, city, page_url | Clinic Performance Dashboard |
| blog_scroll_25 | Track reader engagement | Scroll Depth | article_title, scroll_percent, category | Content Engagement |
| blog_scroll_50 | Track reader engagement | Scroll Depth | article_title, scroll_percent, category | Content Engagement |
| blog_scroll_75 | Track reader engagement | Scroll Depth | article_title, scroll_percent, category | Content Engagement |
| blog_scroll_90 | Measure highly engaged readers | Scroll Depth | article_title, scroll_percent, category | Engaged Readers Audience |
| consultation_landing_page_view | Measure campaign traffic | Page View | campaign_name, source, medium | Campaign Performance |
| consultation_form_start | Track form initiation | Form Interaction | page_location, device_type, campaign | Funnel Analysis |
| consultation_form_submit | Track successful lead generation | Custom Event | patient_name, campaign_name, source | Conversion Tracking |

---

# 2. Funnel Drop-off Tracking for the Booking Form

The appointment booking form contains three sequential steps:

1. Select Clinic Location and Specialty
2. Enter Patient Details
3. Confirm Booking

Google Tag Manager cannot automatically detect movement between these steps. Therefore, the frontend developer will implement custom `window.dataLayer.push()` events whenever a user successfully completes a step.

Google Tag Manager listens to these events and forwards them to Google Analytics 4, where Funnel Exploration reports can be created.

---

## Step 1 – Clinic Location & Specialty

### GTM Trigger

**Trigger Type:** Custom Event

**Event Name**

```
booking_step_complete
```

**Trigger Condition**

```
step_number = 1
```

### dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "Bengaluru - Indiranagar",
  "specialty": "Knee Replacement"
}
```

---

## Step 2 – Patient Details

### GTM Trigger

**Trigger Type:** Custom Event

**Event Name**

```
booking_step_complete
```

**Trigger Condition**

```
step_number = 2
```

### dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "patient_name": "Nidhi Sharma",
  "patient_phone": "captured",
  "preferred_date": "2026-07-15"
}
```

---

## Step 3 – Booking Confirmation

### GTM Trigger

**Trigger Type:** Custom Event

**Event Name**

```
booking_step_complete
```

**Trigger Condition**

```
step_number = 3
```

### dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 3,
  "step_name": "booking_confirmed",
  "clinic_location": "Bengaluru - Indiranagar",
  "specialty": "Knee Replacement",
  "booking_status": "confirmed"
}
```

---

# 3. Funnel Exploration in GA4

The booking funnel will be configured using GA4 Funnel Exploration.

| Funnel Step | Event Condition |
|--------------|----------------|
| Step 1 | booking_step_complete AND step_number = 1 |
| Step 2 | booking_step_complete AND step_number = 2 |
| Step 3 | booking_step_complete AND step_number = 3 |

### Funnel Visualization

```text
Booking Started
      │
      ▼
Step 1
100 Users
      │
      ▼
Step 2
70 Users
      │
      ▼
Step 3
45 Users
```

This report helps identify exactly where users abandon the booking process.

Example:

- Step 1 → 100 Users
- Step 2 → 70 Users
- Step 3 → 45 Users

Insights:

- 30% drop-off between Step 1 and Step 2
- 35.7% drop-off between Step 2 and Step 3

These insights help improve the booking experience and increase appointment conversion rates.

---

# 4. Google Ads Conversion to Import

### Selected Conversion

```
booking_form_submit
```

### Justification

The `booking_form_submit` event represents a completed appointment enquiry and is the highest-intent conversion event.

Unlike `call_now_click` or `whatsapp_click`, it confirms that the user has successfully submitted their appointment request. Importing this event into Google Ads provides the strongest optimization signal for Smart Bidding and enables accurate campaign ROI measurement.

---

# 5. Implementation Notes

- Multi-step form tracking requires collaboration between the frontend development and analytics teams.
- The frontend developer is responsible for implementing the `window.dataLayer.push()` events.
- Google Tag Manager listens for these custom events and forwards them to GA4.
- Important conversion events should be marked as Key Events (Conversions) in GA4.
- Event names and parameters should follow GA4 naming conventions to maintain consistency across reporting.

---

# 6. Benefits of this Tracking Setup

- Complete visibility into the patient journey.
- Accurate booking funnel analysis.
- Identification of user drop-off points.
- Better campaign performance measurement.
- High-quality remarketing audience creation.
- Improved Google Ads optimization.
- Data-driven conversion rate optimization.

---

# Conclusion

This GTM implementation provides complete visibility into the patient journey from the first website interaction through successful appointment booking.

By combining custom `window.dataLayer.push()` events, Google Tag Manager, and GA4 Funnel Exploration, OrthoNow can accurately monitor user behaviour, identify conversion bottlenecks, improve campaign performance, and optimize appointment bookings through data-driven decision making.