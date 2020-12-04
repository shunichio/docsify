**1、导入本地JSON数据，并获得其中的对象值**

```javascript
// 获取JSON数据
const appData = require('../data.json')

// 得到对象值
const seller = appData.seller
const goods = appData.goods
const ratings = appData.ratings
```

**2、在devServer中添加相应代码，封装API，便于前端调用**

```javascript
before(app) {
  app.get('/api/seller', function(req, res) {
    res.json({
      errno: 0,
      data: seller
    })
  });
  app.get('/api/goods', function(req, res) {
    res.json({
      errno: 0,
      data: goods
    })
  });
  app.get('/api/ratings', function(req, res) {
    res.json({
      errno: 0,
      data: ratings
    })
  });
}
```

