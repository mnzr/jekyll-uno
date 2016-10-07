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
Sometimes after changing your .gitignore the new (un)ignored files don't get get behind the scenes, as you would have expected. The reason behind this is you have already committed the files that you added to .gitignore and now git is tracking the files. Unless you delete them git will keep an eye on any changes on those files as it would do with any other. To get the new .gitignore rule registered with the repo you will have to manually remove tho file(s) from git's cache.

So here is what you need to do:

STEP 1: Commit any staged progress, otherwise they will get lost
STEP 2: Run this with the file name. If there are way to many, replace `<file>` with `.` to remove all files from cache:

``` shell
git rm -r --cached <file> 
```

STEP 3: Now no files are in cache, so add them except the ones mentioned in .gitignore:

``` shell
git add . 
```

STEP 4: Lastly, simply commit again:

``` shell
git commit -m "fixed .gitignore" 
```

Tada, problem solved! If you ever want to undo this, just add the file to git:

``` shell
git add <file> 
```

