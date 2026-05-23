---
title: "Experimenting with Claude Code CLI in the Home Lab 🛡️"
author: Jesse Moore
date: 2026-05-23 12:00:00 -0500
categories: [AI, Pentesting]
tags: [claude, ai, pentesting, homelab, netexec, ludus, redteaming, hacksmarter]
---

I've been wanting to get familiar with the Claude Code CLI for a while now, so I finally dedicated some time to put it to the test in my home lab.

Inspired by some of the cool work I've seen by [@alleem](https://twitter.com/alleem) regarding pentesting, I decided to see if I could teach Claude to act as an "internal pentester." My goal was to have it generate reports based on the HackSmarter example report format.

## The Workflow

I used a **Proxmox + Ludus** setup to host the LeHack 2024 environment. I built a Kali machine and gave Claude access to it to run commands, train, and validate the creation of a custom NetExec skill.

## My Takeaways

**The Learning Curve:** It was incredibly insightful, though I definitely had to correct the model quite a bit to keep it on the right workflows.

**Structure Matters:** I realized that Claude performed much better once I created a standardized folder structure including existing scripts. This prevented it from having to "reinvent the wheel" every time we hit a familiar objective.

**Security Hygiene:** I also experimented with having Claude sanitize my secrets automatically — a massive win to ensure I don't accidentally expose credentials when I git push.

## The Verdict

The result was some pretty neat reports, but it required a lot of manual guidance and correction. While AI is a powerful assistant, I think I still enjoy the manual side of pentesting the most.

## Reports

I've attached the two reports I generated from my testing of the LeHack 2024 Ludus environment:

| Report | Description |
|--------|-------------|
| [**Standard Pentest Report**](/reports/lehack2024/report.html) | Traditional markdown-rendered HTML engagement report — findings, evidence, timeline, host matrix |
| [**Casebook Report**](/reports/lehack2024/casebook.html) | Operator casebook style — phosphor-green CRT aesthetic, attack graph, TTP matrix, attack chains |

Has anyone else been playing around with CLI agents for security tasks?

---

`#InfoSec` `#Pentesting` `#ClaudeCode` `#AI` `#CyberSecurity` `#HomeLab` `#Ludus` `#RedTeaming`
