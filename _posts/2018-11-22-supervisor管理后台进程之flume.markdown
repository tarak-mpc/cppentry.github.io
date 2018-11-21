---
layout:     post
title:      supervisor管理后台进程之flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
之前后台进程都是用nohup  *.sh 来跑的，不方便维护和监控，</p>
<div><img src="https://img-blog.csdn.net/20170714141747702?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlhbmdydWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170714141816201?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlhbmdydWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<br><p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
每台服务器启一个flume收集日志的后台进程。</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
可以进行监控，和通过页面，启动，关闭服务，查看日志</p>
<h4 style="color:rgb(51,51,51);font-size:14px;line-height:1.42857;font-family:Arial, sans-serif;">
1.2. 介绍</h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Supervisor是一个进程控制系统. 它是一个C/S系统(注意: 其提供WEB接口给用户查询和控制), 它允许用户去监控和控制在类UNIX系统的进程. 它的目标与launchd, daemontools和runit有些相似, 但是与它们不一样的是, 它不是作为init(进程号pid是1)运行. 它是被用来控制进程, 并且它在启动的时候和一般程序并无二致. </p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
那么通俗点，它的作用是什么？你的Nginx，Tomcat，memcache，Redis...会崩么，不会？好吧，那你自己写的服务器监测脚本呢？好吧，不要再纠结了，交给Supervisor吧，它会帮你维护这些，即使它们不小心崩了，Supervisor会帮你看住它们，维护它们。</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
安装（Centos）：</p>
<ol style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;"><li style="list-style-type:decimal;"># yum install python-setuptools</li><li style="list-style-type:decimal;"># easy_install supervisor</li><li style="list-style-type:decimal;">如果easy_install不好使就从官方下载：</li><li style="list-style-type:decimal;">然后通过python安装：</li><li style="list-style-type:decimal;"># tar zxf supervisor-3.1.3.tar.gz</li><li style="list-style-type:decimal;"># cd supervisor</li><li style="list-style-type:decimal;"># python setup.py install</li></ol><p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<br>
成功安装后可以登陆python控制台输入import supervisor 查看是否能成功加载。<br>
生成配置文件(supervisord.conf)：<br>
echo_supervisord_conf &gt; /etc/supervisord.conf</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
配置文件：</p>
<p></p><pre><code class="language-html">; Sample supervisor config file.
;
; For more information on the config file, please see:
; http://supervisord.org/configuration.html
;
; Notes:
;  - Shell expansion ("~" or "$HOME") is not supported.  Environment
;    variables can be expanded using this syntax: "%(ENV_HOME)s".
;  - Quotes around values are not supported, except in the case of
;    the environment= options as shown below.
;  - Comments must have a leading space: "a=b ;comment" not "a=b;comment".
;  - Command will be truncated if it looks like a config file comment, e.g.
;    "command=bash -c 'foo ; bar'" will truncate to "command=bash -c 'foo ".

[unix_http_server]
file=/tmp/supervisor.sock   ; the path to the socket file
;chmod=0700                 ; socket file mode (default 0700)
;chown=nobody:nogroup       ; socket file uid:gid owner
;username=user              ; default is no username (open server)
;password=123               ; default is no password (open server)

[inet_http_server]         ; inet (TCP) server disabled by default
port=*:9001
;username=admin              ; default is no username (open server)
;password=admin               ; default is no password (open server)

[supervisord]
logfile=/tmp/supervisord.log ; main log file; default $CWD/supervisord.log
logfile_maxbytes=50MB        ; max main logfile bytes b4 rotation; default 50MB
logfile_backups=10           ; # of main logfile backups; 0 means none, default 10
loglevel=info                ; log level; default info; others: debug,warn,trace
pidfile=/tmp/supervisord.pid ; supervisord pidfile; default supervisord.pid
nodaemon=false               ; start in foreground if true; default false
minfds=1024                  ; min. avail startup file descriptors; default 1024
minprocs=200                 ; min. avail process descriptors;default 200
;umask=022                   ; process file creation umask; default 022
;user=chrism                 ; default is current user, required if root
;identifier=supervisor       ; supervisord identifier, default is 'supervisor'
;directory=/tmp              ; default is not to cd during start
;nocleanup=true              ; don't clean up tempfiles at start; default false
;childlogdir=/tmp            ; 'AUTO' child log dir, default $TEMP
;environment=KEY="value"     ; key value pairs to add to environment
;strip_ansi=false            ; strip ansi escape codes in logs; def. false

; The rpcinterface:supervisor section must remain in the config file for
; RPC (supervisorctl/web interface) to work.  Additional interfaces may be
; added by defining them in separate [rpcinterface:x] sections.

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

; The supervisorctl section configures how supervisorctl will connect to
; supervisord.  configure it match the settings in either the unix_http_server
; or inet_http_server section.

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as in [*_http_server] if set
;password=123                ; should be same as in [*_http_server] if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available

; The sample program section below shows all possible program subsection values.
; Create one or more 'real' program: sections to be able to control them under
; supervisor.

;[program:theprogramname]
;command=/bin/cat              ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=999                  ; the relative start priority (default 999)
;autostart=true                ; start at supervisord start (default: true)
;startsecs=1                   ; # of secs prog must stay up to be running (def. 1)
;startretries=3                ; max # of serial start failures when starting (default 3)
;autorestart=unexpected        ; when to restart if exited after running (def: unexpected)
;exitcodes=0,2                 ; 'expected' exit codes used with autorestart (default 0,2)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; send stop signal to the UNIX process group (default false)
;killasgroup=false             ; SIGKILL the UNIX process group (def false)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (0 means none, default 10)
;stdout_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (0 means none, default 10)
;stderr_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A="1",B="2"       ; process environment additions (def no adds)
;serverurl=AUTO                ; override serverurl computation (childutils)

; The sample eventlistener section below shows all possible eventlistener
; subsection values.  Create one or more 'real' eventlistener: sections to be
; able to handle event notifications sent by supervisord.

;[eventlistener:theeventlistenername]
;command=/bin/eventlistener    ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;events=EVENT                  ; event notif. types to subscribe to (req'd)
;buffer_size=10                ; event buffer queue size (default 10)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=-1                   ; the relative start priority (default -1)
;autostart=true                ; start at supervisord start (default: true)
;startsecs=1                   ; # of secs prog must stay up to be running (def. 1)
;startretries=3                ; max # of serial start failures when starting (default 3)
;autorestart=unexpected        ; autorestart if exited after running (def: unexpected)
;exitcodes=0,2                 ; 'expected' exit codes used with autorestart (default 0,2)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; send stop signal to the UNIX process group (default false)
;killasgroup=false             ; SIGKILL the UNIX process group (def false)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=false         ; redirect_stderr=true is not allowed for eventlisteners
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (0 means none, default 10)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (0 means none, default 10)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A="1",B="2"       ; process environment additions
;serverurl=AUTO                ; override serverurl computation (childutils)

; The sample group section below shows all possible group values.  Create one
; or more 'real' group: sections to create "heterogeneous" process groups.

;[group:thegroupname]
;programs=progname1,progname2  ; each refers to 'x' in [program:x] definitions
;priority=999                  ; the relative start priority (default 999)



[program:gdata]
command=/home/web_admin/pro/server/gdata/run.sh
directory=/home/web_admin/pro/server/gdata



[program:x1flume]
command=/home/web_admin/opt/v2_flume-apache170/bin/flume-ng agent --conf /home/web_admin/opt/v2_flume-apache170/conf --conf-file /home/web_admin/opt/v2_flume-apache170/conf/x1_dir_to_db_flume.conf  --name a1 -Dflume.root.logger=INFO,console
directory=/home/web_admin/opt/v2_flume-apache170/starDir
autostart = false
autorestart = false
redirect_stderr=true
stdout_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x1_dir.log
stdout_logfile_maxbytes=200MB
stdout_logfile_backups=10 
stderr_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x1_dir_err.log
stderr_logfile_maxbytes=100MB

[program:x2flume]
command=/home/web_admin/opt/v2_flume-apache170/bin/flume-ng agent --conf /home/web_admin/opt/v2_flume-apache170/conf --conf-file /home/web_admin/opt/v2_flume-apache170/conf/x2_dir_to_db_flume.conf  --name a1 -Dflume.root.logger=INFO,console 
directory=/home/web_admin/opt/v2_flume-apache170/starDir
autostart = false
autorestart = false
redirect_stderr=true
stdout_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x2_dir.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=10

[program:x3flume]
command=/home/web_admin/opt/v2_flume-apache170/bin/flume-ng agent --conf /home/web_admin/opt/v2_flume-apache170/conf --conf-file /home/web_admin/opt/v2_flume-apache170/conf/x3_dir_to_db_flume.conf  --name a1 -Dflume.root.logger=INFO,console 
directory=/home/web_admin/opt/v2_flume-apache170/starDir
autostart = false
autorestart = false
redirect_stderr=true
stdout_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x3_dir.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=10



[program:x4flume]
command=/home/web_admin/opt/v2_flume-apache170/bin/flume-ng agent --conf /home/web_admin/opt/v2_flume-apache170/conf --conf-file /home/web_admin/opt/v2_flume-apache170/conf/x4_dir_to_db_flume.conf  --name a1 -Dflume.root.logger=INFO,console 
directory=/home/web_admin/opt/v2_flume-apache170/starDir
autostart = false
autorestart = false
redirect_stderr=true
stdout_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x4_dir.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=10


[program:x8flume]
command=/home/web_admin/opt/v2_flume-apache170/bin/flume-ng agent --conf /home/web_admin/opt/v2_flume-apache170/conf --conf-file /home/web_admin/opt/v2_flume-apache170/conf/x8_dir_to_db_flume.conf  --name a1 -Dflume.root.logger=INFO,console 
directory=/home/web_admin/opt/v2_flume-apache170/starDir
autostart = false
autorestart = false
redirect_stderr=true
stdout_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x8_dir.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=10


[program:x9flume]
command=/home/web_admin/opt/v2_flume-apache170/bin/flume-ng agent --conf /home/web_admin/opt/v2_flume-apache170/conf --conf-file /home/web_admin/opt/v2_flume-apache170/conf/x9_dir_to_db_flume.conf  --name a1 -Dflume.root.logger=INFO,console 
directory=/home/web_admin/opt/v2_flume-apache170/starDir
autostart = false
autorestart = false
redirect_stderr=true
stdout_logfile=/home/web_admin/opt/v2_flume-apache170/starDir/log/x9_dir.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=10





; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

;[include]
;files = relative/directory/*.ini
</code></pre><br><p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
主要参数说明:</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<span style="color:rgb(0,0,0);">该配置块包含一个或者多个program段，program来表明supervisord要控制哪些程序。该配置块的头部是有固定格式的，一个关键字program，后面跟着一个冒号，接下来才是程序名。例如：[program:foo]，foo就是程序名，在使用supervisorctl来操作程序的时候，就是以foo来标明的。</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
command：启动程序使用的命令，可以是绝对路径或者相对路径<br>
process_name：一个python字符串表达式，用来表示supervisor进程启动的这个的名称，默认值是%(program_name)s<br>
numprocs：Supervisor启动这个程序的多个实例，如果numprocs&gt;1，则process_name的表达式必须包含%(process_num)s，默认是1<br>
numprocs_start：一个int偏移值，当启动实例的时候用来计算numprocs的值<br>
priority：权重，可以控制程序启动和关闭时的顺序，权重越低：越早启动，越晚关闭。默认值是999<br>
autostart：如果设置为true，当supervisord启动的时候，进程会自动重启。<br>
autorestart：值可以是false、true、unexpected。false：进程不会自动重启，unexpected：当程序退出时的退出码不是exitcodes中定义的时，进程会重启，true：进程会无条件重启当退出的时候。<br>
startsecs：程序启动后等待多长时间后才认为程序启动成功<br>
startretries：supervisord尝试启动一个程序时尝试的次数。默认是3<br>
exitcodes：一个预期的退出返回码，默认是0,2。<br>
stopsignal：当收到stop请求的时候，发送信号给程序，默认是TERM信号，也可以是 HUP, INT, QUIT, KILL, USR1, or USR2。<br>
stopwaitsecs：在操作系统给supervisord发送SIGCHILD信号时等待的时间<br>
stopasgroup：如果设置为true，则会使supervisor发送停止信号到整个进程组<br>
killasgroup：如果设置为true，则在给程序发送SIGKILL信号的时候，会发送到整个进程组，它的子进程也会受到影响。<br>
user：如果supervisord以root运行，则会使用这个设置用户启动子程序<br>
redirect_stderr：如果设置为true，进程则会把标准错误输出到supervisord后台的标准输出文件描述符。<br>
stdout_logfile：把进程的标准输出写入文件中，如果stdout_logfile没有设置或者设置为AUTO，则supervisor会自动选择一个文件位置。<br>
stdout_logfile_maxbytes：标准输出log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小<br>
stdout_logfile_backups：标准输出日志轮转备份的数量，默认是10，如果设置为0，则不备份<br>
stdout_capture_maxbytes：当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB<br>
stdout_events_enabled：如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发<br>
stderr_logfile：把进程的错误日志输出一个文件中，除非redirect_stderr参数被设置为true<br>
stderr_logfile_maxbytes：错误log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小<br>
stderr_logfile_backups：错误日志轮转备份的数量，默认是10，如果设置为0，则不备份<br>
stderr_capture_maxbytes：当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB<br>
stderr_events_enabled：如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发<br>
environment：一个k/v对的list列表<br>
directory：supervisord在生成子进程的时候会切换到该目录<br>
umask：设置进程的umask<br>
serverurl：是否允许子进程和内部的HTTP服务通讯，如果设置为AUTO，supervisor会自动的构造一个url<br>
    <br>
eg：</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
启动</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
supervisord -c /home/web_admin/opt/supervisor/supervisord.conf  //启动supervisor</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
打开控制台<br>
supervisorctl</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
status stop start reload update shutdown 命令进行相关操作</p>
<br>            </div>
                </div>