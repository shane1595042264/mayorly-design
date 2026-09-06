# Mayorly: product spec

**Status:** design settled, implementation starting. Last updated 2026-09-05. Supersedes the farm-and-chests spec of 2026-09-04; see [04-decisions.md](04-decisions.md).

## One line

A life OS you walk around in. You are the mayor of a small town. Every task is a journal entry; a silent clerk files it into the right ledger; time at your desk earns tokens; tokens buy your leisure, your decorations, your next room, and eventually gate what you let yourself spend in real life.

## Why the lore changed

The first design was a farm with chests, and a task was an item in a chest. It worked mechanically and it was not immersive. A chest full of "email the dentist" is a spreadsheet wearing a costume. A **journal** is what a person who runs a town would actually keep, and a **ledger** is what their clerk would actually file things into. The nouns now match the fiction.

Nothing mechanical changed. The cap of nine, the locked user-made categories, the mute classifier, the one-timer rule, the token rates, the leisure gate: all identical. Only the world around them moved indoors.

## Why this is not another gamified todo app

Research across 146 shipping products found the market splits into three clusters that never touch: cozy pixel productivity with **zero AI**, LLM desktop characters with **no task management**, and LLM task triage with **no character and no aesthetic**. Full findings in [research/prior-art.md](research/prior-art.md).

Two things nobody ships:

1. **Paying tokens, earned from real work, to unlock your own leisure.** Not found in any of the 146. A self-control mechanism, not a decoration.
2. **A token economy that reaches outside the app.** The long-term thesis (below) is that the same tokens gate discretionary real-world spending. No productivity product does this. It is the reason this is a life OS and not a todo list.

## The world

**You are the mayor.** Not a farmer, not an adventurer. Someone whose job is to keep a town running, which is what a life is.

**You start in one room: the Mayor's Hall.** An office. In it:

| thing | what it is in the fiction | what it is in the app |
|---|---|---|
| the **mail tray** | letters arriving on the desk corner | the inbox, where captured entries land unfiled |
| the **clerk** | a mute records-keeper who files your post | the classifier, embodied and silent |
| the **ledger shelf** | nine ledgers on a bookshelf | the categories, hard cap nine |
| a **hand-written ledger** | one you wrote yourself, with a clasp | a user-made category the clerk may never touch |
| the **desk** | where the mayor sits down to work | the one timer, the only place tokens are earned |
| the **couch** | where the mayor puts their feet up | where paid-for leisure is consumed |
| the **door** | leads to rooms the town does not have yet | purchasable content |

**Rooms are content, and content is bought with tokens.** A farm next door. A kitchen. A tavern. Each is a room we build first, with its own art in the assets repo and its own entry in the manifest, and the player buys it. Nothing is generated at runtime, ever; if it is not in the manifest, it does not exist. This is why the asset pipeline came before the game.

The player walks with arrow keys or WASD, with real tile collision. A keyboard shortcut always works without walking, so a three-second capture never costs twenty seconds of walking. The game is the texture, not a tax.

## The loop

```
capture  ->  file  ->  work  ->  tokens  ->  spend
 (you)     (clerk)    (desk)              (leisure / store / rooms)
```

### 1. Capture

Global hotkey from anywhere in macOS. One text field, no category picker, no date picker, no prompts. Type, hit return, it lands in the mail tray.

A modifier-combo global hotkey needs **no macOS Accessibility permission**, so capture is promptless. Media keys would trigger the prompt; do not use one.

### 2. File, silently

The clerk never speaks. No dialogue, no clarifying questions, no approval step. It files and it tags, and you correct it by dragging an entry to a different ledger.

**Ledger rules**

- **Hard cap of 9 ledgers.** At 9 the clerk stops creating and starts fitting to the closest existing ledger.
- The clerk **may rename and merge its own ledgers** to free a slot.
- **Hand-written ledgers are locked.** The clerk can file into them. It can never rename, merge, or delete them.
- Every ledger carries a written **inclusion criterion**, not just a name. Two ledgers with names but no criteria are indistinguishable to a classifier; two with criteria are separable.
- A ledger needs **4 or more entries before it is bound**. Until then entries sit in the tray. A ledger appearing on the shelf should feel earned.

**Two-cadence architecture.** Letting the online classifier create ledgers is the single mechanism that produces forty near-duplicate ledgers. So it is split:

| | runs | model | may create ledgers |
|---|---|---|---|
| **Induction** | weekly, or when the tray crosses a threshold | larger | yes, proposes |
| **Assignment** | per entry, batched | small and cheap | **no** |

**Entertainment tagging.** Every entry is tagged entertainment or not. The test is whether it provides productive value. Exercise, studying, and work are not entertainment. Playing Minecraft is.

**The clerk's log.** Because the clerk is mute it needs a channel that is not speech. A log records every action it took, in machine-log voice: filed, tagged, bound, merged, renamed, left alone, no room. This is the only place it reports.

### 3. Work: the desk

- **One timer, ever.** The desk is the only place work happens.
- Sit down with an entry to start. Interval customisable, 25 minutes default. Each completed interval is one tomato.
- **Get up** at any time to pause and switch. Banked tomatoes are kept.
- Completing an entry at the desk stamps it. The stamp is the only completion animation; there is no crop and no forged furniture any more.

### 4. Tokens

Tokens are earned **only at the desk**. Ticking an entry done without desk time pays nothing, so the timer cannot be skipped. This rule was set in the farm design and is deliberately kept: it is the anti-cheat that makes the economy mean something.

- **12 tokens per tomato**, plus **50% bonus at 3 tomatoes or more**. Long grinds beat scattered ones.
- **Leisure costs 1 token per minute.** 30 minutes of Minecraft is 30 tokens, roughly two and a half tomatoes of real work.

### 5. Spend

Three sinks in v1, one later.

1. **Leisure.** Entertainment entries sit visible but locked in their ledger until paid for. Then they are consumed on the couch: the timer counts down the time you bought, the bar turns purple, and nothing is earned. That is the point.
2. **The store.** Decorations for the hall, all hand-drawn and licensed, never generated.
3. **Rooms.** The door in the hall leads to rooms you can buy. Each is real content built in advance.
4. **Later: real life.** See the thesis.

## The thesis: a life OS

The tokens are a budget for the part of life that a bank balance does not govern. Everyone checks whether they *can* afford something. Almost nobody has a system for whether they *should*, when the thing is discretionary: a game, a gadget, a night out. The intended end state is that a non-necessary real-world purchase costs tokens as well as money, with a record of what was spent on what. The bank account answers "can I"; the ledger answers "have I earned it".

This is **not in v1**. V1 is the Mayor's Hall, the journal, the clerk, the desk, and the three in-app sinks. The thesis is written down here so that every v1 decision is made with it in view, and so nobody redesigns the token economy later without knowing where it is meant to go.

## How this ships

- **Open source first.** Monetisation, if any, comes after traction. Nothing in v1 is gated behind payment.
- **Built for one user first.** The founder uses it daily and polishes until he likes it. Every placeholder is eventually replaced by his own art; every room is designed by him.
- **The asset pipeline is the content pipeline.** Rooms, decorations, and characters all enter the game the same way: a slot, a brief, a hand-drawn PNG that passes the validator, a manifest entry. See the [assets repo](https://github.com/juntaoli-dev/mayorly-assets).

## What this product refuses to do

Research found a graveyard of abandoned gamified todo apps and one large controlled study on companion-AI manipulation. These are hard invariants. Evidence in [research/npc-mood-and-motivation.md](research/npc-mood-and-motivation.md).

- **No streaks that break. No wilting. No dying pet.** Nothing decays because you had a bad week.
- **Unfinished entries go back in their ledger.** That is the entire consequence.
- **No emotional neglect framing, ever.** The clerk does not have feelings about your output.
- **No guilt as a mechanic.**
- **An explicit pause exists**, built on purpose.

## A door for mods

Mayorly is standalone and will stay standalone. But the data is the user's, so the app will expose a **local sync API**: read the journal, ledgers and token balance, and post completions. Anyone can build a Stardew Valley mod, a phone widget, or a terminal client against it for free. This is how the Stardew crowd gets Mayorly inside their game without Mayorly becoming a mod. Not v1; designed for from v1 so nothing in the data model assumes a single client.

## Open questions

1. **Classifier host.** Small local model versus a hosted flash-class model. Written as a swappable interface, not blocking.
2. **Wrong entertainment tags have no recovery path.** With the clerk mute and no approval step, a bad tag silently charges tokens for something useful. Cheapest fix is flipping the tag from the entry itself.
3. **Whether ticking an entry done should pay a small base.** The farm design said no, tokens come only from desk time, and this spec keeps that. Reverse it if the desk turns out to be too much friction for two-minute tasks.
4. **What the first buyable room is.** The farm is drawn first because its tiles already exist, but a kitchen or tavern may be the better second room. Decide when the hall is playable.
