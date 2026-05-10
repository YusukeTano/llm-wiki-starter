---
name: llm-wiki-starter
version: 1.9.1
description: Create or update an Obsidian-ready Japanese LLM Wiki. Use this when the user wants a purpose-specific raw-sources-to-wiki knowledge base where hearing selections become real directories, the wiki grows as sources are added, and privacy/sensitive-information handling is configured before ingest. Standard privacy keeps operationally necessary names/team names and redacts other sensitive fields. Generated README.md includes purpose-adaptive wiki usage prompts whose wording and actions change based on the user's purpose, actual directories, selected Wiki/Outputs items, HOME sections, and privacy policy.
---

# LLM Wiki Starter Skill v1.9.1

Use this Skill when the user wants to create or update an Obsidian-ready LLM Wiki: a directory or repository where rough sources such as Slack threads, articles, notes, PDFs, images, data, meeting notes, research material, reading notes, travel plans, customer interviews, or project documents are incrementally turned by an LLM into a persistent, interlinked Markdown wiki.

This is a no-script generic Skill. Do not depend on a fixed directory-creation script. Design and create the directory tree directly from the user's purpose, hearing selections, and privacy/sensitive-information choices.

## Core philosophy

- Raw sources are immutable. Read them, but do not rewrite them unless the user explicitly asks.
- The wiki is the compiled knowledge layer. The LLM creates, updates, links, reconciles, and maintains Markdown pages.
- Schema/rule files discipline future agents. `AGENTS.md` and `CLAUDE.md` describe how to maintain the wiki.
- `index.md` and `log.md` are mandatory. `index.md` is the content-oriented catalog; `log.md` is the chronological append-only history.
- Useful answers should compound. Reusable FAQs, comparisons, analyses, manuals, reports, summaries, and checklists should be filed into the wiki or outputs.
- Generated `README.md` must include copy-pasteable, purpose-specific wiki usage prompts so the user can keep using this particular wiki without remembering the full workflow.
- `個人用メモ/` is private. Never read, summarize, index, cite, commit, or link it unless the user explicitly moves content elsewhere.
- Privacy and sensitive-information handling must be decided before ingest.
- Secrets are never knowledge-base content. Passwords, tokens, API keys, private keys, session cookies, production credentials, and production connection strings must not be reproduced in wiki pages, outputs, logs, or final responses.

## Most important rule: hearing selections must become actual directories

Do not only write the user's choices into `HOME.md`, `README.md`, or `AGENTS.md`.

- Every chosen or recommended Wiki item must become an actual numbered directory under `20_Wiki/`.
- Every chosen or recommended Outputs item must become an actual numbered directory under `30_成果物/`.
- Purpose-specific Raw Source categories should become actual numbered directories under `10_素材/` when useful.
- HOME selections must become headings in `HOME.md` when they are not already part of the default HOME sections.
- Privacy choices must become durable rules in `個人情報・機密情報ポリシー.md`, `AGENTS.md`, `CLAUDE.md`, `HOME.md`, `README.md`, `index.md`, `log.md`, and `wiki設定.json`.
- `README.md` must include a purpose-adaptive `このWikiの使い方プロンプト集` section. The prompt names, targets, actions, and output destinations must change according to the user's purpose and the actual directory tree.
- `directory_manifest.md` and `wiki設定.json` must preserve the mapping from selected items to actual directories.
- Before reporting completion, verify that the selected item names appear in the real directory tree.

Bad behavior:

```text
User chooses FAQ, 手順・マニュアル, トラブル対応集.
Agent only writes those words in HOME.md, but creates generic directories such as:
20_Wiki/01_概念/
30_成果物/01_レポート/
```

Correct behavior:

```text
20_Wiki/
├── 01_FAQ/
├── 02_手順・マニュアル/
└── 03_未解決・要確認事項/

30_成果物/
├── 01_トラブル対応集/
├── 02_業務チェックリスト/
└── 03_新人向けオンボーディングガイド/
```

## Base directory rule

Always create these top-level directories:

```text
00_受信箱/
10_素材/
20_Wiki/
30_成果物/
90_アーカイブ/
個人用メモ/
```

Do not automatically create generic child directories such as `20_Wiki/01_概念/` or `30_成果物/01_レポート/` unless the user's purpose or selections actually call for them.

For a Slack Q&A wiki, use Slack/Q&A-specific children. For a travel wiki, use travel-specific children. For a reading wiki, use book/reading-specific children. For an unknown purpose, infer useful child directories from the user's words.

Every empty directory must contain `.gitkeep`. `個人用メモ/` also receives `.gitkeep`, but `.gitignore` must exclude `/個人用メモ/` so it is not committed.

## Initial hearing flow

Ask the purpose first. Then tailor Wiki, Outputs, HOME, and privacy examples to that purpose.

If the user already provides the purpose and preferences in one message, do not ask again. Use what is available and proceed.

If the user says `お願いします`, `おまかせ`, `おすすめ`, `目的に合ったおすすめ`, `いい感じで`, `デフォルトで`, or gives an unclear answer after seeing options, treat that as `目的に合ったおすすめ` for Wiki/Outputs/HOME. For privacy, use `標準（おすすめ：人名のみそのまま）` unless the user gives a stricter or looser policy.

### 1. Purpose

Ask exactly:

```text
このWikiの目的を一言で言うと何ですか？
```

Show examples:

```text
例:
- MSPの業務で分からないことを聞くSlackチャンネルの情報をまとめる
- AI時代の個人知識管理を研究する
- 読書メモを構造化して、自分の考えを育てる
- 顧客調査・競合調査・プロダクト仮説を蓄積する
- 健康、習慣、心理状態の記録を長期的に整理する
- 旅行計画の調査資料を、あとから見返せるWikiにする
- 講義・教材・論文を整理して学習ノートにする
- 趣味や創作の設定・アイデアを長期的に整理する
```

Also ask for a project directory name only if useful. If none is given, use `LLM_Wiki`.

### 2. Wiki structure and required items

After the purpose is known, ask:

```text
Wikiの構成や、必ず欲しい項目はありますか？
```

Generate 6 to 10 purpose-specific options. Always include `目的に合ったおすすめ`.

Template:

```text
目的が「{目的}」なら、例えば以下が合いそうです。

選択肢:
- 目的に合ったおすすめ
- {目的別候補1}
- {目的別候補2}
- {目的別候補3}
- {目的別候補4}
- {目的別候補5}
- {目的別候補6}
```

Examples:

- 業務Q&A / Slack / MSP: `業務カテゴリ別Q&A`, `FAQ`, `手順・マニュアル`, `トラブルシューティング事例`, `ツール・システム別ナレッジ`, `用語集・略語集`, `担当者・問い合わせ先`, `Slackスレッド要約`, `未解決・要確認事項`, `エスカレーション基準`
- Research / investigation: `主要論点`, `先行研究・関連資料`, `ソース別要約`, `仮説・反証`, `用語集`, `矛盾・未解決`, `年表`, `引用・根拠メモ`
- Reading / books: `章別要約`, `登場人物・組織`, `重要テーマ`, `重要引用`, `伏線・論点`, `読書中の問い`, `感想・解釈`, `関連作品・背景知識`
- Business / product / customer research: `顧客セグメント`, `顧客インサイト`, `競合比較`, `課題仮説`, `プロダクト仮説`, `意思決定メモ`, `インタビュー要約`, `未検証リスク`
- Personal / self-improvement / health: `目標`, `習慣ログ`, `体調・心理パターン`, `トリガー`, `実験・検証`, `振り返り`, `注意事項`, `専門家に確認すること`
- Travel planning: `候補地`, `旅程`, `交通`, `宿泊`, `予算`, `持ち物`, `予約・未確定事項`, `現地メモ`
- Learning / course notes: `講義ノート`, `用語集`, `理解度チェック`, `問題・演習`, `復習計画`, `参考資料`, `未理解ポイント`, `試験対策`
- Creative / story / worldbuilding: `世界観`, `キャラクター`, `場所`, `プロット`, `伏線`, `設定ルール`, `矛盾チェック`, `アイデア断片`

For purposes not covered above, infer from the purpose. Prefer concrete categories that would be meaningful as Obsidian folders.

### 3. Required Outputs

Ask:

```text
Outputsに必ず欲しい項目はありますか？
```

Generate 5 to 9 purpose-specific options. Always include `目的に合ったおすすめ`.

Examples:

- 業務Q&A / Slack / MSP: `FAQ集`, `業務マニュアル`, `トラブル対応集`, `新人向けオンボーディングガイド`, `週次・月次Q&Aサマリー`, `用語集・略語集`, `業務チェックリスト`, `引き継ぎ資料`, `エスカレーション早見表`
- Research / investigation: `調査レポート`, `比較表`, `論点整理`, `仮説検証メモ`, `参考文献リスト`, `発表スライド`, `要旨・サマリー`
- Reading / books: `読書まとめ`, `章別サマリー`, `人物相関図`, `テーマ別メモ`, `引用集`, `感想文・レビュー`, `再読用メモ`
- Business / product / customer research: `競合比較表`, `顧客インサイトレポート`, `仮説検証メモ`, `意思決定メモ`, `提案資料`, `ロードマップ草案`, `調査サマリー`
- Personal / self-improvement / health: `週次レビュー`, `月次レポート`, `習慣チェックリスト`, `実験ログ`, `次アクション`, `振り返りテンプレート`
- Travel planning: `旅程表`, `予算表`, `持ち物リスト`, `予約一覧`, `候補比較表`, `当日用チェックリスト`, `旅行後まとめ`
- Learning / course notes: `学習まとめ`, `復習シート`, `問題集`, `理解度レポート`, `試験対策メモ`, `発表資料`
- Creative / story / worldbuilding: `企画書`, `キャラクター一覧`, `プロット表`, `設定資料`, `矛盾チェック表`, `章別アウトライン`

Outputs should be reusable deliverables, not simply another copy of Wiki categories.

### 4. HOME information

Ask:

```text
HOMEに表示したい情報はありますか？
```

Show purpose-specific examples, then always show default sections.

Template:

```text
目的が「{目的}」なら、HOMEには例えば以下を追加すると便利です。

目的別の例:
- {目的別HOME項目1}
- {目的別HOME項目2}
- {目的別HOME項目3}
- {目的別HOME項目4}

デフォルト:
いまの結論 / 現在の状態 / どこに何があるか / まず読むページ / 決まっていること / これから決めること / 触るときのルール / 次にやると良さそうなこと / 関連ページ
```

Default HOME sections are always included unless the user explicitly asks to remove them:

```text
いまの結論
現在の状態
どこに何があるか
まず読むページ
決まっていること
これから決めること
触るときのルール
次にやると良さそうなこと
関連ページ
```

If the user wants to know where each directory is, ensure `どこに何があるか` contains a detailed directory map.

### 5. Privacy and sensitive-information handling

After HOME is resolved, ask how to handle personal information and sensitive information before creating or updating the wiki.

Ask:

```text
個人情報・機密情報はどこまで伏せますか？
```

Always mention these categories:

```text
対象例:
- 人名
- 個人連絡先（メールアドレス、電話番号、住所、個人SNSなど）
- 人事情報（評価、異動、退職、病歴、勤怠、懲戒、給与など）
- 契約金額
- パスワード
- 本番環境の認証情報（APIキー、トークン、秘密鍵、セッション情報、接続文字列など）
```

Show these options exactly:

```text
選択肢:
- 標準（おすすめ：人名のみそのまま）
  - 業務上必要な担当者名やチーム名は残す
  - 個人連絡先・人事情報: 伏せる
  - 契約金額: 原則伏せる。必要な場合だけ金額帯などに丸める
  - パスワード・本番環境の認証情報: 常に伏せる。保存しない、引用しない、ログに残さない

- 人名含めてすべて伏せる
  - 人名: 役割名または匿名IDに置換する
  - 個人連絡先: 伏せる
  - 人事情報: 伏せる
  - 契約金額: 原則伏せる。
  - パスワード・本番環境の認証情報: 常に伏せる。保存しない、引用しない、ログに残さない

- 厳格
  - 人名・個人連絡先・人事情報・契約金額をすべて伏せる
  - パスワード・本番環境の認証情報は常に伏せる

- カスタム
  - カテゴリごとに「残す / 役割化する / 匿名ID化する / 金額帯に丸める / 完全に伏せる」を指定する
```

Mode meanings:

- `標準（おすすめ：人名のみそのまま）`: 業務上必要な担当者名、Slack表示名、チーム名はそのまま残す。個人連絡先、人事情報、パスワード、本番環境の認証情報は伏せる。契約金額は原則伏せ、必要な場合だけ金額帯などに丸める。
- `人名含めてすべて伏せる`: 人名は役割名または匿名IDに置換する。個人連絡先、人事情報、契約金額、パスワード、本番環境の認証情報は伏せる。人名が日常運用に不要な場合や、Wikiを広めに共有する場合に使う。
- `厳格`: 人名、個人連絡先、人事情報、契約金額、パスワード、本番環境の認証情報をすべて伏せる。金額帯や識別可能な引用も避ける。共有Wikiに入れるには危険な素材は、最小限の伏せ字メモだけにするか、git外で扱うことを提案する。
- `カスタム`: カテゴリごとの指定に従う。ただし、パスワード・APIキー・トークン・秘密鍵・本番環境の認証情報については、カスタム指定よりも「保存しない」ルールを優先する。

Purpose-specific privacy examples should be adapted. For example:

- 業務Q&A / Slack / MSP: 標準では業務上必要なSlack表示名、担当者名、チーム名を残す。`人名含めてすべて伏せる` では人名を役割名または匿名IDにする。`厳格` では識別につながる細部や金額帯も避ける。個人連絡先、人事情報、契約金額、トークン、認証情報は伏せる。
- Research: 公開論文の著者名など公開情報として必要な人名は標準で残せる。非公開インタビュー対象者名は、共有範囲に応じて `人名含めてすべて伏せる`、`厳格`、または `カスタム` を提案する。
- Reading: 作品内の登場人物名は残す。実在の知人名は、共有予定がある場合 `人名含めてすべて伏せる`、`厳格`、または `カスタム` を提案する。
- Personal/health: 標準でも、個人連絡先、人事情報、病歴、通院先、家族情報、個人識別子は慎重に扱う。共有予定がある場合は `厳格` または `カスタム` を提案する。
- Travel: 標準では必要な同行者名を残せるが、予約番号、住所、電話番号、個人連絡先は伏せる。
- Customer/product research: 標準では業務上必要な顧客担当者名や社内担当者名を残せる。共有範囲によっては `人名含めてすべて伏せる`、`厳格`、または `カスタム` を提案する。契約条件や金額は伏せるかレンジ化する。

If the user says `おすすめ`, `おまかせ`, or gives no privacy preference, use `標準（おすすめ：人名のみそのまま）`.

## Non-negotiable secret handling

Regardless of the selected policy, never write raw values of the following into wiki pages, outputs, logs, `index.md`, `HOME.md`, `README.md`, `AGENTS.md`, `CLAUDE.md`, `wiki設定.json`, or final responses:

- passwords
- one-time passwords
- API keys
- access tokens
- refresh tokens
- session cookies
- private keys
- SSH keys
- database credentials
- production connection strings
- production environment authentication information

Replace them with placeholders such as:

```text
[SECRET_REDACTED]
[PROD_CREDENTIAL_REDACTED]
[API_KEY_REDACTED]
[TOKEN_REDACTED]
[PRIVATE_KEY_REDACTED]
```

Do not create a mapping file from placeholders back to raw secrets. If the user explicitly asks to keep real secret values, decline to store them in the wiki and suggest using an approved secrets manager outside this repository.

## Selection resolution rules

1. `目的に合ったおすすめ` means: choose a coherent recommended set based on the purpose.
2. Do not preserve generic defaults when the purpose-specific set is known.
3. If a selected item contains an explanatory parenthetical, use the concise label for the directory name and keep the full label in `directory_manifest.md`.
4. If the user gives multiple comma-separated items, split them into separate items unless clearly one phrase.
5. Preserve Japanese labels where practical. Replace `/` and `\` with `・`. Avoid `:`, `*`, `?`, `"`, `<`, `>`, `|` in file or directory names.
6. Use stable numbering: `01_`, `02_`, `03_`. Do not renumber existing non-empty directories unless the user asks.
7. When updating an existing Wiki, never delete non-empty directories. Empty unselected generic directories may be removed or moved to `90_アーカイブ/` only if the user asked to clean them up.
8. Privacy choices become durable configuration, not just chat context.
9. If a privacy choice conflicts with secret safety, secret safety wins.

## Privacy application rules

Before ingesting or summarizing sources:

1. Read `個人情報・機密情報ポリシー.md` and `wiki設定.json`.
2. Inspect the source for personal information, contract-sensitive details, and secrets.
3. Apply the selected policy before writing generated content to `20_Wiki/`, `30_成果物/`, `HOME.md`, `index.md`, or `log.md`.
4. Do not quote raw sensitive values in the final response.
5. If a source contains secrets or production credentials, do not reproduce them. Mention only that sensitive credentials were detected and redacted.

Name handling by mode:

- In `標準（おすすめ：人名のみそのまま）`, keep operationally necessary responsible-person names, Slack display names, and team names as written unless the user asks otherwise. Do not attach personal contact details, HR/personnel information, contract amounts, or secrets to those names.
- In `人名含めてすべて伏せる`, replace human names with roles or anonymous IDs such as `[PERSON_001]` or `[ROLE:営業担当]` when context is needed.
- In `厳格`, fully redact human names or use only generic placeholders such as `[PERSON_REDACTED]`. Avoid stable IDs when they could make a person re-identifiable.
- In `カスタム`, follow the user's category-specific choice.

Suggested placeholders:

```text
[PERSON_001]
[PERSON_002]
[ROLE:営業担当]
[TEAM:運用チーム]
[EMAIL_REDACTED]
[PHONE_REDACTED]
[ADDRESS_REDACTED]
[HR_INFO_REDACTED]
[AMOUNT_REDACTED]
[AMOUNT_RANGE:100万円台]
[CONTRACT_DETAIL_REDACTED]
[SECRET_REDACTED]
[PROD_CREDENTIAL_REDACTED]
```

When using anonymous person IDs, keep them consistent within the same page when possible. Do not create a real-name mapping unless the user explicitly asks. If a mapping is needed, place it under `個人用メモ/` and do not index, summarize, cite, or commit it.

Raw sources are usually immutable, but privacy still matters:

- Do not modify raw sources unless the user explicitly asks.
- If a raw source contains secrets, advise the user not to commit it.
- If the wiki is a git repository, ensure `.gitignore` contains common secret-file patterns.
- Prefer creating redacted summaries in `20_Wiki/` rather than copying sensitive raw text.
- If a source is too sensitive for the shared repository, suggest moving it to `個人用メモ/` or another non-git private location.

`log.md` should record that redaction was applied, but must not contain raw sensitive values.

Good:

```markdown
## [2026-05-09] ingest | Slackスレッド: VPN接続トラブル
- FAQとトラブルシューティングを更新。
- メールアドレス1件、認証情報らしき文字列1件をポリシーに従って伏せた。人名は選択ポリシーに従って扱った。
```

Bad:

```markdown
- APIキー: sk-...
- パスワード: ...
```

## Wiki growth rules

This wiki is not a fixed template. It grows as rough information is added.

Create a new Markdown page under `20_Wiki/` when:

- it is a reusable FAQ, procedure, troubleshooting case, concept, entity, comparison, or note;
- it would be searched for later as an independent topic;
- adding it to an existing page would make that page too long or hard to use;
- it captures a new unresolved issue, contradiction, or decision point.

Update an existing page instead when:

- the information is a supplement to an existing FAQ;
- it is a variation of an existing procedure or troubleshooting case;
- it clarifies an existing concept, tool, project, entity, or term;
- users would expect to find it on an existing page.

Create a new child directory only when:

- multiple pages are likely to belong there;
- existing directories do not fit the material;
- the category is meaningful enough to explain in `HOME.md` and `index.md`;
- it supports the wiki's stated purpose.

Do not create a new directory for a single isolated item unless the user explicitly asks.

When adding or changing directories, update the real directory tree, `.gitkeep`, `directory_manifest.md`, `wiki設定.json`, `HOME.md`, `index.md`, and `log.md` together.

## Directory creation workflow

Create this base structure:

```text
{Project}/
├── 00_受信箱/
├── 10_素材/
├── 20_Wiki/
├── 30_成果物/
├── 90_アーカイブ/
├── 個人用メモ/
├── .gitignore
├── AGENTS.md
├── CLAUDE.md
├── HOME.md
├── README.md
├── index.md
├── log.md
├── directory_manifest.md
├── 個人情報・機密情報ポリシー.md
└── wiki設定.json
```

Then add purpose-specific child directories.

Raw source directory examples:

- Slack Q&A: `01_Slackスレッド`, `02_Slackエクスポート`, `03_添付ファイル`, `04_スクリーンショット`, `05_関連ドキュメント`
- Research: `01_論文`, `02_記事`, `03_レポート`, `04_データ`, `05_画像・図表`
- Reading: `01_章メモ`, `02_引用`, `03_関連資料`, `04_画像`, `05_メモ断片`
- Travel: `01_候補地メモ`, `02_予約情報`, `03_地図・交通`, `04_画像`, `05_リンク集`
- Unknown purpose: `01_未分類素材`, `02_添付ファイル`

Wiki directories must use the resolved Wiki items exactly:

```text
20_Wiki/01_<Wiki項目1>/
20_Wiki/02_<Wiki項目2>/
20_Wiki/03_<Wiki項目3>/
```

Outputs directories must use the resolved Outputs items exactly:

```text
30_成果物/01_<Outputs項目1>/
30_成果物/02_<Outputs項目2>/
30_成果物/03_<Outputs項目3>/
```

## Required root files

### `.gitignore`

Must include:

```gitignore
# Private notes are not part of the wiki or git history
/個人用メモ/

# Obsidian local workspace/cache noise
.obsidian/workspace*
.obsidian/cache/
.trash/

# OS/editor noise
.DS_Store
Thumbs.db
*.swp
*.tmp

# Secrets and credentials should not be committed
.env
.env.*
*.pem
*.key
*.p12
*.pfx
id_rsa*
id_ed25519*
credentials.json
*credentials*.json
secrets.*
*secret*.txt
*token*.txt
```

### `HOME.md`

Must include:

- Purpose
- `いまの結論`
- `現在の状態`
- `どこに何があるか` with a directory map table
- `まず読むページ`
- `決まっていること`
- `これから決めること`
- `触るときのルール`
- `個人情報・機密情報の扱い`
- `次にやると良さそうなこと`
- `関連ページ`
- Any purpose-specific HOME sections selected or recommended

The privacy section should link to `個人情報・機密情報ポリシー.md` and summarize the selected mode without exposing sensitive values.


### `README.md`

Must explain how to use the wiki and include a copy-pasteable `このWikiの使い方プロンプト集` section.

Most important: the README prompt collection is not a fixed generic checklist. Generate it from the actual wiki that was just created or updated.

Use these inputs when writing the prompt collection:

- the user's purpose;
- the actual `10_素材/` child directories;
- the actual `20_Wiki/` child directories;
- the actual `30_成果物/` child directories;
- selected or recommended HOME sections;
- the selected privacy/sensitive-information policy;
- the likely real-world workflow implied by the purpose.

Do not copy the same prompt text into every README. It is acceptable to keep the same broad prompt families, but their titles, nouns, action verbs, target directories, and completion reports must be purpose-specific.

Good examples of adaptation:

- For an MSP Slack Q&A wiki, do not only say `Wikiに整理する`. Say things like `SlackスレッドをFAQ・手順・トラブル事例に整理する`, `未解決・要確認事項を棚卸しする`, `新人向けオンボーディングガイドを更新する`, and point to actual directories such as `20_Wiki/02_FAQ/`, `20_Wiki/03_手順・マニュアル/`, `20_Wiki/04_トラブルシューティング事例/`, and `30_成果物/04_新人向けオンボーディングガイド/`.
- For a travel planning wiki, say things like `予約情報を旅程・交通・宿泊に反映する`, `予算表を更新する`, `当日用チェックリストを作る`, and point to actual travel directories.
- For a research wiki, say things like `新しい論文をソース別要約・主要論点・仮説に統合する`, `矛盾・未解決を点検する`, `調査レポートを作る`, and point to actual research directories.
- For a reading wiki, say things like `章メモから章別要約・登場人物・重要テーマを更新する`, `伏線・論点を整理する`, `読書まとめを作る`, and point to actual reading directories.

The README should use this structure:

```markdown
# README

## このWikiの目的
{目的}

## 基本の考え方
- `00_受信箱/` は未整理の投入口。
- `10_素材/` は元情報。原則として改変しない。
- `20_Wiki/` はLLMが整理・統合した知識。
- `30_成果物/` は共有・配布・提出しやすい形にまとめた成果物。
- `90_アーカイブ/` は古いもの、使わなくなったもの、履歴として残すもの。
- `個人用メモ/` はWikiにもgitにも取り込まない。
- 個人情報・機密情報は `個人情報・機密情報ポリシー.md` に従う。

## このWikiの使い方プロンプト集
{目的に合わせて生成したプロンプト集}
```

### README prompt generation rules

Create 8 to 12 prompts. Include the four base workflow families below, but adapt their titles and bodies to the purpose. Also include prompts for asking the wiki, saving a useful chat answer, running a wiki health check, and checking privacy/sensitive information. These additional prompts must also be purpose-specific, not generic.

Base workflow families:

1. `00_受信箱/`に入ったものを、目的に合う素材ディレクトリへ分類する。
2. `10_素材/`の素材を、目的に合う`20_Wiki/`ページへ整理・統合する。
3. `20_Wiki/`の内容を、目的に合う`30_成果物/`へまとめる。
4. `HOME.md`を、目的に合う現在地・状態・次アクションが分かるように更新する。

Then add 2 to 6 purpose-specific prompts derived from the selected Wiki and Outputs directories. These should cover the real ways the user will use the wiki after it is created. Examples:

- MSP/Slack Q&A: `FAQだけ更新する`, `手順・マニュアルだけ更新する`, `トラブルシューティング事例を追加する`, `未解決・要確認事項を棚卸しする`, `エスカレーション早見表を更新する`, `新人向けオンボーディングガイドを作る`.
- Research: `新しい論文を取り込む`, `論点整理を更新する`, `仮説・反証を更新する`, `参考文献リストを整える`, `調査レポートを作る`.
- Reading: `章メモを取り込む`, `登場人物・テーマを更新する`, `重要引用を整理する`, `読書まとめを作る`.
- Product/customer research: `インタビュー要約を取り込む`, `顧客インサイトを更新する`, `競合比較を作る`, `意思決定メモを書く`.
- Travel: `予約情報を取り込む`, `旅程表を更新する`, `予算表を更新する`, `持ち物リストを作る`, `当日用チェックリストを作る`.
- Learning: `講義ノートを取り込む`, `未理解ポイントを整理する`, `復習シートを作る`, `試験対策メモを作る`.
- Creative/story: `設定メモを取り込む`, `キャラクター情報を更新する`, `プロット表を作る`, `矛盾チェックをする`.

Each prompt must contain:

- a purpose-specific title;
- target directories using actual paths from this wiki;
- `最初に読むもの`, usually `AGENTS.md`, `wiki設定.json`, `個人情報・機密情報ポリシー.md`, `HOME.md`, `index.md`, and recent `log.md` when appropriate;
- concrete actions based on the selected directories;
- where to write the result;
- whether to update `HOME.md`, `index.md`, `directory_manifest.md`, and `log.md`;
- privacy/sensitive-information instructions;
- `個人用メモ/` is never read, moved, summarized, indexed, cited, or committed;
- a completion report format.


Required additional wiki-usage prompts:

- A purpose-specific `このWikiに質問する` prompt. It should instruct the agent to read `index.md` first, then relevant pages, and answer with links to wiki pages. For MSP, the wording should be about asking operational Q&A; for travel, asking about itinerary or reservations; for research, asking about hypotheses or evidence.
- A purpose-specific `この回答をWikiに保存する` prompt. It should instruct the agent to decide whether the answer belongs in `20_Wiki/` or `30_成果物/`, then update related pages, `index.md`, and `log.md`.
- A purpose-specific health-check prompt. It should check for stale pages, contradictions, orphan pages, index gaps, and domain-specific gaps. For MSP, include outdated procedures, unresolved questions, and missing escalation criteria. For travel, include expired reservations or missing booking details. For research, include unsupported claims, missing citations, and contradictions.
- A purpose-specific privacy/sensitive-information check prompt. It should inspect generated files without printing secrets or raw sensitive values.

Avoid vague generic phrases when a concrete purpose-specific phrase is available. For example, in an MSP Slack Q&A wiki prefer `Slackスレッドの質問・回答・前提・結論・未解決を抜き出す` over `素材を要約する`.

### Base prompt family templates to adapt

Use these as templates, not as final fixed text. Replace the bracketed parts with actual purpose-specific directories, labels, and tasks.

#### 1. Inbox classification prompt

```markdown
### {目的別タイトル: 例 Slackスレッドを素材ディレクトリへ分類する}

`00_受信箱/` に入っているものを、このWikiの目的に合わせて分類してください。

最初に読むもの:
- `AGENTS.md`
- `wiki設定.json`
- `個人情報・機密情報ポリシー.md`
- `directory_manifest.md`

対象:
- `00_受信箱/`

分類先の候補:
- `{10_素材/実ディレクトリ1}/`: {用途説明}
- `{10_素材/実ディレクトリ2}/`: {用途説明}
- `{10_素材/実ディレクトリ3}/`: {用途説明}

やってほしいこと:
- {目的に合う分類観点を書く。例: Slackの場合は質問・回答・添付・スクリーンショット・関連ドキュメントで分ける}
- 元情報は素材として扱い、勝手に大きく書き換えない
- 分類先が分からないものは、最も近い場所に置くか、必要なら `{10_素材/未分類系ディレクトリ}` を作って理由を残す
- `個人用メモ/` は読まない・移動しない・分類しない
- 個人情報・機密情報は `個人情報・機密情報ポリシー.md` に従う
- 移動・追加内容を `log.md` に追記する
- 必要なら `directory_manifest.md` と `index.md` を更新する

完了時に教えてほしいこと:
- 何をどこへ分類したか
- 判断に迷ったもの
- 新しく作ったディレクトリ
- 機密情報・個人情報の扱いで注意した点
```

#### 2. Wiki integration prompt

```markdown
### {目的別タイトル: 例 Slack Q&AをFAQ・手順・トラブル事例に整理する}

`10_素材/` の素材を、このWikiの目的に合わせて `20_Wiki/` に整理・統合してください。

最初に読むもの:
- `AGENTS.md`
- `wiki設定.json`
- `個人情報・機密情報ポリシー.md`
- `HOME.md`
- `index.md`
- `log.md` の直近の更新

対象:
- `{10_素材/実ディレクトリ1}/`
- `{10_素材/実ディレクトリ2}/`

主な反映先:
- `{20_Wiki/実ディレクトリ1}/`: {ここに何を書くか}
- `{20_Wiki/実ディレクトリ2}/`: {ここに何を書くか}
- `{20_Wiki/実ディレクトリ3}/`: {ここに何を書くか}

やってほしいこと:
- {目的に合う抽出観点を書く。例: Slack Q&Aなら質問、背景、結論、手順、例外、担当者、未解決を抽出する}
- 既存ページに追記できる内容は既存ページを更新する
- 独立して検索されそうなテーマは新しいMarkdownページにする
- 関連ページ同士にObsidianリンクを張る
- 根拠となる素材への参照を残す
- 未解決・要確認事項は該当ディレクトリまたはページに記録する
- 新しいディレクトリは、本当に必要なときだけ作る
- `index.md` と `log.md` を更新する
- 個人情報・機密情報はポリシーに従って扱う

完了時に教えてほしいこと:
- 新しく作ったページ
- 更新したページ
- どの素材から何を反映したか
- 未解決・要確認事項
- 次に取り込むとよさそうな素材
```

#### 3. Output creation prompt

```markdown
### {目的別タイトル: 例 新人向けオンボーディングガイドを作る}

`20_Wiki/` の内容をもとに、`30_成果物/` に成果物を作ってください。

作りたい成果物:
- {実際の `30_成果物/` ディレクトリから選ばせる。例: FAQ集 / 業務マニュアル / トラブル対応集 / 新人向けオンボーディングガイド}

主な参照元:
- `{20_Wiki/実ディレクトリ1}/`
- `{20_Wiki/実ディレクトリ2}/`
- `{20_Wiki/実ディレクトリ3}/`

出力先:
- `{30_成果物/実ディレクトリ}/`

やってほしいこと:
- `index.md` を見て関連ページを探す
- 読む人がそのまま使える構成にする
- 重要な前提・決定事項・未決事項を分けて書く
- 根拠となるWikiページや素材への参照を残す
- 個人情報・機密情報はポリシーに従って伏せる
- 作成した成果物を適切な `30_成果物/` サブディレクトリに置く
- `index.md` と `log.md` を更新する

完了時に教えてほしいこと:
- 作成した成果物
- 参照した主なWikiページ
- まだ不足している情報
- 次に作るとよさそうな成果物
```

#### 4. HOME update prompt

```markdown
### {目的別タイトル: 例 MSP Q&A Wikiの現在地をHOME.mdに反映する}

現在のWikiの状態が分かるように `HOME.md` を更新してください。

やってほしいこと:
- `いまの結論` を、このWikiの目的に照らして最新化する
- `現在の状態` を最新化する
- `どこに何があるか` のディレクトリ説明を、実際のディレクトリ構成に合わせて更新する
- `まず読むページ` を最新化する
- `決まっていること` と `これから決めること` を更新する
- `触るときのルール` に必要な注意を追加する
- `次にやると良さそうなこと` を更新する
- `関連ページ` を更新する
- {目的別HOME項目。例: MSPなら最近よくある質問、重要な未解決事項、エスカレーション注意点。旅行なら現在の旅程案、未予約、予算状況。研究なら現在の仮説、重要論点、未読資料。}
- 個人情報・機密情報の扱いを `個人情報・機密情報ポリシー.md` と矛盾しないようにする
- 更新内容を `log.md` に追記する

完了時に教えてほしいこと:
- HOME.mdで更新した項目
- 今のWikiの状態の要約
- 次にやるとよさそうなこと
```

#### 5. Ask-the-wiki prompt

```markdown
### {目的別タイトル: 例 このMSP業務Q&A Wikiに質問する}

このWikiを使って、次の質問に答えてください。

質問:
- {ここに質問を書く}

最初に読むもの:
- `index.md`
- `HOME.md`
- 関連しそうな `20_Wiki/` のページ
- 必要に応じて `log.md` の直近の更新

やってほしいこと:
- `index.md` から関連ページを探す
- 関連ページを読んで、目的に合った形で答える
- 根拠になったWikiページへのObsidianリンクを入れる
- 分からないこと、古そうな情報、追加確認が必要なことは明示する
- 個人情報・機密情報ポリシーに従う

完了時に教えてほしいこと:
- 回答
- 参照したページ
- 未確認・要確認の点
- この回答をWikiに保存した方がよいか
```

#### 6. Save-answer prompt

```markdown
### {目的別タイトル: 例 この回答をMSPナレッジとして保存する}

今の回答を、このWikiで再利用できる知識として保存してください。

やってほしいこと:
- `20_Wiki/` に入れるべき知識か、`30_成果物/` に置くべき成果物か判断する
- 既存ページに追記できるなら追記する
- 独立ページにした方がよければ、実際の目的に合うディレクトリに新規Markdownページを作る
- 関連ページへのObsidianリンクを追加する
- `index.md` と `log.md` を更新する
- 個人情報・機密情報ポリシーに従う

完了時に教えてほしいこと:
- 保存先
- 更新したページ
- なぜその場所に保存したか
```

#### 7. Health-check prompt

```markdown
### {目的別タイトル: 例 MSPナレッジの健康診断をする}

このWikiの健康診断をしてください。

確認してほしいこと:
- 古くなっていそうなページ
- 矛盾している記述
- 孤立ページやリンク切れ
- `index.md` に載っていないページ
- `HOME.md` の現在の状態と実態のズレ
- {目的別の点検項目。例: MSPなら未解決Q&A、古い手順、足りないエスカレーション基準。旅行なら未予約、期限切れ予約、予算未更新。研究なら根拠不足、矛盾、未読重要資料。}
- 個人情報・機密情報ポリシーに反していそうな生成物

ルール:
- 勝手に大きく変更せず、まず改善案を一覧にする
- 軽微なリンク修正や `index.md` 反映は行ってよい
- 修正した場合は `log.md` に記録する
- 秘密情報や本番認証情報は出力しない

完了時に教えてほしいこと:
- 問題の一覧
- 優先度
- 修正したもの
- 次に対応すべきこと
```

#### 8. Privacy-check prompt

```markdown
### {目的別タイトル: 例 個人情報・契約情報・認証情報の混入を点検する}

生成済みのWikiページと成果物について、`個人情報・機密情報ポリシー.md` に反していないか点検してください。

対象:
- `20_Wiki/`
- `30_成果物/`
- `HOME.md`
- `index.md`
- `log.md`
- `README.md`
- `AGENTS.md`
- `CLAUDE.md`

ルール:
- Raw sources は、明示的に依頼しない限り改変しない
- パスワード、APIキー、トークン、秘密鍵、本番環境の認証情報は出力しない
- 問題があれば、実値を表示せずに伏せ字へ修正する
- `log.md` には、検出カテゴリと対応内容だけを記録する

完了時に教えてほしいこと:
- 修正したファイル
- 修正カテゴリ
- 今後の注意点
```

### Purpose-specific README examples

These are examples of the style. Do not force these exact directories if the actual directory tree differs.

#### MSP / Slack Q&A example prompt titles

```markdown
### 1. Slackスレッドを素材ディレクトリへ分類する
### 2. Slack Q&AをFAQ・手順・トラブル事例に整理する
### 3. 未解決・要確認事項を棚卸しする
### 4. FAQ集を更新する
### 5. トラブル対応集を作る
### 6. 新人向けオンボーディングガイドを更新する
### 7. エスカレーション早見表を更新する
### 8. HOME.mdに最近の重要Q&Aと未解決事項を反映する
```

#### Research example prompt titles

```markdown
### 1. 新しい論文・記事を素材ディレクトリへ分類する
### 2. 新しい資料を主要論点・ソース別要約・仮説に統合する
### 3. 矛盾・未解決・反証候補を点検する
### 4. 調査レポートを作る
### 5. 比較表を作る
### 6. 参考文献リストを更新する
### 7. HOME.mdに現在の結論・仮説・次に読む資料を反映する
```

#### Travel example prompt titles

```markdown
### 1. 旅行メモ・予約情報を素材ディレクトリへ分類する
### 2. 素材を旅程・交通・宿泊・予算に反映する
### 3. 未予約・未確定事項を棚卸しする
### 4. 旅程表を更新する
### 5. 予算表を更新する
### 6. 持ち物リストと当日用チェックリストを作る
### 7. HOME.mdに現在の旅程案・予算状況・次の予約アクションを反映する
```

### README prompt quality checklist

Before finishing README.md, verify:

- The section name is `このWikiの使い方プロンプト集`.
- Prompt titles mention the actual purpose or domain, not only generic labels.
- Prompts use actual directories that exist in the project.
- Prompts mention the selected Wiki directories and Outputs directories.
- The user's original four workflow ideas are represented: inbox classification, wiki organization, output creation, and HOME update.
- Purpose-specific prompts are included beyond the four base workflows.
- Prompts include privacy/sensitive-information policy and `個人用メモ/` exclusions.
- Prompts are copy-pasteable into a new agent session without requiring hidden context.
- If the actual directory tree changes later, README prompt targets should be updated too.

### `index.md`

Must be content-oriented. Start with links to root files and directory categories. Include item-to-directory mapping tables for Raw Sources, Wiki, and Outputs. Include a link to `個人情報・機密情報ポリシー.md`.

### `log.md`

Must be append-only and start with:

```markdown
## [YYYY-MM-DD] init | LLM Wiki初期化
```

Record purpose, selected Wiki items, selected Outputs items, selected HOME items, selected privacy mode, and the fact that directories were created. Do not record raw sensitive values.

### `AGENTS.md`

This is the canonical agent instruction file. It must say:

- Read `wiki設定.json`, `個人情報・機密情報ポリシー.md`, `HOME.md`, `index.md`, and recent `log.md` before major work.
- Do not read or index `個人用メモ/`.
- Raw sources are immutable.
- Apply the privacy/sensitive-information policy before writing generated wiki pages, outputs, `HOME.md`, `index.md`, or `log.md`.
- Never reproduce passwords, API keys, access tokens, private keys, session cookies, production credentials, or production connection strings.
- New reusable answers should be saved into the best matching Wiki or Outputs directory.
- When adding/changing categories, update the actual directory tree, `directory_manifest.md`, `wiki設定.json`, `HOME.md`, `index.md`, and `log.md` together.
- Use the directory mapping table to avoid dumping content into generic folders.
- Keep the `README.md` `このWikiの使い方プロンプト集` aligned with the actual directory tree, privacy policy, and purpose-specific workflow.

### `CLAUDE.md`

Keep this short and point to `AGENTS.md` and the privacy policy:

```markdown
# CLAUDE.md

このリポジトリで作業する場合は、まず `AGENTS.md` と `個人情報・機密情報ポリシー.md` を読んでください。
`個人用メモ/` は読まない・要約しない・リンクしない・gitに含めないでください。
パスワード、APIキー、トークン、秘密鍵、本番環境の認証情報は保存・引用・出力しないでください。
```

### `directory_manifest.md`

Must include a table with columns:

```text
Layer | Selected item | Directory | Notes
```

This is the easiest place to verify that hearing selections became directories.

### `個人情報・機密情報ポリシー.md`

Create this file at the project root. It must include:

```markdown
# 個人情報・機密情報ポリシー

## 適用範囲
このポリシーは、20_Wiki、30_成果物、HOME.md、index.md、log.md、README.md、AGENTS.md、CLAUDE.md、directory_manifest.md、wiki設定.json に適用する。
Raw sources は原則として改変しないが、生成物にはこのポリシーを適用する。

## 選択したモード
{標準（おすすめ：人名のみそのまま） / 人名含めてすべて伏せる / 厳格 / カスタム}

## カテゴリ別ルール
| カテゴリ | 扱い | 例 |
|---|---|---|
| 人名 | {扱い} | 標準: 山田太郎 / 人名含めてすべて伏せる: [PERSON_001] / 厳格: [PERSON_REDACTED] |
| 個人連絡先 | {扱い} | [EMAIL_REDACTED] |
| 人事情報 | {扱い} | [HR_INFO_REDACTED] |
| 契約金額 | {扱い} | [AMOUNT_REDACTED] / [AMOUNT_RANGE:100万円台] |
| パスワード | 常に伏せる | [SECRET_REDACTED] |
| 本番環境の認証情報 | 常に伏せる | [PROD_CREDENTIAL_REDACTED] |

## 絶対に保存しないもの
- パスワード
- APIキー
- アクセストークン
- リフレッシュトークン
- セッションCookie
- 秘密鍵
- SSH鍵
- DB認証情報
- 本番環境の接続文字列

## 取り込み時のルール
- 選択したモードに従って、生成物へ書く前に必要な伏せ字処理をする。
- 標準（おすすめ：人名のみそのまま）の場合、業務上必要な担当者名・Slack表示名・チーム名は残し、個人連絡先・人事情報・契約金額・秘密情報を伏せる。
- log.md には伏せた件数やカテゴリだけを書き、実値は書かない。
- 判断に迷う情報は伏せる。ただし標準モードで、業務上必要な人名・チーム名かどうかだけが迷点の場合は、必要性を判断して残してよい。
- 必要ならユーザーに確認する。
```

### `wiki設定.json`

Must include at least:

```json
{
  "version": "1.9.1",
  "purpose": "...",
  "created": "YYYY-MM-DD",
  "source_directories": [],
  "wiki_directories": [],
  "output_directories": [],
  "home_sections": [],
  "private_directory": "個人用メモ",
  "privacy_policy_file": "個人情報・機密情報ポリシー.md",
  "privacy": {
    "mode": "標準（おすすめ：人名のみそのまま）",
    "rules": {
      "人名": "業務上必要な担当者名やチーム名は残す",
      "個人連絡先": "伏せる",
      "人事情報": "伏せる",
      "契約金額": "原則伏せる。必要な場合だけ金額帯に丸める",
      "パスワード": "常に伏せる。保存しない、引用しない、ログに残さない",
      "本番環境の認証情報": "常に伏せる。保存しない、引用しない、ログに残さない"
    },
    "never_store": [
      "passwords",
      "api_keys",
      "access_tokens",
      "refresh_tokens",
      "session_cookies",
      "private_keys",
      "ssh_keys",
      "database_credentials",
      "production_connection_strings"
    ]
  },
  "rules": [
    "selected Wiki items must be actual directories under 20_Wiki",
    "selected Output items must be actual directories under 30_成果物",
    "個人用メモ is excluded from wiki processing and git",
    "apply privacy policy before writing generated wiki content",
    "never store raw secrets or production credentials"
  ]
}
```

## Existing wiki update workflow

When the user already has a wiki and wants this Skill applied:

1. Inspect the existing tree.
2. Identify current generic directories that were not selected, especially under `20_Wiki/` and `30_成果物/`.
3. Resolve the intended purpose-specific Wiki and Outputs items.
4. Resolve or ask for the privacy/sensitive-information policy.
5. Add missing purpose-specific directories.
6. Add or update `個人情報・機密情報ポリシー.md`.
7. Update `AGENTS.md`, `CLAUDE.md`, `.gitignore`, and `wiki設定.json` so the privacy policy is durable.
8. Do not delete non-empty directories.
9. Empty generic directories may be removed or archived if the user requested cleanup.
10. Update `.gitkeep`, `directory_manifest.md`, `wiki設定.json`, `HOME.md`, `index.md`, `README.md` including the purpose-adaptive `このWikiの使い方プロンプト集`, `AGENTS.md`, and `log.md`.
11. Verify with a final tree and a mapping table.

If asked to audit existing pages for privacy issues:

- Scan generated wiki pages and outputs.
- Redact according to the current policy.
- Do not print discovered secrets to the user.
- Record only categories/counts in `log.md`.
- Do not rewrite raw sources unless the user explicitly requests it.

## Verification before final response

Before saying the work is done, verify these points:

- `20_Wiki/` contains the resolved Wiki items as real directories.
- `30_成果物/` contains the resolved Outputs items as real directories.
- `10_素材/` is purpose-specific or intentionally minimal.
- Empty directories have `.gitkeep`.
- `.gitignore` excludes `/個人用メモ/` and common secret files.
- `HOME.md` has the default sections, purpose-specific sections, and `個人情報・機密情報の扱い`.
- `個人情報・機密情報ポリシー.md` exists and matches the user's selected privacy policy.
- `AGENTS.md` and `CLAUDE.md` instruct agents to apply the privacy policy.
- `directory_manifest.md` and `wiki設定.json` match the real tree and include privacy configuration.
- `README.md` includes `このWikiの使い方プロンプト集` whose prompts are adapted to the actual purpose and directory tree. The base prompt families must be present, but their names and content must be purpose-specific, not a fixed generic list.
- `log.md` has an init or update entry and does not contain raw sensitive values.

Final response should include:

- Created or updated path.
- Short explanation of the purpose-specific directories created.
- Confirmation that selected Wiki and Outputs items were reflected as directories.
- Confirmation that privacy/sensitive-information policy was recorded.
- Confirmation that `README.md` contains purpose-adapted wiki usage prompts.
- A brief next step, such as where to put raw sources.
