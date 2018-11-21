---
layout:     post
title:      Hadoop的HDFS命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/someby/article/details/82945672				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html" rel="nofollow">HDFS用户命令指南</a></p>

<p><a href="http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html" rel="nofollow">HDFS文件系统Shell命令</a></p>

<p>   Hadoop的三种命令形式</p>

<p>    hadoop fs ---适用于任何不同的文件系统，比如本地文件系统和HDFS文件系统<br>
    hadoop dfs---只能适用于HDFS文件系统<br>
    hdfs dfs---跟hadoop dfs的命令作用一样，也只能适用于HDFS文件系统<br>
    <br>
    1.HDFS上存储的文件是以Block的方式存在的，一个文件的不同的Block具体存储在哪些Block机器上没有任何关系；<br>
    2.主从结构<br>
        主节点，只有一个：nameNode（HA下会有多个nameNode）<br>
        从节点，有多个：dataNode<br>
    nameNode负责：<br>
        接受用户操作的请求<br>
        维护文件系统的目录结构<br>
        管理文件与Block之间的关系，Block与DataNode之间的关系<br>
    dataNode负责：<br>
        存储文件<br>
        文件被分成Block存储到磁盘上<br>
        为保证数据安全，文件会有多个副本<br>
    3.HDFS本身只是一套分布式数据存储的逻辑管理软件，实际上数据是分布式存储在具体的Linux机器的磁盘上且被HDFS管理；<br>
    4.HDFS提供了分布式文件管理的众多的工具，例如hdfs fsck / <br>
    5.HDFS读取数据：Client向nameNode发起读数据的请求，nameNode会检索Client读取数据的元数据信息来决定第一个Block具体在哪些太机器上，并根据hadoop的机架感知策略等决定把那个副本返回给Client读取，其实nameNode是返回了该副本的URL，接下来就是Client通过该URL建立InputStream进行数据的读取；后续Block按照同样的方式读取，以此类推；<br>
    6.HDFS写入数据：此时客户端向nameNode发起写数据的请求，nameNode会决定第一个第一个Block及副本些在哪些台机器上，一般而言如果有三个副本，如果两个副本在同一个Rack，另外一个副本在其他机架上，但是实际上写入数据的时候Client只写入每个Block的第一个副本给HDFS，Client写入每个Block的第一个副本后，后续该Block的副本是在nameNode的管理下基于第一个副本的数据进行pipeline方式写入；</p>

<p>Java编程示例：</p>

<pre class="has">
<code>import java.io.IOException;
import java.io.InputStream;
import java.net.URI;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;

/**
 * FileName: HelloHDFS
 * Author:   hadoop
 * Email:    3165845957@qq.com
 * Date:     18-10-4 上午11:51
 * Description:
 */
public class HelloHDFS {
    public static void main(String[] args) throws IOException {
        String uri = "hdfs://Master:9000";
        Configuration config = new Configuration();
        FileSystem fs = FileSystem.get(URI.create(uri),config);
        //列出hdfs上/usr/spark目录下的所有文件和目录
        FileStatus[] statuses = fs.listStatus(new Path("/"));
        for ( FileStatus status : statuses){
            System.out.println(status);
        }
        //在hdfs的/usr/spark目录中写入一个文件，并写入一个文本行
        FSDataOutputStream os = fs.create(new Path("/user/hadoop/test.log"));
        os.write("Hello World!".getBytes());
        os.flush();
        os.close();
        //显示在hdfs的//home/hadoop/test/test.log下的内容
        InputStream is = fs.open(new Path("/user/hadoop/test.log"));
        IOUtils.copyBytes(is,System.out,1024,true);
    }
}
</code></pre>

<p><br>
    </p>            </div>
                </div>