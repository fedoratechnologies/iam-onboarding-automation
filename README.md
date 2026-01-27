HTHS Student Onboarding Automation

Overview

This repository contains the automation used to onboard Holy Trinity High School (HTHS) students into Fedora’s IAM stack.

The workflow ingests a CSV from Nextcloud, normalizes student data, and prepares it for downstream identity provisioning systems such as Active Directory, Keycloak, and future integrations (LMS, email, MFA).

This repo is designed to be:
	•	Auditable
	•	Repeatable
	•	Safe for automation
	•	Extensible via AWX / Ansible

⸻
Architecture (High Level)
Nextcloud (CSV)
   ↓
n8n Workflow
   ↓
Normalized JSON
   ↓
AD / Keycloak / Future Systems

Current Scope (v1)

✔ CSV ingestion from Nextcloud
✔ CSV → JSON parsing
✔ Data normalization (name, email, cohort/year)
✔ Cohort → year extraction
✔ Clean, automation-ready JSON output

❌ No user creation yet (handled in later phases)

Data Flow

Input Source
	•	Nextcloud WebDAV
	•	File path example:

  /Project Docs/HTHS/HTHS_clean_student.csv

  Required CSV Headers

⚠️ Header names are case- and whitespace-sensitive


Header Name
Description
First Name [Required]
Student first name
Last Name [Required]
Student last name
Email Address [Required]
Student school email
Cohort.  [Required]
Path ending in graduation year (note double space)


Example value:
/Users/Students/2026

Normalized Output Schema

Each student is transformed into the following JSON structure:
{
  "first_name": "Trevion",
  "last_name": "Psdffdsy",
  "email": "tpgdgsdg9@holytrinity-hs.org",
  "year": "2029"
}

Repository Structure
hths-onboarding-automation/
├── README.md
├── n8n/
│   └── workflows/
│       └── hths-students-csv-ingest.json
├── ad/
│   └── scripts/
│       └── create_students.ps1
├── awx/
│   └── job-templates/
├── keycloak/
│   └── docs/
└── data/
    └── samples/
        └── students_sample.csv


        n8n Workflow Notes
	•	Trigger: Manual Trigger
	•	Ingestion: Nextcloud → Download
	•	Parsing: Extract from File (CSV)
	•	Transformation: Code node (JavaScript)

  Year Extraction Logic

  const cohortKey = "Cohort.  [Required]";
const cohortRaw = $json[cohortKey];
const match = cohortRaw.match(/(\d{4})$/);
const year = match ? match[1] : null;


Future Phases

Planned next steps:
	•	Active Directory user creation (OU-aware)
	•	AWX / Ansible integration
	•	Keycloak user sync & group assignment
	•	Validation + error reporting
	•	Batch controls & dry-run mode
	•	Audit logging

⸻

Change Control
	•	All changes tracked via git
	•	Workflow JSON must be exported and committed after updates
	•	Breaking changes require version bump (v2, v3, etc.)

⸻

Maintainer

Fedora Technologies
Security & IAM Automation Team

⸻

If you want, next I can:
	•	Add a “How to run” section
	•	Write a runbook for operators
	•	Add a schema validation block
	•	Prep the AD provisioning README

