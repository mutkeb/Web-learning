# 第一章——走入Javascript世界

## 认识Javascript

`JavaScript`主要由三部分组成：

1. 核心（ECMAScript）
2. 文档对象模型（DOM）
3. 浏览器对象模型（BOM）

### 核心

规定了这门语言的基本组成部分

### 文档对象模型

就是可以获取到我们写的所有的 `html`标签

基于以下几种接口：

* DOM遍历和范围：可以找到页面中所有的标签
* DOM事件：例如给某个图片添加拖动事件，使图片可以随意拖动
* DOM样式：可以更改页面中所有元素的样式，例如更改某一段文字的颜色

### 浏览器对象模型

`BOM`只会处理跟浏览器相关的东西，比如：

* 弹出新窗口功能
* 移动、缩放、关闭浏览器窗口的功能
* 给用户提供显示器分辨率的功能
* 提供浏览器信息

## 在HTML中使用JavaScript

1. 使用 `script`标签嵌入 `JavaScript`

   ```js
   // script标签嵌入JavaScript代码
   <script>
       // JavaScript代码
       let name = "Bob";
       function(){
           console.log("我的名字叫："+name);
       }
   </script>
   ```

2. 注意 `script`标签在 `HTML`文件中的位置

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8" />
       <title>Document</title>
     </head>
     <body>
       <!-- 正常的html标签一定要写在script标签的前面 -->
       <div></div>
       <!-- 在body标签的内部并在末尾 -->
       <script></script>
     </body>
   </html>
   ```

3. `JavaScript`写在 `HTML`外部

   ```js
   <script src='index.js'></script>
   ```

## JavaScript入门

### 单行注释

```js
// 单行注释
```

### 块级注释

```js
/*
 * 注释
 * 注释
 */
```

### 字符串

> 可以是单引号也可以是双引号

### console访问控制台

```js
console.log("要输出的内容");
```

### 模板字符串

在一般的字符串中，如果我们要将字符串和变量拼接起来，要用 `+`去拼接，例如：

```js
let firstName = "胡";
let lastName = "雪岩";

let say = "大家好，我姓" + firstName + "，名" + lastName;

console.log(say);
```

模板字符串的核心是反引号和占位符 `${expression}`，

```js
let firstName = "胡";
let lastName = "雪岩";

let say = `大家好，我姓${firstName}，名${lastName}`;

console.log(say);
```

### 转义符（\）

```js
let str = "华为正式发布操作系统---\"鸿蒙OS\"";
console.log(str);
```

在前后引号前面添加一个转义符

### 多行字符串拼接

```js
let str = `春眠不觉晓
处处闻啼鸟
夜来风雨声
花落知多少`;

console.log(str);
```

此时字符串是带有空格的，不需要用\n

### 在字符串中使用表达式

```js
let number1 = 20;
let number2 = 10;
console.log(
  "两个数的和是：" +
    (number1 + number2) +
    "\n两个数的差是：" +
    (number1 - number2) +
    "。"
);
```

这样的做法太过繁琐

```js
let number1 = 20;
let number2 = 10;
console.log(`两个数的和是：${number1 + number2} 
两个数的差是：${number1 - number2} 。`);
```

### 模板字符串中使用三元表达式

```js
let str = `这里是${false ? "浙江" : "江苏"}`;

console.log(str); // 江苏
```

#### 使用场景一

一个class后面可以跟多个类名

```js
const classes = `header ${
  isLargeScreen() ? "" : `icon-${item.isCollapsed ? "column" : "row"}`
}`;
```

#### 使用场景二

js代码中组装HTML代码

```js
let htmlCode = `
    <img src='' />
    ${
      true
        ? `<img src='https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1906469856,4113625838&fm=26&gp=0.jpg' />`
        : `<img src='' />`
    }
`;
console.log(htmlCode);
// <img src='' />
//    <img src='https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1906469856,4113625838&fm=26&gp=0.jpg' />
```

# 第二章——基础数据类型

## 变量

### 使用let定义变量

```js
let name = "Will Smith";
console.log(name);
```

注意： `let`声明变量的时候，不能重复声明同名变量

```js
let number = 20;
let number = 30;
```

### 使用const定义变量

与let定义变量的方式相同

### let和const异同点

1. `let`定义的变量可以被多此重新赋值

   ```js
   let name = "Bob";
   console.log(name); // Bob
   
   name = "Tom";
   console.log(name); // Tom
   ```

2. const定义的变量只能赋值一次

   ```js
   const name = "Bob";
   console.log(name); // Bob
   
   name = "Tom"; // 报错
   console.log(name); // 不执行
   ```

3. `let`定义变量的时候，可以不赋初值

   ```js
   let age;
   console.log(age); // undefined
   ```

4. `const`定义变量的时候，要赋初始值，否则会报错

   ```js
   const age; // 报错
   console.log(age);  // 不执行
   ```

## 数值类型

### 整数

```js
let number = 8;
console.log(number); // 8
```

* 八进制

  ```js
  let number1 = 010; // 八进制的8
  let number2 = 011; // 八进制的9
  let number3 = 012; // 八进制的10
  ```

* 十进制

  ```js
  let number1 = 7; // 十进制的7
  let number2 = 20; // 十进制的20
  ```

* 十六进制

  ```js
  let number1 = 0x010; // 十六进制的16
  let number2 = 0x11; // 十六进制的17
  let number3 = 0x12; // 十六进制的18
  ```

### 浮点数

浮点数必须包含一个小数点，并且小数点后面至少有一位数字，小数点前面可以没有数字，但是不推荐这种写法

```js
let floatNumber1 = 2.0;
let floatNumber2 = 0.4;
let floatNumber3 = .2; // 正确，但是不推荐
```

极大或极小的数值，用科学计数法 `e`来表示

```js
let bigNumber = 9.43e7; // 等于94300000
```

```js
let smallNumber = 3e-7; // 等于0.0000003
```

### 浮点数的精度丢失现象

浮点数值的最高精度是17位小数

例如，0.1+0.2的结果不是0.3

```js
let number1 = 0.1;
let number2 = 0.2;
console.log(number1 + number2); // 0.30000000000000004
```

所以以后不要用这种条件判断

```js
if (a + b == 0.3) {
  console.log('输出成功');
}
```

### NaN

出现 `NaN`的情况

1. 0/0
2. 字符串乘以数字
3. `NaN`和任何数进行运算

## 类型转换/字符串拼接

### 隐式转换

* 数字字符串加数字，数字隐式转换为字符串

```js
console.log(20 + "20"); // 2020
// 调换位置亦可
console.log("20" + 20); // 2020
```

* 数字字符串与数字做非加法运算，字符串隐式转换为数字

  ```js
  console.log("20" - 10); // 10
  console.log(10 * "10"); // 100
  console.log(10 / "2"); // 5
  ```

* 数字字符串与数字字符串做非加法运算，隐式转换为数字

  ```js
  console.log("20" - "10"); // 10
  console.log("20" / "10"); // 2
  console.log("20" * "10"); // 200
  ```

### 强制类型转换

#### parseInt

* 整数字符串转换为整数

  ```js
  let number = "20";
  //  将number转换为整数类型
  let converNumber = parseInt(number);
  console.log(converNumber); // 20
  // 判断转换后的数据类型
  console.log(typeof converNumber); // number
  ```

* 小数字符串转换为整数

  ```js
  let number = "20.5";
  let converNumber = parseInt(number);
  console.log(converNumber); // 20  不足21一律按照20算
  console.log(typeof converNumber); // number
  ```

* 小数转换为整数

  ```js
  let number = 20.5;
  let converNumber = parseInt(number);
  console.log(converNumber); // 20
  ```

#### parseFloat

```js
let number = "20.9";
let converNumber = parseFloat(number);
console.log(converNumber); // 20.9
console.log(typeof converNumber); // number
```

### 字符串拼接

```js
let age = 20;
let name = "小李子";
let output = "我叫：" + name + ",今年" + age + "岁了";
console.log(output);
```

### 运算符

#### 相等

```js
let number1 = 45;
let number2 = 45;
console.log(number1 == number2); // true
```

#### 全等

```js
let number1 = 45;
let number2 = 45;
console.log(number1 === number2); // true
```

不仅会对值进行判断，还会对类型进行判断

#### 自增/自减

```js
let balloon = 10;
let money = 0;
// 卖出去第一个
balloon--; // 9
money++; // 1

// 卖出去第二个
balloon--; // 8
money++; // 2

// .....
// 以此类推
```

# 第三章——数组

## 认识数组

#### 根据索引修改对应位置的值

```js
let arr = ['张三','李四','王五','Lisa'];
// 修改'王五'为'Tom'
arr[2] = 'Tom';
// 输出数组
console.log(arr);
```

#### 数组索引（下标）可以在新的位置上插入值

```js
let arr = ['张三','李四','王五','Lisa'];

// 修改值
arr[0] = 'Three';
arr[1] = 'Four';
arr[2] = 'Five';
arr[3] = '丽莎';
console.log(arr);

// 添加值
arr[4] = 'Polly';

console.log(arr);
```

## 数组元素操作

### 增

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

// 在末尾添加“河海大学”
schools.push('河海大学');
console.log(schools); // 清华大学','北京大学','浙江大学','同济大学','河海大学'
```

添加多个值

```js
schools.push('河海大学');
schools.push('大连理工大学');
schools.push('哈尔滨工业大学');

// 上述三步操作可以一次性完成
schools.push('河海大学', '大连理工大学', '哈尔滨工业大学');
```

### 删

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

// 在末尾添加“河海大学”
schools.push('河海大学');
console.log(schools); // 清华大学','北京大学','浙江大学','同济大学','河海大学'

// 从末尾删除一个元素
schools.pop();
console.log(schools); // 清华大学','北京大学','浙江大学','同济大学'
```

#### shift（从前往后）

#### splice（删除指定位置的值）

##### 一个参数

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

// 删除从下标为1的位置到结束位置的值
let deleteSchools = schools.splice(1);
// 删除之后，原数组中的剩余内容
console.log(schools); // ["清华大学"]
// 删除的内容
console.log(deleteSchools); // ["北京大学", "浙江大学", "同济大学"]
```

##### 两个参数

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

// 从下标为0开始,往后数两个元素,删除
let deleteSchools = schools.splice(0, 2);
console.log(schools); // ['浙江大学', '同济大学']
console.log(deleteSchools); // ['清华大学', '北京大学']
```

### 改

1. 第一个值表示起始位置
2. 第二个值表示步长
3. 第三个参数，要替换的数组的值

案例一

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

schools.splice(2, 0, '江西理工大学');
console.log(schools); //  ["清华大学", "北京大学", "江西理工大学", "浙江大学", "同济大学"]
```

案例二

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

schools.splice(2, 1, '江西理工大学');
console.log(schools); // ["清华大学", "北京大学", "江西理工大学", "同济大学"]
```

案例三

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

schools.splice(2, 2, '江西理工大学');
console.log(schools); // ["清华大学", "北京大学", "江西理工大学"]
```

### 查

#### 一个参数

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

let result = schools.indexOf('大连理工');
console.log(result); // -1
```

#### 两个参数

第一个参数是要找的值，第二个参数是开始寻找的位置

```js
let schools = ['清华大学', '北京大学', '浙江大学', '同济大学'];

let result = schools.indexOf('浙江大学', 3);
console.log(result); // -1
```

## 二维数组

```js
[
  [2, '第二名', false],
  [2, '第二名', false],
  [2, '第二名', false],
];
```

```js
// 定义一个一维数组
let arr1 = [];
// 给一维数组里面添加数据
arr1[0] = '宇智波佐助';
arr1[1] = '男';
arr1[2] = '下忍';
arr1[3] = 12;
console.log(arr1); // ['宇智波佐助','男','下忍',12]
// 给一维数组里面添加数据
arr2 = [];
arr2[0] = '春野樱';
arr2[1] = '女';
arr2[2] = '下忍';
arr2[3] = 12;
console.log(arr2); // ['春野樱','女','下忍',12]
// 给一维数组里面添加数据
arr3 = [];
arr3[0] = '漩涡鸣人';
arr3[1] = '男';
arr3[2] = '下忍';
arr3[3] = 12;
console.log(arr3); // ['漩涡鸣人','男','下忍',12]
// 将一维数组添加到另一个数组中，形成二维数组
let arr = [];
arr[0] = arr1;
arr[1] = arr2;
arr[2] = arr3;
console.log(arr);
// [
//   ['宇智波佐助', '男', '下忍', 12],
//   ['春野樱', '女', '下忍', 12],
//   ['漩涡鸣人', '男', '下忍', 12],
// ]
```

或者是

```js
let arr = [];
arr.push([]);
arr[0].push('宇智波佐助');
arr[0].push('男');
arr[0].push('下忍');
arr[0].push(12);

arr.push([]);
arr[1].push('春野樱');
arr[1].push('女');
arr[1].push('下忍');
arr[1].push(12);

arr.push([]);
arr[2].push('漩涡鸣人');
arr[2].push('男');
arr[2].push('下忍');
arr[2].push(12);

console.log(arr);
```

# 第五章——循环

for……of循环

```js
let peppaFamily = ["佩奇", "乔治", "猪妈妈", "猪爸爸"];

for (let item of peppaFamily) {
  console.log(item);
}
// 输出:
// 佩奇
// 乔治
// 猪妈妈
// 猪爸爸
```

# 第六章——函数

## 函数概述

### 获取随机数

```js
const num = Math.random() * 1000;
```

[0,1000)

## 自定义函数

![img](https://document.youkeda.com/P3-4-HTML-CSS/6/1.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

用函数表达式声明

![img](https://document.youkeda.com/P3-4-HTML-CSS/6/2.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

也可以用箭头函数简写为

```js
let print = () => {
  console.log("JavaScript 真有趣");
};
```

### 函数声明的提升

```js
f();
function f() {}
```

function只要有，就会出现在最前面，不存在先声明再使用的关系

> 注意，function命令结尾不需要加；，函数表达式是要加的

### 立刻执行函数

当函数只使用一次时，通常使用IIFE

```js
(function() {
  console.log("这个函数只执行一次");
})();
```

会在声明后立即调用函数

## 函数参数

```js
// 在圆括号运算符中加入参数 figure，用来接收外部传入的数据
function code(figure) {
  const num1 = Math.random() * 0.9 + 0.1;
  const num2 = Math.floor(num1 * Math.pow(10, figure));
  
  console.log(num2);
}

// 不要忘记调用函数，调用函数后函数才会执行
code(4);
code(6);
```

默认参数

```js
// 参数 figure(位数) txt(文本) 
function code(figure, txt = "随机数：") {
  const num1 = Math.random() * 0.9 + 0.1;
  const num2 = Math.floor(num1 * Math.pow(10, figure));
  
  console.log(txt, num2);
}

code(6);
```

## 内置函数——计时器1

### 延时执行setTimeout（）

![img](https://document.youkeda.com/P3-4-HTML-CSS/6/3.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

```js
console.log(1);

/**
 * 第一个参数是代码，注意代码需用引号包裹，否则会立即执行代码
 * 第二个参数是 1000，即 1000ms 后执行 console.log(2)
 */
setTimeout('console.log(2)', 1000);

/**
 * 第一个参数是匿名函数
 * 第二个参数是 2000，即 2s 后执行 console.log(3)
 */
setTimeout(function () {
  console.log(3);
}, 2000);

// 第一个参数是函数名，注意函数名后不要加小括号“()”，否则会立即执行 print4
setTimeout(print4, 3000);

console.log(5);

function print4() {
  console.log(4);
}
```

### 回调函数

当（触发条件达成）之后，就会通过函数指针调用函数

## 内置函数——计时器2

![img](https://document.youkeda.com/P3-4-HTML-CSS/6/4.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

区别：setInterval指定某个任务每隔一段时间就执行一次，也就是无限次的定时执行

# 第七章——对象

## 对象概述

![img](https://document.youkeda.com/P3-4-HTML-CSS/7/1.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

大括号来定义一个对象

```js
let person = {
  name: 'henry',
  age: 18,
  run: function() {
    console.log('running');
  }
}

person.run();
```

对象创建

```js
// 第一步：创建构造函数
function People(name, age) {
  this.name = name;
  this.age = age;
}

// 第二步：通过 new 创建对象实例
let person = new People('henry', 18);
console.log(person);
```

## 对象属性操作

### 属性读取

```js
let person = {
  name: 'henry',
  age: 18
}

console.log(person.name);
console.log(person['name']);
```

### 属性赋值

```js
let person = {
  name: 'henry',
  age: 18
}

person.name = 'tom';
person['age'] = 10

console.log(person.name);
console.log(person.age);
```

### 属性查看

```js
let person = {
  name: 'henry',
  age: 18
}

console.log(Object.keys(person));
```

### 属性的删除和增加

```js
let person = {
  name: 'henry',
  age: 18
}

delete person.name;

console.log(person);
```

```js
let person = {
  name: 'henry',
  age: 18
}

person.gender = 'male';
```

## 遍历对象属性

### 用 `for……in`遍历属性

```js
let person = {
  name: 'henry',
  age: 18,
}

for (let key in person) {
  console.log('键名：' + key + '；键值：' + person[key]);
}
```

### 借助 `Object.keys`遍历属性

```js
let person = {
  name: 'henry',
  age: 18,
}

let keys = Object.keys(person);

for (let i = 0; i < keys.length; i++) {
  console.log('键名：' + keys[i] + '；键值：' + person[keys[i]]);
}
```

## 对象的继承

```js
// 字面量
let o1 = {
  name: 'alice',
};

// 构造函数
let o2 = new Object();
let o3 = new Object();

// 继承
funtion O4() {
}; 
O4.prototype = o1; 
let o4 = new O4();
```

### 属性是否存在

```js
let person = {
  name: 'henry',
  age: 18,
};

'name' in person;
'gender' in person;
'toString' in person;
```

### 自身属性是否存在

```js
let person = {
  name: 'henry',
  age: 18,
};

person.hasOwnProperty('name');
person.hasOwnProperty('gender');
person.hasOwnProperty('toString');
```

### Object与JSON、Map的区别

1. JSON=>JavaScript对象

   ```js
   // 一个 JSON 字符串
   const jsonStr =
     '{"sites":[{"name":"Runoob", "url":"www.runoob.com"},{"name":"Google", "url":"www.google.com"},{"name":"Taobao", "url":"www.taobao.com"}]}';
   
   // 转成 JavaScript 对象
   const obj = JSON.parse(jsonStr);
   ```

2. JavaScript对象=>JSON

   ```js
   const jsonStr2 = JSON.stringify(obj)；
   ```

Map和Object的区别

1. Object的键通常是字符串，但一个Map的键可以是任意值
2. Map中的键值是有序的，而添加到对象中的键不是
3. Map中的键值个数可以直接获取，Object需要借助Object.keys（）等计算得到
4. Map可直接进行迭代，Object要借助Object.keys（）
5. Map不存在键名和原型键名冲突问题，可以直接覆盖，Object不行

## 内置对象——Math、Storage

### Math

#### 常量

```js
Math.E // 常数e。
Math.LN2 // 2 的自然对数。
Math.LN10 // 10 的自然对数。
Math.LOG2E // 以 2 为底的e的对数。
Math.LOG10E // 以 10 为底的e的对数。
Math.PI // 常数π。
Math.SQRT1_2 // 0.5 的平方根。
Math.SQRT2 // 2 的平方根。
```

静态方法

```js
Math.abs() // 绝对值
Math.ceil() // 向上取整
Math.floor() // 向下取整
Math.round() // 四舍五入取整
Math.max() // 最大值
Math.min() // 最小值
Math.pow() // 指数运算
Math.sqrt() // 平方根
Math.log() // 自然对数
Math.exp() // e的指数
Math.random() // 随机数
```

### Storage

#### 数据存入

```js
window.localStorage.setItem('myLocalStorage', 'storage Value');
```

![img](https://qgt-document.oss-cn-beijing.aliyuncs.com/P3-4-HTML-CSS/7/4.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

要存入对象的话，最好先转成JSON

```js
const obj = {
  name: 'henry',
  age: 18
}
const value = JSON.stringify(obj);
window.localStorage.setItem('myLocalStorage', value);
```

#### 读取数据

```js
window.localStorage.getItem('myLocalStorage');
```

#### 清除缓存

```js
window.localStorage.clear();
```

## 内置对象——String

### 包装对象

```js
let v2 = new String('abc');
```

### 字符串长度

```js
let len = 'here is an apple'.length;
```

### 查找字符

```js
let str = 'here is an apple';
const index = str.indexOf('an');
console.log(index);
```

### 去掉两端空格

```js
// 'here' 之前有一个空格，'apple' 之后有三个空格
let str = ' here is an apple   ';
const trimedStr = str.trim();
console.log(str.length);
console.log(trimedStr.length);
```

### 截取字符串：substring/substr

```js
let url = 'https://www.youkeda.com/userhome#collect';

// 首先找到 # 后第一个字母的下标
const index = url.indexOf('#') + 1;

// 有 hash 才能进行截取，没有就直接提示不存在
if (index) {
  // 用 substring 截取字符串
  const hash1 = url.substring(index, url.length);

  // 计算 hash 的长度
  const lenHash = url.length - index;
  // 用 substr 截取字符串
  const hash2 = url.substr(index, lenHash);

  console.log(hash1);
  console.log(hash2);
} else {
  console.log('不存在 hash');
}
```

* substring（start，end）
* substr（start，len）

### 分割字符串：split

```js
const splitedStr = 'a|b|c'.split('|');
console.log(splitedStr);
```

## 内置对象——Array

### 连接数组

```js
let arr = [1, 2, 3, 4];

arr.join(" "); // '1 2 3 4'
arr.join(" | "); // "1 | 2 | 3 | 4"
arr.join(); // "1,2,3,4"
```

### 倒序排列

```js
let arr = ["a", "b", "c"];

arr.reverse(); // ["c", "b", "a"]
arr; // ["c", "b", "a"]
```

### 排序

```js
let arr = [
  { name: "jenny", age: 18 },
  { name: "tom", age: 10 },
  { name: "mary", age: 40 },
];

arr.sort(function (a, b) {
  return a.age - b.age;
});

console.log(arr);
```

从小到大

### 遍历：map/forEach

```js
let arr = [
  { name: "jenny", age: 18 },
  { name: "tom", age: 10 },
  { name: "mary", age: 40 },
];

// elem: 数组成员
// index: 成员下标
// a: 整个数组
const handledArr = arr.map(function (elem, index, a) {
  elem.age += 1;
  console.log(elem, index, a);
  return elem.name;
});

console.log(arr);
console.log(handledArr);
```

返回的是一个由elem.name组成的数组

#### 无返回值的遍历

```js
const handledArr = arr.forEach(function (elem, index, a) {
  elem.age += 1;
  console.log(elem, index, a);
  return elem.name;
});

console.log(handledArr);
```

## 内置对象——Date

### 获得当前事件：new Date（）

```js
let now = new Date();
console.log(now);
```

### 日期运算

时间差：毫秒数

```js
let dt1 = new Date(2020, 2, 1);
let dt2 = new Date(2020, 3, 1);

// 求差值
let diff = dt2 - dt1;

// 一天的毫秒数
let ms = 24 * 60 * 60 * 1000;

console.log(diff / ms); // 31
```

早晚比较：大小于符号

### 解析日期字符串

```js
let dt = Date.parse("2020-1-6");
console.log(dt); // 1578240000000
```

返回毫秒数

### 时间对象转时间字符串

```js
let dt = new Date();
let dtStr = dt.toJSON();

console.log(dtStr); // 2020-01-03T09:44:18.220Z
```

### 获取时间对象的年/月/日

```js
let dt = new Date();
dt.getTime(); // 返回实例距离1970年1月1日00:00:00的毫秒数。
dt.getDate(); // 返回实例对象对应每个月的几号（从1开始）。
dt.getDay(); // 返回星期几，星期日为0，星期一为1，以此类推。
dt.getFullYear(); // 返回四位的年份。
dt.getMonth(); // 返回月份（0表示1月，11表示12月）。
dt.getHours(); // 返回小时（0-23）。
dt.getMilliseconds(); // 返回毫秒（0-999）。
dt.getMinutes(); // 返回分钟（0-59）。
dt.getSeconds(); // 返回秒（0-59）。
```

### 设置时间对象的年/月/日

```js
let dt = new Date();
dt.setTime(ms); // 设置实例距离1970年1月1日00:00:00的毫秒数。
dt.setDate(date); // 设置实例对象对应每个月的几号（从1开始）。
dt.setFullYear(year); // 设置四位的年份。
dt.setMonth(month); // 设置月份（0表示1月，11表示12月）。
dt.setHours(hour); // 设置小时（0-23）。
dt.setMilliseconds(ms); // 设置毫秒（0-999）。
dt.setMinutes(min); // 设置分钟（0-59）。
dt.setSeconds(sec); // 设置秒（0-59）。
```

# 第八章——BOM

## BOM

BOM最重要的对象有4个

* window（窗口）
* navigator（浏览器）
* history（历史）
* screen（显示屏幕）
* location（地址）

**特别强调几点**

1. screen是整个电脑唯一的
2. navigator是整个浏览器唯一的，如果有多个浏览器就会有多个navigator
3. window是每个网页唯一的，每个网页都有一个独立的window
4. history，location是每个网页的信息，也是网页唯一的

## window

### 什么是window

![img](https://style.youkeda.com/img/course/f4/8/2.png?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

1. window对象表示一个浏览器窗口或一个frame框架，它处于对象层次的最顶端
2. window对象是浏览器对象中的默认对象，所以可以隐式地引用window对象地属性和方法

```js
console.log('优课达');
window.console.log('优课达');

console.log(navigator);
console.log(window.navigator);

function hello() {}
console.log(hello);
console.log(window.hello);
```

`console.log`等于`window.console.log`

## Location/History

### Location

![image-20220901132854528](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220901132854528.png)

#### 只需要掌握一个方法--**reload（）**

为了放置无限快速循环，设置一个定时器延迟调用reload

```js
setTimeout(function () {
  window.location.reload();
}, 3000);
```

#### 跳转到新的地址

```js
window.location = 'https://www.youkeda.com';
```

### Histroy

允许操作会话历史记录

如果原始网页为 `https://www.youkeda.com`，那么history中存储为

```js
// 会话记录
['https://www.youkeda.com'];
```

有两个方法需要掌握，back（），和forward（），分别对应到浏览器左上角地返回和前进按钮

## Navigator/Screen

### Navigator

表示用户代理地状态和标识，也就是浏览器基本信息，我们需要了解一个属性--**userAgent**，代表当前浏览器的用户代理

```html
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_2) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/79.0.3945.130 Safari/537.36
```

这是userAgent的输出内容

# 第九章——DOM和DOM操作

## 初识DOM

HTML的文档是树状结构

![img](https://style.youkeda.com/img/course/f4/9/1.jpeg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

## 访问DOM

DOCUMENT元素会存在全局变量window下面，我们可以通过如下代码来访问

```js
window.document;
```

![img](https://style.youkeda.com/img/course/f4/9/3.png?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

### 选择器查询方法

```js
document.querySelector('main .core .subtitle');
```

迭代查询

```js
let subtitle = document.querySelector('main .core .subtitle');
console.log(subtitle.querySelector('a'));
```

还可以利用得到的元素继续筛选

选择器全量查询

在上面的技术中只能查询到第一个满足条件的节点

```js
document.querySelectorAll('input');
```

## DOM属性

### DOM类别

1. 元素节点
2. 特性节点
3. 文本节点
4. 其他不重要的类别

```js
let divDom = document.querySelector('div#test');
console.log(divDom.nodeType, divDom.nodeName, divDom.nodeValue);

// 获取DIV节点的第一个儿子节点，代表 '优课达' 这个字符串
let txtDom = divDom.firstChild;
console.log(txtDom.nodeType, txtDom.nodeName, txtDom.nodeValue);

// 获取DIV节点的id属性
let attDom = divDom.attributes.id;
console.log(attDom.nodeType, attDom.nodeName, attDom.nodeValue);
```

对应的结果为

![image-20220901135133285](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220901135133285.png)

1. HTML标签都是元素节点，可以用 `nodeName`获取标签名称
2. 纯文本都是文本节点，可以用 `nodeValue`获取文本内容
3. 标签的每个属性都是特性节点，可以用 `nodeName`取得属性Key，用 `nodeValue`取得属性Value
4.  ``attributes``可以获取元素节点的所有属性，得到的结果是一个字典

### DOM内容

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8" />
  <title>优课达</title>
</head>
<body>
  <div id="test">
    优课达
    <p>youkeda</p>
    <p>学的比别人好一点</p>
  </div>
  <script src="./index.js"></script>
</body>
```

```js
let divDom = document.querySelector('div#test');
console.log(divDom.outerHTML, divDom.innerHTML, divDom.innerText);
```

结果如下

![image-20220901135402769](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220901135402769.png)

### DOM亲属

```js
let divDom = document.querySelector('div#test');
console.log(divDom.firstChild, divDom.lastChild);
console.log('-----');
console.log(divDom.childNodes);
console.log('-----');
console.log(divDom.parentNode);
```

![image-20220901135500813](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220901135500813.png)

### DOM样式

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8" />
  <title>优课达</title>
</head>
<body>
  <h1 class="test youkeda" style="color: #FF3300; font-size: 24px;">优课达</h1>
  <script src="./index.js"></script>
</body>
```

```js
const h1Dom = document.querySelector('h1');
console.log(h1Dom.classList);
console.log(h1Dom.style);
console.log(h1Dom.style.color);
```

## DOM操作（一）

### DOM样式修改

1. 创建标签节点

   ```js
   const div = document.createElement('div');
   ```

   ```js
   const div = document.createElement('div');
   const txt = document.createTextNode('优课达-学的比别人好一点');
   ```

   继续把 `txt`添加到 `div`中，把 `div`添加到 `body`中

   ```js
   const div = document.createElement('div');
   const txt = document.createTextNode('优课达-学的比别人好一点');
   div.appendChild(txt);
   document.body.appendChild(div);
   ```

2. 添加新节点

   ```js
   const root = document.querySelector('ul.root');
   const sars = document.querySelector('li.sars');
   
   // 创建 H1N1
   const H1N1 = document.createElement('li');
   const H1N1Txt = document.createTextNode('H1N1');
   H1N1.appendChild(H1N1Txt);
   root.appendChild(H1N1);
   
   // 创建 新型冠状病毒
   const nCoV = document.createElement('li');
   const nCoVTxt = document.createTextNode('新型冠状病毒');
   nCoV.appendChild(nCoVTxt);
   root.insertBefore(nCoV, sars);
   ```

   利用函数简化

   ```js
   function createDisease(txt) {
     const dom = document.createElement('li');
     const domTxt = document.createTextNode(txt);
     dom.appendChild(domTxt);
     return dom;
   }
   
   const root = document.querySelector('ul.root');
   const sars = document.querySelector('li.sars');
   
   // 创建 H1N1
   const H1N1 = createDisease('H1N1');
   root.appendChild(H1N1);
   
   // 创建 新型冠状病毒
   const nCoV = createDisease('新型冠状病毒');
   root.insertBefore(nCoV, sars);
   ```

3. 设置样式、属性

   ```js
   img.setAttribute('style', 'width: 100%; height: 100%;');
   ```

   单独设置某些属性

   ```js
   dom.style.color = 'xxxx';
   ```

   classList

   ```js
   const div = document.createElement('div');
   div.className = 'foo';
   
   // 初始状态：<div class="foo"></div>
   console.log(div.outerHTML);
   
   // 使用 classList API 移除、添加类值
   div.classList.remove("foo");
   div.classList.add("anotherclass");
   
   // <div class="anotherclass"></div>
   console.log(div.outerHTML);
   
   // 如果 visible 类值已存在，则移除它，否则添加它
   div.classList.toggle("visible");
   
   // add/remove visible, depending on test conditional, i less than 10
   div.classList.toggle("visible", i < 10 );
   
   console.log(div.classList.contains("foo"));
   
   // 添加或移除多个类值
   div.classList.add("foo", "bar", "baz");
   div.classList.remove("foo", "bar", "baz");
   
   // 使用展开语法添加或移除多个类值
   const cls = ["foo", "bar"];
   div.classList.add(...cls);
   div.classList.remove(...cls);
   
   // 将类值 "foo" 替换成 "bar"
   div.classList.replace("foo", "bar");
   ```

4. innerHTML

   ```js
   function createDisease(txt) {
     const dom = document.createElement('li');
   
     // 我们可以直接用innerHTML设置其纯文本
     dom.innerHTML = txt;
     return dom;
   }
   ```

## DOM操作（二）

```js
let data = [
  '<em>肺炎</em>实时疫情动态',
  '<em>肺炎</em>的症状有哪些症状',
  '<em>肺炎</em>武汉',
  '<em>肺炎</em>症状',
  '<em>肺炎</em>最新',
  '<em>肺炎</em>是怎么引起的',
  '<em>肺炎</em>最新消息',
  '<em>肺炎</em>实时',
  '<em>肺炎</em>症状及表现',
  '<em>肺炎</em>最新情况'
];

function createSearchItem(txt) {
  const item = document.createElement('li');
  item.innerHTML = `<i class="search"></i><p>${txt}</p><i class="edit"></i>`;
  return item;
}

const input = document.querySelector('input');

const login = document.querySelector('.login');
const searchResult = document.querySelector('.search-result');

// 监听键盘事件
input.addEventListener('keyup', function() {
  // this 是DOM节点，this.value可以获取input内输入的值
  if (this.value === '肺炎') {
    // 先把原始内容清空
    searchResult.innerHTML = '';
    for (let i = 0; i < data.length; i++) {
      searchResult.appendChild(createSearchItem(data[i]));
    }

    login.style.display = 'none';
    searchResult.style.display = 'block';
  } else {
    login.style.display = 'block';
    searchResult.style.display = 'none';
  }
});
```

通过函数，批量创建li的节点

# 第十章——DOM事件

## DOM事件

### 一、事件嵌套到HTML代码中

```html
<div onclick="console.log('xxx')"></div>
```

### 二、事件方法替换

```js
dom.onclick = function () {};
```

### DOM事件

#### 焦点事件

focus：获取焦点事件

blur：失去焦点事件

#### 鼠标事件

click：点击事件

mousedown：在元素上按下任意鼠标按钮

mouseenter：移到有事件监听的元素内

mouseleave：指针移出元素范围外

mousemove：指定在元素内移动时持续触发

mouseover：指针移到有事监听的元素或者它的子元素内

#### 键盘事件

keydown：键盘按下事件

keyup：键盘释放事件

#### 视图事件

scroll：文档滚动事件

resize：窗口放缩事件

#### 资源

load：资源加载成功的事件

### 实现步骤：

监听DOM事件，使用DOM操作，修改DOM属性

## 冒泡、捕获、委托

### 冒泡

![img](https://style.youkeda.com/img/course/f4/10/7.gif)

点击事件的触发有先后顺序

1. 点击事件触发 `button.like-btn`监听事件
2. 冒泡找到其父亲节点，触发父亲节点的监听事件
3. 一次冒泡到html根元素为止

```js
const likeBtn = document.querySelector('.like-btn');
likeBtn.addEventListener('click', function() {
  console.log('.like-btn');
});

const workspaceOpt = document.querySelector('.workspace-opt');
workspaceOpt.addEventListener('click', function() {
  console.log('.workspace-opt');
});

const workspace = document.querySelector('.workspace');
workspace.addEventListener('click', function() {
  console.log('.workspace');
});

document.body.addEventListener('click', function() {
  console.log('body');
});
```

打印结果如下

```python
".like-btn"
".workspace-opt"
".workspace"
"body"
```

### 阻止冒泡

```js
// ......省略
likeBtn.addEventListener('click', function(e) {
  // 点击事件
  e.stopPropagation()

// ......省略
```

### 捕获

是从根HTML节点开始依次移动到当前元素

```js
dom.addEventListener('click', function() {}, true);
```

> 加入第三个参数 `true`

### 委托

想要在大量子元素中单击任何一个都可以运行一段代码。可以将事件监听器设置在其父节点上

子元素监听器

```js
const box = document.querySelector('.box');
const imgArr = box.children;

for (let i = 0; i < imgArr.length; i++) {
  imgArr[i].addEventListener('click', function() {
    document.body.style.backgroundImage = `url(${imgArr[i].src})`;
  });
}
```

父元素监听器

```js
const box = document.querySelector('.box');

box.addEventListener('click', function(e) {
  // document.body.style.backgroundImage = `url(${imgArr[i].src})`;
});
```

## 表单元素事件

元素内容变化--input和change

```js
const nick = document.querySelector('input.nick');
nick.addEventListener('input', function() {
  console.log('-----input');
  console.log(nick.value);
});

nick.addEventListener('change', function() {
  console.log('-----change');
  console.log(nick.value);
});
```

* input：只要在输入框里面输入值就会触发
* change：要等到输入框失去焦点才会触发

## 滚动事件

### 无尽滚动

当页面快滚动到底部时，添加新的文章内容到 `body`中

```js
window.addEventListener('scroll', function () {
  // 可以通过clientHeight获取内容高度
  const height = document.body.clientHeight;

  // 通过screen.height获取浏览器的高度
  const screenHeight = window.screen.height;

  // 当距离底部的距离小于500时，触发页面新增内容
  if (height - window.scrollY - screenHeight < 500) {
    console.log('加载新文章内容');
    // 在底部添加10张图片
    const div = document.createElement('div');
    let str = '';
    for (let i = 0; i < 10; i++) {
      str += `
       <img
        class="first"
        alt=""
        src="https://document.youkeda.com/P3-1-HTML-CSS/1.8/1.jpg?x-oss-process=image/resize,h_300"
      />
      `;
    }
    div.innerHTML = str;
    document.body.appendChild(div);
  }
});
```

# 第十一章——网络请求

## URL

多个参数之间用 `&`分隔

```
https://www.douban.com/gallery/topic/116390/?from=hot_topic_note&sort=new
```

### 相对路径

不是以 `/`开头的路径

### 默认路径

不需要输入完整的路径也可以打开网页

## API+GET请求

API：应用程序接口

### 作用：可以快速调用某个程序

### fetch调用API

利用JS获取网站数据

```js
fetch(
  'https://www.fastmock.site/mock/b73a1b9229212a9a3749e046b1e70285/f4/f4-11-1-1'
)
  .then(function (response) {
    return response.json();
  })
  .then(function (myJson) {
    console.log(myJson);
  });
```

fetch的返回结果是一个 `Promise`对象

#### Promise

是异步编程的一种解决方案

### Get请求

`fetch`默认发起 `GET请求`

## POST请求

### fetch - POST操作

```js
fetch(
  'https://www.fastmock.site/mock/b73a1b9229212a9a3749e046b1e70285/f4/f4-11-4-1',
  {
    method: 'POST'
  }
)
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

可以通过 `body`属性完成 `POST`请求的传参，此时 `headers`里面加入 `contentType信息`。

## Get和Post使用起来最大的区别

最直观的区别就是GET把参数包含在URL中，POST通过request body传递参数。

## Chrome Network如何查看网络请求

1. 打开Network面板
2. 刷新页面

![img](https://style.youkeda.com/img/course/f4/11/2.png?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)
