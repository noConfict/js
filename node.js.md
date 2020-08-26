## fs模块

fs模块提供了很多有用的方法，需要先去安装他，并且从服务端引入进来。

```js
const fs = require('fs')
```

文件系统模块

fs.readdir(path)：读取文件内容

fs.statSync(path)：查看文件状态，把API获取到的返回结果存储到变量中

fs.stat(path，callback)：异步，callback(err,result)  err：可能产生的错误   result：查看的结果，结果为Stats对象

fs.mkdir(path,callback(err))：异步创建目录

fs.makdirSync(path)：同步创建目录

fs.write(path,buffer,callback(err,bytewritten,buffer))：写入buffer到指定的文件，byteWritten指定 从buffer中被写入的字节数

fs.write(path,string)：将string写入指定的文件，string不是字符串，则会抛出异常

fs.writeFile(file,data,callback)：异步地写入数据到文件，如果文件已存在，则覆盖文件，如果文件不存在则创建文件。

fs.appendFile(path,data,callback)：追加式写入数据到文件

fs.readFile(path,callback(err,data))：读取文件，data为读取到的数据，格式为buffer

fs.unlink(path,callback(err))：异步删除文件或符号链接

fs.unlinkSync(path)：同步删除文件

fs.existsSync(path)：判断文件是否存在，该方法的异步已弃用

fs.copyFile(src,dest,callback(err))：异步地将src拷贝到dest，如果dest已经存在，则覆盖它，src表示源文件，dest拷贝操作的目标文件名

fs.createReadStream(path)：创建可读取的流对象，一旦有数据流入，获取这段数据，rs.on('data',callback(chunk_data)) on用于添加事件，data是一个事件名称，数据流入事件，固定名称,chunk_data：流入的分段数据；end结束的事件名称

fs.createWriteStream(path)：创建可写入的流对象

通过流复制大型文件1.zip到2.zip

```js
let rs = fs.createReadStream('./1.zip');
// 创建写入的流
let ws = fs.createWriteStrem('./2.zip');

rs.pipe(ws)      
```

## 同步和异步

​		同步：阻止后续代码的执行，只有执行完当前才会往后执行其它的；通过返回值获取结果。

​		异步：不阻止后续代码的执行，通过回调函数获取结果

## http协议

客户端浏览器和web服务器之间的协议

（1）通用头信息

​	Request URL：请求的URL，浏览器端请求的内容

​	Requst Method：get获取，得到  post

​	StatusCode：响应的状态码

​		1**：服务器收到请求，需要请求者继续执行操作

​		2**：成功的响应

​		3**：重定向，需要进一步的操作以完成请求

​		4**：客服端请求错误

​		5**：服务器端错误

（2）响应（response）头信息

​	Content-Type：响应内容类型

​	Location：跳转的URL

（3）请求头信息

（4）用于向服务器传递信息

## http模块

创建web服务器

```js
const http = require('http');//引入http模块
const app = http.createServer();//创建服务器，返回serve对象
app.listen(8080);//监听8080端口
```

服务器端根据浏览器端的请求做出响应

事件：一旦有请求，自动执行回调函数

```js
app.on('request',(request,response)=>{
    response.write('hello');
    response.end();//发送响应
})
```

response的API

response.writeHead(statusCode\[,statusMessage][,headers])：向请求发送响应头。statusCode：状态码，这个方法设置的响应头高于setHead()方法

response.wirte()：设置响应的内容

response.end()：结束比发送响应，如果end函数添加内容相当于调用了write()

## express

第三方的模块

基于Node.js平台，快速、开放、极简的WEB开发框架

下载安装  npm install express

（1）创建web服务器

```js
const express = require('express');
const app = express();//创建web服务器
app.listen(8080);//设置端口
```

（2）路由

用来接收请求，以及做出响应

可以处理特定的请求，包括请求的方法，请求的URL、回调函数

当浏览器发出的请求匹配的方法和请求的URL就会自动触发该路由执行回调函数

get请求方法

```js
app.get('/login',(request,response)=>{
    response.send();//设置响应的内容并发送
    response.redirect('https://www.baidu.com');//重定向
    response.sendFile();//设置响应的文件并发送，文件需要使用绝对路径
})
```

| 传递方式 | 格式                     | 路由中获取                                                   |
| -------- | ------------------------ | ------------------------------------------------------------ |
| get      | 查询字符串<br />keyword= | req.query<br />结果为对象                                    |
| post传递 | 流（不可见）             | req.on('data',(chunk)=>{<br />//chunk是分段获取的数据，格式为buffer，<br />需要转为字符串，格式为查询字符串}) |
| 路由传参 | 通过URL/package/npm      | 在路由中设置参数<br />app.get('/package/:pname',(req,res)=>{<br />req.params.pname//获取pname}) |

（3）路由器

为了解决不同模块下的URL可能出现相同，和所有的路由都是在服务器下，无法进行团队的模块化的问题，把同一个模块下的路由放在一起，最后把路由器挂载在服务器下。

router.js

```js
const express = require('express');
//使用express的Router方法创建路由器
const route = express.Router();

route.get('/list',(req,res)=>{
	res.send('list')
})
// 导出路由
module.exports = route
```

server.js

```js
const route = require('./router.js');
app.use('/user',route)// 挂载route到服务器
```

## 中间件

express中的中间件

​		浏览器想web服务器发送请求，中间件可以拦截到对路由的请求，也可以做出响应。中间件分为应用级中间件、路由中间件、内置中间件、第三方中间件、错误处理中间件。

（1）应用级中间件，也称为自定义中间件

```js
app.use(url,(req,res,next)=>{});
// url:表示需要拦截的路由
```

例子：

```js
app.use('/list',(req,res,next)=>{
    console.log(req.requery);
    if(req.query.username != 'root'){
        res.send('不是管理员');
    }else{
        next();
    }
})
```

（2）路由级中间件

就是路由器的使用

（3）内置中间件

就是express中自带的中间件

**托管静态资源中间件**

静态资源：html，css，js，图像，视频

使用托管静态资源的中间件，让浏览器端自动找文件。

```js
app.use('/public',express.static('./public'));
//前面的url表示前端可以通过这个url来获取静态资源，后面的public表示后端存放静态资源的路径
```



## mysql模块

