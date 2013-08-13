# Git Workshop

@nicknisi | [github.com/nicknisi/git-workshop](https://github.com/nicknisi/git-workshop)

## History

The original [git workshop](http://www.draconianoverlord.com/git-workshop.html)
was presented by Stephen Haberman in 2010 and covered the basics of getting
started with git.

## Prerequesites

You should have a basic understanding of how to use git for this workshop. You should
also have a free account on [Github](https://github.com) and be sure you can push to it
either by [generating an SSH key](https://help.github.com/articles/generating-ssh-keys)
or by using the [https method](https://help.github.com/articles/set-up-git).

> Do not clone or fork this repository yet. We will get to that in the *Introducing hub*
> section

## Overview

The following topics are going to be covered in this workshop:

+ Teaching git about Github with hub
+ Finding bugs with bisect
+ Messin' with history
    + Advanced interactive rebase
    + Inserting commits into existing history (cherry-picking)
    + Undoing and redoing
+ Git configuration
    + Commit hooks
    + `rerere`

-------

### Introducing hub

[hub](http://hub.github.com/) is a wrapper around git that teaches git about Github. hub
provides a number of shortcuts and commands that make it easier to work with git projects
hosted on Github. For example, cloning this repository is a lot simpler with hub.
Instead of typing

```bash
git clone https://github.com/nicknisi/git-workshop.git
```

to clone this repo, you just need to run

```bash
git clone nicknisi/git-workshop
```

#### Installing hub

```bash
# install on OS X
$ brew install hub

# other systems
$ curl http://hub.github.com/standalone -sLo ~/bin/hub
$ chmod +x ~/bin/hub

# alias it as git
$ alias git=hub
```

As you can see, hub wraps the git command to extend it with additional functionality. hub
makes working with github URLs a lot easier, but it does a lot more than that.

#### hub Commands

+ `git fork` - Create a fork of the repository on your Github account
+ `git pull-request` - Issue a pull request on Github without ever leaving your terminal
+ `git browse` - Open a web browser straight to the repository page on Github
+ `git create` - Create a repository out on Github from your terminal
+ ... and many more!

#### Activity: Using hub

With hub installed on your machine, create a fork of this repository, Add your name to
the CONTRIBUTERS.md file, and issue a pull request without ever leaving your terminal.

1. Clone this repository
    ```bash
    git clone nicknisi/git-workshop
    ```
1. Fork this repository to your Github account: `git fork`
    ```bash
    # This will automatically set up a new remote named `USERNAME`
    # where USERNAME is your Github username
    git fork
    ```
1. Open up the CONTRIBUTERS.md file in your favorite editor (hopefully vim) and add your name to the list
1. Commit and push up the change to your fork
    ```bash
    # USERNAME is your Github username
    git commit -a -m "added NAME to the list"`
    git push -u USERNAME master
    ```
1. Issue a pull request from the command line back to this repo
    ```bash
    # USERNAME is your Github username
    git pull-request -b nicknisi:master -h USERNAME:master
    ```
1. Open up your repo in Github to confirm your commit has been pushed up and a pull
   request has been opened
    ```bash
    git browse
    ```

### Finding bugs with `git bisect`

[bisect](https://www.kernel.org/pub/software/scm/git/docs/git-bisect.html) is a fantastic tool to help you discover the exact commit that introduced a bug into
the repository. This works by telling git bisect where a good state is: where your code
is working as expected, and where a bad state is: where the code is broken. git wil then
check out a commit in the middle, and you tell it whether this is in a good state or a
bad state. The bisect command works by performing a binary search between one commit and
another in order to find the exact commit that introduced the bug.

Performing a git bisect can be either a manual or an automatic process, and it depends on
the state of the repository and the bug that has been introduced. To perform a git
bisect, run

```bash
git bisect start
```

Then, tell bisect where a bad commit exists
```bash
git bisect bad HEAD
```

Finally, tell bisect where a good commit is
```bash
git bisect good 53cb134
```

Git will then check out a commit between these two commits and you can manually rerun
whatever steps or tests are necessary to reproduce the issue. If you can reproduce the
issue, run `git bisect bad` and `git bisect good` if you are unable to reproduce it. Git
will then pick another issue based on your response until there are no more commits left
to check. At this point, git will inform you of the commit that introduced the issue

#### Activity: Where did that bug come from?

In this activity, we are presented with a pretty simple HTML file that is supposed to display a simple square and a slider.
When the slider is changed, the square should change colors, based on the input of from the slider. At the latest commit in
the bisect branch, we are getting no JavaScript errors, and we are not seeing a slider.

We know that the code is working as intended at [37d0821](https://github.com/nicknisi/git-workshop/commit/37d0821049bd94bdc5aaa357311d4a6711d4b16d). Let's use `git bisect` to determine where things went wrong.

1. Checkout the [bisect](https://github.com/nicknisi/git-workshop/tree/bisect) branch
    ```bash
    git checkout bisect
    ```
1. Look at the *index.html* file in a browser and test the file to how it is failing
1. Look through the log to determine where the last known good state of the application is (I have marked this commit with
   the commit message "GOOD STATE")
    ```bash
    git log --graph --pretty=oneline --abbrev-commit --decorate
    ```
1. Start up bisect
    ```bash
    git bisect start
    ```
1. Alert bisect of the bad commit
    ```bash
    git bisect bad HEAD
    ```
1. Alert bisect of the good commit
    ```bash
    git bisect good 37d0821
    ```
1. git will now pick a commit between the two and check it out. Reload the page in the browser and determine if the slider
   is working properly (showing up).
    + If it is showing up, `git bisect good`
    + If it is not showing up, `git bisect bad`
1. After each command, git will checkout a new commit until it has determined the exact commit that introduced the bug
1. Once you have determined the bad commit, you should be presented with a message like this:
    ```bash
    ❯ git bisect bad                                                                                                       ✔
    9378fec...|bisect
    9378fec280242b4a02b6972c3ca9b61cdf14cad5 is the first bad commit
    commit 9378fec280242b4a02b6972c3ca9b61cdf14cad5
    Author: Nick Nisi <nick@nisi.org>
    Date:   Tue Aug 13 00:05:23 2013 -0500

        adjust animation speed slowing down again

        :100644 100644 2bf5ba28648e5c388556b37701addac90d9daf20 63ec399ce0f457bd17e5e6933a0a140c1435223d M      index.html

    ```
1. To end bisect at any time reset it
    ```bash
    git bisect reset
    ```

Following these steps, we are able to quickly determine which is the [bad
commit](https://github.com/nicknisi/git-workshop/commit/9378fec280242b4a02b6972c3ca9b61cdf14cad5).

#### Automating bisect

The process of finding a bad commit with bisect can be automated if the problem you are trying to find can be tested with a
script. Using `git bisect run`, we can provide a command or a script to execute against each commit that bisect checks. If
ths script returns 0 then the commit is considered `good`. Otherwise, it is considered `bad`. This can be really useful if
you are trying to track down what commit introduced a failing unit test or build.
