# Prompt Engineering Best Practices for the GTI Agentic Platform

Read this first. Everything in the rest of the repo applies the principles below. The reusable prompts are designed to run inside the **Google Threat Intelligence (GTI) Agentic platform**, which is powered by Gemini. That means two things are always true at once:

- **Structure** follows Gemini prompt design strategy. How you build a prompt (role, constraints, decomposition, examples, output format, ordering) is governed by how Gemini reasons.
- **Subject matter** is specific to VirusTotal and GTI Agentic. What you ask about (IoCs, hashes, threat actors, campaigns, YARA, IoC Stream, Private Scanning) is governed by what the GTI dataset and its agents actually support.

You are learning Gemini-style prompting applied to GTI.

---

## Why prompt engineering matters for GTI

The GTI Agentic platform runs on Gemini 3 and uses Retrieval Augmented Generation (RAG) over Google's curated threat intelligence, filtered OSINT, and in some cases Google Search. It routes work between specialized agents, primarily a Threat Intel (CTI) agent and a Malware Analysis agent. The quality of what comes back depends heavily on how you frame the request. A vague prompt produces generic intel. A well-structured prompt produces a sourced, decision-ready answer.

GTI states this directly in its own guidance: "The more information you provide, the better the response is." Prompt engineering is the discipline of providing that information in a form the model uses well.

A second reason matters for security work specifically: every Agentic answer ships with citations and a visible reasoning trace (the "Thinking and Tools" sections). Good prompts make that trace easier to audit, and they make hallucinations easier to catch. When an output looks off, GTI recommends you ask the agent for its reasoning rather than discarding the result.

---

## The ten core practices

These combine the Gemini 3 prompting guidance with the GTI Agentic "Best practices" list. Each prompt in this repo demonstrates several of them, and each file names which ones it uses so the file doubles as a teaching artifact.

### 1. Assign a role

Tell the agent who it is before you tell it what to do. "You are a senior CTI analyst" or "You are a malware reverse engineer" sets the vocabulary, depth, and judgment the model applies. GTI lists this explicitly ("Providing a role like 'you are a malware analyst'"), and Gemini 3 treats an assigned persona seriously, so keep the role precise and unambiguous.

### 2. Be precise and direct

Gemini 3 responds best to direct, well-structured prompts. State the goal in plain terms and drop persuasive or padded language. "Analyze X and return Y" beats a paragraph of throat-clearing.

### 3. State the final result you want

Say what "done" looks like. GTI's guidance: "Clearly state what you want the final result to be." If you need a verdict, ask for a verdict. If you need a table of IoCs, say so.

### 4. Set constraints and boundaries

Tell the agent what to include, what to ignore, and where to stop. GTI: "Specify what to ignore or what boundaries to stay within." Constraints reduce drift and keep answers scoped to the investigation.

### 5. Decompose complex tasks

For multi-part work, break the prompt into explicit steps or chain prompts so the output of one becomes the input of the next. Gemini's strategy guide calls this breaking down instructions, chaining, and aggregating. In practice: triage first, then pivot, then summarize, rather than asking for all three in one tangled request.

### 6. Use few-shot examples

Show the model what a correct answer looks like. Gemini recommends including examples in almost every non-trivial prompt, and keeping the formatting of those examples identical so the model copies the structure. One or two worked examples often outperform another paragraph of instructions.

### 7. Specify the output format

Ask for the exact shape: a table, a bulleted list, a JSON object, a YARA rule, an executive paragraph. GTI lists "Providing output formatting guidance" as a best practice, and Gemini 3 honors format requests well. For machine-readable output, use a recognized standard (Markdown, JSON, XML, or YAML) so downstream tools can parse it.

### 8. Order context first, instructions last

When you paste a large block of context (a log excerpt, an IoC list, a report), put it at the top and put your question at the very end. Gemini 3 guidance: supply all context first, then the instruction. Bridge the two with an anchor phrase such as "Based on the information above, ...".

### 9. Use explicit dates and source keywords

Never write "latest" or "new." GTI: "Instead of using 'latest' or 'new', provide a date for accuracy." Give a concrete window such as "between 2026-05-01 and 2026-06-01." Use the keywords `curated` or `osint` to steer which GTI data the agent draws on.

### 10. Parametrize for reuse

Turn a good one-off into a template by replacing the specifics with variables. GTI: "Parametrize your prompts to make them reusable." This repo uses GTI's documented `${{variable_name}}` syntax throughout (covered in the next section).

---

## Good versus weak prompts

The fastest way to internalize the practices above is to compare them side by side. Each row below takes a weak prompt for one of the Fundamentals tasks, explains why it underperforms in GTI Agentic, and shows a stronger rewrite that applies the practices. The weak versions are not made up; they are the way most people phrase a request before they learn to structure it.

| Weak prompt | Why it is weak | Stronger prompt |
| --- | --- | --- |
| `Is 8.8.8.8 bad?` | No role, no output shape, and no data-source steering. A yes or no question invites a guess rather than an evidence-based verdict, and it gives the agent no way to tell you when GTI simply has no data. | `You are a CTI analyst. Triage the IP 8.8.8.8 using curated Google Threat Intelligence data. Return a verdict (MALICIOUS, SUSPICIOUS, BENIGN, or UNKNOWN), the GTI score and antivirus summary, any known associations, and one recommended next step. If GTI has no data, say so explicitly.` |
| `Tell me about this file 5d41402abc4b2a76b9719d911017c592` | "Tell me about" is undirected, so the depth and format are left to chance. No role to anchor the malware-analysis intent, and no instruction to use existing GTI data first, which risks spending Private Scanning quota. | `You are a malware analyst. Using existing Google Threat Intelligence data for hash 5d41402abc4b2a76b9719d911017c592, identify the file, list its observed behaviors, extract notable indicators, and give a two-sentence verdict. If the hash is not in GTI, say so and stop.` |
| `Look up this domain and tell me everything about it, latest info` | "Everything" is unbounded so the answer sprawls, "latest" is vague where an explicit date is needed, and there is no decision the output is meant to support and no format. | `You are a threat infrastructure analyst. Assess secure-account-verify.example using curated Google Threat Intelligence data to support a block decision. Cover reputation, hosting and infrastructure, related indicators, and threat associations. Note explicitly when a category has no data.` |
| `Summarize recent threats to healthcare` | "Recent" has no boundaries, so results drift across an undefined window. No region scope, no ranking, and no output structure, which produces an unfocused wall of text. | `You are a threat intelligence analyst. Summarize notable threats to healthcare in North America between 2026-06-09 and 2026-06-16, ranked by significance. Use curated Google Threat Intelligence data and present the result as a short headline list followed by a table.` |

The pattern across every row is the same: the weak prompt leaves the role, the boundaries, the data source, and the output shape to the model, while the stronger prompt states each one. That is the whole discipline in miniature.

---

## Why Markdown is the right format for prompts

LLMs, including Gemini, work very well with Markdown. The structure that Markdown provides (headings, bullet lists, fenced code blocks, tables) maps cleanly onto the way these models parse instructions, so a prompt written in Markdown is easier for the model to follow than the same text as an unstructured paragraph.

Two practical reasons to standardize on Markdown for your GTI prompt library:

- **Clear delimiters improve responses.** Gemini 3 guidance recommends using consistent delimiters to separate the parts of a prompt. XML-style tags such as `<role>`, `<constraints>`, `<task>`, and `<output_format>`, or Markdown headings, both work. Pick one style and use it consistently within a single prompt. For long or complex sections you can also use `BEGIN`/`END` or `{}` delimiters to mark where a block starts and stops. The ordering, labeling, and delimiters all affect output quality.
- **Markdown is a parseable standard.** When you want output that another tool can read, Gemini guidance says to use a widely recognized format. Markdown is one of those formats, which is why the prompt bodies in this repo live inside fenced code blocks: they are copy-ready and unambiguous.

Every prompt file in this repo is a `.md` file. The prompt body itself is wrapped in a fenced code block so you can copy it in one click and paste it straight into the Agentic conversation field or save it as a GTI prompt.

---

## A fast way to author prompts: Gemini plus Google Docs

You do not have to write these prompts by hand from a blank page. A practical authoring loop:

1. **Draft with Gemini.** Ask Gemini to generate a first-draft prompt for the GTI task you have in mind. Describe the role, the inputs, the constraints, and the output shape you want, and have it produce the prompt in Markdown. Treat the result as a starting point and refine it, the same way GTI advises refining Agentic answers with follow-ups rather than starting over.
2. **Refine in Google Docs.** Paste the draft into a Google Doc to edit, comment, and review with teammates. This is also where you fill in the role and the `${{variable_name}}` placeholders, add a worked example, and tighten the constraints.
3. **Export as Markdown.** Google Docs can export directly to Markdown (File, then Download, then Markdown (.md)). That gives you a clean `.md` file you can drop into this repo, commit, and share, with the structure already in the format the model prefers.
4. **Save it in GTI.** Once the prompt produces output you are happy with, save it in the Agentic Prompts view (Prompts, then + Create prompt) so it becomes a reusable, parametrized prompt for the whole team.

This loop keeps the whole library in one format end to end: drafted in Markdown, reviewed in Markdown, stored in Markdown, and executed as a saved GTI prompt.

---

## The `${{variable_name}}` convention

GTI Agentic supports dynamic variables in saved prompts using the placeholder syntax `${{variable_name}}`. When you run a saved prompt, GTI shows a form that collects a value for each variable, and those are the only fields you fill in. This is what turns a prompt into a reusable template.

Worked example, straight from the GTI documentation pattern:

```
Give me a report on ${{file_hash}}
```

When saved and reused, GTI prompts you for `file_hash`, you paste a SHA256, and the prompt runs with that value substituted in. Every prompt in this repo follows this convention. A few conventions we apply for consistency:

- Use lowercase `snake_case` names: `${{file_hash}}`, `${{threat_actor}}`, `${{start_date}}`.
- Name the variable for the value it holds, not the position it sits in.
- Keep date variables explicit, for example `${{start_date}}` and `${{end_date}}` in `YYYY-MM-DD` form, so you never fall back on "latest."
- Reuse the same variable name across prompts where the value means the same thing, so the library feels consistent.

---

## A note on verifying output

Gemini 3 is optimized to run at its default temperature, and the GTI Agentic platform manages model settings for you, so there are no knobs to turn inside the conversation. Your control surface is the prompt and the follow-ups.

Because generative AI can produce inaccurate information, treat every answer as a starting point and verify anything critical against the citations GTI provides. If an answer looks wrong, ask the agent to explain its reasoning, which is GTI's documented method for surfacing hallucinations and then writing a better follow-up prompt.

---

## Sources

This guide is grounded in official Google and VirusTotal documentation:

- Gemini API, Prompt design strategies: https://ai.google.dev/gemini-api/docs/prompting-strategies
- Gemini 3 prompting guide, Gemini Enterprise Agent Platform: https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/start/gemini-3-prompting-guide
- GTI Agentic User Guide: https://gtidocs.virustotal.com/docs/agentic-user-guide
- GTI Agentic Platform: https://gtidocs.virustotal.com/docs/agentic-platform

---

[Return to README.md](../README.md)
