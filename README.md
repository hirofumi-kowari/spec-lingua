# SpecLingua

[日本語](README.ja.md)

**SpecLingua (SL)** is a set of conventions, and the tools that follow them, for sharing business-system specifications between people and AI. It is built for teams that already have design documents (Excel, Word, Markdown) and a review culture around them, and that want AI to draft, check and trace changes without replacing those documents.

Status: **v0.0.1, draft**. The design has not yet been tried on a real project. The documents describe what is intended; they are not a report of what has been proven.

## What it does

- **Keeps your existing design documents as the source of truth.** AI reads a faithful Markdown conversion of them; the originals stay where they are.
- **Records each change in a change specification.** What was true before, what changes, what stays the same, open questions, acceptance criteria, and where the change has been reflected.
- **Separates evidence from decisions.** Every AI-drafted statement carries a provenance tag (`stated` / `observed` / `inferred` / `unverified`). A statement becomes *confirmed* only when a person decides so, and that decision is recorded in the ledger you already use.
- **Splits checking three ways.** Scripts check form, references, versions and traceability. AI review looks for missing cases and weak evidence. People decide scope, business rules and acceptance.
- **Asks nothing of the user except plain language.** The user hands over a request and materials, edits the drafted text as ordinary prose, and answers questions. IDs, references and metadata are maintained by the tools.

## Documents

| File | Audience | Contents |
|---|---|---|
| [SL_v0_0_1_concepts.en.md](SL_v0_0_1_concepts.en.md) | Decision makers, designers | Principles, division of responsibility with the development harness, document model, provenance and decision states, inspection stages, workflow |
| [SL_v0_0_1_reference.en.md](SL_v0_0_1_reference.en.md) | Implementers | Schema: front matter keys, sections and tables, ID and reference formats, fixed vocabulary, worked example, checklist item structure, extension schemas, evidence references, parsing order |

The table links to the English translations. The Japanese originals are also available: [Concepts](SL_v0_0_1_concepts.ja.md) and [Reference](SL_v0_0_1_reference.ja.md). Machine-readable keys, IDs and fixed values in the schema are English ASCII, so the conventions work in any language; only headings, column labels and prose are localised.

## Relation to the development harness

SpecLingua defines *how the specification is prepared* before an AI-driven development loop runs: the rules a person sets, the evidence an AI must cite, and the questions it must raise instead of guessing. The loop itself (execution by AI, automated checks, quality gates, human acceptance) is explained in the companion repository **[ai-dev-harness](https://github.com/hirofumi-kowari/ai-dev-harness)**.

## Roadmap

1. Stage A: one real modification on an existing system, using the change specification and the inspection script. Revise the documents from what is learned.
2. Stage B: new development and design-document-first modifications.
3. Tools (`sl_check`, drafting skills) and a user guide follow the Stage A results.

## License

The documents in this repository are licensed under [Creative Commons Attribution 4.0 International](LICENSE) (CC BY 4.0). See [LICENSE.ja.md](LICENSE.ja.md) for a Japanese summary.
