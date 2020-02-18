# [我的学习笔记](https://chaoskey.gitee.io/notes/)

## 目录

[《机器学习：概率视角》](https://chaoskey.gitee.io/notes/docs/mlapp)

[主动投资组合管理](https://chaoskey.gitee.io/notes/docs/apm)

[微分几何](https://chaoskey.gitee.io/notes/docs/diffgeo)

[杂记](https://chaoskey.gitee.io/notes/docs/self)

## 更新流程简单舒服，页面主题简洁，完全支持Markdown+LaTeX。

1）**本地撰写**内容：用[Typora](https://www.typora.io/)写基于Markdown+LaTeX的内容。

2)   利用静态网站生成器Hugo生成待发布的静态文件： 执行`./forgitee`([下载](https://chaoskey.gitee.io/notes/assets/forgitee))即可。

- 这个脚本实际依次执行了：1. 对所有md文件进行部分内容替换，确保站点可以完全解析；2. 执行`hugo -D`,生成待发布的静态文件，生成文件夹`public`; 3. 执行`fortypora`([下载](https://chaoskey.gitee.io/notes/assets/fortypora))对所有md文件进行逆向置换复原，确保Typora打开能完全显示正常。

3） 将站点**git提交**到[码云（gitee）](https://gitee.com)： https://gitee.com/chaoskey/notes 

- 包括站点全部源码（相当于文档云同步）和生成的静态文件目录`public`。

4） 部署到： https://chaoskey.gitee.io/notes

![](https://chaoskey.gitee.io/notes/images/0103.jpg)

