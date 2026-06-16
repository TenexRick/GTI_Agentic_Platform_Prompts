# Campaign Pivoting and Commonalities

**Tier:** Advanced template

## Description

Start from a seed indicator or campaign and expand outward: find related indicators, compute what they have in common, and surface the infrastructure and tooling that ties them together. Use it to grow a small lead into a fuller picture of a campaign.

## When to use it

- You have one or a few indicators and suspect they are part of a larger campaign.
- You want the agent to pivot across related IoCs and identify shared attributes (mutexes, infrastructure, certificates, behaviors).
- You are building or expanding an IoC collection for hunting or blocking.

## How this prompt is structured

This is a chained, multi-step prompt. It tells the agent to work in stages (anchor, pivot, compute commonalities, summarize) so each stage builds on the last. This mirrors Gemini's guidance to decompose complex work into sequential steps, and it maps to GTI's documented ability to execute queries and compute commonalities of IoCs.

## Variables

| Variable | Description |
| --- | --- |
| `${{seed_indicator}}` | The starting point: an IoC, an actor, or a campaign name to anchor the pivot. |
| `${{pivot_scope}}` | How far to expand, for example `infrastructure and files` or `network only`. Bounds the pivot. |
| `${{start_date}}` | Start of the activity window to consider, `YYYY-MM-DD`. |
| `${{end_date}}` | End of the activity window to consider, `YYYY-MM-DD`. |
| `${{max_indicators}}` | A cap on how many related indicators to return, for example `25`. Keeps output manageable. |

## Prompt body

```
<role>
You are a threat hunter in Google Threat Intelligence who expands campaigns from a seed indicator and identifies what ties related indicators together.
</role>

<task>
Starting from the seed below, expand to related indicators and compute their commonalities.
Seed: ${{seed_indicator}}
Pivot scope: ${{pivot_scope}}
Activity window: ${{start_date}} to ${{end_date}}
</task>

<instructions>
Work in stages and show your result for each:
1. Anchor: establish what the seed is and its key attributes.
2. Pivot: find related indicators within the pivot scope and the activity window. Cap the set at ${{max_indicators}} indicators.
3. Commonalities: identify attributes shared across the related set (shared infrastructure, mutexes, certificates, registrars, behaviors, ATT&CK techniques).
4. Cluster: state whether the related set appears to form one campaign or several, and why.
</instructions>

<constraints>
- Prioritize curated Google Threat Intelligence data and note where OSINT is used.
- Stay within the activity window and the pivot scope.
- Do not exceed ${{max_indicators}} related indicators. If more exist, return the most relevant and say so.
- Keep confirmed relationships separate from probable ones.
</constraints>

<output_format>
Return Markdown with these sections:
1. Anchor summary: what the seed is.
2. Related indicators: a table with columns Indicator, Type, Relationship, and Confidence.
3. Commonalities: a bulleted list of shared attributes across the set.
4. Cluster assessment: one or several campaigns, with reasoning.
5. Suggested next pivots: two or three follow-up moves.
</output_format>
```

## Expected output shape

A staged Markdown report: an anchor summary, a capped related-indicator table with relationship and confidence, a commonalities list, a cluster assessment, and suggested next pivots. In the Agentic interface, related indicators may be executable or openable in Google Threat Intelligence. GTI citations accompany the answer.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): threat hunter persona.
- **Task decomposition and chaining** (Gemini): explicit anchor, pivot, commonalities, cluster stages.
- **Aggregation** (Gemini): combines many related indicators into a single commonalities view.
- **Explicit constraints and bounds** (Gemini, GTI): activity window, pivot scope, indicator cap.
- **Explicit dates** (GTI): `${{start_date}}` and `${{end_date}}`.
- **Confidence separation** (GTI): confirmed versus probable relationships.
- **Parametrization** (GTI): `${{seed_indicator}}`, `${{pivot_scope}}`, `${{start_date}}`, `${{end_date}}`, `${{max_indicators}}`.

---

[Return to README.md](../README.md)
