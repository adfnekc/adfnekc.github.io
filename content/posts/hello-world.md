---
title: "Hello World：我的第一篇博客"
date: 2025-01-01T10:00:00+08:00
lastmod: 2025-01-01T10:00:00+08:00
draft: false
author: "你的名字"
summary: "这是站点初始化时自带的一篇示例文章，展示 PaperMod 主题的排版、代码高亮、目录与评论区效果。"
description: "示例文章：展示 PaperMod 主题与 giscus 评论区。"
categories: ["随笔"]
tags: ["Hugo", "GitHub Pages", "教程"]
ShowToc: true
TocOpen: false
comments: true
---

Welcome! 这篇文章可以删掉，也可以作为你写作时的格式参考。

## 一、写作流程

整个博客没有任何后台，工作流就是三步：

1. 在 `content/posts/` 下新建一个 `.md` 文件
2. 用 Markdown 写内容
3. `git commit && git push`

推送到 `main` 分支后，GitHub Actions 会自动构建并发布，几秒到一分钟内就能在线上看到。

## 二、Markdown 格式示例

### 1. 标题与列表

- 无序列表
- **加粗**、*斜体*、`行内代码`
- [链接](https://github.com/adityatelange/hugo-PaperMod)

1. 有序列表
2. 第二项

### 2. 引用

> 这是一段引用。支持多行。
>
> > 也支持嵌套引用。

### 3. 代码块（自动高亮）

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, GitHub Pages!")
}
```

```bash
hugo new posts/my-new-post.md
hugo server -D
```

### 4. 表格

| 项目 | 说明 |
| --- | --- |
| 静态生成器 | Hugo |
| 主题 | PaperMod |
| 托管 | GitHub Pages |
| 评论 | giscus（GitHub Discussions） |

### 5. 任务清单

- [x] 初始化仓库
- [x] 配置主题
- [ ] 写第一篇文章
- [ ] 配置 giscus 评论

### 6. 图片

把图片放在 `static/images/` 下，然后用绝对路径引用：

```markdown
![示例图片](/images/example.png)
```

## 三、关于评论

页面底部的评论区由 **giscus** 提供，每一篇文章对应 GitHub Discussions 里的一个讨论帖。
你需要用 GitHub 账号登录后才能发言——这既是限制，也是天然的防垃圾评论机制。

## 四、关于目录

这篇文章开启了 `ShowToc: true`，右侧（桌面端）或顶部（移动端）会显示由标题自动生成的目录。

---

Happy writing! 🎉
