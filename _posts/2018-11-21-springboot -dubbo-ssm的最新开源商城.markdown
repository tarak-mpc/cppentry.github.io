---
layout:     post
title:      springboot -dubbo-ssm的最新开源商城
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/a1439226817/article/details/77776949				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>下载地址  <a href="http://git.oschina.net/catshen/zscat_sw" rel="nofollow">http://git.oschina.net/catshen/zscat_sw</a></h2>
<div>最新全部开源 springboot -dubbo-ssm的最新开源商城</div>
<div><span style="background-color:rgb(255,255,255);color:rgb(64,72,91);font-size:15px;line-height:25px;">技术选型</span></div>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li>
<p style="line-height:25px;">
1、后端</p>
</li><li>
<p style="line-height:25px;">
核心框架：Spring Framework 4.0,kafka redis,mongodb,storm</p>
</li><li>
<p style="line-height:25px;">
分布式框架： zookeeper dubbox2.8.4 分布式锁,springboot</p>
</li><li>
<p style="line-height:25px;">
分布式调用链 zikpin brove</p>
</li><li>
<p style="line-height:25px;">
分布式日志分析 elk Elasticsearch，Logstash，Kibana</p>
</li><li>
<div class="white">
<div class="highlight">
<pre style="overflow:auto;font-family:Menlo, 'Liberation Mono', Consolas, 'DejaVu Sans Mono', 'Ubuntu Mono', 'Courier New', 'andale mono', 'lucida console', monospace;background-color:rgb(248,248,248);border:1px solid rgb(238,238,238);font-size:13px;line-height:19px;color:rgb(51,51,51);">Elasticsearch,solr,luence：搜索，提供分布式全文搜索引擎
</pre>
</div>
</div>
</li><li>
<p style="line-height:25px;">
　　Logstash: 日志收集，管理，存储</p>
</li><li>
<p style="line-height:25px;">
　　Kibana ：日志的过滤web 展示</p>
</li><li>
<p style="line-height:25px;">
分布式监控 dubbo-monitor dubbo-admin</p>
</li><li>
<p style="line-height:25px;">
mq通信框架 kafka redis mongodb</p>
</li><li>
<div class="white">
<div class="highlight">
<pre style="overflow:auto;font-family:Menlo, 'Liberation Mono', Consolas, 'DejaVu Sans Mono', 'Ubuntu Mono', 'Courier New', 'andale mono', 'lucida console', monospace;background-color:rgb(248,248,248);border:1px solid rgb(238,238,238);font-size:13px;line-height:19px;color:rgb(51,51,51);">分库分表 sharding jdbc
</pre>
</div>
</div>
</li><li>
<ul class="task-list"><li>安全框架：Apache Shiro 1.2</li></ul></li><li>
<p style="line-height:25px;">
视图框架：Spring MVC 4.0</p>
</li><li>
<p style="line-height:25px;">
服务端验证：Hibernate Validator 5.1</p>
</li><li>
<p style="line-height:25px;">
任务调度：Spring Task 4.0</p>
</li><li>
<p style="line-height:25px;">
持久层框架：MyBatis 3.2</p>
</li><li>
<p style="line-height:25px;">
数据库连接池：Alibaba Druid 1.0</p>
</li><li>
<p style="line-height:25px;">
缓存框架：Ehcache 2.6、Redis</p>
</li><li>
<p style="line-height:25px;">
Luence搜索引擎</p>
</li><li>
<p style="line-height:25px;">
日志管理：SLF4J 1.7、Log4j2 logback</p>
</li><li>
<p style="line-height:25px;">
工具类：Apache Commons、Jackson 2.2、Xstream 1.4、Dozer 5.3、POI 3.9</p>
</li><li>
<p style="line-height:25px;">
2、前端</p>
</li><li>
<p style="line-height:25px;">
JS框架：jQuery 1.9。</p>
</li><li>
<p style="line-height:25px;">
CSS框架：bootstrap ace admin框架界面。</p>
</li><li>
<p style="line-height:25px;">
客户端验证：JQuery Validation Plugin 1.11。</p>
</li><li>
<p style="line-height:25px;">
富文本：CKEcitor</p>
</li><li>
<p style="line-height:25px;">
文件管理：CKFinder</p>
</li><li>
<p style="line-height:25px;">
百度 web upload 图片上传插件手机端框架：Jingle</p>
</li><li>
<p style="line-height:25px;">
数据表格：jqGrid</p>
</li><li>
<p style="line-height:25px;">
对话框：jQuery jBox</p>
</li><li>
<p style="line-height:25px;">
下拉选择框：jQuery Select2</p>
</li><li>
<p style="line-height:25px;">
树结构控件：jQuery zTree</p>
</li><li>
<p style="line-height:25px;">
日期控件： My97DatePicker</p>
</li></ul><div>
<h1>zscat</h1>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li>
<p style="line-height:25px;">
├── sw-common -- SSM框架公共模块（kafka提供者）</p>
</li><li>
<p style="line-height:25px;">
├── sw-mq -- 消息系统（已集成kafka）</p>
</li><li>
<p style="line-height:25px;">
├── sw-storm -- storm系统（可以kafka，redis，hbase，mysql，mongodb数据）</p>
</li><li>
<p style="line-height:25px;">
├── sw-schedule -- 分布式定时任务</p>
</li><li>
<p style="line-height:25px;">
├── dubbo-cache-starter --dubbo自定义缓存（redis,ehcache,mixcache）</p>
</li><li>
<p style="line-height:25px;">
├── app-monitor --dubbo服务监控和统计</p>
</li><li>
<p style="line-height:25px;">
├── sw_manager -- 后台管理模板</p>
</li><li>
<p style="line-height:25px;">
├── sw-portl -- 官网门户展示</p>
</li><li>
<p style="line-height:25px;">
├── sw-search-- search管理系统（实现了luence，solr两种搜索）</p>
</li><li>
<p style="line-height:25px;">
| ├── search-api -- search相关的service</p>
</li><li>
<p style="line-height:25px;">
| ├── search-service -- search相关的service实现 dubbo服务</p>
</li><li>
<p style="line-height:25px;">
| ├── search-web -- search消费者 前端展示</p>
</li><li>
<p style="line-height:25px;">
├── sw-blog-- blog管理系统</p>
</li><li>
<p style="line-height:25px;">
| ├── blog-api -- blog相关的service</p>
</li><li>
<p style="line-height:25px;">
| ├── blog-service -- blog相关的service实现 dubbo服务</p>
</li><li>
<p style="line-height:25px;">
| ├── blog-web -- blog消费者 前端展示</p>
</li><li>
<p style="line-height:25px;">
├── sw-cms-- cms管理系统</p>
</li><li>
<p style="line-height:25px;">
| ├── cms-api -- cms相关的service</p>
</li><li>
<p style="line-height:25px;">
| ├── cms-service -- cms相关的service实现 dubbo服务</p>
</li><li>
<p style="line-height:25px;">
| ├── cms-web -- cms消费者 前端展示</p>
</li><li>
<p style="line-height:25px;">
├── sw-shop-- shop管理系统</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-goods-api -- shop商品相关的service</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-goods-service -- shop商品相关的service实现 dubbo服务</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-member-api -- shop会员相关的service</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-member-service -- shop会员相关的service实现 dubbo服务</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-order-api -- shop订单相关的service</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-order-service -- shop订单相关的service实现 dubbo服务</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-web -- shop消费者 前端展示</p>
</li><li>
<p style="line-height:25px;">
| ├── shop-h5-- h5消费者 前端展示</p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/blob/master/sw-search/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">搜索模块具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/sw_manager/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">后台管理具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/sw_blog/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">blog管理具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/sw_cms/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">cms管理具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/sw_shop/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">商城管理具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/sw-mq/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">mq管理具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/dubbo-cache-starter/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">dubbo缓存具体介绍</a></p>
</li><li>
<p style="line-height:25px;">
<a href="http://git.oschina.net/catshen/zscat_sw/tree/master/dubbo-monitor/README.md" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">dubbo监控和统计具体介绍</a></p>
</li></ul><p style="line-height:25px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);">
1.项目部署，根据doc目录下的 zsboot.sql，weixin.sql，分别创建数据库，相关数据库配置 参考application.properties 先安装 spring-boot-starter-dubbo模块到本地 加群下载 zscat-tools.jar 解压运行zscat-tools 下面的run.bat 同时启动zookeep Redis nginx，默认配置 参考application.properties修改</p>
<h3><a id="2blog模块为例" class="anchor" href="http://git.oschina.net/catshen/zscat_sw#2blog%E6%A8%A1%E5%9D%97%E4%B8%BA%E4%BE%8B" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;display:block;margin-left:-20px;"></a>2.blog模块为例</h3>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li>a.启动blog-services下面的BlogServiceApplication主类，启动blog的dubbo服务</li><li>b.启动blog-web下面的BlogWebApplication主类，访问 <a href="http://localhost:2001/front/blog/indexs" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2001/front/blog/indexs</a></li></ul><h3><a id="3cms模块为例" class="anchor" href="http://git.oschina.net/catshen/zscat_sw#3cms%E6%A8%A1%E5%9D%97%E4%B8%BA%E4%BE%8B" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;display:block;margin-left:-20px;"></a>3.cms模块为例</h3>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li>a.启动cms-services下面的cmsServiceApplication主类，启动blog的dubbo服务</li><li>b.启动cms-web下面的cmsWebApplication主类，访问 <a href="http://localhost:2001/web/cms/index" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2001/web/cms/index</a> <a href="http://localhost:2001/wap/cms/index" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2001/wap/cms/index</a></li></ul><h3><a id="4shop模块为例" class="anchor" href="http://git.oschina.net/catshen/zscat_sw#4shop%E6%A8%A1%E5%9D%97%E4%B8%BA%E4%BE%8B" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;display:block;margin-left:-20px;"></a>4.shop模块为例</h3>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li>a.启动shop-services下面的ShopServiceApplication主类，启动blog的dubbo服务</li><li>b.启动shop-web下面的ShopWebApplication主类，访问 <a href="http://localhost:2007/front" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2007/front</a> <a href="http://localhost:2007/youhong" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2007/youhong</a></li><li>c.启动shop-h5下面的ShopWebApplication主类，访问 <a href="http://localhost:2006/wap1" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2006/wap1</a></li></ul><h3><a id="5search模块为例" class="anchor" href="http://git.oschina.net/catshen/zscat_sw#5search%E6%A8%A1%E5%9D%97%E4%B8%BA%E4%BE%8B" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;display:block;margin-left:-20px;"></a>5.search模块为例</h3>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li>a.启动search-services下面的SearchServiceApplication主类，启动blog的dubbo服务</li><li>b.启动search-web下面的SearchWebApplication主类，访问 <a href="http://localhost:2008/" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2008</a></li></ul><h3><a id="6启动sw_manager下面的-gunsapplication主类访问-httplocalhost--admin--111111" class="anchor" href="http://git.oschina.net/catshen/zscat_sw#6%E5%90%AF%E5%8A%A8sw_manager%E4%B8%8B%E9%9D%A2%E7%9A%84-gunsapplication%E4%B8%BB%E7%B1%BB%E8%AE%BF%E9%97%AE-httplocalhost--admin--111111" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;display:block;margin-left:-20px;"></a>6.启动sw_manager下面的
 GunsApplication主类，访问 <a href="http://localhost/" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost</a> admin 111111</h3>
<h3><a id="7启动sw_portl下面的-portlwebapplication主类访问-httplocalhost2009gwindex" class="anchor" href="http://git.oschina.net/catshen/zscat_sw#7%E5%90%AF%E5%8A%A8sw_portl%E4%B8%8B%E9%9D%A2%E7%9A%84-portlwebapplication%E4%B8%BB%E7%B1%BB%E8%AE%BF%E9%97%AE-httplocalhost2009gwindex" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;display:block;margin-left:-20px;"></a>7.启动sw_portl下面的
 PortlWebApplication主类，访问 <a href="http://localhost:2009/gw/index" rel="nofollow" style="color:rgb(9,94,171);text-decoration:none;background-color:transparent;">http://localhost:2009/gw/index</a></h3>
<div><br><br>
-  1.解压solr 放入d盘<br>
-   2.解压tomcat，双击bin下面的startup.bat 启动tomcat<br>
-   3.访问http://localhost:8080/solr/#/core1/query 可以测试<br>
-   <br>
-   http://localhost:2008/index1/init  会将blog的文章数据创建索引<br>
-   http://localhost:2008/index1/init1  会将cms 的文章数据创建索引<br>
-   然后在输入框可以输入数据查询<br>
![输入图片说明](https://git.oschina.net/uploads/images/2017/0829/201626_9af43d02_134431.png "solr.png")<br>
![输入图片说明](https://git.oschina.<br></div>
<ul class="task-list" style="line-height:24px;color:rgb(64,72,91);font-size:15px;background-color:rgb(255,255,255);"><li><a href="https://gitee.com/jmdhappy/xxpay-master" rel="nofollow" style="line-height:25px;color:rgb(9,94,171);text-decoration:none;background-color:transparent;">https://gitee.com/jmdhappy/xxpay-master</a><span style="line-height:25px;"> 支付</span></li></ul><img src="https://img-blog.csdn.net/20170901202032909?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTE0MzkyMjY4MTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20170901202052380?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTE0MzkyMjY4MTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
            </div>
                </div>