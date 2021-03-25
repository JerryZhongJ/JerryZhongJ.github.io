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
具有可靠性(Soundness): 找到的invariant必然是对的
