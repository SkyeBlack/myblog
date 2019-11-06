---
title: keythereum使用
---

# 新建yourfilename.js文件

```bash
var express = require('express');//引入express
var app = express();//注册成app(这里我们也可以应用路由中间件 var router =express.Router(),接着在写接口时,将app替换为router)
var bodyParser = require('body-parser'); //引入插件包，解决post请求参数问题
app.use(bodyParser.json());//接收json
app.use(bodyParser.urlencoded({
    extended: true
}));
var keythereum = require("keythereum");//引入eth私钥插件
var md5 = require("nodejs-md5");//引入md5加密 
var fs = require('fs'); //文件模块
//设置跨域访问
app.all('*', function (req, res, next) {   //设置全局的全域问题，我这里是把所有的全部允许了，如果不怕麻烦或者业务需求，你也可以在请求里面写对应的跨域问题
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By", ' 3.2.1');
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});

var datadir;//data存放的地址路径
var key;
console.log("start");

fs.readFile('./config.json', 'utf-8', function(err, data) {
	if (err) {
		console.log('config error');		
		process.exit();
	} else {
		var jdata = JSON.parse(data)
		datadir = jdata.datadir;
		keydir = jdata.keydir;
		key = jdata.key;
		//配置服务端口
		app.listen(jdata.port) //监听服务端口
    }
});
	
//根据eth地址获取私钥
//地址请求用res.query获取值，json请求用res.body获取值
app.post('/getprikey',function (req, res) {
	try{	
		var address = req.body.address;
		var sign = req.body.sign;
		var signstr = address;
		var isSign = false;
		md5.string.quiet(signstr, function (err, md5) {
			if (err) {
				console.log(err);
			}
			else {
				md5 = md5.toUpperCase();
				if(sign == md5) 
				{
					isSign = true;			
				} else {
					console.log("signstr=" + signstr);
					console.log("sign=" + sign);
					console.log("md5=" + md5);
				}
			}
		});
		if(!isSign){
			res.json({error:"sign error"});
			return;
		}
				
		var keyObject = keythereum.importFromFile(address, datadir);  
		var privateKey = keythereum.recover(password, keyObject);  

		res.json({address:address,prikey:privateKey.toString('hex')});
	}
	catch(e){
		console.log(e);
		res.json({error:"error"});
	}
});		

//创建eth地址
app.post('/createAddress',function (req, res) {
	try{
		var password = req.body.password;
		var sign = req.body.sign;
		var signstr = password ;
		var isSign = false;
		md5.string.quiet(signstr, function (err, md5) {
			if (err) {
				console.log(err);
			}
			else {
				md5 = md5.toUpperCase();
				if(sign == md5) 
				{
					isSign = true;			
				} else {
					console.log("signstr=" + signstr);
					console.log("sign=" + sign);
					console.log("md5=" + md5);
				}
			}
		});
		if(!isSign){
			res.json({error:"sign error"});
			return;
		}
		
		var dk = keythereum.create();
		var keyObject = keythereum.dump(password, dk.privateKey, dk.salt, dk.iv);		
		keythereum.exportToFile(keyObject,datadir + "keystore/");
		res.json({address:"0x"+keyObject.address,prikey:dk.privateKey.toString('hex')});		
	}
	catch(e){
		console.log(e);
		res.json({error:"error"});
	}
});

```
# 配置文件config.js

```bash
{
	"datadir":"D:/Tool/ETH/v1.9.2/testdata/",
	"port":xxxx,
	"key":"xxxxxxx"
}

```
# 安装依赖
需要node环境

```bash
npm install express
npm install body-parser
npm install express-xml-bodyparser
npm install keythereum
npm install nodejs-md5
```

# 执行命令
`node yourfilename.js`