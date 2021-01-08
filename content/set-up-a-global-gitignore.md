+++
title = "Set Up A Global .gitignore"
date = 2021-01-09
[taxonomies]
tags = ["git", "tips"]
+++

If you've been using git for any amount of time, you've come across `.gitignore`. I'm not going to go through how it works, but did you know you can set up a *global .gitignore?*

What do you put in this global .gitignore? My recommendation is caches and auto-generated files and directories created by your operating system, and your editor/IDE of choice. I share my .gitignore across multiple operating systems, so I've ended up with quite a comprehensive one. 

As a starting point, I think this one works pretty well: [global .gitignore starting point]([https://www.gitignore.io/?templates=rider,linux,macos,windows,clion+all,monodevelop,intellij+all,visualstudio,jetbrains+all,visualstudiocode](https://www.gitignore.io/?templates=rider,linux,macos,windows,clion+all,monodevelop,intellij+all,visualstudio,jetbrains+all,visualstudiocode))

What to put in the repository .gitignore? I recommend putting caches and auto-generated files and directories created by tooling used by your code or on your code, and auto-generated files and directories specific to the repository.

My rule of thumb for where to put a specific ignore clause is to consider at what "level" the choice was made to use the tool that creates the files and directories I want to ignore were made. Personal choices, like what operating system to use, which editor or IDE, etc are ignored in the global .gitignore. Choices made for a specific repository is put in the local .gitignore.

Even though a lot of you repositories share the same ignore clauses (all your JavaScript repositories ignore `node_modules`, for example) - **don't put that in your global ignore**. That just means that someone else is going to commit node_modules by accident.

You can put your global .gitignore wherever you like in your file system. Personally I prefer my `home` directory. Then you tell git where the global .gitignore file is located by saving it to the git config:

```bash
# macOS / Linux
git config --global core.excludesfile ~/.gitignore

# Windows
git config --global core.excludesfile %USERPROFILE%\.gitignore**
```

Happy coding!
