---
layout: post
title: "不同语言之间互联互通的有意思的函数"
date: 2017-03-26
StatsCS: true
comments: true

---

其实早就想说说这个有意思的话题，直到最近看了spark，又再次陷入这个有意思的事情，决定上来说说这个。从哪里开始说起呢？就从我这几个月学js的心得说起吧。javascript，一门神奇的语言，印象最深的特点之一是，函数经常接受另一个函数作为参数传入，官方称这叫回调，当然了，又有同步回调，异步回调，弄的人懵懵的。这是它的特色，函数也是同string array啥的类似的对象。暂且不讨论不太容易理解的回调精髓，就说说我常用的几个用函数作为参数的方法吧。

### 1. `map()`方法

语法：
	
```javascript
var new_array = arr.map(callback[, thisArg])
```
咦，发现了`callback`吧！那`map()`究竟能实现怎样的功能呢？场景是这样的：比如我有个数组，我想对数组中的每一个元素都进行同样一个操作，如对每一个数字做平方，传统的思维方式是写循环遍历整个数组，但是这个时候我们有`map()`了，这个循环就不用写了。
	
- Example

```javascript
// for loop
var arr = [1, 3, 5, 7, 9];
var new_array = new Array(arr.length);
for(var i = 0; i < arr.length; i++) {
	new_array[i] = arr[i] * arr[i];
}
```

```javascript
// map()
var arr = [1, 3, 5, 7, 9];
var new_array = arr.map(function(ele) {
	return ele * ele;
})
```
打开node试试吧！看是不是很有趣！
	
<img width="390" alt="2017-03-26 10 43 57" src="https://cloud.githubusercontent.com/assets/18478302/24332243/d23fa1c2-1275-11e7-9c54-84a21a2dc9f8.png">
	
### 2. `filter()`方法

只能说，紧随`map()`之后的必须是`filter()`大法啊！
	
语法：
	
```javascript
var new_array = arr.filter(callback[, thisArg])
```
同样地，它回调的是什么样的函数呢？字面意思`过滤`，所以它会返回一个满足这个回调函数条件的新数组。本来也是要用循环遍历所有元素找出来满足条件的，这个时候有了`filter()`也就不用了。
	
- Example

```javascript
var arr = [1, 2, 1, 3, 4, 1, 5, 1];
var new_array = arr.filter(function(ele) {
	return ele !== 1;
})
```
<img width="442" alt="2017-03-26 10 58 48" src="https://cloud.githubusercontent.com/assets/18478302/24332372/dced9ed8-1277-11e7-8c1b-3cdf9c99ca81.png">
	
### 3. `every()`方法

与`filter()`类似，只不过`every()`返回的是一个布尔值，用来判断是否数组中所有元素都满足回调函数的条件。
	
语法：
	
```javascript
arr.every(callback[, thisArg])
```
- Example

```javascript
var arr = [1, 2, 3, 4, 5, 6, 7];
arr.every(x => x >=5);
```
<img width="316" alt="2017-03-26 11 11 26" src="https://cloud.githubusercontent.com/assets/18478302/24332468/98695cb4-1279-11e7-8eef-c6fbfc1b3e37.png">

我check了一下是否数组内的所有元素都大于5，结果是false。这里还用到了非常有意思的箭头函数。简单来说，类似于匿名函数，因为它没有名字，并且非常适合`map()`, `filter()`等等这类方法。
	
### 4. `reduce()`方法

语法：
	
```javascript
arr.reduce(callback, [initialValue])
```

`reduce()`接受的回调函数是用来把数组所有元素减少到一个值，听起来有点懵，看两个例子好了。

- 栗子一
	
```javascript
var arr = [1, 2, 3, 4];
var sum = arr.reduce(function(acc, val) {
	return acc * val;
})
// 24
```
它做了什么呢？它把所有的元素都乘起来了。
	
- 栗子二

```javascript
var str = ['a', 'b', 'c', 'd', 'e'];
var new_str = str.reduce(function(acc, val) {
	return acc.concat(val);
})
// 'abcde'
```
一个连接字符串的小例子。
	
- 栗子三

```javascript
var arr = [[1, 2], [3, 4, 5], [6, 7, 8, 9]];
var new_arr = arr.reduce(function(acc, val) {
	acc.concat(val);
})
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```
这个是传说中的拍扁你！之后R里面你也会见到。
	
类似的函数还有多少呢？我没数过，的确特别特别多，这是函数式编程思想，很有趣。链接收好不谢。

[underscore.js](http://underscorejs.org/)
[lodash](https://lodash.com/)
[lazy.js](http://danieltao.com/lazy.js/)

> 话说回来，你以为我是学js知道的上面这仨链接的么？还真不是。作为一个资历尚浅但是由衷热爱R的代码狗，怎么可能没听说过`purrr`这个包呢！初识`purrr`，就觉得这不是把js里的函数式编程思想copy进来了嘛！R本来就是函数式编程的，但这个包似乎让这个思想更加深刻了～

### `purrr`包经典函数：`map()`家族

```r
# .x is list or vector, .f is function or formular
map(.x, .f, ...)
```
同js里面的`map()`方法，这里的`map()`同样是对向量的每一个元素做同样一个操作，如何定义这个操作呢？可以是函数，也可以是公式。两个小例子：

- 栗子一

```r
1:10 %>%
	map(function(x) x ^ 2)
```
- 栗子二

```r
1:10 %>%
	map(~ .x + 1)
```
返回的是和向量等长的list，很烦人是不是？来跟我拍扁它！这里拍扁可以用`map_dbl()`（双精度）这个家伙。家族有：`map_lgl()``map_chr()``map_int()`分别用来拍扁不同的数据类型。

```r
1:10 %>%
	map_dbl(function(x) x ^ 2)
```
另一个拍扁方法是`flatten()`家族。其中`flatten()`一定返回list，家族有：`flatten_lgl()` `flatten_dbl()` `flatten_chr()` `flatten_int()` `flatten_df()`

为了体会其功效，专门写一个复杂的list套list的情况：

<img width="293" alt="2017-04-01 8 27 37" src="https://cloud.githubusercontent.com/assets/18478302/24573397/329d559e-16b5-11e7-9de9-4ac12ee6a122.png">

`flatten()`一次只能拍扁一下，啥意思呢？就是它只能每次把最外层list拍扁一下，拍完了还是list

```r
list1 <- list(list(c(1, 2, 3), c(2, 3, 4)), c(3, 4, 5))
flatten(list1)
```

<img width="262" alt="2017-04-01 8 30 45" src="https://cloud.githubusercontent.com/assets/18478302/24573431/8fa230e8-16b5-11e7-8c97-c45418632e02.png">

咦，是不是`list1[2]`给拍扁了，拍扁之后的长度也从2变成了5，但可能这并不是你想要的。那我们还得再拍扁一下。

```r
flatten(list1) %>%
	flatten()
```
<img width="257" alt="2017-04-01 9 46 14" src="https://cloud.githubusercontent.com/assets/18478302/24579277/b9d64ad8-1724-11e7-828f-c318aeacbad7.png">

这次终于彻底拍扁了，`flatten()`的使命彻底结束。但这依旧不是你想要的吧！我知道你想要向量。

```r
flatten(list1) %>%
	flatten() %>%
		flatten_dbl()
```
终极目标达成！不知道你看懂没有。前提是你要对`R`里面的数据类型及数据结构有很好的理解，如果你说不清楚`list1[1]`和`list1[[1]]`的区别，就得去搞搞清楚了。

同样，我认为更简单的做法就是`unlist()`，因为它有个`recursive`的选项，你可以选择递归下去，也可以做一次就结束。`unlist(list1)`直接一步到位。

### `reduce()`家族

成员：`reduce()` `reduce_right()` `reduce2()` `reduce2_right()`

药效一致：`accumulate()` `accumulate_right()`

同js里面的`reduce()`方法，它会把`list`或者`vector`以某种方法浓缩成一个，这个方法就是你定义的函数。

```r
a <- 1:7 %>%
	reduce(`-`)
# -26
```

`reduce_right()`是从右向左来递归。`accumulate()`会把中间结果给出来。

### `every()`

同js里的`every()`方法，给出的结果也是`True` or `False`。比如：

```r
a <- 1:10
a %>%
	every(function(x) {
		x < 11
	})
# TRUE
```

剩下的我就不多说了，类似这样在js和R里成对的函数还有哪些呢？

|   javascript   |   R   |
|:------:|:-----:|
|`map()` |`map()`家族|
|`reduce()` `reduceRight()`|`reduce()`家族|
|`every()`|`every()`|
|`some()`|`some()`|
|`indexOf()` `lastIndexOf()`|`detect_index()`|
|`find()`|`detect()`|
|`invoke()`|`invoke()`家族|
|`flatten()`|`flatten()`家族|
|`negate()`|`negate()`|
|`...`|`...`|

不同语言之间的相互借鉴非常之有趣吧～

但我开篇提到了spark，其实是闲来无聊看了看spark发现了几个对`RDD`的转化操作才决定把这篇写掉的。不过在spark中用到类似函数一点都不奇怪啦～毕竟人家生来就是折腾大数据的。`RDD`是什么我就不过多解释，就把它想成一个大大的数据集。对于分布式计算呢，它会被分成多个分区，然后每个分区单独操作。在转化操作中，`map()` `flatMap()` `filter()` `reduce()` `foreach()` `fold()`比较常用。以python和scala中为例（java太残暴），你或许能看懂什么了。这说明，python和scala里面都有类似函数。以及python里面的`lambda`，js里面的`=>`，Scala里的`=>`非常适合配上这类函数一起用。

```python
nums = sc.parallelize([1, 2, 3, 4])
squared = nums.map(lambda x: x * x).collect()
```

```scala
val input = sc.parallelize(List(1, 2, 3, 4))
val result = input.map(x => x * x)
```

大部分时候我都喜欢采用这种省时省力的方式，把很多东西联想到一起，一次性搞定不同语言不同框架中类似的想法，然后自己再感慨感慨世间万物互联互通，有相似的内在哲理，不同的计算机语言当然也是这样，最重要的是要搞清楚什么场景下用什么语言和工具更合适，学习新东西的成本倒不高～