---
title: Tracking changes with a local repository
teaching: 35
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Know how to set up a new Git repository.
- Understand how to start tracking files.
- Be able to commit changes to your repository.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How do I get started with Git?
- Where does Git store information?

::::::::::::::::::::::::::::::::::::::::::::::::::

Version control is centred round the notion of a *repository* which holds your
directories and files. We'll start by looking at a local repository. The local
repository is set up in a directory in your local filesystem (local machine).
For this we will use the command line interface.

:::::::::::::::::::::::::::::::::::::::::  callout

## Why use the command line?

There are [lots of graphical user interfaces][guis] (GUIs) for using Git: both stand-alone
and integrated into IDEs (e.g. MATLAB, Rstudio, PyCharm).
We are deliberately not using a GUI for this course because:

- you will have a better understanding of how the git comands work
  (some functionality is often missing and/or unclear in GUIs)
- you will be able to use Git on any computer
  (e.g. remotely accessing HPC systems, which generally only have Linux command line access)
- you will be able to use any GUI, rather than just the one you have learned
- you'll be able to search for help online and understand the answers

By the end of the course, this should no longer be you:
![](https://imgs.xkcd.com/comics/git.png){alt='xkcd-git-comic'}


::::::::::::::::::::::::::::::::::::::::::::::::::

## Setting up Git

Instructions for setting up Git on your own machine are given under
[setup](../learners/setup.md).

You can verify you have everything set up correctly like this:

```bash
$ ssh -T git@github.com
```

```output
Hi <YOUR_GITHUB_USERNAME>! You've successfully authenticated, but GitHub does not provide shell access.
```

## Tell Git who we are

As part of the information about changes made to files Git records who made
those changes. In teamwork this information is often crucial (do you want to
know who rewrote your 'Conclusions' section?). So, we need to tell Git about
who we are (note that you need to enclose your name in quote marks):

```bash
$ git config --global user.name "Your Name" 			# Put your quote marks around your name
$ git config --global user.email yourname@yourplace.org
```

## Set a default editor

When working with Git we will often need to provide some short but useful
information. In order to enter this information we need an editor. We'll now
tell Git which editor we want to be the default one (i.e. Git will always bring
it up whenever it wants us to provide some information).

You can choose any editor available on your system,
but for this course we will use `nano`.

```bash
$ git config --global core.editor nano
```

## Set remote merge strategy

Set the default behaviour for merging remote branches (this afternoon).

```bash
git config --global pull.rebase false
```

## Git's global configuration

We can now preview (and edit, if necessary) Git's global configuration (such as
our name and the default editor which we just set up). If we look in our home
directory, we'll see a `.gitconfig` file,

```bash
$ cat ~/.gitconfig
```

```output
[user]
	name = Your Name
	email = yourname@yourplace.org
[core]
	editor = nano
```

**These global configuration settings will apply to any new Git repository
you create on your computer.**
i.e. the `--global` commands above are only required once per computer.

***

## Create a new repository with Git

We will be working with a simple example in this tutorial. It will be a paper
that we will first start writing as a single author and then work on it further
with one of our colleagues.

First, let's create a directory within your home directory:

```bash
$ cd								# Switch to your home directory.
$ pwd								# Print working directory (output should be /home/<username>)
$ mkdir paper
$ cd paper
```

Now, we need to set up this directory up to be a Git repository (or "initiate
the repository"):

```bash
$ git init
```

```output
Initialized empty Git repository in /home/user/paper/.git/
```

The directory "paper" is now our working directory.

If we look in this directory, we'll find a `.git` directory:

```bash
$ ls .git
```

```output
branches  config  description  HEAD  hooks  info  objects refs
```

The `.git` directory contains Git's [configuration files][inside-dot-git]. Be careful not to
accidentally delete this directory!

## Tracking files with a git repository

Now, we'll create a file. Let's say we're going to write a journal paper, so
we will start by [adding the author names and a title][add-author-title], then save the file.

```bash
$ nano paper.md
# Add author names and paper title
```

:::::::::::::::::::::::::::::::::::::::::  callout

## Text editors on your OS

`nano` should be available whatever OS you are using.
If you prefer a different editor feel free to use that instead e.g. `notepad` on Windows:

```bash
notepad paper.md
```

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::  callout

## Accessing files from the command line

In this lesson we create and modify text files using a command line interface
(e.g. terminal, Git Bash etc), mainly for convenience.
**These are normal files which are also accessible from the file browser (e.g. Windows explorer),
and by other programs.**

Your typical workflow using version control might involve editing files using
e.g. MATLAB, PyCharm, Rstudio etc and committing from a command line interface.


::::::::::::::::::::::::::::::::::::::::::::::::::

`git status` allows us to find out about the current status
of files in the repository. So we can run,

```bash
$ git status
```

```output
On branch master

Initial commit

Untracked files:
(use "git add <file>..." to include in what will be committed)

paper.md

nothing added to commit but untracked files present (use "git add" to track)
```

Information about what Git knows about the directory is displayed. We are on
the `master` branch, which is the default branch in a Git respository
(one way to think of branches is like parallel versions of the project - more
on branches later).

For now, the important bit of information is that our file is listed as
**Untracked** which means it is in our working directory but Git is not
tracking it - that is, any changes made to this file will not be recorded by
Git.

:::::::::::::::::::::::::::::::::::::::::  callout

## Default branch name

Some implementations of git (e.g. on  newish Macs) have chosen to overwrite the default
branch name, and use `main` instead of `master`.
If this is the case, you can either mentally switch out `master` with `main` for the
rest of the course, or if you prefer you can change the branch name to `master` using

```bash
git branch -m master
```

To make this a permanent change for new repos, you would need to run

```bash
git config --global init.defaultBranch master
```

::::::::::::::::::::::::::::::::::::::::::::::::::

## Add files to a Git repository

To tell Git about the file, we will use the `git add` command:

```bash
$ git add paper.md
$ git status
```

```output
On branch master

Initial commit

Changes to be committed:
(use "git rm --cached <file>..." to unstage)

	new file:   paper.md
```

Now our file is listed underneath where it says **Changes to be committed**.

`git add` is used for two purposes. Firstly, to tell Git that a given file
should be tracked. Secondly, to put the file into the Git **staging area**
which is also known as the *index* or the *cache*.

The staging area can be viewed as a "loading dock", a place to hold files we have
added, or changed, until we are ready to tell Git to record those changes in the
repository.

![](fig/git-staging-area.svg){alt='The staging area'}

## Commit changes

In order to tell Git to record our change, our new file, into the repository,
we need to  **commit** it:

```bash
$ git commit
# Type a commit message: "Add title and authors"
# Save the commit message and close your text editor (nano, notepad etc.)
```

Our default editor will now pop up. Why? Well, Git can automatically figure out
that directories and files are committed, and by whom (thanks to the information
we provided before) and even, what changes were made, but it cannot figure out
why. So we need to provide this in a commit message.

If we save our commit message **and exit the editor**, Git will now commit our file.

```output
[master (root-commit) 21cfbde]
1 file changed, 2 insertions(+) Add title and authors
create mode 100644 paper.md
```

This output shows the number of files changed and the number of lines inserted
or deleted across all those files. Here, we have changed (by adding) 1 file and
inserted 2 lines.

Now, if we look at its status,

```bash
$ git status
```

```output
On branch master
nothing to commit, working directory clean
```

our file is now in the repository.
The output from the `git status` command means that we have a clean directory
i.e. no tracked but modified files.

Now we will work a bit further on our *paper.md* file by [starting the introduction][start-intro]
section.

```bash
$ nano paper.md
# Write introduction section
```

If we now run,

```bash
$ git status
```

we see changes not staged for commit section and our file is marked as
modified:

```output
On branch master
Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git restore -- <file>..." to discard changes in working directory)

     modified:	 paper.md

no changes added to commit (use "git add" and/or "git commit -a")
```

This means that a file Git knows about has been modified by us but
has not yet been committed. So we can add it to the staging area and then
commit the changes:

```bash
$ git add paper.md
$ git commit				# "Write introduction"
```

Note that in this case we used `git add` to put paper.md to the staging
area. Git already knows this file should be tracked but doesn't know if we want
to commit the changes we made to the file  in the repository and hence we have
to add the file to the staging area.

It can sometimes be quicker to provide our commit messages at the command-line
by doing `git commit -m "Write introduction section"`.

In our introduction, we should [cite a paper] describing the main instrument used.

```bash
$ nano paper.md 			# Cite instrument paper in introduction
```

Let's also create a file `refs.txt` to hold our references:

```bash
$ nano refs.txt				# Add the reference
```

Now we need to record our work in the repository so we need to make a commit.
First we tell Git to track the references.

```bash
$ git add refs.txt			# Track the refs.txt file
$ git status				# Verify that refs.txt is now tracked
```

The file `refs.txt` is now tracked.  We also have to add
paper.md to the staging area. But there is a shortcut. We can use
`commit -a`. This option means "commit all files that are tracked and
that have been modified".

```bash
$ git commit -am "Reference J Bloggs and add references file"	# Add and commit all tracked files
```

and Git will add, then commit, both the directory and the file.

In order to add all tracked files to the staging area, use `git commit -a`
(which may be very useful if you edit e.g. 10 files and now you want to commit all of them).

![](fig/git-committing.svg){alt='The Git commit workflow'}



[guis]: https://git-scm.com/downloads/guis
[inside-dot-git]: https://jvns.ca/blog/2024/01/26/inside-git/?utm_source=tldrnewsletter
[add-author-title]: https://github.com/gcapes/git-course-paper/commit/8bd913361c97cdf754b9952ac6c2ac40531aed84#diff-0403ef06adf405f7b310b4518bd6a3559854f54c61676f676ce9cbfee7172ab6
[start-intro]: https://github.com/gcapes/git-course-paper/commit/fd89f56eaf67c60318e3111268e0097359a3fa4e#diff-0403ef06adf405f7b310b4518bd6a3559854f54c61676f676ce9cbfee7172ab6
[cite a paper]: https://github.com/gcapes/git-course-paper/commit/6e3919a05cd1b3ce922d35c65815204e0c0db711#diff-0403ef06adf405f7b310b4518bd6a3559854f54c61676f676ce9cbfee7172ab6


:::::::::::::::::::::::::::::::::::::::: keypoints

- `git init` initializes a new repository
- `git status` shows the status of a repository
- Files can be stored in a project’s `working directory` (which users see), the `staging area` (where the next commit is being built up) and the `local repository` (where commits are permanently recorded)
- `git add` puts files in the staging area
- `git commit` saves the staged content as a new commit in the local repository
- Always write a log message when committing changes

::::::::::::::::::::::::::::::::::::::::::::::::::


