# 一. 基础



## 1. 初始化文件

```js
npm init -y
```



## 2. 安装nuxt.js

```js
npm i nuxt
```



## 3. 修改配置文件

```json
{
  "name": "nuxt-test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    // nuxt中已内置nuxt命令
    "dev": "nuxt"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "nuxt": "^2.15.8"
  }
}

```



## 4. 创建目录

创建pages文件夹，此文件夹为路由规则

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/服务端渲染/images/WX20220301-221800@2x.png" alt="WX20220301-221800@2x" style="zoom:50%;" />



执行命令

```js
npm run dev
```

此时项目启动。



## 5. 使用git管理代码

新建.gitignore文件排除非上传文件

```js
node_modules
.next
```



```js
// 初始化git仓库
git init

// 查看状态
git status

git add .
git commit -m ""
git branch new_branch
```



## 6. 路由

### 6.1 基本路由

假设 `pages` 的目录结构如下：

```bash
pages/
--| user/
-----| index.vue
-----| one.vue
--| index.vue
```

那么，Nuxt.js 自动生成的路由配置如下：

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'user',
      path: '/user',
      component: 'pages/user/index.vue'
    },
    {
      name: 'user-one',
      path: '/user/one',
      component: 'pages/user/one.vue'
    }
  ]
}
```

### 6.2 路由导航

```js
// a链接
<a herf="/">首页</a>

// 导航链接组件
<router-link to="/">首页</router-link>

// 编程式导航
this.$router.push('/')
```



### 6.3 动态路由

在 Nuxt.js 里面定义带参数的动态路由，需要创建对应的**以下划线作为前缀**的 Vue 文件 或 目录。

以下目录结构：

```bash
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

Nuxt.js 生成对应的路由配置表为：

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```

你会发现名称为 `users-id` 的路由路径带有 `:id?` 参数，表示该路由是可选的。如果你想将它设置为必选的路由，需要在 `users/_id` 目录内创建一个 `index.vue` 文件。



### 6.4 嵌套路由

创建内嵌子路由，你需要添加一个 Vue 文件，同时添加一个**与该文件同名**的目录用来存放子视图组件。

**Warning:** 别忘了在父组件(`.vue`文件) 内增加 `<nuxt-child/>` 用于显示子视图内容。

假设文件结构如：

```bash
pages/
--| users/
-----| _id.vue
-----| index.vue
--| users.vue
```

Nuxt.js 自动生成的路由配置如下：

```js
router: {
  routes: [
    {
      path: '/users',
      component: 'pages/users.vue',
      children: [
        {
          path: '',
          component: 'pages/users/index.vue',
          name: 'users'
        },
        {
          path: ':id',
          component: 'pages/users/_id.vue',
          name: 'users-id'
        }
      ]
    }
  ]
}
```



users.vue（父路由中）

```html
<div>
	<h1>users 父路由</h1>  
	<!-- 子路由出口 -->
	<nuxt-child />
</div>
```

### 6.5 自定义路由

新建==next.config.js==

```js
module.export = {
  router: {
    base: '/base',
    // routes:一个数组，路由配置表
    // resolve:解析路由组件路径
    
    // 将hello路由与about路由指向同一个文件
    extendRoutes(routes, resolve) {
      routes.push({
        path: '/hello',
        name: 'hello',
        component: resolve(__dirname, 'pages/about.vue')
      })
    }
  }
}
```





## 7. 布局

通过在layouts文件夹下新建default.vue来进行公共布局。

```vue
<template>
	<div>
    layouts/default文件
    <!-- 此处必须为nuxt组件 -->
    <nuxt />
  </div>
</template>

<script>
	export default {
    name: "layoutDefault",
  }
</script>
```

- 指定默认布局文件

```js
// app/index.vue文件中
//指定默认布局文件（此处只写文件名即可），默认为default
layout: "foo"
```



## 8.  异步数据

### asyncData

![WX20220307-220946@2x](/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/服务端渲染/images/WX20220307-220946@2x.png)



```vue
<template>
	<div>
    <h1>{{ title }}</h1>
    <next-link to="/about">about</next-link>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HomePage',
  // 服务端与客户端会进行两次渲染
  // 服务端与客户端都无法获取this
  console.log(this) // undefined
  async asyncData() {
    const res = await axios({
      methods: 'GET',
      url: 'http://localhost:3000/data.json'
    })
    return res.data
  },
  data() {
    return {
      title: 'keyi'
    }
  }
}
</script>
```

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/服务端渲染/images/WX20220307-220035@2x.png" alt="WX20220307-220035@2x" style="zoom:80%;" />



asyncData方法返回的异步数据会与data中的数据进行合并。

如果想要使页面内容有利于seo或者提升首屏渲染速度，就在asyncData中拿取数据。

如果是普通数据或者非异步数据则，正常初始化到data中即可。



### 上下文对象

```js
async asyncData(context){}

// asyncData接收的参数为上下文对象，可以获取页面传递等参数
```

