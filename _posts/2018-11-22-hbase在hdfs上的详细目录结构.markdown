---
layout:     post
title:      hbase在hdfs上的详细目录结构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：原创文章，转载请注明出处！					https://blog.csdn.net/L_15156024189/article/details/83444255				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hbase目录结构如图：</p>

<p><img alt="" class="has" height="583" src="https://img-blog.csdnimg.cn/20181027104401344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1198"></p>

<p> 列出/hbase目录下的所有目录和文件，结构如下：</p>

<pre class="has">
<code class="language-java">drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/.tmp/data
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/.tmp/data/hbase

drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/MasterProcWALs
-rw-r--r--   1 root supergroup          0 2018-10-27 18:26 /hbase/MasterProcWALs/state-00000000000000000003.log

drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/WALs
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/WALs/bigdata111,16201,1540635983442
-rw-r--r--   1 root supergroup         83 2018-10-27 18:26 /hbase/WALs/bigdata111,16201,1540635983442/bigdata111%2C16201%2C1540635983442.1540635991168
-rw-r--r--   1 root supergroup         83 2018-10-27 18:26 /hbase/WALs/bigdata111,16201,1540635983442/bigdata111%2C16201%2C1540635983442.meta.1540635991546.meta

drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/default
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/.tabledesc
-rw-r--r--   1 root supergroup        398 2018-10-27 18:26 /hbase/data/hbase/meta/.tabledesc/.tableinfo.0000000001
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740
-rw-r--r--   1 root supergroup         32 2018-10-27 18:26 /hbase/data/hbase/meta/1588230740/.regioninfo
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740/info
-rw-r--r--   1 root supergroup       5397 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740/info/eae735e8d1824bffa04eed7336397113
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/1588230740/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/1588230740/recovered.edits/3.seqid
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/.tabledesc
-rw-r--r--   1 root supergroup        312 2018-10-27 18:26 /hbase/data/hbase/namespace/.tabledesc/.tableinfo.0000000001
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58
-rw-r--r--   1 root supergroup         42 2018-10-27 18:26 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/.regioninfo
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/info
-rw-r--r--   1 root supergroup       4963 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/info/6f69ab990ede4b1f9f5dc42d83c56b48
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/recovered.edits/2.seqid
-rw-r--r--   1 root supergroup         42 2018-10-27 18:26 /hbase/hbase.id
-rw-r--r--   1 root supergroup          7 2018-10-27 18:26 /hbase/hbase.version
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/oldWALs
</code></pre>

<p>/hbase下包含2个文件和5个目录。</p>

<p>（1）<a>hbase.id</a></p>

<p>hbase.id文件记录了hbase的id，具体内容如下：</p>

<pre class="has">
<code class="language-java">PBUF
$3eee3dad-50e2-4dc7-84a3-e1a8b80a0064</code></pre>

<p><a>（2）hbase.version</a></p>

<p>hbase.version记录了hbase的版本，是一个二进制文件，内容如图：</p>

<p><img alt="" class="has" height="124" src="https://img-blog.csdnimg.cn/20181027105339404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="870"></p>

<p>（3）.tmp</p>

<p>.tmp是临时目录，是一个空目录，如图：</p>

<p><img alt="" class="has" height="218" src="https://img-blog.csdnimg.cn/20181027104450141.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p>（4）MasterProcWALs</p>

<p>MasterProcWALs目录下含有一个HMaster主节点状态日志文件，如图：</p>

<p><img alt="" class="has" height="251" src="https://img-blog.csdnimg.cn/20181027104524436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p><a>起初，日志内容为空</a></p>

<p>（5）WALs</p>

<p>日志目录，子目录结构如图：</p>

<p><img alt="" class="has" height="239" src="https://img-blog.csdnimg.cn/20181027104940155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1200"></p>

<table><tbody><tr><td>
			<p><a>其中bigdata111%2C16201%2C1540635983442.1540635991168日志内容如图：</a></p>
			</td>
		</tr></tbody></table><p><img alt="" class="has" height="264" src="https://img-blog.csdnimg.cn/20181027105034899.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="863"></p>

<p>（6）oldWALs</p>

<p>hbase操作相关的旧日志存放目录，如图：</p>

<p><img alt="" class="has" height="229" src="https://img-blog.csdnimg.cn/20181027105403766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p>（7）data</p>

<p>data目录是最重要的目录，存储hbase数据，下面含有两个命名空间default和hbase，其中default是默认命名空间，如果创建的表未指定命名空间，将存放在该命名空间下，habse是系统命名空间，他们分别对应default和hbase目录，其中刚开始default目录为空，而hbase目录结构如下：</p>

<p><img alt="" class="has" height="340" src="https://img-blog.csdnimg.cn/20181027110041532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1124"></p>

<p> </p>

<p>2、HBase相关操作后目录变化</p>

<p> 创建表mytable</p>

<pre class="has">
<code>create 'mytable','cf'</code></pre>

<p>之后，目录结构如下：</p>

<pre class="has">
<code class="language-java">drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/.tmp/data
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/.tmp/data/default
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/.tmp/data/hbase
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/MasterProcWALs
-rw-r--r--   1 root supergroup        461 2018-10-27 18:26 /hbase/MasterProcWALs/state-00000000000000000003.log
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/WALs
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/WALs/bigdata111,16201,1540635983442
-rw-r--r--   1 root supergroup         83 2018-10-27 18:26 /hbase/WALs/bigdata111,16201,1540635983442/bigdata111%2C16201%2C1540635983442.1540635991168
-rw-r--r--   1 root supergroup         83 2018-10-27 18:26 /hbase/WALs/bigdata111,16201,1540635983442/bigdata111%2C16201%2C1540635983442.meta.1540635991546.meta
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/.tabledesc
-rw-r--r--   1 root supergroup        285 2018-10-27 18:57 /hbase/data/default/mytable/.tabledesc/.tableinfo.0000000001
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b
-rw-r--r--   1 root supergroup         42 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/.regioninfo
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/cf
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/recovered.edits/2.seqid
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/.tabledesc
-rw-r--r--   1 root supergroup        398 2018-10-27 18:26 /hbase/data/hbase/meta/.tabledesc/.tableinfo.0000000001
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740
-rw-r--r--   1 root supergroup         32 2018-10-27 18:26 /hbase/data/hbase/meta/1588230740/.regioninfo
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740/info
-rw-r--r--   1 root supergroup       5397 2018-10-27 18:36 /hbase/data/hbase/meta/1588230740/info/eae735e8d1824bffa04eed7336397113
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/1588230740/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/meta/1588230740/recovered.edits/3.seqid
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/.tabledesc
-rw-r--r--   1 root supergroup        312 2018-10-27 18:26 /hbase/data/hbase/namespace/.tabledesc/.tableinfo.0000000001
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58
-rw-r--r--   1 root supergroup         42 2018-10-27 18:26 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/.regioninfo
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/info
-rw-r--r--   1 root supergroup       4963 2018-10-27 18:36 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/info/6f69ab990ede4b1f9f5dc42d83c56b48
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 18:26 /hbase/data/hbase/namespace/8a0cf0539605aa1c70908eac9a24da58/recovered.edits/2.seqid
-rw-r--r--   1 root supergroup         42 2018-10-27 18:26 /hbase/hbase.id
-rw-r--r--   1 root supergroup          7 2018-10-27 18:26 /hbase/hbase.version
drwxr-xr-x   - root supergroup          0 2018-10-27 18:26 /hbase/oldWALs
</code></pre>

<p>目录变化：</p>

<p>.tmp目录：增加了/hbase/.tmp/data/default 空目录；</p>

<p>MasterProcWALs目录：<a>state-00000000000000000003.log中记录了操作日志，一段时间见后会被删除，重新创建state-00000000000000000004.log空白日志文件；</a></p>

<p>WALs目录：记录创建表的相关日志；</p>

<p>data目录：增加了<a>mytable目录，结构如下：</a></p>

<pre class="has">
<code class="language-java">drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/.tabledesc
-rw-r--r--   1 root supergroup        285 2018-10-27 18:57 /hbase/data/default/mytable/.tabledesc/.tableinfo.0000000001
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/.tmp
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b
-rw-r--r--   1 root supergroup         42 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/.regioninfo
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/cf
drwxr-xr-x   - root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 18:57 /hbase/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/recovered.edits/2.seqid
</code></pre>

<p> 插入数据后，cf目录下不会立即生成数据文件，而是保存在内存（MemStore）中，当执行stop-hbase.sh关闭HBase后会将数据刷新到文件中，如图：</p>

<p><img alt="" class="has" height="330" src="https://img-blog.csdnimg.cn/20181027115637815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xfMTUxNTYwMjQxODk=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p>删除表后增加/archive目录，删除的表放入该目录下，如图：</p>

<pre class="has">
<code class="language-java">drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data
drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default
drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default/mytable
drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b
drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/cf
-rw-r--r--   1 root supergroup       5004 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/cf/eab191595e614af0b75e14475fd58eca
drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/cf-2
-rw-r--r--   1 root supergroup       4930 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/cf-2/d26b377dfb8c43e4abee2d5e79a7180d
drwxr-xr-x   - root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/recovered.edits
-rw-r--r--   1 root supergroup          0 2018-10-27 20:03 /hbase/archive/data/default/mytable/bcc4a32d806193ddf0600d026237ff8b/recovered.edits/22.seqid
</code></pre>            </div>
                </div>