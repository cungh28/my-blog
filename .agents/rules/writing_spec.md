# Blog Writing and Translation Rules

When writing, generating, editing, or translating blog posts for this repository:

1. **NO EMOJIS OR ICONS:**
   - Never use any emojis or icons (e.g. 📐, 💻, ⚛️, 🌿, 🚀, 💡, 📌, 📝, 🔍, ⚠️, ⭐, 🎯, 🔥, ✨, 🎉, ☕, 🏃, etc.) anywhere in post titles, headers, bullet points, or body content. Maintain a clean, academic, and minimalist tone.

2. **FILE NAMING & EXTENSIONS:**
   - Vietnamese content: `<slug>.vi.md`
   - English content: `<slug>.en.md`
   - Filenames in lowercase kebab-case without Vietnamese diacritics.

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
