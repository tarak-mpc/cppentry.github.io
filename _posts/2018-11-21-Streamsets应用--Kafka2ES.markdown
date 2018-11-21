---
layout:     post
title:      Streamsets应用--Kafka2ES
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>一、场景</h1>

<p>将指定kafka的一个topic的数据实时入库到elasticsearch中。</p>

<h1>二、环境</h1>

<p>kafka:0.10.2</p>

<p>elasticsearch:5.6.1</p>

<h1>三、应用</h1>

<h2>3.1、概述</h2>

<p><img alt="" class="has" height="229" src="https://img-blog.csdn.net/20180821175118919?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="497"></p>

<p>Origins:Kafka Consumer</p>

<p>Destiations:ElasticSearch</p>

<h2>3.2、注意事项</h2>

<p>1、若只读取一个topic的数据，origins使用Kafka Consumer；若要同时读取多个topic，则使用Kafka Multitopic。</p>

<p>2、kafka若要实现读取历史数据，要添加auto.offset.reset=earliest</p>

<h2>3.3、流程</h2>

<h3>3.3.1、Origins配置</h3>

<p>（1）选择源</p>

<p><img alt="" class="has" height="388" src="https://img-blog.csdn.net/20180821175611283?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="391"></p>

<p>Origins选择Kafka Consumer</p>

<p><img alt="" class="has" height="95" src="https://img-blog.csdn.net/20180821175659458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="83"></p>

<p>（2）配置</p>

<p>General</p>

<p><img alt="" class="has" height="275" src="https://img-blog.csdn.net/20180821175936477?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>Kafka</p>

<p> </p>

<p><img alt="" class="has" height="573" src="https://img-blog.csdn.net/20180821180013483?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>Data Format</p>

<p><img alt="" class="has" height="329" src="https://img-blog.csdn.net/20180821180043681?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<h3>3.3.2、Destiations配置</h3>

<p>（1）选择目的</p>

<p><img alt="" class="has" height="319" src="https://img-blog.csdn.net/20180821180217344?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="286"></p>

<p>Destiations选择ElasticSearch</p>

<p><img alt="" class="has" height="95" src="https://img-blog.csdn.net/20180821180318919?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="89"></p>

<p>（2）配置</p>

<p>General</p>

<p><img alt="" class="has" height="390" src="https://img-blog.csdn.net/20180821180348606?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>ElasticSearch</p>

<p><img alt="" class="has" height="813" src="https://img-blog.csdn.net/20180821180424480?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<h3>3.3.3、Pipeline</h3>

<p>(1)</p>

<p><img alt="" class="has" height="313" src="https://img-blog.csdn.net/20180821180746915?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="406"></p>

<p>(2)</p>

<p>General</p>

<p><img alt="" class="has" height="784" src="https://img-blog.csdn.net/20180821180812964?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>Error Records</p>

<p><img alt="" class="has" height="230" src="https://img-blog.csdn.net/20180821180833835?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<p><img alt="" class="has" height="349" src="https://img-blog.csdn.net/20180821180944244?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<h2>3.4、效果</h2>

<p><img alt="" class="has" height="913" src="https://img-blog.csdn.net/20180821181410617?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzNjA2ODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>            </div>
                </div>