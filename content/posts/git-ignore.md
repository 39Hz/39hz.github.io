---
title: "Git 仓库整理术：.gitignore 不完全指南"
date: 2025-06-11T12:00:00+08:00
author: "39Hz"
tags: ["Git"]
categories: ["教程"]
cover:
  image: "/images/git-ignore/git-ignore-cover.png"
  caption: "Git Commands Visualized"
  relative: false
---

# Git 仓库整理术：.gitignore 不完全指南

![Git-Logo](https://git-scm.com/images/logos/downloads/Git-Logo-2Color.png){width=50%}

作为开发者，我们每天都在和 Git 打交道。一个干净、清晰的提交历史是高效协作和维护项目的基础。但你是否遇到过这样的情况：

-   不小心把 `node_modules` 文件夹提交到了仓库，导致仓库体积暴增？
-   `console.log` 的调试文件、IDE 的配置文件（如 `.vscode/` 或 `.idea/`）也出现在了提交记录里？
-   包含了敏感信息（如 API 密钥）的 `.env` 文件被公开到了 GitHub？
-   MacOS 上讨厌的 `.DS_Store` 文件也混入了版本控制？

如果答案是肯定的，那么你需要立刻了解并使用 `.gitignore` 文件。它是一个简单但极其强大的工具，能帮助你从源头上解决这些问题。

## 什么是 .gitignore？

`.gitignore` 是一个纯文本文件，它的作用是告诉 Git **哪些文件或目录应该被忽略，不纳入版本控制**。

当你执行 `git add .` 时，Git 会检查当前目录下是否存在 `.gitignore` 文件，并根据其中的规则来决定哪些文件不会被暂存（staged）。

### 为什么它如此重要？

1.  **保持仓库整洁**：避免提交自动生成的文件、日志文件、编译产物等。这让你的提交历史专注于真正的代码变更。
2.  **减小仓库体积**：像 `node_modules` 这样的目录包含了成千上万个文件，将其排除能极大地减小仓库的体积，加快克隆、拉取的速度。
3.  **避免安全风险**：防止将包含密码、API 密钥或其他敏感信息的配置文件（如 `.env`）意外提交到公共仓库。
4.  **减少合并冲突**：忽略针对个人开发环境的配置文件（如 IDE 设置），可以避免团队成员之间因此产生不必要的合并冲突。

##　最佳实践和技巧

1.  尽早创建: 在项目初始化 (git init) 后就立即创建 .gitignore 文件，这样可以从一开始就避免错误提交。

2.  使用模板: GitHub 维护了一个非常全面的 [gitignore 模板仓库](https://github.com/github/gitignore)，涵盖了几乎所有的编程语言和框架。你可以直接从中找到适合你项目的模板，并根据需要进行修改。

3.  配置全局 .gitignore: 有些文件（如操作系统文件或编辑器配置）是你希望在所有项目中都忽略的。你可以配置一个全局的 .gitignore 文件。
    ```
    # 创建全局忽略文件
    touch ~/.gitignore_global

    # 告诉 Git 它的位置
    git config --global core.excludesfile ~/.gitignore_global
    ```


## 如何使用 .gitignore？

使用起来非常简单。你只需要在你的项目根目录下创建一个名为 `.gitignore` 的文件，然后把你想忽略的文件或目录模式写进去，每行一个。

### 基础语法和匹配规则

`.gitignore` 的规则非常直观，以下是一些核心语法：

1.  **注释**: 以 `#` 开头的行是注释，会被 Git 忽略。
    ```gitignore
    # 这是一个注释
    ```

2.  **忽略特定文件**: 直接写文件名即可。
    ```gitignore
    # 忽略根目录下的 debug.log 文件
    debug.log
    ```

3.  **忽略目录**: 在名称后加上斜杠 `/` 来表示这是一个目录。
    ```gitignore
    # 忽略 node_modules 目录下的所有内容
    node_modules/
    ```

4.  **使用通配符**:
    -   `*` 匹配零个或多个字符。
    -   `?` 匹配一个任意字符。
    -   `[]` 匹配方括号中的任意一个字符。

    ```gitignore
    # 忽略所有 .log 文件
    *.log

    # 忽略所有 .tmp 文件
    *.tmp

    # 忽略名为 a, b, c 的文件
    [abc].txt
    ```

5.  **指定路径**:
    ```gitignore
    # 只忽略根目录下的 TODO 文件，不忽略子目录下的
    /TODO

    # 忽略所有名为 logs 的文件夹
    logs/

    # 忽略 build/logs/ 目录下的所有 .log 文件
    build/logs/*.log
    ```

6.  **双星号 `**`**: 匹配任意多级目录。
    ```gitignore
    # 忽略任何位置的 a.txt 文件
    **/a.txt

    # 忽略所有 __pycache__ 目录
    **/__pycache__/
    ```

7.  **例外规则（取反）**: 在模式前加上感叹号 `!` 表示不忽略。
    **注意**：如果一个文件的父目录被忽略了，那么这个文件即使被设置为例外也无法生效。

    ```gitignore
    # 忽略所有 .log 文件
    *.log

    # 但是不忽略 important.log 这个文件
    !important.log

    # 这个规则会失效，因为整个 logs 目录已经被忽略了
    logs/
    !logs/important.log
    ```

