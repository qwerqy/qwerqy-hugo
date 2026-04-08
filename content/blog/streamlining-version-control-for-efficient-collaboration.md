---
title: Streamlining Version Control for Efficient Collaboration
date: 2023-06-22
excerpt: Over the past year, I have come to rely on several key Git commands that have greatly improved my efficiency and collaboration with teammates. In this blog post, I will share my most used Git commands and explain how they have enhanced my development process.
keywords:
  - Git commands for Frontend Engineers
  - Using Git in a large development team
  - Git rebase vs merge for efficient version control
  - Streamlining development process with Git
  - Git Cherry Pick for selective commits
  - Maintaining clean commit history with Git Squashing
  - Command-line tools for Git operations
  - Git pull --rebase for keeping branch up to date
  - Safe code pushing with git push --force-with-lease
  - Git practices for effective collaboration
  - Improving coding efficiency with Git
  - Superside Frontend Engineer Git practices
  - Advantages of Git in fast-paced environments
  - Mastering Git for large codebase management
  - Avoiding merge conflicts with Git rebase
  - Terminal vs GUI for Git operations
  - Overcoming coding challenges with Git commands
  - Enhanced development process with Git
  - Git commands in real-life coding situations
  - Git workflow tips for Frontend Engineers
---

As a Frontend Engineer at Superside, we have about 60+ members **worldwide** working on the codebase **day-to-day,** **around the clock.** In a fast-paced environment, version control is the backbone of my workflow.

Over the past year, I have come to rely on several key Git commands that have greatly improved my efficiency and collaboration with teammates. In this blog post, I will share my most used Git commands and explain **how they have enhanced my development process.**

## Less of Git Merge, More of Git Rebase

- In a big company with constant code changes, keeping my branch up to date is **crucial**. Instead of relying heavily on `git merge`, I have found `git rebase` to be more suitable in such scenarios.
- With code being pushed frequently throughout the day, using `git merge` can **clutter the commit tree** and make it **difficult to review changes.** I reserve `git merge` specifically for merging branches into the master.
- In an ideal scenario, automated pipelines handle the branch-to-master merges, making it easier to manage and review code changes.

It is also good practice to perform `git rebase` on your branch with the master branch before asking for reviews and eventually get it approved and deployed for few things:

- To make sure **all unit tests are updated** in your branch, to **avoid coverage report errors**.
- To make sure **merge conflicts are dealt** with beforehand, and save yourself from crying later.
- There could be potential fixes that has been pushed after you created your branch that might come in handy during those **blocking** moments.

In summary, I use `git rebase` **from the master branch** to keep my branch up to date and `git merge` to merge my branch **into the master branch.**

## Git Cherry Pick

- There are instances where I need to incorporate specific commits from one branch into another without merging the entire branch. This is where `git cherry-pick` comes in handy.
- By using `git cherry-pick`, I can select individual commits and apply them to my current branch, saving time and effort in managing separate branches.

In a real life situation, you might be working on branch, only to know that you have been breaking a lot of stuff. You might accidentally removed an important commit by another engineer during a rebase, or accidentally pushed a commit at a wrong branch. This can be handy. You wanna know why I know? _I was there…_

## Squashing Commits

- To maintain a clean and concise commit history, I often squash my commits **before merging** or **pushing my changes**.
- Squashing commits allows me to **combine multiple related commits into a single commit,** making it easier to understand and review the changes.

This is really useful when you have over 20 commits in your branch and depending on your pipelines, having too much commits can sometime **break one of your pipelines.** Broke the unit testing pipeline when my MR had over 30 commits. Believe me, squashing helps.

## Less Reliance on GUI, Terminal is the way.

- I have found it more efficient to rely on command-line tools for most Git operations.
- VSCode & Webstorm’s Git interface is cool, but having to move my hand to my mouse and click buttons have made it inefficient for me for all the microseconds I lost.
- Working with the CLI enables me to quickly execute commands, navigate through branches, and visualize the commit history, all without leaving the terminal.

## —force vs —force-with-lease

- To keep my branch up to date with the latest changes from the remote repository, I often use `git pull --rebase` instead of a simple `git pull`.
- After rebasing, I use `git push --force-with-lease` to safely push my changes to the remote repository, ensuring I do not **overwrite any unintentional updates** made by my teammates.
- I only use `git push --force` on my personal projects or any projects that **don’t involve 10 other engineers.**

These are the Git commands & practices that have become an integral part of my daily development workflow over the past year. By leveraging `git rebase` for keeping branches up to date, utilizing `git cherry-pick` for selective commits, squashing commits for cleaner history, relying on the CLI, and mastering rebasing and `git push` options, I have streamlined my version control process for **efficient collaboration.**

Remember, Git is a powerful tool that offers **numerous features and commands** to adapt to different development scenarios. Experiment, explore, and find the commands that work best for your workflow.

Happy coding and collaborating!
