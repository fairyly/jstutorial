An open JavaScript tutorial book, focusing on client devices, written in Chinese.

# JavaScript 标准教程   by ruanyifeng


* 1.区分 null 和 undefined

  null是一个表示”无”的对象，转为数值时为0；undefined是一个表示”无”的原始值，转为数值时为NaN。
  
* 2.函数可以调用自身，这就是递归（recursion）。下面就是通过递归，计算斐波那契数列的代码。
  ```
  function fib(num) {
    if (num === 0) return 0;
    if (num === 1) return 1;
    return fib(num - 2) + fib(num - 1);
  }

  fib(6) // 8
  ```

* 3.Base64转码

  ```
  Base64是一种编码方法，可以将任意字符转成可打印字符。使用这种编码方法，主要不是为了加密，而是为了不出现特殊字符，简化程序的处理。

  JavaScript原生提供两个Base64相关方法。

  btoa()：字符串或二进制值转为Base64编码
  atob()：Base64编码转为原来的编码
  var string = 'Hello World!';
  btoa(string) // "SGVsbG8gV29ybGQh"
  atob('SGVsbG8gV29ybGQh') // "Hello World!"
  这两个方法不适合非ASCII码的字符，会报错。

  btoa('你好')
  // Uncaught DOMException: The string to be encoded contains characters outside of the Latin1 range.
  要将非ASCII码字符转为Base64编码，必须中间插入一个转码环节，再使用这两个方法。

  function b64Encode(str) {
    return btoa(encodeURIComponent(str));
  }

  function b64Decode(str) {
    return decodeURIComponent(atob(str));
  }

  b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
  b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
  ```

* 4.不能在条件语句中声明函数
  ```
  根据ECMAScript的规范，不得在非函数的代码块中声明函数，最常见的情况就是if和try语句。
  
  if (foo) {
    function x() {}
  }

  try {
    function x() {}
  } catch(e) {
    console.log(e);
  }
  上面代码分别在if代码块和try代码块中声明了两个函数，按照语言规范，这是不合法的。但是，实际情况是各家浏览器往往并不报错，能够运行。

  但是由于存在函数名的提升，所以在条件语句中声明函数，可能是无效的，这是非常容易出错的地方
  ```

* 5.立即调用的函数表达式（IIFE）

  ```
  在Javascript中，一对圆括号()是一种运算符，跟在函数名之后，表示调用该函数。比如，print()就表示调用print函数。

  有时，我们需要在定义函数之后，立即调用该函数。这时，你不能在函数的定义之后加上圆括号，这会产生语法错误。

  function(){ /* code */ }();
  // SyntaxError: Unexpected token (
  产生这个错误的原因是，function这个关键字即可以当作语句，也可以当作表达式。

  // 语句
  function f() {}

  // 表达式
  var f = function f() {}
  为了避免解析上的歧义，JavaScript引擎规定，如果function关键字出现在行首，一律解释成语句。因此，JavaScript引擎看到行首是function关键字之后，认为这一  段都是函数的定义，不应该以圆括号结尾，所以就报错了。

  解决方法就是不要让function出现在行首，让引擎将其理解成一个表达式。最简单的处理，就是将其放在一个圆括号里面。

  (function(){ /* code */ }());
  // 或者
  (function(){ /* code */ })();
  上面两种写法都是以圆括号开头，引擎就会认为后面跟的是一个表示式，而不是函数定义语句，所以就避免了错误。这就叫做“立即调用的函数表达式”（Immediately-  Invoked Function Expression），简称IIFE。

  注意，上面两种写法最后的分号都是必须的。如果省略分号，遇到连着两个IIFE，可能就会报错。

  // 报错
  (function(){ /* code */ }())
  (function(){ /* code */ }())
  上面代码的两行之间没有分号，JavaScript会将它们连在一起解释，将第二行解释为第一行的参数。

  推而广之，任何让解释器以表达式来处理函数定义的方法，都能产生同样的效果，比如下面三种写法。

  var i = function(){ return 10; }();
  true && function(){ /* code */ }();
  0, function(){ /* code */ }();
  甚至像下面这样写，也是可以的。

  !function(){ /* code */ }();
  ~function(){ /* code */ }();
  -function(){ /* code */ }();
  +function(){ /* code */ }();
  new关键字也能达到这个效果。

  new function(){ /* code */ }

  new function(){ /* code */ }()
  // 只有传递参数时，才需要最后那个圆括号
  通常情况下，只对匿名函数使用这种“立即执行的函数表达式”。它的目的有两个：一是不必为函数命名，避免了污染全局变量；二是IIFE内部形成了一个单独的作用域，  可以封装一些外部无法读取的私有变量。

  // 写法一
  var tmp = newData;
  processData(tmp);
  storeData(tmp);

  // 写法二
  (function (){
    var tmp = newData;
    processData(tmp);
    storeData(tmp);
  }());
  上面代码中，写法二比写法一更好，因为完全避免了污染全局变量。
  ```

* 6.parseInt方法还可以接受第二个参数（2到36之间），
  表示被解析的值的进制，返回该值对应的十进制数。默认情况下，parseInt的第二个参数为10，即默认是十进制转十进制。
  
  ```
  parseInt('1000') // 1000
  // 等同于
  parseInt('1000', 10) // 1000
  下面是转换指定进制的数的例子。

  parseInt('1000', 2) // 8
  parseInt('1000', 6) // 216
  parseInt('1000', 8) // 512
  上面代码中，二进制、六进制、八进制的1000，分别等于十进制的8、216和512。这意味着，可以用parseInt方法进行进制的转换。

  如果第二个参数不是数值，会被自动转为一个整数。这个整数只有在2到36之间，才能得到有意义的结果，超出这个范围，则返回NaN。如果第二个参数是0、undefined   和null，则直接忽略。

  parseInt('10', 37) // NaN
  parseInt('10', 1) // NaN
  parseInt('10', 0) // 10
  parseInt('10', null) // 10
  parseInt('10', undefined) // 10
  如果字符串包含对于指定进制无意义的字符，则从最高位开始，只返回可以转换的数值。如果最高位无法转换，则直接返回NaN。

  parseInt('1546', 2) // 1
  parseInt('546', 2) // NaN
  上面代码中，对于二进制来说，1是有意义的字符，5、4、6都是无意义的字符，所以第一行返回1，第二行返回NaN。

  前面说过，如果parseInt的第一个参数不是字符串，会被先转为字符串。这会导致一些令人意外的结果。

  parseInt(0x11, 36) // 43
  // 等同于
  parseInt(String(0x11), 36)
  parseInt('17', 36)
  上面代码中，十六进制的0x11会被先转为十进制的17，再转为字符串。然后，再用36进制解读字符串17，最后返回结果43。

  这种处理方式，对于八进制的前缀0，尤其需要注意。

  ```

* 7.parseFloat()

  ```
  parseFloat方法用于将一个字符串转为浮点数。

  parseFloat('3.14') // 3.14
  如果字符串符合科学计数法，则会进行相应的转换。

  parseFloat('314e-2') // 3.14
  parseFloat('0.0314E+2') // 3.14
  如果字符串包含不能转为浮点数的字符，则不再进行往后转换，返回已经转好的部分。

  parseFloat('3.14more non-digit characters') // 3.14
  parseFloat方法会自动过滤字符串前导的空格。

  parseFloat('\t\v\r12.34\n ') // 12.34
  如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回NaN。

  parseFloat([]) // NaN
  parseFloat('FF2') // NaN
  parseFloat('') // NaN
  上面代码中，尤其值得注意，parseFloat会将空字符串转为NaN。

  这些特点使得parseFloat的转换结果不同于Number函数。

  parseFloat(true)  // NaN
  Number(true) // 1

  parseFloat(null) // NaN
  Number(null) // 0

  parseFloat('') // NaN
  Number('') // 0

  parseFloat('123.45#') // 123.45
  Number('123.45#') // NaN
  ```

* 8、ajax原理和XmlHttpRequest对象
 
    Ajax的原理简单来说通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。这其中最关键的一步就是从     服务器获得请求数据。要清楚这个过程和原理，我们必须对 XMLHttpRequest有所了解。
    
* 9、深拷贝和浅拷贝

  JS 中的浅拷贝与深拷贝，只是针对复杂数据类型（Object，Array）的复制问题。浅拷贝与深拷贝都可以实现在已有对象上再生出一份的作用。但是对象的实例是存储
  
  在堆内存中然后通过一个引用值去操作对象，由此拷贝的时候就存在两种情况了：拷贝引用和拷贝实例，这也是浅拷贝和深拷贝的区别。

  浅拷贝：浅拷贝是拷贝引用，拷贝后的引用都是指向同一个对象的实例，彼此之间的操作会互相影响
  
  深拷贝：在堆中重新分配内存，并且把源对象所有属性都进行新建拷贝，以保证深拷贝的对象的引用图不包含任何原有对象或对象图上的任何对象，拷贝后的对象与原   来的对象是完全隔离，互不影响
  
  ```
  浅拷贝

  浅拷贝分两种情况，拷贝直接拷贝源对象的引用 和 源对象拷贝实例，但其属性（类型为Object，Array的属性）拷贝引用。

  拷贝原对象的引用

  这是最简单的浅拷贝。例：


  var a = {c:1};
  var b = a;
  console.log(a === b); // 输出true。
  a.c = 2;
  console.log(b.c); // 输出 2
  源对象拷贝实例，其属性对象拷贝引用。

  这种情况，外层源对象是拷贝实例，如果其属性元素为复杂杂数据类型时，内层元素拷贝引用。
  对源对象直接操作，不影响两外一个对象，但是对其属性操作时候，会改变两外一个对象的属性的只。
  常用方法为：Array.prototype.slice(), Array.prototype.concat(), jQury的$.extend({},obj)，例：


  var a = [{c:1}, {d:2}];
  var b = a.slice();
  console.log(a === b); // 输出false，说明外层数组拷贝的是实例
  a[0].c = 3;
  console.log(b[0].c); // 输出 3，说明其元素拷贝的是引用
  
  
  深拷贝

  深拷贝后，两个对象，包括其内部的元素互不干扰。常见方法有JSON.parse(),JSON.stringify()，jQury的$.extend(true,{},obj)，lodash的_.cloneDeep和_.clone(value, true)。例：


  var a = {c: {d: 1}};
  var b = $.extend(true, {}, a);
  console.log(a === b); // 输出false
  a.c.d = 3;
  console.log(b.c.d); // 输出 1，没有改变。

  ```
