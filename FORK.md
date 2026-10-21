# Fork notes

This is a personal fork of [michael-denyer/pstack-claude](https://github.com/michael-denyer/pstack-claude), used only on this machine. Nothing here goes back upstream.

## Identity

- The marketplace is `pstack-claude-rk` in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) and [`.agents/plugins/marketplace.json`](.agents/plugins/marketplace.json). The two must match.
- The plugin stays `pstack`. Skills and the generator rely on the `pstack:<name>` namespace, so it installs as `pstack@pstack-claude-rk`.
- Both `pstack@pstack-claude` and `pstack@pstack-claude-rk` register the same `pstack:` names. Disable one before enabling the other.

## Install

```shell
/plugin marketplace add ~/workspace/pstack-claude-rk
/plugin install pstack@pstack-claude-rk
```

## Versions

- The fork runs its own line: `1.0.0`, `1.0.1`, … `VERSION` must be plain `MAJOR.MINOR.PATCH`.
- Every fork release, including one that only merges upstream, bumps the patch and adds a `## <version> - <title>` entry at the top of `CHANGES.md`.
- Upstream release entries merged in after 1.0.0 are relabelled `## Upstream <version> - <title>`. The generator ignores a heading whose first word is not a version, so upstream's numbers never collide with the fork's.
- Installed copies update only when the version changes.

## Edit loop

1. Edit on a branch, then run `bun tools/generate.mjs` and `bun test tests/`.
2. To try uninstalled edits, run `claude --plugin-dir ~/workspace/pstack-claude-rk/plugins/pstack`. A `pstack:poteto-agent` delegate given no path reads the highest-versioned `poteto-mode/SKILL.md` under `~/.claude/plugins/`, not this checkout, so pass delegates the checkout path or test delegate behavior from an install.
3. Merge to `main`, bump the version, run `bun tools/generate.mjs`, commit, then:

```shell
/plugin marketplace update pstack-claude-rk
claude plugin update pstack@pstack-claude-rk
```

Restart Claude Code and confirm the version.

## Merging upstream

The `upstream` remote fetches michael-denyer/pstack-claude; its push URL is `DISABLE`.

```shell
git fetch upstream
git log --oneline main..upstream/main
git switch main && git switch -c merge-upstream-<version>
git merge upstream/main
```

Resolve conflicts:

- `VERSION`: the next fork version.
- `CHANGES.md`: the fork's new entry first, then upstream's new entries relabelled `## Upstream <version> - <title>`, then the older history. Never take one side wholesale.
- Manifests: the `version` lines are restamped by the generator; keep `"name": "pstack-claude-rk"` in both marketplace files.
- A skill declared in [`tools/forks.json`](tools/forks.json): a real three-way merge.
- `tools/forks.json`: keep both sides' entries. `tools/upstream.json` and `bun.lock`: take upstream's.

Then run `bun install --frozen-lockfile` if `bun.lock` changed, `bun tools/generate.mjs`, `bun test tests/`, and `bun tools/generate.mjs --check`, commit, merge to `main`, and update the install.

Never run `tools/sync.mjs` without `--dry-run` here. Upstream's syncs from Cursor's pstack arrive inside the merged commits, and CI's dry-run audit stays as is.

## Changing skills

A fork-only edit to a file that comes from Cursor's pstack is declared in `tools/forks.json` as a `policy` fork with `upstream: "not-proposed"`, following [CONTRIBUTING.md](CONTRIBUTING.md#the-sync-boundary). A file with no upstream source gets no entry.
