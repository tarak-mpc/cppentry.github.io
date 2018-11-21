---
layout:     post
title:      由于在写oozie hive action时候script属性指定hql脚本时多写了一个斜杠导致的一个悲剧，以及解决方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>今天在编写 oozie hive action,编写的action如下：</p>
<p>&lt;action name="action_0_0_0"&gt;<br>
        &lt;hive xmlns="uri:oozie:hive-action:0.2"&gt;<br>
            &lt;job-tracker&gt;${jobTracker}&lt;/job-tracker&gt;<br>
            &lt;name-node&gt;${nameNode}&lt;/name-node&gt;<br>
            &lt;job-xml&gt;/tmp/hupu/hive/res/hive-site.xml&lt;/job-xml&gt;<br>
            &lt;script&gt;/tmp/hupu/hive/tmphql/&lt;/script&gt;<br>
        &lt;/hive&gt;<br>
        &lt;ok to="fork_97" /&gt;<br>
        &lt;error to="kill" /&gt;<br>
    &lt;/action&gt;<br></p>
<p><br></p>
<p>script节点的tmphql后面多了一个斜杠，然后导致了一个CDH5.3.0上的yarn的一个BUG，后续版本中应该已经解决了，如果你用的是这个版本，然后恰巧你也出现了这个失误，下面的内容也许就对你有帮助了。</p>
<p><br></p>
<p>由于这个BUG，会导致你的nm起不来，具体错误日志中有以下重要信息：</p>
<p>
</p><table class="DataTable" style="border-collapse:collapse;border-spacing:0px;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;"><tbody><tr style="border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);background-color:transparent;"><td class="nonMessageColumn" style="vertical-align:top;">
下午5点17:58.437</td>
<td class="nonMessageColumn fatal" style="color:rgb(185,74,72);vertical-align:top;">
FATAL</td>
<td class="nonMessageColumn" style="vertical-align:top;">
org.apache.hadoop.yarn.event.AsyncDispatcher</td>
<td style="vertical-align:top;">
<pre class="message" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:13px;background-color:transparent;">Error in dispatcher thread
java.lang.IllegalArgumentException: Can not create a Path from an empty string
	at org.apache.hadoop.fs.Path.checkPathArg(Path.java:127)
	at org.apache.hadoop.fs.Path.&lt;init&gt;(Path.java:135)
	at org.apache.hadoop.fs.Path.&lt;init&gt;(Path.java:94)
	at org.apache.hadoop.yarn.server.nodemanager.containermanager.localizer.LocalResourcesTrackerImpl.getPathForLocalization(LocalResourcesTrackerImpl.java:420)
	at org.apache.hadoop.yarn.server.nodemanager.containermanager.localizer.ResourceLocalizationService$PublicLocalizer.addResource(ResourceLocalizationService.java:773)
	at org.apache.hadoop.yarn.server.nodemanager.containermanager.localizer.ResourceLocalizationService$LocalizerTracker.handle(ResourceLocalizationService.java:687)
	at org.apache.hadoop.yarn.server.nodemanager.containermanager.localizer.ResourceLocalizationService$LocalizerTracker.handle(ResourceLocalizationService.java:629)
	at org.apache.hadoop.yarn.event.AsyncDispatcher.dispatch(AsyncDispatcher.java:173)
	at org.apache.hadoop.yarn.event.AsyncDispatcher$1.run(AsyncDispatcher.java:106)
	at java.lang.Thread.run(Thread.java:745)
</pre>
</td>
</tr><tr style="border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);background-color:rgb(249,249,249);"><td class="nonMessageColumn" style="vertical-align:top;">
下午5点17:58.445</td>
<td class="nonMessageColumn" style="vertical-align:top;">
INFO</td>
<td class="nonMessageColumn" style="vertical-align:top;">
org.apache.hadoop.yarn.event.AsyncDispatcher</td>
<td style="vertical-align:top;">
<pre class="message" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:13px;background-color:transparent;">Exiting, bbye..</pre>
</td>
</tr></tbody></table><br><p><br></p>
<p>
</p><table class="DataTable" style="border-collapse:collapse;border-spacing:0px;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;"><tbody><tr style="border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);background-color:rgb(249,249,249);"><td class="nonMessageColumn" style="vertical-align:top;">
下午5点17:59.386</td>
<td class="nonMessageColumn" style="vertical-align:top;">
INFO</td>
<td class="nonMessageColumn" style="vertical-align:top;">
org.apache.hadoop.yarn.server.nodemanager.containermanager.ContainerManagerImpl</td>
<td style="vertical-align:top;">
<pre class="message" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:13px;background-color:transparent;">Applications still running : [application_1423189731488_0063]</pre>
</td>
</tr></tbody></table><br><p>我首先按照第一个重要信息去GOOGLE，在jira上找到了原来这是这个版本的一个BUG，后面版本应该FIX掉了，但是我想改源码重新部署集群，于是我找日志发现了下面第二个重要信息，提示说我有一个一个application仍在运行，很奇怪，找不到这个正在运行的程序，是由于上面那个斜杠导致的失误导致应用程序状态不一致了。</p>
<pre class="message" style="color:rgb(51,51,51);line-height:20px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:13px;background-color:transparent;">Applications still running : [application_1423189731488_0063]</pre>
<pre class="message" style="color:rgb(51,51,51);line-height:20px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:13px;background-color:transparent;">于是继续GOOGLE，找到一篇文章：http://stackoverflow.com/questions/27065011/cdh-5-2-error-starting-nodemanager-service-nodemanager-failed-in-state-inited-c</pre>
<pre class="message" style="color:rgb(51,51,51);line-height:20px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:13px;background-color:transparent;">其中说到删除<span style="color:rgb(34,34,34);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:16.8999996185303px;">/tmp/hadoop-yarn/yarn-nm-recovery目录，我的目录是配在：/var/lib/hadoop-yarn/yarn-nm-recovery  （具体配置的哪个目录可以在cm中的 node manager group中找到）。于是我删除掉无法启动的nm节点上的这个目录，然后重启动，OK了。</span></pre>
<p></p>
            </div>
                </div>