# Git Workshop

## History

The original [git workshop](http://www.draconianoverlord.com/git-workshop.html)
was presented by Stephen Haberman in 2010 and covered the basics of getting
started with git.

## Prerequesites

You should have a basic understanding of how to use git for this workshop. You should
also have a free account on [Github](https://github.com) and be sure you can push to it
either by [generating an SSH key](https://help.github.com/articles/generating-ssh-keys)
or by using the [https method](https://help.github.com/articles/set-up-git).

### Clone this Repository

You can clone this repository to your local machine by issuing the following command:

```shell
git clone https://github.com/nicknisi/git-workshop.git
```

## Overview

The following topics are going to be covered in this workshop:

+ Branching Strategies
	+ flow
	+ flux
+ Git Configuration
	+ Commit Hooks
	+ `rerere`
+ `hub`
+ Messin' With History
	+ Advanced Interactive Rebase
	+ Inserting Commits into existing history (cherry-picking)
	+ Undoing and Redoing
+ Searching the history
	+ bisect

-------

### Introducing hub

[hub](http://hub.github.com/) is a wrapper around git that teaches git about Github. hub
provides a number of shortcuts and commands that make it easier to work with git projects
hosted on Github. For example, cloning this repository is a lot simpler with hub.
Instead of typing

```shell
git clone https://github.com/nicknisi/git-workshop.git
```

to clone this repo, you just need to run

```shell
git clone nicknisi/git-workshop
```

#### Installing hub

```shell
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

### Activity: Using hub

With hub installed on your machine, create a fork of this repository, Add your name to
the CONTRIBUTERS.md file, and issue a pull request without ever leaving your terminal.

1. Clone this repository
	```shell
	git clone nicknisi/gitworkshop
	```
1. Fork this repository to your Github account: `git fork`
	```shell
	git fork
	```
1. Open up the CONTRIBUTERS.md file in your favorite editor (hopefully vim) and add your name to the list
1. Commit and push up the change to your fork
	```shell
	git commit -a -m "added NAME to the list"`
	git push -u origin master
	```
1. Issue a pull request from the command line back to this repo
	```shell
	git pull-request -b nicknisi:master -h origin:master
	```
1. Open up your repo in Github to confirm your commit has been pushed up and a pull
   request has been opened
	```shell
	git browse
	```
