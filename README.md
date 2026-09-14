
# An AI-powered clinic appointment automation prototype built with n8n, Google Gemini, Telegram, and Google Sheets.

### The system helps patients check doctor availability, request appointments, view existing appointments, and cancel appointments through a conversational Telegram interface.

### Booking requests are sent to clinic staff for approval before an appointment is officially created.

### > **Note:** This is an educational prototype created to explore AI automation and workflow orchestration. It is not intended for production healthcare use.

---

## Overview

The goal of this project was to build a conversational appointment assistant that can handle common clinic appointment tasks while keeping important business operations deterministic and controlled.

Instead of allowing the AI to directly modify appointment records, the AI is mainly used to understand the patient's request and conversational context.

The workflow then uses predefined logic and Google Sheets data to perform appointment-related operations.

---

## Features

### Appointment Booking

- Understands natural-language appointment requests
- Identifies the requested doctor and date
- Checks doctor availability
- Shows available appointment slots
- Collects the patient's full name
- Allows the patient to select a time
- Asks the patient to confirm the selected slot
- Creates a booking request for staff approval
- Rechecks availability before final confirmation
- Creates an Appointment ID after approval
- Sends the confirmed appointment details to the patient

### Staff Approval

- Sends booking requests to a separate Telegram staff group
- Provides Approve / Reject actions for staff
- Allows only authorized staff to approve or reject requests
- Rechecks appointment availability before creating the final appointment
- Handles rejected booking requests
- Notifies the patient about the final result

### Appointment Lookup

Patients can ask about their existing appointments.

The system retrieves appointment information from Google Sheets rather than relying on the AI's memory.

### Appointment Cancellation

- Patients can request cancellation of an appointment
- The system retrieves the patient's confirmed appointments
- If there is one appointment, it can be selected automatically
- If there are multiple appointments, the patient can cancel them individually
- Cancellation updates the appointment status instead of deleting the record
- Cancelled appointments remain available as historical records

### Conversational Handling

The assistant maintains context across multiple messages.

For example:

```text
Patient: I want an appointment with Dr. Sarah Khan.

Assistant: What date would you like?

Patient: Monday.

Assistant: Dr. Sarah Khan is available on Monday...
         These time slots are available...

Patient: 3 PM.

Assistant: Dr. Sarah Khan is available at 3 PM on Monday.
           Would you like to reply "yes" to confirm?

Patient: Yes.

Assistant: Thanks! Your appointment request has been sent
           to the clinic for approval.
```

After staff approval, the patient receives the final appointment confirmation.

---

## Workflow Architecture

The workflow separates conversational AI from deterministic business logic.

```text
Patient
   |
   v
Telegram
   |
   v
n8n Workflow
   |
   v
Google Gemini AI
   |
   | Understands patient request
   | Extracts appointment information
   v
Deterministic Workflow Logic
   |
   +----------------------+
   |                      |
   v                      v
Google Sheets        Availability Engine
   |                      |
   |                      v
   |                Available Slots
   |                      |
   +----------+-----------+
              |
              v
       Booking Request
              |
              v
     Staff Telegram Group
              |
        Approve / Reject
              |
              v
      Recheck Availability
              |
              v
      Confirmed Appointment
              |
              v
           Patient
```

---

## AI + Automation Approach

One of the main design decisions in this project was separating **AI reasoning** from **deterministic workflow logic**.

### The AI is used for conversational understanding

Examples include:

* Understanding appointment requests
* Extracting information from natural language
* Understanding confirmation messages
* Identifying cancellation intent
* Maintaining conversational context

### The workflow handles critical operations deterministically

Examples include:

* Checking doctor availability
* Reading appointment records
* Creating booking requests
* Updating cancellation status
* Generating Appointment IDs
* Checking staff authorization
* Rechecking availability before final confirmation

This reduces the risk of allowing the AI to directly make important appointment-data changes.

---

## Data Structure

The system uses **Google Sheets as the data source for the prototype**.

### Doctor Schedules

Stores information such as:

* Doctor ID
* Doctor Name
* Specialty
* Working Day
* Start Time
* End Time
* Appointment Duration
* Active Status

### Schedule Exceptions

Stores temporary schedule changes such as:

* Doctor unavailable on a specific date
* Temporary unavailable time ranges
* Additional available hours

### Booking Requests

Stores appointment requests before final approval.

Example information includes:

* Request ID
* Patient Name
* Patient Contact
* Doctor
* Appointment Date
* Appointment Time
* Duration
* Request Status
* Created / Updated timestamps

### Appointments

Stores confirmed appointment records.

Example information includes:

* Appointment ID
* Request ID
* Patient Name
* Patient Contact
* Doctor
* Appointment Date
* Appointment Time
* Duration
* Token Number
* Status
* Created / Updated timestamps
* Confirmation / Cancellation timestamps

### Clinic Settings

Stores configurable clinic information such as:

* Clinic ID
* Clinic Name
* Currency
* Time Zone
* Booking approval requirement
* Booking window
* Communication channels
* Staff approval channel
* Authorized staff information

---

## Safety and Business Rules

The workflow follows several important rules.

* The AI does not directly modify appointment records.
* Appointment IDs are generated by the workflow rather than invented by the AI.
* Doctor availability comes from the scheduling data.
* Appointment availability is rechecked before final confirmation.
* Booking requests require staff approval before becoming confirmed appointments.
* Cancellation updates the appointment status instead of deleting the record.
* Existing appointment information is retrieved from Google Sheets.
* The system does not rely on AI memory as the source of truth for appointment data.
* Staff approval actions are restricted to authorized staff.
* Multiple appointments can exist for the same patient.
* Appointments can be cancelled individually.

---

## Tech Stack

| Technology    | Purpose                                              |
| ------------- | ---------------------------------------------------- |
| n8n           | Workflow automation and business logic               |
| Google Gemini | Natural-language understanding and conversational AI |
| Telegram      | Patient and staff communication                      |
| Google Sheets | Appointment and scheduling data                      |
| JavaScript    | Deterministic data processing and workflow logic     |

---

## Demo

### Patient Conversation

#### Booking Request

<img width="822" height="786" alt="Screenshot 2026-09-14 222654" src="https://github.com/user-attachments/assets/d4f3ea26-a0eb-40bf-9094-89e8436ae810" />

#### Appointment Confirmation

<img width="822" height="788" alt="Screenshot 2026-09-14 222714" src="https://github.com/user-attachments/assets/aef708df-72f3-4fb9-bfa1-ae094cd64535" />

---

### Workflow Architecture

### Full Workflow

<img width="1492" height="682" alt="Screenshot 2026-09-14 205925" src="https://github.com/user-attachments/assets/05d4d57c-9f37-431a-9b39-3ea180c9e86a" />

### Patient and Appointment Logic
<img width="1492" height="686" alt="Screenshot 2026-09-14 211127" src="https://github.com/user-attachments/assets/5835c626-8e16-447e-8b61-53e64ae807cd" />


### Cancellation Flow

<img width="1495" height="687" alt="Screenshot 2026-09-14 211044" src="https://github.com/user-attachments/assets/a7c8b100-86c3-4a28-8475-548072912592" />


### Staff Approval and Appointment Creation

<img width="1372" height="687" alt="Screenshot 2026-09-14 210854" src="https://github.com/user-attachments/assets/45cf9177-94cd-44ac-8089-9ed5a3daea4a" />

---

## Testing

The workflow was tested through real Telegram conversations during development.

Examples of tested functionality include:

* Listing available doctors
* Checking doctor availability
* Selecting a specific appointment time
* Collecting the patient's full name
* Sending a booking request for staff approval
* Approving a booking request
* Rechecking availability before final confirmation
* Generating an Appointment ID
* Looking up an existing appointment
* Cancelling a confirmed appointment
* Preventing unauthorized staff approval
* Handling normal conversational messages after completing an appointment

Testing was an important part of the development process because several workflow problems were only discovered by examining actual node inputs and outputs.

---

## Development and Debugging

This project was built iteratively rather than as a single completed workflow.

During development, several problems were discovered and fixed, including:

* AI-generated information that did not match the clinic's doctor list
* Incorrect interpretation of cancellation intent
* Workflow branches executing simultaneously
* Structured AI output not matching the expected schema
* Nested output fields being referenced incorrectly
* Appointment Sheet columns changing after nodes were configured
* Patient responses being routed through the wrong workflow branch
* AI state sometimes failing to preserve information correctly
* Follow-up messages being incorrectly interpreted as appointment confirmations
* Cancellation records initially being returned with incorrect field mappings

These issues were identified by testing the workflow, examining node inputs and outputs, and then changing the relevant workflow logic.

---

## What I Learned

Building this project helped me understand several practical concepts in AI automation.

### 1. AI should not necessarily control the entire system

An AI model can understand language, but important business operations can be more reliable when handled by deterministic workflow logic.

Combining AI with deterministic automation made the system more predictable.

### 2. Debugging requires looking at the actual data

I learned that changing the AI prompt is not always the correct solution.

Sometimes the problem was caused by:

* A wrong field reference
* A workflow connection
* An incorrect output structure
* A changed Google Sheets column
* A routing condition

Looking at actual node inputs and outputs helped identify where the problem really occurred.

### 3. Structured output matters

When AI is connected to an automation workflow, the output needs to follow a predictable structure so that later workflow steps can use it reliably.

### 4. Testing individual scenarios is important

A workflow can look correct on the canvas but still behave incorrectly during a real conversation.

Testing individual scenarios helped reveal problems that were not obvious from the workflow diagram.

### 5. AI and automation solve different problems

This project helped me understand that AI is useful for interpreting human language, while deterministic automation is useful for enforcing business rules and performing predictable operations.

---

## Current Limitations

This project is currently a prototype.

It does not currently include:

* WhatsApp integration
* Production healthcare security and compliance
* Production-grade database infrastructure
* Multi-clinic deployment
* Advanced authentication and user management
* Full rescheduling functionality
* Production monitoring and reliability infrastructure

The current cancellation flow is designed so that patients can cancel appointments individually.

---

## Future Improvements

Possible future improvements include:

* Rescheduling appointments
* WhatsApp integration
* Support for multiple authorized staff members
* A proper production database
* More advanced clinic configuration
* Better appointment management interfaces
* Production-grade security and compliance
* Improved monitoring and error handling
* Multi-clinic support

---

## Disclaimer

This project is an educational/demo prototype created to explore AI automation and workflow orchestration.

It should not be used to manage real patient information or deployed as a production healthcare system without appropriate security, privacy, compliance, testing, and professional review.
