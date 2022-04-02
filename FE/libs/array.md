libs_array

### 一. 实现Array.isArray

```JavaScript
if (!Array.isArray){
  Array.isArray = function(arg){
    return Object.prototype.toString.call(arg) === '[object Array]';
  };
}
```

### 二. 将类数组转换为数组

1. #### 借用数组的方法进行转换

```JavaScript
// 1. slice
Array.prototype.slice.call(arguments)

// 2. concat
[].concat.apply([], arguments)
```

1. #### es6的方式转换

```JavaScript
// 1. ...扩展运算符
[...arguments]

// 2. Array.from()
Array.from(arguments)
```



### 三. 判断是否为数组



```JavaScript
var a = [];
// 1.基于instanceof
a instanceof Array;

// 2.基于constructor
a.constructor === Array;

// 3.基于Object.prototype.isPrototypeOf
Array.prototype.isPrototypeOf(a);

// 4.基于getPrototypeOf
Object.getPrototypeOf(a) === Array.prototype;

// 5.基于Object.prototype.toString
Object.prototype.toString.call(a) === '[object Array]';

// 6. 通过Array.isArray
Array.isArray(a)
```



### 四. 数组方法实现

1. #### forEach

```JavaScript
 Array.prototype.myForEach = function(fn, context = window){
    let len = this.length
    for(let i = 0; i < len; i++){
        typeof fn === 'function' && fn.call(context, this[i], i)
    }
}
```



1. #### filter

```JavaScript
Array.prototype.myFilter = function(fn, context = window){
    let len = this.length,
        result = []
    for(let i = 0; i < len; i++){
        if(fn.call(context, this[i], i, this)){
            result.push(this[i])
        }
    }
    return result
}
```

1. #### every

```JavaScript
Array.prototype.myEvery = function(fn, context){
    let result = true,
        len = this.length
    for(let i = 0; i < len; i++){
        result = fn.call(context, this[i], i, this)
        if(!result){
            break
        }
    }
    return result
}
```

1. #### some

```JavaScript
Array.prototype.mySome = function(fn, context){

    let result = false,

        len = this.length

    for(let i = 0; i < len; i++){

        result = fn.call(context, this[i], i, this)

        if(result){

            break

        }

    }

    return result

}
```

1. #### findIndex

```JavaScript
Array.prototype.myFindIndex = function (callback) {

    for (let i = 0; i < this.length; i++) {

        if (callback(this[i], i)) {

            return i

        }

    }

}
```

1. Reduce

```JavaScript
Array.prototype.myReduce = function (fn, initialValue) {

    let arr = Array.prototype.call(this)

    let res, startIndex

    res = initialValue ? initialValue : arr[0]

    startIndex = initialValue ? 0 : 1

    for (let i = startIndex; i < arr.length; i++) {

        res = fn.call(null, res, arr[i], i, this)

    }

    return res

}
```



### 三. 实现数组扁平化

```JavaScript
let ary = [1, [2, [3, 4, 5]]]
```

1. #### 普通递归+concat

```JavaScript
const flatten = function(ary){

    let result = []



    for(let i = 0;i<ary.length;i++){

        if(Array.isArray(ary[i])){

            result = result.concat(flatten(ary[i]))

        } else {

            result.push(ary[i])

        }

    }

    return result

}
```

1. #### reduce+concat

```JavaScript
const flatten = function(ary){

    return ary.reduce((prev, next)=>{

        return prev.concat(Array.isArray(next) ? flatten(next) : next)

    }, [])

}
```

1. #### while+concat

```JavaScript
const flatten = function(ary){

    while(ary.some(item=>Array.isArray(item))){

        ary = [].concat(...ary)

    }

    return ary

}
```

1. #### toString+split

```JavaScript
const flatten = function(ary){

    return ary.toString().split(',')

}
```

1. #### flat

```JavaScript
const flatten = function(ary){

    return ary.flat(Infinity)

}
```

1. #### 正则

```JavaScript
const flatten6 = function(ary){

    let str = JSON.stringify(ary)

    str = str.replace(/(\[|\])/g, '')

    return JSON.parse(`[${str}]`)

}
```



### 四. 去重

1. 利用 `ES6` 语法（扩展运算符）

```JavaScript
const unique1 = (array) => {

  // return Array.from(new Set(array))

  return [...new Set(array)]

}
```



1. 利用 `forEach()` + 对象容器

```JavaScript
const unique2 = (array) => {

  const arr = []

  const obj = {}

  array.forEach(item => {

    if (!obj.hasOwnProperty(item)) {

      obj[item] = true

      arr.push(item)

    }

  })

  return arr

}
```



1. 利用 `forEach` 和 `indexOf`

```JavaScript
const unique3 = (array) => {

  const arr = []

  array.forEach(item => {

    if (arr.indexOf(item) === -1) {

      arr.push(item)

    }

  })

  return arr

}
```



1. 利用 `filter`  **+**  `indexOf`

```JavaScript
const unique4 = (array) => {

  return array.filter((item,index) => {

    return array.indexOf(item) === index;

  })

}
```



1. 利用 `forEach` 和 `includes` (本质同3)

```JavaScript
const unique6 = (array) => {

  let result = [];

  array.forEach(item => {

    if(!result.includes(item)){

      result.push(item);

    }

  })

  return result;

 }
```



1. 利用 `sort` 

```JavaScript
const unique6 = (array) => {
  let result = array.sort(function (a,b) {
    return a - b;
  });
  for(let i = 0;i < result.length;i ++){
    if(result[i] === result[i+1]){
      result.splice(i + 1,1);
      i --;
    }
  }
  return result;

}
```



1. 双层for循环

```JavaScript
function unique(array) {

    // res用来存储结果
    var res = [];
    for (var i = 0, arrayLen = array.length; i < arrayLen; i++) {
        for (var j = 0, resLen = res.length; j < resLen; j++ ) {
            if (array[i] === res[j]) {
                break;
            }
        }
        // 如果array[i]是唯一的，那么执行完循环，j等于resLen
        if (j === resLen) {
            res.push(array[i])
        }
    }
    return res;

}

console.log(unique(array)); // [1, "1"]
```









### 五. 排序

1. #### 冒泡排序

原理：利用数组的前一项与相邻的后一项相比较，判断大/小，交换位置

```JavaScript
const bubbleSort = function(ary){
    for(let i = 0; i < ary.length - 1; i++){
        for(let j = 0; j < ary.length - 1 - i; j++){
            if(ary[j] > ary[j+1]){
                [ary[j], ary[j+1]] = [ary[j+1], ary[j]]
            }
        }
    }

    return ary

}
```



1. #### 选择排序

原理：利用数组的某项与后面所有的值相比较，判断大/小，交换位置

```JavaScript
const bubbleSort = function(ary){
    for(let i = 0; i < ary.length - 1; i++){
        for(let j = i + 1; j < ary.length; j++){
            if(ary[i] > ary[j]){
                [ary[i], ary[j]] = [ary[j], ary[i]]
            }
        }
    }
    return ary

}
```

1. #### 原生排序

```JavaScript
Array.sort((a, b)=>a-b)
```



1. #### 快速排序

原理：取数组的中间值作为基准，判断左右两边的值大或小，添加到相应数组，递归调用，然后将所有的值拼接在一起。

```JavaScript
const quick_sort = function(ary){
    if(ary.length < 1){
        return ary
    }

    let centerIndex = Math.floor(ary.length/2)
    let centerVal = ary.splice(centerIndex, 1)[0]

    let left = []
    let right = []
    ary.forEach(item => {
        item > centerVal ? right.push(item) : left.push(item)
    })

    return [...quick_sort(left), ...[centerVal], ...quick_sort(right)]

}
```

1. #### 插入排序

原理：先将数组中的一项添加到新数组中，循环数组每一项与新数组中比较，比较大的值放在后面小的放到新数组的前面。

```JavaScript
 const insertion_sort = function(ary){
    let newAry = ary.splice(0, 1)
    for(let i = 0; i < ary.length; i++){
        let cur = ary[i]
        for(let j = newAry.length - 1; j >= 0;){
            if(cur < newAry[j]){
                j--
                j === -1 && newAry.unshift(cur)
            } else {
                newAry.splice(j + 1, 0, cur)
                j = -1
            }
        }
    }
    return [...newAry]
}
```



### 六. 最大值与最小值

1. #### 假设法

```JavaScript
const maxMin = function(ary){
    let [min, max] = [ary[0], ary[1]]
    ary.forEach(ele => {
        min > ele ? min = ele : null
        max < ele ? max = ele : null
    })
    return [min, max]
}
```

1. #### math.max() + 假设法

```JavaScript
var arr = [6, 4, 1, 8, 2, 11, 23];
var result = arr[0];
for (var i = 1; i < arr.length; i++) {
    result =  Math.max(result, arr[i]);
}
console.log(result);
```

1. #### reduce

```JavaScript
var arr = [6, 4, 1, 8, 2, 11, 23];
function max(prev, next) {
    return Math.max(prev, next);
}

console.log(arr.reduce(max));
```

1. #### 排序

```JavaScript
var arr = [6, 4, 1, 8, 2, 11, 23];
arr.sort(function(a,b){return a - b;});
console.log(arr[arr.length - 1])
```

1. #### 利用Math.max

```JavaScript
Math.max.apply(null, ary)

// 扩展运算符
Math.max(...arr)

// eval
var max = eval("Math.max(" + arr + ")");
```

### 七. 平均值

```JavaScript
const balance = function(ary){
    ary.sort((a, b) => a - b)
    ary.shift()
    ary.pop()
    let num = 0
    ary.forEach(item => {
        num += item
    })
    return (num/ary.length).toFixed(2)
}
```









### 八. 数组乱序



```JavaScript
function shuffle(a) {

    var j, x, i;

    for (i = a.length; i; i--) {

        j = Math.floor(Math.random() * i);

        x = a[i - 1];

        a[i - 1] = a[j];

        a[j] = x;

    }

    return a;

}
```





### 九. 将数组扁平化并去除其中重复数据，最终得到一个升序且不重复的数

```JavaScript
let arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];



Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})
```