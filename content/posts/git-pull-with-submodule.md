---
title: "Git Pull With Submodule"
date: 2021-05-01T11:09:48+08:00
draft: true
---

#tips

For a repo with submodules, we can pull all submodules with

```
git submodule update --init --recursive
```

for the first time. All submodules will be pulled down locally.

To update submodules, we can use

> For git **1.8.2** or above, the option --remote was added to support updating to latest tips of remote branches

```
git submodule update --recursive --remote
```

This has the added benefit of respecting any "non default" branches specified in the `.gitmodules` or `.git/config` files.

For git **1.7.3** or above you can use

```
git submodule update --recursive
```

or simply

```
git pull --recurse-submodules
```

## TL;DR

For a repo with no pulled submodules yet, do

```
git submodule update --init --recursive
```

To update submodules, do

```
git submodule update --recursive --remote
```

> References:
> 
> 1. [Easy way to pull latest of all git submodules](http://stackoverflow.com/questions/1030169/easy-way-pull-latest-of-all-submodules)
