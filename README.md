#### 1、综合题
```
function Foo() {
    getName = function () { alert (1); };
    return this;
}
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
var getName = function () { alert (4);};
function getName() { alert (5);}

//答案：
Foo.getName();//2  
getName();//4   因为变量提升与函数表达式，4会覆盖5

---
    区别在于 var getName 是函数表达式，而 function getName 是函数声明
    函数表达式最大的问题，在于js会将此代码拆分为两行代码分别执行
    console.log(x);//输出：function x(){}
    var x=1;
    function x(){}
    ------------------------------------
    var x;
    function x(){}
    console.log(x);
    x=1;

---

Foo().getName();//1  1覆盖4，返回this，指向的是window ===window.getName
getName();//1   window.getName
new Foo.getName();//2   ne(Foo.getName)();
                        //将getName函数作为了构造函数来执行
new Foo().getName();//3 点（.）的优先级高于new操作  (new Foo()).getName()
                    //返回this，指向new，实例的getName 是protitype  
new new Foo().getName();//3  new ((new Foo()).getName)();

-----------------------------------------------------------------------------
var name = 'World!';
(function () {
    if (typeof name === 'undefined') {
        var name = 'Jack';
        console.log('Goodbye ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
------------------------------
 变量声明提前，在 JavaScript中， functions 和 variables 会被提升。
 变量提升是JavaScript将声明移至作用域 scope (全局域或者当前函数作用域) 顶部的行为。
    此题相当于：
    var name = 'World!';
    (function () {
        var name;
        if (typeof name === 'undefined') {
            name = 'Jack';
            console.log('Goodbye ' + name);
        } else {
            console.log('Hello ' + name);
        }
    })();
所以结果是：Goodbye Jack

```
#### 2、使用 typeof bar === "object" 判断 bar 是不是一个对象有神马潜在的弊端？如何避免这种弊端？

使用 typeof 的弊端是显而易见的(这种弊端同使用 instanceof)：


```
let obj = {};
let arr = [];

console.log(typeof obj === 'object');  //true
console.log(typeof arr === 'object');  //true
console.log(typeof null === 'object');  //true
从上面的输出结果可知，typeof bar === "object" 并不能准确判断 bar 就是一个 Object。可以通过 Object.prototype.toString.call(bar) === "[object Object]" 来避免这种弊端：

let obj = {};
let arr = [];

console.log(Object.prototype.toString.call(obj));  //[object Object]
console.log(Object.prototype.toString.call(arr));  //[object Array]
console.log(Object.prototype.toString.call(null));  //[object Null]
另外，为了珍爱生命，请远离 ==：
珍爱生命

而 [] === false 是返回 false 的。
```


#### 3、下面的代码会在 console 输出神马？为什么？


```
(function(){
  var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));   
console.log("b defined? " + (typeof b !== 'undefined'));
这跟变量作用域有关，输出换成下面的：

console.log(b); //3
console,log(typeof a); //undefined
拆解一下自执行函数中的变量赋值：

b = 3;
var a = b;
所以 b 成了全局变量，而 a 是自执行函数的一个局部变量。
a defined? false
b defined? true
```


### 4、下面的代码会在 console 输出神马？为什么？


```
var myObject = {
    foo: "bar",
    func: function() {
        var self = this;
        console.log("outer func:  this.foo = " + this.foo);
        console.log("outer func:  self.foo = " + self.foo);
        (function() {
            console.log("inner func:  this.foo = " + this.foo);
            console.log("inner func:  self.foo = " + self.foo);
        }());
    }
};
myObject.func();
第一个和第二个的输出不难判断，在 ES6 之前，JavaScript 只有函数作用域，所以 func 中的 IIFE 有自己的独立作用域，并且它能访问到外部作用域中的 self，所以第三个输出会报错，因为 this 在可访问到的作用域内是 undefined，第四个输出是 bar。如果你知道闭包，也很容易解决的：

(function(test) {
            console.log("inner func:  this.foo = " + test.foo);  //'bar'
            console.log("inner func:  self.foo = " + self.foo);
}(self));
outer func:  this.foo = bar
outer func:  self.foo = bar
inner func:  this.foo = undefined
inner func:  self.foo = bar
```


#### 5、将 JavaScript代码包含在一个函数块中有神马意思呢？为什么要这么做？

```
换句话说，为什么要用立即执行函数表达式（Immediately-Invoked Function Expression）。
闭包在函数化编程中也是经常用到，比如函数柯里化。部分应用

IIFE 有两个比较经典的使用场景，一是类似于在循环中定时输出数据项，二是类似于 JQuery/Node 的插件和模块开发。

for(var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i);  
    }, 1000);
}
上面的输出并不是你以为的0，1，2，3，4，而输出的全部是5，这时 IIFE 就能有用了：

for(var i = 0; i < 5; i++) {
    (function(i) {
      setTimeout(function() {
        console.log(i);  
      }, 1000);
    })(i)
}
而在 JQuery/Node 的插件和模块开发中，为避免变量污染，也是一个大大的 IIFE：

(function($) { 
        //代码
 } )(jQuery);
```

#### 6、在严格模式('use strict')下进行 JavaScript 开发有神马好处？


```
消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
消除代码运行的一些不安全之处，保证代码运行的安全；
提高编译器效率，增加运行速度；
为未来新版本的Javascript做好铺垫。
```

#### 7、下面两个函数的返回值是一样的吗？为什么？


```
function foo1()
{
  return {
      bar: "hello"
  };
}

function foo2()
{
  return
  {
      bar: "hello"
  };
}
在编程语言中，基本都是使用分号（;）将语句分隔开，这可以增加代码的可读性和整洁性。而在JS中，如若语句各占独立一行，通常可以省略语句间的分号（;），JS 解析器会根据能否正常编译来决定是否自动填充分号：

var test = 1 + 
2
console.log(test);  //3
在上述情况下，为了正确解析代码，就不会自动填充分号了，但是对于 return 、break、continue 等语句，如果后面紧跟换行，解析器一定会自动在后面填充分号(;)，所以上面的第二个函数就变成了这样：

function foo2()
{
  return;
  {
      bar: "hello"
  };
}
所以第二个函数是返回 undefined。
```


#### 8、神马是 NaN，它的类型是神马？怎么测试一个值是否等于 NaN?


```
NaN 是 Not a Number 的缩写，JavaScript 的一种特殊数值，其类型是 Number，不是一个数字同时自己不等于自己，可以通过 isNaN(param) 来判断一个值是否是 NaN：

console.log(isNaN(NaN)); //true
console.log(isNaN(23)); //false
console.log(isNaN('ds')); //true
console.log(isNaN('32131sdasd')); //true
console.log(NaN === NaN); //false
console.log(NaN === undefined); //false
console.log(undefined === undefined); //false
console.log(typeof NaN); //number
console.log(Object.prototype.toString.call(NaN)); //[object Number]
ES6 中，isNaN() 成为了 Number 的静态方法：Number.isNaN().
```


#### 9、解释一下下面代码的输出


```
console.log(0.1 + 0.2);   //0.30000000000000004
console.log(0.1 + 0.2 == 0.3);  //false
JavaScript 中的 number 类型就是浮点型，JavaScript 中的浮点数采用IEEE-754 格式的规定，这是一种二进制表示法，可以精确地表示分数，比如1/2，1/8，1/1024，每个浮点数占64位。但是，二进制浮点数表示法并不能精确的表示类似0.1这样 的简单的数字，会有舍入误差。

由于采用二进制，JavaScript 也不能有限表示 1/10、1/2 等这样的分数。在二进制中，1/10(0.1)被表示为 0.00110011001100110011…… 注意 0011 是无限重复的，这是舍入误差造成的，所以对于 0.1 + 0.2 这样的运算，操作数会先被转成二进制，然后再计算：

0.1 => 0.0001 1001 1001 1001…（无限循环）
0.2 => 0.0011 0011 0011 0011…（无限循环）
双精度浮点数的小数部分最多支持 52 位，所以两者相加之后得到这么一串 0.0100110011001100110011001100110011001100...因浮点数小数位的限制而截断的二进制数字，这时候，再把它转换为十进制，就成了 0.30000000000000004。

对于保证浮点数计算的正确性，有两种常见方式。

一是先升幂再降幂：

function add(num1, num2){
  let r1, r2, m;
  r1 = (''+num1).split('.')[1].length;
  r2 = (''+num2).split('.')[1].length;

  m = Math.pow(10,Math.max(r1,r2));
  return (num1 * m + num2 * m) / m;
}
console.log(add(0.1,0.2));   //0.3
console.log(add(0.15,0.2256)); //0.3756
二是是使用内置的 toPrecision() 和 toFixed() 方法，注意，方法的返回值字符串。

function add(x, y) {
    return x.toPrecision() + y.toPrecision()
}
console.log(add(0.1,0.2));  //"0.10.2"
```

#### 10、实现函数 isInteger(x) 来判断 x 是否是整数


```
可以将 x 转换成10进制，判断和本身是不是相等即可：

function isInteger(x) { 
    return parseInt(x, 10) === x; 
}
ES6 对数值进行了扩展，提供了静态方法 isInteger() 来判断参数是否是整数：

Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
JavaScript能够准确表示的整数范围在 -2^53 到 2^53 之间（不含两个端点），超过这个范围，无法精确表示这个值。ES6 引入了Number.MAX_SAFE_INTEGER 和 Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限，并提供了 Number.isSafeInteger() 来判断整数是否是安全型整数。
```


#### 11、在下面的代码中，数字 1-4 会以什么顺序输出？为什么会这样输出？

```
(function() {
    console.log(1); 
    setTimeout(function(){console.log(2)}, 1000); 
    setTimeout(function(){console.log(3)}, 0); 
    console.log(4);
})();
这个就不多解释了，主要是 JavaScript 的定时机制和时间循环，不要忘了，JavaScript 是单线程的。详解可以参考 从setTimeout谈JavaScript运行机制。
1432
```


#### 12、写一个少于 80 字符的函数，判断一个字符串是不是回文字符串


```
function isPalindrome(str) {
    str = str.replace(/\W/g, '').toLowerCase();
    return (str == str.split('').reverse().join(''));
}

```
#### 13、写一个按照下面方式调用都能正常工作的 sum 方法

```
console.log(sum(2,3));   // Outputs 5
console.log(sum(2)(3));  // Outputs 5
针对这个题，可以判断参数个数来实现：

function sum() {
  var fir = arguments[0];
  if(arguments.length === 2) {
    return arguments[0] + arguments[1]
  } else {
    return function(sec) {
       return fir + sec;
    }
  }

}
```



#### 14、根据下面的代码片段回答后面的问题


```
for (var i = 0; i < 5; i++) {
  var btn = document.createElement('button');
  btn.appendChild(document.createTextNode('Button ' + i));
  btn.addEventListener('click', function(){ console.log(i); });
  document.body.appendChild(btn);
}
1、点击 Button 4，会在控制台输出什么？
2、给出一种符合预期的实现方式

1、点击5个按钮中的任意一个，都是输出5
2、参考 IIFE。
```


#### 15、下面的代码会输出什么？为什么？


```
var arr1 = "john".split(''); j o h n
var arr2 = arr1.reverse(); n h o j
var arr3 = "jones".split(''); j o n e s
arr2.push(arr3);
console.log("array 1: length=" + arr1.length + " last=" + arr1.slice(-1));
console.log("array 2: length=" + arr2.length + " last=" + arr2.slice(-1));
会输出什么呢？你运行下就知道了，可能会在你的意料之外。

MDN 上对于 reverse() 的描述是酱紫的：

Description
The reverse method transposes the elements of the calling array object in place, mutating the array, and returning a reference to the array.
reverse() 会改变数组本身，并返回原数组的引用。

array 1: length=5 last=j,o,n,e,s
array 2: length=5 last=j,o,n,e,s
```


#### 16、下面的代码会输出什么？为什么？


```
console.log(10 + '20') ; 1020
console.log(1 +  "2" + "2"); //122
console.log(1 +  +"2" + "2"); //32
console.log(1 +  -"1" + "2"); //02
console.log(+"1" +  "1" + "2"); //112
console.log( "A" - "B" + "2"); //NaN2
console.log( "A" - "B" + 2); //NaN
输出什么，自己去运行吧，需要注意三个点：

多个数字和数字字符串混合运算时，跟操作数的位置有关
console.log(2 + 1 + '3'); / /‘33’
console.log('3' + 2 + 1); //'321'
数字字符串之前存在数字中的正负号(+/-)时，会被转换成数字
console.log(typeof '3');   // string
console.log(typeof +'3');  //number
同样，可以在数字前添加 ''，将数字转为字符串

console.log(typeof 3);   // number
console.log(typeof (''+3));  //string
对于运算结果不能转换成数字的，将返回 NaN
console.log('a' * 'sd');   //NaN
console.log('A' - 'B');  // NaN
```



#### 17、如果 list 很大，下面的这段递归代码会造成堆栈溢出。如果在不改变递归模式的前提下修善这段代码？


```
var list = readHugeList();

var nextListItem = function() {
    var item = list.pop();

    if (item) {
        // process the list item...
        nextListItem();
    }
};
原文上的解决方式是加个定时器：

var list = readHugeList();

var nextListItem = function() {
    var item = list.pop();

    if (item) {
        // process the list item...
        setTimeout( nextListItem, 0);
    }
};
解决方式的原理请参考第10题。
```

#### 18、什么是闭包？举例说明

```
函数返回函数
函数记住并访问其所在的词法作用域，叫做闭包现象，而此时函数对作用域的引用叫做闭包
```

#### 19、解释下列代码的输出


```
console.log("0 || 1 = "+(0 || 1));
console.log("1 || 2 = "+(1 || 2));
console.log("0 && 1 = "+(0 && 1));
console.log("1 && 2 = "+(1 && 2));
逻辑与和逻辑或运算符会返回一个值，并且二者都是短路运算符：

逻辑与返回第一个是 false 的操作数 或者 最后一个是 true的操作数
console.log(1 && 2 && 0);  //0
console.log(1 && 0 && 1);  //0
console.log(1 && 2 && 3);  //3
如果某个操作数为 false，则该操作数之后的操作数都不会被计算

逻辑或返回第一个是 true 的操作数 或者 最后一个是 false的操作数
console.log(1 || 2 || 0); //1
console.log(0 || 2 || 1); //2
console.log(0 || 0 || false); //false
如果某个操作数为 true，则该操作数之后的操作数都不会被计算

如果逻辑与和逻辑或作混合运算，则逻辑与的优先级高：

console.log(1 && 2 || 0); //2
console.log(0 || 2 && 1); //1
console.log(0 && 2 || 1); //1
在 JavaScript，常见的 false 值：

0, '0', +0, -0, false, '',null,undefined,null,NaN
要注意空数组([])和空对象({}):

console.log([] == false) //true
console.log({} == false) //false
console.log(Boolean([])) //true
console.log(Boolean({})) //true
所以在 if 中，[] 和 {} 都表现为 true：
```

#### 20、解释下面代码的输出


```
console.log(false == '0') //true
console.log(false === '0') //false
```

#### 21、解释下面代码的输出


```
var a={},
    b={key:'b'},
    c={key:'c'};

a[b]=123;
a[c]=456;

console.log(a[b]);
输出是 456，参考原文的解释：

The reason for this is as follows: When setting an object property, JavaScript will implicitly stringify the parameter value. In this case, since b and c are both objects, they will both be converted to "[object Object]". As a result, a[b] anda[c] are both equivalent to a["[object Object]"] and can be used interchangeably. Therefore, setting or referencing a[c] is precisely the same as setting or referencing a[b].
```



#### 22、解释下面代码的输出


```
console.log((function f(n){return ((n > 1) ? n * f(n-1) : n)})(10));
结果是10的阶乘。这是一个递归调用，为了简化，我初始化 n=5，则调用链和返回链如下：

递归
```


#### 23、解释下面代码的输出


```
(function(x) {
    return (function(y) {
        console.log(x);
    })(2)
})(1);
输出1，闭包能够访问外部作用域的变量或参数。
```


#### 24、解释下面代码的输出，并修复存在的问题


```
var hero = {
    _name: 'John Doe',
    getSecretIdentity: function (){
        return this._name;
    }
};

var stoleSecretIdentity = hero.getSecretIdentity;

console.log(stoleSecretIdentity());
console.log(hero.getSecretIdentity());
将 getSecretIdentity 赋给 stoleSecretIdentity，等价于定义了 stoleSecretIdentity 函数：

var stoleSecretIdentity =  function (){
        return this._name;
}
stoleSecretIdentity 的上下文是全局环境，所以第一个输出 undefined。若要输出 John Doe，则要通过 call 、apply 和 bind 等方式改变 stoleSecretIdentity 的this 指向(hero)。

第二个是调用对象的方法，输出 John Doe。
```


#### 25、给你一个 DOM 元素，创建一个能访问该元素所有子元素的函数，并且要将每个子元素传递给指定的回调函数。


```
函数接受两个参数：

DOM
指定的回调函数
原文利用 深度优先搜索(Depth-First-Search) 给了一个实现：

function Traverse(p_element,p_callback) {
   p_callback(p_element);
   var list = p_element.children;
   for (var i = 0; i < list.length; i++) {
       Traverse(list[i],p_callback);  // recursive call
   }
}
```
#### 26 小题

```
var foo = 1;
function bar() {
    foo = 10;
    return;
    function foo() {}
}
bar();
console.log(foo); //1

var foo = 1;
function bar() {
    foo = 10;
    return;
    function foo() {}
}
bar();
foo = 20
console.log(foo); //20

var foo = 1;
function bar() {
    foo = 10;
    return;
}
bar();
console.log(foo); //10

---------------------------------
var foo = {n:1};
var bar = foo;
foo.x = foo = {n:2};
console.log(foo.x);
console.log(bar.x);

undefined
Object {n: 2}

--------------------------------
var fullname = 'John Doe';
var obj = {
   fullname: 'Colin Ihrig',
   prop: {
      fullname: 'Aurelio De Rosa',
      getFullname: function() {
         return this.fullname;
      }
   }
};
console.log(obj.prop.getFullname());
 
var test = obj.prop.getFullname;
 
console.log(test());
Aurelio De Rosa
John Doe

------------------------------------
var a = new Object();
a.value = 1;
b = a;
b.value = 2;
alert(a.value); //2   

------------------------------------
var scope = 'top';
var f1 = function() {
    console.log(scope);
};
var f2 = function() {
    var scope = 'f2';
    f1();
};
f2();
 top

```
#### 27、下面这个ul，如何点击每一列的时候alert其index

```

// 方法一：
var lis=document.getElementById('2223').getElementsByTagName('li');
for(var i=0;i<3;i++)
{
    lis[i].index=i;
    lis[i].onclick=function(){
        alert(this.index);
    };
}
 
//方法二：
var lis=document.getElementById('2223').getElementsByTagName('li');
for(var i=0;i<3;i++)
{
    lis[i].onclick=(function(a){
        return function() {
            alert(a);
        }
    })(i);
}
```

#### 28、数组去重

```
var arr = [1,3,1,2,3,4,55,23,4,23,2];
        function uniquueArr(arr) {
            var obj = {},
                arr1 = [];
            for(var i=0,len = arr.length; i<len; i++) {
                if( !obj[arr[i]] ) {
                    obj[arr[i]] = true;
                    arr1.push(arr[i]);
                }
            }
            return arr1;
        }
        console.log(uniquueArr(arr));
        -----------------------------------------
        let unique = function(array){
          return [...new Set(array)];
        }
        ------------------------------------------
        let unique = function (array) {
            let ro = {};
            let ra = [];
            array.forEach(item=>{
                if(!ro[item]){
                    ro[item] = item;
                    ra.push(item);
                }
            });
            return ra;
        }
```
#### 29、找出一个字符串里面出现最多个数的字符

```
var str='dasdasdadsssssaaaaaaaaaaaaa';
        function maxCode(str) {
            var obj = {},
                index = 0,
                max = 0;
            for(var i=0,len = str.length; i<len; i++) {
                if( !obj[str.charAt(i)] ) {
                    obj[str.charAt(i)] = 1;
                } else {
                    obj[str.charAt(i)] ++;
                }
            }
            for(var i in obj){
                if(obj[i] > max) {
                    index = i;
                    max = obj[index];
                }
            }
            return {
                index :index,
                max : max
            }
        }
        console.log(maxCode(str));
        
----------------------------------------------------------------
let countChar = function countChar(str){
    let ro = {};
    for(let c of str){
        if(!ro[c]){
            ro[c] = 1;
        }else{
            ro[c] ++;
        }
    }
    return ro;
}
------------------------------------------------------------------
let countChar = function countChar(str){
    return Array.prototype.reduce.call(str,function(pre,current){
        if(pre[current]){
            pre[current] ++;
        }else{
            pre[current ] = 1;
        }
        return pre;
    },{});
}
-------------------------------------------------------------------
let findMaxDuplicateChar = function (str){
  let chars = countChar(str);
  let max = 0;
  let char = null;
  for(let c in chars){
    if(chars[c] > max){
      max = chars[c];
      char = c;
    }
  }
  return char;
}
```
#### 30、this指向问题

```
function A() {
            this.name = 'lvdaofeng';
            return 'wangdaming';
        };
        var a = new A();
        console.log(a.name);
        function B() {
            this.name = 'chengli';
            return {
                name : 'dapang'
            }
        }
        var b = new B();
        console.log(b.name);
lvdaofeng   指向的new
dapang
```
#### 31、函数编程的题

```
var test = ( function(a) {
    this.a = a;
    return function(b) {
        return this.a+ b;
    }
})( (function(a, b){
    return a
})(1,2) );
console.log(test(4));
5
```

#### 32、原型链与原型链


```
var a = 1;
        console.log(a.__proto__);//Number
        console.log(a.__proto__.__proto__);//Object
        console.log(a.__proto__.__proto__.__proto__);//null
        console.log(a.prototype);//undefined
        var a = {
            x:1
        };
        console.log(a.__proto__);//Object
        console.log(a.__proto__.__proto__);//null
        console.log(a.prototype);//undefined
        function a() {
            this.y = 2;
        }
        console.log(a.__proto__);//Object
        console.log(a.__proto__.__proto__);//null
        console.log(a);//Object {x: 1}
        var Person = function() {};
        var p = new Person();
        console.log(p.__proto__ );//Object {}
        console.log(Person.prototype);//Object {}
----------------------------------------------------------
var obj = {
            name:1
        };
        var aa = obj;
        aa.name = 2;
        var bb = obj ;
        bb.name = 3;
        console.log(aa.name);//3
        function Obj() {
            this.name = 1;
        }
        var aa = new Obj();
        aa.name = 2;
        var bb = new Obj();
        bb.name = 3;
-------------------------------------------------------------
function A() {
    this.add1 = function() {
        return ++this.num;
    }
}
A.prototype.num = 1;
var a1 = new A();
var a2 = new A();
console.log(a1.add1());//2
console.log(a2.add1());//2


---------------------------------------------------------------
function A() {
    this.add1 = function() {
        return ++this.num.x;
    }
}
A.prototype.num = {
    x:1
};
var a1 = new A();
var a2 = new A();
console.log(a1.add1());//2
console.log(a2.add1());//3
--------------------------------------------------------------

Array.isArray( Array.prototype )   //true
```

#### 33、一些有意思的题

```
1、["1", "2", "3"].map(parseInt)   //[1, NaN, NaN]
         *    parseInt的语法：parseInt(string, radix), 
         *     string必选。要被解析的字符串 
         *     radix可选。解析数字的基数，介于2~36之间，省略或为0则将以10为基数来解析，
                         小于2或大于36 则 parseInt() 将返回 NaN。
         *    return: 解析后的数字
             
         *    所以本题就是问：
         *    parseInt('1', 0);
         *    parseInt('2', 1);
         *    parseInt('3', 2);
         *
         *  所以结果输出： [1, NaN, NaN]
-----------------------------------------------------------------------------------------
2、[typeof null, null instanceof Object]

         * 解析：
         *    typeof 返回一个表示类型的字符串
             typeof 的结果请看下面:
             **type**         **result**
             Undefined   "undefined"
             Null        "object"
             Boolean     "boolean"
             Number      "number"
             String      "string"
             Symbol      "symbol"
             Host object Implementation-dependent
             Function    "function"
             Object      "object"

             instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上
             
             所以输出["object", false]
         *
-----------------------------------------------------------------------------------------------         
3、[ [3,2,1].reduce(Math.pow), [].reduce(Math.pow) ]
    第二个表达式会报异常. 
    第一个表达式等价于 Math.pow(3, 2) => 9; Math.pow(9, 1) =>9
    
    一个error
----------------------------------------------------------------------------------------------
4、var val = 'smtg';
console.log('Value is ' + (val === 'smtg') ? 'Something' : 'Nothing'); 
   + 优先级大于 ?
   此题等价于： 'Value is true' ? 'Something' : 'Nothing'
   所以结果是：'Something'
   
-----------------------------------------------------------------------------------------------  
5、var END = Math.pow(2, 53);
var START = END - 100;
var count = 0;
for (var i = START; i <= END; i++) {
    count++;
}
console.log(count);

js的精确整数最大为:Math.pow(2,53)-1 
会直接卡死

var a = 111111111111111110000,
    b = 1111;
a + b;//111111111111111110000  超过最大数


-------------------------------------------------------------------------------------------------
6、var ary = [0,1,2];
ary[10] = 10;
ary.filter(function(x) { return x === undefined;});

var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== 'function') {
      throw new TypeError();
    }
    var res = [];
    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++) {
      if (i in t) { // 注意这里!!!
        var val = t[i];
        if (fun.call(thisArg, val, i, t)) {
          res.push(val);
        }
      }
    }
    从上面可知filter对数组进行遍历时，会首先检查这个索引值是不是数组的一个属性.测试一下：
    console.info(0 in ary); //true
    console.info(1 in ary); //true
    console.info(4 in ary); //false
    console.info(10 in ary); // false
    也就是说3~9的索引根本没有是初始化
    
    所以答案：[];
    
    var ary = Array(3);
    ary[0]=2
    ary.map(function(elem) { return '1'; });
    
    ["1", undefined × 2]
    
--------------------------------------------------------------------------------------------    
7、var two   = 0.2
var one   = 0.1
var eight = 0.8
var six   = 0.6
[two - one == one, eight - six == two]

偶尔会有正确，true,false

----------------------------------------------------------------------------------------------
8、function showCase(value) {
    switch(value) {
    case 'A':
        console.log('Case A');
        break;
    case 'B':
        console.log('Case B');
        break;
    case undefined:
        console.log('undefined');
        break;
    default:
        console.log('Do not know!');
    }
}
showCase(new String('A'));   //Do not know!  switch 是===比较
-------------------------------------------------------------------------------------------------
9、  []==[]  false
/*
    解析： 这题没什么可说的，
    console.info([] instanceof Array); // true
    console.info([] instanceof Object); // true
    []是一个数组对象. [] == [] 等价于：
    var a = [];
    var b = [];
    a == b; 所以肯定是false
*/
----------------------------------------------------------------------------------------------
10、1 + - + + + - + 1   // 2  等价于  1 + 0-0 +0 +0 + 0- 0+ 1   
-----------------------------------------------------------------------------------------------
11、function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)   //21
----------------------------------------------------------------------------------------------
var x = [].reverse;
x();
//window 
----------------------------------------------------------------------------------------------
Number.MIN_VALUE > 0   也是大于0的  true
----------------------------------------------------------------------------------------------
console.info([1 < 2 < 3, 3 < 2 < 1]);
解析：
    这个题会让人误以为是 2 > 1 && 2 < 3 其实不是的.
    这个题相当于：
    1 < 2 => true;
    true < 3 => 1 < 3 =>true;
    3 < 2 => false;
    false < 1 => 0 < 1 =>true;
    
    所以答案是：[true, true]
------------------------------------------------------------------------------------------------
2 == [[[2]]]   true
-----------------------------------------------------------------------------------------------
3.toString()
3..toString()
3...toString()

3.toString() //error
3..toString() // '3'
3...toString() // error
var a = 3;
a.toString(); // '3'
-----------------------------------------------------------------------------------------------
var a = /123/,
    b = /123/;
a == b
a === b

正则表达式不比较
------------------------------------------------------------------------------------------------
var a = [1, 2, 3],
    b = [1, 2, 3],
    c = [1, 2, 4]
a ==  b
a === b
a >   c
a <   c
false, false, false, true    

数组不能用=== == 比较但是可以 > < 来比较
------------------------------------------------------------------------------------------------
var a = {}, b = Object.prototype;
[a.prototype === b, Object.getPrototypeOf(a) === b]
 解析：
    具体的对象没有prototype属性，所以a.prototype是undefined,
    Object.getPrototypeOf(obj) 返回一个具体对象的原型
    
    答案：false, true
------------------------------------------------------------------------------------------------
var lowerCaseOnly =  /^[a-z]+$/;
[lowerCaseOnly.test(null), lowerCaseOnly.test()]
------------------------------------------------------------------------------------------------
 ```

#### 34、不用临时变量，交换两个变量的值

```
function swap(a , b) {  
  b = b - a;
  a = a + b;
  b = a - b;
  return [a,b];
}
module.exports = swap;

```

#### 35、找出数组中最大差值


```
let getMaxProfit = function getMaxProfit(arr) {
    let max_min = arr.reduce(function(pre,current){
        if(pre.min > current){
            pre.min = current;
        }
        if(pre.max < current){
            pre.max = current;
        }
        return pre;
    },{min:arr[0],max:arr[0]});
    return max_min.max - max_min.min;
}
--------------------------------------------------------------
let getMaxProfit = function getMaxProfit(arr){
    let max = Math.max.apply(Math,arr);
    let min = Math.min.apply(Math,arr);
    return max - min;
}
---------------------------------------------------------------
let getMaxProfit = function getMaxProfit(arr){
    let max = Math.max(...arr);
    let min = Math.min(...arr);
    return max - min;
}
----------------------------------------------------------------

```
