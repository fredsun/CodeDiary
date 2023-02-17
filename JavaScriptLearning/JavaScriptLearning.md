1. 解释性语言，编译发生在运行中
1. 区分JavaScript和JQuery
   1. 概念
      1. JavaScript 是通过标签插入到HTML页面
      2. JQuery是一个JavaScript函数库, 或者说是一个框架使用JQuery首先要在 HTML 代码最前面加上对 jQuery 库的引用, 库文件既可以放在本地，也可以直接使用知名公司的 CDN，好处是这些大公司的 CDN 比较流行，用户访问你网站之前很可能在访问别的网站时已经缓存在浏览器中了，所以能加快网站的打开速度。另外一个好处是显而易见的，节省了网站的流量带宽

    2. 入口函数的不同
       1. js：window.onload = function(){内部放js}　
       2. jQuery:$(function(){})或者$(document).ready(function(){})
    3. 获取元素的方式不同
       1. js：常用的以下几个，除了id，其他的结果都是伪数组
       2. jQuery：$(" ")通过和css雷同的选择器方式来获取元素。得到的是jquery对象（dom元素的数组，外加其他一些成员）
    4. 获取内联，内嵌，外部
       1. JS操作样式的方法只能获取内联样式，不能取内嵌的和外部的。
       1. JQUERY操作样式的方法可以是内联的也可以是内嵌的。

1. let 和 var
   1. ES6新增let
   2. JS只有函数作用域和全局作用域，无块级作用域, function(){}是块级，即暂时性死区TDZ
   3. 作用域只在let在的代码区块，而且有暂时性的死区约束temporal dead zone（未声明前无法调用）【var类似全局变量
   4. let适用于for循环(js里for循环是独立的块作用域)，
   5. let无法重复声明

1. 变量提升Hoisting
   1. 只会提升声明，不会赋值

1. DOM（Document Object Model）文档对象模型

1. 变量名，小驼峰。下划线是私有

1. 单引号双引号皆可
   
1. 数组尾部增删 myArray.push()/pop();
1. 数组头部增删 myArray.push()/pop();
1. 匿名函数可以作为变量，而后调用变量, **不建议**
   ```
   var myGreeting = function() {
     alert('hello');
   }
   myGreeting();
   ```
1. 事件, e,evt,event
1. 内联事件处理器—不建议
   类似android直接在xml里添加onClick监听
   ```
    <btn onClick="...">;
   ``` 
   而应该使用类似 findViewById()
   ```
   const button1 = document.querySelector('button');
   button1.onclick = change;
   ```
   而又因直接.onclick会导致 functionA 被 functionB 覆盖
   ```
   myElement.onclick = functionA;
   myElement.onclick = functionB;
   ```
   不如 addEventListener 可以同时设置多个监听，以及 removeEventListener()
   ```
   myElement.addEventListener('click', functionA);
   myElement.addEventListener('click', functionA);
   ```
2. document.querySelector???????????

1. 当一个元素的一个事件类型，同时有两个事件处理器被激活

1. touch传递之事件捕获及冒泡，类似android 触摸传递的down
   1. 捕获：父类从外到内，每一层都判断是否有对事件进行监听 类似 android 触摸 down
   2. 冒泡：子类从内到外，每一层都判断是否有对事件进行监听 类似 android 触摸 up
   3. 现代浏览器默认，所有事件处理程序都在冒泡阶段进行注册
   4. 拦截冒泡传递, e.stopPropagation();
   5. 过去 NetScape 只用事件捕获，Internet Explorer只是用事件冒泡
   6. 如果一定要在捕获阶段拦截，addEventListener()第三个属性设置为true
   7. 事件委托，子类想全部都监听事件，则用父类去监听，比如列表选项框
1. 对象
   1. 对象的名字表现为命名空间，子命名空间类似子类
   2. 对象做到了字符串到值的映射，数组做到了数字到值的映射，对象也被成为关联数组(associative array)
