---
layout:     post
title:      Hadoop Node II
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wqhaber/article/details/77618309				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hadoop完全分布式">hadoop完全分布式</h2>

<pre><code>hdfs命令:
//. == /user/centos
hdfs dfs -put xxx.tar .
</code></pre>



<h2 id="找出所有hadoop配置信息">找出所有hadoop配置信息</h2>

<pre><code>hadoop-common-2.7.3.jar/core-default.xml            //core-site.xml
hadoop-hdfs-2.7.3.jar/hdfs-default.xml              //hdfs-site.xml
hadoop-mapreduce-client-core.7.3.jar/mapred-default.xml //mapred-site.xml
hadoop-yarn-common-2.7.3.jar/yarn-default.xml       //yarn-site.xml
</code></pre>



<h2 id="hadoop-client编程">hadoop client编程</h2>

<pre><code>1.pom.xml
    &lt;?xml version="1.0" encoding="UTF-8"?&gt;
    &lt;project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
        &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

        &lt;groupId&gt;com.it18zhang&lt;/groupId&gt;
        &lt;artifactId&gt;my-hadoop-day01&lt;/artifactId&gt;
        &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
        &lt;dependencies&gt;
            &lt;dependency&gt;
                &lt;groupId&gt;junit&lt;/groupId&gt;
                &lt;artifactId&gt;junit&lt;/artifactId&gt;
                &lt;version&gt;4.11&lt;/version&gt;
            &lt;/dependency&gt;
            &lt;dependency&gt;
                &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
                &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
                &lt;version&gt;2.7.3&lt;/version&gt;
            &lt;/dependency&gt;
        &lt;/dependencies&gt;
    &lt;/project&gt;
2.测试类

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.FSDataOutputStream;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.junit.Test;

    import java.io.IOException;

    /**
     * 测试hdfs
     */
    public class TestHDFS {
        @Test
        public void testWrite() throws Exception {
            //配置对象
            Configuration conf = new Configuration();
            //conf.set("fs.defaultFS","hdfs://192.168.231.100:8020");
            //文件系统
            FileSystem fs = FileSystem.get(conf);
            Path path = new Path("/user/centos/3.txt");
            //创建输出流
            FSDataOutputStream out = fs.create(path);
            String str = "hello world" ;
            out.write(str.getBytes());
            out.flush();
            out.close();
        }

        @Test
        public void testWrite2() throws Exception {
            //配置对象
            Configuration conf = new Configuration();
            //conf.set("fs.defaultFS","hdfs://192.168.231.100:8020");
            //文件系统
            FileSystem fs = FileSystem.get(conf);
            Path path = new Path("/user/centos/3.txt");
            //创建输出流
            FSDataOutputStream out = fs.create(path);
            byte[] bytes = new byte[128 * 1024 * 1024 + 1] ;
            out.write(bytes);
            out.flush();
            out.close();
        }
    }
</code></pre>



<h2 id="url">URL</h2>

<pre><code>uniform resource locator,统一资源定位。
方案         authority   
${scheme}://${hostname}:${port}/${path}?${queryStr}#{segment}
http://www.baidu.com:80/index.html
fs.defautlFS=hdfs://s100:8020
             file:///
</code></pre>



<h2 id="filesystemgetconf">FileSystem.get(conf);</h2>

<pre><code>SERVICE_FILE_SYSTEMS.put("file",LocalFileSystem.class);
SERVICE_FILE_SYSTEMS.put("viewfs",LocalFileSystem.class);
SERVICE_FILE_SYSTEMS.put("har",LocalFileSystem.class);
SERVICE_FILE_SYSTEMS.put("ftp",LocalFileSystem.class);
SERVICE_FILE_SYSTEMS.put("hdfs",DistributedFileSystem.class);

fs.file.impl=
dfs.replication.max=512 
dfs.replication.min=1   
dfs.replication=3
</code></pre>



<h2 id="查询hadoop集群的配置">查询hadoop集群的配置</h2>

<pre><code>hdfs getconf -namenodes
hdfs getconf -confKey dfs.replication.min
</code></pre>



<h2 id="修改最小副本数">修改最小副本数</h2>

<pre><code>1.[hdfs-site.xml]
dfs.replication.min=2

2.通过命令查看
    hdfs getconf -confKey dfs.replication.min
    可以即刻查看结果，集群没有生效。

3.需要重启, 不需要分发
</code></pre>



<h2 id="修改block大小">修改block大小</h2>

<pre><code>1.块最小值
    [hdfs-site.xml]
    dfs.namenode.fs-limits.min-block-size=148576(1M)
    防止创建大量小文件。必须是512字节的倍数。
    512字节是校验单位。

2.校验位数
    每多数字节进行一次校验，默认是512.不能超过dfs.stream-buffer-size值。
    [hdfs-site.xml]
    dfs.bytes-per-checksum = 512

3.dfs写入时缓冲区设置
    dfs写入时，数据先进入buffer，默认缓冲区大小4096（4K）
    [hdfs-site.xml]
    dfs.stream-buffer-size=4094

3.配置块大小
    [hdfs-site.xml]
    dfs.blocksize=128M
</code></pre>



<h2 id="安全模式">安全模式</h2>

<pre><code>hdfs dfsadmin -safemode enter
hdfs dfsadmin -safemode leave
hdfs dfsadmin -safemode wait
hdfs dfsadmin -safemode get
</code></pre>

<h2 id="hdfs写入过程剖析">hdfs写入过程剖析</h2>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">DFSOutputStream</span>
    <span class="hljs-tag">DataStreamer</span>
    <span class="hljs-tag">DFSPackage</span><span class="hljs-rules">{
        <span class="hljs-rule"><span class="hljs-attribute">buf </span>:<span class="hljs-value"> <span class="hljs-number">33</span> +  <span class="hljs-number">64</span>K
              <span class="hljs-number">64</span><span class="hljs-function">k((<span class="hljs-number">512</span> + <span class="hljs-number">4</span>)</span> * <span class="hljs-number">126</span> + x)
    </span></span></span>}
    <span class="hljs-tag">dataQueue</span>
    <span class="hljs-tag">ackQueue</span></code></pre>

<p><a href="http://blog.csdn.net/wqhaber/article/details/77591618" rel="nofollow">More Details</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>