# claude_code_configs
Claude Code の設定ファイルたち

## 設定手段一覧

### 1. CLAUDE.md（指示・ルール）

| スコープ | ファイル | 共有 | 用途 |
|---------|---------|------|------|
| マネージド(最優先) | `/etc/claude-code/CLAUDE.md` 等 | IT管理 | 組織全体の指示、除外不可 |
| ローカル | `./CLAUDE.local.md` | No(.gitignore) | 個人的なプロジェクト設定 |
| プロジェクト | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Yes(git) | チーム共有の指示 |
| ユーザー | `~/.claude/CLAUDE.md` | No | 全プロジェクト共通の個人設定 |

- 形式: Markdown
- `@path/to/file` でインポート可能（最大5段階）
- サブディレクトリにも配置可能（そのディレクトリのファイル読み取り時にオンデマンドで読み込み）

#### .claude/rules/ ディレクトリ

`.claude/rules/*.md` や `~/.claude/rules/*.md` にトピック別ルールを配置可能。YAML frontmatter で `paths: ["src/**/*.ts"]` のようにパス条件付きで適用できる。

### 2. settings.json（動作設定・権限）

| スコープ | ファイル | 共有 | 優先度 |
|---------|---------|------|--------|
| マネージド | `/etc/claude-code/managed-settings.json` 等 | IT管理 | 最高（上書き不可） |
| CLIフラグ | `--model` 等 | - | セッション限り |
| ローカル | `.claude/settings.local.json` | No(.gitignore) | プロジェクト設定より上 |
| プロジェクト | `.claude/settings.json` | Yes(git) | ユーザー設定より上 |
| ユーザー | `~/.claude/settings.json` | No | 最低 |

- 形式: JSON
- 主な設定項目: `permissions`(allow/deny/ask)、`env`、`model`、`hooks`、`sandbox`、`autoMemoryEnabled` 等

### 3. .mcp.json（MCPサーバー設定）

| スコープ | ファイル | 共有 |
|---------|---------|------|
| マネージド | `/etc/claude-code/managed-mcp.json` 等 | IT管理 |
| プロジェクト | `./.mcp.json` | Yes(git) |
| ユーザー | `~/.claude.json` の `mcpServers` フィールド | No |

- 形式: JSON
- stdio / HTTP トランスポート対応
- `${VAR:-default}` で環境変数展開可能

### 4. keybindings.json（キーバインド）

| スコープ | ファイル |
|---------|---------|
| ユーザーのみ | `~/.claude/keybindings.json` |

- 形式: JSON
- コンテキスト別（Chat, Global 等）にキー割当を定義
- `ctrl+k ctrl+s` のようなコード（2段階キー）対応
- `null` 設定でデフォルトキーの無効化が可能

### 5. Hooks（イベントハンドラ）

settings.json 内の `hooks` セクション、または `.claude/hooks.json` / `~/.claude/hooks.json` に定義。

- イベント: `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop` 等
- ハンドラ種別: command, HTTP, prompt, agent
- ツール実行前後にバリデーションや自動処理を挟める

### 6. 環境変数

シェルの `export` または settings.json の `env` セクションで設定。主なもの:

| 変数 | 用途 |
|-----|------|
| `ANTHROPIC_API_KEY` | API認証 |
| `ANTHROPIC_MODEL` | デフォルトモデル上書き |
| `CLAUDE_CODE_EFFORT_LEVEL` | 推論の深さ (low/medium/high) |
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | 自動メモリ無効化 |
| `DISABLE_AUTOUPDATER` | 自動更新無効化 |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 最大出力トークン数 |

### 7. Auto Memory（自動メモリ）

| スコープ | ファイル |
|---------|---------|
| プロジェクト別 | `~/.claude/projects/<project>/memory/MEMORY.md` 等 |

- Claude が学習した内容を自動保存
- マシンローカル（git共有されない）
- `MEMORY.md` の先頭200行が毎セッション自動読み込み

### 8. その他

| 仕組み | ファイル | 用途 |
|--------|---------|------|
| statusline | `~/.claude/statusline.json` | ターミナル下部のステータスバーUI |
| Plugins | `plugin.json` + settings.json の `enabledPlugins` | プラグイン管理 |
| `~/.claude.json` | `~/.claude.json` | 認証トークン、OAuthなどの内部状態 |

## 優先度まとめ

```
settings.json:  マネージド > CLIフラグ > ローカル > プロジェクト > ユーザー
CLAUDE.md:      マネージド > ローカル > プロジェクト > ユーザー
permissions:    deny > ask > allow
```

## 使い分けの指針

| やりたいこと | 使う設定 |
|-------------|---------|
| Claudeへの指示・ルール | CLAUDE.md |
| 権限・モデル・動作制御 | settings.json |
| 外部ツール連携 | .mcp.json |
| キーボード操作カスタマイズ | keybindings.json |
| ツール実行前後の自動処理 | Hooks |
| ランタイム設定の上書き | 環境変数 |
| チーム共有したい | `.claude/settings.json` + `CLAUDE.md`（gitコミット） |
| 個人設定にしたい | `*.local.*` 系 or `~/` 配下のファイル |

## サンプルファイル

`samples/` ディレクトリにプロジェクトレベルの設定サンプルを収録している。

```
samples/
├── CLAUDE.md                      # プロジェクト指示（チーム共有）
├── CLAUDE.local.md                # プロジェクト指示（個人用、gitignore対象）
├── .mcp.json                      # MCPサーバー設定
└── .claude/
    ├── CLAUDE.md                  # プロジェクト指示（.claude/ 配下の代替配置）
    ├── settings.json              # プロジェクト設定（チーム共有）
    ├── settings.local.json        # プロジェクト設定（個人用、gitignore対象）
    ├── hooks.json                 # イベントハンドラ
    └── rules/
        ├── typescript.md          # TypeScript用ルール（src/**/*.ts に適用）
        ├── testing.md             # テスト用ルール（tests/**/*.test.ts に適用）
        └── api.md                 # API用ルール（src/api/** に適用）
```

## Q&A

### CLAUDE.md と .claude/rules/ の違いは？

どちらも「Claudeへの指示」を書く場所だが、以下の点が異なる。

| | CLAUDE.md | .claude/rules/*.md |
|---|---|---|
| 適用範囲 | 常に全体に適用 | `paths` 指定で特定パスのみに適用可能 |
| 粒度 | 1ファイルにまとめて書く | トピック別に複数ファイルに分割 |
| 読み込みタイミング | セッション開始時 | セッション開始時（ただし `paths` 指定ありの場合は該当ファイル操作時） |
| 条件付き適用 | 不可 | YAML frontmatter で `paths: ["src/**/*.ts"]` のように条件指定可能 |

使い分け:

- **CLAUDE.md**: プロジェクト全体に常に適用したい一般的な指示（ビルド手順、コーディング規約の基本方針、注意事項など）
- **rules/**: 特定の領域にだけ適用したいルール（TypeScriptファイルだけ、テストだけ、API層だけ、など）

ルールが増えてきたら `rules/` に分割し、CLAUDE.md は全体方針だけに絞るのがよい。

### CLAUDE.md と rules/ はプロンプトにどう結合される？

内部のプロンプト組み立て構造（system prompt か user prompt か等）は公式には非公開。ただし以下は分かっている。

**読み込みタイミングの違い:**

| | CLAUDE.md | rules/（`paths` なし） | rules/（`paths` あり） |
|---|---|---|---|
| 読み込み | セッション開始時に即座 | セッション開始時に即座 | 該当パスのファイル操作時に遅延読み込み |
| 再読み込み | `/compact` 時にディスクから再読み込み | 同左 | 同左 |

**InstructionsLoaded フックで観測できる情報:**

```json
{
  "hook_event_name": "InstructionsLoaded",
  "file_path": "/project/CLAUDE.md",
  "memory_type": "Project",
  "load_reason": "session_start"
}
```

`load_reason` の値:
- `session_start` — セッション開始時（CLAUDE.md、`paths` なしの rules）
- `path_glob_match` — ファイル操作でパスが一致した時（`paths` ありの rules）
- `nested_traversal` — サブディレクトリの CLAUDE.md 発見時
- `include` — `@path/to/file` インポート展開時

**公開されていないこと:**
- system prompt と user prompt のどちらに注入されるか
- tool 定義との相対的な位置関係
- 複数スコープの指示がどう結合・順序付けされるか
- 内部で使われる XML タグ構造

**実用上の最大の違い — コンテキスト消費の効率:**
- CLAUDE.md: 毎セッション必ず全文がコンテキストに載る
- rules/（`paths` あり）: 関係ないファイルを扱っている間はコンテキストを消費しない

プロンプト上の配置位置の違いよりも、「いつコンテキストに載るか」が実用上の最大の違いとなる。
