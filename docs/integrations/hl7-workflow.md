# HL7 & EMR Integration Protocols

## 1. General Principles & Approval Hierarchy
* **Order Processing:** Automatic processing creates services directly from incoming HL7 messages. Failed orders trigger a red **HL7 Requests** alert and require manual resolution.
* **Report Filename Conventions:**
  * **Unidirectional interfaces:** `[{DOS}][{PFN}][{SN}]_{TS}`
  * **Bidirectional interfaces:** `{TS}`
* **Approval Workflow:**
  * **OpenText / PointClickCare Workflow:** Upon facility project readiness and OpenText confirmation, notify `legal-ops@company.com` to send a supplemental agreement to the client prior to activating report forwarding.
  * **Non-OpenText EMR Workflow:** Notify `legal-ops@company.com` to initiate direct client agreement signing before facility connection.

---

## 2. EMR Specifications Matrix

| EMR Vendor | Directionality | Report Format | Line Breaks (`\.br\`) | Key Configuration Notes |
| :--- | :--- | :--- | :--- | :--- |
| **eOMIS** | Bidirectional | `HL7/TXT` | Enabled (`YES`) | Filename: `{EAN}_{TS}`. Requires CPT mapping via compendium, facility alias mapping, and mandatory rules for exam reasons. |
| **CorEMR** | Unidirectional | `HL7/TXT` | Disabled (`NO`) | Filename: `[{DOS}][{PFN}][{SN}]_{TS}`. Facility contact: Sample Text (`user@name.com`). |
| **PointClickCare** | Unidirectional | `HL7/PDF`, `HL7/TXT` | Disabled (`NO`) | Filename: `{TS}_{IAN}_{DOS} - {PFN}`. Route via OpenText SFTP (`xxxxxxx`). Facility account number mapped to `xxxxxx`. |

---

## 3. Negative Constraints & Failure Prevention
* **No Direct EMR Activation**: Never enable report forwarding for PointClickCare/OpenText integrations before verifying that a signed supplemental agreement exists.
* **Hard Credential Isolation**: Hardcoded SFTP passwords or SSH keys must never be committed to source repositories.
* **Strict Message Validation**: Messages with unmapped CPT codes or missing facility aliases must route to red **HL7 Requests** alert queue rather than partial creation.