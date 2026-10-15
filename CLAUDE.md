# Calibration Tracker

Web register of instruments at Bulwer Island, showing each instrument's calibration status against its schedule. Built like a spreadsheet: one row per instrument, and clicking a row opens an overlay with that instrument's calibration history and certificate PDFs.

## About the owner

James is an instrumentation technician, not a software developer. He edits through the GitHub web UI and does not use a terminal.

- Do the work yourself, then explain what changed in plain words. Don't hand him command-line steps.
- Use Australian spelling and dates (DD/MM/YYYY in the UI).
- He and a few colleagues use this with view or edit access.

## Stack and rules

A single file, `calibration_tracker.html`, with plain HTML, CSS and JavaScript. No framework and no build step.

- Supabase JS currently loads from the jsDelivr CDN. That's acceptable here because the tracker needs a connection anyway, but if this becomes a PWA, copy the library into a local `lib/` folder like the Calibration-Certificate app.
- Keep the spreadsheet feel: dense table, sticky header, red/amber/green status colours defined as CSS variables in `:root`.

## Database (shared with Calibration-Certificate)

Supabase project `fwnbnssiekedrruikytk`. The publishable key in the HTML is meant to be public; row-level security is on for every table. Never put a service-role key in this repo.

- `instruments`: one row per instrument. `tag_number` is unique and is the AMS Tag used by Terminal Gauging Services (TGS). Also `area` (for example BBU, Kirra Street, PMB), `manufacturer`, `model`, `serial_number`, `interval_months` (default 12), `responsible_person`.
- `calibration_events`: one row per calibration. `result` is `Passed` or `Failed`. `pdf_path` points into the `certificates` storage bucket. Ignore rows where `is_draft` is true when working out status.
- `profiles`: role is `admin`, `editor` or `viewer`. Hide edit controls from viewers.
- Also `sites`, `instrument_documents`, `calibration_procedures`, `calibration_procedure_items`.

The Calibration-Certificate app writes to these same tables. Check it before altering a column.

## Status logic

The table reads from the `instrument_status` database view, not the raw tables. The view takes each instrument's latest non-draft calibration and adds the instrument's `interval_months` to get `next_due` and `days_until_due`. Its `status` is `Overdue` (red), `Due Soon` within 30 days (amber), `OK` (green) or `No calibration on record` (grey). Change the rule in the view, not in the page, so every app agrees.

## Planned work

- Upload a certificate PDF made outside the app, extract its details automatically, and let James confirm the fields before saving.
- Automated reminders for calibrations coming due.

## Before you finish

- Check the table loads, filters work, and the overlay opens with history and PDF links.
- Summarise the change for James in two or three plain sentences.
