---
layout: post
title: JavaScript 常用资源
category: 备忘
tags: JavaScript
keywords: JavaScript
description: 
---

## 常用代码

### 交互
    alert();

    prompt();
    result = prompt(title[, default]);
    浏览器会显示一个带有文本消息的模态窗口，还有 input 框和确定/取消按钮。

    confirm
    result = confirm(question);
    confirm 函数显示一个带有 question 和两个按钮的模态窗口：确定和取消。
    点击确定返回 true，点击取消返回 false。

### 多个条件：“else if”
    if (year < 2015) {
      alert( 'Too early...' );
    } else if (year > 2015) {
      alert( 'Too late' );
    } else {
      alert( 'Exactly!' );
    }

### 三元运算符 ‘?’
    let result = condition ? value1 : value2
    计算条件结果，如果结果为真，则返回 value1，否则返回 value2。

### 逻辑运算符：|| (或), && (与), ! (非)。

### while
    while (condition) {
      // 代码
      // 所谓的“循环体”
    }

    “do…while” 循环
    do {
      // 循环体
    } while (condition);
    循环首先执行循环体，然后检查条件，当条件为真时，重复执行循环体。

### “for” 循环    
    for (begin; condition; step) {
      // ... loop body ...
    }

    通过示例来了解这部分的含义。下述循环运行从 i 等于 0 到 3（但不包括 3）的 alert(i)：
    for (let i = 0; i < 3; i++) { // 结果为 0、1、2
      alert(i);
    }

    跳过
    for 循环的任何部分都可以被跳过。

    let i = 0; // 我们已经声明并分配了
    for (; i < 3; i++) { // "begin" 部分不再需要
      alert( i ); // 0, 1, 2
    }
    
    也可以移除 step 部分：
    let i = 0;
    for (; i < 3;) {
      alert( i++ );
    }

    实际上我们可以删除所有内容，从而创建一个无限循环：
    for (;;) {
      // 无限循环
    }
    请注意 for 的两个 ; 必须存在，否则会出现语法错误。

### continue;  break;
    跳到标签
    outer: for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        let input = prompt(`Value at coords (${i},${j})`, '');
        // 如果是空字符串或已取消，则中断这两个循环。
        if (!input) break outer; // (*)
        // 做些有价值的事
      }
    }

### "switch" 语句
    switch(x) {
      case 'value1':  // if (x === 'value1')
        ...
        [break]

      case 'value2':  // if (x === 'value2')
        ...
        [break]

      default:
        ...
        [break]
    }

### 函数声明
    function showMessage(text = "no text given") {
      alert( 'Hello everyone!' );
      return;
    }

    命名技巧
    showMessage(..)     // 显示信息
    getAge(..)          // 返回 age (gets it somehow)
    calcSum(..)         // 计算求和并返回结果
    createForm(..)      // 创建表格 (通常会返回它)
    checkPermission(..) // 检查权限并返回 true/false

    PS:当 JavaScript 准备运行脚本或代码块时，它首先在其中查找函数声明并创建函数。我们可以将其视为“初始化阶段”.
    因此，声明为函数声明的函数可以比定义的更早调用。

### pow(x,n)
    将 x 与自身相乘 n 次，然后返回结果。


### 函数表达式
    let sayHi = function() {
      alert( "Hello" );
    };

### 回调函数&匿名函数
    function ask(question, yes, no) {
      if (confirm(question)) yes()
      else no();
    }

    ask(
      "Do you agree?",
      function() { alert("You agreed."); },
      function() { alert("You canceled the execution."); }
    );

### 箭头函数
    let func = function(arg1, arg2, ...argN) {
      return expression;
    }

    let sum = (a, b) => a + b;
    /* 箭头函数更短：
    let sum = function(a, b) {
      return a + b;
    };*/

    let double = n => n * 2; 只有一个参数，那么括号可以省略
    let sayHi = () => alert("Hello!"); 没有参数，括号应该是空的

### 编码风格

![avatar](https://raw.githubusercontent.com/fankcoder/fankcoder.github.io/master/static/code-style-js.png

### Figure brackets（大括号）
    if (condition) {
      // do this
      // ...and that
      // ...and that
    }

### “for…in” 循环
    为了使用对象所有的属性，就可以利用 for..in 循环。这跟 for(;;) 是完全不一样的东西。
    let user = {
      name: "John",
      age: 30,
      isAdmin: true
    };

    for(let key in user) {
      // keys
      alert( key );  // name, age, isAdmin
      // 属性键的值
      alert( user[key] ); // John, 30, true
    }

### Symbol 是唯一标识符的基本类型

    Symbol 使用 Symbol() 创建的，调用带有一个可选的描述。

    Symbol 总是不同的值，即使它们有相同的名称。如果我们希望同名 Symbol 相等，那么我们应该使用全局注册表：Symbol.for(key) 返回（如果需要的话创建）一个以 key 作为名称的全局 Symbol。Symbol.for 的多次调用完全返回相同的 Symbol。

### 字符串截取
    substr(start [, length ])
    返回一个从指定位置开始的指定长度的子字符串
    substring(start, end)
    返回位于 String 对象中指定位置的子字符串。


### 页面跳转
    window.navigate("top.jsp");
    window.history.back(-1);
    window.location.href="login.jsp?backurl="+window.location.href; 
    self.location='top.htm';
    top.location='xx.jsp';
    
### 加载完成
    window.onload 
    必须等页面内包括图片的所有元素加载完成后才能执行。
    不能同时编写多个，只执行一个
    $(document).ready()
    是DOM结构绘制完毕后就可以执行
    可以编写多个
    简写$(function(){});
    $(window).load()等同与window.onload

### 刷新页面
    history.go(0) 
    location.reload() 
    location=location 
    location.assign(location) 
    document.execCommand('Refresh') 
    window.navigate(location) 
    location.replace(location) 
    document.URL=location.href 

### json转化和解析

    JSON.parse("{a:'111',b:'ccc'}");  //解析
    eval("("+"{{ cpu_data }}"+")"); //解析

### 时间转换

    var day1 = parseInt(new Date().valueOf()/1000); //获得当前时间时间戳
    day2 = new Date(day1*1000);
    alert(day2.getFullYear()+"-"+(day2.getMonth()+1)+"-"+day2.getDate()+" "+day2.getHours()+":"+day2.getMinutes()+":"+day2.getSeconds())
    d = new Date();
    s = d.getFullYear() + "-";
    s += ("0"+(d.getMonth()+1)).slice(-2) + "-";
    s += ("0"+d.getDate()).slice(-2) + " ";
    s += ("0"+d.getHours()).slice(-2) + ":";
    s += ("0"+d.getMinutes()).slice(-2) + ":";
    s += ("0"+d.getSeconds()).slice(-2) + ".";
    s += ("00"+d.getMilliseconds()).slice(-3);

### URI编码转换

    var a="':'";
    en = encodeURI(a);    //编码
    a = decodeURI(en);    //解码

### HTML编码转换

    function htmlEncode(value){
      return $('<div/>').text(value).html();
    }

    function htmlDecode(value){
      return $('<div/>').html(value).text();
    }
