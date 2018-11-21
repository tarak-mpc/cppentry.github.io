---
layout:     post
title:      HDFS的读写流程，NameNode和SecondaryNameNode的工作机制，DataNode工作机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：Please make the source marked					https://blog.csdn.net/qq_31807385/article/details/84281031				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="HDFS%E7%9A%84%E5%86%99%E6%B5%81%E7%A8%8B%EF%BC%9A-toc" style="margin-left:80px;"><a href="#HDFS%E7%9A%84%E5%86%99%E6%B5%81%E7%A8%8B%EF%BC%9A" rel="nofollow">HDFS的写流程：</a></p>

<p id="%C2%A0HDFS%E7%9A%84%E8%AF%BB%E6%B5%81%E7%A8%8B-toc" style="margin-left:80px;"><a href="#%C2%A0HDFS%E7%9A%84%E8%AF%BB%E6%B5%81%E7%A8%8B" rel="nofollow"> HDFS的读流程</a></p>

<p id="NameNode%E5%92%8CSecondaryNameNode%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6%EF%BC%9A-toc" style="margin-left:80px;"><a href="#NameNode%E5%92%8CSecondaryNameNode%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6%EF%BC%9A" rel="nofollow">NameNode和SecondaryNameNode的工作机制：</a></p>

<p id="%E6%A0%B7%E4%BE%8B%EF%BC%9A-toc" style="margin-left:80px;"><a href="#%E6%A0%B7%E4%BE%8B%EF%BC%9A" rel="nofollow">样例：</a></p>

<p id="DateNode%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6%EF%BC%9A-toc" style="margin-left:80px;"><a href="#DateNode%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6%EF%BC%9A" rel="nofollow">DateNode的工作机制：</a></p>

<hr id="hr-toc"><h3 id="HDFS%E7%9A%84%E5%86%99%E6%B5%81%E7%A8%8B%EF%BC%9A">HDFS的写流程：</h3>

<p style="text-align:center;"><img alt="" class="has" height="536" src="https://img-blog.csdnimg.cn/20181120092537664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxODA3Mzg1,size_16,color_FFFFFF,t_70" width="756"></p>

<p> </p>

<h3 id="%C2%A0HDFS%E7%9A%84%E8%AF%BB%E6%B5%81%E7%A8%8B"> HDFS的读流程</h3>

<p style="text-align:center;"><img alt="" class="has" height="546" src="https://img-blog.csdnimg.cn/20181120092309398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxODA3Mzg1,size_16,color_FFFFFF,t_70" width="738"></p>

<h3 id="NameNode%E5%92%8CSecondaryNameNode%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6%EF%BC%9A">NameNode和SecondaryNameNode的工作机制：</h3>

<p style="text-align:center;"><img alt="" class="has" height="606" src="https://img-blog.csdnimg.cn/2018112009061467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxODA3Mzg1,size_16,color_FFFFFF,t_70" width="824"></p>

<p> </p>

<h3 id="%E6%A0%B7%E4%BE%8B%EF%BC%9A">样例：</h3>

<p style="text-align:center;"><img alt="" class="has" height="601" src="https://img-blog.csdnimg.cn/20181120090737594.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxODA3Mzg1,size_16,color_FFFFFF,t_70" width="739"></p>

<blockquote>
<p>这里面，每一次启动时，加载到内存的都是最新的fsimage镜像文件和seen.txid中指示的edit.log文件 ，每一个传输给2NN的也是这个最新的fsimage文件和seen.txid指示的edit.log文件，2NN会将这两个文件加载到2NN的内存中，然后保存其镜像， 传送给NN。</p>
</blockquote>

<p style="text-align:center;"><img alt="" class="has" height="515" src="https://img-blog.csdnimg.cn/20181120090746654.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxODA3Mzg1,size_16,color_FFFFFF,t_70" width="725"></p>

<h3 id="DateNode%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6%EF%BC%9A">DateNode的工作机制：</h3>

<p style="text-align:center;"><img alt="" class="has" height="516" src="https://img-blog.csdnimg.cn/20181120093759433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxODA3Mzg1,size_16,color_FFFFFF,t_70" width="724"></p>

<p> 如果NN认为DN挂掉了 ，那么NN就不会在上面存放任何内容。</p>

<p> </p>            </div>
                </div>