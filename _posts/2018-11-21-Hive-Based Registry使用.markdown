---
layout:     post
title:      Hive-Based Registry使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="color:#638a4e;"><span style="background-color:#faffd0;"><span style="font-family:Arial;">对于Hive干什么用这里就不多介绍了，根据文档的介绍，Hive形式的注册表分为：Boot hive,System hv和User hive三种。Boot hive就是指那部分在Boot过程中需要使用到的注册表信息，这部分是实在没办法放在System hive中了，所以就把它们单独放在Boot hive中了。System
 hive是不用放在Boot中的和系统相关的注册表信息，而User hive则是用户相关的注册表信息了。下面就看看这三个部分的具体内容：<span></span></span></span></span>
<p><span style="font-size:14px;line-height:28px;"> 1.Boot hive ，原文是这样介绍的：</span></p>
<p><span style="font-size:14px;line-height:28px;">The boot hive contains system settings that are applied only during boot. The boot hive is read out of ROM and used to start drivers and file systems needed to reach the
 system hive file. Once the system hive is mounted, the boot hive is discarded. Changes made to the registry during the boot process are copied into the system hive once it is available. The boot hive
 in ROM remains unchanged.</span></p>
<p><span style="font-size:14px;line-height:28px;">从这段介绍中可以理解Boot中的信息是用来在后面获得System hive而在Boot阶段所必需包括的注册表信息。因此Boot hive中的内容只要能让后面获得System hive成为可能就可以了。</span></p>
<p><span style="font-size:14px;line-height:28px;"> 2.System hive，原文如下：</span></p>
<p><span style="font-size:14px;line-height:28px;">The system hive contains system settings that do not pertain to any one user. The OEM selects the system hive file name and location. The system hive file is typically
 named System.hv, but the location can vary.</span></p>
<p><span style="font-size:14px;line-height:28px;">The registry value<span> </span><strong style="line-height:28px;">SystemHive</strong><span> </span>under<span> </span><strong style="line-height:28px;">HKEY_LOCAL_MACHINE\init\BootVars</strong><span> </span>indicates
 the path and file name of the system hive.</span></p>
<p><span style="font-size:14px;line-height:28px;">从中可以看出System和用户无关，由OEM来选择System hive的文件名与位置，默认的System hive会被命名为System.hv，但位置不固定。在<strong style="line-height:28px;">HKEY_LOCAL_MACHINE\init\BootVars</strong><span> </span>键下的值<strong style="line-height:28px;">SystemHive </strong>指定System
 hive的名字和路径。</span></p>
<p><span style="font-size:14px;line-height:28px;"> 3.User hive,原文如下：</span></p>
<p><span style="font-size:14px;line-height:28px;">A user hive contains user-specific settings. All user hives are named User.hv and are located inside separate user directories. Each directory is given the name of the user to which it belongs.</span></p>
<p><span style="font-size:14px;line-height:28px;">The registry value<span> </span><strong style="line-height:28px;">ProfileDir</strong><span> </span>under<span> </span><strong style="line-height:28px;">HKEY_LOCAL_MACHINE\init\BootVars</strong><span> </span>indicates
 the location where all user directories are placed.</span></p>
<p><span style="font-size:14px;line-height:28px;">User hive包括了和用户相关的注册表信息。所有的User hive都是一样的名字：User.hv。但是放在不同的用户目录下，例如有两个用户的目录A，B，两个目录里的User hive都叫做User.hv。但是一个是A/User.hv。另一个是B/User.hv。同System hive类似，在<strong style="line-height:28px;">HKEY_LOCAL_MACHINE\init\BootVars</strong><span> </span>下的<strong style="line-height:28px;">ProfileDir</strong><span> </span>指定了所有用户目录存放的路径。</span></p>
<p><span style="font-size:14px;line-height:28px;"> 有了以上的信息，下面来看如何使能Hive Registry。</span></p>
<p><span style="font-size:14px;line-height:28px;"> 1.在PB中把Hive-based Registry Catalog item加进来。</span></p>
<p><span style="font-size:14px;line-height:28px;"> 2.添加注册表[HKEY_LOCAL_MACHINE\init\BootVars]下的内容：<br style="line-height:28px;">
    "SystemHive"="&lt;your system hive location&gt;"</span></p>
<p><span style="font-size:14px;line-height:28px;">    "ProfileDir"="&lt;your user hive location&gt;"</span></p>
<p><span style="font-size:14px;line-height:28px;">    "DefaultUser"="&lt;username&gt;"<br style="line-height:28px;">
    "Flags"=dword:&lt;your value&gt;</span></p>
<p><span style="font-size:14px;line-height:28px;"> 3.把在Boot阶段所需要的注册表信息封装在所示的注释中</span></p>
<p><span style="font-size:14px;line-height:28px;">   ; HIVE BOOT SECTION<br style="line-height:28px;">
   &lt;your registry settings&gt;<br style="line-height:28px;">
   ; END HIVE BOOT SECTION<br style="line-height:28px;">
 4.在存放System hive的分区，要做相应的设置，在CE5.0之后的要设<strong style="line-height:28px;">MountAsBootable = dword:1</strong>.对于早期的版本，则需要设置<strong style="line-height:28px;">MountFlags =dword :2</strong>。</span></p>
<p><span style="font-size:14px;line-height:28px;">分区路径为[HKEY_LOCAL_MACHINE\System\StorageManager\Profiles\&lt;MyProfile&gt;\&lt;MyFileSystem&gt;]<br style="line-height:28px;">
 5.如果希望在每次修改后自动保存，那么在[HKEY_LOCAL_MACHINE\init\BootVars]下设置                             "RegistryFlags"=dword:1</span></p>
<p><span style="font-size:14px;line-height:28px;"> 以上都是根据PB的帮助文档和别人的Blog获得的信息，能不能使用起来我还没有验证过。不过应该差不多了，最后说一下自己的一点理解，也算一个例子吧。例如我们的系统使用BINFS和FATFS，System.hv存放在FAT上面，那么在Boot Hive中需要把BINFS和FATFS都使能起来，不然在后面的加载过程中得不到System.hv的话其他驱动就没法加载了。只有在Boot Hive中把BINFS和FATFS都使能起来，这样才能在FAT分区中获得System.hv这样才能保证后面的驱动正常的加载。</span></p>
            </div>
                </div>