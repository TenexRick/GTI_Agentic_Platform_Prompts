# Prompt Engineering for the Google Threat Intelligence Agentic Platform

A hands-on workshop and reusable prompt library for getting better results from the **Google Threat Intelligence (GTI) Agentic platform**.

Built and presented by **TENEX.AI**.
Authors: **Anthony Guida** (VP of Customer Engineering) and **Rick Kotlarz** (Customer Engineering).

---

## Table of contents

- [What this is](#what-this-is)
- [Learning objectives](#learning-objectives)
- [Who it is for](#who-it-is-for)
- [Requirements](#requirements)
- [How to use this repo](#how-to-use-this-repo)
- [How to adapt the prompts](#how-to-adapt-the-prompts)
- [Tiered prompt index](#tiered-prompt-index)
  - [Fundamentals (beginner)](#fundamentals-beginner)
  - [Use case prompts (intermediate)](#use-case-prompts-intermediate)
  - [Advanced templates](#advanced-templates)
- [The `${{variable_name}}` convention](#the-variable_name-convention)
  - [Worked example](#worked-example)
- [A note on accuracy](#a-note-on-accuracy)
- [Sources and attribution](#sources-and-attribution)
- [Legal disclaimer](#legal-disclaimer)

New to prompting? Start with [Prompt Engineering Best Practices](01_fundamentals/00_prompt_engineering_best_practices.md).

---

## What this is

**What is the Google Threat Intelligence Agentic platform?** It is the conversational interface to Google's threat intelligence. You chat directly with specialized AI agents, powered by Google Gemini and grounded in Google's comprehensive security data, to accelerate investigations, analyze potential threats, and get immediate answers to security questions. In short, it simplifies threat intelligence by letting you ask, rather than query.

This repo teaches you to prompt that platform well, and gives you ten ready-to-use, parametrized prompts spanning everyday triage to advanced, team-standardized workflows.

The organizing idea behind every file:

- **Structure follows Gemini.** How you build a prompt (role, constraints, decomposition, examples, output format, ordering) follows Gemini prompt design strategy, because the platform runs on Gemini.
- **Subject matter follows GTI.** What you ask about (IoCs, hashes, domains, threat actors, malware families, campaigns, vulnerabilities, YARA and YARA-L) is specific to VirusTotal and GTI Agentic capabilities.

You are learning Gemini-style prompting applied to GTI.

## Learning objectives

By the end of the workshop you will be able to:

1. Explain what prompt engineering is and why it materially changes GTI Agentic output quality.
2. Apply the core Gemini prompt design patterns: role assignment, explicit constraints, task decomposition, few-shot examples, and output formatting.
3. Apply the GTI Agentic best practices: explicit dates, source steering with `curated` and `osint`, role and format guidance, and quota-aware file handling.
4. Use the `${{variable_name}}` convention to turn any prompt into a reusable, team-shareable template.
5. Run the included prompts across the full skill range, from single-IoC triage to a standardized executive brief.
6. Operationalize prompts as saved, shared team workflows inside GTI.

## Who it is for

Two audiences, one ramp:

- **New to prompt engineering.** Start at the fundamentals and the best-practices guide. Each prompt is copy-ready.
- **Experienced threat intel analysts.** Skim the fundamentals, then focus on the advanced templates and the patterns each one demonstrates.

## Requirements

The Agentic platform is only available to users with the Google Threat Intelligence (Google TI) Enterprise or Enterprise Plus licenses, with no quota limitations on its use.

A few related notes from the GTI documentation:

- Uploading files to Private Scanning through Agentic consumes your existing Private Scanning quota, so check whether an IoC is already in GTI before submitting a file.
- Do not submit personal or sensitive information in queries or file uploads. You are responsible for the content of your submissions.
- A group administrator can disable all AI tools, including Agentic, under My group, then Settings, then AI Settings, to meet legal or compliance requirements.

## How to use this repo

1. **Read the best-practices guide first:** [Prompt Engineering Best Practices](01_fundamentals/00_prompt_engineering_best_practices.md). It covers the Gemini 3 strategies, why Markdown is the right format, and a fast way to author prompts with Gemini and Google Docs.
2. **Pick a prompt** from the tiered index below that matches your task.
3. **Copy the prompt body.** Every prompt body sits in a fenced code block for one-click copy.
4. **Fill the variables.** Replace each `${{variable_name}}` with your value, or paste the prompt into the Agentic conversation field and run it.
5. **Save it in GTI for reuse.** In Agentic, go to Prompts, then **+ Create prompt**, paste the body, and keep the `${{variable_name}}` placeholders. GTI will show a form for those variables every time you run it.

## How to adapt the prompts

- **Swap the role** to match your team's voice or seniority.
- **Tighten the constraints** to your environment (data sources, what to ignore, confidence handling).
- **Edit the output format** to match your ticketing, reporting, or detection tooling.
- **Reuse variable names** across prompts where the value means the same thing, so the library feels consistent.
- **Refine, do not restart.** If the first answer is close, use follow-up prompts to improve it, which is also GTI's recommended workflow.

## Tiered prompt index

### Fundamentals (beginner)

| # | Prompt | What it does |
| --- | --- | --- |
| 00 | [Prompt Engineering Best Practices](01_fundamentals/00_prompt_engineering_best_practices.md) | Read first. The Gemini 3 strategies, Markdown affinity, and the Gemini plus Google Docs authoring loop. |
| 01 | [IoC Triage and Enrichment](01_fundamentals/01_ioc_triage_and_enrichment.md) | Fast, sourced verdict on a single IoC. |
| 02 | [File Hash Analysis](01_fundamentals/02_file_hash_analysis.md) | Plain-language behavior and verdict for a hash, quota-aware. |
| 03 | [Domain and URL Investigation](01_fundamentals/03_domain_url_investigation.md) | Reputation, infrastructure, and a block or monitor call. |

### Use case prompts (intermediate)

| # | Prompt | What it does |
| --- | --- | --- |
| 04 | [Threat Actor Profile](02_use_case_prompts/04_threat_actor_profile.md) | Structured actor profile with ATT&CK-mapped TTPs. |
| 05 | [Malware Family Summary](02_use_case_prompts/05_malware_family_summary.md) | Capability and detection summary for a named family. |
| 06 | [Vulnerability Briefing](02_use_case_prompts/06_vulnerability_briefing.md) | CVE exposure, exploitation status, and mitigation. |
| 07 | [Threat Landscape Summary](02_use_case_prompts/07_threat_landscape_summary.md) | Time-bounded, filtered intel digest. |

### Advanced templates

| # | Prompt | What it does |
| --- | --- | --- |
| 08 | [Campaign Pivoting and Commonalities](03_advanced_templates/08_campaign_pivoting_and_commonalities.md) | Expand a seed into a campaign and compute commonalities. |
| 09 | [Detection and YARA Support](03_advanced_templates/09_detection_and_yara_support.md) | Draft and validate YARA or YARA-L detection logic. |
| 10 | [Standardized Team Brief Template](03_advanced_templates/10_standardized_team_brief_template.md) | The capstone: a full structured template for executive briefs. |

## The `${{variable_name}}` convention

GTI Agentic supports dynamic variables in saved prompts using the placeholder syntax `${{variable_name}}`. When you run a saved prompt, GTI shows a form that collects a value for each variable. That is what makes a prompt reusable.

### Worked example

Take a minimal prompt with one variable:

```
Give me a report on ${{file_hash}}
```

Save it in GTI as a prompt. The next time you run it, GTI asks you for `file_hash`. You paste a SHA256:

```
5d41402abc4b2a76b9719d911017c592a1b2c3d4e5f60718293a4b5c6d7e8f90
```

GTI substitutes the value and runs:

```
Give me a report on 5d41402abc4b2a76b9719d911017c592a1b2c3d4e5f60718293a4b5c6d7e8f90
```

The prompts in this repo apply the same idea with richer structure. Conventions we follow:

- Lowercase `snake_case` names: `${{file_hash}}`, `${{threat_actor}}`, `${{start_date}}`.
- Name the variable for the value it holds.
- Keep dates explicit and in `YYYY-MM-DD` form so you never rely on "latest."
- Reuse the same name across prompts when the value means the same thing.

## A note on accuracy

Generative AI can produce inaccurate information. Treat every Agentic answer as a starting point and verify anything critical against the citations GTI provides. If an answer looks wrong, ask the agent to explain its reasoning, which is GTI's documented way to surface and correct hallucinations.

## Sources and attribution

This workshop is grounded entirely in official Google and VirusTotal documentation:

- GTI Agentic Platform: https://gtidocs.virustotal.com/docs/agentic-platform
- GTI Agentic User Guide: https://gtidocs.virustotal.com/docs/agentic-user-guide
- Gemini 3 prompting guide, Gemini Enterprise Agent Platform: https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/start/gemini-3-prompting-guide
- Gemini API, Prompt design strategies: https://ai.google.dev/gemini-api/docs/prompting-strategies

---

## Legal disclaimer

Google Threat Intelligence, the Google Threat Intelligence Agentic platform, VirusTotal, Gemini, and all related names, logos, product features, and documentation are the property of Google LLC and its affiliates. All rights in those products and materials belong to Google. The prompt design strategies and Agentic best practices referenced in this workshop originate from Google and VirusTotal official documentation, which is cited throughout and linked in the Sources and attribution section above.

This workshop is an independent educational resource created by TENEX.AI to illustrate use and to share Google's published best practices for effectively leveraging the Google Threat Intelligence Agentic platform. It is not created, endorsed, sponsored, or affiliated with Google or VirusTotal, and it does not claim any ownership of their intellectual property. Any trademarks are used only for identification and educational reference. The content is provided as is, without warranty of any kind, and is not a substitute for the official Google and VirusTotal documentation. Always verify against the primary sources, and follow your organization's licensing terms and policies when using the platform.
