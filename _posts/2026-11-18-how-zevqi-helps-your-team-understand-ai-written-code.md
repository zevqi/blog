---
layout: post
title: "How Zevqi helps your team understand the code AI writes"
description: "What Zevqi and its open-source CLI do: map who knows which files, record review evidence, gate pull requests, and show managers where understanding is thin."
date: 2026-11-18 12:00:00 +0000
author: Neha Singh
canonical_url: https://zevqi.com/blog/how-zevqi-helps-your-team-understand-ai-written-code
tags: [ai, programming, softwareengineering, codereview, devtools]
---

*Disclosure: I'm Neha, the founder of Zevqi. This is also published on [zevqi.com](https://zevqi.com?utm_source=github-pages&utm_medium=referral&utm_campaign=blog-launch) where it is kept up to date.*

AI coding agents can now write a large pull request in minutes. Understanding that pull request still takes a person, and nothing in your tools records whether that person ever did it. A manager cannot see it, and the team usually finds out when something breaks.

Zevqi is built to make that visible. It is a command-line tool plus a team dashboard. The tool runs on each developer's machine and records evidence that someone understood a change. The dashboard shows an engineering manager which parts of the codebase have that evidence, which parts have none, and which parts only one person knows.

This post explains what it does, how a team uses it day to day, exactly what data it collects, and where it does not help.

## The problem: code that works and that nobody understands

We call it comprehension debt: code that passes its tests and ships, but that nobody on the team could explain. Technical debt is a shortcut someone knows about. Comprehension debt has no owner, because nobody chose it. An agent wrote the change, the review was a skim, and the merge button was green.

It shows up in predictable places:

- **A single owner.** One developer prompted the agent for a feature, so only they have context. If they leave or go on holiday, the file is a black box.
- **A quiet author.** The person who knew a module well has moved to another project and has not touched it in months.
- **Unreviewed AI changes.** A change written by an agent landed, and no second person ever read it properly.

Code review does not solve this. A review judges a change at the moment it is made. It does not leave a record of who came to understand the code afterwards. An approved 900-line diff and a person who could explain the module to a new joiner look identical in your pull request history.

## What Zevqi is

Zevqi has two parts:

1. **The command-line tool** (open source, MIT licence). Each developer installs it. It maps who has touched which files from git history, helps a developer record that they understood a change, and can check a pull request before it merges.
2. **The team dashboard** at zevqi.com. It collects the results from everyone's tool and shows a manager coverage, blind spots and decisions to make this week.

The central idea is simple: a review is **evidence for one version of one file**. It expires when the file changes, and after 90 days. So the answer to "who understands this file?" stays honest instead of getting stale.

## Set it up in about five minutes

You need a Node.js install and a git repository. A team lead creates a team on the website (there is a 7-day free trial that needs no card) and shares the team key. Each developer then runs two commands in their repository:

```
npm install -g zevqi
zevqi setup --key <your-team-key>
```

`zevqi setup` joins the team, turns Zevqi on in that repository (it installs hooks for Claude Code and adds instructions to `AGENTS.md`), and runs a first scan. After that, the team dashboard fills in.

You can also try it without joining anything. `zevqi scan` builds the git knowledge map on your machine only, with no account and no API key:

```
zevqi scan --dry-run
```

That prints exactly what a scan would upload, and sends nothing.

## What the command-line tool does

| Command | What it does | Needs a model key? |
|---|---|---|
| `zevqi scan` | Maps who holds knowledge of each file from git history: single-author files, authors who have gone quiet, untouched files, and AI-assisted commits | No |
| `zevqi review <file>` | You read recent changes to a file and describe them in your own words. That is recorded as evidence. Your written answers stay on your machine | No |
| `zevqi lesson add` | Saves a short lesson and quiz that your coding agent wrote about a change | No |
| `zevqi learn` | Takes you through the quiz for the oldest un-reviewed change | No |
| `zevqi check` | Pull request gate: lists changed files that have AI changes nobody has understood. It can fail a CI job | No |
| `zevqi gate warn` or `block` | Makes Claude Code warn or stop when it is about to commit while AI changes are still un-reviewed | No |
| `zevqi status`, `stats`, `doctor` | What is waiting, your own streak, and a check that everything is wired up | No |
| `zevqi ui` | A local dashboard on your machine for your own lessons and knowledge map | No |

A model API key is optional. If you add your own, Zevqi can write diff-specific quiz questions itself, and the diff then goes to your model provider under your own account. You are told before that happens. Without a key, your coding agent writes the lessons, or you describe the change yourself in `zevqi review`.

## What a manager sees

The dashboard turns everyone's evidence into a few views a manager can act on:

- **Coverage.** The share of AI-changed files that have current review evidence, with the formula shown. It counts how many files have two or more reviewers, one reviewer, out-of-date evidence, or none.
- **Decisions for this week.** A short list such as "get `payments/refund.ts` reviewed" or "add a second reviewer for `auth/session.ts`", each with the reason it was raised.
- **Knowledge map from git.** Files only one person has touched across several commits, files whose main author has gone quiet, and AI-assisted changes with no review.
- **Knowledge concentration.** How many files rest on a single reviewer, and how many of those are in sensitive areas such as authentication, billing and infrastructure.
- **Open questions.** When a developer marks a change "needs clarification", it appears here until someone follows up.
- **A weekly digest** by email or Slack that lists the decisions, so the manager does not have to open anything.

Every flag explains itself. The levels (high, medium, low) come from fixed, visible rules, and nothing is turned into a made-up score. You can see a full example on sample data in the [demo dashboard](https://zevqi.com/demo/dashboard?utm_source=github-pages&utm_medium=referral&utm_campaign=blog-launch) without signing up.

### It is about files, not about people

We made a deliberate choice here. The People page lists, in alphabetical order, the files each person has reviewed and the files where they are the only reviewer. There is no pass rate, no level and no ranking of developers on the manager's side. If a tool like this becomes a scoreboard, people game it or resent it, and the data stops meaning anything. Streaks and levels exist only on each developer's own machine, as private motivation.

## The pull request gate

`zevqi check` answers one question for a pull request: which changed files have AI changes that nobody on the team has understood yet? Run it in CI:

```
zevqi check --base origin/main --fail-on unreviewed
```

It exits non-zero if a changed file has un-reviewed AI changes, so you can make it a required check. You decide how strict to be: `none` only reports, `unreviewed` fails on files nobody has reviewed, and `blind` fails on the highest-risk ones. In CI you can pass a team key through an environment variable, so no developer setup is needed on the build machine.

## What data leaves your machine

This is the first question a security reviewer asks, so here is the full answer. Source code never reaches Zevqi.

**Sent for each developer on the team:** their email (and a name if given), the repository name and the paths of the files a lesson touches, whether each lesson is waiting, passed or failed, a content fingerprint of each file (so Zevqi knows when it changed after a review), and one of three answers after a quiz: understood, needs clarification, or investigate further.

**Sent by the git scan:** file paths, commit counts, first and last commit dates, how many commits were AI-assisted, and each author as a pseudonymous id (a one-way hash of their email) with their commit counts.

**Sent only if a developer turns sharing on:** a short lesson summary and open questions.

**Never sent:** source code, diffs, commit messages, quiz questions and answers, the explanation a developer writes in `zevqi review`, and your model API key.

Two honest caveats. Author ids are pseudonymous, not anonymous: someone with a list of candidate emails could hash them and compare. And file paths can reveal project names. The full list, with how to delete a team and its data, is on the [security page](https://zevqi.com/security?utm_source=github-pages&utm_medium=referral&utm_campaign=blog-launch).

## What Zevqi does not do

- **It does not prove a developer is competent.** A passed quiz or a written review shows that someone engaged with a change. It is a signal, not proof, and it is self-reported.
- **It does not replace code review.** Review decides whether a change is good. Zevqi records who afterwards understood it.
- **It only counts AI-assisted commits when the commit says so**, for example through a co-author trailer. The real share of AI-written code is probably higher than the number it shows.
- **Agent-written lessons are early access.** They rely on your coding agent following the instructions Zevqi adds to `AGENTS.md`. Try it on one repository first. The git scan and `zevqi review` work without any agent.
- **It is young.** There is no SOC 2 report, SSO or audit log yet. The security page lists these gaps plainly.

## Who it is for

Zevqi fits an engineering manager or tech lead of a team of 2 to 50 developers who use AI coding agents and want to know where understanding is thin. It is most useful when files change often, when a few people hold most of the knowledge, or when you onboard new developers into a codebase an agent mostly wrote.

It is probably not worth it for a solo developer who wants a quick explanation of one change (just ask your assistant), or for a team that does not use AI agents and already has a healthy review culture.

## What it costs

The command-line tool is free and open source, and works alone with no account. The team dashboard has a 7-day free trial for up to 5 developers with no card. After that, a Team plan is $12 per developer per month, or $10 on annual billing, for 2 to 50 developers.

## Try it

- Look at the demo dashboard with sample data, no sign-up: https://zevqi.com/demo/dashboard?utm_source=github-pages&utm_medium=referral&utm_campaign=blog-launch
- Start the 7-day team trial, no card: https://zevqi.com/trial?utm_source=github-pages&utm_medium=referral&utm_campaign=blog-launch
- Preview a scan on your own repository without sending anything: `npm install -g zevqi` then `zevqi scan --dry-run`
- Read the code: https://github.com/zevqi/zevqi

If you try it and something is unclear or missing, the feedback box in the dashboard goes straight to us. We would rather hear what did not work than what did.
