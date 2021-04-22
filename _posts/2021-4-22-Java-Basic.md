---
title: Java基础语法 -- 与C++的区别
cover: https://cdn.jsdelivr.net/gh/JerryZhongJ/Pictures/20210326221207.JPG
tags: ["Java"]
---
Java语法其实和C++很像, 不如说感觉就像是在C++的基础修改得到的. 因此, 与其列出Java的语法, 不如突出强调Java与C++的不同之处. 这样避免了重复学习, 也避免我们将两两种语言的语法混淆.

本文是基于C++的语法来介绍Java的语法, 因此和C++保持一致的地方就不会提到.
<!--more-->

## main
```Java
public class ClassName{
    public static void main(String[] args){
        ...
    }
}
```

## 变量

Java中的变量类型只有两种: **基本数据类型**和**引用类型**. 其中引用类型类似于C++中的指针, 指向一个堆空间. 但Java没有指针, 这些细节都被隐藏了. 所以只需要记住, 声明一个变量时, 除了基本数据类型, 都是引用. 

因此, 所有对象的声明只是在声明一个引用, 对象真正的实体在`new`时定义.  
因此, 引用的赋值`a = b`只是让`a`指向`b`的对象, 并不会拷贝`b`的对象. 若需拷贝使用`Object.clone()`.
因此, 数组也是一个对象, 也需要`new`.

### 基本数据类型
Java的数据类型都带有一个默认值0, 因此初始化时总是有值的
- **整型**: `byte`, `short`, `int`, `long`(相当于C++中的`long long`)
  - 不存在无符号数, 总是带符号的.
  - `int`的长度为4 byte, 与机器无关.
- **浮点数**: `float`, `double`.
- **字符**: `char`.
  - UTF-16编码
- **布尔**: `boolean`.
  - 只有`true`和`false`两个取值. 无法再用非零整型来表示`true`.
  - 在需要条件表达式的地方, 只接受boolean类型.
  - 整数类型和布尔类型之间不能强制类型转换, 使用`x != 0`和`b ? 1 : 0`来实现.
- **枚举**: `enum`.

### 常量
`final`关键字, 而不是`const`.

### 数组
Java中的数组本质上是`java.util.Arrays`的一个对象.

声明的语法: `int[] arr;`.
- 可以把`int[]`看作一种类型. 
定义的语法: `arr = new int[100]`. 或者初始化`arr = new int[]{ ... }`.
- 类似于C++中动态数组的申请!
- 数组长度可以为0.
`array.length`可以获取数组的长度.

#### 多维数组
和C/C++一样, n维数组可以理解成n-1维数组的一维数组. 但是在C/C++中, "数组"类型包括长度. 即`int[5]`和`int[10]`是不同的类型. 数组是一个类型的数组, 那么二维数组要么是`int[][5]`, 要么是`int[][10]`. 但是Java中数组的长度只是Arrays对象的一个描述, 元素类型相同\长度不同的数组是同一类型. 

因此**Java中的二维数组不一定是矩形**!  
因此, 定义时低维数组的长度不一定要给出. `x = new int[5][]`全然大丈夫。

Java中的多维数组跟近似于C++中的:
```C
double **array2d = new (double*)[len];
for(int i = 0;i < len;i++)
  array2d = new double[len[i]];
```

#### foreach语句

Java中有类似Python中for语句的写法:

```Java
for(Type i : obj){
  // statement
}
```

### 字符串
字符串有两个类来实现: **String**和**StringBuilder**. 

- 带双引号的字符串(如"String"), 默认就是一个String类的对象. 因此可以通过`String str = "String"`来声明并定义一个字符串.
- String类对象可以通过`+`进行连接. 对于String和其他类型(包括基本数据类型和任何对象), 那么会先将后者转换为字符串(`toString()`方法).
- String类的比较用`str1.equals(str2)`, 而不能用`==`. 后者表示比较两个是不是同一个对象!
- String类字符串是**不可更改的**. 所有的修改操作实际上都是创建一个新的字符串. 要频繁修改的字符串用StringBuilder来表示.

## IO

## 类和对象

Java没有友元.
### 类修饰符
写在`class`前面, 如;
```Java
public class Employer{
  ...
}
```
- **public**: 可以在[包](#包)外被访问.
  - 一个文件只能有一个public class, 而且该类名与文件名一致.
- **abstract**: 抽象类, 没有实现方法, 不能创建对象.
- **final**: 不可被继承.
- **缺省**: 只能在同一个包内被使用.

### 成员方法
1. Java中的方法默认都是动态绑定的, 都是C++中的虚函数.
1. `final`修饰符不表示返回常量, 而是表示**方法不能被覆盖**.
2. 覆盖方法时, 子类方法的权限不能比父类的还严格. 比如, 父类声明方法时是`public`的权限, 那么在子类覆盖时就只能是`public`.
3. 若已经覆盖了父类的方法, 但是又需要调用父类的方法, 使用`super.foo()`. 不用担心, 这里的调用不会动态绑定.
   
### 成员的访问权限

由限制到公开依次为:
- **private**: 只在类内可见. 与C++一致
- **default**: 对同一个包内可见. 不需要打出来. C++没有.
- **protected**: 对子类可见. 与C++一致.
- **public**: 完全公开. 与C++一致.

可见, 同一个包内的其他类是比子类更优先的存在. 因为包内的类属于同一个模块, 都由开发者维护. 但子类可能是外面杂七杂八的人写的.

与C++不同的是, 权限修饰符需要在每个成员前面都要显式地写出来.
### 构造方法
当构造方法需要调用本类的其他构造方法时, 用`this(...)`.   
需要调用父类的构造方法时用`super(...)`. C++中用成员初始化表.  
`this()`和`super()`需要在构造方法的第一行写, 且两者不能同时出现. 否则`super()`会被重复调用.
### 初始化
- 对于一般的成员变量, 既可以在类定义里面直接向变量赋值, 也可以指构造函数中赋值.
- 对于静态的成员, 既可以直接赋值, 也可以在静态初始化器中赋值. 静态初始化器写作:
  ```Java
  class ClassName{
      static int num;
      static{
        num = 10;
      }
  }
  ```
- 对于常量(`final`), 可以直接赋值, 或者在构造函数中赋值.
  - 常量一定至少要在构造函数的结尾前赋过值!
### 继承
Java中继承的写法为:
```Java
class Employer extends Human{

}
```
Java中的继承都相当于C++中的`public`继承, 即成员的权限不变.

## 多文件
### 包
Java中的相当于C++中的namespace, 而不是头文件. 头文件里面是声明, 不加过不了编译. 但在Java中使用一个类不需要声明, 但是需要说明清楚它的位置, 也就是在哪个包里.

包可以理解为模块, 是在类之上的又一层封装. 一个包开放一个接口给包外, 那就是public class. 其他的class就像private成员变量之于类一样, 是用来辅助实现这个模块的功能. 另一方面, 封装还能避免命名重复, 就是上述的namespace.

嵌套的包的作用域并不嵌套, `foler1.folder2`与`folder1`是两个包. 

包的创建通过`package pack1.pack2. ...`. 包的名字与文件夹名大小写一致. 

编译时并不检查package是否与文件目录相符, 可以在任意目录javac, 只需要指明源文件的位置即可.  

class文件需要按照package摆放, 而且classpath中指出根目录, 否则将找不到.class文件. classpath默认包含'.', 因此在根目录下用`java pack1.pack2.ClassName`就能执行ClassName程序. 必须在根目录, 以package.ClassName的形式执行.如我在/java/package_test/test.java中声明`package package_test`, 使用`javac test.java`编译. 则必须在/home下使用`java package_test.test`执行. 在/java/package_test下使用`java test`会显示: "错误: 找不到或无法加载主类 test".

编译时若使用`-d <bin_dir>`参数, javac将会在bin_dir下面生成.class文件, 并且根据package生成相应的目录结构来摆放.class文件. 若不加`-d <bin_dir>`, javac就会在源文件的地方生成.class文件, 此时假如源文件没有正确摆放, .class也没有正确摆放. 那么java就找不到.class了.

### import
import的作用和`using namespace`很像, 是起到省略的作用. 本身不是必须的, 只需要程序里的所有类都详细说明位置(如java.lang.System)即可. *但import了一个类后, 就相当于导入到这个包里面了*.

import是导入类, 不是导入包! 可以用`*`来指代一个包里的所有类.  
其中通配符`*`只能代替类名, 因此`import folder1.*`不包括`folder1.folder2`的类; `import folder1.*.*`是非法的.

### classpath

如上所说, classpath是用来指示package的根目录, 而.class文件就放在classpath下的package里.

设置系统环境变量是没有意义的. JVM默认会查找jdk的类库, 不需要手动指定.  
classpath默认值是当前目录`.`. 在使用`java`执行时或`javac`编译时, 通过`-cp`参数可以修改, 但别忘了加上当前目录`.`.

一个jar包可以看作一个文件夹. jar里面直接就是package和.class文件, 这样我们写classpath时写的就是jar文件.  
如`/java.jar/package_test/test.class`和`/java/package_test/test.class`是等价的(package_test是test的包). 我们在想使用test.class, 只需要`-cp /java.jar`.

## 抽象类与接口

