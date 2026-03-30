# PromptArchitect Notebook 01 — Video script (~4–5 min)

**Audience:** Developers curious about structured prompts. **Tone:** Playful, plain English, staccato. **On camera:** Look at the lens in the hook; glance at screen for demos.

---

## Why this structure (short-form tutorial research)

- **First 2–3 seconds:** Promise a payoff (before/after, time saved, “one line”). Swipe decisions are brutal on Shorts/Reels.
- **Result-first:** Show fat output *before* you explain nine sections—curiosity carries people through theory.
- **One idea per beat:** Cut fluff. Text on screen = 3–5 words max when you speak.
- **Direct “you”:** “You type this. You get that.” Feels like a friend, not a lecture.
- **Pattern interrupt:** Jump from tiny input to huge prompt—visual contrast = retention.
- **End with one next step:** Notebook 02, docs, or “try your own task string.”

---

## Slide deck copy (paste into PowerPoint / Google Slides)

Each block = one slide. Title + bullets.

**Slide 1 — Title**  
One sentence in. Production prompt out.  
PromptArchitect · mycontext-ai · Notebook 01

**Slide 2 — Hook**  
You have one line of English.  
You need a *real* system prompt.  
Not vibes. Structure.

**Slide 3 — What it is**  
PromptArchitect = auto prompt engineer.  
`build(task)` → 9 sections.  
Research-backed order. One API call.

**Slide 4 — The magic line**  
`PromptArchitect(provider="openai")`  
`result = architect.build(task)`  
`prompt = result.improved_context.assemble()`

**Slide 5 — Before / after**  
In: ~30 characters.  
Out: ~1 500 tokens of structure.  
Same task. Wildly different usefulness.

**Slide 6 — Nine sections (memory trick)**  
Top: who + mission.  
Middle: rules, style, examples.  
Bottom: format, guard rails, thinking, **task last**.

**Slide 7 — Why order matters**  
LLMs are picky about *where* words sit.  
Early = anchor. Late = “do this now.”  
We don’t guess. We follow the research.

**Slide 8 — Thinking strategies**  
Five atomic slugs.  
Chain-of-thought vibes. Tree-of-thought vibes. Self-check vibes.  
Pick one. Or pick a **combo** (archetype).

**Slide 9 — Archetypes**  
`deliberative` = angles + steps + verify.  
`build(..., reasoning_strategies=["deliberative"])`  
You steer the brain of the prompt.

**Slide 10 — Same brain, different outfit**  
OpenAI. Anthropic. Gemini.  
Same Context. Different `assemble`.  
Meet the model where it lives.

**Slide 11 — Recap**  
One call. Nine sections.  
Strategies optional. Providers flexible.  
Notebook 02 = improve an old prompt.

**Slide 12 — CTA**  
Open the notebook.  
Set your API key.  
Change the task string. Break nothing.

---

## Full transcript (read aloud; ~4:30 at easy pace)

**0:00–0:12 — Hook + lens**  
Hey. You.  
One sentence. That’s all you have.  
You need a production prompt. Role. Rules. Format. The works.  
I’m going to show you how we cheat—nicely—with PromptArchitect.

**0:12–0:35 — What it is**  
PromptArchitect lives inside **mycontext-ai**.  
Think: junior prompt engineer in a box.  
You hand it plain English. It hands back a **nine-part** system.  
Not random paragraphs. A **pattern**. Research baked in.  
You’re not memorizing frameworks. You’re calling **one** function.

**0:35–1:05 — Setup (over screen)**  
Quick boring bit—then fireworks.  
Install the package. Load your key.  
We print the version so you know you’re not on yesterday’s bug.  
If your kernel screams, swap to the project venv. You’ve got this.

**1:05–1:45 — Part 1: the demo**  
Watch the screen.  
Task: “Analyze customer support tickets.” That’s it.  
We do `PromptArchitect`. We do `build`. We do `assemble`.  
Boom. Wall of text.  
Count the words. Smile.  
**Input tiny. Output huge. On purpose.**

**1:45–2:25 — Nine sections, human words**  
Okay—why isn’t this just a blob?  
**Order matters.** Models read like humans with a highlight pen.  
Top: who you are. What winning looks like.  
Middle: rules, tone, a few examples so it *gets* you.  
Late: output shape. Guard rails—what *not* to do.  
Thinking strategy sits **right before** your final task—so it doesn’t get lost in a long prompt.  
Last line? **Your task.** Recency. “Do this now.”

**2:25–2:55 — Strategies (playful)**  
We ship five atomic brain-modes. Step by step. Multiple angles. Verify. Explain simply. Get weird.  
They’re named boring on purpose—so your **code** stays boring and your **model** behaves.

**2:55–3:25 — Archetypes**  
Don’t want to mix yourself? Grab an archetype.  
“Deliberative” is my drama queen: explore options, walk steps, then doubt itself and fix.  
In code: `reasoning_strategies=["deliberative"]`.  
You’re not typing the essay. The architect **injects** the essay *into the right slot*.

**3:25–3:55 — Quality + provider**  
There’s a score bump baked in—we’re not chasing vanity numbers today.  
Trust: empty prompt in, structured prompt out, metrics move.  
Same object, different **provider** skin—OpenAI vs Anthropic vs Gemini.  
One brain. Three handshakes.

**3:55–4:20 — Linguistic rules (one breath)**  
Under the hood, a bunch of tiny rules nudge wording: must, never, testable constraints.  
You don’t see the rulebook. You see the result. That’s the point.

**4:20–4:45 — Close**  
Recap: **PromptArchitect**. **`build`**. **Nine sections**. **Optional strategies**. **Provider-aware assemble**.  
This notebook is **01**—auto-build from scratch.  
**02** is “I already have a mess—fix it.”  
Go break your own task string. Tag me when it’s weird. I want weird.

---

## B-roll / screen cues (editor notes)

| Time | Show on screen |
|------|------------------|
| 0:20 | Logo or notebook title cell |
| 0:50 | `%pip` + version line |
| 1:10 | Highlight `task = "..."` |
| 1:20 | Scroll `assemble()` output (fast, then slow) |
| 1:50 | Assembly table / SECTION_NAMES |
| 2:30 | `THINKING_STRATEGIES` loop or list |
| 3:00 | `reasoning_strategies=["deliberative"]` line |
| 3:35 | `assemble_for_model` or provider loop |
| 4:10 | Summary table in notebook |

---

## Optional on-screen text overlays (Shorts-style)

- “1 line → 9 sections”
- “Order = science”
- “Task always last”
- “deliberative = think hard”
- “Notebook 02 next”
