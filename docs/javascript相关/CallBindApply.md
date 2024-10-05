## call、apply、bind

call、apply、bind 作用是改变函数执行时的上下文，简而言之就是改变函数运行时的 this 指向

```js
var name = 'lucy'
var obj = {
  name: 'martin',
  say: function () {
    console.log(this.name)
  }
}
obj.say() // martin，this 指向 obj 对象
setTimeout(obj.say, 0) // lucy，this 指向 window 对象
```

## call 用法

call 方法调用一个函数，并使用指定的 this 值和单独给出的参数来调用该函数。

> call 方法调用一个函数，并使用指定的 this 值和单独给出的参数来调用该函数。

```js
/**
 * Function.prototype.call方法调用一个函数，指定函数内部的this值和传入的参数。
 * 这个方法立即执行函数，并且你可以显式地设置this的值和传递参数。
 * 它的第一个参数是this值，其余参数则是你希望传递给函数的参数列表。

 * @param {Object} context - 调用函数时作为this的值。
 * @param {...any} args - 传递给函数的参数列表，可以是零个或多个。
 * 
 * @returns {*} 函数调用的结果。
 * 
 * @example
 * function sum(a, b) {
 *   return a + b;
 * }
 * 
 * const obj = { value: 0 };
 * sum.call(obj, 1, 2); // 将obj作为sum函数的this，传入参数1和2，返回3。
 */
```

> 具体用法

```js
/***
 * @param thisArg 参数一传入this指向
 * @param argArray  参数二传入参数 1,2,3 可选
 * @returns {*} 直接返回函数执行结果(调用函数)
 */

var obj = {
  name: 'martin',
  say: function () {
    console.log(this, 'this')
    console.log(this.name)
  }
}

var obj1 = { name: 'lucy' }

obj.say() // 输出martin this指向是obj对象
obj.say.call(obj1) // 输出lucy 改变this指向到obj1对象
```

> call 传参

```js
function Product(name, price) {
  this.name = name
  this.price = price
}
// call 传参方式 ...args[]
function Food(name, price) {
  Product.call(this, name, price)
  this.category = 'food'
}

function Ship(...args) {
  Product.call(this, ...args)
  this.category = 'Ship'
}

function Car() {
  //  Array.prototype.slice.call
  // Array.prototype.slice.call(arguments)能将具有length属性的对象(key值为数字)转成数组。[]是Array的示例，所以可以直接使用[].slice（）方法。
  const args = Array.prototype.slice.call(arguments)
  //或者 const args = [].slice.call(arguments)
  Product.call(this, ...args)
  this.category = 'Car'
}

console.log(new Food('cheese', 5))
console.log(new Food('和谐号', 50000))
console.log(new Car('宝马', 50000, 50000))
```

## bind 用法

bind 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

> bind 方法传参讲解

```js
/**
 * 使用Function.prototype.bind方法预设函数的上下文（this）和部分参数。
 * bind方法会创建一个新的函数，在这个新函数被调用时，
 * 传入的this值将作为它运行时的this值，且传入的参数会被预先填入新函数的参数列表。
 * 剩余的参数在新函数被调用时传入。
 *
 * @param {Object} context - 预设的this值，即新函数内部的this指向。
 * @param {...any} args - 预先填入新函数参数列表的参数，可以是零个或多个。
 * @returns {Function} 返回一个绑定过this值和部分参数的新函数。
 *
 * @example
 * function greet(greeting, name) {
 *   console.log(`${greeting}, ${name}!`);
 * }
 *
 * const boundGreet = greet.bind(null, 'Hello'); // 预设greeting为'Hello'
 * boundGreet('John'); // 输出: Hello, John!
 */
```

> 具体用法

```js
const obj = {
  name: 'João',
  lastName: 'Silva',
  fullName() {
    return this.name + ' ' + this.lastName
  }
}

const obj2 = {
  name: 'Maria',
  lastName: 'Souza'
}
// 返回一个新的函数
console.log(obj.fullName.bind(obj2)())
```

> bind 传参

```js
/**
 * 创建一个产品类，用于表示具有名称和价格的产品。
 * @constructor
 * @param {string} name - 产品的名称。
 * @param {number} price - 产品的价格。
 */
function Product(name, price) {
  this.name = name
  this.price = price
}

/**
 * 创建一个食品类，继承自产品类。
 * 食品类添加了一个category属性，用于表示食品的类别。
 * @constructor
 * @param {string} name - 食品的名称。
 * @param {number} price - 食品的价格。
 */
function Food(name, price) {
  Product.bind(this, name, price)()
  this.category = 'food'
}

/**
 * 创建一个船舶类，继承自产品类。
 * 船舶类添加了一个category属性，用于表示船舶的类别。
 * 使用了剩余参数语法，以适应不同数量的参数。
 * @constructor
 * @param {...any} args - 传递给产品类构造函数的参数。
 */
function Ship(...args) {
  Product.bind(this, ...args)()
  this.category = 'Ship'
}

// 创建一个食品实例并打印
console.log(new Food('cheese', 5))

// 创建一个船舶实例并打印
console.log(new Ship('和谐号', 50000))
```

## apply 用法

通常情况下，在调用函数时，函数内部的 this 值是访问该函数的。使用时 apply()，您可以调用现有函数 this，而无需先将函数作为属性附加到对象上。因此，您可以使用一个对象来用作通用的实用函数。

**_备注：这个函数 call()几乎是完全相同的，只是函数参数在 call()逐个作为列表传递，而在 apply()它们中会组合到一个对象中，通常是一个数组——例如，func.call(this, "eat", "bananas")与 func.apply(this, ["eat", "bananas"])。_**

> apply 方法传参讲解

```js
/**
 * Function.prototype.apply方法调用一个函数，同样允许显式地设置函数内部的this值，
 * 但与call方法不同的是，apply方法接受一个数组或类数组对象作为函数的参数列表。
 * 这使得apply方法在参数数量不确定或需要传递数组时特别有用。

 * @param {Object} context - 调用函数时作为this的值。
 * @param {Array|Array-like object} argsArray - 一个数组或者类数组对象，其元素将作为单独的参数传递给调用的函数。
 * 
 * @returns {*} 函数调用的结果。
 * 
 * @example
 * function sum(a, b) {
 *   return a + b;
 * }
 * 
 * const obj = { value: 0 };
 * sum.apply(obj, [1, 2]); // 将obj作为sum函数的this，通过数组[1, 2]传递参数，返回3。
 */
```

> 具体用法

```js
const obj = {
  name: 'João',
  lastName: 'Silva',
  fullName() {
    return this.name + ' ' + this.lastName
  }
}

const obj2 = {
  name: 'Maria',
  lastName: 'Souza'
}

console.log(obj.fullName.apply(obj2))
```

> apply 传参

```js
/**
 * 产品基类，用于定义所有产品共有的属性和方法。
 * @constructor
 * @param {string} name - 产品的名称。
 * @param {number} price - 产品的价格。
 */
function Product(name, price) {
  this.name = name
  this.price = price
}

/**
 * 食品类，继承自Product，添加了类别属性。
 * @constructor
 * @param {string} name - 产品的名称。
 * @param {number} price - 产品的价格。
 */
function Food(name, price) {
  Product.apply(this, [name, price])
  this.category = 'food'
}

/**
 * 船类，继承自Product，添加了类别属性。
 * 使用剩余参数语法来接收任意数量的参数。
 * @constructor
 */
function Ship(...args) {
  Product.apply(this, args)
  this.category = 'Ship'
}

/**
 * 车类，继承自Product，添加了类别属性。
 * 通过转换arguments对象为数组，以支持传入任意数量的参数。
 * @constructor
 */
function Car() {
  const args = Array.prototype.slice.call(arguments)
  Product.apply(this, args)
  this.category = 'Ship'
}

// 创建并打印一个Food实例
console.log(new Food('cheese', 5))

// 创建并打印一个Ship实例
console.log(new Ship('和谐号', 50000))

// 创建并打印一个Car实例
console.log(new Car('宝马', 4554545))
```
