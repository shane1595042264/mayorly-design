# todoFarm: product spec

**Status:** design, pre-implementation. Last updated 2026-09-04.

## One line

A todo list you walk around in. You dump a task, a silent classifier files it, and a craftsman turns what you finish into things that decorate your farm.

## Why this is not another gamified todo app

Research across 146 shipping products found the market splits into three clusters that never touch: cozy pixel productivity apps with **zero AI**, LLM desktop characters with **no task management**, and LLM task triage with **no character and no aesthetic**. Full findings in [research/prior-art.md](research/prior-art.md).

Two things nobody ships:

1. **Paying real coins, earned from real work, to unlock your own leisure.** Not found in any of the 146 products. This is a self-control mechanism, not a decoration, and it cannot be cloned by bolting a chatbot onto Todoist.
2. **A completion path that produces art.** Every cozy productivity app surveyed is timer-fed. Crops and furniture here are task-fed, and the farm becomes a record of what you actually did.

## The loop

```
capture  ->  classify  ->  work  ->  reward  ->  spend
 (you)      (silent AI)   (furnace)  (craftsman)  (store / leisure)
```

### 1. Capture

Global hotkey from anywhere in macOS. One text field, no category picker, no date picker, no prompts. Type, hit return, it is gone.

Verified during research: a modifier-combo global hotkey needs **no macOS Accessibility permission**, so capture is promptless. Media keys would trigger the prompt; do not use one.

### 2. Classify, silently

The AI never speaks. No dialogue, no clarifying questions, no approval step. It files and it tags, and you correct it by dragging.

**Chest rules**

- **Hard cap of 9 chests.** At 9 the classifier stops creating and starts fitting to the closest existing chest.
- The classifier **may rename and merge its own chests** to free a slot.
- **User-made chests are locked.** It can file into them. It can never rename, merge, or delete them.
- Every chest carries a written **inclusion criterion**, not just a name. Two chests with names but no criteria are indistinguishable to a classifier; two with criteria are separable.
- A chest needs **4 or more items before it is born**. Until then items sit unfiled. This is also good game design, since a chest appearing should feel earned.

**Two-cadence architecture.** Letting the online classifier create chests is the single mechanism that produces forty near-duplicate chests. So it is split:

| | runs | model | may create chests |
|---|---|---|---|
| **Induction** | weekly, or when unfiled crosses a threshold | larger | yes, proposes |
| **Assignment** | per item, batched | small and cheap | **no** |

**Entertainment tagging.** Every item is tagged entertainment or not. The test is whether it provides productive value. Exercise, studying, and work are not entertainment. Playing Minecraft is.

**The ledger.** Because the AI is mute it needs a channel that is not speech. A workshop ledger records every action it took, in machine-log voice: filed, tagged, built, merged, renamed, left alone, no room. This is the only place it reports.

### 3. Work: the furnace

- **One timer, ever.** The furnace is the only place work happens.
- Start by dragging an item into it. The craftsman locks in and begins.
- Interval customisable, 25 minutes default. Each completed interval is one tomato.
- **Rack it** at any time to pause and switch. Banked tomatoes are kept.

### 4. Reward: two completion paths

This is the core distinction and it is what makes the furnace worth using.

| path | how | yields |
|---|---|---|
| **Tick it done** | check it off in the chest, no timer | a **crop** |
| **Burn it in the furnace** | pomodoro session, then complete | **furniture**, forged by the craftsman |

Crops are cheap and plentiful. Furniture is the good stuff and only the furnace produces it.

Each NPC owns a source. This decides which art gets commissioned next.

| NPC | produces | ships |
|---|---|---|
| craftsman | furniture and fittings | v1 |
| (none) | crops, from ticked items | v1 |
| fisherman | fish, tackle, pond dressing | later |
| treasure hunter | watches, jewellery, curios | later |

### 5. Spend: coins

Coins are earned **only in the furnace**. Ticking a box without burning time pays nothing, so the timer cannot be skipped.

- **12 coins per tomato**, plus **50% bonus at 3 tomatoes or more**. Long grinds beat scattered ones.
- **Leisure costs 1 coin per minute.** 30 minutes of Minecraft is 30 coins, roughly two and a half tomatoes of real work.

Two sinks:

1. **The store.** Decorations, all hand-drawn and licensed, never generated. Crops and forged furniture cannot be bought, only earned, so a farm cannot be faked with coins.
2. **Unlocking your own entertainment items.** They sit visible but locked in their chest until paid for.

### 6. Leisure

An entertainment item goes into the same furnace panel in a different mode. The craftsman sits on the couch with chips and the TV on, the timer counts down the time you paid for, and the progress bar turns from ember to purple. Nothing is earned in there, and that is the point.

## The world

- Player walks with arrow keys or WASD. Real tile collision.
- **Chests do not live on the farm.** The farm is purely the reward space: crops, furniture, decorations, and the things you earned. Chests live in their own space.
- The workshop holds the furnace. The store is a stall.
- A keyboard shortcut always works without walking, so a three-second capture never costs twenty seconds of walking. The game is the texture, not a tax.

## What this product refuses to do

Research found a graveyard of abandoned gamified todo apps and one large controlled study on companion-AI manipulation. These are hard invariants, not preferences. Evidence in [research/npc-mood-and-motivation.md](research/npc-mood-and-motivation.md).

- **No streaks that break. No wilting. No dying pet.** Nothing decays because you had a bad week.
- **Unfinished work goes back in the chest.** That is the entire consequence.
- **No emotional neglect framing, ever.** "I exist solely for you, please do not leave" measurably increases churn, negative word of mouth, and perceived legal liability.
- **No guilt as a mechanic.** The NPC's state never tracks your worth or your output.
- **An explicit pause exists**, built on purpose rather than discovered as necessary later.

Growth replaced mood and relationship entirely. The craftsman does not have feelings about your performance. He makes things out of what you finish.

## Open questions

1. **Classifier host.** Small local model (free, offline, no backend, nothing leaves the Mac) versus a hosted flash-class model (no download, needs a key and a thin proxy). Written as a swappable interface so this is not blocking.
2. **Wrong entertainment tags have no recovery path.** With the AI mute and no approval step, a bad tag silently charges coins for something useful. Cheapest fix is flipping the tag from the item row.
3. Whether chests get their own building interior or their own screen.
