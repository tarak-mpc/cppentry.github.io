---
layout:     post
title:      redhat kafka 自启动脚本
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre style="border:0px;font-family:Consolas, 'Liberation Mono', Courier, monospace;color:rgb(51,51,51);line-height:16px;"></pre><div class="line" id="LC1" style="border:0px;"><pre><code class="language-plain">#!/bin/sh
#
# chkconfig: 345 99 01
# description: Kafka
#
# File : Kafka
#
# Description: Starts and stops the Kafka server
#

source /etc/rc.d/init.d/functions

KAFKA_HOME=/opt/kafka
KAFKA_USER=kafka
# See how we were called.
case "$1" in

  start)
    echo -n "Starting Kafka:"
    /sbin/runuser $KAFKA_USER -c "nohup $KAFKA_HOME/bin/kafka-server-start.sh $KAFKA_HOME/config/server.properties &gt; /var/log/kafka/server.out 2&gt; /var/log/kafka/server.err &amp;"
    echo " done."
    exit 0
    ;;

  stop)
    echo -n "Stopping Kafka: "
    /sbin/runuser  $KAFKA_USER  -c "ps -ef | grep kafka.Kafka | grep -v grep | awk '{print \$2}' | xargs kill"
    echo " done."
    exit 0
    ;;
  hardstop)
    echo -n "Stopping (hard) Kafka: "
    /sbin/runuser  $KAFKA_USER  -c "ps -ef | grep kafka.Kafka | grep -v grep | awk '{print \$2}' | xargs kill -9"
    echo " done."
    exit 0
    ;;

  status)
    c_pid=`ps -ef | grep kafka.Kafka | grep -v grep | awk '{print $2}'`
    if [ "$c_pid" = "" ] ; then
      echo "Stopped"
      exit 3
    else
      echo "Running $c_pid"
      exit 0
    fi
    ;;

  restart)
    stop
    start
    ;;

  *)
    echo "Usage: cassandra {start|stop|hardstop|status|restart}"
    exit 1
    ;;

esac</code></pre><br></div><div class="line" id="LC59" style="border:0px;"></div><div class="line" style="border:0px;">转自：https://github.com/edwardcapriolo/kafka-rpm/blob/master/SOURCES/kafka.init</div>
            </div>
                </div>