---
layout:     post
title:      Hadoop常见的shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="%E4%B8%80%E3%80%81Hadoop%20%E5%91%BD%E4%BB%A4-toc" style="margin-left:0px;"><a href="#%E4%B8%80%E3%80%81Hadoop%20%E5%91%BD%E4%BB%A4" rel="nofollow">一、Hadoop 命令</a></p>

<p id="1%E3%80%81%E6%9F%A5%E7%9C%8BHadoop%E7%9A%84%E7%89%88%E6%9C%AC-toc" style="margin-left:40px;"><a href="#1%E3%80%81%E6%9F%A5%E7%9C%8BHadoop%E7%9A%84%E7%89%88%E6%9C%AC" rel="nofollow">1、查看Hadoop的版本</a></p>

<p id="2%E3%80%81%E6%9F%A5%E7%9C%8BHadoop%E5%91%BD%E4%BB%A4%E7%9A%84%E6%A0%BC%E5%BC%8F-toc" style="margin-left:40px;"><a href="#2%E3%80%81%E6%9F%A5%E7%9C%8BHadoop%E5%91%BD%E4%BB%A4%E7%9A%84%E6%A0%BC%E5%BC%8F" rel="nofollow">2、查看Hadoop命令的格式</a></p>

<p id="%C2%A0%C2%A02.1%E3%80%81%E5%8E%8B%E7%BC%A9%E6%96%87%E4%BB%B6archive-toc" style="margin-left:80px;"><a href="#%C2%A0%C2%A02.1%E3%80%81%E5%8E%8B%E7%BC%A9%E6%96%87%E4%BB%B6archive" rel="nofollow">  2.1、压缩文件archive</a></p>

<p id="2.2%E3%80%81%E5%9C%A8%E4%B8%A4%E4%B8%AAHDFS%E4%B9%8B%E9%97%B4%E6%8B%B7%E8%B4%9D%E6%95%B0%E6%8D%AE%20%EF%BC%88hadoop%20distcp%EF%BC%89-toc" style="margin-left:80px;"><a href="#2.2%E3%80%81%E5%9C%A8%E4%B8%A4%E4%B8%AAHDFS%E4%B9%8B%E9%97%B4%E6%8B%B7%E8%B4%9D%E6%95%B0%E6%8D%AE%20%EF%BC%88hadoop%20distcp%EF%BC%89" rel="nofollow">2.2、在两个HDFS之间拷贝数据 （hadoop distcp）</a></p>

<p id="3%E3%80%81%E9%80%9A%E8%BF%87Hadoop%20fs%20%E6%9F%A5%E7%9C%8BHDFS%E7%9A%84%E6%A0%BC%E5%BC%8F-toc" style="margin-left:40px;"><a href="#3%E3%80%81%E9%80%9A%E8%BF%87Hadoop%20fs%20%E6%9F%A5%E7%9C%8BHDFS%E7%9A%84%E6%A0%BC%E5%BC%8F" rel="nofollow">3、通过Hadoop fs 查看HDFS的格式</a></p>

<p id="%C2%A0%C2%A0%20%C2%A0%C2%A0%20%C2%A0%E4%BA%8C%E3%80%81hdfs%E5%91%BD%E4%BB%A4-toc" style="margin-left:0px;"><a href="#%C2%A0%C2%A0%20%C2%A0%C2%A0%20%C2%A0%E4%BA%8C%E3%80%81hdfs%E5%91%BD%E4%BB%A4" rel="nofollow">       二、hdfs命令</a></p>

<p id="1%E3%80%81%E6%9F%A5%E7%9C%8Bhdfs%E7%9A%84%E7%89%88%E6%9C%AC-toc" style="margin-left:40px;"><a href="#1%E3%80%81%E6%9F%A5%E7%9C%8Bhdfs%E7%9A%84%E7%89%88%E6%9C%AC" rel="nofollow">1、查看hdfs的版本</a></p>

<p id="2%E3%80%81%E6%9F%A5%E7%9C%8Bhdfs%E5%91%BD%E4%BB%A4%E7%9A%84%E6%A0%BC%E5%BC%8F-toc" style="margin-left:40px;"><a href="#2%E3%80%81%E6%9F%A5%E7%9C%8Bhdfs%E5%91%BD%E4%BB%A4%E7%9A%84%E6%A0%BC%E5%BC%8F" rel="nofollow">2、查看hdfs命令的格式</a></p>

<p id="%C2%A0%C2%A0%20%C2%A03%E3%80%81%E7%AE%A1%E7%90%86%E5%91%98%E6%93%8D%E4%BD%9C%E5%91%BD%E4%BB%A4hdfs%20dfsadmin-toc" style="margin-left:40px;"><a href="#%C2%A0%C2%A0%20%C2%A03%E3%80%81%E7%AE%A1%E7%90%86%E5%91%98%E6%93%8D%E4%BD%9C%E5%91%BD%E4%BB%A4hdfs%20dfsadmin" rel="nofollow">    3、管理员操作命令hdfs dfsadmin</a></p>

<p id="%E4%B8%89%E3%80%81%E6%93%8D%E4%BD%9CHDFS%E7%9A%84%E4%B8%80%E8%88%ACshell%E5%91%BD%E4%BB%A4%EF%BC%88hadoop%20fs%20%E5%92%8C%20hdfs%20dfs%20%E9%80%9A%E7%94%A8%EF%BC%89-toc" style="margin-left:0px;"><a href="#%E4%B8%89%E3%80%81%E6%93%8D%E4%BD%9CHDFS%E7%9A%84%E4%B8%80%E8%88%ACshell%E5%91%BD%E4%BB%A4%EF%BC%88hadoop%20fs%20%E5%92%8C%20hdfs%20dfs%20%E9%80%9A%E7%94%A8%EF%BC%89" rel="nofollow">三、操作HDFS的一般shell命令（hadoop fs 和 hdfs dfs 通用）</a></p>

<p id="1%E3%80%81%E5%88%97%E5%87%BA%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95(-ls%20%E5%92%8C%20-ls%20-R)-toc" style="margin-left:40px;"><a href="#1%E3%80%81%E5%88%97%E5%87%BA%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95(-ls%20%E5%92%8C%20-ls%20-R)" rel="nofollow">1、列出文件目录(-ls 和 -ls -R)</a></p>

<p id="2%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E5%88%9B%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9%EF%BC%88-mkdir%EF%BC%89-toc" style="margin-left:40px;"><a href="#2%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E5%88%9B%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9%EF%BC%88-mkdir%EF%BC%89" rel="nofollow">2、在HDFS中创建文件夹（-mkdir）</a></p>

<p id="3%E3%80%81%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E5%88%B0HDFS%E4%B8%AD%EF%BC%88-put%20-moveFromLocal%20-copyFromLocal%EF%BC%89-toc" style="margin-left:40px;"><a href="#3%E3%80%81%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E5%88%B0HDFS%E4%B8%AD%EF%BC%88-put%20-moveFromLocal%20-copyFromLocal%EF%BC%89" rel="nofollow">3、上传文件到HDFS中（-put -moveFromLocal -copyFromLocal）</a></p>

<p id="4%E3%80%81%E4%BB%8EHDFS%E4%B8%AD%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6%EF%BC%88-get%20-moveToLocal%20-copyToLocal%EF%BC%89-toc" style="margin-left:40px;"><a href="#4%E3%80%81%E4%BB%8EHDFS%E4%B8%AD%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6%EF%BC%88-get%20-moveToLocal%20-copyToLocal%EF%BC%89" rel="nofollow">4、从HDFS中下载文件（-get -moveToLocal -copyToLocal）</a></p>

<p id="5%E3%80%81%E7%9B%B4%E6%8E%A5%E5%9C%A8HDFS%E4%B8%AD%E6%9F%A5%E7%9C%8B%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6%EF%BC%88-text%2F-cat%EF%BC%89-toc" style="margin-left:40px;"><a href="#5%E3%80%81%E7%9B%B4%E6%8E%A5%E5%9C%A8HDFS%E4%B8%AD%E6%9F%A5%E7%9C%8B%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6%EF%BC%88-text%2F-cat%EF%BC%89" rel="nofollow">5、直接在HDFS中查看某个文件（-text/-cat）</a></p>

<p id="6%E3%80%81%E5%88%A0%E9%99%A4%E5%9C%A8HDFS%E4%B8%AD%E7%9A%84%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6(%E5%A4%B9)%EF%BC%88-rm%EF%BC%89-toc" style="margin-left:40px;"><a href="#6%E3%80%81%E5%88%A0%E9%99%A4%E5%9C%A8HDFS%E4%B8%AD%E7%9A%84%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6(%E5%A4%B9)%EF%BC%88-rm%EF%BC%89" rel="nofollow">6、删除在HDFS中的某个文件(夹)（-rm）</a></p>

<p id="7%E3%80%81%E6%8A%8AHDFS%E6%8C%87%E5%AE%9A%E7%9B%AE%E5%BD%95%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E5%90%88%E5%B9%B6%E8%BE%93%E5%87%BA%E5%88%B0%E6%9C%AC%E5%9C%B0%EF%BC%88-getmerge%EF%BC%89-toc" style="margin-left:40px;"><a href="#7%E3%80%81%E6%8A%8AHDFS%E6%8C%87%E5%AE%9A%E7%9B%AE%E5%BD%95%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E5%90%88%E5%B9%B6%E8%BE%93%E5%87%BA%E5%88%B0%E6%9C%AC%E5%9C%B0%EF%BC%88-getmerge%EF%BC%89" rel="nofollow">7、把HDFS指定目录下的文件合并输出到本地（-getmerge）</a></p>

<p id="8%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E5%A4%8D%E5%88%B6%E6%96%87%E4%BB%B6%EF%BC%88-cp%EF%BC%89-toc" style="margin-left:40px;"><a href="#8%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E5%A4%8D%E5%88%B6%E6%96%87%E4%BB%B6%EF%BC%88-cp%EF%BC%89" rel="nofollow">8、在HDFS中复制文件（-cp）</a></p>

<p id="9%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E7%A7%BB%E5%8A%A8%E6%96%87%E4%BB%B6%EF%BC%88-mv%EF%BC%89-toc" style="margin-left:40px;"><a href="#9%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E7%A7%BB%E5%8A%A8%E6%96%87%E4%BB%B6%EF%BC%88-mv%EF%BC%89" rel="nofollow">9、在HDFS中移动文件（-mv）</a></p>

<p id="10%E3%80%81%E7%BB%9F%E8%AE%A1hdfs%E5%AF%B9%E5%BA%94%E8%B7%AF%E5%BE%84%E4%B8%8B%E7%9A%84%E7%9B%AE%E5%BD%95%E4%B8%AA%E6%95%B0%EF%BC%8C%E6%96%87%E4%BB%B6%E4%B8%AA%E6%95%B0%EF%BC%8C%E6%96%87%E4%BB%B6%E6%80%BB%E8%AE%A1%E5%A4%A7%E5%B0%8F%EF%BC%88-count%EF%BC%89-toc" style="margin-left:40px;"><a href="#10%E3%80%81%E7%BB%9F%E8%AE%A1hdfs%E5%AF%B9%E5%BA%94%E8%B7%AF%E5%BE%84%E4%B8%8B%E7%9A%84%E7%9B%AE%E5%BD%95%E4%B8%AA%E6%95%B0%EF%BC%8C%E6%96%87%E4%BB%B6%E4%B8%AA%E6%95%B0%EF%BC%8C%E6%96%87%E4%BB%B6%E6%80%BB%E8%AE%A1%E5%A4%A7%E5%B0%8F%EF%BC%88-count%EF%BC%89" rel="nofollow">10、统计hdfs对应路径下的目录个数，文件个数，文件总计大小（-count）</a></p>

<p id="11%E3%80%81%E6%98%BE%E7%A4%BAhdfs%E5%AF%B9%E5%BA%94%E8%B7%AF%E5%BE%84%E4%B8%8B%E6%AF%8F%E4%B8%AA%E6%96%87%E4%BB%B6%E5%A4%B9%E5%92%8C%E6%96%87%E4%BB%B6%E7%9A%84%E5%A4%A7%E5%B0%8F%EF%BC%88-du%EF%BC%89-toc" style="margin-left:40px;"><a href="#11%E3%80%81%E6%98%BE%E7%A4%BAhdfs%E5%AF%B9%E5%BA%94%E8%B7%AF%E5%BE%84%E4%B8%8B%E6%AF%8F%E4%B8%AA%E6%96%87%E4%BB%B6%E5%A4%B9%E5%92%8C%E6%96%87%E4%BB%B6%E7%9A%84%E5%A4%A7%E5%B0%8F%EF%BC%88-du%EF%BC%89" rel="nofollow">11、显示hdfs对应路径下每个文件夹和文件的大小（-du）</a></p>

<p id="12%E3%80%81%E6%94%B9%E5%8F%98%E4%B8%80%E4%B8%AA%E6%96%87%E4%BB%B6%E5%9C%A8hdfs%E4%B8%AD%E7%9A%84%E5%89%AF%E6%9C%AC%E4%B8%AA%E6%95%B0%EF%BC%88-setrep%EF%BC%89-toc" style="margin-left:40px;"><a href="#12%E3%80%81%E6%94%B9%E5%8F%98%E4%B8%80%E4%B8%AA%E6%96%87%E4%BB%B6%E5%9C%A8hdfs%E4%B8%AD%E7%9A%84%E5%89%AF%E6%9C%AC%E4%B8%AA%E6%95%B0%EF%BC%88-setrep%EF%BC%89" rel="nofollow">12、改变一个文件在hdfs中的副本个数（-setrep）</a></p>

<p id="13%E3%80%81%E6%9F%A5%E7%9C%8BHDFS%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6%E7%9A%84%E7%8A%B6%E6%80%81%EF%BC%88-stat%EF%BC%89-toc" style="margin-left:40px;"><a href="#13%E3%80%81%E6%9F%A5%E7%9C%8BHDFS%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6%E7%9A%84%E7%8A%B6%E6%80%81%EF%BC%88-stat%EF%BC%89" rel="nofollow">13、查看HDFS某个文件的状态（-stat）</a></p>

<p id="14%E3%80%81%E6%98%BE%E7%A4%BAHDFS%E7%9A%84%E6%96%87%E4%BB%B6%E5%B0%BE%E9%83%A8%EF%BC%88-tail%EF%BC%89-toc" style="margin-left:40px;"><a href="#14%E3%80%81%E6%98%BE%E7%A4%BAHDFS%E7%9A%84%E6%96%87%E4%BB%B6%E5%B0%BE%E9%83%A8%EF%BC%88-tail%EF%BC%89" rel="nofollow">14、显示HDFS的文件尾部（-tail）</a></p>

<hr id="hr-toc"><p><span style="color:#f33b45;"><strong>hadoop fs  等价于 hdfs dfs </strong></span></p>

<p>善用help命令求帮助</p>

<ul><li>hadoop -help</li>
	<li>hdfs -help</li>
	<li>hadoop fs -help 命令  </li>
	<li>hdfs dfsadmin -help 命令</li>
	<li>hdfs dfs -help 命令</li>
</ul><p>如：查看ls命令的帮助：hadoop fs -help ls</p>

<h1 id="%E4%B8%80%E3%80%81Hadoop%20%E5%91%BD%E4%BB%A4">一、Hadoop 命令</h1>

<h2 id="1%E3%80%81%E6%9F%A5%E7%9C%8BHadoop%E7%9A%84%E7%89%88%E6%9C%AC">1、查看Hadoop的版本</h2>

<p> hadoop version<br>
Hadoop 2.7.1<br>
Compiled by hadoop on 2016-08-01T13:16Z<br>
Compiled with protoc 2.5.0<br>
From source with checksum 57dc8ba59eafb68e93f427035b18d9d<br>
This command was run using /software/servers/hadoop-2.7.1/share/hadoop/common/hadoop-common-2.7.1.jar</p>

<h2 id="2%E3%80%81%E6%9F%A5%E7%9C%8BHadoop%E5%91%BD%E4%BB%A4%E7%9A%84%E6%A0%BC%E5%BC%8F">2、查看Hadoop命令的格式</h2>

<p>hadoop -help<br>
Usage: hadoop [--config confdir] [COMMAND | CLASSNAME]<br>
  CLASSNAME            run the class named CLASSNAME<br>
 or<br>
  where COMMAND is one of:<br>
  fs                   run a generic filesystem user client    操作HDFS的命令<br>
  version              print the version                 <br>
  jar &lt;jar&gt;            run a jar file<br>
                       note: please use "yarn jar" to launch<br>
                             YARN applications, not this command.<br>
  checknative [-a|-h]  check native hadoop and compression libraries availability<br>
  distcp &lt;srcurl&gt; &lt;desturl&gt; copy file or directories recursively<br>
  archive -archiveName NAME -p &lt;parent path&gt; &lt;src&gt;* &lt;dest&gt; create a hadoop archive<br>
  classpath            prints the class path needed to get the<br>
  credential           interact with credential providers<br>
                       Hadoop jar and the required libraries<br>
  daemonlog            get/set the log level for each daemon<br>
  trace                view and modify Hadoop tracing settings</p>

<h3 id="%C2%A0%C2%A02.1%E3%80%81%E5%8E%8B%E7%BC%A9%E6%96%87%E4%BB%B6archive">  <br>
2.1、压缩文件archive</h3>

<p>hadoop archive -archiveName name.har -p &lt; hdfs parent dir &gt; &lt; src &gt;* &lt; hdfs dst &gt;<br>
命令中参数name：压缩文件名，自己任意取；<br>
&lt; hdfs parent dir &gt; ：压缩文件所在的父目录；<br>
&lt; src &gt;：要压缩的文件名；<br>
&lt; hdfs dst &gt;：压缩文件存放路径<br>
*示例：hadoop archive -archiveName hadoop.har -p /user 1.txt 2.txt /des<br>
示例中将hdfs中/user目录下的文件1.txt，2.txt压缩成一个名叫hadoop.har的文件存放在hdfs中/des目录下，<br>
如果1.txt，2.txt不写就是将/user目录下所有的目录和文件压缩成一个名叫hadoop.har的文件存放在hdfs中/des目录下<br>
显示har的内容可以用如下命令：hadoop fs -ls /des/hadoop.jar<br>
显示har压缩的是那些文件可以用如下命令：hadoop fs -ls -R har:///des/hadoop.har<br>
注意：har文件不能进行二次压缩。如果想给.har加文件，只能找到原来的文件，重新创建一个。har文件中原来文件的数据并没有变化，har文件真正的作用是减少NameNode和DataNode过多的空间浪费。</p>

<h3 id="2.2%E3%80%81%E5%9C%A8%E4%B8%A4%E4%B8%AAHDFS%E4%B9%8B%E9%97%B4%E6%8B%B7%E8%B4%9D%E6%95%B0%E6%8D%AE%20%EF%BC%88hadoop%20distcp%EF%BC%89">2.2、在两个HDFS之间拷贝数据 （hadoop distcp）</h3>

<h2 id="3%E3%80%81%E9%80%9A%E8%BF%87Hadoop%20fs%20%E6%9F%A5%E7%9C%8BHDFS%E7%9A%84%E6%A0%BC%E5%BC%8F">3、通过Hadoop fs 查看HDFS的格式</h2>

<p>hadoop fs &lt;args&gt; URI<br>
一个获取文件的demo：hadoop fs -get hdfs://ns3/user/ls/auto_rerun/dependencies</p>

<p>URI说明：<br>
URI格式是scheme://authority/path。对HDFS文件系统，scheme是hdfs，对本地文件系统，scheme是file。其中scheme和authority参数都是可选的，如果未加指定，就会使用配置中指定的默认scheme。<br>
一个HDFS文件或目录比如/parent/child可以表示成hdfs://namenode:namenodeport/parent/child，或者更简单的/parent/child（假设你配置文件中的默认值是namenode:namenodeport）。<br>
大多数FS Shell命令的行为和对应的Unix Shell命令类似，出错信息会输出到stderr，其他信息输出到stdout。</p>

<p>参数说明：<br>
hadoop fs -help<br>
Usage: hadoop fs [generic options]<br>
    [-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-cat [-ignoreCrc] &lt;src&gt; ...]<br>
    [-checksum &lt;src&gt; ...]<br>
    [-chgrp [-R] GROUP PATH...]<br>
    [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]<br>
    [-chown [-R] [OWNER][:[GROUP]] PATH...]<br>
    [-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]<br>
    [-count [-q] [-h] &lt;path&gt; ...]<br>
    [-cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;]<br>
    [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]<br>
    [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]<br>
    [-df [-h] [&lt;path&gt; ...]]<br>
    [-du [-s] [-h] &lt;path&gt; ...]<br>
    [-expunge]<br>
    [-find &lt;path&gt; ... &lt;expression&gt; ...]<br>
    [-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]<br>
    [-getfacl [-R] &lt;path&gt;]<br>
    [-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]<br>
    [-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]<br>
    [-help [cmd ...]]<br>
    [-ls [-d] [-h] [-R] [&lt;path&gt; ...]]<br>
    [-mkdir [-p] &lt;path&gt; ...]<br>
    [-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-moveToLocal &lt;src&gt; &lt;localdst&gt;]<br>
    [-mv &lt;src&gt; ... &lt;dst&gt;]<br>
    [-put [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]<br>
    [-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; ...]<br>
    [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; ...]<br>
    [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]<br>
    [-setfattr {-n name [-v value] | -x name} &lt;path&gt;]<br>
    [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; ...]<br>
    [-stat [format] &lt;path&gt; ...]<br>
    [-tail [-f] &lt;file&gt;]<br>
    [-test -[defsz] &lt;path&gt;]<br>
    [-text [-ignoreCrc] &lt;src&gt; ...]<br>
    [-touchz &lt;path&gt; ...]<br>
    [-truncate [-w] &lt;length&gt; &lt;path&gt; ...]<br>
    [-usage [cmd ...]]</p>

<h1 id="%C2%A0%C2%A0%20%C2%A0%C2%A0%20%C2%A0%E4%BA%8C%E3%80%81hdfs%E5%91%BD%E4%BB%A4">       <br>
二、hdfs命令</h1>

<h2 id="1%E3%80%81%E6%9F%A5%E7%9C%8Bhdfs%E7%9A%84%E7%89%88%E6%9C%AC"><br>
1、查看hdfs的版本</h2>

<p>hdfs version<br>
Hadoop 2.7.1<br>
Compiled by hadoop on 2016-08-01T13:16Z<br>
Compiled with protoc 2.5.0<br>
From source with checksum 57dc8ba59eafb68e93f427035b18d9d<br>
This command was run using /software/servers/hadoop-2.7.1/share/hadoop/common/hadoop-common-2.7.1.jar</p>

<h2 id="2%E3%80%81%E6%9F%A5%E7%9C%8Bhdfs%E5%91%BD%E4%BB%A4%E7%9A%84%E6%A0%BC%E5%BC%8F"><br>
2、查看hdfs命令的格式</h2>

<p>hdfs -help<br>
Usage: hdfs [--config confdir] [--loglevel loglevel] COMMAND<br>
       where COMMAND is one of:<br>
  dfs                  run a filesystem command on the file systems supported in Hadoop.<br>
  classpath            prints the classpath<br>
  namenode -format     format the DFS filesystem<br>
  secondarynamenode    run the DFS secondary namenode<br>
  namenode             run the DFS namenode<br>
  journalnode          run the DFS journalnode<br>
  zkfc                 run the ZK Failover Controller daemon<br>
  datanode             run a DFS datanode<br>
  dfsadmin             run a DFS admin client<br>
  haadmin              run a DFS HA admin client<br>
  fsck                 run a DFS filesystem checking utility<br>
  balancer             run a cluster balancing utility<br>
  jmxget               get JMX exported values from NameNode or DataNode.<br>
  mover                run a utility to move block replicas across<br>
                       storage types<br>
  oiv                  apply the offline fsimage viewer to an fsimage<br>
  oiv_legacy           apply the offline fsimage viewer to an legacy fsimage<br>
  oev                  apply the offline edits viewer to an edits file<br>
  fetchdt              fetch a delegation token from the NameNode<br>
  getconf              get config values from configuration<br>
  groups               get the groups which users belong to<br>
  snapshotDiff         diff two snapshots of a directory or diff the<br>
                       current directory contents with a snapshot<br>
  lsSnapshottableDir   list all snapshottable dirs owned by the current user<br>
                        Use -help to see options<br>
  portmap              run a portmap service<br>
  nfs3                 run an NFS version 3 gateway<br>
  cacheadmin           configure the HDFS cache<br>
  crypto               configure HDFS encryption zones<br>
  storagepolicies      list/get/set block storage policies<br>
  version              print the version</p>

<p><span style="color:#f33b45;"><strong>hdfs balancer</strong></span><br>
如果管理员发现某些DataNode保存数据过多，某些DataNode保存数据相对较少，可以使用上述命令手动启动内部的均衡过程<br>
    <br>
    <br>
 hdfs dfs -help<br>
Usage: hadoop fs [generic options]<br>
    [-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-cat [-ignoreCrc] &lt;src&gt; ...]<br>
    [-checksum &lt;src&gt; ...]<br>
    [-chgrp [-R] GROUP PATH...]<br>
    [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]<br>
    [-chown [-R] [OWNER][:[GROUP]] PATH...]<br>
    [-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]<br>
    [-count [-q] [-h] &lt;path&gt; ...]<br>
    [-cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;]<br>
    [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]<br>
    [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]<br>
    [-df [-h] [&lt;path&gt; ...]]<br>
    [-du [-s] [-h] &lt;path&gt; ...]<br>
    [-expunge]<br>
    [-find &lt;path&gt; ... &lt;expression&gt; ...]<br>
    [-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]<br>
    [-getfacl [-R] &lt;path&gt;]<br>
    [-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]<br>
    [-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]<br>
    [-help [cmd ...]]<br>
    [-ls [-d] [-h] [-R] [&lt;path&gt; ...]]<br>
    [-mkdir [-p] &lt;path&gt; ...]<br>
    [-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-moveToLocal &lt;src&gt; &lt;localdst&gt;]<br>
    [-mv &lt;src&gt; ... &lt;dst&gt;]<br>
    [-put [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]<br>
    [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]<br>
    [-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; ...]<br>
    [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; ...]<br>
    [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]<br>
    [-setfattr {-n name [-v value] | -x name} &lt;path&gt;]<br>
    [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; ...]<br>
    [-stat [format] &lt;path&gt; ...]<br>
    [-tail [-f] &lt;file&gt;]<br>
    [-test -[defsz] &lt;path&gt;]<br>
    [-text [-ignoreCrc] &lt;src&gt; ...]<br>
    [-touchz &lt;path&gt; ...]<br>
    [-truncate [-w] &lt;length&gt; &lt;path&gt; ...]<br>
    [-usage [cmd ...]]</p>

<h2 id="%C2%A0%C2%A0%20%C2%A03%E3%80%81%E7%AE%A1%E7%90%86%E5%91%98%E6%93%8D%E4%BD%9C%E5%91%BD%E4%BB%A4hdfs%20dfsadmin">    <br>
3、管理员操作命令hdfs dfsadmin</h2>

<p>hdfs dfsadmin -help<br>
hdfs dfsadmin performs DFS administrative commands.<br>
Note: Administrative commands can only be run with superuser permission.<br>
The full syntax is: </p>

<p>hdfs dfsadmin<br>
    [-report [-live] [-dead] [-decommissioning]]<br>
    [-safemode &lt;enter | leave | get | wait&gt;]<br>
    [-saveNamespace]<br>
    [-rollEdits]<br>
    [-restoreFailedStorage true|false|check]<br>
    [-refreshNodes]<br>
    [-refreshWhitelists Y|N|ALL]<br>
    [-printWhitelists   Y|N|ALL]<br>
    [-refreshBlackList Y|N|ALL]<br>
    [-printBlackList  Y|N|ALL]<br>
    [-printWhitelistsNameNode   Y|N|ALL]<br>
    [-refreshWhitelistsDataNode Y|N|ALL]<br>
    [-printWhitelistsDataNode   Y|N|ALL]<br>
    [-refreshOtherClusterNode Y|N|ALL]<br>
    [-printOtherClusterNode  Y|N|ALL]<br>
    [-refreshSecurityPathLists Y|N|ALL]<br>
    [-printSecurityPathLists   Y|N|ALL]<br>
    [-refreshSensitiveDir Y|N|ALL]<br>
    [-printSensitiveDir   Y|N|ALL]<br>
    [-refreshTrashPolicyList Y|N|ALL]<br>
    [-printTrashPolicyList   Y|N|ALL]<br>
    [-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;]<br>
    [-clrQuota &lt;dirname&gt;...&lt;dirname&gt;]<br>
    [-setSpaceQuota &lt;quota&gt; [-storageType &lt;storagetype&gt;] &lt;dirname&gt;...&lt;dirname&gt;]<br>
    [-clrSpaceQuota [-storageType &lt;storagetype&gt;] &lt;dirname&gt;...&lt;dirname&gt;]<br>
    [-finalizeUpgrade]<br>
    [-rollingUpgrade [&lt;query|prepare|finalize&gt;]]<br>
    [-refreshServiceAcl]<br>
    [-refreshUserToGroupsMappings]<br>
    [-refreshSuperUserGroupsConfiguration]<br>
    [-refreshCallQueue]<br>
    [-refresh &lt;host:ipc_port&gt; &lt;key&gt; [arg1..argn]<br>
    [-reconfig &lt;datanode|...&gt; &lt;host:ipc_port&gt; &lt;start|status&gt;]<br>
    [-printTopology]<br>
    [-refreshNamenodes datanode_host:ipc_port]<br>
    [-deleteBlockPool datanode_host:ipc_port blockpoolId [force]]<br>
    [-setBalancerBandwidth &lt;bandwidth in bytes per second&gt;]<br>
    [-fetchImage &lt;local directory&gt;]<br>
    [-allowSnapshot &lt;snapshotDir&gt;]<br>
    [-disallowSnapshot &lt;snapshotDir&gt;]<br>
    [-shutdownDatanode &lt;datanode_host:ipc_port&gt; [upgrade]]<br>
    [-getDatanodeInfo &lt;datanode_host:ipc_port&gt;]<br>
    [-metasave filename]<br>
    [-triggerBlockReport [-incremental] &lt;datanode_host:ipc_port&gt;]<br>
    [-getAuditFilterState]<br>
    [-refreshAuditFilter]<br>
    [-getERPAuthState]<br>
    [-refreshERPAuthState]<br>
    [-help [cmd]]</p>

<p><br>
hdfs dfsadmin -help<br>
管理员可以通过dfsadmin管理HDFS，用法可以通过上述命令查看<br>
3.1、hdfs dfsadmin -report显示文件系统的基本数据</p>

<p>3.2、hdfs dfsadmin -safemode &lt; enter | leave | get | wait &gt;<br>
enter：进入安全模式；leave：离开安全模式；get：获知是否开启安全模式；<br>
wait：等待离开安全模式</p>

<h1 id="%E4%B8%89%E3%80%81%E6%93%8D%E4%BD%9CHDFS%E7%9A%84%E4%B8%80%E8%88%ACshell%E5%91%BD%E4%BB%A4%EF%BC%88hadoop%20fs%20%E5%92%8C%20hdfs%20dfs%20%E9%80%9A%E7%94%A8%EF%BC%89">三、操作HDFS的一般shell命令（hadoop fs 和 hdfs dfs 通用）</h1>

<h2 id="1%E3%80%81%E5%88%97%E5%87%BA%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95(-ls%20%E5%92%8C%20-ls%20-R)">1、列出文件目录(-ls 和 -ls -R)</h2>

<p>hadoop fs -ls URI<br>
查看HDFS根目录下的目录：hadoop fs -ls / <br>
递归查看HDFS根目录下的目录：hadoop fs -ls -R /</p>

<h2 id="2%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E5%88%9B%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9%EF%BC%88-mkdir%EF%BC%89"><br>
2、在HDFS中创建文件夹（-mkdir）</h2>

<p>hadoop fs -mkdir 文件夹名称 </p>

<p>hadoop fs -mkdir &lt; hdfs path&gt;<br>
只能一级一级的建目录，父目录不存在的话使用这个命令会报错</p>

<p>hadoop fs -mkdir -p &lt; hdfs path&gt; <br>
所创建的目录如果父目录不存在就创建该父目录</p>

<h2 id="3%E3%80%81%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E5%88%B0HDFS%E4%B8%AD%EF%BC%88-put%20-moveFromLocal%20-copyFromLocal%EF%BC%89"><br>
3、上传文件到HDFS中（-put -moveFromLocal -copyFromLocal）</h2>

<p>hadoop fs -put 本地源路径 目标存放路径<br>
将本地系统中的一个log文件上传到di文件夹中：hadoop fs -put test.log /di<br>
备注：我们通过Hadoop Shell上传的文件是存放在DataNode的Block（数据块）中的，通过Linux Shell是看不到文件的，只能看到Block。<br>
因此，可以用一句话来描述HDFS：把客户端的大文件存放在很多节点的数据块中。</p>

<p>adoop fs -put &lt; local file &gt; &lt; hdfs file &gt;<br>
hdfs file的父目录一定要存在，否则命令不会执行</p>

<p>hadoop fs -put  &lt; local file or dir &gt;...&lt; hdfs dir &gt;<br>
hdfs dir 一定要存在，否则命令不会执行</p>

<p>hadoop fs -put - &lt; hdsf  file&gt;<br>
从键盘读取输入到hdfs file中，按Ctrl+D结束输入，hdfs file不能存在，否则命令不会执行</p>

<p>moveFromLocal<br>
hadoop fs -moveFromLocal  &lt; local src &gt; ... &lt; hdfs dst &gt;<br>
与put相类似，命令执行后源文件 local src 被删除，也可以从从键盘读取输入到hdfs file中</p>

<p>copyFromLocal<br>
hadoop fs -copyFromLocal  &lt; local src &gt; ... &lt; hdfs dst &gt;<br>
与put相类似，也可以从从键盘读取输入到hdfs file中</p>

<h2 id="4%E3%80%81%E4%BB%8EHDFS%E4%B8%AD%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6%EF%BC%88-get%20-moveToLocal%20-copyToLocal%EF%BC%89">4、从HDFS中下载文件（-get -moveToLocal -copyToLocal）</h2>

<p>hadoop fs -get HDFS文件路径 本地存放路径<br>
将刚刚上传的test.log下载到本地的Desktop文件夹中：hadoop fs -get /di/test.log /home/hadoop/Desktop</p>

<p>hadoop fs -get &lt; hdfs file &gt; &lt; local file or dir&gt;<br>
local file不能和 hdfs file名字不能相同，否则会提示文件已存在，没有重名的文件会复制到本地</p>

<p>hadoop fs -get &lt; hdfs file or dir &gt; ... &lt; local  dir &gt;<br>
拷贝多个文件或目录到本地时，本地要为文件夹路径<br>
注意：如果用户不是root， local 路径要为用户文件夹下的路径，否则会出现权限问题，</p>

<p>moveToLocal<br>
当前版本中还未实现此命令</p>

<p>copyToLocal<br>
hadoop fs -copyToLocal &lt; local src &gt; ... &lt; hdfs dst &gt;<br>
与get相类似</p>

<h2 id="5%E3%80%81%E7%9B%B4%E6%8E%A5%E5%9C%A8HDFS%E4%B8%AD%E6%9F%A5%E7%9C%8B%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6%EF%BC%88-text%2F-cat%EF%BC%89">5、直接在HDFS中查看某个文件（-text/-cat）</h2>

<p>hadoop fs -text(-cat) 文件存放路径<br>
在HDFS查看刚刚上传的test.log文件：hadoop fs -text /di/test.log</p>

<p>hadoop fs -text &lt; hdsf file&gt;<br>
将文本文件或某些格式的非文本文件通过文本格式输出</p>

<h2 id="6%E3%80%81%E5%88%A0%E9%99%A4%E5%9C%A8HDFS%E4%B8%AD%E7%9A%84%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6(%E5%A4%B9)%EF%BC%88-rm%EF%BC%89">6、删除在HDFS中的某个文件(夹)（-rm）</h2>

<p>hadoop fs -rm(r) 文件存放路径<br>
删除刚刚上传的test.log文件：hadoop fs -rm /di/test.log<br>
删除HDFS中的di文件夹：hadoop fs -rm -R /di</p>

<p>hadoop fs -rm &lt; hdfs file &gt; ...<br>
hadoop fs -rm -r &lt; hdfs dir&gt;...<br>
每次可以删除多个文件或目录</p>

<h2 id="7%E3%80%81%E6%8A%8AHDFS%E6%8C%87%E5%AE%9A%E7%9B%AE%E5%BD%95%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E5%90%88%E5%B9%B6%E8%BE%93%E5%87%BA%E5%88%B0%E6%9C%AC%E5%9C%B0%EF%BC%88-getmerge%EF%BC%89">7、把HDFS指定目录下的文件合并输出到本地（-getmerge）</h2>

<p>hadoop fs -getmerge &lt; hdfs dir &gt;  &lt; local file &gt;<br>
将hdfs指定目录下所有文件排序后合并到local指定的文件中，文件不存在时会自动创建，文件存在时会覆盖里面的内容</p>

<p>hadoop fs -getmerge -nl  &lt; hdfs dir &gt;  &lt; local file &gt;<br>
加上nl后，合并到local file中的hdfs文件之间会空出一行</p>

<h2 id="8%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E5%A4%8D%E5%88%B6%E6%96%87%E4%BB%B6%EF%BC%88-cp%EF%BC%89">8、在HDFS中复制文件（-cp）</h2>

<p>hadoop fs -cp  &lt; hdfs file &gt;  &lt; hdfs file &gt;    前面是源，后面是目标<br>
目标文件不能存在，否则命令不能执行，相当于给文件重命名并保存，源文件还存在<br>
hadoop fs -cp &lt; hdfs file or dir &gt;... &lt; hdfs dir &gt;<br>
目标文件夹要存在，否则命令不能执行</p>

<h2 id="9%E3%80%81%E5%9C%A8HDFS%E4%B8%AD%E7%A7%BB%E5%8A%A8%E6%96%87%E4%BB%B6%EF%BC%88-mv%EF%BC%89">9、在HDFS中移动文件（-mv）</h2>

<p>hadoop fs -mv &lt; hdfs file &gt;  &lt; hdfs file &gt;<br>
目标文件不能存在，否则命令不能执行，相当于给文件重命名并保存，源文件不存在</p>

<p>hadoop fs -mv  &lt; hdfs file or dir &gt;...  &lt; hdfs dir &gt;<br>
源路径有多个时，目标路径必须为目录，且必须存在。<br>
注意：跨文件系统的移动（local到hdfs或者反过来）都是不允许的</p>

<h2 id="10%E3%80%81%E7%BB%9F%E8%AE%A1hdfs%E5%AF%B9%E5%BA%94%E8%B7%AF%E5%BE%84%E4%B8%8B%E7%9A%84%E7%9B%AE%E5%BD%95%E4%B8%AA%E6%95%B0%EF%BC%8C%E6%96%87%E4%BB%B6%E4%B8%AA%E6%95%B0%EF%BC%8C%E6%96%87%E4%BB%B6%E6%80%BB%E8%AE%A1%E5%A4%A7%E5%B0%8F%EF%BC%88-count%EF%BC%89">10、统计hdfs对应路径下的目录个数，文件个数，文件总计大小（-count）</h2>

<p>hadoop fs -count &lt; hdfs path &gt;<br>
统计hdfs对应路径下的目录个数，文件个数，文件总计大小<br>
输出一行用空格分割：分别为目录个数，文件个数，文件总计大小，输入路径<br>
 7           64             867944  hdfs://ns3/user/ls/auto_rerun/dependencies</p>

<h2 id="11%E3%80%81%E6%98%BE%E7%A4%BAhdfs%E5%AF%B9%E5%BA%94%E8%B7%AF%E5%BE%84%E4%B8%8B%E6%AF%8F%E4%B8%AA%E6%96%87%E4%BB%B6%E5%A4%B9%E5%92%8C%E6%96%87%E4%BB%B6%E7%9A%84%E5%A4%A7%E5%B0%8F%EF%BC%88-du%EF%BC%89">11、显示hdfs对应路径下每个文件夹和文件的大小（-du）</h2>

<p>hadoop fs -du &lt; hdsf path&gt; <br>
显示hdfs对应路径下每个文件夹和文件的大小</p>

<p>hadoop fs -du - h &lt; hdsf path&gt; <br>
显示hdfs对应路径下每个文件夹和文件的大小,文件的大小用方便阅读的形式表示，例如用64M代替67108864（相比上面做了单位处理，便于阅读）</p>

<p>hadoop fs -du -s &lt; hdsf path&gt; <br>
显示hdfs对应路径下所有文件和的大小（显示一行结果，汇总所有的文件大小）</p>

<h2 id="12%E3%80%81%E6%94%B9%E5%8F%98%E4%B8%80%E4%B8%AA%E6%96%87%E4%BB%B6%E5%9C%A8hdfs%E4%B8%AD%E7%9A%84%E5%89%AF%E6%9C%AC%E4%B8%AA%E6%95%B0%EF%BC%88-setrep%EF%BC%89">12、改变一个文件在hdfs中的副本个数（-setrep）</h2>

<p>hadoop fs -setrep -R 3 &lt; hdfs path &gt;<br>
改变一个文件在hdfs中的副本个数，上述命令中数字3为所设置的副本个数，-R选项可以对一个人目录下的所有目录+文件递归执行改变副本个数的操作</p>

<h2 id="13%E3%80%81%E6%9F%A5%E7%9C%8BHDFS%E6%9F%90%E4%B8%AA%E6%96%87%E4%BB%B6%E7%9A%84%E7%8A%B6%E6%80%81%EF%BC%88-stat%EF%BC%89">13、查看HDFS某个文件的状态（-stat）</h2>

<p>hdoop fs -stat [format] &lt; hdfs path file&gt;<br>
返回对应路径的状态信息<br>
[format]可选参数有：%b（文件大小），%o（Block大小），%n（文件名），%r（副本个数），%y（最后一次修改日期和时间）<br>
可以这样书写hadoop fs -stat %b%o%n &lt; hdfs path file&gt;，不过不建议，这样每个字符输出的结果不是太容易分清楚<br>
hadoop fs -stat %b &lt; hdfs path file&gt;<br>
hadoop fs -stat %o &lt; hdfs path file&gt;<br>
hadoop fs -stat %n &lt; hdfs path file&gt;</p>

<h2 id="14%E3%80%81%E6%98%BE%E7%A4%BAHDFS%E7%9A%84%E6%96%87%E4%BB%B6%E5%B0%BE%E9%83%A8%EF%BC%88-tail%EF%BC%89"><br>
14、显示HDFS的文件尾部（-tail）</h2>

<p>hadoop fs -tail &lt; hdfs file &gt;<br>
在标准输出中显示文件末尾的1KB数据</p>

<p><br>
参考：<br>
https://segmentfault.com/a/1190000002672666</p>

<p><br>
 </p>            </div>
                </div>