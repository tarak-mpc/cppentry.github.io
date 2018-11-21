---
layout:     post
title:      hadoop学习记（2）--HDFS+yarn+MapReduce关系与原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011320740/article/details/78870171				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>什么是hadoop?</strong></p>
<p>Hadoop无非就是：HDFS(文件系统)，yarn(任务调配)，mapReduce(<span style="color:rgb(51,51,51);">编程模型</span>，大数据并行运算)，我们安装完hadoop就已经包括了以上；</p>
<p>Hadoop集群其实就是HDFS集群，说到HDFS,下面来谈谈什么是HDFS</p>
<p><br></p>
<p><strong>HDFS：</strong>其实就是个文件系统，和fastDFS类似，像百度云，阿里云等就是个文件存储系统，当然一般如果仅仅是为了用来存储文件的话直接fastDFS这个就已经够了，HDFS目的不单单是用来存储文件这么简单，它还涉及分布式计算等。</p>
<p>HDFS工作原理，用文字有点难以表达，那么我就直接画个图吧:</p>
<p><img src="https://img-blog.csdn.net/20171222100820378" alt=""></p>
<p>HDFS分有NameNode和DataNode，NameNode是整个文件系统目录，基于内存存储，存储的是一些文件的详细信息，比如文件名、文件大小、创建时间、文件位置等。Datanode是文件的数据信息，也就是文件本身，不过是分割后的小文件。上面图已经有做了介绍了，这里就不再赘述了。</p>
<p><br></p>
<p><strong>Yarn：</strong><span style="color:rgb(51,51,51);">是一种新的</span><span style="color:rgb(51,51,51);"> Hadoop</span><span style="color:rgb(51,51,51);">资源管理器，它是一个通用资源管理系统，可为上层应用提供统一的资源管理和调度，它的引入为集群在利用率、资源统一管理和数据共享等方面带来了巨大好处。</span></p>
<p><br></p>
<p><strong>MapReduce：</strong><span style="color:rgb(51,51,51);">MapReduce</span><span style="color:rgb(51,51,51);">是一种编程模型，用于大规模数据集（大于</span><span style="color:rgb(51,51,51);">1TB</span><span style="color:rgb(51,51,51);">）的并行运算。概念</span><span style="color:rgb(51,51,51);">"Map</span><span style="color:rgb(51,51,51);">（映射）</span><span style="color:rgb(51,51,51);">"</span><span style="color:rgb(51,51,51);">和</span><span style="color:rgb(51,51,51);">"Reduce</span><span style="color:rgb(51,51,51);">（归约）</span><span style="color:rgb(51,51,51);">"</span><span style="color:rgb(51,51,51);">，是它们的主要思想，都是从函数式编程语言里借来的，还有从矢量编程语言里借来的特性。它极大地方便了编程人员在不会分布式并行编程的情况下，将自己的程序运行在</span>分布式系统<span style="color:#333333;">上。</span><span style="color:rgb(51,51,51);">当前的软件实现是指定一个</span><span style="color:rgb(51,51,51);">Map</span><span style="color:rgb(51,51,51);">（映射）函数，用来把一组键值对映射成一组新的键值对，指定并发的</span><span style="color:rgb(51,51,51);">Reduce</span><span style="color:rgb(51,51,51);">（归约）函数，用来保证所有映射的键值对中的每一个共享相同的键组。</span></p>
<p><span style="color:rgb(51,51,51);">Mapreduce</span><span style="color:rgb(51,51,51);">工作原理如下图：</span></p>
<p><span style="color:rgb(51,51,51);"> <img src="https://img-blog.csdn.net/20171222100838877" alt=""></span></p>
<p><span style="color:rgb(51,51,51);">MapReduce</span><span style="color:rgb(51,51,51);">所编写好的程序将跑在各个</span><span style="color:rgb(51,51,51);">DataNode</span><span style="color:rgb(51,51,51);">上，这里有个概念就是计算向数据移动，也就是</span><span style="color:rgb(51,51,51);">DataNode</span><span style="color:rgb(51,51,51);">的数据文件存储在这里，我的程序发送到</span><span style="color:rgb(51,51,51);">DataNode</span><span style="color:rgb(51,51,51);">节点上去读取数据和分析数据就好了。期间会有出现各个</span><span style="color:rgb(51,51,51);">DataNode</span><span style="color:rgb(51,51,51);">之间进行数据发送，比如说节点</span><span style="color:rgb(51,51,51);">DataNode1</span><span style="color:rgb(51,51,51);">进行这台机读取数据时进行</span><span style="color:rgb(51,51,51);">shuffle</span><span style="color:rgb(51,51,51);">时需要把相同的</span><span style="color:rgb(51,51,51);">key</span><span style="color:rgb(51,51,51);">作为一组调用一次</span><span style="color:rgb(51,51,51);">reduce</span><span style="color:rgb(51,51,51);">，那么如果这时当然会有一些同</span><span style="color:rgb(51,51,51);">key</span><span style="color:rgb(51,51,51);">的在其他节点</span><span style="color:rgb(51,51,51);">DataNode</span><span style="color:rgb(51,51,51);">上的，所以就需要进行数据传送。</span><span style="color:rgb(51,51,51);">Input</span><span style="color:rgb(51,51,51);">这里的</span><span style="color:rgb(51,51,51);">wordcount.txt</span><span style="color:rgb(51,51,51);">就是</span><span style="color:rgb(51,51,51);">DataNode</span><span style="color:rgb(51,51,51);">上的文件数据。</span><span style="color:rgb(51,51,51);">Split</span><span style="color:rgb(51,51,51);">阶段是</span><span style="color:rgb(51,51,51);">MapReduce</span><span style="color:rgb(51,51,51);">一定会执行的，这是它的规则，而</span><span style="color:rgb(51,51,51);">map</span><span style="color:rgb(51,51,51);">阶段就是我们必须进行手动干预的，也就是编码对数据进行分析，分析成</span><span style="color:rgb(51,51,51);">map</span><span style="color:rgb(51,51,51);">文件，然后再</span><span style="color:rgb(51,51,51);">shuffle</span><span style="color:rgb(51,51,51);">阶段中自发进行数据派送，规则是以同样的</span><span style="color:rgb(51,51,51);">key</span><span style="color:rgb(51,51,51);">为一组调用</span><span style="color:rgb(51,51,51);">reduce</span><span style="color:rgb(51,51,51);">阶段进行数据压缩，</span><span style="color:rgb(51,51,51);">reduce</span><span style="color:rgb(51,51,51);">也是进行手动干预的，我们编码进行数据计算，计算同</span><span style="color:rgb(51,51,51);">key</span><span style="color:rgb(51,51,51);">的个数，统计完后就可以输出一个文件出来了，这整个过程数据的传输都是放在</span><span style="color:rgb(51,51,51);">context</span><span style="color:rgb(51,51,51);">这个上下文中。下面是借鉴网上的一张图，</span><span style="color:rgb(51,51,51);">HDFS</span><span style="color:rgb(51,51,51);">与</span><span style="color:rgb(51,51,51);">MapReduce</span><span style="color:rgb(51,51,51);">之间的关系协助大概就是这么个意思。</span></p>
<p><img src="https://img-blog.csdn.net/20171222100917539" alt=""></p>
<p>以上纯属个人的一点浅薄认识，如有不对的地方望指正。</p>
            </div>
                </div>