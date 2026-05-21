# Kubernetes Study Project Rules

This repository is a long-running Kubernetes study project. Use these rules when answering questions, updating documentation, or summarizing study sessions.

## Study Method: Root-Cause Learning

When answering technical questions, prefer this structure:

1. Identify the original problem that existed before the technology.
2. Explain how the problem was handled before this technology.
3. Describe the limitations of the older approach.
4. Explain the abstraction or mechanism this technology introduced.
5. Connect the idea to current Kubernetes practice.
6. Provide a minimal hands-on way to verify the idea when useful.
7. Cite official documentation or primary sources when the answer depends on facts that may change or should be verified.

Do not treat root-cause learning as a separate research document by default. For a single concept, include the origin problem, historical context, and references inside the concept document.

## Documentation Policy

Use `docs/` for human-readable study material:

- `docs/study-log/`: daily learning summaries, meaningful Q&A, experiment results, unresolved questions, and next study candidates.
- `docs/concepts/`: stable concept documents. Each concept should include root-cause context and references.
- `docs/deep-dives/`: broad investigations that span multiple concepts, comparisons, historical background, or architectural tradeoffs.
- `docs/labs/`: hands-on practice records, commands, observations, errors, fixes, and lessons learned.

Use `manifests/` for runnable Kubernetes YAML and reusable resource examples.
Use `assets/` for diagrams, screenshots, and supporting media.

## Concept Document Shape

Prefer this structure for `docs/concepts/*.md`:

```markdown
# Concept Name

## 해결하려던 문제

## 기존 방식의 한계

## 핵심 아이디어

## 동작 방식

## 실습으로 확인하기

## 자주 헷갈리는 점

## References
```

## AI Wiki Policy

The user will create the `omx_wiki/` directory manually, likely with Obsidian. Do not create it unless explicitly asked.

When `omx_wiki/` exists, treat it as AI-oriented long-term memory, not as the main human documentation surface.

Only ingest durable, reusable knowledge into `omx_wiki/`:

- Core definitions
- Why a technology or abstraction exists
- Final understanding of confusing concepts
- Reliable facts confirmed from official or primary sources
- Short reusable summaries extracted from `docs/concepts/` or `docs/deep-dives/`

Do not ingest:

- Raw conversation transcripts
- Full daily logs
- Unverified guesses
- Long lab output
- Temporary planning notes

## Daily Study Merge

At the end of a study day, extract meaningful learning content from the conversation.

If there was meaningful study activity:

1. Create or update `docs/study-log/YYYY-MM-DD.md`.
2. Promote stable concept material into `docs/concepts/`.
3. Promote broad cross-concept investigations into `docs/deep-dives/`.
4. Record hands-on practice in `docs/labs/`.
5. If `omx_wiki/` exists, ingest only durable reusable knowledge into it.

If there was no meaningful study activity, do not create empty files.

## Scheduled Follow-Up

Daily heartbeat reminders should ask whether to summarize the day before changing files.
The default flow is:

1. Ask whether there was Kubernetes study content to summarize.
2. If the user approves, update `docs/` and optionally `omx_wiki/`.
3. Create a branch, commit, and pull request when files change.
4. Do not auto-merge study summaries unless the user explicitly asks.

## Skill Promotion Rule

Keep these rules in `AGENTS.md` until the workflow becomes stable across multiple study repositories.
Create a dedicated skill only after the same process repeats often enough to justify reuse, for example across both Kubernetes and Elasticsearch projects.

