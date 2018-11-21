---
layout:     post
title:      kafka zookeeper graylog filelog 监控配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>先简单记录下操作步骤，日后把详细原理给大家讲一下。</p>
<p>本文讲解单kafka实例和单zookeeper实例的过程。</p>
<p>下载 kafka 并解压。</p>
<p>运行kafka包里的 zookeeper程序。</p>
<p>运行kafka包里的 kafka程序。</p>
<p>创建 一个topic：oraclelog。</p>
<p>在graylog中创建 kafka raw text input，创建其他kafka input会报错，目前没有研究报错原因。</p>
<p>在 graylog 配置中输入 zookeeper的监听地址，输入topic：^oraclelog$ ，其他默认。</p>
<p>以上步骤配置完成后，graylog可以接收kafka数据了。</p>
<p>报错处理：目前遇到graylog 后台日志/var/log/graylog/server/current 报错 localhost：9020 reufuse connection。该端口为 kafka的监听端口，应该是graylog 端连上zookeeper后获取metadata的信息给出 localhost：9092。于是找到kafka配置文件，将 listener配置由默认localhost 改为 kafka IP，重启kafka问题解决。</p>
            </div>
                </div>