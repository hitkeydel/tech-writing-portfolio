# Documentation: HL7 EMR Integration Workflow

## 1. Overview & General Principles

### 1.1 Inbound Order Processing
- **Automatic Processing**: Configured via system settings to automatically convert incoming HL7 messages into service orders.
- **Error Handling**: Orders failing automatic processing are flagged in red under **HL7 Requests**. Manual processing and issue resolution are required for flagged orders. Reference documentation: `https://XXXXX.com/docs/hl7-requests`.

### 1.2 Outbound Report Formatting & Naming Rules
- **Vendor Alignment**: Report transmission formats must be agreed upon with vendors prior to activation.
- **Filename Conventions**:
  - **Unidirectional Interfaces**: Must contain comprehensive identifiers. Recommended template: `[{DOS}][{PFN}][{SN}]_{TS}`.
  - **Bidirectional Interfaces**: Must include a timestamp token (`{TS}`) to guarantee filename uniqueness.

### 1.3 Legal & Administrative Onboarding Workflow
1. **OpenText Integrations**:
   - Complete facility project setup.
   - Upon receiving OpenText confirmation, notify the Legal/Account Department (`<account_manager@mail.com>`) with client details and facility lists.
   - Await client execution of the supplemental agreement (Addendum) before enabling and activating report forwarding via OpenText.
2. **Direct (Non-OpenText) Integrations**:
   - Notify Senior Management / Integration Lead (`<integration_lead@mail.com>`) to initiate the supplemental agreement directly with the client prior to technical setup.

---

## 2. Integration Protocols by System

### 2.1 eOMIS (Bidirectional Integration)

#### Configuration & Entity Mapping
1. **SFTP & Inbound Receiver**:
   - Navigation: **Setup > Referring > Appointment Receiver Providers**.
   - Enable **Enable auto processing** for automated service generation from HL7 messages.
2. **Test & Study Mapping**:
   - Navigation: **Setup > Tests > Studies**.
   - Mapping Logic: Studies are resolved using CPT codes and modifiers matching the agreed compendium.
   - *Error*: Unresolved CPT code or modifier.
   - *Resolution*: Verify against approved compendium; if missing or ambiguous, request code clarification from vendor or client.
3. **Facility Mapping**:
   - Navigation: **Setup > Referring > Referring Facilities**.
   - Mapping Logic: Facilities are matched via facility numbers mapped to aliases.
   - *Error*: Unknown facility incoming order.
   - *Resolution*: Confirm facility details with client, create facility record, assign alias, update Mandatory Fields Rules, assign client account/contract, and update referring groups.
4. **Physician & Patient Entity Creation**:
   - **Referring Doctors**: Auto-created and linked to facility.
   - **Patients**: Auto-created upon order intake.
   - *Error*: Database conflict due to existing patient with mismatched Patient ID.
   - *Resolution*: Correct Patient ID in system or coordinate with client for ID reconciliation.
5. **Mandatory Fields Rules**:
   - Enforce **Reason for Exam or Diagnosis** to ensure history data propagates to Medical Diagnostic Workstation (MDW) and appears on preliminary reports.

#### Outbound Report Configuration
- **SFTP Setup**: **Setup > Connection Manager** (Add SFTP endpoint).
- **Report Parameters**:
  - **Report Format**: `HL7/TXT`
  - **Line Breaks**: Enable `Use \.br\ for line breaks`
  - **Report Filename Template**: `{EAN}_{TS}`

---

### 2.2 CorEMR (Unidirectional Integration)

#### Transmission Parameters
- **Format**: `HL7/TXT`
- **Line Break Option**: `Use \.br\ for line breaks` = **Disabled (NO)**
- **Report Filename Template**: `[{DOS}][{PFN}][{SN}]_{TS}`

---

### 2.3 PointClickCare (Unidirectional Integration via OpenText)

#### Client Onboarding Procedure

##### Scenario A: New Domain Setup (New Service Provider)
1. Identify owner contact for the XXXXX (sub)domain.
2. Verify if domain owner has prior PointClickCare (PCC) transmission history.
3. If no prior history, contact OpenText (`<opentext_support@opentext.com>`) for registration.
4. Send formal initiation email to domain owner, facility contact, and PCC representative:
   ```text
   Subject: PCC Integration Request - [Client Name]
   
   Hello,
   Our client [Client Name] is looking to send radiology results to [Facility Name]. 
   Please initiate the integration process with PointClickCare:
     * The facility needs to receive radiology results from [Client Name].
     * [Client Name] uses XXXXX as its radiology software connected to PointClickCare via OpenText.
   Please include support@XXXXX.com in all correspondence for this project.
   ```
5. Submit setup request to OpenText onboarding representatives:
   ```text
   Subject: Connect a new service provider [Client Name] via XXXXX to PointClickCare
   
   Hello,
   We received a request to connect [Client Name] to PointClickCare for radiology results delivery.
     - Service provider name: [Client Name]
     - Address: [Client Address]
     - Contact name and email: [Client Contact]
     - Website: [Client Website]
     - Service state(s): [States]
   Please confirm listing status in PCC Partner Exchange and estimated onboarding timeline.
   ```

##### Scenario B: Adding Facilities for Existing Clients
1. Send instruction template to client:
   ```text
   Dear Customer,
   To enable report transfer to [Facility Name]'s PointClickCare system, please request the facility to initiate integration.
   The facility must notify their PointClickCare Account Manager with the following details:
     * The facility needs to receive radiology results from [Client Name].
     * [Client Name] uses XXXXX connected to PointClickCare via OpenText.
   Please include support@XXXXX.com in all correspondence.
   ```
2. Notify OpenText upon confirmation:
   ```text
   Subject: New radiology results integration request
   
   Hello,
   Our client [Client Name] requests report transfer setup to facility [Facility Name].
   Service Provider: [Client Name]
   Facility Name: [Facility Name]
   Facility Address: [Facility Address]
   Facility Account Number (MSH:6): [Facility MSH:6 Identifier]
   ```
   *Note: MSH:6 corresponds to `Referring Facility Full Name`.*

#### XXXXX System Configuration
1. **Facility Settings** (**Setup > Referring > Referring Facilities**):
   - **Report Format**: Select `PDF`, `HL7/PDF`, and `HL7/TXT`.
   - **Viewer Link**: Enable `Include shared viewer link`.
   - **Report Filename Template**: `{TS}_{IAN}_{DOS} - {PFN}`
2. **SFTP Connection Manager** (**Setup > Connection Manager**):
   - **Type**: SFTP (FTP over SSH)
   - **Host / Port**: `<sftp_host>:<port>`
   - **Authentication**: `<username>` / `<password>`
   - **Remote Path**: `/out/live/new`