---
title: GitHub 工作流
date: 2023/11/12
categories:
- [计算机科学, Git]
tags: 杂项
cover: assets/pexels-02.jpg
---

# GitHub 工作流

+++info 阅读提示
本文记录一个常见的 GitHub 协作流程：本地从 main 拉分支开发 → 提交到远端分支 → main 更新时用 rebase 同步 → 发起 PR → squash merge → 清理远端/本地分支。适合个人开发或小团队协作。
+++

## 标准流程（从克隆到合并）

1. 将远程 GitHub 仓库克隆到本地：
   - `git clone <repo_url>`

2. 从 `main` 创建并切换到新分支（例如 `my_branch`）：
   - `git checkout -b my_branch`

3. 在新分支 `my_branch` 上修改代码，查看变更：
   - `git diff`

4. 将需要提交的修改加入暂存区：
   - `git add <changed_name>`

5. 提交到本地仓库：
   - `git commit`

6. 将本地分支推送到远端：
   - `git push origin my_branch`

## main 更新时：同步到分支（rebase）

7. 如果发现 `main` 分支有更新，需要把 `main` 的改动同步到 `my_branch`。

8. 切换到本地 `main`：
   - `git checkout main`

9. 拉取远端 `main` 更新本地：
   - `git pull origin main`

10. 切回开发分支：
   - `git checkout my_branch`

11. 将 `main` 的更新 rebase 到当前分支：
   - `git rebase main`

12. 如果产生冲突：手动解决冲突后继续 rebase（直到完成）。rebase 成功后，相当于在最新 `main` 上“重新播放”了自己的提交。

13. rebase 后再次推送分支到远端：
   - `git push origin my_branch`

## 合并到 main 与清理分支

14. 在 GitHub 上创建 Pull Request，将 `my_branch` 合并到 `main`。

15. 合并策略选择 `squash and merge`：把分支上的多个 commit 压缩成一个 commit 合入 `main`，使主分支历史更干净。

16. 合并完成后，在 GitHub 上删除远端分支（Delete branch）。

17. 本地删除分支前先切回 `main`：
   - `git checkout main`
   - `git branch -D my_branch`

18. 最后更新本地 `main`：
   - `git pull origin main`

{% links %}
- site: "GitHub Docs：Pull Requests"
  owner: "GitHub"
  url: "https://docs.github.com/en/pull-requests"
  desc: "PR 流程与合并策略（squash / merge / rebase）"
  image: "assets/pexels-15.jpg"
  color: "#e9546b"
- site: "Git 文档：rebase"
  owner: "Git"
  url: "https://git-scm.com/docs/git-rebase"
  desc: "rebase 的原理、冲突处理与常见用法"
  image: "assets/pexels-11.jpg"
  color: "#4e7cff"
{% endlinks %}

