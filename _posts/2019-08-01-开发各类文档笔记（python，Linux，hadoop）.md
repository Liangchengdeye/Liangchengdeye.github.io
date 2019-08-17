# 开发各类文档笔记，python，Linux，hadoop 

#  python相关模块
###  subprocess模块
 python调用实现linux命令
###  python 一键安装依赖
https://blog.csdn.net/loyachen/article/details/52028825
###  mrjob 模块
实现python操作hadoop的MapReduce
https://github.com/Yelp/mrjob
###  hdfs模块
python 对hdfs进行操作
###  requests-html 原requests作者新开发爬虫模块
github：https://github.com/kennethreitz/requests-html
中文文档：https://cncert.github.io/requests-html-doc-cn/#/
###  myqr模块
实现python生成各类验证码
https://blog.csdn.net/bruce_6/article/details/83006956
### 虚拟环境打包：pip freeze > requirements.txt
安装：pip install -r requirements.txt
### 爬虫管理前端Gerapy分布式爬虫管理框架
https://blog.csdn.net/weixin_42336579/article/details/81103681
### python 测试模块coverage，代码覆盖率测试
https://blog.csdn.net/qq_19467623/article/details/78675823
# linux 
### 统计被查询关键字出现的总数：
:%s/string/&/gn
### linux跳转行
:10
### 统计连接数
netstat -nat|grep -i "80"|wc -l
### redis 统计连接数
info clients
### redis 查看连接IP列表
client list

### 1. top 
- top A:查看详细信息 A/a切换窗口
- top -p port :查看指定端口信息

### 2. ls
- ls -l file:查看文件最后修改时间

### 3. wc
- wc -l file:查看文件多少行
- wc -w filename 看文件里有多少个word
-  wc -L filename 文件里最长的那一行是多少个字。

### 4. grep
- grep word 筛选关键字

### 5. ps
- -A    列出所有的程序
- -w	显示加宽可以显示较多的资讯
- -au	显示较详细的资讯
- -aux	显示所有包含其他使用者的进程
- -e	显示任何进程（此参数的效果和指定"A"参数相同）
- -f	全格式(显示UID,PPIP,C与STIME栏位)
- -C	指定执行指令的名称，并列出该指令的程序的状况
- ps -aux --sort -pmem, -pcpu 查看cpu内存情况，降序排序
- ps -aux --sort -pmem, -pcpu | head 20 显示前20条

### 6. 后台运行
- command  >  out.file  2>&1  & 所有的标准输出和错误输出都将被重定向到一个叫做out.file 的文件中。
- nohup command & 后台运行，默认产生输出文件nohup.out
- nohup command > myout.file 2>&1 & 输出文件重定向

# hadoop
### demo
[demo1](!https://blog.csdn.net/bentley2010/article/details/79339733)
[demo2](!https://www.jianshu.com/p/70bd81b2956f)
[free](!https://www.douban.com/event/30593394/)
[free2](!https://www.douban.com/event/30919399/)

### mrjob 安装指南：
1. pip install -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com mrjob 如果第二种方式安装报错，就使用这种方式安装
2. pip install -i mrjob http://pypi.douban.com/simple/ 这种是通过国内镜像安装

### centos7 yum安装pip
https://blog.csdn.net/yulei_qq/article/details/52984334
### pip 国内镜像
https://www.cnblogs.com/sunnydou/p/5801760.html