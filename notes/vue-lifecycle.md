**1、 ```vue``` 实例在被创建之前都要经过一系列的初始化过程,这个过程就是 ```vue``` 的生命周期。具体流程如图所示**

<div align="center">
  <img src="https://cn.vuejs.org/images/lifecycle.png" width="500" hegiht="1266.25" align="center"/>
</div>

可以看到在 ```vue``` 整个的生命周期中会有很多钩子函数被提供给我们以便在 ```vue``` 生命周期不同的时刻进行操作, 那么先列出所有的钩子函数，然后我们再一一详解:
+ **beforeCreate**
+ **created**
+ **beforeMount**
+ **mounted**
+ **beforeUpdate**
+ **updated**
+ **beforeDestroy**
+ **destroyed**

**2、在 ```beforeCreate``` 和 ```created``` 钩子函数间的生命周期**

在这两个生命周期之间，进行初始化事件，进行**数据（ ```data``` ）**的观测，可以看到在 ```created``` 的时候数据已经和 ```data``` 属性进行绑定（放在 ```data``` 中的属性值发生改变的同时，视图也会改变），完成了 ```data``` 数据的初始化，但 ```el``` 没有初始化，此时还没有 ```el``` 选项，即 ```dom``` 还未被渲染，亦不能被引用。

**3、在 ```created``` 和 ```beforeMount``` 钩子函数间的生命周期**

在这一阶段 ```vue``` 处理的事情比较多，首先会判断实例对象是否有 ```el``` 选项。如果有的话就继续向下编译，如果没有 ```el``` 选项，则停止编译，也就意味着停止了生命周期，直到在该 ```vue``` 实例上调用 ```vm.$mount(el)``` 。然后，我们往下看 ```template``` 参数选项的有无对生命周期的影响，结果是：
+ 如果 ```vue``` 实例对象中有 ```template``` 参数选项，则将其作为模板编译成 ```render``` 函数
+ 如果没有 ```template``` 选项，则将外部 ```HTML``` 作为模板编译

**4、在 ```beforeMount```和 ```mounted``` 钩子函数间的生命周期**

此阶段是给 ```vue``` 实例对象添加 ```el``` 成员，并且替换掉挂在的 ```DOM``` 元素。此时完成了 ```el``` 和 ```data``` 初始化 。

**5、在 ```mounted``` 钩子函数的生命周期**

在 ```mounted``` 之前```<el></el>```中还是通过 ```{{message}}``` 进行占位的，此时挂载到页面上的数据还是 ```JavaScript``` 中的虚拟 ```DOM``` 形式存在的。在 ```mounted``` 之后可以看到 ```<el></el>``` 中的内容发生了变化，完成了 ```el``` 和 ```data``` 的挂载。

**6、在 ```beforeUpdate``` 和 ```updated``` 钩子函数间的生命周期**

当 ```vue``` 发现 ```data``` 中的数据发生了改变，会触发对应组件的重新渲染，先后调用 ```beforeUpdate``` 和 ```updated``` 钩子函数。

**7、在 ```beforeDestroy``` 和 ```destroyed``` 钩子函数间的生命周期**

```beforeDestroy``` 钩子函数在实例销毁之前调用，在这一步，实例仍然完全可用。
```destroyed``` 钩子函数在 ```Vue``` 实例销毁后调用，调用后，```Vue``` 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

**8、 ```Vue``` 生命周期总结**

>```beforeCreated``` 函数： ```el``` 和 ```data``` 并未初始化 。
>
>```created``` 函数：完成了 ```data``` 数据的初始化，但是 ```el``` 并未初始化。此阶段可以进行数据的请求等操作，数据修改后更新dom的操作可以放在 ```this.$nextTick``` 回调中进行。
>
>```beforeMount``` 函数：完成了 ```el``` 和 ```data``` 初始化 。
>
>```mounted``` 函数 ：完成数据 ```data``` 和 ```dom``` 元素的挂载。
>
>```updated``` 函数：数据 ```data``` 更新后，对 ```dom```结构进行更新。
>
>初始化组件时，仅执行了 ```beforeCreate/Created/beforeMount/mounted``` 四个钩子函数。
>
>当改变 ```data``` 中定义的变量（响应式变量）时，会执行 ```beforeUpdate/updated``` 钩子函数。
>
>初始化和销毁时的生命钩子函数均只会执行一次， ```beforeUpdate/updated``` 可多次执行。
>
>在页面刷新或者刚开始加载时，我们能发现 ```el``` 包裹的内容可能还是 ```{{message}}``` ，这里就是应用的 ```Virtual DOM```（虚拟Dom）技术，先把坑占住了。到后面 ```mounted``` 挂载的时候再把值渲染进去。

>仅当子组件完成挂载后，父组件才会挂载。

>当子组件完成挂载后，父组件会主动执行一次 ```beforeUpdate/updated``` 钩子函数（仅首次）。

>父子组件在 ```data``` 变化中是分别监控的，但是在更新 ```props``` 中的数据是关联的（可实践）。

>销毁父组件时，先将子组件销毁后才会销毁父组件。