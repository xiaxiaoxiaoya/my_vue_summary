# 目录

1.[VUE2.0项目搭建步骤](#v2)

2.[VUE4.0脚手架搭建](#v4)

3.[vuex项目总结](#vuex)

4.[vue项目所遇问题总结](#vue)

# <span id="v2">VUE2.0项目搭建步骤</span>

### 一、搭建脚手架

```js
npm i vue-cli --save  //安装脚手架
vue init wabpack demo //选择模板
npm uninstall vue-cli -g  //卸载vue
//vue -V：查看是否安装成功及版本号  
//vue list：可以查看所有模板
```

### 二、拆分静态组件
### 三、设计路由

```js
npm i vue-router --save //下载路由
```

1、拆分路由组件：新建pages文件夹/xxx.vue；
2、定义路由的文件夹及文件：routes/index.js；

```js
import admin from "@/pages/admin.vue";
export default [
  {path:"/admin",component:admin},

  //设置重定向
  {path:"/",redirect:"admin"}
]

```

3、创建路由器的文件夹及文件：router/index.js；

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)
import routes from '@/routes'

export default new VueRouter({
    routes,
    // mode:"history"//路由器类型
})

```

4、注册路由：在main.js中注册路由

```js
import Vue from 'vue'
import App from './App.vue'
import router from '@/router'

Vue.config.productionTip=false
new Vue({
    el:"#app",
    router,
    render:h => h(App)
})
```

### 四、设计数据(数据结构应该是什么样，考虑请求在哪儿发送)

```
npm i axios --save
```

### 五、搭建vuex仓库(数据结构应该是什么样，考虑请求在哪儿发送)

```js
npm i vuex --save //下载脚手架
```

1、新建仓库src/store/index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
//创建一个仓库 :将仓库对象中的所有核心组件都单独创建一个文件并且引入。
import state from './state.js'
import getters from './getters.js'
import mutations from './mutations.js'
import actions from './actions.js'
export default new Vuex.Store({
    state,
    getters,
    mutations,
    actions,
});
```

2、新建仓库的所有核心模块文件，在index.js中引入使用，连第1步

```js
store文件夹://index.js、state.js、getters.js、mutations.js、actions.js、
```

3、在main.js中注册vuex 即仓库store

# <span id="v4">VUE4.0脚手架搭建</span>

### 一、下载4.0脚手架

```js
npm i @vue/cli -g
```

### 二、创建项目

```js
vue create my-project
```

### 三、开发环境启动

```js
npm run serve
```

### 四、生产环境启动

```js
npm run build //打包文件
```

### 五、配置文件修改

> 创建一个vue.config.js文件进行配置

```js
module.exports = {
    //是否使用eslint
    lintOnSave: false,
    devServer: {
        port: 5566, // 配置端口号
        open: true, //配置自动启动浏览器
    }
}
```

> 在配置中设置别名

```js
const path = require('path');
//__dirname 当前文件所在的目录 : layout
function resolve (dir) {
    return path.join(__dirname, dir)
}
module.exports={
    lintOnSave:false,
    devServer:{
        port:3001,
        open:true,
    },
    configureWebpack:{//配置原生webpack，在此配置别名
        resolve: {
            alias: {
                'components': resolve('src/components'),
                'pages': resolve('src/pages'),
            }
        }
    }
}
```



# <span id="vuex">vuex项目总结</span>

项目中的总结：

>  1、每个组件都有一个计算属性与vuex的每一个sate数据一一对应；此时可以用到辅助函数，如mapState;
> 一般情况下，仓库中的state和getters，在组件中都会有一个计算属性与之对应；
> 一对情况下，仓库中的mutations和actions，在组件中都会有一个方法与之对应；
>
> 2、辅助函数只能用来显示数据，而不能用于操作数据。此时，需要我们自己写计算属性，利用计算属性中的get和set方法去获取才和修改vuex中state中的值；
>
> 3、在真实开发中，不管同步还是异步，为了统一对vuex进行操作，都通过dispach分发一个action,在action中提交(commit)一个mutation,最后在mutation中进行修改同步state. //流程：dispath——action——commit——mutation——修改——state
>
> 4、在开发过程中，建一个mutaion_tyep.js,文件夹用于存放操作mutation的常量名。防止程序员写错单词。而且，最重要的是：当打开项目，查看这个文件，文件上的备注，可以一目了然的看出来，这个mutaion是做什么用的。 注：actions和mutaions中的对象名(通过被简化成一个方法形式)，要替换成常量是，需要加上中括号[]。
>
> 5、只有action接收的默认参数是与store实例有相同方法和属性的对象，其他都是会接收到一个state。

# <span id="vue">vue项目所遇问题总结</span>

### 1、在匿名函数中修改外部变量

> 如果直接在mounted计算属性中写匿名函数改变外部变量是不行的。this指向不对。

法一，可以通过回调，像点击等之类的回调方法。

法二，在vue中，可以直接用mounted方法。

```js
async mounted (){
  const body = await axios({
         url:'/xiaxiaoxia',
  })
  console.log(body.data)
  this.data1=body.data
  console.log(this.data1)
},
```

但这样的话，万一mounted计算属性对象不止一个呐？因此，

法三，可以在methods中定义一下这个方法，然后在mounted中调用一下就可以解决这个问题了。**推荐使用**

```js
mounted: function() {
    console.log(this)
    this.getdata()
      
},
methods: {
    async getdata (){
        const body = await axios({
            url:'/xiaxiaoxia',
        })
        console.log(body.data)
        this.data1=body.data
        console.log(this.data1)
    }
}
```

法四，就是通过axios原生写法，不用await:

```js
 axios.get("/xiaxiaoxia").then(res => {
     //get()中的参数要与mock.js文件中的Mock.mock()配置的路由保持一致
     this.data1 = res.data;
     console.log(res.data); //在console中看到数据
 }).catch(res => {
     alert("wrong");
 });
```

