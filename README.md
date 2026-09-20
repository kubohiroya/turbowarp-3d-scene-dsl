# turbowarp-3d-scene-dsl

[日本語](README.ja.md)

**This package is retired. Use [`@kubohiroya/turbowarp-scene-graph`](https://github.com/kubohiroya/turbowarp-scene-graph) instead.**

```bash
pnpm remove @kubohiroya/turbowarp-3d-scene-dsl
pnpm add @kubohiroya/turbowarp-scene-graph
```

## Why

This package wrapped `@kubohiroya/turbowarp-scene-graph`. Almost everything it exported was a
re-export, and its own code was mostly a layer that rewrote that package's error messages from
`Scene graph ...` to `3D scene ...`. That is a rename, not a responsibility: it added a package to
maintain, and it turned another package's internal strings into this one's public contract. The two
parts that were genuinely its own — YAML input and output, and the `{scene3d, ar}` envelope a
Kamishibai scene carries — are now modules of `turbowarp-scene-graph`, where the document model they
build on already lived.

The SB3 release workflow is gone with it. The extension it shipped only answered a status reporter
and never dispatched a call plan, so nothing was lost by removing it. Turning a call plan into
TurboWarp blocks is still unimplemented; it belongs in whichever app needs it, not in a planning
library.

## Migrating

`@kubohiroya/turbowarp-scene-graph` has the same behavior under the names it always used:

| Was | Now |
|---|---|
| `parseSceneYaml` | `parseSceneGraphYaml` |
| `stringifySceneYaml` | `stringifySceneGraphYaml` |
| `validateSceneDocument` | `validateSceneGraphDocument` |
| `normalizeSceneDocument` | `normalizeSceneGraphDocument` |
| `validateSceneNodeTemplate` | `validateSceneGraphNode` |
| `stringifySceneGraphValue` | unchanged |
| `Kamishibai3DSceneExtension` and friends | unchanged |
| `createTurboWarpExtensionPlan` | unchanged |
| `SceneDocument`, `NormalizedSceneDocument`, `SceneNodeTemplate`, `SceneOptions` | `SceneGraphDocument`, `NormalizedSceneGraphDocument`, `SceneGraphNode`, `SceneGraphOptions` |
| `ARSceneControl`, `ARTargetBinding`, `TurboWarpARScenePlanCall` re-exported here | import from `@kubohiroya/turbowarp-ar/plan` |

Two behavior changes to check for:

- **Error messages are no longer rewritten.** What began `3D scene ...` now begins `Scene graph ...`,
  and AR errors keep their `TurboWarp AR ...` prefix. Code matching on those strings needs updating.
- **`ar.layer` no longer accepts `camera-under-3d`.** The AR extension never implemented that value
  and discarded it at runtime. Use `above-stage` (the default) or `below-stage`; the AR camera
  background already renders under a 3D scene on the same layer.

The JSON Schemas moved to `schemas/` in the same repository, so their `$id` URLs changed to
`https://raw.githubusercontent.com/kubohiroya/turbowarp-scene-graph/main/schemas/...`.

## License

Mozilla Public License 2.0. See [LICENSE](LICENSE).
