---
layout:     post
title:      "nodejs 教程"
subtitle:   "资源来自thenewboston"
date:       2018-03-15 16:00:00
author:     "LiuQ"
header-img: "img/nodejs.png"
header-mask: 0.3
catalog:    true
tags:
    - nodejs
    - express

---


## 准备工作

1，更换atom 的主题，主题下载完成后，重启atom，即可进行选择。
2,安装命令行插件 platformio-ide-terminal

## 4.global object

输出文件目录，文件所在目录，`__filename`,`__dirname`,
```js
    //延迟函数
    setTimeout(function(){
       console.log('3 seconds have passed ');
    },3000);



    //间隔函数
    var time = 0;
    var timer = setInterval(function(){

       time += 2;
       console.log(time + ' seconds have passed ');
       if (time > 5) {
         clearInterval(timer);
       }
       },2000);
```
## 5 函数表达式


1.


    //正常的函数声明

    function sayHi(){
      console.log('hi');
    }

    //函数调用
    sayHi();

    //function expression
    //通过变量生成函数，调用变量
    var sayBye = function(){
      console.log('bye');
    };

    sayBye();


2. 函数传参传的是函数


    //函数传参传的是函数

    function callFunction(fun){
      fun();
    }

    var sayBye = function(){
      console.log('bye');
    }

    callFunction(sayBye);

## 6 moudles & require

- 传参传的是数组，显示数组元素


    var counter = function(arr){
      return 'There are ' + arr.length + ' elements in this array';
    };

    console.log(counter(['shaun','crystal','ryu']));

-  create a moudle & require a moudle


    //count.js
    //create a moudle
    var counter = function(arr){
      return 'There are ' + arr.length + ' elements in this array';
    };
    module.exports = counter;

    //app.js
    //require a moudle
    var counter = require('./count');

    console.log(counter(['heheh','haha','xixi']));

- 导入多个变量与方法

具体到方法名与变量名：


    var counter = function(arr){
      return 'There are ' + arr.length + ' elements in this array';
    };
    //导出多个方法
    var adder = function(a,b){

      //注：return 后的符号为esc键，tab键上的
      return `The sum of the 2 numbers is ${a+b}`;
    };

    var pi = 3.142;

    module.exports.pi = pi;
    module.exports.adder = adder;
    module.exports.counter = counter;


在调用方法时，也需要具体到变量名和方法名

    var stuff = require('./stuff');

    console.log(stuff.counter(['heheh','haha','xixi']));
    console.log(stuff.adder(1,2));
    console.log(stuff.pi);


- 导出方法的形式，有3种


    //one
    var counter = function(arr){ return 'There are ' + arr.length + ' elements in this array';
    };
    var adder = function(a,b){  return `The sum of the 2 numbers is ${a+b}`;};
    var pi = 3.142;


    module.exports = {
      counter : counter,
      adder : adder,
      pi : pi
    };


    //two
    module.exports.pi = 3.142;
    module.exports.adder  = function(a,b){
     return `The sum of the 2 numbers is ${a+b}`;
     };
    module.exports.counter = function(arr){
       return 'There are ' + arr.length + ' elements in this array';
     };

     //three

     var counter = function(arr){ return 'There are ' + arr.length + ' elements in this array';
     };
     var adder = function(a,b){  return `The sum of the 2 numbers is ${a+b}`;};
     var pi = 3.142;

     module.exports.pi = pi;
     module.exports.adder = adder;
     module.exports.counter = counter;


## 8 监听事件event

- 注册监听事件&触发监听事件


[官方demo](https://nodejs.org/api/events.html)

    var events = require('events');
    var myEmitter = new events.EventEmitter();
    myEmitter.on('someEvent',function(mssg){
      console.log(mssg);
    });
    myEmitter.emit('someEvent','the event was emitted');

- 对events,util的使用，遍历events的监听方法


    var events = require('events');
    var utils =require('util');
    var Person = function(name){
      this.name = name;
    };
    utils.inherits(Person,events.EventEmitter);
    var james = new Person('james');
    var mary = new Person('mary');
    var ryu = new Person('ryu');
    var hehe = new Person('hehe');
    var person = [james,mary,ryu];
    person.forEach(function(person){
      person.on('speak',function(mssg){
        console.log(person.name + ' said: '+mssg);
      });
    });

    james.emit('speak','hey judes');
    james.emit('write','hey judes');
    mary.emit('speak','marry ');
    ryu.emit('speak','ryu');
    hehe.emit('speak','hehe');

## 9 core module file system

[api](https://nodejs.org/api/fs.html#fs_fs_writefilesync_file_data_options)

- 读取文件内容



    var fs = require('fs');
     readMe = fs.readFileSync('readMe.txt','utf8');
    console.log(readMe);

    //若使用readeFile，则需要写回掉函数

    fs.readFile('/etc/passwd', (err, data) => {
    if (err) throw err;
    console.log(data);
    });

- 写入文件

[官方demo](https://nodejs.org/api/fs.html#fs_fs_writefile_file_data_options_callback)

    var fs = require('fs');
    var readMe = fs.readFileSync('readMe.txt','utf8');
    console.log(readMe);
    fs.writeFileSync('writeMe.txt', readMe);

    //若使用writeFile,

    fs.writeFile('message.txt', 'Hello Node.js', (err) => {
      if (err) throw err;
      console.log('The file has been saved!');
    });

- 创建文件夹，添加文件&删除文件，删除文件夹


    var fs = require('fs');
    fs.mkdir('stuff',function(){
      fs.readFile('readMe.txt','utf8',function(err,data){
      fs.writeFile('./stuff/writeMe.txt',data);
    });
    });


    fs.unlink('./stuff/writeMe.txt',function(){
      fs.rmdir('stuff');
    });


## 11 client & Server

客户端请求数据时，发送请求request 以及请求头request headers
服务器回复数据时，回复response以及response headers

end方法的意义：

    //MUST be called on each response
    //signal to server that all of response headers and body have been sent,
    //server should consider this message complete
    res.end('Hey ninjas');



其中，Response headers包含两个内容

- Content-Type
- Status


-------
 将txt文件的内容通过`pipes`现在在页面上，

     var http = require('http');
     var fs = require('fs');
     var server = http.createServer(function(req,res){
       //send a response header  to the request
       console.log(req.url);
       res.writeHead(200,{'Content-Type':'text/plain'});
       var myReadStream = fs.createReadStream(__dirname + '/readMe.txt','utf8');
       myReadStream.pipe(res);
     });

     server.listen(3000,'127.0.0.1');
     console.log('listening ....');

## 13 buffer缓存

- 临时存储，
- 缓存区存满后，传输
- 一次传输小块数据

## 14 Streams

[api](https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options)

- Writable Streams -将数据写入到流中
- Readable Streams 从流中读取数据
- 双工 写入与读取流

读取文件时，一块一块读取，存到缓存中，而不是读取整个文件

调用 file system 的`createReadStream`时，不填写编码参数时，返回的结果是buffer,

    var myReadStream = fs.createReadStream(__dirname + '/readMe.txt');
    liu@ubuntuos:~/NodejsPro/tutorial4nodejs2/11_client_server$ node app.js
    new chunk received:
    <Buffer 54 68 65 20 66 69 72 73 74 20 74 69 6d 65 20 49 20 63 61 6d 65 20 74 6f 20 74 68 69 73 20 6d 61 72 6b 65 74 2c
    20 4d 72 2e 20 44 61 73 20 73 68 6f 77 ... >
    new chunk received:
    <Buffer 61 74 65 6c 79 2c 0a 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
    20 20 20 20 20 20 20 20 20 20 20 20 20 ... >
    new chunk received:
    <Buffer 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
    20 20 20 20 20 20 20 20 20 20 20 20 20 ... >
    new chunk received:
    <Buffer 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
    20 20 20 20 20 20 20 20 20 20 20 20 20 ... >
    new chunk received:
    <Buffer 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 65 61 63 68 20 6f 6e 65 20 61 20 6c 69
    74 74 6c 65 20 77 6f 6f 64 65 6e 2d 66 ... >

带参数(utf8编码)，返回文档内容：

    var myReadStream = fs.createReadStream(__dirname + '/readMe.txt','utf8');


## 15 Writable Streams


当文件中内容过多时，会将文件切成多份进行处理:

    var fs = require('fs');
    var myReadStream = fs.createReadStream(__dirname + '/readMe.txt','utf8');
    var myWriteStream = fs.createWriteStream(__dirname + '/writeMe.txt');
    myReadStream.on('data',function(chunk){
      console.log('new chunk received:');
      myWriteStream.write(chunk);
      });
    liu@ubuntuos:~/NodejsPro/tutorial4nodejs2/11_client_server$ node app.js
    new chunk received:
    new chunk received:
    new chunk received:
    new chunk received:
    new chunk received:


## 16 pipes 传输速度要快于buffer


    var fs = require('fs');
    var myReadStream = fs.createReadStream(__dirname + '/readMe.txt');
    var myWriteStream = fs.createWriteStream(__dirname + '/writeMe.txt');
    //通过pipes写入
    myReadStream.pipe(myWriteStream);

**note:** createReadStream才能调用[pipe](https://nodejs.org/api/stream.html#stream_readable_pipe_destination_options)

## 17 html

将html文件通过fs,pipes write


## 18 Serving JSON
输出json文件



      `//更改Content-Type 为'application/json'
      res.writeHead(200,{'Content-Type':'application/json'});
      var myObj = {
        name:'Ryu',
        job:'Ninja',
        age:29
      };
      res.end(JSON.stringify(myObj));`

## 21 package.json 文件

- What does the --save option mean?\


>[stackoverflow对参数-save的解释](https://stackoverflow.com/questions/19578796/what-is-the-save-option-for-npm-install)

`npm install express -save` 中的`-save`作用，执行上述命令安装express的同时也会在package.json文件中，添加`  "dependencies": {
    "express": "^4.16.3"
  }`,即使卸载express后，此信息仍在package.json中，设为依赖项。

- install nodemon

[nodemon 是一款非常实用的工具，用来监控 NodeJS 源代码的任何变化和自动重启你的服务器，这样我们只需要刷新页面就能看到你的改动](http://bubkoo.com/2014/12/02/use-nodemon-with-node-applications/)

省得更改代码后，再重启服务器，自动帮你重启了：


    [nodemon] starting `node app.js`
    [nodemon] restarting due to changes...
    [nodemon] starting `node app.js`
    [nodemon] restarting due to changes...
    [nodemon] starting `node app.js`

## 23 express

1 HTTP方法

- GET  
- POST
- DELETE
- PUT


2 响应Requests

- GET app.get('route',fn)
- POST app.post('route',fn)
- DELETE app.delete('route',fn)


## 24 Route(Express)
-------------------

- [API :app.get()](http://expressjs.com/en/api.html#app.get.method)


- [API : res.send()](http://expressjs.com/en/api.html#res.send)

>send参数中可发送，Buffer，String，Object ,数组

    res.send(new Buffer('whoop'));
    res.send({ some: 'json' });
    res.send('<p>some html</p>');
    res.status(404).send('Sorry, we cannot find that!');
    res.status(500).send({ error: 'something blew up' });




### code
    app.get('/profile/:id', function(req,res){
      res.send('You requested to see a profile with the id of ' + req.params.id);
    });

### 输入：

http://127.0.0.1:3000/profile/liuqhttp://127.0.0.1:3000/profile/liuq

### 页面显示：
You requested to see a profile with the id of liuq


## 25 模板引擎(Templating Engines)part1


- [API : res.sendFile()](http://expressjs.com/en/api.html#res.sendFile)


#### example:

请求`127:0.0.1:3000/liuq/hello.jpg`，则将位于/uploads/liuq/hello.jpg(更多的是填写类似与index.html的文件)显示在页面中：


    app.get('/user/:uid/photos/:file', function(req, res){
      var uid = req.params.uid
        , file = req.params.file;

      req.user.mayViewFilesFrom(uid, function(yes){
        if (yes) {
          res.sendFile('/uploads/' + uid + '/' + file);
        } else {
          res.status(403).send("Sorry! You can't see that.");
        }
      });
    });
比如：通过sendfile显示界面：

    var app = express();
    app.get('/',function(req,res){
      //res.send('this is the home page');
      res.sendFile(__dirname+ '/index.html')
    });


- 安装`ejs`


    npm install ejs -save

- 设置express框架中的view engines 为ejs


    app.set('view engine','ejs');

- ejs中传参的问题

前端profile.ejs:中的person,与后端person一致

    <body>
      <h1>Welcome to the profile of <%= person %></h1>
      <p>You wont find a website better anywhere else!</p>
    </body>

后端将输入的name转化为渲染需要的person:

    app.get('/profile/:name', function(req,res){
      //res.send('You requested to see a profile with the id of ' + req.params.id);
      res.render('profile',{person: req.params.name});
    });

[res.render:用法](https://expressjs.com/en/api.html#res.render)Renders a view and sends the rendered HTML string to the client

- 后端插入数据(name,data.age,data.job)，前端显示数据


    app.get('/profile/:name', function(req,res){
      var data = {age:29,job:'ninja'};
      res.render('profile',{person: req.params.name,data:data});
    });

    <body>
      <h1>Welcome to the profile of <%= person %></h1>
      <p><strong>Age: </strong><%= data.age %></p>
      <p><string>Job: </strong><%= data.job %></p>
    </body>

## 26 模板引擎(Templating Engines)part2

[HTML中`h`,`p`,`li`,`ul`的含义：](https://www.w3.org/community/webed/wiki/HTML/Training/Basic_content)


### Heading
***


    <h1>h1 example</h1>
    <h2>h2 example</h2>
    <h3>h3 example</h3>
    <h4>h4 example</h4>
    <h5>h5 example</h5>
    <h6>h6 example</h6>

![alt text](https://www.w3.org/community/webed/wiki/images/4/44/Tr_hn01.png "Logo Title Text 1")

### Paragraph
***

    <p>This is paragraph 1</p>
    <p>This is paragraph 2</p>

![alt text](https://www.w3.org/community/webed/wiki/images/8/8a/Tr_p01.png "Logo Title Text 1")  

### Line
***

    <p>This is paragraph 1</p>
    <hr>
    <p>This is paragraph 2</p>

![alt text](https://www.w3.org/community/webed/wiki/images/0/0f/Tr_hr01.png "Logo Title Text 1")

### List
***

#### unordered list(无序) ul,li



  <ul>
    <li>List 1</li>
    <li>List 2</li>
    <li>List 3</li>
  </ul>

![alt text](https://www.w3.org/community/webed/wiki/images/6/6c/Tr_ul01.png "Logo Title Text 1")


- [数组的foreach 处理](https://stackoverflow.com/questions/29190925/how-to-array-of-objects-in-template-ejs)：


    <body>
      <h1>Welcome to the profile of <%= person %></h1>
      <p><strong>Age: </strong><%= data.age %></p>
      <p><string>Job: </strong><%= data.job %></p>

      <h2>Hobbies</h2>
      <ul>
        <% data.hobbies.forEach(function(item){%>
          <li><%= item %></li>
        <%});%>
      </ul>
    </body>

## 27 链接的跳转


`partials/nav.ejs`:存放两个链接

    <nav>
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>

    index.ejs中嵌入<nav.ejs>
    <body>
      <% include partials/nav.ejs %>
      <h1>Welcome to the most awesome website on the net</h1>
      <p>You wont find a website better anywhere else!</p>
    </body>

## 28 Middleware 中间层

#### [app.use()](https://expressjs.com/en/4x/api.html#app.use)作用：
Mounts the specified middleware function or functions at the specified path: the middleware function is executed when the base of the requested path matches path.
******

    var express = require('express');
    var app = express();
    app.get("/", function(httpRequest, httpResponse, next){
        httpResponse.write("Hello");
        // next(); //remove this and see what happens
    });
    app.get("/", function(httpRequest, httpResponse, next){
        httpResponse.write(" World !!!");
        httpResponse.end();
    });


    app.listen(8080);

#### 中`next()`作用：

- 没有注释 `next()`运行效果:页面显示"Hello World  !!!"
- 注释`next()`后，error:"This page isn’t working"

**A route handler must end the request or call the next route handler.
路由要么以end结束请求，要么只向下一路由的handler
**

[参考1:中间件教程](http://qnimate.com/express-js-middleware-tutorial/)

[参考2:stackoverflow](https://stackoverflow.com/questions/10695629/what-is-the-parameter-next-used-for-in-express)

next()用来调用处理下一个路径的路由。


1 导入`css`文件，

    var app = express();
    app.use('/assets',express.static('assets'));  

导入成功后，网页中的主题格式可以正确显示。


## 29 Query String

[API:req.query()](https://expressjs.com/en/4x/api.html#req.query)

打印出`query`:

    app.get('/contact',function(req,res){
      console.log(req.query);
      res.render('contact');
    });
服务运行后，浏览器输入：`http://127.0.0.1:3000/contact?dept=marketing&person=joe`

控制台输出:

    { dept: 'marketing', person: 'joe' }


- query输入名字，职位，前端显示出来：


    app.get('/contact',function(req,res){
      // console.log(req.query);
      res.render('contact',{qs:req.query});
    });

  前端：

  <form id = "contact-form">
    <label for = "Who">Who do you want to contact?</label>
    <input type="text" name = "who" value="<%= qs.person %>">
    <label for="department">Which department?</label>
    <input type="text" name="department" value="<%= qs.dept %>">
    <label for="email">Your email</label>
    <input type="email" name="email"/>
    <input type="submit" value="submit"/>
  </form>

  输入：
  http://127.0.0.1:3000/contact?dept=IT&person=joe

## 30 POST 请求 (requests)

#### [app.post() Express API](https://expressjs.com/en/api.html#app.post.method) 使用指定的回调函数将HTTP POST请求路由到指定的路径。


- POST 请求时使用
- 请求服务器接收/存储数据
- 经常在点击提交时，使用

#### GET一般用于获取/查询资源信息，而POST一般用于更新资源信息。

```js
app.get('/contact',function(req,res){
   console.log("get: "+req.query);
  res.render('contact',{qs:req.query});
});

//post方法，和前端的'post'；‘contact’也和前端的'/contact'一一对应
app.post('/contact',urlencodedParser,function(req,res){
  //req从前端获取数据
  console.log(req.body);
  res.render('contact-success',{data:req.body});
});

```

##### 输入127.0.0.1:3000 先执行get,跳转到contact，点击submit 按钮后，执行post，更新内容，跳转到contact-success

    get: {}
    { who: 'liuq', department: 'IT', email: 'liuq@liuq' }
