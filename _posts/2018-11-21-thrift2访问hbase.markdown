---
layout:     post
title:      thrift2访问hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhenyangzhijia/article/details/46334707				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="使用thrift2访问hbase">使用thrift2访问hbase</h1>



<h2 id="需求与环境">需求与环境</h2>

<p>项目中需要使用c++访问hbase批量导入数据，所以这两天配置了一个hadoop集群，安装了hadoop，zookeeper，hbase。 <br>
使用三台虚拟机，一个namenode节点和2个datanode节点。</p>



<h2 id="hbase-thrift2简介">hbase thrift2简介</h2>

<p>hbase使用java语言编写，原生的hbase客户端是不支持C++的，所以选择使用thrift完成项目的开发。 <br>
thrift是一个有facebook贡献的开源项目，用于多语言之间的通信，定义好IDL接口后，可实现二进制形式的高性能通信。 <br>
运行方式是client/server方式。 <br>
在hbase中，官方实现了两个版本的thrift，分别是thrift和thrift2。官方推荐thrift2，thrift以后就不支持了。（但是从hbase的git提交时间来看，thrift2也是好久没更新了，thrift2的cpp示例更是没有，只有thrift的）</p>

<p>hbase的thrift运行方式如下：</p>



<h3 id="server端">server端</h3>

<p>hbase本身已经集成了thrift，使用以下命令开启hbase thrift的服务器端。关闭是将start修改为stop即可。默认情况下监听9090端口。</p>

<pre><code>hbase-daemon.sh start thrift2
hbase-daemons.sh start thrift2 (集群版本)
</code></pre>



<h3 id="client端">client端</h3>

<p>首先需要安装apache thrift客户端，网上有大量的安装教程，这里不在复述，需要注意的是需要安装大量的依赖包，可以先用<code>./configure</code>发现需要安装的依赖包，然后再安装需要的依赖包，根据自己的系统选择需要的包，没有必要按照五花八门的教程安装好多已经安装的包。 <br>
安装过程一如既往</p>

<pre><code>./configure &amp; make &amp; make install
</code></pre>



<h2 id="hbase-thrift2使用">hbase thrift2使用</h2>

<p>由于官方没有给出thrift2示例，之前没有接触过thrift和IDL，所以刚开始有些吃力。所以上网找资料，感谢<strong>走在左边</strong>的<a href="http://haoningabc.iteye.com/blog/2032878" rel="nofollow">博客</a>，在这个博客的基础上，根据自己的hbase版本和安装路径，成功编译成功了简单示例。</p>



<h3 id="版本与文件">版本与文件</h3>

<p>我使用的hbase版本是1.0.1，thrift客户端版本是0.9.2。需要下载hbase带源码的包，示例程序都以及hbase.thrift都在这个包里。 <br>
示例代码如下，可惜的是没有cpp的thrift2版本的代码</p>

<pre><code>hbase-1.0.1.1/hbase-examples/src/main$ ll
total 44
drwxr-xr-x 11 zhijia zhijia 4096  5月 18 03:02 ./
drwxr-xr-x  4 zhijia zhijia 4096  5月 16 06:50 ../
drwxr-xr-x  2 zhijia zhijia 4096  5月 18 03:02 asciidoc/
drwxr-xr-x  4 zhijia zhijia 4096  6月  2 17:36 cpp/
drwxr-xr-x  3 zhijia zhijia 4096  5月 16 06:50 java/
drwxr-xr-x  3 zhijia zhijia 4096  6月  2 17:36 perl/
drwxr-xr-x  3 zhijia zhijia 4096  6月  2 17:36 php/
drwxr-xr-x  2 zhijia zhijia 4096  6月  2 17:36 protobuf/
drwxr-xr-x  4 zhijia zhijia 4096  5月 16 06:50 python/
drwxr-xr-x  3 zhijia zhijia 4096  6月  2 17:36 ruby/
drwxr-xr-x  3 zhijia zhijia 4096  5月 16 06:50 sh/
</code></pre>

<p>hbase thrift2的定义文件hbase.thrift存放路径如下</p>

<pre><code>hbase-1.0.1.1/hbase-thrift/src/main/resources/org/apache/hadoop/hbase/thrift2$ ll
total 32
drwxr-xr-x 4 zhijia zhijia  4096  6月  2 17:36 ./
drwxr-xr-x 4 zhijia zhijia  4096  5月 16 06:50 ../
drwxrwxr-x 2 zhijia zhijia  4096  6月  2 10:56 gen-cpp/
drwxrwxr-x 3 zhijia zhijia  4096  6月  1 15:03 gen-py/
-rw-r--r-- 1 zhijia zhijia 14140  5月 18 03:12 hbase.thrift
</code></pre>



<h3 id="启动thrift服务器">启动thrift服务器</h3>

<pre><code>zhijia@HadoopMaster:~$ hbase-daemons.sh start thrift2
HadoopSlaver1: starting thrift2, logging to /usr/isms/hadoop/hbase1.0.1/bin/../logs/hbase-zhijia-thrift2-HadoopSlaver1.out
HadoopSlaver2: starting thrift2, logging to /usr/isms/hadoop/hbase1.0.1/bin/../logs/hbase-zhijia-thrift2-HadoopSlaver2.out
</code></pre>



<h3 id="编写thrift客户端代码">编写thrift客户端代码</h3>



<h4 id="makefile">makefile</h4>

<pre><code>THRIFT_DIR = /usr/local/include/thrift
LIB_DIR = /usr/local/lib

GEN_SRC = ./gen-cpp/Hbase.cpp \
     ./gen-cpp/Hbase_types.cpp \
     ./gen-cpp/Hbase_constants.cpp

GEN_SRC = ./gen-cpp/hbase_types.cpp ./gen-cpp/hbase_constants.cpp ./gen-cpp/THBaseService.cpp
.PHONY: clean help

default: DemoClient Insert
</code></pre>

<p>HabaseClietn.cpp</p>

<pre><code>#include "THBaseService.h"
#include "config.h"
#include &lt;vector&gt;
#include &lt;transport/TSocket.h&gt;
#include &lt;transport/TBufferTransports.h&gt;
#include &lt;protocol/TBinaryProtocol.h&gt;

using namespace std;
using namespace apache::thrift;
using namespace apache::thrift::protocol;
using namespace apache::thrift::transport;
using namespace apache::hadoop::hbase::thrift2;

using boost::shared_ptr;

int main(int argc, char **argv) {
    boost::shared_ptr&lt;TSocket&gt; socket(new TSocket("172.16.85.161", 9090));
    boost::shared_ptr&lt;TTransport&gt; transport(new TBufferedTransport(socket));
    boost::shared_ptr&lt;TProtocol&gt; protocol(new TBinaryProtocol(transport));

    transport-&gt;open();
    printf("open\n");
    THBaseServiceClient client(protocol);
    TResult tresult;
    TGet get;
    const std::string table("example");
    const std::string thisrow="family1";
    get.__set_row(thisrow);
    client.get(tresult,table,get);
    vector&lt;TColumnValue&gt; list=tresult.columnValues;
    std::vector&lt;TColumnValue&gt;::const_iterator iter;
    for(iter=list.begin();iter!=list.end();iter++) {
     printf("list size: %d\n",list.size());
     printf("get : %s, %s,%s\n",(*iter).family.c_str(),(*iter).qualifier.c_str(),(*iter).value.c_str());//,(*iter).timestamp
}

    transport-&gt;close();
    printf("close\n");
    return 0;
}
</code></pre>



<h4 id="insertcpp">Insert.cpp</h4>

<pre><code>#include "THBaseService.h"
#include "config.h"
#include &lt;vector&gt;
#include &lt;transport/TSocket.h&gt;
#include &lt;transport/TBufferTransports.h&gt;
#include &lt;protocol/TBinaryProtocol.h&gt;

using namespace std;
using namespace apache::thrift;
using namespace apache::thrift::protocol;
using namespace apache::thrift::transport;
using namespace apache::hadoop::hbase::thrift2;

using boost::shared_ptr;

int main(int argc, char **argv) {
    boost::shared_ptr&lt;TSocket&gt; socket(new TSocket("172.16.85.161", 9090));
    boost::shared_ptr&lt;TTransport&gt; transport(new TBufferedTransport(socket));
    boost::shared_ptr&lt;TProtocol&gt; protocol(new TBinaryProtocol(transport));

    transport-&gt;open();
    TPut put;
    THBaseServiceClient client(protocol);
    const std::string table="example";
    const std::string thisrow="first";
    put.__set_row(thisrow);
    TColumnValue columnValue;
    const std::string thisfamily="family1";
    columnValue.__set_family(thisfamily);
    const std::string thisqualifier="fromcpp";
    columnValue.__set_qualifier(thisqualifier);
    const std::string thisvalue="vppisok";
    columnValue.__set_value(thisvalue);
    vector&lt;TColumnValue&gt; columnValues;
    columnValues.push_back(columnValue);
    put.__set_columnValues(columnValues);
    client.put(table,put);
    transport-&gt;close();
    printf("close\n");
    return 0;
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>