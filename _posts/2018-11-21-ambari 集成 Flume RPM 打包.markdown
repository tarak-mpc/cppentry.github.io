---
layout:     post
title:      ambari 集成 Flume RPM 打包
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="ambari-集成-flume-rpm-打包">ambari 集成 Flume RPM 打包</h1>

<p>最近有一个新的工作，因为公司服务器过多，为了保证flume安装版本位置配置一致并且安装方便 决定使用PRM打包的方式来完成flume的安装配置工作：</p>

<h3 id="目录">目录</h3>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#ambari-%E9%9B%86%E6%88%90-flume-rpm-%E6%89%93%E5%8C%85" rel="nofollow">ambari 集成 Flume RPM 打包</a><ul>
<li><ul>
<li><a href="#%E7%9B%AE%E5%BD%95" rel="nofollow">目录</a></li>
</ul>
</li>
<li><a href="#ambari-%E9%9B%86%E6%88%90-flume" rel="nofollow">ambari  集成  flume</a></li>
<li><a href="#rpm-%E6%89%93%E5%8C%85" rel="nofollow">RPM 打包</a><ul>
<li><a href="#linux-%E7%8E%AF%E5%A2%83" rel="nofollow">linux 环境</a></li>
<li><a href="#%E5%8E%9F%E7%90%86" rel="nofollow">原理</a></li>
<li><a href="#spec-%E8%A7%84%E8%8C%83%E5%86%99%E6%B3%95" rel="nofollow">SPEC 规范写法</a></li>
<li><a href="#%E6%9C%80%E7%BB%88%E7%94%9F%E6%88%90%E7%9A%84-flumespec" rel="nofollow">最终生成的  flume.spec</a></li>
</ul>
</li>
<li><a href="#rpm-%E5%AE%89%E8%A3%85%E6%89%93%E5%8C%85" rel="nofollow">RPM 安装打包</a></li>
</ul>
</li>
</ul>
</div>
</div>


<hr>



<h2 id="ambari-集成-flume">ambari  集成  flume</h2>

<ul>
<li>ambari     版本   2.6.0</li>
<li>Hadoop   版本   2.7.3</li>
<li>flume       版本   1.7.0</li>
</ul>



<h2 id="rpm-打包">RPM 打包</h2>

<blockquote>
  <p>RPM（Redhat Package Manager）是用于Redhat、CentOS、Fedora等Linux 分发版（distribution）的常见的软件包管理器。因为它允许分发已编译的软件，所以用户只用一个命令就可以安装软件。yum安装软件，优点是全自动化安装</p>
</blockquote>



<h3 id="linux-环境">linux 环境</h3>

<p>首先请准备一个Linux环境，比如CentOS。  <br>
RPM打包使用的是rpmbuild命令，这个命令来自rpm-build包，这个是必装的。当然也可以直接安装rpmdevtools，这个工具还包含一些其他的工具，同时它依赖rpm-build，所以直接安装的话会同时把rpm-build装上。</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>yum install rpm-build
<span class="hljs-variable">$ </span>yum install rpmdevtools</code></pre>

<p>当然，根据不同的软件构建过程，还需要其他的编译打包工具，比如C语言的make、gcc，python的setuptools等，根据需要安装即可。</p>



<h3 id="原理">原理</h3>

<p>RPM打包的时候需要编译源码，还需要把编译好的配置文件啊二进制命令文件啊之类的东西按照安装好的样子放到合适的位置，还要根据需要对RPM的包进行测试，这些都需要先有一个“工作空间”。rpmbuild命令使用一套标准化的“工作空间”：</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>rpmdev-setuptree</code></pre>

<p>rpmdev-setuptree这个命令就是安装rpmdevtools带来的。可以看到运行了这个命令之后，在$HOME家目录下多了一个叫做rpmbuild的文件夹，里边内容如下：</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>tree rpmbuild
rpmbuild
├── <span class="hljs-constant">BUILD</span>                   <span class="hljs-comment">#源码包被解压至此，并在该目录的子目录完成编译</span>
├── <span class="hljs-constant">BUILDROOT</span>               <span class="hljs-comment">#保存 %install 阶段安装的文件</span>
├── <span class="hljs-constant">RPMS</span>                    <span class="hljs-comment">#生成/保存二进制 RPM 包</span>
├── <span class="hljs-constant">SOURCES</span>                 <span class="hljs-comment">#保存源码包（如 .tar 包）和所有 patch 补丁</span>
├── <span class="hljs-constant">SPECS</span>                   <span class="hljs-comment">#保存 RPM 包配置（.spec）文件</span>
└── <span class="hljs-constant">SRPMS</span>                   <span class="hljs-comment">#生成/保存源码 RPM 包(SRPM)</span></code></pre>

<p>如果没有安装rpmdevtools的话，其实用mkdir命令创建这些文件夹也是可以的。</p>



<pre class="prettyprint"><code class=" hljs ruby">mkdir -p ~<span class="hljs-regexp">/rpmbuild/</span>{<span class="hljs-constant">BUILD</span>,<span class="hljs-constant">RPMS</span>,<span class="hljs-constant">SOURCES</span>,<span class="hljs-constant">SPECS</span>,<span class="hljs-constant">SRPMS</span>}</code></pre>

<p>从这些文件的名字大体也能看得出来都是干嘛用的。具体来说：</p>



<h3 id="spec-规范写法">SPEC 规范写法</h3>

<p>SPEC文件：</p>



<pre class="prettyprint"><code class="language-shell hljs perl"><span class="hljs-comment">#用一句话概括该软件包尽量多的信息。</span>
Summary: PAM module <span class="hljs-keyword">for</span> LDAP.              
<span class="hljs-comment">#软件包的名字，最终RPM软件包是用该名字与版本号，释出号及体系号来命名软件包的。</span>
Name: pam_ldap  
<span class="hljs-comment">#软件版本号。仅当软件包比以前有较大改变时才增加版本号。</span>
Version: <span class="hljs-number">185</span> 
<span class="hljs-comment">#软件包释出号。一般我们对该软件包做了一些小的补丁的时候就应该把释出号加1。          </span>
Release: <span class="hljs-number">1</span> 
<span class="hljs-comment">#源程序软件包的名字。如 apache-flume-1.7.0-bin.tar.gz</span>
Source: <span class="hljs-variable">%{name}</span>-<span class="hljs-variable">%{version}</span>-bin.tar.gz  
<span class="hljs-comment">#软件的主页</span>
URL: http:<span class="hljs-regexp">//flume</span>.apache.org/
<span class="hljs-comment">#软件包所采用的版权规则。具体有：GPL（自由软件），BSD，MIT，Public Domain（公共域），</span>
<span class="hljs-comment">#Distributable（贡献），commercial（商业），Share（共享）等，一般的开发都写GPL。</span>
Copyright: LGPL
<span class="hljs-comment">#软件包所属类别 系统环境/基础                 </span>
Group: System Environment/Base 
<span class="hljs-comment">#这个是安装或编译时使用的“虚拟目录”，后面可使用$RPM_BUILD_ROOT 方式引用</span>
BuildRoot: <span class="hljs-variable">%{_tmppath}</span>/<span class="hljs-variable">%{name}</span>-root  
<span class="hljs-comment">#该rpm包所依赖的软件包名称，可以用&gt;=或&lt;=表示大于或小于某一特定版本         </span>
Requires: openldap cyrus-sasl openssl                               
Obsoletes: pam_ldap

<span class="hljs-variable">%description</span>                                                                          
<span class="hljs-comment">#软件包详细说明，可写在多个行上。 %开头的是rpm定义的宏命令</span>
<span class="hljs-variable">%prep</span>段
这个段是预处理段，通常用来执行一些解开源程序包的命令，为下一步的编译安装作准备。

<span class="hljs-variable">%prep</span>
<span class="hljs-variable">%setup</span> -n
setup命令用于启动名称服务与功能。系统配置工具

<span class="hljs-variable">%build</span>
本段是建立段，所要执行的命令为生成软件包服务，如make 命令。
./configure
make

<span class="hljs-variable">%install</span>段
本段是安装段，其中的命令在安装软件包时将执行，如make install命令。
[ <span class="hljs-string">"<span class="hljs-variable">$RPM_BUILD_ROOT</span>"</span> != <span class="hljs-string">"/"</span> ] &amp;&amp; rm -rf <span class="hljs-variable">$RPM_BUILD_ROOT</span>
<span class="hljs-keyword">mkdir</span> -p <span class="hljs-variable">$RPM_BUILD_ROOT</span>/{etc,lib64/security}
install -<span class="hljs-keyword">m</span> <span class="hljs-number">755</span> pam_ldap.so \
               <span class="hljs-variable">$RPM_BUILD_ROOT</span>/lib64/security/
install -<span class="hljs-keyword">m</span> <span class="hljs-number">644</span> <span class="hljs-variable">%{SOURCE1}</span> <span class="hljs-variable">$RPM_BUILD_ROOT</span>/etc/ldap.conf
<span class="hljs-keyword">chmod</span> <span class="hljs-number">755</span> <span class="hljs-variable">$RPM_BUILD_ROOT</span>/lib64/security/<span class="hljs-variable">*.</span>so*
Install和cp类似，都可以将文件/目录拷贝到指定的地点。但是，install允许你控制目标文件的属性。install通常用于程序的makefile，使用它来将程序拷贝到目标（安装）目录。
本段做的工作是将二进制文件和配置文件(pam_ldap.so, ldap.conf)，拷贝到相应的目录，并赋予相应的权限属性
<span class="hljs-variable">%files</span>段
本段是文件段，用于定义软件包所包含的文件，分为三类–说明文档（doc），配置文件（config）及执行程序，还可定义文件存取权限，拥有者及组别。

<span class="hljs-variable">%defattr</span>(-,root,root)

<span class="hljs-variable">%attr</span>(<span class="hljs-number">0755</span>,root,root) /lib64/security/<span class="hljs-variable">*.</span>so*

<span class="hljs-variable">%attr</span>(<span class="hljs-number">0644</span>,root,root) <span class="hljs-variable">%config</span>(noreplace) /etc/ldap.conf

<span class="hljs-variable">%doc</span> AUTHORS NEWS COPYING COPYING.LIB README ChangeLog pam.d

<span class="hljs-variable">%changelog</span>段
本段是修改日志段。你可以将软件的每次修改记录到这里，保存到发布的软件包中，以便查询之用
</code></pre>



<h3 id="最终生成的-flumespec">最终生成的  flume.spec</h3>

<p>SPEC文件：</p>



<pre class="prettyprint"><code class="language-shell hljs perl">Summary  : This is a  Flume  RPM <span class="hljs-keyword">package</span>.
Name     : apache-flume
Version  : <span class="hljs-number">1.7</span>.<span class="hljs-number">0</span>
Release  : <span class="hljs-number">1</span>
License  : GPL
Group    : System duomi
Source   : apache-flume-<span class="hljs-number">1.7</span>.<span class="hljs-number">0</span>-bin.tar.gz
BuildRoot: <span class="hljs-variable">%{_tmppath}</span>/<span class="hljs-variable">%{name}</span>-<span class="hljs-variable">%{version}</span>-bin
Url      : http:<span class="hljs-regexp">//www</span>.baidu.com
Packager : flume
PreReq   : pcre
Prefix:    <span class="hljs-variable">%{_prefix}</span>
Prefix:    <span class="hljs-variable">%{_sysconfdir}</span>
<span class="hljs-variable">%define</span>    userpath <span class="hljs-variable">%{_tmppath}</span>/<span class="hljs-variable">%{name}</span>-<span class="hljs-variable">%{version}</span>-bin


<span class="hljs-variable">%description</span>
This <span class="hljs-keyword">package</span> is just a nginx RPM.
<span class="hljs-variable">%prep</span>
<span class="hljs-variable">%setup</span> -n <span class="hljs-variable">%{name}</span>-<span class="hljs-variable">%{version}</span>-bin
<span class="hljs-variable">%install</span>
install -d <span class="hljs-variable">$RPM_BUILD_ROOT</span><span class="hljs-variable">%{userpath}</span>
cp -a  <span class="hljs-variable">$RPM_BUILD_DIR</span>/<span class="hljs-variable">%{name}</span>-<span class="hljs-variable">%{version}</span>-bin/*  <span class="hljs-variable">$RPM_BUILD_ROOT</span><span class="hljs-variable">%{userpath}</span>
<span class="hljs-variable">%clean</span>
rm -rf <span class="hljs-variable">$RPM_BUILD_ROOT</span>
rm -rf <span class="hljs-variable">$RPM_BUILD_DIR</span>/<span class="hljs-variable">%{name}</span>-<span class="hljs-variable">%{version}</span>-bin
<span class="hljs-variable">%files</span>
<span class="hljs-variable">%defattr</span>(-,root,root)
<span class="hljs-variable">%{userpath}</span>

<span class="hljs-variable">%post</span>
</code></pre>



<h2 id="rpm-安装打包">RPM 安装打包</h2>

<pre class="prettyprint"><code class="language-shell hljs haml">rpm(选项)(参数)

    -<span class="ruby">a：查询所有套件；
</span>    -<span class="ruby">b&lt;完成阶段&gt;&lt;套件档&gt;+或-t &lt;完成阶段&gt;&lt;套件档&gt;+：设置包装套件的完成阶段，并指定套件档的文件名称；
</span>    -<span class="ruby">c：只列出组态配置文件，本参数需配合<span class="hljs-string">"-l"</span>参数使用；
</span>    -<span class="ruby">d：只列出文本文件，本参数需配合<span class="hljs-string">"-l"</span>参数使用；
</span>    -<span class="ruby">e&lt;套件档&gt;或--erase&lt;套件档&gt;：删除指定的套件；
</span>    -<span class="ruby">f&lt;文件&gt;+：查询拥有指定文件的套件；
</span>    -<span class="ruby">h或--hash：套件安装时列出标记；
</span>    -<span class="ruby">i：显示套件的相关信息；
</span>    -<span class="ruby">i&lt;套件档&gt;或--install&lt;套件档&gt;：安装指定的套件档；
</span>    -<span class="ruby">l：显示套件的文件列表；
</span>    -<span class="ruby">p&lt;套件档&gt;+：查询指定的<span class="hljs-constant">RPM</span>套件档；
</span>    -<span class="ruby">q：使用询问模式，当遇到任何问题时，rpm指令会先询问用户；
</span>    -<span class="ruby"><span class="hljs-constant">R</span>：显示套件的关联性信息；
</span>    -<span class="ruby">s：显示文件状态，本参数需配合<span class="hljs-string">"-l"</span>参数使用；
</span>    -<span class="ruby"><span class="hljs-constant">U</span>&lt;套件档&gt;或--upgrade&lt;套件档&gt;：升级指定的套件档；
</span>    -<span class="ruby">v：显示指令执行过程；
</span>    -<span class="ruby">vv：详细显示指令执行过程，便于排错。</span></code></pre>

<p>进入到RPMS下，出现一个安装打包好的rpm文件 <br>
apache-flume-1.7.0-1.x86_64.rpm <br>
1  查看安装包里面有什么东西</p>

<pre class="prettyprint"><code class="language-shell hljs lasso">rpm <span class="hljs-attribute">-qlp</span> apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-subst">-</span><span class="hljs-number">1.</span>x86_64<span class="hljs-built_in">.</span>rpm </code></pre>

<p>2  查看数据</p>

<pre class="prettyprint"><code class="language-shell hljs lasso">rpm <span class="hljs-attribute">-qpi</span> apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-subst">-</span><span class="hljs-number">1.</span>x86_64<span class="hljs-built_in">.</span>rpm </code></pre>

<p>3  安装RPM</p>

<pre class="prettyprint"><code class="language-shell hljs lasso">rpm <span class="hljs-attribute">-ivh</span> apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-subst">-</span><span class="hljs-number">1.</span>x86_64<span class="hljs-built_in">.</span>rpm </code></pre>

<p>4   查看安装的文件和文件夹</p>



<pre class="prettyprint"><code class="language-shell hljs lasso">cd /<span class="hljs-built_in">var</span>/tmp/apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span></code></pre>

<p>完成</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>