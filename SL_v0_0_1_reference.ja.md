# SpecLingua v0.0.1 Reference

**日付**: 2026-10-11
**ステータス**: ドラフト
**対応バージョン**: SpecLingua v0.0.1
**概念文書**: `SL_v0_0_1_concepts.ja.md`（以下「concepts」）

本書は、SpecLingua の文書と機械処理に関する定義を、項目ごとに示す。思想と設計原則は concepts に記載する。導入手順と現場の仕組みへの接続方法は、導入計画（本レポジトリに含まない）で扱う。スキーマの定義が文書間で異なる場合は、本書を適用する。

「段階A」は既存改修1件を実案件で試行する段階、「段階B」は新規開発と設計書改訂先行の改修に広げる段階を指す。改修仕様書（`kind: change`）は段階Aで使用する。新規設計書（`kind: design`）は、段階Bで確定する前の案である。本書に示す新規設計書の定義は、改修仕様書と同じ仕組みを適用できることを示すためのものである。

本書のパス、ID、コミット、判断、実行結果はすべて架空の例である。

---

## 1. 言語と表示の規則

| 区分 | 規則 |
|---|---|
| 内部表現と管理情報のキー | ASCII の英小文字と `_` |
| 状態・種別・由来の値 | 英語の固定語（5節） |
| ID | ASCII の英大文字・数字・区切りの `-` |
| 見出しと列名 | `lang` で選ぶ対応表に登録された表示名 |
| 本文の表に書く状態・種別・由来 | 固定語、または対応表の表示名。表示名は、内部では対応する固定語に変換する |
| 本文、理由、問い、業務上の値 | 文書の言語 |
| ファイル名、シート名、既存の項番 | 現場の名称のまま |

対応表は言語ごとに1ファイルとし、見出し・列名・固定語の表示名を記録する。一つの項目に複数の別名を登録できる。

```yaml
lang: ja
sections:
  purpose: [改修目的, 目的]
  scope: [対象と基準]
  before: [変更前]
  changes: [変更後, 変更内容]
  unchanged: [変更しない範囲, 変更対象外]
  open: [未決事項]
  acceptance: [検証条件, 受け入れ条件]
  reflection: [反映状況]
columns:
  changes: [ID, 種別, 内容, 由来, 出典, 採否, 状態]
  open: [ID, 内容, 影響, 課題, 状態]
  acceptance: [ID, 対応, 前提, 操作, 期待, 出典, 関連する問い]
  reflection: [C, 設計書, コード, テスト, 設計書の証拠, コードの証拠, テストの証拠]
  scope: [ID, 参照, 備考]
terms:
  confirmed: 確定
  tentative: 暫定
  undecided: 未決
  stated: 明記
  observed: 観測
  inferred: 推測
  unverified: 未確認
  added: 追加
  modified: 変更
  removed: 削除
  candidate: 採用候補
  adopted: 採用
  excluded: 除外
```

対応表にない見出しや列名は、検査で「識別不能」として報告する。検査では文書を変更しない。表示名の修正と別名の登録は、起草・整形処理で行う。利用者が固定語を暗記する必要はない。

---

## 2. 管理情報

管理情報は、文書の先頭に YAML で記録する。ツールが生成し、利用者は編集しない。人による編集や正本との不一致を検出した場合、検査では差分を報告するだけとする。再生成は、検査とは別の更新処理で行う。その際も、固定した `base` や承認対象を、現在の内容で上書きしてはならない。

### 2.1 改修仕様書（kind: change）

```yaml
---
sl: "0.1"
kind: change
id: CHG-20261011-01
title: 予約の週次上限の追加
lang: ja
status: undecided
reflected: not_reflected
base:
  design_doc: docs/design/reservation.xlsx
  version: rev.3
  snapshot: snapshots/design/reservation-rev3.xlsx
  content_hash: "sha256:<退避版のハッシュ>"
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

| キー | 必須条件 | 意味 |
|---|---|---|
| `sl` | 常に必要 | スキーマの版。本文の改訂番号や製品の版とは別 |
| `kind` | 常に必要 | `change` |
| `id` | 常に必要 | 変更セットの ID（3節） |
| `title` | 常に必要 | 件名。ツールが依頼に基づいて付ける |
| `lang` | 常に必要 | 表示言語。対応表の選択に使う |
| `status` | 常に必要 | 採否、台帳の確定判断、対象範囲の未決事項に基づいて決定する表示値（2.3） |
| `reflected` | 常に必要 | 採用した変更項目の設計書への反映状況に基づいて決定する表示値（2.3） |
| `base` | 常に必要 | 変更前の設計書・コードを固定するための記録。固定後は上書きしない。基準を確認できない段階では、何が不足しているかを明示し、ready を通過させない |
| `base.design_doc` | 常に必要 | 対象の設計書のパス |
| `base.version` | 常に必要 | 改訂履歴の版、またはコミット |
| `base.snapshot` | 常に必要 | 退避版のパス |
| `base.content_hash` | 常に必要 | 退避版のハッシュ |
| `base.faithful_md` | 常に必要 | 退避版から作成した忠実MD |
| `base.code.commit` | 常に必要 | 調査したコードのコミット |
| `base.code.uncommitted` | 未コミット変更がある場合 | 退避版または差分記録への参照。コミットだけで対象全体を表せる場合は `null` |
| `target` | 反映前は `null` | 反映後の `design_doc`、`version`、`content_hash`、`faithful_md` と、確認記録への参照配列 `confirmation` |
| `approval` | 承認前は `null` | 判断記録への参照配列 `decisions`。例：`decisions: [decisions#41]`。判断記録そのものが正本であり、この項目は参照用の索引 |
| `ledgers.decisions` | 常に必要 | 決定事項台帳の場所。接続設定から取得する |
| `ledgers.issues` | 常に必要 | 課題表の場所。接続設定から取得する |
| `records` | 証拠を参照する場合 | 既存の改修記録の場所。新しい実績台帳を作成するための指定ではない |
| `extensions` | 常に必要。未適用なら `[]` | 適用した追加規則の `id`、`version`、`ref`（9節） |

`approval.decisions` から参照する各判断記録は、対象の C・AC、承認した仕様と前提の版、判断者、日時を識別できるものとする。項目ごとに判断や承認版が異なっていてよい。文書全体の承認を、一つのコミットに限定する必要はない。

### 2.2 新規設計書（kind: design、段階Bで確定）

```yaml
---
sl: "0.1"
kind: design
id: DES-20261011-01
title: 社員予約管理
lang: ja
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

| キー | 必須条件 | 意味 |
|---|---|---|
| `kind` | 常に必要 | `design` |
| `id` | 常に必要 | 設計書の ID（3節） |
| `baseline` | 常に必要 | 最初の版と確認対象の記録。改訂時には、基準となる版を識別する。`base`／`target` は記録しない |
| `reflected` | 持たない | 新規設計書には反映先となる正本がないため、この項目は使用しない。実装・テストの状況は、実装状況表（4.2）に示す |
| そのほか | 改修仕様書と同じ | `sl`、`title`、`lang`、`status`、`approval`、`ledgers`、`records`、`extensions` |

### 2.3 表示値の導出

`status` の表示値は、次の条件に従って決定する。

- 採用範囲に未決事項がある場合は `undecided` とする。
- 未決事項がなく、暫定判断がある場合は `tentative` とする。
- 採用した全項目に有効な確定判断がある場合は `confirmed` とする。
- 採用項目がまだない起草中の文書は `undecided` とする。

除外項目には、除外するという判断が必要である。判断の有効性（5節 `validity`）は別に検査する。表示値が `confirmed` であることだけを理由に、ready・finish を通過させてはならない。

`reflected` は改修仕様書だけに使用する。表示値は、次の条件に従って決定する。

- 採用した全項目の反映を証拠で確認できる場合は `reflected` とする。
- 残件のすべてが依頼中である場合は `requested` とする。
- 未反映の項目がある場合は `not_reflected` とする。
- 記録の不足や矛盾により、反映の有無を判断できない場合は `unverified` とする。
- 採用項目がまだない場合は `not_reflected` とする。

---

## 3. ID と参照

### 3.1 ID の書式

| 対象 | 書式 | 採番 |
|---|---|---|
| 変更セット | `CHG-YYYYMMDD-NN` | ツール |
| 新規設計書 | `DES-YYYYMMDD-NN` | ツール |
| 変更項目／未決事項／検証条件 | `C-NN`／`Q-NN`／`AC-NN` | ツール。文書内で一意 |
| 出典 | `SRC-NN` | ツール。`scope` の出典一覧で定義 |
| 新規設計書の要件 | `FN-{分類}{連番}`（機能）、`BIZ-{分類}{連番}`（業務ルール）、`RL-{名称}`（ロール）、`EX-{名称}`（外部連携） | ツール。既定の接頭辞（段階Bで確定） |
| 新規設計書の設計要素 | `SC-{分類}{連番}`（画面）、`EN-{連番}`（エンティティ） | 同上 |
| 確認観点 | `CK-{区分}-NNN` | 追加規則の作成者 |
| 追加規則の項目 | `{規則の item_prefix}-NN` | ツール。規則の名前空間内で一意 |

採番方法は接続設定で変更できる。ただし、接頭辞と区切りは変更しない。確定後に ID を削除・再利用して、過去の判断を別の内容に対応づけてはならない。

### 3.2 参照の書式

| 対象 | 書式 | 例 |
|---|---|---|
| 他文書の項目 | `{文書ID}/{項目ID}` | `CHG-20261011-01/C-02` |
| 設計書の箇所 | `{doc}@{version} {locator}`。`locator` は現場の形式に従う（Excel は `シート!範囲`、Markdown は `#見出し`、Word は `§番号`） | `base.design_doc@rev.3 入力チェック!B12:B18` |
| コード | `{path}:{lines}@{commit}`。未コミット変更を含む場合は、基準のコミットに加えて、退避版・差分記録を参照する | `src/reservations/reservations.service.ts:120-168@a1b2c3d` |
| 台帳の判断 | `decisions#{No}` | `decisions#41` |
| 課題 | 現場の課題 ID | `ISS-20261011-01` |
| 依頼 | `request#{id}`。依頼本文の保存場所は接続設定に基づいて特定する | `request#REQ-01` |
| 反映・実行の証拠 | `records#{記録ID}` | `records#REF-01` |
| 追加規則の項目 | `{文書ID}/{規則ID}/{項目ID}` | `CHG-20261011-01/count_limit/LM-01` |

`base.design_doc`、`decisions`、`records` は、管理情報で指定した参照先を表す。照合では、参照先の位置だけでなく、対象の版も確認する。

### 3.3 配列と空値

複数件を参照できる項目は、内部表現では常に配列として扱う。Markdown のセルでは、参照が1件ならその ID を記述する。複数件なら、`C-01, C-02` のようにカンマで区切って記述する。参照がない場合は `—` と表示し、内部では空配列とする。参照 ID 自体にはカンマを使用しない。この規則は参照の書式に関するものであり、自由記述の文章には適用しない。

---

## 4. 章と表

### 4.1 改修仕様書

| 章のキー | 表示名（ja） | 表示名（en） | 表の列のキー（順序固定） |
|---|---|---|---|
| `purpose` | 改修目的 | Purpose | （表なし） |
| `scope` | 対象と基準 | Scope and baseline | `id`, `ref`, `note` |
| `before` | 変更前 | Before | （表なし。本文に由来を付ける） |
| `changes` | 変更後 | Changes | `id`, `kind`, `content`, `provenance`, `source`, `adoption`, `status` |
| `unchanged` | 変更しない範囲 | Unchanged | （表なし） |
| `open` | 未決事項 | Open questions | `id`, `content`, `affects`, `issue`, `state` |
| `acceptance` | 検証条件 | Acceptance criteria | `id`, `changes`, `given`, `when`, `then`, `source`, `questions` |
| `reflection` | 反映状況 | Reflection status | `id`, `design_doc`, `code`, `test`, `design_evidence`, `code_evidence`, `test_evidence` |

表に示す8章はすべて必須とし、順序も固定する。記載内容がない章も見出しを残す。表の種類は章のキーで識別する。ID の接頭辞は、その表で使用できる ID かどうかの検査に使う。変更項目表と反映状況表はどちらも C-ID を持つため、接頭辞だけでは表の種類を区別できない。

各列の意味は、次のとおりである。

| 列 | 意味 |
|---|---|
| `source` | SRC-ID の配列。問いは期待値の根拠ではないため、Q-ID は記述しない |
| `questions` | Q-ID の配列。期待値が未決の場合は、`then` に `unverified` を記述し、この列に関連する Q を記述する |
| `affects` | 影響する C／AC の配列 |
| `issue` | 課題表の ID |
| `design_evidence` ほか | `records#…` の配列。状態値と同じセルに混在させない |

### 4.2 新規設計書（段階Bで確定）

| 章のキー | 表示名（ja） | 表の列のキー（順序固定） |
|---|---|---|
| `purpose` | 目的と利用者 | （表なし） |
| `scope` | 対象範囲と出典 | `id`, `ref`, `note` |
| `requirements` | 要件 | `id`, `kind`, `content`, `provenance`, `source`, `adoption`, `status` |
| `design` | 全体設計 | （表なし。方式・データ・認証認可・外部連携の方針を本文と追加規則で記述） |
| `behavior` | 個別設計 | （機能ごと。画面・API・バッチの動作は追加規則の表で記述） |
| `acceptance` | 検証条件 | `id`, `requirements`, `given`, `when`, `then`, `source`, `questions` |
| `open` | 未決事項 | `id`, `content`, `affects`, `issue`, `state` |
| `implementation` | 実装状況 | `id`, `design`, `code`, `test`, `design_evidence`, `code_evidence`, `test_evidence` |

`requirements.kind` には、`function`／`rule`／`role`／`external`／`nonfunctional` のいずれかを記述する。`implementation` は、実装に入るまで空のままでよい。`design` と `behavior` の表の構造は、対象別の追加規則（9節）で定める。

---

## 5. 固定語

| キー | 値（ja 表示） |
|---|---|
| `kind`（文書） | `change`、`design` |
| `changes.kind` | `added`（追加）、`modified`（変更）、`removed`（削除） |
| `requirements.kind` | `function`（機能）、`rule`（業務ルール）、`role`（ロール）、`external`（外部連携）、`nonfunctional`（非機能） |
| `provenance` | `stated`（明記）、`observed`（観測）、`inferred`（推測）、`unverified`（未確認） |
| `adoption` | `candidate`（採用候補）、`adopted`（採用）、`excluded`（除外） |
| `status` | `undecided`（未決）、`tentative`（暫定）、`confirmed`（確定） |
| `validity`（検査結果にのみ） | `valid`（有効）、`recheck`（要再確認） |
| `reflected`、`reflection.design_doc` | `not_reflected`（未反映）、`requested`（反映依頼中）、`reflected`（反映済み）、`unverified`（未確認） |
| `reflection.code`、`implementation.code` | `pending`（未着手）、`done`（実装済み）、`unverified`（未確認） |
| `reflection.test`、`implementation.test` | `not_run`（未実行）、`pass`（合格）、`fail`（不合格）、`inconclusive`（判定不能）、`n_a`（対象外） |
| `open.state` | `open`（未解決）、`resolved`（解決済み）、`out_of_scope`（対象外） |

`removed` は、業務上の定義や動作を廃止する意図を表す。物理ファイルやデータの削除指示を意味しない。

---

## 6. セルと本文の書式

### 6.1 由来セル

由来セルでは、`inferred: 理由` または `推測: 理由` の書式を認める。最初のコロンより前を固定語、後を理由として解析する。内部では、`provenance: inferred` と `reason` に分けて保持する。`inferred` には、空でない理由が必要である。

### 6.2 本文中の由来

本文中の由来は角括弧で記述する。固定語の代わりに、対応表の表示名を使用してもよい。参照と理由は、それぞれ別に解析する。

```
登録は期限未入力でも保存できる。[stated SRC-02]
調査したコードも同じ動作である。[observed SRC-04]
更新時にも同じ条件を適用する。[inferred SRC-03: 登録と更新で同じ入力チェック表を参照]
期限に過去日を指定できるかは不明。[unverified Q-01]
```

### 6.3 状態セル

状態列には、正本に基づいて決定した状態を表示する。人がセルを「確定」や「合格」に変更しても、承認や検証の証拠にはならない。検査では、台帳や実行記録との不一致を報告する。

反映の有無を証拠から判断できない場合は `unverified` とする。作業していないことが分かっている状態（`not_reflected`、`pending`、`not_run`）とは区別する。

### 6.4 省略

記述を省略した結果、機械検査に必要な情報が欠けた場合は、推測で補完してはならない。その箇所を `unverified` とし、未決事項に対応づける。

---

## 7. 記述例

### 7.1 改修仕様書（起草時）

次の例は、2.1 の管理情報に続けて記述する本文である。この段階では、更新への適用と週の起点が未決のため、ready は通過しない。

```markdown
# CHG-20261011-01 予約の週次上限の追加

## 改修目的
社員一人の予約を週5件までに制限し、特定の社員による予約の集中を防ぐ。[stated SRC-01]

## 対象と基準
| ID | 参照 | 備考 |
|---|---|---|
| SRC-01 | request#REQ-01 | 週次上限の依頼 |
| SRC-02 | base.design_doc@rev.3 入力チェック!B12:B18 | 登録の入力チェック |
| SRC-03 | base.design_doc@rev.3 入力チェック!B30:B36 | 更新の入力チェック |
| SRC-04 | src/reservations/reservations.service.ts:120-168@a1b2c3d | createReservation / updateReservation |
| SRC-05 | base.design_doc@rev.3 メッセージ一覧!B88 | ERR-週次上限 の文言 |

## 変更前
登録・更新とも、同じ週の予約件数に関わらず保存できる。[stated SRC-02 SRC-03]
調査したコードも同じ動作である。[observed SRC-04]

## 変更後
| ID | 種別 | 内容 | 由来 | 出典 | 採否 | 状態 |
|---|---|---|---|---|---|---|
| C-01 | 変更 | 登録時、その社員の今週の予約件数が上限（weeklyLimit、既定5件）に達していれば保存しない | 明記 | SRC-01 | 採用候補 | 未決 |
| C-02 | 変更 | 更新時（利用日を別の週へ移す場合）にも同じ条件を適用する | 推測: 登録と更新で同じ入力チェック表を参照している | SRC-03 | 採用候補 | 未決 |

## 変更しない範囲
週次上限以外の入力条件は変更しない。

## 未決事項
| ID | 内容 | 影響 | 課題 | 状態 |
|---|---|---|---|---|
| Q-01 | 週の起点はいつか（月曜始まりか。当日を含むか） | C-01, C-02, AC-03 | ISS-20261011-01 | 未解決 |
| Q-02 | 更新にも週次上限の条件を適用するか | C-02, AC-02 | ISS-20261011-02 | 未解決 |

## 検証条件
| ID | 対応 | 前提 | 操作 | 期待 | 出典 | 関連する問い |
|---|---|---|---|---|---|---|
| AC-01 | C-01 | 今週の予約が5件 | 今週の利用日で登録 | 保存されない。ERR-週次上限 を表示 | SRC-01, SRC-05 | — |
| AC-02 | C-02 | 今週の予約が5件、別の週の予約が1件 | その予約の利用日を今週へ更新 | 未確認 | SRC-03 | Q-02 |
| AC-03 | C-01, C-02 | 今週の予約が5件、利用日が週の境界（日曜と月曜） | 登録・更新で保存 | 未確認 | — | Q-01 |

## 反映状況
| C | 設計書 | コード | テスト | 設計書の証拠 | コードの証拠 | テストの証拠 |
|---|---|---|---|---|---|---|
| C-01 | 未反映 | 未着手 | 未実行 | — | — | — |
| C-02 | 未反映 | 未着手 | 未実行 | — | — | — |
```

### 7.2 改修仕様書（判断後）

業務担当者が、次の判断を `decisions#41` に記録したとする。

- C-01・C-02 を採用し、登録・更新の両方に週次上限の条件を適用する
- 週は月曜始まり日曜までとし、当日を含む。Q-01・Q-02 を解決し、課題表の記録を更新する
- 今週の件数が上限未満の場合は、登録・更新とも保存できる条件を維持する
- C-01・C-02 と AC-01〜AC-04 を、改修仕様書のコミット `b2c3d4e` の内容として確定する

出典一覧に `SRC-06 | decisions#41 | 更新への適用、週の起点、上限未満の条件の確定判断` を追加する。`approval.decisions` の値は `[decisions#41]` となる。表示する状態は、正本に基づいて再生成する。

| AC | 対応C | 確定した検証条件 | 出典 |
|---|---|---|---|
| AC-01 | C-01 | 今週の予約が5件のとき、今週の利用日で登録すると、保存されず ERR-週次上限 が表示される | SRC-01, SRC-05 |
| AC-02 | C-02 | 今週の予約が5件のとき、別の週の予約の利用日を今週へ更新すると、保存されず ERR-週次上限 が表示される | SRC-05, SRC-06 |
| AC-03 | C-01, C-02 | 週は月曜始まり日曜まで。日曜の予約が5件でも、翌月曜の利用日なら登録・更新で保存できる | SRC-06 |
| AC-04 | C-01, C-02 | 今週の予約が4件以下なら、登録・更新で保存できる | SRC-02, SRC-03, SRC-06 |

Q の行は削除せず、状態を `resolved` として判断記録に対応づける。C-02 の由来は、`inferred` のまま保持する。

### 7.3 新規設計書（抜粋、段階B）

```markdown
# DES-20261011-01 社員予約管理

## 要件
| ID | 種別 | 内容 | 由来 | 出典 | 採否 | 状態 |
|---|---|---|---|---|---|---|
| FN-RES01 | 機能 | 社員が予約を登録・更新する | 明記 | SRC-01 | 採用候補 | 未決 |
| BIZ-LIM01 | 業務ルール | 社員一人の予約は週5件まで（上限は社員ごとに設定）。登録・更新の両方に適用 | 明記 | SRC-01 | 採用候補 | 未決 |
| RL-EMP | ロール | 社員。自分の予約の登録・更新ができる | 推測: 依頼に社員の記載があるが、他の社員の予約を扱えるかは未確認 | SRC-01 | 採用候補 | 未決 |

## 検証条件
| ID | 対応 | 前提 | 操作 | 期待 | 出典 | 関連する問い |
|---|---|---|---|---|---|---|
| AC-01 | BIZ-LIM01 | 今週の予約が5件 | 今週の利用日で登録 | 保存されない | SRC-01 | — |
| AC-02 | BIZ-LIM01 | 同上 | 別の週の予約の利用日を今週へ更新 | 保存されない | SRC-01 | — |
| AC-03 | RL-EMP | 他の社員の予約 | 予約を更新 | 未確認 | — | Q-01 |
```

新規設計書では、改修仕様書と同じ列、固定語、由来の書式を使用する。異なるのは、`changes` の代わりに `requirements` を使用すること、`base`／`target` の代わりに `baseline` を使用すること、反映状況の代わりに実装状況を記録することである。

---

## 8. 確認観点の定義

確認観点は、仕様に何が記述されているかを確認するための問いである。業務仕様の値を格納する追加スキーマ（9節）とは区別する。

```yaml
- id: CK-API-010
  category: screen_api
  question: 登録と更新の両方に同じ入力条件を適用するか
  applies_to: [change, design]
  targets: [screen, api]
  evidence: source_required
  check_by: review
  required_at: [ready, finish]
  severity: required
  origin: base
  on_unverified: open_question
```

| 属性 | 値・意味 |
|---|---|
| `id` | `CK-{区分}-NNN` |
| `category` | `business_rule`、`screen_api`、`data`、`batch_integration`、`operation` |
| `question` | 問いの文。文書の言語で記述する。言語別に保持する場合は `question: {ja: …, en: …}` とする |
| `applies_to` | `change`、`design` の配列 |
| `targets` | 技術的対象の配列（`screen`、`api`、`batch`、`migration`、`integration`） |
| `evidence` | `source_required`（出典が必要）、`observation_ok`（観測による確認でもよい）、`none` |
| `check_by` | `machine`（検査スクリプト）、`review`（AIレビューと人） |
| `required_at` | 必須となる検査場面の配列。要素は `draft`、`ready`、`finish`。該当する場面がない場合は `[]` とする |
| `severity` | `required`、`recommended`、`optional` |
| `origin` | `base`、`org`、`project` |
| `on_unverified` | `open_question`（未決事項に起票）、`note`（注記のみ） |
| `supersedes` | 組織・案件の規則で共通の項目を省略・置換する場合に、対象の ID を記録する配列。`reason` を必須とする |

一つの項目には、一つの問いを記述する。`check_by: machine` の項目は、判定対象の項目と条件を、本書の検査定義（11節）に定める。定義されていない意味上の判断を、機械検査で実施済みと表示してはならない。

`on_unverified: note` を指定した場合も、ready・finish の必須条件を満たしていなければ、検査を通過させてはならない。concepts 9.4 の共通定義は、`supersedes` の対象にできない。

---

## 9. 業務仕様の追加スキーマ

共通管理スキーマは、画面項目の型や必須条件、件数の上限といった業務仕様の内容を定義しない。これらの内容は、対象別の追加規則で定める。

確認観点は「必要な条件が検討されているか」という問いを定義する。追加スキーマは、その条件を記録する項目を定義する。

### 9.1 定義の書式（例：期間あたりの登録件数の上限）

次の例は、追加規則の作成者が記述するスキーマの定義である。個別案件の仕様値を記述したものではない。

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

| 定義の語 | 意味 |
|---|---|
| `type` | 値の型。`string`、`integer`、`decimal`、`date`、`boolean`、`scalar`（文字列・数値・真偽値のいずれか）、`array`、`object` |
| `enum` | 単一値の候補 |
| `item_enum` | 配列要素の候補 |
| `min_items` | 配列の最小要素数 |
| `required` | 採用した項目が ready・finish で満たす条件。draft では、不足を未決事項として記録できる。ただし、型が不正な値を有効として扱ってはならない |

### 9.2 利用者に見せる形

「社員一人の予約は週5件までにしてください」という依頼から、AIが C-01 と C-02（更新への適用案）を起草する。人の判断後は、次の表として表示する。

| 項目 | 集計の単位 | 期間 | 対象処理 | 上限 | 超過時の動作 |
|---|---|---|---|---|---|
| 予約件数 | 社員ごと | 週（月曜始まり） | 登録・更新 | 社員ごとの設定値（weeklyLimit、既定5件） | 保存せず、ERR-週次上限 を表示する |

この表は、C-01・C-02 を追加規則に従って整理した表示であり、別に手修正する正本ではない。利用者がこの表から編集する場合も、ツールは対応する正本（改修仕様書、設計書）の変更案を示す。

### 9.3 内部表現

内部表現を、利用者が作成・保守する必要はない。

```yaml
extensions:
  count_limit:
    items:
      - id: LM-01
        subject: 予約
        per: 社員
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

`change_refs`（新規設計書では `requirement_refs`）と `source_refs` は、共通の参照情報である。追加規則で、由来や確定状態を再定義してはならない。

### 9.4 管理情報への記録

```yaml
extensions:
  - id: count_limit
    version: "1.0"
    ref: schemas/extensions/count_limit.md
```

管理情報の `extensions` には、定義への参照を記録する。内部表現の `extensions` には、その定義に従う値を記録する。適用した規則の版に対応する定義を、後から復元できるように保持する。AIが規則を選択しただけでは、その規則に含まれる業務条件が承認されたことにはならない。

### 9.5 検査できること

| 確認方法 | この例で確認する内容 |
|---|---|
| 構造の検査 | 型と必須項目、`operations` の要素、`change_refs` と `source_refs` の参照先 |
| 記録との照合 | C-01・C-02 に有効な判断があるか。表示と正本の内容が一致するか |
| AIレビュー・人の確認 | 更新にも適用すべきか。未入力時の動作が依頼に合うか。過去日などの条件が不足していないか |
| 実行による検証 | 登録・更新で実際に保存を拒否し、指定のメッセージを表示するか |

自然文から項目を抽出できない場合や、本文と内部表現が矛盾する場合は、その箇所を `unverified` と表示する。型の条件を満たすために、値を創作して補完してはならない。追加規則の全項目を、すべての案件に適用する必要はない。

---

## 10. 証拠の参照

### 10.1 記録の ID

`records#{記録ID}` で参照するには、改修記録に記録 ID が必要である。既存の改修記録（現場の改修概要 md など）には行の ID がないため、接続設定で次のいずれかの方法を選ぶ。

| 方法 | 内容 | 向き不向き |
|---|---|---|
| ID 列を追加する | 改修記録の変更ファイル一覧、反映確認の記録、テスト結果の表に、`REF-NN`／`IMP-NN`／`TEST-NN` を記録する列を追加する | 推奨する方法。人が ID を確認でき、後から参照が壊れない。既存の抽出ツールが使用する列順を変えないように、列は末尾に追加する |
| 合成 ID | ツールが、表の種類と行番号から ID を生成する | 既存記録を変更したくない場合に使用する。ただし、行を挿入すると参照がずれる |

### 10.2 記録の例

| 証拠の参照 | 対象 | 確認内容 |
|---|---|---|
| `records#REF-01` | C-01 | 設計書 rev.4 の入力チェック!B14 への反映。確認者、日時、対象設計書のハッシュ |
| `records#REF-02` | C-02 | 設計書 rev.4 の入力チェック!B32 への反映。同上 |
| `records#IMP-01` | C-01 | コード `d4e5f6a` の reservations.service.ts createReservation。変更ファイル一覧の行と実装確認の記録 |
| `records#IMP-02` | C-02 | 同 updateReservation |
| `records#TEST-01` | AC-01〜AC-04 | 実行 RUN-01 の個別テスト結果。対象の仕様・コード・ビルド、実行者、日時 |

`target` には、反映後の設計書のパス、版 rev.4、ハッシュ、忠実MD、`confirmation: [records#REF-01, records#REF-02]` を記録する。`base` の rev.3 は変えない。

RUN-01 は、承認対象の仕様 `b2c3d4e` とコード `d4e5f6a` から作成したビルド BLD-01 を対象とする。結果を使用する時点でも、対象が一致することを確認する。実行対象に未コミット・未追跡の内容を含む場合は、その内容を復元できる記録も、実行結果に対応づける。

| テスト項目 | 対応AC | 確認した操作 | 個別結果 |
|---|---|---|---|
| UT-001 | AC-01 | 今週5件のとき今週の利用日で登録 | pass |
| UT-002 | AC-02 | 今週5件のとき別の週の予約を今週へ更新 | pass |
| UT-003 | AC-03 | 日曜に5件のとき翌月曜の利用日で登録 | pass |
| UT-004 | AC-03 | 日曜に5件のとき利用日を翌月曜へ更新 | pass |
| UT-005 | AC-04 | 今週4件のとき登録 | pass |
| UT-006 | AC-04 | 今週4件のとき更新 | pass |

実行記録には、各ケースの結果を期待値と比較した結果を保持する。成功件数だけでは、この対応表を裏付ける証拠にならない。

### 10.3 反映状況の導出

| C | 設計書 | コード | テスト | 設計書の証拠 | コードの証拠 | テストの証拠 |
|---|---|---|---|---|---|---|
| C-01 | 反映済み | 実装済み | 合格 | records#REF-01 | records#IMP-01 | records#TEST-01 |
| C-02 | 反映済み | 実装済み | 合格 | records#REF-02 | records#IMP-02 | records#TEST-01 |

ツールは、C に対応する AC、AC に対応するテスト項目、各テストの結果と実行対象を順に確認し、C ごとの表示状態を決定する。同じ実行記録を、複数の C から参照してよい。

`test` 列には、未実行があれば `not_run`、失敗があれば `fail` を表示する。結果や実行対象の一致を判断できない場合は、`inconclusive` を表示する。これらが混在する場合は、失敗、判定不能、未実行の順に残件を表示する。必要なケースがすべて成功した場合だけ `pass` とする。検証が不要な場合は、その根拠と判断を参照し、`n_a` とする。

この表だけで案件全体の完了を決めてはならない。有効な判断、採用範囲の未決事項の解消、必要な設計書への反映、個別テストの結果、既存の完了条件がそろった場合に、finish を通過できる。

---

## 11. 解析の順序と検査

### 11.1 解析の順序

1. 管理情報の `sl`、`kind`、`lang` と、適用する追加規則（`extensions`）を確認する
2. コードブロック外の見出しを、対応表に従って章のキーに変換する。各必須章が一度ずつ存在することを確認する。通常の小見出しは本文として扱う
3. 各章で規定された列見出しを持つ表を識別し、列の数・対応・順序を確認する。空の表も、列見出しで識別する
4. 各セルを型に従って解析する。固定語、理由、参照配列、自由記述を混同しない
5. ID の一意性、参照先、状態と正本の一致、各検査場面の必須条件を確認する

章番号や見出しの段数だけで、章を判定してはならない。同じ章の重複、必須章の欠落、列の不足、識別不能な構造は、draft で報告する。根拠の内容を取得できていないことと、文書の構造を解析できないことは区別する。

検査では文書を変更しない。未知の列や記述を無断で削除してはならない。HTML コメントなどの隠しマーカーは必須としないが、必要な現場では接続設定に定めて使用できる。

### 11.2 検査項目と本書の対応

検査の場面（draft／ready／finish）と判定は concepts 7.2 に従い、段階Aの運用は導入計画に従う。各検査で照合する本書の定義は、次のとおりである。

| 検査 | 照合する定義 |
|---|---|
| K-01 管理情報と章立て | 2節のキーと必須条件、4節の章、11.1 の解析順序 |
| K-02 ID の一意性と参照 | 3節 |
| K-03 出典の実在 | 3.2 の参照書式。`scope` の出典一覧にある各 `ref` の参照先を特定できること |
| K-04 base の固定 | `base.content_hash` と退避版、`base.faithful_md` の `source_hash` |
| K-05 target と現物 | `target.content_hash` と現物、`target.confirmation` の実在 |
| K-06 由来と推測の理由 | 5節 `provenance`、6.1・6.2 の書式 |
| K-07 C と AC、期待値の出典 | 4.1 の `acceptance` 列。`then` が `unverified` なら `questions` が空でないこと |
| K-08 確定判断の参照と有効性 | `approval.decisions` から参照する各記録に、対象 C・AC と承認版が記録されていること。また、承認版以降に当該項目の差分がないこと |
| K-09 未決と全体状態 | 2.3 の導出規則と `open.state` |
| K-10 Q と課題表 | `open.issue` の実在 |
| K-11 反映状況の網羅 | 4.1 の `reflection` 列。採用したすべての C の行があり、状態と証拠列が矛盾しないこと |
| K-12 既存の完了条件 | 接続設定で指定した既存フックの結果 |

---

## 12. 接続設定の項目

| 項目 | 内容 |
|---|---|
| 正本の場所と優先順位 | 設計書、忠実MD、知識MD の順序 |
| 対応表 | `lang` ごとの見出し・列名・固定語の表示名（1節） |
| 台帳と課題表 | `ledgers.decisions`、`ledgers.issues` の場所と、判断記録・課題の列の対応 |
| 記録 | `records` の場所と、記録 ID の付与方法（10.1） |
| テスト結果 | 個別結果の参照方法と、結果の対象コードの識別方法 |
| 依頼の保存場所 | `request#{id}` の参照先 |
| 既存の完了条件とフック | K-12 で参照する結果 |
| 設計書反映と実装の順序 | 現場の規約 |
| 追加規則 | 適用する規則の ID、版、定義の場所 |
| 採番 | ID の採番方法（接頭辞と区切りは変えない） |
| 隠しマーカー | 使用する場合の書式 |
