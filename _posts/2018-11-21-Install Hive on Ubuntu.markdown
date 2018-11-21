---
layout:     post
title:      Install Hive on Ubuntu
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>This post is about the <span class="keyword">installation</span> of Hive on Ubuntu. The environment of my system is ubuntu12.04, hadoop1.0.3, hive 0.9.0. Supposing the hadoop is installed.</p>
<p>The details of installation for hive describe as follows:</p>
<p><strong>step 1:</strong> download hive-0.9.0.tar.gz from the url: <a href="http://hive.apache.org/index.html" rel="nofollow">
http://hive.apache.org/index.html</a></p>
<p><strong>step 2:</strong> uncompress the above file using linux command in the shell.</p>
<strong></strong><pre><code class="language-plain">$ sudo tar -xzf hive-0.9.0.tar.gz</code></pre>
<p><strong>step 3:</strong> copy the uncompressed files to the directory /home/hadoop/.</p>
<pre><code class="language-html">$ sudo cp -r /home/user/hive-0.9.0 /home/hadoop/</code></pre>
<p><strong>step 4:</strong> entry the configure directory of hive and copy the configure file.</p>
<strong></strong><pre><code class="language-html">$ cd /home/hadoop/hive-0.9.0/conf

$ sudo cp hive-env.sh.template hive-env.sh</code></pre>
<p><strong>step 5:</strong> modify the configure file in the last line as follows:</p>
<strong></strong><pre><code class="language-html">$ sudo gedit hive-env.sh

#add the below configure information

export Hadoop_HOME=/home/hadoop/hadoop-1.0.3

export HIVE_HOME=/home/hadoop/hive-0.9.0

export PATH=$PATH:$HIVE_HOME/bin</code></pre>
<p><strong>step 6:</strong> change the directory of hive to the hadoop user.</p>
<strong></strong><pre><code class="language-html">$ sudo chown -R hadoop:hadoop hive-0.9.0</code></pre>
<p><strong>step 7:</strong> start hive and test hive</p>
<pre><code class="language-html">$ bin/hive or bin/hive start or ./hive

$ show tables;

$ create database hive;

$ use hive;

$ create table test(id int,name string);</code></pre>
<p> </p>
            </div>
                </div>