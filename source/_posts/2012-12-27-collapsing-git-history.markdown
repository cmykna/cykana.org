---
layout: post
title: "Collapsing git history"
date: 2012-12-27 10:14
comments: true
categories: git, adventure
---
When I was first setting up Octopress, I kind of did it wrong. I straight cloned it from Github into cykana.org locally, which brings Octopress's own git history along with it. What I _should've_ done was clone Octopress separately, then inintialize a new repo and cp/add/commit everything in the Octopress directory into it. Oops. Let's muck around and try to fix this.

Note: I've got `git log` aliased to `log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short`, so that's what'll show up in log codeblocks below.

## Truncating

It's pretty easy to collapse a git history down to a particular commit (actually, truncate is probably more accurate). Put the SHA of the commit you want to use as your new history starting point in `.git/info/grafts`. When you do a git log, you'll see _only_ that commit in your history:

{% codeblock %}
* 30b7452 2012-12-27 | cykana.org initial commit (grafted, HEAD, master) [Chris Cykana]
{% endcodeblock %}

This is temporary: if you remove `.git/info/grafts`, you'll get the full history back:

{% codeblock %}
* 30b7452 2012-12-27 | cykana.org initial commit (HEAD, master) [Chris Cykana]
* bfe99ef 2012-12-24 | Merge branch 'master' of github.com:cmykna/cykana.org (origin/master) [Chris Cykan
*   48d3e75 2012-12-19 | Merge pull request #844 from jsvensson/rsyncargs (HEAD, origin/master, origin/HEAD, master) [Brandon Mathis]
|\  
| * 916b87a 2012-12-09 | Rakefile option for any additional rsync arguments [Johan Svensson]
* |   c9f820f 2012-12-19 | Merge pull request #824 from liangsun/patch-1 [Brandon Mathis]
{% endcodeblock %}

To make it permanent, run `git filter-branch` without any arguments. Notice how how `grafts` is no longer before HEAD in the log entry, and the new commit SHA:

{% codeblock %}
* e1ce881 2012-12-27 | cykana.org initial commit (HEAD, master) [Chris Cykana]
{% endcodeblock %}

__IMPORTANT__: this completely sends your previous history to the _moon_ for all commits that are children of the current SHA. However, you can recover the old history by doing `git reset --hard SHA`, where SHA is a commit from before the graft/filter operation.

## Oh wait, what about remotes?

This works great locally, but what if you're _really_ dumb and have already pushed the version of your repo with all the history to a remote endpoint? Well, you get this:

{% codeblock %}
# Your branch and 'origin/master' have diverged,
# and have 1 and 626 different commits each, respectively.
{% endcodeblock %}

Uh-oh. Now git thinks our branch is 600+ commits behind the branch it's tracking remotely, and pulling from remote will put all those commits back in your history. One solution to this is to just do the graft without the filter-branch: that'll let git pretend that you don't have that old Octopress history locally and keep it out of your logs, but it'll still be visible on Github, which isn't ideal. I just want to track _my_ commits.

## So...now what?





According to people who are probably smarter than me, grafts are more of a quick hack than something you should be making a regular part of your workflow, but in specific cases, git's graft mechanism can be really helpful. Here's what helped me figure all this out:

* [StackOverflow](http://stackoverflow.com/a/475931)
* [StackOverflow](http://stackoverflow.com/a/6802005)
* [wiki.kernel.org GraftPoint entry](https://git.wiki.kernel.org/index.php/GraftPoint)