#### VueRouter 学习笔记



## 一、介绍

> Vue 中的页面都是SPA(单页面应用),  页面之间的跳转实际上只是更改了页面的内容，页面的地址是没有变化的，而这一需求<a>标签不能实现，于是推出 VueRouter, 他让我们实现页面的切换就变得易如反掌
>
> 单页面应用：所有的页面最终都打包集合成一个vue界面，这样的好处是不用切换url地址就能改变当前页面内容，坏处是第一次加载会慢一点 





## 二、环境搭建&简单使用

------

```vue
1. 安装vue-router依赖包
npm install vue-router --save-dev

2. 导入vue-router包，配置路由对象，并将路由暴露出去
// ../router/index.js文件
import Vue from 'vue'
import Router from 'vue-router'  //引入vue-router包
import Hello from './components/Hello.vue' //引入Hello.vue页面
import Hi from './components/Hi.vue' //引入Hello.vue页面

Vue.use(Router) //将插件安装到Vue项目中

//创建Router对象，并将他暴露出去
const router = new Router({
  routes: [            
    {                
      path: '/',           //路由链接
      component: Hello,    //路由连接对应的页面
      name: 'Hello'        //路由名称
    },
    {  path: '/Hi',  component: Hi, name: 'Hi'}
  ]
})
export default router


3. 将router对象挂载到Vue全局实例中 
// main.js文件
import Vue from 'vue'
import App from './App.vue'
import router from './router/index'

new  Vue({
  render: h => h(App),
  router
}).$mount('#app')


4. 在App.vue中加入<router-view></router-view>
//App.vue文件
<template>
  <div>
    <router-view style='flex:1'></router-view>
  </div>
</template>

new  Vue({
  render: h => h(App),
  router
}).$mount('#app')


5. 开启服务，在浏览器中输入就可以跳到对应界面
通过http://localhost:8080/#/    会访问到Hello界面
通过http://localhost:8080/#/Hi 会访问到Hi界面

```





## 三、VueRouter学习

------



### 1、使用router-link实现跳转

`注：需要在顶层界面App.vue添加router-view才可以使用`

```vue
//App.vue文件
<template>
  <div>
    <router-link to="/">首页</router-link> | 
    <router-link to="/hi">Hi页面</router-link>

    <router-view style='flex:1'></router-view>
  </div>
</template>
```



### 2、配置子路由时的跳转

`注：需要在顶层界面App.vue添加router-view, 有子路由的话，其父界面也需要添加router-view`

~~~vue
//../router/index.js文件
export default new Router({
  routes: [             
    { path: '/', name: 'Hello', component: Hello },
    { path:'/hi',
      component:Hi,
      children:[
        {path:'/',component:Hi},
        {path:'hi1',component:Hi1},
        {path:'hi2',component:Hi2},
      ]
    }
  ]
})


//App.vue文件
<template>
  <div>
    <router-link to="/">首页</router-link> | 
    <router-link to="/hi">Hi页面</router-link> |
    <router-link to="/hi/hi1">-Hi页面1</router-link> |
    <router-link to="/hi/hi2">-Hi页面2</router-link>
    // 顶层App需要添加router-view
    <router-view style='flex:1'></router-view>
  </div>
</template>


//Hi.vue页面
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
	// 有子路由的话，其父界面也需要添加router-view
    <router-view class="aaa"></router-view>
  </div>
</template>
~~~



### 3、使用代码进行跳转

```vue
this.$router.push('/hi')  //跳转到hi界面
this.$router.go(1)        //前进
this.$router.go(-1)       //后退
```



###  4、页面跳转时携带参数

~~~vue
//跳转时携带参数
<router-link :to="{name:'hi',params:{username:'jspang', id:'888'}}">Hi页面</router-link>
    
//在hi界面接受参数
this.$route.params.username 
~~~



### 5、通过浏览器url跳转时携带参数

~~~vue
//../router/index.js文件
export default new Router({
  routes: [             
    { path: '/', name: 'Hello', component: Hello },
    { 
      path:'/params/:newsId/:newsTitle',
      component:Params
    }
  ]
})

//页面跳转
<router-link to="/params/198/标题题">Params页面</router-link>

//接受传值
this.$route.params.newsId
this.$route.params.newsTitle
~~~



###  6、vue-router的重定向

~~~vue
//../router/index.js文件
export default new Router({
  routes: [             
    {path: '/',component: Hello},
    {
      path:'/params/:newsId(\\d+)/:newsTitle',
      component:Params
    },
    {  //普通重定向
      path:'/goback',
      redirect:'/'
    },
    { //带参数的重定向 (\\d+)是正则，表示只接受数字
      path:'/goParams/:newsId(\\d+)/:newsTitle',
      redirect:'/params/:newsId(\\d+)/:newsTitle'
    }
  ]
})

//页面跳转
<router-link to="/goback">回到主界面</router-link>
<router-link to="/goParams/198/标题题">跳转Params页面</router-link>
~~~



### 7、Mode的设置和404页面的处理

> mode有两个值，默认为hash
>
> - hash模式(默认)：http://jsapng.com/lms/#/  url会带#号
> - history模式：http://jsapng.com/lms/   url不带#号，就像正常的url 

~~~vue
//../router/index.js文件
export default new Router({
  mode: histroy  // 这里设置了history url不会带#， 例如http://jsapng.com/lms/
  routes: [            
    { path: '/',  component: Hello, name: 'Hello'},
    { path: '/Hi', component: Hi, name: 'Hi'},
    {                
      path: '*',    //404页面 如果找不到页面，会打开此页面             
      component: ErrorPage,    
      name: 'ErrorPage'         
    },
  ]
})
~~~





### 8、vueRouter中name的作用

#### 1. 可以对当前界面进行标识，通过$router.name就可以获取name值

~~~vue
//../router/index.js文件
export default new Router({
  mode: histroy  // 这里设置了history url不会带#， 例如http://jsapng.com/lms/
  routes: [            
    { path: '/',  component: Hello, name: 'Hello'},
    { path: '/Hi', component: Hi, name: 'Hi'},
    {                
      path: '*',    //404页面 如果找不到页面，会打开此页面             
      component: ErrorPage,    
      name: 'ErrorPage'         
    },
  ]
})

//在Hello界面时 获得的值是Hello, 如果没有设置name，则没有值
this.$router.name  
~~~



#### 2. 当单页面由多个模块(router-view)组成时  可以根据name识别加载哪一个

~~~vue
// ../router/index.js文件
export default new Router({
  routes: [            
    {
      path: '/',
      components: {
        default:Hello,
        left:Hi1,
        right:Hi2
      }
    },{
      path: '/Hi',
      components: {
        default:Hello,
        left:Hi2,
        right:Hi1
      }
    }
  ]
})


//App.vue文件
<template>
  <div class="hello">
    // 两个页面进行调换
	<router-link to="/">首页</router-link> | 
	<router-link to="/hi">Hi页面</router-link> |
	<router-view ></router-view>

	//左边界面
	<router-view name="left" style="float:left;width:50%;background-color:#ccc;height:300px;"></router-view>
	//右边界面
	<router-view name="right" style="float:right;width:50%;background-color:#c0c;height:300px;"></router-view>

  </div>
</template>
~~~





## 四、知识点总结

------



> 1. VueRouter介绍，为什么要使用VueRouter
> 2. VueRouter的创建过程及简单使用 4'
> 3. VueRouter的使用
>
>       1. 使用router-link实现跳转 ,需要再顶层界面中添加router-view才可以使用
>
>       2. 2 .有子路由时的跳转, 需要在其父界面中添加router-view才可以使用
>
>       3. 使用代码进行跳转
>
>       4. 页面跳转时内部传参
>
>       5. 浏览器url跳转时传参
>
>       6. vue-router的重定向
>
>       7. mode的设置和404页面的处理
>
>       8. vue-router中name的作用
>
>          1. 可以对当前界面进行标识，通过$router.name就可以获取name值
>
>          2. 单页面多模块(router-view)组成  一个页面由多个vue界面组成
>
>             





## 五、学习



* 视频：<https://v.qq.com/x/page/b0393z46qf7.html>   31 - 41讲

* 文章：<http://jspang.com/detailed?id=25#toc211>



