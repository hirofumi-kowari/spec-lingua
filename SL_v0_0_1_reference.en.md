# SpecLingua v0.0.1 Reference

**Date**: 2026-10-11
**Status**: Draft
**Applicable version**: SpecLingua v0.0.1
**Concept document**: `SL_v0_0_1_concepts.en.md` (called “Concepts” below)

[Japanese original](SL_v0_0_1_reference.ja.md)

This English translation preserves the definitions and requirements of the Japanese original. Example prose and display labels are translated; schema keys, fixed values, IDs, paths, and external location references retain their original notation. Examples with translated headings use `lang: en`. Japanese labels remain where a table explicitly defines the Japanese mapping.

This document defines SpecLingua documents and machine processing item by item. Concepts describes the rationale and design principles. The rollout plan, which is not included in this repository, covers deployment procedures and integration with a team's existing systems. If schema definitions differ between documents, this document takes precedence.

Stage A means a trial involving one real change to an existing system. Stage B extends the work to new development and changes in which design documents are revised first. Change specifications (`kind: change`) are used in Stage A. New-development design documents (`kind: design`) are a proposal pending finalization in Stage B. Their definitions here demonstrate how the mechanisms used for change specifications can also apply to new development.

All paths, IDs, commits, decisions, and execution results in this document are fictional examples.

---

## 1. Language and display rules

| Category | Rule |
|---|---|
| Keys in internal representations and metadata | Lowercase ASCII letters and `_` |
| Status, type, and provenance values | Fixed English values (Section 5) |
| IDs | Uppercase ASCII letters, digits, and the separator `-` |
| Headings and column labels | Display names registered in the mapping selected by `lang` |
| Status, type, and provenance in body tables | Fixed values or mapped display labels. Display labels are converted to the corresponding fixed values internally |
| Body text, reasons, questions, and business values | The document language |
| File names, sheet names, and existing item numbers | Retain the names used by the team |

Use one mapping file per language to record heading, column, and fixed-value display labels. An item may have multiple aliases.

```yaml
lang: en
sections:
  purpose: [Purpose of the change, Purpose]
  scope: [Scope and baseline]
  before: [Before]
  changes: [Changes, Change details]
  unchanged: [Unchanged, Out of scope]
  open: [Open questions]
  acceptance: [Acceptance criteria, Acceptance conditions]
  reflection: [Reflection status]
columns:
  changes: [ID, Type, Content, Provenance, Sources, Adoption, Status]
  open: [ID, Content, Affects, Issue, Status]
  acceptance: [ID, Corresponds to, Given, When, Then, Sources, Related questions]
  reflection: [C, Design document, Code, Test, Design evidence, Code evidence, Test evidence]
  scope: [ID, Reference, Notes]
terms:
  confirmed: Confirmed
  tentative: Tentative
  undecided: Undecided
  stated: Stated
  observed: Observed
  inferred: Inferred
  unverified: Unverified
  added: Added
  modified: Modified
  removed: Removed
  candidate: Candidate
  adopted: Adopted
  excluded: Excluded
```

Checks report headings or column labels missing from the mapping as “unrecognized.” Checking does not modify the document. Display-label corrections and alias registration occur during drafting or formatting. Users do not need to memorize fixed values.

---

## 2. Metadata

Record metadata as YAML at the beginning of the document. Tools generate it; users do not edit it. If a check detects manual edits or a discrepancy with the source of truth, it only reports the difference. Regeneration is a separate update operation. That operation must not overwrite a frozen `base` or approved content with current content.

### 2.1 Change specification (kind: change)

```yaml
---
sl: "0.1"
kind: change
id: CHG-20261011-01
title: Add a weekly reservation limit
lang: en
status: undecided
reflected: not_reflected
base:
  design_doc: docs/design/reservation.xlsx
  version: rev.3
  snapshot: snapshots/design/reservation-rev3.xlsx
  content_hash: "sha256:<snapshot hash>"
  faithful_md: docs/derived/reservation-rev3.md
  code:
    commit: a1b2c3d
    uncommitted: null
target: null
approval: null
ledgers:
  decisions: ledgers/decisions.md
  issues: records/CHG-20261011-01.md
records: records/CHG-20261011-01.md
extensions: []
---
```

| Key | Required condition | Meaning |
|---|---|---|
| `sl` | Always required | Schema version, separate from the body revision or product version |
| `kind` | Always required | `change` |
| `id` | Always required | Change-set ID (Section 3) |
| `title` | Always required | Title assigned by the tool from the request |
| `lang` | Always required | Display language, used to select the mapping |
| `status` | Always required | Display value determined from adoption status, confirmation decisions in the ledger, and open questions within scope (Section 2.3) |
| `reflected` | Always required | Display value determined from the status of applying adopted change items to design documents (Section 2.3) |
| `base` | Always required | Record freezing the design documents and code before the change. Do not overwrite it after freezing. If the baseline cannot yet be verified, identify what is missing and do not allow ready to pass |
| `base.design_doc` | Always required | Target design-document path |
| `base.version` | Always required | Revision-history version or commit |
| `base.snapshot` | Always required | Snapshot path |
| `base.content_hash` | Always required | Snapshot hash |
| `base.faithful_md` | Always required | Faithful Markdown created from the snapshot |
| `base.code.commit` | Always required | Commit of the code investigated |
| `base.code.uncommitted` | When uncommitted changes exist | Reference to a snapshot or saved diff. `null` when the commit alone represents the entire target |
| `target` | `null` before application | Post-application `design_doc`, `version`, `content_hash`, `faithful_md`, and the array of verification-record references `confirmation` |
| `approval` | `null` before approval | `decisions`, an array of references to decision records; for example `decisions: [decisions#41]`. The decision records are the source of truth; this field is a reference index |
| `ledgers.decisions` | Always required | Decision-ledger location, obtained from the integration configuration |
| `ledgers.issues` | Always required | Issue-register location, obtained from the integration configuration |
| `records` | When referencing evidence | Location of the existing change record. It does not specify creation of a new execution ledger |
| `extensions` | Always required; `[]` if none apply | `id`, `version`, and `ref` of applied extension rules (Section 9) |

Every decision record referenced by `approval.decisions` must identify the affected C and AC items, approved specification and assumption versions, decision maker, and date/time. Decisions and approved versions may differ by item. Approval of the entire document need not be tied to a single commit.

### 2.2 New-development design document (kind: design; finalized in Stage B)

```yaml
---
sl: "0.1"
kind: design
id: DES-20261011-01
title: Employee reservation management
lang: en
status: undecided
baseline:
  version: "0.1"
  content_hash: null
approval: null
ledgers:
  decisions: ledgers/decisions.md
  issues: ledgers/issues.md
records: null
extensions: []
---
```

| Key | Required condition | Meaning |
|---|---|---|
| `kind` | Always required | `design` |
| `id` | Always required | Design-document ID (Section 3) |
| `baseline` | Always required | A record of the first version and reviewed content. Identifies the baseline version for revisions. Do not record `base`/`target` |
| `reflected` | Not present | New-development design documents have no separate source-of-truth document to update, so this field is not used. Show implementation and test status in the implementation status table (Section 4.2) |
| Other fields | Same as for change specifications | `sl`, `title`, `lang`, `status`, `approval`, `ledgers`, `records`, `extensions` |

### 2.3 Deriving display values

Determine `status` according to these conditions:

- Use `undecided` when open questions exist within the adopted scope.
- Use `tentative` when there are no open questions but a tentative decision exists.
- Use `confirmed` when every adopted item has a valid confirmation decision.
- A draft document with no adopted items yet is `undecided`.

Excluded items require a decision to exclude them. Check decision validity separately (Section 5, `validity`). A display value of `confirmed` alone must not make ready or finish pass.

Use `reflected` only for change specifications. Determine its display value according to these conditions:

- Use `reflected` when evidence verifies application of all adopted items.
- Use `requested` when all outstanding items have been requested.
- Use `not_reflected` when any item remains unapplied.
- Use `unverified` when missing or conflicting records prevent a determination of whether the changes have been applied.
- Use `not_reflected` when there are no adopted items yet.

---

## 3. IDs and references

### 3.1 ID formats

| Subject | Format | Assignment |
|---|---|---|
| Change set | `CHG-YYYYMMDD-NN` | Tools |
| New-development design document | `DES-YYYYMMDD-NN` | Tools |
| Change item / open question / acceptance criterion | `C-NN` / `Q-NN` / `AC-NN` | Tools; unique within the document |
| Source | `SRC-NN` | Tools; defined in the source list under `scope` |
| Requirements in new-development design documents | `FN-{category}{sequence}` (function), `BIZ-{category}{sequence}` (business rule), `RL-{name}` (role), `EX-{name}` (external integration) | Tools; default prefixes (finalized in Stage B) |
| Design elements in new-development design documents | `SC-{category}{sequence}` (screen), `EN-{sequence}` (entity) | Same as above |
| Checklist item | `CK-{category}-NNN` | Extension-rule author |
| Extension-rule item | `{rule item_prefix}-NN` | Tools; unique within the rule's namespace |

The numbering method may be changed through integration configuration, but prefixes and separators remain unchanged. After confirmation, do not delete or reuse IDs in a way that associates past decisions with different content.

### 3.2 Reference formats

| Subject | Format | Example |
|---|---|---|
| Item in another document | `{documentID}/{itemID}` | `CHG-20261011-01/C-02` |
| Location in a design document | `{doc}@{version} {locator}`. The `locator` follows the team's format: `sheet!range` for Excel, `#heading` for Markdown, or `§number` for Word | `base.design_doc@rev.3 入力チェック!B12:B18` |
| Code | `{path}:{lines}@{commit}`. For uncommitted changes, reference snapshots or saved diffs as well as the baseline commit | `src/reservations/reservations.service.ts:120-168@a1b2c3d` |
| Decision in the ledger | `decisions#{No}` | `decisions#41` |
| Issue | The team's issue ID | `ISS-20261011-01` |
| Request | `request#{id}`. Resolve the request-body location through integration configuration | `request#REQ-01` |
| Application or execution evidence | `records#{recordID}` | `records#REF-01` |
| Extension-rule item | `{documentID}/{ruleID}/{itemID}` | `CHG-20261011-01/count_limit/LM-01` |

`base.design_doc`, `decisions`, and `records` refer to the destinations specified in metadata. Cross-check the target version as well as the referenced location.

### 3.3 Arrays and empty values

Fields that can reference multiple entries are always arrays internally. In a Markdown cell, write the ID directly for one reference. For several references, separate them with commas, as in `C-01, C-02`. Display `—` for no references and represent it internally as an empty array. Reference IDs themselves must not contain commas. This is a reference-format rule; it does not apply to free-form prose.

---

## 4. Sections and tables

### 4.1 Change specification

| Section key | Display name (ja) | Display name (en) | Table column keys: fixed order |
|---|---|---|---|
| `purpose` | 改修目的 | Purpose | No table |
| `scope` | 対象と基準 | Scope and baseline | `id`, `ref`, `note` |
| `before` | 変更前 | Before | No table; add provenance to the text |
| `changes` | 変更後 | Changes | `id`, `kind`, `content`, `provenance`, `source`, `adoption`, `status` |
| `unchanged` | 変更しない範囲 | Unchanged | No table |
| `open` | 未決事項 | Open questions | `id`, `content`, `affects`, `issue`, `state` |
| `acceptance` | 検証条件 | Acceptance criteria | `id`, `changes`, `given`, `when`, `then`, `source`, `questions` |
| `reflection` | 反映状況 | Reflection status | `id`, `design_doc`, `code`, `test`, `design_evidence`, `code_evidence`, `test_evidence` |

All eight sections are required in the listed order. Keep headings even for empty sections. The section key identifies the table type. ID prefixes check whether an ID is allowed in that table. Both the change-item table and the application-status table contain C-IDs, so prefixes alone cannot distinguish table types.

The columns have the following meanings.

| Column | Meaning |
|---|---|
| `source` | Array of SRC-IDs. Do not record Q-IDs: questions are not a basis for expected results |
| `questions` | Array of Q-IDs. When an expected result is undecided, write `unverified` in `then` and the related Q items in this column |
| `affects` | Array of affected C and AC items |
| `issue` | ID in the issue register |
| `design_evidence` and related columns | Arrays of `records#…` references. Do not mix them with status values in the same cell |

### 4.2 New-development design document: finalized in Stage B

| Section key | Display name (ja; English translation in parentheses) | Table column keys: fixed order |
|---|---|---|
| `purpose` | 目的と利用者 (Purpose and users) | No table |
| `scope` | 対象範囲と出典 (Scope and sources) | `id`, `ref`, `note` |
| `requirements` | 要件 (Requirements) | `id`, `kind`, `content`, `provenance`, `source`, `adoption`, `status` |
| `design` | 全体設計 (Overall design) | No table; describe policies for approaches, data, authentication and authorization, and external integration in the body and extension rules |
| `behavior` | 個別設計 (Detailed design) | Per function; describe screen, API, and batch behavior in extension-rule tables |
| `acceptance` | 検証条件 (Acceptance criteria) | `id`, `requirements`, `given`, `when`, `then`, `source`, `questions` |
| `open` | 未決事項 (Open questions) | `id`, `content`, `affects`, `issue`, `state` |
| `implementation` | 実装状況 (Implementation status) | `id`, `design`, `code`, `test`, `design_evidence`, `code_evidence`, `test_evidence` |

Set `requirements.kind` to one of `function` / `rule` / `role` / `external` / `nonfunctional`. `implementation` may remain empty until implementation starts. Subject-specific extension rules define the table structures for `design` and `behavior` (Section 9).

---

## 5. Fixed values

| Key | Values: English display labels |
|---|---|
| `kind` (document) | `change`, `design` |
| `changes.kind` | `added` (Added), `modified` (Modified), `removed` (Removed) |
| `requirements.kind` | `function` (Function), `rule` (Business rule), `role` (Role), `external` (External integration), `nonfunctional` (Nonfunctional) |
| `provenance` | `stated` (Stated), `observed` (Observed), `inferred` (Inferred), `unverified` (Unverified) |
| `adoption` | `candidate` (Candidate), `adopted` (Adopted), `excluded` (Excluded) |
| `status` | `undecided` (Undecided), `tentative` (Tentative), `confirmed` (Confirmed) |
| `validity` (check results only) | `valid` (Valid), `recheck` (Requires recheck) |
| `reflected`, `reflection.design_doc` | `not_reflected` (Not applied), `requested` (Application requested), `reflected` (Applied), `unverified` (Unverified) |
| `reflection.code`, `implementation.code` | `pending` (Not started), `done` (Implemented), `unverified` (Unverified) |
| `reflection.test`, `implementation.test` | `not_run` (Not run), `pass` (Pass), `fail` (Fail), `inconclusive` (Inconclusive), `n_a` (Not applicable) |
| `open.state` | `open` (Open), `resolved` (Resolved), `out_of_scope` (Out of scope) |

`removed` expresses the intention to retire a business definition or behavior. It does not instruct physical deletion of files or data.

---

## 6. Cell and body syntax

### 6.1 Provenance cells

A provenance cell may use `inferred: reason` or `Inferred: reason`. Parse the text before the first colon as the fixed value and the text after it as the reason. Internally, store them separately as `provenance: inferred` and `reason`. A nonempty reason is required for `inferred`.

### 6.2 Provenance in body text

Write provenance in square brackets. A mapped display label may replace the fixed value. Parse references and reasons separately.

```
Creation allows saving without a deadline. [stated SRC-02]
The investigated code behaves the same way. [observed SRC-04]
Apply the same condition to updates. [inferred SRC-03: Creation and updates refer to the same input-check table]
It is unknown whether a past date may be used as the deadline. [unverified Q-01]
```

### 6.3 Status cells

A status column displays a state determined from the source of truth. A person changing a cell to “Confirmed” or “Pass” does not provide approval or verification evidence. Checks report discrepancies with the ledger or execution records.

Use `unverified` when evidence cannot establish whether the change was applied. Distinguish this from a state in which work is known not to have been performed (`not_reflected`, `pending`, or `not_run`).

### 6.4 Omissions

If omitted text leaves information required for machine checks missing, do not fill the gap by inference. Mark the relevant part `unverified` and link it to an open question.

---

## 7. Examples

### 7.1 Change specification: drafting

The following body follows the metadata in Section 2.1. At this stage, applicability to updates and the start of the week are undecided, so ready does not pass.

```markdown
# CHG-20261011-01 Add a weekly reservation limit

## Purpose
Limit each employee to five reservations per week to prevent reservations from being concentrated among particular employees. [stated SRC-01]

## Scope and baseline
| ID | Reference | Notes |
|---|---|---|
| SRC-01 | request#REQ-01 | Request for a weekly limit |
| SRC-02 | base.design_doc@rev.3 入力チェック!B12:B18 | Input checks for creation |
| SRC-03 | base.design_doc@rev.3 入力チェック!B30:B36 | Input checks for updates |
| SRC-04 | src/reservations/reservations.service.ts:120-168@a1b2c3d | createReservation / updateReservation |
| SRC-05 | base.design_doc@rev.3 メッセージ一覧!B88 | Message text for ERR-週次上限 |

## Before
Creation and updates both allow saving regardless of the number of reservations in the same week. [stated SRC-02 SRC-03]
The investigated code behaves the same way. [observed SRC-04]

## Changes
| ID | Type | Content | Provenance | Sources | Adoption | Status |
|---|---|---|---|---|---|---|
| C-01 | Modified | During creation, do not save if the employee's reservation count for the current week has reached the limit (weeklyLimit, default 5) | Stated | SRC-01 | Candidate | Undecided |
| C-02 | Modified | Apply the same condition to updates that move the usage date to a different week | Inferred: Creation and updates refer to the same input-check table | SRC-03 | Candidate | Undecided |

## Unchanged
Do not change input conditions other than the weekly limit.

## Open questions
| ID | Content | Affects | Issue | Status |
|---|---|---|---|---|
| Q-01 | When does the week start: Monday? Is the current day included? | C-01, C-02, AC-03 | ISS-20261011-01 | open |
| Q-02 | Does the weekly-limit condition also apply to updates? | C-02, AC-02 | ISS-20261011-02 | open |

## Acceptance criteria
| ID | Corresponds to | Given | When | Then | Sources | Related questions |
|---|---|---|---|---|---|---|
| AC-01 | C-01 | Five reservations in the current week | Create a reservation with a usage date in the current week | Not saved; display ERR-週次上限 | SRC-01, SRC-05 | — |
| AC-02 | C-02 | Five reservations in the current week and one in another week | Update that reservation's usage date to the current week | Unverified | SRC-03 | Q-02 |
| AC-03 | C-01, C-02 | Five reservations in the current week; usage date at the week boundary, Sunday and Monday | Save through creation and update | Unverified | — | Q-01 |

## Reflection status
| C | Design document | Code | Test | Design evidence | Code evidence | Test evidence |
|---|---|---|---|---|---|---|
| C-01 | not_reflected | pending | not_run | — | — | — |
| C-02 | not_reflected | pending | not_run | — | — | — |
```

### 7.2 Change specification: after decisions

Suppose the business representative records the following decisions in `decisions#41`.

- Adopt C-01 and C-02, applying the weekly-limit condition to both creation and updates.
- Define a week as Monday through Sunday, including the current day. Resolve Q-01 and Q-02 and update the issue register.
- Preserve the condition that both creation and updates may save when the current week's count is below the limit.
- Confirm C-01, C-02, and AC-01 through AC-04 as the content in change-specification commit `b2c3d4e`.

Add `SRC-06 | decisions#41 | Confirmation of update applicability, week start, and below-limit conditions` to the source list. Set `approval.decisions` to `[decisions#41]`. Regenerate displayed status from the source of truth.

| AC | Corresponding C | Confirmed acceptance criterion | Sources |
|---|---|---|---|
| AC-01 | C-01 | With five reservations in the current week, creating one with a usage date in that week does not save and displays ERR-週次上限 | SRC-01, SRC-05 |
| AC-02 | C-02 | With five reservations in the current week, moving another week's reservation into the current week does not save and displays ERR-週次上限 | SRC-05, SRC-06 |
| AC-03 | C-01, C-02 | A week runs Monday through Sunday. Even with five reservations on Sunday, creation and updates can save a usage date on the following Monday | SRC-06 |
| AC-04 | C-01, C-02 | With four or fewer reservations in the current week, both creation and updates can save | SRC-02, SRC-03, SRC-06 |

Keep Q rows, mark them `resolved`, and link them to the decision record. C-02 retains its `inferred` provenance.

### 7.3 New-development design document: excerpt, Stage B

```markdown
# DES-20261011-01 Employee reservation management

## Requirements
| ID | Type | Content | Provenance | Sources | Adoption | Status |
|---|---|---|---|---|---|---|
| FN-RES01 | function | Employees create and update reservations | Stated | SRC-01 | Candidate | Undecided |
| BIZ-LIM01 | rule | Each employee may make up to five reservations per week, with the limit configured per employee. Applies to both creation and updates | Stated | SRC-01 | Candidate | Undecided |
| RL-EMP | role | Employee: may create and update their own reservations | Inferred: The request mentions employees, but whether they may handle other employees' reservations is unverified | SRC-01 | Candidate | Undecided |

## Acceptance criteria
| ID | Corresponds to | Given | When | Then | Sources | Related questions |
|---|---|---|---|---|---|---|
| AC-01 | BIZ-LIM01 | Five reservations in the current week | Create a reservation with a usage date in the current week | Not saved | SRC-01 | — |
| AC-02 | BIZ-LIM01 | Same as above | Move another week's reservation into the current week | Not saved | SRC-01 | — |
| AC-03 | RL-EMP | Another employee's reservation | Update the reservation | Unverified | — | Q-01 |
```

New-development design documents use the same columns, fixed values, and provenance syntax as change specifications. They differ by using `requirements` instead of `changes`, `baseline` instead of `base`/`target`, and implementation status instead of change application status.

---

## 8. Checklist definitions

A checklist item is a question used to check what the specification contains. It is distinct from an extension schema that stores business-specification values (Section 9).

```yaml
- id: CK-API-010
  category: screen_api
  question: Should the same input conditions apply to both creation and updates?
  applies_to: [change, design]
  targets: [screen, api]
  evidence: source_required
  check_by: review
  required_at: [ready, finish]
  severity: required
  origin: base
  on_unverified: open_question
```

| Attribute | Values and meaning |
|---|---|
| `id` | `CK-{category}-NNN` |
| `category` | `business_rule`, `screen_api`, `data`, `batch_integration`, `operation` |
| `question` | Question in the document language. For language-specific text, use `question: {ja: …, en: …}` |
| `applies_to` | Array containing `change` and/or `design` |
| `targets` | Array of technical subjects: `screen`, `api`, `batch`, `migration`, `integration` |
| `evidence` | `source_required` (a source is required), `observation_ok` (observation is acceptable), or `none` |
| `check_by` | `machine` (checking script) or `review` (AI review and people) |
| `required_at` | Array of stages at which the item is mandatory: `draft`, `ready`, `finish`. Use `[]` if no stage applies |
| `severity` | `required`, `recommended`, `optional` |
| `origin` | `base`, `org`, `project` |
| `on_unverified` | `open_question` (create an open question) or `note` (annotation only) |
| `supersedes` | Array of common-item IDs omitted or replaced by organizational or project rules. Requires `reason` |

Each item contains one question. For `check_by: machine`, define the item to inspect and the evaluation condition in this document's check definitions (Section 11). A semantic judgment not defined there must not be displayed as having been performed by a machine check.

Even with `on_unverified: note`, a check must not pass if mandatory ready or finish conditions are unmet. The common definitions in Concepts Section 9.4 cannot be targets of `supersedes`.

---

## 9. Business-specification extension schemas

The common management schema does not define business-specification content such as screen-field types, required conditions, or count limits. Subject-specific extension rules define that content.

A checklist item asks whether the necessary conditions have been considered. An extension schema defines the fields used to record those conditions.

### 9.1 Definition syntax: example limit on entries per period

The following is a schema definition written by an extension-rule author. It is not a set of specification values for an individual project.

```yaml
id: count_limit
version: "1.0"
applies_to: [change, design]
targets: [api, screen]
item_prefix: LM
properties:
  subject:
    type: string
    required: true
  per:
    type: string
    required: true
  period:
    type: string
    enum: [day, week, month]
    required: true
  period_start:
    type: string
    enum: [monday, sunday, first_day]
    required: true
  operations:
    type: array
    item_enum: [create, update]
    min_items: 1
    required: true
  limit:
    type: object
    required: true
    properties:
      source: {type: string, enum: [fixed, field], required: true}
      value: {type: scalar, required: true}
  on_exceed:
    type: object
    required: true
    properties:
      effect: {type: string, enum: [reject_save], required: true}
      message: {type: string, required: true}
```

| Definition term | Meaning |
|---|---|
| `type` | Value type: `string`, `integer`, `decimal`, `date`, `boolean`, `scalar` (a string, number, or Boolean), `array`, or `object` |
| `enum` | Allowed values for a single value |
| `item_enum` | Allowed values for array elements |
| `min_items` | Minimum number of array elements |
| `required` | A condition an adopted item must meet at ready and finish. At draft, missing information may be recorded as an open question. Values with an invalid type must not be treated as valid |

### 9.2 What users see

From the request “Please limit each employee to five reservations per week,” AI drafts C-01 and C-02, the latter proposing applicability to updates. After a human decision, display the following table.

| Item | Aggregation unit | Period | Operations | Limit | Behavior when exceeded |
|---|---|---|---|---|---|
| Reservation count | Per employee | Week, starting Monday | Create and update | Per-employee setting: weeklyLimit, default 5 | Do not save; display ERR-週次上限 |

This table is a view of C-01 and C-02 organized according to the extension rules, not a separately maintained source of truth. If a user edits through the table, the tool presents proposed changes to the corresponding source of truth: the change specification or design document.

### 9.3 Internal representation

Users do not need to create or maintain the internal representation.

```yaml
extensions:
  count_limit:
    items:
      - id: LM-01
        subject: Reservations
        per: Employee
        period: week
        period_start: monday
        operations: [create, update]
        limit:
          source: field
          value: weeklyLimit
        on_exceed:
          effect: reject_save
          message: ERR-週次上限
        change_refs: [C-01, C-02]
        source_refs: [SRC-01, SRC-03, SRC-05, SRC-06]
```

`change_refs` (`requirement_refs` for new-development design documents) and `source_refs` are common reference information. Extension rules must not redefine provenance or confirmation status.

### 9.4 Recording rules in metadata

```yaml
extensions:
  - id: count_limit
    version: "1.0"
    ref: schemas/extensions/count_limit.md
```

Metadata `extensions` records references to definitions. Internal-representation `extensions` records values conforming to those definitions. Retain the definition for the applied rule version so that it can be reconstructed later. AI selecting a rule does not, by itself, approve the business conditions in that rule.

### 9.5 What can be checked

| Verification method | What it checks in this example |
|---|---|
| Structural checks | Types and required fields, elements of `operations`, and targets of `change_refs` and `source_refs` |
| Cross-checking against records | Whether C-01 and C-02 have valid decisions, and whether the display matches the source of truth |
| AI review and human review | Whether the condition should apply to updates; whether behavior for missing input matches the request; and whether conditions such as past dates are missing |
| Verification by execution | Whether creation and updates actually refuse to save and display the specified message |

If a field cannot be extracted from natural language or the body conflicts with the internal representation, display the affected part as `unverified`. Do not invent values to satisfy type constraints. Not every extension-rule item needs to apply to every project.

---

## 10. Evidence references

### 10.1 Record IDs

To use a `records#{recordID}` reference, the change record needs record IDs. Existing records, such as a team's change-summary Markdown, have no row IDs, so choose one of the following methods in the integration configuration.

| Method | Description | Suitability |
|---|---|---|
| Add an ID column | Add a column containing `REF-NN` / `IMP-NN` / `TEST-NN` to the changed-file list, application-verification records, and test-result tables in the change record | Recommended. People can see the IDs, and references do not break later. Append the column so that the order used by existing extraction tools is unchanged |
| Synthetic IDs | The tool generates IDs from the table type and row number | Use when existing records should not be modified. Inserting rows shifts references |

### 10.2 Example records

| Evidence reference | Subject | What is verified |
|---|---|---|
| `records#REF-01` | C-01 | Application to 入力チェック!B14 in design-document rev.4; reviewer, date/time, and target design-document hash |
| `records#REF-02` | C-02 | Application to 入力チェック!B32 in design-document rev.4; the same other information as above |
| `records#IMP-01` | C-01 | reservations.service.ts createReservation in code `d4e5f6a`; changed-file-list row and implementation-review record |
| `records#IMP-02` | C-02 | The corresponding updateReservation implementation |
| `records#TEST-01` | AC-01 through AC-04 | Individual test results for RUN-01; specification, code, and build under test, executor, and date/time |

Record the updated design-document path, version rev.4, hash, faithful Markdown, and `confirmation: [records#REF-01, records#REF-02]` in `target`. Do not change rev.3 in `base`.

RUN-01 targets approved specification `b2c3d4e` and build BLD-01 created from code `d4e5f6a`. Verify that the target still matches when using the results. If the execution target includes uncommitted or untracked content, also link the execution result to records that can reconstruct that content.

| Test case | Corresponding AC | Operation checked | Individual result |
|---|---|---|---|
| UT-001 | AC-01 | Create with a usage date in the current week when that week already has five reservations | pass |
| UT-002 | AC-02 | Move another week's reservation into the current week when that week already has five reservations | pass |
| UT-003 | AC-03 | Create with a usage date on the following Monday when Sunday has five reservations | pass |
| UT-004 | AC-03 | Update the usage date to the following Monday when Sunday has five reservations | pass |
| UT-005 | AC-04 | Create when the current week has four reservations | pass |
| UT-006 | AC-04 | Update when the current week has four reservations | pass |

The execution record retains each case's comparison with its expected result. A pass count alone does not provide evidence for this mapping.

### 10.3 Deriving change application status

| C | Design document | Code | Test | Design evidence | Code evidence | Test evidence |
|---|---|---|---|---|---|---|
| C-01 | Applied | Implemented | Pass | records#REF-01 | records#IMP-01 | records#TEST-01 |
| C-02 | Applied | Implemented | Pass | records#REF-02 | records#IMP-02 | records#TEST-01 |

Tools follow each C to its AC items, each AC to its test cases, and each test to its result and execution target to determine the displayed status for the C. Multiple C items may reference the same execution record.

In the `test` column, display `not_run` if any case is unexecuted and `fail` if any case failed. Display `inconclusive` when the result or target match cannot be determined. When these states coexist, show outstanding items in this order: failed, inconclusive, then unexecuted. Use `pass` only when all required cases succeed. If verification is unnecessary, reference the supporting basis and decision and use `n_a`.

This table alone must not determine project completion. The finish check may pass when valid decisions, resolution of open questions within the adopted scope, required design-document updates, individual test results, and existing completion conditions are all satisfied.

---

## 11. Parsing order and checks

### 11.1 Parsing order

1. Check metadata `sl`, `kind`, `lang`, and the applicable extension rules (`extensions`).
2. Map headings outside code blocks to section keys. Verify that each required section occurs exactly once. Treat ordinary subheadings as body content.
3. Identify tables with the column headings specified for each section, and check column count, mapping, and order. Identify empty tables by their headings too.
4. Parse each cell according to its type. Do not confuse fixed values, reasons, reference arrays, and free-form text.
5. Check ID uniqueness, reference targets, consistency between status and the sources of truth, and mandatory conditions for each check stage.

Do not identify a section solely from its number or heading level. Report duplicate sections, missing required sections, missing columns, and unrecognized structures at draft. Distinguish unavailable supporting content from an unparseable document structure.

Checking does not modify documents. Unknown columns or text must not be deleted without authorization. Hidden markers such as HTML comments are optional; teams that need them may define them in integration configuration.

### 11.2 Checks and their corresponding definitions

Check stages (draft / ready / finish) and outcomes follow Concepts Section 7.2; Stage A operation follows the rollout plan. Each check uses the definitions below.

| Check | Definitions checked |
|---|---|
| K-01 Metadata and section structure | Keys and required conditions in Section 2, sections in Section 4, and parsing order in Section 11.1 |
| K-02 ID uniqueness and references | Section 3 |
| K-03 Source existence | Reference formats in Section 3.2; the target of every `ref` in the `scope` source list can be identified |
| K-04 Frozen base | `base.content_hash` and the snapshot; `source_hash` in `base.faithful_md` |
| K-05 target and the actual artifact | `target.content_hash` and the actual artifact; existence of `target.confirmation` |
| K-06 Provenance and inference reasons | `provenance` in Section 5 and syntax in Sections 6.1–6.2 |
| K-07 C-to-AC relationships and sources for expected results | `acceptance` columns in Section 4.1; if `then` is `unverified`, `questions` must not be empty |
| K-08 Confirmation references and validity | Each record referenced by `approval.decisions` records the affected C and AC items and approved version; the relevant items have no differences since that version |
| K-09 Open questions and overall status | Derivation rules in Section 2.3 and `open.state` |
| K-10 Q-to-issue-register relationship | Existence of `open.issue` |
| K-11 Coverage of the application status table | `reflection` columns in Section 4.1; a row exists for every adopted C, and status values do not conflict with evidence columns |
| K-12 Existing completion conditions | Results of existing hooks specified in integration configuration |

---

## 12. Integration-configuration items

| Item | Content |
|---|---|
| Locations and precedence of sources of truth | Order of design documents, faithful Markdown, and knowledge Markdown |
| Language mappings | Headings, column labels, and fixed-value display labels for each `lang` (Section 1) |
| Ledger and issue register | Locations of `ledgers.decisions` and `ledgers.issues`, and mappings to decision-record and issue columns |
| Records | `records` location and record-ID assignment method (Section 10.1) |
| Test results | How to reference individual results and identify the code to which they apply |
| Request storage | Target of `request#{id}` |
| Existing completion conditions and hooks | Results referenced by K-12 |
| Order of design-document updates and implementation | The team's conventions |
| Extension rules | IDs, versions, and definition locations of applied rules |
| Numbering | ID assignment method; do not change prefixes or separators |
| Hidden markers | Syntax, if used |
