###### vue-cli3的使用

1. `npm install -g @vue/cli (一次安装) node-sass (需要单独处理)`
2. `vue create projName` 创建项目
3. `npm run serve` ：开发环境搭建
4. `npm run build`：生产环境搭建
5. `npm run lint`：代码检测工具（会自动修正）

```
<template> -html代码，最多可以包含一个
<script> -js代码，最多可以包含一个
<style> -css代码，最多可以包含一个
style 标签加上 scoped 属性。css局部生效
style 标签讲视 lang="scss"，支持scss
```



###### 单文件组件：

​		在 `App.vue` 文件里写组件内容，若有多个单文件组件，则在 `./src/components/` 目录下创建组件，再进行导入导出。

```vue
<template>
  <div>
    hello vue
    <input type="text" ref="mytext">
    <button @click="handleAdd">按钮</button>
    <ul>
      <li v-for="item in dataList" :key="item">{{item}}</li>
    </ul>
    <navbar></navbar>
    <sidebar></sidebar>
  </div>
</template>

<script>
//导入
import navbar from './components/Navbar'
import sidebar from './components/Sidebar'
import Vue from 'vue'

//注册为全局组件
Vue.component('navbar', navbar)
Vue.component('sidebar', sidebar)

//导出
export default {
  data () {
    return {
      dataList: []
    }
  },
  methods: {
    handleAdd () {
      this.dataList.push(this.$refs.mytext.value)
    }
  }
}
</script>
```

