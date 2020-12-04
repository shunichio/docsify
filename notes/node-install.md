### nodejs安装

+ Windows下的NodeJS安装是比较方便的（v0.6.0版本之后，支持windows native），只需要登陆 [NodeJS官网](http://nodejs.org/) ，便可以看到首页的“INSTALL”按钮，直接点击就会自动下载安装了； 

+ 安装过程基本直接“NEXT”就可以了。（windows的安装msi文件在过程中会直接添加path的系统变量，变量值是你的安装路径，例如`C:\Program Files\nodejs`）； 

+ 安装完成后可以使用`cmd`（`win+r`然后输入`cmd`进入安装目录）测试下是否安装成功。方法：在`cmd`下输入`node -v`，出现下图版本提示就是完成了NodeJS的安装； 

### npm的安装

+ 由于新版的NodeJS已经集成了`npm`，所以之前`npm`也一并安装好了。同样可以使用`cmd`命令行输入`npm -v`来测试是否成功安装，出现版本提示便OK了；

### 配置全局模块的存放路径及缓存路径

+ 我们可以将全局模块的存放路径以及cache的路径放在NodeJS的安装主目录下，便在NodeJs下建立`"node_global"`及`"node_cache"`两个文件夹；

+ 启动`cmd`，输入 `npm config set prefix "D:\nodejs\node_global"` 以及 `npm config set cache "D:\nodejs\node_cache"` 【`D:\nodejs`是nodejs的安装目录】；

+ 现在我们来装个模块试试，选择`express`这个比较常用的模块。同样在`cmd`命令行里面，输入`npm install express -g`（`-g`这个参数意思是装到`global`目录下，也就是上面说设置的`D:\nodejs\node_global`里面）。待`cmd`里面的安装过程滚动完成后，会提示`express`装在了哪、版本还有它的目录结构是怎样；

### 配置环境变量

+ 在 **系统变量** 下新建`NODE_PATH`，输入`D:\nodejs\node_global\node_modules`，由于改变了`module`的默认地址，所以 **用户变量（非系统变量）** 都要跟着改变一下（用户变量`PATH`修改为`D:\nodejs\node_global\`）；

### 结果

+ 以上步骤都OK的话，我们可以再次开启`cmd`命令行，进入`nodejs`的安装目录，运行node程序（直接输入`node`即可），输入`require('express')`来测试下node的模块全局路径是否配置正确了。正确的话`cmd`会列出`express`的相关信息，如出错一般都是`NODE_PATH`的配置不对，可以检查下第④⑤步。