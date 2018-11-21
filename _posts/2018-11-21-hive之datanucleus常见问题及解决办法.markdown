---
layout:     post
title:      hive之datanucleus常见问题及解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
hiveserver <br>
./hive --service start-hive <br>
./hive --service stop-hive <br><br>
hive的metastore使用了datanucleus <br><br>
下载datanucleus-samples-jdo-tutorial-2.0-src.zip <br>
解压datanucleus-samples-jdo-tutorial-2.0，把里面的lib目录，src目录，datanucleus.properties  log4j.properties orm.xml都拷到另外一个目录下面<br><br>
创建 target/classes文件。 <br><br>
修改datanucleus.properties文件为： <br>
javax.jdo.PersistenceManagerFactoryClass=org.datanucleus.jdo.JDOPersistenceManagerFactory<br><br>
javax.jdo.option.ConnectionDriverName=com.mysql.jdbc.Driver <br>
javax.jdo.option.ConnectionURL=jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true<br>
javax.jdo.option.ConnectionUserName=hive <br>
javax.jdo.option.ConnectionPassword=123456 <br>
javax.jdo.option.Mapping=hsql <br><br>
datanucleus.metadata.validate=false <br>
datanucleus.autoCreateSchema=true <br>
datanucleus.validateTables=false <br>
datanucleus.validateConstraints=false <br><br><br>
加上下面这个选项会出现问题：Unable to fetch table *** <br>
&lt;property&gt; <br>
  &lt;name&gt;datanucleus.cache.collections&lt;/name&gt; <br>
  &lt;value&gt;false&lt;/value&gt; <br>
&lt;/property&gt; <br><br><br>
datanucleus.cache.level2.type       从 soft  改为  none <br><br><br>
datanucleus.fixedDatastore = true; 出现问题。 <br>
https://issues.apache.org/jira/browse/HIVE-1841 <br><br>
hive-site.xml中加入下面的配置能够解决下面的问题：Failures with DELETEME tables <br>
&lt;property&gt; <br>
&lt;name&gt;datanucleus.fixedDatastore&lt;/name&gt; <br>
&lt;value&gt;true&lt;/value&gt; <br>
&lt;/property&gt; <br><br>
并发造成的错误： <br>
Caused by: com.mysql.jdbc.exceptions.MySQLSyntaxErrorException: Table 'dataoven_prod_hadoop.DELETEME1309959999747' doesn't exist<br><br>
http://search-hadoop.com/m/bi3w92UVF3L/Failures+with+DELETEME+tables&amp;subj=Failures+with+DELETEME+tables<br><br>
The datastore exception was thrown because com.mysql.jdbc.DatabaseMetaData.getColumns has a race condition. It was called with tableNamePattern == null and because of that it went and fetched a list of all tables, and for each table, it executed a query to
 fetch table info. Before all table-info fetches were done, if a table were dropped by some other process, the fetch would fail and getColumns would throw an exception. This is a race condition bug.<br><br>
For now, I have enabled datanucleus.fixedDatastore in hive-site.xml to eliminate the use of DELTEME tables, virtually eliminating the problem. But the problem can still happen with non-DELETEME causes due to the 2 bugs described above or when datanucleus.fixedDatastore
 needs to be disabled occasionally. <br><br><br>
hiveserver take too much memory： <br>
http://search-hadoop.com/m/PIsyn1GZxfh1&amp;subj=java+lang+OutOfMemoryError+PermGen+space+when+running+as+a+service+<br><br>
move all properties from jpox.properties to hive-site.xml <br>
https://issues.apache.org/jira/browse/HIVE-610 <br><br><br><br><br><br>
操作如下： <br>
编译生成的class文件到target/classes <br>
tianzhao@tianzhao-VirtualBox:~/software/datanucleus/example$ javac  -cp lib/jdo2-api-2.3-ec.jar:lib/datanucleus-core-2.0.3.jar   -sourcepath src/java/ src/java/org/datanucleus/samples/jdo/tutorial/*.java -d target/classes<br><br>
tianzhao@tianzhao-VirtualBox:~/software/datanucleus/example$ cp src/java/org/datanucleus/samples/jdo/tutorial/package-hsql.orm target/classes/org/datanucleus/samples/jdo/tutorial/<br><br>
tianzhao@tianzhao-VirtualBox:~/software/datanucleus/example$ java -cp target/classes/:lib/datanucleus-enhancer-2.0.3.jar:lib/datanucleus-core-2.0.3.jar:lib/jdo2-api-2.3-ec.jar:lib/asm-3.1.jar org.datanucleus.enhancer.DataNucleusEnhancer target/classes/org/datanucleus/samples/jdo/tutorial/package-hsql.orm<br><br>
在数据库中创建表 <br>
tianzhao@tianzhao-VirtualBox:~/software/datanucleus/example$ java -cp target/classes/:lib/datanucleus-rdbms-2.0.3.jar:lib/datanucleus-core-2.0.3.jar:lib/jdo2-api-2.3-ec.jar:lib/mysql-connector-java-5.1.6.jar org.datanucleus.store.rdbms.SchemaTool -props datanucleus.properties
 -create target/classes/org/datanucleus/samples/jdo/tutorial/package-hsql.orm <br><br>
执行相关操作 <br>
tianzhao@tianzhao-VirtualBox:~/software/datanucleus/example$ java -cp lib/jdo2-api-2.3-ec.jar:lib/datanucleus-core-2.0.3.jar:lib/datanucleus-rdbms-2.0.3.jar:lib/mysql-connector-java-5.1.6.jar:target/classes/:.  org.datanucleus.samples.jdo.tutorial.Main<br><br><br>
datanucleus-samples-jdo-tutorial-2.0 下面有build.xml   pom.xml两个文件 <br>
pom.xml <br>
Files used by Maven2 for building and running the tutorial. <br><br>
build.xml <br>
File used by Ant for building the tutorial. <br>
这里只是转成直接使用java 和 javac的的命令 <br><br><br><br><br><br><br><br><br><br><br>
hive用的datanucleus版本。 <br><br>
build.properties : <br>
# <br>
# Data nucleus repository - needed for jdo2-api-2.3-ec.jar download <br>
# <br>
datanucleus.repo=http://www.datanucleus.org/downloads/maven2 <br><br><br>
ivy/libraries.properties : <br>
datanucleus-connectionpool.version=2.0.3 <br>
datanucleus-core.version=2.1.1 <br>
datanucleus-enhancer.version=2.1.0-release <br>
datanucleus-rdbms.version=2.1.1 <br><br><br>
metastore/ivy.xml : <br>
        &lt;dependency org="org.datanucleus" name="datanucleus-connectionpool" rev="${datanucleus-connectionpool.version}"&gt;<br>
          &lt;exclude module="proxool" /&gt; <br>
          &lt;exclude module="c3p0" /&gt; <br>
          &lt;exclude module="datanucleus-core" /&gt; <br>
          &lt;exclude module="datanucleus-rdbms" /&gt; <br>
          &lt;exclude module="commons-collections" /&gt; <br>
          &lt;exclude module="commons-pool" /&gt; <br>
          &lt;exclude module="commons-dbcp" /&gt; <br>
          &lt;exclude org="com.jolbox" module="bonecp"/&gt; <br>
        &lt;/dependency&gt; <br>
        &lt;dependency org="org.datanucleus" name="datanucleus-core" rev="${datanucleus-core.version}"&gt;<br>
          &lt;exclude org="javax.jdo" module="jdo2-api"/&gt; <br>
          &lt;exclude org="org.apache.geronimo.specs" module="geronimo-jta_1.1_spec"/&gt;<br>
          &lt;exclude org="org.eclipse.equinox" module="org.eclipse.equinox.registry"/&gt;<br>
          &lt;exclude org="org.eclipse.equinox" module="org.eclipse.equinox.common"/&gt;<br>
          &lt;exclude org="org.eclipse.core" module="org.eclipse.core.runtime"/&gt; <br>
          &lt;exclude org="org.eclipse.osgi" module="org.eclipse.osgi"/&gt; <br>
          &lt;exclude org="log4j" module="log4j"/&gt; <br>
        &lt;/dependency&gt; <br>
        &lt;dependency org="org.datanucleus" name="datanucleus-enhancer" rev="${datanucleus-enhancer.version}"&gt;<br>
          &lt;exclude org="org.datanucleus" module="datanucleus-core"/&gt; <br>
          &lt;exclude org="javax.jdo" module="jdo2-api"/&gt; <br>
          &lt;exclude org="asm" module="asm"/&gt; <br>
          &lt;exclude org="org.apache.ant" module="ant"/&gt; <br>
        &lt;/dependency&gt; <br>
        &lt;dependency org="org.datanucleus" name="datanucleus-rdbms" rev="${datanucleus-rdbms.version}"&gt;<br>
          &lt;exclude org="org.datanucleus" module="datanucleus-core"/&gt; <br>
          &lt;exclude org="javax.jdo" module="jdo2-api"/&gt; <br>
          &lt;exclude org="org.apache.geronimo.specs" module="geronimo-jta_1.1_spec"/&gt;<br>
          &lt;exclude org="org.datanucleus" module="datanucleus-thirdparty"/&gt; <br>
          &lt;exclude org="org.apache.ant" module="ant"/&gt; <br>
          &lt;exclude org="oracle" module="ojdbc14_g"/&gt; <br>
        &lt;/dependency&gt; <br><br>
http://www.jpox.org/servlet/jira/browse/NUCCORE-559?page=com.atlassian.jira.plugin.system.issuetabpanels%3Aall-tabpanel<br>            </div>
                </div>