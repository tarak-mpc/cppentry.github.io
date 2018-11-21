---
layout:     post
title:      大数据学习篇：hadoop深入浅出系列之HDFS（四）——shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">上一篇我们讲了</span><a href="http://blog.csdn.net/stronglyh/article/details/48666673" rel="nofollow"><span style="font-family:'KaiTi_GB2312';font-size:18px;">HDFS的读文件和写文件</span></a><span style="font-family:'KaiTi_GB2312';font-size:18px;">，今天我们来讲HDFS的shell操作。</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">首先，我们进入到hadoop文件夹下，然后输入bin/hdfs命令，我们能发现有很多提示信息，Usage: hdfs [--config confdir] COMMAND where COMMAND is one of 这句话的大致意思就是说hdfs后面要跟命令，我们之前格式化namenode执行的命令是bin/hdfs namenode -format，下面有</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><span style="background-color:rgb(255,255,255);"><img alt="" src="https://img-blog.csdn.net/20150924182125887"></span>，</p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">当我们输入bin/hdfs dfs的时候，我们发现后面可以跟很多命令，懂shell的话，一眼就看出来这些跟shell命令基本一致</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20150924182635047"></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">下面我们说一些常用的关于hdfs的shell操作</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:24px;"><strong>一）ls命令</strong></span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -ls hdfs://192.168.1.91:9000/</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">看到这个hdfs://192.168.1.91:9000/是不是似曾相识呢，当然了，这是我们安装hadoop的时候配置的。注意（刚安装hadoop这里面是没有文件的）</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20150924190342218"></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span><br></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">简单的说说d rwxr-xr-x </span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">d：表示文件夹   如果是文件这个地方就是 -</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">r：读权限，read---------数字4表示</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">w：写权限，write-----------数字2表示</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">r：可执行，excute-----------数字1表示</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">上面的总共有三组  rwx第一组是所有者，r-x第二组是组，r-x第三组是其他用户，数字表示755</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">  - root supergroup</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">- 表示副本数，因为是文件夹 所以没有副本  所以就是-</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">root 文件所有者</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">supergroup 组名</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">0 2015-09-24 18:39 hdfs://192.168.1.91:9000/ceshi</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">0表示文件大小</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">2015-09-24 18:39 文件创建日期</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">hdfs://192.168.1.91:9000/ceshi 文件地址</span></p>
<p><span style="font-size:18px;"><br><span style="font-family:'KaiTi_GB2312';"></span></span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">现在我们想看ceshi文件下有什东西，我们使用如下命令</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -ls hdfs://192.168.1.91:9000/ceshi</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20150924194740604"></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">我们看到第一位是 -   证明这两个是个文件</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">大家有没有发现，要先进入一个文件夹下才能看里面的内容，那么我们是否可以既查看目录，也可以查看里面的文件</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">执行命令 bin/hdfs dfs -ls -help</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><img src="https://img-blog.csdn.net/20150925221353250?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">原来后面能跟那么多参数啊，此处我们使用 -R，R表示递归，要注意，R要大写</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -ls -R  hdfs://192.168.1.91:9000/  
</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><img src="https://img-blog.csdn.net/20150925221159273?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span><br></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">有人会问了，后面跟个地址是不是太麻烦，如果要查的是本地的呢</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -ls -R /   读出来的效果跟上面一样，这样看来命令能自动识别到对应位置</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span><br></p>
<p><img src="https://img-blog.csdn.net/20150925221633943?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">如果最后的/不加，就会获取到home目录下的文件</span></p>
<p><br></p>
<p><strong><span style="font-family:'KaiTi_GB2312';font-size:24px;">二）删除</span></strong></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -rm -R /ceshi，加R就是递归删除</span></p>
<p><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span><br></p>
<div><img src="https://img-blog.csdn.net/20150925222152521" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">这个时候我们再来查看，发现文件夹ceshi没有了</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925222326255" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">如果想删除名字比较相似的，可以用通配符，比如bin/hdfs dfs -rm -R /hhh*，删除所有hhh开头的文件名</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:24px;"><strong>三）新建</strong></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -mkdir  hdfs://192.168.1.91:9000/ceshi1</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925222819034" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:24px;"><strong>四）上传文件</strong></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -put NOTICE.txt /ceshi1</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925223556312" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">通过ls命令，我们发现上传成功了</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925223610496" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:24px;"><strong>五）下载</strong></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">既然有上传，那么肯定少不了下载，命令如下</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -get /ceshi1</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -get /ceshi1</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;"><img src="https://img-blog.csdn.net/20150925232410779" alt=""></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925224901315" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">这样ceshi1就被我们下载到本地了，如果下载到指定的目录，bin/hdfs dfs -get /ceshi1 /A，其中A是本地位置</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:24px;"><strong>六）查看文件内容</strong></span></div>
<div> <span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -text  /ceshi1/NOTICE.txt</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925225318958" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">问题来了，如果文件内容比较多，有很多行，展示岂不是很变态，可以使用管道more</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925225551055" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">如果想看文件靠后的内容，可以使用tail</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925225733020" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">七）追加内容</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">我们从本地上传一个文件hello，里面内容是hello you  hello me，然后上传到hdfs上</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">上传之后先查看文件内容，然后再上传一次hello文件，但是使用的是追加的方式 ：</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">bin/hdfs dfs -appendToFile hello  /ceshi1/hello</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><img src="https://img-blog.csdn.net/20150925230447767" alt=""></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#33ccff;">-------------------------------------------------------</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;">效果真是立竿见影啊</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#ff0000;">要注意啦，如果先把文件插到中间或者其他位置，shell是完成不了的哦。</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;color:#ff0000;"><br></span></div>
<div><br></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:18px;"><strong>还有一些其他的命令就不说了，还有cat（查看）chmod（修改权限）copy(复制) mv（移动）getfacl（显示权限信息）等等。</strong></span></div>
<div><br></div>
<div>﻿﻿</div>
<div>﻿﻿</div>
            </div>
                </div>