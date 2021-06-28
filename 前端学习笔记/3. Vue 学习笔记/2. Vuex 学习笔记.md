#### Vuex 学习笔记



## 一、介绍

> Vuex 是实现组件全局状态(数据)管理的一种机制。可以方便的实现组件之间数据的共享，相当于Java中的全局变量



**使用Vuex统一管理状态的好处**

1. 能够在vuex中集中管理全局共享的数据，易于开发和后期维护

2. 能够高效的实现组件之间的数据共享，提高开发效率

3. 存储在vuex中的数据是响应式的，能够实现保持数据与页面的同步

   

**Vue中组件之间共享数据的方式**

* 父向子传值：v-bind  属性绑定
* 子向父传值：v-on     事件绑定  
* 兄弟组件之间共享数据：EventBus  
* 全局数据统一管理：Vuex



## 二、环境搭建

```scss
1. 安装vuex依赖包
npm install vuex --save

2.导入vuex包，并安装到Vue项目中，new一个Vuex.Store的实例对象，并将他暴露出去
//store.js文件
import Vue from 'vue'
import Vuex from 'vuex' //导入vuex包

Vue.use(Vuex) //安装到Vue项目中

//创建Store对象，并将他暴露出去
export default new Vuex.Store({
  // state中存放的就是全局共享的数据
  state: {
    count: 0
  },
 }

3.在创建Vue期间，将store对象挂载到vue实例中
//main.js文件
import Vue from 'vue'
import App from './App.vue'
import store from './store'

new  Vue({
  render: h => h(App),
  // 将创建的共享数据对象，挂载到Vue实例中
  // 所有的组件，就可以直接从store中获取全局的数据了
  store
}).$mount('#app')

```



## 三、Vuex中的四个属性介绍



### 一、 State 数据源

state中用于提供共享数据源，所有全局共享的数据都要放入次数据中

```scss
//store.js
const store = new Vuex.Store({
  //创建store数据源，提供唯一公共数据  
  state: {
    count: 0
  },
})

//组件中访问State中数据的方式
this.$store.state.count
```



### 二、 Mutation 数据变更

Mutation用于变更Store中的数据
1. 推荐只通过Mutation变更Store数据，不建议使用这种方式(this.$store.state.count++)直接操作Store中的数据, 这种写法不利于项目后期的维护
2. 通过这种方式虽然操作起来稍微繁琐一些，但是可以集中监听所有数据的变化

```scss
//store.js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutation:{
     add(state){
         //变更状态
         state.count++
     },
      //携带参数
     addN(state, step){
          state.count +=step
     }
  }
})

//触发mutation中的方法
this.$store.commit('add')
//需要传递参数的写法
this.$store.commit('addN', 3)
```



### 三、 Action 异步数据变更

如果通过异步操作变更数据，必须通过Action，而不能在Mutation中直接操作，但是Action中如果要变更数据，不能直接变更，而是通过调用Mutation的方式间接变更数据

```scss
//store.js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutation:{
     add(state){
         state.count++
     }
  },
  actions:{
       addAsync(context){
           setTimeout(() => {
               context.commit('add')
           }, 1000)
       },
       addAsyncN(context, step){
           setTimeout(() => {
               context.commit('add', step)
           }, 1000)
       }
  }
})


//触发 Action 
this.$store.dispatch('addAsync')
//需要传递参数的写法
this.$store.dispatch('addAsync', 5)
```



### 四、 Getter 包装数据

Getter用于对Store中的数据进行加工处理形成新的数据，不会修改state中的数据，只用于包装数据
1. Getter可以对Store中已有的数据加工处理之后形成新的数据，类似Vue的计算属性
2. Stroe中的数据发生变化，Getter的数据也会跟着变化

```scss
//store.js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  getters: {
       showNum: state => {
          return '当前最新的数量是' + state.count
       }
  }
})

//使用 Getters
methods:{
   handle(){
      //触发action的方式
      this.$store.getters.showNum
   }
}
```







## 四、知识点总结



> 1. Vuex介绍
>
> 2. Vuex的简单实用
>     store的具体实用步骤 3'
> 3. Vuex中的核心概念
>     state的介绍及使用
>     mutation的介绍及使用
>     action的介绍及使用
>     getter的介绍及使用





## 五、学习

* 视频：<https://www.bilibili.com/video/BV1h7411N7bg?p=2>




