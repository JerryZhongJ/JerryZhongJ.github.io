
## 什么是make [^overview]

[^overview]:<https://www.gnu.org/software/make/manual/html_node/Overview.html#Overview>

对于一个大项目, 往往源文件众多. 而每次修改只是改动其中的几个, 因此我们需要选择性的recomplie. 比如说, 改动了头文件, 所有引用了这个头文件的代码都需要重新编译, 其他无需编译. 重新编译后产生新的目标文件, 又需要重新链接. make用于决定什么文件需要recompile.

make 一般用于C语言, 但实际上并不限定语言, 只要编译器可以在shell中运行. make也不限定于编程, 可以用于任何牵一发而动全身的任务, 即:
> You can use it to describe any task where some files must be updated automatically from others whenever the others change.

## Makefile 基础
makefile描述了项目文件的依赖关系.
### 规则
makefile 有一系列规则(rules)构成:
```
target: prerequisites
    recipe
    ...
    ...
```
_target_: 一般是生成文件的名字, 或者一个动作名(伪目标, [Phony targets](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html#Phony-Targets)).

_prerequisite_: 生成target所依赖的依赖文件

_recipe_: make要执行的动作, 用来告诉make怎么update target. 可以写在一行, 也可以写在多行. 注意: **必须在每一行前面加一个tab**.
[^rule]

[^rule]:<https://www.gnu.org/software/make/manual/html_node/Rule-Introduction.html#Rule-Introduction>

### 执行过程
make默认执行第一个target, 这被叫做*default goal*

在处理一个rule的时候, make会看prerequisites, 看他们是否需要更新, 即转去处理prerequisites中的文件作为target的rules. 若prerequisites中的文件比target要新, 或者target本身就不存在, 那么必须执行recipe.

规则相当于构建了一棵树, 第一个target作为根节点, prerequisites作为其子节点. make在处理时, 便沿着这棵树处理. 当子节点比自己新, 或者自己的文件不存在, 就会执行recipe更新自己, 并继续. 

而不在这棵树内的rule, 需要在make时显式地指出. [^work]

[^work]:<https://www.gnu.org/software/make/manual/html_node/How-Make-Works.html#How-Make-Works>

### 变量
变量用来代表一个字符串, 在引用它的地方, 会直接展开成该字符串.

因此, 当文件名众多, 且常常一起出现, 就用变量来代替, 如:
```
objects = main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o
```
而引用该变量, 用 _$(variable)_, 如 _$(objects)_

### 语法

## 高级用法
### 通配符


