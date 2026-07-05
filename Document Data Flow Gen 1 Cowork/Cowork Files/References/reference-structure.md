# Reference Document Structure (authoritative)

The output `.docx` must match `reference-document.docx` **exactly** — same sections,
same order, same tables, same column headers. Only the *content* changes with each
input JSON. Reproduce this layout verbatim.

The input is a **Power BI dataflow export** (`model.json`-style). Key fields:
`name`, `description`, `version`, `culture`, `modifiedTime`,
`pbi:mashup.queriesMetadata` (per-query `queryId`, `queryName`, `loadEnabled`),
`pbi:mashup.document` (the M / Power Query source code), and `entities[]` — each with
`name`, `description`, `pbi:refreshPolicy` (`$type`, `location`), and `attributes[]`
(`name` + `dataType`).

## Provenance rule (critical)
Two kinds of content appear in the reference doc:
- **Extracted** — taken directly from the JSON. State as fact.
- **Inferred** — business meaning, keys, relationships, KPIs, governance advice that
  the JSON does NOT contain. The reference doc always hedges these with
  **"Presumably…"**, **"Business-inferable:"**, **"recommended"**, or
  **"Not included in export"**. Preserve that hedging language. Never present an
  inferred fact as certain, and never invent values that aren't derivable.

---

## Title block
1. **Title** (Title style): `{name} – Business and Technical Documentation`
2. Line (subtitle/italic): `Created from the provided Power BI dataflow`
3. **Metadata table — 4×2**, no header row, label | value:
   | Creation date | {today, e.g. "July 5, 2026"} |
   | Export file | {input JSON filename} |
   | Dataflow version | {version} |
   | Last modified per export | {modifiedTime → "YYYY-MM-DD HH:MM:SS UTC" + raw ISO in parens} |

## Table of Contents  (Heading 1: "Table of Contents")
A contents listing of sections 1–7. (A Word TOC field or a simple list.)

## 1. System Overview  (Heading 1: "1. System Overview")
- 3 intro paragraphs: (a) name/version/culture/last-modified sentence; (b) the
  business areas the queries cover; (c) how the queries load (Power Query from CSV,
  `Csv.Document` over `Web.Contents`, header promotion, typing).
- **Overview table — 8×2**, label | value:
  Dataflow name · Description · Version · Culture · Last modified per export ·
  Number of queries · Number of entities · Explicit relationships in the export.

## 2. Source Systems  (Heading 1: "2. Source Systems")
- 1 intro paragraph naming the source system(s) (derive host/URL from the M
  `Web.Contents(...)` calls in `pbi:mashup.document`).
- **Source table — (1+N)×5**, header row + one row per query:
  `Source system | Server | Database | Schema | Source object / Used in`.
  Database/Schema are typically "Not applicable" for CSV sources; Source object =
  the `.csv` file (decode `refreshPolicy.location`).

## 3. Table Catalog  (Heading 1: "3. Table Catalog")
- 1 intro paragraph (profile per query, in export order; note Query ID + Load
  enabled are shown).
- **For each query, in `queriesMetadata` order**, emit:
  1. **Heading 2** = query name.
  2. **Classification table — 15×2**, fixed left-column labels in this order:
     `Classification` · `Description` · `Query ID` · `Type` · `Load enabled` ·
     `Source system` · `Source object` · `Granularity` · `Primary key` ·
     `Key foreign keys` · `Business purpose` · `Use for business users` ·
     `Special logic` · `Usage notes` · `Refresh per export`.
     - Extracted: Query ID, Load enabled (On/Off), Source system, Source object,
       Description (or "Not included in export"), Refresh per export (from
       `refreshPolicy.$type`).
     - Inferred (hedge): Classification, Type (Fact/Dimension/Lookup), Granularity,
       Primary key, Key foreign keys, Business purpose, Use for business users,
       Special logic, Usage notes.
  3. Paragraph: **"Technical Structure"**.
  4. **Column table — (1+N)×2**, header `Column | Data type`; one row per
     `attributes[]` entry (`name`, `dataType`) — extracted verbatim.

## 4. Core Business Logic  (Heading 1: "4. Core Business Logic")
- 1 intro paragraph.
- Bulleted list (List Paragraph) of business-relevant transformations that are
  **explicitly visible in the M `document`** (CSV load, header promotion, explicit
  typing, identical yearly structures, absence of unpivot/filter/dedup/derived
  columns, etc.). Only list what the M actually shows.

## 5. Recommended Relationships  (Heading 1: "5. Recommended Relationships")
- 1 intro paragraph (note the export defines no relationships if none present).
- Sub-caption "Relationships explicitly defined in the export" + **table 3-col**
  `Relationship | Status | Rationale` (often a single "Not included in export" row).
- Sub-caption "Additionally recommended relationships" + **table 3-col**
  `Relationship | Status | Rationale`; every row Status = "recommended" (inferred).

## 6. Key Figures  (Heading 1: "6. Key Figures")
- 1 intro paragraph (KPIs are not in the export; derived from fact/dimension fields).
- **Table 3-col**: `Key figure | Technical reference | Business meaning`. Technical
  reference cites columns/tables (e.g. `SUM(OrderQuantity)`); all inferred.

## 7. Data Governance Notes  (Heading 1: "7. Data Governance Notes")
- 1 intro paragraph.
- Bulleted list of recommendations (data owner, source dependency, personal data,
  refresh window, data-quality rules, glossary, formalize relationships, validate
  KPIs, clarify ambiguous queries, consider consolidation).
- Final paragraph beginning **"Quality check:"** confirming every query in
  `queriesMetadata` was documented and Query IDs / Load-enabled flags were carried.

---

## Fidelity checklist (verify before delivering)
- [ ] One Heading-2 block per query in `queriesMetadata`, in export order.
- [ ] Every query has BOTH a 15-row Classification table and a Column/Data-type table.
- [ ] Column tables list every attribute with its exact `dataType`.
- [ ] All 7 numbered Heading-1 sections present, in order, plus Title + TOC.
- [ ] Inferred content is hedged ("Presumably", "recommended", "Business-inferable",
      "Not included in export") — never asserted as certain.
- [ ] "Quality check:" closing line lists the documented-query count.
