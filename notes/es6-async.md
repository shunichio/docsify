#### 含义

**1. ```async``` 表示函数里有异步操作， ```await``` 表示紧跟在后面的表达式需要等待结果。**

**2. ```async``` 函数的 ```await``` 命令后面，可以是 ```Promise``` 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。如果如果不是 ```Promise``` 对象，会被转成一个立即```resolve```的 ```Promise``` 对象。**

**3. ```await``` 命令必须在 ```async``` 函数内部， ```async``` 函数完全可以看作多个异步操作，包装成的一个 ```Promise``` 对象，而 ```await``` 命令就是内部 ```then``` 命令的语法糖。**

**4. ```async``` 函数内部 ```return``` 语句返回的值是 ```Promise``` 对象，会成为```then```方法回调函数的参数。**

**5. ```async``` 函数内部抛出错误，会导致返回的 ```Promise``` 对象变为reject状态。抛出的错误对象会被```catch```方法回调函数接收到。**


#### 例子
```javascript
// 定义函数getStockPriceByName()，声明内部包含多个异步操作
async function getStockPriceByName(name) {
  // 异步函数继发执行
  const symbol = await getStockSymbol(name);	// 异步函数getStockSymbol()
  const stockPrice = await getStockPrice(symbol);	// 异步函数getStockPrice()
  return stockPrice;	// 返回价格，Promise对象
}

// 执行函数getStockPriceByName(),得到结果
getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```

```javascript
// 定义函数logInOrder()，声明内部有多个异步函数
async function logInOrder(urls) {
  // 对传入的Url数组进行遍历
  const textPromises = urls.map(async url => {	// 定义匿名函数，内部异步请求函数fetch()并发执行，遍历时不必按照顺序继发请求。该匿名函数为异步函数，后续代码（for循环）会继续执行
    const response = await fetch(url);	// 请求结果依次放入数组中
    return response.text();
  });

  for (const textPromise of textPromises) {
    // 等待结果，按次序输出结果
    console.log(await textPromise);
  }
}
```


