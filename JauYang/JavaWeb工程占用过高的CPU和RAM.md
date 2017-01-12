###[回到首页](https://github.com/JauYang/pitfalls-back-end)
#JavaWeb工程占用过高的CPU和RAM
====

**描述：**主要是各个web工程占用较高的cpu和ram，导致服务器运行缓慢

**问题：**web工程占用过多的cpu和ram

**原因：**每个web工程都独立一个tomcat，并且没有去设置tomcat的最大最小内存，导致程序启动时占用过高的ram

**解决：**

1、先修复cpu占用较高的问题，一般情况javaweb程序占用cpu过高的原因死循环在占用进程<br/>
*查看占用cpu较高的进程：<br/>
 ```shell
  $ ps auxw --sort=rss
 ```
  ![image](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/Uploads/Images/%E6%9F%A5%E7%9C%8B%E5%8D%A0%E7%94%A8CPU%E8%BE%83%E9%AB%98%E7%9A%84%E8%BF%9B%E7%A8%8B.png)
  可以看到该该进程为我们的web程序并且占用的cpu较高<br/>
*查看该进程对应的哪些线程在占用cpu：<br/>
 ```shell
 $ ps -mp 10847 -o THREAD,tid,time
 ```
 ![image](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/Uploads/Images/%E6%9F%A5%E7%9C%8B%E8%BF%9B%E7%A8%8B%E4%B8%8B%E5%90%84%E7%BA%BF%E7%A8%8B%E7%9A%84cpu%E5%8D%A0%E7%94%A8%E6%83%85%E5%86%B5.png)<br/>
 可以看到这三个线程占用的cpu较高：<br/>
 ![image](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/Uploads/Images/%E8%BF%99%E4%B8%89%E4%B8%AA%E7%BA%BF%E7%A8%8B%E5%8D%A0%E7%94%A8cpu%E8%BE%83%E9%AB%98.png)<br/>
*将线程tid转为16进制<br/>
 ```shell
 $ printf "%x\n" 11111
 ```
  ![image](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/Uploads/Images/%E7%BA%BF%E7%A8%8BTID%E8%BD%AC16%E8%BF%9B%E5%88%B6.png)<br/>
*使用java的性能检测工具jstack来查看该线程占用cpu过高的问题出在哪里？<br/>
 ```shell
 $ jstack 10847 | grep 2b67 -A 30
 ```
  ![image](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/Uploads/Images/%E7%BA%BF%E7%A8%8B%E8%A2%AB%E5%93%AA%E9%87%8C%E5%8D%A0%E7%94%A8.png)<br/>
  可以定位到该java类的382行的代码有问题，去查看代码：<br/>
  ```java
   while (true) {
     if (mResult.toString().length() > 0) {
      break;
     }
    }
   ```
  的确是该处有一个死循环的代码，导致该web工程占用的cpu较高，找到问题的产生原因，接下来就是针对具体的问题具体解决。<br/>
  
2、解决占用ram较高的问题：<br/>
    一般设置tomcat的堆内存即可：<br/>
    catalina.sh文件的第一行加入下边这句： <br/>
    JAVA_OPTS='-Xms128m -Xmx256m -XX:PermSize=256M -XX:MaxNewSize=512m'<br/>
    -Xms128m JVM初始分配的堆内存<br/>
    -Xmx256m JVM最大允许分配的堆内存，按需分配<br/>
    -XX:PermSize=256M JVM初始分配的非堆内存<br/>
    -XX:MaxPermSize=512M JVM最大允许分配的非堆内存，按需分配<br/>
