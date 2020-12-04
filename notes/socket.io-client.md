## Socket.io

```html
<template>
  <div class="socket_io">
    <a-radio-group
      v-model="switchValue"
      class="radio-wrapper"
      @change="changeRadio"
    >
      <a-radio :value="true">开</a-radio>
      <a-radio :value="false">关</a-radio>
    </a-radio-group>
  </div>
</template>
```

```javascript
import SocketIO from 'socket.io-client';

export default {
  name: 'socket_io',
  data() {
    return {
      switchValue: false
    };
  },
  mounted() {
    this._initSocketIo();
  },
  destroyed() {
    const self = this;
    this.socket.emit('closeSerialPort', {
      name: self.serialPort
    });
    this.socket.emit('disconnect');
  },
  methods: {
    _initSocketIo() {
      this.socket = SocketIO.connect('/', {
        path:'/my-first-ws-app', // 必须以“/”开头
        query: {
          username: 'mySocketIoApplication'
        }
      });

      this.socket.on('connect', () => {
        console.log('连接-----> 成功');
      });

      this.socket.on('reconnect', () => {
        console.log('重新连接-----> 成功');
      });

      this.socket.on('disconnect', () => {
        console.log('断开连接-----> 成功');
      });

      this.socket.on('messageEvent', result => {
        if (result && result.length) {
          console.log('被扫描的标签：--------> ', result);
          this.scannedLabel = result;
        }
      });

      this.socket.on('openSerialPort', result => {
        console.log('打开串口-------->', result);

        if (result.code) {
          this.serialPort = result.data;
          return false;
        } else {
          this.$message.error(result.msg);
          this.switchValue = false;
        }
      });

      this.socket.on('closeSerialPort', result => {
        console.log('关闭串口---------->', result);

        if (!result.code) {
          this.$message.error(result.msg);
          this.switchValue = true;
        }
      });
    },
    changeRadio(e) {
      const selectedVal = e.target.value;
      const self = this;
      if (selectedVal) {
        console.log('开启串口');
        this.socket.emit('openSerialPort');
      } else {
        console.log('关闭串口');
        this.socket.emit('closeSerialPort', { name: self.serialPort });
      }
    }
  }
};
```
