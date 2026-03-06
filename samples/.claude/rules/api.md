---
paths:
  - "src/api/**"
  - "src/routes/**"
---

# API ルール

- RESTful な命名規則に従う
- エラーレスポンスは統一フォーマット `{ error: { code, message } }` を使う
- 入力バリデーションには zod を使う
- 認証が必要なエンドポイントには authMiddleware を適用する
- レスポンスの型は必ず定義する
