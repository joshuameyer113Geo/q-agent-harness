# The 3 things in my Claude Code setup

If you've been meaning to set up Claude Code properly, this is where I'd start. Three pieces, all free, all open source, each takes minutes.

## 1. The instructions file

Claude Code reads a file called CLAUDE.md before it works for you: your stack, your rules, what it should never touch, what done means. Without it you re-explain yourself every session.

Start from my template: [HOT-MEMORY-template.md](https://github.com/kju4q/q-agent-harness/blob/main/memory/hot/HOT-MEMORY-template.md). Fill it in, save it as CLAUDE.md in your project root, and your next session starts by reading who you are.

## 2. The guard

A small mod I built that watches what Claude Code is about to do and blocks the dangerous stuff before it runs. Env files stay untouchable, risky commands never get the chance to execute. I tested it by asking Claude to read my own keys, and it refused me with my own rule.

Install it from inside Claude Code with one command:

/plugin install guard --marketplace kju4q/guard

The repo is here if you want to read what it blocks first: [github.com/kju4q/guard](https://github.com/kju4q/guard)

## 3. Skills

Reusable abilities you teach Claude Code once and invoke with one command. I built one called slides, I type /slides in any repo and it turns that project into a presentation from the actual code. It lives in its own repo: [github.com/kju4q/slides](https://github.com/kju4q/slides).

The rest of my skills live here: [github.com/kju4q/q-claude-skills](https://github.com/kju4q/q-claude-skills). The README has install steps for both Claude Code and the Claude app. Copy one into your setup, then change it until it works the way you work, and when you're ready to build your own from scratch, [this guide walks you through it](https://github.com/kju4q/q-claude-skills/blob/main/docs/make-your-own-skill.md).

Stuck on any of these? Ask me in the comments, that's what they're for.

Built by [@qendresahhoti](https://instagram.com/qendresahhoti), more at [github.com/kju4q](https://github.com/kju4q)
