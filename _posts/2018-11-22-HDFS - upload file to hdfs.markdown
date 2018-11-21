---
layout:     post
title:      HDFS - upload file to hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/PeersLee/article/details/82847114				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <pre class="has">
<code>#!/bin/bash

export JAVA_HOME=/data/jdk1.8.0_111
export HADOOP_HOME=/data/hadoop-2.6.5
export PATH=${JAVA_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:$PATH

export JRE_HOME=${JAVA_HOME}/jre
export CLASS_HOME=.:${JAVA_HOME}/lib:${JRE_HOME}/lib

log_src_dir=/home/lpl/logs/log/
log_2_upload_dir=/home/lpl/logs/2upload/

hdfs_log_dir=/data/logs/2018

ls $log_src_dir |while read log_file
do
    # if [ "$log_file" = "access.log" ]; then
    if [[ "$log_file"  == access.log* ]]; then
        now=`date "+%Y-%m-%d-%H-%M-%S-%N"`
        source_file=$log_src_dir$log_file 
        target_file=$log_2_upload_dir$now

        #cp $source_file $target_file 
        mv $source_file $target_file 

        #echo "copy "$source_file" -&gt; "$target_file
        echo "move "$source_file" -&gt; "$target_file
    fi
done

ls $log_2_upload_dir |grep -v "have_uploaded" |while read log_file
do
    now=`date "+%Y-%m-%d.%H:%M:%S.%N"`
    source_file=$log_2_upload_dir$log_file
    target_file=$log_2_upload_dir"have_uploaded."$now

    hadoop fs -put $source_file $hdfs_log_dir
    mv $source_file $target_file

    echo "move "$source_file" -&gt; "$target_file
done

#ls $log_2_upload_dir |while read log_file do
    #echo $log_file
#done
</code></pre>

<p> </p>            </div>
                </div>