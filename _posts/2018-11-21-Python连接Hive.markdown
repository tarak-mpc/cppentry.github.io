---
layout:     post
title:      Python连接Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xyfengbo/article/details/51683676				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-size:14px;">1.在使用Python连接hive之前，需要将hive安装包下的lib/py中的文件拷贝到python的sys.path中的<span style="font-size:14px;">site-packages下，否则引入对应的包会报错，这个是使用hive提供的Python接口来调用hive客户端。</span></span></div>
<div><br></div>
<div><span style="font-size:14px;"><br></span></div>
<div><span style="font-size:14px;">2 启动hive 的thrift</span></div>
<div><br></div>
<div><span style="font-size:14px;">确保以下服务开启：</span></div>
<div><span style="font-size:14px;">hive --service hiveserver</span></div>
<div><span style="font-size:14px;">默认端口是10000</span></div>
<div><br></div>
<div style="font-family:'Courier New';"><span style="font-size:15px;"><span style="color:#000080;"><strong>from </strong></span>hive_service <span style="color:#000080;"><strong>import </strong></span>ThriftHive<br><span style="color:#000080;"><strong>from </strong></span>thrift <span style="color:#000080;"><strong>import </strong></span>Thrift<br><span style="color:#000080;"><strong>from </strong></span>thrift.transport <span style="color:#000080;"><strong>import </strong></span>TSocket<br><span style="color:#000080;"><strong>from </strong></span>thrift.transport <span style="color:#000080;"><strong>import </strong></span>TTransport<br><span style="color:#000080;"><strong>from </strong></span>thrift.protocol <span style="color:#000080;"><strong>import </strong></span>TBinaryProtocol<br><br><span style="color:#000080;"><strong>def </strong></span>ReadHiveTest(sql):<br>
    <span style="color:#000080;"><strong>try</strong></span>:<br>
        tSocket = TSocket.TSocket(<span style="color:#008000;"><strong>'172.18.1.88'</strong></span>,<span style="color:#0000ff;">10000</span>)<br>
        tTransport = TTransport.TBufferedTransport(tSocket)<br>
        protocol = TBinaryProtocol.TBinaryProtocol(tTransport)<br>
        client = ThriftHive.Client(protocol)<br>
        tTransport.open()<br>
        client.execute(sql)<br>
        <span style="color:#000080;"><strong>return </strong></span>client.fetchAll()<br>
    <span style="color:#000080;"><strong>except </strong></span>Thrift.TException, tx:<br>
        <span style="color:#000080;"><strong>print </strong></span><span style="color:#008000;"><strong>'%s' </strong></span>% (tx.message)<br>
    <span style="color:#000080;"><strong>finally</strong></span>:</span></div>
<div><span style="font-family:'Courier New';"><span style="font-size:15px;">        tTransport.close()</span></span></div>
<div style="font-family:'Courier New';"><span style="font-size:15px;"><br><span style="color:#000080;"><strong>if </strong></span>__name__ == <span style="color:#008000;"><strong>'__main__'</strong></span>:<br>
    showDatabasesSql = <span style="color:#008000;"><strong>'show databases'<br></strong></span><span style="color:#008000;"><strong>   </strong></span> showTablesSql = <span style="color:#008000;"><strong>'show tables'<br></strong></span><span style="color:#008000;"><strong>   </strong></span> selectSql = <span style="color:#008000;"><strong>'SELECT * FROM 07_jn_mysql_2'<br></strong></span><span style="color:#008000;"><strong>   </strong></span> result = ReadHiveTest(selectSql)<br>
    <span style="color:#000080;"><strong>print</strong></span>(result[<span style="color:#0000ff;">1</span>])</span></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div> </div>
            </div>
                </div>