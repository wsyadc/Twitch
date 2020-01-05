//这是简单的新闻网站的最终版
var http = require('http');
var fs = require('fs');
var path = require('path');
var mime = require('mime');
var url = require('url');
var querystring = require('querystring');
var _ = require('underscore');
http.createServer(function (req,res) {
    var urlobj = url.parse(req.url);
    req.url=req.url.toLowerCase();
    req.method=req.method.toLowerCase();
    if(req.url==='/'||req.url==='/index'&&req.method==='get')//新闻主页 该板块实现显示所有新闻标题
    {
        readdata(function (list) {
            render(path.join(__dirname, 'htmls', 'index.html'),res,{list:list});

        })


    } //获取静态资源模块
    else if(req.url.indexOf('/static-resources')===0 && req.method==='get')
    {
        fs.readFile(path.join(__dirname,urlobj.pathname),function (err,data) {
            if(err)
            {
                throw err;
            }
            res.setHeader('Content-Type',mime.getType(req.url));
            res.end(data);

        });


    }
    else if(req.url==='/submit'&&req.method==='get')//提交页面
    {render(path.join(__dirname,'htmls','add.html'),res);


    }
    else if(req.url==='/add'&&req.method==='post')//post提交数据
    {
        readdata(function (list) {
            postdata(req,function (postbody) {
                postbody.id = list.length;
                list.push(postbody);
                //写入文件
                writedata(JSON.stringify(list),function () {
                    res.statusCode= 302;
                    res.statusMessage= 'found';
                    res.setHeader('Location','/');
                    res.end('over!' );
                });

            });
        });
    }
    //显示新闻详情页
    else if(urlobj.pathname==='/item' && req.method==='get')
    {
        readdata(function (list_news) {
            var model =null;


            for(var i=0;i<list_news.length;i++)
            {
                if('id='+list_news[i].id.toString() === urlobj.query)//无法通过urlobj.id获取id的值，那么就通过改变另一方来适应它
                {
                    model = list_news[i];
                    break;
                }
            }
            if(model)
            {
                render(path.join(__dirname, 'htmls', 'item.html'),res,{item:model});
            }
            else{
                res.end('NO SUCH ITEM');
            }
        });

    }
    //文件不存在
    else
    {
        fs.readFile(path.join(__dirname,'htmls','404.html'),function (err,data) {
            if(err)
            {
                throw err;
            }
            res.end(data);


        });

    }


}).listen(8080,function () {
    console.log('服务已启动，请访问http://localhost:8080');

});
function render(filename,res,tqldata){
    fs.readFile(filename,function (err,data) {
        if (err) {
            res.writeHead(404, 'not found', {'Content-Type': 'text/html;charset=utf-8'});
            res.write('Not Found');
            return;

        }
        if(tqldata)
        {
            var fn = _.template(data.toString('utf-8'));
            data =fn(tqldata);
            //console.log('我们实际的data'+data);
        }

        res.setHeader('Content-Type', mime.getType(filename));
        res.end(data);
    });



}
//读取文件子函数
function readdata(callback) {
    fs.readFile(path.join(__dirname,'data','data.json'),'utf-8',function (err,data) {
        if (err && err.code !== 'ENOENT') {
            throw err;
        }
        var list_news = JSON.parse(data ||'[]');
        callback(list_news);
    });

}
//写入文件子函数
function writedata(data,callback) {
    fs.writeFile(path.join(__dirname,'data','data.json'),data,function (err) {  //写入数据JOSN.stringify为转化为字符串数据并写入。
        if(err)
        {
            throw err;
        }
        //写入文件执行完毕后，执行callback函数
        callback();
    });
}
//post获取数据子函数
function postdata(req,callback) {
    var array =[];//储存数据的数组，里面数据为Buffer类型
    req.on('data',function (chunk) {//chunk 为用户提交的数据的一部分，类型为Buffer
        array.push(chunk);//把数据加到array中
    });
    req.on('end',function () {
        //在这个事件中将数据进行汇总为一个大的Buffer并将其转换类型及进行·。。。。。
        //用到buffer方法
        var postbody = Buffer.concat(array);//整合数据转化为大的一个数据流buffer
        postbody = postbody.toString('utf8');//转化为字符串
        postbody = querystring.parse(postbody);//转化为json对象
        callback(postbody);
    });
}
