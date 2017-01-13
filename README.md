# pitfalls-back-end
那些年，我们一起踩过的后端的坑！

###1、iOS WebView 加载我们自己服务器网页，部分脚本无法加载

**描述：**iOS WebView 加载我们自己服务器网页，部分脚本无法加载, 主要是更换https以后出现的情况。   

**问题：** iOS WebView 加载我们自己服务器网页，部分脚本无法加载

**原因：**主要是H5端js调用的接口不对，以前的域名为bank.snaillove.com，但在国外SSL签名的时候说bank是关键字不能使用，所以就换成banks.snaillove.com进行了SSL认证。   

**解决：**   
>前端同事直接修改H5端调用的接口即可

###2、[JavaWeb工程占用过高的CPU和RAM](https://github.com/JauYang/pitfalls-back-end/blob/master/JauYang/JavaWeb%E5%B7%A5%E7%A8%8B%E5%8D%A0%E7%94%A8%E8%BF%87%E9%AB%98%E7%9A%84CPU%E5%92%8CRAM.md)
