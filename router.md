# 路由

## 前端路由和后端路由

- 后端路由：后端处理url和页面之间的映射关系 (早期用的是后端渲染)

- 前段渲染： 浏览器中显示的页面大部分都是前段写的js代码，在浏览器中执行最终渲染出来的页面(前后端分离阶段)

- 前端路由：(SPA:单页面富应用阶段)

## 前端路由

前端路由的核心：改变url但是页面不进行整体的刷新

有两种方式改变路由不会使得浏览器刷新

### 改变hash值 (默认)

```javascript
location.hash = '/foo'
```

### HTML5中的history模式

#### history.pushState()

```javascript
history.pushState({}, '', '/foo')
history.pushState({}, '', '/about')
history.pushState({}, '', '/demo')
```

#### history.back() history.forward()

```javascript
history.pushState({}, '', '/foo')
history.back() //后退
history.forward() //前进
```

#### history.replaceState()

```javascript
history.replaceState({}, '', '/foo')
```

replaceState 和 pushState 的区别是 replaceState改变的url是不能通过前进和后退来改变的

#### history.go()

他可以指定前进或者后退的步数 (负数为后退，正数为前进)

```javascript
history.pushState({}, '', '/foo')
history.pushState({}, '', '/about')
history.pushState({}, '', '/demo')
history.go(-2)
history.go(1)
```

## vue-router

> vue-router的使用

- 1.创建路由组件
- 2.配置路由映射 ：组件和路径的映射关系
- 3.使用路由 ： 通过 <router-link> 和 <router-view>

```<router-link to=''>``` 指定路径 ```<router-view>``` 指定替换的位置，它就相当于占位符

```javascript
// router里面的index.js
import Home from '../components/Home'
import About from '../components/About'
const routes = [
    {
        path: '/home',
        name: 'home',
        component: Home
    },
    {
        path: '/about',
        name: 'about',
        component: About
    }
]
```

```vue
<template>
<!--App.vue-->
  <div id="app">
    <router-link to="/home">Home</router-link>
    <router-link to="/about">About</router-link>
    <router-view></router-view>
  </div>
</template>
```

```vue
<template>
<!--Home.vue-->
  <h2>This is a home page</h2>
</template>

<script>
export default {

}
</script>
```

```vue
<template>
<!--About.vue-->
    <h2>This is a about page</h2>
</template>

<script>
export default {
  
}
</script>
```

### 路由的默认路径 重定向

```javascript
//router中的index.js
const routes =[
    {
        path: '/',
        redirect: '/home'
    },
    {
        path: '/home',
        components: Home
    }
] 
```

### 将默认的 模式从hash改为history模式

```javascript
const router = new VueRouter({
  routes,
  mode: 'history'   //在index.js中添加这一行即可
})
```

### router-link的补充

- router-link标签默认转换成a标签，我们可以用tag属性来改变标签
- router-link默认使用的是 ```history.pushState``` 我们可以添加 ```replace``` 属性来改变
- router-link默认的选中的class名为 ```router-link-active```, 我们可以通过用 ```active-class``` 属性

默认的活跃状态的类名也可以通过路由的index文件来改

```javascript
//router中的 index.js
const router = new VueRouter({
    routes,
    mode: 'history',
    linkActiveClass: 'active'//这一行改变了默认活跃状态的类名
})
```

### 通过js代码修改路由

通过 ```this.$router.push()``` 和 ```this.$router.replace()```

```vue
<!--这里是App.vue文件-->
<template>
  <div id="app">
    <button @click="changeToHomeRouter" :class="{active: isShow}">Home</button>
    <button @click="changeToAboutRouter" :class="{active: !isShow}">About</button>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isShow: 1
    }
  },
  methods: {
    changeToHomeRouter() {
      this.$router.replace('/home')
      this.changeIsShow()
    },
    changeToAboutRouter() {
      this.$router.push('/about')
      this.changeIsShow()
    },
    changeIsShow() {
      this.isShow = !this.isShow;
    }
  }
}
</script>

<style>
.active {
  color: #f40;
}
</style>
```

### 动态路由

首先在index.js 配置路由的时候，动态的部分用 ```:name``` 

我们可以用 ```this.$route.params.name``` 这里的name是上面在index.js中配置的名字

```vue
<!--User.vue-->
<template>
  <div>
    <h2>This is the user page.</h2>
    <p>The username of this user is {{username}}.</p>
  </div>
</template>

<script>
export default {
  name: "User",
  computed: {
    username() {
      return this.$route.params.username
    }
  }
}
</script>
```

```vue
<!--App.vue-->
<template>
<div id="app">
<router-link to="/home">Home</router-link>
<router-link to="/about">About</router-link>
<router-link v-bind:to="`/user/${username}`">User</router-link>
<router-view></router-view>
</div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      username: '陌黎',
    }
  }

}
</script>

<style>
.active {
  color: #f40;
}
</style>
```

```javascript
/*
* index.js
* */
const routes = [
    {
        path: '/',
        redirect: '/home'
    },
    {
        path: '/home',
        component: Home
    },
    {
        path: '/about',
        component: About
    },
    {
        path: '/user/:username',
        component: User
    }
]
```

### 路由的懒加载

按需加载所需的Vue组件

#### 懒加载的方式

- 方式一：Vue异步组件和webpack的代码分析

```javascript
const Home = resolve => {require.ensure(['../components/Home.vue'], () => {resolve(require('..components/Home.vue'))})}
```

- 方式二： AMD写法
```javascript
const About = resolve => require(['..components/About.vue'], resolve);
```

前两种方式我们不怎么用，我们一般用的是第三种方法

- 方式三： 在Es6中

```javascript
const Home = () => import('../components/Home.vue')
```

例子：

```js
/*router中的index.js*/
//上面就不用import导入了

const routes = [
  {
    path: '/',
    redirect: '/home'
  },
  {
    path: '/home',
    component: () => import('../components/Home')
  },
  {
    path: '/about',
    component: () => import('../components/About')
  },
  {
    path: '/user/:username',
    component: () => import('../components/User')
  }
]
```

### 嵌套路由

比如在home页面中，我们希望有 ```/home/news``` ```/home/message```

使用嵌套路由的步骤

- 创建对应的子组件，并在路由映射中配置
- 在组件内使用 ```router-view```标签

```javascript
//router中的index.js
const routes = [
    {
        path: '/',
        redirect: '/home'
    },
    {
        path: '/home',
        component: () => import('../components/Home'),
        children: [
            {
                path: '',
                redirect: 'news'
            },
            {
                path: 'news',
                component: () => import('../components/Home/news')
            },
            {
                path: 'message',
                component: () => import('../components/Home/Message')
            }
        ]
    },
    {
        path: '/about',
        component: () => import('../components/About')
    },
    {
        path: '/user/:username',
        component: () => import('../components/User')
    }
]
```

```vue
<!--在Home.vue中-->
<template>
  <div class="home">
    <h2>This is a home page</h2>
    <router-link to="/home/news">News</router-link>
    <router-link to="/home/message">Message</router-link>
    <router-view></router-view>
  </div>
</template>
```

### 路由传递参数的方式

> params的类型

- 配置路由的格式 ```/router/:id```
- 传递的方式 在path后面跟上对应的值
- 传递后形成的路径 ```/user/zhangsan ```

> query类型

- 配置路由的格式 ```/router```
- 传递的方式 对象中使用query的key作为传递方式
- 传递后形成的路径 ```/user?id=1```

query的例子

```vue
<!--App.vue-->
<template>
  <router-link v-bind:to="{
      path: '/user',
      query: {
        id: 1,
        name: 'zhangsan'
      }
    }">User</router-link>
  <router-view></router-view>
</template>
```

```vue
<!--User.vue-->
<template>
  <div>
    <h2>This is the user page.</h2>
    <p>The username of this user is {{username}}.</p>
  </div>
</template>

<script>
export default {
  name: "User",
  computed: {
    username() {
      return this.$route.query.name
    }
  }
}
</script>
```

```javascript
//index.js
{
    path: '/user', 
        component: () => import('../components/User')
}
```

### 导航守卫

在SPA应用中，我们希望路由改变的同时，我们去修改网页的标题

我们可以通过

```javascript
window.document.title = '新标题';
```

我们一般用```router.beforeEach()``` 和 ```router.afterEach()```

### beforeEach afterEach 钩子函数

在路由跳转的前一刻，Vue执行 beforeEach

语法：

```javascript
router.beforeEach((to, from, next) => {
    next()//这里必须调用next，否则不能正常跳转
})
```
在路由跳转之后，Vue执行 afterEach

```javascript
router.afterEach((to, from) => {
})
```

在route中添加name， 在beforeEach中通过 ```to.matched[0].name```

```javascript
//index.js
{
    path: '/about',
        name: '关于',
    component: () => import('../components/About')
}
router.beforeEach((to, from, next) => {
    document.title = to.matched[0].name,
    next()
})
```

### keep-alive

router-view 也是一个组件，如果直接被包在 keep-alive中，所有路径匹配到的视图组件，都会被缓存

他就不会被频繁的创建，也就不会频繁回调create生命周期函数

```vue
<!--Home.vue-->
<template>
  <div class="home">
    <h2>This is a home page</h2>
    <router-link to="/home/news">News</router-link>
    <router-link to="/home/message">Message</router-link>
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
  </div>
</template>
```

### activated 和 deactivated 函数

- activated() 在组件处于活跃状态时回调的函数
- deactivated() 在组件处于不活跃状态时回调的函数

下面的例子 ：点击news时候，打印activated,点击 message时， 打印 deactivated

```vue
<!--news.vue-->
<template>
  <div>
    <ul>
      <li>news1</li>
      <li>news2</li>
      <li>news3</li>
      <li>news4</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'news',
  activated() {
    console.log('activated');
  },
  deactivated() {
    console.log('deactivated');
  }
}
</script>
```

那么我们希望切换组件，当再次切换回的时候，仍然显示同一组件

我们就要用到 ```beforeRouterLeave(to, from, next)``` 这也是个导航守卫


















