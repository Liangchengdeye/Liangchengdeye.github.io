---
layout: post
title: ���������ĵ��ʼǣ�python��Linux��hadoop 
subtitle: ���������ĵ��ʼǣ�python��Linux��hadoop 
categories: Code
tags:
    - ����ʼ�
    - LINUX 
    - Linux
    - hadoop
---
# ���������ĵ��ʼǣ�python��Linux��hadoop 

#  python���ģ��
###  subprocessģ��
 python����ʵ��linux����
###  python һ����װ����
https://blog.csdn.net/loyachen/article/details/52028825
###  mrjob ģ��
ʵ��python����hadoop��MapReduce
https://github.com/Yelp/mrjob
###  hdfsģ��
python ��hdfs���в���
###  requests-html ԭrequests�����¿�������ģ��
github��https://github.com/kennethreitz/requests-html
�����ĵ���https://cncert.github.io/requests-html-doc-cn/#/
###  myqrģ��
ʵ��python���ɸ�����֤��
https://blog.csdn.net/bruce_6/article/details/83006956
### ���⻷�������pip freeze > requirements.txt
��װ��pip install -r requirements.txt
### �������ǰ��Gerapy�ֲ�ʽ���������
https://blog.csdn.net/weixin_42336579/article/details/81103681
### python ����ģ��coverage�����븲���ʲ���
https://blog.csdn.net/qq_19467623/article/details/78675823
# linux 
### ͳ�Ʊ���ѯ�ؼ��ֳ��ֵ�������
:%s/string/&/gn
### linux��ת��
:10
### ͳ��������
netstat -nat|grep -i "80"|wc -l
### redis ͳ��������
info clients
### redis �鿴����IP�б�
client list

### 1. top 
- top A:�鿴��ϸ��Ϣ A/a�л�����
- top -p port :�鿴ָ���˿���Ϣ

### 2. ls
- ls -l file:�鿴�ļ�����޸�ʱ��

### 3. wc
- wc -l file:�鿴�ļ�������
- wc -w filename ���ļ����ж��ٸ�word
-  wc -L filename �ļ��������һ���Ƕ��ٸ��֡�

### 4. grep
- grep word ɸѡ�ؼ���

### 5. ps
- -A    �г����еĳ���
- -w	��ʾ�ӿ������ʾ�϶����Ѷ
- -au	��ʾ����ϸ����Ѷ
- -aux	��ʾ���а�������ʹ���ߵĽ���
- -e	��ʾ�κν��̣��˲�����Ч����ָ��"A"������ͬ��
- -f	ȫ��ʽ(��ʾUID,PPIP,C��STIME��λ)
- -C	ָ��ִ��ָ������ƣ����г���ָ��ĳ����״��
- ps -aux --sort -pmem, -pcpu �鿴cpu�ڴ��������������
- ps -aux --sort -pmem, -pcpu | head 20 ��ʾǰ20��

### 6. ��̨����
- command  >  out.file  2>&1  & ���еı�׼����ʹ�������������ض���һ������out.file ���ļ��С�
- nohup command & ��̨���У�Ĭ�ϲ�������ļ�nohup.out
- nohup command > myout.file 2>&1 & ����ļ��ض���

# hadoop
### demo
[demo1](!https://blog.csdn.net/bentley2010/article/details/79339733)
[demo2](!https://www.jianshu.com/p/70bd81b2956f)
[free](!https://www.douban.com/event/30593394/)
[free2](!https://www.douban.com/event/30919399/)

### mrjob ��װָ�ϣ�
1. pip install -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com mrjob ����ڶ��ַ�ʽ��װ������ʹ�����ַ�ʽ��װ
2. pip install -i mrjob http://pypi.douban.com/simple/ ������ͨ�����ھ���װ

### centos7 yum��װpip
https://blog.csdn.net/yulei_qq/article/details/52984334
### pip ���ھ���
https://www.cnblogs.com/sunnydou/p/5801760.html