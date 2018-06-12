---
layout: post
title: Attack of the Git Clones
tags: [Cyber News]
---

Git has some important configuration files that are not fetched from the server, such as the *.git/config* file and other hook files which are basically scripts. These files are run at specific times in the git workflow. For instance, the post-checkout hook is run anytime the git checks out files into the current working directory.

## Git Vulnerability
A remote repository may contain a definition of a submodule, and the submodule's repository data can be bundled as a folder in the parent directory. When a git repository is recursively cloned using the command ``git clone --recursive <project url>``, git will first checkout the parent repository into the working directory, then it realizes that the submodule is already present in the working directory and does not needs to be checked out and skips it.

The problem here is that the configuration files are not cloned from the remote repositories because this would enable the remote server to execute programs on your computer using these hooks. But, since the submodule is a part of the parent repository, it is directly coned into the working directory and hence it is possible that a carefully crafted submodule can already have these hooks configured. If such a malicious parent repository is recursively cloned, the git workflow will checkout the entire parent repository in the current working directory and then skip cloning the submodule which is already included in the parent. But, it will continue to execute the hooks at the end of the git workflow which can then enable the possibility of arbitrary code execution \[[1]\].

---

## References
\[1\] Thomson E., "Announcing the May 2018 Git Security Vulnerability".

[1]: https://blogs.msdn.microsoft.com/devops/2018/05/29/announcing-the-may-2018-git-security-vulnerability/ "Announcing the May 2018 Git Security Vulnerability"
---
