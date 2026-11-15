---
description: Build the site and check it in day and night mode
---

Verify the site before a commit or pull request.

1. Run `hugo --gc -d <a scratch directory>` (not `public/`). Report any errors and warnings. Warnings about `languageCode` are expected.
2. Look for template functions or config keys that are newer than Hugo 0.108.0, because CI builds with that version (see CLAUDE.md).
3. Start the preview server from `.claude/launch.json` and open the home page, a post, and the Work page in the browser pane.
4. For each, take a screenshot at desktop width and at mobile width (375px), first in day mode, then in night mode. To preview night mode, run `document.documentElement.setAttribute('data-theme','dark')` in the page.
5. Check that nothing uses a hardcoded hex colour outside `assets/scss/style.scss`, and that every colour variable has both a day and a night value.
6. If any colour pair changed, calculate its WCAG contrast ratio and compare with the table in DESIGN.md.
7. Stop the preview server and summarise what passed, what failed, and what you could not check.
