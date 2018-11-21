---
layout:     post
title:      shell命令执行hive脚本（hive交互，hive的shell编程）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/longshenlmj/article/details/50542683" rel="nofollow">原文链接</a><br></p>
<p></p>
<div id="article_content" class="article_content" style="font-size:15px;color:rgb(85,85,85);line-height:35px;font-family:'microsoft yahei';">
<div class="markdown_views" style="font-size:14px;">
<h2 id="hive执行方式" style="font-weight:100;">Hive执行方式</h2>
<h3 id="hive的hql命令执行方式有三种" style="font-weight:100;">
<a name="t1" style="color:rgb(12,137,207);"></a>Hive的hql命令执行方式有三种：</h3>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">1、CLI 方式直接执行
2、作为字符串通过shell调用hive –e执行（-S开启静默，去掉”OK”，”Time taken”）
3、作为独立文件，通过shell调用 hive –f或hive –i执行执行
</code></pre>
<h4 id="方式1" style="font-weight:100;"><a name="t2" style="color:rgb(12,137,207);"></a>方式1</h4>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">键入“hive”，启动hive的cli交互模式。Set可以查看所有环境设置参数，并可以重设。其他命令如，
    Use database        选择库
    quit/exit   退出Hive的交互模式 
    set –v  显示Hive中的所有变量
    set &lt;key&gt;=&lt;value&gt;       设置参数
    执行本地shell ：!&lt;cmd&gt;       交互模式下可执行shell命令，例如（查看linux根目录下文件列表："!ls -l /;"）
    操作云命令：dfs &lt; command&gt;        交互模式下直接操作hadoop命令如 dfs fs –ls
    Hql语句       执行查询并输出到标准输出
    add [FILE|JAR|ARCHIVE] &lt;value&gt; [&lt;value&gt;]*       增加一个文件到资源列表
    list FILE       列出所有已经添加的资源
</code></pre>
<h4 id="方式二" style="font-weight:100;"><a name="t3" style="color:rgb(12,137,207);"></a>方式二</h4>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">Hql作为字符串在shell脚本中执行，如
    hive -e "use ${database};select * from tb"
查询结果可以直接导出到本地本件（默认分隔符为\t）:
    hive -e "select * from tb" &gt; tb.txt
</code></pre>
<h5 id="如果需要查看执行步骤则在命令前面添加" style="font-weight:100;">
<a name="t4" style="color:rgb(12,137,207);"></a>如果需要查看执行步骤，则在命令前面添加</h5>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">    set –x
</code></pre>
<h5 id="另外在shell脚本中字符串有两种定义方式" style="font-weight:100;">
<a name="t5" style="color:rgb(12,137,207);"></a>另外，在shell脚本中，字符串有两种定义方式:</h5>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">1)  直接定义字符串对象：sql=”字符串”

2)  通过命令定义：sql=$(cat &lt;&lt;endtag 字符串endtag)方式可以将字符串复制给sql，执行hql命令的shell脚本如下：
</code></pre>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.0470588);border:1px solid rgba(128,128,128,.0745098);">&lt;code class="language-shell hljs bash has-numbering" style="display: block; padding: 0px; background: transparent; color: inherit; box-sizing: border-box; font-family: "Source Code Pro", monospace;font-size:undefined; white-space: pre; border-radius: 0px; word-wrap: normal;"&gt;&lt;span class="hljs-comment" style="color: rgb(136, 0, 0); box-sizing: border-box;"&gt;####### execute hive ######&lt;/span&gt;
sql=$(cat &lt;&lt;!EOF

USE pmp;
&lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;set&lt;/span&gt; mapred.queue.names=queue3;

drop table &lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;if&lt;/span&gt; exists people_targeted_delivery;
create table people_targeted_delivery
( special_tag_id int,
  cnt bigint
);

INSERT OVERWRITE LOCAL DIRECTORY &lt;span class="hljs-string" style="color: rgb(0, 136, 0); box-sizing: border-box;"&gt;'$cur_path/people_targeted_delivery'&lt;/span&gt;
ROW FORMAT DELIMITED FIELDS TERMINATED BY &lt;span class="hljs-string" style="color: rgb(0, 136, 0); box-sizing: border-box;"&gt;'\t'&lt;/span&gt; 
select special_tag_id,count(&lt;span class="hljs-number" style="color: rgb(0, 102, 102); box-sizing: border-box;"&gt;1&lt;/span&gt;) 
from t_pmp_special_user_tags
group by special_tag_id;

!EOF)
&lt;span class="hljs-comment" style="color: rgb(136, 0, 0); box-sizing: border-box;"&gt;############  execute begin   ###########&lt;/span&gt;
&lt;span class="hljs-built_in" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;echo&lt;/span&gt; &lt;span class="hljs-variable" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;$sql&lt;/span&gt;
&lt;span class="hljs-variable" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;$HIVE_HOME&lt;/span&gt;/bin/hive &lt;span class="hljs-operator" style="box-sizing: border-box;"&gt;-e&lt;/span&gt; &lt;span class="hljs-string" style="color: rgb(0, 136, 0); box-sizing: border-box;"&gt;"&lt;span class="hljs-variable" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;$sql&lt;/span&gt;"&lt;/span&gt;

&lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;exit&lt;/span&gt;Code=$?
&lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;if&lt;/span&gt; [ &lt;span class="hljs-variable" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;$exitCode&lt;/span&gt; &lt;span class="hljs-operator" style="box-sizing: border-box;"&gt;-ne&lt;/span&gt; &lt;span class="hljs-number" style="color: rgb(0, 102, 102); box-sizing: border-box;"&gt;0&lt;/span&gt; ];&lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;then&lt;/span&gt;
         &lt;span class="hljs-built_in" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;echo&lt;/span&gt; &lt;span class="hljs-string" style="color: rgb(0, 136, 0); box-sizing: border-box;"&gt;"[ERROR] hive execute failed!"&lt;/span&gt;
         &lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;exit&lt;/span&gt; &lt;span class="hljs-variable" style="color: rgb(102, 0, 102); box-sizing: border-box;"&gt;$exitCode&lt;/span&gt;
&lt;span class="hljs-keyword" style="color: rgb(0, 0, 136); box-sizing: border-box;"&gt;fi&lt;/span&gt;&lt;/code&gt;&lt;ul class="pre-numbering" style="box-sizing: border-box; position: absolute; width: 50px; background-color: rgb(238, 238, 238); top: 0px; left: 0px; margin: 0px; padding: 6px 0px 40px; border-right: 1px solid rgb(221, 221, 221); list-style: none; text-align: right;"&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;1&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;2&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;3&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;4&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;5&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;6&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;7&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;8&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;9&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;10&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;11&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;12&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;13&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;14&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;15&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;16&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;17&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;18&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;19&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;20&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;21&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;22&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;23&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;24&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;25&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;26&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;27&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;28&lt;/li&gt;&lt;/ul&gt;&lt;ul class="pre-numbering" style="box-sizing: border-box; position: absolute; width: 50px; background-color: rgb(238, 238, 238); top: 0px; left: 0px; margin: 0px; padding: 6px 0px 40px; border-right: 1px solid rgb(221, 221, 221); list-style: none; text-align: right;"&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;1&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;2&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;3&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;4&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;5&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;6&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;7&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;8&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;9&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;10&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;11&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;12&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;13&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;14&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;15&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;16&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;17&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;18&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;19&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;20&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;21&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;22&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;23&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;24&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;25&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;26&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;27&lt;/li&gt;&lt;li style="box-sizing: border-box; padding: 0px 5px;"&gt;28&lt;/li&gt;&lt;/ul&gt;</pre>
<h4 id="方式三" style="font-weight:100;"><a name="t6" style="color:rgb(12,137,207);"></a>方式三</h4>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">将hql语句保存为独立文件，后缀名不限制，可以用.q或者.hql作为标识：
    A，这个文件在cli模式下，用source命令执行，如：source ./mytest.hql
    B，在shell中执行命令，如：hive -f  mytest.sql
</code></pre>
<h4 id="hive指定预执行文件命令hive-i或叫初始化文件" style="font-weight:100;">
<a name="t7" style="color:rgb(12,137,207);"></a>Hive指定预执行文件命令“hive –i”（或叫初始化文件）</h4>
<pre style="font-family:'Source Code Pro', monospace;line-height:1.45;color:rgb(51,51,51);"><code style="font-family:'Source Code Pro', monospace;color:inherit;background-color:transparent;">命令：hive -i hive-script.sql
在hive启动cli之前，先执行指定文件（hive-script.sql）中的命令。
也就是说，允许用户在cli启动时预先执行一个指定文件，比如，有一些常用的环境参数设置，频繁执行的命令，可以添加在初始化文件中，比如，
    某些参数设置
        set mapred.queue.names=queue3;
        SET mapred.reduce.tasks=14;
    添加udf文件
        add JAR ./playdata-hive-udf.jar;
    设置Hive的日志级别 
        hive -hiveconf hive.root.logger=INFO;
</code></pre>
</div>
</div>
<div class="bdsharebuttonbox tracking-ad bdshare-button-style0-16" style="font-family:'microsoft yahei';">
<a href="http://blog.csdn.net/longshenlmj/article/details/50542683#" rel="nofollow" class="bds_more" style="text-decoration:none;line-height:16px;color:rgb(51,51,51);"></a><a href="http://blog.csdn.net/longshenlmj/article/details/50542683#" rel="nofollow" class="bds_qzone" title="分享到QQ空间" style="text-decoration:none;line-height:16px;"></a><a href="http://blog.csdn.net/longshenlmj/article/details/50542683#" rel="nofollow" class="bds_tsina" title="分享到新浪微博" style="text-decoration:none;line-height:16px;"></a><a href="http://blog.csdn.net/longshenlmj/article/details/50542683#" rel="nofollow" class="bds_tqq" title="分享到腾讯微博" style="text-decoration:none;line-height:16px;"></a><a href="http://blog.csdn.net/longshenlmj/article/details/50542683#" rel="nofollow" class="bds_renren" title="分享到人人网" style="text-decoration:none;line-height:16px;"></a><a href="http://blog.csdn.net/longshenlmj/article/details/50542683#" rel="nofollow" class="bds_weixin" title="分享到微信" style="text-decoration:none;line-height:16px;"></a></div>
<div id="digg" style="text-align:center;font-family:'microsoft yahei';">
</div>
<br><p></p>
            </div>
                </div>