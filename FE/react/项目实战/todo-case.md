# 一. 环境搭建



## 1. 创建项目

```js
pnpm create vite
```

进入文件夹

安装依赖：

```js
pnpm install
```





# 二. 清单应用

## 1. 清单应用

- 原始版本

```jsx

import React, { useState, useEffect } from "react";

const App = function() {
  let [ value, setValue ] = useState('')
  let [ listData, setList ] = useState([
    {text: '吃饭',done: true},
    {text: '睡觉',done: false},
    {text: '打胖子',done: false},
  ])  

  let [ allDone, setAllDoe ] = useState(false)
  // 添加项
  let addItem = function() {
    setList([...listData, {
      text: value,
      done: false
    }])
    setValue('')
  }

  // 清除已完成
  let deleteItem = function() {
    setList(listData.filter(item=>!item.done))
  }

  // 点击完成某一项
  let handleChange = function(e, index) {
    let newList = [...listData]
    newList[index].done = e.target.checked
    setList(newList)
  }

  // 点击全选
  let handleToggleAll = function(e) {
    let newList = [...listData]
    newList.forEach(item=>item.done = e.target.checked)
    setList(newList)
    setAllDoe(e.target.checked)
  }

  // 删除某一项
  let handleRemote = function(index) {
    let newList = [...listData]
    newList.splice(index, 1)
    setList(newList)
  }

  let activeNum = listData.filter(item=>item.done).length

  // 计算属性，同vue computed， 第二个参数为监听某个数据，发生变动进行触发函数
  useEffect(()=>{
    setAllDoe(activeNum === listData.length)
  }, [listData])

  return <div>
    <input type="text" value={ value } onChange={e=>setValue(e.target.value)} />
    <button onClick={addItem}>添加</button>
    <button onClick={deleteItem}>清除</button>
    <ul>
      {
      	// 三元运算符，条件判断语句
        listData.length ?
        listData.map((item, index)=>{
          return <li>
            <input type="checkbox" checked={ item.done } onChange={ e=>handleChange(e, index) } />
            { item.text } 
            <span onClick={()=>handleRemote(index)}>❌</span>
          </li>
        })
        :
        <div>
          小老弟，啥也没有啊
        </div>
      }
    </ul>
    <div>
      <span>全选</span>
      <input type="checkbox" checked={ allDone } onChange={ e => handleToggleAll(e) } />
      {activeNum}/{listData.length}
    </div>
  </div>
}

export default  App
```



- ts版本

```jsx
import React, { useState, useEffect } from "react";

// react提供的FC对象
const App:React.FC = function() {
  // 定义Todo泛型对象
  interface Todo {
    text: string,
    done: boolean
  }
  // 类型别名，react提供的类型
  type inoutEvent = React.ChangeEvent<HTMLInputElement>


  let [ value, setValue ] = useState<string>('')
  let [ listData, setList ] = useState<Todo[]>([
    {text: '吃饭',done: true},
    {text: '睡觉',done: false},
    {text: '打胖子',done: false},
  ])  

  let [ allDone, setAllDoe ] = useState(false)
  let addItem = function() {
    setList([...listData, {
      text: value,
      done: false
    }])
    setValue('')
  }

  let deleteItem = function() {
    setList(listData.filter(item=>!item.done))
  }

  // vue等可直接使用e: Event,为ts提供
  // 但是react中的event是自定义之后的，所以使用react提供的类型检查器
  let handleChange = function(e: inoutEvent, index: number) {
    let newList = [...listData]
    newList[index].done = e.target.checked
    setList(newList)
  }

  let handleToggleAll = function(e: inoutEvent) {
    let newList = [...listData]
    newList.forEach(item=>item.done = e.target.checked)
    setList(newList)
    setAllDoe(e.target.checked)
  }

  let handleRemote = function(index: number) {
    let newList = [...listData]
    newList.splice(index, 1)
    setList(newList)
  }

  let activeNum = listData.filter(item=>item.done).length

  useEffect(()=>{
    setAllDoe(activeNum === listData.length)
  }, [listData])

  return <div>
    <input type="text" value={ value } onChange={e=>setValue(e.target.value)} />
    <button onClick={addItem}>添加</button>
    <button onClick={deleteItem}>清除</button>
    <ul>
      {
        listData.length ?
        listData.map((item, index)=>{
          return <li>
            <input type="checkbox" checked={ item.done } onChange={ e=>handleChange(e, index) } />
            { item.text } 
            <span onClick={()=>handleRemote(index)}>❌</span>
          </li>
        })
        :
        <div>
          小老弟，啥也没有啊
        </div>
      }
    </ul>
    <div>
      <span>全选</span>
      <input type="checkbox" checked={ allDone } onChange={ e => handleToggleAll(e) } />
      {activeNum}/{listData.length}
    </div>
  </div>
}

export default  App
```



## 2. 安装eslint

```js
pnpm install eslint -d
```



```js
//初始化
npx eslint --init
```

其他配置同vue。

Package.json配置：

```json
"scripts": {
  "lint": "eslint --fix src"
}
```

执行后会自动检测错误：

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/React/项目实战/images/WX20220306-101651@2x.png" alt="WX20220306-101651@2x" style="zoom:80%;" />



## 3. husky

项目规范强制校验。

eslint：代码规范

husky：控制git规范，代码在commit之前必须要执行一遍eslint

Git：管理代码

```js
// 安装
pnpm install husky -d

// git仓库初始化
git init

// 执行初始化
npx husky install

// 在commit之前进行校验(执行npm run lint命令)
npx husky add .husky/pre-commit "npm run lint"

// 随后执行上传git操作
git add .
git commit -m "first upload"
// 如果有eslint语法错误，则会提示错误，停止commit
```

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/React/项目实战/images/WX20220306-103146@2x.png" alt="WX20220306-103146@2x" style="zoom:80%;" />



## 4. git规范

新建==scripts/verifyCommit.js==

在npm中执行

```js
// 在commit中进行校验
npx husky add .husky/commit-msg "node scripts/verifyCommit.js"

// 查看上次提交log
cat .git/COMMIT_EDITMSG
```

==verifyCommit.js==中：

```js
const fs =require('fs')

const msg = fs.readFileSync('.git/COMMIT_EDITMSG','utf-8').trim()
const mergeRe = /^(Merge pull request|Merge branch)/
const commitRE =
  /^(revert: )?(feat|fix|docs|dx|refactor|perf|test|workflow|build|ci|chore|types|wip|release|deps)(\(.+\))?: .{1,50}/

if(!commitRE.test(msg)){
    if(!mergeRe.test(msg)){
        console.log('git commit信息格式有问题')
        console.error(`需要使用以下格式 type(module): message
具体逻辑请看scripts/verifyCommit.js
        `)
      	// 退出程序
        process.exit(1)
    }
}else{
    console.log('git commit校验通过')
}
  
// feat(hooks): message
// feat: message
// revert: feat:message //撤回
```

然后进行提交commit时，会首先校验eslint语法，通过后在校验commit的log是否符合规范，通过后才可以提交成功。

```js
feat：新功能
fix：改bug
docs：文档
perf：性能
test：测试
style：样式
ci：打包集成相关
build：部署
wip：写了一半没完成
release：发版
deps：依赖更改
refactor：代码重写，不影响功能
chore：不知道是啥，小修改 <--- 就写这个
```

