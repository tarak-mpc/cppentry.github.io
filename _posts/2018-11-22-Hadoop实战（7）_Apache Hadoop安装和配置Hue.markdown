---
layout:     post
title:      Hadoop实战（7）_Apache Hadoop安装和配置Hue
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/padluo/article/details/78409509				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>系列目录：</p>

<p><a href="http://blog.csdn.net/padluo/article/details/78236793" rel="nofollow" target="_blank">Hadoop实战（1）_阿里云搭建Hadoop2.x的伪分布式环境</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78322222" rel="nofollow" target="_blank">Hadoop实战（2）_虚拟机搭建Hadoop的全分布模式</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78334715" rel="nofollow" target="_blank">Hadoop实战（3）_虚拟机搭建CDH的全分布模式</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78357977" rel="nofollow" target="_blank">Hadoop实战（4）_Hadoop的集群管理和资源分配</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78384445" rel="nofollow" target="_blank">Hadoop实战（5）_Hadoop的运维经验</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78391329" rel="nofollow" target="_blank">Hadoop实战（6）_搭建Apache Hadoop的Eclipse开发环境</a></p>

<p>配置Hue的主要步骤：</p>

<ul>
<li>安装所需的rpm包</li>
<li>编辑Hadoop的配置文件</li>
<li>安装Hue</li>
<li>启动Hue及验证</li>
</ul>

<h1 id="安装所需的rpm包">安装所需的rpm包</h1>



<pre class="prettyprint"><code class=" hljs cmake">yum <span class="hljs-keyword">install</span> ant
yum <span class="hljs-keyword">install</span> asciidoc
yum <span class="hljs-keyword">install</span> cyrus-sasl-devel
yum <span class="hljs-keyword">install</span> cyrus-sasl-gssapi
yum <span class="hljs-keyword">install</span> gcc
yum <span class="hljs-keyword">install</span> gcc-c++
yum <span class="hljs-keyword">install</span> krb5-devel

<span class="hljs-comment"># for unit tests only</span>
yum <span class="hljs-keyword">install</span> libtidy 

yum <span class="hljs-keyword">install</span> libxml2-devel
yum <span class="hljs-keyword">install</span> libxslt-devel
yum <span class="hljs-keyword">install</span> make
<span class="hljs-comment"># yum install mvn (from maven package or maven3 tarball)</span>
yum <span class="hljs-keyword">install</span> mysql
yum <span class="hljs-keyword">install</span> mysql-devel
yum <span class="hljs-keyword">install</span> openldap-devel
yum <span class="hljs-keyword">install</span> python-devel
yum <span class="hljs-keyword">install</span> sqlite-devel

<span class="hljs-comment"># for version 7+</span>
yum <span class="hljs-keyword">install</span> openssl-devel

yum <span class="hljs-keyword">install</span> gmp-devel</code></pre>



<h1 id="编辑hadoop的配置文件">编辑Hadoop的配置文件</h1>

<table>
<thead>
<tr>
  <th>参数文件</th>
  <th>参数</th>
  <th>参考值</th>
  <th>说明</th>
</tr>
</thead>
<tbody><tr>
  <td>hdfs-site.xml</td>
  <td>dfs.webhdfs.enabled</td>
  <td>true</td>
  <td>开启webhdfs功能</td>
</tr>
<tr>
  <td>core-site.xml</td>
  <td>hadoop.proxyuser.root.hosts</td>
  <td><code>*</code></td>
  <td>设置Hadoop集群的代理用户，<code>*</code>表示任何用户</td>
</tr>
<tr>
  <td>core-site.xml</td>
  <td>hadoop.proxyuser.root.groups</td>
  <td><code>*</code></td>
  <td>设置Hadoop集群的代理用户组</td>
</tr>
</tbody></table>


<p><strong><code>hdfs-site.xml</code></strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.webhdfs.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p><strong><code>core-site.xml</code></strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.root.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.root.groups<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h1 id="安装hue">安装Hue</h1>

<p><strong>解压hue-3.7.0-cdh5.4.2.tar.gz</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">mkdir <span class="hljs-attribute">-p</span> /root/tools
tar <span class="hljs-attribute">-zxvf</span> hue<span class="hljs-subst">-</span><span class="hljs-number">3.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.4</span><span class="hljs-number">.2</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz <span class="hljs-attribute">-C</span> ~/training<span class="hljs-subst">/</span></code></pre>

<p><strong>编译安装</strong>：注意系统时间</p>



<pre class="prettyprint"><code class=" hljs ruby">cd ~<span class="hljs-regexp">/training/hue</span>-<span class="hljs-number">3.7</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">4.2</span>
<span class="hljs-constant">PREFIX</span>=<span class="hljs-regexp">/root/training</span><span class="hljs-regexp">/ make install</span></code></pre>

<p><strong>bug解决</strong></p>



<pre class="prettyprint"><code class=" hljs applescript">/usr/include/gnu/stubs.h:<span class="hljs-number">9</span>:<span class="hljs-number">27</span>: <span class="hljs-keyword">error</span>: gnu/stubs-<span class="hljs-number">64.</span>h: No such <span class="hljs-type">file</span> <span class="hljs-keyword">or</span> direct                          ory
<span class="hljs-keyword">error</span>: command 'gcc' failed <span class="hljs-keyword">with</span> <span class="hljs-keyword">exit</span> status <span class="hljs-number">1</span>

[root@hadoop25pseudo hue-<span class="hljs-number">3.7</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.4</span><span class="hljs-number">.2</span>]<span class="hljs-comment"># rpm -qa | grep glibc*</span>
glibc-headers-<span class="hljs-number">2.12</span>-<span class="hljs-number">1.132</span>.el6.x86_64
glibc-static-<span class="hljs-number">2.12</span>-<span class="hljs-number">1.132</span>.el6.i686
glibc-common-<span class="hljs-number">2.12</span>-<span class="hljs-number">1.132</span>.el6.x86_64
glibc-<span class="hljs-number">2.12</span>-<span class="hljs-number">1.132</span>.el6.x86_64
glib2-<span class="hljs-number">2.26</span><span class="hljs-number">.1</span>-<span class="hljs-number">3.</span>el6.x86_64
dbus-glib-<span class="hljs-number">0.86</span>-<span class="hljs-number">6.</span>el6.x86_64
glibc-<span class="hljs-number">2.12</span>-<span class="hljs-number">1.132</span>.el6.i686
glibc-devel-<span class="hljs-number">2.12</span>-<span class="hljs-number">1.132</span>.el6.i686

<span class="hljs-comment"># 缺少64位的</span>
yum install -y glibc-devel</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">/bin/bash: rsync: <span class="hljs-command"><span class="hljs-keyword">command</span> <span class="hljs-title">not</span> <span class="hljs-title">found</span></span>

yum install -y rsync</code></pre>

<p><strong>添加用户hue</strong></p>



<pre class="prettyprint"><code class=" hljs perl">adduser hue
<span class="hljs-keyword">chown</span> -R hue.hue /root/training/hue/</code></pre>

<p><strong>修改hue.ini</strong>(<code>$HUE_HOME/desktop/conf/hue.ini</code>)参数文件</p>

<table>
<thead>
<tr>
  <th>参数</th>
  <th>参考值</th>
</tr>
</thead>
<tbody><tr>
  <td>http_host</td>
  <td>hadoop25pseudo</td>
</tr>
<tr>
  <td>http_port</td>
  <td>8888</td>
</tr>
<tr>
  <td>server_user</td>
  <td>root</td>
</tr>
<tr>
  <td>server_group</td>
  <td>root</td>
</tr>
<tr>
  <td>default_user</td>
  <td>root</td>
</tr>
<tr>
  <td>default_hdfs_superuser</td>
  <td>root</td>
</tr>
<tr>
  <td>fs_defaultfs</td>
  <td>hdfs://hadoop25pseudo:9000</td>
</tr>
<tr>
  <td>webhdfs_url</td>
  <td><a href="http://hadoop25pseudo:50070/webhdfs/v1" rel="nofollow" target="_blank">http://hadoop25pseudo:50070/webhdfs/v1</a></td>
</tr>
<tr>
  <td>hadoop_conf_dir</td>
  <td>/root/training/hadoop-2.4.1/etc/hadoop</td>
</tr>
<tr>
  <td>resourcemanager_host</td>
  <td>hadoop25pseudo</td>
</tr>
<tr>
  <td>resourcemanager_api_url</td>
  <td><a href="http://hadoop25pseudo:8088" rel="nofollow" target="_blank">http://hadoop25pseudo:8088</a></td>
</tr>
<tr>
  <td>proxy_api_url</td>
  <td><a href="http://hadoop25pseudo:8088" rel="nofollow" target="_blank">http://hadoop25pseudo:8088</a></td>
</tr>
<tr>
  <td>history_server_api_url</td>
  <td><a href="http://hadoop25pseudo:19888" rel="nofollow" target="_blank">http://hadoop25pseudo:19888</a></td>
</tr>
</tbody></table>




<pre class="prettyprint"><code class=" hljs avrasm">find . -name hue<span class="hljs-preprocessor">.ini</span>
<span class="hljs-preprocessor"># ./desktop/conf/hue.ini</span>
cd desktop/conf/
vi hue<span class="hljs-preprocessor">.ini</span></code></pre>



<pre class="prettyprint"><code class=" hljs ini"><span class="hljs-setting">http_host=<span class="hljs-value">hadoop25pseudo</span></span>
<span class="hljs-setting">http_port=<span class="hljs-value"><span class="hljs-number">8888</span></span></span>
<span class="hljs-setting">server_user=<span class="hljs-value">root</span></span>
<span class="hljs-setting">server_group=<span class="hljs-value">root</span></span>
<span class="hljs-setting">default_user=<span class="hljs-value">root</span></span>
<span class="hljs-setting">default_hdfs_superuser=<span class="hljs-value">root</span></span>

<span class="hljs-setting">fs_defaultfs=<span class="hljs-value">hdfs://hadoop25pseudo:<span class="hljs-number">9000</span></span></span>
<span class="hljs-setting">webhdfs_url=<span class="hljs-value">http://hadoop25pseudo:<span class="hljs-number">50070</span>/webhdfs/v1</span></span>
<span class="hljs-setting">hadoop_conf_dir=<span class="hljs-value">/root/training/hadoop-<span class="hljs-number">2.4</span>.<span class="hljs-number">1</span>/etc/hadoop</span></span>
<span class="hljs-setting">resourcemanager_host=<span class="hljs-value">hadoop25pseudo</span></span>
<span class="hljs-setting">resourcemanager_api_url=<span class="hljs-value">http://hadoop25pseudo:<span class="hljs-number">8088</span></span></span>
<span class="hljs-setting">proxy_api_url=<span class="hljs-value">http://hadoop25pseudo:<span class="hljs-number">8088</span></span></span>
<span class="hljs-setting">history_server_api_url=<span class="hljs-value">http://hadoop25pseudo:<span class="hljs-number">19888</span></span></span></code></pre>



<h1 id="启动hue及验证">启动Hue及验证</h1>

<p>启动Hadoop相关组件<code>start-all.sh</code></p>

<p>启动Hue：</p>



<pre class="prettyprint"><code class=" hljs ruby">cd ~<span class="hljs-regexp">/training/hue</span><span class="hljs-regexp">/build/env</span>
bin/supervisor</code></pre>

<p>验证，访问首页：<code>http://hadoop25pseudo:8888/</code></p>

<hr>

<p>微信公众号「数据分析」，分享数据科学家的自我修养，既然遇见，不如一起成长。 <br>
<img src="https://img-blog.csdn.net/20171031234431279?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFkbHVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="数据分析" title=""></p>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>