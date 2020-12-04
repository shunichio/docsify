### location正则写法

**location规则**

```
location  = / {
  # 精确匹配根目录 / ，主机名后面不能带任何字符串
  [ configuration A ] 
}

location  / {
  # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
  # 但是正则和最长字符串会优先匹配
  [ configuration B ] 
}

location /app/ {
  # 匹配任何以 /app/ 开头的地址，匹配符合以后，还要继续往下搜索
  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  [ configuration C ] 
}

location ~ /app/Abc {
  # 匹配任何以 /app/ 开头的地址，匹配符合以后，还要继续往下搜索
  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  [ configuration CC ] 
}

location ^~ /images/ {
  # 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条。
  [ configuration D ] 
}

location ~* \.(gif|jpg|jpeg)$ {
  # 匹配所有以 gif,jpg或jpeg 结尾的请求
  # 然而，所有请求 /images/ 下的图片会被 config D 处理，因为 ^~ 到达不了这一条正则
  [ configuration E ] 
}

location /images/ {
  # 字符匹配到 /images/，继续往下，会发现 ^~ 存在
  [ configuration F ] 
}

location /images/abc {
  # 最长字符匹配到 /images/abc，继续往下，会发现 ^~ 存在
  # F与G的放置顺序是没有关系的
  [ configuration G ] 
}

location ~ /images/abc/ {
  # 只有去掉 config D 才有效：先最长匹配 config G 开头的地址，继续往下搜索，匹配到这一条正则，采用
  [ configuration H ] 
}

location ~* /js/.*/\.js

# 以 “=” 开头表示精确匹配
# 如 A 中只匹配根目录结尾的请求，后面不能带任何字符串
# ^~ 开头表示uri以某个常规字符串开头，不是正则匹配
# ~ 开头表示区分大小写的正则匹配
# ~* 开头表示不区分大小写的正则匹配
# / 通用匹配, 如果没有其它匹配,任何请求都会匹配到
# 优先级： (location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)
```

**实际使用中，个人觉得至少应该有三个匹配规则定义，如下：** 

```
# 直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理
# 第一个必选规则
location = / {
  root html
  index index.html index.htm
}


# 第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
# 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
location ^~ /static/ {
  root /html/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
  root /html/static/;
}


# 第三个规则就是通用规则，用来转发动态请求到后端应用服务器
location /api {
    proxy_pass http://www.xxx.com:8080/api/
}
```

**Rewrite规则**

`rewrite`功能就是：使用`nginx`提供的全局变量或自己设置的变量，结合正则表达式和标志位实现`url`重写以及重定向。`rewrite`只能放在 `server{}` , `location{}` , `if{}` 等代码块中，并且只能对域名后边的除去传递的参数外的字符串起作用，例如 `http://seanlook.com/a/we/index.php?id=1&u=str` 只对 `/a/we/index.php` 重写。语法 `rewrite regex replacement [flag];` 
如果想让相对域名或参数字符串起作用，可以使用全局变量匹配，也可以使用`proxy_pass`反向代理。
表面上看 `rewrite` 和 `location` 功能有点像，都能实现跳转，主要区别在于 `rewrite` 是在同一域名内更改获取资源的路径，而 `location` 是对一类路径做控制访问或反向代理，可以 `proxy_pass` 到其他机器。很多情况下 `rewrite` 也会写在 `location` 里，它们的执行顺序是：
1、执行 `server` 块的 `rewrite` 指令
2、执行 `location` 匹配
3、执行选定的 `location` 中的 `rewrite` 指令
如果其中某步 `URI` 被重写，则重新循环执行1-3，直到找到真实存在的文件；循环超过 `10` 次，则返回 `500 Internal Server Error` 错误。

### 示例

1. 在 `VueRouter` 中设置路由的基本路径为 `/app`；
2. 如果脚手架使用的 `2.0` 版本， `config`目录下 `index.js` 中 `build.assetsPublicPath`的值设为 `/app`。如果脚手架使用的 `3.0` 版本， `vue.config.js` 文件中 `baseUrl` 的值设为 `/app`。
3. 执行 `npm run build` 命令后，项目根文件夹会产生一个名为 `dist` 的文件夹，将它重命名为 `app` 后，剪切到自定义的 `nginx` 项目文件存放部署目录中（示例中项目文件的部署目录为 `D:\Website` ）。然后重新配置 `nginx.conf` 文件，配置如下：

```shell
server {
  listen       80;
  server_name  localhost;
  location / {
    root   D:\\Website; # 项目打包后的存放路径
    index  index.html index.htm;
    rewrite '^/$' /app last; #根路径重定向到 /app 路径
  }
  # Nginx反向代理
  location /api {
    proxy_pass   http://192.168.0.18:8090/api;
  }

  #error_page  404              /404.html;

  # redirect server error pages to the static page /50x.html
  #
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
    root   html;
  }
}
```