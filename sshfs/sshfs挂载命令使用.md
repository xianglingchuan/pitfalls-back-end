
###[回到首页](https://github.com/xianglingchuan/pitfalls-back-end)
#linux sshfs挂载命令使用
====

**描述：**主要解决在执行挂载命令以后Web应用程序无法上传文件到指定挂载点

**原因：**需要在执行命令时添加-o allow_other参数。

**解决方法：**

以前经常出现执行挂载命令以后,shell命令可以直接进入挂载点操作，但远程程序一直无法操作成功。 <BR>
后来发现是需要加上-o 参数： <BR>
执行sshfs –h 会看到如下帮助说明<BR>
![](file:///C:/Users/xlc/Desktop/github更新/22.png)


FUSE options: <BR>
FUSE（用户空间文件系统） <BR>
用户空间文件系统（Filesystem in Userspace），是Linux 中用于挂载某些网络空间，如SSH，到本地文件系统的模块，在SourceForge上可以找到相关内容。<BR>


`
sshfs root@127.0. 0. 0:/data/www/rabbit/Upload/    /mnt/file_server_storage
//替换为以下命令即可
sshfs root@127.0. 0. 0:/data/www/rabbit/Upload/    /mnt/file_server_storage –o allow_other
`

配合的在/etc下面增加了一个文件fuse.conf 
在fuse.conf里面就一句话: user_allow_other，保存退出即可。

sshfs安装使用说明请见：http://blog.csdn.net/xianglingchuan/article/details/54411754





