# node.js + vue.js 搭建ueditor   

> A Vue.js project 
>
> nodejs  ueditor  can upload images 

## Build Setup

``` bash
# install dependencies
npm install

# 运行server服务器，部署在8888端口号上
cd server
node index.js


# 运行客户端 部署在端口号8080上，热加载
npm run dev

# build for production with minification
npm run build

```



安装server:

1. npm init

2. 服务器下载安装express和ueditor    npm install --save express    和ueditor

3. 建立public 文件夹，将ueditor中的example中的public复制到刚建立的public下

4. 建立index.js文件，代码为：


       var express =require('express');
       var app=express();
       var ueditor=require('ueditor');
       var path = require('path');
       var bodyParser = require('body-parser');
       app.use(bodyParser.urlencoded({
       extended: true
       }));
       
       app.use(bodyParser.json());
       // app.set('port',process.env.PORT||3000);
       app.get('/',function(req,res){
       res.type('text/plain');
       res.send('hello world!');
       })
       app.use(express.static('public'));
       app.use("/ueditor/ue", ueditor(path.join(__dirname, 'public'), function (req, res, next) {
       //客户端上传文件设置
       var imgDir = '/img/ueditor/'
        var ActionType = req.query.action;
       if (ActionType === 'uploadimage' || ActionType === 'uploadfile' || ActionType === 'uploadvideo') {
           var file_url = imgDir;//默认图片上传地址
           /*其他上传格式的地址*/
           if (ActionType === 'uploadfile') {
               file_url = '/file/ueditor/'; //附件
           }
           if (ActionType === 'uploadvideo') {
               file_url = '/video/ueditor/'; //视频
           }
           res.ue_up(file_url); //你只要输入要保存的地址 。保存操作交给ueditor来做
           res.setHeader('Content-Type', 'text/html');
       }
       //  客户端发起图片列表请求
       else if (req.query.action === 'listimage') {
           var dir_url = imgDir;
           res.ue_list(dir_url); // 客户端会列出 dir_url 目录下的所有图片
       }
       // 客户端发起其它请求
       else {
           // console.log('config.json')
           res.setHeader('Content-Type', 'application/json');
           res.redirect('/nodejs/config.json');
       }
       }));
       //定制404页面
       app.use(function(req,res){
       res.type('text/plain');
       res.status(404);
       res.send('404---not found');
       });
       //定制505页面
       app.use(function(err,req,res,next){
       console.error(err.stack);
       res.type('text/plain');
       res.status(500);
       res.send('500 - Server Error');
       })
       
       app.listen(8888,function(){
       console.log("Express started on http://localhost:8888"+
           ";press Ctrl-c to terminate")
       })
   5.现在server运行的话监听的是8888端口，客户端运行的是8080端口，所以还需要进一步修改

   6.在config中的index.js中的proxyTable:{}中增加：

               '/ueditor': {
                   target: 'http://localhost:8888/',
                   changeOrigin: true,
                   pathRewrite: {
                       '^/ueditor': '/ueditor'
                   }
               },
               '/nodejs': {
                   target: 'http://localhost:8888/',
                   changeOrigin: true,
                   pathRewrite: {
                       '^/nodejs': '/nodejs'
                   }
               },
               '/img': {
                   target: 'http://localhost:8888/',
                   changeOrigin: true,
                   pathRewrite: {
                       '^/img': '/img'
                   }
               }
   7.至此可以正常运行，先跑server node index.js,再跑客户端npm run dev



Node.js + Vue.js 搭建 UEditor
A Vue.js project

Node.js UEditor 支援圖片上傳功能

Build Setup (專案建置步驟)
Bash
# 安裝相依套件
npm install

# 執行 server 伺服器，部署在 8888 連接埠上
cd server
node index.js

# 執行用戶端（Client），部署在 8080 連接埠上，支援熱載入（Hot Reload）
npm run dev

# 專案打包與線上環境編譯
npm run build
安裝與設定 Server 端
初始化專案：

Bash
npm init
安裝 Express 與 UEditor 套件：

Bash
npm install --save express ueditor body-parser
建立靜態資源資料夾：
建立一個名為 public 的資料夾，並將 ueditor 套件中 example/public 裡面的內容複製到剛建立的 public 資料夾底下。

建立 index.js 檔案，程式碼如下：

JavaScript
var express = require('express');
var app = express();
var ueditor = require('ueditor');
var path = require('path');
var bodyParser = require('body-parser');

app.use(bodyParser.urlencoded({
    extended: true
}));
app.use(bodyParser.json());

app.get('/', function(req, res){
    res.type('text/plain');
    res.send('hello world!');
});

app.use(express.static('public'));

app.use("/ueditor/ue", ueditor(path.join(__dirname, 'public'), function (req, res, next) {
    // 用戶端上傳檔案設定
    var imgDir = '/img/ueditor/';
    var ActionType = req.query.action;
    
    if (ActionType === 'uploadimage' || ActionType === 'uploadfile' || ActionType === 'uploadvideo') {
        var file_url = imgDir; // 預設圖片上傳路徑
        
        /* 其他上傳格式的路徑設定 */
        if (ActionType === 'uploadfile') {
            file_url = '/file/ueditor/'; // 附件
        }
        if (ActionType === 'uploadvideo') {
            file_url = '/video/ueditor/'; // 影片
        }
        res.ue_up(file_url); // 只需要輸入儲存路徑，儲存操作會自動交給 ueditor 處理
        res.setHeader('Content-Type', 'text/html');
    }
    // 用戶端發起圖片列表請求
    else if (req.query.action === 'listimage') {
        var dir_url = imgDir;
        res.ue_list(dir_url); // 用戶端會列出 dir_url 目錄下的所有圖片
    }
    // 用戶端發起其他請求
    else {
        res.setHeader('Content-Type', 'application/json');
        res.redirect('/nodejs/config.json');
    }
}));

// 定制 404 頁面
app.use(function(req, res){
    res.type('text/plain');
    res.status(404);
    res.send('404 --- Not Found');
});

// 定制 500 頁面
app.use(function(err, req, res, next){
    console.error(err.stack);
    res.type('text/plain');
    res.status(500);
    res.send('500 - Server Error');
});

app.listen(8888, function(){
    console.log("Express started on http://localhost:8888" + "; press Ctrl-c to terminate");
});
跨網域與連接埠處理：
目前 Server 運作並監聽的是 8888 連接埠，而用戶端（Vue）運作的是 8080 連接埠，因此需要設定代理（Proxy）來解決跨網域問題。

修改 Vue 專案設定：
開啟專案中 config/index.js 檔案，在 proxyTable: {} 中新增以下代理設定：

JavaScript
proxyTable: {
    '/ueditor': {
        target: 'http://localhost:8888/',
        changeOrigin: true,
        pathRewrite: {
            '^/ueditor': '/ueditor'
        }
    },
    '/nodejs': {
        target: 'http://localhost:8888/',
        changeOrigin: true,
        pathRewrite: {
            '^/nodejs': '/nodejs'
        }
    },
    '/img': {
        target: 'http://localhost:8888/',
        changeOrigin: true,
        pathRewrite: {
            '^/img': '/img'
        }
    }
}
執行專案：
至此已可正常運作。請先啟動後端 Server：node index.js，接著再啟動前端用戶端：npm run dev。

   ​

   ​






