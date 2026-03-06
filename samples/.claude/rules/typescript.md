---
paths:
  - "src/**/*.ts"
  - "src/**/*.tsx"
---

# TypeScript ルール

- `any` 型は使わない。`unknown` を使ってから型ガードで絞り込む
- 関数の戻り値の型は明示的に書く
- `interface` より `type` を優先する
- Enum は使わず `as const` オブジェクトを使う
- nullチェックには optional chaining (`?.`) と nullish coalescing (`??`) を使う
