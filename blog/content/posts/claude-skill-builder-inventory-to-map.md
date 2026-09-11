+++
title = "Skill builder: from an inventory of trees to a map of the forest"
date = 2026-09-11
+++

Every skill I generate with Claude’s skill builder comes back the same shape: exhaustive, flat, and strangely hard to read. Pages of commands with every flag filled in, tables of exact values, a paragraph per edge case. It writes down everything.

That’s an inventory of trees. Complete, and useless for navigation. A map is lossy on purpose — it drops the individual trees so you can see where the river runs.

## TL;DR

<figure>
  <img src="/diagrams/skill-loop.svg" alt="Flowchart of the skill-refinement loop: cut what the agent can derive, then test whether a small model gets through on its own — if not, write in the exact trap and retry; if it does, each reviewer finding is cut, softened or merged unless it defends a hard-won pitfall." style="width:100%;height:auto;border-radius:8px;">
  <figcaption>Cut what the agent can derive, keep what it can only learn by failing — and let a small model tell the two apart.</figcaption>
</figure>

Modern agents like Codex and Claude don’t need detailed instructions anymore. Give a decent model the goal and the constraint, and it composes the command, picks the flags, and adapts when the tool on this machine isn’t the one you had in mind.

So I keep making the same edit by hand. Rip out a working six-flag command, and replace it with the sentence describing what the command has to accomplish.

Also, I’m afraid exact instructions expire quickly, while general directions stay longer, though they still expire.

None of this means a skill should be a page of philosophy. A skill exists to save time, and vagueness has its costs.

The line I’ve landed on: delete detail the agent can derive; keep detail it can only learn by failing.

The agent can derive the flags. The agent can figure out the command. It cannot figure out that the obvious approach works everywhere except the one environment you actually run in, or that the thing you need is behind a name nobody would guess. That knowledge has no source but somebody having already lost an afternoon to it.

So three kinds of specifics stay, however sharp they look:

* Which tools to reach for, when the obvious choice is the wrong one.
* Know-how that isn’t in any documentation, because it only shows up on a real run.
* Pitfalls, stated as pitfalls: the thing that looks like it should work, and what actually happens.

The economics decide it. The skill I was writing had a half-hour iteration loop — boot, wait, watch it fail, start over. One documented pitfall saves an iteration. A handful of them saves hours.

Okay… Broad strokes, sure — but which strokes, and where does broad turn into useless?

So I stopped guessing and handed the skill to Haiku. If it fails, don’t reason about why; look at what it actually fell into, write that specific trap into the skill, and run it again. Repeat until it gets through.

What this gives you is a measurement instead of an opinion. Everything the small model handles unaided is, by demonstration, derivable — so it can come out, and you can cut it without the nagging feeling that you’ve removed something load-bearing.

It also puts a stop condition on a task that otherwise has none. Editing a skill has no natural end — there’s always another paragraph to polish. “Haiku got through it” is an end.

Another thing that helped: hand the skill to a meticulous AI reviewer and let it complain.

Not because the complaints are right. The reviewer is as bad at the big picture as the builder is — it will not tell you the skill has no thesis, or that the whole middle section is at the wrong altitude. What it does reliably is snag on sharp detail. An over-specific claim, a hedge that contradicts itself, a number that can’t be verified, a sentence doing three jobs. It finds the splinters.

The trap is answering a splinter with precision. The reviewer says this claim is unsupported, so you add the supporting detail, and now the file is longer and more brittle, and you’ve built more inventory in response to a complaint about inventory.

So the rule I settled on: every finding is a deletion candidate first. Cut the sentence, soften it, or fold it into the one above — and only if none of that works, fix it as asked. Most of the time the complaint evaporates along with the sentence, because a claim vague enough to be obviously true is a claim nobody can call unsupported.

One exception, and it’s the same one as before: don’t sand off a pitfall. When the reviewer objects to a hard-won specific, that objection is wrong — it’s reading a sharp edge as a defect, and that edge is the part somebody paid for.

Otherwise, the loop is simple. The reviewer finds the rough edges, and you take them off with sandpaper rather than filling them in with more wood.
