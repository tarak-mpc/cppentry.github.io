---
layout:     post
title:      HDFS HBase  JAVA编程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">一、启动HDFS 命令</span></p>
<p><span style="font-size:14px;color:#ff0000;">start-dfs.sh</span></p>
<p><span style="font-size:14px;">二、启动HBase 命令</span></p>
<p><span style="font-size:14px;color:#ff0000;">start-hbase.sh</span></p>
<p><span style="font-size:14px;">三、HBase命令</span></p>
<p><span style="font-size:14px;">1)进入HBase </span></p>
<p><span style="font-size:14px;"><span></span><span style="background-color:rgb(255,255,255);"><span style="color:#ff0000;">hbase  shell </span></span></span></p>
<p><span style="font-size:14px;">2)浏览表</span></p>
<p><span style="font-size:14px;"><span style="color:#ff0000;"><span></span>scan  '表名'</span></span></p>
<p><span style="font-size:14px;">3)当删除表时 </span></p>
<p><span style="font-size:14px;"><span></span>首先用       <span style="color:#ff0000;">
disabled '</span></span><span style="color:#ff0000;"><span style="font-size:14px;">表名</span><span style="font-size:14px;">' </span></span></p>
<p><span style="font-size:14px;"><span></span>然后          <span style="color:#ff0000;">drop '<span style="font-size:14px;">表名</span>'</span></span></p>
<p><span style="font-size:14px;">4)</span><span style="font-size:14px;">退出       </span><span style="font-size:14px;"><span style="color:#ff0000;">exit</span></span></p>
<p><span style="font-size:14px;">四、运行程序前需要把数据文件先放到hdfs目录下</span></p>
<p><span style="font-size:14px;">命令</span></p>
<p><span style="font-size:14px;color:#ff0000;">hadoop fs -put file.txt  /hw/</span></p>
<p><span style="font-size:14px;">否则出现错误     java.io.FileNotFoundException: File does not exist:</span></p>
<p><span style="font-size:14px;">五、在linux编译时中文注释乱码 编译出现错误</span></p>
<p><span style="font-size:14px;">把文件另存为UTF-8格式 然后在java编译器的命令输入</span></p>
<p><span style="font-size:14px;color:#ff0000;">javac  -encoding UTF-8 Hw.java</span></p>
<p><span style="font-size:14px;"><span style="color:#ff0000;">java -Dfile.encoding="UTF-8" Hw</span><br></span></p>
<p><span style="font-size:14px;">六、java获取命令行参数</span></p>
<p><span style="font-size:14px;">public static void main(String[] <span style="color:#ff0000;">
args</span>) </span></p>
<p><span style="font-size:14px;">其中args数组保存了命令行参数</span></p>
<p><span style="font-size:14px;">通过arg[0],arg[1]....获取参数</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p> <br></p>
            </div>
                </div>