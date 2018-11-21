---
layout:     post
title:      Hadoop基础教程-第11章 Hive：SQL on Hadoop（11.2 Hive安装与配置）（草稿）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/chengyuqiang/article/details/76757602				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="第11章-hivesql-on-hadoop">第11章 Hive：SQL on Hadoop</h2>



<h2 id="112-hive安装与配置">11.2 Hive安装与配置</h2>

<hr>



<h3 id="1121-安装元数据库">11.2.1 安装元数据库</h3>

<p>Hive的元数据和数据是分开存放的，数据存放在HDFS上，而元数据库默认是存储在Hive自带的Derby数据库。由于Derby只支持同时一个用户访问Hive，所以我们将换成MySQL/MariaDB作为Hive元数据库。</p>

<p><strong>（1）安装数据库</strong> <br>
CentOS 6.x系统可以安装MySQL，CentOS 7.x系统可以直接安装MariaDB</p>



<pre class="prettyprint"><code class=" hljs haml">[root@node3 ~]# yum install -y mariadb mariadb-server
Loaded plugins: fastestmirror
base                                                                                                                                                                  | 3.6 kB  00:00:00     
extras                                                                                                                                                                | 3.4 kB  00:00:00     
updates                                                                                                                                                               | 3.4 kB  00:00:00     
updates/7/x86_64/primary_db                                                                                                                                           | 7.8 MB  00:00:07     
Determining fastest mirrors
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
-<span class="ruby">-&gt; <span class="hljs-constant">Running</span> transaction check
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> mariadb.x86_64 <span class="hljs-number">1</span><span class="hljs-symbol">:</span><span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-<span class="hljs-number">1</span>.el7 will be installed
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> mariadb-server.x86_64 <span class="hljs-number">1</span><span class="hljs-symbol">:</span><span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-<span class="hljs-number">1</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl-<span class="hljs-constant">DBI</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> <span class="hljs-number">1</span><span class="hljs-symbol">:mariadb-server-</span><span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-<span class="hljs-number">1</span>.el7.x86_64
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl-<span class="hljs-constant">DBD</span>-<span class="hljs-constant">MySQL</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> <span class="hljs-number">1</span><span class="hljs-symbol">:mariadb-server-</span><span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-<span class="hljs-number">1</span>.el7.x86_64
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Data::Dumper</span>) <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> <span class="hljs-number">1</span><span class="hljs-symbol">:mariadb-server-</span><span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-<span class="hljs-number">1</span>.el7.x86_64
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">DBI</span>) <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> <span class="hljs-number">1</span><span class="hljs-symbol">:mariadb-server-</span><span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-<span class="hljs-number">1</span>.el7.x86_64
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Running</span> transaction check
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">DBD</span>-<span class="hljs-constant">MySQL</span>.x86_64 <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">4.023</span>-<span class="hljs-number">5</span>.el7 will be installed
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">DBI</span>.x86_64 <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">1.627</span>-<span class="hljs-number">4</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">RPC::PlServer</span>) &gt;= <span class="hljs-number">0</span>.<span class="hljs-number">2001</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">DBI</span>-<span class="hljs-number">1.627</span>-<span class="hljs-number">4</span>.el7.x86_64
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">RPC::PlClient</span>) &gt;= <span class="hljs-number">0</span>.<span class="hljs-number">2000</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">DBI</span>-<span class="hljs-number">1.627</span>-<span class="hljs-number">4</span>.el7.x86_64
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">Data</span>-<span class="hljs-constant">Dumper</span>.x86_64 <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">2.145</span>-<span class="hljs-number">3</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Running</span> transaction check
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">PlRPC</span>.noarch <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">0</span>.<span class="hljs-number">2020</span>-<span class="hljs-number">14</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Net::Daemon</span>) &gt;= <span class="hljs-number">0</span>.<span class="hljs-number">13</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">PlRPC</span>-<span class="hljs-number">0</span>.<span class="hljs-number">2020</span>-<span class="hljs-number">14</span>.el7.noarch
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Net::Daemon::Test</span>) <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">PlRPC</span>-<span class="hljs-number">0</span>.<span class="hljs-number">2020</span>-<span class="hljs-number">14</span>.el7.noarch
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Net::Daemon::Log</span>) <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">PlRPC</span>-<span class="hljs-number">0</span>.<span class="hljs-number">2020</span>-<span class="hljs-number">14</span>.el7.noarch
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Compress::Zlib</span>) <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">PlRPC</span>-<span class="hljs-number">0</span>.<span class="hljs-number">2020</span>-<span class="hljs-number">14</span>.el7.noarch
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Running</span> transaction check
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">IO</span>-<span class="hljs-constant">Compress</span>.noarch <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">2.061</span>-<span class="hljs-number">2</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Compress::Raw::Zlib</span>) &gt;= <span class="hljs-number">2.061</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">IO</span>-<span class="hljs-constant">Compress</span>-<span class="hljs-number">2.061</span>-<span class="hljs-number">2</span>.el7.noarch
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Processing</span> <span class="hljs-constant">Dependency</span><span class="hljs-symbol">:</span> perl(<span class="hljs-constant">Compress::Raw::Bzip2</span>) &gt;= <span class="hljs-number">2.061</span> <span class="hljs-keyword">for</span> <span class="hljs-symbol">package:</span> perl-<span class="hljs-constant">IO</span>-<span class="hljs-constant">Compress</span>-<span class="hljs-number">2.061</span>-<span class="hljs-number">2</span>.el7.noarch
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">Net</span>-<span class="hljs-constant">Daemon</span>.noarch <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">0</span>.<span class="hljs-number">48</span>-<span class="hljs-number">5</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Running</span> transaction check
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">Compress</span>-<span class="hljs-constant">Raw</span>-<span class="hljs-constant">Bzip2</span>.x86_64 <span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">2.061</span>-<span class="hljs-number">3</span>.el7 will be installed
</span>-<span class="ruby">--&gt; <span class="hljs-constant">Package</span> perl-<span class="hljs-constant">Compress</span>-<span class="hljs-constant">Raw</span>-<span class="hljs-constant">Zlib</span>.x86_64 <span class="hljs-number">1</span><span class="hljs-symbol">:</span><span class="hljs-number">2.061</span>-<span class="hljs-number">4</span>.el7 will be installed
</span>-<span class="ruby">-&gt; <span class="hljs-constant">Finished</span> <span class="hljs-constant">Dependency</span> <span class="hljs-constant">Resolution</span>
</span>
Dependencies Resolved

=<span class="ruby">============================================================================================================================================================================================
</span> Package                                                 Arch                                   Version                                           Repository                            Size
=<span class="ruby">============================================================================================================================================================================================
</span>Installing:
 mariadb                                                 x86_64                                 1:5.5.52-1.el7                                    base                                 8.7 M
 mariadb-server                                          x86_64                                 1:5.5.52-1.el7                                    base                                  11 M
Installing for dependencies:
 perl-Compress-Raw-Bzip2                                 x86_64                                 2.061-3.el7                                       base                                  32 k
 perl-Compress-Raw-Zlib                                  x86_64                                 1:2.061-4.el7                                     base                                  57 k
 perl-DBD-MySQL                                          x86_64                                 4.023-5.el7                                       base                                 140 k
 perl-DBI                                                x86_64                                 1.627-4.el7                                       base                                 802 k
 perl-Data-Dumper                                        x86_64                                 2.145-3.el7                                       base                                  47 k
 perl-IO-Compress                                        noarch                                 2.061-2.el7                                       base                                 260 k
 perl-Net-Daemon                                         noarch                                 0.48-5.el7                                        base                                  51 k
 perl-PlRPC                                              noarch                                 0.2020-14.el7                                     base                                  36 k

Transaction Summary
=<span class="ruby">============================================================================================================================================================================================
</span>Install  2 Packages (+8 Dependent packages)

Total download size: 21 M
Installed size: 107 M
Downloading packages:
(1/10): perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64.rpm                                                                                                                |  32 kB  00:00:00     
(2/10): perl-Compress-Raw-Zlib-2.061-4.el7.x86_64.rpm                                                                                                                 |  57 kB  00:00:00     
(3/10): perl-DBD-MySQL-4.023-5.el7.x86_64.rpm                                                                                                                         | 140 kB  00:00:00     
(4/10): perl-DBI-1.627-4.el7.x86_64.rpm                                                                                                                               | 802 kB  00:00:00     
(5/10): perl-Data-Dumper-2.145-3.el7.x86_64.rpm                                                                                                                       |  47 kB  00:00:00     
(6/10): perl-IO-Compress-2.061-2.el7.noarch.rpm                                                                                                                       | 260 kB  00:00:00     
(7/10): perl-Net-Daemon-0.48-5.el7.noarch.rpm                                                                                                                         |  51 kB  00:00:00     
(8/10): perl-PlRPC-0.2020-14.el7.noarch.rpm                                                                                                                           |  36 kB  00:00:00     
(9/10): mariadb-5.5.52-1.el7.x86_64.rpm                                                                                                                               | 8.7 MB  00:00:09     
mariadb-server-5.5.52-1.el7.x8 FAILED                                          
http://mirrors.aliyuncs.com/centos/7/os/x86_64/Packages/mariadb-server-5.5.52-1.el7.x86_64.rpm: [Errno 14] curl#7 - "Failed connect to mirrors.aliyuncs.com:80; Connection refused"00:10 ETA 
Trying other mirror.
(10/10): mariadb-server-5.5.52-1.el7.x86_64.rpm                                                                                                                       |  11 MB  00:00:11     
-<span class="ruby">--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
</span>Total                                                                                                                                                        662 kB/s |  21 MB  00:00:32     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : perl-Data-Dumper-2.145-3.el7.x86_64                                                                                                                                      1/10 
  Installing : perl-Net-Daemon-0.48-5.el7.noarch                                                                                                                                        2/10 
  Installing : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                                                                                                                              3/10 
  Installing : 1:mariadb-5.5.52-1.el7.x86_64                                                                                                                                            4/10 
  Installing : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                                                                                                                               5/10 
  Installing : perl-IO-Compress-2.061-2.el7.noarch                                                                                                                                      6/10 
  Installing : perl-PlRPC-0.2020-14.el7.noarch                                                                                                                                          7/10 
  Installing : perl-DBI-1.627-4.el7.x86_64                                                                                                                                              8/10 
  Installing : perl-DBD-MySQL-4.023-5.el7.x86_64                                                                                                                                        9/10 
  Installing : 1:mariadb-server-5.5.52-1.el7.x86_64                                                                                                                                    10/10 
  Verifying  : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                                                                                                                               1/10 
  Verifying  : 1:mariadb-5.5.52-1.el7.x86_64                                                                                                                                            2/10 
  Verifying  : perl-Data-Dumper-2.145-3.el7.x86_64                                                                                                                                      3/10 
  Verifying  : 1:mariadb-server-5.5.52-1.el7.x86_64                                                                                                                                     4/10 
  Verifying  : perl-PlRPC-0.2020-14.el7.noarch                                                                                                                                          5/10 
  Verifying  : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                                                                                                                              6/10 
  Verifying  : perl-Net-Daemon-0.48-5.el7.noarch                                                                                                                                        7/10 
  Verifying  : perl-DBI-1.627-4.el7.x86_64                                                                                                                                              8/10 
  Verifying  : perl-IO-Compress-2.061-2.el7.noarch                                                                                                                                      9/10 
  Verifying  : perl-DBD-MySQL-4.023-5.el7.x86_64                                                                                                                                       10/10 

Installed:
  mariadb.x86_64 1:5.5.52-1.el7                                                             mariadb-server.x86_64 1:5.5.52-1.el7                                                            

Dependency Installed:
  perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7        perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7        perl-DBD-MySQL.x86_64 0:4.023-5.el7        perl-DBI.x86_64 0:1.627-4.el7           
  perl-Data-Dumper.x86_64 0:2.145-3.el7               perl-IO-Compress.noarch 0:2.061-2.el7              perl-Net-Daemon.noarch 0:0.48-5.el7        perl-PlRPC.noarch 0:0.2020-14.el7       

Complete!
[root@node3 ~]# </code></pre>

<p><strong>（2）数据库配置</strong> <br>
启动MariaDB</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># systemctl start mariadb</span></code></pre>

<p>设置开机启动</p>



<pre class="prettyprint"><code class=" hljs perl">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># systemctl enable mariadb</span>
Created <span class="hljs-keyword">symlink</span> from /etc/systemd/<span class="hljs-keyword">system</span>/multi-user.target.wants/mariadb.service to /usr/lib/systemd/<span class="hljs-keyword">system</span>/mariadb.service.</code></pre>

<p>运行脚本mysql_secure_installation</p>



<pre class="prettyprint"><code class=" hljs applescript">[root@node3 ~]<span class="hljs-comment"># mysql_secure_installation</span>

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order <span class="hljs-keyword">to</span> <span class="hljs-command">log</span> <span class="hljs-keyword">into</span> MariaDB <span class="hljs-keyword">to</span> secure <span class="hljs-keyword">it</span>, we'll need <span class="hljs-keyword">the</span> current
password <span class="hljs-keyword">for</span> <span class="hljs-keyword">the</span> root user.  If you've just installed MariaDB, <span class="hljs-keyword">and</span>
you haven't <span class="hljs-keyword">set</span> <span class="hljs-keyword">the</span> root password yet, <span class="hljs-keyword">the</span> password will be blank,
so you should just press enter here.

Enter current password <span class="hljs-keyword">for</span> root (enter <span class="hljs-keyword">for</span> none): 
OK, successfully used password, moving <span class="hljs-function_start"><span class="hljs-keyword">on</span>.</span>..

Setting <span class="hljs-keyword">the</span> root password ensures <span class="hljs-keyword">that</span> nobody can <span class="hljs-command">log</span> <span class="hljs-keyword">into</span> <span class="hljs-keyword">the</span> MariaDB
root user <span class="hljs-keyword">without</span> <span class="hljs-keyword">the</span> proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
<span class="hljs-keyword">to</span> <span class="hljs-command">log</span> <span class="hljs-keyword">into</span> MariaDB <span class="hljs-keyword">without</span> having <span class="hljs-keyword">to</span> have a user account created <span class="hljs-keyword">for</span>
them.  This <span class="hljs-keyword">is</span> intended only <span class="hljs-keyword">for</span> testing, <span class="hljs-keyword">and</span> <span class="hljs-keyword">to</span> make <span class="hljs-keyword">the</span> installation
go a bit smoother.  You should remove them <span class="hljs-keyword">before</span> moving <span class="hljs-keyword">into</span> a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed <span class="hljs-keyword">to</span> connect <span class="hljs-keyword">from</span> 'localhost'.  This
ensures <span class="hljs-keyword">that</span> someone cannot guess <span class="hljs-keyword">at</span> <span class="hljs-keyword">the</span> root password <span class="hljs-keyword">from</span> <span class="hljs-keyword">the</span> network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MariaDB comes <span class="hljs-keyword">with</span> a database named 'test' <span class="hljs-keyword">that</span> anyone can
access.  This <span class="hljs-keyword">is</span> also intended only <span class="hljs-keyword">for</span> testing, <span class="hljs-keyword">and</span> should be removed
<span class="hljs-keyword">before</span> moving <span class="hljs-keyword">into</span> a production environment.

Remove test database <span class="hljs-keyword">and</span> access <span class="hljs-keyword">to</span> <span class="hljs-keyword">it</span>? [Y/n] n
 ... skipping.

Reloading <span class="hljs-keyword">the</span> privilege tables will ensure <span class="hljs-keyword">that</span> all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all <span class="hljs-keyword">of</span> <span class="hljs-keyword">the</span> <span class="hljs-keyword">above</span> steps, your MariaDB
installation should now be secure.

Thanks <span class="hljs-keyword">for</span> using MariaDB!
[root@node3 ~]<span class="hljs-comment"># </span>
</code></pre>

<p>登录MySQL，查询mysql.user表。如果存在用户名空或密码空的记录，请删除。</p>



<pre class="prettyprint"><code class=" hljs applescript">[root@node3 ~]<span class="hljs-comment"># mysql -uroot -p</span>
Enter password: 
Welcome <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> MariaDB monitor.  Commands <span class="hljs-keyword">end</span> <span class="hljs-keyword">with</span> ; <span class="hljs-keyword">or</span> \g.
Your MariaDB connection <span class="hljs-property">id</span> <span class="hljs-keyword">is</span> <span class="hljs-number">7</span>
Server <span class="hljs-property">version</span>: <span class="hljs-number">5.5</span><span class="hljs-number">.52</span>-MariaDB MariaDB Server

Copyright (c) <span class="hljs-number">2000</span>, <span class="hljs-number">2016</span>, Oracle, MariaDB Corporation Ab <span class="hljs-keyword">and</span> others.

Type 'help;' <span class="hljs-keyword">or</span> '\h' <span class="hljs-keyword">for</span> help. Type '\c' <span class="hljs-keyword">to</span> clear <span class="hljs-keyword">the</span> current input statement.

MariaDB [(none)]&gt; select Host,User,Password <span class="hljs-keyword">from</span> mysql.user;
+<span class="hljs-comment">-----------+------+-------------------------------------------+</span>
| Host      | User | Password                                  |
+<span class="hljs-comment">-----------+------+-------------------------------------------+</span>
| localhost | root | *<span class="hljs-number">6</span>BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| node3     | root | *<span class="hljs-number">6</span>BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| <span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span> | root | *<span class="hljs-number">6</span>BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| ::<span class="hljs-number">1</span>       | root | *<span class="hljs-number">6</span>BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
+<span class="hljs-comment">-----------+------+-------------------------------------------+</span>
<span class="hljs-number">4</span> rows <span class="hljs-keyword">in</span> <span class="hljs-keyword">set</span> (<span class="hljs-number">0.00</span> sec)</code></pre>

<p>授权，可以远程访问</p>



<pre class="prettyprint"><code class=" hljs nginx"><span class="hljs-title">MariaDB</span> [(<span class="hljs-built_in">none</span>)]&gt; grant all <span class="hljs-built_in">on</span> *.* to root@<span class="hljs-string">'%'</span> identified by <span class="hljs-string">'123456'</span> with grant option;
<span class="hljs-title">Query</span> OK, <span class="hljs-number">0</span> rows affected (<span class="hljs-number">0</span>.<span class="hljs-number">00</span> sec)

MariaDB [(<span class="hljs-built_in">none</span>)]&gt; flush privileges;
<span class="hljs-title">Query</span> OK, <span class="hljs-number">0</span> rows affected (<span class="hljs-number">0</span>.<span class="hljs-number">00</span> sec)

MariaDB [(<span class="hljs-built_in">none</span>)]&gt; quit
Bye
[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># </span></code></pre>



<h3 id="1122-下载hive软件包">11.2.2 下载Hive软件包</h3>

<p><a href="https://hive.apache.org/" rel="nofollow">https://hive.apache.org/</a> <br>
<img src="https://img-blog.csdn.net/20170806104234200?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>Downloads–&gt;Download a release now!</p>

<p><a href="http://www.apache.org/dyn/closer.cgi/hive/" rel="nofollow">http://www.apache.org/dyn/closer.cgi/hive/</a> <br>
<img src="https://img-blog.csdn.net/20170806104654146?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
选择stable-2 –&gt; 下载apache-hive-2.1.1-bin.tar.gz</p>

<p>由于Hive是Hadoop的一个客户端，可以独立于集群之外。由于我们的Linux虚拟机只有3台，我们将刚才下载的Hive软件包通过XShell上传到node3节点。</p>



<h3 id="1123-hive安装与配置">11.2.3 Hive安装与配置</h3>

<p><strong>（1）解压缩Hive软件包到/opt目录</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># tar -zxvf apache-hive-2.1.1-bin.tar.gz -C /opt</span></code></pre>

<p><strong>（2）为了与其他组件保持一致，重命名Hive根目录</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># cd /opt</span>
[root<span class="hljs-variable">@node3</span> opt]<span class="hljs-comment"># ls</span>
apache-hive-<span class="hljs-number">2.1</span>.<span class="hljs-number">1</span>-bin  data  hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>  hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>  jdk1.<span class="hljs-number">8.0_112</span>  zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">10</span>
[root<span class="hljs-variable">@node3</span> opt]<span class="hljs-comment"># mv apache-hive-2.1.1-bin/ hive-2.1.1</span>
[root<span class="hljs-variable">@node3</span> opt]<span class="hljs-comment"># ll</span>
total <span class="hljs-number">4</span>
drwxr-xr-x   <span class="hljs-number">3</span> root root   <span class="hljs-number">25</span> <span class="hljs-constant">Jul</span> <span class="hljs-number">12</span> <span class="hljs-number">10</span><span class="hljs-symbol">:</span><span class="hljs-number">13</span> data
drwxr-xr-x  <span class="hljs-number">10</span> root root  <span class="hljs-number">161</span> <span class="hljs-constant">May</span> <span class="hljs-number">14</span> 09<span class="hljs-symbol">:</span><span class="hljs-number">14</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>
drwxr-xr-x   <span class="hljs-number">8</span> root root  <span class="hljs-number">172</span> <span class="hljs-constant">Jul</span> <span class="hljs-number">23</span> <span class="hljs-number">10</span><span class="hljs-symbol">:</span><span class="hljs-number">37</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>
drwxr-xr-x   <span class="hljs-number">9</span> root root  <span class="hljs-number">171</span> <span class="hljs-constant">Aug</span>  <span class="hljs-number">6</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">56</span> hive-<span class="hljs-number">2.1</span>.<span class="hljs-number">1</span>
drwxr-xr-x.  <span class="hljs-number">8</span>   <span class="hljs-number">10</span>  <span class="hljs-number">143</span>  <span class="hljs-number">255</span> <span class="hljs-constant">Sep</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> jdk1.<span class="hljs-number">8.0_112</span>
drwxr-xr-x  <span class="hljs-number">10</span> root root <span class="hljs-number">4096</span> <span class="hljs-constant">Jul</span>  <span class="hljs-number">7</span> <span class="hljs-number">10</span><span class="hljs-symbol">:</span><span class="hljs-number">17</span> zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">10</span>
[root<span class="hljs-variable">@node3</span> opt]<span class="hljs-comment"># </span>
</code></pre>

<p><strong>（3）设置Hive环境变量</strong> <br>
编辑文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> opt]<span class="hljs-comment"># vi /etc/profile.d/custom.sh</span></code></pre>

<p>增加下面内容</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment">#Hive path</span>
<span class="hljs-keyword">export</span> HIVE_HOME=/opt/hive-<span class="hljs-number">2.1</span>.<span class="hljs-number">1</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HIVE_HOME</span>/bin</code></pre>

<p>source一下</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> opt]<span class="hljs-comment"># source /etc/profile.d/custom.sh</span></code></pre>

<p><strong>（4）将MySQL驱动程序复制到Hive的lib目录下面</strong></p>

<p>MySQL的JDBC驱动程序可以从MySQL官网下载 <br>
<a href="https://dev.mysql.com/downloads/connector/j/" rel="nofollow">https://dev.mysql.com/downloads/connector/j/</a> <br>
选择一个熟悉的压缩方式，单击Download按钮 <br>
<img src="https://img-blog.csdn.net/20170806161504372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
找到“No thanks, just start my download.”超链接，单击该链接即可下载。这里我们右键复制该链接的地址<a href="https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.43.tar.gz" rel="nofollow">https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.43.tar.gz</a> <br>
<img src="https://img-blog.csdn.net/20170806161445778?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>下面可以直接在node3节点直接下载JDBC驱动程序了</p>



<pre class="prettyprint"><code class=" hljs avrasm">[root@node3 ~]<span class="hljs-preprocessor"># wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.43.tar.gz</span>
--<span class="hljs-number">2017</span>-<span class="hljs-number">08</span>-<span class="hljs-number">06</span> <span class="hljs-number">04</span>:<span class="hljs-number">22</span>:<span class="hljs-number">47</span>--  https://dev<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>/get/Downloads/Connector-J/mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.43</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
Resolving dev<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span> (dev<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>)... <span class="hljs-number">137.254</span><span class="hljs-number">.60</span><span class="hljs-number">.11</span>
Connecting to dev<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span> (dev<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>)|<span class="hljs-number">137.254</span><span class="hljs-number">.60</span><span class="hljs-number">.11</span>|:<span class="hljs-number">443.</span>.. connected.
HTTP request sent, awaiting response... <span class="hljs-number">302</span> Found
<span class="hljs-label">Location:</span> https://cdn<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>//Downloads/Connector-J/mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.43</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span> [following]
--<span class="hljs-number">2017</span>-<span class="hljs-number">08</span>-<span class="hljs-number">06</span> <span class="hljs-number">04</span>:<span class="hljs-number">22</span>:<span class="hljs-number">49</span>--  https://cdn<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>//Downloads/Connector-J/mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.43</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
Resolving cdn<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span> (cdn<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>)... <span class="hljs-number">184.85</span><span class="hljs-number">.114</span><span class="hljs-number">.192</span>
Connecting to cdn<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span> (cdn<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>)|<span class="hljs-number">184.85</span><span class="hljs-number">.114</span><span class="hljs-number">.192</span>|:<span class="hljs-number">443.</span>.. connected.
HTTP request sent, awaiting response... <span class="hljs-number">200</span> OK
<span class="hljs-label">Length:</span> <span class="hljs-number">3462610</span> (<span class="hljs-number">3.3</span>M) [application/<span class="hljs-built_in">x</span>-tar-gz]
Saving to: ‘mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.43</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>’

<span class="hljs-number">100</span>%[===================================================================================================================================================&gt;] <span class="hljs-number">3</span>,<span class="hljs-number">462</span>,<span class="hljs-number">610</span>   <span class="hljs-number">19.8</span>KB/s   <span class="hljs-keyword">in</span> <span class="hljs-number">2</span>m <span class="hljs-number">39</span>s 

<span class="hljs-number">2017</span>-<span class="hljs-number">08</span>-<span class="hljs-number">06</span> <span class="hljs-number">04</span>:<span class="hljs-number">25</span>:<span class="hljs-number">29</span> (<span class="hljs-number">21.2</span> KB/s) - ‘mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.43</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>’ saved [<span class="hljs-number">3462610</span>/<span class="hljs-number">3462610</span>]

[root@node3 ~]<span class="hljs-preprocessor">#</span></code></pre>

<p>然后解压缩</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># tar -zxvf mysql-connector-java-5.1.43.tar.gz</span>
[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># cd mysql-connector-java-5.1.43</span>
[root<span class="hljs-variable">@node3</span> mysql-connector-java-<span class="hljs-number">5.1</span>.<span class="hljs-number">43</span>]<span class="hljs-comment"># ls</span>
build.xml  <span class="hljs-constant">CHANGES</span>  <span class="hljs-constant">COPYING</span>  mysql-connector-java-<span class="hljs-number">5.1</span>.<span class="hljs-number">43</span>-bin.jar  <span class="hljs-constant">README</span>  <span class="hljs-constant">README</span>.txt  src</code></pre>

<p>其中，mysql-connector-java-5.1.43-bin.jar文件就是我们需要jar包，直接将该jar移动到hive的lib目录下即可。</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> mysql-connector-java-<span class="hljs-number">5.1</span>.<span class="hljs-number">43</span>]<span class="hljs-comment"># mv mysql-connector-java-5.1.43-bin.jar /opt/hive-2.1.1/lib/</span></code></pre>

<p><strong>（5）编辑配置文件</strong></p>



<pre class="prettyprint"><code class=" hljs xml">[root@node3 ~]# cd /opt/hive-2.1.1/conf/
[root@node3 conf]# vi hive-site.xml
[root@node3 conf]# cat hive-site.xml 
<span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.local<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-comment">&lt;!--本地存储，MySQL和hive安装在同一节点--&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://node3:3306/hive?createDatabaseIfNotExist=true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>      
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>          
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>        
          <span class="hljs-comment">&lt;!--mysql数据库用户名--&gt;</span>            
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>root<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>123456<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
[root@node3 conf]# 
</code></pre>

<p><strong>（6）元数据初始化</strong> <br>
从官方文档<a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/GettingStarted</a>可知”Starting from Hive 2.1, we need to run the schematool command below as an initialization step.”，也就是说从Hive 2.1开始，我们需要在下面运行schematool命令作为初始化步骤。</p>



<pre class="prettyprint"><code class=" hljs avrasm">[root@node3 ~]<span class="hljs-preprocessor"># schematool -dbType mysql -initSchema</span>
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/opt/hive-<span class="hljs-number">2.1</span><span class="hljs-number">.1</span>/lib/log4j-slf4j-impl-<span class="hljs-number">2.4</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/opt/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.10</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.logging</span><span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
Metastore connection URL:    jdbc:mysql://node3:<span class="hljs-number">3306</span>/hive?createDatabaseIfNotExist=true
Metastore Connection Driver :    <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.jdbc</span><span class="hljs-preprocessor">.Driver</span>
Metastore connection User:   root
Starting metastore schema initialization to <span class="hljs-number">2.1</span><span class="hljs-number">.0</span>
Initialization script hive-schema-<span class="hljs-number">2.1</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.sql</span>
Initialization script completed
schemaTool completed
[root@node3 ~]<span class="hljs-preprocessor"># </span></code></pre>

<p>登录MySQL/MariaDB即可看到生成的hive数据库</p>



<pre class="prettyprint"><code class=" hljs 1c">[root@node3 ~]<span class="hljs-preprocessor"># mysql -uroot -p</span>
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is <span class="hljs-number">11</span>
Server version: <span class="hljs-number">5.5</span>.<span class="hljs-number">52</span>-MariaDB MariaDB Server

Copyright (c) <span class="hljs-number">2000</span>, <span class="hljs-number">2016</span>, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]&gt; use hive;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [hive]&gt; show tables;
+---------------------------+
<span class="hljs-string">| Tables_in_hive            |</span>
+---------------------------+
<span class="hljs-string">| AUX_TABLE                 |</span>
<span class="hljs-string">| BUCKETING_COLS            |</span>
<span class="hljs-string">| CDS                       |</span>
<span class="hljs-string">| COLUMNS_V2                |</span>
<span class="hljs-string">| COMPACTION_QUEUE          |</span>
<span class="hljs-string">| COMPLETED_COMPACTIONS     |</span>
<span class="hljs-string">| COMPLETED_TXN_COMPONENTS  |</span>
<span class="hljs-string">| DATABASE_PARAMS           |</span>
<span class="hljs-string">| DBS                       |</span>
<span class="hljs-string">| DB_PRIVS                  |</span>
<span class="hljs-string">| DELEGATION_TOKENS         |</span>
<span class="hljs-string">| FUNCS                     |</span>
<span class="hljs-string">| FUNC_RU                   |</span>
<span class="hljs-string">| GLOBAL_PRIVS              |</span>
<span class="hljs-string">| HIVE_LOCKS                |</span>
<span class="hljs-string">| IDXS                      |</span>
<span class="hljs-string">| INDEX_PARAMS              |</span>
<span class="hljs-string">| KEY_CONSTRAINTS           |</span>
<span class="hljs-string">| MASTER_KEYS               |</span>
<span class="hljs-string">| NEXT_COMPACTION_QUEUE_ID  |</span>
<span class="hljs-string">| NEXT_LOCK_ID              |</span>
<span class="hljs-string">| NEXT_TXN_ID               |</span>
<span class="hljs-string">| NOTIFICATION_LOG          |</span>
<span class="hljs-string">| NOTIFICATION_SEQUENCE     |</span>
<span class="hljs-string">| NUCLEUS_TABLES            |</span>
<span class="hljs-string">| PARTITIONS                |</span>
<span class="hljs-string">| PARTITION_EVENTS          |</span>
<span class="hljs-string">| PARTITION_KEYS            |</span>
<span class="hljs-string">| PARTITION_KEY_VALS        |</span>
<span class="hljs-string">| PARTITION_PARAMS          |</span>
<span class="hljs-string">| PART_COL_PRIVS            |</span>
<span class="hljs-string">| PART_COL_STATS            |</span>
<span class="hljs-string">| PART_PRIVS                |</span>
<span class="hljs-string">| ROLES                     |</span>
<span class="hljs-string">| ROLE_MAP                  |</span>
<span class="hljs-string">| SDS                       |</span>
<span class="hljs-string">| SD_PARAMS                 |</span>
<span class="hljs-string">| SEQUENCE_TABLE            |</span>
<span class="hljs-string">| SERDES                    |</span>
<span class="hljs-string">| SERDE_PARAMS              |</span>
<span class="hljs-string">| SKEWED_COL_NAMES          |</span>
<span class="hljs-string">| SKEWED_COL_VALUE_LOC_MAP  |</span>
<span class="hljs-string">| SKEWED_STRING_LIST        |</span>
<span class="hljs-string">| SKEWED_STRING_LIST_VALUES |</span>
<span class="hljs-string">| SKEWED_VALUES             |</span>
<span class="hljs-string">| SORT_COLS                 |</span>
<span class="hljs-string">| TABLE_PARAMS              |</span>
<span class="hljs-string">| TAB_COL_STATS             |</span>
<span class="hljs-string">| TBLS                      |</span>
<span class="hljs-string">| TBL_COL_PRIVS             |</span>
<span class="hljs-string">| TBL_PRIVS                 |</span>
<span class="hljs-string">| TXNS                      |</span>
<span class="hljs-string">| TXN_COMPONENTS            |</span>
<span class="hljs-string">| TYPES                     |</span>
<span class="hljs-string">| TYPE_FIELDS               |</span>
<span class="hljs-string">| VERSION                   |</span>
<span class="hljs-string">| WRITE_SET                 |</span>
+---------------------------+
<span class="hljs-number">57</span> rows in set (<span class="hljs-number">0.00</span> sec)

MariaDB [hive]&gt; </code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>