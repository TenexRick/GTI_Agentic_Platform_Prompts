# Detection and YARA Support

**Tier:** Advanced template

## Description

Generate detection logic from a malware family, a behavior, or a set of indicators. Supports both YARA (file and content matching) and YARA-L (Google SecOps detection language), with a built-in validation step. Use it to turn intel into a deployable detection draft.

## When to use it

- You want a first-draft detection rule from a family, a behavior, or a set of IoCs.
- You need YARA for file or content hunting, or YARA-L for Google SecOps.
- You want the rule explained and sanity-checked, not just emitted.

## How this prompt uses examples

This template includes a short few-shot block that fixes the rule structure and the metadata fields. Few-shot examples are the most reliable way to control format in Gemini, and a rule is exactly the kind of output where structure matters. Swap in your own house style if you have one.

## Variables

| Variable | Description |
| --- | --- |
| `${{detection_target}}` | What to detect: a malware family, a behavior, or a description, for example `Lumma infostealer config retrieval`. |
| `${{rule_language}}` | `YARA` for file or content matching, or `YARA-L` for Google SecOps. |
| `${{indicators}}` | Optional known indicators to incorporate (hashes, strings, domains). Use `none` if working from behavior only. |
| `${{rule_name}}` | The rule name to emit, for example `lumma_config_retrieval`. |

## Prompt body

```
## Role
You are a detection engineer in Google Threat Intelligence who writes precise, low-false-positive detection rules.

## Task
Write a ${{rule_language}} rule named ${{rule_name}} that detects: ${{detection_target}}.
Incorporate these known indicators where appropriate: ${{indicators}}.

## Constraints
- Base the logic on documented Google Threat Intelligence data for the target. Note any assumption you make.
- Favor specificity to limit false positives. Avoid matching on generic or common strings alone.
- Include descriptive metadata: author, date, description, and reference.
- After the rule, explain the detection logic and call out likely false-positive sources.

## Example structure
rule example_family_behavior {
    meta:
        author = "TENEX GTI Workshop"
        date = "2026-06-16"
        description = "Detects example behavior in the example family"
        reference = "GTI"
    strings:
        $s1 = "example_marker" ascii wide
        $s2 = { 6A 40 68 00 30 00 00 }
    condition:
        uint16(0) == 0x5A4D and all of them
}

## Output format
Return Markdown with:
1. The rule inside a single fenced code block, following the structure shown above.
2. Logic explanation: what each part matches and why.
3. False-positive notes: where this rule could misfire and how to tighten it.
4. Validation checklist: three checks to run before deployment.
```

## Expected output shape

A single fenced rule block in the requested language, followed by a plain-language logic explanation, false-positive notes, and a short pre-deployment validation checklist. GTI can generate YARA-L rules directly (for example, "Provide a YARA-L rule to detect the Wannacry Ransomware"), and citations accompany the answer.

## Patterns demonstrated

- **Role assignment** (Gemini, GTI): detection engineer persona.
- **Few-shot example** (Gemini): the `## Example structure` block fixes rule format and metadata fields.
- **Output format control** (Gemini, GTI): rule in one code block plus three explanation sections.
- **Constraint setting** (Gemini, GTI): specificity over breadth, required metadata.
- **Ask-for-reasoning guardrail** (GTI): the rule must be explained and its false positives surfaced, which helps catch flawed logic.
- **Validation step** (Gemini, GTI): a built-in checklist before deployment.
- **Parametrization** (GTI): `${{detection_target}}`, `${{rule_language}}`, `${{indicators}}`, `${{rule_name}}`.

---

[Return to README.md](../README.md)
