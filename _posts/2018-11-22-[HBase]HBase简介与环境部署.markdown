---
layout:     post
title:      [HBase]HBase简介与环境部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>1 HBase简介及其在大数据生态圈的位置</h1>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-e1703fcdcbf4c94f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-6b67f45bf103c694.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p>行列表举例</p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-48192e1a428c3b4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p>行 / 列式数据表,即把列放在一起</p>

<p><br>
列式则适合于大批量查询</p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-393dba95c3de5d31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-0daa3dfb8bacfe24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-a07c848cd2cd927e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<h1>2 HBase数据存储模型及与关系型数据库的区别</h1>

<ul><li>
	<p>分布式基础理论</p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-a0af62ab58e8ec10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-c227babee5667eee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	</li>
	<li>
	<p>HBase 概念</p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-c7232e25170dd313.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-d161df20d59f5978.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-8808c85fefdce599.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-2dddc13bed531f7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	</li>
	<li>
	<p>HBase与传统数据库的区别</p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-682df959ff856085.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-0731df41a42a424c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

	<p>存储例子</p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-ea939e7bc9a3ba88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

	<p>HBase 存储结果</p>
	 

	<p>HBase 数据模型</p>
	 

	<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-642d6790afe52dd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>
	</li>
</ul><h1>3 Hadoop伪分布式集群安装</h1>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-0e46a4fe2069a8d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-2ab4bc491b5a4e7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-24bf5986276b7c6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p>配置 hadoop.env.sh 的 Java 环境变量</p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-256fca382eb6f136.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-403b3c308e692e57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-935ed0d37f03a6a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p>新建一个数据存储目录</p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/4685968-616082f46822f0b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></p>

<p></p>

<p> </p>

<p><br><br>
作者：芥末无疆sss<br>
链接：https://www.jianshu.com/p/dfa7488c5414<br>
來源：简书<br>
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。</p>            </div>
                </div>