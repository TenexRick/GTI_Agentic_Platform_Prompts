# Threat Actor Profile

**Tier:** Use case (intermediate)

## Description

Produce a structured profile of a named threat actor: who they are, who they target, the TTPs they use, and their recent activity. Use it to brief a team before an engagement, to enrich an attribution hypothesis, or to feed a hunt.

## When to use it

- You have an actor name or alias and need a fast, sourced profile.
- You are preparing a hunt or detection effort and want the actor's documented TTPs.
- You need a consistent actor write-up format your team can reuse.

## Variables

| Variable | Description |
| --- | --- |
| `${{threat_actor}}` | The actor name or alias, for example `APT44` or `FIN7`. |
| `${{start_date}}` | Start of the recent-activity window, `YYYY-MM-DD`. |
| `${{end_date}}` | End of the recent-activity window, `YYYY-MM-DD`. |
| `${{target_industry}}` | Optional focus industry to emphasize, for example `financial services`. Use `all` for no narrowing. |

## Prompt body

```
## Role
You are a senior threat intelligence analyst in Google Threat Intelligence who writes actor profiles for a SOC and its leadership.

## Task
Build a profile of the threat actor ${{threat_actor}}, emphasizing activity between ${{start_date}} and ${{end_date}}.
Where relevant, give extra weight to targeting of ${{target_industry}}.

## Constraints
- Use curated Google Threat Intelligence data as the primary basis and note where OSINT is used.
- Use the explicit date range above. Do not interpret "recent" on your own.
- Map TTPs to MITRE ATT&CK technique IDs where documented.
- Clearly separate confirmed attribution from suspected attribution.

## Output format
Return Markdown with these sections:
1. Overview: who the actor is, suspected origin or sponsor, and aliases.
2. Targeting: sectors, regions, and the relevance to ${{target_industry}}.
3. TTPs: a table with columns Tactic, Technique, ATT&CK ID, and Notes.
4. Recent activity: dated bullet points within the window, newest first.
5. Associated indicators and tooling: known malware families, tools, and notable IoCs.
6. Analyst assessment: three to four sentences on what this means for a defender.
```

## Expected output shape

A six-section Markdown profile, with a TTP table mapped to ATT&CK and a dated recent-activity list inside the requested window. Confirmed and suspected attribution are kept separate. GTI citations accompany the answer.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): senior intel analyst writing for SOC plus leadership.
- **Task decomposition** (Gemini): overview, targeting, TTPs, activity, indicators, assessment.
- **Explicit date constraints** (GTI): `${{start_date}}` and `${{end_date}}` replace "recent."
- **Output format control** (Gemini, GTI): mixed sections plus a structured TTP table.
- **Source keyword steering** (GTI): curated-first with OSINT noted.
- **Hallucination guardrail** (GTI): confirmed vs suspected attribution must be separated.
- **Parametrization** (GTI): `${{threat_actor}}`, `${{start_date}}`, `${{end_date}}`, `${{target_industry}}`.

---

[Return to README.md](../README.md)
