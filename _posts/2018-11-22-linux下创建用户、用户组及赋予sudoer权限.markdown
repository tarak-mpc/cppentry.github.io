---
layout:     post
title:      linux下创建用户、用户组及赋予sudoer权限
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
 
<div class="clear"></div>
<div class="postBody">
<div id="cnblogs_post_body">
<p>如现在创建用户hadoop、用户组hadoop，并赋予用户hadoop以sudo权限。</p>
<p>以下命令均需要超级用户运行。</p>
<p>1 创建用户组hadoop</p>
<div class="cnblogs_code">
<pre>groupadd hadoop</pre>
</div>
<p><span style="line-height:1.5;">2 创建用户hadoop并添加进用户组hadoop</span></p>
<div class="cnblogs_code">
<pre>useradd -d /home/hadoop -g hadoop -s /bin/bash -m hadoop</pre>
</div>
<p>其含义如下：</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" href="" rel="nofollow"><img alt="复制代码" src="http://common.cnblogs.com/images/copycode.gif"></a></span></div>
<pre>-d, --<span style="color:rgb(0,0,0);">home HOME_DIR

</span>-g, --<span style="color:rgb(0,0,0);">gid GROUP

-s, --shell SHELL

</span>-m, --create-home</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" href="" rel="nofollow"><img alt="复制代码" src="http://common.cnblogs.com/images/copycode.gif"></a></span></div>
</div>
<p>详见：</p>
<div class="cnblogs_code">
<pre><span style="color:rgb(0,0,255);">man</span> useradd</pre>
</div>
<p>3 添加sudoer权限</p>
<div class="cnblogs_code">
<pre>visudo</pre>
</div>
<p>在文中root行下添加hadoop行</p>
<div class="cnblogs_code">
<pre>root ALL=<span style="color:rgb(0,0,0);">(ALL:ALL) ALL
hadoop ALL</span>=(ALL:ALL) ALL</pre>
</div>
<p>其中：</p>
<p>visudo &lt;=&gt; vi /etc/sudoers</p>
</div>
</div>
            </div>
                </div>