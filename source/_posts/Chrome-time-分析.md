title: Chrome time 分析
author: Steady
date: 2020-07-03 10:18:39
tags:
---
# Stalled（阻塞）

　　浏览器对同一个主机域名的并发连接数有限制，因此如果当前的连接数已经超过上限，那么其余请求就会被阻塞，等待新的可用连接；此外脚本也会阻塞其他组件的下载；

　　优化措施：

　　1、将资源合理分布到多台主机上，可以提高并发数，但是增加并行下载数量也会增大开销，这取决于带宽和CPU速度，过多的并行下载会降低性能；

　　2、脚本置于页面底部；

 

# DNS Lookup（域名解析）

　　请求某域名下的资源，浏览器需要先通过DNS解析器得到该域名服务器的IP地址。在DNS查找完成之前，浏览器不能从主机名那里下载到任何东西。

　　优化措施：

　　1、利用DNS缓存（设置TTL时间）；

　　2、利用Connection:keep-alive特性建立持久连接，可以在当前连接上进行多个请求，无需再进行域名解析；

 

# Initial connection（初始化连接）

　　TCP建立连接的三次握手时间

 

# SSL（包含于HTTPS连接中）

　　http是超文本传输协议，以明文方式发送内容，不提供任何方式的数据加密，如果被不法分子截取浏览器和服务器之间的传输报文，会获取其中的信息。

　　https 是安全套接字层超文本传输协议，就是在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。

　　因此建立HTTPS连接的时间相当于三次握手的时间+SSL时间。

 

# Request sent（发送请求）

　　发送HTTP请求的时间（从第一个bit到最后一个bit）

　　优化措施：

　　1、减少HTTP请求，可以使用CSS Sprites、内联图片、合并脚本和样式表等；

　　2、对不常变化的组件添加长久的Expires头（相当于设置久远的过期时间），在后续的页面浏览中可以避免不必要的HTTP请求；

 

# Waiting（等待响应）

　　通常是耗费时间最长的。从发送请求到收到响应之间的空隙，会受到线路、服务器距离等因素的影响。

　　优化措施：

　　1、使用CDN，将用户的访问指向距离最近的工作正常的缓存服务器上，由缓存服务器直接响应用户请求，提高响应速度；

 

# Content Download（下载）

　　下载HTTP响应的时间（包含头部和响应体）

　　优化措施：

　　1、通过条件Get请求，对比If-Modified-Since和Last-Modified时间，确定是否使用缓存中的组件，服务器会返回“304 Not Modified”状态码，减小响应的大小；

　　2、移除重复脚本，精简和压缩代码，如借助自动化构建工具grunt、gulp等；

　　3、压缩响应内容，服务器端启用gzip压缩，可以减少下载时间