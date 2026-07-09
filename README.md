# Node.js + Vue.js 搭建 UEditor 專案

> A Vue.js project 
>
> nodejs  ueditor  can upload images 

## Build Setup

``` bash
# install dependencies
npm install

# 後端服務將部署在 8888 埠號 (Port) 上：
cd server
node index.js


# 前端服務將部署在 8080 埠號上，並支援熱載入 (Hot Reload)：
npm run dev

# build for production with minification
npm run build

```

安装server:

1. npm init

2. 下載並安裝 Express 與 UEditor

3. 建立靜態資源資料夾

4.建立主程式檔案:

      var express = require('express');
var app = express();
var ueditor = require('ueditor');
var path = require('path');
var bodyParser = require('body-parser');

app.use(bodyParser.urlencoded({
    extended: true
}));
app.use(bodyParser.json());

// 測試根路由
app.get('/', function(req, res){
    res.type('text/plain');
    res.send('hello world!');
});

// 設定靜態檔案目錄
app.use(express.static('public'));

// UEditor 核心處理路由
app.use("/ueditor/ue", ueditor(path.join(__dirname, 'public'), function (req, res, next) {
    // 用戶端上傳檔案設定
    var imgDir = '/img/ueditor/'; // 預設圖片上傳路徑
    var ActionType = req.query.action;
    
    if (ActionType === 'uploadimage' || ActionType === 'uploadfile' || ActionType === 'uploadvideo') {
        var file_url = imgDir;
        
        /* 其他上傳格式的路徑調整 */
        if (ActionType === 'uploadfile') {
            file_url = '/file/ueditor/'; // 附件
        }
        if (ActionType === 'uploadvideo') {
            file_url = '/video/ueditor/'; // 影片
        }
        res.ue_up(file_url); // 您只需要輸入要儲存的路徑，儲存操作交給 ueditor 處理
        res.setHeader('Content-Type', 'text/html');
    }
    // 用戶端發起圖片列表請求（線上管理）
    else if (req.query.action === 'listimage') {
        var dir_url = imgDir;
        res.ue_list(dir_url); // 用戶端會列出 dir_url 目錄下的所有圖片
    }
    // 用戶端發起其它請求（如獲取前端配置 config.json）
    else {
        res.setHeader('Content-Type', 'application/json');
        res.redirect('/nodejs/config.json');
    }
}));

// 定製 404 頁面
app.use(function(req, res){
    res.type('text/plain');
    res.status(404);
    res.send('404 --- Not Found');
});

// 定製 500 頁面
app.use(function(err, req, res, next){
    console.error(err.stack);
    res.type('text/plain');
    res.status(500);
    res.send('500 - Server Error');
});

// 監聽 8888 埠號
app.listen(8888, function(){
    console.log("Express started on http://localhost:8888 ; press Ctrl-c to terminate");
});
   5.修改現在伺服器運行監聽的話是8888端口，客戶端運行的是8080端口，所以還需要進一步

   6. 設定開發環境代理（Proxy）
   請在專案的 config/index.js 檔案中，找到 proxyTable: {} 欄位，並將其修改、增加為以下內容：

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

   ​

   ​






