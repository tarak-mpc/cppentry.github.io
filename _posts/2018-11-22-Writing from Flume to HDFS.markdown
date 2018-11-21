---
layout:     post
title:      Writing from Flume to HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="title topictitle1" style="font-family:Arial, sans-serif;line-height:40px;font-size:24px;">
Example: Writing from Flume to HDFS</h1>
<div class="body conbody" style="color:rgb(102,102,102);font-family:Arial, sans-serif;font-size:13.3333px;line-height:17.3333px;">
<p class="p">Apache Flume is a service for collecting log data. You can capture events in Flume and store them in HDFS for analysis. For a conceptual description of Flume, see the<a class="xref" href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow" style="color:rgb(3,146,178);text-decoration:none;font-weight:bold;">Flume
 User Guide</a>. This example is a quick walkthrough to get Flume up and running.</p>
</div>
<div class="related-links" style="font-family:Arial, sans-serif;color:rgb(102,102,102);font-size:13.3333px;line-height:17.3333px;">
</div>
<div class="topic concept nested1" id="concept_fpw_wvs_gs_unique_1" lang="en-us" style="color:rgb(102,102,102);font-family:Arial, sans-serif;font-size:13.3333px;line-height:17.3333px;" xml:lang="en-us">
<h2 class="title topictitle2" style="line-height:20px;color:rgb(0,0,0);font-size:18px;">
Flume Out of the Box</h2>
<div class="body conbody">
<p class="p">To use Flume in a fresh Quickstart VM:</p>
<div class="p">
<ol class="ol" id="concept_fpw_wvs_gs_unique_1__ol_knn_lws_gs_unique_1"><li class="li" style="line-height:18.98px;">Import a new VM instance.</li><li class="li" style="line-height:18.98px;">Configure the new VM.
<ol class="ol" type="a"><li class="li" style="line-height:18.98px;">Allocate a minimum of 10023 MB memory.</li><li class="li" style="line-height:18.98px;">Allocate 2 CPUs.</li><li class="li" style="line-height:18.98px;">Allocate 20MB video memory.</li><li class="li" style="line-height:18.98px;">Consider setting the clipboard to bidirectional.</li></ol></li><li class="li" style="line-height:18.98px;">Start the VM.</li><li class="li" style="line-height:18.98px;">Launch Cloudera Manager.</li><li class="li" style="line-height:18.98px;">In the browser, click the Cloudera Manager link.</li><li class="li" style="line-height:18.98px;">Start Hue.</li><li class="li" style="line-height:18.98px;">Start Flume.</li><li class="li" style="line-height:18.98px;">Use Telnet to test the default Flume implementation.
<ol class="ol" id="concept_fpw_wvs_gs_unique_1__ol_xqb_yzs_gs_unique_1" type="a"><li class="li" style="line-height:18.98px;">Open a terminal window.</li><li class="li" style="line-height:18.98px;">Install Telnet with the command<span class="keyword cmdname" style="font-family:monospace;">sudo yum install telnet</span>.</li><li class="li" style="line-height:18.98px;">Launch Telnet with the command <span class="keyword cmdname" style="font-family:monospace;">telnet localhost 10001</span>.</li><li class="li" style="line-height:18.98px;">At the prompt, enter <span class="keyword cmdname" style="font-family:monospace;">Hello world!</span>.</li><li class="li" style="line-height:18.98px;">Open <span class="ph filepath" style="font-family:monospace;">/var/log/flume-ng/﻿flume-cmf-flume-AGENT-quickstart.cloudera.log</span>.</li><li class="li" style="line-height:18.98px;">Scroll to the bottom of the log, which should have an entry similar to the following.
<pre class="pre codeblock" style="font-family:monospace, serif;font-size:1em;border:thin dashed rgb(0,110,137);background-color:rgb(255,255,255);">﻿2015-06-05 15:45:55,561 INFO org.apache.flume.sink.LoggerSink: 
Event: { headers:{} body: 48 65 6C 6C 6F 20 77 6F 72 6C 64 21 0D
         Hello world!. }</pre>
</li></ol></li></ol></div>
</div>
</div>
<div class="topic concept nested1" id="concept_jjn_dct_gs_unique_1" lang="en-us" style="color:rgb(102,102,102);font-family:Arial, sans-serif;font-size:13.3333px;line-height:17.3333px;" xml:lang="en-us">
<h2 class="title topictitle2" style="line-height:20px;color:rgb(0,0,0);font-size:18px;">
Writing from Flume to HDFS</h2>
<div class="body conbody">
<p class="p">You can configure Flume to write incoming messages to data files stored in HDFS for later processing.</p>
<p class="p">To configure Flume to write to HDFS:</p>
<div class="p">
<ol class="ol" id="concept_jjn_dct_gs_unique_1__ol_nyn_pct_gs_unique_1"><li class="li" style="line-height:18.98px;">In the VM web browser, open Hue.</li><li class="li" style="line-height:18.98px;">Click <span class="keyword cmdname" style="font-family:monospace;">File Browser</span>.</li><li class="li" style="line-height:18.98px;">Create the <span class="ph filepath" style="font-family:monospace;">/flume/events</span> directory.
<ol class="ol" id="concept_jjn_dct_gs_unique_1__ol_b5x_2gt_gs_unique_1" type="a"><li class="li" style="line-height:18.98px;">In the<span class="ph filepath" style="font-family:monospace;"> /user/cloudera</span>directory, click <span class="keyword cmdname" style="font-family:monospace;">New-&gt;Directory</span>.</li><li class="li" style="line-height:18.98px;">Create a directory named<span class="ph filepath" style="font-family:monospace;">flume</span>.</li><li class="li" style="line-height:18.98px;">In the <span class="ph filepath" style="font-family:monospace;">flume</span> directory, create a directory named <span class="ph filepath" style="font-family:monospace;">events</span>.</li><li class="li" style="line-height:18.98px;">Check the box to the left of the<span class="ph filepath" style="font-family:monospace;">events</span> directory, then click the<span class="keyword cmdname" style="font-family:monospace;">Permissions</span> setting.</li><li class="li" style="line-height:18.98px;">Enable <span class="keyword cmdname" style="font-family:monospace;">Write</span> access for <span class="keyword cmdname" style="font-family:monospace;">Group</span>and <span class="keyword cmdname" style="font-family:monospace;">Other</span> users.</li><li class="li" style="line-height:18.98px;">Click <span class="keyword cmdname" style="font-family:monospace;">Submit</span>.</li></ol></li><li class="li" style="line-height:18.98px;">Change the Flume configuration.
<ol class="ol" id="concept_jjn_dct_gs_unique_1__ol_djp_lgt_gs_unique_1" type="a"><li class="li" style="line-height:18.98px;">Open Cloudera Manager in your web browser.</li><li class="li" style="line-height:18.98px;">In the list of services, click Flume.</li><li class="li" style="line-height:18.98px;">Click the <span class="keyword cmdname" style="font-family:monospace;">Configuration</span> tab.</li><li class="li" style="line-height:18.98px;">Scroll or search for the<span class="keyword cmdname" style="font-family:monospace;">Configuration File</span> item.</li><li class="li" style="line-height:18.98px;">Append the following lines to the<span class="keyword cmdname" style="font-family:monospace;">Configuration File</span> settings.
<pre class="pre codeblock" style="font-family:monospace, serif;font-size:1em;border:thin dashed rgb(0,110,137);background-color:rgb(255,255,255);">tier1.sinks.sink1.type= HDFS
tier1.sinks.sink1.fileType=DataStream
tier1.sinks.sink1.channel      = channel1
tier1.sinks.sink1.hdfs.path = hdfs://localhost:8020/user/cloudera/flume/events</pre>
</li><li class="li" style="line-height:18.98px;">At the top of the settings list, click <span class="keyword cmdname" style="font-family:monospace;">Save Changes</span>.</li></ol></li><li class="li" style="line-height:18.98px;">On the far right, choose <span class="keyword cmdname" style="font-family:monospace;">Actions-&gt;Restart</span> to restart Flume.</li><li class="li" style="line-height:18.98px;">When the restrart is complete, click<span class="keyword cmdname" style="font-family:monospace;">Close</span>.</li><li class="li" style="line-height:18.98px;">Click the <span class="keyword cmdname" style="font-family:monospace;">Home</span> tab. If necessary, start the <span class="keyword cmdname" style="font-family:monospace;">Yarn</span> service.</li><li class="li" style="line-height:18.98px;">In a terminal window, launch Telnet with the command <span class="keyword cmdname" style="font-family:monospace;">telnet localhost 10001</span>.</li><li class="li" style="line-height:18.98px;">At the prompt, enter <span class="keyword cmdname" style="font-family:monospace;">Hello HDFS!</span>.</li><li class="li" style="line-height:18.98px;">In the Hue File Browser, open the<span class="ph filepath" style="font-family:monospace;">/user/cloudera/flume/events</span>directory.</li><li class="li" style="line-height:18.98px;">There will be a file named <span class="ph filepath" style="font-family:monospace;">FlumeData</span>with a serial number as the file extension. Click the file name link to view the data sent by Flume
 to HDFS. The output is similar to the following.
<pre class="pre codeblock" style="font-family:monospace, serif;font-size:1em;border:thin dashed rgb(0,110,137);background-color:rgb(255,255,255);">﻿0000000: 53 45 51 06 21 6f 72 67 2e 61 70 61 63 68 65 2e SEQ.!org.apache.
0000010: 68 61 64 6f 6f 70 2e 69 6f 2e 4c 6f 6e 67 57 72 hadoop.io.LongWr
0000020: 69 74 61 62 6c 65 22 6f 72 67 2e 61 70 61 63 68 itable"org.apach
0000030: 65 2e 68 61 64 6f 6f 70 2e 69 6f 2e 42 79 74 65 e.hadoop.io.Byte
0000040: 73 57 72 69 74 61 62 6c 65 00 00 00 00 00 00 85 sWritable.......
0000050: a6 6f 46 0c f4 16 33 a6 eb 43 c2 21 5c 1b 4f 00 .oF...3..C.!\.O.
0000060: 00 00 18 00 00 00 08 00 00 01 4d c6 1b 01 1f 00 ..........M.....
0000070: 00 00 0c 48 65 6c 6c 6f 20 48 44 46 53 21 0d    ...Hello HDFS!.</pre>
</li></ol></div>
</div>
</div>
<div class="topic concept nested1" id="concept_rkm_qjn_hs_unique_1" lang="en-us" style="color:rgb(102,102,102);font-family:Arial, sans-serif;font-size:13.3333px;line-height:17.3333px;" xml:lang="en-us">
<h2 class="title topictitle2" style="line-height:20px;color:rgb(0,0,0);font-size:18px;">
Sentiment Analysis of Input from Flume</h2>
<div class="body conbody">
<p class="p">Now that Flume is sending data to HDFS, you can apply the Sentiment Analysis example to comments you enter.</p>
<div class="p">All of the source for this example is provided in <a class="xref" href="http://tiny.cloudera.com/flumeToHDFS-sample" rel="nofollow" style="color:rgb(3,146,178);text-decoration:none;font-weight:bold;"><span class="ph filepath" style="font-family:monospace;">flumeToHDFS.tar.gz</span></a>,
 which contains:
<ul class="ul"><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">flume.config</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">makefile</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">Map.java</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">MrManager.java</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">Reduce.java</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">neg-words.txt</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">pos-words.txt</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">stop-words.txt</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">/shakespeare</span>
<ul class="ul" id="concept_rkm_qjn_hs_unique_1__ul_awb_fkq_fs_unique_3"><li class="li" style="line-height:18.98px;">﻿<span class="ph filepath" style="font-family:monospace;">comedies</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">histories</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">poems</span></li><li class="li" style="line-height:18.98px;"><span class="ph filepath" style="font-family:monospace;">tragedies</span></li></ul></li></ul></div>
<div class="p">To test sentiment analysis with Flume input:
<ol class="ol" id="concept_rkm_qjn_hs_unique_1__ol_pzj_1kn_hs_unique_1"><li class="li" style="line-height:18.98px;">Expand <span class="ph filepath" style="font-family:monospace;">flumeToHDFS.tar.gz </span>on the VM.</li><li class="li" style="line-height:18.98px;">In a terminal window, navigate to the /flume2hdfs</li><li class="li" style="line-height:18.98px;">Launch Telnet with the command<span class="keyword cmdname" style="font-family:monospace;">telnet localhost 10001</span>.</li><li class="li" style="line-height:18.98px;">Enter the following lines, hitting Enter after each line.(Telnet returns the response OK to each line).
<pre class="pre codeblock" style="font-family:monospace, serif;font-size:1em;border:thin dashed rgb(0,110,137);background-color:rgb(255,255,255);">I enjoy using CDH. I think CDH is wonderful.
I like the power and flexibility of CDH.
I dislike brussels sprouts. I hate mustard greens.
Flume is a great product. I have several use cases in mind for which it is well suited.</pre>
</li><li class="li" style="line-height:18.98px;">Enter <span class="keyword cmdname" style="font-family:monospace;">run_flume</span> to start the Sentiment Analysis example via the <span class="ph filepath" style="font-family:monospace;">makefile</span>.
 The application returns results from all counters, ending with the custom counters and report.
<pre class="pre codeblock" style="font-family:monospace, serif;font-size:1em;border:thin dashed rgb(0,110,137);background-color:rgb(255,255,255);">﻿	org.myorg.Map$Gauge
		NEGATIVE=2
		POSITIVE=6
**********
Sentiment score = (6.0 - 2.0) / (6.0 + 2.0)
Sentiment score = 0.5

Positivity score = 6.0/(6.0+2.0)
Positivity score = 75%
********** </pre>
</li></ol></div>
</div>
</div>
<span class="copyright" style="color:rgb(102,102,102);font-family:Arial, sans-serif;font-size:13.3333px;line-height:17.3333px;"></span>
<div style="color:rgb(102,102,102);font-family:Arial, sans-serif;font-size:13.3333px;line-height:17.3333px;text-align:center;">
<span>Page generated October 23, 2015.</span></div>
            </div>
                </div>