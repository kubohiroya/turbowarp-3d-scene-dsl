# turbowarp-3d-scene-dsl

[English](README.md)

**このパッケージは廃止しました。[`@kubohiroya/turbowarp-scene-graph`](https://github.com/kubohiroya/turbowarp-scene-graph) を使ってください。**

```bash
pnpm remove @kubohiroya/turbowarp-3d-scene-dsl
pnpm add @kubohiroya/turbowarp-scene-graph
```

## 経緯

このパッケージは `@kubohiroya/turbowarp-scene-graph` を包んでいただけでした。export のほとんどは re-export で、固有のコードは、その scene-graph のエラーメッセージを `Scene graph ...` から `3D scene ...` へ書き換える層が大半を占めていました。これは責務の分離ではなく改名です。保守するパッケージが1つ増えたうえ、他パッケージの内部文字列がこちらの公開契約になってしまっていました。

本当に固有だった2つ — YAML の入出力と、Kamishibai scene が持つ `{scene3d, ar}` エンベロープ — は `turbowarp-scene-graph` のモジュールになりました。それらが土台にしている document モデルは、もともとそちらにあります。

SB3 リリースワークフローも一緒に撤去しました。同梱していた拡張は status reporter に答えるだけで、呼び出し計画を実行してはいなかったため、失われたものはありません。呼び出し計画を TurboWarp のブロック列へ変換する処理は依然として未実装であり、それは計画ライブラリではなく、必要とするアプリ側の責務です。

## 移行

`@kubohiroya/turbowarp-scene-graph` に、もともとの名前のまま同じ振る舞いがあります。

| 旧 | 新 |
|---|---|
| `parseSceneYaml` | `parseSceneGraphYaml` |
| `stringifySceneYaml` | `stringifySceneGraphYaml` |
| `validateSceneDocument` | `validateSceneGraphDocument` |
| `normalizeSceneDocument` | `normalizeSceneGraphDocument` |
| `validateSceneNodeTemplate` | `validateSceneGraphNode` |
| `stringifySceneGraphValue` | 変更なし |
| `Kamishibai3DSceneExtension` 一式 | 変更なし |
| `createTurboWarpExtensionPlan` | 変更なし |
| `SceneDocument`, `NormalizedSceneDocument`, `SceneNodeTemplate`, `SceneOptions` | `SceneGraphDocument`, `NormalizedSceneGraphDocument`, `SceneGraphNode`, `SceneGraphOptions` |
| ここで re-export していた `ARSceneControl`, `ARTargetBinding`, `TurboWarpARScenePlanCall` | `@kubohiroya/turbowarp-ar/plan` から import |

確認が必要な振る舞いの変更が2点あります。

- **エラーメッセージを書き換えなくなりました。** `3D scene ...` で始まっていた文言は `Scene graph ...` になり、AR のエラーは `TurboWarp AR ...` のままです。これらの文字列に依存しているコードは修正が必要です。
- **`ar.layer` は `camera-under-3d` を受け付けなくなりました。** AR 拡張はこの値を実装しておらず、実行時に黙って捨てていました。`above-stage`（既定）か `below-stage` を使ってください。AR のカメラ背景は、同じ layer の 3D scene の下にすでに描かれます。

JSON Schema は同じリポジトリの `schemas/` へ移したため、`$id` URL が `https://raw.githubusercontent.com/kubohiroya/turbowarp-scene-graph/main/schemas/...` に変わっています。

## ライセンス

Mozilla Public License 2.0。[LICENSE](LICENSE) を参照してください。
