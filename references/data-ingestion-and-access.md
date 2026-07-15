# Data Ingestion And Access

## Local Tabular Files

Use a dataframe engine for repeated data work. Prefer pandas, Polars, DuckDB, R data.table/tidyverse, or an equivalent available engine.

### Excel

After `01_BUSINESS_CONTEXT.md` approval:

1. Inspect workbook metadata once: file size, modification time, sheet names, approximate dimensions, and headers.
2. Ask the user which sheet is relevant when names or roles are ambiguous.
3. Build a full-column inventory before selecting analysis fields: column name, inferred type, missingness, unique count, and 3-10 representative non-sensitive sample values.
4. Tag each column or column family as candidate signal, text-to-scan, post-decision/leakage, outcome/proxy outcome, identifier/join key, exclude, or investigate.
5. For text-to-scan fields, profile semantic and structural patterns: common tokens, keyword families, script/language patterns, length buckets, mixed scripts, special characters, punctuation, parentheses/brackets, casing, digit ratio, prefixes/suffixes, and obvious business terms. Do not dismiss business-named text fields as mere identifiers until reviewed.
6. For categorical fields with small cardinality, inspect value counts and, after the target is approved, value-level target or outcome rates before reducing the field to a binary indicator. Use training data only for target-dependent profiling.
7. Ask focused questions for fields tagged investigate when their meaning, population timing, or operational availability could change the result.
8. Load approved sheets and decision-relevant columns into a dataframe for deeper analysis after the inventory is recorded.
9. Normalize types once, especially dates, identifiers, booleans, categorical fields, and text fields used for pattern scans.
10. After date parsing, check plausible year ranges, null changes, and whether numeric counts were accidentally converted to epoch-like dates.
11. Write a cache under `data/interim/`, preferably Parquet.
12. Use Feather or pickle only if Parquet is unavailable. Record the portability limitation.
13. Reuse the cached dataframe for profiling, preparation, modeling, and review.

Do not repeatedly open Excel, read cells one at a time, or reload the full workbook for each analytical question.

### Cache Identity

Define cache identity from:

- Source path and source size.
- Source modification time or content hash.
- Sheet name.
- Selected columns.
- Parsing and type-normalization version.
- Material transformation version when caching prepared data.
- Inventory version when column tags or text-scan rules change.

Invalidate the cache if any identity component changes or required columns are absent.

Record a machine-readable metadata file beside the cache when practical:

```json
{
  "source": "path/to/source.xlsx",
  "sheet": "approved_sheet",
  "source_fingerprint": "...",
  "engine": "pandas",
  "columns": ["..."],
  "rows": 0,
  "cache": "data/interim/source.parquet",
  "created_at": "..."
}
```

### CSV And TSV

- Load small files directly into a dataframe.
- Specify delimiter, encoding, identifier types, null tokens, and date parsing when inference is unreliable.
- Cache expensive normalization, joins, or repeated reads.
- Never allow numeric inference to remove leading zeros from identifiers without confirmation.

## Data Understanding Boundary

During Data Understanding, inspect enough data to establish schema, semantics, timing, quality, grain, and fitness. Do not construct the final feature set or optimize a model before `02_DATA_UNDERSTANDING.md` approval.

Store broad profiling outputs in `outputs/diagnostics/`. Keep only material findings in the gated document.

## Optional MCP Or Warehouse Access

Treat every connector as a replaceable extension. Do not assume a specific warehouse.

When data access tools are available:

1. Search approved schemas, metric definitions, and verified prior queries.
2. Confirm grain, joins, filters, date logic, and decision-time semantics.
3. Run inexpensive metadata and row-count probes first.
4. Save queries under `code/sql/` or the equivalent query folder.
5. Record query owner, execution ID, extraction time, row count, and source snapshot when available.
6. Materialize the extraction into a dataframe-friendly local format for reproducible analysis.
7. Do not run expensive, sensitive, or high-risk extraction without required approval.

Never treat a prior query as correct merely because it executed successfully. Verify that its business definitions match the current signed-off artifacts.

## SQL Review Gate For Expert Strict

```markdown
## SQL Review Gate

- Query and purpose:
- Sources and grain:
- Joins, filters, and date logic:
- Estimated cost and sensitivity:
- Reviewer:
- Approved to execute: Yes / No
- Notes:
```

## Provenance Minimum

Every analysis-ready dataset must identify its source, extraction or file timestamp, grain, filters, selected fields, transformation code, row count, cache path, and sensitivity classification.
