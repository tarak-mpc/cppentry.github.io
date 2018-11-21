---
layout:     post
title:      StreamingPro
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
    <div class="show-content-free">
            <h2>Declarative workflows for building Spark Streaming</h2>
<div class="image-package">
<div class="image-container">
<div class="image-container-fill"></div>
<div class="image-view"><img src="https://upload-images.jianshu.io/upload_images/1063603-968e744a1ef2e334.png" alt="1063603-968e744a1ef2e334.png"></div>
</div>
<div class="image-caption"></div>
</div>
<h2>Spark Streaming</h2>
<p>Spark Streaming is an extension of the core Spark API that enables stream processing from a variety of sources.<br>
Spark is a extensible and programmable framework for massive distributed processing of datasets,<br>
called Resilient Distributed Datasets (RDD). Spark Streaming receives input data streams and divides the data into batches, which are then processed by the Spark engine to generate the results.<br>
Spark Streaming data is organized into a sequence of DStreams,<br>
represented internally as a sequence of RDDs.</p>
<h2>StreamingPro</h2>
<p>StreamingPro is not a complete application, but rather  a extensible and programmable framework for spark streaming (also include spark,storm)<br>
that can easily be used to build your streaming application.</p>
<p>StreamingPro also make it possible that all you should do to build streaming program is assembling components(eg. SQL Component) in configuration file.</p>
<h2>Features</h2>
<ul><li>Pure Spark Streaming(Or normal Spark) program (Storm in future)</li>
<li>No need of coding, only declarative workflows</li>
<li>Rest API for interactive</li>
<li>SQL-Oriented workflows support</li>
<li>Data continuously streamed in &amp; processed in near real-time</li>
<li>dynamically CURD of workflows  at runtime via Rest API</li>
<li>Flexible workflows (input, output, parsers, etc...)</li>
<li>High performance</li>
<li>Scalable</li>
</ul><h2>Documents</h2>
<ul><li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Properties" rel="nofollow">Properties</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Build" rel="nofollow">Build</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Run-your-first-application" rel="nofollow">Run your first application</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Submit-application" rel="nofollow">Submit application</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/How-To-Add-New-Compositor" rel="nofollow">dynamically CURD of workflows  at runtime via Rest API</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Recovery" rel="nofollow">Recovery</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Common-compositors-introduction" rel="nofollow">Useful modules introduction</a></li>
<li><a href="https://link.jianshu.com?t=https://github.com/allwefantasy/streamingpro/wiki/Runtime-support" rel="nofollow">Other runtime support</a></li>
</ul><h3>Architecture</h3>
<div class="image-package">
<div class="image-container">
<div class="image-container-fill"></div>
<div class="image-view"><img src="https://upload-images.jianshu.io/upload_images/1063603-383c19104e141031.png" alt="1063603-383c19104e141031.png"></div>
</div>
<div class="image-caption">Snip20160510_3.png</div>
</div>
<h3>Declarative workflows</h3>
<div class="image-package">
<div class="image-container">
<div class="image-container-fill"></div>
<div class="image-view"><img src="https://upload-images.jianshu.io/upload_images/1063603-968e744a1ef2e334.png" alt="1063603-968e744a1ef2e334.png"></div>
</div>
<div class="image-caption">Snip20160510_4.png</div>
</div>
<h3>Implementation</h3>
<div class="image-package">
<div class="image-container">
<div class="image-container-fill"></div>
<div class="image-view"><img src="https://upload-images.jianshu.io/upload_images/1063603-26dd2d88611a8b93.png" alt="1063603-26dd2d88611a8b93.png"></div>
</div>
<div class="image-caption">Snip20160510_1.png</div>
</div>

          </div>
              </div>
                </div>