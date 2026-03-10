---
layout: post
title: My morning brief — how I start every day already knowing what matters
permalink: /morning-brief
---

At 7 AM every morning, a message arrives on my Telegram.

Today's meetings. Upcoming calls. Any travel. Birthdays. What's on my plate this week. Anything I need to know before I start.

I did not open Google Calendar to build that digest. I did not open my task manager. I did not open my email. The message arrived because I built a system that delivers it — once, about a year ago — and it has run every morning since.

By the time I'm having my first coffee, I already know the shape of my day.

---

## Why a morning brief matters

The first twenty minutes of the day are disproportionately important. What you do with them sets the operating mode for everything that follows. If you start the day reacting — opening email, scanning notifications, trying to piece together what needs to happen — you are already behind. The day is running you.

A brief changes that. It means you arrive at your first task with context already loaded. You know what's happening. You know what matters. You are choosing how to engage rather than being pulled by whatever is loudest.

The brief is not a productivity trick. It is an orientation tool. It answers the question "what does today require of me?" before the day starts asking it in less orderly ways.

---

## What my brief contains

The format has evolved over time but the core sections have stayed consistent:

**Today's schedule** — every meeting and call, with times. No detail about what each is, just that it exists and when.

**This week's overview** — a scan of the next 5 days. Anything coming up that I need to be thinking about.

**Travel** — if there's a flight or overnight anywhere in the next 72 hours, it appears here.

**Flagged items** — anything I've explicitly marked as needing attention that hasn't been addressed yet.

That's it. Four sections. Reads in under a minute.

---

## How it works

Claude Code runs a skill every morning at a scheduled time. The skill connects to Google Calendar, reads the relevant events across the defined window, formats them into the digest structure, and sends the message to Telegram.

No app to open. No input from me. The system connects to the calendar, does the reading, formats the output, and delivers it.

The setup took a few hours the first time — writing the skill file, connecting the calendar access, getting the Telegram integration working, testing the output format until it felt right. Since then, it has run without any maintenance.

---

## The part that surprised me

I expected to save time. I did — the few minutes I used to spend piecing together my day are gone.

What I did not expect was the quality of attention shift. When the brief arrives and I have read it, the background processing stops. The vague awareness of "I should check what's on today" disappears. My mind is not running a low-level scan for things I might be forgetting. It is just present.

That is worth more than the time.

---

## Building your own

A morning brief is one of the highest-return first automations to build with Claude Code, precisely because it is daily and the benefit compounds.

Start simple. Even a basic version — just today's calendar events, delivered to wherever you start your morning — is better than nothing. You can add sections over time as you learn what you actually want to know before the day starts.

The goal is to answer, before anything else asks for your attention: what does today require?

<nav style="display: flex; flex-direction: column; gap: 5px; margin-top: 10px; padding-top: 20px; border-top: 1px solid #eee;">
  <div>
    <a href="/claude-code-vs-chatgpt" style="text-decoration: none; color: #0366d6;">← Previous: Claude Code vs ChatGPT for business</a>
  </div>
  <div>
    <a href="/ea-briefings" style="text-decoration: none; color: #0366d6;">Next: How I automated my EA briefings →</a>
  </div>
  <div>
    <a href="/notes" style="text-decoration: none; color: #0366d6;">Notes</a>
  </div>
</nav>
