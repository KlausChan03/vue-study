# Vue.js
## 起步
### 通过src在项目中引入vue.js

1. 常用指令
   1. 条件渲染（v-if / v-show） 
      1. v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
      2. 相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
   2. 列表渲染 （v-for）
      1. v-for 指令需要使用 item in items 形式的特殊语法，其中 items 是源数据数组，而 item 则是被迭代的数组元素的别名。
   3. 事件处理（v-on）
   4. 表单输入绑定（v-model）
      1. v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。
2. 组件
   1. 命名：支持使用 kebab-case 和使用 PascalCase。自己习惯使用前者。
   2. 注册：支持全局和局部注册。
      1. 局部注册 局部注册的组件在其子组件中不可用。 
        ```
        var ComponentA = { /* ... */ }
        var ComponentB = {
        components: {
            'component-a': ComponentA
        },
        // ...
        }

        // ES2015模块
        import ComponentA from './ComponentA.vue'
        export default {
        components: {
            ComponentA
        },
        // ...
        }
        ```
3. 过渡 & 动画
   1. 单元素/组件的过渡
        #### Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡
      1. 条件渲染 (使用 v-if)
      2. 条件展示 (使用 v-show)
      3. 动态组件
      4. 组件根节点
         1. 过渡的类名
            1. v-enter：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
            2. v-enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
            3. v-enter-to: 定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter 被移除)，在过渡/动画完成之后移除。
            4. v-leave: 定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
            5. v-leave-active：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
            6. v-leave-to: 定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。 

        ![image](https://github.com/KlausChan03/vue-study/raw/master/images/transition.png)
   2. 多个元素的过渡

## 进阶
### 通过脚手架vue-cli构建项目
1. 引入外部模块
   1. 在 vue 中优雅的使用第三方库
        ```
        eg1:
        main.js
        import moment from 'moment'
        Object.defineProperty(Vue.prototype, '$moment', { value: moment })
        ...
        xx.vue
        export default {
            created() {
                console.log('The time is 'this.$moment().format('HH:mm'))
            }
        }
        eg2:
        main.js
        import lodash from 'lodash'
        Object.defineProperty(Vue.prototype, '_', { value: lodash })
        xx.vue
        _this.dataList.startList = _.sortBy(_this.dataList.startList, function(o) { return o.eventStatus; });
        
        ```
# 路由 (Vue Router)
## 动态路由匹配
我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 User 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 vue-router 的路由路径中使用“动态路径参数”(dynamic segment) 来达到这个效果  
```
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```  
现在呢，像 /user/foo 和 /user/bar 都将映射到相同的路由。  
一个“路径参数”使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 this.$route.params，可以在每个组件内使用。于是，我们可以更新 User 的模板，输出当前用户的 ID：

```
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```
### 响应路由参数的变化
当使用路由参数时，例如从 /user/foo 导航到 /user/bar，**原来的组件实例会被复用**。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。**不过，这也意味着组件的生命周期钩子不会再被调用**。  
复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch (监测变化) $route 对象：  
```
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```  
或者使用 beforeRouteUpdate 导航守卫：
```
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```
### 捕获所有路由或 404 Not found 路由
常规参数只会匹配被 / 分隔的 URL 片段中的字符。如果想匹配**任意路径**，我们可以使用通配符 (*)： 
```
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}
```
当使用一个通配符时，$route.params 内会自动添加一个名为 pathMatch 参数。它包含了 URL 通过通配符被匹配的部分：  
```
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```
### 匹配优先级  
有时候，同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。  
## 编程式的导航  
除了使用 \<router-link> 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。
```
router.push(location, onComplete?, onAbort?)
```
```
router.replace(location, onComplete?, onAbort?)
```
| 声明式 | 编程式 | 
| :----: | :----: | 
| ```<router-link :to="...">``` | ```router.push(...)``` |  
```
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```  
**注意：如果提供了 path，params 会被忽略，上述例子中的 query 并不属于这种情况。取而代之的是下面例子的做法，你需要提供路由的 name 或手写完整的带有参数的 path：**
```
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```
**同样的规则也适用于 router-link 组件的 to 属性。**

router.replace 跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

在 router.push 或 router.replace 中提供 onComplete 和 onAbort 回调作为第二个和第三个参数。这些回调将会在导航成功完成 (在所有的异步钩子被解析之后) 或终止 (导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行相应的调用。

```
router.go(n)
```
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 window.history.go(n)。

Vue Router 的导航方法 (push、 replace、 go) 在各类路由模式 (history、 hash 和 abstract) 下表现一致。

## 命名路由
有时候，通过一个名称来标识一个路由显得更方便一些，特别是在链接一个路由，或者是执行一些跳转的时候。你可以在在创建 Router 实例的时候，在 routes 配置中给某个路由设置名称。
```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```
要链接到一个命名路由，可以给 router-link 的 to 属性传一个对象，也可以调用 router.push()：
```
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>

router.push({ name: 'user', params: { userId: 123 }})
```
## 命名视图
有时候想同时 (同级) 展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar (侧导航) 和 main (主内容) 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。
```
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```
一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 components 配置 (带上 s)：
```
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

# 状态管理 (Vuex)

## Vuex 是什么？

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

### 什么是“状态管理模式”？

状态自管理应用包含以下几个部分：
- state，驱动应用的数据源；
- view，以声明方式将 state 映射到视图；
- actions，响应在 view 上的用户输入导致的状态变化。  

以下是一个表示“单向数据流”理念的简单示意：  

![image](https://github.com/KlausChan03/vue-study/raw/master/images/flow.png)  

但是，当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏：  

- 多个视图依赖于同一状态。
- 来自不同视图的行为需要变更同一状态。  

对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。  

因此，我们为什么不把组件的共享状态抽取出来，以一个全局单例模式管理呢？在这种模式下，我们的组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为！

通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。

![image](https://github.com/KlausChan03/vue-study/raw/master/images/vuex.png)    

## Vuex 是什么？  

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的状态 (state)。Vuex 和单纯的全局对象有以下两点不同： 

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。  

2. 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。  

## 开始
  
### 最简单的 Store 

安装 Vuex 之后，让我们来创建一个 store。创建过程直截了当——仅需要提供一个初始 state 对象和一些 mutation：
```
// 如果在模块化构建系统中，请确保在开头调用了 Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```
现在，你可以通过 store.state 来获取状态对象，以及通过 store.commit 方法触发状态变更：  

```
store.commit('increment')

console.log(store.state.count) // -> 1
```  

## 核心概念

### State

> state就是Vuex中的公共的状态，我是将state看作是所有组件的data， 用于保存所有组件的公共数据。   

### Getter

> 我将getters属性理解为所有组件的computed属性, 也就是计算属性. vuex的官方文档也是说到可以将getter理解为store的计算属性, getters的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。  

### Mutations

> 我将mutaions理解为store中的methods属性, mutations对象中保存着更改数据的回调函数,该函数名官方规定叫type, 第一个参数是state, 第二参数是payload, 也就是自定义的参数。  

### Actions

Action 类似于 mutation，不同在于：
- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

# 疑难杂症
## 安装依赖
### 解决sass安装失败
```
npm install yarn 
yarn config set sass_binary_site http://cdn.npm.taobao.org/dist/node-sass -g
yarn install
```
## 使用规范
### 不能在重复引入computed和watch等Vue属性，否则后者会覆盖前者。
