# プロジェクト指示 (CLAUDE.md)

## コーディング規約
- 言語: TypeScript
- インデント: スペース2つ
- 文字列はシングルクォートを使う
- セミコロンは省略しない

## プロジェクト構成
- `src/` - ソースコード
- `tests/` - テストコード
- `docs/` - ドキュメント

## テスト
- テストフレームワーク: vitest
- テスト実行: `npm test`
- カバレッジ: `npm run test:coverage`

## ビルド
- `npm run build` でビルド
- 出力先は `dist/`

## 注意事項
- .env ファイルは絶対にコミットしない
- APIキーをハードコードしない
- console.log はデバッグ後に必ず削除する

## 外部ファイルの読み込み
@docs/api-guidelines.md
@docs/database-schema.md
