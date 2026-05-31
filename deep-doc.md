Generate a deep, thorough technical document about a library or service for my personal understanding.

## Instructions

The user will specify a library, service, or codebase. Your job is to read deeply and produce a comprehensive technical document.

**Pre-authorized actions — execute without asking for confirmation:**

- Read any file, directory, or config in the workspace
- Run grep, find, cat, ls, tree, and any other read-only commands
- Install packages solely to inspect types/source (e.g. `npm install`, `pip install`)
- Follow imports across files as deep as needed

**Document structure — complete every section, do not skip:**

1. **Overview** — What problem does this solve? Where does it fit in the ecosystem?
1. **Architecture** — High-level design, major subsystems, and how they relate
1. **Core Modules** — For every significant module/file: what it does, key exports, internal logic
1. **Data Flow** — How data moves through the system end-to-end; include key execution paths
1. **Public API Reference** — All public methods, options, and signatures with descriptions
1. **Configuration** — Every config option, its type, default, and effect
1. **Extension Points** — Plugins, hooks, middleware, or override patterns
1. **Design Decisions** — Notable patterns, tradeoffs, and architectural choices
1. **Gotchas & Non-obvious Behaviors** — Things that would surprise a new user
1. **Glossary** — Key internal terms defined

**Execution rules:**

- Read the actual source before writing any section — do not rely on prior knowledge
- If a file is large, keep reading until you have genuine understanding (do not stop at 1%)
- After finishing one section, immediately continue to the next without pausing
- Do not stop to ask “should I continue?” — keep going until the document is complete
- Write to `docs/deep-dive-[library-name].md` as you go (so progress is saved)
- When fully done, print: ✅ Deep-dive document complete → docs/deep-dive-[library-name].md

This is a long-running task. Maintain momentum from start to finish.