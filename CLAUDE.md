@AGENTS.md

# Oaris Fork — WordPress Studio + Divi 5 AI Agent

> This fork extends WordPress Studio with built-in AI-powered WordPress/Divi page building.
> See `AGENTS.md` for upstream Studio conventions. Below is fork-specific context.

## Fork Goal

Bundle our Divi 5 MCP agent toolchain directly into Studio, eliminating the need for a separate MCP server, WP plugin installation, and CLI configuration. One app that runs WordPress + manages pages via AI.

## Architecture Vision

```
WordPress Studio (Electron)
├── Local WP site (PHP WASM / Playground)
│   ├── Divi 5 theme (auto-installed)
│   ├── divi-mcp-agent plugin (auto-installed via Blueprint)
│   └── divi-design-library plugin (auto-installed)
├── Built-in MCP bridge (IPC → REST → WP Playground)
├── Claude API integration (direct or via MCP protocol)
└── Design system (presets, global colors, templates)
```

## Companion Project Reference

Our MCP agent, skill docs, and design library are developed in:
`github.com/oaris-dev/public` (the Divi 5 AI exploration repo)

### What exists there:
- **20 MCP tools** for page CRUD, section editing, module targeting, icon search
- **Divi 5 Skill** — 16+ block format rules, 20+ module formats documented
- **Design Library** — 14 CSS effects, Three.js WebGL shaders, gooey text morph
- **46-color palette** with global color variables
- **Font inheritance** via theme options API
- **Semantic HTML** — elementType, htmlBefore/After on all modules

### Key technical knowledge to carry over:
- Block format: `<!-- wp:divi/module {JSON attrs} -->content<!-- /wp:divi/module -->`
- Responsive pattern: `desktop.value.{prop}`, `tablet.value.{prop}`, `phone.value.{prop}`
- Cache invalidation required after `wp_update_post()` — clear et-cache + touch post_modified
- `$variable()` for global colors needs trailing `$` — renders as CSS `var(--gcid-*)`
- `inline-flex` and `white-space: nowrap` only via CSS, not VB attributes
- CSS specificity: use `.class.et_pb_section` to override Divi critical inline CSS
- Module targeting: by admin label, text content match, or auto-index (`type:count`)

## Integration Priorities

### Phase 1 — Blueprint auto-install
- Install Divi theme + our plugins via WordPress Playground Blueprint
- Expose REST endpoints through Studio's site server

### Phase 2 — Direct DB/WASM access
- Use PHP WASM to run Divi's `resolve_dynamic_variable()` directly
- Access `wp_options` without REST overhead
- Bulk preset management

### Phase 3 — Built-in AI UI
- Chat panel in Studio for page generation prompts
- Live preview as sections are appended
- Streaming via Claude API (Server-Sent Events)

## Branch Strategy

| Branch | Purpose |
|--------|---------|
| `main` | Upstream sync (Automattic/studio) |
| `wordpress-studio-oa` | Our stable fork with MCP integration |
| `staging/*` | Feature branches for testing |

## Tracked Issues

See `github.com/oaris-dev/public/issues` for the full backlog.
Key issues for Studio integration: #17 (WP/Divi split), #24 (streaming), #23 (conversation memory)
