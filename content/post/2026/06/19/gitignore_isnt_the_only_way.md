---
title: "Ignore files in git"
date: 2026-06-19T16:03:23+03:00
categories:
    - software development
    - git
tags:
    - git
    - gitignore
url: /2026/06/ignore-files-in-git
---

Hello! :wave: I haven't written anything in a long time. From now on, I'll use this blog to take notes on software, Linux, or interesting articles I've read online.

The first one is about how to ignore files in Git other than using .gitignore, something I read about [gitignore alternatives](https://nelson.cloud/.gitignore-isnt-the-only-way-to-ignore-files-in-git/) and didn't know about before.

The three files you can use to ignore files are:
```sh
* .gitignore
* .git/info/exclude
* ~/.config/git/ignore
```


## .gitignore
is the standard file that everybody knows.

## .git/info/exclude
The exclude file lives in the .git directory of every Git repository but changes to it are not checked into git. You would have a personal file in a repository that you don’t want to check into git but you also don’t want to add to .gitignore
You would add personal.txt to .git/info/exclude.

## ~/.config/git/ignore
The ignore file lives in your machine’s home directory. Filenames that are added to this file are ignored globally at a machine level. It’s a great place to add files that you want to ignore in every Git repository on your computer.
For example, if you’re on macOS, adding .DS_Store here would be ideal.

## .gitignore_global
You can use a global ignore file that allows you to ignore files across all Git repositories on your system.

```sh
touch ~/.gitignore_global
```

```sh
# Add common patterns for your system
cat << 'EOF' > ~/.gitignore_global
# macOS
.DS_Store

# Windows
Thumbs.db
Desktop.ini
$RECYCLE.BIN/

# Linux
.Trash-*

# IDEs and editors
.vscode/
.idea/
*.swp
*.swo
EOF
```

```sh
# Configure Git to use global .gitignore
git config --global core.excludesfile ~/.gitignore_global
```

```sh
# Verify configuration
git config --global --get core.excludesfile
```

And if you ever want to return to the default setting, run:

```sh
git config --global --unset core.excludesFile
```
