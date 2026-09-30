---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
lastmod: {{ .Date }}
draft: true
# 权重/排序可留空
# weight: 10
author: ""

# 分类与标签
categories: []
tags: []

# 摘要：出现在列表页与 SEO 描述里
summary: ""
description: ""

# 封面图：放到 static/ 或与文章同目录，写相对路径
# cover:
#   image: "cover.jpg"
#   alt: "封面"
#   caption: ""

# 展示控制
ShowToc: true
TocOpen: false
hidemeta: false
comments: true
disableHLJS: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowShareButtons: true
ShowCodeCopyButtons: true
---
