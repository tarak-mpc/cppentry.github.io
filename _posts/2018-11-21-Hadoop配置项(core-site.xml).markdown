---
layout:     post
title:      Hadoop配置项(core-site.xml)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div align="center">
<div style="text-align:left;"><span style="font-size:18px;">Hadoop配置项(core-site.xml) :</span></div>
<br><table border="1" cellspacing="0" cellpadding="0" width="100%"><tbody><tr><td style="background:#FFFFFF;">
<p align="center"><span style="color:rgb(44,44,44);"> name</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="center"><span style="color:#2C2C2C;">value </span></p>
</td>
<td style="background:#FFFFFF;">
<p align="center"><span style="color:#2C2C2C;">Description </span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> fs.default.name</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">hdfs://hadoopmaster:9000</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">定义HadoopMaster</span>的URI和端口</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> fs.checkpoint.dir</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">/opt/data/hadoop1/hdfs/namesecondary1</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">定义hadoop</span>的name备份的路径，官方文档说是读取这个，写入dfs.name.dir</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> fs.checkpoint.period</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">1800</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">定义name</span>备份的备份间隔时间，秒为单位，只对snn生效，默认一小时</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> fs.checkpoint.size</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">33554432</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">以日志大小间隔做备份间隔，只对snn</span>生效，默认64M</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.compression.codecs</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">org.apache.hadoop.io.compress.DefaultCodec,<br>
com.hadoop.compression.lzo.LzoCodec,<br>
com.hadoop.compression.lzo.LzopCodec,<br>
org.apache.hadoop.io.compress.GzipCodec,<br>
org.apache.hadoop.io.compress.BZip2Codec<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">Hadoop</span><span style="color:#2C2C2C;">所使用的编解码器，gzip</span>和bzip2为自带，lzo需安装hadoopgpl或者kevinweil，逗号分隔，snappy也需要单独安装</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.compression.codec.lzo.class</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">com.hadoop.compression.lzo.LzoCodec</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">LZO</span><span style="color:#2C2C2C;">所使用的压缩编码器 </span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> topology.script.file.name</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">/hadoop/bin/RackAware.py</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">机架感知脚本位置 </span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> topology.script.number.args</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">1000</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">机架感知脚本管理的主机数，IP</span>地址 </p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> fs.trash.interval</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">10800</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">HDFS</span><span style="color:#2C2C2C;">垃圾箱设置，可以恢复误删除，分钟数，0</span>为禁用，添加该项无需重启hadoop</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.filter.initializers</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">org.apache.hadoop.security.<br>
AuthenticationFilterInitializer<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">需要jobtracker,tasktracker<br>
namenode,datanode</span>等http访问端口用户验证使用，需配置所有节点</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.type</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">simple | kerberos | #AUTHENTICATION_HANDLER_CLASSNAME#</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">验证方式，默认为简单，也可自己定义class,</span>需配置所有节点</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.<br>
token.validity<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">36000</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">验证令牌的有效时间,</span>需配置所有节点</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.<br>
signature.secret<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">默认可不写参数</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">默认不写在hadoop</span>启动时自动生成私密签名,需配置所有节点</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.cookie.domain</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">domian.tld</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">http</span><span style="color:#2C2C2C;">验证所使用的cookie</span>的域名，IP地址访问则该项无效，必须给所有节点都配置域名才可以。</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.<br>
 simple.anonymous.allowed<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">true | false</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">简单验证专用，默认允许匿名访问，true</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.<br>
kerberos.principal<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">HTTP/localhost@$LOCALHOST</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">Kerberos</span><span style="color:#2C2C2C;">验证专用，参加认证的实体机必须使用HTTP</span>作为K的Name</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.http.authentication.<br>
kerberos.keytab<br>
(</span><span style="color:#2C2C2C;">排版调整，实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">/home/xianglei/hadoop.keytab</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">Kerberos</span><span style="color:#2C2C2C;">验证专用，密钥文件存放位置</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.security.authorization</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">true|false</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">Hadoop</span><span style="color:#2C2C2C;">服务层级验证安全验证，需配合hadoop-policy.xml</span>使用，配置好以后用dfsadmin,mradmin -refreshServiceAcl刷新生效</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.file.buffer.size</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">131072</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">用作序列化文件处理时读写buffer</span>的大小</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.security.authentication</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">simple | kerberos</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">hadoop</span><span style="color:#2C2C2C;">本身的权限验证，非http</span>访问，simple或者kerberos</p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.logfile.size</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">1000000000</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">设置日志文件大小，超过则滚动新日志</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> hadoop.logfile.count</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">20</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">最大日志数</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.bytes.per.checksum</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">1024</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">每校验码所校验的字节数，不要大于io.file.buffer.size</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.skip.checksum.errors</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">true | false</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">处理序列化文件时跳过校验码错误，不抛异常。默认false</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.serializations</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">org.apache.hadoop.io.<br>
serializer.WritableSerialization</span></p>
<p align="left"><span style="color:#2C2C2C;">(</span><span style="color:#2C2C2C;">排版需要。实际配置不要回车)</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">序列化的编解码器</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> io.seqfile.compress.blocksize</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">1024000</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">块压缩的序列化文件的最小块大小，字节</span></p>
</td>
</tr><tr><td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;"> webinterface.private.actions</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">true | false</span></p>
</td>
<td style="background:#FFFFFF;">
<p align="left"><span style="color:#2C2C2C;">设为true</span>，则JT和NN的tracker网页会出现杀任务删文件等操作连接，默认是false</p>
</td>
</tr></tbody></table></div>
            </div>
                </div>