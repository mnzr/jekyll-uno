---
published: true
layout: post
date: 2015-04-15T11:03:31.000Z
description: Solution to .gitignore file not ignoring
categories:
  - Tips
tags:
  - git
  - tips
---
Sometimes when you manually edit the .gitignore file in a repo to ignore some specific files, git doesn't really do that immediately. The reason for this is: the files you want git to ignore is already in git’s cache, so the system can’t “unsee” those files. Remedy to this problem is very simple:
- Commit any progress, otherwise they will get lost
- Then run the command below with the file that's not being ignored:

``` git rm --cached path/to/file ```

- But if the file number is huge, remove them al from cachel at once:

``` git rm -r --cached . ```

- Lastly, simply commit again:

```git commit -m "fixed .gitignore"```

Tada, problem solved! All the files you mentioned in .gitignore will get ignored once again, as intended. You can check it out using ``` git status ```.