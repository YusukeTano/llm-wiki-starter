---
name: llm-wiki-starter
version: 1.6.2
description: Create and update an Obsidian-ready Japanese LLM Wiki. Use this when the user wants a purpose-specific raw-sources-to-wiki knowledge base where hearing selections become real directories and privacy/sensitive-information rules are recorded before ingest.
---

# LLM Wiki Starter Skill v1.6.2 - no-script, generic, privacy-aware

Use this skill when the user wants to create or update an Obsidian-ready LLM Wiki: a directory/repository where rough sources such as Slack threads, articles, notes, PDFs, data, images, meeting notes, research material, reading notes, travel plans, health logs, customer interviews, or project documents are incrementally turned by an LLM into a persistent, interlinked Markdown wiki.

This version intentionally has no directory-creation script. The agent must design and create the directory tree directly from the user's purpose, selections, and privacy policy.

## Core philosophy

1. Raw sources are immutable. The LLM may read source materials, but should not rewrite them unless the user explicitly asks.
2. The wiki is the compiled knowledge layer. The LLM creates, updates, links, reconciles, and maintains Markdown wiki pages.
3. Schema/rule files discipline the agent. `AGENTS.md` and `CLAUDE.md` tell future LLM agents how to maintain the wiki.
4. `index.md` and `log.md` are mandatory. `index.md` is the content-oriented catalog. `log.md` is the chronological append-only history.
5. Useful answers should compound. Reusable FAQs, comparisons, syntheses, decision memos, manuals, reports, summaries, and checklists should be saved back into the wiki or outputs.
6. Private notes stay private. Never read, summarize, index, cite, commit, or link anything under `個人用メモ/` unless the user explicitly moves it elsewhere.
7. Privacy is designed before ingest. Ask how personal information and sensitive information should be handled, record the decision, and apply it to generated wiki pages and outputs.
8. Secrets are never knowledge-base content. Passwords, tokens, API keys, private keys, and production authentication information must not be reproduced in the wiki, outputs, logs, or final responses.

## Most important rule: hearing selections must become actual directories

Do not only write the user's choices into `HOME.md`, `README.md`, or `AGENTS.md`.

- Every chosen or recommended Wiki item must become an actual numbered directory under `20_Wiki/`.
- Every chosen or recommended Outputs item must become an actual numbered directory under `30_成果物/`.
- Purpose-specific Raw Source categories should become actual numbered directories under `10_素材/` when useful.
- HOME selections must become headings in `HOME.md` when they are not already part of the default HOME sections.
- Privacy choices must become actual rules in `個人情報・機密情報ポリシー.md`, `AGENTS.md`, `wiki設定.json`, and `HOME.md`.
- The generated `directory_manifest.md` and `wiki設定.json` must preserve the mapping from selected items to actual directories.
- Before reporting completion, verify that selected item names appear in the real directory tree.

Correct behavior example:

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

Bad behavior: creating only generic folders such as `20_Wiki/01_概念/` or `30_成果物/01_レポート/` when the user selected purpose-specific items.

## Base structure

Always create these top-level directories:

```text
00_受信箱/
10_素材/
20_Wiki/
30_成果物/
90_アーカイブ/
個人用メモ/
```

Do not automatically create generic child directories unless the user's purpose or selections actually call for them:

```text
10_素材/01_記事/
10_素材/02_論文/
20_Wiki/01_概念/
20_Wiki/02_人物・組織/
20_Wiki/03_テーマ/
30_成果物/01_レポート/
30_成果物/02_比較表/
```

For a Slack Q&A wiki, use Slack/Q&A-specific children. For a travel wiki, use travel-specific children. For a reading wiki, use book/reading-specific children. For a purpose not listed in examples, infer appropriate children from the user's purpose.

## Initial hearing flow

Ask the purpose first. Then tailor Wiki, Outputs, HOME, and privacy examples to that purpose.

If the user already provides the purpose and other preferences in one message, do not ask again. Use what is available and proceed.

If the user says `お願いします`, `おまかせ`, `おすすめ`, `目的に合ったおすすめ`, `いい感じで`, or `デフォルトで` after seeing options, treat that as `目的に合ったおすすめ` for Wiki/Outputs/HOME. For privacy, use `標準（おすすめ）（人名：そのまま、他：伏せる）` unless the user chooses `厳格` or `カスタム`.

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

Also ask for a project directory name only if it is useful. If none is given, use `LLM_Wiki`.

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

For purposes not covered above, infer from the purpose. Use nouns that would be meaningful as Obsidian folders. Prefer concrete categories over abstract generic folders.

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

Outputs should be reusable deliverables, not just storage categories.

### 4. HOME information

Ask:

```text
HOMEに表示したい情報はありますか？
```

Show purpose-specific examples, then always show the default sections.

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

If the user says they want to know where each directory is, ensure `どこに何があるか` contains a detailed directory map.

### 5. Personal and sensitive information handling

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

Show exactly these options:

```text
選択肢:
- 標準（おすすめ）（人名：そのまま、他：伏せる）
  - 人名: そのまま残す
  - 個人連絡先: 伏せる
  - 人事情報: 伏せる
  - 契約金額: 原則伏せる。必要な場合だけ金額帯などに丸める
  - パスワード・本番環境の認証情報: 常に伏せる。保存しない、引用しない、ログに残さない

- 厳格
  - 人名・個人連絡先・人事情報・契約金額をすべて伏せる
  - パスワード・本番環境の認証情報は常に伏せる

- カスタム
  - カテゴリごとに「残す / 役割化する / 匿名ID化する / 金額帯に丸める / 完全に伏せる」を指定する
```

Important: Standard mode keeps human names as written. Do not anonymize names in standard mode merely because they are names. Redact the sensitive attribute, not the ordinary name.

Examples:

- Good in standard mode: `山田さんがこの手順を回答した。`
- Good in standard mode: `山田さんの個人電話番号は [PHONE_REDACTED]。`
- Bad in standard mode: `[PERSON_001] がこの手順を回答した。` when the user chose standard mode and no other sensitivity exists.

For the user's purpose, add purpose-specific privacy examples:

- 業務Q&A / Slack / MSP:
  - `標準ではSlack表示名・担当者名などの人名はそのまま残す`
  - `チーム名や問い合わせ窓口名は残してよい場合がある`
  - `個人メール、電話番号、住所、人事情報、契約金額は伏せるかレンジ化する`
  - `障害対応に必要なシステム名は残すが、接続先URL、トークン、認証情報は伏せる`
  - `社外共有や広範囲共有を前提にする場合は、厳格またはカスタムで人名匿名化を提案する`
- Research / investigation:
  - `標準では公開論文の著者名や公開情報に含まれる人名は残す`
  - `非公開インタビュー対象者の名前を伏せたい場合は、厳格またはカスタムを選ぶ`
  - `調査対象企業の公開情報は残し、非公開契約情報は伏せる`
- Reading / books:
  - `作品内の登場人物名は残す`
  - `実在の知人名を伏せたい場合は、厳格またはカスタムを選ぶ`
- Personal / health:
  - `標準では人名は残るため、共有予定がある場合は厳格またはカスタムを推奨する`
  - `個人連絡先、病歴、通院先、家族情報、人事情報は伏せる`
- Travel:
  - `標準では同行者名は残るため、必要に応じて厳格またはカスタムで匿名化する`
  - `予約番号、住所、電話番号、個人連絡先は伏せる`
  - `宿泊施設名は目的に応じて残すか伏せる`
- Customer/product research:
  - `標準では顧客担当者名や社内担当者名は残す`
  - `顧客名・担当者名を匿名化したい場合は、厳格またはカスタムを選ぶ`
  - `企業名を残すかは目的と共有範囲に応じて確認する`
  - `契約金額や個別条件は伏せるかレンジ化する`

If the user says `おすすめ`, `おまかせ`, or gives no preference, use `標準（おすすめ）（人名：そのまま、他：伏せる）`.

## Non-negotiable secret handling

Regardless of the selected policy, never write the following raw values into wiki pages, outputs, logs, `index.md`, `HOME.md`, `README.md`, `AGENTS.md`, `CLAUDE.md`, or final responses:

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
4. If the user gives multiple comma-separated items, split them into separate items unless they are clearly one phrase with examples in parentheses.
5. Preserve Japanese labels where practical. Replace `/` and `\` with `・`. Avoid `:`, `*`, `?`, `"`, `<`, `>`, `|` in file or directory names.
6. Use stable numbering: `01_`, `02_`, `03_`. Do not renumber existing non-empty directories unless the user asks.
7. When updating an existing Wiki, never delete non-empty directories. Empty unselected generic directories may be removed or moved to `90_アーカイブ/` only if the user asked to clean them up.
8. Privacy choices become durable configuration, not just chat context. Record them in `個人情報・機密情報ポリシー.md`, `wiki設定.json`, `AGENTS.md`, `HOME.md`, and `log.md`.
9. If a privacy choice conflicts with secret safety, secret safety wins.
10. In standard mode, ordinary human names are intentionally preserved. Do not replace ordinary person names with `[PERSON_001]` or role labels unless the user selects `厳格` or `カスタム`.

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

### Raw source directories

Infer source directories from the purpose. Examples:

- Slack Q&A: `01_Slackスレッド`, `02_Slackエクスポート`, `03_添付ファイル`, `04_スクリーンショット`, `05_関連ドキュメント`
- Research: `01_論文`, `02_記事`, `03_レポート`, `04_データ`, `05_画像・図表`
- Reading: `01_章メモ`, `02_引用`, `03_関連資料`, `04_画像`, `05_メモ断片`
- Travel: `01_候補地メモ`, `02_予約情報`, `03_地図・交通`, `04_画像`, `05_リンク集`
- Personal/health: `01_日次メモ`, `02_習慣ログ`, `03_体調メモ`, `04_参考資料`, `05_専門家確認メモ`
- Customer/product research: `01_顧客インタビュー`, `02_競合資料`, `03_市場調査`, `04_プロダクトメモ`, `05_添付ファイル`
- Unknown purpose: `01_未分類素材`, `02_添付ファイル`

### Wiki directories

Use the resolved Wiki items exactly as the basis for directories:

```text
20_Wiki/01_<Wiki項目1>/
20_Wiki/02_<Wiki項目2>/
20_Wiki/03_<Wiki項目3>/
```

Include at least one source-summary or source-note category when the purpose involves incoming sources. Include an unresolved/needs-check category when the purpose involves evolving knowledge, team work, investigation, operations, health, travel planning, or decision making.

### Outputs directories

Use the resolved Outputs items exactly as the basis for directories:

```text
30_成果物/01_<Outputs項目1>/
30_成果物/02_<Outputs項目2>/
30_成果物/03_<Outputs項目3>/
```

Outputs should be deliverables or reusable artifacts, not simply another copy of the Wiki categories.

### Empty directories

Every empty directory must contain `.gitkeep`.

`個人用メモ/` also receives `.gitkeep`, but `.gitignore` must exclude `/個人用メモ/` so it is not committed.

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
- Apply the privacy policy before writing generated wiki pages, outputs, `HOME.md`, `index.md`, or `log.md`.
- In standard mode, keep ordinary human names as written.
- Never reproduce passwords, API keys, access tokens, private keys, session cookies, production credentials, or production connection strings.
- New reusable answers should be saved into the best matching Wiki or Outputs directory.
- When adding/changing categories, update the actual directory tree, `directory_manifest.md`, `wiki設定.json`, `HOME.md`, `index.md`, and `log.md` together.
- Use the directory mapping table to avoid dumping content into generic folders.

### `CLAUDE.md`

Keep this short and point to `AGENTS.md` and the privacy policy:

```markdown
# CLAUDE.md

このリポジトリで作業する場合は、まず `AGENTS.md` と `個人情報・機密情報ポリシー.md` を読んでください。
`個人用メモ/` は読まない・要約しない・リンクしない・gitに含めないでください。
標準モードでは通常の人名はそのまま残します。
パスワード、APIキー、トークン、秘密鍵、本番環境の認証情報は保存・引用・出力しないでください。
```

### `directory_manifest.md`

Must include a table with columns:

```text
Layer | Selected item | Directory | Notes
```

This is the easiest place to verify that hearing selections became directories.

### `個人情報・機密情報ポリシー.md`

Create a root file named `個人情報・機密情報ポリシー.md`.

It must include:

```markdown
# 個人情報・機密情報ポリシー

## 適用範囲
このポリシーは、20_Wiki、30_成果物、HOME.md、index.md、log.md、README.md、AGENTS.md、CLAUDE.md、directory_manifest.md、wiki設定.json に適用する。
Raw sources は原則として改変しないが、生成物にはこのポリシーを適用する。

## 選択したモード
{標準（おすすめ）（人名：そのまま、他：伏せる） / 厳格 / カスタム}

## カテゴリ別ルール
| カテゴリ | 扱い | 例 |
|---|---|---|
| 人名 | {標準なら「そのまま残す」 / 厳格なら「完全に伏せる」 / カスタムに従う} | 標準: 山田太郎 / 厳格: [PERSON_REDACTED] |
| 個人連絡先 | {扱い} | [EMAIL_REDACTED] |
| 人事情報 | {扱い} | [HR_INFO_REDACTED] |
| 契約金額 | {扱い} | [AMOUNT_REDACTED] / [AMOUNT_RANGE:100万円台] |
| パスワード | 常に伏せる | [SECRET_REDACTED] |
| 本番環境の認証情報 | 常に伏せる | [PROD_CREDENTIAL_REDACTED] |

## モード別の初期値

### 標準（おすすめ）（人名：そのまま、他：伏せる）
- 人名: そのまま残す
- 個人連絡先: 伏せる
- 人事情報: 伏せる
- 契約金額: 原則伏せる。必要な場合だけ金額帯などに丸める
- パスワード・本番環境の認証情報: 常に伏せる。保存しない、引用しない、ログに残さない

### 厳格
- 人名・個人連絡先・人事情報・契約金額をすべて伏せる
- パスワード・本番環境の認証情報は常に伏せる

### カスタム
- カテゴリごとに「残す / 役割化する / 匿名ID化する / 金額帯に丸める / 完全に伏せる」を指定する

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
- 生成物へ書く前に、選択したポリシーに従って処理する。標準モードでは通常の人名は残す。
- log.md には伏せた件数やカテゴリだけを書き、実値は書かない。
- 判断に迷う機密情報は伏せる。人名の扱いで迷う場合は選択したモードに従う。
- 必要ならユーザーに確認する。
```

### `wiki設定.json`

Must include at least:

```json
{
  "version": "1.6.2",
  "purpose": "...",
  "created": "YYYY-MM-DD",
  "source_directories": [],
  "wiki_directories": [],
  "output_directories": [],
  "home_sections": [],
  "private_directory": "個人用メモ",
  "privacy_policy_file": "個人情報・機密情報ポリシー.md",
  "privacy": {
    "mode": "標準（おすすめ）（人名：そのまま、他：伏せる）",
    "rules": {
      "人名": "そのまま残す",
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
    "standard mode keeps ordinary person names as written",
    "never store raw secrets or production credentials"
  ]
}
```

## Wiki growth rules

This wiki is not a fixed template. It grows as rough information is added.

### When to create a new page

Create a new Markdown page under `20_Wiki/` when:

- it is a reusable FAQ, procedure, troubleshooting case, concept, entity, comparison, or note;
- it would be searched for later as an independent topic;
- adding it to an existing page would make that page too long or hard to use;
- it captures a new unresolved issue, contradiction, or decision point.

### When to update an existing page

Update an existing page instead of creating a new one when:

- the information is a supplement to an existing FAQ;
- it is a variation of an existing procedure or troubleshooting case;
- it clarifies an existing concept, tool, project, entity, or term;
- users would expect to find it on an existing page.

### When to create a new directory

Create a new child directory only when:

- multiple pages are likely to belong there;
- existing directories do not fit the material;
- the category is meaningful enough to explain in `HOME.md` and `index.md`;
- it supports the wiki's stated purpose.

Do not create a new directory for a single isolated item unless the user explicitly asks.

When adding or changing directories, update the real directory tree, `.gitkeep`, `directory_manifest.md`, `wiki設定.json`, `HOME.md`, `index.md`, and `log.md` together.

## Existing wiki update workflow

When the user already has a wiki and wants this skill applied:

1. Inspect the existing tree.
2. Identify current generic directories that were not selected, especially under `20_Wiki/` and `30_成果物/`.
3. Resolve the intended purpose-specific Wiki and Outputs items.
4. Resolve or ask for the privacy policy.
5. Add missing purpose-specific directories.
6. Add or update `個人情報・機密情報ポリシー.md`.
7. Update `AGENTS.md`, `CLAUDE.md`, `.gitignore`, and `wiki設定.json` so the privacy policy is durable.
8. Do not delete non-empty directories.
9. Empty generic directories may be removed or archived if the user requested cleanup.
10. Update `.gitkeep`, `directory_manifest.md`, `wiki設定.json`, `HOME.md`, `index.md`, `README.md`, `AGENTS.md`, and `log.md`.
11. Verify with a final tree and a mapping table.

If asked to audit existing pages for privacy issues:

- Scan generated wiki pages and outputs.
- Apply the selected policy.
- In standard mode, keep ordinary names but redact contact details, HR information, contract amounts, and secrets.
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
- `log.md` has an init or update entry and does not contain raw sensitive values.

Final response should include:

- Created or updated path.
- Short explanation of the purpose-specific directories created.
- Confirmation that selected Wiki and Outputs items were reflected as directories.
- Confirmation that privacy policy was recorded.
- A brief next step, such as where to put raw sources.
