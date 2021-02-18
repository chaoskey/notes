# [我的学习笔记](https://chaoskey.gitee.io/notes/)

## 目录

[《有限元法自动求解微分方程》【翻译】](https://chaoskey.gitee.io/notes/docs/fem/)

[基于Julia科学计算](https://chaoskey.gitee.io/notes/docs/julia/)

[理论物理学习笔记](https://chaoskey.gitee.io/notes/docs/theophy/)

[微分几何](https://chaoskey.gitee.io/notes/docs/diffgeo)

[机器学习：概率视角](https://chaoskey.gitee.io/notes/docs/mlapp)

[主动投资组合管理](https://chaoskey.gitee.io/notes/docs/apm)

## 更新流程简单舒服，页面主题简洁，完全支持Markdown+LaTeX。

以 [用Hugo+码云搭建支持Markdown+LaTeX的云笔记](https://chaoskey.gitee.io/notes/posts/0000/) 为准。

1）**本地撰写**内容：用[Typora](https://www.typora.io/)写基于Markdown+LaTeX的内容。

2）利用静态网站生成器Hugo生成待发布的静态文件： 执行`./forgitee`([下载](https://gitee.com/chaoskey/notes/raw/master/forgitee))即可。

- 这个脚本实际依次执行了：1. 对所有md文件进行部分内容替换，确保站点可以完全解析； 2. 执行`hugo -D`,生成待发布的静态文件，生成文件夹`public`;  3. 执行`./fortypora`对所有md文件进行逆向置换复原，确保Typora打开能完全显示正常。

3） 将站点**git提交**到[码云（gitee）](https://gitee.com)： https://gitee.com/chaoskey/notes 

- 包括站点全部源码（相当于文档云同步）和生成的静态文件目录`public`。

4） 部署到： https://chaoskey.gitee.io/notes

5） 一键发布的脚本`./publish`（注意，必须根据你自己的情况修改之），依次执行了： 1.  `./forgitee`;  2.  将修改过的笔记提交到`master`分支； 3. 将`public`提交到`gh-pages`分支；  4. 将`gh-pages`分支`push`到`gitee`和`github`。

![](https://chaoskey.gitee.io/notes/images/0103.jpg)

## 支持双向链接（Roam Link）

具体参见： [基于Obsidian＋Hugo＋Gitee的笔记系统](https://chaoskey.gitee.io/notes/posts/%E5%9F%BA%E4%BA%8EObsidianHugoGitee%E7%9A%84%E7%AC%94%E8%AE%B0%E7%B3%BB%E7%BB%9F/)


