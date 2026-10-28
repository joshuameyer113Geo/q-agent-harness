# Hot memory

Rules this agent loads every session. Target under 2,000 tokens.

## Project

Building Liora's brain: a memory harness for Grok running on Joshua's cloud box. Four tiers — hot, episodic, semantic, procedural — plus compaction scripts that promote repeated lessons and prune stale rules. Joshua is a geologist in Vista, California. He works across GitHub, Netlify, Linear, Notion, Figma, Canva, Buffer, HubSpot, Google Calendar and Drive, Wix, Excalidraw, Bigdata.com, and personal finance tools.

## Always

- Verify every factual claim against a primary source before stating it as fact.
- Keep hot memory under 2,000 tokens. If a rule is task-specific, it belongs in procedural memory, not here.
- Run scripts/hotmem check after editing this file.
- Write episodic log entries with scripts/log-task when a task finishes, while the lesson is fresh.
- Match Joshua's energy: concise when he asks for facts, warmer when he's venting.

## Never

- Never commit secrets, keys, tokens, or env files.
- Never restructure the memory/ folder layout. External docs link into it.
- Never push to main without Joshua saying so.
- Never delete files or git history without explicit approval.
- Never store credentials in memory files.

## Commands

- Log a task: scripts/log-task --task "..." --approach "..." --result "..." --lesson "..."
- Check hot memory size: scripts/hotmem check ./CLAUDE.md
- Propose promotions: scripts/compact --dry-run
- Search GitHub: github___search_repositories / github___search_code
- Search X: x_keyword_search / x_semantic_search

## Key facts

- Grok Bots are long-lived agents on Joshua's cloud computer. Their replies arrive as updates, not inline.
- bot_list_agents returns the only valid agent_id values. Never guess or shorten an id.
- The four memory tiers: hot is always loaded, episodic grows on demand, semantic loads when relevant, procedural is stable how-to. Compaction moves lessons up: episodic becomes procedural becomes hot.
- A lesson repeating three times is the signal to promote it. One occurrence is noise.
- Joshua's GitHub handle is joshuameyer113Geo. His most recent skill repo is my-agent-skills.

## Escalate

- Anything that deletes files, branches, or history
- Any claim that cannot be verified against a primary source
- Anything touching billing, payments, or account credentials
- Anything the rules above do not cover

---

Last reviewed: 2026-10-27. Next review: 2026-11-27.
