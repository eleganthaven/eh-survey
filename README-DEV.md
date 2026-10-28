# EH Survey — developer notes

Live app: https://eleganthaven.github.io/eh-survey/
Repo: https://github.com/eleganthaven/eh-survey

## What it is
A single-file mobile checklist for bathroom site surveys. No backend, no build step.
Everything is in `index.html` (vanilla JS + CSS). State is kept in `localStorage`.

## Files
- `index.html` — the whole app (UI, data, output logic).
- `CHECKLIST.txt` — the checklist structure in a plain-text format (the source of truth; see format below).
- `survey-structure.json` — the same structure as JSON, for importing into other software.

## Data model (JSON / `DEFAULT_DATA` in index.html)
```
section { title, subsections[] }
subsection { title, options[] }          // title "" = no group header
                                          // title starting with "—" = divider band (e.g. "— BATHTUB —")
option { label, type, hidden, checked, text, min, max, step, unit }
  type: "tick" (checkbox) | "ticktext" (checkbox + short text) | "text" (text field) | "slider"
  hidden: true = "hidden service" → printed with " (H)" in the output
```

## Plain-text format (`CHECKLIST.txt`)
```
SECTION NAME                 uppercase line, no indent
  == BAND ==                 divider band inside a section
  Group name:                group header
  - option                   checkbox
  - option (H)               hidden service
  - Label [text]             text field
  - Label [tick+text]        checkbox + text
  - Label [slider 0-100%]    slider (unit optional: %, £)
  :                          end of group (following options have no group header)
```
The app's **Export** button copies the current structure in exactly this format.

## Output format (what "Copy" produces)
Plain text. One block per section, blank line between sections.
```
SECTION NAME
Band                         (only if something under the band is ticked, e.g. "Bathtub", "Walls")
option                       ungrouped ticked option, one per line
Group: option, option        grouped ticked options on one line, first letter lower-cased
Label text                   text fields: label + text (Name + address, Notes, Client adds: text only)
option (H)                   hidden service marker
```
CLIENT section prints without the "CLIENT" header.

## Integration hints
- To embed: copy the `<script>` and `<style>` blocks from `index.html`, or load `survey-structure.json` and render it with your own UI.
- Functions worth reusing in `index.html`: `normalize()` (data shape), `collectSec()` / `updateOut()` (output text), `exportStructure()` (text format), `lc()`.
- `KEY` (localStorage key) changes on each structure version so old saved forms don't mix with a new structure.
- Password for Edit mode is the constant `PASSWORD`.
