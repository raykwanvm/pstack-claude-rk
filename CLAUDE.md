# pstack-claude-rk

A personal fork of pstack-claude, installed as `pstack@pstack-claude-rk` on this machine only.

@FORK.md
@CONTEXT.md

Read [CONTRIBUTING.md](CONTRIBUTING.md) before changing skills, the generator, hooks, the Pi extension, `poteto-mode/scripts`, or workflows. It lists the extra checks each of those needs.

## Rules

- Run `bun tools/generate.mjs` after any edit. Never hand-edit what it writes: manifest versions, Models sections, prompt stubs, runtime preambles.
- Change model defaults in `plugins/pstack/models.json`, never in a skill body.
- A release is three steps: bump `VERSION`, add `## <version> - <title>` at the top of `CHANGES.md`, regenerate.
- Upstream release headings merged in are relabelled `## Upstream <version> - <title>`.
- Never run `tools/sync.mjs` without `--dry-run`.
- Never add a `plugins/pstack/commands/` directory.
- Declare a fork-only edit to an upstream-derived skill or agent in `tools/forks.json`.

## Before calling work done

```shell
bun test tests/
bun tools/generate.mjs --check
```
