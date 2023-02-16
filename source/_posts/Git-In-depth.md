---
title: Git In-depth
date: 2019-07-20 11:16:30
categories: 学习笔记
tags: [FrontEndMasters, Git, GitHub]
---

## Git In-Depth

### Introduction

By coding along with us in this workshop you’ll:

通过与我们一起编写代码，您将:

+ Learn how git represents commits and branches internally to gain deep insights into how git works under the hood…
+ 了解git如何在内部表示提交和分支，从而深入了解git的底层工作原理……
+ Discover the power of rebasing in git, along with how to avoid common pitfalls. Learn to tell when it’s appropriate to re-write history…
+ 了解在git中重新建立基础的强大功能，以及如何避免常见的陷阱。学会分辨什么时候改写历史是合适的……
+ Discover how git hooks can make your life easier by running analysis on your code before you commit in order to prevent common errors from making their way into your codebase…
+ 了解git钩子是如何让您的工作变得更简单的，在您提交代码之前运行对代码的分析，以防止常见错误进入您的代码库……
+ Discover lesser-known features of git that will save you hours of time when resolving merge conflicts and track down bugs…
+ 发现git不太为人所知的特性，这将为您节省几个小时的时间来解决合并冲突和跟踪bug…
+ Learn how to harness the power of the GitHub API to fetch information about your repositories.
+ 学习如何利用GitHub API的强大功能来获取关于存储库的信息。
<!-- more -->
#### Requirements

+ command line that supports unix style commands
+ git version > 2.0(check with `git --version`)
+ github.com account
+ clone [git.io/advanced-git](https://github.com/nnja/advanced-git) for slides and exercises
+ (oxs only) install homebrew - [https://brew.sh/](https://brew.sh/)

### Course Agenda

![xkcd: git](https://www.explainxkcd.com/wiki/images/4/4d/git.png)

Class Overview

+ What is Git?
+ Working Area, Staging Area, Repository
+ Staging Area Deep Dive
+ References, Commits, Branches
+ Stashing
+ Merging + Rebasing
+ History + Diffs
+ Fixing Mistakes
+ Rebase
+ Forks, Remote Reponsitories
+ The Danger Zone
+ Advanced Tools
+ Customization - Config, Ignore, Hooks, Templates
+ Social Git: Integrating GitHub with CI Tools
+ Advanced GitHub: Working with the GitHub API

## Git Foundations

### Data Storage

#### WHAT IS GIT

Distributed Version Control System

#### HOW DOES GIT STORE INFORMATION

+ At its core, git is like a key value store.
+ The Value = Data
+ The Key = Hash of the Data
+ You can use the key to retrieve the content.

#### The Key - SHA1

+ Is a cryptographic hash function.
+ 是一个加密哈希函数。
+ Given a piece of data, it produces a 40-digit hexadecimal number.
+ 给定一段数据，它产生一个40位的十六进制数。
+ This value should always be the same if the given input is the same.
+ 如果给定的输入是相同的，那么这个值应该总是相同的。

### Git Blobs & Trees

#### The Value - Blob

+ git stores the compressed data in a blob, along with metadata in a header:
  + the identifier blob
  + the size of the content \0 delimiter
  + content

#### UNDER THE HOOD - GIT HASH-OBJECT

Asking Git for the SHA1 of contents:
`echo 'Hello, World!' | git hash-object --stdin`
`8ab686eafeb1f44702738c8b0f24f2567c36da6d`

Generating the SHA1 of the contents, with metadata:
`echo 'blob 14\0Hello, World!' | openssl sha1`
`8ab686eafeb1f44702738c8b0f24f2567c36da6d`

It’s a Match!

8ab68...

| blob          | 14  |
| ------------- | --- |
| \0            |
| Hello, World! |

#### WHERE DOES GIT STORE ITS DATA

```bash
~/projects/sample
git init
Initialized empty Git repository in /Users/nina/projects/sample/.git/
```

.git directory contains data about our repository

#### WHERE ARE BLOBS STORED

```bash
❯ echo 'Hello, World!' | git hash-object -w --stdin
8ab686eafeb1f44702738c8b0f24f2567c36da6d
```

```bash
❯ rm -rf .git/hooks

❯ tree .git .git
├── HEAD
├── config
├── description ├── info
│ └── exclude
├── objects                 # our blob is stored in objects
│ ├──8a                     # the directory starts with the first 2 chars of the hash.
│ │ └── b686eafeb1f44702738c8b0f24f2567c36da6d
│ ├── info
│ └── pack └── refs
├── heads └── tags
```

#### WE NEED SOMETHING ELSE

The blob is missing information!

+ filenames
+ directory structures

Git stores this information in a tree.

#### TREE

a **tree** contains pointers (using SHA1):

+ to blobs
+ to other trees

and metadata:

+ **type** of pointer (**blob** or **tree**)
+ **filename** or directory name
+ **mode** (executable file, symbolic link, ...)

```bash
.
├── copies
│ └── hello-copy.txt
└── hello.txt
1 directory, 2 files
```

4ad20...

| tree | `<size>` |           |
| ---- | -------- | --------- |
| \0   |          |           |
| blob | 8ab68    | hello.txt |
| tree | a14ca    | copies    |

![TREES POINT TO BLOBS & TREES](/static/images/WX20190716-143824.png)

#### IDENTICAL CONTENT IS ONLY STORED ONCE

在git的存储文件中，tree可以包含了当前文件夹下的blob及相关联的tree，当两个tree指向的blob内容相同时，根据前面提到的，生成的sha1也相同，那么两个不同tree的blob将指向同一个blob。

#### OTHER OPTIMIZATIONS - PACKFILES, DELTAS

+ Git objects are compressed
+ As files change, their contents remain mostly similar.
+ Git optimizes for this by compressing these files together, into a Packfile
+ The Packfile stores the object, and “deltas”, or the differences between one version of the file and the next.
+ Packfiles are generated when:
  + you have too many objects, during gc(garbage collection), or during a push to a remote

### Git Commits

#### COMMIT OBJECT

a commit points to:

+ a tree

and contains metadata:

+ author and committer
+ date
+ message
+ parent commit (one or more)

the SHA1 of the commit is the hash of all this information

fae12...
| commit  | size             |
| ------- | ---------------- |
| tree    | 8ab68            |
| parent  | a14ca            |
| author  | Nina             |
| message | “Initial Commit” |

![COMMITS POINT TO PARENT COMMITS AND TREES](/static/images/WX20190716-145819.png)

#### A COMMIT IS A CODE SNAPSHOT

#### COMMITS UNDER THE HOOD - MAKE A COMMIT

```bash
echo 'Hello World!' > hello.txt
git add hello.txt
git commit -m "Initial commit"

[master (root-commit) adf0e13] Initial commit
1 file changed, 1 insertion(+)
create mode 100644 hello.txt
```

#### COMMITS UNDER THE HOOD - LOOK IN .GIT/OBJECTS

```bash
tree .git/objects

.git/objects
├── 58
│ └── 1caa0fe56cf01dc028cc0b089d364993e046b6
├── 98
│ └── 0a0d5f19a64b4b30a87d4206aade58726b60e3
├── ad # commit tree
│ └── f0e13658d9b4424efe03c3ac3281facf288c13
├── info
└── pack
```

#### COMMITS UNDER THE HOOD - LOOKING AT OBJECTS

```bash
❯ cat .git/objects/98/0a0d5f19a64b4b30a87d4206aade58726b60e3
xKOR04fH/IQHak%
```

oops!
remember, git objects are compressed.

#### GIT CAT-FILE -T (TYPE) AND -P (PRINT THE CONTENTS)

```bash
❯ git cat-file -t 980a0 # -t print the type
blob
```

```bash
git cat-file -p 980a0 #  -p print the contents
Hello World!
```

```bash
❯ git cat-file -t 581caa
tree

❯ git cat-file -p 581caa
100644 blob 980a0d5f19a64b4b30a87d4206aade58726b60e3
hello.txt
```

```bash
❯ git cat-file -t adf0e1
commit

❯ git cat-file -p adf0e1
tree 581caa0fe56cf01dc028cc0b089d364993e046b6
author Nina <nina@nnja.io> 1506214053 -0700
committer Nina <nina@nnja.io> 1506214053 -0700

Initial commit
```

#### WHY CAN’T WE “CHANGE” COMMITS

+ If you change any data about the commit, the commit will have a new SHA1 hash.
+ Even if the files don’t change, the created date will.

#### REFERENCES - POINTERS TO COMMITS

+ Tags
+ Branches
+ HEAD - a pointer to the current commit

#### REFERENCES - UNDER THE HOOD

```bash
❯ tree .git .git
├── HEAD
└── refs
  ├── heads        # refs/heads is where branches live
  │   └── master
  └── tags
```

```bash
❯ git log --oneline
adf0e13 (HEAD -> master) Initial commit

❯ cat .git/refs/heads/master # /refs/heads/master contains which commit the branch points to
adf0e13658d9b4424efe03c3ac3281facf288c13

cat .git/HEAD
ref: refs/heads/master # HEAD is usually a pointer to the current branch
```

### Exercise: Simple Git Commit

1. Configure your editor
   + [git.io/config-editor](git.io/config-editor)
2. Complete exercise
   + [https://github.com/nnja/advanced-git/blob/master/exercises/Exercise1-SimpleCommit.md](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise1-SimpleCommit.md)
3. Check out cheat sheet for using less
   + git.io/use-less

Exercise:

+ Create a new folder and initialize it as a git repo

```bash
mkdir ./sample
cd ./sample

git status
fatal: not a git repository (or any of the parent directories): .git

git init
Initialized empty Git repository in /Users/liuyong/Documents/前端练习/front-end-masters/18-Git-In-depth/sample/.git/
```

+ Create a file, stage it, and commit it to your new repo

```bash
echo 'Hello World!' > hello.txt

git add hello.txt

git commit -m "Initial commit"
[master (root-commit) 662b7ce] Initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
```

+ Look at your .git folder, using tree if you have it

```bash
tree .git
.git
├── COMMIT_EDITMSG
├── FETCH_HEAD
├── HEAD
├── branches
├── config
├── description
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── fsmonitor-watchman.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   ├── pre-receive.sample
│   ├── prepare-commit-msg.sample
│   └── update.sample
├── index
├── info
│   └── exclude
├── logs
│   ├── HEAD
│   └── refs
│       └── heads
│           └── master
├── objects
│   ├── 58
│   │   └── 1caa0fe56cf01dc028cc0b089d364993e046b6
│   ├── 66
│   │   └── 2b7ce25ded7cfc94d4cefdcaf114bc61a2e476
│   ├── 98
│   │   └── 0a0d5f19a64b4b30a87d4206aade58726b60e3
│   ├── info
│   └── pack
└── refs
    ├── heads
    │   └── master
    └── tags

15 directories, 24 files
```

+ Inspect the objects in your .git/objects folder using git cat-file. See if you can find the tree, blob, and commit objects for your recent commit.

```bash
git cat-file -t 581caa
tree

git cat-file -p 581caa
100644 blob 980a0d5f19a64b4b30a87d4206aade58726b60e3    hello.txt


git cat-file -t 662b7
commit

git cat-file -p 662b7
tree 581caa0fe56cf01dc028cc0b089d364993e046b6
author liuyong <564447023@qq.com> 1563329855 +0800
committer liuyong <564447023@qq.com> 1563329855 +0800

Initial commit


git cat-file -t 980a0
blob

git cat-file -p 980a0
Hello World!
```

+ Look at your .git/HEAD and .git/refs/heads/master files and see if you can figure out where these references are pointing to.

```bash
cat .git/HEAD
ref: refs/heads/master

cat .git/refs/heads/master
662b7ce25ded7cfc94d4cefdcaf114bc61a2e476

git log --oneline
662b7ce (HEAD -> master) Initial commit

git branch new_branch

tree .git/refs
.git/refs
├── heads
│   ├── master
│   └── new_branch
└── tags

2 directories, 2 files
```

## Git Areas & Stashing

### Working Area, Staging Area, Repository

#### THREE AREAS WHERE CODE LIVES

1. Working Area
2. Staging Area
3. Repository

#### THE WORKING AREA(A.K.A WORKING TREE)

+ The files in your working area that are also not in the staging are are not handled by git.
+ Also called **untracked files**

#### THE STAGING AREA (A.K.A INDEX, CACHE)

+ What files are going to be part of the next commit
+ The staging area is how git knows what will change between the current commit and the next commit.

#### THE REPOSITORY

+ The files git knows about!
+ Contains all of your commits

#### CLOSER LOOK: THE STAGING AREA

##### THE STAGING AREA

+ The staging area is how git knows what will change between the current commit and the next commit.
+ Tip: a “clean” staging area isn’t empty!
+ Consider the baseline staging area as being an exact copy of the latest commit.

```bash
❯ git ls-files -s # The plumbing command ls-files -s will show you what’s in the staging area.
100644 b8b2583b242add97d513d8d8b85a46b9b5fb29cb 0 index.txt
100644 ed52afd72f64b1f8575e81bb4cb02ef1215739f6 0 posts/
welcome.txt
```

##### MOVING FILES IN & OUT OF THE STAGING AREA

+ add a file to the next commit:
  + `git add <file>`
+ delete a file in the next commit:
  + `git rm <file>`
+ rename a file in the next commit:
  + `git mv <file>`

##### GIT ADD -P

+ one of my favorite tools
+ allows you to stage commits in hunks
+ interactively!
+ It’s especially useful if you’ve done too much work for one commit.

##### USING GIT ADD -P

```bash
❯ git add -p
.
.
.
diff displayed
.
. .
Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]? ?
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
k - leave this hunk undecided, see previous undecided hunk
K - leave this hunk undecided, see previous hunk
s - split the current hunk into smaller hunks
e - manually edit the current hunk
? - print help
```

##### “UNSTAGE” FILES FROM THE STAGING AREA

+ Not removing the files.
+ You’re replacing them with a copy that’s currently in the repository.

##### BASICS: HOW CONTENT MOVES IN GIT

![HOW CONTENT MOVES IN GIT](/static/images/WX20190717-110640.png)

### Stashing

#### GIT STASH

+ Save un-committed work.
+ The stash is safe from destructive operations.

#### GIT STASH - BASIC USE

+ stash changes
  + `git stash`
+ list changes
  + `git stash list`
+ show the contents
  + `git stash show stash@{0}`
+ apply the last stash
  + `git stash apply`
+ apply a specific stash
  + `git stash apply stash@{0}`

#### ADVANCED STASHING - KEEPING FILES

+ Keep untracked files
  + `git stash --include-untracked`
+ Keep all files (even ignored ones!)
  + `git stash --all`

#### ADVANCED STASHING - OPERATIONS

+ Name stashes for easy reference
  + `git stash save "WIP: making progress on foo"`
+ Start a new branch from a stash
  + `git stash branch <optional stash name>`
+ Grab a single file from a stash
  + `git checkout <stash name> -- <filename>`

#### ADVANCED STASHING - CLEANING THE STASH

+ Remove the last stash and applying changes:
  + `git stash pop`
  + tip: doesn’t remove if there’s a merge conflict
+ Remove the last stash
  + `git stash drop`
+ Remove the nth stash
  + `git stash drop stash@{n}`
+ Remove all stashes
  + `git stash clear`

#### EXAMINING STASH CONTENTS - GIT SHOW

```bash
❯ git stash list
stash@{0}: WIP on master: 9703ef3 example
stash@{1}: WIP on example3: 0be5e31 hello
stash@{2}: WIP on example3: 0be5e31 a commit

❯ git stash show stash@{2}

 goodbye.txt  | 1 +
 hello.txt | 2 +-
 2 files changed, 2 insertions(+), 1 deletion(-)
```

#### GIT STASH - WHAT I USE

+ Keep untracked files
  + `git stash --include-untracked`
+ Name stashes for easy reference
  + `git stash save "WIP: making progress on foo"`

### Exercise: Staging & Stashing

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise2-StagingAndStashing.md)

```bash
git clone git@github.com:nnja/advanced-git-exercises.git

cd advanced-git-exercises/

git checkout exercise2
Branch 'exercise2' set up to track remote branch 'exercise2' from 'origin'.
Switched to a new branch 'exercise2'
```

+ Use git ls-files -s to view the contents of the staging area.

```bash
git ls-files -s
100644 980a0d5f19a64b4b30a87d4206aade58726b60e3 0       hello.txt
```

+ Make a change and try to stage it interactively (`git add -p`).

```bash
echo "This is a test of the emergency git-casting system" >> hello.txt


git add -p

diff --git a/hello.txt b/hello.txt
index 980a0d5..424b148 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1 +1,2 @@
 Hello World!
+This is a test of the emergency git-casting system
Stage this hunk [y,n,q,a,d,e,?]? # press Enter


y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
e - manually edit the current hunk
? - print help
@@ -1 +1,2 @@
 Hello World!
+This is a test of the emergency git-casting system
Stage this hunk [y,n,q,a,d,e,?]? y # press y and enter to stage this hunk


git status
On branch exercise2
Your branch is up to date with 'origin/exercise2'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   hello.txt


git ls-files -s
100644 424b14840bb04f45db15b4a1a72a3480ce1f2060 0       hello.txt
```

+ Use `git reset` to undo the staging of your file.

```bash
git reset hello.txt

Unstaged changes after reset:
M       hello.txt



git status

On branch exercise2
Your branch is up to date with 'origin/exercise2'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

+ Stash your change with a unique stash message, then unstash and apply it back to the exercise2 branch.

```bash
git stash save "emergency git-casting"

Saved working directory and index state On exercise2: emergency git-casting


git status

On branch exercise2
Your branch is up to date with 'origin/exercise2'.

nothing to commit, working tree clean


git stash list

stash@{0}: On exercise2: emergency git-casting


git stash show stash@{0}

 hello.txt | 1 +
 1 file changed, 1 insertion(+)


git stash apply stash@{0}

On branch exercise2
Your branch is up to date with 'origin/exercise2'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")


git diff hello.txt

diff --git a/hello.txt b/hello.txt
index 980a0d5..424b148 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1 +1,2 @@
 Hello World!
+This is a test of the emergency git-casting system
```

## References, Commits, Branches

### References(pointers to commits)

#### THREE TYPES OF GIT REFERENCES

+ Tags & Annotated Tags
+ Branches
+ HEAD

#### WHAT’S A BRANCH

+ A branch is just a pointer to a particular commit.
+ The pointer of the current branch changes as new commits are made.

#### WHAT IS HEAD

+ HEAD is how git knows what branch you’re currently on, and what the next parent will be.
+ It’s a pointer.
  + It usually points at the name of the current branch.
  + But, it can point at a commit too (detached HEAD).
+ It moves when:
  + You make a commit in the currently active branch
  + When you checkout a new branch

```bash
❯ git checkout master

❯ cat .git/HEAD
ref: refs/heads/master

❯ git checkout feature
Switched to branch 'feature'

❯ cat .git/HEAD
ref: refs/heads/feature
```

#### SAMPLE REPO - A SIMPLE BLOG

+ We’re working on a simple blog.
+ index.txt contains links to our entries, and post titles
+ the posts/ directory contains our blog entries.

```bash
❯ mkdir posts
❯ echo 'This is my very first blog entry.'> posts/welcome.txt
❯ echo 'welcome.txt Welcome to my blog' > index.txt
```

#### CURRENT REPO STATE

```bash
❯ git init
❯ git add .
❯ git commit -m "Initial commit"
[master (root-commit) cd0b57c] Initial commit
 2 files changed, 2 insertions(+)
 create mode 100644 index.txt
 create mode 100644 posts/welcome.txt
```

```bash
❯ git checkout -b tech_posts
Switched to a new branch 'tech_posts'
```

```bash
❯ git add posts/python.txt
❯ git commit -m "New blog post about python"
[tech_posts 2b0b3f2] New blog post about python
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 posts/python.txt
```

### Tags & Annotated Tags

#### LIGHTWEIGHT TAGS

+ Lightweight tags are just a simple pointer to a commit.
+ When you create a tag with no arguments, it captures the value in HEAD

```bash
❯ git checkout master
Switched to branch 'master'
❯ git tag my-first-commit
```

#### ANNOTATED TAGS: GIT TAG -A

+ Point to a commit, but store additional information.
  + Author, message, date.

```bash
❯ git tag -a v1.0 -m "Version 1.0 of my blog"
❯ git tag my-first-commit v1.0
❯ git show v1.0
tag v1.0
Tagger: Nina Zakharenko <nina@nnja.io>
Date: Sun Sep 24 17:01:21 2017 -0700
Version 1.0 of my blog
```

#### WORKING WITH TAGS

+ List all the tags in a repo
  + `git tag`
+ List all tags, and what commit they’re pointing to
  + `git show-ref --tags`
+ List all the tags pointing at a commit
  + `git tag --points-at <commit>`
+ Looking at the tag, or tagged contents:
  + `git show <tag-name>`

#### TAGS & BRANCHES

+ Branch
  + The current branch pointer moves with every commit to the repository
+ Tag
  + The commit that a tag points doesn’t change. It’s a snapshot!

### Detached Head & Dangling Commits

#### HEAD-LESS / DETACHED HEAD

+ Sometimes you need to checkout a specific commit (or tag) instead of a branch.
+ git moves the HEAD pointer to that commit
+ as soon as you checkout a different branch or commit, the value of HEAD will point to the new SHA
+ There is no reference pointing to the commits you made in a detached state.

```bash
❯ git checkout cd0b57
Note: checking out 'cd0b57'.

You are in 'detached HEAD' state. You can
look around, make experimental
changes and commit them, and you can
discard any commits you make in this
state without impacting any branches by
performing another checkout.
```

```bash
❯ git add posts/second-post.txt
❯ git commit -m "My second post"
[detached HEAD 1f6ee83] My second post
 1 file changed, 1 insertion(+)
 create mode 100644 posts/second-post.txt
```

+ Save your work:
  + Create a new branch that points to the last commit you made in a detached state.
    + g`it branch <new-branch-name> <commit>`
  + Why the last commit?
    + Because the other commits point to their parents.

#### DANGLING COMMITS

+ Discard your work:
  + If you don’t point a new branch at those commits, they will no longer be referenced in git. (dangling commits)
  + Eventually, they will be garbage collected.

```bash
❯ git checkout master
Warning: you are leaving 1 commit behind,
not connected to any of your branches:
  1f6ee83 My second post
```

### Exercise: References

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise3-References.md)

```bash
git checkout exercise3
Branch 'exercise3' set up to track remote branch 'exercise3' from 'origin'.
Switched to a new branch 'exercise3'
```

+ Check the value of your HEAD variable (hint: look in .git) and confirm you're pointed at the exercise3 branch.

```bash
cat .git/HEAD

ref: refs/heads/exercise3


git branch

  exercise2
* exercise3
  master
```

+ Use show-ref to look at your other heads.

```bash
git show-ref --heads

43388fee19744e8893467331d7853a6475a227b8 refs/heads/exercise2
e348ebc1187cb3b4066b1e9432a614b464bf9d07 refs/heads/exercise3
43388fee19744e8893467331d7853a6475a227b8 refs/heads/master


git cat-file -p 43388fee # You can see for yourself that our master branch is pointing to our "Initial commit"

tree 581caa0fe56cf01dc028cc0b089d364993e046b6
author Nina Zakharenko <nina@nnja.io> 1507168309 -0700
committer Nina Zakharenko <nina@nnja.io> 1507168309 -0700

Initial commit


cat-file -p e348ebc # Whereas our exercise3 branch is pointing to our newer commit from Exercise 2:

tree cbcdf5dda7853d595fe0b1942cb0d1d72eb910f3
parent 43388fee19744e8893467331d7853a6475a227b8
author Nina Zakharenko <nina@nnja.io> 1507168872 -0700
committer Nina Zakharenko <nina@nnja.io> 1507168872 -0700

Testing the emergency git-casting system
```

+ Create a lightweight tag and confirm that it's pointing at the right commit.

```bash
git tag my-exercise3-tag


git show-ref --tags

e348ebc1187cb3b4066b1e9432a614b464bf9d07 refs/tags/my-exercise3-tag


git tag --points-at e348ebc

my-exercise3-tag
```

+ Create an annotated tag, and use `git show` to see more information about it.

```bash
git tag -a "exercise3-annotated-tag" -m "This is my annotated tag for exercise 3"


git show exercise3-annotated-tag

tag exercise3-annotated-tag
Tagger: liuyong <564447023@qq.com>
Date:   Wed Jul 17 21:49:21 2019 +0800

This is my annotated tag for exercise 3

commit e348ebc1187cb3b4066b1e9432a614b464bf9d07 (HEAD -> exercise3, tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3)
Author: Nina Zakharenko <nina@nnja.io>
Date:   Wed Oct 4 19:01:12 2017 -0700

    Testing the emergency git-casting system

diff --git a/hello.txt b/hello.txt
index 980a0d5..b31a35b 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1 +1,2 @@
 Hello World!
+This is a test of the emergency git-casting system.
```

+ Get into a "detached HEAD" state by checking out a specific commit, then confirm that your HEAD is pointing at this commit rather than at a branch.

```bash
git log --oneline

e348ebc (HEAD -> exercise3, tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3) Testing the emergency git-casting system
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit


git checkout e348ebc

Note: checking out 'e348ebc'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at e348ebc Testing the emergency git-casting system


cat .git/HEAD

e348ebc1187cb3b4066b1e9432a614b464bf9d07
```

+ Make a new commit, then switch branches to confirm that you're leaving a commit behind.

```bash
echo "This is a test file" > dangle.txt

git add dangle.txt


git commit -m "This is a dangling commit"

[detached HEAD 68185fb] This is a dangling commit
 1 file changed, 1 insertion(+)
 create mode 100644 dangle.txt


git log --oneline

68185fb (HEAD) This is a dangling commit
e348ebc (tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3) Testing the emergency git-casting system
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit


cat .git/HEAD

68185fb235972ea0024d43ba68cf43018d5608b0


git checkout exercise3

Warning: you are leaving 1 commit behind, not connected to
any of your branches:

  68185fb This is a dangling commit

If you want to keep it by creating a new branch, this may be a good time
to do so with:

 git branch <new-branch-name> 68185fb

Switched to branch 'exercise3'
Your branch is up to date with 'origin/exercise3'.
```

## Merging & Rebasing

### Merging & Fast Forward

#### UNDER THE HOOD - MERGE COMMITS ARE JUST COMMITS

```bash
❯ git cat-file -p 649efc8c9
tree 8c90eb0e4af7e0b05f736c933dc55e14e45031f2
parent 14176d1d63b6a85308fd61a9a20c8aeed398043b
parent 1ea4e219580910921dce70e96e5da96f12f5066b
author Kenneth Reitz <me@kennethreitz.org> 1499378257 -0400
committer Kenneth Reitz <me@kennethreitz.org> 1499378257 -0400
Merge branch 'master' of github.com:kennethreitz/requests
```

#### FAST FORWARD

```bash
git checkout master
❯ git merge feature Updating 2733233..9703930
Fast-forward
 index.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Fast-forward happens when there are no commits on the base branch that occurred after the feature branch was created.

#### GIT MERGE --NO-FF (NO FAST FORWARD)

+ To retain the history of a merge commit, even if there are no changes to the base branch:
  + use `git merge --no-ff`
+ This will *force* a merge commit, even when one isn’t necessary.

```bash
git checkout master

git merge new_feature --no-ff
```

### Merge Conflicts

+ Attempt to merge, but files have diverged.
+ Git stops until the conflicts are resolved.

```bash
git merge feature

Auto-merging feature
CONFLICT (add/add): Merge conflict in feature
```

#### GIT RERERE - REUSE RECORDED RESOLUTION

+ git saves how you resolved a conflict
+ next conflict: reuse the same resolution

+ useful for:
  + long lived feature branch (like a refactor)
  + rebasing

+ Turn it on:
  + `git config rerere.enabled true`
  + use --global flag to enable for all projects

```bash
❯ git config rerere.enabled true
❯ git checkout master
❯ git merge feature
Auto-merging feature
CONFLICT (add/add): Merge conflict in file
Recorded preimage for 'file'
Automatic merge failed; fix conflicts and then commit the result.
### Fix the merge conflict in file.
❯ git add file
❯ git commit -m "Resolve conflict"
Recorded resolution for 'feature'.
[master 0fe266d] Resolve conflict
```

> When I commit the conflict resolution, it’s **recorded**.

+ Now: Feature wasn’t ready, I undid the merge.
+ When I try to merge again:

```bash
❯ git merge feature
Auto-merging feature
CONFLICT (add/add): Merge conflict in feature
Resolved 'feature' using previous resolution.
Automatic merge failed; fix conflicts and then commit the result.

❯ git add file

❯ git diff --staged
diff --git a/file b/file
index 587be6b..a354eda 100644
--- a/file
+++ b/file
@@ -1 +1 @@
-The old change
+This is how I resolved my conflict.
\ No newline at end of file
```

> The resolution is **automatically reapplied**.

### Exercise: Merging & ReReRe

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise4-MergingAndReReRe.md)

```bash
git checkout exercise4

Branch 'exercise4' set up to track remote branch 'exercise4' from 'origin'.
Switched to a new branch 'exercise4'
```

+ Merge the exercise3 branch into exercise4, and look at the git log.

```bash
git merge exercise3

Updating 43388fe..e348ebc
Fast-forward
hello.txt | 1 +
1 file changed, 1 insertion(+)


git log --oneline

e348ebc (HEAD -> exercise4, tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3) Testing the emergency git-casting system
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit
```

+ Use git reset --hard to reset your exercise4 branch back one commit, then use the --no-ff option to git merge to merge exercise3 again. Look at the git log, how is it different from the last step?

```bash
git reset --hard HEAD^

HEAD is now at 43388fe Initial commit

git merge --no-ff exercise3

Merge made by the 'recursive' strategy.
 hello.txt | 1 +
 1 file changed, 1 insertion(+)


git log --graph

*   commit fff6b444c433d7f5f903da489e5f80e0dc6f17d0 (HEAD -> exercise4)
|\  Merge: 43388fe e348ebc
| | Author: liuyong <564447023@qq.com>
| | Date:   Thu Jul 18 10:54:00 2019 +0800
| |
| |     Merge branch 'exercise3' into exercise4
| |
| * commit e348ebc1187cb3b4066b1e9432a614b464bf9d07 (tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3)
|/  Author: Nina Zakharenko <nina@nnja.io>
|   Date:   Wed Oct 4 19:01:12 2017 -0700
|
|       Testing the emergency git-casting system
|
* commit 43388fee19744e8893467331d7853a6475a227b8 (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2)
  Author: Nina Zakharenko <nina@nnja.io>
  Date:   Wed Oct 4 18:51:49 2017 -0700
  
      Initial commit
(END)
```

+ Make two conflicting changes to hello.txt in two different branches.

```bash
git branch

  exercise2
  exercise3
* exercise4
  master


git checkout -b mundo

Switched to a new branch 'mundo'


git branch

  exercise2
  exercise3
  exercise4
  master
* mundo


git status

On branch mundo
nothing to commit, working tree clean


git add hello.txt

git commit -m "Changing World to Mundo"
On branch mundo
nothing to commit, working tree clean


git checkout exercise4

vi hello.txt # Edit hello.txt to say "Hola World!"

git add hello.txt

git commit -m "changing Hello to Hola"
[exercise4 4955141] changing Hello to Hola
 1 file changed, 1 insertion(+), 1 deletion(-)
```

+ Enable ReReRe, then merge one branch into the other.

```bash
git config rerere.enabled true

git merge mundo
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Recorded preimage for 'hello.txt'
Automatic merge failed; fix conflicts and then commit the result.

git rerere diff
--- a/hello.txt
+++ b/hello.txt
@@ -1,6 +1,6 @@
-<<<<<<<
-Hello Mundo!
-=======
+<<<<<<< HEAD
 Hola World!
->>>>>>>
+=======
+Hello Mundo!
+>>>>>>> mundo
 This is a test of the emergency git-casting system.

git add hello.txt

git commit -m "Merging in mundo branch"
[exercise4 ff91b70] Merging in mundo branch
```

+ Backup again with git reset --hard, then attempt the merge again. Notice how ReReRe automatically resolves the conflict for you.

```bash
git reset --hard HEAD^
HEAD is now at fff6b44 Merge branch 'exercise3' into exercise4

git merge mundo
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Resolved 'hello.txt' using previous resolution.
Automatic merge failed; fix conflicts and then commit the result.

cat hello.txt
Hola Mundo!
This is a test of the emergency git-casting system.
```

## History & Diffs

### Useful Commit Message

#### BAD COMMIT MESSAGES

![bad commits](https://imgs.xkcd.com/comics/git_commit.png)

#### GOOD COMMITS ARE IMPORTANT

+ Good commits help preserve the history of a code base.
+ They help with:
  + debugging & troubleshooting creating release notes
  + code reviews
  + rolling back
  + associating the code with an issue or ticket

> Commit message is in future tense. ‘Fix’ vs ‘Fixed’
> Short subject, followed by a blank line.

```bash
git-rebase: don't ignore unexpected command line arguments

Currently, git-rebase will silently ignore any unexpected command-line
switches and arguments (the command-line produced by git rev-parse).
This allowed the rev-parse bug, fixed in the preceding commits, to go
unnoticed.  Let's make sure that doesn't happen again.  We shouldn't be
ignoring unexpected arguments.  Let's not.
```

> A description of the current behavior, a short summary of why the fix is needed. Mention side effects.
> The description is broken into 72 char lines.

#### ANATOMY OF A GOOD COMMIT

+ Good commit message
+ Encapsulates one logical idea
+ Doesn’t introduce breaking changes
  + i.e. tests pass

### Git Log

+ `git log` - the basic command that shows you the history of your repository

```bash
❯ git log
commit 25b3810089709394636412adf3d887cb55cb47eb (HEAD -> test)
Author: Nina Zakharenko <nina@nnja.io>
Date: Wed Sep 27 22:46:38 2017 -0700

    Add a README file to the project

commit cd0b57c21487871b6353a58366414b72f2e4ee22 (tag: v1.0, tag: my-first-commit)
Author: Nina Zakharenko <nina@nnja.io>
Date:   Sun Sep 24 14:44:28 2017 -0700

    Initial commit
```

#### GIT LOG --SINCE

+ The site is slow. What changed since yesterday?
  + `git log --since="yesterday"`
  + `git log --since="2 weeks ago"`

#### GIT LOG --FOLLOW

+ Log files that have been moved or renamed
  + `git log --name-status --follow -- <file>`

#### GIT LOG --GREP

+ Search for commit messages that match a regular expression:
  + `git log —grep <regexp>`
  + Can be mixed & matched with other git flags.
+ Example:
  + `git log --grep=mail --author=nina --since=2.weeks`

#### GIT LOG DIFF-FILTER

+ Selectively include or exclude files that have been:
+ (A)dded, (D)eleted, (M)odified & more...

```bash
> git log --diff-filter=R --stat
5e1ced3 make session serializer extensible support serializing 1-item dicts
with tag as key refactor serializer into flask.json.tag module continues
#1452, closes #1438, closes #1908
 flask/{json.py => json/__init__.py} | 13 ++-----------
 1 file changed, 2 insertions(+), 11 deletions(-)
8cf32bc Adds in blueprints and an application factory
 examples/flaskr/flaskr/{ => blueprints}/flaskr.py | 55 ++++++++++++++
+----------------------------------------
 1 file changed, 15 insertions(+), 40 deletions(-)
92fa444 Moves largerapp into patterns dir and add test
 examples/{ => patterns}/largerapp/setup.py
 examples/{ => patterns}/largerapp/yourapplication/__init__.py
 examples/{ => patterns}/largerapp/yourapplication/static/style.css
 examples/{ => patterns}/largerapp/yourapplication/templates/index.html  | 0
```

#### GIT LOG: REFERENCING COMMITS

+ `^` or `^n`
  + no args: ==^1: the first parent commit
  + n: the nth parent commit
+ `~` or `~n`
  + no args: == ~1: the first commit back, following 1st parent
  + n: number of commits back, following only 1st parent

> note: `^` and `~` can be combined

#### REFERENCING COMMITS

+ Both commit nodes B and C are parents of commit node A.
  Parent commits are ordered left-to-right.
+ A is the latest commit.

### Git Show & Diffs

#### GIT SHOW: LOOK AT A COMMIT

+ show commit and contents:
  + `git show <commit>`
+ show files changed in commit:
  + `git show <commit> --stat`
+ look at a file from another commit:
  + `git show <commit>:<file>`

#### DIFF

+ Diff shows you changes:
  + between commits
  + between the staging area and the repository
  + what’s in the working area

+ unstaged changes
  + `git diff`
+ staged changes
  + `git diff --staged`

#### “DIFF” BRANCHES

+ Which branches are merged with master, and can be cleaned up?
  + `git branch --merged master`

+ Which branches aren’t merged with master yet?
  + `git branch --no-merged master`

### Exercise: History & Diffs

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise5-HistoryAndDiffs.md)

```bash
git checkout exercise5
Branch 'exercise5' set up to track remote branch 'exercise5' from 'origin'.
Switched to a new branch 'exercise5'
```

+ Practice creating a well-crafted commit - look at the format given on the slides for help.

```bash
vi hello.txt # Change your `hello.txt` to say [greeting] [noun]!

cat hello.txt
[greeting] [noun]!
This is a test of the emergency git-casting system.

git mv hello.txt hello.template # Rename hello.txt to hello.template

git add hello.template

git commit
# This will open your text editor
# Type the following...
Replacing greeting with tokens for i18n

Currently, hello.txt contains both Spanish and English.
Let's replace Hola with a [greeting] token, and Mundo
with a [noun] token. That way, we can localize hello.txt for
any language!

# Save and exit your editor

[exercise5 4b2b90e] Replacing greeting with tokens for i18n
 1 file changed, 1 insertion(+), 1 deletion(-)
```

+ Use `git log` to find commits created since yesterday. Rename a file and use the `--name-status` and `--follow` options to `git log` to track down when the file was renamed, and what it used to be called. Use `--grep` to search within commit messages, and --diff-filter to find renamed and modified files from `git log`.

```bash
git log --since="yesterday"
commit 145d1acd6490c80b3549f4cb2ec0f95ec5ffec5c (HEAD -> exercise5)
Author: liuyong <564447023@qq.com>
Date:   Thu Jul 18 14:32:41 2019 +0800

    Replacing greeting with tokens for i18n

    Currently, hello.txt contains both Spanish and English.
    Let\'s replace Hola with a [greeting] token, and Mundo
    with a [noun] token. That way, we can localize hello.txt for
    any language!

commit 268a5e7a09a42973a84d93162c9022ccda45ad02
Author: liuyong <564447023@qq.com>
Date:   Thu Jul 18 14:28:00 2019 +0800

    rename file


git log --name-status --follow --oneline hello.template
145d1ac (HEAD -> exercise5) Replacing greeting with tokens for i18n
M       hello.template
268a5e7 rename file
R073    hello.txt       hello.template
fec9e7b Changing Hello to Hola
M       hello.txt
afa34a6 Changing World to Mundo
M       hello.txt
e348ebc (tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3) Testing the emergency git-casting system
M       hello.txt
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit
A       hello.txt

git log --diff-filter=R --find-renames
commit 268a5e7a09a42973a84d93162c9022ccda45ad02
Author: liuyong <564447023@qq.com>
Date:   Thu Jul 18 14:28:00 2019 +0800

    rename file

git log --diff-filter=M --oneline
145d1ac (HEAD -> exercise5) Replacing greeting with tokens for i18n
fec9e7b Changing Hello to Hola
afa34a6 Changing World to Mundo
e348ebc (tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3) Testing the emergency git-casting system
```

+ Use `git show` to get more information about a specific git hash.

```bash
git log --grep=i18n --oneline
145d1ac (HEAD -> exercise5) Replacing greeting with tokens for i18n

git show 145d1ac
commit 145d1acd6490c80b3549f4cb2ec0f95ec5ffec5c (HEAD -> exercise5)
Author: liuyong <564447023@qq.com>
Date:   Thu Jul 18 14:32:41 2019 +0800

    Replacing greeting with tokens for i18n

    Currently, hello.txt contains both Spanish and English.
    Let\'s replace Hola with a [greeting] token, and Mundo
    with a [noun] token. That way, we can localize hello.txt for
    any language!

diff --git a/hello.template b/hello.template
index a6c57ac..03686d4 100644
--- a/hello.template
+++ b/hello.template
@@ -1,2 +1,2 @@
 [greeting] [noun]!
-This is a test of the emergency git-casting system.
+

git show 145d1ac --stat --oneline
145d1ac (HEAD -> exercise5) Replacing greeting with tokens for i18n
 hello.template | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

+ Try the `--merged` and `--no-merged` options to git branch to see which branches have been merged into master (or not).

```bash
git branch --merged master
  exercise2
  master

git branch --no-merged master
  exercise3
  exercise4
* exercise5
  mundo
```

## Fixing Mistakes

### Git Checkout

Restore working tree files or switch branches

#### WHAT HAPPENS WHEN YOU GIT CHECKOUT A BRANCH

1. Change HEAD to point to the new branch
2. Copy the commit snapshot to the staging area
3. Update the working area with the branch contents

#### WHAT HAPPENS WHEN YOU GIT CHECKOUT - - FILE

Replace the working area copy with the version from the current staging area

> Warning: This operation overwrites files in the working directory without warning!

#### GIT CHECKOUT: OVERWRITE FILES WITH STAGING AREA COPY

+ Overwrite the working area file with the staging area version from the last commit
  + `git checkout -- <file_path>`

> Warning: This operation overwrites files in the working directory without warning!
<!-- markdownlint-disable md033 -->
#### WHAT HAPPENS WHEN YOU GIT CHECKOUT <COMMIT> - - FILE

1. Update the staging area to match the commit
2. Update the working area to match the staging area.

> Warning: This operation overwrites files in the working directory without warning!

#### GIT CHECKOUT: FROM A SPECIFIC COMMIT

+ Checkout a file from a specific commit
  + copies to both working area & staging area
  + `git checkout <commit> -- <file_path>`

+ Restore a deleted file
  + `git checkout <deleting_commit>^ -- <file_path>`

> Warning: This operation overwrites files in the working directory without warning!

### Git Clean & Reset

#### GIT CLEAN

+ Git clean will clear your working area by deleting untracked files.
  > Warning: this operation cannot be undone!
+ Use the `—dry-run` flag to see what would be deleted
  + The `-f` flag to do the deletion
  + The `-d` flag will clean directories

```bash
❯ git clean --dry-run
Would remove a-note.txt

❯ git clean -d --dry-run
Would remove a-note.txt
Would remove scratch/

❯ git clean -d -f
Removing a-note.txt
Removing scratch/

❯ git status
nothing to commit, working tree clean
```

#### GIT RESET

+ Reset is another command that performs different actions depending on the arguments.
  + with a path
  + without a path
  + By default, git performs a `git reset —mixed`
+ For commits:
  + Moves the HEAD pointer, optionally modifies files
+ For file paths:
  + Does not move the HEAD pointer, modifies files

#### RESET --SOFT: MOVE HEAD

```bash
❯ git reset --soft HEAD~
```

#### RESET --SOFT: RESULT

#### RESET --MIXED: MOVE HEAD, COPY FILES TO STAGE

```bash
❯ git reset HEAD~
❯ git reset --mixed HEAD~
```

#### RESET --MIXED: RESULT

#### RESET --HARD: MOVE HEAD, COPY FILES TO STAGE & WORKING

```bash
❯ git reset --hard HEAD~
```

> Warning: this overwrites files and cannot be undone!

#### RESET --HARD: RESULT

> Warning: this overwrites files and cannot be undone!

#### GIT RESET <COMMIT> CHEAT CHEAT

1. Move HEAD and current branch
2. Reset the staging area
3. Reset the working area

```bash
--soft = (1)
--mixed =(1) & (2) (default)
--hard = (1) & (2) & (3)
```

#### DANGER: GIT RESET CAN CHANGE HISTORY

```bash
❯ git reset --soft HEAD~
❯ git commit -m “new commit”
```

> Warning: never push changed history to a shared or public repository!

#### GIT RESET -- <FILE>

```bash
❯ git reset <file>
```

#### GIT RESET -- <FILE>: RESULT

#### GIT RESET <COMMIT> -- <FILE>

```bash
❯ git reset <commit> -- <file>
```

#### GIT RESET <COMMIT> -- <FILE>: RESULT

#### GIT RESET <COMMIT> -- <FILE> CHEAT CHEAT

1. ~~Move HEAD and current branch~~
2. Reset the staging area
3. ~~Reset the working area~~

This operation does not work with flags!

#### UNDO A GIT RESET WITH ORIG_HEAD

+ In case of an accidental `git reset -`
+ Git keeps the previous value of HEAD in variable called `ORIG_HEAD`
+ To go back to the way things were:
  + `git reset ORIG_HEAD`

### Git Revert

#### GIT REVERT - THE “SAFE” RESET

+ Git revert creates a new commit that introduces the opposite changes from the specified commit.
+ The original commit stays in the repository.

+ Tip:
  + Use revert if you’re undoing a commit that has already been shared.
  + Revert does not change history.

#### GIT REVERT - PICK A COMMIT TO UNDO

```bash
❯ git log --oneline
2b0b3f2 (HEAD -> tech_posts) New blog post about python
cd0b57c (tag: v1.0, tag: my-first-commit, master) Initial commit

❯ git show 2b0b3f2
commit 2b0b3f24f3d7e8df809e46eb10c11ba66139acb8 (HEAD -> tech_posts)
Author: Nina Zakharenko <nina@nnja.io>
Date: Sun Sep 24 14:55:35 2017 -0700
    New blog post about python
diff --git a/posts/python.txt b/posts/python.txt
```

```bash
❯ git revert 2b0b3f2
[tech_posts a08a108] Revert "New blog post about python"
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 posts/python.txt

❯ git log --oneline
a08a108 (HEAD -> tech_posts) Revert "New blog post about python"
2b0b3f2 New blog post about python
cd0b57c (tag: v1.0, tag: my-first-commit, master) Initial commit
```

#### SUMMARY: COMMON WAYS OF UNDOING CHANGES

1. checkout
2. reset
3. revert

### Exercise: Fixing Git Mistakes

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise6-FixingMistakes.md)

```bash
git checkout exercise6
Branch 'exercise6' set up to track remote branch 'exercise6' from 'origin'.
Switched to a new branch 'exercise6'
```

+ Make bad changes to a file, then use `git checkout` to fix it. Use `git checkout` to reset your file back to an earlier point in time.

```bash
echo "bad data" > hello.template

cat hello.template
bad data

git checkout -- hello.template

cat hello.template
[greeting] [noun]!
This is a test of the emergency git-casting system.

git log --name-status --follow --oneline hello.template # Let's find the commit where hello.txt was renamed to hello.template
4b2b90e (HEAD -> exercise6, origin/exercise6) Replacing greeting with tokens for i18n
R073    hello.txt       hello.template
fec9e7b Changing Hello to Hola
M       hello.txt
afa34a6 Changing World to Mundo
M       hello.txt
e348ebc (tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3) Testing the emergency git-casting system
M       hello.txt
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit
A       hello.txt


git checkout fec9e7b -- hello.txt # Now let's checkout hello.txt from one commit before then

cat hello.txt
Hola World!
This is a test of the emergency git-casting system.


git reset HEAD hello.txt

git rm hello.template
rm 'hello.template'


git status
On branch exercise6
Your branch is up to date with 'origin/exercise6'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    hello.template

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        hello.txt


git commit -m "Deleting hello.template"
[exercise6 6aca1c7] Deleting hello.template
 1 file changed, 2 deletions(-)
 delete mode 100644 hello.template


git log --diff-filter=D --oneline -- hello.template
6aca1c7 (HEAD -> exercise6) Deleting hello.template
# Ah, it was deleted at 6aca1c7. Let's use the caret (^) syntax to checkout hello.template from one commit before that

git checkout 6aca1c7^ -- hello.template

cat hello.template
[greeting] [noun]!
This is a test of the emergency git-casting system.
```

+ Use git clean to remove untracked files from your repo. Remember to use `--dry-run` first.

```bash
git clean --dry-run
Would remove hello.txt

git clean -f
Removing hello.txt
```

+ Stage a change and then use `git reset` to unstage it. Use `git reset --hard` to reset your branch back pointer, staging area, and working area to an earlier commit. Use "mixed mode" to reset your branch back to an earlier commit, then use `ORIG_HEAD` to reset your branch back to where you were.

```bash
git status
On branch exercise6
Your branch is ahead of 'origin/exercise6' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   hello.template


git reset -- hello.template

git status
On branch exercise6
Your branch is ahead of 'origin/exercise6' by 1 commit.
  (use "git push" to publish your local commits)

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        hello.template

nothing added to commit but untracked files present (use "git add" to track)

git log --oneline # Let's find the commit before we deleted hello.template
6aca1c7 (HEAD -> exercise6) Deleting hello.template
4b2b90e (origin/exercise6) Replacing greeting with tokens for i18n
ff91b70 (origin/exercise5) Merging in mundo branch
fec9e7b Changing Hello to Hola
4582f72 Merge branch 'exercise3' into exercise4
afa34a6 Changing World to Mundo
7ea8b01 Merge branch 'exercise3' into exercise4
e348ebc (tag: my-exercise3-tag, tag: exercise3-annotated-tag, origin/exercise3, exercise3) Testing the emergency git-casting system
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit

rm hello.template # Let's remove the untracked copy of hello.template

git reset 4b2b90e -- hello.template # Reset hello.template back to 4b2b90e
# This will update the staging area with 'hello.template' from that commit.
Unstaged changes after reset:
D       hello.template


git status
On branch exercise6
Your branch is ahead of 'origin/exercise6' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   hello.template

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    hello.template


cat hello.template
cat: hello.template: No such file or directory
# At this point, we could commit the file back into our repository if we wanted to.

git reset --hard HEAD
HEAD is now at 6aca1c7 Deleting hello.template

git log -2 --oneline
6aca1c7 (HEAD -> exercise6) Deleting hello.template
4b2b90e (origin/exercise6) Replacing greeting with tokens for i18n

# Reset our repo back to 4b2b90e
git reset 4b2b90e
Unstaged changes after reset:
D       hello.template

git log -1 --oneline
4b2b90e (HEAD -> exercise6, origin/exercise6) Replacing greeting with tokens for i18n

git reset ORIG_HEAD

git log -1 --oneline
6aca1c7 (HEAD -> exercise6) Deleting hello.template
```

+ Practice using `git revert` to safely revert a commit with a new commit.

```bash
# Let's revert the last commit.
git revert 6aca1c7
[exercise6 65e32b1] Revert "Deleting hello.template"
 1 file changed, 2 insertions(+)
 create mode 100644 hello.template
```

## Rebase & Amend

### Git Amend

#### AMEND A COMMIT

+ Amend is a quick and easy shortcut that lets you make changes to the previous commit.

```bash
❯ cat index.txt
welcome.txt Welcome to my blog
python.txt Why python is my favorite language

❯ git commit -m "Add a blog post about Python"
[tech_posts 4080a79] Add a blog post about Python
 1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 A

❯ git add posts/python.txt

❯ git commit --amend
[tech_posts de53317] Add a blog post about Python
Date: Wed Sep 27 22:12:31 2017 -0700
2 files changed, 1 insertion(+)
create mode 100644 A
create mode 100644 posts/python.txt
```

#### COMMITS CAN’T BE EDITED

+ Remember, commits can’t be edited!
+ A commit is referenced by the SHA of all its data.
+ Even if the tree the commit points to is the same, and the author is the same, the date is still different!
+ A new commit is created.

### Rebase

#### WHAT IS REBASE ANYWAY

+ Imagine our tech_posts and master branch have diverged.
+ We don’t want a messy merge commit in our history.

+ We can pull in all the latest changes from master, and apply our commits on top of them by changing the parent commit of our commits.

Rebase = give a commit a new parent (i.e. a new “base” commit)

#### REBASE: REWINDING HEAD

```bash
❯ git checkout tech_posts Switched to branch 'tech_posts'
❯ git rebase master
First, rewinding head to replay your work on top of it...
Applying: Add a blog post about Python
```

#### REBASE: APPLY NEW COMMITS

```bash
❯ git rebase master
First, rewinding head to replay your work on top of it...
Applying: Add a blog post about Python
```

#### POWER OF REBASING - REPLAY COMMITS

+ Commits can be:
  + edited
  + removed
  + combined
  + re-ordered
  + inserted
+ Before they’re “replayed” on top of the new HEAD.

#### INTERACTIVE REBASE (REBASE -I OR REBASE --INTERACTIVE)

+ Interactive rebase opens an editor with a list of “todos”
  + in the format of: `<command> <commit> <commit msg>`
  + git will pick the commits in the specified order, or stop to take an action when editing or a conflict occurs.

+ interactive rebase with a shortcut:
  + `git rebase -i <commit_to_fix>^`
  + (the ^ specifies the parent commit)

#### REBASE OPTIONS

+ `pick`
  + keep this commit
+ `reword`
  + keep the commit, just change the message
+ `edit`
  + keep the commit, but stop to edit more than the message
+ `squash`
  + combine this commit with the previous one. stop to edit the message
+ `fixup`
  + combine this commit with the previous one. keep the previous commit message
+ `exec`
  + run the command on this line after picking the previous commit
+ `drop`
  + remove the commit (tip: if you remove this line, the commit will be dropped too!)

#### EXAMPLE REBASE

```bash
❯ git log --oneline
a37be8d (HEAD -> tech_posts) Oops, I forgot an index entry for my new post
6857c3d posts/django-framework.txt
2733233 Add a blog post about Python

❯ git rebase -i 6857c3d^
# editor will open
reword 6857c3d posts/django-framework.txt
squash a37be8d Oops, I forgot an index entry for my new post
# editor opens twice.
#  1. reword (change commit)
#  2. squash (combine commits)
❯ git log --oneline
9413427 (HEAD -> tech_posts) Add a new blog post explaining the pros and cons of django
2733233 (master) Add a blog post about Python
25b3810 Add a README file to the project
cd0b57c (tag: v1.0, tag: my-first-commit) Initial commit
```

#### TIP: USE REBASE TO SPLIT COMMITS

Editing a commit can also split it up into multiple commits!

1. Start an interactive rebase with `rebase -i`
2. mark the commit with an edit
3. `git reset HEAD^`
4. `git add`
5. `git commit`
6. repeat (4) & (5) until the working area is clean!
7. `git rebase --continue`

### Fixup & Autosquash

#### TIP: “AMEND” ANY COMMIT WITH FIXUP & AUTOSQUASH

What if we want to amend an arbitrary commit?

1. `git add new files`
2. `git commit --fixup <SHA>`
   1. this creates a new commit, the message starts with ‘fixup!’
3. `git rebase -i --autosquash <SHA>^`
4. git will generate the right todos for you! just save and quit.

#### FIXUP & AUTOSQUASH EXAMPLE

```bash
❯ git log --oneline
9413427 (HEAD -> tech_posts) Add a new blog post explaining the pros and cons of django
2733233 (master) Add a blog post about Python
25b3810 Add a README file to the project
cd0b57c (tag: v1.0, tag: my-first-commit) Initial commit
# I want to edit commit 27332 (Add blog post about Python)
❯ git add posts/python.txt
❯ git commit --fixup 2733233
[tech_posts 5e980a7] fixup! Add a blog post about Python
 1 file changed, 1 insertion(+), 1 deletion(-)

❯ git log --oneline
5e980a7 (HEAD -> tech_posts) fixup! Add a blog post about Python # git added new commit 5e980 message is: fixup! Add a blog post about Python
9413427 Add a new blog post explaining the pros and cons of django 2733233 (master) Add a blog post about Python
25b3810 Add a README file to the project
cd0b57c (tag: v1.0, tag: my-first-commit) Initial commit
```

```bash
❯ git rebase -i --autosquash 25b3810^ # use the -i and --autosquash flags && use ref of the parent commit
pick 25b3810 Add a README file to the project
pick 2733233 Add a blog post about Python
fixup 5e980a7 fixup! Add a blog post about Python # git will automatically reorder commits, and mark the commit with fixup action
pick 9413427 Add a new blog post explaining the pros and cons of django

❯ git log --oneline
68377f4 (HEAD -> tech_posts) Add a new blog post explaining the pros and cons of django
a8656d9 Add a blog post about Python # shiny new fixed up commit!
25b3810 Add a README file to the project
cd0b57c (tag: v1.0, tag: my-first-commit) Initial commit
```

#### REBASE --EXEC (EXECUTE A COMMAND)

`$ git rebase -i —exec “run-tests” <commit>`

2 options for exec:

1. add it as a command when doing interactive rebase
2. use it as a flag when rebasing

+ When used as a flag, the command specified by exec will run after every commit is applied.
+ This can be used to run tests.
+ The rebase will stop if the command fails, giving you a chance to fix what’s wrong.

### Abort

#### PULL THE RIP CORD

+ At any time before rebase is done, if things are going wrong:
  + `git rebase --abort`

#### REBASE PRO TIP

+ Before you `rebase` / `fixup` / `squash` / `reorder`:
+ Make a copy of your current branch:
  + `git branch my_branch_backup`
+ git branch will make a new branch, without switching to it

+ If rebase “succeeds” but you messed up...
+ `git reset my_branch_backup --hard`

+ You’re back in business!

#### REBASE ADVANTAGES

+ Rebase is incredibly powerful!
+ You can slice and dice your git history.
+ It’s easy to fix previous mistakes in code.
+ You can keep your git history neat and clean.

#### COMMIT EARLY & OFTEN VS GOOD COMMITS

+ Git Best Practice:
  + “commit often, perfect later, publish once”

+ When working locally:
  + Commit whenever you make changes!
  + It’ll help you be a more productive developer.
+ Before pushing work to a shared repo:
  + Rebase to clean up the commit history

#### WARNING: NEVER REWRITE PUBLIC HISTORY

+ Rebase commits are copies
+ If other people are working on the same repository they would be working on different commits.
+ You could cause massive merge conflicts
+ Even worse, you can cause people to lose their work!

> Warning: If you rewrite history and push to a public repo, monsters will eat you!

### Exercise: Git Rebase & Amend

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise7-RebaseAndAmend.md)

```bash
liuyong$ git checkout exercise7
Branch 'exercise7' set up to track remote branch 'exercise7' from 'origin'.
Switched to a new branch 'exercise7'
```

+ Make a commit, then practice using the `--amend` option to make another change to the previous commit.

```bash
echo "This is the first file" > first.txt
echo "This is the second file" > second.txt
git add first.txt

git commit -m "Committing two new files"
[exercise7 d2fcb23] Committing two new files
 1 file changed, 1 insertion(+)
 create mode 100644 first.txt

git add second.txt

git commit --amend
[exercise7 7d55d2b] Committing two new files
 Date: Sat Jul 20 09:20:11 2019 +0800
 2 files changed, 2 insertions(+)
 create mode 100644 first.txt
 create mode 100644 second.txt
# Confirm that we've committed both files now
```

+ Make two non-conflicting changes to two different branches. Rebase one branch onto the other.

```bash
git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

git checkout -b exercise7-2
Switched to a new branch 'exercise7-2'

git log --oneline
43388fe (HEAD -> exercise7-2, origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, master, exercise2) Initial commit

echo "New feature" > feature.txt

git add feature.txt

git commit -m "Adding a new feature"
[exercise7-2 6aa0c97] Adding a new feature
 1 file changed, 1 insertion(+)
 create mode 100644 feature.txt

git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

echo "Master has continued to change" >> hello.txt # The double arrow >> means 'append' to the file instead of overwrite.

git add hello.txt

git commit -m "Master has continued to change"
[master a6ff221] Master has continued to change
 1 file changed, 1 insertion(+)

git checkout exercise7-2
Switched to branch 'exercise7-2'

git rebase master
First, rewinding head to replay your work on top of it...
Applying: Adding a new feature

# Check that the changes to master are incorporated into our feature branch

git log --oneline
b265f83 (HEAD -> exercise7-2) Adding a new feature
a6ff221 (master) Master has continued to change
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, exercise2) Initial commit
```

+ Make another change to your current branch. Use an interactive rebase (`git rebase -i`) to rebase the two branches. Try squashing your two commits and rewording the message during the rebase.

```bash
echo "Another new feature" > another_feature.txt

git add another_feature.txt

git commit -m "Adding another new feature"
[exercise7-2 075490a] Adding another new feature
 1 file changed, 1 insertion(+)
 create mode 100644 another_feature.txt

git log -n 3 --oneline
075490a (HEAD -> exercise7-2) Adding another new feature
b265f83 Adding a new feature
a6ff221 (master) Master has continued to change

git rebase -i HEAD~2
Successfully rebased and updated refs/heads/exercise7-2.

git log --oneline
075490a (HEAD -> exercise7-2) Adding another new feature
b265f83 Adding a new feature
a6ff221 (master) Master has continued to change
43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, exercise2) Initial commit
```

## Forks & Remote Repos

### Github vs. Git

#### GITHUB VS GIT - THE KEY IS COLLABORATION

+ Git:
  + Open source version control software
+ Github:
  + Repository hosting
  + Browse code
  + Issues
  + Pull Requests
  + Forks

### Remotes

+ A remote is a git repository stored elsewhere - on the web, in github, etc.
+ *origin* is the default name git gives to the server you cloned from.

+ Cloning a remote repository from a URL will fetch the whole repository, and make a local copy in your .git folder.

+ You may have different privileges for a remote.
  + Read/Write for some, Read Only for others.

#### CLONE REPOSITORY

`git clone git@github.com:lisa/projA.git`

#### VIEWING REMOTES

```bash
❯ git remote -v
origin git@github.com:lisa/projA.git (fetch)
origin git@github.com:lisa/projA.git (push)
```

#### CLONED SOMEONE ELSE’S REPOSITORY

```bash
git clone git@github.com:dan/projB.git
```

### Forks, Pull Requests & Upstreams

#### FORK

+ A fork is a copy of a repository that’s stored in your GitHub account.
+ You can clone your fork to your local computer.

#### MERGING CHANGES TO ORIGINAL PROJECT FROM A FORK

#### STAYING UP TO DATE

+ While you work on your fork, other changes are getting merged into the source repository.
+ In order to stay up to date, set up an upstream

#### UPSTREAM

+ The upstream repository is the base repository you created a fork from.
+ This isn’t set up by default, you need to set it up manually.
+ By adding an upstream remote, you can pull down changes that have been added to the original repository after you forked it.

`❯ git remote add upstream https://github.com/ORIG_OWNER/REPO.git`

### Github Workflow

#### TRIANGULAR WORKFLOW

![workflow](/static/images/WX20190720-100204.png)

#### TRACKING BRANCHES

+ Track a branch to tie it to an upstream branch.
  + Bonus: Use git push / pull with no arguments

+ To checkout a remote branch, with tracking:
  + `git checkout -t origin/feature`
+ Tell Git which branch to track the first time you push:
  + `git push -u origin feature`

```bash
❯ git branch * master
❯ git fetch
❯ git branch -vv
* master bbeb1c32 [origin/master: behind 124] Merge branch 'master' of ...
# which upstream branch is being tracked
# how many commits you’re ahead or behind
```

#### FETCH

+ Git fetch is important for keeping your local repository up to date with a remote.
+ It pulls down all the changes that happened on the server
+ But, it doesn’t change your local repository!

#### PULL

+ Pulling will pull down the changes from the remote repository to your local repository, and merging them with a local branch.

+ Under the hood:
  + `git pull = git fetch && git merge`

+ If changes happened upstream, git will create a merge commit.
+ Otherwise, it will fast-forward.

#### PUSH

+ Pushing sends your changes to the remote repository
+ git only allows you to push if your changes won’t cause a conflict

+ TIP:
  + To see commits which haven’t been pushed upstream yet, use:
    + `git cherry -v`

#### GIT PULL —REBASE

+ Git pull —rebase will fetch, update your local branch to copy the upstream branch, then replay any commits you made via rebase.
+ Bonus: When you open a PR, there will be no unsightly merge commits!

#### NOTE: TAGS

+ Git doesn’t automatically push local tags to a remote repository.
+ To push tags:
  + `git push <tagname>`
  + `git push --tags`

#### CONTRIBUTING TO OPEN SOURCE PROJECTS - PULL REQUESTS

+ Before opening a PR:
  + Keep commit history clean and neat. Rebase if needed.
  + Run projects tests on your code
  + Pull in Upstream changes (preferably via rebase to avoid merge commits)
  + check for a CONTRIBUTING (.md/.txt) in the project root

+ After opening a PR:
  + Explain your changes throughly in the pull request
  + Link to any open issues that your pull request might fix
  + Check back for comments from the maintainers

#### ADVICE

+ Encourage developers to work on their own forks of a repository.
+ Mistakes are less likely to happen if no one is pushing directly to the “source of truth” for your codebase!
+ You can rebase and force push freely to your own origin, as long as no one else is cloning your branch.

#### PUSHING/MERGING YOUR CHANGES BACK TO A REMOTE

+ Rule of thumb:
  + Rebase commits on your local feature branch
  + Merge feature branches back to origin (or upstream)
+ When accepting a pull request:
  + Squash and merge or rebase with care.
  + You’ll lose context about the work on the feature branch!
  + It’ll make it harder to debug when issues arise in the future

### Exercise: Forks & Remote Repos Exercise

[Exercise Link](https://github.com/nnja/advanced-git/blob/master/exercises/Exercise8-ForksAndRemoteRepos.md)

```bash
git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
```

+ Create your own Github fork of [https://github.com/nnja/advanced-git-exercises](https://github.com/nnja/advanced-git-exercises).

In Github, go to [https://github.com/nnja/advanced-git-exercises](https://github.com/nnja/advanced-git-exercises) and create your own fork of this repo by clicking the Fork button in the top right corner. This should create a copy of the repo that belongs to you.

+ Look at your git remotes. Rename your origin remote (nnja's copy) to upstream. Add your personal fork as the origin remote.

```bash
git remote -v
origin  git@github.com:nnja/advanced-git-exercises.git (fetch)
origin  git@github.com:nnja/advanced-git-exercises.git (push)

git remote rename origin upstream

git remote -v
upstream        git@github.com:nnja/advanced-git-exercises.git (fetch)
upstream        git@github.com:nnja/advanced-git-exercises.git (push)
```

## Danger Zone

### Local & Remote Destructive Operations

#### LOCAL DESTRUCTIVE OPERATIONS

+ `git checkout -- <file>`
  + If the file is present in the staging area, it’ll be overwritten.
+ `git reset --hard`
  + Will overwrite changes that are staged and in the working area.

+ Unless changes are stashed, there’s no way of getting them back!
+ Tip: use `git stash --include-untracked` to include working area changes in your stash

#### REMOTE DESTRUCTIVE OPERATIONS - REWRITING HISTORY

+ There are many operations that can rewrite history:
  + rebase
  + amend
  + reset
+ If your code is hosted or shared:
  + Never run `git push -f`

### Recover Lost Work

+ Use ORIG_HEAD:
  + The commit HEAD was pointing to before a:
    + reset
    + merge
+ Check for repository copies:
  + github
  + coworker

#### ORIG_HEAD TO UNDO A MERGE

+ use ORIG_HEAD to undo merges
+ `git reset --merge ORIG_HEAD`
+ use `--merge` flag to preserve any uncommitted changes

#### USING GIT REFLOG AND ‘@‘ SYNTAX

+ By default, git keeps commits around for about 2 weeks.
+ If you need to go back in time, and find a commit that’s no longer referenced, you can look in the reflog.
+ The syntax of reflog is different.
  + `HEAD@{2}` means “the value of HEAD 2 moves ago”

```bash
❯ git reflog
e7cd68b (HEAD -> dont_overwrite_vary_header, origin/dont_overwrite_vary_header) HEAD@{0}: reset:
94c49a4 HEAD@{1}: commit: A
e7cd68b (HEAD -> dont_overwrite_vary_header, origin/dont_overwrite_vary_header) HEAD@{2}:
commit overwrite Vary header when setting for cookie access #2317
a54419a HEAD@{3}: rebase finished: returning to refs/heads/dont_overwrite_vary_header
a54419a HEAD@{4}: rebase: Don\'t overwrite Vary header when setting for cookie
```

## GitHub

### GitHub Shortcuts

+ Press '?' on any [github.com](https://github.com) page for a list of shortcuts
+ Then, hit 'Show all'

### Continuous Intergration

+ Merging smaller commits frequently, instead of waiting until a project is "done" and doing one big merge.
+ This means that features can be released quicker!
+ CI only works well when there are tests that ensure that new commits didn't "break the build"
+ It's even possible to perform a deployment at the end of a CI build!

## Advanced GitHub

### GitHub API

[GitHub Developer](https://developer.github.com/)
