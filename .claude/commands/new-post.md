---
description: Scaffold a new blog post as a draft
argument-hint: <post title>
---

Create a new blog post titled "$ARGUMENTS".

1. Turn the title into a lowercase, hyphenated slug.
2. Run `hugo new posts/<slug>.md`.
3. In the new file's front matter, make sure these are set: `title`, `date` (today), a one-sentence `description`, and `draft: true`. Remove the placeholder description and image from the archetype if they don't fit.
4. Leave the body as a short outline of headings only. Do not write the post: the content is the author's voice and experience, and must not be invented.
5. Tell me the file path and remind me to set `draft: false` when it's ready to publish.

Follow the rules in CLAUDE.md.
