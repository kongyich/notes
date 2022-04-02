### 一. 判断是否为promise

```JavaScript
const isPromise = function(fn){
    return typeof fn.then === 'function'
}
```



### 二. 实现call函数

```JavaScript
Function.prototype.myCall = function(context,...args) {
    context =  (context ?? window) || new Object(context)
    context.fn = this
    const result = context.fn(args)
    delete context.fn
    return result
}
```

### 三. 实现apply函数

```JavaScript
Function.prototype.myApply = function(context, arr) {
    context =  (context ?? window) || new Object(context)
    // const args = arguments[1]
    context.fn = this
    const result = arr ? context.fn(...arr) : context.fn()
    delete context.fn
    return result
}
```

#### call 的使用场景

**1、对象的继承**。如下面这个例子：

```JavaScript
function superClass () {
    this.a = 1;
    this.print = function () {
        console.log(this.a);
    }
}

function subClass () {
    superClass.call(this);
    this.print();
}



subClass();// 1
```



subClass 通过 call 方法，继承了 superClass 的 print 方法和 a 变量。此外，subClass 还可以扩展自己的其他方法。

**2、借用方法**。还记得刚才的类数组么？如果它想使用 Array 原型链上的方法，可以这样：

```JavaScript
let domNodes = Array.prototype.slice.call(document.getElementsByTagName("*"));
```



这样，domNodes 就可以应用 Array 下的所有方法了。

#### apply 的一些妙用

**1、Math.max**。用它来获取数组中最大的一项。

```JavaScript
let max = Math.max.apply(null, array);
```



同理，要获取数组中最小的一项，可以这样：

```JavaScript
let min = Math.min.apply(null, array);
```



**2、实现两个数组合并**。在 ES6 的扩展运算符出现之前，我们可以用 Array.prototype.push来实现。

```JavaScript
let arr1 = [1, 2, 3];let arr2 = [4, 5, 6];

Array.prototype.push.apply(arr1, arr2);console.log(arr1); // [1, 2, 3, 4, 5, 6]
```







### 四. 实现bind函数

> bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )

```JavaScript
Function.prototype.myBind = function (context, ...outerArgs) {  // this->func context->obj outerArgs->[10,20]
  let self = this
  // 返回一个函数
  return function F(...innerArgs) { //返回了一个函数，...innerArgs为实际调用时传入的参数
    // 考虑new的方式
    if(self instanceof F) {      
        return new self(...outerArgs, ...innerArgs)    
    }    // 把func执行，并且改变this即可

    return self.apply(context, [...outerArgs, ...innerArgs]) //返回改变了this的函数，参数合并

  }}
```







### 五. new关键字

1. #### 实现逻辑

要创建 Person 函数的新实例，必须使用 new 操作符。以这种方式调用构造函数实际上会经历以下 几个步骤：

1. 创建一个新对象（例如p1）

2. 将新对象的__proto__属性指向函数的prototype（即p1.__proto__ === Person.prototype ）

3. 将函数的this指向新创建的对象

4. 返回新对象

​    4.1. 构造函数没有显式返回值，返回this

​    4.2. 构造函数有显式返回值，且是基本类型，比如number，string，那么还是返回this

​    4.3. 构造函数有显式返回值，且是对象类型，比如{a:1}，那么返回这个对象



1. #### 具体实现

```JavaScript
function Person(name,age){
    this.name = name;
    this.age = age;    
    this.sayName = function(){
        console.log(this.name)    
    }
}



//方法一

function createNew(){
    const obj = new Object();    
    const fnconstructor = [].shift.call(arguments);    
    obj.__proto__ = fnconstructor.prototype;        
    const resultObj = fnconstructor.apply(obj,arguments);    
    return typeof resultObj === 'object'? resultObj : obj
}



//方法二

const createNew = (Con, ...args) => {    
    const obj = {}    
    Object.setPrototypeOf(obj, Con.prototype)    
    let result = Con.apply(obj, args)    
    return result instanceof Object ? result : obj
}



const per1 = createNew(Person,'xiaokong',18)

console.log(per1) //{ name: 'xiaokong', age: 18, sayName: [Function] }
```



### 六. 防抖函数

防抖的原理就是：你尽管触发事件，但是我一定在事件触发 n 秒后才执行，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行，总之，就是要等你触发完事件 n 秒内不再触发事件，我才执行。



### 七. 节流函数

降低触发的频率，如果持续触发事件，每隔一段时间，只执行一次事件。





### 八. each函数（jq）

jQuery 的 each 方法，作为一个通用遍历方法，可用于遍历对象和数组。

语法为：

```JavaScript
jQuery.each(object, [callback])
```

回调函数拥有两个参数：第一个为对象的成员或数组的索引，第二个为对应变量或内容。

```JavaScript
// 遍历数组
$.each( [0,1,2], function(i, n){
    console.log( "Item #" + i + ": " + n );
});

// Item #0: 0
// Item #1: 1
// Item #2: 2
// 遍历对象

$.each({ name: "John", lang: "JS" }, function(i, n) {
    console.log("Name: " + i + ", Value: " + n);
});

// Name: name, Value: John

// Name: lang, Value: JS
```



```JavaScript
function each(obj, callback) {
    let len, i = 0
    if (Array.isArray(obj)) {
        len = obj.length
        for (; i < len; i++) {
            if (callback.call(obj[i], i, obj[i]) === false) {
                break
            }
        }
    } else {
        for (i in obj) {
            if(callback.call(obj[i], i, obj[i]) === false) {
                break
            }
        }
    }

    return obj

}
```