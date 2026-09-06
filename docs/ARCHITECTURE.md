# Architecture Notes

## Processing pipeline

1. The researcher submits a PDF or text.
2. `pdf.js` extracts PDF text while preserving page identity.
3. The application estimates whether the document can be analyzed directly.
4. Longer documents are split dynamically into page-aware chunks.
5. Claude extracts structured findings from each chunk.
6. A final Claude pass consolidates the partial findings.
7. Every proposed quotation is checked programmatically against the indicated page.
8. The researcher reviews the structured record.
9. Quotations are revalidated immediately before persistence.
10. Claude operates Notion through MCP after explicit human approval.
11. Saved records become part of the searchable research library.

## Independent capability fallbacks

The application deliberately separates three different runtime decisions.

### Model capability

Preferred model: `claude-sonnet-5`

A legacy model is considered only when the API explicitly reports that the primary model itself is unavailable or unsupported. Rate limits, server failures, authentication errors, network failures, and unrelated feature errors never trigger a model downgrade.

### Structured extraction capability

Preferred order:

1. JSON Outputs
2. Strict Tool Use
3. standard forced tool use

Each level only falls back when that specific feature is explicitly unsupported.

### MCP capability

Preferred order:

1. current public MCP Connector format;
2. constrained-runtime compatibility format.

Model fallback and MCP-format fallback are independent axes.

## Quotation verification

The verifier is deterministic and does not ask the model whether its own quotation is correct.

A quotation is normalized only for extraction artifacts such as:

- whitespace;
- line wrapping;
- line-break hyphenation;
- Unicode smart quotes;
- Unicode dash variants.

The normalized quotation must still exist in the normalized source-page text.

A semantic match or paraphrase is intentionally insufficient.

## Failure policy

The project favors visible failure over hidden degradation.

Examples:

- a failed chunk stops consolidation after one retry;
- a PDF exceeding the configured page limit is rejected rather than truncated;
- a tool-level MCP error invalidates the whole operation;
- transient API errors do not masquerade as capability incompatibility.
