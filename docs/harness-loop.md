# The loop: how Google's self-editing harness works, and how to run it by hand

On October 9, 2026, Google Cloud AI Research open sourced [Harness²](https://github.com/google-research/harness2), a system where the model stays locked and a second agent edits only what's wrapped around it. The paper is [Harness²: Recursive Agent Harnessing for an Open World](https://arxiv.org/abs/2610.14224). The model that solved 64 percent of a benchmark of office, web, and coding tasks with its default setup solved nearly 76 percent once its harness had been edited. GLM-5 inside OpenCode, 64.2 to 75.7. Gemini Flash moved too, 72.4 to 77.5. Nothing inside the models changed. Every extra point came from files.

Two things before you take that number home. The authors scored their own system, the agent doing the editing was Claude Opus 4.8, and some of the gains are small enough to be luck. And on most of their benchmarks the weaker model gained more, so GLM-5 with an edited harness ended up beating Gemini Flash running the defaults. That's the same thing HuggingFace found a week earlier running one model through four coding tools and getting 62 percent in one and 33 in another. Two labs, two methods, one answer: the model is the part you rent, the harness is the part you own, and it's the part that moves the score.

This doc does two things. It maps Google's eight harness files onto this repo, so you can see what you already have. Then it writes their loop down as something you can run by hand in one session.

## The eight files

Google's harness is eight components, and every one of them is a plain file mounted into the agent's runtime. Here's where each one lives in this kit, and what it is in Claude Code terms.

| Google's component | What it is | Where it lives here |
| --- | --- | --- |
| System prompt | The instructions the agent reads before it starts | [`memory/hot/`](../memory/hot/), your `CLAUDE.md` or `AGENTS.md`. Start from [the hot memory template](../memory/hot/HOT-MEMORY-template.md) |
| Guardrails | What the agent may not do, and what needs a human | [`docs/approval-policy.md`](approval-policy.md), [`docs/architecture-boundaries.md`](architecture-boundaries.md). For a version that blocks before the command runs, [guard](https://github.com/kju4q/guard) |
| Memory | What the agent carries between tasks | The four tiers in [`memory/`](../memory/), and [COMPACTION.md](../COMPACTION.md) for how things move between them |
| Skills | Proven workflows loaded when the task matches | [`memory/procedural/`](../memory/procedural/). In Claude Code these are skills, and [q-claude-skills](https://github.com/kju4q/q-claude-skills) has mine |
| Tool guidance | Which tools to reach for on which job | [`docs/task-setups.md`](task-setups.md), plus the Commands section of your hot memory file |
| Scripts | Deterministic steps the agent can run instead of improvising | [`scripts/`](../scripts/): `log-task`, `hotmem`, `compact` |
| Subagents | A second agent with a fresh context, used to check the first | The external review loop in [VERIFICATION.md](../VERIFICATION.md), the strongest of the four loops |
| Plugin | Code that runs around the agent, before and after its actions | In Claude Code, hooks and plugins. [guard](https://github.com/kju4q/guard) is one |

If you've done [the 3 upgrades](three-upgrades.md), you already have a system prompt, tool guidance, and the beginning of guardrails. Most people stop there and that's fine. What Google adds is a way of editing the eight you have so they get better instead of just getting longer.

## The loop

Here's what their agent does, and this is the part you can copy. It proposes two small edits to its own harness, one general and one specific to the task. It runs the same task once with each version. It compares every run against the original. It keeps only the edits where the output got better. It writes a one line lesson into a file called `EXPERIENCE.md` so the next task is steered by what it learned. Then it does it again.

Propose, run, compare, keep, log.

The detail that matters: the agent isn't allowed to believe itself. It can't keep a lesson unless two runs side by side prove it. Every line in that experience file is backed by two runs it compared side by side.

Now the honest version of what most of us do. We add a rule to the instructions file, we run the next task, it feels better or it doesn't, and the rule stays either way. That's the loop with the compare step missing, and the compare step is where the whole thing lives. The paper doesn't ask you to trust a change. It asks you to run the same task twice and keep the version that won.

## Run it by hand, one session

You need one task you ran this week that disappointed you, and the prompt you used. That's it. Thirty to forty minutes.

### 1. Baseline

Run the task again, as is, with your current instructions file. Save the full output somewhere you can look at it next to the others. A folder called `loop/` with `baseline.md` in it works.

### 2. Propose

In the same session, ask for the edits. The rule is that every proposed edit has to point at something that actually happened in the baseline run, not at a general idea of good practice.

```
Read my instructions file and the output you just produced. Propose two or three edits to the instructions file, one line each, that would have changed this specific output for the better. For each edit, quote the line in the output that it would have fixed. Do not propose anything you cannot tie to a line in the output.
```

If it can't tie an edit to a line, the edit doesn't get tested. That's my version of the paper's rule, every edit has to be justified by the run, and it's the one that keeps the file honest.

### 3. Run

For each proposed edit, open a fresh session, apply that one edit and nothing else, run the exact same task with the exact same prompt, save the output. `loop/edit-1.md`, `loop/edit-2.md`, `loop/edit-3.md`. One edit per run. If you stack them you'll never know which one did the work.

### 4. Compare

This is the step that can't happen inside the session that did the work. Open a new session, with no memory of the task, and hand it the files.

```
Here is a task, the definition of done from my instructions file, and four outputs: a baseline and three variants. For each variant, tell me whether it is better, worse, or the same as the baseline against the definition of done, and quote the lines that decide it. Do not reward length. If the difference is cosmetic, say "same."
```

If you don't have a definition of done in your instructions file yet, that's upgrade three in [the 3 upgrades](three-upgrades.md), and you'll want it before this step, because "better" with no definition is just taste.

### 5. Keep

An edit that won stays in the instructions file. An edit that lost or tied comes out. The file should be the same length or shorter than when you started. If it got longer and nothing won, you added noise.

Google's caveat applies to you too: a small win is inside the noise. If an edit barely wins, run the task once more with it before you keep it. Twice is a result, once is weather.

### 6. Log

One line per edit, whether it won or lost, into the experience file. In this kit that file already exists: it's the task log in [`memory/episodic/`](../memory/episodic/), and `scripts/log-task` writes the entry for you.

```
scripts/log-task --task "loop: <task name>" --approach "edit: <the one line>" --result "won | lost | tied" --lesson "<why, in one sentence>"
```

A lost edit is worth logging. It's the thing you'll be tempted to add again next month.

## Where it goes from here

Google's agent does one more thing, and this kit already has the shape of it. After every task, their system saves the harnesses it kept and the lessons it learned into a library that guides future edits. That's a second level of the loop, where the system also learns which kinds of edits tend to win.

Here, that's [COMPACTION.md](../COMPACTION.md). When the same lesson shows up three times in the task log, it moves up a tier, into a procedure or a hot memory rule. Run `scripts/compact --dry-run` after you've done the loop a few times and it will propose the promotions for you.

So the full picture is:

```
   loop      ──►  task log       one line per edit, won or lost
   task log  ──►  compaction     a lesson that repeats three times moves up
   compaction──►  hot memory     and the next loop starts from a better file
```

Which is why this week the move is to test the rules you already have before you add a single new one.

## Sources

- [Harness²: Recursive Agent Harnessing for an Open World](https://arxiv.org/abs/2610.14224), Google Cloud AI Research, October 2026
- [google-research/harness2](https://github.com/google-research/harness2), Apache-2.0
- The HuggingFace multi-harness result referenced above is covered in [the 3 upgrades](three-upgrades.md)

Built by [@qendresahhoti](https://instagram.com/qendresahhoti), more at [github.com/kju4q](https://github.com/kju4q)
