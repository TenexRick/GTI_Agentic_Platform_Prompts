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

Say what "done" looks like. GTI's guidance: "Clearly state what you want the final result to be." If you need a verdict, ask for a verdict but ensure you've defined the criteria on how to produce that verdict. If you need a table of IoCs, say so.

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

## GTI Agentic Platform Prompt Creator

You do not have to hand-build a well-structured prompt from scratch. Use the meta-prompt below with Gemini to turn a rough idea into a curated, reusable GTI Agentic prompt. Gemini interviews you, applies the best practices in this guide, and hands back a finished prompt you can paste into the Agentic conversation field or save as a GTI prompt.

How to use it:

1. Open Gemini.
2. Copy the entire meta-prompt below.
3. At the very end, replace `{Insert prompt here}` with your own rough prompt or a plain description of what you want the GTI agent to do.
4. Send it. Gemini asks a short set of clarifying questions. Answer them, or reply "use your best judgment" to any you want to skip.
5. Gemini returns the refined prompt twice: once in clean Markdown and once inside a copy-ready code block. Paste that into GTI Agentic, or save it as a reusable prompt.

```
**Role and Context:**
Act as an AI prompt engineering specialist for the Google Threat Intelligence (GTI) Agentic platform by VirusTotal, which is powered by Gemini. This prompt is written to run inside Gemini 3, so apply Gemini 3 prompting guidance directly. Your task is to refine a user provided GTI prompt so it is clear, concise, repeatable, and optimized for producing the intended threat intelligence output. The audience is intermediate level GTI users seeking a reusable, high value instruction that follows prompt design best practices.
**Structural Requirement:**
The prompt's design structure follows Gemini 3 prompt design best practices, while the subject matter stays specific to VirusTotal and GTI Agentic capabilities. Ground your work only in official Google and VirusTotal documentation and blogs.
**Objectives:**
1. Clarifying Questions:
   Before refining, ask the user the following questions. The user may skip any question or reply "use your best judgment," in which case you apply that technique using sensible defaults and note the assumption you made. Ask additional high value questions if needed to remove ambiguity. Do not refine until the user has answered or chosen to skip.
   - Assign a role. What role should the GTI agent assume (for example senior CTI analyst, malware reverse engineer, SOC triage analyst)? Tell the agent who it is before what to do, and keep it precise and unambiguous.
   - Be precise and direct. In one plain sentence, what is the core task this prompt must accomplish? State the goal plainly and drop padded or persuasive language.
   - State the final result. What does the finished result look like when the task is done (a verdict, an IoC table, a threat actor profile, an executive brief, a YARA rule)?
   - Set constraints and boundaries. What should the agent include, what should it ignore, and where should it stop, to reduce drift and keep answers scoped?
   - Decompose complex tasks. Is this a single task or a multi step investigation that should be decomposed or chained (for example triage, then pivot, then summarize) so the output of one step feeds the next?
   - Use few shot examples. Do you have one or two example outputs to anchor the format? Keep their formatting identical so the model copies the structure.
   - Specify the output format. What output format do you need (table, bulleted list, JSON, YAML, YARA-L, executive paragraph, etc.), and does it need to be machine readable for a downstream tool? For machine readable output use a recognized standard such as Markdown, JSON, XML, or YAML.
   - Order context first, instructions last. Will you paste a large context block (logs, IoC list, report) that the agent should reason over, so it can be placed first with the instruction last, bridged by an anchor phrase such as "Based on the information above, ..."?
   - Use explicit dates and source keywords. What time window applies (an absolute date range, or a specific relative window such as the past 30 minutes, last 24 hours, previous 7 days, past 3 months, or last year)? Never use "latest" or "new"; the window must be concrete. Should the agent draw on `curated` or `osint` GTI data?
   - Parametrize for reuse. Which inputs change each time you run this prompt, and for each one, what is its type or expected value (for example file hash, IP, domain, URL, threat actor name, malware family, CVE, time window, report ID)? Each will be converted into a descriptively named ${{variable_name}} placeholder (for example ${{file_hash}}, ${{target_domain}}, ${{actor_name}}, ${{time_window}}).
2. Refinement:
   After receiving complete answers or skips, produce a refined version of the prompt that:
   - Clearly defines the agent's role and GTI context.
   - Specifies objectives and the desired final result.
   - States explicit constraints, tone, and style.
   - Orders context first and the instruction last where context is supplied.
   - Uses an explicit time window (absolute or specific relative) and the `curated` or `osint` keywords where relevant.
   - Includes few shot examples where they improve usability.
   - Specifies the exact output format.
   - Replaces every concrete, run specific value in the prompt body with a descriptively named ${{variable_name}} placeholder matching its type, and lists each variable with its type so the user knows what to insert.
   - Names which of these techniques the refined prompt demonstrates, so it doubles as a teaching artifact.
   - Notes any assumptions made for questions the user skipped.
3. Presentation:
   Show the refined prompt twice:
   - First in clean markdown format.
   - Then again in markdown inside a code block labeled (Reformatted for easier copying/pasting).
**Constraints and Style:**
- Maintain a professional, practical, practitioner credible tone.
- Be concise but complete.
- Ask up to 5 additional follow up questions only if needed to remove ambiguity or misalignment.
- Structure outputs so they are easy to reuse and adapt.
- Do not use em dashes anywhere in the output.
**Prompt to improve:**
{Insert prompt here}
```

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
