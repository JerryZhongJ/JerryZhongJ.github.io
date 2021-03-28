---
title: Introduction to Software Analysis & Testing
tags: ["Software Analysis", "Software Testing"]
---

## Software Analysis



目的:
提高软件质量: 可靠性, 安全性, 性能

软件开发时间50%在于测试和debug.

识别bug,发现安全漏洞

Program Analysis is the process of automatically discovering useful facts about programs.

An example of useful facts: programming error.

3 kind:
- dynamic: run-time analysis. By running the Program and oberving its behavior. 
- static: compile-time analysis. By Inpecting the source code or binary code.
- Hybrid. Combining the two above.

Well-known dynamic analysis tools:
1. **Purify**: checking memory accesses such as array bounds.
2. **Valgrind**: dectecting memory leaks in x86 binary programs.
3. **Eraser**: detecting data races(多线程同时读写同一变量,至少有一个在写,使得该变量不可用), which indicating errors producing different results from run to run.
4. **Daikon**: finding likely invariants, which ramain true in every run.

Well-known static analysis tools:
1. **Lint**, **FindBugs**, **Coverity**: inspecting suspicious error patterns.
2. **SLAM**: check if C programs respect API usage rules.
3. **Faceboo infer**: detecting memory leaks in Android.
4. **ESC/Java**: specifying and verifying invariants in Java.

Invariant
dynamic analysis(Daikon): 
只能运行程序有限次, 但是程序可能的路径是无限的. 所以只能发现likely invariant.   
但是Daikon可以确定性地排除一些可能取值, 即便只运行一次.

static analysis: 可以发现definitely invariant.

Static Analysis:
operates on intermediate representation(中间表示), such as Control-flow graph(控制流图).

abstract state vs. concrete state: 是否追踪变量的具体取值, 还是抽象成可能取值的集合.

不具有完备性(Completeness): 不一定找到所有的invariant
具有可靠性(Soundness): 找到的invariant必然是对的.

程序分析无法同时实现**可结束**且**完备**且**可靠**, 不同的程序分析在完备与可靠之间trade off.

程序分析的用处:
1. 编译器: 生成高效等价代码
2. 软件工具: 用于testing, debugging, verification.
    1. 发现程序错误
    2. 证明程序不变量
    3. 生成代码
    4. 定位错误
3. IDEs: 理解代码, 重组代码. 主要用于大型项目.


## Software Testing

软件开发包括开发者, 测试者,管理者.

要点:
1. 清晰的程序规约(Program Specification).
    - Program Specification 描述程序的预期行为.
    - 测试就是在检查程序的行为是否符合程序规约. 
2. 开发和测试独立.
    - 开发组来实现, 测试组来确定程序规约.
    - 这意味着测试不应该管具体如何实现从而决定规约. 
    - 开发组可以确定规约.
3. 资源有限
3. 程序规约是会变化的(需求是不断改变的)

### 测试方法
![](assets/posts_ref/Introduction to Software Analysis & Testing/testing approuches.png)

两个维度: 人工 vs. 自动, 白箱 vs. 黑箱. 两个坐标系不是二元的, 而是连续的. 

Automated:
1. 找bug更快.
2. 不需要亲自生成测试集. 因此当需求改变, 不需要改变测试集, 由程序自动生成.
白箱和黑箱不是界限分明的, 有时候可能只能开放部分代码, 而其他部分保密. 此之谓"灰箱模型".

Manual:
1. 人类更有可能生成更有效的测试.
2. 人类更有可能覆盖更多的代码.

理想的方法介于两者之间: Semi-Automated Approach. 比如说由人类设定好合法输入是什么, 由机器根据语法来生成测试输入.

BlackBox:
1. 适合无法更改的代码. 如底层代码.
2. 避免陷入细节, 而忽视整体的行为.
3. 程序可以任意形式, 即便是二进制机器码.

WhiteBox:
1. 测试集更有针对性
2. 更有可能覆盖更多代码.


对软件测试的研究就是将其自动化, 往上面移动.

**Feedback-Directed Random Testing**: issues Random Commands that change in response to feedback issued by GUI.
**Symbolic Execution**: needs to inspect the source code.


### Specification
pre- and post- condition: 两个谓词, 分别在函数执行前和执行后, 是assertion的一种.

frame conditions: 在pre-&post-condition中没有提到的内容.我们认为其没有变化. 
    - 比如: 栈的pop函数, 我们的pre-condition保证执行前时size > 0, 那么post-condition保证函数返回值是栈顶. 对于栈的其余元素, 我们没有提及, 默认没有变化.
![using pre-&post-condition to test](assets/posts_ref/Introduction to Software Analysis & Testing/using pre post condition.png)
### Measuring Test Suite Quality
如何评价测试集的好坏:
1. 是否全面, 足够发现bugs.
2. 是否臃肿, 不利于运行和维护.

两种方式:
1. Code Coverage metrics: 看测试集通过程序后, 有多少代码被执行到了.
2. Mutation(突变) Analysis: 让代码发生突变(类似于基因突变), 然后运行相同的测试. 若突变代码成功通过, 那么测试集不够强.

Code Coverage类型
1. Function Coverage:
2. Statement Coverage:
3. Branch Coverage: 条件分支
4. Others: line ~, condition ~, basic block ~, path ~. 
优势: 测量起来简单.
不足: 不是充分条件. 即便有高代码覆盖率, 也不能保证bug被找出来了. *考率信息竞赛的题目, 一般样例都是简单的, 但也可以让所有代码被执行到. 但是我们不敢说程序就是正确的. 所有代码都被执行, 而且输出也是正确的, 这种情况可能只是对于某些输入(简单的样例)才成立. 况且, 程序可能没有考虑一些特殊情况.*

Mutation Analysis:
基于两个假设:
Competent Programmer Hypothesis: 程序的错误一般是小的语义错误.(所以大的架构,逻辑没有错的意思?)
Coupling effect: 多个简单错误会级联或耦合, 形成其他更加严重的故障.

修改程序的代码, 如把一个加号改成减号. 拿这个突变程序去通过测试集时, 理应有一些测试样例通不过.

不足: 可能存在一些突变程序, 它与原程序等价. 这个突变程序对于所有输入(不只是测试集中的输入), 都会输出与源程序一致. 因此对于这个变种程序, 测试集肯定全部通过的. 这是真实在实践中存在的问题, 往往需要人工判断变种程序是否与源程序等价.