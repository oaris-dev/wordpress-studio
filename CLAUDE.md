@AGENTS.md

# Oaris Fork — WordPress Studio + Divi 5 AI Agent

> This fork extends WordPress Studio with built-in AI-powered WordPress/Divi page building.
> See `AGENTS.md` for upstream Studio conventions. Below is fork-specific context.

## Fork Goal

Integrate our Divi 5 MCP agent into Studio's existing AI agent system. Studio already has Claude Agent SDK, tools, skills, and MCP server — we extend them with Divi-specific capabilities.

## What Upstream Already Has

```
apps/cli/ai/
├── agent.ts          — Claude Agent SDK (Sonnet/Opus 4.6)
├── tools.ts          — 13 tools (site CRUD, WP-CLI, screenshots, block validator)
├── mcp-server.ts     — MCP stdio server wrapping Studio tools
├── system-prompt.ts  — System prompt builder
├── security.ts       — Path approval, allowed tools
├── block-validator.ts — Validates blocks via headless browser + wp.blocks
└── browser-utils.ts  — Puppeteer for screenshots + validation

apps/studio/src/modules/agent-instructions/
├── lib/skills.ts     — Skill installation to .agents/skills/{id}/SKILL.md
└── lib/skills-constants.ts — BUNDLED_SKILLS registry
```

## What We Add

### From companion project (github.com/oaris-dev/public):
- **24 MCP tools** — page CRUD, section editing, module targeting, icon search, preset management
- **Divi 5 Skill** — block format rules, 20+ module formats, animation/scroll/semantic HTML
- **Design Library plugin** — 14 CSS effects, Three.js WebGL, gooey text morph
- **Preset system** — 233 clean presets, audit/cleanup/dedup tools
- **46-color palette** with `$variable()` global color references

## Integration Strategy

**Don't rebuild — extend upstream's systems:**

1. **Register Divi skill** → Add to `BUNDLED_SKILLS` in `skills-constants.ts`
2. **Add Divi tools** → Extend `createStudioTools()` in `tools.ts`
3. **Blueprint for Divi** → Auto-install theme + plugins on site creation
4. **Keep MCP compatible** → Our MCP server works independently with Claude Code

## Product Direction

| Path | Description | Status |
|------|-------------|--------|
| **Path 1** | Local WP + MCP (current hybrid) | Working, used in production |
| **Path 2** | Studio integration (extend upstream) | Exploring on this branch |
| **Path 3** | Parallel: maintain both paths | **Recommended** |
| **Path 3.1** | Deep Studio fork with custom UI | Future option |

Current recommendation: **Path 3** — keep improving Local WP MCP (real projects use it), explore Studio integration in parallel. Don't lock in to one path yet.

## Key Technical Knowledge

- Block format: `<!-- wp:divi/module {JSON attrs} -->content<!-- /wp:divi/module -->`
- Responsive: `desktop.value.{prop}`, `tablet.value.{prop}`, `phone.value.{prop}`
- Cache invalidation required after `wp_update_post()` — clear et-cache + touch post_modified
- `$variable()` for global colors needs trailing `$` → renders as CSS `var(--gcid-*)`
- `inline-flex` and `white-space: nowrap` only via CSS, not VB attributes
- CSS specificity: `.class.et_pb_section` to override Divi critical inline CSS
- Module targeting: by admin label, text content match, or auto-index (`type:count`)
- Semantic HTML: `elementType`, `htmlBefore`, `htmlAfter` on any module
- WP-CLI works with Local by Flywheel via sourcing ssh-entry env vars
- Divi 5.1.0 AI Agent: 742KB bundle with OpenAI Assistants SDK, no Divi intelligence yet

## Branch Strategy

| Branch | Purpose |
|--------|---------|
| `trunk` | Upstream sync (Automattic/studio) |
| `wordpress-studio-oa` | Our integration branch |
| `staging/*` | Feature experiments |

## Tracked Issues

### This repo (oaris-dev/wordpress-studio)
- #1 — Investigate upstream skills system for Divi skill registration
- #2 — Blueprint: auto-install Divi + MCP plugins
- #3 — Add Divi tools to Studio's createStudioTools()

### Companion repo (oaris-dev/public)
- #17 — Consider splitting WordPress MCP from Divi MCP
- #24 — Streaming for live generation preview
- #32 — WP-CLI tool for direct DB access
- #33 — Block validator
- #34 — Screenshot tool
