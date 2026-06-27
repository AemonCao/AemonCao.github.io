# Repository Guidelines

## Project Structure & Module Organization

This repository is a Hexo-based GitHub Pages blog. Global site settings live in `_config.yml`, and reusable post/page templates are in `scaffolds/`. Author content belongs in `source/`: posts in `source/_posts/`, drafts in `source/_drafts/`, and standalone pages such as `source/about/`, `source/categories/`, and `source/tags/`. Because `post_asset_folder` is enabled, keep article images and other media in a folder beside the matching Markdown post, for example `source/_posts/my-post/cover.png`. The active theme is under `themes/next/`; `themes/landscape/` is the bundled default theme. Generated output in `public/` is not source and should not be committed.

## Build, Test, and Development Commands

- `pnpm install`: install dependencies from `pnpm-lock.yaml`.
- `pnpm server`: start the local Hexo preview server.
- `pnpm build`: generate the static site into `public/`.
- `pnpm clean`: remove Hexo generated files before a fresh build.
- `pnpm exec hexo new post "Post Title"`: create a new post from the scaffold.

GitHub Actions currently builds on Node.js `24.18.0` with `pnpm run build`; use that command too when validating deployment-related changes.

## Coding Style & Naming Conventions

Write posts as Markdown with YAML front matter. Keep filenames descriptive and stable because permalinks include `:title`; existing content uses both English slugs and Chinese titles. Store post-specific assets in a same-name folder and reference them with relative paths. For YAML, EJS, Stylus, and JavaScript, follow the surrounding style, use two-space indentation where present, and avoid broad theme refactors when making content-only changes.

## Testing Guidelines

There is no automated test suite configured. Treat `pnpm build` as the required validation for content, configuration, and theme changes. For layout or style edits, also run `pnpm server` and review affected pages locally, including posts with images, code blocks, categories, and tags.

## Commit & Pull Request Guidelines

Recent commits are short Chinese summaries such as `更新文章`, with occasional conventional prefixes like `typo:`. Keep commits focused and imperative; mention the affected article, theme, or config when useful. Pull requests should include a concise description, validation performed, linked issue if applicable, and screenshots for visual theme or layout changes. Do not commit `node_modules/`, `public/`, logs, or local editor artifacts.

## Security & Configuration Tips

Do not place secrets in `_config.yml`, workflow files, or source content. Keep deployment settings in GitHub repository secrets and preserve `source/CNAME` when changing Pages configuration.
