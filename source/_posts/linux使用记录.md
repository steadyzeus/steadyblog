title: linux git 使用记录
author: John Doe
date: 2018-12-18 04:45:53
tags:
---
1.全文磁盘搜索：
grep -rn "hello,world!" *

2.git reset --hard 
至于这几个参数：
--mixed 
意思是：不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
这个为默认参数,git reset --mixed HEAD^ 和 git reset HEAD^ 效果是一样的。
 
--soft  
不删除工作空间改动代码，撤销commit，不撤销git add . 
 
--hard
删除工作空间改动代码，撤销commit，撤销git add . 

注意完成这个操作后，就恢复到了上一次的commit状态。

3.

 