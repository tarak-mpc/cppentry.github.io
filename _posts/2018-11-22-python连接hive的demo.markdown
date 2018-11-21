---
layout:     post
title:      python连接hive的demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span>      在部署了两个hive后，分别在namenode和一个datanode上，用nohup hive --service hiveserver &amp;将hive server启动。</span>
<div>     <span> </span><span style="color:#4F009A;">Hive server 让Hive提供Thrift服务的服务器形式运行，允许不同的语言编写客户端进行访问</span>。使用Thrift、JDBC、ODBC连接器的客户需要运行hive服务器来和Hive进行通信。通过设置HIVE_PORT环境变量来指明服务器监听的端口（默认的是10000）<br><br><pre><code class="language-plain">alias python='python2.6'
PYTHONPATH=$PYTHONPATH:~/pyhive
export PATH HADOOP_HOME HIVE_HOME PYTHONPATH</code></pre><br>
pyhive为python的thrift文件目录，需要加载到pythonpath中<br><br><pre><code class="language-python">import sys
from hive_service import ThriftHive
from hive_service.ttypes import HiveServerException
from thrift import Thrift
from thrift.transport import TSocket
from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol

try:
    transport = TSocket.TSocket('192.168.30.201', 10000)
    transport = TTransport.TBufferedTransport(transport)
    protocol = TBinaryProtocol.TBinaryProtocol(transport)

    client = ThriftHive.Client(protocol)
    transport.open()
    hql = '''CREATE TABLE people(a STRING, b INT, c DOUBLE) row format delimited fields terminated by ',' '''
    print hql

    client.execute(hql)
    client.execute("LOAD DATA LOCAL INPATH '/home/diver/data.txt' INTO TABLE people")
    #client.execute("SELECT * FROM people")
    #while (1):
    #  row = client.fetchOne()
    #  if (row == None):
    #    break
    #  print row
    client.execute("SELECT count(*) FROM people")
    print client.fetchAll()

    transport.close()

except Thrift.TException, tx:
    print '%s' % (tx.message)</code></pre><br>
这样有了数据仓库基本执行脚本的模板了，对于执行的环境变量可以再做封装部分，脚本只突出逻辑处理。<br><br></div>
</div>
            </div>
                </div>