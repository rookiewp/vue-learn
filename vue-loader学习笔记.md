1. vue-loader使用：
    ```js
    module: {
      rules: [
        {
          test: /\.vue$/,
          loader: 'vue-loader',
        },
      ]
    },
    plugins: [
      new VueLoaderPlugin(),
    ]
    ```
2. vue-loader首先将单文件组件(SFC,也就是.vue文件)转为：
    ```js
      import { render, staticRenderFns } from "./App.vue?vue&type=template&id=7ba5bd90&"
      import script from "./App.vue?vue&type=script&lang=js&"
      export * from "./App.vue?vue&type=script&lang=js&"
      import style0 from "./App.vue?vue&type=style&index=0&lang=css&"

      /* normalize component */
      import normalizer from "!../node_modules/vue-loader/lib/runtime/componentNormalizer.js"
      var component = normalizer(
        script,
        render,
        staticRenderFns,
        false,
        null,
        null,
        null
      )
      component.options.__file = "src/App.vue"
      export default component.exports
    ```

3. 第二步对应的内容作为一个`module`，下面的三个`import`作为依赖，将生成三个`moulde`：
    ```js
      import { render, staticRenderFns } from "./App.vue?vue&type=template&id=7ba5bd90&"
      import script from "./App.vue?vue&type=script&lang=js&"
      import style0 from "./App.vue?vue&type=style&index=0&lang=css&"
    ```
4.  三个`module`分别为：
   "./App.vue?vue&type=template&id=7ba5bd90&": \<template>中的内容
      ```js
      <template>
        <div id="app1">
          <HelloWorld msg="Welcome to Your Vue.js App"/>
        </div>
      </template>
      ```

     "./App.vue?vue&type=script&lang=js&"：\<script>中的内容
      ```js
        <script>
        import HelloWorld from './components/HelloWorld.vue'
        export default {
          name: 'App',
          data: function() {
            return {
              age: 21,
            }
          },
          components: {
            HelloWorld
          }
        }
        </script>
      ```
    "./App.vue?vue&type=style&index=0&lang=css&"：\<style>中的内容
    ```js
    <style>
      #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
      }
    </style>
    ```

5. `import { render, staticRenderFns } from "./App.vue?vue&type=template&"`，其中`render`, `staticRenderFns`这两个方法从哪来呢？答案是`VueLoaderPlugin`


6. `VueLoaderPlugin`：会在`webpack`的`modules.rules`中加上转换template的`templateLoader`，在resolver到`"./App.vue?vue&type=template&"`时，会调用`templateLoader`，将:
    ```js
      <div id="app1">
        <HelloWorld msg="Welcome to Your Vue.js App"/>
      </div>
    ```
    转换为:
    ```js
    var render = function() {
      var _vm = this
      var _h = _vm.$createElement
      var _c = _vm._self._c || _h
      return _c(
        "div",
        { attrs: { id: "app1" } },
        [_c("HelloWorld", { attrs: { msg: "Welcome to Your Vue.js App" } })],
        1
      )
    }
    var staticRenderFns = []
    render._withStripped = true
    export { render, staticRenderFns }
    ```

