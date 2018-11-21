---
layout:     post
title:      Hadoop MapReduce操作Hbase范例学习（TableMapReduceUtil）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lan12334321234/article/details/70049937				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                Hbase里的数据量一般都小不了，<span style="color:#FF0000;"><strong>因此MapReduce跟Hbase就成了天然的好搭档。</strong></span>
<br><br><span style="font-size:large;"><span style="color:#FF0000;"><strong>1.ZK授权表</strong></span></span>
<br><br>首先一点来说，<span style="color:#FF0000;"><strong>Hbase是强依赖于ZK的。</strong></span>博主所在的team，就经常出现ZK连接数太多被打爆然后Hbase挂了的情况。<strong>一般在访问Hbase表之前，需要通过访问ZK得到授权：</strong>
<br><br><pre><code class="language-java">
/**
     * 为hbase表授权。
     *
     * @param tableConfigKey 任意一个字符串。
     * @param tableName 需要授权的表名, scan涉及到的表不需要额外授权。
     * @param job 相关job。
     * @throws IOException
     */
    public static void initAuthentication(String tableConfigKey, String tableName, Job job) throws IOException {
        Configuration peerConf = job.getConfiguration();
        peerConf.set(tableConfigKey, tableName);
        ZKUtil.applyClusterKeyToConf(peerConf, tableName);
        if (User.isHBaseSecurityEnabled(peerConf)) {
            LOGGER.info("Obtaining remote user authentication token with table:{}", tableName);
            try {
                User.getCurrent().obtainAuthTokenForJob(peerConf, job);
            } catch (InterruptedException ex) {
                LOGGER.info("Interrupted obtaining remote user authentication token");
                LOGGER.error("Obtained remote user authentication token with table:{}, error:\n", tableName, ex);
                Thread.interrupted();
            }
            LOGGER.info("Obtained remote user authentication token with table:{}", tableName);
        }
    }
</code></pre>
<br><br><span style="font-size:large;"><strong>2.thrift对象转化</strong></span>
<br><br>本例中操作的对象为XX表，Column Family为”P”, Qualifer 为”P”与”C”，里面对应的value都是thrift对象。其中”P”对应的thrift对象为:
<br><br><pre><code class="language-java">struct UserProfile {
    1: optional byte sex; 
    2: optional i32 age;
    3: optional string phoneBrand;
    4: optional string locationProvince;
}</code></pre>
<br>“C”对应的thrift对象为:
<br><br><pre><code class="language-java">struct UserClickInfo {
    1: required i32 totolAck;
    2: required i32 totalClick;
    3: optional map&lt;i64, map&lt;string, i32&gt;&gt; ackClick;
}</code></pre>
<br><br>这个时候我们就需要经常将Bytes转化为thrift对象，通用的方法为：
<br><br><pre><code class="language-java">/**
     * convert byte array to thrift object.
     *
     * @param &lt;T&gt; type of thrift object.
     * @param thriftObj an thrift object.
     * @return byte array if convert succeeded, &lt;code&gt;null&lt;/code&gt; if convert failed.
     * @throws TException
     */
    public static final &lt;T extends TBase&lt;T, ?&gt;&gt; T convertBytesToThriftObject(byte[] raw, T thriftObj) throws TException {
        if (ArrayUtils.isEmpty(raw)) {
            return null;
        }
        Validate.notNull(thriftObj, "thriftObj");

        TDeserializer serializer = new TDeserializer(new TBinaryProtocol.Factory());
        serializer.deserialize(thriftObj, raw);
        return thriftObj;
    }</code></pre>
<br><br><span style="font-size:large;"><strong>3.Map阶段读取Hbase里的数据</strong></span>
<br><br><span style="color:#FF0000;"><strong>在Map阶段对Hbase表扫描，得出数据</strong></span>
<br><br><pre><code class="language-java">
//输出的KV均为Text
    static class ReadMapper extends TableMapper&lt;Text,Text&gt; {

        @Override
        protected void map(ImmutableBytesWritable key, Result res, Context context) throws IOException,InterruptedException{
            String uuid = StringUtils.reverse(Bytes.toString(key.copyBytes()));
            if (res == null || res.isEmpty()) return;
            res.getFamilyMap(USER_FEATURE_COLUMN_FAMILY);


            for(KeyValue kv:res.list()) {
                String qualifier = Bytes.toString(kv.getQualifier());
                //String qualifier = kv.getKeyString();
                if(qualifier.equals("P")) {

                    try {
                        UserProfile userProfile = new UserProfile();
                        convertBytesToThriftObject(kv.getValue(), userProfile);
                        String profileRes = userProfile.getAge() + "," + userProfile.getSex() + ","
                                + userProfile.getPhoneBrand() + "," + userProfile.getLocationProvince();
                        context.write(new Text(uuid),new Text(profileRes));
                    } catch (Exception ex) {}
                }
                else if(qualifier.equals("C")) {
                    UserClickInfo userClickInfo = new UserClickInfo();
                    try {
                        convertBytesToThriftObject(kv.getValue(), userClickInfo);
                        Map&lt;Long,Map&lt;String,Integer&gt;&gt; resMap = userClickInfo.getAckClick();
                        for(Map.Entry&lt;Long,Map&lt;String,Integer&gt;&gt; entry:resMap.entrySet()) {
                            String appid = String.valueOf(entry.getKey());
                            int click = entry.getValue().get("click");
                            int ack = entry.getValue().get("ack");
                            String all = appid + "," + String.valueOf(click) + "," + String.valueOf(ack);
                            context.write(new Text(uuid),new Text(all));
                        }
                        int allClick = userClickInfo.getTotalClick();
                        int allAck = userClickInfo.getTotolAck();
                        String allNum = "999," + String.valueOf(allClick) + "," + String.valueOf(allAck);
                        context.write(new Text(uuid),new Text(allNum));
                    } catch (Exception ex) {}
                }
            }
        }
    }
</code></pre>
<br><br><span style="font-size:large;"><strong>4.run方法里配置相关驱动</strong></span>
<br><br>run方法里需要配置一些相关的参数，保证任务的顺利进行。
<br><br>其中， TableMapReduceUtil.addDependencyJars 方法添加了完成任务一些必要的类。
<br><br><pre><code class="language-java">public int run(String[] args) throws Exception{
        Configuration conf = HBaseConfiguration.create();

        Job job = Job.getInstance(conf,"read_data_from_hbase");
        job.setJarByClass(ReadDataFromHbase.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);
        job.setReducerClass(ReadReducer.class);
        job.setSpeculativeExecution(false);

        TableMapReduceUtil.addDependencyJars(job.getConfiguration(),StringUtils.class, TimeUtils.class, Util.class,
                CompressionCodec.class, TStructDescriptor.class, ObjectMapper.class, CompressionCodecName.class, BytesInput.class);

        Scan scan = new Scan();
        //对整个CF扫描
        scan.addFamily(USER_FEATURE_COLUMN_FAMILY);

        String table = "XXX";
        initAuthentication(table,table,job);
        TableMapReduceUtil.initTableMapperJob(table,
                scan,
                ReadMapper.class,
                Text.class,
                Text.class,
                job);

        String output = "";
        FileSystem.get(job.getConfiguration()).delete(new Path(output), true);
        FileOutputFormat.setOutputPath(job,new Path(output));

        return job.waitForCompletion(true) ? 0 : 1;
    }</code></pre>
<br><br><span style="font-size:large;"><strong>5.完整的代码</strong></span>
<br><br><span style="color:#0000FF;"><strong>TableMapReduceUtil.initTableMapperJob把HBase表中的数据注入到Mapper中</strong></span>
<br><br><pre><code class="language-java">package com.xiaomi.xmpush.mr_job_and_tools.task;

import com.twitter.elephantbird.thrift.TStructDescriptor;
import XXX.XXX.XXX.XXX.common.util.TimeUtils;
import XXX.XXX.XXX.thrift.UserClickInfo;
import XXX.XXX.XXX.thrift.UserProfile;
import org.apache.commons.lang.ArrayUtils;
import org.apache.commons.lang.Validate;
import org.apache.commons.lang3.StringUtils;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.KeyValue;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.security.User;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.hbase.zookeeper.ZKUtil;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;
import org.apache.parquet.bytes.BytesInput;
import org.apache.parquet.format.CompressionCodec;
import org.apache.parquet.format.Util;
import org.apache.parquet.hadoop.metadata.CompressionCodecName;
import org.apache.thrift.TBase;
import org.apache.thrift.TDeserializer;
import org.apache.thrift.TException;
import org.apache.thrift.protocol.TBinaryProtocol;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import shaded.parquet.org.codehaus.jackson.map.ObjectMapper;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

/**
 * Created by WangLei on 17-3-13.
 */
public class ReadDataFromHbase extends Configured implements Tool{

    private static final Logger LOGGER = LoggerFactory.getLogger(ReadDataFromHbase.class);
    public static final byte[] USER_FEATURE_COLUMN_FAMILY = Bytes.toBytes("P");

    /**
     * convert byte array to thrift object.
     *
     * @param &lt;T&gt; type of thrift object.
     * @param thriftObj an thrift object.
     * @return byte array if convert succeeded, &lt;code&gt;null&lt;/code&gt; if convert failed.
     * @throws TException
     */
    public static final &lt;T extends TBase&lt;T, ?&gt;&gt; T convertBytesToThriftObject(byte[] raw, T thriftObj) throws TException {
        if (ArrayUtils.isEmpty(raw)) {
            return null;
        }
        Validate.notNull(thriftObj, "thriftObj");

        TDeserializer serializer = new TDeserializer(new TBinaryProtocol.Factory());
        serializer.deserialize(thriftObj, raw);
        return thriftObj;
    }


    /**
     * 为hbase表授权。
     *
     * @param tableConfigKey 任意一个字符串。
     * @param tableName 需要授权的表名, scan涉及到的表不需要额外授权。
     * @param job 相关job。
     * @throws IOException
     */
    public static void initAuthentication(String tableConfigKey, String tableName, Job job) throws IOException {
        Configuration peerConf = job.getConfiguration();
        peerConf.set(tableConfigKey, tableName);
        ZKUtil.applyClusterKeyToConf(peerConf, tableName);
        if (User.isHBaseSecurityEnabled(peerConf)) {
            LOGGER.info("Obtaining remote user authentication token with table:{}", tableName);
            try {
                User.getCurrent().obtainAuthTokenForJob(peerConf, job);
            } catch (InterruptedException ex) {
                LOGGER.info("Interrupted obtaining remote user authentication token");
                LOGGER.error("Obtained remote user authentication token with table:{}, error:\n", tableName, ex);
                Thread.interrupted();
            }
            LOGGER.info("Obtained remote user authentication token with table:{}", tableName);
        }
    }

    static class ReadMapper extends TableMapper&lt;Text,Text&gt; {

        @Override
        protected void map(ImmutableBytesWritable key, Result res, Context context) throws IOException,InterruptedException{
            String uuid = StringUtils.reverse(Bytes.toString(key.copyBytes()));
            if (res == null || res.isEmpty()) return;
            res.getFamilyMap(USER_FEATURE_COLUMN_FAMILY);


            for(KeyValue kv:res.list()) {
                String qualifier = Bytes.toString(kv.getQualifier());
                //String qualifier = kv.getKeyString();
                if(qualifier.equals("P")) {

                    try {
                        UserProfile userProfile = new UserProfile();
                        convertBytesToThriftObject(kv.getValue(), userProfile);
                        String profileRes = userProfile.getAge() + "," + userProfile.getSex() + ","
                                + userProfile.getPhoneBrand() + "," + userProfile.getLocationProvince();
                        context.write(new Text(uuid),new Text(profileRes));
                    } catch (Exception ex) {}
                }
                else if(qualifier.equals("C")) {
                    UserClickInfo userClickInfo = new UserClickInfo();
                    try {
                        convertBytesToThriftObject(kv.getValue(), userClickInfo);
                        Map&lt;Long,Map&lt;String,Integer&gt;&gt; resMap = userClickInfo.getAckClick();
                        for(Map.Entry&lt;Long,Map&lt;String,Integer&gt;&gt; entry:resMap.entrySet()) {
                            String appid = String.valueOf(entry.getKey());
                            int click = entry.getValue().get("click");
                            int ack = entry.getValue().get("ack");
                            String all = appid + "," + String.valueOf(click) + "," + String.valueOf(ack);
                            context.write(new Text(uuid),new Text(all));
                        }
                        int allClick = userClickInfo.getTotalClick();
                        int allAck = userClickInfo.getTotolAck();
                        String allNum = "999," + String.valueOf(allClick) + "," + String.valueOf(allAck);
                        context.write(new Text(uuid),new Text(allNum));
                    } catch (Exception ex) {}
                }
            }
        }
    }

    static class ReadReducer extends Reducer&lt;Text,Text,Text,Text&gt; {
        @Override
        protected void reduce(Text key, Iterable&lt;Text&gt; values, Context context) throws IOException,InterruptedException{
            List&lt;String&gt; resultList = new ArrayList&lt;String&gt;();
            for(Text each:values) {
                resultList.add(each.toString());
            }
            String res = StringUtils.join(resultList,":");
            context.write(key,new Text(res));
        }
    }

    @Override
    public int run(String[] args) throws Exception{
        Configuration conf = HBaseConfiguration.create();

        Job job = Job.getInstance(conf,"read_data_from_hbase");
        job.setJarByClass(ReadDataFromHbase.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);
        job.setReducerClass(ReadReducer.class);
        job.setSpeculativeExecution(false);

        TableMapReduceUtil.addDependencyJars(job.getConfiguration(),StringUtils.class, TimeUtils.class, Util.class,
                CompressionCodec.class, TStructDescriptor.class, ObjectMapper.class, CompressionCodecName.class, BytesInput.class);

        Scan scan = new Scan();
        //对整个CF扫描
        scan.addFamily(USER_FEATURE_COLUMN_FAMILY);

        String table = "XXX";
        initAuthentication(table,table,job);
        TableMapReduceUtil.initTableMapperJob(table,
                scan,
                ReadMapper.class,
                Text.class,
                Text.class,
                job);

        String output = "";
        FileSystem.get(job.getConfiguration()).delete(new Path(output), true);
        FileOutputFormat.setOutputPath(job,new Path(output));

        return job.waitForCompletion(true) ? 0 : 1;
    }

    public static void main(String[] args) throws Exception{
        System.exit(ToolRunner.run(new ReadDataFromHbase(),args));
    }
}</code></pre>
<br><br>然后将代码打包，提交到集群上运行对应的shell脚本即可。
<br><br>转自：<a href="http://www.tuicool.com/articles/u6z6Nvb" rel="nofollow">http://www.tuicool.com/articles/u6z6Nvb</a>
<br>            </div>
                </div>