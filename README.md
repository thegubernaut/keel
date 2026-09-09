# Gubernaut Keel

**Gubernaut Keel stops your AI agent from getting stuck in an expensive loop, running
directly inside your own JavaScript or TypeScript program.**

It installs as the package `@gubernaut/core`. This repository is a full copy of the
Gubernaut project's code, with this page written to explain Keel in plain language. The
original, main copy of this project lives at
[github.com/thegubernaut/gubernaut](https://github.com/thegubernaut/gubernaut).

---

## What problem does this solve?

Sometimes an AI agent gets stuck. It tries the same thing over and over, and every single
try costs you money, because you pay for every message sent to the AI model. If nobody is
watching, this can burn through a lot of money very fast, before a person even notices.

Keel is the same decision-making engine as Tiller (Gubernaut's Python proxy), but built to
run **directly inside your own JavaScript or TypeScript program**, with no separate program
to start and no network connection needed. You feed it three numbers, and it tells you what
to do next.

## How is Keel different from Tiller?

Gubernaut ships as two products, both making the exact same decision, in two different
places:

| | Gubernaut Tiller | Gubernaut Keel |
| --- | --- | --- |
| Where it runs | A separate small program on your computer | Directly inside your own code |
| What it needs | Sits in front of your network calls to the AI | Nothing extra. No network hop, no separate program |
| What it does | Watches AND stops the call automatically | Tells you what to do. Your own code has to act on it |
| Best for | Python projects, or "just make it work" | JavaScript/TypeScript projects that want full control |

**Keel decides. It does not act on its own.** When Keel tells you the agent is stuck, it is
your own code's job to actually stop the call. This is the one thing Keel deliberately
leaves to you, in exchange for running with no extra moving parts.

## How does it work, in plain words?

Every time your AI agent takes a turn, you give Keel three simple numbers:

1. **How intense** the turn feels.
2. **How positive or negative** the turn feels.
3. **How much this turn repeats** the last one.

Keel never reads your actual words. It only ever looks at these three numbers. That matters,
because it means nothing written in the conversation can trick Keel into ignoring a problem:
it isn't reading text at all, so there is nothing in the text to fool it.

Based on those three numbers, Keel decides one of three things:

| What Keel decides | What your code should do |
| --- | --- |
| Everything is fine | Nothing. Let the turn continue as normal. |
| Things are getting worse | Add a gentle nudge asking the AI to calm down. |
| The agent is stuck in a loop | Stop the call yourself, before it is ever sent to the AI model. |

Keel is a pure decision maker: the same numbers always produce the same answer, every time,
with no guessing and no randomness involved.

## Install it, step by step

**Step 1. Install the package.**

```bash
npm install @gubernaut/core
```

**Step 2. Create the decision-maker, once, in your program.**

```js
import { Governor } from "@gubernaut/core";

const gov = await Governor.create();
```

**Step 3. Ask it what to do, every turn.**

```js
for (const turn of conversation) {
  const { posture } = gov.tick({
    intensity: turn.intensity,     // a number from 0 to 1
    valence: turn.valence,         // a number from -1 to 1, negative means hostile or upset
    repetition: turn.repetition,   // a number from 0 to 1, how much this repeats the last turn
  });

  if (posture === "REGROUND") break;   // Keel says the loop is stuck. Your code stops it here.
}
```

That's it. No separate program to run, no network connection, and it works the same way in
a browser, in Node, or on an edge server like Cloudflare Workers.

## Does this actually save money? Yes, and here is the honest number

Keel runs the exact same decision-making engine that Tiller runs, so it produces the exact
same results. We tested it against real AI models, on purpose, in a situation designed to
make the AI get stuck in a loop, and compared the cost with the decision-maker on versus
off.

**Across every test we ran, following the decision saved somewhere between 79.8% and 95.9%
of the money that would otherwise have been wasted.** The best single result: a test that
would have cost $0.1669 cost only $0.0068 when the loop was stopped in time, a 95.9% saving.
That is the best result we saw, not an average, and we always show you the worst result too
so nothing is hidden.

Every number here comes from a real, repeatable test. Anyone can run the same test
themselves and check our math. The full, detailed record, including every test we ran, is
in this same repository under [`receipts/`](receipts/), and also at
[gubernaut.com/research](https://gubernaut.com/research).

## What Keel does not do

Being honest about the edges of what something does is just as important as explaining what
it does.

- Keel does not read or judge what your AI actually says. It only looks at the three
  numbers described above.
- Keel does not stop anything by itself. It tells you what it decided; acting on that
  decision is your code's job.
- Keel cannot promise it will catch every possible bad situation. It has been tested a lot,
  and we show you exactly what it catches and what it misses, in this repository's
  [`docs/LIMITS.md`](docs/LIMITS.md).
- Keel is not a mind and does not think or feel anything. It is a simple, predictable set of
  rules, the same way a thermostat is a simple, predictable set of rules for a room's
  temperature.

## Try it yourself

Everything Keel does is designed so a stranger, with no special access, can check it.

```bash
git clone https://github.com/thegubernaut/gubernaut.git
cd gubernaut/packages/core-js
npm ci
npm test
```

If your results disagree with ours, that is more useful to us than if they match. Please
tell us: [open an issue](https://github.com/thegubernaut/gubernaut/issues/new?template=reproduction.yml).

## Cost, license, and who is behind this

Keel is completely **free** and licensed under **Apache-2.0**. There is no paid tier, no
account, and nothing is collected about you while it runs.

If you use Gubernaut in your own work, please cite it:

> Gubernaut Research. *Gubernaut Cognitive Controller (GCC).* Zenodo.
> https://doi.org/10.5281/zenodo.21303518

No consciousness or mind claims are made anywhere in this project. This is a regulation
layer: it watches a small number of signals and responds to them, in a way that can be
measured and checked. Byline: Gubernaut Research.

**Full technical documentation:** [github.com/thegubernaut/gubernaut](https://github.com/thegubernaut/gubernaut) ·
**Website:** [gubernaut.com](https://gubernaut.com)
