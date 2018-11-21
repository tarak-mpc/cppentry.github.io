---
layout:     post
title:      Hbase 设置irbrc保存hbase的操作命令历史
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wisgood/article/details/23380907				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">Hbase 设置irbrc保存hbase的操作命令历史</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">在用户目录下，新建一个文件.irbrc，输入以下内容<br><br>
require 'irb/ext/save-history'<br>
IRB.conf[:SAVE_HISTORY] = 100<br>
IRB.conf[:HISTORY_FILE] = "#{ENV['HOME']}/.irb_history"<br>
Kernel.at_exit do<br>
    IRB.conf[:AT_EXIT].each do |i|<br>
        i.call<br>
    end<br>
end<br><br><br>
重新进入hbase shell，执行几个命令，退出后，命令会保存在用户目录中的.irb_history文件中</span><br></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140410203832515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lzZ29vZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:18px;"><img src="" alt=""><br></span></p>
<p><span style="font-size:18px;"><img src="" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>