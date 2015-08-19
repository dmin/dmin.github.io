---
layout: post
title:  "How to update Github pull requests - or - An introduction to git rebase"
date:   2015-08-13 23:34:27
categories: git github "work flow"
---
It was bound to happen - you just didn't see it coming.

You submitted a pull request to your favorite open source project on Github. You're feeling good - of course you're feeling good - you just made the world a better place! A few hours later it happens - the project owner wants you to make a change to your pull request.

What? A change? To your pull request? But its perfect! It takes a minute, but you get past the initial shock. As you collect yourself you realize that you don't know how to update a pull request! Isn't that what new pull requests are for - to update previous pull requests?

Its actually pretty trivial to update your pull request. All you need to do is make a new commit and push it to the branch of your pull request. Let's walk through an example.

I recently submitted a pull request to [github.com/emberjs/guides](https://github.com/emberjs/guides) repository. To create the pull request I used Github's "edit file" feature that automatically creates a fork of the project in your Github account and makes a new branch for your changes. Before accepting the pull request one of the project collaborators asked me to make a change.

The first thing I need to do is clone the forked repository to my local machine.

{% highlight bash %}
$ git clone https://github.com/dmin/guides.git
...
$ cd guides
{% endhighlight %}

My pull request pointed to the "patch-2" branch on the forked repository, so I'll checkout that branch (if you're wondering: even though "patch-2" is a remote branch git is smart enough to know what you want to do here and will create a local branch automatically).

{% highlight bash %}
$ git checkout patch-2
{% endhighlight %}

Now that I'm on the correct branch I can make the change in my text editor. When that's done I can add the changes to staging and commit them.

{% highlight bash %}
$ git add .
$ git commit -m "Made changes. More words here."
...
{% endhighlight %}

Technically the changes are ready to be pushed back to the fork on Github - when that happens Github will automatically update the pull request with the new commit. So I could do this:

{% highlight bash %}
# You may not want to do this yet - read on.
$ git push origin patch-2
...
{% endhighlight %}

If I push the new commit up to the Github fork now my simple change to emberjs/guides will be shown in the commit history as two separate commits. Since both the commits I made are logically part of the same change I'm trying to make it would be convenient if I could merge both commits into one. Enter ```git rebase```.

The ```git rebase``` command can do a lot of things, but right now we're interested in "squashing" two commits into one commit so the commit history is cleaner.  This can be accomplished using the interactive option of the rebase command.

{% highlight bash %}
$ git rebase --interactive HEAD~2
{% endhighlight %}

This command is telling git that I want to perform an interactive rebase on the last two commits. Git will open up your text editor with something similar to the below text:

(NOTE: If you are using the Atom text editor you need to tell git to use the --wait option when opening Atom. Run the following command: ```git config --global core.editor "atom --wait"```)

{% highlight bash %}
pick 0f8a78e My original commit message is here.
pick bfe35b5 Made changes. More words here.

# Rebase cd30563..bfe35b5 onto cd30563
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
{% endhighlight %}

To "squash" the two commits into one I'll "pick" the original commit, and "squash" the new commit into the original. The text file should now read:

{% highlight bash %}
pick 0f8a78e My original commit message is here.
squash bfe35b5 Made changes. More words here.
{% endhighlight %}

When the file is saved and the text editor is closed git will open another text editor window for you to enter a new commit message that represents both "squashed" commits.

Now I can push the newly squashed commit back to the forked repository and my pull request will have the required updates with out any unneeded commits!

{% highlight bash %}
$ git push origin patch-2 --force
{% endhighlight %}

(we need to use the --force option because the git rebase command is rewriting history by "squashing" two commits into one new commit. You should not use the --force option when pushing to branches that are used by other people.)
