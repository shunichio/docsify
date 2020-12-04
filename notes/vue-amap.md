**1、使用 ```npm``` 安装 ```vue-map``` 框架。**

```npm install vue-amap --save```

**2、脚本初始化，在 ```main.js``` 中调用 ```initAMapApiLoader``` 即可。***
```javascript
import VueAMap from 'vue-amap'

Vue.use(VueAMap);
VueAMap.initAMapApiLoader({
  key: 'YOUR_KEY',
  plugin: ['AMap.Geolocation', 'AMap.Geocoder'],  //根据需要与高德地图官网API，自行安装插件。实现定位服务，安装Geolocation插件与Geocoder插件即可
  v: '1.4.4'	// vue-amap默认调用高德地图1.4.4版本的API
});
```

**3、将定位服务封装在单独的```js```文件中，因为实例化的操作直接基于高德地图的 ```sdk``` 完成，所以需要引入 ```lazyAMapApiLoaderInstance```**
```javascript
import { lazyAMapApiLoaderInstance } from 'vue-amap'

export const getCity = () => {
  return new Promise((resolve, reject) => {
    lazyAMapApiLoaderInstance.load().then(() => {
      // 取消eslint检查，否则AMap类将报错
      /* eslint-disable */
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,
        timeout: 60000
      })
      geolocation.getCityInfo((status, result) => {		// 通过IP获取城市名称等信息
        if (status === 'complete' && result.info === 'SUCCESS') {		// 获取城市名称成功
          resolve(`${result.country}-${result.province}-${result.city}`)
        } else {	// 获取城市名称失败
          reject(result.message)
        }
      })
    })
  })
}

export const getPosition = () => {
  return new Promise((resolve, reject) => {
    lazyAMapApiLoaderInstance.load().then(() => {
      // 取消eslint检查，否则AMap类将报错
      /* eslint-disable */
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,
        timeout: 60000
      })
      // 获取当前位置经纬度
      geolocation.getCurrentPosition((status, result) => {
        if (status === 'complete' && result.info === 'SUCCESS') {	// 获取经纬度信息成功
          // 经纬度数组
          const lnglatXY = [result.position.lng, result.position.lat]
          const geocoder = new AMap.Geocoder({
            radius: 1000,
            extensions: 'all'
          })
          geocoder.getAddress(lnglatXY, function(status2, result2) {	// 转换为位置文字信息
            if (status2 === 'complete' && result2.info === 'OK') {	// 位置文字信息转换成功
              resolve(result2.regeocode.formattedAddress)
            } else {	// 位置文字信息转换失败
              reject(result2.message)
            }
          })
        } else {	// 获取经纬度信息失败
          reject(result.message)
        }
      })
    })
  })
}
```