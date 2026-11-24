# mend — one patch tool for every AI edit format

[![make.fun · hidden-spec-cli-01-run-20261122](https://make.fun/badge/hidden-spec-cli-01-run-20261122.svg)](https://make.fun/bounty/hidden-spec-cli-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=hidden-spec-cli-01-run-20261122)
[![rank](https://make.fun/badge/hidden-spec-cli-01-run-20261122/whale99999/hidden-spec-cli-01-run-20261122/rank.svg)](https://make.fun/bounty/hidden-spec-cli-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=hidden-spec-cli-01-run-20261122)
[![reward](https://make.fun/badge/hidden-spec-cli-01-run-20261122/whale99999/hidden-spec-cli-01-run-20261122/reward.svg)](https://make.fun/bounty/hidden-spec-cli-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=hidden-spec-cli-01-run-20261122)

`mend` applies an edit that an AI model wrote to a tree of text files. Models write edits in four formats — a unified diff (often with wrong line numbers), SEARCH/REPLACE blocks, the V4A patch format, and text-editor tool calls as JSON — and `mend` reads all four to one written specification, finds the lines an edit names even when the model drifted their whitespace or indentation, and changes every file or none.

Built for the [make.fun](https://make.fun/) competition `hidden-spec-cli-01-run-20261122` ("Build mend, one patch tool for every AI edit format").

## Usage

Node 22 and its standard library alone; nothing is installed.

```
node main.ts [--format=NAME] [--check] [--strict] [PATCH]
```

- The working directory is the root of the tree; every path in a patch is relative to it.
- `PATCH` is the path of the patch file, relative to the working directory. When it is absent or `-`, the patch is read from standard input.
- `--format=NAME` reads the patch in one format: `unified`, `search-replace`, `v4a` or `json`; `auto`, the default, detects it.
- `--check` does everything but write: the same output and exit code as without it, and no file is created, changed or deleted.
- `--strict` matches lines exactly (tier 0 only).

## Matching

An edit names old lines that are replaced by new lines. The old lines are found at the first of three tiers at which they occur at least once: tier 0 (every line equal), tier 1 (equal once trailing spaces and tabs are removed), tier 2 (equal once leading and trailing spaces and tabs are removed). At tier 2, new lines are re-indented from the old lines' leading whitespace to the file's. A context line is always written as the file's own matched line, never the patch's.

## Exit codes and output

- Exit 0: the tree is written. One line per changed path (`A` added, `D` deleted, `M` modified, `R old -> new` moved), then `ok: <n> files`.
- Exit 1: nothing is written. One `error: <code> <path>` line per failed edit (`no-match`, `ambiguous`, `missing`, `exists`, `unsafe`), then `failed: <k> of <n> edits`. Edits after a failed one still run, so every failure is reported.
- Exit 2: nothing is written, exactly one line: `error: usage`, `error: no-patch`, `error: format`, or `error: parse <N>`.

## Files

- `main.ts` — the CLI.
- `runner.ts` — the kit's trial runner (kept as the kit has it): with `MAKEFUN_ENV_URL` set, `main.ts` hands a trial over to it.
- `check.ts` — runs `main.ts` on the kit's public practice set and prints the practice score.
- `SPEC.md` is the whole contract; see the competition kit for it.

## Practice

```
node check.ts <path to the kit's program/generator/practice.json>
```

## License

MIT. See `LICENSE`.
