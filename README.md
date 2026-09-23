# gherkin-context-diagram-assignment
# Assignment Submission: Patient Reschedule Feature

## 1. Acceptance Criteria (Gherkin Syntax)

```gherkin
Feature: Patient Appointment Management

  Scenario: Patient reschedules an appointment within 24 hours of window
    Given a patient has a scheduled appointment for "2026-10-15T10:00:00Z"
    When the patient requests a reschedule to "2026-10-16T14:00:00Z" less than 24 hours before the original time
    Then the system should apply a late-change flag
    And emit an "AppointmentRescheduled" event to the Notification Service
    And display a confirmation message with updated details to the patient
C4Context
    title System Context Diagram - Reschedule Appointment Flow

    Person(patient, "Patient", "Schedules, cancels, or reschedules appointments via UI/Mobile App.")

    System_Boundary(core_app, "Healthcare Platform") {
        System(app_ui, "Patient Web/Mobile App", "Provides user interface for viewing and managing appointments.")
        System(apt_service, "Appointment Service", "Handles appointment scheduling logic, late-change flag rules, and database updates.")
        System(notif_service, "Notification Service", "Listens for events and sends SMS/Email/Push updates.")
    }

    Rel(patient, app_ui, "Submits reschedule request")
    Rel(app_ui, apt_service, "POST /appointments/{id}/reschedule")
    Rel(apt_service, apt_service, "Evaluates time delta (< 24 hrs) & applies late-change flag")
    Rel(apt_service, notif_service, "Emits 'AppointmentRescheduled' Event", "Event Bus / Message Queue")
    Rel(notif_service, patient, "Sends email/SMS confirmation")
    Rel(apt_service, app_ui, "Returns confirmation details")
    Rel(app_ui, patient, "Displays updated appointment UI")

{
  "eventId": "evt_9876543210",
  "eventType": "AppointmentRescheduled",
  "timestamp": "2026-10-15T01:30:00Z",
  "data": {
    "patientId": "pat_10293",
    "appointmentId": "apt_88492",
    "previousTime": "2026-10-15T10:00:00Z",
    "newTime": "2026-10-16T14:00:00Z",
    "isLateChange": true,
    "rescheduledAt": "2026-10-15T01:30:00Z"
  }
}

