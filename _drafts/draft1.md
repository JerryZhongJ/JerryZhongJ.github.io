---
title: HTML & CSS
tags: ["HTML", "CSS"]
---
## HTML DOM

全称为**Document Object Model**, 文档对象模型. DOM是HTML的编程接口, 定义访问和操作HTML元素的方法. *HTML本质上是和markdown一样的文本语言*, 但是通过DOM, 文档将会被解析成有组织的节点或者说对象, 并且我们可以对这些对象进行操纵, 从而实现对文档结构, 样式和内容的修改.[^DOM]

[^DOM]: <https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Introduction>

有了DOM, 我们可以通过编程语言来操作网页元素, 最常见的是JavaScript. 但其实这不是绝对的, 理论上任何编程语言结合DOM都可以操纵网页, 如Python:  

```python
# Python DOM example
import xml.dom.minidom as m
doc = m.parse("C:\\Projects\\Py\\chap1.xml");
doc.nodeName # DOM property of document object;
p_list = doc.getElementsByTagName("para");
```

## HTML元素

形如`<div>`与`</div>`之间包含的内容为一个**元素**, `<div>`被称为**标签**

### 块级元素 行内元素

块级元素(block element)举例:  

```html
<p>This paragraph is a block-level element; its background has been colored to display the paragraph's parent element.</p>
```

效果为:
<p>This paragraph is a block-level element; its background has been colored to display the paragraph's parent element.</p>

行内元素(inline element)举例:  

```html
<p>This <span>span</span> is an inline element; its background has been colored to display both the beginning and end of the inline element's influence</p>
```

效果为:
<p>This <span>span</span> is an inline element; its background has been colored to display both the beginning and end of the inline element's influence</p>

两者的区别为[^block_vs_inline]:

1. 块级元素占据其父元素的整个宽度; 但行内元素只占据它对应标签的边框所包含的空间.(?)
2. 块级元素会另起一行; 行内元素不会.
   - 等于说块级元素之间不会并列显示, 而会换行显示
3. 块级元素可以包含行内元素和其他块级元素; 行内元素一般只包含数据和其他行内元素. 因此块级元素可以更大型
4. 块级元素可以控制高度, 宽度, 外边距, 内边距; 行内元素的上述参数由其内容决定, (除了**内边距padding**,**外边距margin**和**边框border**)不可改变.[^block_vs_inline2]

[^block_vs_inline]: <https://developer.mozilla.org/zh-CN/docs/Web/HTML/Block-level_elements>; <https://developer.mozilla.org/zh-CN/docs/Web/HTML/Inline_elements>
[^block_vs_inline2]: <https://www.jeffjade.com/2015/06/24/2015-06-24-css-block-inline/>

### div

### span

行内元素. 改变文本颜色, 如:

```html
<p>My mother has <span style="color:blue">blue</span> eyes.</p>
```

<p>My mother has <span style="color:blue">blue</span> eyes.</p>

## CSS 盒模型

在CSS中, 每个元素被理解成一个个盒子.

### 块内盒子 行内盒子

这里的概念正如[块级元素-行内元素](#块级元素-行内元素)所说.

### 内/外部显示方式

外部显示方式: 就是块级还是内联  
内部显示方式: 盒子内部元素的布局方式, 默认是**正常文档流**. 可以通过`display`属性来修改.

### 组成部分

这里的组成是指块级盒子

- **Content Box**: 内容区域, 通过设置属性`hegiht`和`width`控制大小.
  - 对于父盒子, 其子盒子就是其内容, 包括外边框的部分!
- **Padding Box**: 内边距, 控制与内容与边框的距离. 其大小通过设置属性`padding`来控制.
  - 与外边距不同, **没有负值**
- **Border Box**: 边框. 通过属性`border`来控制.
- **Margin Box**: 外边距, 控制盒子与其他元素的距离.其大小通过参数`margin`来控制.
  - 改变块级盒子的外边距也会改变父盒子的大小.
  - 可以设置为负值, 此时可能超出父盒子的边框,或者与其他元素重叠.
  - **外边距折叠**: 外边距是**虚**的, 而不是**实**的. 两个相邻的外边距不会**碰**在一起, 而是相互**渗透**. 最终, 两个盒子的隔开距离是两个外边距的较大值.

[^consist]
[^consist]: <https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E5%A4%96%E8%BE%B9%E8%B7%9D%EF%BC%8C%E5%86%85%E8%BE%B9%E8%B7%9D%EF%BC%8C%E8%BE%B9%E6%A1%86>

### 标准盒模型

与**替代盒模型**相对, 不同在于当我们说盒子的`height`和`width`, 我们实际指的是什么.  
对于前者, `height`和`width`是指内容的大小, 整个盒子的大小需要再加上内边距和边框(外边距不算);  
对于后者, 是指总大小, 即内容 + 内边距 + 边框的大小.

默认浏览器都是采用标准盒模型.

![盒模型](assets/posts_ref/box-model.png)

## CSS 语法

## CSS属性

### position

[^position]

[^position]: <https://developer.mozilla.org/zh-CN/docs/Web/CSS/position>; <https://www.runoob.com/css/css-positioning.html>

有5种取值:

- `static`: 默认取值. 表
- `absolute`:
- `fixed`
- `sticky`:

## CSS 布局

### 正常流布局

浏览器默认的布局方式. 就是[块级元素-行内元素](#块级元素-行内元素)中所说的。

1. 一个块级元素的内容宽度是其父元素的100%，其高度与其内容高度一致。
2. 内联元素的height width与内容一致。你无法设置内联元素的height width
3. 块级元素按照基于其父元素的书写顺序(默认: 从上至下)的块流动方向放置 --- 每个块级元素会在上一个元素下面另起一行，它们会被设置好的margin 分隔。
4. 内联元素只要在其父级块级元素的宽度内有足够的空间，它们与其他内联元素、相邻的文本内容（或者被包裹的）被安排在同一行。如果空间不够，溢出的文本或元素将移到新的一行。

### 修改布局

- `display`属性
- `float`

### 弹性盒子
[^flexbox]

[^flexbox]: <http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html>

弹性盒子(Flexbox)是一种用于按行或按列布局元素的一维布局方法, 块级元素不再另起一行。元素可以膨胀以填充额外的空间, 收缩以适应更小的空间.  

在设置元素的属性`display: flex`, 改变的是这个元素的**内部**显示方式. 设置了`display: flex`的父元素被称之为 flex 容器(flex container).在 flex 容器中表现为柔性的盒子的元素被称之为 flex 项 (flex item).

- 在flex容器设置`flex-direction: column`来设置flex项的分布方向.  
- 在flex容器设置`flex-wrap`指定容器是否全部包含flex项, 可选值有：
  - `wrap`：
  ![](/assets/flex-wrap.jpg)
  - `nowrap`：
  ![](/assets/flex-nowrap.png)
  - `wrap-reverse`： 
  ![](/assets/flex-wraprev.jpg)

### 网格布局
[^grid]

[^grid]: <https://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html>



## CSS 选择
