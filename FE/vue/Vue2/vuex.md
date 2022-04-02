





# 一. 基本用法





# 二. 案例



## 1. 购物车

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/Vue/Vue2/images/WX20220222-221538@2x.png" alt="WX20220222-221538@2x" style="zoom:50%" />





- components/pop-cart.vue

```vue
<template>
  <el-popover
    width="350"
    trigger="hover"
  >
    <el-table :data="cartProducts" size="mini">
      <el-table-column property="title" width="130" label="商品"></el-table-column>
      <el-table-column property="price" label="价格"></el-table-column>
      <el-table-column property="count" width="50" label="数量"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="scope">
          <el-button size="mini" @click="deleteItem(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div>
      <p>共 {{ getProdCount }} 件商品 共计¥{{getProdPrice}}</p>
      <el-button size="mini" type="danger" @click="$router.push({ name: 'cart' })">去购物车</el-button>
    </div>
    <el-badge :value="getProdCount" class="item" slot="reference">
      <el-button type="primary">我的购物车</el-button>
    </el-badge>
  </el-popover>
</template>

<script>
import { mapGetters, mapMutations, mapState } from 'vuex'

export default {
  name: 'PopCart',
  data () {
    return {
      // cartProducts: [
      //   { id: 1, title: 'iPad Pro', price: 500.01, isChecked: false, count: 0 },
      //   { id: 2, title: 'H&M T-Shirt White', price: 10.99, isChecked: false, count: 0 },
      //   { id: 3, title: 'Charli XCX - Sucker CD', price: 19.99, isChecked: false, count: 0 }
      // ]
    }
  },
  
  // 引入vuex数据源
  computed: {
    ...mapState('cart', ['cartProducts']),
    // 第一个参数为所属仓库名，第二个参数为具体字段
    ...mapGetters('cart', ['getProdCount', 'getProdPrice'])
  },
  methods: {
    ...mapMutations('cart', ['deleteItem'])
  }
}
</script>

<style>

</style>

```



- views/cart.vue

```vue

<template>
  <div>
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>购物车</el-breadcrumb-item>
    </el-breadcrumb>
    <el-table :data="cartProducts" style="width: 100%">
      <el-table-column width="55">
        <template v-slot:header>
          <el-checkbox v-model="checkedAll" size="mini"> </el-checkbox>
        </template>
        <!--
          @change="updateProductChecked"  默认参数：更新后的值
          @change="updateProductChecked(productId, $event)"  123, 原来那个默认参数
            当你传递了自定义参数的时候，还想得到原来那个默认参数，就手动传递一个 $event
         -->
        <template v-slot="scope">
          <el-checkbox size="mini" @change="updateCheckedItem({
            prodId: scope.row.id,
            checked: $event
          })" :value="scope.row.isChecked"> </el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="title" label="商品"> </el-table-column>
      <el-table-column prop="price" label="单价"> </el-table-column>
      <el-table-column prop="count" label="数量">
        <template v-slot="scope">
          <el-input-number :value="scope.row.count" @change="updateProduct({
            prodId: scope.row.id,
            count: $event
          })" size="mini"></el-input-number>
        </template>
      </el-table-column>
      <el-table-column prop="totalPrice" label="小计"> </el-table-column>
      <el-table-column label="操作">
        <template>
          <el-button size="mini">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div>
      <p>已选 <span>{{checkedCount}}</span> 件商品，总价：<span>{{checkedPrice}}</span></p>
      <el-button type="danger">结算</el-button>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapMutations, mapState } from 'vuex'

export default {
  name: 'Cart',
  data () {
    return {
      // cartProducts: [
      //   { id: 1, title: 'iPad Pro', price: 500.01 },
      //   { id: 2, title: 'H&M T-Shirt White', price: 10.99 },
      //   { id: 3, title: 'Charli XCX - Sucker CD', price: 19.99 }
      // ]
    }
  },
  computed: {
    ...mapState('cart', ['cartProducts']),
    ...mapGetters('cart', ['checkedCount', 'checkedPrice']),
    checkedAll: {
      get () {
        return this.cartProducts.every(prod => prod.isChecked)
      },
      set (value) {
        this.updateCheckedAll(value)
      }
    }
  },
  methods: {
    ...mapMutations('cart', ['updateCheckedAll', 'updateCheckedItem', 'updateProduct'])
  }
}
</script>

<style></style>

```



- views/products.vue

```vue
<template>
  <div>
    <el-breadcrumb separator="/">
      <el-breadcrumb-item><a href="#/">首页</a></el-breadcrumb-item>
      <el-breadcrumb-item><a href="#/">商品列表</a></el-breadcrumb-item>
    </el-breadcrumb>
    <el-table :data="products" style="width: 100%">
      <el-table-column prop="title" label="商品"> </el-table-column>
      <el-table-column prop="price" label="价格"> </el-table-column>
      <el-table-column prop="address" label="操作">
        <template v-slot="scope">
          <el-button @click="addToCart(scope.row)">加入购物车</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { mapActions, mapMutations, mapState } from 'vuex'
export default {
  name: 'ProductList',
  computed: {
    // 参数以为命名空间，参数二为数据
    ...mapState('products', ['products'])
  },
  data () {
    return {
      // products: [
      //   { id: 1, title: 'iPad Pro', price: 500.01 },
      //   { id: 2, title: 'H&M T-Shirt White', price: 10.99 },
      //   { id: 3, title: 'Charli XCX - Sucker CD', price: 19.99 }
      // ]
    }
  },
  created () {
    this.getProducts()
  },
  methods: {
    ...mapActions('products', ['getProducts']),
    ...mapMutations('cart', ['addToCart'])
  }
}
</script>

<style></style>

```



- store/index.js

vuex中的插件系统：

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/Vue/Vue2/images/WX20220222-214836@2x.png" alt="WX20220222-214836@2x" style="zoom:60%;" />

<img src="/Users/kongyi/Library/Mobile Documents/com~apple~CloudDocs/myPages/FE/Vue/Vue2/images/WX20220222-214904@2x.png" alt="WX20220222-214904@2x" style="zoom:50%;" />

```js
import Vue from 'vue'
import Vuex from 'vuex'
import products from './modules/products'
import cart from './modules/cart'

Vue.use(Vuex)

// vuex拦截器，每次触发mutation时执行
const myPlugins = store => {
  store.subscribe((mutation, state) => {
    if (mutation.type.startsWith('cart/')) {
      window.localStorage.setItem(
        'cart-products',
        JSON.stringify(state.cart.cartProducts)
      )
    }
  })
}

export default new Vuex.Store({
  state: {},
  mutations: {},
  actions: {},
  // modules集成模块
  modules: {
    products,
    cart
  },
  // 注册插件
  plugins: [myPlugins]
})

```

- store/modules/cart.js

```js
const state = {
  cartProducts: JSON.parse(window.localStorage.getItem('cart-products')) || []
}
const getters = {
  getProdCount (state) {
    return state.cartProducts.reduce((sum, next) => (sum += next.count), 0)
  },
  getProdPrice (state) {
    return state.cartProducts.reduce((pre, next) => (pre += next.totalPrice), 0)
  },
  checkedCount (state) {
    return state.cartProducts.reduce((sum, next) => {
      if (next.isChecked) {
        sum += next.count
      }
      return sum
    }, 0)
  },
  checkedPrice (state) {
    return state.cartProducts.reduce((pre, next) => {
      if (next.isChecked) {
        pre += next.totalPrice
      }
      return pre
    }, 0)
  }
}
const mutations = {
  addToCart (state, product) {
    // 1. cartProducts没有该商品，把该商品添加到数组，并增加count，isChecked，totalPrice
    // 2. cartProducts有该商品，让该商品的数量加1，选中，计算小计
    const prod = state.cartProducts.find(item => item.id === product.id)
    if (prod) {
      prod.count++
      prod.isChecked = true
      prod.totalPrice = prod.count * prod.price
    } else {
      state.cartProducts.push({
        ...product,
        count: 1,
        isChecked: true,
        totalPrice: product.price
      })
    }

    console.log(state.cartProducts)
  },
  deleteItem (state, id) {
    const index = state.cartProducts.findIndex(item => item.id === id)
    state.cartProducts.splice(index, 1)
  },
  updateCheckedAll (state, checked) {
    state.cartProducts.forEach(prod => {
      prod.isChecked = checked
    })
  },
  updateCheckedItem (state, { checked, prodId }) {
    const prod = state.cartProducts.find(prod => prod.id === prodId)
    prod && (prod.isChecked = checked)
  },

  // 点击商品数量
  updateProduct (state, { prodId, count }) {
    const prod = state.cartProducts.find(prod => prod.id === prodId)
    if (prod) {
      prod.count = count
      prod.totalPrice = count * prod.price
    }
  }
}
const actions = {}

export default {
  namespaced: true,
  state,
  getters,
  mutations,
  actions
}

```



- store/modules/productes.js

```js
import axios from 'axios'

const state = {
  products: []
}
const getters = {}
const mutations = {
  setProducts (state, payload) {
    state.products = payload
  }
}
const actions = {
  async getProducts ({ commit }) {
    const { data } = await axios({
      method: 'GET',
      url: 'http://127.0.0.1:3000/products'
    })
    commit('setProducts', data)
  }
}

export default {
  namespaced: true,
  state,
  getters,
  mutations,
  actions
}

```





# 三. 实现原理



## 1. 基本结构



```js
let _Vue = null
class Store {}

function install(Vue) {
    _Vue = Vue
}

export default {
    Store,
    install
}
```





## 2. 实现



```js
let _Vue = null
class Store {
  constructor (options) {
    const {
      state = {},
      getters = {},
      mutations = {},
      actions = {}
    } = options
    this.state = _Vue.observable(state)
    this.getters = Object.create(null)
    Object.keys(getters).forEach(key => {
      Object.defineProperty(this.getters, key, {
        get: () => getters[key](state)
      })
    })
    this._mutations = mutations
    this._actions = actions
  }

  commit (type, payload) {
    this._mutations[type](this.state, payload)
  }

  dispatch (type, payload) {
    this._actions[type](this, payload)
  }
}

function install (Vue) {
  _Vue = Vue
  _Vue.mixin({
    beforeCreate () {
      if (this.$options.store) {
        _Vue.prototype.$store = this.$options.store
      }
    }
  })
}

export default {
  Store,
  install
}

```













