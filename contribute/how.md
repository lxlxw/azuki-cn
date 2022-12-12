# 如何贡献

## 目标

贡献使开源社区成为学习、启发和创造的绝佳场所。

我们的目标是让对 Azuki 的贡献变得简单和透明。

非常感谢您所做的任何贡献。

## 从Github编辑

通过拉取请求提出对项目的更改。一般的 pull request 工作流程如下：

1. Open a pull request
2. Push to the branch (`git push origin feature/DocsFeature`)
3. Commit your changes (`git commit -m 'Add some DocsFeature'`)
4. Create your feature branch (`git checkout -b feature/DocsFeature`)
5. Fork the project

### 编辑清洁提交

#### 逻辑上独立的提交

提交应该是[原子](https://en.wikipedia.org/wiki/Atomic\_commit#Atomic\_commit\_convention)的，并分解为逻辑上独立的更改。差异也应该便于审阅者阅读和审阅。

#### 有意义的提交信息

当其他人深入了解提交历史以了解为什么进行特定更改以及它打算解决什么问题时，提交消息非常重要并且对他们非常有帮助。出于这个原因，提交消息应该写得很好并符合以下格式：

所有提交消息都应该以一个简短的（最多 50 个字符）行开始，总结更改并且应该跳过句号。这是提交的标题。还优选该摘要以“\[area]”为前缀，其中 area 是被修改代码的一般区域的标识符，例如

```
* [ci] enforce whitelist of nightly features
* [language] removing VerificationPass trait
```

在提交标题之后（除非它本身是不言自明的），应该有一个空白行，然后是提交主体，其中包括作为单独段落的更详细的解释性文本。建议提交正文以 72 个字符换行，以便 Git 有足够的空间来缩进文本，同时仍将所有内容总体保持在 80 个字符以下。

提交正文应提供有意义的提交消息，其中：

* 解释提交内容的作用与目的。
* 证明改变内容，即为什么改变的结果更好。
* 考虑过但放弃的替代内容（如果有的话）。

### 回应审稿人的反馈

在审查过程中，审查者可能会要求您更改您的拉取请求。如果需要更改特定提交，则应直接修改该提交。_不应_在您的 PR 之上的单独提交中进行响应审查的更改，除非对这些更改进行单独、不同的提交在逻辑上是有意义的。这有助于保持提交历史的清洁。

如果您的拉取请求已过时并且需要更新，因为`main`已经先进，您应该通过执行以下操作将您的分支重新定位在最新的主分支之上：

```
git fetch upstream
git checkout topic
git rebase -i upstream/main
```

您_不应该_通过将最新的主分支合并到您的分支来更新您的分支。PR 中包含的合并提交往往会使审阅者更难以理解所做的更改，尤其是在合并不干净且需要解决冲突的情况下。因此，具有合并提交的 PR 将被拒绝。

### 问题

Auzki CN 使用[GitHub 问题](https://github.com/lxlxw/azuki-cn/issues)来跟踪错误。请包括必要的信息和说明以说明您的问题。



## 从Gitbook编辑

请发送邮件[contact@azukicn.com](https://mail:contact@azukicn.com)让我们知道。



\


