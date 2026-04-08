---
title: What happens when rebasing.
date: 2024-10-22
tags:
  - git
comment: Good for solo MR, sort of bad with team without communication
---

When rebasing MyBranch onto master, "incoming" refers to MyBranch (the branch being replayed), and "current" is master. Rebase resets MyBranch to master, replays its commits, and temporarily considers master as "current." After rebasing, MyBranch becomes "current" again. This differs from merging, where "incoming" is the branch being merged, and "current" remains your branch.
