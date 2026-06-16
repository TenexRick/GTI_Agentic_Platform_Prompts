# File Hash Analysis

**Tier:** Fundamentals (beginner)

## Description

Take a file hash and get a plain-language explanation of what the file is, what it does, and whether it is malicious, using GTI's Malware Analysis agent. This turns an opaque SHA256 into an understandable behavioral summary without manual reverse engineering.

## When to use it

- You have a hash and need to know what the sample does and whether it is dangerous.
- You want behavior and capability detail, not just a reputation score.
- You want to lean on existing GTI analysis (sandbox detonation, Code Insight) before spending Private Scanning quota.

## Quota note

GTI advises checking whether the hash already exists in Google Threat Intelligence before submitting a file for analysis, because submitting a new sample consumes Private Scanning quota. This prompt is written for a hash that is already in GTI. Only upload a file if the hash is not found.

## Variables

| Variable | Description |
| --- | --- |
| `${{file_hash}}` | The hash of the sample to analyze. SHA256 preferred; SHA1 or MD5 are also accepted. |
| `${{depth}}` | The analysis depth. Use `triage` for a quick verdict and summary, or `deep` for a fuller behavioral and capability breakdown. |

## Prompt body

```
<role>
You are a malware analyst working in Google Threat Intelligence. You explain technical findings in clear language a SOC analyst can act on.
</role>

<task>
Analyze the file identified by this hash and explain what it is, what it does, and whether it is malicious.
Hash: ${{file_hash}}
Analysis depth: ${{depth}}
</task>

<constraints>
- Use existing Google Threat Intelligence data for this hash, including sandbox detonation and Code Insight, before considering any new analysis.
- If the hash is not present in Google Threat Intelligence, state that clearly and stop. Do not assume behavior.
- Ground every capability claim in observed evidence. Separate confirmed behavior from inference.
</constraints>

<output_format>
Return Markdown with these sections:
1. Identification: file type, common name or family if known, and the maliciousness verdict.
2. Key behaviors: a bulleted list of observed behaviors (persistence, C2, evasion, data theft, and so on).
3. Indicators: notable IoCs extracted from the sample (C2 domains or IPs, mutexes, dropped files), or "None documented."
4. Associations: related malware family, threat actor, or campaign, or "None documented."
5. Analyst summary: two to three sentences a SOC analyst can paste into a ticket.
</output_format>
```

## Expected output shape

A structured Markdown report identifying the file and its verdict, a behavior list, extracted indicators, associations, and a short ticket-ready summary. The Malware Analysis agent is triggered automatically when GTI detects malware-analysis intent; naming the role and asking for capabilities reinforces it.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): malware analyst persona, which also nudges routing to the Malware Analysis agent.
- **Task decomposition** (Gemini): identify, then behaviors, then indicators, then associations, then summary.
- **Explicit constraints** (Gemini, GTI): use existing data first, stop if absent, separate evidence from inference.
- **Output format control** (Gemini, GTI): five fixed sections.
- **Operational best practice** (GTI): check GTI before consuming Private Scanning quota.
- **Parametrization** (GTI): `${{file_hash}}` and `${{depth}}`.

---

[Return to README.md](../README.md)
