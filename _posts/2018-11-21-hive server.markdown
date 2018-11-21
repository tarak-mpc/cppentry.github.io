---
layout:     post
title:      hive server
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hive-server">hive server</h1>



<h2 id="hive-1">hive 1</h2>

<p>启动： </p>

<blockquote>
  <p>hive –service hiveserver</p>
</blockquote>

<p>jdbc连接</p>

<blockquote>
  <p>org.apache.hadoop.hive.jdbc.HiveDriver <br>
  dbc:hive://172.22.1.100:10000/test</p>
</blockquote>



<h2 id="hive2">hive2</h2>

<p>启动：</p>

<blockquote>
  <p>hive –service hiveserver2</p>
</blockquote>

<p>jdbc连接</p>

<blockquote>
  <p>org.apache.hive.jdbc.HiveDriver <br>
  dbc:hive2://172.22.1.100:10000/test</p>
</blockquote>

<h2 id="开启远程写的权限">开启远程写的权限</h2>

<blockquote>
  <p>hdfs-site.xml</p>
</blockquote>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.permissions<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h2 id="配置用户自定义安全策略">配置用户自定义安全策略</h2>

<p>首先hive支持多种安全认证方式：NONE,NOSASL, KERBEROS, LDAP, PAM ,CUSTOM等，一般默认配置为none就是没有任何验证，所以需要其他的安全策略，我选择的是CUSTOM(自定义安全策略)。 <br>
如果将hive.server2.authentication设置成CUSTOM，则需要设置 <br>
hive.server2.custom.authentication.class来指定用于权限认证的类，这个类需要实现 <br>
org.apache.hive.service.auth.PasswdAuthenticationProvider接口。 <br>
首先实现一个自定义类：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> com.imis.project;
<span class="hljs-keyword">import</span> java.io.BufferedReader;
<span class="hljs-keyword">import</span> java.io.File;
<span class="hljs-keyword">import</span> java.io.FileReader;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.security.MessageDigest;
<span class="hljs-keyword">import</span> java.security.NoSuchAlgorithmException;
<span class="hljs-keyword">import</span> javax.security.sasl.AuthenticationException;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.hive.conf.HiveConf;
<span class="hljs-keyword">import</span> org.apache.hive.service.auth.PasswdAuthenticationProvider;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CustomHiveServer2Auth</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">PasswdAuthenticationProvider</span>  {</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">Authenticate</span>(String username, String password)
            <span class="hljs-keyword">throws</span> AuthenticationException {

        <span class="hljs-keyword">boolean</span> ok = <span class="hljs-keyword">false</span>;
        String passMd5 = <span class="hljs-keyword">new</span> MD5().md5(password);
        HiveConf hiveConf = <span class="hljs-keyword">new</span> HiveConf();
        Configuration conf = <span class="hljs-keyword">new</span> Configuration(hiveConf);

        String filePath=<span class="hljs-keyword">null</span>;
        File file=<span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            filePath=conf.get(<span class="hljs-string">"hive.server2.custom.authentication.file"</span>);
            System.out.println(<span class="hljs-string">"hive.server2.custom.authentication.file ["</span>
                    + filePath + <span class="hljs-string">"] .."</span>);
            file = <span class="hljs-keyword">new</span> File(filePath);
        } <span class="hljs-keyword">catch</span> (Exception e) {

            System.out.println(<span class="hljs-string">"not found!"</span>);
        }
        BufferedReader reader = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            reader = <span class="hljs-keyword">new</span> BufferedReader(<span class="hljs-keyword">new</span> FileReader(file));
            String tempString = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">while</span> ((tempString = reader.readLine()) != <span class="hljs-keyword">null</span>) {
                String[] datas = tempString.split(<span class="hljs-string">","</span>, -<span class="hljs-number">1</span>);
                <span class="hljs-keyword">if</span>(datas.length != <span class="hljs-number">2</span>) <span class="hljs-keyword">continue</span>;
                <span class="hljs-comment">//ok</span>
                <span class="hljs-keyword">if</span>(datas[<span class="hljs-number">0</span>].equals(username) &amp;&amp; datas[<span class="hljs-number">1</span>].equals(passMd5)) {
                    ok = <span class="hljs-keyword">true</span>;
                    <span class="hljs-keyword">break</span>;
                }
            }
            reader.close();
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> AuthenticationException(<span class="hljs-string">"read auth config file error, ["</span> + filePath + <span class="hljs-string">"] .."</span>, e);
        } <span class="hljs-keyword">finally</span> {
            <span class="hljs-keyword">if</span> (reader != <span class="hljs-keyword">null</span>) {
                <span class="hljs-keyword">try</span> {
                    reader.close();
                } <span class="hljs-keyword">catch</span> (IOException e1) {}
            }
        }
        <span class="hljs-keyword">if</span>(ok) {
            System.out.println(<span class="hljs-string">"user ["</span> + username + <span class="hljs-string">"] auth check ok .. "</span>);
        } <span class="hljs-keyword">else</span> {
            System.out.println(<span class="hljs-string">"user ["</span> + username + <span class="hljs-string">"] auth check fail .. "</span>);
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> AuthenticationException(<span class="hljs-string">"user ["</span> + username + <span class="hljs-string">"] auth check fail .. "</span>);
        }
    }

    <span class="hljs-comment">//MD5加密</span>
    class MD5 {
        <span class="hljs-keyword">private</span> MessageDigest digest;
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">char</span> hexDigits[] = {<span class="hljs-string">'0'</span>,<span class="hljs-string">'1'</span>,<span class="hljs-string">'2'</span>,<span class="hljs-string">'3'</span>,<span class="hljs-string">'4'</span>,<span class="hljs-string">'5'</span>,<span class="hljs-string">'6'</span>,<span class="hljs-string">'7'</span>,
        <span class="hljs-string">'8'</span>,<span class="hljs-string">'9'</span>,<span class="hljs-string">'a'</span>,<span class="hljs-string">'b'</span>,<span class="hljs-string">'c'</span>,<span class="hljs-string">'d'</span>,<span class="hljs-string">'e'</span>,<span class="hljs-string">'f'</span>};

        <span class="hljs-keyword">public</span> <span class="hljs-title">MD5</span>() {
            <span class="hljs-keyword">try</span> {
              digest = MessageDigest.getInstance(<span class="hljs-string">"MD5"</span>);
            } <span class="hljs-keyword">catch</span> (NoSuchAlgorithmException e) {
              <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> RuntimeException(e);
            }
        }
         <span class="hljs-keyword">public</span> String <span class="hljs-title">md5</span>(String str) {
            <span class="hljs-keyword">byte</span>[] btInput = str.getBytes();
            digest.reset();
            digest.update(btInput);
            <span class="hljs-keyword">byte</span>[] md = digest.digest();
            <span class="hljs-comment">// 把密文转换成十六进制的字符串形式</span>
            <span class="hljs-keyword">int</span> j = md.length;
            <span class="hljs-keyword">char</span> strChar[] = <span class="hljs-keyword">new</span> <span class="hljs-keyword">char</span>[j * <span class="hljs-number">2</span>];
            <span class="hljs-keyword">int</span> k = <span class="hljs-number">0</span>;
            <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; j; i++) {
                <span class="hljs-keyword">byte</span> byte0 = md[i];
                strChar[k++] = hexDigits[byte0 &gt;&gt;&gt; <span class="hljs-number">4</span> &amp; <span class="hljs-number">0xf</span>];
                strChar[k++] = hexDigits[byte0 &amp; <span class="hljs-number">0xf</span>];
            }
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> String(strChar);
        }
    }
}</code></pre>

<p>然后整个项目打包，包括包的结构命名为HiveServer2Auth.jar，放到$HIVE_HOME/lib下 <br>
hive-site.xml</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.authentication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>CUSTOM<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.custom.authentication.class<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.imis.project.CustomHiveServer2Auth<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.custom.authentication.file<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/apache-hive-0.13.1-bin/conf/hive.server2.users.conf<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>在$HIVE_HOME/conf下新建文件hive.server2.users.conf，里面写入内容： <br>
[root@dev conf]# cat hive.server2.users.conf <br>
user,5f4dcc3b5aa765d61d8327deb882cf99</p>

<p>//下面这个是password的MD5码，你可以自己用上面的函数定制你的密码 <br>
5f4dcc3b5aa765d61d8327deb882cf99</p>

<p>然后重要的一件事，配置hive的日志 <br>
在hive的conf目录下执行</p>

<blockquote>
  <p>cp   hive-log4j.properties.template hive-log4j.properties <br>
  修改 <br>
  hive.log.dir=/home/hadoop/hive/conf # 默认的存储位置 <br>
  hive.log.file=hive.log  # 默认的文件名</p>
</blockquote>

<p>最后在相应目录下新建hive.log文件就可以看到hive系统日志了</p>

<p>当然你也可以配置job的日志</p>

<p>启动hiveserver2,已经做到了权限控制。</p>

<h2 id="远程调用mapreduce的问题">远程调用mapreduce的问题</h2>

<p>返回错误代码1，表示的是用户认证的错误 <br>
返回错误代码2，表示的是参数的问题</p>

<p>此处查看hive.log发现是</p>

<blockquote>
  <p>user=imis, access=EXECUTE, inode=”/tmp/hadoop-yarn”:hadoop:supergroup:rwx—-)</p>
</blockquote>

<p>此处解决方案：首先发现是权限的问题然后通过命令</p>

<blockquote>
  <p>hadoop fs -ls /tmp</p>
</blockquote>

<p>发现有两个目录hadoop-yarn和hive，在hive里面就是刚刚在自定义安全策略里面的用户名的目录比如user <br>
然后就会发现user是没有办法使用hadoop-yarn目录的，因为user没有权限，所以首先把hive目录下的用户授予权限，然后改变hadoop-yarn的权限。</p>

<blockquote>
  <p>hdfs dfs -chmod -R 777 /tmp/hadoop-yarn <br>
  hdfs dfs -chomd -R 777 /tmp/hive</p>
  
  <p>注：此处hadoop-tarn和hive的用户是hadoop组是supergroup，有一个现象就是root用户没有授予权限的权限，只能由hadoop用户授予777权限。</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>