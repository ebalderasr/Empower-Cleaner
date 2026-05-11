<div align="center">

# Empower-Cleaner

### From Waters Empower PDF reports to a clean concentration CSV — entirely in your browser

<br>

[![Stack](https://img.shields.io/badge/Stack-HTML_·_JavaScript_·_PDF.js-0F766E?style=for-the-badge)]()
[![Focus](https://img.shields.io/badge/Focus-Waters_Empower_·_HPLC_Extraction-0891B2?style=for-the-badge)]()
[![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](./LICENSE)
[![Part of](https://img.shields.io/badge/Part_of-HostCell_Lab_Suite-14B8A6?style=for-the-badge)](https://github.com/ebalderasr)

</div>

---

## What is Empower-Cleaner?

Empower-Cleaner is a **browser-based PDF extraction tool** for Waters Empower HPLC reports. Drop one or more report PDFs and it parses the concentration table, extracts each sample entry, sorts the results by clone, replicate, and timepoint, then generates a clean downloadable CSV.

It runs fully client-side in the browser using [PDF.js](https://mozilla.github.io/pdf.js/). **No installation. No backend. No data upload.**

---

## Why it matters

Waters Empower exports are often easy to read visually but inconvenient to reuse in downstream analysis. Copying concentrations by hand from PDF tables is slow and error-prone, especially when processing many clones, replicates, and timepoints.

Empower-Cleaner turns that repetitive cleanup step into a single drag-and-drop action:

- Extracts all matching samples from one or more PDFs
- Normalizes the output into a compact CSV
- Sorts records consistently for downstream kinetics or screening workflows
- Replaces missing concentration cells with `0.0` so the dataset stays rectangular

---

## How it works

Three steps. No scripts. No manual table cleanup.

<br>

**Step 1 — Upload Waters Empower PDFs**

Drag one or more report PDFs onto the drop zone or click to browse. Only `.pdf` files are accepted.

<br>

**Step 2 — Process the reports**

Click **Procesar PDFs**. The app scans each PDF page, finds the table containing `SampleName` and `Concentration`, and extracts matching rows.

<br>

**Step 3 — Review and download**

The app shows a dataset summary and a preview table, then lets you download a CSV containing all extracted records.

<br>

---

## Extraction logic

Empower-Cleaner is intentionally narrow: it is designed for a specific Waters Empower report layout and naming convention.

### Table detection

For each PDF page, the parser:

- Locates a row containing both `SampleName` and `Concentration`
- Detects nearby table columns such as `Name`, `RT`, `Area`, `Amount`, `Concentration`, and `Units`
- Rebuilds each subsequent data row from text coordinates
- Stops when the page reaches footer/report metadata such as `Report Method` or `Printed`

### Concentration handling

- Primary source: the `Concentration` column
- Fallback source: the last numeric token in `Amount` when `Concentration` is empty
- If neither yields a valid number, concentration is stored as `0.0`

### Sorting

All extracted rows are sorted as:

1. `Clona`
2. `Replica`
3. `Tiempo`

---

## Required sample naming format

The parser only keeps rows whose `SampleName` matches this structure:

```text
YYYYMMDD_CLONA_REPLICA_TTIEMPO
YYYYMMDD_CLONA_REPLICA_TTIEMPO_extra
YYYYMMDD CLONA REPLICA TTIEMPO
```

Examples:

```text
20250115_A_1_T0
20250115_A_1_T72
20250115_B_2_T96_dup
```

Parsed fields:

- `Clona`: alphabetic token after the date, converted to uppercase
- `Replica`: integer token after `Clona`
- `Tiempo`: integer after `T`
- `suffix`: any remaining text after the timepoint, kept for preview only

If a row does not match that pattern, it is ignored.

---

## Output format

The downloadable CSV contains:

```csv
Clona,Replica,Tiempo,Concentration,Original_SampleName
```

Example:

```csv
Clona,Replica,Tiempo,Concentration,Original_SampleName
A,1,0,125.4,"20250115_A_1_T0"
A,1,24,118.9,"20250115_A_1_T24"
B,2,72,0.0,"20250115_B_2_T72"
```

Notes:

- `Concentration` is exported with one decimal place
- Missing or empty concentration values become `0.0`
- `Original_SampleName` is preserved exactly as read from the PDF

---

## Features

| | |
|---|---|
| **Zero installation** | Runs entirely in the browser |
| **Multi-file processing** | Accepts multiple Waters Empower PDFs in one session |
| **Layout-aware parsing** | Reconstructs tabular PDF text using positional grouping |
| **Strict sample parsing** | Extracts clone, replicate, and timepoint from `SampleName` |
| **Deterministic sorting** | Output ordered by clone, replicate, and time |
| **CSV export** | One-click download for downstream analysis |
| **Local-only workflow** | No server, no upload, no data leaves your machine |

---

## Expected input

Empower-Cleaner expects PDF reports exported from Waters Empower with a table that includes at least:

- `SampleName`
- `Concentration`

Optional columns such as `Amount`, `RT`, `Area`, `Name`, and `Units` may be present. They help the parser align the table, but only concentration and sample name are exported.

This tool is best suited for report pages where:

- sample rows begin with a date-like token such as `20250115`
- the report uses the standard tabular text layout preserved in the PDF
- concentration values are printable as plain text, not only embedded as images

---

## Tech stack

**PDF parsing**

![PDF.js](https://img.shields.io/badge/PDF.js-EA4335?style=flat-square)

**Frontend**

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat-square&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat-square&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)

---

## Project structure

```text
Empower-Cleaner/
├── index.html          ← full app: UI, styles, PDF parsing, CSV export
├── README.md           ← project documentation
└── LICENSE             ← MIT license
```

---

## Limitations

- The parser assumes a specific Waters Empower PDF structure
- `SampleName` must follow the expected date/clone/replicate/timepoint pattern
- Clone names are currently limited to alphabetic characters in the parser
- Concentration extraction depends on text positioning and may fail on heavily reformatted exports
- The app previews up to 300 rows, although the CSV includes the full dataset

---

## License

Released under the [MIT License](./LICENSE).
