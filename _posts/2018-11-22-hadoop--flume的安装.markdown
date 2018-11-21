---
layout:     post
title:      hadoop--flume的安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shursulei/article/details/53997774				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、下载flume <br>
链接：<a href="http://www.apache.org/dyn/closer.lua/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz" rel="nofollow">http://www.apache.org/dyn/closer.lua/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz</a> <br>
二、安装flume <br>
1、解压并提取flume的安装包 <br>
修改flume，复制flume-env.sh.template成flume-env.sh文件 <br>
注意：在root的权限下</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-keyword">cp</span> conf/flume-env<span class="hljs-preprocessor">.sh</span><span class="hljs-preprocessor">.template</span> conf/flume-env<span class="hljs-preprocessor">.sh</span></code></pre>

<p>修改 flume-env.sh 配置文件,主要是JAVA_HOME变量设置，如下：</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># Licensed to the Apache Software Foundation (ASF) under one</span>
<span class="hljs-preprocessor"># or more contributor license agreements.  See the NOTICE file</span>
<span class="hljs-preprocessor"># distributed with this work for additional information</span>
<span class="hljs-preprocessor"># regarding copyright ownership.  The ASF licenses this file</span>
<span class="hljs-preprocessor"># to you under the Apache License, Version 2.0 (the</span>
<span class="hljs-preprocessor"># "License"); you may not use this file except in compliance</span>
<span class="hljs-preprocessor"># with the License.  You may obtain a copy of the License at</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor">#     http://www.apache.org/licenses/LICENSE-2.0</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># Unless required by applicable law or agreed to in writing, software</span>
<span class="hljs-preprocessor"># distributed under the License is distributed on an "AS IS" BASIS,</span>
<span class="hljs-preprocessor"># WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.</span>
<span class="hljs-preprocessor"># See the License for the specific language governing permissions and</span>
<span class="hljs-preprocessor"># limitations under the License.</span>

<span class="hljs-preprocessor"># If this file is placed at FLUME_CONF_DIR/flume-env.sh, it will be sourced</span>
<span class="hljs-preprocessor"># during Flume startup.</span>

<span class="hljs-preprocessor"># Enviroment variables can be set here.</span>

<span class="hljs-preprocessor"># export JAVA_HOME=/opt/jdk</span>
<span class="hljs-preprocessor"># Give Flume more memory and pre-allocate, enable remote monitoring via JMX</span>
<span class="hljs-preprocessor"># export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"</span>

<span class="hljs-preprocessor"># Let Flume write raw event data and configuration information to its log files for debugging</span>
<span class="hljs-preprocessor"># purposes. Enabling these flags is not recommended in production,</span>
<span class="hljs-preprocessor"># as it may result in logging sensitive user information or encryption secrets.</span>
<span class="hljs-preprocessor"># export JAVA_OPTS="$JAVA_OPTS -Dorg.apache.flume.log.rawdata=true -Dorg.apache.flume.log.printconfig=true "</span>

<span class="hljs-preprocessor"># Note that the Flume conf directory is always included in the classpath.</span>
<span class="hljs-preprocessor">#FLUME_CLASSPATH=""</span></code></pre>

<p>2、验证是否安装成功</p>



<pre class="prettyprint"><code class=" hljs oxygene">Java HotSpot(TM) <span class="hljs-number">64</span>-Bit Server VM warning: <span class="hljs-keyword">Using</span> incremental CMS <span class="hljs-keyword">is</span> <span class="hljs-keyword">deprecated</span> <span class="hljs-keyword">and</span> will likely be removed <span class="hljs-keyword">in</span> a <span class="hljs-keyword">future</span> release
Flume <span class="hljs-number">1.7</span>.<span class="hljs-number">0</span>
Source code repository: https:<span class="hljs-comment">//git-wip-us.apache.org/repos/asf/flume.git</span>
Revision: <span class="hljs-number">511</span>d868555dd4d16e6ce4fedc72c2d1454546707
Compiled <span class="hljs-keyword">by</span> bessbd <span class="hljs-keyword">on</span> Wed Oct <span class="hljs-number">12</span> <span class="hljs-number">20</span>:<span class="hljs-number">51</span>:<span class="hljs-number">10</span> CEST <span class="hljs-number">2016</span>
<span class="hljs-keyword">From</span> source <span class="hljs-keyword">with</span> checksum <span class="hljs-number">0</span>d21b3ffdc55a07e1d08875872c00523
</code></pre>

<p><img src="https://img-blog.csdn.net/20170103204400607?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2h1cnN1bGVp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>为了运行方便，我们把bin目录加到Path中: <br>
vim /etc/profile</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> FLUME_HOME=/home/sulei/桌面/apache-flume-<span class="hljs-number">1.7</span>.<span class="hljs-number">0</span>-bin
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$JAVA_HOME</span>/bin:<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$HADOOP_HOME</span>/sbin:<span class="hljs-variable">$HIVE_HOME</span>/bin:<span class="hljs-variable">$HBASE_HOME</span>/bin:<span class="hljs-variable">$FLUME_HOME</span>/bin
</code></pre>

<p>更新操作</p>

<blockquote>
  <p>source /etc/profile</p>
</blockquote>

<p>测试安装是否成功</p>

<blockquote>
  <p>flume-ng help</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs http"><span class="hljs-attribute">Usage</span>: <span class="hljs-string">/home/sulei/桌面/apache-flume-1.7.0-bin/bin/flume-ng &lt;command&gt; [options]...</span>

<span class="lasso">commands:
  help                      display this help text
  agent                     run a Flume agent
  avro<span class="hljs-attribute">-client</span>               run an avro Flume client
  version                   show Flume version info

<span class="hljs-built_in">global</span> options:
  <span class="hljs-subst">--</span>conf,<span class="hljs-attribute">-c</span> <span class="hljs-subst">&lt;</span>conf<span class="hljs-subst">&gt;</span>          use configs <span class="hljs-keyword">in</span> <span class="hljs-subst">&lt;</span>conf<span class="hljs-subst">&gt;</span> directory
  <span class="hljs-subst">--</span>classpath,<span class="hljs-attribute">-C</span> <span class="hljs-subst">&lt;</span>cp<span class="hljs-subst">&gt;</span>       append <span class="hljs-keyword">to</span> the classpath
  <span class="hljs-subst">--</span>dryrun,<span class="hljs-attribute">-d</span>               <span class="hljs-keyword">do</span> <span class="hljs-literal">not</span> actually start Flume, just print the command
  <span class="hljs-subst">--</span>plugins<span class="hljs-attribute">-path</span> <span class="hljs-subst">&lt;</span>dirs<span class="hljs-subst">&gt;</span>     colon<span class="hljs-attribute">-separated</span> <span class="hljs-built_in">list</span> of plugins<span class="hljs-built_in">.</span>d directories<span class="hljs-built_in">.</span> See the
                            plugins<span class="hljs-built_in">.</span>d section <span class="hljs-keyword">in</span> the user guide for more details<span class="hljs-built_in">.</span>
                            Default: <span class="hljs-variable">$FLUME_HOME</span>/plugins<span class="hljs-built_in">.</span>d
  <span class="hljs-attribute">-Dproperty</span><span class="hljs-subst">=</span>value          sets a Java system property value
  <span class="hljs-attribute">-Xproperty</span><span class="hljs-subst">=</span>value          sets a Java <span class="hljs-attribute">-X</span> option

agent options:
  <span class="hljs-subst">--</span>name,<span class="hljs-attribute">-n</span> <span class="hljs-subst">&lt;</span>name<span class="hljs-subst">&gt;</span>          the name of this agent (required)
  <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span>,<span class="hljs-attribute">-f</span> <span class="hljs-subst">&lt;</span>file<span class="hljs-subst">&gt;</span>     specify a config file (required <span class="hljs-keyword">if</span> <span class="hljs-attribute">-z</span> missing)
  <span class="hljs-subst">--</span>zkConnString,<span class="hljs-attribute">-z</span> <span class="hljs-subst">&lt;</span>str<span class="hljs-subst">&gt;</span>   specify the ZooKeeper connection <span class="hljs-keyword">to</span> use (required <span class="hljs-keyword">if</span> <span class="hljs-attribute">-f</span> missing)
  <span class="hljs-subst">--</span>zkBasePath,<span class="hljs-attribute">-p</span> <span class="hljs-subst">&lt;</span>path<span class="hljs-subst">&gt;</span>    specify the base path <span class="hljs-keyword">in</span> ZooKeeper for agent configs
  <span class="hljs-subst">--</span>no<span class="hljs-attribute">-reload</span><span class="hljs-attribute">-conf</span>          <span class="hljs-keyword">do</span> <span class="hljs-literal">not</span> reload config file <span class="hljs-keyword">if</span> changed
  <span class="hljs-subst">--</span>help,<span class="hljs-attribute">-h</span>                 display help text

avro<span class="hljs-attribute">-client</span> options:
  <span class="hljs-subst">--</span>rpcProps,<span class="hljs-attribute">-P</span> <span class="hljs-subst">&lt;</span>file<span class="hljs-subst">&gt;</span>   RPC client properties file <span class="hljs-keyword">with</span> server connection <span class="hljs-keyword">params</span>
  <span class="hljs-subst">--</span>host,<span class="hljs-attribute">-H</span> <span class="hljs-subst">&lt;</span>host<span class="hljs-subst">&gt;</span>       hostname <span class="hljs-keyword">to</span> which events will be sent
  <span class="hljs-subst">--</span>port,<span class="hljs-attribute">-p</span> <span class="hljs-subst">&lt;</span>port<span class="hljs-subst">&gt;</span>       port of the avro source
  <span class="hljs-subst">--</span>dirname <span class="hljs-subst">&lt;</span>dir<span class="hljs-subst">&gt;</span>        directory <span class="hljs-keyword">to</span> stream <span class="hljs-keyword">to</span> avro source
  <span class="hljs-subst">--</span>filename,<span class="hljs-attribute">-F</span> <span class="hljs-subst">&lt;</span>file<span class="hljs-subst">&gt;</span>   text file <span class="hljs-keyword">to</span> stream <span class="hljs-keyword">to</span> avro source (default: std input)
  <span class="hljs-subst">--</span>headerFile,<span class="hljs-attribute">-R</span> <span class="hljs-subst">&lt;</span>file<span class="hljs-subst">&gt;</span> File containing event headers as key/value pairs <span class="hljs-keyword">on</span> each <span class="hljs-literal">new</span> line
  <span class="hljs-subst">--</span>help,<span class="hljs-attribute">-h</span>              display help text

  Either <span class="hljs-subst">--</span>rpcProps <span class="hljs-literal">or</span> both <span class="hljs-subst">--</span>host <span class="hljs-literal">and</span> <span class="hljs-subst">--</span>port must be specified<span class="hljs-built_in">.</span>

Note that <span class="hljs-keyword">if</span> <span class="hljs-subst">&lt;</span>conf<span class="hljs-subst">&gt;</span> directory is specified, then it is always included first
<span class="hljs-keyword">in</span> the classpath<span class="hljs-built_in">.</span></span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>