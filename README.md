## JavaScript基础总结

- js基础知识总结，包含es6部分。

-------

### 目录

[TOC]

------
###  1. 数据类型

#### 1.1  数据类型种类

1. 原始类型： 字符串，数字型，布尔型， undefined， null，Symbol（es6）

2. 对象类型： 对象、数组、函数

<JavaScript权威指南>

```js

//new出来的，typeof 均为object

Number(); // 0, 数字0

new Number(); // Number {0} ,__proto__: Number

//Symbol： 无字面形式

```

#### 1.2 数据类型检测

##### 1.2.1 typeof

1. 一元运算符，优先级较高，后面()可写可不写

2. 返回值：

  1. 原始数据类型：number, string, boolean,  undefined, symbol(es6), 

  2. 引用数据类型：object, function, 【注意：无数组】

3. 缺点：

  1. null 识别为 object

  2. NaN 识别为 number

  3. new出来的，都是object

  4. 没有单独的数组类型，为object

```js

let firstName = Symbol();

typeof firstName; // 'symbol'

typeof NaN; //number

typeof null; //object

typeof []; //object

//new 出来的都是object

typeof new Boolean(); //object

typeof new String(); //object

typeof new Number(); //object

typeof false; //boolean

typeof Boolean(); //boolean

typeof Number(); //number

typeof Array(); //object,typeof无array类型

//没有单独的数组类型

typeof Array(); //object

```

##### 1.2.2 instanceof

运算符，用于检测构造函数的prototype是否出现在检测对象的原型链上

```js

[] instanceof Array; //true

new Array(1,2,3) instanceof Array

{} instanceof Object; //true

true instanceof Boolean; //false

new Boolean(true) instanceof Boolean; //true

function C(){}

var d = new C()

d instanceof C; //true

```

##### 1.2.3 检测数组

**Array.isArray()**  ES5

```js

let arr = [];

Array.isArray(arr); //true

//类数组

function fn(arguments){

  Array.isArray(arguments); //false

}

fn();

```

Array .isArray ()优于 instanceof，因为可以检测iframes传入的数组

```js

var iframe = document.createElement('iframe');

document.body.appendChild(iframe);

document.body.appendChild(iframe);

var xArray = window.frames[1].Array;

var arr = new xArray(1,2,3); //[1,2,3]

arr instanceof Array //false

Array.isArray(arr) //true

```

Array.isArray 不存在时，使用如下方法创建

```js

if(!Array.isArray){

  Array.isArray = function(obj){

    return Object.prototype.toString.call(obj) === '[Object Array]'

  }

}

```

##### 1.2.4 Object.prototype.toString.call(arg)  类识别 

返回值：[object + 类型（首字母大写）]

1. 原始数据类型：String, Number, Boolean, Undefined, Null, Symbol*(es6)*

2. 引用数据类型：Function, Array, Object, RegExp, Window,  Set*(es6)*, Map*(es6)*

```js

/*原始数据类型*/

//字符串

{

  Object.prototype.toString.call('dd'); //"[object String]"

  Object.prototype.toString.call(new String('dd')); //"[object String]"

}

//数字型

{

  Object.prototype.toString.call(3); //"[object Number]"

  Object.prototype.toString.call(new Number(3)); //"[object Number]"

  Object.prototype.toString.call(NaN); //"[object Number]"

}

//布尔型

{

  Object.prototype.toString.call(true); //"[object Boolean]"

  Object.prototype.toString.call(new Boolean(2)); //"[object Boolean]"

}

//undefined

{

  Object.prototype.toString.call(undefined); //"[object Undefined]"

}

//null

{

  Object.prototype.toString.call(null); //"[object Null]"

}

//symbol

{

  Object.prototype.toString.call(Symbol()); //"[object Symbol]"

}

/*引用数据类型*/

//函数

{

  Object.prototype.toString.call(function(){}); //"[object Function]"

  Object.prototype.toString.call(new Function()); //"[object Function]"

}

//数组

{

  Object.prototype.toString.call(new Array()); //"[object Array]"

  Object.prototype.toString.call([]); //"[object Array]"

}

//对象

{

  Object.prototype.toString.call({}); //"[object Object]"

  Object.prototype.toString.call(new Object()); //"[object Object]"

}

//set

{

  Object.prototype.toString.call(new Set()); // "[object Set]"

}

//map

{

  Object.prototype.toString.call(new Map()); //"[object Map]"

}

```

##### 1.2.5 数字检测，排除NaN影响

- 利用只有NaN不等于自身，排除NaN

- es6中 Object.is()，可弥补有些浏览器下，全等缺陷

- isNaN 检测*是否为非数字*。判断Number()的结果是否为NaN

- Number.isNaN()  [*es6*]检测*是否为NaN*。 isNaN增强版

```js

//利用只有NaN不等于自身，排除NaN

{

  if(typeof arg === 'number' && arg === arg){}

}

//es6中 Object.is()，可弥补有些浏览器下，全等缺陷

{

  +0 === -0; //某些浏览器下，false

  NaN === NaN; //false

  Object.is(+0, -0); //true

  Object.is(NaN, NaN); //true

}

//ES5中，isNaN() 函数检测是否为非数字。

//等价于：检测Number()结果是否为NaN

{

  isNaN(1); //false

  isNaN('1'); //false,会进行数据类型转换

  isNaN(''); // false , Number('') === 0

  isNaN(null); //false，Number(null) === 0

  isNaN(false); //false，Number(false) === 0

  isNaN(NaN); //true

  isNaN(undefined); //true，Number(undefined) => NaN

  isNaN('a'); //true，Number('a') => NaN

  isNaN({}); //true

  isNaN(function(){}); //true

}

//Number.isNaN() [es6]检测是否为NaN， isNaN增强版

{

  Number.isNaN(NaN); //true

  //其他都是false

  Number.isNaN(true);  //false

  Number.isNaN(undefined) //false

  Number.isNaN('') //false

}

```

Number.isNaN() 的 polyfill

- 利用isNaN为true时，只有NaN的typeof 结果为number

- 利用只有 NaN 不跟自己相等的特性

```js

//在isNaN() 的基础上增加了一个 type 的判断

{

  if(!Number.isNaN){

    Number.isNaN = function(n){

      return typeof n === 'number' && window.isNaN(n);

    }

  }

}

//只有 NaN 不跟自己相等的特性。

{

  if(!Number.isNaN){

    Number.isNaN = function(n){

      return n !== n;

    }

  }

}

```

------

### 2. 数组

#### 2.1 数组创建

1. 通过构造函数： new Array()

2. 数组字面量。[]

3. Array.of()  。*es6*

4. Array.from(iterator, mapFn, thisArg) 从一个类似数组或可迭代对象中创建一个新的数组实例。*es6*

```js

//1. new 通过构造函数

{

  let arr1 = new Array(3); //length为3的空数组[]

  let arr2 = new Array(1,2); //[1,2]

  let arr3 = new Array('grace'); //['grace]

}

//通过构造函数，创建数组，可省略关键字new

{

  let arr = Array(3,4);//[3,4]

  arr instanceof Array; //true

}

//2. [] 通过数组字面量创建

{

  let arr1 = [1, 2, 3];

  let arr2 = [,,]; //【不推荐】ie8- 3项，其他浏览器 2项

  let arr3 = [1,2,]; //【不推荐】 ie8- 3项[1, 2, undefined]， 其他浏览器[1,2]

}

//3 Array.of()

{

  Array.of(2); //[2]

  Array.of(2,3); //[2,3]

}

//4 Array.from()

{

  Array.from('abc'); //['a', 'b', 'c']

  Array.from(312); //[] 数字不可迭代



  //用于数组去重，映射函数用法同map

  let set = new Set([2,2,3,3])

  Array.from(set, (item)=>{

    return item * 2

  }); //[4, 6]

}

```

####  2.2 数组读取

1. 数组是经过优化的对象，访问速度比一般对象快；

2. 数组索引范围：0～2^32 -2。

3. 数组索引，是特殊的对象属性。数字索引，会转化为字符串，进行访问。可以为数组自定义属性名。但只有在索引范围的属性名，才是数组索引，才会影响数组长度。

4. 可通过控制数组长度，增删数组

```js

let arr = [1,2]

//字符串索引 == 数字索引

arr[1]; //2

arr['1']; //2

//数组为特殊的对象，可自定义属性

//在索引范围的属性值，为索引，改变数组长度

arr[3] = 3; // arr: [1,2,,3] | arr.length: 4

//不再索引范围的，不改变数组长度

arr['key'] = 'value'; //arr=[1,2,,3,{key: 'value'}] | arr.length: 4

```

#### 2.3 数组方法

##### 2.3.1 修改原数组

1. 栈方法( last in first out) ：push, pop

2. 队列方法(first in first out)：unshift, shift

3. 操作方法-数组删除/插入/替换：splice

4. 重排序方法: reverse, sort

5. 填充数组：fill, copyWithin。*es6*

```js

//push，unshift

{

  let arr = [3,4,2];

  arr.push(0); //返回新数组长度4， arr = [3,4,2,0]

}

//pop, shift

{

  let arr = [3,4,2];

  arr.pop(); //返回弹出项, arr = [3,4,2]

}

//splice

//参数：（startIndex, delNum, insertItems）

//startIndex为负时，用this.length相加

//返回包含删除项的数组

//在原数组上进行修改

{

  let arr = [1,2,3];

  //插入

  arr.splice(0,0,3,3);// arr: [3,3,1,2,3],返回[]，因删除0项

  //删除

  arr.splice(0, 3); //arr: [2,3], 返回：[3,3,1]

  //替换：先删除，再插入

  arr.splice(0,1,5); //arr: [5,3], 返回： [2]



  //warning

  arr.splice(1); //[2,3]指定startIndex,但未指定删除数量时，删除到末尾

}

//reverse, sort

//修改并返回原数组

{

  let arr = [3,4,2];

  let arr2 = arr.reverse(); //arr = [3,4,2]， 返回值同arr

  let arr3 = arr.sort(); //arr=[2,3,4]，sort默认按照升序排列

  console.log(arr === arr2, arr === arr3); //true, true





  /*arr.sort(compareFn)，可以接受一个比较函数，比较Fn两个参数

  如果第1个参数应位于第2个参数前，返回负数

  如果第1个参数应位于第2个参数后，返回正数

  如果两个参数相等，返回0

  ==》返回负数和0，不动；返回正数，前后两项交换位置。

  */

  //升序

  function compare(val1, val2){

    if(val1 < val2){

      return -1;

    }else if(val1 > val2){

      return 1;

    }else{

      return 0;

    }

  }

  //简化

  function compare(val1, val2){

    return val1 - val2;

  }

  eg:

  let obj = [{No: 3, name: 'melon'}, {No: 2, name: 'strawberry'}]

  obj.sort((item1, item2)=>{

    return item1.No - item2.No;

  });

  console.log(obj); //[{No: 2, name: 'strawberry'}, {No: 3, name: 'melon'}]

}

//fill

//参数：(val, startIndex, endIndex)

//1. start 默认为0；end 默认为 this.length。 fill替换的对象为this。

//2. start、end 为负数时。用this.length 相加。start < end时，不操作。

// 修改并返回原数组

{

  let arr = [1,2,3];

  arr.fill(5); //[5,5,5]

  arr.fill(6,1); //[5,6,6]

  arr.fill(7,1,2); //[5,7,6]

  let arr2 = arr.fill(8, -2, -1); //[5,8,6]



  console.log(arr === arr2); //true

}

//copyWithin

//参数：(pasteStart, copyStart, copyEnd)

//参数规律同 fill

//修改并返回数组

{

  let arr = [1,2,3,4];

  let arr2 = arr.copyWithin(0,2); // [3,4,3,4]

  arr.copyWithin(-2,1,3); //[3,4,4,3] pasteStart：-2 + arr.length

  arr.copyWithin(1); //[3,3,4,4] copyStartmore从0开始

  console.log(arr === arr2); //true

}

//fill vs copyWithin

//fill: 填充数组值来源于外部

//copyWithin: 填充数组与元素来源于本数组

```

##### 2.3.2 不修改原数组的 :

1. 数组转换方法：

  - 转化为字符串：toString(), toLocaleString(), join,

  - 返回数组：valueOf()

```js

let arr = [3,4,undefined,null];

//toString, toLocaleString(), join

//undefined, null 会被转化为空字符串

{

  arr.toString(); //返回'3,4,,'

  arr.join('-'); //返回'3-4--'

  arr.join(); //返回'3,4,,'

}

//valueOf 返回数组

//valueOf结果指向arr引用地址

let arr2 = arr.valueOf(); // 返回[3,4,undefined,null]

{

  let arr = [];

  let arr1 = arr.valueOf();

  arr.push(1);

  console.log(arr); //[1]

  console.log(arr1); //[1]

}

```

2. 操作方法：concat、slice

```js

//concat

{

  let arr = [1]

  let arr2 = arr.concat(2); //[1,2]

  let arr3 = arr.concat([3,3]); //[1,3,3]

}



//slice

{

  let arr = [3,4,undefined,null];

  let arr5 = arr.slice(); // [3,4,undefined,null]

  let arr6 = arr.slice(1); //[4,undefined,null]

  let arr7 = arr.slice(1,3); //[4,undefined]，不包括结束位置index

  let arr8 = arr.slice(1, -1); //等价于arr.slice(1, arr.length + (-1))

}

//slice截取数组，原数组中的引用数据类型，只拷贝了引用地址

{

  let obj = {a: 1};

  let arr = [];

  let arr1 = arr.slice();

  arr.push(obj); //arr: [{a:1}]

  let arr2 = arr.slice(); //[{a:1}]



  obj.a = 2;

  console.log(arr); //[{a:2}]

  console.log(arr2); //[{a:2}]

  console.log(arr1); //[]

}

```

3. 位置方法：

- indexOf() 、lastIndexOf() 

  - 返回数组中给定元素的第一个索引。

  - 找不到返回-1。

- find()、findIndex()  两个参数：回掉函数、this指向。 指定条件 检索数组。 *es6*

  - find返回第一个符合条件的元素。找不到返回undefined。

  - findIndex 返回第一个符合条件的值的索引。找不到返回-1

```js

let arr = [1,2,3];

//indexOf() ，lastIndexOf()

{

  arr.indexOf(2); //'1'

  arr.lastIndexOf(2); //'3'

  arr.indexOf(5); //-1

}

//find, findIndex

{

  arr.find(item => item > 2); //3

  arr.find(item => item > 5); //undefined



  arr.findIndex(item => item > 2); //2

  arr.findIndex(item => item > 5); -1

}

```

3. 迭代方法（ES5）：均不修改原数组。每一项上调用的函数有3个参数—迭代项、该项的位置、数组本身。

      -  every、some： 返回布尔型

      -  filter、map：返回数组。

      -  forEach: 无返回值。作用同for。按顺序遍历。

```js

let arr = [1,2,3]

//every——检测是否所有项都满足条件

let everyRes = arr.every((item, index, arr)=>{

  console.log(this)

  return item > 2;

});

everyRes; //false



//some——检测是否存在满足条件的项

let someRes = arr.some((item,index, arr)=>{

  return item > 2;

});

someRes; //true



// filter——筛选出符合条件的项

let filterRes = arr.filter((item, index, arr)=>{

  return item >1;

});

filterRes; //[2,3]



//map——将return结果组成新数组

let mapRes = arr.map((item, index, arr)=>{

  return item * 2;

});

mapRes; //[2,4,6]



//forEach，用途相当于for

arr.forEach((item, index, arr)=>{

  console.log(item)

})

```

4. 归并方法：reduce、reduceRight

      - 参数：

        - 接受2个参数。每一项上调用的函数、作为归并初始值。

        - 函数有4个参数，前一个值（prev）、当前值（cur）、当前项的索引（index）、该数组。

          - 有初始值时，第一次遍历，prev为归并初始值,cur为第一项，index为0；迭代arr.length次。

          - 无初始值时，第一次遍历，prev为第0项，cur为第2项，index为1。迭代arr.length - 1次。

      - reduce：从第一项开始

      - reduceRight：从最后一项开始

```js

let arr = [1,2,3]

//迭代3次,从第1项开始

let reduceRes1 = arr.reduce((prev, cur, index, arr)=>{

  console.log(index);

  return prev + cur;

}, 0);

//index 0

//index 1

//index 2

//reduceRes1 6



//迭代2次，从第2项开始

let reduceRes2 = arr.reduce((prev, cur, index, arr)=>{

  console.log(index);

  return prev + cur;

});

//index 1

//index 2

//reduceRes1 6

```

Q：检测数组中是否存在某元素的方法：

A：`indexOf`、`lastIndexOf`、`some`、`find`、`findIndex`

#### 2.4 类数组对象

##### 2.4.1 类数组特点

- 有length属性。

- 有可转化为正整数的索引，且索引 < 2^32 - 1。

- 只能调用数组的某些方法：

  - 3种经典类数组，可用forEach遍历法

  - 自定义类数组，不能用forEach遍历。

  - 上述两种，均可用for循环遍历。

  - slice、splice、indexOf……不适用容。可用Array.from()转化为真数组，再调用数组API。

```js

//类数组1， domNodes

{

  let nodes = document.querySelectorAll('div');

  Array.isArray(nodes); //false

}

//类数组2，Arguments

{

  function fn(arguments){

    Array.isArray(arguments); //false

  }

  fn();

}

//类数组3，jqNOdes

{

  Array.isArray($('div')); //false

}

//创建一个类数组，不可使用

{

  let obj = {'1': 'a', '2': 'b', 'k1': 'c'};

  obj.length = 2;

  for(let i = 0; i < obj.length; i++){

    console.log(obj[i]);

  }

  obj.forEach(()=>{}); //报错

  obj.map(()=>{})



  let arr = Array.from(obj);

  console.log(arr); //[undefined, "a"] 不存在项，用undefined补上

}

```

##### **2.4.2 类数组转换**

1. 遍历类数组，逐项push进一个数组中。代码较多，不简洁。且必须要有length属性。

2. Array.prototype.slice.call()。 实用。不够直观。

3. Array.from()。直观，能转化所有可迭代对象

```js

let obj = {length: 3};

//遍历类数组

{

  function makeArray(arrayLike){

    let result = [];

    for(let i = 0; i < arrayLike.length; i++){

      result.push(arrayLike[i])

    }

    return result;

  }



  makeArray(obj); // [undefined, undefined, undefined]

}



//Array.prototype.slice.call()

{

  function makeArray(arrayLike){

    return Array.prototype.slice.call(arrayLike);

  }

  makeArray(obj); //[empty × 3]

}



//Array.from()

{

  function makeArray(arrayLike){

    return Array.from(arrayLike);

  }

  makeArray(obj); //undefined, undefined, undefined]

}

```

#### 2.5 定型数组

（看了一边，用的时候，再过一遍）

1. 专门用于处理数值类型。为JS提供快速的按位运算。

2. 不可修改长度。

3. 适用数组方法。除了修改原数组的方法和concat。因concat会混乱数据类型，不符合定型数组初衷。

4. 类型包括：

  1. Int8Array、Uint8Array、Uint8ClapedArray

  2. Int16Array、Uint16Array

  3. Int32Array、Uint32Array

  4. Float32Array、Float32Array

5. 创建定型数组3中方法：

  1. 数组缓冲区+ 视图缓冲区+set进数据

  2. new Int8Array()

  3. ……用到时，再了解。

tips:

1.  js数字用 64bit == 8 byte 存储一个浮点数；

------

### 3.  对象

#### 3.1 创建对象

1. 对象直接量`{}`

  - 属性名：JavaScript标识符、字符串（包括空字符串）

  - es6扩展

    1. 属性初始值简写：属性名与本地变量名同名时，可只写属性名。

    2. 对象方法简写：省略冒号和function关键字。

    3. 可计算属性名：使用[]表示该属性名称是可计算的。

2. new + 构造函数

  - `new Object()` `new Array()` `new Date()` `new RegExp()`

3. Object.create(*proto*, [*descriptors*]) 以第一个参数为原型，创建新对象

  - proto，新创建对象的原型

    - 可为null，此时会创建一个没有原型的对象，不继承任何东西，也没有基础方法。

    - 创建普通空对象：

      - `{}`

      - `new Object()`

      - `Object.create(Object.prototype)`

  - 第2个参数，属性描述

    - 等同于调用`Object.defineProperties()`

```js

//对象直接量

{

  function createPerson(name, age){

    let suffix = 'name'

    return {

      //属性简写

      age,

      //对象方法简写

      sayName(){

        console.log(this.name)

      },

      //可计算属性名，内容最终会被转化为字符串

      ['full' + suffix]: name

    }

  }

}

//Object.create(proto, descriptors)

{

  //以o为原型创建对象b，创建并描述y属性。

  let o = {x: 1};

  let b = Object.create(o, {

    y: {value: 2, writable: true, enumerable: true, configurable: true}

  });

}

```

#### 3.2 查询和设置属性

1. 属性沿原型链查询，直到找到属性或查到原型为null为止。

2. 给对象创建属性，或给属性赋值，不会修改原型链上的属性。

3. 不能给只读属性重新赋值。

4. 不能通过同名自有属性覆盖只读的继承属性。

```js

let o = {}

Object.defineProperty(o, 'x', {value: 1, writable: false});

o.x = 2; //赋值失败，不能给只读属性重新赋值

console.log(o.x); //1

let b = Object.create(o);

b.x = 2; //赋值失败，不能通过同名自有属性覆盖只读的继承属性

console.log(b.x); //1

Object.defineProperty(b, 'x', {value: 5});

console.log(b.x); //5

console.log(o.x); //1 给对象属性赋值，不会修改原型链上的属性

```

#### 3.3 删除属性

`delete` 运算符可以删除对象属性。

1. 但只是断开属性和属性值的联系，不会去操作属性值。

2. delete 只能删除自由属性，不能删除继承属性。

```js

//只是断开属性和属性值的联系

{

  let obj = {key: {x: 1}};

  let obj2 = obj.key;

  delete obj.key;

  console.log(obj);  //{}

  console.log(obj2); // {x:1}

}

//delete 只能删除自由属性，不能删除继承属性。

{

  let o = Object.create({x:1});

  delete o.x;

  console.log(o.x); //1

}

```

#### 3.4 检测属性

1. `in`  自有属性 + 继承属性

2. `hasOwnProperty()`  自有属性

3. `propertyIsEnumerable()` 自有 && 可枚举属性

```js

let o = {x: 1}

let b = Object.create(o, {y: {value: 2, enumerable: false}});

//in 检测自由属性+继承属性

{

  'x' in b; //true 继承属性

  'y' in b; //true 自有属性

}

//hasOwnProperty 检测自有属性

{

  b.hasOwnProperty('x'); //false 继承属性

  b.hasOwnProperty('y'); //true 自有属性

}

//propertyIsEnumerable 检测自有&可枚举属性

{

  b.propertyIsEnumerable('x'); //false 继承属性

  b.propertyIsEnumerable('y'); //false 自有、不可枚举属性



  o.propertyIsEnumerable('x'); //true x是o的自由可枚举属性

}

```

#### 3.5 枚举属性

1. `for/in` 可枚举 && 自有属性 + 继承属性

2. `Object.keys()` 可枚举 && 自有属性

3. `Object.getOwnPropertyNames()` 自有属性

es6中自有属性的枚举顺序：

1. 数字键按升序排序；

2. 字符串键，按照被加入对象的顺序排序；

3. Symbol键按照他们被加入对象的顺序排序。

4. 字符串键在数字键后。

```js

let o = {x: 1, '1': 'judy'}

let b = Object.create(o, {z: {value: 2, enumerable: false}});

b[Symbol()] = 1;

b['2'] = 'jack';

b['0'] = 'alice';

b.y = 3;

//for/in  可枚举的自有+继承属性

{

  let keyList = []

  for(let key in b){

    keyList.push(key);

  }

  //先自有属性，后继承属性

  console.log(keyList); // ['0', '2', y', '1','x']

}

//Object.keys() 可枚举的自有属性

{

  console.log(Object.keys(b)); //['0', '2', y']

}

//Object.getOwnPropertyNames() 自有属性，包括不可枚举的

{

  //字符串键在数字键后，字符串键按加入对象顺序排序

  console.log(Object.getOwnPropertyNames(b)); //['0', '2', z', 'y']

}

```

#### 3.6 存取器属性（accessor property）

1. getter + setter， 可读写存取器属性

2. 只有getter，只读存取器属性

3. 只有setter，只写存取器属性

```js

var p = {

  //x,y 可读写的数据属性

  x: 2.0,

  y: 2.0,

  //r 可读写属性

  get r(){

    return Math.sqrt(this.x * this.x + this.y * this.y);

  },

  set r(newVal){

    var oldVal = Math.sqrt(this.x * this.x + this.y * this.y);

    var radio = newVal / oldVal;

    this.x *= radio;

    this.y *= radio;

  },

  //theta 只读属性

  get theta(){

    return Math.atan2(this.x, this.y);

  },

  //movement 只写属性

  set movement(dis){

    this.x += dis / this.theta;

    this.y += dis * this.theta;

  }

};

//r为可读写属性，可以读取和修改其值。

console.log(p.r); //2.83

p.r = 5;

console.log(p.x, p.r); //5.36, 5

//p为只读属性，改变其值，无效

console.log(p.theta); //0.79

p.theta = 1;

console.log(p.theta); // //0.79

//movement 为只写属性，读取值永远返回undefined

console.log(p.movement); //undefined

p.movement = 2;

console.log(p.x); //6.08

```

Vue.js 计算属性的getter&setter

```js

var app = new Vue({

  el: '#app',

  data: {

    firstName: 'Machael',

    lastName: 'Jackson'

  },

  computed: {

    fullName: {

      get: function(){

        return this.firstName + this.lastName;

      },

      set: function(newVal){

        var names = newVal.split(' ');

        this.firstName = names[0];

        this.lastName = names[names.length - 1]

      }

    }

  }

})

```

#### 3.7 属性的特性

- 属性描述符

  1. 数据属性：`value`, `writable` , `enumerable`,  `configurable`

  2. 存取器属性：`get`, `set`, `enumerable`, `configurable`

- `Object.getOwnPropertyDescriptor()` 获得对象某自有属性的属性描述符

- `Object.defineProperty()`、`Object.defineProperties()` 创建或修改属性

  1. 不可扩展对象，可以编辑已有的自有属性，但不可增加新属性。

  2. 不可配置属性，不可以修改其`configurable`和`enumerable`。

    1. 不可配置的数据属性，可以将其`writable`从`true`改为`false`，但不可以从`false`改为`true`。

    2. 数据属性的`configurable`和`writable`同时为`false`时，不能修改其值。有一个为true时，可以修改。

  3. 不可配置的存取器属性，不能修改其getter和setter，也不能将其转为数据属性。

  4. 不可配置的数据属性，不能将其转为存取器属性。

```js

let o = {

  x: 1,

  get y(){},

  set y(newV){}

};

//数据属性

{

  Object.getOwnPropertyDescriptor(o, 'x');

  //{value: 1, writable: true, enumerable: true, configurable: true}

}

//存取器属性

{

  Object.getOwnPropertyDescriptor(o, 'y');

  //{get: f, set: f, enumerable: true, configurable: true}

}

//Object.defineProperty()

{

  // 将数据属性，转为访问器属性

  Object.defineProperty(o, 'x', {get: function(){return 2}});

  Object.getOwnPropertyDescriptor(o, 'x');

  //get: ƒ, set: undefined, enumerable: true, configurable: true}

}

//Object.defineProperties()

{

  Object.defineProperties(o, {

    x: {configurable: true},

    y: {value: 2}

  });

  Object.getOwnPropertyDescriptor(o, 'y');

  //{value: 2, writable: false, enumerable: true, configurable: true}

}

```

属性特性的默认值：

```js

//通过Object.defineProperty()给对象新建属性，3个特性值默认为false

{

  Object.defineProperty(o, 'x', {value: 1});

  Object.getOwnPropertyDescriptor(o, 'x');

  //{value: 1, writable: false, enumerable: false, configurable: false}

}

//通过如下方式新建属性，3个特性值默认为true

{

  o.y = 1;

  Object.getOwnPropertyDescriptor(o, 'y');

  //{value: 1, writable: true, enumerable: true, configurable: true}

}

```

#### 3.8 对象的三个属性

1. 原型属性

  1. 获取原型`Object.getPrototypeOf`

  2. 检测原型`isPrototypeOf()`

      1. *object*  instanceof  *constructor*

2. 类属性：`Object.prototype.toString.call()`

3. 可扩展性

  - 检测可扩展性：

    1. `Object.isExtensible()` 可扩展

    2. `Object.isSealed()` 封闭

    3. `Object.isFrozen()` 冻结

  - 将对象转为不可扩展：（不影响原型和继承）

    1. `Object.preventExtensions()`  对象不可扩展

    2. `Object.seal()`  对象不可扩展 && 自有属性不可配置

    3. `Object.freeze()` 对象不可扩展 && 自有属性不可配置 && 自有属性只读（不影响setter）

```js

//原型属性

{

  let o = {x: 1};

  let b = Object.create(o);



  Object.getPrototypeOf(b); //{x: 1}

  o.isPrototypeOf(b); //true

}

//类属性

{

  let d = new Date();

  Object.prototype.toString.call(d); // '[object Date]'

}

//可扩展性

{

  let o = {x: 1};

  let b = Object.create(o);

  b.z = 1;



  Object.preventExtensions(b); 

  Object.getOwnPropertyDescriptor(b, 'z');

  //{value: 1, writable: true, enumerable: true, configurable: true}



  Object.seal(b)

  Object.getOwnPropertyDescriptor(b, 'z');

  //{value: 1, writable: true, enumerable: true, configurable: false}



  Object.freeze(b);

  Object.getOwnPropertyDescriptor(b, 'z');

  //{value: 1, writable: false, enumerable: true, configurable: false}



  b.y = 1;

  console.log(b.y); //undefined



  o.k = 1;

  console.log(b.k); //1

  console.log(Object.isExtensible(b)); // false

  console.log(Object.isSealed(b)); // true

  console.log(Object.isFrozen(b)); // true

  console.log(Object.isExtensible(o)); //true

}

```



------

### Symbol

*前面看的总结，后续再补充。*

##### Symbol创建

1. 直接创建，不通过new关键字

  ```js

  //直接创建

  {

    let firstName = Symbol()

  }

  //接受可选参数，用于描述该Symbol，便于代码阅读。该描述不可用于属性访问。

  {

    let firtstName = Symbol('first name')

  }

  ```

2. 通过[]赋值和取值。

  ```js

  let firtstName = Symbol('first name')

  let person = {}

  person[firstName] = 'Judy'

  person[firstName] // 'Judy'

  person.firstName // undefined

  'firstName' in person // false

  ```

3. 可通过typeof 进行类型检测

  ```js

  let firstName = Symbol()

  typeof firstName // 'symbol'

  ```

4. 类型强制转换

  ```js

  let firstName = Symbol()

  //不能直接与数字或字符串进行拼接等。会报错。

  firstName + '' // 报错。TypeError: Cannot convert a Symbol value to a string

  //等价布尔值 true

  !!firstName // true

  //可调用toString()方法，返回有用信息

  firstName.toString() // "Symbol(first name)"

  ```



**References**：

1. 喜马拉雅-陪你读书（JavaScript WEB前端），沙溢
2. JavaScript 高级程序设计，第3版
3. JavaScript 权威指南，第6版
4. 深入理解ES6，第1版
