---
title: "Git Commands Visualized - Git 命令可视化指南"
date: 2025-05-22T12:00:00+08:00
author: "39Hz"
tags: ["Git"]
categories: ["教程"]
cover:
  image: "/images/git-commands-visualized-cover.png"
  caption: "Git Commands Visualized"
  relative: false
---

> [🔗 原文链接 🌳🚀 CS Visualized: Useful Git Commands](https://dev.to/lydiahallie/cs-visualized-useful-git-commands-37p1)

-----

尽管 Git 是一个非常强大的工具，但我想大多数人都会同意，它的命令行有时也可能……像一场突如其来的噩梦 😐。别担心！我一直觉得，在脑海中将 Git 操作可视化对于理解其运作机制非常有帮助：当我执行某个命令时，分支是如何交互的？它将如何影响提交历史？为什么当我对 `master` 分支执行了 `hard reset`、`force push` 到 `origin` 并且 `rimraf` 掉了 `.git` 文件夹后，我的同事会崩溃大哭呢？（温馨提示：请勿在共享仓库轻易尝试最后那部分操作！）

因此，我认为这将是为一些最常用且最实用的 Git 命令创建可视化示例的绝佳机会！🥳 本文将介绍许多命令，它们大多拥有可选参数以改变其行为。在我的示例中，我将着重展示命令的默认行为，尽量不添加（太多）额外的配置选项，力求简洁明了！😄

-----

## [](https://www.google.com/search?q=git+merge)合并 (Merge)

在项目中拥有多个分支，可以将新功能的开发、bug 修复等工作彼此隔离开来，这非常方便。它确保了我们不会意外地将未经批准或可能损坏的代码推送到生产环境。一旦更改得到团队的认可，我们就希望将这些宝贵的成果合并回我们的主生产分支中！

将更改从一个分支应用到另一个分支的常用方法之一就是执行 `git merge`！Git 主要可以执行两种类型的合并：**快进 (fast-forward)** 🐇 或 **非快进 (no-fast-forward)** 🐢。

这听起来可能有点抽象，让我们通过可视化来一探究竟！

### [](https://www.google.com/search?q=git+fastforward)快进 (`--ff`)

当你的目标分支（例如 `master`）在你的特性分支（例如 `dev`）创建之后，没有产生任何新的提交时，就可以进行**快进合并**。在这种情况下，Git 有点……*小聪明*，它会首先尝试最简单的选项：快进！这种类型的合并不会创建新的提交记录，而是直接将待合并分支（`dev`）上的提交“快速移动”到当前分支（`master`）的顶端 🥳。

![快进合并](/images/git-commands-visualized-merge-ff.gif)

完美！现在 `master` 分支就拥有了 `dev` 分支上的所有更改。那么，**非快进合并**又是什么情况呢？

### [](https://www.google.com/search?q=git+nofastfoward)非快进 (`--no-ff`)

如果你的当前分支与你想要合并的分支相比没有任何额外的提交，那自然是理想状况，但不幸的是，现实中这种情况并不总是发生！如果我们当前所在的分支（比如 `master`）上，有一些我们想要合并的分支（比如 `dev`）所没有的新提交，Git 就会执行一次*非快进合并*。

通过非快进合并，Git 会在你的活动分支（`master`）上创建一个新的*合并提交*。这个特殊的提交有两个父提交：一个指向当前的活动分支，另一个指向我们想要合并进来的分支！

![非快进合并](/images/git-commands-visualized-merge-no-ff.gif)

干得漂亮，一次完美的合并！🎉 `master` 分支现在包含了我们在 `dev` 分支上所做的所有更改，并且保留了各自独立开发的历史。

### [](https://www.google.com/search?q=git+merge-conflicts)合并冲突 (Merge Conflicts)

尽管 Git 在决定如何合并分支和智能地将更改应用到文件方面非常出色，但它并不总能自己做出所有决定 🙂。当我们要合并的两个分支在同一个文件的同一行上都有更改，或者一个分支删除了另一个分支中修改过的文件时，就可能发生合并冲突。

在这种情况下，Git 会暂停合并过程，并请求你介入，帮助决定保留哪个版本的更改！假设在我们的 `master` 和 `dev` 两个分支中，我们都编辑了 `README.md` 文件的第一行。

![冲突](/images/git-commands-visualized-conflict.png)

如果我们想将 `dev` 分支合并到 `master` 分支，Git 就会提示合并冲突：你希望标题是 `Hello!` 还是 `Hey!` 呢？

当尝试合并时，Git 会在冲突的文件中用特殊标记标示出冲突的具体位置。我们可以手动编辑这些文件，移除我们不想要的更改，保留需要的版本，然后保存文件。完成冲突解决后，重新 `git add` 已更改的文件，最后执行 `git commit` 来完成这次合并 🥳。

![合并冲突](/images/git-commands-visualized-merge-conflict.gif)

太棒了！尽管合并冲突有时确实很烦人，但它们的存在是完全合理的：Git 不应该替我们*主观臆断*想要保留哪些更改。

-----

## [](https://www.google.com/search?q=git+rebase)变基 (Rebase)

我们刚刚看到了如何通过执行 `git merge` 将一个分支的更改应用到另一个分支。另一种将一个分支的更改集成到另一个分支的方法是执行 `git rebase`。

`git rebase` 的核心思想是：它会*复制*当前分支（例如 `dev`）的提交，然后将这些复制的提交逐个应用到你指定的目标分支（例如 `master`）的顶部。

![变基](/images/git-commands-visualized-rebase.gif)

搞定！我们现在 `dev` 分支上就拥有了 `master` 分支上的所有最新更改，并且 `dev` 分支的提交历史看起来像是紧接着 `master` 分支之后开发的，形成了一条直线 🎊。

与合并（`merge`）相比，一个显著的区别是 `rebase` 通常不会创建合并提交（除非发生冲突并解决）。它会努力保持一个更清晰、更线性的 Git 历史。当我们把 `dev` 分支变基到 `master` 分支上时，`dev` 分支上的提交会“移动”到 `master` 的最新提交之后。

**重要提示：** `git rebase` **会改变项目的提交历史**，因为它会为那些被“移动”的提交创建新的哈希值！

因此，当你在一个已经被推送到远程并且可能有多人协作的分支上工作时，要非常小心使用 `rebase`。然而，`rebase` 在以下场景中非常有用：
当你在自己的特性分支上开发，同时主分支（如 `master` 或 `main`）接收了新的更新。你可以将主分支的最新更改通过 `rebase` 同步到你的特性分支上，这样可以帮助你提前解决潜在的冲突，并使你的特性分支在合并回主分支时更加顺畅！😄

### [](https://www.google.com/search?q=git+interactive-rebase)交互式变基 (Interactive Rebase)

在 `rebase` 过程中，我们甚至可以修改即将被应用的提交！这可以通过强大的*交互式变基*（`git rebase -i` 或 `git rebase --interactive`）来实现。当你想要整理当前工作分支上的一系列提交时，交互式变基也同样非常有用。

在交互式变基模式下，我们可以对选定范围内的每个提交执行以下操作：

- `pick` (p): 使用该提交 (这是默认操作)
- `reword` (r): 使用该提交，但修改提交信息
- `edit` (e): 使用该提交，但暂停以便修改此提交 (例如，拆分提交或修改文件内容)
- `squash` (s): 将此提交合并到前一个提交中，并合并它们的提交信息
- `fixup` (f): 将此提交合并到前一个提交中，但丢弃此提交的日志信息
- `exec` (x): 在每个提交被应用到分支之前，在其上运行一个 shell 命令
- `drop` (d): 删除该提交
- `label` (l): 为当前 `HEAD` 打上标签
- `reset` (t): 重置 `HEAD` 到某个标签
- `merge` (m): 创建一个合并提交，但保留原始合并提交的分支。(通常用于重新创建一个合并)

太棒了！这样，我们就能完全掌控我们的提交历史。例如，如果我们想删除某个不再需要的提交，只需在交互式界面中将其标记为 `drop`。

![变基-删除提交](/images/git-commands-visualized-rebase-drop.gif)

或者，如果我们想将多个相关的微小提交（比如一些 "WIP" 提交或小的修复）压缩成一个更有意义的提交，以保持历史记录的整洁，`squash` 或 `fixup` 将是你的好帮手！

![变基-合并提交](/images/git-commands-visualized-rebase-squash.gif)

交互式变基赋予了你对提交进行精细化管理的强大能力，即使是在你当前的活动分支上，也能助你打造一个干净、专业的提交历史！

-----

## [](https://www.google.com/search?q=git+reset)重置 (Reset)

有时我们可能会提交一些之后发现并不想要的更改。也许是一个临时的 `WIP` (Work In Progress，进行中) 提交，或者是一个不小心引入了错误的提交！🐛 在这种情况下，我们可以使用 `git reset` 来回退。

`git reset` 命令用于将当前 `HEAD` 指向指定的提交，并且根据你选择的模式，它可以选择性地修改暂存区（Staging Area/Index）和工作目录（Working Directory）。

### [](https://www.google.com/search?q=git+soft-reset)软重置 (Soft reset)

*软重置* (`git reset --soft <commit>`) 会将 `HEAD` 指针移动到指定的提交，但它会保留在那之后的所有提交所引入的更改。这些更改会被放回暂存区（Staging Area），就好像你刚刚 `git add` 了它们一样。你的工作目录中的文件内容不会改变。

假设我们不想保留添加了 `style.css` 文件的提交 `9e78i`，也不想保留添加了 `index.js` 文件的提交 `035cc`。但是，我们确实想保留新添加的 `style.css` 和 `index.js` 文件本身的内容，并可能将它们合并成一个新的提交。这是软重置的完美用例。

![软重置](/images/git-commands-visualized-reset-soft.gif)

执行软重置后，当你输入 `git status` 时，你会看到先前提交所做的所有更改都已暂存。这非常棒，因为这意味着我们可以修改这些文件的内容，或者将它们合并成一个单一的、更完善的提交！

### [](https://www.google.com/search?q=git+hard-reset)硬重置 (Hard reset)

有时，我们不希望保留某些提交引入的任何更改，也包括工作目录中的改动。与软重置不同，我们不再需要访问这些被丢弃的更改。Git 应该彻底将其状态重置回指定提交时的状态：这会同时改变 `HEAD` 指向、暂存区内容以及你工作目录中的文件！💣

**警告：`git reset --hard` 是一个具有破坏性的操作，它会丢弃所有未提交的更改以及指定提交之后的所有提交内容。请谨慎使用！**

![硬重置](/images/git-commands-visualized-reset-hard.gif)

Git 已经彻底丢弃了在 `9e78i` 和 `035cc` 上引入的更改，并将仓库的状态（包括工作目录）重置回了提交 `ec5be` 时的样子。

（还有一种介于两者之间的 `git reset --mixed <commit>`，这是默认模式。它会移动 `HEAD` 指针，并重置暂存区，但保留工作目录中的更改。这些更改会显示为未暂存状态。）

-----

### [](https://www.google.com/search?q=git+revert)还原 (Revert)

另一种安全地撤销更改的方法是执行 `git revert`。通过 `revert` 某个提交，我们会创建一个*全新的提交*，这个新提交的内容恰好是指定提交更改的“反操作”。

假设提交 `ec5be` 添加了一个 `index.js` 文件。后来，我们意识到我们实际上不想要这个提交引入的更改了！让我们来 `revert` 提交 `ec5be`。

完美！新的提交 `9e78i` (这是一个示例哈希值) 准确地还原了由提交 `ec5be` 所引入的更改（例如，如果 `ec5be` 是添加文件，则 `9e78i` 会删除该文件）。执行 `git revert` 对于撤销某个特定提交非常有用，因为它不会修改分支的现有历史记录，而是向前追加一个新的“撤销”提交，这对于已经推送到共享仓库的提交来说是更安全的选择。

-----

## [](https://www.google.com/search?q=git+cherrypick)拣选 (Cherry-pick)

当某个其他分支上的特定提交包含了我们当前活动分支恰好需要的更改时，我们可以使用 `git cherry-pick <commit-hash>` 来“拣选”那个提交！通过 `cherry-pick` 一个提交，我们会在当前的活动分支上创建一个新的提交，这个新提交包含了被拣选的提交所引入的全部更改。

假设 `dev` 分支上的提交 `76d12` 对 `index.js` 文件做了一个重要的修复或添加了一个小功能，而我们希望这个更改也能够应用到 `master` 分支上。但我们不想要 `dev` 分支上的所有其他更改，只关心这一个特定的提交！

![拣选](/images/git-commands-visualized-cherry-pick.gif)

酷！现在 `master` 分支上也拥有了原先在 `dev` 分支上由提交 `76d12` 引入的更改，并且它是作为一个全新的提交出现在 `master` 分支上的。

-----

## [](https://www.google.com/search?q=git+fetch)抓取 (Fetch)

如果我们有一个远程 Git 仓库（例如 GitHub 上的一个仓库），那么这个远程仓库可能包含了我们本地当前分支所没有的最新提交！也许是你的同事合并了一个新特性，或者推送了一个紧急修复等等。

我们可以通过对远程仓库执行 `git fetch <remote-name>` (例如 `git fetch origin`) 来在本地获取这些最新的更改信息！`git fetch` 命令会下载远程仓库的数据到你的本地仓库，但它并**不会**自动合并或修改你当前的工作。它只会更新你本地仓库中对应的远程跟踪分支（例如 `origin/master`）。。

[](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fi%2Fbulx1voegfji4vwgndh4.gif)

*(上图演示了 `git fetch` 后，本地的 `origin/master` 远程跟踪分支被更新以反映远程仓库的状态)*

执行 `git fetch` 后，我们就可以在本地查看自上次同步以来远程仓库发生的所有更改（例如，通过 `git log master..origin/master`）。既然我们已经在本地获取了新数据，接下来就可以从容决定如何处理这些更新了，比如可以选择合并（`merge`）或变基（`rebase`）。

-----

## [](https://www.google.com/search?q=git+pull)拉取 (Pull)

虽然 `git fetch` 对于获取分支的远程信息非常有用，但我们也可以直接执行 `git pull <remote-name> <branch-name>` (例如 `git pull origin master`)。实际上，`git pull` 是两个命令的组合：它首先执行 `git fetch` 来获取最新的数据，紧接着，它会尝试将最新的更改自动**合并**（默认行为）到你当前的本地分支中。

太棒了！执行 `git pull` 后，我们的本地分支现在与远程分支完全同步，并拥有了所有最新的更改！🤩 （注意：如果本地有未提交的更改与拉取的更改冲突，或者远程历史与本地历史有分歧，`pull` 可能会遇到合并冲突或需要你解决的问题。）

![拉取](/images/git-commands-visualized-pull.gif)

太棒了，我们现在与远程分支完全同步，并拥有所有最新的更改！🤩

-----

## [](https://www.google.com/search?q=git+reflog)引用日志 (Reflog)

每个人在操作 Git 时都可能会犯错，这完全没关系！有时你可能会觉得你把 Git 仓库搞得一团糟，甚至想过要不要干脆删掉重来。在你这么做之前，请了解 `git reflog`！

`git reflog` (Reference Log, 引用日志) 是一个非常有用的命令，它可以显示一份记录了 `HEAD` 和你的分支指针在过去一段时间内所有移动的日志！这包括了合并、重置、还原、提交、切换分支等等——基本上是你对本地仓库所做的任何会改变分支顶端指向的操作。

![引用日志](/images/git-commands-visualized-reflog.gif)

如果你不小心执行了一个错误的操作（比如一次错误的 `reset`），你可以查阅 `reflog`，找到在那个错误操作之前的状态（通常表示为 `HEAD@{<number>}`），然后使用 `git reset` 命令（例如 `git reset --hard HEAD@{<number>}`）将你的分支恢复到那个安全的状态，从而轻松地撤销错误！

假设我们实际上不想合并 `origin` 分支。当我们执行 `git reflog` 命令时，我们看到合并前仓库的状态位于 `HEAD@{1}`。那我们就执行 `git reset --hard HEAD@{1}`，将 `HEAD` 指回它在 `HEAD@{1}` 时的位置！

![引用日志-重置](/images/git-commands-visualized-reflog-reset.gif)

我们可以看到，最新的操作（这次是 `reset`）也已经被记录到了 `reflog` 的顶部！`git reflog` 真的是 Git 中的“后悔药”。

-----

> **关于分支命名的一点说明：** 在 Git 的早期版本中，默认的主干分支通常命名为 `master`。然而，为了促进一个更具包容性的社区标准，像 GitHub 这样的代码托管平台以及许多项目现在都推荐并默认使用 `main` 作为主干分支的名称。在本文的示例中，我们可能交替使用 `master`，请考虑在你的新项目中使用 `main`。

-----
