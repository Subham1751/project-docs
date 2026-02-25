# Automated Academic Research Paper Validation System

An end-to-end automated pipeline to **extract**, **standardize**, and **validate** academic research paper evidence (acceptance email, accepted paper, fee receipt) for each faculty member, and generate **structured Excel reports** for audit/verification.

---

## Overview

This project automates the validation of academic research papers by processing three key PDFs per faculty/paper:

1. **Acceptance email** (PDF)
2. **Accepted paper** (PDF)
3. **Fee receipt** (PDF)

It extracts essential metadata using **PDF text extraction + OCR (when needed)** and applies **rule-based validation** to confirm authenticity and consistency. Results are exported to Excel for easy review by administrative/research teams.

---

## Problem Statement

Manual verification of research paper submissions is time-consuming and error-prone due to:

- Multiple documents per paper
- Mixed PDF formats (text-based vs scanned)
- Inconsistent templates and metadata placement
- Risk of invalid/forged submissions

This system reduces effort by automating:

- PDF content extraction
- Metadata parsing via regex
- Consistency checks across documents
- Generation of Excel-based validation reports

---

## Workflow

### Step 1 — Data Collection (Input PDFs)

Collect the following PDFs for each faculty/paper record:

- Acceptance mail (PDF)
- Accepted paper (PDF)
- Fee receipt (PDF)

### Step 2 — Metadata Extraction (PDF → Text → Excel)

1. Extract text using:
   - `pdfplumber` for text-based PDFs
   - OCR pipeline (`pdf2image` + `pytesseract` + `PIL`) for scanned PDFs
2. Apply **regex patterns** to extract key metadata:
   - Conference email
   - Acceptance email date
   - Title
   - Fee receipt date
   - Authors
   - Affiliations
3. Store extracted fields into an **Excel extraction report** using `pandas`.

### Step 3 — Validation Logic (Checkpoints → Excel)

Validate extracted fields using checkpoints such as:

- Fee receipt date must be **after** acceptance email date
- Title must **match** between accepted paper and acceptance email
- Author must exist in the accepted paper’s author list
- Conference email must not be from a **personal email domain** (e.g., Gmail/Yahoo/Outlook, etc.)

Store results into a **validation Excel report**, where each checkpoint is a separate column returning:

- `Confirmed`
- `Not Confirmed`

---

## Features

- Handles both text-based and scanned PDFs (OCR supported)
- Regex-driven metadata extraction for flexible parsing
- Rule-based validation across multiple documents
- Excel reporting for:
  - Extracted metadata
  - Validation checkpoints + overall status
- Designed for offline/local usage (suitable for institutional workflows)

---

## Tech Stack

### Language

- **Python**

### Libraries

- **pandas** — data processing + Excel export
- **re** — regex extraction
- **pdfplumber** — extract text from PDFs
- **pytesseract** — OCR for scanned PDFs
- **dateutil** — robust date parsing
- **pdf2image** — convert PDF pages to images for OCR
- **PIL (Pillow)** — image handling for OCR

---

## Project Structure (Recommended)

```text
project-root/
│
├── data/
│   ├── input_pdfs/
│         ├── faculty_01/
│         | ├── acceptance_email.pdf
│         │ ├── accepted_paper.pdf
│         │ └── fee_receipt.pdf
|         |
│         └── faculty_02/...
│
│
├── extraction_report.xlsx
├── validation_report.xlsx
│
|
├── extractor.py
├── validator.py
│
├── requirements.txt
└── README.md
```

---

## Installation & Setup

## 1) Create Environment (Optional but Recommended)

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate
```

## 2) Install Python Dependencies

```bash
pip install -r requirements.txt
```

## 3) Install OCR Dependency (Tesseract)

This project uses `pytesseract`, which requires **Tesseract OCR** to be
installed on your system.

- Install Tesseract for your OS (Windows/macOS/Linux)
- Ensure `tesseract` is available in PATH\
- Or set the Tesseract path in your code if required.

---

# How to Run

## 1) Place Input PDFs

Put all PDFs into the `data/input_pdfs/` directory (organized per
faculty/paper as you follow).

## 2) Run Extraction

Generates the extraction report Excel.

```bash
python src/extractor.py
```

## 3) Run Validation

Reads extracted metadata and generates the validation report Excel.

```bash
python src/validator.py
```

If you have a single combined runner script (e.g., `main.py`), replace
the commands accordingly.

---

# Outputs

## 1) Extraction Report (Excel)

Contains extracted fields such as:

- conference_email
- acceptance_email_date
- title
- fee_receipt_date
- authors
- affiliations
- source_file_names (optional but recommended)

Saved to (example):

    data/outputs/extraction_report.xlsx

## 2) Validation Report (Excel)

Contains checkpoint columns such as:

- fee_date_after_acceptance_date → Confirmed / Not Confirmed
- title_match_email_vs_paper → Confirmed / Not Confirmed
- author_in_paper_author_list → Confirmed / Not Confirmed
- conference_email_not_personal → Confirmed / Not Confirmed
- overall_status → Validated / Not Validated

Saved to (example):

    data/outputs/validation_report.xlsx

---

# Validation Checkpoints (Implemented)

### 1) Fee receipt date after acceptance email date

Ensures payment proof is chronologically valid.

### 2) Title match between acceptance email and accepted paper

Prevents mismatch or substitution of paper documents.

### 3) Author verification

Confirms the claimed author appears in the accepted paper's author list.

### 4) Conference email authenticity

Flags acceptance emails coming from personal domains (e.g.,
Gmail/Yahoo/Outlook).\
Helps detect non-official communication.

---

# Current Progress

- Extracts content from PDFs successfully (OCR supported)
- Regex patterns extract key metadata reliably (pattern-specific)
- Validation results are generated and exported
- System can determine whether a paper is validated based on
  checkpoints

---

# Notes & Limitations

- OCR accuracy depends heavily on scan quality and resolution.
- Regex rules may need tuning for different conference
  templates/receipt formats.
- Title matching may require fuzzy matching (case, punctuation, line
  breaks) for improved robustness.


### || Documentation repo is read-only; contributions not accepted ||
