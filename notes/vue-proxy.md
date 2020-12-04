```javascript
/**
 * 判断参数是否为对象
 * @param {object} obj
 */
const isObj = obj => {
  return Object.prototype.toString.call(obj) === '[object Object]'
}

/**
 * 可以对 key 产生影响的函数，本示例中的 logger 函数
 */
const efftctStack = []

// Map 对象
// {
//   target:{
//     key: [fn1,fn2,fn3,...fn]
//   }
// }
const targetMap = new Map()

const track = (target, key) => {
  const rxFn = efftctStack[efftctStack.length - 1]
  if (!rxFn) return // 如果没有能对属性产生影响的函数，就返回，停止依赖收集

  let targetKeyMap = targetMap.get(target)
  if (!targetKeyMap) {
    targetKeyMap = new Map()
    targetMap.set(target, targetKeyMap)
  }
  let keySet = targetKeyMap.get(key)
  if (!keySet) {
    keySet = new Set()
    targetKeyMap.set(key, keySet)
  }
  keySet.add(rxFn)
}

const trigger = (target, key, value) => {
  let targetKeyMap = targetMap.get(target)
  if (!targetKeyMap) return
  let keySet = targetKeyMap.get(key)
  if (!keySet) return
  keySet.forEach(fn => {
    fn() // logger()
  })
}

const reactive = originObj => {
  if (!isObj(originObj)) return originObj

  const proxyObj = new Proxy(originObj, {
    get(target, key, receiver) {
      let res = Reflect.get(target, key, receiver)
      console.log('get :>> ', key)
      track(target, key)
      return isObj(res) ? reactive(res) : res
    },
    set(target, key, value, receiver) {
      let res = Reflect.set(target, key, value, receiver)
      trigger(target, key, value)
      return res
    },
  })

  return proxyObj
}

const effect = fn => {
  const rxFn = function () {
    try {
      efftctStack.push(rxFn)
      return fn()
    } finally {
      efftctStack.pop()
    }
  }
  rxFn()
}

const logger = params => {
  console.log('信息打印 :>> ', params)
}

const demoData = reactive({ a: 0 })

effect(() => logger(demoData.a))

setInterval(() => {
  demoData.a++
}, 1000)
```
