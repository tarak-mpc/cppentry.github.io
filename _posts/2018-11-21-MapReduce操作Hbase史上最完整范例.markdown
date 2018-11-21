---
layout:     post
title:      MapReduce操作Hbase史上最完整范例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/bitcarmanlee/article/details/62055337				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hbase里的数据量一般都小不了，因此MapReduce跟Hbase就成了天然的好搭档。本文中，本博主将给出最详细的用MR读取Hbase中数据的实例。</p>



<h2 id="1zk授权表">1.ZK授权表</h2>

<p>首先一点来说，Hbase是强依赖于ZK的。博主所在的team，就经常出现ZK连接数太多被打爆然后Hbase挂了的情况。一般在访问Hbase表之前，需要通过访问ZK得到授权：</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 为hbase表授权。
     *
     *<span class="hljs-javadoctag"> @param</span> tableConfigKey 任意一个字符串。
     *<span class="hljs-javadoctag"> @param</span> tableName 需要授权的表名, scan涉及到的表不需要额外授权。
     *<span class="hljs-javadoctag"> @param</span> job 相关job。
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initAuthentication</span>(String tableConfigKey, String tableName, Job job) <span class="hljs-keyword">throws</span> IOException {
        Configuration peerConf = job.getConfiguration();
        peerConf.set(tableConfigKey, tableName);
        ZKUtil.applyClusterKeyToConf(peerConf, tableName);
        <span class="hljs-keyword">if</span> (User.isHBaseSecurityEnabled(peerConf)) {
            LOGGER.info(<span class="hljs-string">"Obtaining remote user authentication token with table:{}"</span>, tableName);
            <span class="hljs-keyword">try</span> {
                User.getCurrent().obtainAuthTokenForJob(peerConf, job);
            } <span class="hljs-keyword">catch</span> (InterruptedException ex) {
                LOGGER.info(<span class="hljs-string">"Interrupted obtaining remote user authentication token"</span>);
                LOGGER.error(<span class="hljs-string">"Obtained remote user authentication token with table:{}, error:\n"</span>, tableName, ex);
                Thread.interrupted();
            }
            LOGGER.info(<span class="hljs-string">"Obtained remote user authentication token with table:{}"</span>, tableName);
        }
    }</code></pre>

<p>代码相对比较简单，都是Hbase与ZK提供的一些辅助工具类。不解释。</p>



<h2 id="2thrift对象转化">2.thrift对象转化</h2>

<p>本例中操作的对象为XX表，Column Family为”P”, Qualifer 为”P”与”C”，里面对应的value都是thrift对象。其中”P”对应的thrift对象为:</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">struct</span> UserProfile {
    <span class="hljs-number">1</span>: optional <span class="hljs-keyword">byte</span> sex; 
    <span class="hljs-number">2</span>: optional i32 age;
    <span class="hljs-number">3</span>: optional <span class="hljs-keyword">string</span> phoneBrand;
    <span class="hljs-number">4</span>: optional <span class="hljs-keyword">string</span> locationProvince;
}</code></pre>

<p>“C”对应的thrift对象为:</p>



<pre class="prettyprint"><code class=" hljs cpp"><span class="hljs-keyword">struct</span> UserClickInfo {
    <span class="hljs-number">1</span>: required i32 totolAck;
    <span class="hljs-number">2</span>: required i32 totalClick;
    <span class="hljs-number">3</span>: optional <span class="hljs-stl_container"><span class="hljs-built_in">map</span>&lt;i64, <span class="hljs-stl_container"><span class="hljs-built_in">map</span>&lt;<span class="hljs-built_in">string</span>, i32&gt;</span>&gt;</span> ackClick;
}</code></pre>

<p>这个时候我们就需要经常将Bytes转化为thrift对象，通用的方法为：</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * convert byte array to thrift object.
     *
     *<span class="hljs-javadoctag"> @param</span> &lt;T&gt; type of thrift object.
     *<span class="hljs-javadoctag"> @param</span> thriftObj an thrift object.
     *<span class="hljs-javadoctag"> @return</span> byte array if convert succeeded, &lt;code&gt;null&lt;/code&gt; if convert failed.
     *<span class="hljs-javadoctag"> @throws</span> TException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> &lt;T extends TBase&lt;T, ?&gt;&gt; T <span class="hljs-title">convertBytesToThriftObject</span>(<span class="hljs-keyword">byte</span>[] raw, T thriftObj) <span class="hljs-keyword">throws</span> TException {
        <span class="hljs-keyword">if</span> (ArrayUtils.isEmpty(raw)) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
        Validate.notNull(thriftObj, <span class="hljs-string">"thriftObj"</span>);

        TDeserializer serializer = <span class="hljs-keyword">new</span> TDeserializer(<span class="hljs-keyword">new</span> TBinaryProtocol.Factory());
        serializer.deserialize(thriftObj, raw);
        <span class="hljs-keyword">return</span> thriftObj;
    }</code></pre>



<h2 id="3map阶段读取hbase里的数据">3.Map阶段读取Hbase里的数据</h2>

<p>在Map阶段对Hbase表扫描，得出数据</p>



<pre class="prettyprint"><code class=" hljs lasso">    <span class="hljs-comment">//输出的KV均为Text</span>
    static class ReadMapper extends TableMapper<span class="hljs-subst">&lt;</span>Text,Text<span class="hljs-subst">&gt;</span> {

        @Override
        <span class="hljs-keyword">protected</span> <span class="hljs-literal">void</span> <span class="hljs-built_in">map</span>(ImmutableBytesWritable key, Result res, Context context) throws IOException,InterruptedException{
            <span class="hljs-built_in">String</span> uuid <span class="hljs-subst">=</span> StringUtils<span class="hljs-built_in">.</span>reverse(<span class="hljs-built_in">Bytes</span><span class="hljs-built_in">.</span>toString(key<span class="hljs-built_in">.</span>copyBytes()));
            <span class="hljs-keyword">if</span> (res <span class="hljs-subst">==</span> <span class="hljs-built_in">null</span> <span class="hljs-subst">||</span> res<span class="hljs-built_in">.</span>isEmpty()) <span class="hljs-keyword">return</span>;
            res<span class="hljs-built_in">.</span>getFamilyMap(USER_FEATURE_COLUMN_FAMILY);


            for(KeyValue kv:res<span class="hljs-built_in">.</span><span class="hljs-built_in">list</span>()) {
                <span class="hljs-built_in">String</span> qualifier <span class="hljs-subst">=</span> <span class="hljs-built_in">Bytes</span><span class="hljs-built_in">.</span>toString(kv<span class="hljs-built_in">.</span>getQualifier());
                <span class="hljs-comment">//String qualifier = kv.getKeyString();</span>
                <span class="hljs-keyword">if</span>(qualifier<span class="hljs-built_in">.</span><span class="hljs-keyword">equals</span>(<span class="hljs-string">"P"</span>)) {

                    try {
                        UserProfile userProfile <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> UserProfile();
                        convertBytesToThriftObject(kv<span class="hljs-built_in">.</span>getValue(), userProfile);
                        <span class="hljs-built_in">String</span> profileRes <span class="hljs-subst">=</span> userProfile<span class="hljs-built_in">.</span>getAge() <span class="hljs-subst">+</span> <span class="hljs-string">","</span> <span class="hljs-subst">+</span> userProfile<span class="hljs-built_in">.</span>getSex() <span class="hljs-subst">+</span> <span class="hljs-string">","</span>
                                <span class="hljs-subst">+</span> userProfile<span class="hljs-built_in">.</span>getPhoneBrand() <span class="hljs-subst">+</span> <span class="hljs-string">","</span> <span class="hljs-subst">+</span> userProfile<span class="hljs-built_in">.</span>getLocationProvince();
                        context<span class="hljs-built_in">.</span>write(<span class="hljs-literal">new</span> Text(uuid),<span class="hljs-literal">new</span> Text(profileRes));
                    } catch (Exception ex) {}
                }
                <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(qualifier<span class="hljs-built_in">.</span><span class="hljs-keyword">equals</span>(<span class="hljs-string">"C"</span>)) {
                    UserClickInfo userClickInfo <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> UserClickInfo();
                    try {
                        convertBytesToThriftObject(kv<span class="hljs-built_in">.</span>getValue(), userClickInfo);
                        <span class="hljs-built_in">Map</span><span class="hljs-subst">&lt;</span>Long,<span class="hljs-built_in">Map</span><span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>,<span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;&gt;</span> resMap <span class="hljs-subst">=</span> userClickInfo<span class="hljs-built_in">.</span>getAckClick();
                        for(<span class="hljs-built_in">Map</span><span class="hljs-built_in">.</span>Entry<span class="hljs-subst">&lt;</span>Long,<span class="hljs-built_in">Map</span><span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>,<span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;&gt;</span> entry:resMap<span class="hljs-built_in">.</span>entrySet()) {
                            <span class="hljs-built_in">String</span> appid <span class="hljs-subst">=</span> <span class="hljs-built_in">String</span><span class="hljs-built_in">.</span>valueOf(entry<span class="hljs-built_in">.</span>getKey());
                            int click <span class="hljs-subst">=</span> entry<span class="hljs-built_in">.</span>getValue()<span class="hljs-built_in">.</span>get(<span class="hljs-string">"click"</span>);
                            int ack <span class="hljs-subst">=</span> entry<span class="hljs-built_in">.</span>getValue()<span class="hljs-built_in">.</span>get(<span class="hljs-string">"ack"</span>);
                            <span class="hljs-built_in">String</span> <span class="hljs-literal">all</span> <span class="hljs-subst">=</span> appid <span class="hljs-subst">+</span> <span class="hljs-string">","</span> <span class="hljs-subst">+</span> <span class="hljs-built_in">String</span><span class="hljs-built_in">.</span>valueOf(click) <span class="hljs-subst">+</span> <span class="hljs-string">","</span> <span class="hljs-subst">+</span> <span class="hljs-built_in">String</span><span class="hljs-built_in">.</span>valueOf(ack);
                            context<span class="hljs-built_in">.</span>write(<span class="hljs-literal">new</span> Text(uuid),<span class="hljs-literal">new</span> Text(<span class="hljs-literal">all</span>));
                        }
                        int allClick <span class="hljs-subst">=</span> userClickInfo<span class="hljs-built_in">.</span>getTotalClick();
                        int allAck <span class="hljs-subst">=</span> userClickInfo<span class="hljs-built_in">.</span>getTotolAck();
                        <span class="hljs-built_in">String</span> allNum <span class="hljs-subst">=</span> <span class="hljs-string">"999,"</span> <span class="hljs-subst">+</span> <span class="hljs-built_in">String</span><span class="hljs-built_in">.</span>valueOf(allClick) <span class="hljs-subst">+</span> <span class="hljs-string">","</span> <span class="hljs-subst">+</span> <span class="hljs-built_in">String</span><span class="hljs-built_in">.</span>valueOf(allAck);
                        context<span class="hljs-built_in">.</span>write(<span class="hljs-literal">new</span> Text(uuid),<span class="hljs-literal">new</span> Text(allNum));
                    } catch (Exception ex) {}
                }
            }
        }
    }</code></pre>



<h2 id="4run方法里配置相关驱动">4.run方法里配置相关驱动</h2>

<p>run方法里需要配置一些相关的参数，保证任务的顺利进行。 <br>
其中，<code>TableMapReduceUtil.addDependencyJars</code>方法添加了完成任务一些必要的类。</p>



<pre class="prettyprint"><code class=" hljs avrasm">    public int run(String[] args) throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>

        Job job = Job<span class="hljs-preprocessor">.getInstance</span>(conf,<span class="hljs-string">"read_data_from_hbase"</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setJarByClass</span>(ReadDataFromHbase<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setMapOutputKeyClass</span>(Text<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setMapOutputValueClass</span>(Text<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setReducerClass</span>(ReadReducer<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setSpeculativeExecution</span>(false)<span class="hljs-comment">;</span>

        TableMapReduceUtil<span class="hljs-preprocessor">.addDependencyJars</span>(job<span class="hljs-preprocessor">.getConfiguration</span>(),StringUtils<span class="hljs-preprocessor">.class</span>, TimeUtils<span class="hljs-preprocessor">.class</span>, Util<span class="hljs-preprocessor">.class</span>,
                CompressionCodec<span class="hljs-preprocessor">.class</span>, TStructDescriptor<span class="hljs-preprocessor">.class</span>, ObjectMapper<span class="hljs-preprocessor">.class</span>, CompressionCodecName<span class="hljs-preprocessor">.class</span>, BytesInput<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        Scan scan = new Scan()<span class="hljs-comment">;</span>
        //对整个CF扫描
        scan<span class="hljs-preprocessor">.addFamily</span>(USER_FEATURE_COLUMN_FAMILY)<span class="hljs-comment">;</span>

        String table = <span class="hljs-string">"XXX"</span><span class="hljs-comment">;</span>
        initAuthentication(table,table,job)<span class="hljs-comment">;</span>
        TableMapReduceUtil<span class="hljs-preprocessor">.initTableMapperJob</span>(table,
                scan,
                ReadMapper<span class="hljs-preprocessor">.class</span>,
                Text<span class="hljs-preprocessor">.class</span>,
                Text<span class="hljs-preprocessor">.class</span>,
                job)<span class="hljs-comment">;</span>

        String output = <span class="hljs-string">""</span><span class="hljs-comment">;</span>
        FileSystem<span class="hljs-preprocessor">.get</span>(job<span class="hljs-preprocessor">.getConfiguration</span>())<span class="hljs-preprocessor">.delete</span>(new Path(output), true)<span class="hljs-comment">;</span>
        FileOutputFormat<span class="hljs-preprocessor">.setOutputPath</span>(job,new Path(output))<span class="hljs-comment">;</span>

        return job<span class="hljs-preprocessor">.waitForCompletion</span>(true) ? <span class="hljs-number">0</span> : <span class="hljs-number">1</span><span class="hljs-comment">;</span>
    }</code></pre>



<h2 id="5完整的代码">5.完整的代码</h2>



<pre class="prettyprint"><code class=" hljs avrasm">package XXX<span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.mr</span>_job_and_tools<span class="hljs-preprocessor">.task</span><span class="hljs-comment">;</span>

import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.twitter</span><span class="hljs-preprocessor">.elephantbird</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.TStructDescriptor</span><span class="hljs-comment">;</span>
import XXX<span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.TimeUtils</span><span class="hljs-comment">;</span>
import XXX<span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.UserClickInfo</span><span class="hljs-comment">;</span>
import XXX<span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.XXX</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.UserProfile</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.commons</span><span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.ArrayUtils</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.commons</span><span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.Validate</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.commons</span><span class="hljs-preprocessor">.lang</span>3<span class="hljs-preprocessor">.StringUtils</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configured</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystem</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.KeyValue</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Result</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Scan</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.ImmutableBytesWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableMapReduceUtil</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableMapper</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.User</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.ZKUtil</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.Text</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Reducer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.lib</span><span class="hljs-preprocessor">.output</span><span class="hljs-preprocessor">.FileOutputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Tool</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.ToolRunner</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.parquet</span><span class="hljs-preprocessor">.bytes</span><span class="hljs-preprocessor">.BytesInput</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.parquet</span><span class="hljs-preprocessor">.format</span><span class="hljs-preprocessor">.CompressionCodec</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.parquet</span><span class="hljs-preprocessor">.format</span><span class="hljs-preprocessor">.Util</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.parquet</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.metadata</span><span class="hljs-preprocessor">.CompressionCodecName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.TBase</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.TDeserializer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.TException</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.protocol</span><span class="hljs-preprocessor">.TBinaryProtocol</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>
import shaded<span class="hljs-preprocessor">.parquet</span><span class="hljs-preprocessor">.org</span><span class="hljs-preprocessor">.codehaus</span><span class="hljs-preprocessor">.jackson</span><span class="hljs-preprocessor">.map</span><span class="hljs-preprocessor">.ObjectMapper</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.ArrayList</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.List</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Map</span><span class="hljs-comment">;</span>

<span class="hljs-comment">/**
 * Created by WangLei on 17-3-13.
 */</span>
public class ReadDataFromHbase extends Configured implements Tool{

    private static final Logger LOGGER = LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(ReadDataFromHbase<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
    public static final byte[] USER_FEATURE_COLUMN_FAMILY = Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"P"</span>)<span class="hljs-comment">;</span>

    <span class="hljs-comment">/**
     * convert byte array to thrift object.
     *
     * @param &lt;T&gt; type of thrift object.
     * @param thriftObj an thrift object.
     * @return byte array if convert succeeded, &lt;code&gt;null&lt;/code&gt; if convert failed.
     * @throws TException
     */</span>
    public static final &lt;T extends TBase&lt;T, ?&gt;&gt; T convertBytesToThriftObject(byte[] raw, T thriftObj) throws TException {
        if (ArrayUtils<span class="hljs-preprocessor">.isEmpty</span>(raw)) {
            return null<span class="hljs-comment">;</span>
        }
        Validate<span class="hljs-preprocessor">.notNull</span>(thriftObj, <span class="hljs-string">"thriftObj"</span>)<span class="hljs-comment">;</span>

        TDeserializer serializer = new TDeserializer(new TBinaryProtocol<span class="hljs-preprocessor">.Factory</span>())<span class="hljs-comment">;</span>
        serializer<span class="hljs-preprocessor">.deserialize</span>(thriftObj, raw)<span class="hljs-comment">;</span>
        return thriftObj<span class="hljs-comment">;</span>
    }


    <span class="hljs-comment">/**
     * 为hbase表授权。
     *
     * @param tableConfigKey 任意一个字符串。
     * @param tableName 需要授权的表名, scan涉及到的表不需要额外授权。
     * @param job 相关job。
     * @throws IOException
     */</span>
    public static void initAuthentication(String tableConfigKey, String tableName, Job job) throws IOException {
        Configuration peerConf = job<span class="hljs-preprocessor">.getConfiguration</span>()<span class="hljs-comment">;</span>
        peerConf<span class="hljs-preprocessor">.set</span>(tableConfigKey, tableName)<span class="hljs-comment">;</span>
        ZKUtil<span class="hljs-preprocessor">.applyClusterKeyToConf</span>(peerConf, tableName)<span class="hljs-comment">;</span>
        if (User<span class="hljs-preprocessor">.isHBaseSecurityEnabled</span>(peerConf)) {
            LOGGER<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"Obtaining remote user authentication token with table:{}"</span>, tableName)<span class="hljs-comment">;</span>
            try {
                User<span class="hljs-preprocessor">.getCurrent</span>()<span class="hljs-preprocessor">.obtainAuthTokenForJob</span>(peerConf, job)<span class="hljs-comment">;</span>
            } catch (InterruptedException ex) {
                LOGGER<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"Interrupted obtaining remote user authentication token"</span>)<span class="hljs-comment">;</span>
                LOGGER<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Obtained remote user authentication token with table:{}, error:\n"</span>, tableName, ex)<span class="hljs-comment">;</span>
                Thread<span class="hljs-preprocessor">.interrupted</span>()<span class="hljs-comment">;</span>
            }
            LOGGER<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"Obtained remote user authentication token with table:{}"</span>, tableName)<span class="hljs-comment">;</span>
        }
    }

    static class ReadMapper extends TableMapper&lt;Text,Text&gt; {

        @Override
        protected void map(ImmutableBytesWritable key, Result res, Context context) throws IOException,InterruptedException{
            String uuid = StringUtils<span class="hljs-preprocessor">.reverse</span>(Bytes<span class="hljs-preprocessor">.toString</span>(key<span class="hljs-preprocessor">.copyBytes</span>()))<span class="hljs-comment">;</span>
            if (res == null || res<span class="hljs-preprocessor">.isEmpty</span>()) return<span class="hljs-comment">;</span>
            res<span class="hljs-preprocessor">.getFamilyMap</span>(USER_FEATURE_COLUMN_FAMILY)<span class="hljs-comment">;</span>


            for(KeyValue kv:res<span class="hljs-preprocessor">.list</span>()) {
                String qualifier = Bytes<span class="hljs-preprocessor">.toString</span>(kv<span class="hljs-preprocessor">.getQualifier</span>())<span class="hljs-comment">;</span>
                //String qualifier = kv<span class="hljs-preprocessor">.getKeyString</span>()<span class="hljs-comment">;</span>
                if(qualifier<span class="hljs-preprocessor">.equals</span>(<span class="hljs-string">"P"</span>)) {

                    try {
                        UserProfile userProfile = new UserProfile()<span class="hljs-comment">;</span>
                        convertBytesToThriftObject(kv<span class="hljs-preprocessor">.getValue</span>(), userProfile)<span class="hljs-comment">;</span>
                        String profileRes = userProfile<span class="hljs-preprocessor">.getAge</span>() + <span class="hljs-string">","</span> + userProfile<span class="hljs-preprocessor">.getSex</span>() + <span class="hljs-string">","</span>
                                + userProfile<span class="hljs-preprocessor">.getPhoneBrand</span>() + <span class="hljs-string">","</span> + userProfile<span class="hljs-preprocessor">.getLocationProvince</span>()<span class="hljs-comment">;</span>
                        context<span class="hljs-preprocessor">.write</span>(new Text(uuid),new Text(profileRes))<span class="hljs-comment">;</span>
                    } catch (Exception ex) {}
                }
                else if(qualifier<span class="hljs-preprocessor">.equals</span>(<span class="hljs-string">"C"</span>)) {
                    UserClickInfo userClickInfo = new UserClickInfo()<span class="hljs-comment">;</span>
                    try {
                        convertBytesToThriftObject(kv<span class="hljs-preprocessor">.getValue</span>(), userClickInfo)<span class="hljs-comment">;</span>
                        Map&lt;Long,Map&lt;String,Integer&gt;&gt; resMap = userClickInfo<span class="hljs-preprocessor">.getAckClick</span>()<span class="hljs-comment">;</span>
                        for(Map<span class="hljs-preprocessor">.Entry</span>&lt;Long,Map&lt;String,Integer&gt;&gt; entry:resMap<span class="hljs-preprocessor">.entrySet</span>()) {
                            String appid = String<span class="hljs-preprocessor">.valueOf</span>(entry<span class="hljs-preprocessor">.getKey</span>())<span class="hljs-comment">;</span>
                            int click = entry<span class="hljs-preprocessor">.getValue</span>()<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"click"</span>)<span class="hljs-comment">;</span>
                            int ack = entry<span class="hljs-preprocessor">.getValue</span>()<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"ack"</span>)<span class="hljs-comment">;</span>
                            String all = appid + <span class="hljs-string">","</span> + String<span class="hljs-preprocessor">.valueOf</span>(click) + <span class="hljs-string">","</span> + String<span class="hljs-preprocessor">.valueOf</span>(ack)<span class="hljs-comment">;</span>
                            context<span class="hljs-preprocessor">.write</span>(new Text(uuid),new Text(all))<span class="hljs-comment">;</span>
                        }
                        int allClick = userClickInfo<span class="hljs-preprocessor">.getTotalClick</span>()<span class="hljs-comment">;</span>
                        int allAck = userClickInfo<span class="hljs-preprocessor">.getTotolAck</span>()<span class="hljs-comment">;</span>
                        String allNum = <span class="hljs-string">"999,"</span> + String<span class="hljs-preprocessor">.valueOf</span>(allClick) + <span class="hljs-string">","</span> + String<span class="hljs-preprocessor">.valueOf</span>(allAck)<span class="hljs-comment">;</span>
                        context<span class="hljs-preprocessor">.write</span>(new Text(uuid),new Text(allNum))<span class="hljs-comment">;</span>
                    } catch (Exception ex) {}
                }
            }
        }
    }

    static class ReadReducer extends Reducer&lt;Text,Text,Text,Text&gt; {
        @Override
        protected void reduce(Text key, Iterable&lt;Text&gt; values, Context context) throws IOException,InterruptedException{
            List&lt;String&gt; resultList = new ArrayList&lt;String&gt;()<span class="hljs-comment">;</span>
            for(Text each:values) {
                resultList<span class="hljs-preprocessor">.add</span>(each<span class="hljs-preprocessor">.toString</span>())<span class="hljs-comment">;</span>
            }
            String res = StringUtils<span class="hljs-preprocessor">.join</span>(resultList,<span class="hljs-string">":"</span>)<span class="hljs-comment">;</span>
            context<span class="hljs-preprocessor">.write</span>(key,new Text(res))<span class="hljs-comment">;</span>
        }
    }

    @Override
    public int run(String[] args) throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>

        Job job = Job<span class="hljs-preprocessor">.getInstance</span>(conf,<span class="hljs-string">"read_data_from_hbase"</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setJarByClass</span>(ReadDataFromHbase<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setMapOutputKeyClass</span>(Text<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setMapOutputValueClass</span>(Text<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setReducerClass</span>(ReadReducer<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        job<span class="hljs-preprocessor">.setSpeculativeExecution</span>(false)<span class="hljs-comment">;</span>

        TableMapReduceUtil<span class="hljs-preprocessor">.addDependencyJars</span>(job<span class="hljs-preprocessor">.getConfiguration</span>(),StringUtils<span class="hljs-preprocessor">.class</span>, TimeUtils<span class="hljs-preprocessor">.class</span>, Util<span class="hljs-preprocessor">.class</span>,
                CompressionCodec<span class="hljs-preprocessor">.class</span>, TStructDescriptor<span class="hljs-preprocessor">.class</span>, ObjectMapper<span class="hljs-preprocessor">.class</span>, CompressionCodecName<span class="hljs-preprocessor">.class</span>, BytesInput<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        Scan scan = new Scan()<span class="hljs-comment">;</span>
        //对整个CF扫描
        scan<span class="hljs-preprocessor">.addFamily</span>(USER_FEATURE_COLUMN_FAMILY)<span class="hljs-comment">;</span>

        String table = <span class="hljs-string">"XXX"</span><span class="hljs-comment">;</span>
        initAuthentication(table,table,job)<span class="hljs-comment">;</span>
        TableMapReduceUtil<span class="hljs-preprocessor">.initTableMapperJob</span>(table,
                scan,
                ReadMapper<span class="hljs-preprocessor">.class</span>,
                Text<span class="hljs-preprocessor">.class</span>,
                Text<span class="hljs-preprocessor">.class</span>,
                job)<span class="hljs-comment">;</span>

        String output = <span class="hljs-string">""</span><span class="hljs-comment">;</span>
        FileSystem<span class="hljs-preprocessor">.get</span>(job<span class="hljs-preprocessor">.getConfiguration</span>())<span class="hljs-preprocessor">.delete</span>(new Path(output), true)<span class="hljs-comment">;</span>
        FileOutputFormat<span class="hljs-preprocessor">.setOutputPath</span>(job,new Path(output))<span class="hljs-comment">;</span>

        return job<span class="hljs-preprocessor">.waitForCompletion</span>(true) ? <span class="hljs-number">0</span> : <span class="hljs-number">1</span><span class="hljs-comment">;</span>
    }

    public static void main(String[] args) throws Exception{
        System<span class="hljs-preprocessor">.exit</span>(ToolRunner<span class="hljs-preprocessor">.run</span>(new ReadDataFromHbase(),args))<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>然后将代码打包，提交到集群上运行对应的shell脚本即可。</p>

<h2 id="6版本信息">6.版本信息</h2>

<p>本文中的代码，对应的hadoop版本为2.6,Hbase版本为0.98。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>