---
title: Latex基础
---
本文没有指明来源的部分, 默认来源为 [lshort](https://ctan.org/pkg/lshort-zh-cn)

## 组成
LaTex主要由文本与命令组成.
### 命令
命令有两种形式: 反斜杠+一串字母`\LaTex`, 或反斜杠+一个非字母`\$`

命令是大小写敏感的. 命令带`*`与不带`*`有不同的含义, 不能混用.

命令可接收一些参数, **可选参数**与**必选参数**. 必选参数用`{` `}`包围; 可选参数用`[` `]`包围.

命令具有作用范围, 有些命令对其后的所有的内容有效. 若想限制作用范围, 可以将命令在一个**分组**内使用. 一个分组用`{` `}`包围.

### 环境

Latex的环境是由`\begin{environment name}`和`\end{enviroment name}` 包围的区域. 环境会对整个区域会产生一些效果, 或生成特殊的文档元素. 和命令一样, 后面可接受可选参数和必选参数, 用`[` `]`或`{` `}`包围.

部分环境允许嵌套使用.

## 结构
[^structure]

[^structure]: <https://en.wikibooks.org/wiki/LaTeX/Document_Structure>
LaTex文档的总体结构为:
```LaTex
\documentclass{...}

\begin{document}
...
\end{document}
```
LaTex最开头以`\documentclass`开始.

**前言**(preamble)是位于`\documentclass`和`\begin{document}`的部分. 里面的命令一般对整个文档有效. 

前言后面就是文档的正文, 由`\begin{document}`和`\end{doucument}`包围.

对于`\end{document}`后面的内容, LaTex不会处理, 因此适合于添加注释, 或者存放一些临时的文字.

### 文档类

文档类(documentclass)规定了文档的性质, 里面包含了文档的布局. 

在Latex代码的开头通过`\documentclass[options]{class}`来指定文档类, 其中options可选.

常见的文档类有:
- **article**: 文章格式. 用于论文, 报告, 说明文档.
- **report**: 长篇报告. 用于综述, 长篇论文, 简单书籍.
- **book**: 书籍. 具有前言, 正文, 后记等.
- **ctex**~系列: 包括**ctexart**, **ctexrep**, **ctexbook**, **ctexbeamer**等. 支持中文.
  - 需要用XƎLATEX 或 LuaLATEX 进行编译

常见的可选参数包括:
- **10pt, 11pt, 12pt**: 指定文档的基本字号。默认为10pt。
- **a4paper, letterpaper, ...**: 指定纸张大小，默认为美式信纸letterpaper（8:5×11英寸）。可指定选项还包括a5paper，b5paper，executivepaper和legalpaper。
- **twoside, oneside**: 指定单面/双面排版。双面排版时，奇偶页的页眉页脚、页边距不同。article和report默认为oneside，book默认为twoside。
- **onecolumn, twocolumn**指定单栏/双栏排版。默认为onecolumn。
- **openright, openany**: 指定新的一章\chapter是在奇数页（右侧）开始，还是直接紧跟着上一页开始。report默认为openany，book默认为openright。对article无效。
- **landscape**: 指定横向排版。默认为纵向。
- **titlepage, notitlepage**: 指定标题命令\maketitle是否生成单独的标题页。article默认为notitlepage，report和book默认为titlepage。
- **fleqn**: 令行间公式左对齐。默认为居中对齐。leqno将公式编号放在左边。默认为右边。
- **draft, final**: 指定草稿/终稿模式。草稿模式下，断行不良的地方会在行尾添加一个黑色方块。默认为final.

### 宏包

宏包(packages)用来增强LaTex的功能, 相当于插件和扩展. 如插入图片, 增加颜色, 和插入源代码等.

在前言处, 通过`usepackage[options]{package name}`来调用宏包. 其中options可选; package name可包含多个, 用逗号隔开
- 在指定多个package name时, options将视作同时对多个宏包指定相同的参数, 若某个宏包不能识别参数, 将会报错.

使用宏包前, 需要确保宏包已被安装.

## 基本格式

### 空格
在LaTex中, 空格和tab统一视为空格. 连续若干个空格被视为**一个空格**. 忽略行开头的空格.

### 换行
这里的换行有两个含义: 
- 一个段落结束, 另起一个段落.
  - 这种情况用两个换行符实现.
  - 行末的换行符视作一个空格. 连续两个换行符视作一个换行. 多个空行视作一个空行.
- 一个段落未结束, 但内容过多一行写不下
  - 一般而言, LaTex会自动换行.(废话)
  - 但在西文中, 对于一些空格隔开的词组, 我们希望保持完整而不被换行隔开. 此时用`~`代替空格.

#### 手动换行
对于换行的第二含义, 有时我们一行并没有写完, 但还是希望从换一行开始写, 就需要手动换行.

手动换行的命令有`\\[length]`, `\\*[length]`, `\newline`.

`\\[length]`: 可在文本, 表格, 公式处使用; 可带参数, 表示换行处的垂直间距.

`\newline`: 只可在文本处使用; 不带参数.

`\\*[length]`与`\\[length]`类似, 但表示禁止在换行处分页.

#### 微调
我们可以使用命令`\linebreak[n]`和`\nolinebreak[n]`指示LaTex文档的哪些位置适合换行或不适合换行. 其中`n`表示合适或不合适的程度, 取值为0-4(0 means it will be easily ignored and 4 means do it anyway) [^linebreak], 默认值为4.

[^linebreak]: <https://www.overleaf.com/learn/latex/line_breaks_and_blank_spaces>
### 断页

与换行类似, LaTex能自动当内容过多时自动换页. 

我们可以使用`\pagebreak[n]`和`\nopagebreak[n]`进行微调.

对于手动换页, 有`newpage`和`clearpage`两个指令. 两者的区别在于:

`newpage`: 在分栏模式中, 可以起到另起一栏的作用. 对于分页前的浮动的元素, 如图片等, 可以留到新的一页.

`\clearpage`: 在分栏模式中, 起到另起一页的作用. 对于分页前的还没有显示的浮动元素, 将会被强制插入进一页空白页, 然后再开新页.

### 注释

`%...`: 行注释

### 特殊字符
LaTex有一些字符有特殊用途, 因此不能直接使用:
`# $ % & { } _ ^ ~ \`

若想表示这些字符, 使用`\`来转义.
`\# \$ \% \& \{ \} \_ \^{} \~{} \textbackslash`.
其中, `\^`与`\~`本身是命令, 后面接`{}`, 表示参数为空, 由此可以表示'^'和'~'. 而`\\`已经是换行命令, 因此只能用"textbackslash"代替.

转义符号本质上是指令.

### 标点符号
中文标点无过多注意.

而西文中的前后引号是不同的. 单引号的前引号用 `, 后引号用'. 双引号同理, 前引号用``, 后引号用 '' (两个单引号)或 " (一个双引号) 

## 文档元素
### 章节
`article`, `report`, `book`文档类提供了以下命令:
`\chapter{⟨title⟩}`, `\section{⟨title⟩}`, `subsection{⟨title⟩}`, `\subsubsection{⟨title⟩}`, `\paragraph{⟨title⟩}`, `\subparagraph{⟨title⟩}`.

其中,  `article`类不包含`\chapter`命令.

每个命令有两种变体:
- `\section[short title]{title}`: 带可选参数, 可选参数表示短标题, 只在目录和页眉显示. 
- `\section*{title}`: 标题不加入"编制", 即不计入目录, 不显示页眉, 也没有编号.

关于编号, `article`对`\section`, `\subsection`, `\subsubsection`提供编号. `book/report`对`\chapter`, `\section`, `\subsection`提供编号. 即, 最高的三级有编号.
#### 目录
使用`tableofcontent`生成目录, 目录本身不写入目录, 但这可以配置.

#### 附录
使用`\appendix`, 之后的章节编号会有所不同的, 即最高一级的编号使用拉丁字母.

#### 标题页
在正文处使用`\maketitle`生成标题页. 使用前需要用`\title{...}`, `\author{...}`提供信息. 如有需要可以用`\date{..}`提供日期, `\today`提供今天日期, `\thanks`在脚注生成致谢名单, 用`\and`隔开人名.

### 交叉引用
可以在文章中提及文章另一处的章节编号, 图片, 公式等, 称之为**交叉引用**.

被引用的地方添加`\label{name}`. 在引用的地方用`\ref{name}`和`pageref{name}`, 编译后这两个命令分别被替换为相应的编号和页码.

添加`\label`的具体位置:
- **章节**: 在`\section`后立即使用.
- **行间公式**: 在公式内的任意位置使用. 对于多行公式, 在要引用的公式的那一行使用.
- **有序列表**: 在`\item`后的任意位置使用.
- **图标**: 在`\caption`之后立即使用.
- **定理**: 在定理中的任意位置使用.

对没有编号的命令中使用`\label`, 如`\section*`会导致编号错误.

### 脚注
在文本后使用`\footnote{...}`可以生成脚注, 参数是脚注的文字.

在表格等地方, 可能不能使用`\footnote`. 此时, 可以先在要注释的地方添加`\footnotemark`, 然后再在外面的文本中`\footnotetext{...}`. [^footnotetext]

[^footnotetext]:<http://joshua.smcvt.edu/latex2e/_005cfootnotetext.html#g_t_005cfootnotetext>

使用`\marginpar[left-margin]{right-margin}`生成边注. 其中,若不提供可选参数, 则无论编译后这段文字出现在奇数页或偶数页, 都显示必选参数内的文字. 若提供了可选参数, 则在奇数页显示right-margin, 偶数页显示left-margin.
- 注意奇偶和left/right, 记得可选参数在偶数页展示.
### 列表
要显示列表, 需要使用**环境**. 其中, `enumerate`是有序列表, `itemize`是无序列表. 在环境内, 使用`\item ...`列举每一项.

```LaTex
\begin{enumerate}
\item ...
\end{enmuerate}
```

列表最多嵌套4层.

`description`环境的作用与列表类似, 但更像是列举一系列关键字, 并给出其解释. 环境中的`\item`有可选参数用来写关键字. 关键字在列表中以粗体显示在前面, 描述信息跟在其后.

### 引用
有两种引用环境: 
- `quote`: 短文字, 首行不缩进.
- `quotation`: 多段文字, 首行缩进.

引用环境一般会额外再缩进一点.


### 图片
[^image]

[^image]: <https://www.overleaf.com/learn/latex/Inserting_Images>
需要`graphicx`宏包. 且用不同的编译器编译时可能需要不同的编译选项.

使用`\includegraphics[options]{filename}`加载图片. 其中, **文件名不要包含空格和句点**.

使用 `\graphicspath{ {path1} {path2}} `指定图片的路径. 可以有多个路径.


### 超链接

### 代码块

### 伪代码

## 数学
## 其他