## **以下内容引用自http://beta.atatech.org/articles/9003**
# 响应式设计-间距自适应布局方案

大家在平常的需求中, 肯定遇到过类似于下图所展示的列表:

![水平列表](http://exodia.net/img/list.png)

列表元素与元素之间的间距都是预先计算好的, 这种一列一列的布局方式的运用也体现在栅格系统中。

当产品需求需要调整元素宽度,或增加元素时, 则需要重新计算间距并调整。

SO, 更好的方式是一切都让浏览器自动帮我们计算, 而这一切都可以在所有浏览器中办到。


## HTML

我们以最简单的DEMO开始, 这里仅关注列表区块部分, 头尾以及列表元素内的内容都先丢弃, 因此, 简化后的HTML如下:
```html
<div class="mod">
   <ul class="list">
       <li class="item"></li>
       <li class="item"></li>
       <li class="item"></li>
       <li class="item"></li>
       <li class="item"></li>
       <li class="item"></li>
   </ul>
</div>
```
## 传统方案

我们假设页面宽度为990px, 整个list宽度也如此, 每行6个li元素, 每个li宽度为150px, 则li之间的间距为 (990 - 150*6)/5 = 18px。

传统方案会对每个列表设置固定的间距, 这里是18px, 让li一行显示也有多种方法: float或者inline-block。

## 间距自适应方案

对CSS3有关注的同学, 相信对flex布局不会陌生,
一种强大的布局方式, flex布局让浏览器自动计算间距绰绰有余。

如果不需要兼容浏览器, 新特性看起来都是那么的美好. 然而在很久之前, 浏览器就为我们提供了自动计算间距的功能,
若非响应式, 这个功能或许还会呆在遗忘的角落中. 下面本文开始介绍这个功能。

大家对text-align这个属性应该不会太陌生吧? 我们经常在文本对齐中设置其值为left, center, right。
然而还有一个值justify, 或许就用得比较少了, 这个属性用于将文字两端对齐, 让浏览器自动计算间距的关键就在此。
text-align适用于inline, inline-*的元素上, 各个浏览器的支持程度也很完善。

间距自适应布局的基本思路很简单: 对父容器设置text-align: justify; 对子元素设置display: inline-block.
不过真正实现起来还是有些细节工作需要做, 我们先看看对前面的HTML片段应用自适应间距方案的CSS代码:

```css
.list {
    font-size: 0;
    text-align: justify;
}

.list::after {
    content: "";
    display: inline-block;
    width: 100%;
}

.item {
    display: inline-block;
    font-size: 12px;
    text-align: left;
}
```
代码稍微比思路复杂了一些, 解释下上述代码的意义:

- 第一段代码对父容器.list设置属性text-align: justify; font-size: 0;
text-align部分不难理解, 仅仅是为了让list下的元素两端对齐. font-size:0 则是为了解决对元素设置
inline-block导致的4px间隙。
注意, 这里的4px间隙主要是伪元素造成的.

- 第二段代码对父容器添加伪元素属性, 是为了让父容器的子元素呈现两行, 之所以这样做是因为,
在文字两端对齐的排版方案中, 最后一行的文字总是左对齐的, 可以想象, 最后一行的文字一般都是不会充满整行的,
如果也是两端对齐, 这个排版效果就....; 第一行也是最后一行,
因此我们通过伪元素添加一个空内容, 并设置宽度100%, 强制使其换行, 因此我们的列表元素得以获得两端对齐的效果.

  CSS3有一个text-align-last的属性, 是用来设置最后一行文本的对齐方式, 和text-align的值一样, 本可以用这个方式解决
  上面说到的末行左对齐的问题, 但是浏览器支持度不高, 我自己测试了一下, IE8+, FF支持, FF要加moz前缀.

- 第三段代码就比较容易理解了, 让列表元素以inline-block呈现, 同时重置font-size和text-align两个会继承的属性.

## 兼容性解决方案
### IE8+

E8+虽然是支持text-align-last属性.aspx),不过支持得也有点问题: 设置font-size:0时, 该属性无效, 真是奇葩, 但是不设font-size为0,又会出现4px间隙。

解决方案为: 设置text-justify:distribute-all-lines;

### IE6/7
1. 自适应间距解决方案一样与IE8+一致, 但是要对父容器多设置zoom:1;
2. inline-block兼容, 这个就比较容易又常见了: *display:inline; *zoom:1;

## 兼容性代码

兼容所有浏览器的的自适应间距CSS代码如下:

```css
.list {
    font-size: 0;
    text-align: justify;

    /*for IE*/
    text-justify: distribute-all-lines;
    zoom:1;
}

.list::after {
    content: "";
    display: inline-block;
    width: 100%;
}

.item {
    display: inline-block;
    font-size: 12px;

    /*IE6 7*/
    *display: inline;
    zoom:1;
}
```
## 总结

经过一番折腾, 我们发现IE在文本处理这块做得反而是最领先的, 同时也整理出了跨绝大多数浏览器的间距自适应解决方案。

为什么是绝大多数呢? 撸主发现MAC下的Opera 12设置font-size:0会产生字体更大的BUG, win下还待检测,
不过还好, opera国内占有率似乎不高. 这个BUG也有人提交给了Opera官方, 相信会在下一个版本中修复,
而撸主也会竭尽所能去寻找该BUG的修复方案:)

