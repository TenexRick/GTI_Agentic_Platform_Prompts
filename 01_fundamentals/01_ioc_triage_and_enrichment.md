# IoC Triage and Enrichment

**Tier:** Fundamentals (beginner)

## Description

Take a single indicator of compromise (IP, domain, URL, or file hash) and return a fast, sourced verdict: is it malicious, what is its reputation, and what is it associated with. This is the everyday first move when an alert hands you one indicator and you need a decision.

## When to use it

- An alert or ticket gives you one IoC and you need a maliciousness call before you escalate or close.
- You want reputation, the GTI score, and known associations (threat actors, malware, campaigns) in one pass.
- You want the answer grounded in GTI's curated data, not generic web opinion.

## Variables

| Variable | Description |
| --- | --- |
| `${{ioc}}` | The single indicator to triage. An IP address, domain, URL, or file hash (SHA256, SHA1, or MD5). |
| `${{data_source}}` | Which GTI data to prioritize. Use `curated` for vetted intel or `osint` to include filtered open-source context. |

## Prompt body

```
<role>
You are a senior CTI analyst performing first-pass indicator triage in Google Threat Intelligence.
</role>

<task>
Triage the following indicator and return a maliciousness verdict with supporting evidence.
Indicator: ${{ioc}}
</task>

<constraints>
- Prioritize ${{data_source}} Google Threat Intelligence data.
- Base the verdict on the GTI score, antivirus detections, and documented associations.
- Do not speculate. If GTI has no data on the indicator, say so explicitly.
- If you are uncertain, state your confidence level and what additional context would resolve it.
</constraints>

<output_format>
Return Markdown with these sections, in this order:
1. Verdict: one of MALICIOUS, SUSPICIOUS, BENIGN, or UNKNOWN, with a one-line justification.
2. Reputation: the GTI score and the antivirus detection summary.
3. Associations: any related threat actors, malware families, or campaigns, as a bulleted list. Write "None documented" if empty.
4. Recommended next step: one sentence.
</output_format>
```

## Expected output shape

A short Markdown report: a one-word verdict with justification, a reputation line with the GTI score and AV summary, a bulleted associations list, and a single recommended next step. Inline GTI citations will accompany the answer in the Agentic interface.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): casts the agent as a CTI analyst.
- **Explicit constraints** (Gemini, GTI): defines data source, what to ignore, and how to handle missing data.
- **Response format control** (Gemini, GTI): fixes the four output sections.
- **Source keyword steering** (GTI): `curated` vs `osint` controls the data the agent draws on.
- **Hallucination guardrail** (GTI): requires the agent to state when GTI has no data and to express confidence.
- **Parametrization** (GTI): `${{ioc}}` and `${{data_source}}`.
