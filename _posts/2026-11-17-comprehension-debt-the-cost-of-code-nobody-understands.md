---
layout: post
title: "Comprehension debt: the cost of code nobody on your team understands"
description: "AI agents write the diff in minutes. Understanding it still takes a person. Here is how to see the gap, and three things any team can do this week."
date: 2026-11-17 09:00:00 +0000
author: Neha Singh
tags: [ai, programming, softwareengineering, management, codereview]
---

*Disclosure: I'm Neha, the founder of [Zevqi](https://zevqi.com), which builds a tool in this space. The practical advice below works without it.*

A few years ago, most of the code in a pull request was written by the person who opened it. That person understood it, because they had just typed it.

That is no longer a safe assumption. An AI coding agent can now produce a large diff in minutes, across files its author never opens. The pull request gets a skim, the tests pass, and it merges. Everything looks healthy.

Three months later someone asks why a module behaves the way it does, and nobody on the team can say.

## What comprehension debt is

Technical debt is code you know is shortcut-ridden. **Comprehension debt is code that works but that no one on the team understands.** It is invisible, because nothing is broken. It only shows up when something is: an outage at 2 a.m., a security question from a customer, or the one person who half-remembered a module going on holiday.

AI did not invent the problem. Teams have always had files only one person understood. What changed is the speed at which the gap can grow, because the author of a change can now be an agent that does not remember it, and the human who merged it may not have read most of it.

## Why review does not solve it

Code review is good at judging a change at the moment it is made. It does not leave behind a record of who actually came to understand the code. It also does not tell a manager which parts of the codebase have no one behind them.

A reviewer approving a 900-line diff at 5 p.m. is not the same as a person who could explain the module to a new joiner. The two can look identical in your pull-request history.

## Three things any team can do this week, with no tool

1. **Make authors explain AI-written changes.** In the pull request description, ask for two sentences: what this change does, and the one part most likely to break. If the author cannot write them, the change is not ready to merge.
2. **Rotate who reviews unfamiliar areas.** If one person always reviews the payments code, they are the only one who understands it. Have a second person review it once in a while, even if slower.
3. **List the files only one person has ever touched.** This is the closest cheap signal for where understanding is thin. A single git command can produce it, which I show in a separate post.

## Make the invisible visible

The reason comprehension debt grows is that nobody sees it. So whatever you do, the first step is to look at where knowledge actually sits: who has touched which files, who has stopped touching them, which recent changes were written by an AI tool and never reviewed by a person.

That is what we built Zevqi to show. Its command-line tool reads your git history on your own machine and builds a knowledge map, and your team can add short lessons or attested reviews as evidence that a person understood a specific version of a file. The dashboard shows managers where understanding is recorded and where it is not. Your source code never reaches Zevqi, and no model key is needed. The tool is open source, with a seven-day trial of the team dashboard that needs no card.

It does not prove a developer is good, it does not rank people, and it does not replace review. It records evidence that expires when a file changes, so the answer to "who understands this?" stays honest.

## The question to ask on Monday

If your most knowledgeable person on a critical module left tomorrow, what would break first, and would you know in advance?

If the honest answer is "I would find out when it happened", you have comprehension debt. The good news is that it is cheap to measure and not hard to pay down once you can see it.

*Try the demo dashboard without signing up: https://zevqi.com/demo/dashboard*


---

*Zevqi builds an open-source (MIT) [command-line tool](https://github.com/zevqi/zevqi) that shows which parts of your codebase your team has demonstrably understood. Install it with `npm install -g zevqi`. Source code never reaches Zevqi. [Try the 7-day team trial](https://zevqi.com/trial) or [explore the demo dashboard](https://zevqi.com/demo/dashboard).*
