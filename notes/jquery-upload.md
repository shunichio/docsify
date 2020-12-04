```FormData``` 对象，可以使用一系列的键值对来模拟一个完整的表单，然后使用 ```XMLHttpRequest``` 发送这个“表单”。但上传文件部分只有底层的 ```XMLHttpRequest``` 对象发送上传请求，那么怎么通过 ```jQuery``` 的 ```Ajax``` 上传呢？


### 使用form表单初始化FormData对象方式上传文件

+	HTML代码

```html
<form id="uploadForm" enctype="multipart/form-data">
  <input id="file" type="file" name="file"/>
  <button id="upload" type="button">upload</button>
</form>
```

+	JavaScript代码

```javascript
$.ajax({
  url: '/upload',
  type: 'POST',
  cache: false,
  data: new FormData($('#uploadForm')[0]),
  processData: false,
  contentType: false
})
  .done(function(res) {})
  .fail(function(res) {})
```

**注意:**

+ ```processData```设置为```false```,因为```data```值是```FormData```对象，不需要对数据做处理
+ ``` form```标签添加```enctype="multipart/form-data"```属性
+	```cache```设置为```false```，上传文件不需要缓存
+	```contentType```设置为```false```,因为是由```form```表单构造的```FormData```对象，且已经声明了属性```enctype="multipart/form-data"```，所以这里设置为```false```
+	上传后，服务器端代码需要使用从查询参数名为```file```获取文件输入流对象，因为```input```中声明的是```name="file"```

如果不是用```form```表单构造FormData对象又该怎么做呢？

### 使用FormData对象添加字段方式上传文件

+	HTML代码

```html
<div id="uploadForm">
  <input id="file" type="file"/>
  <button id="upload" type="button">upload</button>
</div>
```
这里没有```form```标签，也没有```enctype="multipart/form-data"```属性。

+	JavaScript代码

```javascript
var formData = new FormData()
formData.append('file', $('#file')[0].files[0])
$.ajax({
  url: '/upload',
  type: 'POST',
  cache: false,
  data: formData,
  processData: false,
  contentType: false
})
  .done(function(res) {})
  .fail(function(res) {})
```

**注意:这里和上面的方式有几处不同**

+	```append()```的第二个参数应是文件对象，即```$('#file')[0].files[0]```
+ 	```contentType```也要设置为```‘false'```
+	从代码```$('#file')[0].files[0]```中可以看到一个```<input type="file">```标签能够上传多个文件，只需要在```<input type="file">```里添加```multiple```或```multiple="multiple"```属性
