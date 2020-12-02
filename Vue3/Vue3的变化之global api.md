## Vue3的变化

### Global API 

#### createApp

`Vue 2.x` 中有许多全局API和配置项可以全局修改Vue的行为。

例如声明一个全局组件：

```javascript
Vue.component('button-counter', {
  data: () => ({
    count: 0
  }),
  template: '<button @click="count++">Clicked {{ count }} times.</button>'
})
```

例如声明一个全局指令：

```javascript
Vue.directive('focus', {
  inserted: el => el.focus()
})
```

虽然这种方式提供了一定的便利，但同样导致了许多问题。严格意义上讲，`Vue 2` 没有“app”的概念，我们定义的应用只是通过`new Vue()` 创建的根Vue实例。每一个从相同Vue 构造函数创建的根实例会共享相同的全局配置。这就导致了：

- 在测试的时候，全局配置很容易意外地污染其他测试用例。用户需要小心地存储原始全局配置，并在每次测试后恢复 (例如重置 `Vue.config.errorHandler`)。有些 API 像 `Vue.use` 以及 `Vue.mixin` 甚至连恢复效果的方法都没有，这使得涉及插件的测试特别棘手。这就导致vue-test-utils 必须提供一个特殊的 API `createLocalVue` 来处理此问题

  ```javascript
  import { createLocalVue, mount } from '@vue/test-utils'
  
  // create an extended `Vue` constructor
  const localVue = createLocalVue()
  
  // install a plugin “globally” on the “local” Vue constructor
  localVue.use(MyPlugin)
  
  // pass the `localVue` to the mount options
  mount(Component, { localVue })
  ```

- 全局配置使得在同一页面上的多个“app”之间共享同一个 Vue 副本非常困难，而不能提供不同全局配置

  ```javascript
  // this affects both root instances
  Vue.mixin({
    /* ... */
  })
  
  const app1 = new Vue({ el: '#app-1' })
  const app2 = new Vue({ el: '#app-2' })
  ```

为了避免以上这些问题，Vue 3中提供了一个新的全局API：`createApp`

调用`createApp` 会返回一个app 实例，(一个在Vue 3中的新实例)。

```javascript
import { createApp } from 'vue'

const app = createApp({})
```

这个app的实例会暴露之前全局api的子集。所有可以修改Vue行为的api都移到了app的实例上面。

以下表格显示了当前全局api和app实例上相对应的方法：

![](C:\Users\user.DESKTOP-R005VRA\Desktop\my-notes\images\微信截图_20201201172055.png)

所有其他不全局修改行为的全局API都改为以exports的方式，参考文档[Global API Treeshaking](https://v3.cn.vuejs.org/guide/migration/global-api-treeshaking.html#_2-x-%E8%AF%AD%E6%B3%95)

`Vue.prototype` 被`config.globalProperties`取代

在Vue 2中，`Vue.prototype`通常用来设置一些可以在组件中全局访问的变量和方法。

而Vue 3通过`config.globalProperties` 提供了等价的功能。可以在组件中通过`this` 访问

```javascript
// before - Vue 2
Vue.prototype.$http = () => {}

// after - Vue 3
const app = Vue.createApp({})
app.config.globalProperties.$http = () => {}
```

#### 挂载App实例

在用`createApp(/* options */)` 初始化后，app实例可以通过`app.mount(domTarget)` 挂载。

```javascript
import { createApp } from 'vue'
import MyApp from './MyApp.vue'

const app = createApp(MyApp)
app.mount('#app')
```

#### Provide / Inject

与在Vue 2.x 根实例的`provide` 选项类似，Vue 3 app 实例也可以提供可以被任何在app中的组件注入的依赖。

```javascript
// in the entry
app.provide('guide', 'Vue 3 Guide')

// in a child component
export default {
  inject: {
    book: {
      from: 'guide'
    }
  },
  template: `<div>{{ book }}</div>`
}
```



### Global API TreeShaking

#### 2.x 语法

假如你曾在Vue中手动修改DOM，你可能是通过以下这种形式：

```javascript
import Vue from 'vue'

Vue.nextTick(() => {
    // something DOM-related
})
```

假如你在写测试用例的过程中涉及测试异步组件，你可能会这样写：

```javascript
import { shallowMount } from '@vue/test-utils'
import { MyComponent } from './MyComponent.vue'

test('an async feature', async () => {
  const wrapper = shallowMount(MyComponent)

  // execute some DOM-related tasks

  await wrapper.vm.$nextTick()

  // run your assertions
})
```

`Vue.nextTick()` 是直接暴露在Vue 对象上的一个全局API——事实上，实例方法`$nextTick()` 只是一个`Vue.nextTick()` 的一个简单包装，为方便起见，回调中的 `this` 上下文会自动绑定当前实例。

但假如你未曾遇到过要手动操作DOM的场景，或是你不曾测试过异步组件或只是单纯出于其他原因更加偏爱与使用`window.setTimeout()` 来代替？这种情况下，`nextTick()` 的相关代码就会是`dead code` (指从未被使用过的代码)。 `dead code` 对于我们的应用来说是多余的，特别是对于客户端来讲，每一个字节都会有影响。

像 `webpack` 这样的模块打包工具支持`tree-shaking` ，可以解决`dead code` 的问题。不幸的是，由于当前Vue版本的代码组织方式，像`Vue.nextTick()`的全局API是不可被`tree-shaking` 的，所以不管你在应用中有没有使用它，都会被打包进最后的代码包中。

#### 3.x 语法

在Vue 3中，全局API和内部API已经被重构成支持`tree-shaking`的新形式。因此，全局API现在只能通过ES Module的导出的形式被访问。例如之前的例子可以用一下的方式实现：

```javascript
import { nextTick } from 'vue'

nextTick(() => {
  // something DOM-related
})

import { shallowMount } from '@vue/test-utils'
import { MyComponent } from './MyComponent.vue'
import { nextTick } from 'vue'

test('an async feature', async () => {
  const wrapper = shallowMount(MyComponent)

  // execute some DOM-related tasks

  await nextTick()

  // run your assertions
})
```

在当前Vue 3中直接调用`Vue.nextTick()` 会返回`undefined is not a function` 错误。

由于这种变化，在使用支持`tree-shaking` 的模块打包工具打包后，没用被使用过的全局API会被剔除，这样可以产出更加小的代码包。