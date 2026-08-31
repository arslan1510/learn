# pi-learn

[![video](assets/thumbnail.png)](https://www.youtube.com/watch?v=kzcI5F4tGiU)

A reusable [Pi](https://github.com/earendil-works/pi) package for learning from first principles. It combines a teaching skill, graded quizzes, Obsidian-friendly lesson logs, and maker subagents that render and inspect lesson visuals before publishing them.

## Included resources

- `skills/teach/` — first-principles teaching and discovery-based explanations
- `skills/visualize/` — decides when a visual genuinely improves a lesson
- `extensions/quiz.ts` — graded single- and multi-select questions with immediate feedback
- `extensions/md-log.ts` — mirrors lesson prose and question/answer blocks into Markdown
- `agents/mermaid-maker.md` — verifies structural diagrams by rendering and inspecting them
- `agents/svg-maker.md` — verifies spatial/geometric visuals by rendering and inspecting them
- `extensions/visual-tools/tools/` — child-only authoring and rendering tools used by the maker agents

The package intentionally does **not** bundle its own `ask_user_question`, web-research, or subagent launcher. It uses the maintained extensions already installed in your Pi environment instead.

## Dependencies

Install these Pi packages once at user scope:

```bash
pi install npm:@juicesharp/rpiv-ask-user-question
pi install npm:pi-subagents
pi install npm:pi-web-access
```

They provide:

- `@juicesharp/rpiv-ask-user-question` — ungraded preference and clarification questions
- `pi-subagents` — the `subagent` tool, built-in `researcher`, and package-agent discovery
- `pi-web-access` — the web tools used by the built-in researcher

The maker agents inherit your current subagent model policy; this package does not pin provider-specific model IDs.

## Install everywhere

Install this repository as a user-scoped local Pi package:

```bash
pi install /absolute/path/to/learn
```

Replace `/absolute/path/to/learn` with wherever you cloned the repository. Pi records that path in `~/.pi/agent/settings.json`, making the package available in every project on the machine. The directory must remain at that path.

For a portable install from this fork's package branch instead:

```bash
pi install git:github.com/arslan1510/learn@extension
```

Do **not** also clone this repository into each project's `.pi` directory. One user-scoped package installation is enough.

## Use

Start Pi in any project. The `teach` and `visualize` skills, `quiz` tool, Markdown log commands, and maker agents are discovered automatically.

```text
/md-log path/to/existing-note.md
/md-unlog
```

`/md-log` only links an existing file. It backfills the active session branch and then appends new user prompts, lesson prose, quizzes, and `ask_user_question` exchanges.

The visual makers publish verified PNGs under `<project>/viz/`. Mermaid rendering uses the package's `@mermaid-js/mermaid-cli` dependency. SVG rendering requires `rsvg-convert` (preferred) or ImageMagick's `magick` on `PATH`.

## Development

```bash
npm install
npm run typecheck
```

Pi package resources are declared in the root `package.json`. Maker tools are loaded only in their child agents through `subagentOnlyExtensions`, so they do not clutter the parent session's tool list.

## Keep the fork in sync

`origin` is this fork and `upstream` is the original repository. The [Sync upstream main](.github/workflows/sync-upstream.yml) workflow checks upstream every Monday at 03:23 UTC and updates the fork's `main` branch automatically. It can also be run manually from the repository's **Actions** tab.

`main` remains a clean mirror of upstream. Updates are merged into the Pi-specific `extension` branch deliberately so conflicts or compatibility problems can be resolved before release:

```bash
git fetch origin
git switch extension
git merge origin/main
npm install
npm run typecheck
git push origin extension
```

If the merge conflicts, resolve it on `extension` and rerun the typecheck before pushing.
