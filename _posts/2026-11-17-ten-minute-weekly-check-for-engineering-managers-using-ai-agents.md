---
layout: post
title: "A ten-minute weekly check for engineering managers whose teams use AI coding agents"
description: "Five questions that show where your team knowledge is thin, before an incident does."
date: 2026-11-17 09:00:00 +0000
author: Neha Singh
tags: [engineeringmanagement, ai, leadership, softwareengineering, management]
---

*Disclosure: I'm Neha, founder of Zevqi. The ritual works with a spreadsheet; I mention our tool at the end.*

If your developers use AI coding agents, your team ships more code than it used to. What you probably cannot say with confidence is how much of that code anyone could explain.

You do not need a big process. Ten minutes on the same day each week, with the same five questions, will tell you more than a quarterly audit.

## The five questions

**1. Which files changed this week that only one person has ever worked on?**
These are your single points of knowledge. If they are in important areas, they need a second person.

**2. Which merged changes were written mostly by an AI tool and never reviewed by a human?**
Many teams can answer this only by asking around. Some AI tools mark commits with a co-author line, which makes the list easy to pull from git.

**3. Whose areas are at risk this month?**
Check holidays, notice periods and parental leave against the list from question 1. The goal is not alarm. It is to have the conversation while the person is still around to have it.

**4. What did developers say they did not understand?**
If your team records questions after reviews or onboarding, read them. A repeated question about the same module is a signal the code, or its documentation, needs work.

**5. Which one walkthrough should happen this week?**
Pick exactly one. A thirty-minute session where the person who knows a module explains it to someone who does not is the cheapest fix there is. Put it in the calendar before you close the document.

## How to run it

- Same day, same ten minutes, every week. The habit matters more than the tooling.
- Write down the one decision you made. A weekly list of decisions becomes a record of how your team's knowledge changed.
- Do not turn it into a scoreboard. If people feel ranked, they will game the numbers. Frame it around files and areas, not individuals.
- Expire your own assumptions. If someone understood a file six months ago and it has changed a lot since, that understanding is out of date.

## Where a tool helps

A spreadsheet works for a team of five. When you have more repositories and more people, collecting these five answers by hand gets slow. That is the problem Zevqi is built for. Its dashboard shows the git knowledge map, coverage of files with current evidence of understanding, open questions from developers and recent AI-assisted changes, and it emails a weekly digest of decisions worth making with links to the files. Every flag shows the reason it was raised, and nothing is turned into a score for a person.

Your source code never reaches Zevqi, and no model key is needed. The command-line tool is open source, and the team dashboard has a seven-day trial with no card.

## The point

You cannot manage what you cannot see. Whether you use a spreadsheet or a dashboard, make where knowledge sits visible every week, and make one small decision from it.

*Demo dashboard, no sign-up: https://zevqi.com/demo/dashboard*


---

*Zevqi builds [zlearn](https://github.com/zevqi/zlearn), an open-source (MIT) command-line tool that shows which parts of your codebase your team has demonstrably understood. Source code never reaches Zevqi. [Try the 7-day team trial](https://zevqi.com/trial) or [explore the demo dashboard](https://zevqi.com/demo/dashboard).*
