---
title: "Git: How to do pre-commit"
tags: [git]
categories: Git
cover: images/Git/cover.png
excerpt: Whenever I want to commit, I always commit the object file created by C++ to the github server. This cannot be simply specified by the .gitignore file, a better way is to specify this in the pre-commit file
---

<!-- toc -->

## STEP1: Find .git/hooks
Find the hooks folder in the (hidden) .git configuration folder
```shell
cd .git/hooks
```

## STEP2: Create `pre-commit` file
```shell
touch pre-commit
``` 

## STEP3: edit `pre-commit` file
```shell
vi pre-commit
```
and add something like this:

```shell
#!/bin/bash

# store the top level git repository path
root_directory=$(git rev-parse --show-toplevel)

# list all subdirectories
subdirectories=("HW1", "HW2", "HW3")

# iterate over each subdirectory
for subdir in "$(subdirectories[@])";
do
    (cd "$root_directory/$subdir" && make clean)
done

# Continue with the commit
exit 0
```

Now whenever you commit, it will automatically clean the subdirectory and will not commit any executable files. Note, if the operating system changes, the script will also need to change.