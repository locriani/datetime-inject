# datetime-inject

UserPromptSubmit hook. Emits exactly one bracketed line to stdout, which Claude
Code appends to the conversation as context:

```
[Current TimeStamp: Tuesday, June 2nd, 2026 | 2026-06-02 14:33:07 PDT (UTC-07:00)]
```

## Why

The harness already stamps the **date**. It does not carry the wall clock, and
time-of-day is what "before EOD", "overnight", "how long has this been running"
all turn on.

The script takes a single `date(1)` snapshot so every field refers to the same
instant — no cross-call second-skew — and deliberately omits `set -e`, because a
non-zero exit from a UserPromptSubmit hook can block prompt submission. It always
exits 0.

Mutation surface: **none**. It reads the clock, writes one line to stdout, and
ignores its stdin.

## Story

Approved 2026-09-13: "pending list so far is approved" — the verbatim
[SETUP-LIST.md](https://github.com/locriani/ai-additions/blob/main/SETUP-LIST.md)
ledger row:

> | `datetime-inject` | 2026-09-13 | "pending list so far is approved" | **Yes** — 2026-09-16, "datetime-inject logic-rendering are approved for enabling": `claude plugin install datetime-inject@ai-additions` (user scope, 1.0.0) |

**Enabled** 2026-09-16 on "datetime-inject logic-rendering are approved for
enabling" — installed then from the `ai-additions` marketplace; since the
2026-10-10 extraction this repo's own marketplace is the install source
(below).

## Provenance

From `Standard Configs/claude-plugins/datetime-inject/` at `d36d794`. Script
byte-identical; only its registration moved to `${CLAUDE_PLUGIN_ROOT}`.

Extracted 2026-10-10 from
[locriani/ai-additions](https://github.com/locriani/ai-additions) at commit
`a01d7eb87d3d24b1cd65bcf509eed82000f7fa21` via `git filter-repo
--subdirectory-filter plugins/datetime-inject`; both carried commits keep their
original authorship. Content is byte-identical to `plugins/datetime-inject` at
that commit except this README's Story, Provenance, Install, and Layout
sections.

## Install

```
claude plugin marketplace add locriani/datetime-inject
claude plugin install datetime-inject@datetime-inject
```

Update: `claude plugin marketplace update datetime-inject`, then reinstall.

## Layout

- `.claude-plugin/plugin.json` — plugin manifest: name, description, version 1.0.0, author, MIT.
- `.claude-plugin/marketplace.json` — this repo's own marketplace manifest: one entry, `source: ./`.
- `hooks/datetime-inject.sh` — the hook: one `date(1)` snapshot, one bracketed line to stdout, always exits 0.
- `hooks/hooks.json` — registers the script as a `UserPromptSubmit` command hook via `${CLAUDE_PLUGIN_ROOT}`.

No tests, no runtime dependencies beyond `bash` and `date(1)`.
`.github/workflows/validate.yml` checks JSON manifests (`json.tool`), compiles
any Python sources, and runs `bash -n` over shell scripts on every push.
