# Threat Landscape Summary

**Tier:** Use case (intermediate)

## Purpose

Produce a time-bounded rollup of threat activity relevant to a sector, region, or both. Use it for a daily or weekly intel summary, a shift handoff, or a quick read on what changed in a defined window.

## When to use it

- You need a recurring summary of what happened over a defined period.
- You want activity filtered to a sector or region, not the whole firehose.
- You want a consistent, dated digest you can run on a schedule.

## Variables

| Variable | Description |
| --- | --- |
| `${{sector}}` | The industry to focus on, for example `healthcare`. Use `all` for no sector filter. |
| `${{region}}` | The geography to focus on, for example `European Union`. Use `global` for no region filter. |
| `${{start_date}}` | Start of the reporting window, `YYYY-MM-DD`. |
| `${{end_date}}` | End of the reporting window, `YYYY-MM-DD`. |

## Prompt body

```
<role>
You are a threat intelligence analyst in Google Threat Intelligence producing a periodic landscape briefing.
</role>

<task>
Summarize notable threat activity affecting ${{sector}} in ${{region}} between ${{start_date}} and ${{end_date}}.
</task>

<constraints>
- Use the explicit date window above. Do not use "latest" or "recent" as a substitute.
- Prioritize curated Google Threat Intelligence data and note where OSINT is included.
- Include only items that fall inside the window and match the sector and region filters.
- Rank items by significance, most significant first.
</constraints>

<output_format>
Return Markdown with these sections:
1. Headline: the two or three most important developments, as a short bulleted list.
2. Threat activity: a table with columns Date, Item, Type (actor, campaign, malware, vulnerability), and Why it matters.
3. Emerging trends: patterns observed across the window, as bullets.
4. Watch list: items to monitor going into the next period.
5. Sources note: one line on the balance of curated versus OSINT used.
</output_format>
```

## Filled-in example

```
<role>
You are a threat intelligence analyst in Google Threat Intelligence producing a periodic landscape briefing.
</role>

<task>
Summarize notable threat activity affecting healthcare in North America between 2026-06-09 and 2026-06-16.
</task>

<constraints>
- Use the explicit date window above. Do not use "latest" or "recent" as a substitute.
- Prioritize curated Google Threat Intelligence data and note where OSINT is included.
- Include only items that fall inside the window and match the sector and region filters.
- Rank items by significance, most significant first.
</constraints>

<output_format>
Return Markdown with these sections:
1. Headline: the two or three most important developments, as a short bulleted list.
2. Threat activity: a table with columns Date, Item, Type (actor, campaign, malware, vulnerability), and Why it matters.
3. Emerging trends: patterns observed across the window, as bullets.
4. Watch list: items to monitor going into the next period.
5. Sources note: one line on the balance of curated versus OSINT used.
</output_format>
```

## Expected output shape

A dated Markdown digest: a short headline list, a ranked activity table scoped to the window and filters, a trends section, a watch list, and a one-line sources note. GTI citations accompany the answer. This prompt pairs well with a recurring schedule.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): intel analyst producing a periodic briefing.
- **Explicit date constraints** (GTI): `${{start_date}}` and `${{end_date}}` replace "latest" and "recent."
- **Aggregation** (Gemini): pulls many items across the window into one ranked digest.
- **Constraint setting** (Gemini, GTI): in-window and on-filter only, ranked by significance.
- **Output format control** (Gemini, GTI): mixed sections plus an activity table.
- **Source keyword steering** (GTI): curated-first with OSINT noted.
- **Parametrization** (GTI): `${{sector}}`, `${{region}}`, `${{start_date}}`, `${{end_date}}`.
