### 步骤

开发一个前端模块可以概括为以下几个步骤：
（1）写静态页面、拆分为静态组件；
（2）发请求（`API`）
（3）`Vuex`三连环（`actions`、`mutations`、`state`）
（4）组件获取仓库数据，动态展示数据

### 初始化项目

创建文件夹：自定义名字如`project_pyg`

初始化项目：打开`cmd`，输入`vue create 项目名称`

`cd 文件夹路径` 跳转到当前项目

`npm run serve`启动服务器

### 项目文件结构

```
├── node_modules:存储项目依赖包
├── public:静态资源，webpack原封不动打包到dist文件夹
│   ├── favicon.ico: 页签图标
│   └── index.html: 主页面
├── src: 程序员源代码文件夹
│   │── api
│   │   └── index.js: 统一管理项目接口的模块
│   │   └── request.js: 对axios进行二次封装
│   ├── assets: 存放公共的静态资源（多个组件共用）
│   │   └── logo.png
│   │── components: 存放非路由组件（全局组件）
│   │   └── HelloWorld.vue
│   │── pages: 路由组件文件夹
│   │   └── Home: Home组件
│   │   │   └── index.vue
│   │── router
│   │   └── index.js: 配置路由的地方
│   │── store: vuex仓库
│   │   └── home: 存储home数据的小仓库
│   │   │   └── index.js
│   │── App.vue: 唯一的根组件，汇总所有组件
│   │── main.js: 入口文件
├── .gitignore: git版本管制忽略的配置
├── babel.config.js: babel的配置文件
├── package.json: 应用包配置文件，项目的详细信息记录
├── README.md: 项目描述文件
├── package-lock.json：包版本控制文件（各种包的来源）
```

`pubilc/index.html`是一个模板文件，作用是生成项目的入口文件，`webpack`打包的`js,css`也会自动注入到该页面中。我们浏览器访问项目的时候就会默认打开生成好的`index.html`。
`components`： 非路由组件（全局组件），其他组件放在views或者pages文件夹中
`main.js`： 程序入口文件，最先执行的文件

### 项目其他配置

#### 关闭`eslint`自动校验

```js
// vue.config.js文件
lintOnSave: false
```

#### 配置`src`文件夹简写方式（配置别名）

```json
// jsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
        "@/*": ["src/*"]
    },
      "exclude": ["node_modules","dist"]
}
```

### 组件页面样式

组件页面的样式使用的是less，浏览器不识别该样式，需要下载相关依赖

```
npm install --save less less-loader@6
```

如果想让组件识别less样式，则在组件中设置

```vue
<style scoped lang="less"></style>
```

### 路由组件搭建

#### 安装路由：`npm i vue-router@3`

#### 路由组成

前端所谓路由：K V键值对

- key:  URL（地址栏中的路径）
- value: 相应的路由组件

#### 创建文件夹

在`src`目录下创建`pages || views`文件夹：经常用于放置路由组件

`router`文件夹：用来配置路由

```js
// router/index.js 配置路由的地方
import Vue from 'vue'; // 引入Vue
import VueRouter from 'vue-router'; // 引入路由
// 应用插件
Vue.use(VueRouter)
// 引入路由组件
import Home from '@/pages/Home'
import Login from '@/pages/Login'
// 配置路由
export default new VueRouter({
    // 配置路由
    routes: [
        {
            path: "/home",
            component: Home,
   // meta存放自定义属性，show控制组件显示隐藏
            meta:{show: true}
        },
        {
            path: "/login",
            component: Login
        },
    ]
})
```

在`main.js`中引入路由

```js
import router from '@/router'
new Vue({
  render: h => h(App),
  // 注册路由：底下的写法是K V 一致省略V【router要小写】
  // 书写router后，组件身上都拥有$route、$router属性
  router
}).$mount('#app')
```

- `$route`:  获取路由信息【路径、query、params等等】
- `$router`:  编程式导航进行路由跳转【push、replace】

放置路由

```vue
<template>
  <!-- 路由组件显示的地方 -->
    <router-view></router-view>
</template>
```

#### 路由跳转

- 声明式导航

  ```vue
  <router-link :to="{name:home}">跳转</router-link>
  ```

- 编程式导航

  ```vue
  this.$router.push('/home')
  this.$router.replace({name:'peqi'})
  ```

编程是导航更强大，可以在跳转之前做一些其他的业务逻辑

#### 路由传参

- 路由传参（对象写法）可以是`name`、`path`形式，但是如果携带了`params`参数，则必须是`name`形式

- 指定`params`参数可传可不传，配置路由时，在占位符`:xxx`后面加个问号`?`

  ```js
  {
      path: "/search/:keyword?",
      component: Search
   },
  ```

- 指定`params`参数可传可不传，但如果`params`传递的是一个空字符串，路径会出现问题，使用`undefined`解决

  ```vue
  this.$router.push({
     name: 'search',
     params:{
        keyword: this.keyword || undefined
     }
  })
  ```

- 路由可以传递`props`参数，有三种写法

  ```js
  {
  	name:'xiangqing',
  	path:'detail/:id',
  	component:Detail,
  
  //1.对象写法，额外传递一些props参数给Detail组件
  	// props:{a:900}
  
  //2.布尔值写法，为true，则把路由收到的所有 params参数通过props传给Detail组件
  	// props: true
  	
  //3.函数写法，该函数返回的对象中每一组key-value都会通过props传给Detail组件
  	props：($route) => {
  		return {
  // 可以同时传递query参数和params参数
  			id: $route.query.id,
  			title: $route.params.title
  		}
  	}
  }
  ```

- 编程式路由跳转到当前路由（参数不变），多次执行会抛出`NavigationDuplicated`的警告错误，解决方案：

  ①使用`push`方法跳转路由传递参数时，添加成功和失败回调（指标不治本）

  ```vue
  this.$router.push({name:'search',params:{keyword: this.keyword}},()=>{},()=>{})
  ```

  ②通过重写`push`和`replace`方法解决

  ```js
  // router/index.js
  // 先把VueRouter原型对象的push，先保存一份
  let originPush = VueRouter.prototype.push;
  // 重写push | replace
  // 第一个参数，告诉原来push方法，往哪里跳转（传递哪些参数）
  VueRouter.prototype.push = function(location,resolve,reject) {
      if(resolve && reject) {
          originPush.call(this,location,resolve,reject)
      } else {
          originPush.call(this,location,()=>{},()=>{})
      }
  }
  ```

### `axios`二次封装

#### 目的：

- 请求拦截器：发送请求之前处理一些业务
- 响应拦截器：当服务器数据返回以后，可以处理一些事情

#### 安装

```
npm install --save axios 
```

#### 创建文件夹

在`src`目录下新建文件夹`api`，用于存放关于`axios`请求的文件

创建二次封装`axios`的`js`文件，如`request.js`,`datareq.js`等，在文件内引入`axios`，利用`axios`的`create`方法创建`aixos`实例，然后添加请求拦截器和响应拦截器，可以在拦截器中处理一些业务逻辑。最后向外暴露处理好的`axios`实例

```js
// 对于axios进行二次封装

// 引入axios
import axios from 'axios';
// 引入进度条
import nprogress from 'nprogress';
// 引入进度条样式
import 'nprogress/nprogress.css';
// 在当前模块引入store
import store from '@/store';

// 1.利用axios的create方法，创建一个axios实例,可以传入一个配置对象
const requests = axios.create({
    // 配置对象
    baseURL: "/api",
    timeout: 5000
});
// 请求拦截器
requests.interceptors.request.use((config) => {
    // 如果仓库中存储了uuid游客身份
    if (store.state.detail.uuid_token) {
        // 请求头添加一个字段(userTempId)
        config.headers.userTempId = store.state.detail.uuid_token;
    }
    // 如果仓库中存储了用户token
    if (store.state.user.token) {
        // 请求头添加一个字段(token)
        config.headers.token = store.state.user.token;
    }
    // 发请求开启进度条
    nprogress.start();
    return config;
});
// 响应拦截器
requests.interceptors.response.use((res) => {
    // 响应成功关闭进度条
    nprogress.done();
    return res.data;
}, (error) => {
    return Promise.reject(new Error('faile'));
})

// 对外暴露
export default requests;
```



### `API`接口统一管理

项目很小：完全可以在组件的生命周期函数中发请求

项目大：在`api`文件夹下创建一个`index.js`文件进行统一管理

```js
// 统一管理项目接口的模块
// 引入二次封装的axios（带有请求、响应的拦截器）
import requests from './request';
// 对外暴露一个函数，只要外部调用这个函数，就像服务器发起ajax请求
// 发请求: axios发请求返回结果是Promise对象
export const reqCategoryList = () => requests({ url: '/product/getBaseCategoryList', method: 'get' });
```

可以在入口文件`main.js`中统一引入`api/index.js`中的全部请求函数，只要能获取到组件实例`vm`的地方就可以直接发使用这些请求函数请求。

```js
// main.js
// 统一引入 接口api文件夹里面全部请求函数
import * as API from '@/api';
new Vue({
  render: h => h(App),
  beforeCreate() {
      // 把这些请求函数注册到vue的实例上
    Vue.prototype.$API = API;
  },
}).$mount('#app')
```

在组件上可以直接调用请求函数发请求

```vue
this.$API.reqCategoryList()
```

#### 跨域问题

跨域：协议、域名、端口号不同的请求

解决的方法：`JSONP`、`CORS`、代理

配置代理解决跨域：在`vue.config.js`文件中配置

```js
 devServer: {
    proxy: {
      '/api': { // 在发请求时，路径中带有/api这样的请求，代理服务器会工作，找下方地址的服务器要数据
        target: 'http://39.98.123.211',
        pathRewrite: { '^api': '' }
      }
    }
  }
```

### `nprogress`进度条插件

安装插件

```
npm install --save nprogress
```

在`api`文件夹下的`axios`二次封装文件中引入

```js
// 引入进度条
import nprogress from 'nprogress'
// 引入进度条样式
import "nprogress/nprogress.css";

// 在请求拦截器中开启进度条 nprogress.start();
// 在响应拦截器中结束进度条 nprogress.done()
```

可以通过修改`nprogress.css`文件的`background`来修改进度条颜色。

```css
#nprogress .bar {
  background: #29d;

  position: fixed;
  z-index: 1031;
  top: 0;
  left: 0;

  width: 100%;
  height: 2px;
}
```

### 状态管理库`vuex`

`vuex`是官方提供的一个插件，状态管理库，集中式管理项目中组件共用的数据。只有项目很大、组件很多、数据多且难以维护，才使用`vuex`。

#### 安装`vuex`

```
npm install --save vuex@3
```

#### 新建文件夹

创建`store`文件夹用于集中存放数据（仓库）

大仓库`store/index.js`

```js
import Vue from 'vue';
import Vuex from 'vuex';
// 应用插件
Vue.use(Vuex);
// 引入存储home数据的小仓库
import home from './home'
import search from './search'

// 对外暴露Store类的一个实例
export default new Vuex.Store({
    // 实现vuex仓库模块化开发存储数据
    modules: {
        home,
        search
    }
})
```

小仓库：`store/home/index.js`

```js
//准备actions对象——响应组件中用户的动作
const actions = {}
//准备mutations对象——修改state中的数据
const mutations = {}
//准备state对象——保存具体的数据
const state = {}
//准备getters对state中数据进行加工后使用（看作计算属性）
const getters = {}
// 对外暴露
export default {
    actions,
    mutations,
    state,
    getters
}
```

### 函数的防抖与节流

- 节流：在规定的间隔时间范围内不会重复触发回调，只有大于这个时间间隔才会触发回调，把频繁触发变为少量触发

  （一定时间内，只会执行一次）

- 防抖：前面的所有的触发都会被取消，最后一次执行在规定的时间之后才会触发，只会执行一次

  （前面的触发都会被取消，只执行最后一次）如果一直重复触发，执行的时间会往后推迟。

### `mock`模拟数据

`mock`生成随机数据，拦截`Ajax`请求

安装

```
npm i mockjs
```

1.在`src`下新建`mock`文件夹

2.准备`JSON`数据（`mock`文件夹下新建`JSON`文件），格式化一下，不要留空格。

3.把`mock`数据里面的图片放置到`public`文件夹中`images`文件夹（`public`文件夹在打包的时候，会把相应的资源原封不动打包到`dist`文件夹中）

4.在`mock`文件夹下新建`mockServe.js`文件通过`mockjs`插件实现模拟数据

```js
// 先引入mockjs模块
import Mock from 'mockjs';
// 把JSON数据格式引入进来[JSON数据格式根本没有对外暴露，但是可以引入]
// webpack默认可对外暴露的：图片、JSON数据格式
import banner from './banner.json';
import floor from './floor.json';

// mock数据：第一个参数请求地址  第二个参数，请求数据
Mock.mock("/mock/banner", { code: 200, data: banner });// 模拟首页大的轮播图的数据
Mock.mock("/mock/floor", { code: 200, data: floor });
```

5.把`mockServe.js`文件在入口文件中引入（至少需要执行一次，才能模拟数据）

```js
// main.js
// 引入MockServe,js---mock数据
import '@/mock/mockServe'
```

### `swiper`基本使用

安装`swiper`

```
npm i swiper@5
```

1.引入相应的`css、js`文件

```vue
import Swiper from 'swiper'; 
import 'swiper/css/swiper.css';
```

2.添加HTML内容。`Swiper7`的默认容器是`.swiper`，`Swiper6`之前是`.swiper-container`。

```html
<div class="swiper">
    <div class="swiper-wrapper">
        <div class="swiper-slide">Slide 1</div>
        <div class="swiper-slide">Slide 2</div>
        <div class="swiper-slide">Slide 3</div>
    </div>
    <!-- 如果需要分页器 -->
    <div class="swiper-pagination"></div>
    
    <!-- 如果需要导航按钮 -->
    <div class="swiper-button-prev"></div>
    <div class="swiper-button-next"></div>
    
    <!-- 如果需要滚动条 -->
    <div class="swiper-scrollbar"></div>
</div>
<!--导航等组件可以放在Swiper容器之外 -->
```

3.给`Swiper`定义一个大小，当然不要也行

```css
.swiper {
    width: 600px;
    height: 300px;
}  
```

4.初始化`Swiper`（轮播图添加动态效果）

```js
var mySwiper = new Swiper ('.swiper-container', {
    direction: 'vertical', // 垂直切换选项
    loop: true, // 循环模式选项
    
    // 如果需要分页器
    pagination: {
      el: '.swiper-pagination',
      clickable: true,
    },
    
    // 如果需要前进后退按钮
    navigation: {
      nextEl: '.swiper-button-next',
      prevEl: '.swiper-button-prev',
    },
    
    // 如果需要滚动条
    scrollbar: {
      el: '.swiper-scrollbar',
    },
 })        
```

### 组件间通信

1. `props`：用于父子间通信
2. 自定义事件：`@on`、`@emit`可以实现子给父通信
3. 全局事件总线：`$bus` 全能
4. 插槽：父传子
5. `vuex`：全能
6. `pubsub-js`：vue中几乎不用 全能

### `getters`

计算属性：利用已有属性的属性值，造出一个新的属性

`vuex`中的`getters`：为了简化仓库数据`state`而生，可以把我们将来在组件中需要用的数据简化一下，方便组件获取数据

组件中获取仓库数据时，`state`是按照小仓库的数量分成若干个模块的，而`getters`是不分模块的

### 使用`echarts`

安装 `npm install echarts --save`



### vue中展示.md文件

#### 安装 vue-markdown-loader

将markdown文件解析并编译成html文件



```shell
npm install vue-markdown-loader  -D
```

#### 安装 vue-loader 、vue-template-compiler

将文件渲染成vue组件



```shell
npm install vue-loader  -D
npm install vue-template-compiler  -D
```

#### 安装 github-markdown-css、highlight.js

美化样式、高亮代码



```shell
npm install github-markdown-css  -D
npm install highlight.js  -D
```

#### 在vue.config.js中配置webpack



```js
module.exports = {
  chainWebpack: config => {
    config.module.rule('md')
      .test(/\.md/)
      .use('vue-loader')
      .loader('vue-loader')
      .end()
      .use('vue-markdown-loader')
      .loader('vue-markdown-loader/lib/markdown-compiler')
      .options({
        raw: true
      })
  }
}
```

#### 在main.js中引入样式文件



```dart
// markdown样式
import 'github-markdown-css'
// 代码高亮
import 'highlight.js/styles/github.css'
```

#### 在vue组件中使用

将md文件作为组件导入，注意：class的值必须包含 markdown-body ，否则样式无法生效！



```xml
<template>
  <demo-md class="markdown-body"></demo-md>
</template>

<script>
import DemoMd from './demo.md';
export default {
  components: {
    DemoMd
  }
}
</script>
```

### Vue中解析md字符串

#### 安装

```bash
npm i marked
npm i highlight.js -D  
```

#### 引入

```js
// marked
import { marked } from "marked";
// 代码高亮
import "highlight.js/styles/github-dark.css";
```

#### 使用

```vue
<template>
<div class="markbody" v-html="artbody"></div>
</template>

<script>
// marked
import { marked } from "marked";
// 代码高亮
import "highlight.js/styles/github-dark.css";

marked.setOptions({
  renderer: new marked.Renderer(),
  highlight: function (code, lang) {
    const hljs = require("highlight.js");
    const language = hljs.getLanguage(lang) ? lang : "plaintext";
    return hljs.highlight(code, { language }).value;
  },
  langPrefix: "hljs language-", // highlight.js css expects a top-level 'hljs' class.
  pedantic: false,
  gfm: true,
  breaks: false,
  sanitize: false,
  smartLists: true,
  smartypants: false,
  xhtml: false,
});
export default {
  name: "ArtMain",
  data() {
    return {
      artbody: "",
    };
  },
  mounted() {
    this.$store.dispatch("articleById", this.$route.query.id);
  },
  computed: {
    artmain() {
      return this.$store.state.article.artmain;
    },
  },
  watch: {
    artmain(newVal) {
      if (newVal) {
        this.artbody = marked.parse(newVal.body);
      }
    },
  },
};
</script>
```



### Vue中md文档编辑器

#### 安装

```shell
npm install mavon-editor
```

#### 在Vue中引入并使用

```vue
  <template>
        <div id="editor">
            <mavon-editor style="height: 100%"></mavon-editor>
        </div>
    </template>
    <script>
    // Local Registration
    import { mavonEditor } from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'
    export default {
        name: 'editor',
        components: {
            mavonEditor
            // or 'mavon-editor': mavonEditor
        }
    }
    </script>
    <style>
    #editor {
        margin: auto;
        width: 80%;
        height: 580px;
    }
    </style>
```

## 路由跳转页面滚动至头部

```js
// 对外暴露VueRouter类的实例
let router = new VueRouter({
    // 配置路由信息
    routes,
    // 滚动行为
    scrollBehavior(to, from, savedPosition) {
        // 始终滚动到顶部
        return { y: 0 }
    },
})
```

