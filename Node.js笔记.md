# Node.js基础

## ch01. Node.js初识

### -01.Node.js是什么

Node.js是一个基于Chrome V8引擎的JavaScript运行环境

- 基于 [Express 框架 ](http://www.expressjs.com.cn/)，可以快速构建 Web 应用
- 基于 [Electron 框架](https://electronjs.org/)，可以构建跨平台的桌面应用
- 基于 [restify 框架](http://restify.com/)，可以快速构建 API 接口项目
- 读写和操作数据库、创建实用的命令行工具辅助前端开发、etc…

### -02.查看Node.js版本号

1. 快捷键windows徽标+R打开 运行
2.  输入cmd 回车或者确定 打开终端
3. 输入 node 空格 -v 回车

### -03.在Node.js环境中执行JavaScript代码

1. 打开终端（如直接在所属文件夹中按住Shift + 鼠标右键选择d打开PowerShell窗口，则不需要切换文件目录。
2. 切换文件目录：cd 空格 文件夹的路径
3.  输入 node 空格 要执行的js文件的路径

### -04.终端中的快捷键

在windows的PowerShell或cmd终端中，我们可以通过如下快捷键，来提高终端的操作效率：

- 使用 ↑ 键，可以快速定位到上一次执行的命令
- 使用 tab 键，能够快速补全路径
- 使用 esc 键，能够快速清空当前已输入的命令
- 输入 cls 命令，可以清空终端

## ch02. fs文件系统模块

### -01.什么是fs 文件系统模块

fs 模块是 Node.js 官方提供的，用来操作文件的模块，它提供了一系列的方法和属性，用来满足用户对文件的操作需求。例如：

- **fs.readFile()** 方法，用来**读取指定文件中的内容**
- **fs.writrFile()**方法，用来向指定的文件中**写入内容**

如果要在JavaScript代码中，使用fs 模块来操作文件，则需要使用如下的方式先导入它：

```js
const fs = require('fs');
```

### -02.读取指定文件中的内容

①  fs.readFile() 方法，可以读取指定文件中的内容，语法格式如下：

```js
fs.readFile(path[,options],callback);
```

- 参数1：**必选**参数，字符串，表示文件的路径
- 参数2：可选参数，表示以什么编码格式来读取文件
- 参数3：**必选**参数，文件读取完成后，通过回调函数拿到读取的结果

②  代码实例：以 utf8 的编码格式，读取指定文件的内容，并打印 err 和 dataStr的值

```js
const fs = require('fs');
fs.readFile('./files/11.txt','utf8',function(err,dataStr) {
// 如果读取成功，则 err 的值为 null
// 如果读取失败，则 err 的值为 错误对象。dataStr 的值为 undefined
  console.log(err); // 打印 失败的结果
  console.log('-----');
  console.log(dataStr); // 打印 成功的结果
})
```

③  判断文件是否读取成功，可以判断 err 对象是否为 null ，从而知晓文件读取的结果：

- 如果读取成功，则 err 的值为 null
-  如果读取失败，则 err 的值为 错误对象。dataStr 的值为 undefined

```js
const fs = require('fs');
fs.readFile('./files/1.txt','utf8'.function(err, result) {
   if (err) {
      return console.log('文件读取失败！' + err.message);
   }
   console.log('文件读取成功，内容是：' + result);
})
```

### -03.向指定的文件中写入内容

①  fs.writeFile() 方法，可以向执行的文件中写入内容，语法格式如下：

```
fs.writeFile(file,data[,options],callback)
```

- 参数1：**必选**参数，需要指定一个文件路径的字符串，表示文件的存放路径。
- 参数2：**必选**参数，表示要写入的内容
- 参数3：可选参数，表示以什么格式写入文件内容，默认值是 utf8 。
- 参数4：**必选**参数，文件写入完成后的回调函数。

②  fs.writeFile()  示例代码：向指定的文件路径中，写入文件内容。

```js
const fs = require('fs');
fs.writeFile('./files/2.txt','Hello Node.js!',function(err) {
   console.log(err);
})
```

③  判断文件是否写入成功：可以判断 err 对象是否为 null，从而知晓文件写入的结果。

- 如果文件写入成功，则 err 的值等于 null
- 如果文件写入失败，则 err 的值等于一个 错误对象

```js
const fs = require('fs');
fs.writeFile('./files/2.txt','Hello Node.js!',function(err) {
  if(err) {
    return console.log('文件写入失败！' + err.message);
  }
  console.log('文件写入成功！');
})
```

**注意**：

- fs.writeFile() 方法只能用来创建文件，不能用来创建路径
- 重复调用 fs.writeFile() 写入同一个文件时，新写入的内容会覆盖之前的旧内容

### -04.fs模块-路径动态拼接的问题

在使用 fs 模块操作文件时，如果提供的操作路径是以 ./ 或 ../ 开头的**相对路径**时，很容易出现路径动态拼接错误的问题。

原因：代码在运行的时候，**会以执行 node 命令时所处的目录**，动态拼接处被操作文件的完整路径。

```
PS C:\Users\86185\Desktop\Node.js\Node.js基础\day_01> node .\05-演示路径的问题.js
读取文件成功！111
PS C:\Users\86185\Desktop\Node.js\Node.js基础\day_01> cd ../
PS C:\Users\86185\Desktop\Node.js\Node.js基础> node .\day_01\05-演示路径的问题.js
读取文件失败！ENOENT: no such file or directory, open 'C:\Users\86185\Desktop\Node.js\Node.js基础\files\1.txt'
// 要读取的文件的正确完整路径是：
C:\Users\86185\Desktop\Node.js\Node.js基础\day_01\files\1.txt 
```

解决方案：在使用 fs 模块操作文件时，**直接提供完整的路径**，不要提供 ./ 或 ../ 开头的相对路径，从而方式路径动态拼接的问题。

移植性非常差，不利于维护：

```js
// 在 js 里面，一个 \ 代表转义的意思，\\ 才代表一个真正的斜线，因此需要手动补全路径中的斜线
fs.readFile('C:\\Users\\86185\\Desktop\\Node.js\\Node.js基础\\day_01\\files\\1.txt', 'utf8', function (err, dataStr) {
  ...
})
```

**__dirname 获取文件所处的绝对路径**（最终解决方案）

```js
fs.readFile(__dirname + '/files/1.txt', 'utf8', function (err, dataStr) {
  ...
})
```

## ch03. path路径模块

### -01.什么是 path 路径模块

path 模块是 Node.js 官方提供的、用于处理路径的模块，它提供了一系列的方法和属性，用来满足用户对路径的处理需求。例如：

- path.join() 方法，用来将多个路径片段拼接成一个完整的路径字符串
- path.basename() 方法，用来从路径字符串中，将文件名解析出来

如果要在JavaScript代码中，使用 path 模块来操作文件，则需要使用如下的方式先导入它：

```js
const path = require('path')
```

### -02.路径拼接path.join()

①  使用 path.join() 方法，可以把多个路径片段拼接为完整的路径字符串，语法格式如下：

```
path.join([...paths])
```

- ...paths`<string>`路径片段的序列
- 返回值：`<string>`

②  path.join() 代码示例

```js
// 注意： ../ 会抵消前面的路径
const pathStr = path.join('/a','/b/c','../','./d','e');
console.log(pathStr); // 输出 a\b\d\e

const pathStr2 = path.join(__dirname,'./files/1.txt');
console.log(pathStr2); // 输出 当前文件所处目录\files\1.txt
```

**注意**：今后凡是涉及到路径拼接的操作，都要使用 path.join() 方法进行处理。不要使用 + 进行字符的拼接。

### -03. 获取路径中的文件名 path.basename()

①  使用 `path.basename()` 方法，可以获取路径中的最后一部分，经常通过这个方法获取路径中的文件名，语法格式如下：

```
path.basename(path[,ext])
```

- path：`<string>`**必选**参数，表示一个路径的字符串
- ext：`<string>`可选参数，表示文件扩展名
- 返回值：`<string>`表示路径中的最后一部分

②  `path.basename()` 代码示例

```js
const fpath = '/a/b/c/index.html'; // 文件的存放路径
var fullName = path.basename(fpath);
console.log(fullName); // 输出 index.html

var nameWithoutExt = path.basename(fpath,'.html');
console.log(nameWithoutExt); // 输出 index
```

### -04.获取路径中的文件扩展名 path.extname()

①  使用 path.extname() 方法，可以获取路径中的扩展名部分，语法格式如下

```js
path.extname(path)
```

- path`<string>`**必选**参数，表示一个路径的字符串
- 返回值`<string>`：返回得到的扩展名字符串

②  path.extname() 代码示例

```js
const fpath = '/a/b/c/index.html'; // 路径字符串
const fext = path.extname(fpath);
console.log(fext); // 输出 .html
```

综合案例-拆分文件

1. 创建两个正则表达式，分别用来匹配<style>和<script>标签
2. 使用 fs 模块，读取需要被处理的HTML 文件
3. 自定义 resolveCSS 方法，来写入 index.css 样式文件
4. 自定义 resolveJS 方法，来写入 index.js 脚本文件
5. 自定义 resolveHTML 方法，来写入 index.html 文件



## ch04. http 模块

### -01. 服务器相关的概念

#### [1]  IP 地址

IP地址 就是互联网上每台计算机的唯一地址（唯一性），已知对方 IP地址，才能与对应的电脑之间进行数据通信。

IP 地址 的格式：通常用 “点分十进制” 表示成（a,b,c,d）的形式，其中a,b,c,d都是0-255之间的十进制整数。例如：（192.168.1.1）

注意：

- 互联网中每台 Web 服务器，都有自己的 IP 地址。例如在终端中运行 ping www.baidu.com 命令，即可查看百度服务器 IP 地址。
- 在开发期间，自己的电脑既是一台服务器，也是一个客户端，为了方便测试，可以在自己的浏览器中输入 127.0.0.1 这个 IP地址，就能把自己的电脑当做一台服务器进行访问了。

#### [2]  域名和域名服务器

尽管 IP地址 能够唯一地标记网络上的计算机，但IP地址是一长串数字，不直观，而且不便于记忆，于是人们又发明了另一套**字符型**的地址方案，即所谓的域名地址。

IP地址 和 域名 是一一对应的关系，这份对应关系存放在一种叫做 **域名服务器** 的电脑中，使用者只需通过好记的域名访问对应的服务器即可，对应的转换工作由域名服务器实现，因此，**域名服务器就是提供 IP地址 和 域名之间的转换服务的服务器**。

注意：

- 单独使用IP地址，互联网中的电脑也能够正常工作，但使用域名更加方便
- 在开发测试期间，127.0.0.1 对应的域名是 localhost ，它们都代表我们自己的这台电脑，在使用效果上没有任何区别。

#### [3]  端口号

计算机中的端口号，就好像现实生活中的门牌号一样，在一台电脑中，可以运行成百上千个 web 服务，每个 web服务 都对应一个唯一的端口号，客户端发送过来的网络请求，通过端口号，可以准确地交给**对应的 web服务** 进行处理。

注意：

- 每个端口不能同时被多个 web服务 占用。
- 在实际应用中，URL 中的 **80 端口号可以被省略**。

### -02. 创建最基本的 web 服务器

#### [1] 创建 web 服务器的基本步骤

1. 导入 http 模块
2. 创建 web 服务器实例
3. 为服务器实例绑定 request 事件，监听客户端的请求
4. 启动服务器

##### ①  步骤1 - 导入 http 模块

如果希望在自己的电脑上创建一个 web 服务器，从而对外提供服务，则需要导入 http 模块：

```js
const http = require('http')
```

##### ②  步骤2 - 创建 web 服务器实例

调用 http.createServer() 方法，即可快速创建一个 web 服务器实例：

```js
const server = http.createServer()
```

##### ③  步骤3 - 为服务器实例绑定 request 事件，监听客户端的请求

```js
// 使用服务器实例的 .on() 方法，为服务器绑定一个 request 事件
server.on('request',(req,res) => {
  // 只要有客户端来请求我们自己的服务器，就会触发 request 事件，从而调用这个事件处理函数
  console.log('Someone visit our web server')
})
```

##### ④  步骤4 - 启动服务器

调用服务器实例的 .listen() 方法，即可启动当前的 web 服务器实例：

```js
// 调用sever.listen(端口号，cb回调)方法，即可启动 web 服务器
server.listen(80,() => {
    console.log('http server running at http://127.0.0.1')
})
```

#### [2] req 请求对象

只要服务器接收到了客户端的请求，就会调用通过 server.on() 为服务器绑定的 request 事件处理函数。如果想在事件处理函数中，**访问与客户端相关的 数据 或 属性**，可以使用如下的方式：

```js
server.on('request',(res) => {
   // req 是请求对象，它包含了与客户端相关的数据和属性，例如：
   // req.url 是客户端请求的 url 地址
   // req.method 是客户端的 method 请求类型
   const str = `Your request url is ${req.url},and request     method is ${req.method}`;
   console.log(str);
})
```

#### [3] res 响应对象

在服务器的 request 事件处理函数中，如果想 **访问与服务器相关的 数据 或 属性**，可以使用如下的方式

```js
server.on('request',(req,res) => {
  // res 是响应对象，它包含了与服务器相关的数据和属性，例如：
  // 要发送到客户端的字符串
  const str = 'Your request url is ${req.url},and request method is ${req.method}';
  // res.end() 方法的作用：
  // 向客户端发送指定的内容，并结束这次请求的处理过程
  res.end(str);
})
```

#### [4] 解决中文乱码的问题

当调用 res.end() 方法，向客户端发送中文内容时，会出现乱码问题，此时，需要通过 **res.setHeader()** 手动设置内容的编码格式：

```js
server.on('request',(req,res) => {
  // 发送的内容包含中文
  const str = `您请求的 url 地址是 ${req.url},请求的类型是 ${req.method}`;
  // 为了防止中文显示乱码的问题，需要设置响应头 Content-Type 的值为 text/html; charset=utf-8
  res.setHeader('Content-Type','text/html; charset=utf-8');
  // 把包含中文的内容，响应给客户端
  res.end(str);
})
```

#### [5] 根据不同的 url 响应不同的 html 内容

核心实现步骤：

1. 获取请求的 url 地址
2. 设置默认的响应内容为 404 Not found
3. 判断用户请求的是否为 / 或 index.html 首页
4. 判断用户请求的是否为 about.html 关于页面
5. 设置 Content-Type 响应头，防止中文乱码
6. 使用 res.end() 把内容响应给客户端

```js
server.on('request',(req,res) => {
  const url = req.url; // 1. 获取请求的url地址
  let content = '<h1>404 Not found!</h1>'; // 2.设置默认响应内容
  if (url === '/' || url === '/index.html') {
    content = '<h1>首页</h1>'; // 3. 用户请求的是首页
  } else if (url === '/about.html') {
    content = '<h1>关于页面</h1>'; // 4. 用户请求的是关于页面
  }
  // 5.设置Content-Type响应头，防止中文乱码
  res.setHeader('Content-Type','text/html; charset=utf-8');
  res.end(content); // 6. 把内容发送给客户端
})
```

## ch05. 模块化

### -01. 模块化的基本概念

#### [1] 什么是模块化

模块化是指解决一个复杂问题是，自顶向下逐层**把系统划分成若干模块的过程**。对于整个系统来说，模块是可组合、分解和更换的单元。

编程领域中的模块化，就是**遵守固定的规则**，把一个**大文件**拆成**独立并相互依赖**的**多个小模块**。

把代码进行模块化拆分的好处：

1. 提高了代码的复用性
2. 提高了代码的可维护性
3. 可以实现按需加载

#### [2] 模块化规范

模块化规范就是对代码进行模块化的拆分和组合时，需要遵守的规则，例如

- 引用模块 的语法格式
- 在模块中 向外暴露成员 的语法格式

好处：降低沟通成本，方便各个模块之间的相互调用。

### -02. Node.js中的模块化

#### [1] Node.js 中模块的分类

Node.js 中根据模块来源的不同，将模块分为了 3 大类：

- 内置模块（由Node.js 官方提供，例如fs , path , http等）
- 自定义模块（用户创建的每个 .js 文件，都是自定义模块）
- 第三方模块（由第三方开发出来的模块，非官方非自定义，使用前需先下载）

#### [2] 加载模块

使用强大的 require() 方法，可以加载需要的模块进行使用，例如：

```js
// 1. 加载内置的 fs 模块
const fs = require('fs')
// 2. 加载用户的自定义模块 （前面会有路径）
const custom = require('./custom.js')
// 3. 加载第三方模块（关于下载和使用，之后课程讲解）
const moment = require('moment')
```

> **注意：**使用 require() 方法加载其他模块时，会执行被加载模块中的代码

#### [3] Node.js 中的模块作用域

什么是模块作用域

和函数作用域类似，在自定义模块中定义的变量、方法等成员，**只能在当前模块内被访问**，这种模块级别的访问限制，叫做**模块作用域**。

好处：**防止全局变量污染**

#### [4] 向外共享模块作用域中的成员

- 在每个.js 自定义模块中都有一个 `module`对象，里面 **存储了和当前模块有关的信息**。
- 在自定义模块中，可以使用 `module.exports` 对象，将模块内的成员共享出去，供外界使用。外界用 `require() 方法`导入自定义模块时，**得到的就是 `module.exports` 指向的对象。**
-  为了简化代码，Node 提供了`exports`对象，默认情况下，`exports`和`module.exports`指向同一个对象。最终共享的结果，以`module.exports`指向的对象为准。

`exports`和`module.exports`的使用误区

时刻谨记，`require()`模块时，得到的永远是 `module.exports`指向的对象

```js
exports.username = 'zs';
module.exports = {gender: '男',age: 22};
// require() 得到的是 {gender: '男',age: 22}
```

```js
module.exports.username = 'zs';
exports = {gender: '男',age: 22};
// {username: 'zs'}
```

```js
exports.username = 'zs';
module.exports.gender = '男';
// {username: 'zs',gender: '男'}
```

```js
exports = {username: 'zs',gender: '男'};
module.exports = exports;
module.exports.age = 22;
// {username: 'zs',gender: '男',age: 22}
```

**注意**：为了防止混乱，建议不要在同一个模块中同时使用`exports`和`module.exports`

#### [5] Node.js中的模块化规范

Node.js 遵循了 **CommonJS 模块化规范**，CommonJS 规定了**模块的特性**和**各模块之间如何相互依赖**

CommonJS规定：

- 每个模块内部， **`module` 变量**代表当前模块。
- `module` 变量是一个对象，它的`exports`属性（即**`module.exports`**）**是对外的接口**
- 加载某个模块，其实是加载该模块的 `module.exports`属性，**`require()`方法用于加载模块**

### -03. npm 与 包

#### [1] 包

##### ①  什么是包

Node.js 中的 **第三方模块** 又叫做 **包**

##### ②  包的来源

不同于`Node.js`中的内置模块与自定义模块， **包是由第三方个人或团队开发出来的**，免费供所有人使用。

**注意**：`Node.js`中的包都是免费且开源的，可以免费下载使用

##### ③  为什么需要包

由于`Node.js`的内置模块仅提供了一些底层的 API，导致在基于内置模块进行项目开发时效率很低。

**包是基于内置模块封装出来的**，提供了更高级、更方便的 API，**极大的提高了开发效率**

包和内置模块之间的关系，类似于jQuery和浏览器内置API之间的关系

##### ④  从哪里下载

国外IT公司 npm, Inc 网站 https://www.npmjs.com/ ，是全球最大的包共享平台。**搜索需要的包**

npm, Inc公司提供了地址为 https://registry.npmjs.org/ 的服务器，来对外共享所有的包，可以从这个服务器**下载我们所需要的包**。

##### ⑤  如何下载包

npm, Inc公司提供了 Node Package Manager (简称 npm 包管理工具)，它随着Node.js的安装包一起安装到了用户电脑上

在终端执行 `npm -v`命令，查看npm 包管理工具的版本号。

使用 这个包管理工具从 https://registry.npmjs.org/ 下载包

#### [2] npm 的使用

##### ①  在项目中安装包的命令

如果想在项目中安装指定名称的包，需要在终端运行如下的命令：

```
npm install 包的完整名称
```

上述的装包命令，可以**简写**成如下形式：

```
npm i 包的完整名称
```

##### ②  导入需要的包：

导入的名称，就是装包时的名称，例如：

```js
const moment = require('moment')
```

##### ③  初次装包后多了哪些文件

初次装包完成后，在项目文件夹中多了一个叫做`node_modules`的文件夹和`package-lock.json`的配置文件。其中：

- `node_modules`文件夹用来**存放所有已安装到项目中的包**。`require()`导入第三方包时，就是从这个目录中查找并加载包。
- `package-lock.json`配置文件用来**记录`node_modules`目录下的每一个包的下载信息**，例如包的名字、版本号、下载地址等。

**注意**：不要手动修改`node_modules` 或`package-lock.json`文件中的任何代码，npm 包管理工具会自动维护它们。

##### ④  安装指定版本的包

默认情况下，使用`npm install`命令安装包的时候，**会自动安装最新版本的包**，如需安装指定版本的包，可在包名之后，通过**@符号**指定具体版本，例如

```
npm i moment@2.22.2
```

##### ⑤  包的语义化版本规范

包的版本是以 “点分十进制” 形式进行定义的，总共有三位数字，例如 2.24.0

其中每一位数字所代表的的含义如下：

- 第 1 位数字：大版本 （发生了底层重构）
- 第 2 位数字：功能版本 （新增了某些功能）
- 第 3 位数字：Bug 修复版本 （修复了某些Bug）

**版本号提升的规则**：只要前面的版本号增长了，则后面的版本号**归零**

#### [3] 包管理配置文件

npm 规定，在**项目根目录**中，**必须**提供一个叫做`package.json`的包管理配置文件，用来记录与项目有关的一些配置信息。例如：

- 项目的名称、版本号、描述等
- 项目中都用到了哪些包
- 哪些包只能在**开发期间**会用到
- 哪些包在**开发**和**部署**是都需要用到

##### ①  多人协作的问题：

第三方包的体积过大，不方便团队成员之间共享项目源代码

解决方案：**共享时剔除`node_modules`**

##### ②  如何记录项目中安装了哪些包

在**项目根目录**中，创建了一个叫做`package.json`的配置文件，即可用来记录项目中安装了哪些包，从而**方便剔除**`node_modules`目录之后，在团队成员之间共享项目的源代码。

**注意**：今后在项目开发中，一定要把`node_modules`文件夹，添加到`.gitignore`忽略文件中

##### ③  快速创建 package.json

npm 包管理工具提供了一个**快捷命令**，可以在**执行命令时所处的目录中**，快速创建 `package.json`这个包管理配置文件：

```
npm init -y
```

**注意**：

- 上述命令**只能在英文的目录下成功运行**！所以，项目文件夹的名称一定要使用英文命名，**不要使用中文，不能出现空格**
- 运行`npm install`命令安装包的时候，npm 包管理工具会自动把**包的名称**和**版本号**，记录到`package.json`中

##### ④  dependencies 节点

`package.json`文件中，有一个`dependencies`节点，专门用来记录你使用`npm install`命令安装了哪些包。

##### ⑤  **一次性** 安装所有的包

当我们拿到一个**剔除了`node_modules`**的项目之后，需要先把所有的包下载到项目中，才能将项目运行起来。否则会报类似于下面的错误：

```
// 由于项目运行依赖于 moment 这个包，如果没有提前安装好这个包，就会报如下的错误
Error: Cannot find module 'moment'
```

可以运行`npm install`命令（或npm i)一次性安装所有的依赖包：

```
// 执行 npm install 命令时，npm 包管理工具会先读取 package.json 中的 dependencies 节点。
// 读取到记录的所有依赖包名称和版本号之后，npm 包管理工具会把这些包一次性下载到项目中
npm install
```

##### ⑥  卸载包

可以运行 `npm uninstall`命令，来卸载指定的包：

```
// 使用 npm uninstall 具体的包名 来卸载包
npm uninstall moment
```

注意：`npm uninstall`命令执行成功后，会把卸载的包，自动从`package.json`的`dependencies`节点中移除掉。

##### ⑦  devDependencies 节点

如果某些包**只在项目开发阶段**会用到，在**项目上线之后不会用到**，则建议把这些包记录带`devDependencies`节点中。

与之对应的，如果某些包在**开发**和**项目上线之后**都需要用到，则建议把这些包记录到`dependencies`节点中。

可以使用如下的命令，将包记录到`devDependencies`节点中：

```
// 安装指定的包，并记录到 devDependencies 节点中
npm i 包名 -D
// 注意：上述命令是简写形式，等价与下面完整的写法：
npm install 包名 --save-dev
```

#### [4] 解决下包速度慢的问题

##### ① 为什么下包速度慢

在使用 npm 下包的时候，默认从国外的 https://registry.npmjs.org/ 服务器下载，此时，网络数据的传输需要经过漫长的海底光缆，因此下包速度会很慢。

##### ②  淘宝 NPM 镜像服务器

淘宝在国内搭建了一个服务器，专门把国外官方服务器上的包**同步**到国内的服务器，然后再国内提供下包的服务，从而极大的提高了下包的速度。

扩展：镜像（Mirroring）是一种文件存储形式，一个磁盘上的数据在另一个磁盘上存在一个完全相同的副本即为镜像。

##### ③  切换 npm 的下包镜像源

下包的镜像源，指的就是下包的**服务器地址**

```
# 查看当前的下包镜像源
npm config get registry
# 将下包的镜像源切换为淘宝镜像源
npm config set registry=https://registry.npm.taobao.org/
# 检查镜像源是否下载成功
npm config get registry
```

##### ④  nrm

为了方便的切换下包的镜像源，我们可以安装 nrm 这个小工具，利用 nrm 提供的终端命令，可以快速查看和切换下包的镜像源。

```
# 通过 npm 包管理工具，将 nrm 安装为全局可用的工具
npm i nrm -g
# 查看所有可用的镜像源
nrm ls
# 将下包的镜像源切换为 taobao 镜像
nrm use taobao
```

#### [5] 包的分类

##### ①  项目包

那些被安装到**项目**的`node_modules`目录中的包，都是项目包。分为两类：

- 开发依赖包（被记录到`devDependencies`节点中的包，只在开发期间会用到）
- 核心依赖包（被记录到`dependencies`节点中的包，在开发期间和项目上线之后都会用到）

```
npm i 包名 -D    # 安装 开发依赖包
npm i 包名       # 安装 核心依赖包
```

##### ②  全局包

在执行`npm install`命令时，如果提供了`-g`参数，则会把包安装为**全局包**

全局包会被安装到

`C:\Users\用户目录\AppData\Roaming\npm\node_modules` 目录下

```
npm i 包名 -g           # 全局安装指定的包
npm uninstall 包名 -g   # 卸载全局安装的包
```

注意：

- 只有**工具性质的包**，才有全局安装的必要性，因为它们提供了好用的终端命令。
- 判断某个包是否需要全局安装后才能使用，可以**参考官方提供的使用说明**。

###### i5ting_toc

i5ting_toc 是一个可以把 md 文档转为 html 页面的小工具，使用步骤如下：

```
# 将 i5ting_toc 安装为全局包
npm install -g i5ting_toc
# 调用 i5ting_toc ，轻松实现 md 转 html 的功能
i5ting_toc -f 要转换的md文件路径 -o
```

#### [6] 规范的包结构

一个规范的包，它的组成结构，必须符合以下3点要求：

- 包必须以**单独的目录**而存在
- 包的顶级目录下必须包含`package.json`这个包管理配置文件
- `package.json`中必须包含name，version，main这三个属性，分别代表**包的名字**、**版本号**、**包的入口**。

注意：以上三点要求是一个规范的包结构必须遵守的格式，关于更多的约束，可以参考网址：https://yarnpkg.com/zh-Hans/docs/package-json

#### [7] 开发属于自己的包

① 需要实现的功能

1. 格式化日期
2. 转义HTML中的特殊字符
3. 还原HTML中的特殊字符

② 初始化包的基本结构

新建 itheima-tools文件夹，作为包的根目录

在itheima-tools文件夹中，新建如下三个文件

- package.json （包管理配置文件）
- index.js （包的入口文件）
- README.md （包的说明文档）

③ 初始化 package.json

```json
{
    "name": "itheima-tools-lsp", 包的名称
    "version": "1.0.0", 版本号
    "main": "index.js", 包的入口
    "description": "提供了格式化时间、HTMLEscape相关的功能", 简介 
    关键字
    "keywords": ["itheima","dateFormat","escape"],
    "license": "ISC" 许可协议
}
```

④ 在 index.js 文件中写入 三个实现功能函数

⑤ 将不同的功能进行模块化拆分

- 将格式化时间的功能，拆分到 src -> dateFormat.js 中
- 将处理HTML字符串的功能，拆分到 src -> htmlEscape.js 中
- 在 index.js 中，导入两个模块，得到需要向外共享的方法
- 在 index.js 中，使用 module.exports 把对应的方法共享出去

⑥ 编写包的说明文档

包的根目录中的 README.md 文件，是**包的使用说明文档**，通过它，我们可以事先把包的使用说明，以`markdown`的格式写出来，方便用户参考。

README.md 文件中具体写什么内容，没有强制性的要求，只要能够清晰地把包的作用、用法、注意事项等描述清楚即可。

我们所创建的这个包的README.md 文档中，会包含以下6项内容：

安装方式、导入方式、格式化时间、转义HTML中的特殊字符、还原HTML中的特殊字符、开源协议。

#### [8] 发布包

##### ① 登录 npm 账号

在终端执行`npm login`命令，依次输入用户名、密码、邮箱后，即可登录成功

注意：在运行`npm login`命令之前，必须先把**下包的服务器**地址切换为 **npm 的官方服务器**，否则会导致发布包失败。

##### ② 把包发布到 npm 上

将终端切换到包的根目录之后，运行`npm publish`命令，即可将包发布到 npm 上（注意：包名不能雷同）

##### ③ 删除已发布的包

运行`npm unpublish 包名 --force`命令，即可从 npm 删除已发布的包。

注意：

- `npm unpublish`命令只能删除**72小时以内**发布的包
- `npm unpublish`删除的包，在**24小时内**不允许重复发布
- 发布包的时候要慎重，**尽量不要往npm上发布没有意义的包**

### -04. 模块的加载机制

#### [1] 优先从缓存中加载

**模块在第一次加载后会被缓存**，这也意味着多次使用`require()`不会导致模块的代码被执行多次。

注意：不论是内置模块、用户自定义模块、还是第三方模块，它们都会优先从缓存中加载，从而**提高模块的加载效率**。

#### [2] 内置模块的加载机制

内置模块是由Node.js官方提供的模块，**内置模块的加载优先级最高**。

例如：`require('fs')`始终返回内置的fs模块，即使在`node_modules`目录下有名字相同的包也叫作 fs 。

#### [3] 自定义模块的加载机制

使用`require()`加载自定义模块时，必须指定以 ./ 或 ../ 开头的**路径标识符**。在加载自定义模块时，如果没有指定这样的标识符，则 node 会把它当做**内置模块**或**第三方模块进行加载**。

同时，在使用`require()`导入自定义模块时，如果省略了文件的扩展名，则Node.js 会**按顺序**分别尝试加载以下文件：

1. 按照**确切的文件名**进行加载
2. 补全 .js 扩展名进行加载
3. 补全 .json 扩展名进行加载
4. 补全 .node 扩展名进行加载
5. 加载失败，终端报错

#### [4] 第三方模块的加载机制

如果传递给 `require()`的模块标识符不是一个内置模块，也没有以 ./ 或 ../ 开头，则 Node.js 会从当前模块的父目录开始，尝试从 `/node_modules`文件夹中加载第三方模块。

如果没有找到对应的第三方模块，则移动到**再上一层父目录**中，进行加载，**直到文件系统的根目录。**

例如：假设在`C:\Users\itheima\project\foo.js`文件里调用了`require(tools)`，则 Node.js 会按以下顺序查找：

1. C:\Users\itheima\project\ `node_modules\` tools
2. C:\Users\itheima\ `node_modules\`tools
3. C:\Users\ `node_modules\`tools
4. C:\ `node_modules\`tools
5. 报错

#### [5] 目录作为模块

当把目录作为模块标识符，传递给`require()`进行加载时，有三种加载方式：

1. 在被加载的目录下查找一个叫做`package.json`的文件，并寻找 main属性，作为`require()`加载的入口
2. 如果目录里没有`package.json`文件，或者 main 入口不存在或无法解析，则Node.js会试图加载目录下的 index.js 文件。
3. 上两步都失败，则Node.js会在终端**报错**，报告模块的缺失：`Error Cannot find module 'xxx'`

# Express

## ch01.Express初识

### -01. Express 简介

#### [1] 什么是 Express

官方给出的概念：Express 是**基于 Node.js 平台**，快速、开放、极简的 **Web 开发框架。**

Express的本质：就是一个 npm 上的第三方包，提供了快速创建 Web 服务器的便捷方法。（**Express 是用于快速创建服务器的第三方模块**。）

Express 的中文官网：http://www.expressjs.com.cn/

#### [2] 进一步理解 Express

Express 是**基于**内置的 http模块 进一步封装出来的第三方模块，它们都可以创建 Web服务器。

http 内置模块使用更复杂，开发效率低，而Express极大的**提高了开发效率**。

#### [3] Express 能做什么

对于前端程序员来说，最常见的两种服务器，分别是：

- Web 网站服务器：专门对外提供 Web网页资源的服务器。
- API 接口服务器：专门对外提供 API 接口的服务器。

使用 Express，我们可以方便、快速的创建 Web网站服务器和 API接口服务器

### -02. Express 的基本使用

#### [1]  安装

在项目所处的目录中，运行如下终端命令，即可将 express 安装到项目中使用

```
npm i express@版本号
```

#### [2] 创建基本的 Web 服务器

```js
// 1. 导入 express
const express = require('express')
// 2. 创建 web 服务器
const app = express()
// 3. 调用 app.listen(端口号，启动成功后的回调函数),启动服务器
app.listen(80,() => {
    console.log('express server running at http://127.0.0.1')
})
```

#### [3] 监听 GET 请求

通过`app.get()`方法，可以监听客户端的 GET 请求，具体语法如下：

```js
// 参数1：客户端请求的 URL 地址
// 参数2：请求对应的处理函数
//    req：请求对象（包含了与请求相关的属性和方法）
//    res：响应对象（包含了与响应相关的属性和方法）
app.get('请求URL',function(req,res){ /*处理函数*/ })
```

#### [4] 监听 POST 请求

通过`app.post()`方法，可以监听客户端的 POST 请求，具体语法如下：

```js
// 参数1：客户端请求的 URL 地址
// 参数2：请求对应的处理函数
//    req：请求对象（包含了与请求相关的属性和方法）
//    res：响应对象（包含了与响应相关的属性和方法）
app.post('请求URL',function(req,res){ /*处理函数*/ })
```

#### [5] 把内容响应给客户端

通过`res.send()`方法，可以把处理好的内容，发送给客户端：

```js
app.get('/user',(req,res) => {
    // 向客户端发送 JSON 对象
    res.send({name: 'zs', age: 20, gender: '男' })
}) // '/user' 是一个请求地址

app.post('/user',(req,res) => {
    // 向客户端发送文本内容
    res.send('请求成功')
})
```

#### [6] 获取 URL 中携带的查询参数

通过`req.query`对象，可以访问到客户端 通过**查询字符串**形式 发送到服务器的参数：

```js
app.get('/',(req,res) => {
    // req.query 默认是一个空对象
    // 客户端使用 ?name=zs&age=20 这种查询字符串形式，发送到服务器的参数
    // 可以通过 req.query 对象访问到，例如：
    // req.query.name  req.query.age
    console.log(req.query)
})
```

#### [7] 获取 URL 中的动态参数

通过`req.params`对象，可以访问到 URL 中，通过“:”匹配到的参数：

```js
// URL 地址中，可以通过 :参数名 的形式，匹配动态参数值
app.get('/user/:id',(req,res) => { 
    // 1. /:是固定写法，代表我们要匹配一个动态的参数，但是 :后面的字符串可以是任意合法合理自定义名称
    // 2. 可以有多个动态参数 '/:id/:name'
    // req.params 默认是一个空对象
    // 里面存放着通过 : 动态匹配的参数值
    console.log(req.params)
})
```

### -03 托管静态资源

#### [1] express.static()

express 提供了一个`express.static()`函数，通过它可以非常方便地**创建一个静态资源服务器**。

例如，通过以下代码就可以将 public 目录下的图片、CSS文件、JavaScript文件对外开放了：

```
app.use(express.static('public'))
```

**注意**：Express 在指定的静态目录中查找文件，并对外提供资源的访问路径。因此，存放静态文件的目录名不会出现在 URL 中。

#### [2] 托管多个静态资源目录

如果要托管多个静态资源目录，请多次调用`express.static()`函数

```js
app.use(express.static('pulish'))
app.use(express.static('files'))
```

访问静态资源文件时，`express.static()`函数会根据目录的添加顺序查找所需的文件。

#### [3] 挂载路径前缀

如果希望在托管的**静态资源访问路径**之前，**挂载路径前缀**，可使用如下方式

```js
app.use('/public',express.static('public'))
```

[4] nodemon

##### ① 为什么要使用 nodemon

在编写调试 Node.js 项目时，如果修改了项目的代码，则需要频繁的手动 close 掉，然后再重新启动，非常繁琐。

可以使用 nodemon（https://www.npmjs.com/package/nodemon）这个工具，它能够**监听项目文件的变动**，当代码被修改后，nodemon 会自**动帮我们重启项目**，极大方便了开发和调试。

##### ② 安装 nodemon

在终端中，运行如下命令，即可将 nodemon 安装为全局可用的工具：

```
npm install -g nodemon
```

##### ③ 使用 nodemon

当基于Node.js编写了一个网站应用的时候，传统的方式，是运行`node 文件路径`命令，来启动项目。坏处是：代码修改之后，需要手动重启项目。

将 node 命令替换为 nodemon 命令，使用`nodemon 文件路径`来启动项目。好处：代码被修改之后，会被 nodemon 监听到，从而实现**自动重启项目**的效果

```
node app.js
# 将上方终端命令，替换为下方终端命令，即可实现自动重启项目的效果
nodemon app.js
```

解决vscode中无法运行 nodemon ：

1. 以管理员身份打开 PowerShell
2. 输入`set-ExecutionPolicy RemoteSigned`
3. 选择 Y 或者 A

## ch02. Express 路由

### -01. 路由的概念

#### [1] 什么是路由

广义上来讲，路由就是映射关系

#### [2] 现实生活中的路由

![](C:\Users\86185\Desktop\笔记整理\luyou_01.jpg)

#### [3] Express 中的路由

在 Express 中，路由指的是**客户端的请求**与**服务器处理函数**之间的映射关系

Express 中的路由分 3 部分组成，分别是**请求的类型**、**请求的URL地址**、**处理函数**，格式如下：

```js
app.METHOD(PATH,HANDLER)
```

#### [4] Express 中的路由的例子

```js
// 匹配 GET 请求，且请求 URL 为 /
app.get('/',function(req,res) {
    res.send('Hello World!')
})
```

```js
// 匹配 POST 请求，且请求 URL 为 /
app.post('/',function(req,res) {
    res.send('Got a POST request')
})
```

#### [5] 路由的匹配过程

每当一个请求到达服务器之后，**需要先经过路由的匹配**，只有匹配成功之后，才会调用对应的处理函数。

在匹配时，会按照路由的顺序进行匹配，如果**请求类型**和**请求的 URL** 同时匹配成功，则 Express 会将这次请求，转交给对应的 function 函数进行处理。

匹配路由的注意点：

1. 按照定义的**先后顺序**进行匹配。
2. **请求类型** 和 **请求的URL**同时匹配成功，才会调用对应的处理函数。

### -02.路由的使用

#### [1] 最简单的用法

在 Express 中使用路由最简单的方式，就是把路由挂载到 app（服务器实例）上，实例代码如下：

```js
const express = require('express')
// 创建 Web 服务器，命名为 app
const app = express()
// 挂载路由
app.get('/',(req,res) => {res.send('Hello World.')})
app.post('/',(req,res) => {res.send('Post Request')})
// 启动 Web 服务器
app.listen(80,() => {
    console.log('sever running at http://127.0.0.1')
})
```

#### [2] 模块化路由

为了方便**路由**进行**模块化的管理**，Express **不建议**将路由直接挂载到 app 上，而是 **推荐将路由抽离为单独的模块**。抽离步骤如下：

1. 创建路由模块对应的 .js 文件
2. 调用`express.Router()`函数创建路由对象
3. 向路由对象上挂载具体的路由
4. 使用`module.exports`向外共享路由对象
5. 使用`app.use()`函数注册路由模块

#### [3] 创建路由模块

```js
var express = require('express') // 1. 导入 express
var router = express.Router()  // 2. 创建路由对象
// 3. 挂载获取用户列表的路由
router.get('/user/list',function(req,res) {
    res.send('Get user list')
})
// 4. 挂载添加用户的路由
router.post('/user/add',function(req,res) {
    res.send('Add new user.')
})
module.exports = router  // 5. 向外导出路由对象
```

#### [4] 注册路由模块

```js
// 1. 导入路由模块
const userRouter = require('./router/user.js')
// 2. 使用 app.use() 注册路由模块
app.use(userRouter)
```

#### [5] 为路由模块添加前缀

类似于托管静态资源时，为静态资源统一挂载访问前缀一样，路由模块添加前缀的方式也非常简单：

```js
// 1. 导入路由模块
const userRouter = require('./router/user.js')
// 2. 使用 app.use() 注册路由模块，并添加统一的访问前缀 /api
app.use('/api',userRouter)
```

## ch03.Express 中间件

### -01.中间件的概念

#### [1] 什么是中间件

中间件（Middleware），特指**业务流程**的**中间处理环节**。

#### [2] 现实生活中的例子

在处理污水的时候，一般都要经过**三个处理环节**，从而保证处理过后的废水，达到排放标准。处理污水的这三个中间环节，就可以叫做中间件。

#### [3] Express 中间件的调用流程

当一个请求到达 Express 的服务器之后，可以连续调用多个中间件，从而对这次请求进行预处理。

#### [4] Express 中间件的格式

Express 的中间件，本质上就是一个 fucntion处理函数，包含 `req, res, next` 三个参数，Express中间件的格式如下：

```js
app.get('/',function(req,res,next) {
    next();
})
```

注意：中间件的函数形参列表中，**必须包含`next`参数**。而**路由**处理函数中只包含 req 和 res 。

#### [5] next 函数的作用

`next()` 函数是实现**多个中间件连续调用**的关键，它表示把流转关系**转交**给下一个中间件或路由。

### -02. Express 中间件的初体验

#### [1] 定义中间件函数

可以通过如下的方式，定义一个最简单的中间件函数：

```js
// 常量 mw 所指向的，就是一个中间件函数
const mw = function(req,res,next) {
    console.log('这是一个最简单的中间件函数')
    // 注意：在当前中间件的业务处理完毕后，必须调用 next()函数
    // 表示把流转关系 转交给 下一个中间件或路由
    next()
}
```

#### [2] 全局生效的中间件

客户端的**任何请求**，到达服务器之后，**都会触发的中间件**，叫做全局生效的中间件。

通过调用`app.use(中间件函数)`，即可定义一个**全局生效**的中间件，实例代码如下：

```js
// 常量 mw 所指向的，就是一个中间件函数
const mw = function(req,res,next) {
    console.log('这是一个最简单的中间件函数')
    next()
}
// 全局生效的中间件
app.use(mw)
```

#### [3] 定义全局中间件的简化形式

```js
// 全局生效的中间件
app.use(function(req,res,next) {
    console.log('这是一个最简单的中间件函数')
    next()
})
```

#### [4] 中间件的作用

多个中间件之间，**共享同一份 req 和 res**，基于这样的特性，我们可以在**上游**的中间件中，统一为 req 和 res 对象添加**自定义**的属性或**方法**，供**下游**的中间件或路由进行使用。

[5] 定义多个全局中间件

可以使用`app.use()`**连续定义多个**全局中间件，客户端请求到达服务器之后，会按照中间件**定义的先后顺序**依次进行调用，示例代码如下：

```js
app.use(function(req,res,next) { // 第1个全局中间件
    console.log('调用了第1个全局中间件')
    next()
})
app.use(function(req,res,next) { // 第2个全局中间件
    console.log('调用了第2个全局中间件')
    next()
})
// 请求这个路由，会依次触发上述两个全局中间件
app.get('/user',(req,res) => {
    res.send('Home page.')
})
```

#### [6] 局部生效的中间件

不使用`app.use()`定义的中间件，叫做局部生效的中间件，示例代码如下：

```js
// 定义中间件函数 mw1
const mw1 = function(req,res,next) {
    console.log('这是中间件函数')
    next()
}
// mw1 这个中间件函数只在 当前路由中生效，这种用法属于 局部生效的中间件
app.get('/',mw1,function(req,res) {
    res.send('Home page.')
})
// mw1 这个中间件不会影响下面这个路由
app.get('/user',function(req,res) {
    res.send('User page.')
})
```

#### [7] 定义多个局部中间件

可以在路由中，通过如下两种**等价**的方式，**使用多个局部中间件**：

```js
// 以下两种写法 完全等价 ，可任意选择一种进行使用
app.get('/',mw1,mw2,(req,res) => {res.send('Home page')})
app.get('/',[mw1,mw2],(req,res) => {res.send('Home page')})
```

#### [8] 了解中间件的5个使用注意事项

- 一定要在**路由之前**注册中间件
- 客户端发送过来的请求，**可以连续调用多个**中间件进行处理
- 执行完中间件的业务代码之后，**不要忘记调用`next()`函数**
- 为了**防止代码逻辑混乱**，调用`next()`函数后不要再写额外的代码
- 连续调用多个中间件时，多个中间件之间，**共享 req 和 res 对象**

### -03.中间件的分类

Express官方把常见的中间件，按照用法分成了5大类，分别是：

1. **应用级别** 的中间件
2. **路由级别** 的中间件
3. **错误级别** 的中间件
4. **Express内置** 的中间件
5. **第三方** 的中间件

#### [1] 应用级别 的中间件

通过`app.use()`或`app.get()`或`app.post()`，绑定到 app实例上的中间件：

```js
// 应用级别的中间件（全局中间件）
app.use((req,res,next) => {
    next()
})
// 应用级别的中间件 （局部中间件）
app.get('/',mw1,(req,res) => {
    res.send('Home page.')
})
```

#### [2] 路由级别 的中间件

绑定到`express.Router()`实例上的中间件，叫做路由级别的中间件，它的用法和应用级别中间件没有任何区别。只不过，应用级别中间件时绑定到 app实例上，**路由级别中间件绑定到 router实例上**，代码示例：

```js
var app = express()
var router = express.Router()
// 路由级别中间件
router.use(function(req,res,next) {
    console.log('Time',Date now())
    next()
})
app.use('/',router)
```

#### [3] 错误级别的中间件

作用：专门用来捕获整个项目中发生的异常错误，从而防止项目崩溃的问题。

格式：错误级别中间件的 function处理函数中，**必须有4个形参**，形参顺序从前到后，分别是`(err,req,res,next)`。

```js
app.get('/',function(req,res) { // 路由
    // 抛出了一个自定义的错误
    throw new Error('服务器内部发生了错误')
    res.send('Home page')
})
app.use(function(err,req,res,next) { // 错误级别中间件
    // 在服务器打印错误消息
    console.log('发生了错误：' + err.message)
    // 向客户端响应错误相关的内容
    res.send('Error!' + err.message)
})
```

**注意**：错误级别的中间件，**必须注册在所有路由之后**！

#### [4] Express内置的中间件

自Express 4.16.0 版本开始，Express内置了3个常用的中间件：

1. `express.static `快速托管静态资源的内置中间件 （无兼容性）
2. `express.json` 解析 JSON 格式的请求体数据（有兼容性，4.16.0+）
3. `express.urlencoded`解析 URL-encoded 格式的请求体数据（有兼容性，仅在4.16.0+版本中可用）

```js
// 配置解析 application/json 格式数据的内置中间件
app.use(express.json())
// 配置解析 application/x-www-form-urlencoded 格式数据的内置中间件
app.use(express.urlencoded({extended: false}))
```

#### [5] 第三方的中间件

非Express官方内置的，而是由第三方开发出来的中间件，叫做第三方中间件，在项目中，可以按需下载并配置第三方中间件，从而提高开发效率。

1. 运行 `npm install 中间件名称` 安装中间件
2. 使用` require()`导入中间件
3. 调用`app.use()`注册并使用中间件

### -04.自定义中间件

#### [1] 需求描述与实现步骤

自己**手动模拟**一个类似于`express.urlencoded`这样的中间件，来**解析 POST 提交到服务器的表单数据。**

实现步骤：

1. 定义中间件
2. 监听 req 的 data 事件
3. 监听 req 的 end 事件
4. 使用 querystring模块 解析请求体数据
5. 将解析出来的数据对象挂载为 req.body
6. 将自定义中间件封装为模块

#### [2] 定义中间件

使用`app.use()`来定义全局生效的中间件，代码如下：

```js
app.use(function(req,res,next) {
    // 中间件的业务逻辑
})
```

#### [3] 监听 req 的 data 事件

在中间件中，需要监听req 的 data 事件，来获取客户端发送到服务器的数据。

如果数据量比较大，无法一次性发送完毕，则**客户端会把数据切割后，分批发送到服务器**。所以 data 事件可能会触发多次，每一次触达 data 事件时，**获取到数据只是完整数据的一部分**，需要手动对接收到的数据进行拼接。

```js
// 定义变量，用来存储客户端发送过来的请求体数据
let str = ''
// 需要监听req 的 data 事件（客户端发送过来的新的请求体数据）
req.on('data',(chunk) => {
    // 拼接请求体数据，隐式转换为字符串
    str += chunk
})
```

#### [4] 监听 req 的 end 事件

请求体数据接收完毕之后，会自动触发 req 的 end 事件.

因此，我们可以在 req 的 end 事件中，拿到并处理完整的请求体数据：

```js
// 监听 req 对象的 end 事件（请求体发送完毕后自动触发）
req.on('end',() => {
    // 打印完整的请求体数据
    console.log(str)
    // TODO: 把字符串格式的请求体数据，解析成对象格式
})
```

#### [5] 使用 querystring模块 解析请求体数据

Node.js **内置**了一个 querystring**模块** ，专门用来处理查询字符串，通过这个模块提供的`parse()`函数，可以把查询字符串，解析成对象的格式，示例：

```js
// 导入 Node.js内置的 querystring模块 解析请求体数据
const qs = require('querystring')
// 调用 qs.parse() 方法，把查询字符串解析为对象
const body = qs.parse(str)
```

如果此模块无法使用，可查询官方文档用其他模块替代

#### [6] 将解析出来的数据对象挂载为 req.body

上游的中间件和下游的中间件及路由之间，共享同一份 req 和 res。因此，我们可以将解析出来的数据，挂载为 req 的自定义属性，命名为 req.body，供下游使用。示例代码如下：

```js
// 将解析出来的数据对象，挂载为 req.body
req.body = body
next() // 最后一定要调用 next()函数，执行后续的业务逻辑
```

#### [7] 将自定义中间件封装为模块

为了优化代码的结构，我们可以把自定义的中间件函数，封装为独立的模块。代码示例如下：

```js
// custom-body-parser.js 模块中的代码
const qs = require('querystring')
function bodyParser(req,res,next) {/* 省略其他代码 */}
// 向外导出 解析请求体数据的中间件函数
module.exports = bodyParser 
```

```js
// 使用封装好的自定义中间件
// 1. 导入自定义的中间件模块
const myBodyParser = require('custom-body-parser')
// 2. 将自定义的中间件函数，注册为全局可用的中间件
app.use(myBodyParser)
```

## ch04.使用 Express 写接口

### -01.创建基本的服务器

```js
// 导入 express 模块
const express = require('express')
// 创建 express 的服务器实例
const app = express()
// 在此处编写代码 ...

// 调用 app.listen() 方法，指定端口并启动 Web服务器
app.listen(8080,function() {
    console.log('Express server running at http://127.0.0.1:8080')
})
```

### -02.创建 API 路由模块

```js
// apiRouter.js 【路由模块】
const express = require('express')
const apiRouter = express.Router()
// 在此处挂载对应的路由...

module.exports = apiRouter
```

```js
// app.js 【导入并注册路由模块】
const apiRouter = require('./apiRouter.js')
app.use('/api', apiRouter)
```

### -03.编写接口

#### [1] 编写 GET 接口

```js
apiRouter.get('/get',(req,res) => {
    // 1. 获取客户端通过查询字符串，发送到服务器的数据
    const query = req.query
    // 2. 调用 res.send() 方法，把数据响应给客户端
    res.send({
        status: 0,          // 状态，0表示成功，1表示失败
        msg: 'GET请求成功！', // 状态描述
        data: query         // 需要响应给客户端的具体数据
    })
})
```

#### [2] 编写 POST 接口

```js
apiRouter.post('/post',(req,res) => {
    // 1. 获取客户端通过请求体，发送到服务器的URL-encoded数据
    const body = req.body
    // 2. 调用 res.send() 方法，把数据响应给客户端
    res.send({
        status: 0,          // 状态，0表示成功，1表示失败
        msg: 'POST请求成功！', // 状态描述消息
        data: body          // 需要响应给客户端的具体数据
    })
})
```

**注意**：如果要获取 URL-encoded 格式的请求体数据，必须配置中间件 `app.use(express.urlencoded({extended: false }))`

### -05.CORS 跨域资源共享

#### [1] 接口的跨域问题

上方编写的 GET 和 POST 接口，存在一个很严重的问题：不支持跨域请求

解决接口跨域问题的方案主要有两种：

- CORS （主流的解决方案，**推荐使用**）
- JSONP（有缺陷的解决方案：只支持GET请求）

#### [2] 使用 cors中间件 解决跨域问题

cors 是 Express 的一个第三方中间件，通过安装和配置 cors中间件，可以很方便的解决跨域问题。使用步骤分为如下3步：

1. 运行 `npm install cors`安装中间件
2. 使用`const cors = require('cors')`导入中间件
3. 在路由之前调用`app.use(cors())`配置中间件

#### [3] 什么是 CORS

CORS（Cross-Origin Resource Sharing，跨域资源共享）由一系列 **HTTP响应头** 组成，**这些HTTP响应头决定浏览器是否阻止前端 JS 代码跨域获取资源**。

浏览器的**同源安全策略**默认会阻止网页“跨域”获取资源，但如果接口服务器  **配置了CORS相关的HTTP响应头**，就可以**解除浏览器端的跨域访问限制**。

#### [4] CORS 的注意事项

- CORS 主要在**服务器端**进行配置，客户端浏览器无须做任何额外的配置，即可请求开启了 CORS 的接口。
- CORS 在浏览器中**有兼容性**，只有支持 XMLHttpRequest Level2 的浏览器，才能正常访问开启了 CORS 的服务端接口（例如：IE10+、Chrome4+、Firefox3.5+）。

#### [5] CORS 响应头部 

##### ① Access-Control-Allow-Origin

响应头部中可以携带一个 Access-Control-Allow-Origin 字段，其语法如下：

```js
Access-Control-Allow-Origin：<origin> | *
```

其中，`origin` 参数的值指定了**允许访问该资源的外域 URL**。

例如，下面字段值将**只允许**来自 http://itcast.cn 的请求：

```js
res.setHeader('Access-Control-Allow-Origin','http://itcast.cn')
```

如果指定了 Access-Control-Allow-Origin 字段的值为通配符*，表示允许来自任何域的请求，实例代码如下：

```js
res.setHeader('Access-Control-Allow-Origin','*')
```

##### ② Access-Control-Allow-Headers

默认情况下，CORS **仅**支持**客户端向服务器**发送如下的9个**请求头**：

`Accept`、`Accept-Language`、`Content-Language`、`DPR`、`Downlink`、`Save-Data`、`Viewport-Width`、`Width`、`Content-Type` （值仅限于 text/plain、multipart/form-data、application/x-www-form-urlencoded 三者之一）

如果客户端向服务器**<u>发送了额外的请求头信息</u>**，则需要在**服务器端**，通过Access-Control-Allow-Headers **<u>对额外的请求头进行声明</u>**，否则这次请求会失败！

```js
// 允许客户端额外向服务器发送 Content-Type 请求头 和       X-Custom-Header 请求头
// 注意：多个请求头之间使用英文逗号进行分割
res.setHeader('Access-Control-Allow-Headers','Content-Type','X-Custom-Header')
```

##### ③ Access-Control-Allow-Methods

默认情况下，CORS 仅支持客户端发起 GET、POST、HEAD请求。

如果客户端希望通过 PUT、DELETE 等方式请求服务器的资源，则需要在**服务器端**，通过 Access-Control-Allow-Methods 来**<u>指明实际请求所允许使用的HTTP 方法</u>**。实例代码如下：

```js
// 只允许 POST、GET、DELETE、HEAD 请求方法
res.setHeader('Access-Control-Allow-Methods','POST,GET,DELETE,HEAD')
// 允许所有的 HTTP 请求方法
res.setHeader('Access-Control-Allow-Methods','*')
```

#### [6] CORS 请求的分类

客户端在请求 CORS 接口时，根据 **<u>请求方式</u>** 和 **<u>请求头</u>** 的不同，可以将 CORS 分为两大类，分别是：①简单请求    ②预检请求

##### ① 简单请求

同时满足以下两大条件的请求，就属于简单请求：

- 请求方式为 GET、POST、HEAD 三者之一
- HTTP头部信息 不超过 CORS 默认支持的九个请求头，且不包含自定义头部字段。

##### ② 预检请求

只要符合以下任何一个条件的请求，都需要进行预检请求：

- 请求方式为 GET、POST、HEAD 之外的请求 Method 类型
- 请求头中 包含自定义头部字段
- 向服务器发送了 application/json 格式的数据

在浏览器与服务器正式通信之前，浏览器会**<u>先发送 OPTION 请求进行预检，以获知服务器是否允许该实际请求</u>**，这一次请求被称为“预检请求”。**<u>服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据</u>**。

##### ③ 简单请求 和 预检请求 的区别

**简单请求的特点**：客户端与服务器之间**只会发生一次请求**。

**预检请求的特点**：客户端与服务器之间**会发生两次请**求，OPTION预检请求成功之后，才会发起真正的请求。

### -06.JSONP 接口

#### [1]  JSONP 的概念与特点

概念：浏览器通过`<script>`标签的 src 属性，请求服务器上的数据，同时，服务器返回一个函数的调用，这种请求数据的方式叫做 JSONP。

特点：

- JSONP 不属于真正的 Ajax 请求，因为他没有使用 XMLHttpRequest 这个对象。
- JSONP 仅支持 GET 请求，不支持 POST、PUT、DELETE 等请求。

#### [2] 创建 JSONP 接口的注意事项

如果项目中**已经配置了 CORS** 跨域资源共享，为了**防止冲突**，必须**在配置CORS 中间件之前声明 JSONP 的接口**，否则 JSONP 的接口会被处理成开启了CORS 的接口，实例代码如下：

```js
// 优先创建  JSONP 接口 【这个接口不会被处理成 CORS 接口】
app.get('/api/jsonp',(req,res) => {})
// 再配置 CORS 中间件【后续的所有接口，都会被处理成 CORS接口】
app.use(cors())
// 这是一个开启了 CORS 的接口
app.get('/api/get',(req,res) => {})
```

#### [3] 实现 JSONP 接口的步骤

1. 获取客户端发送过来的回调函数的名字
2. 得到要通过 JSONP 形式发送给客户端的数据
3. 根据前两步得到的数据，拼接出一个函数调用的字符串
4. 把拼接好的字符串，响应给客户端的`<script>`标签进行解析执行

#### [4] 实现 JSONP 接口的具体代码

```js
app.get('/api/jsonp',(req,res) => {
    // 1. 获取客户端发送过来的回调函数的名字
    const funcName = req.query.callback
    // 2. 定义通过 JSONP 形式发送给客户端的数据
    const data = {name: 'zs', age: 22}
    // 3. 根据前两步得到的数据，拼接出一个函数调用的字符串
    const scriptStr = `${funcName}(${JSON.stringify(data)})`
    // 4. 把拼接好的字符串，响应给客户端的`<script>`标签进行解析执行
    res.send(scriptStr)
})
```

#### [5] 在网页中使用 jQuery 发起 JSONP 请求

调用 `$.ajax()`函数，提供JSONP的配置选项，从而发起 JSONP 请求，示例：

```js
$('#btnJSONP').on('click',function() {
    $.ajax({
        method: 'GET', // 请求方式
        url: 'http://127.0.0.1:8080/api/jsonp',
        dataType: 'jsonp', // 表示发起JSONP类型的数据请求
        success: function(res) {
            console.log(res)
        }
    })
})
```

# 数据库

## ch01.数据库的基本概念

### -01.什么是数据库

数据库（database）是用来**组织、存储**和**管理**数据的仓库。

### -02.常见的数据库及分类

市面上的数据库有很多种，最常见的有如下几个：

- MySQL 数据库（目前使用最广泛、流行度最高的开源免费数据库；Community + Enterprise）
- Oracle 数据库（收费）
- SQL Server 数据库（收费）
- Mongodb 数据库（Community + Enterprise）

Community是社区免费版  ， Enterprise是企业收费版

其中，MySQL 、Oracle 、SQL Server 属于**传统型数据库**（又叫做：关系型数据库 或 SQL数据库），这三者的设计理念相同，用法比较类似。

而 Mongodb 属于**新型数据库**（又叫做：非关系型数据库 或 NoSQL 数据库），它在一定程度上弥补了传统型数据库的缺陷。

### -03.传统型数据库的数据组织结构

数据的组织结构：指的就是数据以什么样的结构进行存储。

传统型数据库的数据组织结构，与 Excel 中数据的组织结构比较类似。

#### [1] Excel 的数据组织结构

每个 Excel 中，数据的组织结构由 工作簿、工作表、数据行、列 4部分组成：

- 整个 Excel 叫做 工作簿
- users 和 books 是 工作表
- 工作表中每一行 是 数据行
- 每一行数据由 n列组成

#### [2] 传统型数据库的数据组织结构

在传统型数据库中，数据的组成结构分为 数据库（database）、数据表（tabel）、数据行（row）、字段（field）这四大部分组成。

- 数据库 类似于 Excel 的工作簿
- 数据表 类似于 Excel 的工作表
- 数据行 类似于 Excel 的每一行数据
- 字段 类似于 Excel 的列
- 每个字段都有对应的数据类型

#### [3] 实际开发中库、表、行、字段的关系

- 在实际项目开发中，一般情况下，每个项目都对应独立的数据库。
- 不同的数据，要存储到数据库中的不同表中，例如：用户数据存储到 users 表中，图片数据存储到 books 表中。
- 每个表中具体存储哪些信息，有字段决定，例如：我们可以为 users 表设计 id、username、password 这三个字段
- 表中的行，代表每一条具体的数据。

## ch02.安装并配置 MySQL

#### -01.安装MySQL相关软件

对于开发人员来说，只需要安装 MySQL Server 和 MySQL Workbench 这两个软件，就能满足开发的需要了。

- MySQL Server（专门用来提供数据存储和服务的软件）
- MySQL Workbench（可视化的 MySQL 管理工具，通过它，可以方便的操作存储在 MySQL Server 中的数据。）

-02.MySQL在Windows环境下的安装

Windows安装 MySQL ，只需要运行 mysql-installer-community-8.0.19.0.msi 这个安装包，就能一次性安装MySQL Server 和 MySQL Workbench

`username: root  ` ` password: admin123`

### 使用MySQL Workbench 管理数据库

DataType数据类型：

1. init 整数
2. varchar(length)字符串
3. tinyint(1) 布尔值

字段的特殊表示：

1. PK 主键、唯一标识
2. NN 值不允许为空
3. UQ 值唯一
4. AI 值自动增长

```sql
-- 查询users表中的所有数据
 select * from users
 
-- 从users 表中把 username 和 password 对应的数据查询出来
 select username, password from users
 
-- 向users表中，插入新数据，username 的值为 tony stark ，password 的值为 098123
insert into users (username, password) value ('tony stark', '098123')

-- 将 id 为 4 的用户密码，更新为 888888
update users set password='888888' where id=4

-- 把 users 表中 id 为 2 的用户密码和用户状态，分别更新为 admin123 和 1
update users set password='admin123',status=1 where id=2

-- 从指定的表中，根据 where 条件，删除对用的数据行
delete from 表名称 where 列名称=值
```

order by 进行排序

```sql
-- 对 users 表中的数据，按照 status 字段进行升序降序
select * from users order by status

-- 按照id对结果进行降序的排序，desc表示降序排序 asc表示升序排序（默认情况下就是升序）
select * from users order by id desc

-- 多重排序，对users表中的数据，先按照 status 进行降序排序，再按照 username 字母的顺序，进行升序的排序
select * from order by status desc,username asc
```

count(*)函数用于返回查询结果的总数据条数，语法格式如下：

```sql
select count(*) from 表名称

-- 统计 users表中，状态为 0 用户的总数据
select count(*) from users where status=0
```

使用 as 关键字给列起别名

```sql
-- 将列名称从count(*)修改为total (设置别名)
select count(*) as total from users

-- 给普通列起别名
select username as uname,password as pwd from users
```

### 在项目中操作数据库的步骤

1. 安装操作MySQL数据库的第三方模块（mysql）
2. 通过mysql模块连接到MySQL数据库
3. 通过mysql模块执行SQL语句

#### 安装配置mysql模块

1. 初始化包管理文件

   ```
   npm init -y
   ```

2. 安装mysql模块

   ```
   npm i mysql
   ```

3. 配置mysql模块

   ```js
   // 1.导入mysql模块
   const mysql = require('mysql')
   // 2. 建立与MySQL数据库的链接
   const db = mysql.createPool({
       host: '127.0.0.1', // 数据库的IP地址
       user: 'root', // 登录数据库的账号
       password: 'admin123', // 登录数据库的密码
       database: 'my_db_01' //指定要操作哪个数据库
   })
   ```

   

4. 测试mysql模块能否正常工作

   ```js
   // 调用db.query()函数，指定要执行的SQL语句，通过回调函数拿到执行的结果
   db.query('Select 1', (err, results) => {
     if (err) {
        return console.log(err.message)
     }
     // 只要能打印出[ RowDataPacket { '1': 1 } ]的结果，就证明数据库连接正常
     console.log(results)
   })
   ```

#### 使用mysql模块操作MySQL数据库

##### 1.查询数据

```js
// 查询users表中所有的用户数据
db.query('select * from users', (err, results) => {
    // 查询失败
    if (err) {
        return console.log(err.message)
    }
    // 查询成功返回的结果 results 是一个数组
    console.log(results)
})
```

##### 2.插入数据

```js
// 1.要插入到users表中的数据对象
const user = { username: 'Solider-Man', password: 'pcc321' };
// 2.待执行的SQL语句，其中英文? 表示占位符
const sqlStr = 'insert into users(username,password) values (?,?)';
// 3.使用数组的形式，依次为?占位符指定具体的值
db.query(sqlStr, [user.username, user.password], (err, results) => {
    // 添加失败
    if (err) {
        return console.log(err.message)
    }
    // 添加成功
    // 注意：如果执行的是 inset into 插入语句，则results 是一个对象
    // 可以通过 affectedRows 属性，来判断是否插入数据成功
    if (results.affectedRows === 1) {
        console.log('插入数据成功')
    }
})
```

```js
// 插入数据的便捷方式
const user1 = { username: 'Solider-Man1', password: 'pcc4321' };
// 2.待执行的SQL语句，其中英文? 表示占位符
const sqlStr = 'insert into users set ?';
// 3.直接将数据对象当做占位符的值
db.query(sqlStr, user1, (err, results) => {
    // 添加失败
    if (err) {
        return console.log(err.message)
    }
    // 添加成功
    if (results.affectedRows === 1) {
        console.log('插入数据成功')
    }
})
```

##### 3.更新数据

```js
// 更新数据
const user2 = { id: 6, username: 'aaa', password: '000' };
// 2.待执行的SQL语句
const sqlStr = 'update users set username=?,password=? where id=?';
// 3.调用db.query()函数执行SQL语句的同时，使用数组的形式，依次为?占位符指定具体的值
db.query(sqlStr, [user2.username, user2.password, user2.id], (err, results) => {
    // 更新失败
    if (err) {
        return console.log(err.message)
    }
    // 注意：执行了 update 语句之后，执行的结果，也是一个对象，可以通过affectedRows来判断是否更新成功
    // 更新成功
    if (results.affectedRows === 1) {
        console.log('更新数据成功')
    }
})
```

```js
// 更新数据便捷方式
const user3 = { id: 6, username: 'aaaa', password: '0000' };
// 2.待执行的SQL语句
const sqlStr = 'update users set ? where id=?';
// 3.调用db.query()函数执行SQL语句的同时，使用数组的形式，依次为?占位符指定具体的值
db.query(sqlStr, [user3, user3.id], (err, results) => {
    // 更新失败
    if (err) {
        return console.log(err.message)
    }
    // 注意：执行了 update 语句之后，执行的结果，也是一个对象，可以通过affectedRows来判断是否更新成功
    // 更新成功
    if (results.affectedRows === 1) {
        console.log('更新数据成功')
    }
})
```

##### 4.删除数据

```js
// 删除数据 推荐根据id这样的唯一标识，来删除对应的数据
// 1.待执行的SQL语句，其中英文? 表示占位符
const sqlStr = 'delete from users where id=?';
// 2.调用db.query()函数执行SQL语句的同时，为?占位符指定具体的值
// 注意：如果 SQL语句 中有多个占位符，则必须使用数组为每个占位符指定具体的值
//       如果 SQL语句 中只有一个占位符，则可以省略数组
db.query(sqlStr, 6, (err, results) => {
    // 删除失败
    if (err) {
        return console.log(err.message)
    }
    // 删除成功
    if (results.affectedRows === 1) {
        console.log('删除数据成功')
    }
})
```

##### 5.标记删除

使用`delete`语句，会真正的把数据从表中删除调，为了保险起见，推荐使用标记删除的形式，来模拟删除的动作。

所谓了标记删除，就是在表中设置类似于`status`这样的状态字段，来标记当前这条数据是否被删除。

```js
// 标记删除：使用update语句替代 delete语句，只更新数据的状态，并没有真正删除
const sqlStr = 'update users set status=? where id=?';
db.query(sqlStr, [1, 6], (err, results) => {
    if (err) { // 失败
        return console.log(err.message);
    }
    if (results.affectedRows === 1) { // 成功
        return console.log('删除数据成功！')
    }
})
```

## Web 开发模式

### 服务端渲染

服务器发送给客户端的 HTML 页面，是在服务器通过字符串的拼接动态生成的。因此客户端不需要使用 Ajax 额外请求页面的数据。

```js
app.get('/index.html', (req, res) => {
  const user = { name: 'Bruce', age: 29 }
  const html = `<h1>username:${user.name}, age:${user.age}</h1>`
  res.send(html)
})
```

优点：

- 前端耗时短。浏览器只需直接渲染页面，无需额外请求数据。
- 有利于 SEO。服务器响应的是完整的 HTML 页面内容，有利于爬虫爬取信息。

缺点：

- 占用服务器资源。服务器需要完成页面内容的拼接，若请求比较多，会对服务器造成一定访问压力。
- 不利于前后端分离，开发效率低。

### 前后端分离

前后端分离的开发模式，依赖于 Ajax 技术的广泛应用。后端只负责提供 API 接口，前端使用 Ajax 调用接口。

优点：

- 开发体验好。前端专注页面开发，后端专注接口开发。
- 用户体验好。页面局部刷新，无需重新请求页面。
- 减轻服务器的渲染压力。页面最终在浏览器里生成。

缺点：

- 不利于 SEO。完整的 HTML 页面在浏览器拼接完成，因此爬虫无法爬取页面的有效信息。Vue、React 等框架的 SSR（server side render）技术能解决 SEO 问题。

### 如何选择

- 企业级网站，主要功能是展示，没有复杂交互，且需要良好的 SEO，可考虑服务端渲染
- 后台管理项目，交互性强，无需考虑 SEO，可使用前后端分离
- 为同时兼顾首页渲染速度和前后端分离开发效率，可采用首屏服务器端渲染+其他页面前后端分离的开发模式

## 身份认证

身份认证（Authentication）又称“身份验证”、“鉴权”，是指通过一定的手段，**完成对用户身份的确认**。

在web开发中，也涉及到用户身份的认证，例如：各大网站的收集验证码登录、邮箱密码登录、二维码登录等。

### Session 认证机制

#### 1.HTTP协议的无状态性

HTTP协议的无状态性，指的是**客户端的每次HTTP请求都是独立的**，连续多个请求之间没有直接的关系，**服务器不会主动保留每次HTTP请求的状态**。

相当于超市客流量很多，收银员记不住谁是会员

#### 2.突破HTTP无状态的限制

对于超市来说，为了方便收银员在进行结算时给VIP用户打折，超市可以为每个VIP用户发放会员卡。

现实生活中的**会员卡身份认证方式**，在Web开放中的专业术语叫做**Cookie**

#### 3.什么是Cookie

Cookie是存储在用户浏览器中的一般不超过4kb的字符串，它由一个**名称**(Name)、一个**值**(Value) 和其他几个用于控制Cookie**有效期、安全性、使用范围**的可选属性组成

不同域名下的Cookie各自独立，每当客户端发起请求时，会**自动**把**当前域名下**所有**未过期的Cookie**一同发送到服务器。

Cookie的特性：

1. 自动发送
2. 域名独立
3. 过期时限
4. 4kb限制

#### 4.Cookie在身份认证中的作用

客户端第一次请求服务器的时候，服务器**通过响应头的形式**，向客户端发送一个身份认证的Cookie，客户端会自动将Cookie保存在浏览器中。

#### 5.Cookie不具有安全性

由于Cookie是存储在浏览器中的，而且**浏览器也提供了读写Cookie的API**，因此**Cookie很容易被伪造**，不具有安全性，因此不建议服务器将重要的隐私数据，通过Cookie的形式发送给浏览器。

> 注意：**千万不要使用Cookie存储重要且隐私的数据**！比如用户的身份信息、密码等。

#### 6.提高身份认证的安全性

为了防止伪造会员卡，拿到会员卡后，可以进行刷卡认证。只有刷卡机确认存在的会员卡，才能被正常使用。

这种**会员卡+刷卡认证**的设计理念，就是**Session认证机制的精髓**。

服务端渲染推荐使用 Session 认证机制

#### Session 工作原理

![session](https://brucecai55520.gitee.io/bruceblog/assets/img/Session.c66d5499.png)

#### Express 中使用 Session 认证

##### 安装 express-session 中间件

```bash
npm install express-session
```

##### 配置中间件

```js
// 导入 session 中间件
const session = require('express-session')
// 配置 session 中间件
app.use(
  session({
    secret: 'Bruce', // secret 的值为任意字符串
    resave: false, // 固定写法
    saveUninitalized: true, // 固定写法
  })
)
```

##### 向 session 中存数据

中间件配置成功后，可通过 `req.session` 访问和使用 session 对象，存储用户信息

```js
app.post('/api/login', (req, res) => {
  // 判断用户提交的登录信息是否正确
    if(req.body.username !=='admin' || req.body.password !== '000000'){
        return res.send({status:1,msg:'登录失败'})
    }
  // 将用户的信息，存储到Session中
  req.session.user = req.body;
  // 将用户的登录状态，存储到Session中
  req.session.isLogin = true;

  res.send({ status: 0, msg: '登录成功' });
})
```

##### 从 session 取数据

```js
// 获取用户姓名的接口
app.get('/api/username', (req, res) => {
  // 判断用户是否登录
  if (!req.session.isLogin) {
    return res.send({ status: 1, msg: 'fail' })
  }
  res.send({ status: 0, msg: 'success', username: req.session.user.username })
})
```

##### 清空 session

```js
app.post('/api/logout', (req, res) => {
  // 清空当前客户端的session信息
  req.session.destroy()
  res.send({ status: 0, msg: '退出登录成功' })
})
```

### JWT 认证机制

前后端分离推荐使用 JWT（JSON Web Token）认证机制，是目前最流行的跨域认证解决方案

#### JWT 工作原理

Session 认证的局限性：

- Session 认证机制需要配合 Cookie 才能实现。由于 Cookie 默认不支持跨域访问，所以，当涉及到前端跨域请求后端接口的时候，需要做很多额外的配置，才能实现跨域 Session 认证。
- 当前端请求后端接口不存在跨域问题的时候，推荐使用 Session 身份认证机制。
- 当前端需要跨域请求后端接口的时候，不推荐使用 Session 身份认证机制，推荐使用 JWT 认证机制

JWT 工作原理图：

用户的信息通过 Token 字符串的形式，保存在客户端浏览器中。服务器通过还原 Token 字符串的形式来认证用户的身份。

![JWT](https://brucecai55520.gitee.io/bruceblog/assets/img/JWT.6a82c41d.png)

JWT 组成部分：

- Header（头部）、Payload（有效荷载）、Signature（签名）
- Payload 是真正的用户信息，加密后的字符串
- Header 和 Signature 是安全性相关部分，保证 Token 安全性
- 三者使用 `.` 分隔

```text
Header.Payload.Signature

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MTcsInVzZXJuYW1lIjoiQnJ1Y2UiLCJwYXNzd29yZCI6IiIsIm5pY2tuYW1lIjoiaGVsbG8iLCJlbWFpbCI6InNjdXRAcXEuY29tIiwidXNlcl9waWMiOiIiLCJpYXQiOjE2NDE4NjU3MzEsImV4cCI6MTY0MTkwMTczMX0.bmqzAkNSZgD8IZxRGGyVlVwGl7EGMtWitvjGD-a5U5c
```

JWT 使用方式：

- 客户端会把 JWT 存储在 localStorage 或 sessionStorage 中
- 此后客户端与服务端通信需要携带 JWT 进行身份认证，将 JWT 存在 HTTP 请求头 Authorization 字段中
- 加上 Bearer 前缀

```text
Authorization: Bearer <token>
```

#### Express 使用 JWT

##### 安装

- jsonwebtoken 用于生成 JWT 字符串
- express-jwt 用于将 JWT 字符串解析还原成 JSON 对象

```bash
npm install jsonwebtoken express-jwt
```

##### 定义 secret 密钥

- 为保证 JWT 字符串的安全性，防止其在网络传输过程中被破解，需定义用于加密和解密的 secret 密钥
- 生成 JWT 字符串时，使用密钥加密信息，得到加密好的 JWT 字符串
- 把 JWT 字符串解析还原成 JSON 对象时，使用密钥解密

```js
// 1.导入用于生成JWT字符串的包
const jwt = require('jsonwebtoken')
// 2.导入用于将客户端发送过来的JWT字符串，解析还原成JSON对象的包
const expressJWT = require('express-jwt')

// 密钥为任意字符串
const secretKey = 'Bruce'
```

##### 生成 JWT 字符串

```js
app.post('/api/login', (req, res) => {
  ...
  res.send({
    status: 200,
    message: '登录成功',
    // jwt.sign() 生成 JWT 字符串
    // 参数：用户信息对象、加密密钥、配置对象-token有效期
    // 尽量不保存敏感信息，因此只有用户名，没有密码
    token: jwt.sign({username: userInfo.username}, secretKey, {expiresIn: '10h'})
  })
})
```

##### JWT 字符串还原为 JSON 对象

- 客户端访问有权限的接口时，需通过请求头的 `Authorization` 字段，将 Token 字符串发送到服务器进行身份认证
- 服务器可以通过 express-jwt 中间件将客户端发送过来的 Token 解析还原成 JSON 对象

```js
// unless({ path: [/^\/api\//] }) 指定哪些接口无需访问权限
app.use(expressJWT({ secret: secretKey }).unless({ path: [/^\/api\//] }))
```

##### 获取用户信息

- 当 express-jwt 中间件配置成功后，即可在那些有权限的接口中，使用 `req.user` 对象，来访问从 JWT 字符串中解析出来的用户信息

```js
app.get('/admin/getinfo', (req, res) => {
  console.log(req.user)
  res.send({
    status: 200,
    message: '获取信息成功',
    data: req.user,
  })
})
```

##### 捕获解析 JWT 失败后产生的错误

- 当使用 express-jwt 解析 Token 字符串时，如果客户端发送过来的 Token 字符串过期或不合法，会产生一个解析失败的错误，影响项目的正常运行
- 通过 Express 的错误中间件，捕获这个错误并进行相关的处理

```js
app.use((err, req, res, next) => {
  // token解析失败导致的错误
  if (err.name === 'UnauthorizedError') {
    return res.send({ status: 401, message: '无效的token' })
  }
  // 其他原因导致的错误
  res.send({ status: 500, message: '未知错误'   })
})
```

# 大事件项目

## 1. 初始化

### 1.1 创建项目

1. 新建 `api_server` 文件夹作为项目根目录，并在项目根目录中运行如下的命令，初始化包管理配置文件：

```bash
npm init -y
```

1. 运行如下的命令，安装特定版本的 `express`：

```bash
npm i express@4.17.1
```

1. 在项目根目录中新建 `app.js` 作为整个项目的入口文件，并初始化如下的代码：

```js
// 导入 express 模块
const express = require('express')
// 创建 express 的服务器实例
const app = express()

// write your code here...

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(3007, function () {
  console.log('api server running at http://127.0.0.1:3007')
})
```

### 1.2 配置 cors 跨域

1. 运行如下的命令，安装 `cors` 中间件：

```bash
npm i cors@2.8.5
```

1. 在 `app.js` 中导入并配置 `cors` 中间件：

```js
// 导入 cors 中间件
const cors = require('cors')
// 将 cors 注册为全局中间件
app.use(cors())
```

### 1.3 配置解析表单数据的中间件

1. 通过如下的代码，配置解析 `application/x-www-form-urlencoded` 格式的表单数据的中间件：

```js
app.use(express.urlencoded({ extended: false }))
```

### 1.4 初始化路由相关的文件夹

1. 在项目根目录中，新建`router`文件夹，用来存放所有的路由模块

   > 路由模块中，只存放客户端的请求与处理函数之间的映射关系

2. 在项目根目录中，新建`router_handler`文件夹，用来存放所有的路由处理函数模块

   > 路由处理函数模块中，专门负责存放每个路由对应的处理函数

### 1.5 初始化用户路由模块

1. 在 `router` 文件夹中，新建 `user.js` 文件，作为用户的路由模块，并初始化代码如下：

```js
const express = require('express')
// 创建路由对象
const router = express.Router()

// 注册新用户
router.post('/reguser', (req, res) => {
  res.send('reguser OK')
})

// 登录
router.post('/login', (req, res) => {
  res.send('login OK')
})

// 将路由对象共享出去
module.exports = router
```

1. 在 `app.js` 中，导入并使用 `用户路由模块` ：

```js
// 导入并注册用户路由模块
const userRouter = require('./router/user')
app.use('/api', userRouter)
```

### 1.6 抽离用户路由模块中的处理函数

> 目的：为了保证 `路由模块` 的纯粹性，所有的 `路由处理函数`，必须抽离到对应的 `路由处理函数模块` 中

1. 在 `/router_handler/user.js` 中，使用 `exports` 对象，分别向外共享如下两个 `路由处理函数` ：

```js
/**
 * 在这里定义和用户相关的路由处理函数，供 /router/user.js 模块进行调用
 */

// 注册用户的处理函数
exports.regUser = (req, res) => {
  res.send('reguser OK')
}

// 登录的处理函数
exports.login = (req, res) => {
  res.send('login OK')
}
```

1. 将 `/router/user.js` 中的代码修改为如下结构：

```js
const express = require('express')
const router = express.Router()

// 导入用户路由处理函数模块
const userHandler = require('../router_handler/user')

// 注册新用户
router.post('/reguser', userHandler.regUser)
// 登录
router.post('/login', userHandler.login)

module.exports = router
```

## 2. 登录注册

### 2.1 新建 ev_users 表

1. 在 `my_db_01` 数据库中，新建 `ev_users` 表如下： ![ev_users表结构](http://escook.cn:8088/images/1.jpg)

### 2.2 安装并配置 mysql 模块

> 在 API 接口项目中，需要安装并配置 `mysql` 这个第三方模块，来连接和操作 MySQL 数据库

1. 运行如下命令，安装 `mysql` 模块：

```bash
npm i mysql@2.18.1
```

1. 在项目根目录中新建 `/db/index.js` 文件，在此自定义模块中创建数据库的连接对象：

```js
// 导入 mysql 模块
const mysql = require('mysql')

// 创建数据库连接对象
const db = mysql.createPool({
  host: '127.0.0.1',
  user: 'root',
  password: 'admin123',
  database: 'my_db_01',
})

// 向外共享 db 数据库连接对象
module.exports = db
```

### 2.3 注册

#### 2.3.0 实现步骤

1. 检测表单数据是否合法
2. 检测用户名是否被占用
3. 对密码进行加密处理
4. 插入新用户

#### 2.3.1 检测表单数据是否合法

1. 判断用户名和密码是否为空

```js
// 接收表单数据
const userinfo = req.body
// 判断数据是否合法
if (!userinfo.username || !userinfo.password) {
  return res.send({ status: 1, message: '用户名或密码不能为空！' })
}
```

#### 2.3.2 检测用户名是否被占用

1. 导入数据库操作模块：

```js
const db = require('../db/index')
```

1. 定义 SQL 语句：

```js
const sql = `select * from ev_users where username=?`
```

1. 执行 SQL 语句并根据结果判断用户名是否被占用：

```js
db.query(sql, [userinfo.username], function (err, results) {
  // 执行 SQL 语句失败
  if (err) {
    return res.send({ status: 1, message: err.message })
  }
  // 用户名被占用
  if (results.length > 0) {
    return res.send({ status: 1, message: '用户名被占用，请更换其他用户名！' })
  }
  // TODO: 用户名可用，继续后续流程...
})
```

#### 2.3.3 对密码进行加密处理

> 为了保证密码的安全性，不建议在数据库以 `明文` 的形式保存用户密码，推荐对密码进行 `加密存储`

------

在当前项目中，使用 `bcryptjs` 对用户密码进行加密，优点：

- 加密之后的密码，**无法被逆向破解**
- 同一明文密码多次加密，得到的**加密结果各不相同**，保证了安全性

------

1. 运行如下命令，安装指定版本的 `bcryptjs` ：

```bash
npm i bcryptjs@2.4.3
```

1. 在 `/router_handler/user.js` 中，导入 `bcryptjs` ：

```js
const bcrypt = require('bcryptjs')
```

1. 在注册用户的处理函数中，确认用户名可用之后，调用 `bcrypt.hashSync(明文密码, 随机盐的长度)` 方法，对用户的密码进行加密处理：

```js
// 对用户的密码,进行 bcrype 加密，返回值是加密之后的密码字符串
userinfo.password = bcrypt.hashSync(userinfo.password, 10)
```

#### 2.3.4 插入新用户

1. 定义插入用户的 SQL 语句：

```js
const sql = 'insert into ev_users set ?'
```

1. 调用 `db.query()` 执行 SQL 语句，插入新用户：

```js
db.query(sql, { username: userinfo.username, password: userinfo.password }, function (err, results) {
  // 执行 SQL 语句失败
  if (err) return res.send({ status: 1, message: err.message })
  // SQL 语句执行成功，但影响行数不为 1
  if (results.affectedRows !== 1) {
    return res.send({ status: 1, message: '注册用户失败，请稍后再试！' })
  }
  // 注册成功
  res.send({ status: 0, message: '注册成功！' })
})
```

### 2.4 优化 res.send() 代码

> 在处理函数中，需要多次调用 `res.send()` 向客户端响应 `处理失败` 的结果，为了简化代码，可以手动封装一个 res.cc() 函数

1. 在 `app.js` 中，所有路由之前，声明一个全局中间件，为 res 对象挂载一个 `res.cc()` 函数 ：

```js
// 响应数据的中间件
app.use(function (req, res, next) {
  // status = 0 为成功； status = 1 为失败； 默认将 status 的值设置为 1，方便处理失败的情况
  res.cc = function (err, status = 1) {
    res.send({
      // 状态
      status,
      // 状态描述，判断 err 是 错误对象 还是 字符串
      message: err instanceof Error ? err.message : err,
    })
  }
  next()
})
```

### 2.5 优化表单数据验证

> 表单验证的原则：前端验证为辅，后端验证为主，后端**永远不要相信**前端提交过来的**任何内容**

在实际开发中，前后端都需要对表单的数据进行合法性的验证，而且，**后端做为数据合法性验证的最后一个关口**，在拦截非法数据方面，起到了至关重要的作用。

单纯的使用 `if...else...` 的形式对数据合法性进行验证，效率低下、出错率高、维护性差。因此，推荐使用**第三方数据验证模块**，来降低出错率、提高验证的效率与可维护性，**让后端程序员把更多的精力放在核心业务逻辑的处理上**。

1. 安装 `joi` 包，为表单中携带的每个数据项，定义验证规则：

```bash
npm install joi@17.4.0
```

1. 安装 `@escook/express-joi` 中间件，来实现自动对表单数据进行验证的功能：

```bash
npm install @escook/express-joi
```

1. 新建 `/schema/user.js` 用户信息验证规则模块，并初始化代码如下：

```js
const joi = require('joi')

/**
 * string() 值必须是字符串
 * alphanum() 值只能是包含 a-zA-Z0-9 的字符串
 * min(length) 最小长度
 * max(length) 最大长度
 * required() 值是必填项，不能为 undefined
 * pattern(正则表达式) 值必须符合正则表达式的规则
 */

// 用户名的验证规则
const username = joi.string().alphanum().min(1).max(10).required()
// 密码的验证规则
const password = joi
  .string()
  .pattern(/^[\S]{6,12}$/)
  .required()

// 注册和登录表单的验证规则对象
exports.reg_login_schema = {
  // 表示需要对 req.body 中的数据进行验证
  body: {
    username,
    password,
  },
}
```

1. 修改 `/router/user.js` 中的代码如下：

```js
const express = require('express')
const router = express.Router()

// 导入用户路由处理函数模块
const userHandler = require('../router_handler/user')

// 1. 导入验证表单数据的中间件
const expressJoi = require('@escook/express-joi')
// 2. 导入需要的验证规则对象
const { reg_login_schema } = require('../schema/user')

// 注册新用户
// 3. 在注册新用户的路由中，声明局部中间件，对当前请求中携带的数据进行验证
// 3.1 数据验证通过后，会把这次请求流转给后面的路由处理函数
// 3.2 数据验证失败后，终止后续代码的执行，并抛出一个全局的 Error 错误，进入全局错误级别中间件中进行处理
router.post('/reguser', expressJoi(reg_login_schema), userHandler.regUser)
// 登录
router.post('/login', userHandler.login)

module.exports = router
```

1. 在 `app.js` 的全局错误级别中间件中，捕获验证失败的错误，并把验证失败的结果响应给客户端：

```js
const joi = require('joi')

// 错误中间件
app.use(function (err, req, res, next) {
  // 数据验证失败
  if (err instanceof joi.ValidationError) return res.cc(err)
  // 未知错误
  res.cc(err)
})
```

### 2.6 登录

#### 2.6.0 实现步骤

1. 检测表单数据是否合法
2. 根据用户名查询用户的数据
3. 判断用户输入的密码是否正确
4. 生成 JWT 的 Token 字符串

#### 2.6.1 检测登录表单的数据是否合法

1. 将 `/router/user.js` 中 `登录` 的路由代码修改如下：

```js
// 登录的路由
router.post('/login', expressJoi(reg_login_schema), userHandler.login)
```

#### 2.6.2 根据用户名查询用户的数据

1. 接收表单数据：

```js
const userinfo = req.body
```

1. 定义 SQL 语句：

```js
const sql = `select * from ev_users where username=?`
```

1. 执行 SQL 语句，查询用户的数据：

```js
db.query(sql, userinfo.username, function (err, results) {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)
  // 执行 SQL 语句成功，但是查询到数据条数不等于 1
  if (results.length !== 1) return res.cc('登录失败！')
  // TODO：判断用户输入的登录密码是否和数据库中的密码一致
})
```

#### 2.6.3 判断用户输入的密码是否正确

> 核心实现思路：调用 `bcrypt.compareSync(用户提交的密码, 数据库中的密码)` 方法比较密码是否一致

> 返回值是布尔值（true 一致、false 不一致）

具体的实现代码如下：

```js
// 拿着用户输入的密码,和数据库中存储的密码进行对比
const compareResult = bcrypt.compareSync(userinfo.password, results[0].password)

// 如果对比的结果等于 false, 则证明用户输入的密码错误
if (!compareResult) {
  return res.cc('登录失败！')
}

// TODO：登录成功，生成 Token 字符串
```

#### 2.6.4 生成 JWT 的 Token 字符串

> 核心注意点：在生成 Token 字符串的时候，一定要剔除 **密码** 和 **头像** 的值

1. 通过 ES6 的高级语法，快速剔除 `密码` 和 `头像` 的值：

```js
// 剔除完毕之后，user 中只保留了用户的 id, username, nickname, email 这四个属性的值
const user = { ...results[0], password: '', user_pic: '' }
```

1. 运行如下的命令，安装生成 Token 字符串的包：

```bash
npm i jsonwebtoken@8.5.1
```

1. 在 `/router_handler/user.js` 模块的头部区域，导入 `jsonwebtoken` 包：

```js
// 用这个包来生成 Token 字符串
const jwt = require('jsonwebtoken')
```

1. 创建 `config.js` 文件，并向外共享 **加密** 和 **还原** Token 的 `jwtSecretKey` 字符串：

```js
module.exports = {
  jwtSecretKey: 'itheima No1. ^_^',
}
```

1. 将用户信息对象加密成 Token 字符串：

```js
// 导入配置文件
const config = require('../config')

// 生成 Token 字符串
const tokenStr = jwt.sign(user, config.jwtSecretKey, {
  expiresIn: '10h', // token 有效期为 10 个小时
})
```

1. 将生成的 Token 字符串响应给客户端：

```js
res.send({
  status: 0,
  message: '登录成功！',
  // 为了方便客户端使用 Token，在服务器端直接拼接上 Bearer 的前缀
  token: 'Bearer ' + tokenStr,
})
```

### 2.7 配置解析 Token 的中间件

1. 运行如下的命令，安装解析 Token 的中间件：

```js
npm i express-jwt@5.3.3
```

1. 在 `app.js` 中注册路由之前，配置解析 Token 的中间件：

```js
// 导入配置文件
const config = require('./config')

// 解析 token 的中间件
const expressJWT = require('express-jwt')

// 使用 .unless({ path: [/^\/api\//] }) 指定哪些接口不需要进行 Token 的身份认证
app.use(expressJWT({ secret: config.jwtSecretKey }).unless({ path: [/^\/api\//] }))
```

1. 在 `app.js` 中的 `错误级别中间件` 里面，捕获并处理 Token 认证失败后的错误：

```js
// 错误中间件
app.use(function (err, req, res, next) {
  // 省略其它代码...

  // 捕获身份认证失败的错误
  if (err.name === 'UnauthorizedError') return res.cc('身份认证失败！')

  // 未知错误...
})
```

## 3.个人中心

### 3.1 获取用户的基本信息

#### 3.1.0 实现步骤

1. 初始化 **路由** 模块
2. 初始化 **路由处理函数** 模块
3. 获取用户的基本信息

#### 3.1.1 初始化路由模块

1. 创建 `/router/userinfo.js` 路由模块，并初始化如下的代码结构：

```js
// 导入 express
const express = require('express')
// 创建路由对象
const router = express.Router()

// 获取用户的基本信息
router.get('/userinfo', (req, res) => {
  res.send('ok')
})

// 向外共享路由对象
module.exports = router
```

1. 在 `app.js` 中导入并使用个人中心的路由模块：

```js
// 导入并使用用户信息路由模块
const userinfoRouter = require('./router/userinfo')
// 注意：以 /my 开头的接口，都是有权限的接口，需要进行 Token 身份认证
app.use('/my', userinfoRouter)
```

#### 3.1.2 初始化路由处理函数模块

1. 创建 `/router_handler/userinfo.js` 路由处理函数模块，并初始化如下的代码结构：

```js
// 获取用户基本信息的处理函数
exports.getUserInfo = (req, res) => {
  res.send('ok')
}
```

1. 修改 `/router/userinfo.js` 中的代码如下：

```js
const express = require('express')
const router = express.Router()

// 导入用户信息的处理函数模块
const userinfo_handler = require('../router_handler/userinfo')

// 获取用户的基本信息
router.get('/userinfo', userinfo_handler.getUserInfo)

module.exports = router
```

#### 3.1.3 获取用户的基本信息

1. 在 `/router_handler/userinfo.js` 头部导入数据库操作模块：

```js
// 导入数据库操作模块
const db = require('../db/index')
```

1. 定义 SQL 语句：

```js
// 根据用户的 id，查询用户的基本信息
// 注意：为了防止用户的密码泄露，需要排除 password 字段
const sql = `select id, username, nickname, email, user_pic from ev_users where id=?`
```

1. 调用 `db.query()` 执行 SQL 语句：

```js
// 注意：req 对象上的 user 属性，是 Token 解析成功，express-jwt 中间件帮我们挂载上去的
db.query(sql, req.user.id, (err, results) => {
  // 1. 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 2. 执行 SQL 语句成功，但是查询到的数据条数不等于 1
  if (results.length !== 1) return res.cc('获取用户信息失败！')

  // 3. 将用户信息响应给客户端
  res.send({
    status: 0,
    message: '获取用户基本信息成功！',
    data: results[0],
  })
})
```

### 3.2 更新用户的基本信息

#### 3.2.0 实现步骤

1. 定义路由和处理函数
2. 验证表单数据
3. 实现更新用户基本信息的功能

#### 3.2.1 定义路由和处理函数

1. 在 `/router/userinfo.js` 模块中，新增 `更新用户基本信息` 的路由：

```js
// 更新用户的基本信息
router.post('/userinfo', userinfo_handler.updateUserInfo)
```

1. 在 `/router_handler/userinfo.js` 模块中，定义并向外共享 `更新用户基本信息` 的路由处理函数：

```js
// 更新用户基本信息的处理函数
exports.updateUserInfo = (req, res) => {
  res.send('ok')
}
```

#### 3.2.2 验证表单数据

1. 在 `/schema/user.js` 验证规则模块中，定义 `id`，`nickname`，`email` 的验证规则如下：

```js
// 定义 id, nickname, emial 的验证规则
const id = joi.number().integer().min(1).required()
const nickname = joi.string().required()
const email = joi.string().email().required()
```

1. 并使用 `exports` 向外共享如下的 `验证规则对象`：

```js
// 验证规则对象 - 更新用户基本信息
exports.update_userinfo_schema = {
  body: {
    id,
    nickname,
    email,
  },
}
```

1. 在 `/router/userinfo.js` 模块中，导入验证数据合法性的中间件：

```js
// 导入验证数据合法性的中间件
const expressJoi = require('@escook/express-joi')
```

1. 在 `/router/userinfo.js` 模块中，导入需要的验证规则对象：

```js
// 导入需要的验证规则对象
const { update_userinfo_schema } = require('../schema/user')
```

1. 在 `/router/userinfo.js` 模块中，修改 `更新用户的基本信息` 的路由如下：

```js
// 更新用户的基本信息
router.post('/userinfo', expressJoi(update_userinfo_schema), userinfo_handler.updateUserInfo)
```

#### 3.2.3 实现更新用户基本信息的功能

1. 定义待执行的 SQL 语句：

```js
const sql = `update ev_users set ? where id=?`
```

1. 调用 `db.query()` 执行 SQL 语句并传参：

```js
db.query(sql, [req.body, req.body.id], (err, results) => {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 执行 SQL 语句成功，但影响行数不为 1
  if (results.affectedRows !== 1) return res.cc('修改用户基本信息失败！')

  // 修改用户信息成功
  return res.cc('修改用户基本信息成功！', 0)
})
```

### 3.3 重置密码

#### 3.3.0 实现步骤

1. 定义路由和处理函数
2. 验证表单数据
3. 实现重置密码的功能

#### 3.3.1 定义路由和处理函数

1. 在 `/router/userinfo.js` 模块中，新增 `重置密码` 的路由：

```js
// 重置密码的路由
router.post('/updatepwd', userinfo_handler.updatePassword)
```

1. 在 `/router_handler/userinfo.js` 模块中，定义并向外共享 `重置密码` 的路由处理函数：

```js
// 重置密码的处理函数
exports.updatePassword = (req, res) => {
  res.send('ok')
}
```

#### 3.3.2 验证表单数据

> 核心验证思路：旧密码与新密码，必须符合密码的验证规则，并且新密码不能与旧密码一致！

1. 在 `/schema/user.js` 模块中，使用 `exports` 向外共享如下的 `验证规则对象`：

```js
// 验证规则对象 - 重置密码
exports.update_password_schema = {
  body: {
    // 使用 password 这个规则，验证 req.body.oldPwd 的值
    oldPwd: password,
    // 使用 joi.not(joi.ref('oldPwd')).concat(password) 规则，验证 req.body.newPwd 的值
    // 解读：
    // 1. joi.ref('oldPwd') 表示 newPwd 的值必须和 oldPwd 的值保持一致
    // 2. joi.not(joi.ref('oldPwd')) 表示 newPwd 的值不能等于 oldPwd 的值
    // 3. .concat() 用于合并 joi.not(joi.ref('oldPwd')) 和 password 这两条验证规则
    newPwd: joi.not(joi.ref('oldPwd')).concat(password),
  },
}
```

1. 在 `/router/userinfo.js` 模块中，导入需要的验证规则对象：

```js
// 导入需要的验证规则对象
const { update_userinfo_schema, update_password_schema } = require('../schema/user')
```

1. 并在 `重置密码的路由` 中，使用 `update_password_schema` 规则验证表单的数据，示例代码如下：

```js
router.post('/updatepwd', expressJoi(update_password_schema), userinfo_handler.updatePassword)
```

#### 3.3.3 实现重置密码的功能

1. 根据 `id` 查询用户是否存在：

```js
// 定义根据 id 查询用户数据的 SQL 语句
const sql = `select * from ev_users where id=?`

// 执行 SQL 语句查询用户是否存在
db.query(sql, req.user.id, (err, results) => {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 检查指定 id 的用户是否存在
  if (results.length !== 1) return res.cc('用户不存在！')

  // TODO：判断提交的旧密码是否正确
})
```

1. 判断提交的 **旧密码** 是否正确：

```js
// 在头部区域导入 bcryptjs 后，
// 即可使用 bcrypt.compareSync(提交的密码，数据库中的密码) 方法验证密码是否正确
// compareSync() 函数的返回值为布尔值，true 表示密码正确，false 表示密码错误
const bcrypt = require('bcryptjs')

// 判断提交的旧密码是否正确
const compareResult = bcrypt.compareSync(req.body.oldPwd, results[0].password)
if (!compareResult) return res.cc('原密码错误！')
```

1. 对新密码进行 `bcrypt` 加密之后，更新到数据库中：

```js
// 定义更新用户密码的 SQL 语句
const sql = `update ev_users set password=? where id=?`

// 对新密码进行 bcrypt 加密处理
const newPwd = bcrypt.hashSync(req.body.newPwd, 10)

// 执行 SQL 语句，根据 id 更新用户的密码
db.query(sql, [newPwd, req.user.id], (err, results) => {
  // SQL 语句执行失败
  if (err) return res.cc(err)

  // SQL 语句执行成功，但是影响行数不等于 1
  if (results.affectedRows !== 1) return res.cc('更新密码失败！')

  // 更新密码成功
  res.cc('更新密码成功！', 0)
})
```

### 3.4 更新用户头像

#### 3.4.0 实现步骤

1. 定义路由和处理函数
2. 验证表单数据
3. 实现更新用户头像的功能

#### 3.4.1 定义路由和处理函数

1. 在 `/router/userinfo.js` 模块中，新增 `更新用户头像` 的路由：

```js
// 更新用户头像的路由
router.post('/update/avatar', userinfo_handler.updateAvatar)
```

1. 在 `/router_handler/userinfo.js` 模块中，定义并向外共享 `更新用户头像` 的路由处理函数：

```js
// 更新用户头像的处理函数
exports.updateAvatar = (req, res) => {
  res.send('ok')
}
```

#### 3.4.2 验证表单数据

1. 在 `/schema/user.js` 验证规则模块中，定义 `avatar` 的验证规则如下：

```js
// dataUri() 指的是如下格式的字符串数据：
// data:image/png;base64,VE9PTUFOWVNFQ1JFVFM=
const avatar = joi.string().dataUri().required()
```

1. 并使用 `exports` 向外共享如下的 `验证规则对象`：

```js
// 验证规则对象 - 更新头像
exports.update_avatar_schema = {
  body: {
    avatar,
  },
}
```

1. 在 `/router/userinfo.js` 模块中，导入需要的验证规则对象：

```js
const { update_avatar_schema } = require('../schema/user')
```

1. 在 `/router/userinfo.js` 模块中，修改 `更新用户头像` 的路由如下：

```js
router.post('/update/avatar', expressJoi(update_avatar_schema), userinfo_handler.updateAvatar)
```

#### 3.4.3 实现更新用户头像的功能

1. 定义更新用户头像的 SQL 语句：

```js
const sql = 'update ev_users set user_pic=? where id=?'
```

1. 调用 `db.query()` 执行 SQL 语句，更新对应用户的头像：

```js
db.query(sql, [req.body.avatar, req.user.id], (err, results) => {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 执行 SQL 语句成功，但是影响行数不等于 1
  if (results.affectedRows !== 1) return res.cc('更新头像失败！')

  // 更新用户头像成功
  return res.cc('更新头像成功！', 0)
})
```
