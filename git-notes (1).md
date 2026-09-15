# My Git Notes

Just writing down what I actually understand about Git now, in my own words, so I can come back to this later instead of re-learning it from scratch.

## The basic idea first

Git isn't really "saving diffs" — every commit is a full snapshot of the project at that point in time, and it's linked to the commit before it. That's why the history is a chain (technically a graph, since branches can split and merge). A branch is honestly nothing fancy — it's just a little pointer/label that says "this branch is currently at this commit." That's why making a new branch is instant, it's not copying any files, it's just creating a new pointer. HEAD is just another pointer that tracks whichever branch (or commit) you're currently "on."

Because of this, deleting a branch doesn't actually delete the commits — they just become unreachable and hang around until Git eventually cleans them up.

## The hidden `.git` folder — where all of this actually lives

The moment I run `git init` inside a folder, Git creates a hidden subfolder called `.git` right there, and that's it — that's the whole repository, really. Everything else (my actual files) is just the "working directory." A few things I now understand live inside `.git`:

- Every version of every file I've ever committed is stored as an **object** inside `.git`, identified not by filename but by the SHA-1 hash of its own content. So if two files (or two versions of the same file) have identical content, Git only stores it once. Change even one character and it gets a completely new hash.
- There are basically four kinds of objects in there: **blobs** (raw file contents), **trees** (a snapshot of a directory — basically a list mapping filenames to blob/tree hashes), **commits** (a pointer to one tree, a pointer to the parent commit(s), plus author/date/message), and optionally **tags**.
- Branches themselves are stored as tiny files under `.git/refs/heads/` — literally just a text file containing the 40-character hash of the commit that branch currently points to. That's the whole "branch" mechanism.
- `HEAD` is its own small file that usually just says which branch I'm currently on. If I check out a raw commit hash instead of a branch, HEAD points directly at that commit instead — this is "detached HEAD," and it lets me poke around old history without moving any actual branch.

Since I basically never touch `.git`'s internals by hand, this was mostly just useful for understanding *why* branches are so cheap and instant, and why deleting a branch doesn't delete the commits — they're still sitting in the object database, just unreachable, until Git's garbage collection eventually clears them out.

## Everyday commands I use constantly

- `git status` — my "what's going on right now" command. Shows what's changed, what's staged, what's untracked. I run this constantly, basically before and after everything.
- `git add <file>` — stages a file, i.e. tells Git "put this in the next commit."
- `git commit -m "message"` — actually saves the staged stuff as a permanent snapshot.
- `git diff` — shows me what I've changed but haven't staged yet.
- `git diff --staged` — shows me what's staged but not committed yet (different from plain `git diff`, took me a while to get this).
- `git log --oneline --graph` — my go-to for seeing history without it being overwhelming. One line per commit, plus a little graph so I can see branches/merges visually.

---

## Working locally (branches, merging, rebasing)

This is all stuff that happens entirely on my machine, no GitHub involved.

### Branch commands

- `git branch <name>` — makes a new branch but doesn't move me onto it.
- `git switch <name>` (or `git switch -c <name>` to create + switch in one go) — this is what I actually use to hop between branches. `git checkout` does the same job but it's the older/more overloaded command, so I stick with `switch`.

### Merging vs rebasing — the thing that took longest to click

**Merging** (`git merge <branch>`) combines another branch into the one I'm on.
- If my branch hasn't moved since the other branch split off, Git just fast-forwards — moves the pointer forward, no new commit, nothing to think about.
- If both branches have new commits, Git does a real three-way merge and creates a merge commit with two parents. This is the normal case when you've actually been working on both branches.

**Rebasing** (`git rebase <branch>`) takes my commits off and replays them on top of the other branch instead, so I end up with a straight line instead of a merge commit. Looks cleaner in the log, but it rewrites commit hashes — which is exactly why you're not supposed to rebase stuff you've already pushed/shared with someone else. If someone else already has those commits, rewriting them locally creates a mess.

So basically: merge = keeps true history, adds a merge commit. rebase = fake-straightens the history, but only safe on stuff that's still local/yours.

### How to actually resolve a merge conflict

This used to freak me out, now it's just a checklist:

1. Try the merge (`git merge <branch>`) or rebase (`git rebase <branch>`). If Git can't figure out how to combine a change automatically, it stops and tells me there's a conflict.
2. Open the conflicted file. Git marks the conflicting section like this:
   ```
   <<<<<<< HEAD
   my version of the lines
   =======
   the other branch's version of the lines
   >>>>>>> branch-name
   ```
   `HEAD` side is what my current branch has, the bottom side is what's coming in.
3. Manually edit the file to what it *should* actually say — pick one side, mix both, rewrite it, whatever's correct. Delete all three marker lines (`<<<<<<<`, `=======`, `>>>>>>>`) once I'm done, they're not meant to stay.
4. `git add <file>` — this tells Git "yes, I've resolved this one."
5. Finish it off:
   - if it was a merge → `git commit` (Git usually pre-fills a merge commit message, I just confirm it)
   - if it was a rebase → `git rebase --continue` (repeats for every commit being replayed, since each one can conflict separately)
6. If I panic and just want out → `git merge --abort` or `git rebase --abort` puts everything back exactly how it was before I started. Good safety net when a conflict looks worse than it is.

### Other local commands I used but less often

- `git stash` / `git stash pop` — "shelve" my uncommitted changes so I can switch branches with a clean working directory, then bring them back later. Useful when I realize mid-change that I'm on the wrong branch.
- `git cherry-pick <commit>` — grabs just one specific commit from somewhere else and applies it on my current branch. Didn't use this a lot but good to know it exists instead of manually redoing a change.
- `git reflog` — my "undo history" safety net. Shows every place HEAD has been, even commits that got orphaned by a reset. If I ever mess up a reset or rebase badly, this is how I find my way back to the commit I lost.
- `git revert <commit>` — instead of deleting a bad commit, it makes a new commit that undoes it. Safer than rewriting history if the bad commit's already been pushed/shared.
- `git tag <name>` — sticks a permanent label on a commit, e.g. marking a version.

---

## Working with remotes (GitHub)

This is the stuff that actually involves talking to another copy of the repo over the network.

A "remote" is just another copy of the repo somewhere else — for me that's GitHub, and it's called `origin` by convention. A **remote-tracking branch**, like `origin/master`, is just a local bookmark of where that branch was the last time I talked to GitHub — it only updates when I fetch/pull/push, never just from making commits.

### Connecting to a remote

- `git remote add origin <url>` — connects my local repo to a GitHub repo. Only need to do this once. (If I'd started from `git clone <url>` instead of `git init`, this step is already done for me automatically.)

### Pushing and pulling

- `git push -u origin master` — uploads my commits to GitHub. The `-u` is the important bit the first time: it links my local `master` branch to `origin/master` so that afterwards I can just type `git push` / `git pull` with no arguments and Git already knows where to send/receive from.
- After that first push, it's just `git push` to send new commits up, and `git pull` to bring down + merge new commits from GitHub into my current branch.

Worth knowing the difference between pull and fetch:
- `git fetch` grabs new commits from GitHub but doesn't touch my actual branch — it just updates Git's record of where `origin/master` is. Safe way to peek at what changed before committing to merging it in.
- `git pull` = fetch + merge in one step. This is what I use most of the time when I just want to be up to date.

`git push` will refuse to push if GitHub has commits I don't have locally (like if someone else pushed first) — that's Git protecting me from overwriting their work. In that case I need to `git pull` first, sort out anything that conflicts, and then push again.

- `git push --force-with-lease` — the "I know what I'm doing" version of push, for after I've rebased or amended something I already pushed. It overwrites the remote branch, but only if nobody else has pushed to it since I last checked — so it won't blindly stomp on someone else's work like a plain `--force` would.
