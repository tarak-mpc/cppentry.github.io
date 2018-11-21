---
layout:     post
title:      flume使用（五）：taildirSource重复获取数据和不释放资源解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/maoyuanming0806/article/details/79391657				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79371123" rel="nofollow">flume使用（一）：入门demo</a>                                                                    <br></span></p><p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79376600" rel="nofollow">flume使用（二）：采集远程日志数据到MySql数据库</a>                                                                    <br></span></p><p><span class="content"><a href="http://blog.csdn.net/maoyuanming0806/article/details/79380826" rel="nofollow">flume使用（三）：实时log4j日志通过flume输出到MySql数据库</a>                                    <br></span></p><p><span class="content"><a href="http://blog.csdn.net/maoyuanming0806/article/details/79391010" rel="nofollow">flume使用（四）：taildirSource多文件监控实时采集</a>                                    <br></span></p><p><br></p><p>本文针对【<span class="content"><a href="http://blog.csdn.net/maoyuanming0806/article/details/79391010" rel="nofollow">flume使用（四）：taildirSource多文件监控实时采集</a></span>】一文中提出的两个flume的TailDirSource可能出现的问题进行解决。</p><h1>一、问题思考<br></h1><p>（1）log4j的日志文件肯定是会根据规则进行滚动的：当*.log满了就会滚动把前文件更名为*.log.1,然后重新进行*.log文件打印。这样flume就会把*.log.1文件当作新文件，又重新读取一遍，导致重复。</p><p>（2）当flume监控的日志文件被移走或删除，flume仍然在监控中，并没有释放资源，当然，在一定时间后会自动释放，这个时间根据官方文档设置默认值是120000ms。</p><h1>二、处理方式<br></h1><p>我这里不叫解决方式，在其他人的文章中说这两个是bug，个人认为这都不是bug。大家都知道flume作为apache的顶级项目，真有这样的bug在它的托管网站上肯定有相关pull并且肯定会有尽快的解决。至少，在flume1.8上会解决掉。个人查看了flume1.8处理的bug和功能的增加list中，对于（1）（2）没有关于这样解决项。</p><p>官方文档1.8的release说明：只有这一项关于taildir，解决的是当flume关闭文件同时该文件正更新数据。</p><p><img src="https://img-blog.csdn.net/20180227191528707?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveXVhbm1pbmcwODA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p>官网：http://flume.apache.org/releases/1.8.0.html</p><p>（1）flume会把重命名的文件重新当作新文件读取是因为正则表达式的原因，因为重命名后的文件名仍然符合正则表达式。所以第一，重命名后的文件仍然会被flume监控；第二，flume是<strong>根据文件inode&amp;&amp;文件绝对路径 、文件是否为null&amp;&amp;<strong>文件绝对路径，<strong>这样的条件来判断是否是同一个文件</strong></strong></strong>这个可以看源码：下载源码，放到maven项目（注意路径名称对应），找到taildirsource的包。</p><p>先看执行案例：</p><p><img src="https://img-blog.csdn.net/20180227195533810?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveXVhbm1pbmcwODA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p><br></p><p>确实是有重复，然后看源码：<strong>flume-taildir-source工程</strong><br></p><ul><li>ReliableTaildirEventReader 类的 updateTailFiles 方法</li></ul><pre><code class="language-plain">  public List&lt;Long&gt; updateTailFiles(boolean skipToEnd) throws IOException {
    updateTime = System.currentTimeMillis();
    List&lt;Long&gt; updatedInodes = Lists.newArrayList();

    for (TaildirMatcher taildir : taildirCache) {
      Map&lt;String, String&gt; headers = headerTable.row(taildir.getFileGroup());

      for (File f : taildir.getMatchingFiles()) {
        long inode = getInode(f);
        TailFile tf = tailFiles.get(inode);
        if (tf == null || !tf.getPath().equals(f.getAbsolutePath())) {
          long startPos = skipToEnd ? f.length() : 0;
          tf = openFile(f, headers, inode, startPos);
        } else {
          boolean updated = tf.getLastUpdated() &lt; f.lastModified();
          if (updated) {
            if (tf.getRaf() == null) {
              tf = openFile(f, headers, inode, tf.getPos());
            }
            if (f.length() &lt; tf.getPos()) {
              logger.info("Pos " + tf.getPos() + " is larger than file size! "
                  + "Restarting from pos 0, file: " + tf.getPath() + ", inode: " + inode);
              tf.updatePos(tf.getPath(), inode, 0);
            }
          }
          tf.setNeedTail(updated);
        }
        tailFiles.put(inode, tf);
        updatedInodes.add(inode);
      }
    }
    return updatedInodes;
  }</code></pre>重点：<br><pre><code class="language-plain"> for (File f : taildir.getMatchingFiles()) {
        long inode = getInode(f);
        TailFile tf = tailFiles.get(inode);
        if (tf == null || !tf.getPath().equals(f.getAbsolutePath())) {
          long startPos = skipToEnd ? f.length() : 0;
          tf = openFile(f, headers, inode, startPos);
        } </code></pre><ul><li>TailFile 类的 updatePos 方法：</li></ul><pre><code class="language-plain">  public boolean updatePos(String path, long inode, long pos) throws IOException {
    <strong>if (this.inode == inode &amp;&amp; this.path.equals(path)) {</strong>
      setPos(pos);
      updateFilePos(pos);
      logger.info("Updated position, file: " + path + ", inode: " + inode + ", pos: " + pos);
      return true;
    }
    return false;
  }</code></pre><p>这样带来的麻烦就是当文件更名后仍然符合正则表达式时，会被flume进行监控，即使inode相同而文件名不同，flume就认为是新文件。</p><p>实际上这是开发者自身给自己造成的不便，完全可以通过监控文件名的正则表达式来排除重命名的文件。</p><p>就如正则表达式：【.*.log.* 】这样的正则表达式当然文件由 .ac.log 重命名为.ac.log.1会带来重复读取的问题。</p><p>而正则表达式：【.*.log】 当文件由 .ac.log 重命名为 .ac.log.1 就不会被flume监控，就不会有重复读取的问题。</p><p>以上是针对这个问题并flume团队没有改正这个问题原因的思考。</p><p><br></p><p>当然，如果类似【.*.log.* 】这样的正则表达式在实际生产中是非常必要使用的话，那么flume团队应该会根据github上issue的呼声大小来考虑是否修正到项目中。</p><p><br></p><p>那么实际生产中真需要这样的正则表达式来监控目录下的文件的话，为了避免重复读取，就需要对flume1.7源码进行修改：</p><h2>处理问题（1）方式<br></h2><p></p><h3>1.修改 ReliableTaildirEventReader</h3><p>修改 ReliableTaildirEventReader 类的 updateTailFiles方法。</p><p>去除tf.getPath().equals(f.getAbsolutePath()) 。只用判断文件不为空即可，不用判断文件的名字，因为log4j 日志切分文件会重命名文件。</p><pre><code class="language-plain"> if (tf == null || !tf.getPath().equals(f.getAbsolutePath())) {

修改为：
 if (tf == null) {</code></pre><h3>2.修改TailFile</h3><p>修改TailFile 类的 updatePos方法。</p><p>inode 已经能够确定唯一的 文件，不用加 path 作为判定条件</p><pre><code class="language-plain">    if (this.inode == inode &amp;&amp; this.path.equals(path)) {
修改为：
    if (this.inode == inode) {</code></pre><h3>3.将修改过的代码打包为自定义source的jar <br></h3><p>可以直接打包taildirSource组件即可，然后替换该组件的jar</p><p>此时可以进行测试。<br></p><p><br></p><h2>处理问题（2）<br></h2><p>问题（2）说的是，当监控的文件不存在了，flume资源没有释放。</p><p>这个问题也不是问题，实际上，资源的确会释放，但是 是有一定时间等待。</p><p>查看flume1.7官方文档taildirSource说明：</p><p><img src="https://img-blog.csdn.net/20180227201618489?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveXVhbm1pbmcwODA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>可知，如果这个文件在默认值120000ms内都没有新行append，就会关闭资源；而当有新行append就自动打开该资源。</p><p>也就是说，默认120000ms--》2分钟后会自动关闭所谓没有释放的资源。</p><p>为了避免这么长时间的资源浪费，可以把这个值调小一些。但是，官方给定的默认值为什么这么大（相对于类似超时时间都是秒单位的，而这是分钟单位）？当然不能为所欲为的把这个值改小，频繁的开关文件资源造成系统资源的浪费更应该考虑。</p><p>一般没有很好的测试过性能的话，还是按照默认值来就可以了。</p><p><br></p><p><br></p><p>以上，是个人见解，若有研究不当之处，叙述不当之处，敬请指出，倍加感谢！<br></p><p><span class="content"></span></p><p><br><span class="content"></span></p><p><br><span class="content"></span></p><p><br><span class="content"></span></p>            </div>
                </div>