# CLAUDE.md

Guidance for Claude (and other AI coding tools) working in this repository.
Design decisions live in [DESIGN.md](DESIGN.md). Theme submodule workflow lives in [docs/theme-submodule.md](docs/theme-submodule.md).

## What this repo is

The source of <https://izabelacborges.github.io/>, Izabela Borges' personal website and blog. It is a [Hugo](https://gohugo.io/) site deployed to GitHub Pages.

It exists for two reasons:

1. A place to share thoughts, experiences and lessons learned as a DevOps engineer, in a personal, honest voice. The home page says it: "You don't start out writing good code." It is deliberately not a "Learn X in Y minutes" site.
2. A learning project for Hugo, Go templates, HTML, CSS and JavaScript. Small, understandable changes are preferred over clever ones, and explaining *why* a change works is part of the job.

Pages: Home, Blog (`posts`), Projects, Work, Now. There is also a `read-in-2023` page.

## Layout

| Path | What it is |
| --- | --- |
| `config.toml` | Site config, params and the main menu |
| `content/_index.md` | Home page copy |
| `content/posts/` | Blog posts |
| `content/pages/` | Standalone pages (about, now, work, projects, read-in-2023) |
| `data/author.json`, `data/social.json` | Author card and social links shown on the home page |
| `assets/scss/style.scss` | **All colours and the day/night variables.** Overrides the theme's file of the same name |
| `static/` | Files copied as-is (favicon, author image, social icons) |
| `archetypes/` | Templates used by `hugo new` |
| `themes/winston/` | Git submodule: the author's own fork, [adapted-hugo-winston](https://github.com/izabelacborges/adapted-hugo-winston) |
| `docs/design/` | Palette images referenced by DESIGN.md |
| `.github/workflows/hugo.yml` | Build and deploy to GitHub Pages on push to `main` |
| `.claude/` | Shared Claude Code settings, preview config and slash commands |

## Commands

```bash
hugo server -D          # local preview with drafts, http://localhost:1313
hugo --gc --minify      # production-style build into public/ (gitignored)
hugo new posts/my-post.md
```

## Versions

CI builds with **Hugo extended 0.170.0** and Dart Sass (see `.github/workflows/hugo.yml`), the latest stable release, and local Hugo should match. Keep both on the latest stable version.

- When you bump the Hugo version, change it in `.github/workflows/hugo.yml`, `config.toml` (`module.hugoVersion.min`) and here, say so explicitly, and test the build.
- Check deprecation warnings from `hugo --gc` and fix them rather than silencing them.
- `hugo.IsServer` is used instead of the removed `.Site.IsServer`.

## Working rules

- **Never edit `themes/winston/` for colours.** Colours belong in `assets/scss/style.scss` so a colour tweak is a single-repo change. Theme edits are fine when behaviour or markup must change, but they are a two-repo change: commit in the theme first, push it, then commit the updated submodule pointer here. See [docs/theme-submodule.md](docs/theme-submodule.md).
- **HARD RULE: the theme submodule pointer must always be a commit on `master` of the theme fork.** Never commit a pointer to a theme branch (`sass-use-modules`, a feature or test branch). Branches are for local tests only. Before committing `themes/winston`, check the commit is on the fork's `master`: `git -C themes/winston branch -r --contains $(git -C themes/winston rev-parse HEAD)` must list `origin/master`. If not, merge the theme work into the fork's `master` first (the fork only, never the upstream `zerostaticthemes` repo).
- **Never hardcode a colour in a template or a content file.** Use the CSS variables in `style.scss`. Hex values only appear in `style.scss`, DESIGN.md and `docs/design/`.
- **Keep day and night in sync.** Any new colour variable needs a value in both `:root` and `:root[data-theme='dark']`.
- **Check contrast** when changing any colour pair. Text needs at least 4.5:1 against its background in *both* modes. Accent colours that are too light for text on one background need a separate text variant (this is why links are not the same colour as the dot).
- **Never use blue-green (teal, aqua, cyan, "forest mist") anywhere.** Greens lean yellow: fern, leaf, moss, olive.
- **Don't invent content.** Copy on Home, Work and Now is personal. Edit wording only when asked. Never fabricate career details, dates or opinions.
- **Voice:** first person, warm, direct, a little self-deprecating. No corporate filler ("leverage", "seamless", "unlock").
- **Small commits, one concern each.** Commit messages are short and imperative, as in the existing history ("Change theme colour", "Edit first post").
- **Don't push to `main` directly.** Pushing `main` deploys the live site. Work on a branch and open a pull request.
- **Verify before claiming done.** Run `hugo --gc` and look at the result in a browser, in both day and night mode, at desktop and mobile width.
- **Mind accessibility:** alt text on images, one `h1` per page, visible focus states, and don't rely on colour alone.
- **Don't commit generated or old files:** `public/`, `resources/_gen/`, `*-old.*` are gitignored.
- **Treat anything written here as public.** This repo is public. Don't add private details to docs or commits.

## Dark mode

Day is the default. Night values are defined but there is no switch yet. When it is built:

- Toggle `data-theme="dark"` on `<html>`. Nothing else should be needed for colours.
- Persist the choice in `localStorage` and set the attribute in an inline `<head>` script before first paint, to avoid a flash of the wrong theme.
- Respect `prefers-color-scheme` as the initial default when the visitor hasn't chosen.
- The switch lives on its own branch and may need a small theme change (markup in `header.html`).

## Adding content

- New post: `/new-post <title>` or `hugo new posts/<slug>.md`. Set `draft: false` only when it is ready to publish. Posts need `title`, `date` and `description`.
- New page: add to `content/pages/` with a `menu.main` entry (`name`, `weight`) in the front matter. Existing weights: Work 4, Now 5.
- Images go in `static/images/`, with alt text.

## When unsure

Ask before: changing the colour palette, restructuring content, touching the deploy workflow, bumping versions, or doing anything that affects the live site.
