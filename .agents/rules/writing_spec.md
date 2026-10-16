# Blog Writing and Translation Rules

When writing, generating, editing, or translating blog posts for this repository:

1. **NO EMOJIS OR ICONS:**
   - Never use any emojis or icons (e.g. 📐, 💻, ⚛️, 🌿, 🚀, 💡, 📌, 📝, 🔍, ⚠️, ⭐, 🎯, 🔥, ✨, 🎉, ☕, 🏃, etc.) anywhere in post titles, headers, bullet points, or body content. Maintain a clean, academic, and minimalist tone.

2. **LEAF BUNDLE ARCHITECTURE & SLUGS:**
   - Each post is its own directory named in English kebab-case (e.g. `stillness-beyond-screens/`).
   - DO NOT include order prefixes like `01-`, `02-` in directory names or in `slug` fields.
   - Inside each post bundle:
     - `index.vi.md` with `slug = "vietnamese-slug-without-numbers"`
     - `index.en.md` with `slug = "english-slug-without-numbers"`
     - Associated media assets (images, diagrams) reside in the same bundle.
   - Section indexes use `_index.vi.md` and `_index.en.md`.

3. **FRONTMATTER (TOML `+++ ... +++`):**
   - Always use TOML format.
   - Comments inside `+++` MUST use `#`, NEVER use HTML comments `<!-- ... -->`.
   - Include required fields: `title`, `date`, `draft`, `hideMeta`, `tags`, `categories`, `math`, `description`, `summary`.
   - Never set `date` in the future relative to the build time.
   - For LaTeX formulas, always set `math = true`.

4. **LINKING:**
   - Do NOT use `{{< relref >}}` to link to draft posts. Use relative Markdown links like `[Title](slug/)` or `[Title](./slug/)`.

5. **MATH:**
   - Use standard LaTeX with `$ ... $` for inline math and `$$ ... $$` for block math.

6. **HEADINGS:**
   - Content starts at `##` (H2). Never use `#` (H1) inside the markdown body.

7. **VERIFICATION:**
   - Always verify site generation with `hugo --minify` before committing.
