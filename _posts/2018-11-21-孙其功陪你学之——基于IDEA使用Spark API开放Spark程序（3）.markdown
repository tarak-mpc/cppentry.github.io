---
layout:     post
title:      孙其功陪你学之——基于IDEA使用Spark API开放Spark程序（3）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="content bgF8F8F8 f14" style="font-size:14px;background-color:rgb(248,248,248);border-right-color:rgb(216,217,217);border-bottom-color:rgb(216,217,217);border-left-color:rgb(216,217,217);border-right-width:1px;border-bottom-width:1px;border-left-width:1px;border-style:none solid solid;line-height:28px;min-height:500px;overflow:hidden;color:rgb(51,51,51);font-family:'宋体';">
<div id="content">
<p style="background-color:transparent;text-indent:28px;">
<span style="text-align:center;"><span style="color:rgb(0,102,0);font-family:'宋体';font-size:14px;line-height:26px;">《Spark亚太研究院系列丛书——Spark实战高手之路 从零开始》本书通过Spark的shell测试Spark的工作；使用Spark的cache机制观察一下效率的提升构建Spark的IDE开发环境；通过Spark的IDE搭建Spark开发环境；测试Spark IDE开发环境等等。本节为大家介绍基于IDEA使用Spark
 API开放Spark程序。</span></span></p>
<p style="background-color:transparent;text-indent:28px;">
<span style="text-align:center;">基于IDEA使用Spark API开放Spark程序（3）</span></p>
<p style="background-color:transparent;text-indent:28px;">
把名称改为FirstSparkAppJar：</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s9.51cto.com/wyfs02/M01/4C/29/wKiom1Q3h7_Bik1tAACAAkKhmI4433.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s9.51cto.com/wyfs02/M01/4C/29/wKiom1Q3h7_Bik1tAACAAkKhmI4433.jpg" width="498" height="344" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
<br style="clear:both;width:0px;">
因为每台机器上都安装了Scala和Spark，所以可以把Scala和Spark相关的jar文件都删除掉：</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s7.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3iAbygrXnAABKDjrW_eI276.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s7.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3iAbygrXnAABKDjrW_eI276.jpg" width="498" height="226" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
<br style="clear:both;width:0px;">
接下来进行Build：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s9.51cto.com/wyfs02/M02/4C/29/wKiom1Q3h9yz_N67AABRPbpLId4371.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s9.51cto.com/wyfs02/M02/4C/29/wKiom1Q3h9yz_N67AABRPbpLId4371.jpg" width="496" height="247" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
选择“Build Artifacts”：</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s8.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3iCGyEKHPAAAdnZjmH0k367.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s8.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3iCGyEKHPAAAdnZjmH0k367.jpg" width="320" height="173" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
<br style="clear:both;width:0px;">
第一次是选择Build，以后同一个工程要选择Rebuild，然后等待编译完成：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s3.51cto.com/wyfs02/M00/4C/29/wKiom1Q3iAOheG-kAAAX96s8n9c497.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s3.51cto.com/wyfs02/M00/4C/29/wKiom1Q3iAOheG-kAAAX96s8n9c497.jpg" width="498" height="52" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
进入其编译后目录查看编译完成的文件：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s6.51cto.com/wyfs02/M00/4C/29/wKiom1Q3iAmgUJPsAAA7brr8pVU565.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s6.51cto.com/wyfs02/M00/4C/29/wKiom1Q3iAmgUJPsAAA7brr8pVU565.jpg" width="498" height="65" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
接下来使用spark-submit运行该程序：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s5.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3iH2hpS1MAAA86pxcub4151.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s5.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3iH2hpS1MAAA86pxcub4151.jpg" width="498" height="64" style="border:0px;text-align:center;"></a> </p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s1.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3iIPwCwMvAAEUPTUFnG8219.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s1.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3iIPwCwMvAAEUPTUFnG8219.jpg" width="498" height="310" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
此时查看控制台：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s5.51cto.com/wyfs02/M02/4C/29/wKiom1Q3iGTgxbDVAABW6Ugspe0271.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s5.51cto.com/wyfs02/M02/4C/29/wKiom1Q3iGTgxbDVAABW6Ugspe0271.jpg" width="498" height="304" style="border:0px;text-align:center;"></a> </p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s6.51cto.com/wyfs02/M01/4C/29/wKiom1Q3iGjRoY9WAABfTKOnC1Q155.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s6.51cto.com/wyfs02/M01/4C/29/wKiom1Q3iGjRoY9WAABfTKOnC1Q155.jpg" width="498" height="298" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
最后运行完成任务：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s9.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3iLDTJrkpAAE_Biz5W6c535.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s9.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3iLDTJrkpAAE_Biz5W6c535.jpg" width="498" height="332" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
我们到HDFS控制台查看运行结果：</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s5.51cto.com/wyfs02/M01/4C/29/wKiom1Q3iL7DZMxTAAC75VdoaLE622.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s5.51cto.com/wyfs02/M01/4C/29/wKiom1Q3iL7DZMxTAAC75VdoaLE622.jpg" width="498" height="396" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
<br style="clear:both;width:0px;">
进入SogouQResult文件夹：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s8.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3iQXyC1kvAACWLKVGpnM069.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s8.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3iQXyC1kvAACWLKVGpnM069.jpg" width="498" height="292" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
查看执行结果：</p>
<p style="background-color:transparent;text-indent:28px;">
</p>
<p style="background-color:transparent;text-indent:28px;">
<a href="http://s4.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3iRbwp1kxAACg9g2EBXc917.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s4.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3iRbwp1kxAACg9g2EBXc917.jpg" width="498" height="414" style="border:0px;text-align:center;"></a></p>
<p style="background-color:transparent;text-indent:28px;">
这个内容和我们前面完全spark-shell的执行时一样的。</p>
</div>
</div>
            </div>
                </div>