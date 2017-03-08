---
title: 停简单商户系统前端技术文档 
---
### 前言
>[停简单电子优惠系统](http://open.tingjiandan.com/tcmerchant/)前端基于MVVM框架[vue2.0](http://cn.vuejs.org/v2/guide/)搭建的单页应用，至于为何是单页应用架构原因有二：1，优惠系统前端业务相对比较少，为了快速把之前jsp页面转换为纯前端页面选择[vue-cli](https://github.com/vuejs/vue-cli)官方脚手架快速开发。2，系统适配PC 手机 pad 多终端运行，为了方便代码复用及保持与之前链接一直，引入官方[vue-router 2](http://router.vuejs.org/zh-cn/)路由模式方便快捷管理路由
### 技术要点
* **nodejs** 环境必备[《nodejs基础篇：安装》]()
* **vue2.0** 基础框架[《vue2.0官方文档》](http://cn.vuejs.org/v2/guide/)
* **vue-router2.0** vue单页应用必备路由[《vue-router2.0官方文档》](http://cn.vuejs.org/v2/guide/)
* **vue-cli** 项目脚手架[《vue-cli实践》]()
* **vue-resource** 资源请求
* **vuex** 状态管理 [《vuex实践》]()
* **gulp** 项目打包本身webpack足够，此地引入只是为了方便同步服务器代码[《gulp+webpack入门》]()
* **ES6** [阮一峰《ECMAScript 6 入门》](http://es6.ruanyifeng.com/)
* **webpack** 主流前端打包框架
* **jquery** 前端开发最常用框架，方便一些vue不方便操作的dom操作 具体怎么引入参见[《vue-cli 该怎么引入jquery》]()
<!--more-->

### 项目安装

本地安装git工具，执行命令
        
        git clone http://123.56.155.118:8000\backend\merchant-web.git
或者用gitExtensions等工具拉下代码，切换到dev分支即可，前端开发代码不建议用idea或者eclipse等IDE工具，建议安装[sublime3](http://blog.csdn.net/u011272513/article/details/52088800), [Atom](www.atom.io)等开发工具

打开cmd命令框，切换到项目根目录，初始化项目

        $ npm  install
可能会灰常的慢，
由于众所周知的原因，建议使用国内[淘宝NPM镜像](http://npm.taobao.org/)
        
        $ npm install -g cnpm --registry=https://registry.npm.taobao.org

执行完以后，再次执行
`$ cnpm install`
会很快就会下载完依赖包

**一切准备就绪，现在开始启动项目**

项目启动命令配置在package.json里面
```
"dev": "node build/dev-server.js",//本地启动
"build": "node build/build.js",//打包编译
"deploy": "gulp deploy --env debug",//发布服务器
```
执行命令`npm run dev` 执行成功后，浏览器会自动打开进入到项目首页，至此本地已经可以进入开发流程啦。

### 代码梳理
代码目录如图 ![项目目录](http://omfrbtmyo.bkt.clouddn.com/merchant1.png)
其中build和config目录代码基本可以不用修改，唯一修改的地方也就是`config/index.js`中
```
    index: path.resolve(__dirname, '../dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '/newmerchant/',//根据实际项目名称修改
```
当然如果有能力写个脚本在打包时候自动修改更好了


### 代码详解
由于是面向非前端或者对前端模块化开发不熟的童鞋的文档，故介绍比较详细，废话不多说了，上代码

程序入口文件 main.js
```js
//采用es6语法，不熟的童鞋恶补去
import Vue from 'vue'
import VueResource from 'vue-resource'//官方不推荐了 不过不影响吾等小民使用
import jquery from 'jquery'//此处引入jquery 需要在某些文件配置的呢
import App from './App.vue'//pc和pad都会使用这个
import mApp from './mApp.vue'//移动端就是这个了
import router from './routers/'//全站路由都在这
import './filters/'//过滤器就是他了
import store from './store'//vuex状态管理器

Vue.config.debug = true//这个没啥鸟用
Vue.use(VueResource)
if (sessionStorage.getItem('tjd_token')) {//检测缓存中是否有token 验证登陆
  store.commit('isAuth', true)//如果有token给全局状态设置一下，在请求接口的时候会上送这个字段
}
if (navigator.userAgent.match(/Mobile/i) && !navigator.userAgent.match(/iPad/i)) {//这里简单检测下当前设备类型，选择加载不同的组件
  const app = new Vue({
    el: '#app',
    router: router,
    render: h => h(mApp)
  })
} else {
  const app = new Vue({
    el: '#app',
    router: router,
    render: h => h(App)
  })
}

```
App.vue

```html
<template>

<div id="app">
    <div v-if="!isMobile">
      <headline v-if="isAuth && isHeader"></headline>
      <router-view v-if="isAuth" class="view"></router-view>
      <login v-if="!isAuth"></login>
    </div>
    <div v-else>
      <router-view v-if="isAuth" class="view"></router-view>
      <mlogin v-else></mlogin>
    </div>
    <!-- <transition name="fade"> -->
      <loading v-show="isLoading" :content="loadingText"></loading>
    <!-- </transition> -->
    <transition name="fade">
      <modal v-show="isModal"
          @closemodal="closeModal()"
          :mode="modalinfo.mode"
          :title="modalinfo.title"
          :content="modalinfo.content"
          :width="modalinfo.width"
          :ok="modalinfo.ok"
          :cancel="modalinfo.cancel">
      </modal>
    </transition>
    <transition name="fade">
      <modal-input :modalInputShow="isModalType"></modal-input>
    </transition>
    <img id='tjdLogo' src='./assets/images/tjdLogo.png' style='display:none;'/>
    <img id='codeLogo' src='./assets/images/wechat.png' style='display:none;'/>
</div>

</template>

<script>

import store from './store' //如果使用到状态 必须引入store
import Modal from './components/Modal' //弹出模态框
import ModalInput from './components/ModalInput' //弹出带输入框的模态框
import Loading from './components/Loading' //loading框
import headline from './components/headline' //菜单
import Login from './views/login' //登陆页面
export default {
    store: store,
    components: {//引入的组件需要注册在这  当然也可以统一注册就不必在每个用到的地方都注册一边
        Login,
        Modal,
        Loading,
        ModalInput,
        headline
    },
    data() {//页面数据
        return {
            modalinfo: {//在这舒适化页面数据，键值对格式
                mode: 'alert',
                title: '',
                content: '',
                callback: null
              }
        }
    },
    computed: {//给子页面提供的公共方法
        isAuth() {//是否登陆
            return this.$store.getters.getIsAuth
        },
        isHeader(){//是否显示菜单头
            return this.$store.getters.getIsHeader
        },
        isMobile() {//是否是移动端
            return this.$store.getters.getIsMobile
        },
        isLoading(){//是否加载loading
            return this.$store.getters.getIsLoading
        },
        loadingText(){
            return this.$store.getters.getLoadingText
        },
        isModal(){//是否显示模态框
            return this.$store.getters.getModal
        },
        isModalType(){//模态框类型
            return this.$store.getters.getModalType
        }


    },
    methods: {
        showModal (mode, title, content, ok, cancel) {//封装一个公共的弹出模态框而已
          this.$set(this.modalinfo, 'mode', mode)
          this.$set(this.modalinfo, 'title', title)
          this.$set(this.modalinfo, 'content', content)
          this.$set(this.modalinfo, 'ok', ok)
          this.$set(this.modalinfo, 'cancel', cancel)
          store.commit('modal', true)
        },
        closeModal () {//关闭模态框
          store.commit('modal', false)
        }
    },
    mounted(){}
}

</script>
<style type="text/css">
//*****
</style>

```
## 由于历史原因，本项目所有样式采用原生css实现，并且每一个vue文件带着自己的css在style表情加入scoped可保证每个vue的样式不会相互污染，如果想要把样式合并在一个style文件中，建议用less或者stylus重构下，项目中不同页面重名的class样式类太多了，如果只是简单的复制到一个文件中会导致样式重叠覆盖的，还是用css框架重构下吧 ##

store.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

// 告诉 vue “使用” vuex
Vue.use(Vuex)

// 创建一个对象来保存应用启动时的初始状态
const state = {
  // TODO: 放置初始状态
  isAuth: false, // 是否登录
  isHeader:true,//是否显示头部，默认显示
  authToken: '', // ajax token
  account: {}, // 账户信息
  equipment:'',//设备类型
  modal: false, // 全局modal
  modalText:'',//弹出框提示语
  preloader: false, // preloader
  isMobile:false,//全局设备终端类型
  isLoading:false,
  modalType:0,//密码修改和资料修改
  loadingText:'查询中,请稍后',//loading框默认提示语、
}

// 创建一个对象存储一系列我们接下来要写的 mutation 函数
const mutations = {
    isAuth (state, status) {
        state.isAuth = status
    },
    isMobile(state,status) {
        state.isMobile = status
    },
    equipment(state,type){
        state.equipment = type
    },
    isHeader (state, isHeader) {
        state.isHeader = isHeader
    },
    authToken (state, token) {
        state.authToken = token
    },
    preloader (state, status) {
        state.preloader = status
    },
    modal (state, status) {
        state.modal = status
    },
    isLoading (state,status){
        state.isLoading = status
    },
    loadingText(state,loadingText){
        state.loadingText = loadingText
    },
    modalText(state,modalText){
        state.modalText = modalText
    },
    modalType(state,modalType){
        state.modalType = modalType
    }
}

const getters = {
  getIsAuth: state => { return state.isAuth },
  getIsHeader: state => { return state.isHeader },
  getEquipment: state => { return state.equipment },
  getAuthToken: state => { return state.authToken },
  getModal: state => { return state.modal },
  getPreloader: state => { return state.preloader },
  getIsMobile: state => { return state.isMobile },
  getIsLoading: state => { return state.isLoading },
  getLoadingText: state => { return state.loadingText },
  getModalText: state => { return state.modalText },
  getModalType: state => { return state.modalType },
}

// 整合初始状态和变更函数，我们就得到了我们所需的 store
// 至此，这个 store 就可以连接到我们的应用中
export default new Vuex.Store({
  state,
  getters,
  mutations
})

```

这里就是项目的全局状态，这些状态只会保存到内存中，如果刷新了浏览器所有状态都会丢失的，建议回话等信息要存一份到sessionStore中吧

状态在使用的时候按照以下方式即可，具体实现原理问[度娘](https://vuex.vuejs.org/zh-cn/)吧
```
 this.$store.commit('isAuth', true)
 this.$store.commit('isHeader', true)
 ```
 由于篇幅有限，先写这么多吧，如果项目启动中有任何问题，联系email:yuleiming@foxmail.com