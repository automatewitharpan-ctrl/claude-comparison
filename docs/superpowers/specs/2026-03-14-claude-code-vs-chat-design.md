# Design Spec: Claude Code vs Claude Chat Comparison Page

**Date:** 2026-03-14
**Project:** claude-comparison
**Status:** Approved

---

## Goal

Replace the existing `index.html` (Claude 2.0 vs 1.3) with a new single-page HTML document comparing **Claude Code** (CLI tool) and **Claude Chat** (web interface). Deploy to the existing GitHub remote and Vercel project.

## Visual Style

- Dark background (`#0a0a0a`)
- Split-screen product cards with color identity: purple/violet for Claude Chat, teal/green for Claude Code
- Subtle card borders with glow effect
- Inter/system font stack
- Clean grid layout, no framework dependencies

## Layout (top to bottom)

1. **Header** — minimal nav, title, Anthropic attribution
2. **Hero** — tagline: *"Same intelligence. Different superpowers."* + one-liner for each product
3. **Split-screen cards** — two full-width side-by-side panels:
   - Claude Chat (purple): browser-based, general Q&A, writing, research, no setup required
   - Claude Code (teal): CLI tool, agentic coding, file editing, runs commands, IDE integration
4. **Comparison table** — rows: Interface, Primary use, Setup required, File access, Runs commands, Memory/context, Best for, Pricing tier
5. **"Which one should I use?" section** — two scenario cards linking to the right product
6. **Footer** — anthropic.com attribution

## Deployment

- **File:** `claude-comparison/index.html` (replace in-place)
- **GitHub:** commit + push to existing remote (user pushes manually due to HTTPS credential requirement)
- **Vercel:** deploy via MCP tool to existing project `claude-comparison` under team `automatewitharpan-2641s-projects`
