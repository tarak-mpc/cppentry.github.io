---
layout:     post
title:      Hbase的REST访问
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuzhoulong/article/details/52056497				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-family:'Microsoft YaHei';">使用hbase REST服务器需要先去服务器上启动服务</span></p><span style="font-family:'Microsoft YaHei';">前台启动hbase rest服务<br>bin/hbase rest start -p &lt;port&gt;<br>后台启动hbase服务<br>bin/hbase-daemon.sh start rest -p &lt;port&gt;<br>停止服务<br>bin/hbase-daemon.sh stop rest<br></span><p><span style="font-family:'Microsoft YaHei';">不加端口的情况下，端口默认为8080</span></p><p><span style="font-family:'Microsoft YaHei';"><br></span></p><span style="font-family:'Microsoft YaHei';">利用requests模块访问rest接口的python代码如下，其中注释是利用curl命令的访问方式：</span><p><span style="font-family:'Microsoft YaHei';color:#333333;"><span style="font-size:14px;line-height:26px;"></span></span></p><p></p><pre><code class="language-python">    baseurl = "http://192.168.119.128:8080";

    #获取表table2中rowkey为liu的行
    response = requests.get(baseurl+'/table2/liu', headers={"Accept" : "application/json"})
    '''
              相当于 curl  -H "Accept:application/json"  http://192.168.119.128:8080/table2/liu
    '''
    print response.json()
    #返回的字段名称和值为base64编码的，需要解密查看
    print base64.b64decode(u'bW9ibGllOg==')

    #查看集群状态
    response = requests.get(baseurl+'/status/cluster', headers={"Accept" : "application/json"})
    '''
         相当于 curl  -H "Accept:application/json"  http://192.168.119.128:8080/status/cluster
    '''
    
    print response.json()

    #查看集群版本
    response = requests.get(baseurl+'/version/cluster', headers={"Accept" : "application/json"})
    '''
             相当于  curl  -H "Accept:application/json"  http://192.168.119.128:8080/status/cluster
    '''
    print response.json()

    #获得表list
    response = requests.get(baseurl+'/', headers={"Accept" : "application/json"})
    '''
             相当于  curl  -H "Accept:application/json"  http://192.168.119.128:8080/
    '''
    print response.json()
    
    #table2中添加一行数据rowkey为moblie，xml字段名称和数据base64编码
    rdata='&lt;?xml version="1.0" encoding="UTF-8" standalone="yes"?&gt;&lt;CellSet&gt;&lt;Row key="bW9ibGllOg=="&gt;&lt;Cell column="bW9ibGllOg=="&gt;bGl1emhvdWxvbmcy&lt;/Cell&gt;&lt;/Ro    w&gt;&lt;/CellSet&gt;'
    response = requests.put(baseurl+'/table2/moblie', data=rdata,headers = {'content-type': 'text/xml'})
    print response
    
    #curl -vi -X PUT  -H "Accept: text/xml"  -H "Content-Type: text/xml" -d '&lt;?xml version="1.0" encoding="UTF-8" standalone="yes"?&gt;&lt;CellSet&gt;&lt;Row key="bW9    ibGllOg=="&gt;&lt;Cell column="bW9ibGllOg=="&gt;bGl1emhvdWxvbmcy&lt;/Cell&gt;&lt;/Row&gt;&lt;/CellSet&gt;' "http://192.168.119.128:8080/table2/moblie"
    #添加表users
    rdata='&lt;?xml version="1.0" encoding="UTF-8"?&gt;&lt;TableSchema name="users"&gt;&lt;ColumnSchema name="cf" /&gt;&lt;/TableSchema&gt;'
    response = requests.post(baseurl+'/users/schema', data=rdata,headers = {'content-type': 'text/xml'})
    print response
    #curl -vi -X POST -H "Accept: text/xml"  -H "Content-Type: text/xml" -d '&lt;?xml version="1.0" encoding="UTF-8"?&gt;&lt;TableSchema name="users"&gt;&lt;ColumnSchema    name="cf" /&gt;&lt;/TableSchema&gt;' "http://192.168.119.128:8080/users/schema"
    
    #删除表users
    response = requests.delete(baseurl+'/users/schema')
    print response
    #curl -vi -X DELETE  -H "Accept: text/xml"  "http://192.168.119.128:8080/users/schema"</code></pre><br><span style="color:rgb(51,51,51);font-family:'Microsoft YaHei';font-size:14px;line-height:26px;"></span><p></p><p></p><span style="font-family:'Microsoft YaHei';">更多信息请参考<br>http://hbase.apache.org/book.html#_rest<br><br><br>扩展curl 参数含义<br>-X/--request [GET|POST|PUT|DELETE|…]  使用指定的http method發出 http request<br>-H/--header                           設定request裡的header<br>-i/--include                          顯示response的header<br>-d/--data                             設定 http parameters <br>-v/--verbose                          輸出比較多的訊息<br>-u/--user                             使用者帳號、密碼<br>-b/--cookie                           cookie  <br><br><br>Hbase的访问方式包括：<br>1、Native Java API：最常规和高效的访问方式；<br>2、HBase Shell：HBase的命令行工具，最简单的接口，适合HBase管理使用；<br>3、Thrift Gateway：利用Thrift序列化技术，支持C++，PHP，Python等多种语言，适合其他异构系统在线访问HBase表数据；<br>4、REST Gateway：支持REST 风格的Http API访问HBase, 解除了语言限制；<br>5、MapReduce：直接使用MapReduce作业处理Hbase数据；<br>6、使用Pig/hive处理Hbase数据。</span><p><span style="color:rgb(51,51,51);font-family:'Microsoft YaHei';font-size:14px;line-height:26px;"><br></span></p><p><span style="color:rgb(51,51,51);font-family:'Microsoft YaHei';font-size:14px;line-height:26px;"><br></span></p>            </div>
                </div>