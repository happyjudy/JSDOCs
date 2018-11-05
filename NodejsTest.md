## Nodejs测试

### 1. Mocha

- js 测试框架，能运行在Node.js 和浏览器中，支持BDD和TDD测试。

- 灵活(不包括断言和仿真，自己选对应工具)，允许使用其他断言库。

- 社区成熟用的人多，测试各种东西社区都有示例

  （*本文采用Node内置的[assert断言](http://nodejs.cn/api/assert.html#assert_assert_deepequal_actual_expected_message)*，可自行了解其他断言库）

#### 1.1 安装

```shell
# node环境下
npm install -g mocha
```

#### 1.2 脚本编写

-  测试集(test suite) ： `describe(name, function(){})`  

-  测试用例(test case )：`it(name, function(){})` 

  - 测试的最小单位

-  专用测试： only

  - 允许只测试指定测试集或案例

-  跳过测试： skip

  - 允许跳过指定测试集或案例
-  钩子函数：`before`, `after`, `beforeEach`, `afterEach`

```js
var assert = require('assert');

//测试集&测试案例
//only 专用测试
describe.only('Array-indexOf', function(){
  it('值不存在时，应返回-1', function(){
    assert.equal(-1, [1,2,3].indexOf(0));
  });
});

//测试集可嵌套使用，测试集包含测试集
//skip 跳过测试
//钩子函数
describe('#String', function(){
  before(function(){
    //测试集前
    //执行一次
  });
  beforeEach(function(){
    //每个测试用例前
    //有几个测试用例，执行几次
  });
  afterEach(function(){
    //单个测试用例后
    //有几个测试用例，执行几次
  });
  after(function(){
    //测试集后
    //执行一次
  });
  
  describe('#indexOf', function(){
    it('值存在时，不应返回-1', function(){
      assert.notEqual(-1, '123'.indexOf('1'));
    });
    it.skip('值不存在时，应返回-1', function(){
    assert.equal(-1, '123'.indexOf('0'));
  });
  });
});
```

#### 1.3 Mocha基本使用

##### 13.1 测试执行

```shell
# 执行指定文件
mocha 3.test.js

# 执行默认test目录下测试文件,test子目录下不包括
mocha

#包括test子目录文件
mocha --recursive

#执行指定多个文件
mocha test/{1, 2}.test.js

#shell通配符*
mocha test/*

##也可以使用node通配符
mocha 'test/**/*.@(js|jsx)'
```

##### 13.2 其他命令行参数

1. `--help`或`-h`参数，用来查看Mocha的所有命令行参数。
2. `--reporter`参数用来指定测试报告的格式，默认是`spec`格式。
3. `--reporters`参数可以显示所有内置的报告格式。
4. [`--growl`](http://growl.info/)参数，就会将测试结果在桌面显示。
5. `--watch`参数用来监视指定的测试脚本。只要测试脚本有变化，就会自动运行Mocha。
6. `--bail`参数指定只要有一个测试用例没有通过，就停止执行后面的测试用例。这对[持续集成](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)很有用。
7. `--grep`参数用于搜索测试用例的名称（即`it`块的第一个参数），然后只执行匹配的测试用例。
8. `--invert`参数表示只运行不符合条件的测试脚本，必须与`--grep`参数配合使用。

```shell
mocha add.test.js --reporter spec
#等同于
mocha add.test.js

#查看tap格式报告
mocha add.test.js --reporter tap

#测试名称中包含"indexOf的测试用例
mocha --grep "indexOf"
#测试名称中不包含"indexOf的测试用例
mocha --grep "indexOf" --invert
```

------

### 2. Node.js assert断言

#### 2.1 assert.ok()

`assert.ok(value[, message])`

- value 为`Truthy`时，测试通过

- value为`Falsy`时， 抛出错误信息

- message

  - 无值时或为undefined，抛出默认AssertionError

  - 有值时，AssertionError传入message值

```js
var assert = require('assert');

assert.ok(true); // pass
assert.ok(1); // pass

assert.ok(false); // AssertionError: !!false == true
```

#### 2.2 assert()

`assert.ok()`的别名。

```js
assert(true); //pass

assert(false); //AssertionError: !!false == true
```

#### 2.3 assert.equal()

`assert.equal(actual, expected[, message])`

- `actual == expected` 时，测试通过

```js
assert.equal(1, 1); //pass
assert.equal(1, '1'); //pass

assert.equal(1, 0); //AssertionError: 1 == 0
```

#### 2.4 assert.notEqual()

`assert.notEqual(actual, expected[, message])`

- `actual != expected` 时，测试通过

```js
assert.notEqual(1, -1); //pass

assert.notEqual(1, '1'); //AssertionError: 1 != '1'
```

#### 2.5 assert.strictEqual()

`assert.strictEqual(actual, expected[, message])`

- `actual === expected` 时，测试通过

```js
assert.strictEqual(1, 1); // pass

assert.strictEqual(1, '1'); //AssertionError: 1 === '1'
```

#### 2.6 assert.notStrictEqual()

`assert.notStrictEqual(actual, expected[, message])`

- `actual !== expected`时，测试通过

```js
assert.notStrictEqual(1, '1'); //pass

assert.notStrictEqual(1, 1); //AssertionError: 1 !== 1
```

#### 2.7 assert.deepEqual()

`assert.deepEqual(actual, expected[, message])`

- `actual deepEqual expected`时，测试通过

```js
assert.deepEqual({a: 1}, {a: 1}); //pass
assert.deepEqual({a: 1}, {a: '1'}); //pass

assert.deepEqual({a: function(){}}, {a: function(){}}); //AssertionError: { a: [Function: a] } deepEqual { a: [Function: a] }
assert.equal({a: 1}, {a: 1}); //AssertionError: { a: 1 } == { a: 1 }
```

#### 2.8 assert.notDeepEqual()

`assert.notDeepEqual(actual, expected[, message])`

- `actual notDeepEqual expected`时，测试通过

assert.notDeepEqual(actual, expected[, message])

```js
assert.notDeepEqual({a: function(){}}, {a: function(){}}); // pass

assert.notDeepEqual({a: 1}, {a: '1'}); //AssertionError: { a: 1 } notDeepEqual { a: '1' }
```

#### 2.9 assert.deepStrictEqual()

`assert.deepEqual(actual, expected[, message])`

- `actual deepStrictEqual expected`时，测试通过

```js
assert.deepStrictEqual({a: 1}, {a: 1}); //pass

assert.deepStrictEqual({a: 1}, {a: '1'}); //AssertionError: { a: 1 } deepStrictEqual { a: '1' }
```

#### 2.10 assert.notDeepStrictEqual()

`assert.notDeepEqual(actual, expected[, message])`

- `actual deepStrictEqual expected`时，测试通过

```js
assert.notDeepStrictEqual({a: '1'}, {a: 1}); //pass
assert.notDeepStrictEqual({a: function(){}}, {a: function(){}}); //pass

assert.notDeepStrictEqual({a: 1}, {a: 1}); //AssertionError: { a: 1 } notDeepStrictEqual { a: 1 }
```

#### 2.11 assert.throws()

`assert.throws(fn[, error][, message])`

- fn 抛出一个错误，或者为一个错误
- error 可捕获错误并对错误进行验证。

*assert.throws跟踪错误，所有测试框架都优化过。不要直接用try catch。*

```js
var err = function(){xx};

assert.throws(fn, Error); //pass
assert.throws(fn, ReferenceError); //pass

assert.throws(fn, TypeError); //抛出错误

//pass
assert.throws(fn, function(err){
  return err instanceof ReferenceError;
});

//抛出错误
assert.throws(fn, function(err){
  return err instanceof TypeError;
});

```

#### 2.12 assert.fail()

assert.fail([message])

- 抛出AssertionError错误信息

```js
assert.fail('fail'); //AssertionError: fail

assert.fail(new TypeError('fail')); //AssertionError: TypeError: fail
```

------

### 3. Mocha辅助工具

#### 3.1 istanbul

1. 代码覆盖

  - 行覆盖率(line coverage)

  - 函数执行率(function coverage)

  - 分支覆盖率(brance coverage)

  - 语句覆盖率(statement coverage)

2. 安装 ：`npm install -g istanbul`

3. 运行： `istanbul cover add.js`，

  - 可在控制台查看结果

  - 同时会生成一个coverage目录，`coverage.json`包含覆盖率的原始数据，`coverage/lcov-report`可以打开覆盖率报告。

#### 3.2 SuperTest

1. 安装： `npm install supertest --save-dev`

2. 使用：【没看明白，用的时候再补充】

------

### 4. 测试模式

#### 4.1 TDD

*test-driven development* 测试驱动开发。

- 原理：开发功能代码之前，先编写单元测试用例代码，测试代码确定需要写什么产品代码。

- 基本思想：通过测试推动开发。量化需求分析、设计、质量控制。


#### 4.2 BDD

*behavior-driven development* 行为驱动开发。

- 鼓励软件项目中的开发者、QA和非技术人员或商业参与者之间协作。

- 通过编写行为和规格说明来驱动软件开发。

- 倡导用简洁的自然语言描述系统行为。


#### 4.3 TDD vs BDD

如例子，二者测试用例最大区别在于措辞，BDD读起来更像是一个句子。

目前大多数基于Nodejs的库或应用都选择了BDD。

```js
var assert = require('assert');

function add(x, y){
  return x + y;
}

//TDD
describe('add', function(){
  it('equals 2 when x =1, y=1', function(){
    assert.equal(2, add(x,y));
  });
});

//BDD
describe('add', function(){
  it('1 plus 1 should be 2', function(){
    assert.equal(2, add(x,y));
  });
});
```

------

### 5.  断言库

1. `should.js`：BDD风格
2. `expect.js`：  支持expect()风格
3. `chai`：支持expect(), assert(), should 断言
4. Node.js集成了`assert`断言

![image-20181104161236571](../../Library/Application Support/typora-user-images/image-20181104161236571.png)

------

### 6. 其他node.js测试框架

1. [Jasmine](http://jasmine.github.io/)：
  - BDD测试框架。

  - 不依赖于其他js框架。

  - 不需要DOM。
2. [Karma](http://karma-runner.github.io/)：
  - 模拟生产环境。
  - 可以同时在多浏览器测试代码。
  - …
3. [Jest](https://github.com/facebook/jest)：
    - 基于Jasmine改进。
    - facebook坐庄。
    - 较多用于React项目。
    - 支持断言和仿真。
4. [Tape](https://github.com/substack/tape/)：
    - 体积最小，只提供最关键的东西
    - 对比其他框架，只提供最底层的 API
5. [AVA](https://github.com/avajs/ava)：
    - 异步，性能好
    - 简约，清晰
    - 快照测试和断言需要三方支持

### 7. 测试驱动开发意义

强制要求开发时：

1. 单元尽量解耦，否则单元不可测。
2. 开发前事先设计接口，再实现细节。
3. 便于回归和内部代码重构。



**Tips：**

- 测试框架&断言库的选取，要结合应用场景。选取性价比最高，最适合需求的。
- 是否需要进行测试，取决于代码变动频率和公用程度。
- 业务代码写测试，性价比不高。公用库，比较适合测试驱动开发。
- 案例：
    - [ant-design](https://github.com/ant-design/ant-design)



### References：

1. [Nodejs测试：从0到90（理论篇）](https://yq.aliyun.com/articles/57804),tralight, 云栖社区
2. [测试框架 Mocha 实例教程](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html), *阮一峰*
3. [前端测试框架对比(js单元测试框架对比)](https://www.cnblogs.com/lihuanqing/p/8533552.html), 晴天_雨天