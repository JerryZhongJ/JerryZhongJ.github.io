---
title: "如何在java使用matlab画图"
---

## 要求
- JDK 7or8
- MATLAB Compiler SDK
- 设置 _classpath_ / _dependency_ : javabuilder.jar 在 	matlabroot/toolbox/javabuilder/jar

## 方法
1. 用MATLAB Compiler SDK 将matlab函数转换为java类(_plotter_), 并封装在package中
2. 在java中实例化 _plotter_ class访问MATLAB函数, 并使用 _MWArray_ 来处理类型转换.
3. build and run

## 步骤
1. 在MATLAB中打开LibraryCompiler: 输入指令 _LibraryCompiler_ 
2. 在类型一栏选择 **Java Package** 
