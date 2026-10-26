# The harness tax: measure what Claude Code loads before you type, and cut what doesn't earn it

## Why this exists

Three studies from September and October 2026 looked at the same question from different sides.

[HarnessTax](https://arena.ai/blog/coding-agents-harness-tax/) (Pan, Yang, Arabzadeh, Chiang, Stoica, Zaharia, published by Arena on 16 Sept 2026, project page at [harnesstax.github.io](https://harnesstax.github.io/)) ran 21 model and harness pairs through Claude Code, Codex CLI, and Pi, a minimal harness with four tools: read, write, edit, and bash. On SWE-bench Lite the average harness effect on success stayed within about 2 points, while Claude Code cost about 2x what Pi did. Across both benchmarks, the same model could reach similar success at up to 5x the cost. Claude Code's starting context was over 10x Pi's.

[Liu and Han at Shandong University](https://arxiv.org/abs/2610.04433), [covered by VentureBeat](https://venturebeat.com/orchestration/a-coding-agents-harness-can-triple-its-cost-without-making-it-more-accurate-study-finds), compared Claude Code, OpenCode, and mini-SWE-agent, whose agent is about 100 lines of Python with bash as its only tool. The fixed preamble each one sends is 16,581 tokens in Claude Code, 7,025 in OpenCode, and 829 in mini-SWE-agent, paid again on every step. Cost per task varied by up to 3x. On 447 tasks, Claude Code and mini-SWE-agent landed within five points of each other, and OpenCode trailed by up to 9.

[Lehmann (TU Dresden) and Vahdati (Hannover, TIB)](https://arxiv.org/abs/2610.30499) let models build their own harness and rewrite it twice. Two rewrites raised the held-out score by 2.80 points, with a 95% interval of [-1.44, 7.35], so the gain could be zero. Human-steered systems already report ceiling scores on the public games.

Put together: a bigger harness costs more, it doesn't reliably score more, and in the one study that tried it, letting the model rebuild its own harness didn't reliably help. These are four benchmarks, and some samples are small (HarnessTax used 30 tasks per benchmark). The direction is solid. The exact multiples aren't.

## What the tax is

It's everything Claude Code loads before you type. The system prompt, the built-in tools, MCP tool schemas, MCP server instructions, your CLAUDE.md and memory files, and your skills. All of it is sent again on every step.

On a Max plan you don't pay per token. So for me the tax isn't money. It's context. Less room for the actual work, and more to carry on every step.

## Measure it

Open a fresh session and run `/context` before you type anything else. That way Messages is close to zero and everything you see is the tax.

Here's what each line means.

- **System prompt**: Claude Code's own instructions to the model.
- **System tools**: the built-in tools, like Read, Edit, and Bash, and their schemas.
- **System tools (deferred)**: built-in tools that are listed by name and only load when they're needed.
- **MCP tools**: the MCP tool definitions that are actually in context.
- **MCP tools (deferred)**: MCP tools that are known by name but not loaded until Claude searches for them.
- **MCP server instructions**: the text each MCP server sends to explain when to use it. This loads at start.
- **Memory files**: your CLAUDE.md files, global and project.
- **Skills**: the description of every skill Claude Code can see. The full skill only loads when it's used.
- **Messages**: the conversation so far.
- **Free space**: what's left for the work.
- **Autocompact buffer**: room Claude Code keeps free so it can compact the conversation automatically before the window fills.

System prompt and System tools are Claude Code's own. You can't cut them. MCP tools, MCP server instructions, memory files, and skills are yours. That's where the work is.

## What I found on mine

I ran it on my own setup. 28.4k tokens loaded before I typed a word.

18.2k of that was built-in tools, which I can't touch. 6k was 32 skills. When I checked my session logs, 3 of them had been used in the last 30 days. I kept one more I couldn't confirm either way. My 60 MCP tools cost 625 tokens in context, because they load on demand. A separate 46.5k was listed as deferred, and it only loads when a tool is used.

After I turned off the skills I don't use, it was 19 skills and 2.9k.

Before:

<!-- before -->

After:

<!-- after -->

## What to cut first

In this order.

**1. Skills you haven't called in 30 days.** To find out which ones, this is the prompt I used:

```
List every skill currently loaded, with its folder path and a one-line description of what it does. Group them: the ones I've invoked in the last 30 days (check session logs if you can, otherwise mark unknown), and the rest. Don't move anything yet, just show me the list with a number next to each.
```

The "mark unknown" part matters. It's what kept me from cutting a skill I still wanted.

**2. Skills synced from claude.ai.** Moving the folder doesn't work. I tried it, and they came back on the next sync. Turn them off at [claude.ai/customize/skills](https://claude.ai/customize/skills) instead. [Anthropic's help article](https://support.claude.com/en/articles/12512180-using-skills-in-claude) has the details. This also turns them off in claude.ai chat, so only switch off the ones you don't use there either.

**3. MCP servers you don't use.** Run `/mcp` to see them and remove the ones you never call. In current Claude Code, MCP tools already load on demand, so the schemas cost little. But [the docs](https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search) say tool names and server instructions still load at the start of every session.

**4. CLAUDE.md rules that never changed an output.** If you can't point to a run where a rule made a difference, test it. The compare step in [the loop](harness-loop.md#4-compare) is how.

**5. Memory files that grew past what you read.** If you wouldn't read it yourself, the agent doesn't need it on every step. [COMPACTION.md](../COMPACTION.md) covers what to move down a tier or out.

## The rule for what stays

Anything that loads before you type has to have been used in the last month. If it hasn't, it goes until it earns its way back.

Run `/context` again after every change. Then add a line to the task log with `scripts/log-task`, with the before and after numbers, so you can see it drift over time.

## Sources

- [HarnessTax: How Much Does the Harness Matter for Coding Agents?](https://arena.ai/blog/coding-agents-harness-tax/), Arena, 16 Sept 2026, and the [project page](https://harnesstax.github.io/)
- [What Does a Harness Buy? Tokens, Mostly](https://arxiv.org/abs/2610.04433), Liu and Han, Shandong University, 3 Oct 2026
- [A coding agent's harness can triple its cost without making it more accurate, study finds](https://venturebeat.com/orchestration/a-coding-agents-harness-can-triple-its-cost-without-making-it-more-accurate-study-finds), VentureBeat, 23 Oct 2026
- [Can LLMs Improve Their Own Harness? A Controlled Study on ARC-AGI-3](https://arxiv.org/abs/2610.30499), Lehmann and Vahdati, 21 Oct 2026

Built by [@qendresahhoti](https://instagram.com/qendresahhoti), more at [github.com/kju4q](https://github.com/kju4q)
