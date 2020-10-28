---
title: Make
---
之前在做各种实验的时候都有接触make, 但一直以来都只敢做一些小修改, 没有自己写过
## 什么是make [^overview]

[^overview]:<https://www.gnu.org/software/make/manual/html_node/Overview.html#Overview>

对于一个大项目, 往往源文件众多. 而每次修改只是改动其中的几个, 因此我们需要选择性的recomplie. 比如说, 改动了头文件, 所有引用了这个头文件的代码都需要重新编译, 其他无需编译. 重新编译后产生新的目标文件, 又需要重新链接. make用于决定什么文件需要recompile.

make 一般用于C语言, 但实际上并不限定语言, 只要编译器可以在shell中运行. make也不限定于编程, 可以用于任何牵一发而动全身的任务, 即:
> You can use it to describe any task where some files must be updated automatically from others whenever the others change.

## Makefile 基础
Makefile描述了项目文件的依赖关系.

一个Makefile包含显式规则, 隐式规则, 变量定义, 引导 和注释.
### 规则
makefile 有一系列规则(rules)构成:
```
target: prerequisites
    recipe
    ...
    ...
```
- **target**: 一般是生成文件的名字, 或者一个动作名.(伪目标, [Phony targets](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html#Phony-Targets)).
  - 目标可以包含多个文件名, 用空格隔开. 此时相当于定义了多个单目标的规则, 每个规则的前件和recipe都与原来相同.[^multitarget]
  - 可以用通配符.
  
[^multitarget]: <https://www.gnu.org/software/make/manual/make.html#Multiple-Targets>

- **prerequisite**: 生成target所依赖的依赖文件.
  - 多个文件以空格隔开.
  - 可以使用通配符.
  - 前件有两种: **normal**和**order-only**, 两者区别在*执行过程*中解释. 两种前件通过 `|`来分隔, 左边的是normal, 右边的是order-only.
- **recipe**: make要执行的动作, 用来告诉make怎么update target. 
  - 注意: **必须在每一行前面加一个tab**.
  - '\$'已经用于引用变量, 若想使用'\$', 用'\$\$'来表示.
  - make默认会有回声, 即打印当前的指令. 若要关闭指令, 在指令前加`@`.
  - recipe中的注释不会被当成注释, 而是传递给shell. 由shell来处理.
  
[^rule]

[^rule]:<https://www.gnu.org/software/make/manual/html_node/Rule-Introduction.html#Rule-Introduction>

### 注释
注释是用`#`表示行注释, 行后有`\`的话, 下一行也变成注释.

但有一些例外:
> You cannot use comments within variable references or function calls: any instance of # will be treated literally (rather than as the start of a comment) inside a variable reference or function call.  
Comments within a recipe are passed to the shell, just as with any other recipe text. The shell decides how to interpret it: whether or not this is a comment is up to the shell.  
Within a define directive, comments are not ignored during the definition of the variable, but rather kept intact in the value of the variable. When the variable is expanded they will either be treated as make comments or as recipe text, depending on the context in which the variable is evaluated.

### 执行过程
[^work]

make默认执行第一个target, 这被叫做*default goal*

在处理一个rule的时候, make会看prerequisites, 看他们是否需要更新, 即转去处理prerequisites中的文件作为target的rules. 若prerequisites中的文件比target要新, 或者target本身就不存在, 那么必须执行recipe.

规则相当于构建了一棵树, 第一个target作为根节点, prerequisites作为其子节点. make在处理时, 便沿着这棵树处理. 当子节点比自己新, 或者自己的文件不存在, 就会执行recipe更新自己, 并继续. 

而不在这棵树内的rule, 需要在make时显式地指出. 

另外: 对于order-only前件, make会去检查它, 但不会因为它比target更新而去更新target. [^orderonly]

[^orderonly]: <https://www.gnu.org/software/make/manual/make.html#Types%20of%20Prerequisites>



[^work]:<https://www.gnu.org/software/make/manual/html_node/How-Make-Works.html#How-Make-Works>

## 高阶
### 执行
对于recipe中的每一条指令, 它们默认是在不同的shell中执行的, 因此假如一条指令是`cd ...`, 它的效果不会传递到下一条.

有两种方法避免这个问题:
1. 使用`.ONESHELL`内置目标, 这样**每个**规则recipe**所有**指令在一个shell中执行. (不同规则应该是不同的, 没试验过). 要写成
```
.ONESHELL:
```
2. 使用`&&`连接多条指令, 并用`\`换行.



### 变量
变量用来代表一个字符串, 在引用它的地方, 会直接展开成该字符串.

因此, 当文件名众多, 且常常一起出现, 就用变量来代替, 如:
```
objects = main.o kbd.o command.o display.o insert.o search.o files.o utils.o
```
而引用该变量, 用 `$(variable)`, 如 `$(objects)`

### 伪目标
一般而言, 目标是文件名. 但伪目标不是, 它只是执行动作的名字, 每次显式地make这个目标就会执行它下面的动作.

定义伪目标的一个简单的方式是取一个不存在同名文件的名字, 且以后也永远不会有, 这样每次make它都能保证能执行. 如:
```
clean: 
    rm ./*
```
在每次`make clean`时肯定能执行, 因为压根就没叫`clean`的文件.

但假如有同名的文件, 此时就把该伪目标写作内置目标`.PHONY`的前件, 如:
```
.PHONY: clean
```
这样, 尽管clean文件存在且没有前件, 但在输入`make clean`时还是会执行.

### 通配符

首先应该区分正则表达式和通配符. 两者都可以描述一个语言, 但前者的表达能力更强, 后者充其量只是用一些特殊字符来代替一些字符串. 比如说, 通配符不能表达重复的字符串这种模式, 但正则表达式可以.[^diff]

[^diff]: <https://www.quora.com/Whats-the-difference-between-a-wildcard-and-a-regular-expression>

基本用法为:
- `*`: 匹配零个或多个字符.
  - 注意: 这里的含义与正则里的不同. 它不表示闭包!
- `?`: 匹配0个或1个字符.
- `[...]`: 字符范围. 

若前件里包含通配符, 或者由通配符定义的变量, 需要留意会不会出现匹配结果为空的情况, 此时前件可能为空, 那么这个target就不会被更新.