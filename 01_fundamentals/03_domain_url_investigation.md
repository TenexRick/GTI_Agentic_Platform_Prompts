# Domain and URL Investigation

**Tier:** Fundamentals (beginner)

## Purpose

Investigate a domain or URL: its reputation, hosting and infrastructure, related indicators, and any documented threat ties. Use it to decide whether to block, monitor, or clear web infrastructure that shows up in an alert or a phishing report.

## When to use it

- A domain or URL appears in proxy logs, an email, or a phishing report and you need a verdict plus context.
- You want to understand the infrastructure around it (resolutions, related domains, hosting) to support a block or a pivot.
- You want curated GTI intel with optional OSINT context.

## Variables

| Variable | Description |
| --- | --- |
| `${{web_ioc}}` | The domain or URL to investigate, for example `example-login.com` or `https://example-login.com/verify`. |
| `${{data_source}}` | Which GTI data to prioritize: `curated` or `osint`. |
| `${{decision}}` | The decision you are trying to support, for example `block`, `monitor`, or `allow`. Focuses the recommendation. |

## Prompt body

```
<role>
You are a threat infrastructure analyst in Google Threat Intelligence specializing in web-based threats.
</role>

<task>
Investigate the following web indicator and assess the risk it poses.
Indicator: ${{web_ioc}}
</task>

<constraints>
- Prioritize ${{data_source}} Google Threat Intelligence data.
- Cover reputation, hosting and infrastructure, related indicators, and any documented threat associations.
- Frame the recommendation to support this decision: ${{decision}}.
- State explicitly when a category has no data rather than filling gaps with assumptions.
</constraints>

<output_format>
Return Markdown with these sections:
1. Verdict and risk: MALICIOUS, SUSPICIOUS, BENIGN, or UNKNOWN, plus a one-line risk statement.
2. Infrastructure: hosting, resolutions, registrar or ASN, and notable certificate or WHOIS facts.
3. Related indicators: associated domains, subdomains, IPs, or files, as a bulleted list.
4. Threat associations: linked actors, malware, or campaigns, or "None documented."
5. Recommendation: a one-line call tied to the ${{decision}} decision.
</output_format>
```

## Filled-in example

```
<role>
You are a threat infrastructure analyst in Google Threat Intelligence specializing in web-based threats.
</role>

<task>
Investigate the following web indicator and assess the risk it poses.
Indicator: https://secure-account-verify.example/login
</task>

<constraints>
- Prioritize curated Google Threat Intelligence data.
- Cover reputation, hosting and infrastructure, related indicators, and any documented threat associations.
- Frame the recommendation to support this decision: block.
- State explicitly when a category has no data rather than filling gaps with assumptions.
</constraints>

<output_format>
Return Markdown with these sections:
1. Verdict and risk: MALICIOUS, SUSPICIOUS, BENIGN, or UNKNOWN, plus a one-line risk statement.
2. Infrastructure: hosting, resolutions, registrar or ASN, and notable certificate or WHOIS facts.
3. Related indicators: associated domains, subdomains, IPs, or files, as a bulleted list.
4. Threat associations: linked actors, malware, or campaigns, or "None documented."
5. Recommendation: a one-line call tied to the block decision.
</output_format>
```

## Expected output shape

A Markdown report with a verdict and risk line, an infrastructure section, a related-indicator list useful for pivoting and blocklisting, threat associations, and a decision-aligned recommendation. Citations to GTI sources appear in the interface.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): infrastructure analyst persona.
- **Explicit constraints and boundaries** (Gemini, GTI): defines coverage and no-data handling.
- **Decision-oriented framing** (Gemini): `${{decision}}` tells the agent the result the user needs, per GTI's "state the final result" guidance.
- **Source keyword steering** (GTI): `curated` vs `osint`.
- **Output format control** (Gemini, GTI): five fixed sections.
- **Parametrization** (GTI): `${{web_ioc}}`, `${{data_source}}`, `${{decision}}`.
