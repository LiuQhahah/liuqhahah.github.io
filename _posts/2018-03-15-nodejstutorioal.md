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

##01在windows上安装
##02 在MacOS上安装
##03基础概念

创建`app.js`，使用 vim或其他编辑器打开，

1.   
创建 person变量并赋值，

	var person = {
		firstname: "Bucky",
		lastname:"Roberts",
		age:28
		};
	console.log(person);
输出结果：

	liu@ubuntu:~/nodejsPro/tutorial/03$ node app.js 
	{ firstname: 'Bucky', lastname: 'Roberts', age: 28 }
	liu@ubuntu:~/nodejsPro/tutorial/03$ 
	
	
2. 

创建函数

	//02创建函数
	function addNumber(a,b){
		return a+b;
	}

	console.log(addNumber(11,11));
	
控制台输出：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/03$ node app.js 
	22
有意思的是，在定义的函数方法中，若为空，同时在控制台上执行函数，那么控制台输出的结果则是`undefined`
具体代码：

	//02创建函数
	function addNumber(a,b){
	
	}

	console.log(addNumber(11,11));

3. 定义变量函数以及延迟函数

函数：

	var printBacon = function(){
		console.log("Bacon is healthy ,dont believe the doctors");
	};//注有分号结束符
	printBacon();//此时有括号，而不仅仅是变量名

	setTimeout(printBacon,3000);//此时没有括号，奇怪
	
输出结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/03$ node app.js 
	Bacon is healthy ,dont believe the doctors
	Bacon is healthy ,dont believe the doctors

##04处理多个请求（）

nodejs可以处理多个请求，
代码：

	function placeAnOrder(orderNumber){
	  console.log("Customer order:",orderNumber);


	  cookAndDeliverFood(function(){
	    console.log("Delivered food order:",orderNumber);
	  });
	}

	//模拟一个5秒的操作
	function cookAndDeliverFood(callback){
	  setTimeout(callback,5000);
	}

	//模拟用户的web请求
	placeAnOrder(1);
	placeAnOrder(2);
	placeAnOrder(3);
	placeAnOrder(4);
	placeAnOrder(5);

执行placeAnOrder(1);并不是等到placeAnOrder(orderNumber)中的函数体cookAndDeliverFood(function()执行完后，才接着执行，而是在等待的空隙执行placeAnOrder(2);。。。。等

输出结果：

	Customer order: 1
	Customer order: 2
	Customer order: 3
	Customer order: 4
	Customer order: 5
	//等待5s后，接着输出
	Delivered food order: 1
	Delivered food order: 2
	Delivered food order: 3
	Delivered food order: 4
	Delivered food order: 5
	
	
	
**补**
nodejs的异步I/O，异步I/O的特点就是，每接收一个请求，使用异步调用处理请求，不用等待结果，可以继续运行其他操作，也就是说可以继续接受请求

	//server.js
	//处理一个请求需要5s，通过设置setTimeout设置5s后响应
	var http = require("http");
	var n =0;
	http.createServer(function(req,res){
	  n++;
	  setTimeout(function(){
	    console.log("Accept "+n+"request.");
	    res.end("test");
	  },5000);
	}).listen(3000);


	// client.js
	// 发送一个请求
	exports.send = function () {
	    var http = require('http');
	    var options = {
		host: 'localhost',
		port: '3000',
		path: '/',
		method: 'GET',
	    };

	    var req = http.request(options, function(res){
		exports.counter += 1;
		res.setEncoding('utf8');
		res.on('data', function (c) {
		   console.log(c);
		});
		res.on('end', function() {
		    exports.seccess += 1;
		    console.log("Response: " + exports.seccess);
		});
	    });
	    req.end();
	};

	exports.seccess = 0;


	// attack.js
	// 在1秒内发出约50000个请求
	var client = require('./client');

	var d = 1000,
	    t = Date.now();
	while(Date.now() - t < d) {
	    client.send();
	}
	console.log('end.');


先运行`node server.js`,输出结果：

	AAccept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.
	Accept 9894request.

	
运行`node attack.js`，输出结果：


	test
	Response: 9887
	test
	Response: 9888
	test
	Response: 9889
	test
	Response: 9890
	test
	Response: 9891
	test
	Response: 9892
	test
	Response: 9893
	test
	Response: 9894



##5有趣的References以及==与===

代码：

	var Bucky= {
	  favFood: "bacon",
	  favMovie:"Chappie"
	};

	var Person = Bucky;
	Person.favFood = "salad";
	console.log(Bucky.favFood);
	console.log(Person.favFood);
	//?salad 是Person的favFood的，但是输出的是Bucky的favFood
	//创建Person并不是一个复制一个Bucky对象，而是作为Bucky的References，同时也改变Bucky的值
	//== 与===的不同
	console.log(19=="19");
	console.log(19==="19");
	
输出：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/5$ node app.js 
	salad
	salad
	true
	false

 
 ##06关于`this`范围的探讨
 
 代码：
 
	 var Bucky = {
	  printFirstName :function(){
	    console.log("My name is Bucky");
	    console.log(this === Bucky);
	    console.log(this == Bucky);
	    console.log(this);
	  }
	}

	Bucky.printFirstName();

	function doSomethingWorthless(){
	  console.log("\n I am worthless");
	  // console.log(this);
	  console.log(this=== global);//true

	}

	doSomethingWorthless();
	
结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/6this$ node app.js 
	My name is Bucky
	true
	true
	{ printFirstName: [Function] }

	 I am worthless
	true

Bucky中的this，通过log输出的是`{ printFirstName: [Function] }`;

但是`doSomethingWorthless`中，this的输出，较为丰富：

代码：

	var Bucky = {
	  printFirstName :function(){
	    // console.log("My name is Bucky");
	    // console.log(this === Bucky);
	    // console.log(this == Bucky);
	    // console.log(this);
	  }
	}

	Bucky.printFirstName();

	function doSomethingWorthless(){
	  console.log("\n I am worthless");
	  console.log(this);
	  // console.log(this=== global);//true

	}

	doSomethingWorthless();
	
输出结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/6this$ node app.js 

	 I am worthless
	{ global: [Circular],
	  process: 
	   process {
	     title: 'node',
	     version: 'v4.2.6',
	     moduleLoadList: 
	      [ 'Binding contextify',
		'Binding natives',
		'NativeModule events',
		'NativeModule buffer',
		'Binding buffer',
		'NativeModule internal/util',
		'Binding util',
		'NativeModule timers',
		'Binding timer_wrap',
		'NativeModule _linklist',
		'NativeModule assert',
		'NativeModule util',
		'Binding uv',
		'NativeModule path',
		'NativeModule module',
		'NativeModule internal/module',
		'NativeModule vm',
		'NativeModule fs',
		'Binding fs',
		'NativeModule constants',
		'Binding constants',
		'NativeModule stream',
		'NativeModule _stream_readable',
		'NativeModule _stream_writable',
		'NativeModule _stream_duplex',
		'NativeModule _stream_transform',
		'NativeModule _stream_passthrough',
		'Binding fs_event_wrap',
		'NativeModule console',
		'Binding tty_wrap',
		'NativeModule tty',
		'NativeModule net',
		'Binding cares_wrap',
		'Binding tcp_wrap',
		'Binding pipe_wrap',
		'Binding stream_wrap',
		'Binding signal_wrap' ],
		versions: 
	      { http_parser: '2.5.0',
		node: '4.2.6',	
		v8: '4.5.103.35',
		uv: '1.8.0',
		zlib: '1.2.8',
		ares: '1.10.1-DEV',
		icu: '55.1',
		modules: '46',
		openssl: '1.0.2g-fips' },
	     arch: 'x64',
	     platform: 'linux',
	     release: 
	      { name: 'node',
		lts: 'Argon',
		sourceUrl: 'https://nodejs.org/download/release/v4.2.6/node-v4.2.6.tar.gz',
		headersUrl: 'https://nodejs.org/download/release/v4.2.6/node-v4.2.6-headers.tar.gz' },
	     argv: 
	      [ '/usr/bin/nodejs',
		'/home/liu/nodejsPro/tutorial4nodejs/6this/app.js' ],
	     execArgv: [],
	     env: 
	      { XDG_VTNR: '7',
		LC_PAPER: 'en_US.UTF-8',
		LC_ADDRESS: 'en_US.UTF-8',
		XDG_SESSION_ID: 'c1',
		rvm_bin_path: '/usr/share/rvm/bin',
		LC_MONETARY: 'en_US.UTF-8',
		CLUTTER_IM_MODULE: 'xim',
		XDG_GREETER_DATA_DIR: '/var/lib/lightdm-data/liu',
		SESSION: 'ubuntu',
		GPG_AGENT_INFO: '/home/liu/.gnupg/S.gpg-agent:0:1',
		XDG_MENU_PREFIX: 'gnome-',
		TERM: 'screen',
		SHELL: '/bin/bash',
		VTE_VERSION: '4205',
		QT_LINUX_ACCESSIBILITY_ALWAYS_ON: '1',
		LC_NUMERIC: 'en_US.UTF-8',
		WINDOWID: '65011722',
		OLDPWD: '/home/liu/nodejsPro/tutorial4nodejs',
		GNOME_KEYRING_CONTROL: '',
	 UPSTART_SESSION: 'unix:abstract=/com/ubuntu/upstart-session/1000/1180',
		GTK_MODULES: 'gail:atk-bridge:unity-gtk-module',
		USER: 'liu',
		LC_TELEPHONE: 'en_US.UTF-8',
		QT_ACCESSIBILITY: '1',
		LS_COLORS: 'rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:
	tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:
	*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;
	31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31
	:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:
	*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35
	:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;3
	5:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*
	.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*
	.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:',
		_system_type: 'Linux',
		XDG_SESSION_PATH: '/org/freedesktop/DisplayManager/Session0',
		rvm_path: '/usr/share/rvm',
		XDG_SEAT_PATH: '/org/freedesktop/DisplayManager/Seat0',
		SSH_AUTH_SOCK: '/run/user/1000/keyring/ssh',
		DEFAULTS_PATH: '/usr/share/gconf/ubuntu.default.path',
		TMUX: '/tmp/tmux-1000/default,2926,0',
		XDG_CONFIG_DIRS: '/etc/xdg/xdg-ubuntu:/usr/share/upstart/xdg:/etc/xdg',
		rvm_prefix: '/usr/share',
		DESKTOP_SESSION: 'ubuntu',
		PATH: '/home/liu/bin:/home/liu/.local/bin:/home/liu/bin:/home/liu/.local/bin:/home/liu/bin:/home/liu/.local/bin:/usr/local/sbin:/usr/loca
	l/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/bin:/usr/share/rvm/bin:/snap/bin',
		QT_IM_MODULE: 'fcitx',
		QT_QPA_PLATFORMTHEME: 'appmenu-qt5',
		LC_IDENTIFICATION: 'en_US.UTF-8',
		EVENT_NOEPOLL: '1',
		PWD: '/home/liu/nodejsPro/tutorial4nodejs/6this',
		JOB: 'unity-settings-daemon',
		XDG_SESSION_TYPE: 'x11',

	XMODIFIERS: '@im=fcitx',
        LANG: 'en_US.UTF-8',
        GNOME_KEYRING_PID: '',
        MANDATORY_PATH: '/usr/share/gconf/ubuntu.mandatory.path',
        GDM_LANG: 'en_US',
        LC_MEASUREMENT: 'en_US.UTF-8',
        _system_arch: 'x86_64',
        TMUX_PANE: '%0',
        IM_CONFIG_PHASE: '1',
        COMPIZ_CONFIG_PROFILE: 'ubuntu',
        _system_version: '16.04',
        PAPERSIZE: 'letter',
        GDMSESSION: 'ubuntu',
        rvm_version: '1.29.3 (manual)',
        GTK2_MODULES: 'overlay-scrollbar',
        SESSIONTYPE: 'gnome-session',
        HOME: '/home/liu',
        XDG_SEAT: 'seat0',
        SHLVL: '2',
        LANGUAGE: 'en_US:en',
        GNOME_DESKTOP_SESSION_ID: 'this-is-deprecated',
        UPSTART_INSTANCE: '',
        XDG_SESSION_DESKTOP: 'ubuntu',
        LOGNAME: 'liu',
        UPSTART_EVENTS: 'xsession started',
        COMPIZ_BIN_PATH: '/usr/bin/',
        XDG_DATA_DIRS: '/usr/share/ubuntu:/usr/share/gnome:/usr/local/share/:/usr/share/:/var/lib/snapd/desktop',
        QT4_IM_MODULE: 'fcitx',
        DBUS_SESSION_BUS_ADDRESS: 'unix:abstract=/tmp/dbus-ikWXzGdCb4',
        LESSOPEN: '| /usr/bin/lesspipe %s',
        UPSTART_JOB: 'unity7',
        INSTANCE: '',
        DISPLAY: ':0',
        XDG_RUNTIME_DIR: '/run/user/1000',
        GTK_IM_MODULE: 'fcitx',
        XDG_CURRENT_DESKTOP: 'Unity',
        LC_TIME: 'en_US.UTF-8',
        LESSCLOSE: '/usr/bin/lesspipe %s %s',
        _system_name: 'Ubuntu',
        LC_NAME: 'en_US.UTF-8',

	XAUTHORITY: '/home/liu/.Xauthority',
        _: '/usr/bin/node' },
     pid: 13395,
     features: 
      { debug: false,
        uv: true,
        ipv6: true,
        tls_npn: true,
        tls_sni: true,
        tls_ocsp: true,
        tls: true },
     _needImmediateCallback: false,
     execPath: '/usr/bin/nodejs',
     debugPort: 5858,
     _startProfilerIdleNotifier: [Function: _startProfilerIdleNotifier],
     _stopProfilerIdleNotifier: [Function: _stopProfilerIdleNotifier],
     _getActiveRequests: [Function: _getActiveRequests],
     _getActiveHandles: [Function: _getActiveHandles],
     reallyExit: [Function: reallyExit],
     abort: [Function: abort],
     chdir: [Function: chdir],
     cwd: [Function: cwd],
     umask: [Function: umask],
     getuid: [Function: getuid],
     geteuid: [Function: geteuid],
     setuid: [Function: setuid],
     seteuid: [Function: seteuid],
     setgid: [Function: setgid],
     setegid: [Function: setegid],
     getgid: [Function: getgid],
     getegid: [Function: getegid],
     getgroups: [Function: getgroups],
     setgroups: [Function: setgroups],
     initgroups: [Function: initgroups],
     _kill: [Function: _kill],
     _debugProcess: [Function: _debugProcess],
     _debugPause: [Function: _debugPause],
     _debugEnd: [Function: _debugEnd],
     hrtime: [Function: hrtime],
     dlopen: [Function: dlopen],

	uptime: [Function: uptime],
     memoryUsage: [Function: memoryUsage],
     binding: [Function: binding],
     _linkedBinding: [Function: _linkedBinding],
     _setupDomainUse: [Function: _setupDomainUse],
     _events: 
      { newListener: [Function],
        removeListener: [Function],
        SIGWINCH: [Object] },
     _rawDebug: [Function],
     domain: null,
     _maxListeners: undefined,
     EventEmitter: 
      { [Function: EventEmitter]
        EventEmitter: [Circular],
        usingDomains: false,
        defaultMaxListeners: 10,
        init: [Function],
        listenerCount: [Function] },
     _fatalException: [Function],
     _exiting: false,
     assert: [Function],
     config: { target_defaults: [Object], variables: [Object] },
     nextTick: [Function: nextTick],
     _tickCallback: [Function: _tickCallback],
     _tickDomainCallback: [Function: _tickDomainCallback],
     stdout: [Getter],
     stderr: [Getter],
     stdin: [Getter],
     openStdin: [Function],
     exit: [Function],
     kill: [Function],
     _eventsCount: NaN,
     mainModule: 
      Module {
        id: '.',
        exports: {},
        parent: null,
        filename: '/home/liu/nodejsPro/tutorial4nodejs/6this/app.js',
        loaded: false,
	children: [],
		paths: [Object] } },
	  GLOBAL: [Circular],
	  root: [Circular],
	  Buffer: 
	   { [Function: Buffer]
	     poolSize: 8192,
	     isBuffer: [Function: isBuffer],
	     compare: [Function: compare],
	     isEncoding: [Function],
	     concat: [Function],
	     byteLength: [Function: byteLength] },
	  clearImmediate: [Function],
	  clearInterval: [Function],
	  clearTimeout: [Function],
	  setImmediate: [Function],
	  setInterval: [Function],
	  setTimeout: [Function],
	  console: [Getter] }
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/6this$ 
	
	
	
##7 prototype

>https://cnodejs.org/topic/5220baf4bee8d3cb1234d1c7 对prototype的讨论
>https://github.com/mqyqingfeng/Blog/issues/2 JavaScript深入之从原型到原型链

代码：


	function User(){
	  this.name = "";
	  this.life = 100;
	  this.giveLife = function giveLife(targetPlayer){
	    targetPlayer.life +=1;
	    this.life -=1;
	    console.log(this.name +" gave 1 life to "+targetPlayer.name);
	  }
	}

	var Bucky = new User();
	var Wendy = new User();
	Bucky.name = "Bucky";
	Wendy.name = "Wendy";

	Bucky.giveLife(Bucky);
	Bucky.giveLife(Wendy);
	console.log(Bucky.name+" "+Bucky.life);
	console.log(Wendy.name+" "+Wendy.life);

	//You can add functions to all objects
	User.prototype.uppercut = function uppercut(targetPlayer){
	  targetPlayer.life -=3;
	  this.life+=3;
	  console.log(this.name + " just uppercutted "+targetPlayer.name);
	};

	Wendy.uppercut(Bucky);
	console.log("Bucky "+Bucky.life);
	console.log("Wendy "+Wendy.life);


	//对于所有的对象添加原型prototype
	User.prototype.magic = 60;
	console.log("Bucky.magic "+Bucky.magic);
	console.log("Wendy.magic "+Wendy.magic);
	
代码输出：

使用下`this`的含义，
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/7Prototype$ node app.js 
	Bucky gave 1 life to Bucky
	Bucky gave 1 life to Wendy
	Bucky 99
	Wendy 101
	Wendy just uppercutted Bucky
	Bucky 96
	Wendy 104
	Bucky.magic 60
	Wendy.magic 60


##08模块化


app.js中调用movies.js中的方法

	//app.js
	var movies = require('./movies');

	movies.avator();
	movies.chappie();

	//movies.js
	function printAvatars(){
	  console.log("Avators: PG-13");
	}

	function printChappie() {
	  console.log("Chappie: R");
	}

	module.exports.avator = printAvatars;
	module.exports.chappie = printChappie;


结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/8Modules$ node app.js 
	Avators: PG-13
	Chappie: R
	
##9对模块的改进

代码：

	//movie.js
	//更加方便的导出方法
	module.exports = {
	  printAvatars:function(){
	    console.log("Avators");
	  },
	  printChappie:function(){
	    console.log("Chappie");
	  },

	//特殊的定义方式
	  favMovie : "The Matrix"
	};

	//app.js
	var movies = require('./movies');

	// movies.avator();
	// movies.chappie();

	movies.printAvatars();

	console.log(movies.favMovie);

输出：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/8Modules$ node app.js 
	Avators
	The Matrix


##10模块的继承的继承

	//app.js
	require('./emily');
	require('./bucky')

	//movies.js
	module.exports = {
	  favMovie : ""
	};

	//emily.js
	var movies = require('./movies');
	movies.favMovie = "The NoteBook";
	console.log("Emilys favorite movie is :"+movies.favMovie);

	//ducky.js
	var movies = require('./movies');

	console.log("Emilys favorite movie is :"+movies.favMovie);


输出结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/10Moudles$ node app.js 
	Emilys favorite movie is :The NoteBook
	Emilys favorite movie is :The NoteBook


##11 对象工厂（与10不同）

-----------------------------
new 一个对象出来，和bucky中不一样的对象，与10Moudles中比较，输出结果不同，10中的movies中，定义的是变量，对变量赋值，赋的是movies中的值，所以当emliy赋值过后，bucky中再次调用时，变量值则发生变化，但是11例中的对象不一样，emliy.js与bucky.js new出了两个Object对象，所以结果不同。

当不需要共享时，可以创建不同的对象，实现独立的对象操作
代码：



	//emliy.js
	var movies = require('./movies');


	var emilyMovies = movies();
	emilyMovies.favMovie = "The Notebook";
	console.log("emilyMovies.favMovie: "+emilyMovies.favMovie);

	//movies.js
	//函数
	module.exports = function(){
	  return {
	    favMovie: ""
	  }
	};

	//bucky.js

	var movies = require('./movies');
	//new 一个对象出来
	var buckyMovies = movies();
	console.log("buckyMovies.favMovie: "+buckyMovies.favMovie);

	//app.js
	require('./emily');
	require('./bucky')
	
	
输出：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/11Object$ node app.js
	emilyMovies.favMovie: The Notebook
	buckyMovies.favMovie: 
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/11Object$

**注：**与10输出结果不同

##12第三方库的使用

1. 

`fs`:file system
创建文件，写入内容，读取文件。

	//调用第三方库时，不要加上路径，nodejs认为加了路径则是调用自己的库，
	//而不调用系统库
	var fs = require('fs');

	 //创建文件写入内容
	 fs.writeFileSync("corn.txt","Corn is good , corn is life");

	 //读取文件内容
	 console.log(fs.readFileSync("corn.txt").toString());
输出结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ node app.js 
	Corn is good , corn is life

2. 格式化文件路径

		//格式化路径
		var path = require('path');
		var websiteHome = "Desktop/Bucky//thenewboston/index.html";

		console.log(path.normalize(websiteHome));
	
输出结果：（注意输入的字符串与格式化后的不同）

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ node app.js 
	Desktop/Bucky/thenewboston/index.html
	
3. 输出文件夹名，basename，extname 后缀名


代码：

	//格式化路径
	var path = require('path');

	var websiteAbout = "Desktop/Bucky/thenewboston/about.html";

	//输出文件夹名，basename，extname
	console.log(path.dirname(websiteAbout));
	console.log(path.basename(websiteAbout));
	console.log(path.extname(websiteAbout));

结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ node app.js 
	Desktop/Bucky/thenewboston
	about.html
	.html
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ 
	
4. 循环执行

代码：

	//循环执行,隔2s执行一次
	setInterval(function(){
	  console.log("beer");
	},2000)


结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ node app.js 
	beer
	beer
	beer
	beer
	^C
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ 
	
5. 输出当前文件夹名以及文件路径


		console.log(__dirname);
		console.log(__filename);
	
	
结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ node app.js 
	/home/liu/nodejsPro/tutorial4nodejs/12lib
	/home/liu/nodejsPro/tutorial4nodejs/12lib/app.js
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/12lib$ 

##13创建server
请求服务器时，即使只请求一些（刷新一次），但是同时会请求一次获取小图标的信息favicon.ico

代码：

	var http = require('http');

	function onRequest(request,response){
	  console.log("A user made a request"+request.url);
	  response.writeHead(200,{"Context-Type":"text/plain"});
	  response.write("Here is some data");
	  response.end();
	}

	http.createServer(onRequest).listen(8888);
	console.log("Server is running ...");

输出结果：
	
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/13server$ node server.js 
	Server is running ...
	A user made a request/
	A user made a request/favicon.ico
	A user made a request/
	A user made a request/favicon.ico

##14添加html请求（请求结果为404）

创建index.html文件：

	<!DOCTYPE html>
	<html>
	<head lang="en">
	  <meta charset="utf-8">
	  <title>thenewboston</title>

	</head>

	<body>
	  Wow this site is awesome!
	</body>
	</html>

server.js文件：

	var http = require('http');
	var fs = require('fs');

	//404 response
	function send404Response(response){
	  response.writeHead(404,{"Content-Type":"text/plain"});
	  response.write("Error 404 : Page not found!");
	  response.end();
	}

	//Handle a user request
	function onRequest(request,response){
	  if (request.method =='GET' && request.url == '/') {
	    //  显示html网页
	    response.writeHead(200,{"Content-Type":"text/html"});
	    fs.createReadStream('./index.html').pipe(response);
	  }else {
	    send404Response(response);
	  }
	}

	http.createServer(onRequest).listen(8888);
	console.log("Server is now running....");


##15 Connect

安装connect包

在文件目录下，输入`npm install connect`

1. 使用next参数，函数调用


		//https://github.com/senchalabs/connect
		var connect = require('connect');
		var http = require('http');

		var app=  connect();

		//使用next参数
		function doFirst(request,response,next){
		  console.log("Bacon");
		  next();
		}

		function doSecond(request,response,next){
		console.log("Tuna");
		next();
		}


		app.use(doSecond);
		app.use(doFirst);

		http.createServer(app).listen(8888);
		console.log("Server is running...");

输出结果：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/15folder$ node server.js 
	Server is running...
	Tuna
	Bacon




2. 127.0.0.1:8888/profile 输出



		//127.0.0.1:8888/profile 输出
		function profile(request,response){
		  console.log("User requested profile");
		}

		//127.0.0.1:8888/forum 输出
		function forum(request,response){
		  console.log("User request forum");
		}

		app.use('/profile',profile);
		app.use('/forum',forum);

		http.createServer(app).listen(8888);
		console.log("Server is running...");
	
输出结果：

		liu@ubuntu:~/nodejsPro/tutorial4nodejs/15folder$ node server.js 
		Server is running...
		User requested profile
		User request forum


##16express

>参考链接：
http://blog.ijasoneverett.com/2013/03/a-sample-app-with-node-js-express-and-mongodb-part-1/

执行后，

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/16express$ express -c stylus
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/16express$ npm install -d

生成的文件根目录：

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/16express$ ls
	app.js  bin  node_modules  package.json  public  routes  views
	liu@ubuntu:~/nodejsPro/tutorial4nodejs/16express$ 
	
	
执行：bin目录下的www文件

	liu@ubuntu:~/nodejsPro/tutorial4nodejs/16express$ ./bin/www 
	GET / 200 308ms - 170b
	GET /stylesheets/style.css 200 288ms - 110b

打开链接127.0.0.1:3000

输出的内容是`/views/index.jade`中的内容

	extends layout

	block content
	  h1= title
	  p Welcome to #{title}
	  



