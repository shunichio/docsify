## Vue-Socket.io

```javascript
// main.js
import VueSocketIO from 'vue-socket.io';
Vue.use(
  new VueSocketIO({
    debug: true, // 开发模式
    connection: '/', // websocket地址
    options: {
      path: '', // 地址路径
      query: {
        username: '' // 请求参数
      }
    },
    vuex: {
      store,
      actionPrefix: 'SOCKET_',
      mutationPrefix: 'SOCKET_'
    }
  })
);
```

```html
<template>
  <div class="vue-socket_io">
    <a-radio-group v-model="switchValue" @change="changeRadio">
      <a-radio :value="true">开</a-radio>
      <a-radio :value="false">关</a-radio>
    </a-radio-group>
  </div>
</template>
```

```javascript
export default {
  name: 'vue-socket_io',
  data() {
    return {
      switchValue: false
    }
  },
  // 监听的后台事件
  sockets: {
    // 连接成功后，可以进行的操作
    connect() {
      console.log('连接-----> 成功')
    },
    // 重新连接成功后，可以进行的操作
    reconnect() {
      console.log('重新连接-----> 成功')
    },
    // 断开连接成功后，可以进行的操作
    disconnect() {
      console.log('断开连接-----> 成功')
    },
    messageEvent(result) {
      if (result && result.length) {
        console.log('被扫描的标签：--------> ', result)
        this.scannedLabel = result
      }
    },
    openSerialPort(result) {
      console.log('打开串口-------->', result);

      if (result.code) {
        this.serialPort = result.data;
        return false;
      } else {
        this.$message.error(result.msg);
        this.switchValue = false;
      }
    },
    closeSerialPort(result) {
      console.log('关闭串口---------->', result);

      if (!result.code) {
        this.$message.error(result.msg);
        this.switchValue = true;
      }
    }
  },
  destroyed() {
    const self = this
    // 向后端提交事件
    this.$socket.emit('closeSerialPort', {
      name: self.serialPort
    })
    this.$socket.emit('disconnect')
  },
  methods: {
    changeRadio(e) {
      const selectedVal = e.target.value
      const self = this
      if (selectedVal) {
        console.log('开启串口')
        this.$socket.emit('openSerialPort')
      } else {
        console.log('关闭串口')
        this.$socket.emit('closeSerialPort', { name: self.serialPort })
      }
    }
  }
}
</script>
```
