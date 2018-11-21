---
layout:     post
title:      CaffeOnSpark 安装笔记 可以正常运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhengshidao/article/details/78477906				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>CaffeOnSpark 安装环境 <br>
    平台：Ubuntu 16.04</p>

<p>对于CaffeOnSpark的安装，默认的前提是已经在机器上安装CUDA， Hadoop 和 Spark 集群，并且它们可用的。关于Hadoop和Spark的版本，官方文档有严格定义： <br>
         Hadoop 2.6   Spark  1.6    或者是： Hadoop 2.7   Spark 2.0 <br>
         另外环境需要 Java8.    Scala 2.11.7 </p>

<p>注：安装CaffeOnSpark时，对各个插件的版本有严格要求。</p>

<hr>

<p>安装Apache Maven <br>
在编译CaffeOnSpark中的MakeFile.config文件中用到了mvn命令。 <br>
a. 确保已经安装好 jdk. <br>
b. 到maven官网下载 apache-maven-3.2.5-bin.tar.gz （最新的版本可能发生maven插件冲突，3.2.5是检测可行的版本）。 <br>
c. 解压到工作区：</p>

<pre class="prettyprint"><code class=" hljs avrasm">$ tar –zxvf apache-maven-<span class="hljs-number">3.2</span><span class="hljs-number">.5</span>-bin<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span> </code></pre>

<p>d. 添加环境变量：</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>sudo vi /etc/profile
<span class="hljs-variable">$ </span>export <span class="hljs-constant">M2_HOME</span>=<span class="hljs-regexp">/[maven安装目录]/apache</span>-maven-<span class="hljs-number">3.2</span>.<span class="hljs-number">5</span>
<span class="hljs-variable">$ </span>export <span class="hljs-constant">PATH</span>=<span class="hljs-variable">$M2_HOME</span>/<span class="hljs-symbol">bin:</span><span class="hljs-variable">$PATH</span>
<span class="hljs-variable">$ </span>. /etc/profile</code></pre>

<p>e. 检测是否安装成功：</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>mvn –v</code></pre>

<hr>

<p>安装Cmake  <br>
到官网下载最新的Cmake : <a href="https://cmake.org/download/" rel="nofollow" target="_blank">https://cmake.org/download/</a>  版本无要求 <br>
下载后解压，进入目录执行：</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>./bootstrap
<span class="hljs-variable">$ </span>make –j8
<span class="hljs-variable">$ </span>sudo make install</code></pre>

<p>验证版本：</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>cmake –version</code></pre>

<hr>

<p>安装Google Protobuf 2.5.0 <br>
Protobuf 目前已经全面一直到Github  <a href="https://github.com/google/protobuf" rel="nofollow" target="_blank">https://github.com/google/protobuf</a> 版本必须是 2.5.0 最新版会出现无法解析类名冲突 <br>
下载安装包后解压到目录，进入目录下：</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>./configure
<span class="hljs-variable">$ </span>make
<span class="hljs-variable">$ </span>make check
<span class="hljs-variable">$ </span>sudo make install</code></pre>

<p>验证版本：</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>protoc –version</code></pre>

<p>注意：由于版本库的问题，你的OpenCV可能找不到对应库：</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ sudo vi /etc/<span class="hljs-keyword">ld</span><span class="hljs-preprocessor">.so</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.d</span></code></pre>

<p>在里面添加： </p>



<pre class="prettyprint"><code class=" hljs bash">/usr/local/lib
$ <span class="hljs-built_in">sudo</span> ldconfig    <span class="hljs-comment">#使配置生效</span></code></pre>

<hr>

<p>安装OpenCV <br>
首先安装依赖库：</p>

<pre class="prettyprint"><code class=" hljs lasso">$ sudo apt<span class="hljs-attribute">-get</span> install build<span class="hljs-attribute">-essential</span> libgtk2<span class="hljs-number">.0</span><span class="hljs-attribute">-dev</span> libavcodec<span class="hljs-attribute">-dev</span> <span class="hljs-subst">\</span>
 libavformat<span class="hljs-attribute">-dev</span> libjpeg<span class="hljs-built_in">.</span>dev libtiff4<span class="hljs-built_in">.</span>dev libswscale<span class="hljs-attribute">-dev</span> libjasper<span class="hljs-attribute">-dev</span></code></pre>

<p>从官网下载 OpenCV 注意：CUDA 8.0之后的版本会与OpenCV冲突，所以只能选择2.4 , 本文选择的是17.10推出的小版本2.4.13。 <br>
解压OpenCV并进入目录：</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>cmake .  （注意空格和点）
<span class="hljs-variable">$ </span>sudo make 
<span class="hljs-variable">$ </span>sudo make install</code></pre>

<p>注意：由于版本库的问题，你的OpenCV可能找不到对应库：</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ sudo vi /etc/<span class="hljs-keyword">ld</span><span class="hljs-preprocessor">.so</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.d</span></code></pre>

<p>在里面添加：</p>



<pre class="prettyprint"><code class=" hljs avrasm"> /usr/local/lib
$ sudo ldconfig    <span class="hljs-preprocessor">#使配置生效</span>
（参考：http://www<span class="hljs-preprocessor">.mobibrw</span><span class="hljs-preprocessor">.com</span>/<span class="hljs-number">2017</span>/<span class="hljs-number">7393</span>    
  http://m<span class="hljs-preprocessor">.blog</span><span class="hljs-preprocessor">.csdn</span><span class="hljs-preprocessor">.net</span>/supe_king/article/details/<span class="hljs-number">73882528</span> ）</code></pre>

<p>验证版本：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">pkg</span><span class="hljs-literal">-</span><span class="hljs-comment">config</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">modversion</span> <span class="hljs-comment">opencv</span>  </code></pre>

<hr>

<p>安装Caffe <br>
更新源：</p>

<pre class="prettyprint"><code class=" hljs bash">$ <span class="hljs-built_in">sudo</span> apt-get update</code></pre>

<p>下载Caffe：</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>git clone <span class="hljs-symbol">https:</span>/<span class="hljs-regexp">/github.com/bvlc</span><span class="hljs-regexp">/caffe.git</span></code></pre>

<p>依赖包：</p>



<pre class="prettyprint"><code class=" hljs lasso">$       sudo apt<span class="hljs-attribute">-get</span> install libprotobuf<span class="hljs-attribute">-dev</span> protobuf<span class="hljs-attribute">-compiler</span> $   sudo apt<span class="hljs-attribute">-get</span> install libleveldb<span class="hljs-attribute">-dev</span> $   sudo apt<span class="hljs-attribute">-get</span> install libsnappy<span class="hljs-attribute">-dev</span>
$   sudo apt<span class="hljs-attribute">-get</span> install libopencv<span class="hljs-attribute">-dev</span>
$       sudo apt<span class="hljs-attribute">-get</span> install libhdf5<span class="hljs-attribute">-serial</span><span class="hljs-attribute">-dev</span>
$       sudo apt<span class="hljs-attribute">-get</span> install <span class="hljs-subst">--</span>no<span class="hljs-attribute">-install</span><span class="hljs-attribute">-recommends</span> libboost<span class="hljs-attribute">-all</span><span class="hljs-attribute">-dev</span> $   sudo apt<span class="hljs-attribute">-get</span> install libatlas<span class="hljs-attribute">-base</span><span class="hljs-attribute">-dev</span> $   sudo apt<span class="hljs-attribute">-get</span> install python<span class="hljs-attribute">-dev</span> $   sudo apt<span class="hljs-attribute">-get</span> install libgflags<span class="hljs-attribute">-dev</span> $   sudo apt<span class="hljs-attribute">-get</span> install libgoogle<span class="hljs-attribute">-glog</span><span class="hljs-attribute">-dev</span> $   sudo apt<span class="hljs-attribute">-get</span> install liblmdb<span class="hljs-attribute">-dev</span>
$   sudo apt<span class="hljs-attribute">-get</span> install libatlas<span class="hljs-attribute">-base</span><span class="hljs-attribute">-dev</span>
$   sudo apt<span class="hljs-attribute">-get</span> install <span class="hljs-subst">--</span>no<span class="hljs-attribute">-install</span><span class="hljs-attribute">-recommends</span> libboost<span class="hljs-attribute">-all</span><span class="hljs-attribute">-dev</span>
$   sudo apt<span class="hljs-attribute">-get</span> install libprotobuf<span class="hljs-attribute">-dev</span> libleveldb<span class="hljs-attribute">-dev</span> libsnappy<span class="hljs-attribute">-dev</span> libopencv<span class="hljs-attribute">-dev</span> libhdf5<span class="hljs-attribute">-serial</span><span class="hljs-attribute">-dev</span> protobuf<span class="hljs-attribute">-compiler</span></code></pre>

<p>修改 Makefile 和 Makefile.config，同时选择cpu 或者是 gpu <br>
编译 Caffe :</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>make all
<span class="hljs-variable">$ </span>make test
<span class="hljs-variable">$ </span>make runtest</code></pre>

<hr>

<p>安装CaffeOnSpark <br>
下载CaffeOnSpark:</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>sudo git clone <span class="hljs-symbol">https:</span>/<span class="hljs-regexp">/github.com/yahoo</span><span class="hljs-regexp">/CaffeOnSpark.git  --recursive</span></code></pre>

<p>把Caffe当中的Makefile和Makefile.config拷贝到Caffe-public中替换原来的。 <br>
修在在caffe-public文件夹下的Makefile.config文件：</p>



<pre class="prettyprint"><code class=" hljs ruby">添加：<span class="hljs-constant">INCLUDE_DIRS</span> +=<span class="hljs-variable">${</span><span class="hljs-constant">JAVA_HOME</span>}/<span class="hljs-keyword">include</span></code></pre>

<p>编译CaffeOnSpark:</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>make build</code></pre>

<p>这个过程会下载许多的内容，所以会持续很长时间。 <br>
在安装完成之后会明显出现Build Success，然后就可以根据官方GIT上的操作来跑数据集进行测试。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>