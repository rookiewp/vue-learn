#### 只包含运行时版本（runtime-only）与 完整版（运行时+编译）（runtime-with-complier）的区别

1. 定义：
    - 完整版：同时包含编译器和运行时的版本。
    - 编译器：用来将模板字符串编译成为 JavaScript 渲染函数的代码。
    - 运行时：用来创建 Vue 实例、渲染并处理虚拟 DOM 等的代码。基本上就是除去编译器的其它一切。

2. 入口：
    - 完整版：src/platforms/web/entry-runtime-with-compiler.js
    - 只包含运行时：src/platforms/web/entry-runtime.js

3. 实现原理：
  完整版会重新定义`Vue.prototype.$mount`方法，用新定义的\$mount实现编译，最后用老的\$mount实现挂载。在runtime-only版本没有重新定义\$mount的过程，也就是没有编译的过程，直接调用老的$mount挂载。 
    ```js
    // 保留老的$mount方法
    const mount = Vue.prototype.$mount
    /* 
      1：在新定义的$mount方法中：判断是否定义了render方法，
      如果没有，则将template或el编译成render函数
      2：在同时定义了template和el的情况下，template的优先级大于el:
        new Vue({
          el: '#app',
          template: '<div id="wp">{{ this.message }}</div>',
          data: {
            message: 'hello world'
          }
        }) 
      最终渲染出来的是：<div id="wp">{{ this.message }}</div>，
      而不是：<div id="app">{{ this.message }}</div>
    */
    Vue.prototype.$mount = function (
      el?: string | Element,
      hydrating?: boolean
    ): Component {
      // 编译过程
      ....
      // 最后调用老的mount挂载
      return mount.call(this, el, hydrating)
    }
    ```
  4. 单文件组件(SFC)经过`vue-loader`处理后，是编译过的，是不需要runtime-with-complier版本的
  



    
  