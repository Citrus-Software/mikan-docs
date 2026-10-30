# Mikan Documentation

Official documentation repository for [Mikan Rigging Toolkit](https://github.com/Citrus-Software/mikan).

👉 [View Live Documentation](https://citrus-software.github.io/mikan-docs/)

## 🚀 Local Development

To run and preview the documentation locally:

### Install dependencies

```bash
yarn install
```

### Start the development server

```bash
yarn start
```

The site will open automatically at http://localhost:3000. Changes in the docs/ directory will update live in your browser.

## 📁 Repository Structure

- `docs/`: Markdown (`.md`) and MDX (`.mdx`) documentation source files.
- `docusaurus.config.js`: Core site configuration (navigation, sidebars, theme options, and metadata).
- `src/`: Custom components, pages (such as the landing page), and custom CSS.
- `static/`: Static assets (images, diagrams, downloads) referenced directly in pages.

## 🛠️ Build & Test

Always run a local build before pushing changes to catch broken internal links or formatting errors:

```bash
yarn build
```