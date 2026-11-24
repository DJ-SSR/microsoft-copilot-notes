For the kind of work you've described (Visual Studio + Azure DevOps + Git feature branches + Reqnroll/feature files), a fairly standard and clean workflow is:

## 1. Start from an up-to-date master/main

```shell
git checkout master
git pull origin master
```

Create a feature branch:

```shell
git checkout -b feature/136413-tab-order-fix
```

or whatever naming convention your team uses.

---

## 2. Develop normally

Make changes to:

- `.feature` files
- step definitions
- Selenium code
- helper classes
- test data

Commit as often as makes sense.

Examples:

```shell
git add .
git commit -m "Add tab order scenario"

git add .
git commit -m "Implement tab validation helper"

git add .
git commit -m "Fix failing test"
```

Don't worry about creating lots of commits while developing.

I personally consider these commits to be checkpoints rather than final history.

---

## 3. Push regularly

Especially if you're working on a VM, this is good practice.

```shell
git push origin feature/136413-tab-order-fix
```

Throughout the day you might have:

```text
c1 Initial scenario
c2 Step definition
c3 Fix bug
c4 Refactor
c5 Review comments
```

Nothing wrong with that.

---

## 4. Before creating the PR, update from master

Get latest changes:

```shell
git checkout master
git pull origin master
```

Return to feature branch:

```shell
git checkout feature/136413-tab-order-fix
```

Rebase onto latest master:

```shell
git rebase master
```

Resolve conflicts if required:

```shell
git add <resolved files>
git rebase --continue
```

Repeat until complete.

At this point your work sits on top of the latest master.

---

## 5. Clean up commit history

This is the point where many teams squash work into meaningful commits.

View only your branch commits:

```shell
git log --oneline origin/master..HEAD
```

You recently used this approach, which is exactly what I would recommend.

Example:

```text
31798d4 Retest 136412
18f2c31 Fix review comments
8a4b112 Add selenium helper
4d9d555 Initial implementation
```

Interactive rebase:

```shell
git rebase -i origin/master
```

Git opens something similar:

```text
pick 4d9d555 Initial implementation
pick 8a4b112 Add selenium helper
pick 18f2c31 Fix review comments
pick 31798d4 Retest 136412
```

Change to:

```text
pick 4d9d555 Initial implementation
squash 8a4b112 Add selenium helper
squash 18f2c31 Fix review comments
squash 31798d4 Retest 136412
```

Save and exit.

Git lets you create one final commit message.

Example:

```text
136413 - Validate tab order in product details page

Added feature scenarios
Added tab order validation helper
Added Selenium support methods
Updated step definitions
```

---

## 6. Force-push rewritten history

Because rebasing changes commit hashes:

```shell
git push --force-with-lease origin feature/136413-tab-order-fix
```

Prefer:

```shell
git push --force-with-lease
```

rather than:

```shell
git push --force
```

because it provides protection against overwriting someone else's work.

---

## 7. Create Pull Request

Now the PR contains:

```text
master

A----B----C
           \
            feature
```

with a single clean commit:

```text
136413 - Validate tab order in product details page
```

instead of:

```text
WIP
Fix typo
Oops
Review comments
Try again
Retest
```

Many reviewers appreciate this.

---

## A practical workflow I often recommend

During development:

- Commit frequently
- Push frequently

Before PR:

1. Pull latest master
2. Rebase feature branch onto master
3. Run tests
4. Squash commits
5. Force-with-lease push
6. Create PR

This gives you:

- recovery if VM or machine dies
- easy conflict resolution while work is fresh
- clean PR history
- one logical commit representing the completed feature

For Azure DevOps specifically, another common approach is to leave your branch history untouched and let the PR use **"Squash merge"** when completed. In that model you keep all your development commits, and Azure DevOps creates a single commit on master during the merge. The result on master is almost identical, but you avoid rewriting branch history. Many teams prefer this because it's simpler and safer.