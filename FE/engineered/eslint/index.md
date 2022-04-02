# 一. 安装eslint

```js
pnpm install eslint -D
```



- 初始化

```js
npx eslint --init
```



```js
// 需安装的包
eslint-plugin-vue@latest 
@typescript-eslint/eslint-plugin@latest 
@typescript-eslint/parser@latest
```

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/工程化/eslint/images/WX20220305-145543@2x.png" alt="WX20220305-145543@2x" style="zoom:80%;" />



- package.json中增加配置：

```js
"scripts": {
  	// 增加修补语法命令
    "lint": "eslint --fix src/",
    "dev": "vite",
    "build": "vue-tsc --noEmit && vite build",
    "preview": "vite preview"
},
```

```js
pnpm run lint
```

