---
layout:     post
title:      linux+no such file or directory
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                hadoop version<br>/root/hadoop/bin/hadoop: line 169: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-3.b14.el6_9.i386/bin/java: No such file or directory<br><p>/root/hadoop/bin/hadoop: line 169: exec: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-3.b14.el6_9.i386/bin/java: cannot execute: No such file or directory</p><p>这是由于yum安装java没安装完全所致。</p><p></p><pre><code class="language-python">yum search java-1.7</code></pre><p>看到了java-1.7.0-openjdk-devel.x86_64这个吗，当然你可以serach的是java-1.8，无所谓，你开心就好，再看后面介绍：OpenJDK Development Environment，没错，我们接下来装的就是它</p><pre><code class="language-python">yum -y install java-1.7.0-openjdk-devel.x86_64</code></pre><p>安装之后在输入 hadoop version</p><pre><code class="language-python">Hadoop 2.9.0
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r 756ebc8394e473ac25feac05fa493f6d612e6c50
Compiled by arsuresh on 2017-11-13T23:15Z
Compiled with protoc 2.5.0
From source with checksum 0a76a9a32a5257331741f8d5932f183
This command was run using /root/hadoop/share/hadoop/common/hadoop-common-2.9.0.jar</code></pre><br>成功！            </div>
                </div>