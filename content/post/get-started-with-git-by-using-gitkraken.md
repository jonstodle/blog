+++
date = ""
tags = []
title = "An intro to Git for people who don't like the terminal"
+++
Getting started with Git can be a bit overwhelming. To help you get started I'd
like to teach you Git by using a GUI. More specifically, GitKraken. It's been my
Git client of choice for close to two years and I'm quite happy with it.

To start off, I'm going to cover a bit about what Git and version control is.
It's not a deep dive into technical details, it's just to make sure we're on the
same page when it comes to what Git is and isn't.

After that we're going to start a small project and have a look at how to start
using Git with that project and how to store the project on Github. If you want
to use a different hosting service like GitLab, Bitbucket or Visual Studio Team
Services (Microsoft know how to name things), it's going to be very  similar.
Underneath the fancy UIs, they're all running Git.

Intro
Version control
A version control system (VCS) is a program that helps you keep track of
multiple versions of files. This is basically Dropbox' version history on
steroids.

Unlike Dropbox though, a VCS won't automatically store new version of your
files. You have to manually say when you want to store a new version. You might
think this is a disadvantage. Instead of having lots of versions of a file with
meaningless changes, like a new empty line, you decide when it makes sense to
create a new version.

A VCS also let's you store changes in multiple files in the same version. This
means you can bundle together related changes in multiple files into a new
version. You might give an <div>  tag an id  and style that <div>  inside your
CSS file. Those two changes are related and should probably be contained inside
the same version. We'll look more into this later.

When creating a new version you'll give it a comment, which should explain in
brief terms what the version changes. This makes the version history a readable
log of all the changes you've made to your code. If you're wondering when you
gave that <div>  that and id, you can look that up.

It's also easy to undo the changes a version has made. You might later regret
that you gave that <div>  an id  and styled it in your CSS file. You're able to
back through the history of your versions and undo the version that made the
change. If you want to undo a large version, this is really helpful as you don't
have to remember all the changes.

Git
Background
While there are many version control systems to choose from - Mercurial,
Subversion, Team Foundation Services to name a few, Git has grown to be the most
popular in recent years. Even Windows is version controlled in Git.

Git was created by Linus Torvalds, the guy who created Linux, for use on Linux.
He wanted a better VCS for development on Linux, so he created one.

What makes Git different
Git combines a few ideas from the other popular VCSs. While it's not alone in
doing each of these things, it's the combination of them that makes Git such a
loved tool. Two of those ideas are distributed version control and only storing
change deltas.

Distributed VCS
Most of the previously popular VCSs needs the user to connect to a central
server which contains the master version of the code. Whenever they wanted to
change the code, they needed to connect to server, download the most recent
version, make their changes and submit the new version back to the server. This
requires you to have a connection to the server every time you want to make a
change, and it may take some time to submit the changes if your network
connection is a bit slow.

In a distributed VCS however, you download and make a local copy of the code.
You get the most recent version and a number of previous version, in case you
should need them. You make all your changes locally on the computer and when you
want to submit your updates, you connect to the server and submit your changes.
Git will then do a merge, which will integrate your changes into the version on
the server. Should there be any problems with the merge  operation, a merge
conflict  will occur, and Git will ask you how to fix it. We'll look at merge
conflicts later.

Change deltas
While some version control systems store all the different versions of each
file, Git only tracks the difference between files.

If you add an extra line to a file, some other VCS may store both versions of
that file: the one with the extra line, and the previous version without the
line. Git on the other hand will only store that a line was added at a specific
location. This will give huge storage savings, especially on large projects.

Terminology
Before we get started properly, I want to explain some of the terminology used
in relation to Git. This should help you better understand what is going on
further down.

TermDescriptionCommitThis is what Git calls a version, a collection of changes.
You commit the changes to your files in a commit.Repository (repo)This is all
the files that are version controlled by Git. Usually this will correspond
pretty much to your project or root folder of your code.GitKraken
You can of course hack around in the terminal/command line, but if you want
something a bit more visual I'd recommend GitKraken. It's a client built on top
of Git, letting you poke around the GUI, while it runs the appropriate Git
commands.

There are many alternatives to GitKraken, but GitKraken has been my client of
choice for nigh on two years and it's been serving me well. If you end up hungry
for something different, I know a lot of people prefer SourceTree
[https://www.sourcetreeapp.com/]  or Tower [https://www.git-tower.com/].

You can get GitKraken by downloading it from their website GitKraken.com
[https://www.gitkraken.com/]. Run the installer and launch it. You should now
see something akin to this:



Initializing Git repository
First order of business is to initialize Git. To do this we need a folder that
is the root of our project (it could technically be any folder in you project,
but the root folder is the most common).

We start by creating a folder called git-with-gitkraken. Next we head back to
GitKraken, click the folder icon in the upper left corner and select the Init 
tab. We'll create a local only repository for now. Select the folder we created
in New repository path. We'll skip the two optional fields and the LFS  check
box, but I'll explain them a bit here:

.gitignore
This is a file containing instructions for Git to ignore certain files and
folders. You might have files which you don't want to add to your version
control. File containing passwords or other sensitive information, for example.
Or something like your personal settings for your code editor of choice. If you
share your code with others, they might not want your settings in their editor.

You might also have folder which contain automatically generated files. These
are usually not added to version control as they will be generated anew when you
build your application. These will change all the time, and only add clutter to
your commits.

GitKraken let's you set up one when initializing your repository. You can choose
from a range templates which includes common files and folders to ignore in
certain development environments. There are templates for Python, node and many
more.

Personally i prefer using gitignore.io [https://www.gitignore.io/]  for creating
.gitignore files. It's kept up to date by the community and it let's you combine
multiple templates into one file. You could for example combine the templates
for Python and Visual Studio Code.

We're not going to add a .gitignore right now. We'll want to add some records to
a .gitignore later, but we'll cross that bridge when we get to it.

License
For public projects it's customary to add a file named LICENSE  in the root
folder. This file will contain the license for your code. I'm not going into
details about licensing, but will mention that a popular license these days is
the MIT license [https://opensource.org/licenses/MIT].

LFS
Git LFS is a way to store large outside of Git, while still having them version
controlled. Usually these files a database dumps, logs, video or audio files.
Some code hosting sites sets limitations on file size which can be uploaded. In
most projects this is not going to be a problem.

README.md
The README.md  file contains information about your project. While it might not
have much use in a personal project, it's common to add some information about
the project for others. You'll typically explain what the project is for and
some instructions how to use it, for example.

On most code hosting sites (like Github, Bitbucket, Gitlab, etc) the README file
is shown on the front page of your project.

The README file is typically written in Markdown. Markdown is a simple syntax to
express how to format text. Details about Markdown syntax can be found in the 
"official" docs [https://daringfireball.net/projects/markdown/].

By default GitKraken will add a README.md  file to your repository and commits
it. GitKraken needs there to be at least one commit (Git itself does not), so it
does this automatically.

Adding files
Now we're going to add some files to our project.

Commit added files
Modify files
Commit modified files
Revert commit
Reset to commit
Soft reset
Mixed reset
Hard reset (aka "You're doing it wrong")
Adding remote
Hosting options
Cloning
Merge conflict
Conclusion
