---
title: 认识NumPy
cover: IMG_4354.JPG
header:
    theme: dark
article_header:
    image:
        src: IMG_4354.JPG
tags: ["NumPy"]
---

## 基础
[^basic]

[^basic]: <https://www.numpy.org.cn/user/quickstart.html#%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86>

NumPy的主要对象是多维数组`ndarray`, 里面的元素类型相同 (这一点与Python内置的`list`不同, list可以同时存储不同的类型. 与Python内置的类库`array.array`类似, 但后者只能表示一维数组).

`ndarray`有以下属性;

1. `ndim`: 维度个数. 维度也可成为轴. 维度的个数也可称为`rank`.
2. `shape`: 一个元组, 表示每个维度的大小. 元组的长度就是`ndim`. 其中`(n, )`表示一维数组.
3. `size`: 元素个数.
4. `itemsize`: 单个元素个数

### 创建数组

`numpy.array(...)`:

- 传Python的列表或元组作参, 不能使用多个参数代表一个元组.
- 可以指定关键字`dtype`类型.
- 当列表和元组是多维时, 创建的数组也是多维的.

`numpy.zeros(dimensions)`:

- 传维度元组作参, 创建初始值为0的数组
- 默认类型是`float64`

`numpy.ones(dimensions)`:

- 初始值为1

`numpy.empty(dimensions)`:

- 与`zeros`类似, 但初始值是随机的.

`numpy.range(start, stop, step)`:

- 与Python的`range`类似, 但返回的是数组.
- 可以处理浮点值
  
`numpy.linspace(start, stop, number)`:

- 与`numpy.range`类似, 但接收的不是步长, 而是期待的元素个数.

### 元素操作

#### 算术运算符

**算术运算符**表示对元素操作, 如:`+, -, *, /, ...`.

- 注意: `*` 这里也是对元素操作. 矩阵运算用`@`.
- 支持`+=`, `-=`等, 含义不变, 会改变数组的值.
- 支持关系操作, 得到的是布尔数组
- 对于维数不一样的数组, 会根据广播原则对较小的数组进行扩展. 简单而言就是, 当数组a与数组b操作, 但数组a比数组b缺少一些维度, 而其他维度的大小是相等的. 那么就会对a进行扩展, 把数据duplicate[^broadcast]

[^broadcast]:<https://www.numpy.org.cn/user/basics/broadcasting.html#%E4%B8%80%E8%88%AC%E5%B9%BF%E6%92%AD%E8%A7%84%E5%88%99>

-

#### 一元操作

如求和, 求最大值等. 由`ndarray`类方法来实现. 当作用于数组时, 相当于把数组看成列表, 维度不会体现.

但也可以在`sum`, `max`函数中指定`axis`关键字, 表示对某个轴的数组分开进行.

`axis`接受整数的元组, 从高维到低维从0开始编号. 被指定轴x, 就代表着沿该轴折叠塌缩[^axis]

[^axis]:<https://www.sharpsightlabs.com/blog/numpy-axes-explained/>

```Python
>>> b = numpy.array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11]])
>>> b.sum(axis=0)
array([12, 15, 18, 21])
```

#### 通函数

`ufun`, 通用数学函数, 如`sin`, `exp`等. 对**元素**进行运算.

### 切片

与Python的切片操作类似, 可以用`:`来表示范围. 但numpy还支持多个`:`用`...`省略.

以及, 在提取某一列向量时, 使用`array[:index]`得到的是行向量, 如想得到列向量, 使用`array[:[index]]`. 后者里面的`[index]`, 是利用数组来索引.[^index]

[^index]:<https://www.numpy.org.cn/user/quickstart.html#%E8%8A%B1%E5%BC%8F%E7%B4%A2%E5%BC%95%E5%92%8C%E7%B4%A2%E5%BC%95%E6%8A%80%E5%B7%A7>

### 改变形状

`a.ravel()`: 扁平化
`a.reshape(...)`: 改变形状, 接受元组作为参数. 若某个维度为`-1`则代表这个维度不定, 根据其他维度和大小计算.
`a.T`: 转置.

注意: 以上的函数都是返回改变后的数组, 而不改变这个数组本身.
与`reshape(...)`相对应的是`resize(...)`, 后者是会改变数组本身的.

另外, 在处理列向量时, 总是会自动地转成行向量(不知道是什么规则). 因此若想提取列向量, 或得到列向量等, 需要小心. 用`reshape(-1,1)`来设置形状.

### 数组堆叠

`np.column_stack(tuple of column vectors or arrays)`和`row_stack(tuple of raw vectors or arrays)`

其中, 参数是一个元组, 里面是多个数组或向量.

## 拷贝

### 引用

使用`=`传递, 只是传递对数组的引用.

```Python
>>> a = np.arange(12)
>>> b = a            # no new object is created
>>> b is a           # a and b are two names for the same ndarray object
True
>>> b.shape = 3,4    # changes the shape of a
>>> a.shape
(3, 4)
```

### 浅拷贝

创建了不同的对象, 但是数据是**共享**的.

```Python
>>> s = a[ : , 1:3]     # spaces added for clarity; could also be written "s = a[:,1:3]"
>>> s[:] = 10           # s[:] is a view of s. Note the difference between s=10 and s[:]=10
>>> a
array([[   0,   10,   10,    3],
       [1234,   10,   10,    7],
       [   8,   10,   10,   11]])
```

使用`view`函数返回数组完整的浅拷贝.

### 深拷贝

拷贝整个数据. 使用`copy`函数.

注意: 留意切片的目的, 选择是是否在切片后立即深拷贝!

## 线性代数

`a @ b`: 矩阵相乘

`a.T`: 矩阵转置


