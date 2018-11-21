---
layout:     post
title:      HBase增、删、改、查——hbase shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wild46cat/article/details/53284504				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>HBase增、删、改、查——hbase shell</h1>
<div><span style="font-size:18px;">本文主要参考：</span></div>
<div><span style="font-size:18px;">1、官方文档：http://hbase.apache.org/book.html</span></div>
<div><span style="font-size:18px;">2、http://blog.csdn.net/lifuxiangcaohui/article/details/39894265</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">下面介绍一下，HBase的增删改查。其实，最开始在安装的时候，就已经介绍了基本的HBase shell命令(http://blog.csdn.net/wild46cat/article/details/53214159)，但是因为那时的侧重点是测试安装是否成功，对于实际的理解和使用帮助并不大，所以现在从使用的角度对HBase的增加删除修改查询进行介绍。</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">1、创建表：</span></div>
<div><span style="font-size:18px;">create 'blog','article','user'</span></div>
<div><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20161122130049085?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-size:18px;">2、查看表</span></div>
<div><span style="font-size:18px;">list 'blog'</span></div>
<div><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20161122130126867?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">3、在表中添加数据（HBase中的小技巧，能够利用ruby的面向对象）：</span></div>
<div><span style="font-size:18px;">b = get_table 'blog'</span></div>
<div><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20161122130213180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">b.put 'r1','article:title','The HBase Test'</span></div>
<div><img src="https://img-blog.csdn.net/20161122130455041?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">4、查看插入的数据：</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.scan或者b.get 'r1'</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122130628573?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><br></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">使用put进行插入</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122130949878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122131032910?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">5、查询'blog'表的情况：</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.scan或者b.get 'r1'</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122131322461?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">6、修改article:title</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">put 'r1','article:title','title after change'</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122131543905?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">7、查看是否已经修改了</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.scan或者b.get 'r1'</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122131654419?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">8、删除某个COLUMN，比如删除article:createdate</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.delete 'r1','article:createdate'</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122131940547?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">9、查看现在表中数据</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.scan或者b.get 'r1'</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122132042675?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">10、删除表（先禁用表，在删除表）</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.disable</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">b.drop</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20161122132208204?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">这里没有讲解HBase是如何存储数据的，还有HBase的存储模型是什么样的。但是通过这个例子，应该能够加深对HBase的体会。</span></div>
            </div>
                </div>