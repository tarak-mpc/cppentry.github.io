---
layout:     post
title:      python使用hdfs3模块对hdfs进行操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>之前一直使用hdfs的命令进行hdfs操作，比如：</p>
<pre><code class="prism language-xml">hdfs dfs -ls /user/spark/
hdfs dfs -get /user/spark/a.txt /home/spark/a.txt #从HDFS获取数据到本地
hdfs dfs -put -f /home/spark/a.txt /user/spark/a.txt #从本地覆盖式上传
hdfs dfs -mkdir -p /user/spark/home/datetime=20180817/
....
</code></pre>
<p>身为一个python程序员，每天操作hdfs都是在程序中写各种cmd调用的命令，一方面不好看，另一方面身为一个Pythoner这是一个耻辱，于是乎就挑了一个hdfs3的模块进行hdfs的操作，瞬间就感觉优雅多了：<br>
hdfs 官方API：<a href="https://hdfs3.readthedocs.io/en/latest/api.html" rel="nofollow">https://hdfs3.readthedocs.io/en/latest/api.html</a></p>
<pre><code class="prism language-xml">&gt;&gt;&gt; from hdfs3 import HDFileSystem
#链接HDFS
&gt;&gt;&gt; hdfs = HDFileSystem(host='localhost', port=8020)
&gt;&gt;&gt; hdfs.ls('/user/data')
&gt;&gt;&gt; hdfs.put('local-file.txt', '/user/data/remote-file.txt')
&gt;&gt;&gt; hdfs.cp('/user/data/file.txt', '/user2/data')
</code></pre>
<p>#文件读取</p>
<pre><code class="prism language-xml">#txt文件全部读取
&gt;&gt;&gt; with hdfs.open('/user/data/file.txt') as f:
...     data = f.read(1000000)
#使用pandas读取1000行数据
&gt;&gt;&gt; with hdfs.open('/user/data/file.csv.gz') as f:
...     df = pandas.read_csv(f, compression='gzip', nrows=1000)
</code></pre>
<p>#写入文件</p>
<pre><code class="prism language-xml">&gt;&gt;&gt; with hdfs.open('/tmp/myfile.txt', 'wb') as f:
...     f.write(b'Hello, world!')
</code></pre>
<p>#多节点连接设置</p>
<pre><code class="prism language-xml">host = "nameservice1"
conf = {"dfs.nameservices": "nameservice1",
        "dfs.ha.namenodes.nameservice1": "namenode113,namenode188",
        "dfs.namenode.rpc-address.nameservice1.namenode113": "hostname_of_server1:8020",
        "dfs.namenode.rpc-address.nameservice1.namenode188": "hostname_of_server2:8020",
        "dfs.namenode.http-address.nameservice1.namenode188": "hostname_of_server1:50070",
        "dfs.namenode.http-address.nameservice1.namenode188": "hostname_of_server2:50070",
        "hadoop.security.authentication": "kerberos"
}
fs = HDFileSystem(host=host, pars=conf)
</code></pre>
<p>#API</p>
<pre><code class="prism language-xml">hdfs = HDFileSystem(host='127.0.0.1', port=8020)
hdfs.cancel_token(token=None) #未知，求大佬指点
hdfs.cat(path) #获取指定目录或文件的内容
hdfs.chmod(path, mode) #修改制定目录的操作权限
hdfs.chown(path, owner, group) #修改目录所有者，以及用户组
hdfs.concat(destination, paths) #将指定多个路径paths的文件，合并成一个文件写入到destination的路径，并删除源文件（The source files are deleted on successful completion.成功完成后将删除源文件。）
hdfs.connect() #连接到名称节点 这在启动时自动发生。   LZ:未知作用，按字面意思，应该是第一步HDFileSystem(host='127.0.0.1', port=8020)发生的
hdfs.delegate_token(user=None)
hdfs.df() #HDFS系统上使用/空闲的磁盘空间
hdfs.disconnect() #跟connect()相反，断开连接
hdfs.du(path, total=False, deep=False) #查看指定目录的文件大小，total是否把大小加起来一个总数，deep是否递归到子目录
hdfs.exists(path) #路径是否存在
hdfs.get(hdfs_path, local_path, blocksize=65536)  #将HDFS文件复制到本地,blocksize设置一次读取的大小
hdfs.get_block_locations(path, start=0, length=0) #获取块的物理位置
hdfs.getmerge(path, filename, blocksize=65536) #获取制定目录下的所有文件，复制合并到本地文件
hdfs.glob(path) #/user/spark/abc-*.txt 获取与这个路径相匹配的路径列表
hdfs.head(path, size=1024) #获取指定路径下的文件头部分的数据
hdfs.info(path) #获取指定路径文件的信息
hdfs.isdir(path) #判断指定路径是否是一个文件夹
hdfs.isfile(path) #判断指定路径是否是一个文件
hdfs.list_encryption_zones() #获取所有加密区域的列表
hdfs.ls(path, detail=False) #返回指定路径下的文件路径，detail文件详细信息
hdfs.makedirs(path, mode=457) #创建文件目录类似 mkdir -p
hdfs.mkdir(path) #创建文件目录
hdfs.mv(path1, path2) #将path1移动到path2
open(path, mode='rb', replication=0, buff=0, block_size=0) #读取文件，类似于python的文件读取
hdfs.put(filename, path, chunk=65536, replication=0, block_size=0) #将本地的文件上传到，HDFS指定目录
hdfs.read_block(fn, offset, length, delimiter=None) #指定路径文件的offset指定读取字节的起始点，length读取长度，delimiter确保读取在分隔符bytestring上开始和停止
&gt;&gt;&gt; hdfs.read_block('/data/file.csv', 0, 13)  
b'Alice, 100\nBo'
&gt;&gt;&gt; hdfs.read_block('/data/file.csv', 0, 13, delimiter=b'\n')  
b'Alice, 100\nBob, 200'
hdfs.rm(path, recursive=True) #删除指定路径recursive是否递归删除
hdfs.tail(path, size=1024) #获取 文件最后一部分的数据
hdfs.touch(path) #创建一个空文件
hdfs.walk(path) #遍历文件树
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>