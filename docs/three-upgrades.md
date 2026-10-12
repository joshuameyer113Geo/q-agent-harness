# 3 harness upgrades before you switch models

If you've been using Claude Code, Codex, or any AI tool and some days it feels brilliant while other days it feels like it forgot who you are, I want to show you something before you blame the model or go looking for a better one.

The model is only half of what you're using. The other half is the harness, which is just the word for everything wrapped around the model: the instructions it reads before working, the context it can see, the rules it follows. The tool, basically. And this part matters more than most people think. HuggingFace recently ran the same model, with the same weights, through four different coding tools, and it solved 62 percent of its tasks in one and 33 percent in another. Nothing about the model changed. Only the harness did.

Here's the part I love about that: your model is the same one everyone else has. Your harness is yours. You can't retrain the model, but you can upgrade everything around it, today, for free. These are the three upgrades I'd start with.

## 1. Write the instructions file

Every tool has a file it reads before it starts working for you: in Claude Code it's called CLAUDE.md, in Codex it's AGENTS.md. Think of it as onboarding a new teammate. If the file is empty, your AI shows up to work every single day with no idea who you are, what you're building, or how you like things done, and you re-explain yourself forever.

So write it like you'd brief a person. What is this project and what does it run on. What should never be touched. What does "done" mean to you. How do you like things named and written. Keep it to one page, because a bloated file is just noise, and the whole point of a harness is less noise.

If you've never done this, start with five sentences. You'll feel the difference in the first session.

Want a starting point? Fill in [the hot memory template](../memory/hot/HOT-MEMORY-template.md). In Claude Code, it becomes your CLAUDE.md.

## 2. Hand it what it actually needs

Your AI can only work with what it can see, and most frustrating sessions are really visibility problems. "Fix the auth bug" gives it nothing. The failing test, the file where auth lives, the actual error pasted in, that gives it everything. When I want something written in my style, I don't describe my style, I point at a file that already does it right and say "like this."

This sounds obvious and almost nobody does it consistently. It's the closest thing to the 62 versus 33 gap you can create in your own session, in either direction.

For what to hand it on specific jobs like debugging, research, and decisions, see [task setups](task-setups.md).

## 3. Give it a definition of done

This is the upgrade almost nobody makes, and it's my favorite. Somewhere in that instructions file, tell it what finished means, so it checks its own work before handing it to you: run the tests before saying it's done, no new dependencies without telling me, match the style that's already in the file, and when you're not sure, stop and ask instead of inventing.

Once this exists, you stop being the only quality check in the room. That's the moment it starts feeling like a teammate instead of a tool.

Want to go past a checklist? [VERIFICATION.md](../VERIFICATION.md) walks through four ways a harness checks its own work, from weakest to strongest.

That's the real lesson in the HuggingFace research too: the model that practiced inside real tools beat the one that copied answers from a smarter model. Practice beats copying, for models and for us. A weekend spent upgrading your harness will change more than a weekend spent comparing models.

Built by [@qendresahhoti](https://instagram.com/qendresahhoti), more at [github.com/kju4q](https://github.com/kju4q)
