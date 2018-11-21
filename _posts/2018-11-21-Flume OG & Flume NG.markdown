---
layout:     post
title:      Flume OG & Flume NG
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:16px;"><strong>前话</strong></span></p>
<p>Flume OG 也就是 Flume original generation 由 Cloudera公司开发，最新版本是0.9.4。而Flume NG 就是 Flume next generation， 大概在2011年6月份由 Cloudera贡献给开源社区，目前属于Apache的一个incubator project。</p>
<p><br></p>
<p><strong><span style="font-size:16px;">为什么要命名为Flume NG ？</span></strong></p>
<p>Flume NG的Jira上的第一个Trace：<a href="https://issues.apache.org/jira/browse/FLUME-728" rel="nofollow">FLUME-728</a></p>
<p>Apache 之所以要将 Flume 更命名 Flume NG，是因为Flume NG 对 Flume 进行了大规模的重构。重构的原因如下：</p>
<p>1. 代码过于复杂：Flume 在 Cloudera已经开发了多年，工程显得有些臃肿；</p>
<p>2. Flume NG要将其一些核心组件要进行标准化；</p>
<p>3. sink / source 配置不完善；</p>
<p>4. 改进heartbeat和master架构；</p>
<p>5. 最后一点也很重要：rename packages to org.apache.flume；</p>
<p><br></p>
<p>待续...</p>
<p><br></p>
<p><br></p>
<p>参考：</p>
<p><a href="https://cwiki.apache.org/FLUME/flume-ng.html" rel="nofollow">https://cwiki.apache.org/FLUME/flume-ng.html</a></p>
<p><a href="http://www.cloudera.com/blog/2011/12/apache-flume-architecture-of-flume-ng-2/" rel="nofollow">http://www.cloudera.com/blog/2011/12/apache-flume-architecture-of-flume-ng-2/</a><br></p>
<p><a href="https://github.com/apache/flume" rel="nofollow">https://github.com/apache/flume</a><br></p>
<p><a href="http://incubator.apache.org/projects/flume.html" rel="nofollow">http://incubator.apache.org/projects/flume.html</a><br></p>
            </div>
                </div>