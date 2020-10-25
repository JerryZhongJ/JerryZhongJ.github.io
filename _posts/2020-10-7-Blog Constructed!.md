---
title: 博客建成!
cover: IMG_4319.PNG
header:
    theme: light
article_header:
    image:
        src: IMG_4319.PNG
tags: ["博客", "GitHub Pages", "jekyll", "Ruby"] 
---
今天, 我的github pages博客历时三天, 终于成型. 大概的样式已经确定, 可能还会有有些小修小补, 以及, 我还要推出一个图集板块, 用于展示一些二次元的美图, (还要研究一下怎么给图片打标签).

## 回顾

建立这个博客总共用了3天时间, 这是非常不应该的, 在此我回顾一下我搭建博客的过程,以期从中能够学习到什么.

### 认识 github pages

我选择在github pages搭建博客, 纯粹是因为github比较技术宅一点. 我可以考虑用服务器, 但那就太跃进了, 我怕步子迈的太大. 我可以用简书, csdn, 但那就毫无搭建一说, 没啥意思, 而且不能自定义.

一开始, 读了github pages 的 tutorial, 了解了怎么启用一个github pages, 怎么简单的实现一个单页面的博客. 这里比较简单, 但也花了我2h.

#### github pages 是什么

github pages有三种: 其中**用户或组织**的pages需要建立名为'\<username\>.github.io或\<organization\>.github.io的库, 的url为 'https//\<username\>.github.io' 或 'https://\<organization\>.github.io', 每个账户只能建立一个用户page或组织page. 对于项目的pages, 即在原本的库中, url 为http(s)://\<user\>.github.io/\<repository\> or http(s)://\<organization\>.github.io/\<repository\>.
> You can only create one user or organization site for each github
account.[^aboutgp]

[^aboutgp]:<https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/about-github-pages>

github pages是公开的, 尽管可能与之相关的库是private的.
> Warning: github pages sites are publicly available on the internet, even if the repository for the site is private or internal. If you have sensitive data in your site's repository, you may want to remove it before publishing.

#### github pages 源

github pages的源是一个**分支**下的一个**文件夹**. github会自动从该源发表.

*这里的意思大概是说, 源是jekyll建立的那个文件夹, 里面应该要用Gemfile, _config.yml之类的*

对于**用户或组织**的pages, 默认源在**默认分支的根目录**. 对于项目的pages, 默认源在**gh-pages分支的根目录**.

配置方法, 就在settings的github pages一栏中改就好了.[^gh-source]

[^gh-source]:<https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#choosing-a-publishing-source>

#### github pages主题

贼丑, 别用.

#### 静态网站生成器(SSG)

静态网站意味着不区分用户, 无状态的, 对所有的用户展示同样的内容.[^staticwp]

[^staticwp]:<https://www.techopedia.com/definition/5399/static-web-page>

静态网站生成器的作用是将内容和源, 转换成网页. 网页是静态的, 即由内容决定, 无论什么用户访问都是一样的. 而对于动态的内容管理系统(CMS), 就需要结合数据库, 选择性的展示内容.

注意: SSG本不充当服务器的功能.

github pages默认使用jekyll作为SSG来build内容. 若不想使用jekyll, 需要在source的根目录下放一个 **.nojiekyll**文件.

#### 终止github pages

- 删库
- 在settings中的github pages一栏中, 把源设置为none

#### 限制

- GitHub Pages source repositories have a recommended limit of 1GB. For more information, see "What is my disk quota?"

- Published GitHub Pages sites may be no larger than 1 GB.

- GitHub Pages sites have a soft bandwidth limit of 100GB per month.

- GitHub Pages sites have a soft limit of 10 builds per hour.

### 认识jekyll

github pages的默认主题太丑了, 我决定找其他好看的主题. 因此我跟着tutorial来安装jeykll和 ruby, bundler等, 并在源文件夹用jekyll new了一个. 我把theme设为了一个外面找到的一个模板. 在这里花了我一个晚上, 主要是: windows安装ruby有问题, 不知道bundler和gem是啥. 在设置gemfile时, 我看漏了tutorial的一个步骤, 花了很长时间.

后来我对这个theme不满意, 又在github找到了一个主题. 然后故伎重演进行配置, 但无论怎么配置, 效果都不理想, 完全没法复原demo的样子. 这时我才发现remote theme compatible的问题.

最后, 我才决定直接fork github上的主题, 直接对它进行个性化.

#### Ruby Gem Bundler

Gem是ruby的一个包管理器,类似于 Ubuntu 下的apt-get, Centos 的 yum，Python 的 pip.[^gem]

Gemfile: 记录了你的项目所依赖的模块/包/软件, 以及他们的版本等信息.

Bundler: 自动根据你的Gemfile来计算最佳的版本来安装包, 并且安装他们的依赖.

[^gem]:<https://www.runoob.com/ruby/ruby-rubygems.html>

#### 所谓主题

主题是指把一些内容无关的, 例如样式, 设定等文件隐藏起来. 例如: assets, _layouts,_includes, _sass文件夹.

Gem-based theme是指把主题作为一个Gem, 上述的文件都在该gem内, 在_config.yml指定后, jeykll build就会从该gem里找文件. 因此, 在jeykll建立的根目录下, 即网站的源的根目录下, 要有一个Gemfile, 里面要包含一个主题的那个gem. 在jeykll new时, 已经默认安装了一个minima的主题.

被隐藏的文件可以重载, 即可以在源的根目录的相应文件夹内, 创建相应名字的文件. 在build时, jekyll优先搜索源, 再搜索gem.[^theme]

[^themem]:<https://jekyllrb.com/docs/themes/>

remote theme是指主题寄存在github的库中. 在_config.yml设置remote_theme后, 在github pages上build, jekyll就会找到那个库.

remote theme相比于gem-based有jekyll-remote-theme compatible的问题, 即不是所有theme都能作为remote theme.

## 反思

### 反复查阅浪费时间

我经常在同一个页面反复地看. 这一方面归结于我看得**不仔细**, 即我并没有逐字逐句地阅读, 而是经常跳读. 这主要是因为, 读英语要辛苦一点. 而且我不想浪费时间在一些废话上, 我想快速找到重点. 但问题在于被我省略掉的部分可能包含着重点. 比如说, 我看漏了tutorial说gemfile要删掉一行,导致我反复bundle install失败. 总之, 以后应该保证我了解**每一段**都在讲什么
我没有做笔记.

在预习或上课的时候, 我会在教科书上做笔记. 做笔记的意义在于突出重点, 方便复习时快速捕捉. 然而, 在学习计算机偏实践的知识时, 我却没有养成这种习惯. 大致是因为知识是在网上, 或者电子版, 并不方便我做笔记, 勾画. 而且材料是通过搜索引擎得到的, 非常散乱.

这也是我写博客的初衷 ---- 为了避免为了不常用但重要的知识反复查询搜索. 但这次经历向我提出了更高的要求, 要求我应该**边查变记**. 即无论我学什么, 不是等到我把项目搞定再总结, 而是查到什么就当场记什么, 就像上课做笔记一样. 而且, 做笔记时应**不应目光短浅**, 即觉得什么需要才记什么. 第一, 我不一定能分辨出来什么是重点, 就好像github pages的设置source, 一开始觉得是无关的用不上, 后来看到源相关的东西却看不懂. 而且, 就算当前用不上, 但以后可能会用, 到时候还要回过头看, 就很麻烦.

### 犯了很多低级错误

- 打错字: remote_theme的下划线, output与outputs.
- 看漏了: 因为暖色模式导致看不到几个文件夹, 以为有什么bug没能下载下来.

嘛, 这些事只有经历过才长记性.

## 下一步

- 把VSCode的Mardown配置好, 实现转pdf, 支持数学, 而且样式好看.
- 建立图集. 找到好看的二次元图片网站, 想办法:

    1. 转链
    2. 爬虫爬下来, 再在github中开个仓库存储
- 系统性学习YAML, jekyll, liquid. 实现图集打标签.
- 加入搜索功能, 可以搜索文档内容.
- 实现siderbar自动化.
- 长远计划: 买一个服务器, 实现视频资源在线观看, 支持ios. 实现账号登陆, 以便在上面保护一些私人的内容.
