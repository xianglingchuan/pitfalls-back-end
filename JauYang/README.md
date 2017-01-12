>JavaWeb工程占用过高的CPU和内存比
**描述：**主要是各个web工程占用较高的cpu和ram，导致服务器运行缓慢<br/>
**问题：**web工程占用过多的cpu和ram<br/>
**原因：**每个web工程都独立一个tomcat，并且没有去设置tomcat的最大最小内存，导致程序启动时占用过高的ram和cpu<br/>
**解决：**<br/>
  >1、
