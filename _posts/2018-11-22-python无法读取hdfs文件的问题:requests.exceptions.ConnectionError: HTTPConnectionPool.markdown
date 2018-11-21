---
layout:     post
title:      python无法读取hdfs文件的问题:requests.exceptions.ConnectionError: HTTPConnectionPool
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.问题一描述：在用python的hdfs库操作HDFS时，可以正常的获取到hdfs的文件目录</p>

<pre class="has">
<code class="language-python">from hdfs import *
client = Client("http://10.0.30.9:50070")
print(client.list('/'))</code></pre>

<pre class="has">
<code>['test.txt']</code></pre>

<p>但是在读取文件时，出现了hdfs.util.HdfsError: File /user/dr.who/test.txt not found.的错误，尝试使用pyhdfs也是同样的问题，包括下面说的第二个问题</p>

<pre class="has">
<code class="language-python">from hdfs import *
client = Client("http://10.0.30.9:50070")
print(client.list('/'))
with client.read('test.txt') as reader:
    content = reader.read()
    print(content)</code></pre>

<pre class="has">
<code class="language-python">Traceback (most recent call last):
  File "E:/pycharm/workspace/hadoopforwin/myhdfs.py", line 5, in &lt;module&gt;
    with client.read('test.txt') as reader:
  File "D:\python3.6\lib\contextlib.py", line 81, in __enter__
    return next(self.gen)
  File "D:\python3.6\lib\site-packages\hdfs\client.py", line 678, in read
    buffersize=buffer_size,
  File "D:\python3.6\lib\site-packages\hdfs\client.py", line 112, in api_handler
    raise err
  File "D:\python3.6\lib\site-packages\hdfs\client.py", line 107, in api_handler
    **self.kwargs
  File "D:\python3.6\lib\site-packages\hdfs\client.py", line 210, in _request
    _on_error(response)
  File "D:\python3.6\lib\site-packages\hdfs\client.py", line 50, in _on_error
    raise HdfsError(message, exception=exception)
hdfs.util.HdfsError: File /user/dr.who/test.txt not found.</code></pre>

<p>2.问题一解决方法：出现这个问题是因为没有指定根路径（root path）,需要在调用Client方法连接hdfs时指定root path</p>

<pre class="has">
<code class="language-python">from hdfs import *
client = Client("http://10.0.30.9:50070", root='/')
print(client.list('/'))
with client.read('test.txt') as reader:
    content = reader.read()
    print(content)</code></pre>

<p>执行代码，又出现了新的问题。。。。。</p>

<p>3.问题二描述：报错内容的最后一行如下，这里的hmaster是hadoop主机的主机名，说明程序没有将主机名映射到正确的ip</p>

<pre class="has">
<code class="language-python">requests.exceptions.ConnectionError: HTTPConnectionPool(host='hmaster', port=50075): Max retries exceeded with url: /webhdfs/v1/test.txt?op=OPEN&amp;namenoderpcaddress=hMaster:9000&amp;offset=0 (Caused by NewConnectionError('&lt;urllib3.connection.HTTPConnection object at 0x00000000035BAB38&gt;: Failed to establish a new connection: [Errno 11004] getaddrinfo failed',))</code></pre>

<p>4.问题二解决方法：在运行python程序的主机的hosts文件中加上主机名和ip的映射，对于我所使用的windows系统，hosts文件的路径是<a>C://Windows/System32/drivers/etc/hosts</a>，在文件末尾加上</p>

<pre class="has">
<code>ip 主机名</code></pre>

<p>以本文的情况为例，则是</p>

<pre class="has">
<code>10.0.30.9 hmaster</code></pre>

<p>修改完记得保存，运行程序成功读取文件。</p>

<p>5.在使用hdfs和pyhdfs库时，除了读取文件，还有一些方法也会出现这种情况，解决方法相同</p>            </div>
                </div>