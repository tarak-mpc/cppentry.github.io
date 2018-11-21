---
layout:     post
title:      docker——kafka集群安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wild46cat/article/details/78081051				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>docker——kafka集群安装</h1>
<div><span style="font-size:18px;">本文主要介绍如何在docker中搭建kafka集群。</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">1、下载kafka镜像：</span></div>
<div><span style="font-size:18px;">docker pull wurstmeister/kafka<br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">2、编写docker-compose.yml文件</span></div>
<div><span style="font-size:18px;"></span><pre><code class="language-plain">version: '2'
services:
  kafka:
    build: .
    image: wurstmeister/kafka
    ports:
      - "39094:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 192.168.0.66
      KAFKA_ADVERTISED_PORT: 9092
      KAFKA_CREATE_TOPICS: "test:1:1"
      KAFKA_ZOOKEEPER_CONNECT: 192.168.0.66:62181,192.168.0.66:62182,192.168.0.66:62183
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock</code></pre><br>
需要注意的是这里的KAFKA_ADVERTTISED_PORT:9092如果没有进行设置，会报错。</div>
<div><span style="font-size:18px;">使用命令启动kafka：</span></div>
<div><span style="font-size:18px;">docker-compose up -d</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">3、查看docker中kafka的状态：</span></div>
<div><span style="font-size:18px;">docker ps -a</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">4、如果发现kafka没有启动， 可以都能通过命令查看日志。</span></div>
<div><span style="font-size:18px;">docker logs containedID</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">5、扩展成集群(首先需要修改端口，不然会出现端口已经被占用)：</span></div>
<div><span style="font-size:18px;">docker-compose scale kafka=2 </span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">6、再次修改端口，添加主机：</span></div>
<div><span style="font-size:18px;">docker-compose scale kafka=3</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">7、查看状态。</span></div>
<div><span style="font-size:18px;">docker ps - a</span></div>
<div><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170924234713660?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
            </div>
                </div>