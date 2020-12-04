### 富文本容器

在 `<template></template>` 标签中加入如下代码：

```html
<div class="rich-text">
  <!-- 工具栏容器 -->
  <div id="toolbar-container"></div>

  <!-- 编辑器容器 -->
  <div id="editor">
    <h2 style="text-align:center;">富文本初始化</h2>
  </div>
</div>
```

### 富文本初始化

```javascript
// npm install
import CKEditor from '@ckeditor/ckeditor5-build-decoupled-document';
import '@ckeditor/ckeditor5-build-decoupled-document/build/translations/zh-cn.js'; // 语言包
export default {
  data() {
    return {
      editor: null
    };
  },
  mounted() {
    this._initCKEditor(); // 只能在mounted生命周期进行初始化
  },
  methods: {
    _initCKEditor() {
      CKEditor.create(document.querySelector('#editor'), {
        language: 'zh-cn',
        ckfinder: {
          //后端处理上传逻辑，上传数据格式为 formData ，字段名称为 upload ，返回 json 数据,字段包括 uploaded（值为 true/false）和 url（值为图片地址）
          uploadUrl: 'http://172.16.1.148:8080/sys/article/pictureUpload'
        }
      })
        .then(editor => {
          const toolbarContainer = document.querySelector('#toolbar-container');
          toolbarContainer.appendChild(editor.ui.view.toolbar.element); // 文档（document）类型的富文本编辑器需要执行此操作
          this.editor = editor; //将编辑器保存起来，用来随时获取编辑器中的内容等，执行一些操作
        })
        .catch(error => {
          console.error(error);
        });
    }
  }
};
```

### 富文本样式

```css
.rich-text {
  border: 1px solid rgba(0, 0, 0, 0.15);
  background-color: #eeeeee;
}

#toolbar-container {
  /deep/ .ck.ck-toolbar {
    border: none;
  }
}

#editor {
  height: 800px;
  padding: 20px 80px;
}
```
