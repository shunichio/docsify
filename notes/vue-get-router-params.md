我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 ```User``` 组件，对于所有 ```ID``` 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 ```vue-router``` 的路由路径中使用『动态路径参数』（```dynamic segment```）来达到这个效果：

```javascript
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

现在，像 ```/user/foo``` 和 ```/user/bar``` 都将映射到相同的路由。

当使用路由参数时，例如从 ```/user/foo``` 导航到 ```/user/bar``` ，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。**不过，这也意味着组件的生命周期钩子不会再被调用。**

复用组件时，想对**路由参数的变化**作出响应的话，可以简单地 watch（监测变化） $route 对象：

```javascript
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
    	console.log(to)
    	console.log(from)
      // 对路由变化作出响应...
    }
  }
}
```
或者使用 2.2 中引入的 beforeRouteUpdate 守卫，在组件中，添加代码：

```javascript
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    console.log(to)
    console.log(from)
    // don't forget to call next()
  }
}
```