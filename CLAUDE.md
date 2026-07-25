# Project Instructions for Claude Code

## Branch model

This is a fork of [mpiorowski/late-sh](https://github.com/mpiorowski/late-sh).

| Branch | Role | Rule |
|---|---|---|
| `main` | Upstream mirror | **Never commit directly.** Fast-forward sync from `upstream/main` only. Must stay identical to upstream. |
| `feat/*` | Feature branches | One branch per feature. Created from `main`, rebased onto `main` after upstream syncs. |
| `custom` | Integration branch | All `feat/*` branches merge here. This is the **default working branch** — Docker images, local dev, and deployments use `custom`. |

### Adding a new feature

```
git checkout main
git pull upstream main --ff-only
git checkout -b feat/new-feature
# ... work, commit ...
git checkout custom
git merge feat/new-feature
git push origin custom feat/new-feature
```

### Syncing upstream

```
git fetch upstream main
git checkout main
git merge upstream/main --ff-only
git push origin main
git checkout feat/i18n-framework      # for each active feature branch
git rebase main
git checkout custom
git merge feat/i18n-framework
git push origin custom feat/i18n-framework --force
```

## Documentation

- `README.md` — upstream English README (one fork addition: `[中文版](README-zh.md)` link at top)
- `README-zh.md` — Simplified Chinese translation of README.md (fork-specific)
- `CLAUDE.md` — this file, project instructions for Claude Code (fork-specific)
- `CONTEXT.md` — upstream LLM context, architecture, invariants (never modified)

## Fork purpose

This fork adds Simplified Chinese (zh-Hans) TUI shell support to late.sh.
Target audience: Chinese-speaking users who want a localized terminal experience.
Upstream PRs ([#464](https://github.com/mpiorowski/late-sh/pull/464),
[#465](https://github.com/mpiorowski/late-sh/pull/465)) are pending;
`custom` is the primary way to use late.sh in Chinese.

To switch language: `Ctrl+O` → Display → Language → 中文(简体).

## Known gaps

These affect Chinese users and should be addressed before the fork is
"complete". Ordered by user impact.

### High priority

- **README-zh.md fork awareness** — still references `mpiorowski/late-sh`
  clone URLs; does not clarify that `ssh late.sh` connects to the
  *official* server, which does not have Chinese i18n. Should explain
  the fork's purpose and link to the Docker image
  (`ghcr.io/wings1848/late-sh/late-ssh:custom`).

- **SSH auth banner (AUTH_SETUP_BANNER)** — `late-ssh/src/ssh.rs:47-53`:
  `"late.sh requires SSH public-key auth.\r\nNew here? Install the
  companion CLI: ..."` — hardcoded English, shown on **every connection**
  before auth. Should use `i18n::tr()`.

- **SSH exit message (EXIT_MESSAGE)** — `late-ssh/src/ssh.rs:54`:
  `"Stay late. Code safe. ✨"` — hardcoded English, shown on **every
  disconnect**. Should use `i18n::tr()`.

### Medium priority

- **Chat section untranslated** — ~515 keys in `[chat]` section of
  `zh-hans.toml` are missing. The chat area (composer hints, message
  actions, room labels, polls, feeds, showcase) is where users spend
  most of their time. Falls back to English.

- **GitHub repo description** — currently `"A cozy terminal clubhouse
  for developers..."` — no mention of Chinese support.

- **Splash tips** — `late-ssh/assets/splash_tips/*.json` — 35 + 15
  English tips shown at login. Only `common.splash_hint` uses `tr()`.

### Low priority

- **state.rs hardcoded banners** — `late-ssh/src/app/state.rs`: 3 admin
  banners (`"Artboard editing is disabled..."`, `"Permissions updated..."`)
  are hardcoded English; only visible to admins/mods.

## i18n / Translation

This project supports English (en, default) and Simplified Chinese (zh-hans)
for the TUI shell. The framework lives in:

- `late-ssh/src/app/common/i18n.rs` — `tr(key)`, `trf(key, &[("k", v)])`, thread-local locale
- `late-ssh/locales/en.toml` — source language (always-complete fallback)
- `late-ssh/locales/zh-hans.toml` — Simplified Chinese translations
- `late-core/src/models/language.rs` — `Language` enum, `normalize_id()`, `cycle_id()`

### Adding translations

1. Add keys to `en.toml` first (this is the source-of-truth)
2. Add matching keys to `zh-hans.toml` with Chinese translations
3. Use `i18n::tr("section.key")` for static strings
4. Use `i18n::trf("section.key", &[("placeholder", value)])` for strings with `{placeholder}` substitutions

### Translation scope

- **Translate**: TUI shell text (labels, headings, hints, banners, footer shortcuts)
- **Don't translate**: Game narratives (`door/*`), technical identifiers, key names (Enter, Ctrl+O), command names

### Current translation status

Full TUI shell translation across 6 domains:
settings_modal, help_modal, chat UI, lobby/house, profile/dashboard/news,
common widgets + banner messages. ~1034 translation keys total.
