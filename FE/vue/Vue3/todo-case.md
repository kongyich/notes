# 一. vue2版本代码

app.vue

```vue
<template>
  <div>
    <h1 @click="add()">{{count}}</h1>
  </div>
</template>

<script lang="ts">
  export default {
    data() {
      return {
        count: 2
      }
    },

    methods: {
      add() {
        this.count++
      }
    }
  }
</script>
<style>
</style>
```

`this`无法使用ts的**类型推导** ，因为`this` 可以是任何类型。



# 二. vue3语法



## 1. refs语法

```vue
<template>
  <div>
    <h1 @click="add">{{count}}</h1>
  </div>
</template>

<script lang="ts">
import { ref } from 'vue'
export default{
  setup() {
    let count = ref(2)
    function add() {
      count.value++
    }
    return {
      count,
      add
    }
  }
}
</script>
<style>
</style>
```





## 2. 清单应用



- 基础结构

```vue
<template>
  <div>
    <input type="text" v-model="val">
    <ul>
        <li v-for="item in todos">{{item}}</li>
    </ul>

  </div>
</template>

// 将setup加入script来简写代码形式
<script lang="ts" setup>
import { ref } from 'vue'

let val = ref('')
let todos = ref(['吃饭', '睡觉', '玩豆豆'])
</script>
<style>
</style>
```



- 添加点击按钮增加列表项

```vue
<template>
  <div>
    <input type="text" v-model="val">
    <button @click="addTodo">添加</button>
    <ul>
        <li v-for="item in todos">{{item}}</li>
    </ul>

  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

let val = ref('')
let todos = ref(['吃饭', '睡觉', '玩豆豆'])

function addTodo() {
  	// ref副作用，需要通过value访问数据
    todos.value.push(val.value)
    val.value = ''
}
</script>
<style>
</style>
```



- 引入ts，增加==checkbox==可选功能

```vue
<template>
  <div>
    <input type="text" v-model="val">
    <button @click="addTodo">添加</button>
    <ul>
        <li v-for="item in todos">
            <input type="checkbox" v-model="item.done">
            <span>{{item.title}}</span>
        </li>
    </ul>

  </div>
</template>

<script lang="ts" setup>
import { ref, Ref } from 'vue'

// 定义interface
interface Todo {
    title: string,
    done: boolean
}

// 第一种方案
// let val = ref<string>('')

// 第二种方案，使用内置的Ref
let val:Ref = ref('')
let todos = ref<Todo[]>([
        {title: '吃饭', done: true},
        {title: '睡觉', done: false},
        {title: '打自己', done: false},
    ])

function addTodo() {
    todos.value.push({
        title: val.value,
        done: false
    })
    val.value = ''
}

// 1. 自己定义的变量类型
// 2. 宿主环境（浏览器，node）环境下的变量类型，比如：window
// 3. 第三方框架的类型，可能是1和2的组合
// 4. 泛型等类型的推导，熟练使用工具函数omit，pick，parial
</script>
<style>
</style>
```



- 增加选中变色功能

```vue
<template>
  <ul>
    <li v-for="item in todos">
      <input type="checkbox" v-model="item.done">
        <span :class="{done: item.done}">{{item.title}}</span>
    </li>
  </ul>
</template>

<style>
span.done {
    text-decoration: line-through;
    color:gray
}
</style>
```



- 增加全选绑定功能

```vue
<template>
  <div>
    <input type="text" v-model="val">
    <button @click="addTodo">添加</button>
    <ul>
        <li v-for="item in todos">
            <input type="checkbox" v-model="item.done">
            <span :class="{done: item.done}">{{item.title}}</span>
        </li>
    </ul>

    <div>
        <span>全选</span>
        <input type="checkbox" v-model="allDone">
        <span>{{active}}/{{all}}</span>
    </div>

  </div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue'

interface Todo {
    title: string,
    done: boolean
}

let val = ref<string>('')
let todos = ref<Todo[]>([
        {title: '吃饭', done: true},
        {title: '睡觉', done: false},
        {title: '打自己', done: false},
    ])

function addTodo() {
    todos.value.push({
        title: val.value,
        done: false
    })
    val.value = ''
}

// 计算属性 computed
let all = computed<number>(()=>todos.value.length)
let active = computed<number>(()=>todos.value.filter(v=>v.done).length)
// computed两种写法
let allDone = computed<boolean>({
    get() {
        return active.value === todos.value.length
    },
    set(value: boolean) {
        todos.value.forEach(todo=>{
            todo.done = value
        })
    }
})
</script>
<style>
span.done {
    text-decoration: line-through;
    color:gray
}
</style>
```



- 增加删除与清除功能（完整代码）

```vue
<template>
  <div>
    <input type="text" v-model="val">
    <button @click="addTodo">添加</button>
    <button @click="clearTodo">清理</button>
    <ul v-if="todos.length">
        <li v-for="(item, index) in todos">
            <input type="checkbox" v-model="item.done">
            <span :class="{done: item.done}">{{item.title}}</span>
            <span @click="removeTodo(index)">❌</span>
        </li>
    </ul>
    <div v-else>
        暂无数据
    </div>

    <div>
        <span>全选</span>
        <input type="checkbox" v-model="allDone">
        <span>{{active}}/{{all}}</span>
    </div>

  </div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue'

interface Todo {
    title: string,
    done: boolean
}

let val = ref<string>('')
let todos = ref<Todo[]>([
        {title: '吃饭', done: true},
        {title: '睡觉', done: false},
        {title: '打自己', done: false},
    ])

function addTodo() {
    todos.value.push({
        title: val.value,
        done: false
    })
    val.value = ''
}

// 计算属性 computed
let all = computed<number>(()=>todos.value.length)
let active = computed<number>(()=>todos.value.filter(v=>v.done).length)
let allDone = computed<boolean>({
    get() {
        return active.value === todos.value.length
    },
    set(value: boolean) {
        todos.value.forEach(todo=>{
            todo.done = value
        })
    }
})

// 删除
function removeTodo(index: number) {
    todos.value.splice(index, 1)
}

// 清理
function clearTodo() {
    todos.value = todos.value.filter(v=>!v.done)
}
</script>
<style>
span.done {
    text-decoration: line-through;
    color:gray
}
</style>
```













