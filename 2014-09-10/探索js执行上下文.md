## js执行上下文
公司同事分享的关于js执行上下文的一些干货，我粗略整理了一下，具体见下文~~

### 1、变量对象（VO）
包含：
- 变量声明
- 函数声明
- 函数形参
### 2、执行上下文（Execution Context）
```javascript
activeExecutionContext = {
	VO: {
		{data_var}  //var i = 0;
		{data_func_declaration} //function foo() {}
		{data_func_arguments} //func(30, 20)
	},
	scopeChains: [VO_1,VO_2,...,VO_n],
	this: thisValue
}
```
执行上下文包括：
- 全局上下文
- 函数上下文
- eval上下文

还是直接上代码吧~

```javascript
var a = 10;
function test(x) {
	var b = 20;
}
test(30);
```
上述代码中，全局上下文里的VO和test函数上下文中的VO分别长下面这个样子：

```javascript
VO(globalContext) = {
	...
	a: 10,
	test: <ref to function>
	...
};
VO(test functionContext) = {
	x: 30,
	b: 20
}
```
p.s.调用函数时才会创建函数上下文对象哦

下面来看看完整的全局上下文中的变量对象大概长什么样子：

```javascript
GlobalContextVO (VO === this === global)
global = {
	Math: <...>,
	String: <...>,
	isNaN: function() {[Native Code]}
	...
	...
	window: global // in browser
};
```
看完有没有一种恍然大悟的赶脚，原来所有可以直接拿过来用的内置类呀、功能函数呀，他们的引用都是保存在全局的变量对象中滴~

下面再来看看一般的函数上下文中的变量对象的样子吧：

```javascript
VO(functionContext) === AO;
AO = {
	arguments: <Arg0>
	...
};
arguments = {
	callee,
	length,
	properties-indexes //arguments[0] => param1,
	                   //arguments[1] => param2,...
}
```
这下终于知道函数里莫名其妙多出来的arguments对象是从哪里来的了~~

### 3、VO初始化顺序
1. 形参
2. 函数声明（产生冲突->覆盖）
3. 变量声明（产生冲突->忽略）

### 4、也来说说函数声明和函数表达式
1. 函数声明
```javascript
function a(){
	...
}
```
2. 函数表达式
```javascript
(function a(){})
```
3. 函数表达式调用
```javascript
(function a(){}())
```

关于第三条，同事的解释是词法分析器在解析时先遇到左括号，然后才是function关键字，所以就把它解析成函数表达式喽

最后，上道小题练练手~~
```javascript
function a(){
	alert(this);
}
a.call(null);
```
请问上述程序的运行结果是什么哩？

答曰：自己去浏览器下试试呗

哈，看到结果是不是有点小惊讶，其实仔细想想就明白啦，提示一个关键词-作用域链~

p.s.其实上述内容纯属个人总结，所以外人看起来可能会觉得不够详细，幸好同事给了个参考网址，贴出来共享一下~

[http://dmitry.baranovskiy.com/post/91403200](http://dmitry.baranovskiy.com/post/91403200)

