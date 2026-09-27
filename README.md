# AI Lead-to-Appointment Voice Automation

An event-driven automation that receives new leads, validates and stores their information, triggers an AI voice call, processes the call result, tracks appointments, and records analytics.

Built with **n8n, Supabase, REST APIs, and Retell AI**.

## Architecture

```text
                    LEAD INTAKE
                         │
                         ▼
                  Webhook Form
                         │
                         ▼
              Validate & Clean Data
                         │
                         ▼
                  Supabase Leads
                         │
                         ▼
                  Retell AI Call
                         │
                         ▼
                Update Lead Status
                         │
                         ▼
                   Call Logging
                         │
                         ▼
                  Analytics Event


                  RETELL CALLBACK
                         │
                         ▼
                    Call Ended?
                         │
                         ▼
                  Update Call Log
                         │
                         ▼
               Appointment Booked?
                    /          \
                  YES           NO
                   │             │
                   ▼             ▼
          Save Appointment    No Answer
                   │             │
                   ▼             ▼
            Status: Booked    Analytics
                   │
                   ▼
                Analytics
```

## What It Does

### 1. Lead Intake

A website form sends a POST request to an n8n webhook.

The workflow:

* Receives the lead
* Immediately returns a success response
* Validates required fields
* Cleans the phone number
* Normalizes lead data
* Assigns a default lead status

### 2. Lead Storage

The cleaned lead is inserted into Supabase.

Stored information includes:

* Name
* Phone number
* Email
* Lead source
* Client ID
* Lead status

### 3. AI Voice Call

After the lead is stored, the workflow triggers a Retell AI outbound call through its REST API.

The lead ID, name, and client information are passed as metadata so the callback can be associated with the correct lead.

### 4. Call Tracking

The workflow records the call initiation in Supabase and creates an analytics event.

This creates a traceable relationship between:

```text
Lead → Call → Analytics
```

### 5. Retell Callback Processing

A second webhook receives call completion events from Retell AI.

When a call ends, the workflow:

* Updates the call record
* Stores call status
* Calculates call duration
* Stores the transcript
* Stores the recording URL
* Records the call completion time

### 6. Appointment Detection

The callback metadata is checked to determine whether an appointment was booked.

If an appointment was booked:

```text
Save Appointment
      ↓
Update Lead → Booked
      ↓
Log Analytics Event
```

If no appointment was booked:

```text
Update Lead → No Answer
      ↓
Log Analytics Event
```

## Technologies

* n8n
* REST APIs
* Webhooks
* Retell AI
* Supabase
* JavaScript
* JSON
* Event-driven workflows

## Key Engineering Concepts

This project demonstrates practical experience with:

* Webhook-based automation
* REST API integration
* HTTP authentication
* JSON request/response handling
* Data validation and normalization
* Conditional workflow branching
* Callback/webhook processing
* Cross-workflow data references
* Database CRUD operations through REST APIs
* Event tracking
* Status management
* Dynamic data mapping
* Error validation
* AI voice API integration

## Workflow Preview

![Lead Intake](screenshots/01-lead-intake.png)

![Call Trigger](screenshots/02-call-trigger.png)

![Retell Callback](screenshots/03-retell-callback.png)

![Appointment Routing](screenshots/04-appointment-routing.png)

## Example Lead Input

```json
{
  "full_name": "John Smith",
  "phone_number": "+15551234567",
  "email": "john@example.com",
  "source": "website_form",
  "client_id": "demo-client-001"
}
```

## Security

The portfolio version contains placeholders instead of production credentials.

Before running the workflow, configure your own:

* Supabase credentials
* Retell AI API key
* Retell phone number
* Retell agent ID

No production customer information or credentials are included.

## Running the Workflow

1. Import `workflow/lead-to-appointment.json` into n8n.
2. Configure your own API credentials.
3. Replace the placeholder environment-specific values.
4. Configure the webhook URLs.
5. Connect the workflow to your own Supabase project.
6. Configure your Retell AI agent.
7. Test using the sample payloads in `sample-data/`.

## Project Focus

The goal of this project is to demonstrate how multiple external services can be connected into a complete event-driven automation rather than a single isolated workflow.

The system connects lead intake, database operations, AI voice calling, callback processing, appointment tracking, and analytics into one workflow architecture.
