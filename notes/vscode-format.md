### 安装插件

-	ESLint
- Prettier formatter for Visual Studio Code
- Vetur

### 配置文件

#####	.editorconfig

```
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
tab_width = 2ß

```

#####	.eslintrc.js

```
// 在此项中配置属于自己的ESLint规则
rules: {
  'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
  'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off'
}
```

##### 用户设置 setting.json

```
{
	"eslint.validate": [
    "javascript",
    "javascriptreact",
    "vue",
    {
      "language": "html",
      "autoFix": true
    }
  ],
  "prettier.singleQuote": true,
  "prettier.semi": false
}
```

### 使用

使用 “右键” -> “格式化代码” 或者 快捷键格式化文件

### ```vue-cli 3.0``` 使用 ``` ESLint + Prettier ```格式代码的注意事项

在同时使用``` ESLint ```及``` Prettier ```时，二者可能产生冲突，需要在文件根目录新建一个``` .prettierrc.js ```文件，重写部分 ``` Prettier ```规则。

```javascript
module.exports = {
  semi: false,
  singleQuote: true,
  printWidth: 120
}
```