---
id: infra.rag.retrieve.v1
version: 2026-10-19
title: Infra — RAG Retrieval (v1)
author: team-a@example.com
purpose: "Given a user query, retrieve top-k documents and return a concise context for generation. Intended for RAG pipelines."
inputs:
  - name: query
    required: true
    type: string
  - name: k
    required: false
    type: integer
    default: 5
outputs:
  schema:
    type: object
    properties:
      retrieved_ids:
        type: array
        items: { type: string }
      snippets:
        type: array
        items: { type: string }
safety_constraints:
  - "Do not return raw PII in snippets; redact according to privacy policy."
  - "If no relevant documents, return an explicit empty result and a suggested clarification question."
examples:
  - input:
      query: "How to rotate database credentials for service X?"
    expected_output:
      retrieved_ids: ["doc-123","doc-987"]
      snippets: ["Rotate creds by...","Use vault to..."]
---

Prompt (for retrieval assistant/adapter):

"""
You are the retrieval assistant. Given a short user query, return a ranked list of at most {{k}} document IDs and short location-limited snippets (max 200 characters each) that directly answer the query. Do not invent facts. If a snippet contains PII, redact it by replacing with `<REDACTED>` and mark the snippet as redacted.

Format response as JSON matching the declared schema.
"""

# Notes
- This template is retrieval-only; generation must happen in the RAG generation step.
- The execution harness MUST validate JSON schema before passing snippets to model prompt.
