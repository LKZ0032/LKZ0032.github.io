---
title: new a blog article
date: 2020-02-12 13:11:39
tags: 学习笔记·hexo
---
1. 创建一个文章的md文件  
`hexo new "XXX"`  
格式如上，XXX部分为文章的名字
<!--more-->
2. 自定义文章的标签  
在md文件中的头部，tags中写入标签的类型

3. 运用markdown格式书写内容
markdown格式的学习会在之后的文章中详细的写出。

4. 编译运行网站
运行hexo的命令为  
`hexo s` 或 `hexo server`

5. 部署到远端  
如果已经在GitHub搭建了博客主页，要部署到远端，则需要一下几步（用GitHub搭建博客的方法，在之后的文章中详细的描述）：  
    - `hexo clean` (执行此步骤，直到hexo目录下的public文件夹删除)
    - `hexo g` 或 `hexo generate` (执行此步，重新编译网站文件)
    - `hexo s` 或 `hexo server` (执行此步，在本地预览，无问题的话，执行下一步，部署到远端)
    - `hexo d` 或 `hexo deploy` (执行此步，将网站public文件夹中新的文件部署到远端，过程结束)