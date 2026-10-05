# plugin-example-structkind

The reference **structural `kind`-class** plugin (`kind:examplestructkind`) — a
plugin that serves a whole entity kind whose `OpLoad` returns a member tree the
host folds into `uf.Deploy`.

Unlike the FLAT kind (`candy/plugin-example-kind`, whose body lands as opaque
`uf.PluginKinds`), a **structural** kind declares `Structural:true`; its `OpLoad`
returns a `spec.Deploy` (`DeployNode`) **member tree** that the host folds into
`uf.Deploy` — the same map a builtin pod/group/candy decoder populates in-process —
so the entity participates in deploy/check exactly like a builtin.

It also proves **F5 authored-member input-threading**: the authored resource-member
children are pre-decoded host-side (the core `buildDeployNode` recursion) and
threaded via `op.Env`, and the plugin attaches them to its reply — so the
reconstructed `uf.Deploy` carries the authored members, not a synthesized
stand-in.

## What it provides

| Capability | Surface |
|---|---|
| `kind:examplestructkind` | the `examplestructkind:` entity kind — `OpLoad` returning an authored member tree folded into `uf.Deploy` |

The plugin is **out-of-process only** (not in `compiled_plugins:`), which is
exactly the point: it is the witness that a plugin the loader was not built with
can reconstruct an authored `uf.Deploy` member tree over the wire. It is the
channel the group/pod/vm/kubernetes/local/android/candy externalizations reuse.

## How to use it

Compose the plugin candy, then author the kind:

```yaml
- '@github.com/opencharly/plugin-example-structkind/candy/plugin-example-structkind:<tag>'
```

```yaml
examplestructkind:
  marker: hello
```

## Layout

- `candy/plugin-example-structkind/` — the plugin module: `plugin.go` (the
  provider + `NewProvider()`/`NewMeta()` with `Structural:true` + the `OpLoad`
  member-tree reconstruction), `schema/examplestructkind.cue` (the self-contained
  `#ExamplestructkindInput`), `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-internals:plugin` — the plugin/provider model, including
  the structural `kind` class and the `uf.Deploy` fold. This candy carries no
  `skill:` entity of its own; the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
