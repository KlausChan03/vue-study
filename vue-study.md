# Vue.js
## 起步
### 通过src在项目中引入vue.js
1. 初窥门径
   ```
    <div id="app">
        {{message}}
    </div>

    <div id="app2">
        <span :title="message">
            测试一下
        </span>
    </div>

    <div id="app3">
        <p v-if="seen">现在你看到我了。</p>
    </div>

    <div id="app4">
        <ol>
            <li v-for="todo in todos">
                {{ todo.text }}
            </li>
        </ol>
    </div>
    
    <div id="app5">
        <p>{{ message }}</p>
        <button v-on:click="reverseMessage">反转信息</button>
    </div>

    <div id="app6">
        <p>{{ message }}</p>
        <input type="text" v-model="message">
    </div>

    <div id="app7">
        <ol>
            <todo-item
                v-for="item in groceryList"
                v-bind:todo="item"
                v-bind:key="item.id"
            ></todo-item>
        </ol>
    </div>

    <script>
        // 声明式渲染
        var vm = new Vue({
            el: "#app",
            data: {
                message: "i am back"
            }
        })

        var vm2 = new Vue({
            el: "#app2",
            data: {
                message: '页面加载于' + new Date().toLocaleString()
            }
        })

        // 条件与循环
        var vm3 = new Vue({
            el: "#app3",
            data: {
                seen: false
            }
        })

        var vm4 = new Vue({
            el: "#app4",
            data: {
                todos: [
                    { text: '学习 Javascript' },
                    { text: '学习 Typescript' },
                    { text: '学习 Vue' },
                    { text: '整个有意义的项目' }
                ]
            }
        })
        
        // 处理用户输入
        var vm5 = new Vue({
            el: "#app5",
            data: {
                message: "Hello Vue.js"
            },
            methods: {
                reverseMessage: function() {
                    this.message = this.message.split('').reverse().join('');
                }
            }
        })

        var vm6 = new Vue({
            el: "#app6",
            data: {
                message: 'Hello Vue!'
            }
        })

        // 组件化应用构建
        Vue.component('todo-item', {
            props: ['todo'],
            template: '<li>{{ todo.text }}</li>'
        })

        var vm7 = new Vue({
            el: "#app7",
            data: {
                groceryList: [
                    { id: 0, text: '蔬菜'},
                    { id: 1, text: '水果'}
                ]
            }
        })
        vm3.seen = true;
        vm4.todos.push({ text: '抽时间重构旧项目' });
    </script>
    ```
2. 常用指令
   1. 条件渲染（v-if / v-show） 
      1. v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
      2. 相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
   2. 列表渲染 （v-for）
      1. v-for 指令需要使用 item in items 形式的特殊语法，其中 items 是源数据数组，而 item 则是被迭代的数组元素的别名。
   3. 事件处理（v-on）
   4. 表单输入绑定（v-model）
      1. v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。
3. 组件
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
4. 过渡 & 动画
   1. 
5. 

       

## 进阶
### 通过脚手架vue-cli构建项目
1. 引入外部模块
   1. 在 vue 中优雅的使用第三方库
        ```
        import moment from 'moment'
        Object.defineProperty(Vue.prototype, '$moment', { value: moment })
        export default {
        created() {
            console.log('The time is '.this.$moment().format('HH:mm'))
        }
        }
        ```
        
# 疑难杂症
## 安装依赖
### 解决sass安装失败
```
npm install yarn 
yarn config set sass_binary_site http://cdn.npm.taobao.org/dist/node-sass -g
yarn install
```