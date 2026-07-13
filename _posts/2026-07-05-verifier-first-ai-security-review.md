---
layout: post
title: "Finding Is the Easy Part"
date: 2026-07-05
filename: VERIFY.TXT
dek: "I've run ~50 AI audits. It found thousands of \"bugs.\" Almost none were worth reporting, and that gap turned out to be the whole job. The intro to a series on what I learned."
cats: [SECURITY, AI, EVAL]
status: PUBLISHED
status_note: "the intro to the Verifier-First AI series"
read_time: "5 min"
flag: "[NEW]"
highlights:
  - "Roughly 50 AI-assisted audit runs informed the workflow."
  - "Thousands of plausible candidates narrowed to a handful worth reporting."
  - "Every candidate must separately clear reality, reachability and severity, then reportability."
tldr:
  - "Point an AI at a codebase and it hands you a thousand plausible bugs. Maybe five matter. Finding is the easy part."
  - "Discovery is no longer the bottleneck in AI security. Verification is."
  - "A finding has to clear three separate gates: is it real, is it reachable and severe, would a program actually pay for it."
  - "A real bug with no attacker-reachable path isn't a false positive. It's a non-actionable true finding, and confusing the two rots everything downstream."
  - "Recall first, then precision. A precision stage that can invent a finding is a discovery stage in disguise."
  - "A method, not a tool. It applies anywhere being right matters more than being impressive."
links:
  - { label: "read it on X", url: "https://x.com/i/article/2073859075373441024" }
  - { label: "companion — Horizontal Scaling", url: "/PAPER.md" }
---

*I've run ~50 AI audits. It found thousands of "bugs." Almost none were worth reporting, and that gap turned out to be the whole job. This is the intro to a series on what I learned.*

I've done around 50 smart-contract audits with AI now, and here's the thing nobody building these tools wants to say out loud: **finding is the easy part.**

Point a decent AI agent at a codebase and it'll hand you a thousand plausible-looking vulnerabilities. It feels amazing. Then you start checking them and the number that actually matter is maybe five. A tool that surfaces a thousand candidates where five are real hasn't saved me any time. It just moved the work from *finding* to *filtering*, and filtering an AI's confident but wrong output is often slower than reading the code myself.

So I stopped asking "how many bugs can it find?" and started asking a way less exciting question: **how few false leads does it make me chase, without quietly dropping the real ones?**

That reframe is the whole thing, and it points at a claim I've come to believe strongly:

> **Discovery is no longer the bottleneck in AI security. Verification is.**

We now have agents that can hunt vulnerabilities autonomously. What we mostly don't have is agents that can tell a real, reportable bug from a hallucination, an accepted tradeoff, or a duplicate, and *prove* how often they get it right. That second problem is harder, less flashy, and it's where all the actual value lives.

## Three questions everyone smashes into one

Most AI auditors answer a single question, *"does this look like a bug?"*, and treat the answer as final. In reality a finding has to clear three completely separate gates:

![Verifier-first funnel: broad discovery narrows through reality, reachability and severity, then reportability](/assets/verifier-funnel.svg)

- **Is it real?** Does the mechanism actually exist when you re-derive it from source? A huge share of candidates die right here.
- **Is it reachable and severe?** A real mechanism isn't a vulnerability yet. An attacker has to be able to *reach* the broken state, and something that matters has to *consume* it.
- **Would a program actually pay for it?** Even a real, reachable, severe issue can be non-reportable: out of scope, documented behavior, needs a privileged key the threat model excludes, or a duplicate of something already filed.

Here's the distinction that took me the longest to actually get, and it's the whole game:

> **A real mechanism with no attacker-reachable path is not a false positive. It's a non-actionable true finding.**

Get that one wrong and everything downstream rots. If you treat "real but not reportable" as a false positive, you teach your system that its verification failed, which quietly pushes it to loosen the exact checks keeping it honest. A false positive means your model of the code was wrong. A non-actionable true finding means your model was *right* and there's just nothing to report. Different failures, different fixes.

## Recall first, then precision, never the other way

One trap eats a lot of these tools: filtering hard while you're still discovering. Do that and you throw away real bugs you never even looked at, and you'll never know, because a bug you didn't surface leaves no trace anywhere.

So the order is strict. Map the whole surface first, over-generate candidates, and *only then* run a precision gauntlet, where every stage can only *remove* candidates, never invent them. A "precision" stage that can conjure a new finding is a discovery stage in disguise, and sooner or later it launders a hallucination straight into a report.

## Where it actually stands

I'd rather be honest than impressive, so, plainly.

On live contests, the system has produced a handful of program-triaged Critical and High findings, accepted into triage, rewards pending. That's the signal I trust most: real programs, real triage. It also rejects roughly two-thirds of its own candidates before I ever see them, so most of the noise never reaches me. Recall is the axis I'm still climbing, and for a verifier-first system that's on purpose: the goal isn't to surface everything, it's to not waste your time on what it does surface.

What I don't know yet: live recall on never-seen code, the true rate at which my filters silence real bugs, and how well any of this holds on codebases that look nothing like the ones I've tested. The machinery is built and I trust it. The ground truth that feeds it is still small, but growing that corpus is straightforward, and each new evaluated case strengthens the evaluation loop.

## This isn't really about security

The shape here is a method, not a tool, and it goes way past smart contracts. Every high-stakes AI system eventually hits the same wall: telling a *technically correct* observation apart from something you should actually *act on*. A clinical suggestion that's true but not indicated. A flagged transaction that's weird but authorized. A compliance hit that's real but out of scope. Security just makes that gap impossible to ignore, because a wrong report costs a real human a real afternoon.

Building AI that finds things is the easy, impressive demo. Building AI that knows which findings are real, reachable, and worth acting on, and can prove how well it does that without fooling itself, is the hard part.

It's also the only part that matters.

## What's coming

This is the intro to a series I'm calling **Verifier-First AI**. Over the next few weeks I'm breaking down the ideas that made the biggest difference in practice, each on its own:

1. **Why multi-agent systems hallucinate.** How a chain of agents inherits trust and produces a beautiful, confident, completely wrong report, and the one rule that stops it.
2. **ICE: Impact Chain Escalation.** Why a real bug doesn't matter until you can trace it, end to end, to something that actually breaks.
3. **False positives are five different problems.** Hallucination, unreachable, intended, tradeoff, duplicate: why lumping them together quietly makes your system worse.
4. **Coverage vs reasoning gaps.** The single most useful way I've found to diagnose a missed bug.
5. **Building an evaluation that can't lie.** Why honest measurement is the real bottleneck, and the traps you have to beat.

Follow along if you're building anything where being *right* matters more than being *impressive*.
