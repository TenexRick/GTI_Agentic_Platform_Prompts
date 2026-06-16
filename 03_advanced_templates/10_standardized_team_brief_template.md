# Standardized Team Brief Template (Capstone)

**Tier:** Advanced template

## Description

This is the capstone. It is a single, fully structured template that produces an executive threat brief and demonstrates every prompt design pattern in the repo stacked together. It is built to be saved as a shared GTI prompt so an entire team produces briefs in one consistent voice and format. Change the variables, not the structure.

## When to use it

- You need a leadership-ready threat brief on an actor, campaign, malware, or vulnerability.
- You want every analyst on the team to produce the same brief format from the same prompt.
- You want a reference example of a complete, well-structured Gemini-style prompt applied to GTI.

## Why this is the capstone

It uses the full Gemini 3 structured template (role, instructions, constraints, context, task, output format) with consistent delimiters, a plan-execute-validate instruction block, explicit dates, source steering, a few-shot tone anchor, and a hallucination guardrail. It is also the clearest demonstration of GTI's "parametrize your prompts" guidance: once saved, the only thing a user touches is the variable form.

## Variables

| Variable | Description |
| --- | --- |
| `${{subject}}` | The brief's subject: a threat actor, campaign, malware family, or CVE. |
| `${{subject_type}}` | One of `threat actor`, `campaign`, `malware family`, or `vulnerability`. Tells the agent how to frame the brief. |
| `${{audience}}` | The reader, for example `executive leadership` or `the CISO`. Sets tone and depth. |
| `${{start_date}}` | Start of the activity window, `YYYY-MM-DD`. |
| `${{end_date}}` | End of the activity window, `YYYY-MM-DD`. |
| `${{org_context}}` | One line on the reader's organization or exposure, for example `a US regional hospital network`. Focuses the so-what. |
| `${{length}}` | Target length, for example `one page` or `400 words`. Controls verbosity, which Gemini 3 keeps low unless told otherwise. |

## Prompt body

```
<role>
You are a senior threat intelligence analyst at TENEX.AI delivering briefings to ${{audience}}.
You write with precision and lead with the business impact.
</role>

<instructions>
1. Plan: identify what ${{audience}} needs to know about this ${{subject_type}} to make a decision.
2. Execute: gather the relevant intelligence on the subject within the activity window.
3. Validate: check that every claim is supported by a Google Threat Intelligence source before including it.
4. Format: deliver the brief in the exact structure below, at the target length.
</instructions>

<constraints>
- Prioritize curated Google Threat Intelligence data. Note where OSINT or Google Search is used.
- Use the explicit activity window ${{start_date}} to ${{end_date}}. Do not use "latest" or "recent."
- Lead with impact to the reader. Keep jargon out of the executive summary.
- Separate confirmed facts from assessments. Flag any low-confidence claim.
- Target length: ${{length}}. Be concise; expand only where impact justifies it.
</constraints>

<context>
Subject: ${{subject}}
Subject type: ${{subject_type}}
Reader organization and exposure: ${{org_context}}
</context>

<task>
Based on the context above, write an executive threat brief on ${{subject}}.
</task>

<tone_anchor>
Executive summary sentences should read like this example:
"Group X has actively targeted regional healthcare providers in the last 30 days, and an unpatched VPN appliance in our environment is the most likely entry point."
</tone_anchor>

<output_format>
Return Markdown with these sections:
1. Executive summary: three to four sentences. Impact first, no jargon.
2. What we know: the key facts about the subject, as bullets, within the window.
3. Why it matters to us: relevance tied to ${{org_context}}.
4. Recommended actions: prioritized, owner-ready steps.
5. Confidence and sources: one line on overall confidence and the curated-versus-OSINT balance.
</output_format>

<final_instruction>
Based on the information above, produce the brief. Think carefully before writing, then return only the brief in the specified format.
</final_instruction>
```

## Expected output shape

A concise, leadership-ready Markdown brief at the requested length: an impact-first executive summary, a dated facts section, an organization-specific relevance section, prioritized recommended actions, and a confidence-and-sources line. GTI citations accompany the answer. To export, ask Agentic to "save into a report" for a copyable Markdown widget.

## How to operationalize it

1. Fill the role and tone anchor once for your team and agree on the section structure.
2. Save it in GTI (Prompts, then + Create prompt) so the variable form is all anyone fills in.
3. Reuse it for every executive brief so output stays consistent across analysts and shifts.
4. Fork the resulting conversation to share an in-progress brief with a colleague.

## Patterns demonstrated

This template is the full stack:

- **Role assignment** (Gemini, GTI): TENEX.AI senior analyst persona.
- **Plan, execute, validate instructions** (Gemini 3): the `<instructions>` block enforces a reasoning workflow.
- **Explicit constraints** (Gemini, GTI): data source, dates, tone, confidence separation, length.
- **Context-first ordering with an anchor phrase** (Gemini 3): context precedes the task; "Based on the information above" bridges to the request.
- **Few-shot tone anchor** (Gemini): the `<tone_anchor>` fixes the executive voice.
- **Output format control** (Gemini, GTI): five fixed sections.
- **Verbosity control** (Gemini 3): `${{length}}` keeps the brief tight.
- **Hallucination guardrail** (GTI): validate-before-include and confidence flags.
- **Explicit dates and source steering** (GTI): activity window and curated-versus-OSINT balance.
- **Parametrization for reuse** (GTI): seven variables, designed to be saved as a shared team prompt.
