---
title: "基于Obsidian＋Hugo＋Gitee的笔记系统"
date: 2021-02-18T11:04:32+08:00
draft: true
categories: ["维护"]
tags: ["hugo","码云","obsidian","markdown","latex"]
---


> 本文基于`Typora＋Hugo＋Gitee的笔记系统`，仅把`Typora`改成`Obsidian`而已。
> 
> `Obsidian`是一个强大的知识库，可在纯文本Markdown文件的本地文件夹上运行。 支持`双向链接`（Roam Link）。
> 
> 通过`gitee`可实现云同步；通过`gitee pages`可以发布成网页。
> 
> 但是，`Hugo`无法解析`Obsidian`的`双向链接`，解决这个问题是本文的目标。
> 
>
> 【解决方案】 我通过`shell脚本`和`Hugo短代码`的配合解决这个问题。 通过`shell脚本`将`Obsidian`的`双向链接`转换成`Hugo`可以识别的自定义格式；`Hugo短代码`则负责解析这种自定义格式。
>
>
> 本笔记系统是我自用，仅供参考。

## 回顾基于Typora＋Hugo＋Gitee的笔记系统

改进后的笔记系统，支持形如```[[...]]```以及```![[...]]```的双向链接（后者支持图片），自然就支持`Obsidian`； 如果你不使用双向链接，也可以使用`Typora`（甚至任意文本编辑器）。 所以`Typora＋Hugo＋Gitee的笔记系统`是基础，具体使用方法参见我的笔记主页：[https://gitee.com/chaoskey/notes](https://gitee.com/chaoskey/notes)。此处不再多说！

## 用Hugo短代码获得目标页面路径

### 使用方式

在md文件中需要的地方插入：```{{</* roamlink "docs/fem/_index#第五章 有限元变分形式" */>}}```  。   能在插入的地方显示对应的页面相对路径，就我的笔记而言，结果是：`/notes/docs/fem/#第五章-有限元变分形式`。

又比如：```{{</* roamlink "0097" */>}}``` ， 就我的笔记而言，结果是：`/notes/docs/fem/0097/`。

如果没有重复的文件名，无须指定路径，通过hugo自定义短代吗，可以获得实际的页面路径（如第2例）；如果有重复文件名，则需要指定更详细的相对路径（如第1例）。

这种功能，就是为`双向链接`量身定制的，因为```[[...]]```或```![[...]]```可能只给出一个文件名，而非完整的路径。

### 实现方式

具体代码（Hugo短代码）见项目文件： [roamlink.html](https://gitee.com/chaoskey/notes/raw/master/themes/book/layouts/shortcodes/roamlink.html) 。

为了避免获取的页面路径含空格和换行符，代码被压缩成一行。为清晰起见，我见代码展开如下：

```html
{{ if gt (len .Params) 0 }}
    <!-- 输入解析 -->
    {{ $keys := (split (index .Params 0) "#") }}
    {{ $tag := "" }}
    {{ if (gt (len $keys) 1) }}
        <!-- 定位锚点 -->
        {{ $tag = (index $keys 1  | urlize | lower) }}
    {{ end }}
    {{ $key := (index $keys 0) }}
    <!-- 扩展名解析 -->
    {{ if not (findRE "\\.[^.]*$" $key) }}
        {{ $key = add $key ".md" }}
    {{ end }}
    <!-- 没有扩展名，则默认md文件 ，否则搜索资源文件 -->
    {{ if (strings.HasSuffix $key "md")  }}
        {{ relref . $key }}{{ if $tag }}#{{ $tag }}{{ end }}
    {{ else }}
        <!-- 解析资源路径 -->
        {{ $dir := (path.Split $key) }}
        {{ $key = $dir.File }}
        {{ if gt (len $dir.Dir) 0 }}
            <!-- 含资源路径的情况 -->
            {{ relref . $dir.Dir }}{{ $key }}
        {{ else }}
            <!-- 不含资源路径，则进一步搜索 -->
            {{ $rel :=  0  }}
            {{ range where .Site.Pages "Kind" "section" }}
                {{ if not $rel }}
                    {{ $rel =  (.Resources.GetMatch $key)  }}
                {{ end }}
            {{ end }}
            {{ if $rel }}
                {{ ($rel).RelPermalink }}
            {{ end }}
        {{ end }}
    {{ end }}
{{ end }}
```

## 双向链接和Hugo自定义格式相互转换

### 正向变换

这个功能用shell命令`sed`来实现的，作为shell脚本[forgitee](https://gitee.com/chaoskey/notes/raw/master/forgitee)的一部分。

其中的正则替换代码是：

```txt
## 将[[abc|efg]]  置换成 [efg]({{</* roamlink "abc" */>}})
s/\[\[\([^[]*\)|\([^[]*\)\]\]/[\2]({{</* roamlink "\1" */>}})/g; 

## 将[[abc]]  置换成 [abc]({{</* roamlink "abc" */>}})
s/\[\[\([^[|]*\)\]\]/[\1]({{</* roamlink "\1" */>}})/g;
```

### 逆向变换

作为shell脚本[fortypora](https://gitee.com/chaoskey/notes/raw/master/fortypora)的一部分。

```txt
## 将[abc]({{</* roamlink "abc" */>}})  置换成  [[abc]]
s/\[\([^[]*\)\]({{</*\s*roamlink\s*"\1"\s**/>}})/[[\1]]/g; 

## 将[efg]({{</* roamlink "abc" */>}})  置换成 [[abc|efg]]
s/\[\([^[]*\)\]({{</*\s*roamlink\s*"\([^"]*\)"\s**/>}})/[[\2|\1]]/g;
```

## 日常使用情景

用`Obsidian`记笔记、写作，支持`双向链接`。 

范例：

- ```[[docs/fem/_index#第十八章 Unicorn：统一的连续介质力学求解器]|章节目录]]```

- ```[[0115]]```

- ```[[0115|DOLFIN：C++/Python有限元库》变分形式/组装/边界条件]]```

- ```[[0115.md]]```

- ```![[0257.jpg|图18.2]]```

- ```![[0258.jpg]]```

- ```![[docs/images/0258.jpg]]```

【注意】```![[...]]```中的资源文件所在的目录中，必须存在`_index.md`（空文件文件也行），否则Hugo无法搜索到对应的资源文件。

如果想发布成网页，先执行命令`./forgitee  server` 可在本地打开页面查看勘误。

> 这一步，会自动进行格式变换（包括双向链接、LaTex等等的格式变换）使得`Hugo`能够正常解析。  
> 
> 如果退出服务，也会自动进行格式恢复，使得`Obsidian`能够正常解析。

如果确认无误，执行`./publish`发布到`gitee pages`。

> 这一步也会自动进行格式变换/逆变换，并将生成的静态页面发布到`gitee pages`。
> 
> 【注意】如果修改的内容没有提交&Push到Gitee，也会自动提交。 如果希望添加提交说明，建议在执行`./publish`前，先手工提交。

## 最后说明

由于此笔记系统的用户仅仅是我自己，很多细节我都配置好了，所以我自己能顺利使用。    其它人使用可能不会那么顺利，需要各自进行调整配置。  所以，本文仅供参考。