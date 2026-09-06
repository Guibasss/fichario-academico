# Security and Deployment Notes

## Current Artifact environment

The original project was built as a Claude.ai Artifact. That environment has different security and networking constraints from a normal production web application.

## Secrets

Do not commit:

- Anthropic API keys;
- Gemini API keys;
- Notion tokens;
- private Notion page/database IDs when they expose personal workspace structure;
- runtime credentials;
- `.env` files.

The repository contains `.env.example` only as deployment documentation.

## Production architecture

A production deployment should use a backend or serverless API layer so provider credentials never reach the browser.

Recommended shape:

```text
Browser
  ↓
Backend / serverless API
  ├─ Claude API
  ├─ MCP Connector
  └─ optional external providers
```

## MCP

The application maintains a distinction between:

- the documented public MCP Connector request shape;
- any compatibility path required by a constrained runtime.

Compatibility behavior should not be described as the public API specification.

## User-provided documents

Academic PDFs should be treated as untrusted input. Extraction and AI analysis should not grant the document itself authority to execute unrelated actions.

The research extraction stage and persistence stage are intentionally separated by human review.
