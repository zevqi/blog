---
layout: post
title: "Find the files only one person understands, using just git"
description: "A short git and awk script that lists files only one person has ever changed, plus three caveats that keep the result honest."
date: 2026-11-17 09:00:00 +0000
author: Neha Singh
tags: [git, programming, devops, tutorial, productivity]
---

*Disclosure: I'm Neha, founder of Zevqi. The commands below are plain git and awk, and need no tool of mine.*

"Bus factor" is the number of people who would have to disappear before a project stalls. You do not need a platform to get a first answer. Your git history already knows who has touched which files.

## The one-liner

Run this in the root of a repository (Git Bash on Windows is fine):

```bash
git log --no-merges --format='@%ae' --name-only |
awk '/^@/{a=substr($0,2);next}
     NF{ if(!(($0,a) in s)){s[$0,a]=1; n[$0]++} c[$0]++ }
     END{for(f in n) if(n[f]==1 && c[f]>=3) print c[f], f}' |
sort -rn | head -20
```

It prints the files that exactly one author email has ever changed, across three or more commits, with the commit count first. Those are your single points of knowledge: places where one person is the only one who has ever worked in the file.

## How it works

- `git log --name-only` lists every commit followed by the files it touched. The `--format='@%ae'` marker puts the author email on its own line so awk can tell commits from file names.
- For each file, awk counts how many different authors have touched it (`n`) and how many commits (`c`).
- The `END` block keeps files with one author and at least three commits. The three-commit floor removes files that were created once and never revisited, which are usually not a risk.

## Who is still around?

A single author is not a problem if that person is active. To see who has committed recently:

```bash
git log --no-merges --since="90 days ago" --format='%ae' | sort | uniq -c | sort -rn
```

Compare the two lists. A single-author file whose author does not appear in the second list is the one to look at first.

## Three caveats, so you do not fool yourself

1. **It counts emails, not people.** Someone who commits from two addresses looks like two authors. Add a `.mailmap` file to merge identities and bots.
2. **It does not follow renames.** A file that was moved loses its earlier history, so it can look like a single-author file when it is not. For a specific file, `git log --follow` gives the fuller picture.
3. **Authored is not understood.** The person who wrote a file may no longer remember it, and a person who only reviewed it may understand it well. Git shows who wrote code, not who understands it. Treat the list as a place to ask questions, not a verdict.

## Turning it into a habit

Run it monthly and keep the output. If a file stays on the list for months, schedule a short walkthrough: have the author explain it to a second person, and note who attended. That second person is the beginning of a bus factor of two.

## When agents write the code

With AI coding agents, the commit author may be a developer who accepted a large generated diff without reading most of it. Then "only one person has touched this file" says even less about understanding. Some tools add a co-author trailer to such commits, and `git log --format='%b' | grep -i co-authored-by` shows which commits carry one.

We built Zevqi's zlearn tool to do this kind of analysis automatically, add the AI-assisted signal, and let developers record evidence that they understood a file, so a manager can see coverage over time. Its `zlearn scan` command builds a similar map from git alone, with no model key, and your code never leaves your machine. But if all you wanted was the one-liner above, you have it.


---

*Zevqi builds [zlearn](https://github.com/zevqi/zlearn), an open-source (MIT) command-line tool that shows which parts of your codebase your team has demonstrably understood. Source code never reaches Zevqi. [Try the 7-day team trial](https://zevqi.com/trial) or [explore the demo dashboard](https://zevqi.com/demo/dashboard).*
