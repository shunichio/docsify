### Fragment（碎片）

`vue3` 支持 碎片（ `fragment` ）， 但是仍然推荐单根组件，因为父组件传入的一些非 `props` 会出问题，没有地方可以绑定。这种情况下，要求开发者明确定义属性应该分布在哪里。

```html
<!-- Layout.vue -->
<template>
  <header>...</header>
  <main v-bind="$attrs">...</main>
  <footer>...</footer>
</template>
```

### 自定义指令

如果只想在 `mounted` 和 `updated` 执行相同逻辑，第二个参数可以直接传递一个函数用在组件上时，如果组件是 **单根** 可以正常使用非单根组件有问题，没有执行相关逻辑。

```js
app.directive('myDire', {
  beforeMount() {},
  mounted(el, binding) {
    // binding arg 参数 value 参数的值 modifiers
    //用到的修饰符 oldValue 上一次接受到的值 instance 组件 dir 指令生命周期相关
    console.log('mounted', el, binding)
  },
  beforeUpdate() {},
  updated(el, binding) {
    console.log('updated', el, binding)
  },
  beforeUnmount() {},
  unmounted() {},
})
app.mount('#app')
```

### 穿梭机

把元素渲染到指定的节点中，而不是深度嵌套，但是仍然是父组件的逻辑子组件

```js
<teleport to="body">modal-button</teleport>
```

### render（渲染）

`v-model` 指令扩展为 `modelValue` 和 `onUpdate:modelValue` 在模板编译过程中，我们必须自己提供这些 `prop`

```js
render() {
  return Vue.h('SomeComponent', {
    'modelValue': this.modelValue,
    'onUpdate:modelValue': value => this.$emit('update:modelValue', value),
    'onClick':{
      'handle':function (){},
      'passive':true,
      'once':true// 修饰符
  }
})
```

### slots（插槽）

`$slots` 访问静态插槽每个插槽都是 `Vnode` 数组

```js
// `<div><slot :text="message"></slot></div>`
return Vue.h(
  'div',
  {},
  this.$slots.default({
    text: this.message,
  })
)
```

### plugins（插件）

通常向 `Vue` 添加全局级功能。它可以是公开 `install()` 方法的 `object` ，也可以是 `function`。

接收的参数变化了，原来的 `new Vue()` 变为 用户创建的实例 `app`， `options` 没变。往 `app.config.globalProperties` 上面加属性，事件等，所有实例均可访问

### setup 使用

`setUp` 作为选项，需要返回一个对象，否则会报错。 接受参数为 `props` 与 `context`， `context` 包含 `attrs` 、 `slots` 和 `emit`。

```js
setup(props, context){
  return { }
}

// 或者

setup(props,{ attrs, slots, emit }){
  return { }
}
```

`context` 本身不是响应式的，但是传递的值是 `proxy` 响应式的。
`setup` 的返回值可以提供给**计算属性**、**方法**、**生命周期钩子**、**组件的模板**等等使用，它返回值可以是一个 **_函数_**，作为 `render`

### 响应式

`ref` **_单个原始类型_** 响应式，需要访问到 `value`

```js
const count = ref(0)
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

`reactive` **_对象_** 每一个值响应式，对象会自动展开 `value`， 数组，Map 等内部的值不会自动展开，需要自己访问 `value`

```js
const obj = reactive({ count: 0 })
```

```js
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// 这里需要 .value
console.log(map.get('count').value)
```

`toRefs` 响应式对象解构

```js
import { reactive, toRefs } from 'vue'

const book = reactive({
  author: 'Vue Team',
  year: '2020',
  title: 'Vue 3 Guide',
  description: 'You are reading this book right now ;)',
  price: 'free',
})

let { author, title } = toRefs(book)

title.value = 'Vue 3 Detailed Guide' // 我们需要使用 .value 作为标题，现在是 ref
console.log(book.title) // 'Vue 3 Detailed Guide'
```

`computed` 使用时，也不会自动展开。

`watchEffect` 为了根据 **_反应状态_** 自动应用和重新应用副作用，我们可以使用 `watchEffect` 方法。它 **_立即执行_** 传入的一个 **_函数_** ，同时响应式追踪其 **_依赖_** ，并在其依赖变更时重新运行该函数。最终它 **_返回一个移除监听的函数_** ，可以手动清除监听，否则跟随组件生命周期，传入的函数会在初始化执行一次。

侦听副作用传入的函数可以接收一个 `onInvalidate` 函数作入参，用来注册清理失效时的回调。当 1、副作用即将重新执行时；2、侦听器被停止 (如果在 setup() 或生命周期钩子函数中使用了 watchEffect，则在组件卸载时)时，这个失效回调会被触发。因此，清除函数第一次不会执行而是进行注册。

```js
const data = ref(null)
watchEffect(async onInvalidate => {
  onInvalidate(() => {...}) // 我们在 Promise 解析之前注册清除函数
  data.value = await fetchData(props.id)
})
```

> 异步函数都会隐式地返回一个 `Promise` ，清理函数必须要在 `Promise` 被 `resolve` 之前被注册。

`watch`与 `watchEffect` 比较:

1. 可以懒执行
2. 更具体说明重新运行
3. 访问变化前后的值

```js
// 侦听一个 getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)

// 直接侦听ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})

// 侦听多个数据源
const fooRef = ref('AAAAA')
const barRef = ref('BBBBB')
watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* ... */
})
```

### 生命周期 与 `setup` 对应表

|    生命周期     |       setup       |
| :-------------: | :---------------: |
|  beforeCreate   |   Not needed\*    |
|     created     |   Not needed\*    |
|   beforeMount   |   onBeforeMount   |
|     mounted     |     onMounted     |
|  beforeUpdate   |  onBeforeUpdate   |
|     updated     |     onUpdated     |
|  beforeUnmount  |  onBeforeUnmount  |
|    unmounted    |    onUnmounted    |
|  errorCaptured  |  onErrorCaptured  |
|  renderTracked  |  onRenderTracked  |
| renderTriggered | onRenderTriggered |

### provide/inject

### 函数组件

### global API

|           变化前           |           变化后           |
| :------------------------: | :------------------------: |
|         Vue.config         |         app.config         |
|  Vue.config.productionTip  |          removed           |
| Vue.config.ignoredElements | app.config.isCustomElement |
|       Vue.component        |       app.component        |
|       Vue.directive        |       app.directive        |
|         Vue.mixin          |         app.mixin          |
|          Vue.use           |          app.use           |

`nextTick` 以及所有的 `api` 不再挂载到全局，而是模块导出

```js
import { nextTick } from 'vue'
```

### 一些变化

#### v-model

用于自定义组件时，`v-model`、`prop` 和 **_事件默认名称_** 已更改

- prop：value -> modelValue

- event：input -> update:modelValue

`v-bind` 的 `.sync` 修饰符和组件的 `model` 选项已移除，可用 `v-model` 作为代替

若需要更改 `model` 名称，而不是更改组件内的 `model` 选项，那么现在我们可以将一个 `argument` 传递给 `model`

```html
<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />

<!-- 简写: -->
<ChildComponent v-model:title="pageTitle" />
```

现在可以在同一个组件上使用多个 v-model 进行双向绑定

```html
<ChildComponent
  :title="pageTitle"
  @update:title="pageTitle = $event"
  :content="pageContent"
  @update:content="pageContent = $event"
/>

<!-- 简写： -->
<ChildComponent v-model:title="pageTitle" v-model:content="pageContent" />
```

现在可以自定义 `v-model` 修饰符。

### API Reference（详情请参考文档）
