---
name: dataflow-docs
description: |
  Generates a full Business and Technical Documentation from a Power BI dataflow
  generation JSON export, in German or English (user's choice), always following
  a fixed document structure: system overview, source systems, a per-query table
  catalog (profile + column list), core business logic, relationships, key
  figures, and data-governance notes. Use when user asks to "document my
  dataflow", "generate documentation from this dataflow export", "create dataflow
  documentation", "turn this dataflow JSON into docs", "document this dataflow in
  English", "dataflow Dokumentation auf Deutsch", or uploads a dataflow .json
  export and asks for documentation.
  Do NOT use for live Power BI reports/semantic models (use the Power BI
  tools), for editing an existing Word file (use the docx skill directly), or
  for summarizing arbitrary non-dataflow JSON.
cowork:
  category: writing
  icon: DocumentBulletList
---

## Overview
Takes a Power BI dataflow JSON export and produces a complete Word document that
**always follows the same structure** — the blueprint in
[references/document-structure.md](references/document-structure.md), modelled on
the gold-standard example [references/example-documentation.docx](references/example-documentation.docx).
The output language is **German or English (the user chooses; default German)**,
in factual reference style. Only the *content and the localized labels* change;
the *sections, headings, table layouts, and structure stay identical every time
and in every language*.

## When to Use
- The user has a dataflow `.json` export (in `input/` or attached) and wants it documented.
- The user asks to regenerate documentation, or wants the same export in the other language.

## When NOT to Use
- Documenting a live Power BI report or semantic model — use the Power BI tools instead.
- Editing or reformatting an existing Word document — use the `docx` skill directly.
- Summarizing arbitrary JSON that is not a dataflow export — answer inline or use the relevant skill.

## Quick Start
```
User: "Generate the documentation from this dataflow export in English."
1. Determine language: English (explicitly requested; default would be German).
2. Locate the export: Glob input/**/*.json (or use the attached path); Read it.
3. Read references/document-structure.md — the REQUIRED skeleton + language map.
4. Parse the JSON; map its content onto every section, using the chosen language's labels.
5. Build the Word document via the docx skill, saved to output/.
6. Confirm delivery (Glob output/**/*) and tell the user it's ready and in which language.
```

## Core Instructions

### Step 1: Determine the output language
- If the user names a language ("in English", "auf Englisch", "in German", "auf Deutsch"), use it. Otherwise **default to German**.
- The choice controls every section title, table header, fixed row label, and the generated prose — all taken from the language map in the blueprint. It does **not** change identifiers (names, IDs, column names, data types, GUIDs, source names, relationship keys), which stay verbatim from the JSON.
- State the language used in the final message.

### Step 2: Load the blueprint and the export
- **Always read [references/document-structure.md](references/document-structure.md) first** with the `Read` tool — it defines the mandatory sections, heading order, table layouts, and the German↔English label map. The structure is not optional and not improvised.
- Open [references/example-documentation.docx](references/example-documentation.docx) (via `python -m markitdown` or the `docx` skill) to match tone, heading styles, and table formatting. It is German; for English output, translate the labels per the map and write the prose in English. Its table/query/column names are case-specific — copy the *form*, never the *content*.
- Locate the export: if a file path is attached, `Read` it directly; otherwise find it with `Glob input/**/*.json`. If several JSON files match, list them and ask which is the dataflow export.
- If the file does not parse as JSON, say so plainly and ask the user to re-export — never invent contents.

### Step 3: Extract the model from the JSON
Pull, per the blueprint's "Fixed vs. derived content" rules (parse the JSON in a Python script, not by eye):
- **Dataflow metadata**: name, `description`, `version`, `culture`, modified timestamp.
- **Queries** (`pbi:mashup.queriesMetadata`): per query — name, `queryId`, `loadEnabled`, the Power Query (M) expression from `pbi:mashup.document`, and the column list with data types from the matching `entities[].attributes`.
- **Sources**: server, database, schema, and source object per query (parsed from the M `Sql.Database`/navigation steps); flag tables generated in Power Query (e.g. calendar).
- **Relationships** (`relationships`): every explicit relationship as `Tabelle.Spalte → Tabelle.Spalte`.
- **Refresh**: per-entity `partitions[].refreshTime`.
- **Counts**: number of queries, entities, and explicit relationships — compute these in code, not by eye.

### Step 4: Assemble the document to the blueprint
Build, in order, using the chosen language's labels: title block + metadata table → table of contents → **Section 1 System Overview** → **2 Source Systems** → **3 Table Catalog** (a Heading 2 profile + "Technical structure" column table for **every** query, in JSON order) → **4 Core Business Logic** → **5 Recommended Relationships** (explicit + recommended) → **6 Key Figures** → **7 Data Governance Notes** with the closing quality-check sentence. Use the exact table headers and row labels from the blueprint's language map.
- Hard facts (IDs, columns, types, sources, relationships, counts) come straight from the JSON, untranslated.
- Functional interpretation (purpose, granularity, key guesses, KPI derivations, recommended relationships) is written in the chosen language but must be hedged. Absent field → the localized "Not included in export" placeholder.

### Step 5: Produce the file
- Build the document with the `docx` skill: write a generation script, then run + validate + visually QA it inside a Task (per the docx skill's execution pattern). Save to `output/<dataflow-name>-<Business-and-Technical-Documentation|Fachliche-und-Technische-Dokumentation>.docx` using Heading 1/Heading 2 styles and a generated table of contents.
- All counts and totals must be computed in code, not by eye.

### Step 6: Verify delivery
- `Glob output/**/*` to confirm the file exists before announcing it.
- Tell the user the documentation is ready, in which language, and name the sections it covers.

## Output
A Word document in the chosen language that reproduces the blueprint structure
exactly — every query gets a profile and a column table, and the seven numbered
sections always appear in the same order with the same layouts; only the labels
and prose are localized.

## Guardrails
- **Structure fidelity is mandatory**: always reproduce the blueprint's sections, heading order, table layouts, and labels (in the chosen language). Do not drop, reorder, or improvise sections — only content and localized labels vary.
- **Identifiers are never translated**: dataflow/query/column names, data types, Query IDs/GUIDs, server/database/schema/object names, and relationship keys are copied verbatim from the JSON in both languages.
- **Never fabricate** extracted facts (Query IDs, column names/types, sources, relationships, counts). Missing → the localized "Not included in export" placeholder. Mark every functional inference with hedging language.
- **Never surface secrets**: document a connection/credential *reference*, never an embedded password, key, or token if one appears in the export.
- If the file can't be parsed after a second attempt (e.g. via a converter), say so honestly and ask for a re-export — do not substitute sample data.
- Confirm the output file exists in `output/` before telling the user it's ready.
