---
layout:     post
title:      Kafka Manager部署和使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎转载，转载请注明出处。					https://blog.csdn.net/x541211190/article/details/81191608				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="一前言">一.前言：</h2>

<blockquote>
  <p>新版kafka监控平台的哪家强?对比kafka Manager和kafka Monitor 之后，最终还是选择了Kafka Manager。</p>
</blockquote>

<p>因为kafka Manager支持对Kafka的主题的增删改查操作，和消费者对数据的消费情况。而kafka Monitor对kafka新版(1.0版后),支持不太好，比如在Kafka1.1版中，用代码创建的Topic，地址没有直接绑定zookeeper，而是通过的<code>bootstrap.server</code>指定的Broker地址，间接绑定到zookeeper，在Kafka Monitor中却查看不到这些Topic，果断放弃了使用kafka Monitor。</p>



<h2 id="二kafka-manager在centos上部署">二.Kafka Manager在Centos上部署</h2>



<h3 id="1下载">1.下载</h3>



<h4 id="①选择版本">①选择版本</h4>

<p>截止到2018年8月，最新版为<code>1.3.3.18</code>。获取下载地址：</p>

<p><img src="http://ww1.sinaimg.cn/large/006dWb6Yly1ftl7o6gzalj30ma0glt9j.jpg" alt="" title=""></p>

<p>在centos中的<code>usr/local</code>下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">weget https://github<span class="hljs-preprocessor">.com</span>/yahoo/kafka-manager/archive/<span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.18</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>



<h4 id="②下载完后解压">②下载完后解压：</h4>



<pre class="prettyprint"><code class=" hljs avrasm">tar -zxfv <span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.18</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>



<h3 id="2编译">2.编译</h3>



<h4 id="①修改镜像提高下载速度">①修改镜像，提高下载速度：</h4>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> ~
mkdir .sbt
vim repositories</code></pre>

<p>按<code>i</code>,并将下面的镜像地址拷贝进去</p>



<pre class="prettyprint"><code class=" hljs markdown">[repositories]
local
aliyun: http://maven.aliyun.com/nexus/content/groups/public
typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [<span class="hljs-link_label">organization</span>]/[<span class="hljs-link_label">module</span>]/(scala<span class="hljs-emphasis">_[scalaVersion]/)(sbt_</span>[<span class="hljs-link_label">sbtVersion</span>]/)[<span class="hljs-link_label">revision</span>]/[<span class="hljs-link_label">type</span>]s/[<span class="hljs-link_label">artifact</span>](<span class="hljs-link_url">-[classifier]</span>).[ext], bootOnly</code></pre>

<p>然后按<code>Esc</code>,输入:<code>wq repositories</code></p>



<h4 id="②编译">②编译</h4>

<p>对下载下来的代码进行编译，需借助Play框架。</p>

<p>进入刚才解压后的目录<code>kafka-manager-1.3.3.18</code>,然后执行命令：</p>



<pre class="prettyprint"><code class=" hljs ">./sbt clean dist</code></pre>

<p>注：若出现<code>sbt</code>的错误，可能是没有安装sbt,需要安装一下：</p>



<pre class="prettyprint"><code class=" hljs cmake">yum <span class="hljs-keyword">install</span> sbt</code></pre>

<p>然后重新执行</p>



<pre class="prettyprint"><code class=" hljs ">./sbt clean dist</code></pre>

<p>在编译的过程中，日志中有<code>warn</code>可以，但不要有<code>error</code>。有错误则检查解决。</p>



<h3 id="3运行">3.运行</h3>

<p>将编译好的Kafka Manager，运行起来。</p>



<h4 id="①解压">①解压</h4>

<p>创建目录：</p>

<p>在taget目录中运行会出现jar包混淆的错误，所以尽量拷贝到/user/local/下，这样不受影响。</p>



<pre class="prettyprint"><code class=" hljs perl">cd /user/<span class="hljs-keyword">local</span>/

<span class="hljs-keyword">mkdir</span> kafkaManager</code></pre>

<p>进入编译目录<code>/usr/local/kafka-manager-1.3.3.18/target/universal</code>，将<code>kafka-manager-1.3.3.18.zip</code> 拷贝到刚创建的文件夹中： <code>/user/local/kafkaManager</code>：</p>



<pre class="prettyprint"><code class=" hljs lasso">cd /user/<span class="hljs-built_in">local</span>/kafkaManager
mv /usr/<span class="hljs-built_in">local</span>/kafka<span class="hljs-attribute">-manager</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.18</span>/target/universal/kafka<span class="hljs-attribute">-manager</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.18</span><span class="hljs-built_in">.</span>zip   /user/<span class="hljs-built_in">local</span>/kafkaManager</code></pre>

<p>然后解压<code>.zip</code>文件：</p>



<pre class="prettyprint"><code class=" hljs avrasm">unzip kafka-manager-<span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.18</span><span class="hljs-preprocessor">.zip</span></code></pre>

<p>若提示没有<code>unzip</code>命令，安装一下：<code>yum install unzip</code></p>



<h4 id="②配置zookeeper地址">②配置zookeeper地址</h4>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> ./kafka-manager-<span class="hljs-number">1.3</span>.<span class="hljs-number">3.18</span>/conf

vim application.conf</code></pre>

<p>将<code>kafka-manager.zkhosts="localhost:2181"</code>此行中的localhost,改为你的Zookeeper服务器的IP地址，保存退出即可。</p>



<h4 id="③启动">③启动</h4>

<p>进入bin 文件夹，然后运行</p>



<pre class="prettyprint"><code class=" hljs lasso">cd /usr/<span class="hljs-built_in">local</span>/kafkaManager/kafka<span class="hljs-attribute">-manager</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.18</span>/bin
nohup <span class="hljs-built_in">.</span>/kafka<span class="hljs-attribute">-manager</span> <span class="hljs-subst">&amp;</span></code></pre>

<p>解释：这样运行会让kafkaManager在后台运行了，如果想查看运行日志,看看有没有报错，可以执行：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /usr/local
tail -<span class="hljs-number">100</span> nohup.out</code></pre>



<h2 id="三使用">三.使用</h2>



<h3 id="1启动">1.启动</h3>

<p>成功运行后，通过访问kafka Manager所在服务器的IP:9000端口,即可使用了。</p>

<p>如：10.10.10.10:9000</p>

<blockquote>
  <p>一定是kafka Manager所在服务器IP地址，不是上面配置文件中的ZK的IP地址。端口默认是9000。</p>
</blockquote>

<p>如果想修改运行端口，先杀死上面的进程或者删除<code>/usr/local/kafkaManager/kafka-manager-1.3.3.18/RUNNING_PID</code>文件后，执行：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/kafka<span class="hljs-attribute">-manager</span> <span class="hljs-attribute">-Dconfig</span><span class="hljs-built_in">.</span>file<span class="hljs-subst">=</span>/path/<span class="hljs-keyword">to</span>/application<span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-Dhttp</span><span class="hljs-built_in">.</span>port<span class="hljs-subst">=</span><span class="hljs-number">9001</span></code></pre>



<h3 id="2配置cluster">2.配置cluster</h3>

<p>创建一个cluster：</p>

<p><img src="http://ww1.sinaimg.cn/large/006dWb6Yly1ftl7o6f5qlj30b303kq2x.jpg" alt="" title=""></p>

<p>将<code>Cluster Zookeeper Hosts</code>地址配置为Zookeeper地址，和配置文件中的地址保持一致，全部打钩，保存。</p>

<p><img src="http://ww1.sinaimg.cn/large/006dWb6Yly1ftl7o6g39vj30pw0lc759.jpg" alt="" title=""></p>

<p>查看topic详情和消费信息: <br>
<img src="http://ww1.sinaimg.cn/large/006dWb6Yly1ftl7o6jhwzj31fn0ouabr.jpg" alt="image" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>