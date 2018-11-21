---
layout:     post
title:      HDFS快照 | HDFS Snapshots
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎转载，注明作者和出处就好！如果不喜欢或文章存在明显的谬误，请留言说明原因再踩哦，谢谢，我也可以知道原因，不断进步！					https://blog.csdn.net/Coder__CS/article/details/79430333				</div>
								            <div id="content_views" class="markdown_views prism-tomorrow-night">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><h1 id="hdfs快照"><font><font>HDFS快照</font></font></h1><p></p>

<p><a></a></p>

<div class="section" id="Overview">
<h2 id="概况"><a></a><font><font>概况</font></font></h2>

<p><font><font>
    HDFS快照是文件系统的只读时间点副本。</font><font>快照可以在文件系统的子树上或整个文件系统上进行。</font><font>快照的一些常见用例是数据备份，防止用户错误和灾难恢复。
  </font></font></p>


<p><font><font>
    HDFS快照的实施非常高效：
  </font></font></p>

<ul>

<li><font><font>快照创建是即时的：成本为</font></font><i><font><font>O（1），</font></font></i><font><font>不包括索引节点查找时间。</font></font></li>

<li><font><font>额外内存仅在相对于快照进行修改时使用：内存使用量为</font></font><i><font><font>O（M）</font></font></i><font><font>，其中</font></font><i><font><font>M</font></font></i><font><font>是修改的文件/目录的数量。</font></font></li>

<li><font><font>datanodes中的块不被复制：快照文件记录块列表和文件大小。</font><font>没有数据复制。</font></font></li>

<li><font><font>快照不会对常规HDFS操作产生不利影响：以反向时间顺序记录修改，以便可以直接访问当前数据。</font><font>快照数据是通过从当前数据中减去修改来计算的。</font></font></li>
  </ul>

  <a></a>
<div class="section" id="SnapshottableDirectories">
<h3 id="快照目录"><a></a><font><font>快照目录</font></font></h3>

<p><font><font>
    一旦目录设置为</font></font><i><font><font>snapshottable，</font></font></i><font><font>可以在任何目录上拍摄
     </font><i><font>快照</font></i><font>。</font><font>快照表目录能够容纳65,536个同步快照。</font><font>快照表目录的数量没有限制。</font><font>管理员可以将任何目录设置为快照。</font><font>如果快照可用目录中有快照，则在删除所有快照之前，既不能删除目录，也不能将其重命名。
  </font></font></p>


<p><font><font>
    当前不允许嵌套快照可用目录。</font><font>换句话说，如果某个目录的祖先/后代之一是快照表目录，则不能将该目录设置为snapshottable。
  </font></font></p>

  </div>

<p><a></a></p>

<div class="section" id="SnapshotPaths">
<h3 id="快照路径"><a></a><font><font>快照路径</font></font></h3>

<p><font><font>
    对于快照表目录，路径组件</font></font><i><font><font>“.snapshot”</font></font></i><font><font>用于访问其快照。</font><font>假设</font></font><tt><font><font>/foo</font></font></tt><font><font>是一个snapshottable目录
     </font></font><tt><font><font>/foo/bar</font></font></tt><font><font>是在一个文件/目录</font></font><tt><font><font>/foo</font></font></tt><font><font>，和</font></font><tt><font><font>/foo</font></font></tt><font><font>具有快照</font></font><tt><font><font>S0</font></font></tt><font><font>。</font><font>然后，路径</font></font></p>
<div class="source">
<pre><font><font>/foo/.snapshot/s0/bar</font></font></pre></div><font><font>
    指的是</font></font><tt><font><font>/ foo / bar</font></font></tt><font><font>的快照副本</font><font>。</font><font>通常的API和CLI可以使用“.snapshot”路径。</font><font>以下是一些例子。


</font></font><ul>

<li><font><font>列出快照表目录下的所有快照：

</font></font><div class="source">
<pre><font><font>hdfs dfs -ls /foo/.snapshot</font></font></pre></div></li>

<li><font><font>列出快照</font></font><tt><font><font>s0中</font></font></tt><font><font>的文件</font><font>：

</font></font><div class="source">
<pre><font><font>hdfs dfs -ls /foo/.snapshot/s0</font></font></pre></div></li>

<li><font><font>从快照</font></font><tt><font><font>s0</font></font></tt><font><font>复制文件</font><font>：

</font></font><div class="source">
<pre><font><font>hdfs dfs -cp -ptopax /foo/.snapshot/s0/bar/tmp</font></font></pre></div>

<p><font><font>请注意，此示例使用preserve选项来保留时间戳，所有权，权限，ACL和XAttrs。</font></font></p></li>
  </ul>
  </div>
  </div>

  <a></a>
<div class="section" id="Upgrade">
<h2 id="使用快照升级到hdfs版本"><a></a><font><font>使用快照升级到HDFS版本</font></font></h2>


<p><font><font>
    HDFS快照功能引入了用于与快照进行交互的新保留路径名：</font></font><tt><font><font>.snapshot</font></font></tt><font><font>。</font><font>从不支持快照的旧版HDFS进行升级时，现有的名为</font></font><tt><font><font>.snapshot的</font></font></tt><font><font>路径</font><font>需要先重命名或删除，以避免与保留路径冲突。</font><font>有关</font><font>
    更多信息，</font><font>请参阅</font></font><a href="http://hadoop.apache.org/docs/r2.7.5/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#Upgrade_and_Rollback" rel="nofollow"><font><font>HDFS用户指南中</font></font></a><font><font>的升级部分
     </font><font>。  </font></font></p>

<p></p></div>

<p><a></a></p>

<div class="section" id="SnapshotOperations">
<h2 id="快照操作"><a></a><font><font>快照操作</font></font></h2>
  <a></a>
<div class="section" id="AdministratorOperations">
<h3 id="管理员操作"><a></a><font><font>管理员操作</font></font></h3>

<p><font><font>
    本节中描述的操作需要超级用户权限。
  </font></font></p>


<div class="section">
<h4 id="允许快照"><a></a><font><font>允许快照</font></font></h4>

<p><font><font>
    允许创建目录的快照。</font><font>如果操作成功完成，则该目录变为快照可见。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs dfsadmin -allowSnapshot &lt;path&gt;</font></font></pre></div></li>

<li><font><font>参数：
</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>路径</font></font></td>
<td><font><font>快照表目录的路径。</font></font></td></tr>
    </tbody></table></li>
  </ul>

<p><font><font>
    另请参见相应的Java API
     </font></font><tt><font><font>无效allowSnapshot（路径路径）</font></font></tt><font><font>在</font></font><tt><font><font>HdfsAdmin</font></font></tt><font><font>。
  </font></font></p>

  </div>

<div class="section">
<h4 id="禁止快照"><a></a><font><font>禁止快照</font></font></h4>

<p><font><font>
    禁止要创建的目录的快照。</font><font>在禁用快照之前，必须删除目录的所有快照。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs dfsadmin -disallowSnapshot &lt;path&gt;</font></font></pre></div></li>

<li><font><font>参数：
</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>路径</font></font></td>
<td><font><font>快照表目录的路径。</font></font></td></tr>
    </tbody></table></li>
  </ul>

<p><font><font>
    另请参见相应的Java API
     </font></font><tt><font><font>无效disallowSnapshot（路径路径）</font></font></tt><font><font>在</font></font><tt><font><font>HdfsAdmin</font></font></tt><font><font>。
  </font></font></p>
  </div></div>

<p><a></a></p>

<div class="section" id="UserOperations">
<h3 id="用户操作"><a></a><font><font>用户操作</font></font></h3>

<p><font><font>
    本节介绍用户操作。</font><font>请注意，HDFS超级用户可以在不满足个别操作中的权限要求的情况下执行所有操作。
  </font></font></p>


<div class="section">
<h4 id="创建快照"><a></a><font><font>创建快照</font></font></h4>

<p><font><font>
    创建快照表目录的快照。</font><font>此操作需要snapshottable目录的所有者权限。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs dfs -createSnapshot &lt;path&gt; [&lt;snapshotName&gt;]</font></font></pre></div></li>

<li><font><font>参数：
</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>路径</font></font></td>
<td><font><font>快照表目录的路径。</font></font></td></tr>

<tr class="b">
<td><font><font>snapshotName</font></font></td>
<td><font><font>
        快照名称，它是一个可选参数。</font><font>省略时，使用格式为</font></font><tt><font><font>”syyyyMMdd-HHmmss.SSS’</font></font></tt><font><font>的时间戳生成默认名称
         </font><font>，例如“s20130412-151029.033”。
      </font></font></td></tr>
    </tbody></table></li>
  </ul>

<p><font><font>
    另请参见相应的Java API
     </font></font><tt><font><font>路径createSnapshot（路径路径）</font></font></tt><font><font>和
     </font></font><tt><font><font>路径createSnapshot（路径路径字符串snapshotName）</font></font></tt><font><font> 
    的</font></font><a href="http://hadoop.apache.org/docs/r2.7.5/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow"><tt><font><font>文件系统</font></font></tt></a><font><font>。</font><font>快照路径在这些方法中返回。
  </font></font></p>

  </div>

<div class="section">
<h4 id="删除快照"><a></a><font><font>删除快照</font></font></h4>

<p><font><font>
    从快照表目录中删除快照。</font><font>此操作需要snapshottable目录的所有者权限。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs dfs -deleteSnapshot &lt;path&gt; &lt;snapshotName&gt;</font></font></pre></div></li>

<li><font><font>参数：
</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>路径</font></font></td>
<td><font><font>快照表目录的路径。</font></font></td></tr>

<tr class="b">
<td><font><font>snapshotName</font></font></td>
<td><font><font>快照名称。</font></font></td></tr>
    </tbody></table></li>
  </ul>

<p><font><font>
    另请参见相应的Java API
     </font></font><tt><font><font>无效deleteSnapshot（路径路径字符串snapshotName）</font></font></tt><font><font> 
    的</font></font><a href="http://hadoop.apache.org/docs/r2.7.5/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow"><tt><font><font>文件系统</font></font></tt></a><font><font>。
  </font></font></p>

  </div>

<div class="section">
<h4 id="重命名快照"><a></a><font><font>重命名快照</font></font></h4>

<p><font><font>
    重命名快照。</font><font>此操作需要snapshottable目录的所有者权限。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs dfs -renameSnapshot &lt;path&gt; &lt;oldName&gt; &lt;newName&gt;</font></font></pre></div></li>

<li><font><font>参数：
</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>路径</font></font></td>
<td><font><font>快照表目录的路径。</font></font></td></tr>

<tr class="b">
<td><font><font>使用oldName</font></font></td>
<td><font><font>旧的快照名称。</font></font></td></tr>

<tr class="a">
<td><font><font>新名字</font></font></td>
<td><font><font>新的快照名称。</font></font></td></tr>
    </tbody></table></li>
  </ul>

<p><font><font>
    另请参见相应的Java API
     </font></font><tt><font><font>无效renameSnapshot（路径路径，字符串使用oldName，字符串newName）将</font></font></tt><font><font> 
    在</font></font><a href="http://hadoop.apache.org/docs/r2.7.5/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow"><tt><font><font>文件系统</font></font></tt></a><font><font>。
  </font></font></p>

  </div>

<div class="section">
<h4 id="获取快照目录列表"><a></a><font><font>获取快照目录列表</font></font></h4>

<p><font><font>
    获取当前用户有权拍摄快照的所有快照表目录。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs lsSnapshottableDir</font></font></pre></div></li>

<li><font><font>参数：无</font></font></li>
  </ul>

<p><font><font>
    另请参见相应的Java API
     </font></font><tt><font><font>SnapshottableDirectoryStatus [] getSnapshottableDirectoryListing（）</font></font></tt><font><font> 
    中</font></font><tt><font><font>DistributedFileSystem</font></font></tt><font><font>。
  </font></font></p>

  </div>

<div class="section">
<h4 id="获取快照差异报告"><a></a><font><font>获取快照差异报告</font></font></h4>

<p><font><font>
    获取两个快照之间的差异。</font><font>此操作需要两个快照中的所有文件/目录的读取权限。
  </font></font></p>

<ul>

<li><font><font>命令：

</font></font><div class="source">
<pre><font><font>hdfs snapshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;</font></font></pre></div></li>

<li><font><font>参数：
</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>路径</font></font></td>
<td><font><font>快照表目录的路径。</font></font></td></tr>

<tr class="b">
<td><font><font>fromSnapshot</font></font></td>
<td><font><font>开始快照的名称。</font></font></td></tr>

<tr class="a">
<td><font><font>toSnapshot</font></font></td>
<td><font><font>结束快照的名称。</font></font></td></tr>
    </tbody></table></li>

<li><font><font>结果：

</font></font><table border="0" class="bodyTable">

<tbody><tr class="a">
<td><font><font>+</font></font></td>
<td><font><font>文件/目录已创建。</font></font></td></tr>

<tr class="b">
<td><font><font> - </font></font></td>
<td><font><font>文件/目录已被删除。</font></font></td></tr>

<tr class="a">
<td><font><font>M</font></font></td>
<td><font><font>文件/目录已被修改。</font></font></td></tr>

<tr class="b">
<td><font><font>R</font></font></td>
<td><font><font>文件/目录已被重命名。</font></font></td></tr>
      </tbody></table>
    </li>
  </ul>

<p><font><font>
    一个</font></font><i><font><font>RENAME</font></font></i><font><font>条目表示一个文件/目录已被重命名，但仍然是相同的snapshottable目录下。</font><font>如果将文件/目录重命名为快照目录外部，则会将其报告为已删除。</font><font>从snapshottble目录外部重命名的文件/目录将被报告为新创建。
  </font></font></p>

<p><font><font>
    快照差异报告不保证相同的操作顺序。</font><font>例如，如果我们将目录</font></font><i><font><font>“/ foo”</font></font></i><font><font>重命名</font><font>为</font></font><i><font><font>“/ foo2”</font></font></i><font><font>，然后将新数据附加到文件</font></font><i><font><font>“/ foo2 / bar”</font></font></i><font><font>，则差异报告将为：
    </font></font></p>
<div class="source">
<pre><font><font>    R. / foo  - &gt; / foo2</font></font><font></font><font><font>
    M. / foo / bar</font></font><font></font>
    </pre></div><font><font>
    也就是说，重命名前的原始路径（上例中的</font></font><i><font><font>“/ foo / bar”</font></font></i><font><font>）</font><font>会报告重命名目录下文件/目录的更改</font><font>。


</font></font><p><font><font>





</font></font></p></div></div></div>

<h1 id="快照部署操作实例">快照部署操作实例</h1>

<p>操作实例见我的另一篇文章 <strong><a href="http://blog.csdn.net/Coder__CS/article/details/79430481" rel="nofollow">HDFS创建全局快照</a></strong></p>

<blockquote>
  <p>参考： <br>
  [1] HDFS Snapshots <br>
  <a href="http://hadoop.apache.org/docs/r2.7.5/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html" rel="nofollow">http://hadoop.apache.org/docs/r2.7.5/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html</a></p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>