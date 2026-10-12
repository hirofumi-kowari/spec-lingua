# SpecLingua v0.0.1 Concepts

**Date**: 2026-10-11
**Status**: Draft
**Applicable version**: SpecLingua v0.0.1

[Japanese original](SL_v0_0_1_concepts.ja.md)

This English translation preserves the definitions and requirements of the Japanese original. Example prose and display labels are translated; schema keys, fixed values, IDs, paths, and external location references retain their original notation. Examples with translated headings use `lang: en`.

Sections 4.6–4.8 provide an overview of the schemas. Individual definitions are given in `SL_v0_0_1_reference.en.md`.

In this document, Stage A means a trial involving one real change to an existing system. Stage B extends the work to new development and changes in which design documents are revised first. The rollout plan and procedures for integration with a team's environment are not included in this repository.

---

## 1. What SpecLingua is

### 1.1 Definition

SpecLingua (SL) is a **set of conventions and tools** for sharing business-system specifications between people and AI. The conventions define the meaning of the information recorded in a specification and how to write it. The tools draft, check, and cross-check documents according to those conventions, and verify the relationships among requirements, design, changes, decisions, and verification.

SL defines what to record in a specification, what that information means, and how to relate it to its supporting basis, decisions, and subjects. The harness that uses SL manages when to run AI and tools, whom to ask for confirmation, and how to proceed with the work. A harness is a system that manages AI and tool execution, permissions, and workflow.

The main artifacts handled by SL and the documents that serve as the source of truth differ between new development and changes to an existing system. A source of truth is the document in which the information is officially maintained.

| Development type | Main artifacts defined by SL | Source of truth for the specification |
|---|---|---|
| New development | Design documents based on SL conventions, linking requirements, design, and acceptance criteria | The design documents created for the system. They may be split into multiple documents according to scale |
| Changes to an existing system | A change specification based on SL conventions, describing the current change in Markdown | Existing design documents remain the source of truth for the current specification. The change specification is the source of truth for changes relative to the baseline specification |

SL conventions allow tools to retain and check the required information and its relationships. Their purpose is not to make users memorize section names or YAML keys.

### 1.2 Characteristics

**Keep existing design documents as the source of truth.** Business-system development often uses a process in which Excel or Word design documents are reviewed and agreed upon. SL retains those documents as the source of truth and preserves that process. Separate from the existing design documents, it creates Markdown converted for AI to read (called “faithful Markdown” below) and a change specification describing the current change. It does not require the entire design documentation to be converted to another format and maintained independently of the originals.

**Record the current change in a change specification.** Editing a design document alone does not record the change, its reason, and its scope. The change specification therefore describes the baseline before the change, the intent and individual change items, what remains unchanged, open questions, acceptance criteria, and the status of applying the change.

**Distinguish the basis for a statement from a human decision.** AI-drafted statements carry sources and provenance (stated / observed / inferred / unverified). Confirmation requires a human decision. The existence of a source, an AI recommendation, or a successful test alone does not make the content confirmed. Decisions are recorded centrally in an existing ledger.

**Separate machine checks, AI review, and human decisions.** Checking scripts assess format, source existence, version consistency, references to decision records, and the presence of traceability relationships. AI review identifies ambiguous statements, missing business conditions, and omitted execution paths such as creation, updates, APIs, and batch processing. People decide adoption, business conditions, and acceptance.

**Do not require users to learn a format or maintain duplicate information.** Users provide a request and materials, then edit the draft as ordinary prose. Tools assign IDs, source references, and metadata.

**Record organizational knowledge in checklists and extension rules.** Omissions and decisions identified in projects are accumulated as checklist items and extension rules. A checklist item is a question used to identify missing information during drafting. Extension rules are rules specific to a technical subject, business domain, or organization. They add or adjust checks while retaining SL's common definitions (Section 9).

### 1.3 Scope and exclusions

| Within SL's scope | Outside SL's scope: handled by the harness or existing systems |
|---|---|
| Conventions and section structure for change specifications and new-development design documents | Workflow management, work status, permissions, stopping and resuming work |
| Terms and meanings for sources, provenance, adoption status, confirmation status, and versions | Where decisions are stored: use an existing ledger |
| Drafting, resolving source references, checking, and displaying change application status | Editing design documents, implementation, builds, and test execution |
| Structure of checklists and extension rules | Infrastructure for automated checks and assessment of completion conditions |
| Formats for check results and AI review results | Issue management: use an existing issue register |

Writing a document according to SL conventions alone does not guarantee that it contains all information required by the business or that the implementation is correct. Information sufficiency and implementation correctness are checked through a combination of machine checks, AI review, human decisions, and actual tests. SL's effects on productivity and quality are evaluated in real projects.

### 1.4 User actions and decisions

The four basic actions and decisions explained to users are:

1. Provide the objective and materials.
2. Review the changes, inferences, and open questions presented by AI.
3. Decide the scope of the current work and the expected results.
4. Check evidence of change application and verification, along with outstanding work.

Users can start without first learning the internal conventions or metadata.

---

## 2. Design principles

| Principle | Description |
|---|---|
| Accept flexible input formats while keeping output and its supporting basis accessible | Do not impose an input template. Tools organize the information into the required structure and retain the text and its supporting basis in a form people can review |
| Do not create multiple sources of truth for the same information | Do not require the same information to be maintained independently in different formats. Assign a source of truth to each type of information: specifications, decisions, issues, and execution results |
| Make changes explicit | Do not infer change intent solely from revised design documents. Record the before and after states, exclusions, and acceptance criteria |
| Distinguish supporting information from decisions | Distinguish a statement in a primary source, behavior present in the implementation, and a human decision to adopt it |
| Make open questions explicit while work proceeds | Allow missing information during drafting, and identify open questions that affect implementation or completion. AI must not fill in the answers without a decision |
| Identify what was reviewed | Make it possible to reconstruct who reviewed what and on which assumptions. When content changes, recheck decisions affected by the change |
| Record traceability and evidence | Make the design, implementation, and test results corresponding to a specification accessible. Check the existence of a relationship separately from whether its content satisfies the conditions |
| Keep requirements common to all projects minimal | Add information according to the artifact and technical subject. Do not require the same large template for every project |
| Integrate with existing development practices | Use the existing harness instead of duplicating workflow, ledgers, permissions, or test execution systems |
| Separate internal representation from language-specific display | Use common internal keys, IDs, and fixed values. Map headings, column labels, and status labels to the document language. Do not require users to memorize English fixed values (Section 4.6) |
| Evaluate effects in real projects | Evaluate human effort, duplicate entry, false positives, and missed detections, and adjust the conventions and scope of automation accordingly |

Flexible input formats do not mean business conditions may be confirmed through inference alone. Tools organize the input and present missing or conflicting information in a form the user can decide on.

---

## 3. Three components and their responsibilities

### 3.1 Role of each component

| Component | What it defines or does | Main outputs |
|---|---|---|
| SL conventions | Meanings and relationships of specification information; status terminology; required evidence; checkable conditions; checklist items; extension-rule structure | This document, a one-page summary of the conventions, and a checklist |
| SL tools | Reading, drafting, resolving source references, checking, and displaying change application status | Change-specification Markdown, a list of open questions, check results, and a change application status table |
| Harness | AI and tool invocation, permissions, work stages, human confirmation, ledger recording, design-document editing, implementation, testing, resumption, and completion assessment | Execution records, code changes, test results, and change records |

People decide business adoption, resolve open questions, and confirm content. Document generation and successful harness execution must not substitute for human decisions.

```
User request and materials ──────┐
Existing design documents ───────┼─→ SL tools: draft, resolve sources, check
(source of truth)                │                    │
Checklists and extension rules ──┘                    ▼
                              Change-specification Markdown,
                              open questions, and check results
                                                    │
                                                    ▼
                              Harness: record human decisions,
                              update design documents, implement, test
                                                    │
                                                    ▼ Individual evidence
                              SL tools: display application status, check
```

The arrows show information flow. They do not require every operation to run in this order each time, nor do they authorize automatic updates to documents or records.

### 3.2 Avoid duplicate status management

SL defines the meanings of “confirmed” and “requires recheck.” Decisions are recorded in the existing decision ledger; no SL-specific approval ledger is created. When the ledger already contains a decision, SL refers to that record. Status displayed in a change specification is determined from the authoritative records and the current content.

Use the existing issue register for issues and existing result records for builds and tests. Display tables and internal representations must be regenerable from those sources of truth. Changing only the display or internal representation must not create a status that differs from the authoritative record.

### 3.3 Environment-specific integration configuration

Record the following in the integration configuration to connect SL to an existing development environment:

- Locations and precedence of sources of truth.
- Mappings to existing IDs, item numbers, and locations.
- Language-specific mappings for headings, column labels, and fixed values, along with locations and versions of the applicable extension rules.
- Formats of the decision ledger and issue register, and how to read and write them.
- How to reference test results and verify which code the results apply to.
- Existing completion conditions and hook integration.
- The order of design-document updates and implementation.

The meanings of red text and strikethrough in Excel, folder names, Git restrictions, and hook events vary by team and execution environment. They are therefore not part of SL's common conventions. Any conflict between a team's rules and SL's conventions must be made explicit. Neither set of rules may silently override the other.

---

## 4. Document structure

### 4.1 Sources of truth and derived documents

| Document | Role | Relationship to the source of truth | Created or updated by |
|---|---|---|---|
| Design documents (Excel, Word, MD) | Describe the system's current specification | **Source of truth** | People, through existing procedures |
| Faithful Markdown | A conversion of design documents for AI to read, retaining sheet/cell references and the source version and hash | Derived document; can be regenerated | Conversion script |
| Change-specification Markdown | Describes the current changes, their basis, decisions, and application status | **Source of truth for the current changes** | AI drafts; people revise |
| New-development design Markdown | Describes specifications for new development (Section 4.3) | **Source of truth** when no design documents exist | AI drafts; people revise |
| Decision ledger | Records human confirmation decisions | **Source of truth for decisions** | People; AI may draft proposed entries |
| Issue register (ISSUES) | Records open questions, inconsistencies between design documents, and defects | **Source of truth for open issues** | AI creates entries; people decide |
| Change record | Records implementation and test work performed, handover information, and changed files | Record of work performed | AI records; people review |
| Deliberation record | Records investigation, comparison of options, and the reasoning behind adoption or exclusion | Record | AI and people |

Assign one source of truth to each piece of information. This designation identifies where the information is officially maintained; it does not mean every item is confirmed. A design document containing open questions may still be the source of truth. However, unresolved content must not be used as the basis for implementation while concealing its unresolved status.

Confirmed matters are treated as ordinary input information. When the reasons for a decision need to be checked, the deliberation record may be cited as a source. Record the confirmed conclusion in the ledger, and mark the deliberation record to show that the conclusion has been recorded there.

### 4.2 Change specifications

A change specification describes the current changes to an existing system. Its section structure is fixed as follows. Retain a heading even when the section has no content.

| Section display name | Key | Content |
|---|---|---|
| Purpose | `purpose` | Business reason, expected benefit, and reference to the request |
| Scope and baseline | `scope` | Target design documents and versions, target locations, the code version investigated, and source list |
| Before | `before` | What the design documents state (stated) and what the code currently does (observed). Explicitly identify inconsistencies |
| Changes | `changes` | Change items `C-NN`: type (added / modified / removed), content, provenance and sources, adoption status, and confirmation status |
| Unchanged | `unchanged` | Compatibility, behavior to preserve, exclusions from this change, and their basis |
| Open questions | `open` | `Q-NN`: affected items, references to the issue register, and status |
| Acceptance criteria | `acceptance` | `AC-NN`: corresponding change items, given / when / then, and sources for expected results |
| Reflection status | `reflection` | A table relating each change item to design documents, code, and tests, along with outstanding work |

Section display names use the document language. Tools identify sections using the keys corresponding to those display names (Section 4.6).

The beginning of the document records the change ID, baseline before the change, design documents after the change, code version, ledger and issue-register locations, and display status. These entries are called “metadata.” Tools record the metadata; users do not edit it.

When design documents and code disagree, the current code must not automatically be treated as the correct specification. Establish whether the discrepancy is due to an existing defect or outdated design documents, then decide whether it belongs in the scope of the current change.

If a customer-facing change specification is already maintained as the source of truth in Excel or another format, SL's Markdown is a derived view of that source. The two documents must not be maintained through independent manual edits.

Each change item records its type: added, modified, or removed. “Removed” expresses an intention to retire a business definition or behavior; it is not an instruction to physically delete files or data.

### 4.3 Design documents for new development

For new development with no existing design documents, write requirements and design in Markdown according to SL conventions. AI drafts the content from natural-language descriptions and materials, and people review it. Organize the information into the following categories, using one or more documents according to scale.

| Information category | Content |
|---|---|
| Requirements | Business objectives, users and roles, business rules, required functions and nonfunctional conditions, and exclusions |
| Overall design | System boundaries, architecture, common approaches, and shared policies for data, authentication and authorization, external integration, and similar concerns |
| Detailed design | Inputs and outputs, conditional behavior, data updates, exceptions, and specific screen, API, and batch behavior |
| Acceptance criteria | Preconditions, actions, and expected results used to verify requirements and design |
| Supporting basis, decisions, and open questions | Sources for statements, adoption and confirmation records, and references to unresolved issues |

Small systems do not need a separate file for each category. Write policies shared by multiple functions in common definitions and reference them from the detailed design. Do not duplicate the same statements in each detailed design.

New development has no design document representing the state before a change, so base is unnecessary. Record the first design-document version and the content reviewed. For later revisions, identify the version used as the baseline.

The document structure (requirement / overview / brief / spec) and ID scheme (BIZ- / FN- / SC- / EN- / EX- / RL-) are treated as **document-splitting templates and extension rules** for new development that includes code generation. They are not mandatory for all new development. The detailed section structure is defined in Stage B. Stage B also evaluates whether this method of writing and reviewing documents works in practice.

### 4.4 Recording decisions and open questions

| Record | Recorded content | Information added by SL |
|---|---|---|
| Decision ledger | Human confirmation decisions: subject, content, decision maker, and date | Change ID, affected change items and acceptance criteria, and references to the approved specification and assumption versions |
| Issue register (ISSUES) | Open questions, inconsistencies, and defects: creation date, type, and status | A reference from `Q-NN` in the change specification to an issue ID |

Do not overwrite decision records. To withdraw or change a decision, add a new row and identify the decision it replaces. AI may draft the new entry, but must not record it as confirmed before receiving the human decision.

One decision may cover multiple C and AC items. The integration configuration maps the existing ledger's columns and notation to the information SL requires. Do not add a separate approval ledger.

Count open questions from each issue's status and effect on the current scope, rather than simply counting rows in the issue register. Resolved rows may remain. Excluding an open question from scope requires a reason and a reference to the decision record.

### 4.5 Document formats

| Document | Format | Reason |
|---|---|---|
| Design documents: source of truth | The team's format (Excel, Word, MD) | Preserve existing review practices and storage locations |
| Faithful Markdown | Markdown | AI can read it and locate the source content by sheet and cell |
| Change specifications and new-development design documents | Markdown, with YAML only for the metadata at the beginning | People can edit the body as ordinary prose while tools process the metadata |
| Decision ledger and issue register | The team's format, such as Markdown tables | Reuse existing documents |
| Checklists and extension rules | Markdown, structuring only the parts used for machine processing | Start with natural-language questions; define attributes and constraints only for repeatedly used items (Section 9) |

Artifacts that people read and edit are written in Markdown.

**The scope of machine checking depends on which information is structured and what is checked, rather than on the display format.** A person can edit Markdown while tools check required items, types, references, and relationships in its sections, tables, IDs, and derived internal representations. However, natural-language meaning cannot necessarily be extracted in full. Mark content that cannot be extracted or has an ambiguous interpretation as “unverified”; do not treat it as verified.

In Stage A, strict checking is limited to source existence, version consistency, references to decision records, and traceability relationships. This is an implementation choice for Stage A, not a limitation imposed by Markdown.

Definitions such as API contracts may use machine-oriented formats such as YAML or JSON when appropriate. Even then, identify the source of truth for that part and avoid making users edit the same content twice. Treat the internal representation as derived information that refers back to the original, not as another source of truth.

Syntax, coverage, and consistency checks for new development with code generation are evaluated as extension rules in Stage B.

### 4.6 Schema overview

A schema defines the items to record, value types, required conditions, and references between items. SL distinguishes three categories.

| Category | What it defines | Overview | Full definition |
|---|---|---|---|
| Common management schema | Identification and relationships for documents, change items, requirements, sources, decisions, open questions, and acceptance criteria | This section | Reference Sections 1–6 |
| Business-specification extension schemas | Subject-specific specification content, such as screen input conditions or API inputs and outputs | Section 4.7 | Reference Section 9 |
| Evidence reference rules | References to records used to check design documents, code, and individual test results | Section 4.8 | Reference Section 10 |

This section gives an overview. Key types, required conditions, parsing rules, and complete examples are given in `SL_v0_0_1_reference.en.md` (called “Reference” below). If definitions differ between documents, Reference takes precedence.

Change specifications (`kind: change`) are used in Stage A. New-development design documents (`kind: design`) are a proposal showing how the same mechanisms can apply; they are finalized in Stage B.

#### 4.6.1 Language and display rules

| Category | Rule |
|---|---|
| Keys, fixed values, and IDs | English ASCII. Use this form in internal representations and metadata |
| Headings, column labels, and status labels | Written in the document language and mapped to fixed values through the mapping selected by `lang`. Aliases may also be registered |
| Body text, reasons, questions, and business values | The document language |
| File names, sheet names, and existing item numbers | Retain the names used by the team |

Table cells in the body may contain display labels such as “Confirmed” or “Inferred: reason.” Tools convert these labels to the corresponding fixed values internally, so users do not need to memorize the fixed values. Checks report headings or column labels absent from the mapping as “unrecognized.” Document formatting is separate from checking.

#### 4.6.2 Metadata

Metadata is written as YAML at the beginning of the document. Tools generate it; users do not edit it. Change specifications and new-development design documents have both shared and document-specific keys.

```yaml
# Change specification                # New-development design document
sl: "0.1"                             sl: "0.1"
kind: change                          kind: design
id: CHG-20261011-01                   id: DES-20261011-01
title: Add a weekly reservation limit  title: Employee reservation management
lang: en                              lang: en
status: undecided                     status: undecided
reflected: not_reflected              baseline: {version: "0.1", content_hash: null}
base: {design_doc, version, snapshot, content_hash, faithful_md, code}
target: null
approval: null                        approval: null
ledgers: {decisions, issues}          ledgers: {decisions, issues}
records: <change-record location>      records: null
extensions: []                        extensions: []
```

| Key | Meaning |
|---|---|
| `sl`, `kind`, `id`, `title`, `lang` | Schema version, document kind, ID, title, and display language |
| `status` | A display value determined from adoption status, confirmation decisions in the ledger, and open questions. Directly editing the value does not create a decision record |
| `base` (changes only) | A record that freezes the design documents and code before the change. Do not overwrite it |
| `target` (changes only) | The version and hash of the updated design document, and references to verification records. `null` before the change is applied |
| `reflected` (changes only) | A display value determined from the status of applying adopted items to the design documents |
| `baseline` (new development only) | A record of the first version and reviewed content. Identifies the baseline version for a revision |
| `approval` | An array of references to decision records, for example `decisions: [decisions#41]`. Decisions and approved versions may differ by item. This is an index into decision records, not the source of truth for approval |
| `ledgers`, `records` | Locations of the decision ledger, issue register, and change record, obtained from the integration configuration |
| `extensions` | IDs, versions, and definition references for the applied extension rules (Section 4.7) |

#### 4.6.3 Sections and tables

Every section is required, and the order is fixed. Section display names use the document language; tools identify sections by their corresponding keys. The section key identifies the table type. ID prefixes are used to check whether an ID is allowed in that table. Column order is also fixed, and column labels are checked against the mapping.

**Change specification**

| Section key | Display name | Table columns: keys |
|---|---|---|
| `purpose` | Purpose | — |
| `scope` | Scope and baseline | `id`, `ref`, `note` (source list `SRC-NN`) |
| `before` | Before | —; add provenance to the text |
| `changes` | Changes | `id`, `kind`, `content`, `provenance`, `source`, `adoption`, `status` |
| `unchanged` | Unchanged | — |
| `open` | Open questions | `id`, `content`, `affects`, `issue`, `state` |
| `acceptance` | Acceptance criteria | `id`, `changes`, `given`, `when`, `then`, `source`, `questions` |
| `reflection` | Reflection status | `id`, `design_doc`, `code`, `test`, `design_evidence`, `code_evidence`, `test_evidence` |

**New-development design document** (Stage B)

| Section key | Display name | Table columns: keys |
|---|---|---|
| `purpose` | Purpose and users | — |
| `scope` | Scope and sources | `id`, `ref`, `note` |
| `requirements` | Requirements | `id`, `kind`, `content`, `provenance`, `source`, `adoption`, `status` |
| `design` | Overall design | —; policies for approaches, data, authentication and authorization, and external integration. Extension rules define the structure |
| `behavior` | Detailed design | —; behavior by function. Extension rules define screen, API, and batch tables |
| `acceptance` | Acceptance criteria | `id`, `requirements`, `given`, `when`, `then`, `source`, `questions` |
| `open` | Open questions | `id`, `content`, `affects`, `issue`, `state` |
| `implementation` | Implementation status | `id`, `design`, `code`, `test`, `design_evidence`, `code_evidence`, `test_evidence` |

The differences are limited to the correspondence between `changes` and `requirements`, between `base`/`target` and `baseline`, and between change application status and implementation status. Sources, provenance, adoption status, confirmation status, acceptance criteria, and open questions use the same mechanisms.

In acceptance criteria, `source` contains only sources (`SRC-NN`). Unresolved questions are not a basis for expected results, so record them in `questions`.

#### 4.6.4 IDs, references, and fixed values

| ID | Format | Notes |
|---|---|---|
| Document | `CHG-YYYYMMDD-NN`, `DES-YYYYMMDD-NN` | Assigned by tools |
| Item within a document | `C-NN` (change item), `Q-NN` (open question), `AC-NN` (acceptance criterion), `SRC-NN` (source) | Unique within the document. From another document, use `CHG-20261011-01/C-02` |
| Requirements and design elements for new development | `FN-`, `BIZ-`, `RL-`, `EX-` (requirements); `SC-`, `EN-` (design) | Retain the v0.0.3 prefixes as defaults |
| Existing locations | `{doc}@{version} {locator}`, `{path}:{lines}@{commit}`, `decisions#{No}`, `records#{recordID}`, `request#{id}`, and the team's issue IDs | Check both location and version |

Items that reference multiple entries are retained internally as arrays. In table cells, separate references with commas; display `—` when there are no references.

| Key | Fixed values: display labels |
|---|---|
| `changes.kind` | `added` (Added), `modified` (Modified), `removed` (Removed) |
| `requirements.kind` | `function`, `rule`, `role`, `external`, `nonfunctional` |
| `provenance` | `stated` (Stated), `observed` (Observed), `inferred` (Inferred), `unverified` (Unverified) |
| `adoption` | `candidate` (Candidate), `adopted` (Adopted), `excluded` (Excluded) |
| `status` | `undecided` (Undecided), `tentative` (Tentative), `confirmed` (Confirmed) |
| `validity` (check results) | `valid` (Valid), `recheck` (Requires recheck) |
| Application to design documents | `not_reflected`, `requested`, `reflected`, `unverified` |
| Code | `pending`, `done`, `unverified` |
| Tests | `not_run`, `pass`, `fail`, `inconclusive`, `n_a` |
| `open.state` | `open`, `resolved`, `out_of_scope` |

Write provenance in the body using square brackets, for example `[stated SRC-02]`, `[observed SRC-04]`, `[inferred SRC-03: reason]`, or `[unverified Q-01]`. A reason is required for `inferred`. Distinguish `unverified` from a state in which the work is known not to have been performed (`not_reflected`, `pending`, or `not_run`).

#### 4.6.5 Checklist items

A checklist item is a question used to check what the specification contains. It is distinct from the schema that stores business-specification values (Section 4.7). Write the question in the document language; write attributes using keys and fixed values.

| Attribute | Content |
|---|---|
| `id`, `category`, `question` | `CK-{category}-NNN`, category (business rules / screens and APIs / data / batch and integration / operations), and the question |
| `applies_to`, `targets` | Applicable development types and technical subjects |
| `evidence` | Whether a source is required or observation is acceptable |
| `check_by` | `machine` (checking script) or `review` (AI review and people) |
| `required_at`, `severity` | Check stages at which the item is mandatory, and its importance |
| `origin`, `supersedes` | `base` / `org` / `project`, and the IDs of common items being omitted or replaced; a reason is required |

Each item contains one question. For a `check_by: machine` item, define the evaluation condition in Reference. The common definitions in Section 9.4 cannot be targets of `supersedes`.

#### 4.6.6 Division of machine-processing responsibilities

Tools parse a document in this order:

1. Read the metadata.
2. Map headings to section keys using the mapping.
3. Identify each section's tables by their column headings.
4. Parse each cell according to its type.
5. Check IDs, references, and statuses against the sources of truth.

Headings inside code blocks are not used to identify sections. Checking does not modify the document. Formatting and regeneration are separate operations that show their proposed differences. Hidden markers, such as HTML comments, are not mandatory, but teams that need them may define them in the integration configuration.

| Handled by machine checks | Meaning assessed through AI review and human review |
|---|---|
| Metadata, section and column mappings, types, fixed values, IDs, and references | Whether the body correctly represents the request's intent |
| Presence of a reason for an inference | Whether the reason justifies that inference |
| Relationships between expected results, sources, and open questions | Whether the sources support the expected results |
| Consistency between the subjects, versions, and results in evidence and the displayed status | Whether the implementation and tests adequately check the business conditions |

### 4.7 Business-specification extension schemas

The common management schema does not define business-specification content such as screen-field types and required conditions, API inputs and outputs, or batch rerun conditions. Subject-specific extension rules define these.

A checklist item asks whether the necessary conditions have been considered. An extension schema defines the fields used to record those conditions. Extension schemas define the `design` and `behavior` tables in new-development design documents and any change content that needs structure in change specifications.

Extension rules define `id`, `version`, `applies_to`, `targets`, and `item_prefix`, along with each property's type, allowed values, and required conditions (Reference Section 9.1). Record the applied rule's version and definition reference in metadata `extensions`. In the internal representation, use `change_refs`/`requirement_refs` and `source_refs` to relate the values recorded under the rule to common items. Extension rules must not redefine provenance or confirmation status.

Users see content organized according to the extension rules. For example, draft C-01 and C-02 from “Please limit each employee to five reservations per week,” and show the following after a human decision.

| Item | Aggregation unit | Period | Operations | Limit | Behavior when exceeded |
|---|---|---|---|---|---|
| Reservation count | Per employee | Week, starting Monday | Create and update | Per-employee setting: weeklyLimit, default 5 | Do not save; display ERR-週次上限 |

This table is not a separate source of truth from the change specification or design document. Even when a user edits through this table, the tool presents a proposed change to the source of truth. If a field cannot be extracted from natural language or the extracted result conflicts with the body, mark it `unverified`. Do not invent a value merely to satisfy a type constraint.

Not every extension-rule item needs to apply to every project. Start by structuring parts that are repeatedly compared or checked (Section 9.3). Stage A evaluates common management mechanisms in a real project. The scope of extension-schema application is decided in Stage B or later.

### 4.8 Evidence for decisions, change application, and verification

Tools generate change application status tables for changes and implementation status tables for new development from evidence, rather than asking people to fill them in manually. Use existing records—the decision ledger, change records, and test results—as evidence. Do not create an SL-specific execution ledger.

If existing records have no row IDs, select one of two approaches in the integration configuration: add an ID column or let the tool assign synthetic IDs (Reference Section 10.1). Adding an ID column is recommended.

| Evidence type | Reference | Recorded information |
|---|---|---|
| Decision | `decisions#41` | Affected C and AC items, approved specification and assumption versions, decision maker, and date/time |
| Application to design documents | `records#REF-01` | Destination version and location, reviewer, date/time, and target design-document hash |
| Implementation | `records#IMP-01` | Code commit, files and methods, and implementation-review record |
| Test | `records#TEST-01` | Run ID, individual test results, specification/code/build under test, executor, and date/time |

After human decisions, implementation, and testing, the displayed table may look like this.

| C | Design document | Code | Test | Design evidence | Code evidence | Test evidence |
|---|---|---|---|---|---|---|
| C-01 | Applied | Implemented | Pass | records#REF-01 | records#IMP-01 | records#TEST-01 |
| C-02 | Applied | Implemented | Pass | records#REF-02 | records#IMP-02 | records#TEST-01 |

Tools follow each C to its AC items, each AC to its test cases, and each test to its result and execution target to determine the displayed status for the C. Use `not_run` if any case is unexecuted and `fail` if any case failed. Use `inconclusive` when the result or target match cannot be determined, and `pass` only when all required cases succeeded.

An overall pass count or a list of changed files alone is not evidence. Each execution result must identify the specification and code it applies to. A successful result for old code must not be used as evidence for current code (Section 6.4).

This table alone does not determine project completion. The finish check may pass when valid decisions, resolution of open questions within the adopted scope, required design-document updates, individual test results, and existing completion conditions are all satisfied.

---

## 5. Common information model

### 5.1 Components

The following table classifies the information SL handles by meaning and role. It does not require a separate file or database named after each concept. The information may be mapped to and retained in existing documents or records.

| Concept | Meaning | Main information |
|---|---|---|
| Specification item | A unit for referencing and reviewing requirements, business rules, design elements, and similar content | ID, content, scope, sources, provenance, and references to decision records |
| Change set | A group of change items sharing a scope and baseline for one change effort | Change ID, purpose, baseline (base), exclusions, and change items |
| Change item | An addition, modification, or removal in the current change | C-ID, type, before and after content, subject, supporting basis, and adoption status |
| Source reference | Information used to locate the basis for a statement or decision | Material or record, version, location, and content-identifying information |
| Decision | A human decision to adopt, exclude, or confirm content | Reference to the source of truth, subject, content, decision maker and date/time, and version reviewed |
| Open question | An unresolved matter within the current scope | Q-ID, affected items, reference to the issue register, and status |
| Acceptance criterion | A condition for checking whether the specification is satisfied | AC-ID, target items, preconditions, action, expected result, and sources |
| Relationship | A relationship between two items, artifacts, or pieces of evidence | The two identifiers and relationship type: provides a basis / elaborates / changes / implements / verifies / confirms by decision |
| Evidence | A record establishing that application, review, or execution occurred | Subject, version or hash, result, executor or reviewer, and reference to the record |
| Applicable rule | A rule defining what must be described or checked | Applicability conditions, required information, checking method, and provenance |

### 5.2 Traceability from requirements to verification results

```
New development:
Business requirements → Design elements → Implementation locations
                              └→ Acceptance criteria → Test cases → Execution results

Changes to an existing system:
Request and decisions + Baseline before the change (base)
    → Change items → Updated design-document locations (target) → Implementation locations
          └→ Acceptance criteria → Test cases → Execution results
```

These relationships need not be one-to-one. The presence of a mapping alone does not establish that the description or implementation is sufficient. For example, even if a change item for update processing is linked to code, a separate check is needed to establish whether the code implements the required conditions and whether tests can detect omitted conditions.

---

## 6. Common management rules

### 6.1 Sources and provenance

Record sources and provenance for each statement so that its basis can be checked. Provenance has four categories (fixed values are listed in Section 4.6.4).

| Provenance label | Fixed value | Meaning | Caution |
|---|---|---|---|
| Stated | `stated` | The statement appears in an accepted source, such as a request, design document, recorded decision, or convention | The source's correctness and applicability to the current subject are not necessarily verified |
| Observed | `observed` | The current state was established from code, data, or execution results | The current state is not necessarily correct for the business |
| Inferred | `inferred` | AI supplied a candidate or interpretation based on existing information | Show the reason and referenced sources. Adoption is a separate decision |
| Unverified | `unverified` | The supporting basis has not been obtained, cannot be read, or cannot be interpreted | Create an open question when necessary |

Sources are not limited to one type. Requests, design documents, recorded decisions, code, conventions, and external specifications can all be sources. Record provenance regardless of whether AI or a person wrote the statement. When a person adopts inferred content, its provenance remains “inferred.” Verify adoption by referring to the decision in the ledger.

Source references must contain both location information and information identifying the version and content. People need location information to inspect the supporting material; tools need version and content identifiers to cross-check it. Location alone cannot identify relocation or content changes, while a hash alone does not let people inspect the supporting content.

Source existence and whether that source supports the statement are separate questions. Machine checks verify existence; AI review and people assess whether the source supports the statement.

### 6.2 Adoption and confirmation status

Record adoption status and confirmation status separately. Adoption status indicates whether an item is treated as a candidate, adopted, or excluded (`candidate` / `adopted` / `excluded`). Identify the applicable status from decision records. Separately, manage the state of the decision about the content using the following confirmation statuses.

| Display label | Fixed value | Meaning |
|---|---|---|
| Undecided | `undecided` | The content has not been decided, or required questions remain unresolved |
| Tentative | `tentative` | The decision is provisional, with conditions, deadlines, or other reasons for later review |
| Confirmed | `confirmed` | The content and assumptions are identifiable, and a human confirmation decision exists for them |

**“Tentative” describes the state of a decision; it does not authorize implementation.** Whether investigation or prototyping may proceed tentatively, and whether production implementation may start, is determined by the pre-implementation check conditions and harness conditions.

When investigation, design exploration, or prototyping proceeds tentatively, define its scope and conditions according to existing practices. A prototype must not be treated directly as a confirmed specification for production implementation. In Stage A, production implementation starts only when confirmation decisions for the adopted scope remain valid and the pre-implementation conditions are met (Section 7.2).

Content must not be confirmed solely because a source exists, AI recommends it, AI review marks it “supported,” tests pass, or work is complete. Even content stated in a primary source is not confirmed if its applicability to the current subject remains undecided.

Reuse an existing valid decision when it clearly applies to the current content. Multiple items may be reviewed and confirmed together; a separate conversation for each item is unnecessary.

### 6.3 Validity of confirmation

Distinguish the history of a decision from whether it applies to the current content. The latter is called “confirmation validity” and has two values.

| Display label | Fixed value | Meaning |
|---|---|---|
| Valid | `valid` | The reviewed content and assumptions apply to the current subject |
| Requires recheck | `recheck` | Content or assumptions changed, the decision was withdrawn, or the effect of a change cannot be established |

When a recheck is needed, retain the previous confirmation decision as history and display “Confirmed — requires recheck.” Do not delete the old decision and revert it to undecided. Checks determine whether that decision may be applied to the current implementation.

When a change affects only some items, do not require unrelated items to be reviewed again. If a shared assumption changes, review the items that depend on it. When the affected scope cannot be identified, state that explicitly and suspend application of the decision.

Display provenance and confirmation status as basic information for the user. When needed, also show recheck status, open questions, and outstanding application or verification work.

Checklist assessments (supported / insufficient / conflicting / out of scope) are managed in review records; issue status is managed in the issue register. Design-document application status (not applied / application requested / applied / unverified) is managed from application evidence. The harness manages execution status, and test results manage verification outcomes. Under the principle in Section 3.2, specification documents must not become independent records of these states.

### 6.4 Freezing versions and identifying reviewed content

A change specification records the baseline before the change (base) separately from the design document after the change (target).

| Record | Content | Handling |
|---|---|---|
| base | The design-document version before the change, snapshot hash, corresponding faithful Markdown, and code version investigated | Freeze it; do not overwrite it |
| target | The updated design-document version and hash, and application-verification records for each change item | Tools record it after the update. The mere existence of target does not mean the change has been applied |

A current design document differing from base is not, by itself, an error. If it matches target and an application-verification record exists, treat the update as normally applied. If it also differs from target, or includes changes outside the current scope, recheck decisions affected by those changes.

Identify versions using revision-history versions, commits, and content hashes, rather than relying only on file names or modification times.

**Whichever identification method is chosen, it must satisfy the following requirements.**

| Requirement | Meaning |
|---|---|
| Approved content can be reconstructed | The change specification at the time of the human confirmation decision—change items, acceptance criteria, and exclusions—and the decision's assumptions—versions of requests, design documents, and decisions—can be reconstructed later. Include content that was uncommitted or untracked at approval time |
| Changes after approval can be detected | Differences between the approved and current content can be detected at the relevant item level. Do not require reapproval for display or application-status updates that do not affect the decision |
| The subject of a result can be identified | The specification and code to which a test or build result applies can be identified. Do not use a successful result for old code as verification evidence for current code |

Separate identification requirements from the method used to satisfy them. If the approved content matches committed content, recording the commit and target locations is sufficient. If it includes uncommitted or untracked content, retain a record that can reconstruct the entire approved target, such as snapshots of the files at approval time or a baseline version with a saved diff.

SL's common conventions do not require users to use Git or make a commit for every approval. Avoid retaining only a hash while losing the original content. Tools manage the records needed for identification. Define comparison methods in the integration configuration and tool-internal specification.

Stage A uses existing version control. Commit the change specification before approval, then record the approval-time commit and affected C and AC items in the decision record (`decisions#NN`). Metadata `approval.decisions` is an index for locating that record.

A person makes the commit, or runs a script prepared by AI. Identify code by commit ID, and also record a diff if there are uncommitted changes.

Link test results to existing records that identify the specification, code, and executed build or artifact. Use the commit ID when it fully represents the target. If the target includes uncommitted or untracked content, also reference snapshots or saved diffs.

Results from a target that changed during execution, or from a target different from the current one, must not serve as evidence of success for the current target. Treat result-file timestamps as supporting information; do not establish target identity from timestamps alone. Section 4.8 gives an example.

Custom normalization and hashing are possible ways to meet these requirements, but they are not adopted in Stage A. Identifying the reviewed content is itself required. Evaluate the identification method and its workload.

### 6.5 IDs

| ID | Subject | Assignment |
|---|---|---|
| CHG-date-NN | A change effort: change set | Assigned by tools |
| C-NN / Q-NN / AC-NN | Change items / open questions / acceptance criteria | Unique within the change specification. Combine with the change ID for references from other documents, for example `CHG-20261011-01/C-01` |
| Existing IDs and locations | Design-document item numbers, sheets and cells, issue IDs, and ledger numbers | Reference the existing identifiers directly. For documents with only fixed cells or headings, use external references that include the version and location |
| BIZ- / FN- / SC- / EN- / EX- / RL- | Requirements, functions, screens, entities, external integrations, and roles in new development | Optional under extension rules for new development (Section 4.3) |

IDs are assigned automatically. Automatic ID assignment must not cause the statement's provenance to be treated as inferred. The ID assignment method and statement provenance are separate matters.

Maintain each ID's definition in one place. Later documents and stages reference that definition rather than duplicating it. After confirmation, do not delete or reuse an ID in a way that associates a past decision with different content.

### 6.6 Traceability and evidence

| Relationship | Example evidence |
|---|---|
| Change item → design document | target version, sheet and cell, and application-verification record |
| Change item → implementation | A row in the changed-file list, and the file and method |
| Acceptance criterion → test | Test-case ID, result file, and identification of the code to which the result applies (Section 6.4) |
| Acceptance criterion → source for expected results | References to requests, design documents, decisions, message catalogs, or other sources |

Evidence includes the subject, version or hash, result, executor or reviewer, and reference to the record (Section 5.1). A changed-file list and an overall pass count cannot substitute for this evidence.

Relationships may be one-to-many or many-to-many. Not all elements need to be linked to every other element; start with the relationships that will be used.

Distinguish the presence of a recorded relationship from its substantive correctness. AI review, human review, and test execution establish substantive correctness.

When evidence is missing, display “Unverified,” not “Complete.” Display unexecuted, inconclusive, and out-of-scope results separately from success.

---

## 7. Checks and reviews

### 7.1 Four verification methods

| Method | What it can establish | What it cannot establish on its own |
|---|---|---|
| Machine checks | Format, ID uniqueness, source existence, version consistency, references to decision records, changes after approval, presence of relationships, and conflicts between open questions and confirmation status | Sufficiency of business requirements, interpretation of sources, and whether the implementation substantively satisfies the conditions |
| AI review | Omissions from the request; missing creation, update, API, or batch paths; whether sources support statements; whether exclusions are appropriate; and whether tests can detect an incorrect implementation | Replacement of human adoption or confirmation decisions, or a guarantee that nothing is missed |
| Verification by execution | Code, screen, and integration behavior under specified conditions | Correctness of the expected results themselves, or untested conditions |
| Human decisions | Business intent, adoption, boundary conditions, exclusions, and acceptance | A guarantee of successful results without checking implementation or execution |

For example, machine checks can detect an AC with no source. However, even an existing reference may not support the expected result. Assessing that requires a review that interprets meaning.

Record what was checked and by which method. Treat “a reference exists,” “AI review passed,” “the test passed,” and “the business accepted it” as separate facts.

SL defines checklist items. Checking scripts handle items that can be assessed mechanically; AI review handles items requiring interpretation. The harness manages how those scripts and reviews are run.

### 7.2 Three stages of checking

Define separate check conditions for drafting, before implementation, and before completion. These are categories of check conditions; they do not replace overall workflow state management with an SL-specific system.

| Stage | Allowed states | States that cause failure |
|---|---|---|
| Drafting (draft) | Explicit open questions, unconfirmed expected results, work not started, and tests not run | Unreadable content, ID collisions, or broken structure |
| Before implementation (ready) | Exclusions supported by decisions, and open questions within that excluded scope | Unapproved adopted items, items requiring recheck, unresolved questions within the adopted scope, missing expected results or sources, missing post-application records, or unmet local prerequisites |
| Before completion (finish) | Exclusions supported by a basis and decision, and verification not required in the current stage | Unverified application status, stale results, or unmet existing completion conditions |

Passing a drafting check does not authorize implementation or establish completion. “Before implementation” (ready) in the table means before production implementation. Investigation and prototyping under tentative decisions follow the scope and conditions in Section 6.2.

If a mandatory condition is unmet in a pre-implementation or pre-completion check, do not downgrade the problem to a warning and proceed. Determine the check scope from valid adoption decisions. Do not make a check pass by excluding problematic items from the invocation arguments.

Checks are read-only. They must not automatically mark ledger or document states as confirmed or resolved.

### 7.3 Checklists

A checklist is a list of questions used to identify missing information during drafting. Select items for the subject; they are not uniformly mandatory for every project.

| Subject | Examples of what to check |
|---|---|
| Business rules | Applicability conditions, exceptions, closing/cancellation/reprocessing, and date or amount boundaries |
| Screens and APIs | Creation and updates, paths that do not use the screen, permissions, input errors, and concurrent updates |
| Data | Existing data, NULL and defaults, history, migration, and consistency conditions |
| Batch processing and integration | Reruns, duplicate execution, partial failures, timeouts, and external-system specifications |
| Operations | Audit records, performance conditions, monitoring, and failure recovery |

If performance, auditing, recovery, or similar matters are business requirements, do not exclude them from the specification on the grounds that they are implementation details.

Each checklist item contains one question. Do not combine several questions into one item. Define applicability conditions, the required supporting basis, the verification method (machine checking or meaning-based review), and the check stages at which the item is mandatory.

### 7.4 When code and tests share the same incorrect interpretation

If the same AI creates code and tests from the same incorrect interpretation, the code's behavior and the tests' expected results can agree, allowing tests to pass despite the error.

To address this, link expected results to sources in requests, design documents, and decisions. People review important business conditions. Where possible, use existing acceptance cases, approved input/output examples, and independent regression tests. Review by another AI alone must not be treated as a guarantee of correctness.

---

## 8. Workflow

### 8.1 Changes to an existing system: Stage A example

```
Request and materials
  ↓ Draft (AI): establish the before state, change items,
    candidate open questions, and acceptance criteria
Change specification (draft)
  ↓ Decide (people): adoption and open questions
    → commit before approval → record decisions in the ledger
  ↓ Update design documents (existing procedures)
    → record target → pre-implementation check (ready)
  ↓ Implement and test (harness)
  ↓ Display application status (AI) → pre-completion check (finish)
Report and accept (people)
```

The order of design-document updates and implementation follows the team's conventions. If a team implements only changes marked in red, update the design documents before implementation. Creating a change specification first does not remove the need to update the design documents.

If a design-document update is waiting on another person, display it as outstanding work with “Application requested.” Do not treat the project as complete in this state.

### 8.2 New development: Stage B

```
High-level requirements (request, RFP, conversation)
  ↓ Draft (AI): objectives, functions and business rules, data,
    behavior, acceptance criteria, and candidate open questions
New-development design document (draft)
  ↓ Decide (people) → record in the ledger → pre-implementation check
  ↓ Implement and test
  ↓ Check application status → pre-completion check
```

Start from the information already available, such as high-level requirements, requirements definitions, outline designs, or a brief.

Do not ask the user to select a process stage or input format. AI reviews the materials and environment and identifies where to start. It then requests only the human decisions that are needed. A brief is an optional input or intermediate document; a separate brief format is not mandatory.

### 8.3 AI drafting and human decisions and revisions

At every stage, people decide on AI-drafted content and revise it as needed. Later stages use content that has passed through human decisions and any required revisions. Do not pass AI output directly to the next stage.

Follow these principles when seeking human confirmation:

- Present differences and open questions together so that they can be decided as a group, rather than requesting a separate conversation for each item.
- Reuse an existing human response when it clearly identifies its subject. Do not ask about the same content repeatedly.
- Recheck only content or assumptions that have newly changed. Do not revert unrelated items to undecided.
- Match the content and amount of review to the change's impact. Use different checklist items for a wording-only display change and a change to business-data state transitions.

### 8.4 Harness integration principles

- SL does not manage the workflow. Add drafting, checking, and application-status display to the existing change process.
- Record decision storage locations, the issue register, completion conditions, and execution-result formats in the integration configuration (Section 3.3).
- SL does not change confirmation status merely because the harness returns “complete.” Completion of a response or tool execution is not the same as business completion.
- Passing the change-specification location and the lists of change items and acceptance criteria to the harness is sufficient. The entire design documentation does not need to be passed each time. If only part of a source is extracted, identify it as an excerpt and keep the original accessible.
- Do not treat work instructions found in materials as permission to execute tools. Follow the existing harness and organizational practices for permissions.
- On interruption and resumption, the harness reads the handover information and uses the drafting check to cross-check sources of truth, decisions, current artifacts, and results. Do not treat incomplete execution or missing results as success. Do not append the same decision more than once.

---

## 9. Extension rules and reuse of organizational knowledge

### 9.1 Terms

| Term | Meaning |
|---|---|
| Checklist item | A question used to identify missing information during drafting (Section 7.3) |
| Description rule | A rule defining required information for a subject, the items in each section, and types and constraints where needed. Section 4.7 gives an extension-schema example |
| Extension rules | Checklist items and description rules grouped by technical subject, business domain, or organization |
| Common definitions | Definitions shared across SL that extension rules may not change (Section 9.4) |

### 9.2 Categories and combinations of extension rules

| Category | Examples |
|---|---|
| Development type | New development, changes to existing systems |
| Technical subject | Screens, APIs, batch processing, data migration, external integration |
| Business domain | Approvals, reservations, accounting integration, inspections |
| Organization or project | Terminology, design conventions, and checklist items based on past defects |

Do not duplicate the entire rule set for every combination. Keep the common part minimal and add only the categories needed.

Extension rules can also be used when design documents already exist. For example, they can map the existing section structure or domain-specific checklist items to the information handled by SL.

### 9.3 Defining extension rules incrementally

1. Add natural-language checklist items and examples.
2. Define attributes and relationships when the same concepts recur and doing so helps comparison or checking.
3. Define strict types and constraints only for parts actually used in automated checks or code generation.

AI may inspect the environment and select candidate extension rules. Display the selected candidates concisely and let the user change the selection.

If the applicable rules cannot be determined, start with the common part and record the checklist items that have not been assessed. Do not invent content to fill required fields that do not fit the subject. Selecting a rule by inference does not confirm the business requirements associated with it.

### 9.4 Immutable common definitions and adjustable items

Extension rules may adjust checklist items and detailed description fields. They may not change SL's common definitions. This distinction prevents the same term or state from having different meanings in different projects.

| Common definitions that cannot be changed | Items that may be omitted or adjusted |
|---|---|
| Confirmation requires a human decision about the content | Checklist items unnecessary for the business, with the reason for omission recorded |
| Distinguish stated, observed, inferred, and unverified; give reasons for inferences | Subject-specific required information and detailed fields within each section |
| Do not fill in open questions without a decision | Granularity of acceptance criteria |
| Display missing evidence as unverified, not complete | Scope of items decided together |
| Distinguish base from target | Additional description rules and examples |
| Distinguish source existence from whether it supports the statement | Terminology and notation |

For example, extension rules must not equate test success with confirmation, label an unknown source as stated, or allow a pre-completion check to pass with unresolved questions.

Rules have three layers: Base, Organization, and Project. Higher-level rules may add or adjust checklist items in lower-level rules. If organizational policy considers a common checklist item unnecessary and omits it, record the reason in the organization's extension rules. Do not ignore it without recording a reason.

Display the definitions actually applied. A “last setting wins” process must not conceal the disabling of required items or conflicts between definitions.

Version extension rules. Changing a rule must not automatically apply the change unconditionally to projects already using it (Stage D). Even in Stage A, record where the applied rules are stored.

### 9.5 Ownership and distribution of extension rules

Publish and share the common conventions. Organizational extension rules belong to the adopting organization; system integrators may also offer them as products. Project-specific extension rules serve as material for reuse in similar projects.

---

## 10. Questions to evaluate after Stage A

This document is a draft prepared before Stage A. Evaluate the following using Stage A results and revise this document accordingly. The final row, covering new-development design documents, is evaluated in Stage B.

| Question | What to evaluate | Relevant sections |
|---|---|---|
| Do users understand the four provenance categories? | Could users distinguish observed from inferred? Were Japanese display labels correctly converted to internal fixed values? Should less information be displayed? | 6.1, 4.6 |
| Can adoption and confirmation status be managed separately? | Could both be identified through a single reference column in the ledger? Are the document-level status rules appropriate? | 6.2, 4.4 |
| Does the base/target distinction fit the team's update process? | How often were normal updates incorrectly reported as anomalies? Did people find recording target burdensome? | 6.4 |
| Can approved content be reconstructed and compared? | Was the commit-before-approval practice followed? If approvals included uncommitted or untracked content, could it be reconstructed? Did display-only updates trigger unnecessary reapproval? | 6.4, 4.4 |
| Can execution targets be identified with little effort? | Could the execution target be reproduced, including uncommitted and untracked content? Were changed targets and stale results detected? What were the data-collection burden and false-positive rate? | 4.8, 6.4, 6.6 |
| Can tentative status be distinguished from authorization to start production implementation? | Was necessary investigation or prototyping blocked? Did unconfirmed content proceed to production implementation? | 6.2, 7.2 |
| Do the three check stages fit actual work? | How often was drafting blocked? How often did a pre-implementation check pass something that should have failed? | 7.2 |
| What was the effect of limiting check scope? | Did source and traceability checks detect syntax and coverage problems? Where they did not, what information should be structured and added to checks? | 4.5, 7.1 |
| Can documents be parsed unambiguously? | Were empty tables, column aliases, change-item tables, and application-status tables correctly identified? Were unrecognized documents interpreted by guesswork and allowed to pass? | 4.6 |
| Can displayed status be verified from evidence? | Were unsupported confirmed/pass labels, missing evidence, and results for different targets detected? Did checking leave documents unchanged? | 4.6, 4.8 |
| How much checklist coverage is needed? | Which items helped during drafting and which did not? What information needed to be structured as an extension schema? | 4.7, 7.3, 9 |
| Is the scope of immutable common definitions appropriate? | Did any common definition conflict with the team's conventions? | 9.4 |
| Were deliberation records needed as sources? | Did the ledger alone provide enough information? Which decisions required references to deliberation records? | 4.1 |
| Are the information categories for new-development design documents appropriate? | Does the same mechanism used for change specifications work in a small new-development project in Stage B? | 4.3, 8.2 |

---

## 11. Related documents

| Document | Audience | Purpose |
|---|---|---|
| SL_v0_0_1_concepts.en.md (this document) | People deciding whether to adopt SL, and designers | Explains the basic policy, design principles, division of responsibility, document structure, information model, common management rules, checks, and workflow |
| Rollout plan (not included in this repository) | Implementers | Defines Stage A rollout, harness integration, and evaluation. Schema definitions follow Reference |
| SL_v0_0_1_guide.en.md (not yet written) | Practitioners | Explains how to request drafts, edit the body, record decisions, and verify application of changes |
| SL_v0_0_1_reference.en.md | Users looking up specific definitions, and implementers | Provides the formal schema definitions: all metadata keys and required conditions, sections and columns, ID and reference formats, fixed-value/display-label mappings, examples, checklist attributes, extension-schema definitions, evidence references, parsing order, correspondence with checks, and integration-configuration items |
| Tool-internal specification (not yet written) | Implementers and AI | Defines source cross-checking methods, check evaluation rules, display-value rules, and internal representation |
| Integration configuration (per project) | Implementers and AI | Records the items in Section 3.3 |
| Checklists and extension rules | People and AI | Define questions and description rules by technical subject, business domain, and organization |

### Relationship to Diátaxis

| Category | Documents |
|---|---|
| Explanation | concepts.md |
| How-to | guide.md (not yet written), rollout plan |
| Reference | reference.md, tool-internal specification |
| Tutorial | Anonymized teaching material from Stage A project records, to be created in Stage B or later |
