# pitfalls-back-end
那些年，我们一起踩过的后端的坑！

###1、iOS WebView 加载我们自己服务器网页，部分脚本无法加载

**描述：**iOS WebView 加载我们自己服务器网页，部分脚本无法加载, 主要是更换https以后出现的情况。   

**问题：** iOS WebView 加载我们自己服务器网页，部分脚本无法加载

**原因：**主要是H5端js调用的接口不对，以前的域名为bank.snaillove.com，但在国外SSL签名的时候说bank是关键字不能使用，所以就换成banks.snaillove.com进行了SSL认证。   

**解决：**   
>前端同事直接修改H5端调用的接口即可

###2、[JavaWeb工程占用过高的CPU和RAM](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/JavaWeb%E5%B7%A5%E7%A8%8B%E5%8D%A0%E7%94%A8%E8%BF%87%E9%AB%98%E7%9A%84CPU%E5%92%8CRAM.md)

###3、[MongDB在linux服务端配置安装以及数据备份](https://github.com/xianglingchuan/pitfalls-back-end/blob/master/mongodb-install/mongodb%E5%9C%A8linux%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE.md)

###4、[linux sshfs挂载命令使用](https://github.com/xianglingchuan/pitfalls-back-end/blob/master/sshfs/sshfs%E6%8C%82%E8%BD%BD%E5%91%BD%E4%BB%A4%E4%BD%BF%E7%94%A8.md)

###5、启动防火墙时报错(执行service iptables restart时报错误)

**问题：** 启动防火墙时报错(执行service iptables restart时报错误)

**描述：**启动防火墙时报错(执行service iptables restart时报错误)   
` 
[root@iZ23l37x16xZ ~]#  service iptables restart
iptables：正在卸载模块：                                   [确定]
'ptables：应用防火墙规则：iptables-restore v1.4.7: iptables-restore: unable to initialize table filter
Error occurred at line: 2
Try iptables-restore -h or iptables-restore --help for more information. [失败]
`

**原因：**验证以后发现是以前用vi /etc/systemconfig/iptables时出现没有保存就退出的原因。
导致文档里面可能存在空格或者错误，虽然与其它服务器上的对比用肉眼没有发现问题。

**解决方法：** 直接copy另外一台服务器的iptables文件重启防火墙成功。
