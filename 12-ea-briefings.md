---
layout: post
title: How I automated my EA briefings with Claude Code
permalink: /ea-briefings
---

I have an EA. She handles travel bookings, logistics, payments, coordination — the operational layer that keeps things moving. She is good at her job. The bottleneck was me.

Not because I was slow. But because there was always a step in the middle where I had to translate a need into a briefing and get it to her. See a flight that needs booking. Note it somewhere. Remember to message her. Compose the message. Send it. Wait for confirmation.

Each of those steps is small. Together they add up to a surprising amount of friction — and more importantly, a surprising number of things that fell through the gaps because the middle steps didn't happen at the right time.

I automated the middle.

---

## What the automation does

When travel gets added to my calendar — a flight, a hotel, any overnight — Claude Code picks it up. It reads the event details: destination, dates, relevant preferences (aisle seat, specific airlines, budget parameters). It composes a briefing message in the format my EA expects. It sends that message to her on WhatsApp.

She gets a message that says, in effect: there's a trip coming, here are the details, here's what I need. She books it. I get confirmation.

The trigger was adding an event to a calendar — something I was already doing. The briefing — which I used to have to compose and send separately — now happens automatically as a consequence.

---

## Why this matters beyond the time saved

The EA briefing automation solved a class of problem I didn't fully recognise until it was gone.

The problem was the gap between knowing something needed to happen and acting on it. I would see a flight needed booking. I would note it mentally. I would intend to message her. Sometimes that intention would translate into action immediately. Sometimes it would sit for a day. Sometimes, in a busy week, it would slip through entirely and I'd be booking a last-minute flight at twice the price.

The automation eliminates the gap. The moment a trip appears on the calendar, the briefing goes out. There is no intermediate step where I have to remember, intend, and act. The knowledge that something needs to happen and the action of doing it are the same event.

That is a structural change, not an efficiency improvement.

---

## What you need to build this

The components are straightforward:

**A calendar integration** — Claude Code needs to be able to read calendar events. Google Calendar works well via standard APIs.

**A messaging integration** — the briefing needs to go somewhere your EA actually monitors. WhatsApp works; so does Telegram, email, or Slack depending on what your EA uses.

**A skill file** — a set of instructions that tells Claude Code what to look for in the calendar, what to include in the briefing, and where to send it.

**A clear briefing format** — the single best thing you can do before building this is write out, once, what a perfect EA briefing looks like for your context. What information does she need? In what order? What preferences and constraints should always be included? That document becomes the template the automation follows.

The hardest part is usually the last one — not the technical setup, but the clarity about what good looks like.

---

## The broader pattern

The EA briefing is one instance of a pattern that shows up everywhere in business operations: there is a trigger event, there is an action that should follow, and there is a person who currently has to notice the trigger and execute the action manually.

Automating that middle — so the trigger and the action are directly connected — is available for far more of your operations than most people realise.

The question to ask about any recurring task: what has to happen before I do this? If the answer is "I notice something" — that noticing can be automated.

<nav style="display: flex; flex-direction: column; gap: 5px; margin-top: 10px; padding-top: 20px; border-top: 1px solid #eee;">
  <div>
    <a href="/morning-brief" style="text-decoration: none; color: #0366d6;">← Previous: My morning brief</a>
  </div>
  <div>
    <a href="/notes" style="text-decoration: none; color: #0366d6;">Notes</a>
  </div>
</nav>
