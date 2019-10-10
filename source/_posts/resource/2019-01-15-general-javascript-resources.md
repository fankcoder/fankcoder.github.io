---
layout: post
title: JavaScript 笔记
category: 备忘
tags: JavaScript
keywords: JavaScript
description: 
---
<!-- more-->
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

    let id1 = Symbol("id");
    let id2 = Symbol("id");
    alert(id1 == id2); // false

    Symbol 总是不同的值，即使它们有相同的名称。如果我们希望同名 Symbol 相等，那么我们应该使用全局注册表：Symbol.for(key) 返回（如果需要的话创建）一个以 key 作为名称的全局 Symbol。Symbol.for 的多次调用完全返回相同的 Symbol。
    
### 方法中的 “this”
    对象方法需要访问对象中的存储的信息来完成其工作。为了访问该对象，方法中可以使用 this 关键字。
    let user = {
      name: "John",
      age: 30,
      sayHi() {
        alert(this.name);
      }
    };
    user.sayHi(); // John
    this 的值就是在点之前的这个对象，即调用该方法的对象

### 构造函数
    构造函数在技术上是常规函数。不过有两个约定：
    他们首先用大写字母命名。
    它们只能用 "new" 操作符来执行。这样的调用意味着在开始时创建空的 this，并在最后返回填充的对象。

    例如：
    function User(name) {
      this.name = name;
      this.isAdmin = false;
    }
    let user = new User("Jack");
    alert(user.name); // Jack
    alert(user.isAdmin); // false

    当一个函数作为 new User(...)执行时，它执行以下步骤：
    一个新的空对象被创建并分配给 this。
    函数体执行。通常它会修改 this，为其添加新的属性。返回 this 的值。

### 字符串
    字符串可以包含在单引号、双引号或反引号中.
    单引号和双引号本质上是一样的。但是，反引号允许我们将任何表达式嵌入到字符串中，包括函数调用.
    反引号的另一个优点是它们允许字符串跨行

    alert( `My\n`.length );   //length 属性有字符串长度

    let str = `Hello`;
    alert( str[1000] ); // undefined
    alert( str.charAt(1000) ); // '' (an empty string)

### 数组
    let arr = new Array();
    let arr = [];

    push 在末端添加一个元素.pop 从末端取出一个元素.

    shift取出数组的第一个元素并返回它.unshift在数组的前端添加元素

    性能push/pop 方法运行的比较快，而 shift/unshift 比较慢。

    遍历
    let fruits = ["Apple", "Orange", "Plum"];

    // 迭代数组元素
    for (let fruit of fruits) {
      alert( fruit );
    }

    let arr = ["Apple", "Orange", "Pear"];

    for (let i = 0; i < arr.length; i++) {
      alert( arr[i] );
    }

    alert( arr.length );

#### delete
    元素被删除，但数组长度不变

#### splice
    arr.splice(index[, deleteCount, elem1, ..., elemN])
    索引，删除个数，替换。并且返回删除的元素

#### slice
    arr.slice(start, end)
    它从所有元素的开始索引 "start" 复制到 "end" (不包括 "end") 返回一个新的数组。

#### concat
    arr.concat(arg1, arg2...)

#### 查询数组
    这些是在数组中查询某些内容的方法。

    indexOf/lastIndexOf 和 includes
    arr.indexOf、arr.lastIndexOf 和 arr.includes 方法与字符串操作具有相同的语法，只不过这里是对数组元素而不是字符进行操作：

    arr.indexOf(item, from) 从索引 from 查询 item，如果找到返回索引，否则返回 -1。
    arr.lastIndexOf(item, from) — 和上面相同，只是从尾部开始查询。
    arr.includes(item, from) — 从索引 from 查询 item，如果找到则返回 true。
