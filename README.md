# Penetration Testing Report – Mediroza General Hospital

## Executive Summary
This repository documents a penetration testing assessment of the Mediroza General Hospital web application and supporting files. The evidence shows multiple serious security weaknesses that allowed access to sensitive healthcare and employee information, including patient pathology records, staff data, and shareholder information.

The assessment identified the following major issues:
- Direct exposure of patient lab report files and supporting documents
- SQL injection in web application queries
- Weak authentication and password reuse practices
- Exposure of database artifacts and backup files
- Inadequate document protection controls for PDF files

The overall risk level is high because confidential healthcare data and internal records were accessible through a combination of information disclosure, broken authentication, and injection flaws.

## Scope
The assessment focused on the Mediroza portal, file download functionality, authentication flow, and underlying data exposure. The evidence in this repo includes screenshots and data captures related to:
- Patient report download pages
- PDF metadata and password-protected files
- Staff/employee record exposure
- Shareholder and account data leakage
- SQL injection validation and exploitation
- Password-cracking activity against recovered credential material

## Methodology
The testing approach included:
1. Reconnaissance and information gathering
2. Review of publicly visible file listings and downloadable content
3. Analysis of PDF metadata and encryption protections
4. Authentication testing and credential verification
5. Injection testing against application input parameters
6. Review of database artifacts and backup material
7. Validation of weak password and user enumeration vectors

## Findings

### 1. Sensitive patient files were exposed and downloadable
The portal contained a patient lab report page showing downloadable pathology records for multiple patients. In the captured evidence, the application listed records such as:
- Pathology Report – S. Dlamini
- Pathology Report – P. Reddy
- Pathology Report – E. Thompson

These records were presented as downloadable files, and the associated screenshots confirm the report names and lab references. This indicates that patient records were made available through a file-access mechanism without adequate access validation or proper encryption controls.

Severity: High

### 2. PDF files were inadequately protected
The patient reports were password protected, but the screenshots show that the PDFs could be processed and unlocked using `qpdf` to create an unprotected copy. This indicates weak document-level protection and poor handling of sensitive files.

Evidence from the repository includes:
- `reading_metadata.png`
- `installing_qpdf.png`
- `creating_unlocked_copies.png`
- `pdf1.png`, `pdf2.png`, `pdf3.png`

The metadata and generated unlocked copies demonstrate that the files were not securely protected against unauthorized access or easy circumvention.

Severity: High

### 3. SQL injection exposed internal records
The application was vulnerable to SQL injection. The evidence shows exploitation of the login or query parameter flow, leading to retrieval of database content beyond what a normal user should access. The screenshots show successful SQLi payloads and database record dumps, including staff and shareholder material.

The captured evidence includes:
- `sql_injection.png`
- `sql.png`
- `staff table.png`
- `shareholders.png`

This indicates that input values were directly concatenated into SQL statements without sanitization or parameterization. The resulting access allowed the attacker to enumerate user and staff records and extract sensitive internal data.

Severity: Critical

### 4. Staff and shareholder tables were publicly exposed through database queries
The SQL injection flow enabled access to internal tables, including employee/staff and shareholder records. This is a severe confidentiality issue because it exposes personal information and operational data not intended for end users.

Evidence from the screenshots indicates records such as:
- Staff names and roles
- Employment-related attributes
- Shareholder details and associated business information

Severity: Critical

### 5. WeakPassword and account compromise were feasible
The screenshots show password-guessing and cracking activity against recovered credential material. A wordlist was used in the attack chain, and a successful admin account/password result was demonstrated. The evidence includes:
- `wordlist.png`
- `admin_acc.png`
- `hash3.png`
- `crack3.png`
- `proof_acc_exist.png`

This suggests that password complexity requirements were weak or absent and that credentials were either reused, predictable, or stored with weak hashing.

Severity: High

### 6. Directory and file enumeration revealed additional data sources
The repository evidence shows database artifacts and file listings that were accessible through the application or host environment. In particular, a SQL backup file was accessible in the application structure and likely contained the database schema and content for further exploitation.

This is consistent with insecure file-handling and weak operational controls.

Severity: High

## Attack Path Summary
The observed workflow followed this pattern:
1. Enumerate the application and look for downloadable or exposed files.
2. Identify patient report access points and protected PDFs.
3. Extract metadata and use PDF tools to unlock copies for reading.
4. Test login and query parameters for injection vulnerabilities.
5. Exploit SQL injection and retrieve staff/shareholder records.
6. Recover credentials using wordlists and validation steps.
7. Confirm a valid administrator account and access privilege escalation path.
8. Exfiltrate patient and internal data from the hospital system.

## Evidence Files
The following files in this repository capture the findings:
- `pdf1.png` – patient report page / evidence
- `pdf2.png` – patient report detail / PDF content
- `pdf3.png` – report details and metadata access
- `files.png` – list of downloadable pathology reports
- `sql.png` – database file and SQL-related discovery
- `sql_injection.png` – SQL injection exploitation evidence
- `staff table.png` – extracted staff data
- `shareholders.png` – shareholder data exposure
- `wordlist.png` – password-cracking input evidence
- `admin_acc.png` – admin credential validation evidence
- `hash3.png` – credential hash material
- `crack3.png` – password cracking output
- `proof_acc_exist.png` – confirmation of account existence
- `reading_metadata.png` – metadata review of PDFs
- `installing_qpdf.png` – tool installation used to unlock PDFs
- `creating_unlocked_copies.png` – evidence of PDF circumvention
- `no_acc.png` – failed access attempt or validation screenshot

## Risk Assessment
| Severity | Risk | Description |
| --- | --- | --- |
| Critical | Data exposure through SQL injection | Attackers could retrieve staff, shareholder, and sensitive operational records |
| High | Patient record exposure | Lab report files and sensitive medical data were exposed or accessible |
| High | Weak authentication controls | Password reuse and weak hashes allowed credential recovery |
| High | Insecure document handling | PDFs could be unlocked and copied with basic tools |

## Recommended Remediation
1. Remove all direct SQL injection flaws by using parameterized queries and strict validation.
2. Implement proper authorization checks on all file downloads and report access pages.
3. Restrict directory listing and disable exposure of backup or database files.
4. Enforce strong password policies and MFA for administrative accounts.
5. Upgrade password storage to a modern salted hashing algorithm.
6. Protect PDFs with strong encryption and access controls that cannot be bypassed by simple third-party tools.
7. Conduct continuous security monitoring and vulnerability scanning for web and application layers.
8. Review all healthcare data access logs to identify whether unauthorized access occurred.

## Conclusion
The evidence captured in this repository shows that the Mediroza General Hospital application contains multiple critical vulnerabilities that could lead to unauthorized access to patient and organizational data. The most serious issue is the SQL injection exposure, which enabled broad data retrieval and a clear breach pathway. Combined with weak password practices and insecure document handling, these flaws represent a major security incident risk for sensitive healthcare information.

This assessment highlights the need for immediate remediation before the system is used in a production environment involving protected medical records.
