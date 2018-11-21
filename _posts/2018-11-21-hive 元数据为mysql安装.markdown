---
layout:     post
title:      hive 元数据为mysql安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="border-width:0px;overflow:hidden;line-height:21px;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O1" style="border-width:0px;overflow:hidden;">
<pre><code class="language-html">
</code></pre></div>
<div class="O1" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
安装mysql并且将mysql链接驱动加入到hive/lib中</div>
<div class="O1" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
建立数据库hive</div>
<div class="O1" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
<br></div>
<div class="O2" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
•create database hive </div>
<div class="O1" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
•创建hive用户,并授权</div>
<div class="O2" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
•grant all on hive.* to hive@'%'  identified by 'hive';  </div>
<div class="O2" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
•flush privileges;  </div>
<div class="O" style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);border-width:0px;overflow:hidden;">
 </div>
</div>
</div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<span class="bold" style="font-weight:bold;">安装<span lang="en-us" xml:lang="en-us">Hive</span></span></div>
<div style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<div class="O1" style="border-width:0px;overflow:hidden;">
•解压Hive</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•tar zxvf  hive-0.8.1.tar  /home/test/Desktop</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•建立软连接</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•ln –s hive-0.8.1 hive</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•添加环境变量</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•export HIVE_HOME=/home/test/Desktop/hive</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•export PATH=….HIVE_HOME/bin:$PATH:.</div>
</div>
<div class="O2" style="border-width:0px;overflow:hidden;background-color:rgb(250,250,250);">
<div class="O" style="font-family:verdana, arial, helvetica, sans-serif;line-height:21px;border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">修改<span lang="en-us" xml:lang="en-us">hive-site.xml</span></span></div>
<div class="O" style="font-family:verdana, arial, helvetica, sans-serif;line-height:21px;border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us"></span></span><pre><code class="language-html">&lt;configuration&gt;
        &lt;property&gt;
                &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
                        &lt;value&gt;jdbc:mysql://192.168.0.31:3306/hive?characterEncoding=latin1&lt;/value&gt;
                            &lt;/property&gt;
                                &lt;property&gt;
                                        &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
                                                &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
                                                    &lt;/property&gt;
                                                        &lt;property&gt;
                                                                &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
                                                                        &lt;value&gt;hive&lt;/value&gt;
                                                                            &lt;/property&gt;
                                                                                &lt;property&gt;
                                                                                        &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
                                                                                                &lt;value&gt;hive&lt;/value&gt;
                                                                                                    &lt;/property&gt;
                                                                                                    &lt;/configuration&gt;</code></pre>注意添加<pre><code class="language-html"> &lt;configuration&gt;</code></pre>我配置的时候没加上挂了n次。。</div>
<div class="O" style="font-family:verdana, arial, helvetica, sans-serif;line-height:21px;border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">测试</span></span></div>
<div class="O" style="font-family:verdana, arial, helvetica, sans-serif;line-height:21px;border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us"><span style="color:rgb(51,51,51);font-family:Arial;font-size:18px;line-height:26px;">create table hivetest(id int,name string); </span></span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold"><span lang="en-us" xml:lang="en-us"><span style="font-family:Arial;font-size:18px;color:#333333;"><span style="line-height:26px;">show tables;</span></span></span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold"><span lang="en-us" xml:lang="en-us"><span style="font-family:Arial;font-size:18px;color:#333333;"><span style="line-height:26px;">完美运行！！！！！<br></span></span><br></span></span></div>
<div class="O" style="font-family:verdana, arial, helvetica, sans-serif;line-height:21px;border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us"><br></span></span></div>
</div>
            </div>
                </div>