---
title: "Git commits: An Effective Style Guide"
date: "2018-06-30"
tags: [ git,productivity ]
image: img/posts/git_commits.jpg
---

## Introduction

[Git commits](https://git-scm.com/docs/git-commit) are one of the most underrated features of Git. Pause for a moment
and think;

1. When was the last time you had to revert your code to an earlier point in time?

2. Did you have a hard time finding that commit that messed up your code?

3. Was that because of unclear, mixed commit messages?

4. When that commit was found, did you also realize that you included several file changes in that particular commit,
   resulting in a headache when trying to rebase back to that commit?

**Congratulations**, you just incapacitated one of the strongest features of Git:

[Code versioning & history!](https://git-scm.com/docs/git-reset)

## The importance of keeping commits clean and tidy

As a developer, one of the most difficult tasks that you will stumble upon, is communication. Communication with other
developers, with clients, with managers, but also communication with the **future you**.

Having a clean git commit messages history means that at any time in the future you will be able to revise what you
changed in the code, what functionality parts were removed and what bugs were fixed.

By running `git log` you will be able to view a logical flow of messages that alone explain the whole code history of
the application.
You can also run `git log --pretty=oneline` to view a more dense version of `git log`.

## How to organize a commit message

If you use a Project management tool
like [Jira](https://www.atlassian.com/software/jira), [Asana] (https://asana.com/), [Trello] (https://trello.com/) or
other, and follow a task-breakdown project management flow
like [Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development))
or [Kanban](https://en.wikipedia.org/wiki/Kanban_(development)), you will have all your tasks in one place, tidy and
organized.

Your goal should be to have each commit characterize a corresponding sub-task. No
more `git commit -m "Added code"`, `git commit -m "Added CSS"` or `git commit -m "bugfix"`.
These commits are untraceable and you will **never** be able to use them in the future.

*So, why having them in the first place?*

When you have changed a bunch of classes and you are ready to commit the code, take a moment and think;

1. What did you change in each file? What classes are relevant to the sub-task functionality you worked on?

2. How is your app affected and what breaking changes did you introduce to the code?

## Following a clean message style

By choosing a style and sticking to it, you bring your code to another level.

Other developers will be able to read your message history like a novel and they might never have to look at the actual
code (which will save them much time).

You will also force yourself to **commit what matters, not what is convenient at the current moment**.

## Finally, the style guide

### Message Structure

A clean commit message should consist of three distinct parts separated by a blank line: the title (which describes the
type of code change), and a descriptive body with optional messages about the task following or the issue tracker id (
footer).

The layout should look like this:

```
commit type

body (changes, links, attributions, etc)

issue tracker id (for reference)
```

### The commit type

It can be an abbreviation of what the commit actually is. For example (taken
from [Udacity's commit style guide](http://udacity.github.io/git-styleguide/)):

1. feat: a new feature
2. fix: a bug fix
3. docs: changes to documentation
4. style: formatting, missing semi colons, etc; no code change
5. refactor: refactoring production code
6. test: adding tests, refactoring test; no production code change
7. chore: updating build tasks, package manager configs, etc; no production code
   change

### The body

Anything that the commit changed on the code. It can be a description of the functionality added, a description of the
task completed, or the steps of a bug reproduction and solution.

When composing the body, try to be as detailed as possible. Think that there is a high chance that you will be reading
this commit message two years from now. What would you like to communicate to your future self about?
You can add bullet points, lists, links to Stackoverflow answers and links to your issue tracker.

## Conclusion

Being able to compose meaningful Git commits will not only make you likeable to other developers that will read your
commits.
It will also make you a better writer, as well as more analytical and clean code - oriented.

*Think about your future self. Write better commits, today!*

References:
[Udacity Git Commit Message Style Guide](http://udacity.github.io/git-styleguide/)
