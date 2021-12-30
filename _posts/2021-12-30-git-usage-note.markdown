---
layout: post
title:  "Git Usage Notes"
categories: git
---

## Git Usage Notes
This article contains some notes of git usage.

### Merge
**git merge** is always operating on current branch. Other branches will not be affected.

Make sure two branches involved in merging are up-to-date with latest remote changes.

`git merge other-branch` will merge `other-branch` into current branch. Fast-forward merge is first tried, then 3-way merge.
