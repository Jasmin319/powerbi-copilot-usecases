# Documentation Blueprint (authoritative structure)

Every documentation deliverable this skill produces MUST follow this exact
skeleton, in this order, in the **chosen output language** (German or English),
in factual reference style. The table names, query names, column names, GUIDs
and prose in `references/example-documentation.docx` are **case-specific to one
dataflow** — do not reuse them. Reuse only the **structure**: the same sections,
heading order, table layouts, and column/row labels (translated per the language
map below).

A gold-standard rendered example (German) lives at
`references/example-documentation.docx` — open it to match tone, heading styles,
and table formatting. Treat its *structure* as the contract and its *content* as
illustrative only.

## Output language
- **The structure is identical in every language.** Only the labels (section
  titles, table headers, fixed row labels) and the generated prose change.
- **Choosing the language:** if the user names one (e.g. "in English",
  "auf Englisch", "in German", "auf Deutsch"), use it. If the user does not
  specify, default to **German**. State the language used in the final message.
- **Never translate identifiers** — these are copied verbatim from the JSON in
  any language: dataflow name, query names, column names, data types
  (`int64`, `string`, `boolean`, `date`, `decimal`, `double`), GUIDs / Query IDs,
  server / database / schema / source-object names, and relationship attribute
  names. Translating an identifier breaks traceability to the model.

### Language label map (German ↔ English)
Use the column matching the chosen language for every label below.

| Role | German (default) | English |
|------|------------------|---------|
| Subtitle | Erstellt auf Basis des bereitgestellten Power BI Dataflows | Created from the provided Power BI dataflow |
| Title suffix | – Fachliche und Technische Dokumentation | – Business and Technical Documentation |
| Metadata: created | Erstellungsdatum | Creation date |
| Metadata: export file | Exportdatei | Export file |
| Metadata: version | Dataflow-Version | Dataflow version |
| Metadata: last change | Letzte Änderung laut Export | Last modified per export |
| TOC heading | Inhaltsverzeichnis | Table of Contents |
| Section 1 | 1. Systemübersicht | 1. System Overview |
| Section 2 | 2. Quellsysteme | 2. Source Systems |
| Section 3 | 3. Tabellenkatalog | 3. Table Catalog |
| Section 4 | 4. Zentrale Geschäftslogik | 4. Core Business Logic |
| Section 5 | 5. Empfohlene Beziehungen | 5. Recommended Relationships |
| Section 6 | 6. Wichtige Kennzahlen | 6. Key Figures |
| Section 7 | 7. Data-Governance-Hinweise | 7. Data Governance Notes |
| Property-table header | Eigenschaft \| Definition | Property \| Definition |
| Column-table header | Spalte \| Datentyp | Column \| Data type |
| "Technical structure" label | Technische Struktur | Technical Structure |
| Sources-table header | Quellsystem \| Server \| Datenbank \| Schema \| Quellobjekt / Verwendet in | Source system \| Server \| Database \| Schema \| Source object / Used in |
| "Used in" prefix | Verwendet in: | Used in: |
| Relationship-table header | Beziehung \| Status \| Begründung | Relationship \| Status \| Rationale |
| Relationship subhead (explicit) | Explizit im Export vorhandene Beziehungen | Relationships explicitly defined in the export |
| Relationship subhead (recommended) | Zusätzlich fachlich empfohlene Beziehungen | Additionally recommended relationships |
| Status value (explicit) | explizit im Export | explicit in export |
| Status value (recommended) | empfohlen | recommended |
| Key-figures header | Kennzahl \| Technischer Bezug \| Fachliche Aussage | Key figure \| Technical reference \| Business meaning |
| Missing-field placeholder | Nicht im Export enthalten | Not included in export |
| Load-enabled true / false | Ein / Aus | On / Off |
| Type: fact / dimension / helper | Faktentabelle / Dimensionstabelle / Hilfstabelle | Fact table / Dimension table / Helper table |

**Steckbrief (per-query) row labels** — German ↔ English, same order:
`Einordnung`=Classification, `Beschreibung`=Description, `Query ID`=Query ID,
`Typ`=Type, `Load enabled`=Load enabled, `Quellsystem`=Source system,
`Quellobjekt`=Source object, `Granularität`=Granularity,
`Primärschlüssel`=Primary key, `Wichtige Fremdschlüssel`=Key foreign keys,
`Fachlicher Zweck`=Business purpose,
`Verwendung für Fachanwender`=Use for business users,
`Besondere Logik`=Special logic, `Hinweise zur Verwendung`=Usage notes,
`Refresh im Export`=Refresh per export.

## Fixed vs. derived content
- **Hard facts — copy verbatim from the JSON, never invent or translate**:
  dataflow name, version, culture, modified timestamp, per-query `Query ID`,
  `loadEnabled`, column names + data types, source server/database/schema/object,
  the `relationships` entries, and all counts.
- **Free-text prose facts — translate into the chosen language**: the dataflow's
  `description` field (and any per-entity `description`) is author-written prose,
  not an identifier, so render it in the output language. It is still a *fact*
  (do not embellish it); just translate it. Optionally quote the original in
  parentheses on first use.
- **Functional interpretation — written in the chosen language, must be hedged**:
  business purpose, granularity, primary/foreign-key guesses, KPI derivations,
  recommended relationships. Hedge them — German: "fachlich vermutlich",
  "fachlich ableitbar"; English: "presumably", "business-inferable". Never
  present an inference as an extracted fact.
- If a field is absent in the export, write the placeholder from the language map
  ("Nicht im Export enthalten" / "Not included in export") — do not guess.

## Heading styles
- Section titles `1.`–`7.` and the TOC heading → **Heading 1**.
- Each query steckbrief title in section 3 → **Heading 2** (the query name verbatim).
- The "Technical structure" label → normal paragraph above the column table.

---

## Title block (before section 1)
1. Title paragraph: `<Dataflow-Name>` + the localized title suffix.
2. Subtitle paragraph (localized).
3. Metadata table — 2 columns, no header row, localized labels:
   created date (today, DD.MM.YYYY for DE / YYYY-MM-DD or Month D, YYYY for EN),
   export file (json filename), dataflow version, last-modified timestamp.

## Inhaltsverzeichnis / Table of Contents (Heading 1)
A table of contents listing sections 1–7. (Generated/updated by the docx skill.)

## Section 1 — System Overview (Heading 1)
- 2–3 narrative paragraphs: (a) purpose + description + version + culture +
  modified timestamp; (b) the functional scope, naming the business areas the
  entities cover; (c) how data is loaded (Power Query from SQL, plus any tables
  generated inside the dataflow such as a calendar).
- Property table (localized header), rows in this order: dataflow name,
  description, version, culture, modified timestamp, number of queries, number of
  entities, number of explicit relationships in the export.

## Section 2 — Source Systems (Heading 1)
- Narrative paragraph identifying the source system(s), server, database, schema,
  and any tables generated directly in Power Query.
- Sources table (localized header). One row per source object; in the last column
  put the object on the first line and the localized "Used in: <query name>"
  beneath it. Tables generated in Power Query (e.g. calendar) are marked as
  generated rather than given a SQL object.

## Section 3 — Table Catalog (Heading 1)
- Intro sentence: a steckbrief per query from `queriesMetadata`; Query ID and
  loadEnabled taken exactly from the JSON.
- **For every query, in JSON order**, emit this block:
  1. **Heading 2** = the query name verbatim.
  2. Property table (localized header + localized row labels) in this order:
     Classification, Description, Query ID, Type, Load enabled, Source system,
     Source object, Granularity, Primary key, Key foreign keys, Business purpose,
     Use for business users, Special logic, Usage notes, Refresh per export.
     - Load enabled renders On/Off (Ein/Aus) from `loadEnabled` true/false.
     - Type heuristic: Fact table for `Fact_*`, Dimension table for `Lookup_*`,
       Helper table for `ref*`/helper/generated tables; otherwise pick the
       closest fit from the M logic and hedge it.
     - Special logic summarizes the applied Power Query steps in plain prose
       (column selection, renames to `Key_…`, explicit type casting, null→0
       replacement, derived columns, date-format unification, etc.).
  3. The localized "Technical structure" paragraph label.
  4. Column table (localized header). One row per column with its data type
     exactly as in the JSON.

## Section 4 — Core Business Logic (Heading 1)
- Intro: the points below describe business-relevant transformations explicitly
  visible in the dataflow's Power Query code.
- Bulleted list — one bullet each, naming the affected query and what it does
  (filters, key construction, fallbacks to refresh date, dynamically generated
  calendar, deduplication, date-format unification, removal of complex types,
  etc.). Only include logic actually present in the M code.

## Section 5 — Recommended Relationships (Heading 1)
- Intro paragraph: the export already defines relationships explicitly; additional
  functionally sensible relationships are recommended where derivable from key
  columns but not present in the export.
- Localized subhead (explicit) + relationship table. One row per entry in the
  JSON `relationships`; status = explicit; rationale notes it is stored in the
  JSON under `relationships`. Format each as `Tabelle.Spalte → Tabelle.Spalte`.
- Localized subhead (recommended) + relationship table. Status = recommended;
  rationale gives the functional reason. Include only relationships not already
  in the export.

## Section 6 — Key Figures (Heading 1)
- Intro: measure definitions are not contained in the export; the derivation is
  based on table and column names.
- Key-figures table (localized header). One row per KPI or KPI group derived from
  fact/reference columns, with the technical column reference and a short business
  statement.

## Section 7 — Data Governance Notes (Heading 1)
- Intro: the following are recommendations for stable operation and functional
  maintenance.
- Bulleted list of governance recommendations (data owners, refresh frequency /
  load window, data-quality rules, business glossary, descriptions in the
  semantic model, transparency of fallback logic, formal review of recommended
  relationships, validation of derived fields).
- Closing quality-check paragraph with the actual query count `<N>` (computed in
  code):
  - German: `Qualitätsprüfung: Alle <N> Queries aus queriesMetadata wurden mit
    eigenem Abschnitt dokumentiert, Query IDs sowie loadEnabled-Werte wurden aus
    dem Export übernommen, und technische Unsicherheiten wurden explizit
    gekennzeichnet.`
  - English: `Quality check: All <N> queries from queriesMetadata were documented
    in their own section, Query IDs and loadEnabled values were taken from the
    export, and technical uncertainties were marked explicitly.`
