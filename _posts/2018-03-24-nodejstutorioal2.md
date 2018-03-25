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


## 12 client & Server

客户端请求数据时，发送请求request 以及请求头request headers
服务器回复数据时，回复response以及response headers


其中，Response headers包含两个内容

- Content-Type
- Status
