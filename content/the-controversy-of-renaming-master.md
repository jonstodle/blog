+++
title = "The 'Controversy' of Renaming Master"
date = 2020-06-18T19:45:26Z
[taxonomies]
tags = ["git", "opinion"]
+++
I can start off by stating where I fall in this: I'm a white cisgendered man and I'm **for** changing the default branch name from _master_ to to something else. My preference is _main_ as the new name.

# What's this all about?

If you're reading this some later year than 2020 and wonder what this was all about, or you've been living under the proverbial rock for the last month, here's a quick run-down: Black people have had enough of being killed by police, there are **HUGE** protests all over the US protesting racism, the protests spread to a lot of other countries and the topic of using names for things with a negative/racist origin or negative connotations is being discussed.

One of the trends springing from this is renaming the default branch for git repositories from _master_ to something else.

# Is _master_ actually offensive

Yes.

To some people it is.

Not to all, but that's not the point.

# But does it have a racist origin

Personally I think this is somewhat irrelevant, but there are evidence that indicate that the origin of the usage of _master_ come from the master/slave dichotomy. The naming seems to be borrowed from another source control system called BitKeeper, which uses master/slave to describe a relationship between two different branches of code. [Source](https://mail.gnome.org/archives/desktop-devel-list/2019-May/msg00066.html).

# But changing it is such a hassle because...

## it will break build scripts

A lot of scripts and systems rely on _master_ being the default.

You could argue that, since git allows you to redefine the name of the default branch, you shouldn't rely on it being _master_. But let's be honest - programmers are too lazy to account for that when it's the default for the vast majority of repositories.

Incidentally this is also why we never change versions in our dependencies, never change our operating systems (or it's version), never switch programming languages or upgrade the version of our programming language, and it's also why we haven't changed anything about programming for the last 70 years. It just breaks everything.

## old literature will be wrong

Old books and tutorials will be confusing as they keep referring to _master_, but there is no _master_ anymore.

This goes a bit back to my previous point. Things change. Sometimes because we find a better way to do things; sometimes because we realise we can do better. This is one of the latter.

## this won't solve racism

Changing the name of the default branch of a source control system is not going to end racism.

Of course it's not! Who would claim that!? This is a [strawman argument](https://en.wikipedia.org/wiki/Straw_man). The point is sending a message or signal: We welcome everyone, by not using language that makes people feel unwelcome.

## even Black or other People of Color say it's not offensive

Some say they are not offended by _master_, why change it then?

Great! It's great to hear that they have one less thing to make them feel unwelcome. For others this is not the case; let's make them feel included too.

## it's a lot of work having to do this

It takes you less than 30 seconds to get started:

```
git branch -m master main
git push -u origin main
```

Your local _master_ branch is now named _main_, and it's pushed to origin.

If you're using Github, you can change the default branch by heading to the repositories settings, then _Branches_  in the side menu and select _main_ from the dropdown.

# Some of my thoughts on this

I find it bizarre that people will spend minutes or hours debating this, when it's a 30 second fix. If you have some scripts and CI/CD YAML that depend on _master_, allocate some extra time for that. Congratulations, you've made your project a bit more inclusive.

Also, for a field that sets great store in naming things well and generally prefer things to be precise, it's bizarre that so many will argue against a name that is shorter and more descriptive. With _main_ you can even retain the muscle memory of `ma <tab>` when auto-completing in your terminal.

Some say this is a token gesture, and that they won't do it. But if you can't even muster the will to do _token gesture_ in the fight against racism...

And if you even spend time arguing that it's a token gesture and that's a reason not to do it. **How can I not assume you're racist?**
