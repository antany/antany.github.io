---
title : Shortcuts, Alias and Configurations
categories: Linux Devops
tags: linux devops
description: My simple shortcuts, aliases, and configurations to speed up everyday tasks effortlessly

last_modified_at: 2025-03-02 18:41:00 -0530
---

## Bash Alias

```bash
# stop all docker containers
alias dockersa='docker stop $(docker ps -aq)'

# remove all stopped docker containers
alias dockerra='docker rm $(docker ps -aq)'

```


## Git Configurations
```config
#.gitconfig

[alias]
    lg = log  --graph --pretty=format:'%C(auto)%h %C(dim green)[C]:%<(15,trunc)%cn %C(bold magenta)[A]:%<(15,trunc)%an %C(bold white)%<(55,mtrunc)%s  %C(bold)%cD (%cr) %d' --date=iso -25 
```
