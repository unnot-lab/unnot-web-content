# unnot-web-content

Technical content-delivery repository for the Unnot website.

This repository stores only **published artifacts** (generated content) that the website reads at runtime without redeploying the UI.

## Purpose

- store `dist/manifest.json` and related content files;
- provide a stable runtime source for `VITE_CONTENT_BASE_URL`;
- support rollback via Git commits.

## Expected structure

```text
dist/
  manifest.json
  data/
    help-index.json
    mcp-registry.json
  help/
    **/*.md
```

## Important

- This repository is **generated-only**.
- Do not edit `dist/*` manually.
- **Full bundle** (help + optional MCP): publish script in the main **Unnot** app repo (`publish-web-content.cjs`).
- **MCP gallery only** (registry + icons + manifest MCP sections): use [`unnot-mcp-gallery`](https://github.com/unnot-lab/unnot-mcp-gallery) — `npm run publish:web-content-mcp -- --target "<path-to-this-repo>"` after `dist/` already exists with help (see that repo’s README). No Unnot checkout required for registry-only work.

