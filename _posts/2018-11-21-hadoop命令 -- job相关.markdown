---
layout:     post
title:      hadoop命令 -- job相关
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ljp812184246/article/details/51108230				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
hadoop命令行 与job相关的：<br>
命令行工具 • <br>
1.<span style="color:#FF0000;">查看 Job 信息</span>：<br>
hadoop job <span style="color:#FF0000;">-list</span><span style="background-color:rgb(255,0,0);">
</span><br>
2.杀掉 Job： <br>
hadoop  job –kill  job_id<br>
3.指定路径下查看历史日志汇总：<br>
hadoop job -history output-dir <br>
4.作业的更多细节： <br>
hadoop job -history all output-dir <br>
5.<span style="color:#FF0000;">打印map和reduce完成百分比和所有计数器</span>：<br>
hadoop job <span style="color:#FF0000;">–status</span> job_id <br>
6.杀死任务。被杀死的任务不会不利于失败尝试：<br>
hadoop jab -kill-task &lt;task-id&gt; <br>
7.使任务失败。被失败的任务会对失败尝试不利：<br>
hadoop job  -fail-task &lt;task-id&gt;
            </div>
                </div>