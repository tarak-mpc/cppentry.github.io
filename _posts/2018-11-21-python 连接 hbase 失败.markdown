---
layout:     post
title:      python 连接 hbase 失败
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>当我使用 python 来操作 hbase时，代码如下：</p>

<pre class="has">
<code class="language-python">from thrift import Thrift
from thrift.transport import TSocket, TTransport
from thrift.protocol import TBinaryProtocol
from hbase import Hbase

from hbase import ttypes

transport = TSocket.TSocket('127.0.0.1', 9090)

#设置传输方式
transport = TTransport.TBufferedTransport(transport)
#设置传输协议
protocol = TBinaryProtocol.TBinaryProtocol(transport);

#确定客户端
client = Hbase.Client(protocol)
# 打开连接
transport.open()
#
# column = ttypes.ColumnDescriptor('name')
# client.createTable(tableName="test", columnFamilies=column)
# columns = ['name','coruse']

desc = ttypes.ColumnDescriptor(name="colNameTest1")
#创建（table, [列族们]）
client.createTable('our_table1',[desc])
# client.createTable("student", map(lambda column: ttypes.ColumnDescriptor(column), columns))

print(client.getTableNames())

transport.close()
</code></pre>

<p>运行时出现 </p>

<p><span style="color:#f33b45;">typeError: a bytes-like object is required, not 'str'</span></p>

<p><span><span>刚开始我以为是要求输入一个对象，最后百度后才发现原来 是 方法要求的是 bytes 型的参数，这时只要在 str 参数前加 b"str", 就可以了</span></span></p>            </div>
                </div>