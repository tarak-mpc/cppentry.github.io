---
layout:     post
title:      FTP to HDFS代码示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="center"><a href="http://hadoop101.blogspot.com/2011/12/ftp-to-hdfs.html" rel="nofollow">FTP to HDFS</a></h1>
<div>An FTP client that stores content directly into HDFS is good. It allows data from FTP serves to be directly stored into HDFS instead of first copying the data locally and then uploading it into HDFS. The benefits are apparent from an administrative perspective
 as large datasets can be pulled from FTP servers with minimal human intervention.<br><div><br>
This will greatly simplify data being pulled from FTP Servers to HDFS. This also makes it faster as we reduce one hop into local file system.<br></div>
<p>At present we are faced with the issue of our data lying in different remote FTP server locations.<br></p>
<p>This utility essentially provides following benefits<br>
1. The steps of 'pull data from FTP server', 'store locally', 'tranfer to HDFS' and 'delete local copy' are converted into 1 step - 'Pull data and store into HDFS' .<br>
2. No need to worry about lack of local storage as data goes directly into HDFS.<br>
3. Can be used to run a batch of commands that include pulling data from different FTP servers.</p>
<p>All of this greatly simplifies administrative tasks.</p>
<p>Thanks to <a href="https://issues.apache.org/jira/secure/ViewProfile.jspa?name=ankur" rel="nofollow">
Ankur</a> for fixing issue <a id="key-val" href="https://issues.apache.org/jira/browse/HADOOP-3246" rel="nofollow">
HADOOP-3246</a>.</p>
<p>Following program does the job :</p>
<pre><code class="language-java">import java.io.IOException;
import java.io.OutputStream;
import java.net.URI;
import java.net.URISyntaxException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.fs.ftp.FTPFileSystem;
import org.apache.hadoop.io.IOUtils;

public class FTPtoHDFS
{
    public static void main(String[] args) throws IOException, URISyntaxException
    {
        String src="test1.txt";
        Configuration conf = new Configuration();
        FTPFileSystem ftpfs = new FTPFileSystem();
        ftpfs.setConf(conf);
        ftpfs.initialize(new URI("ftp://username:password@host"), conf);
        FSDataInputStream fsdin = ftpfs.open(new Path(src), 1000);
        FileSystem fileSystem=FileSystem.get(conf);
        OutputStream outputStream=fileSystem.create(new Path(args[0]));
        IOUtils.copyBytes(fsdin, outputStream, conf, true);
    }
}</code></pre><br><br></div>
            </div>
                </div>