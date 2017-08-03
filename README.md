# jenkins
jenkins迁移
# jenkins ver 2.71 for linux
***
一、安装前环境部署
查看系统信息
```
#uname -a
Linux localhost 2.6.32-642.el6.x86_64 #1 SMP Tue May 10 17:27:01 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
# cat /etc/issue
CentOS release 6.8 (Final)
Kernel \r on an \m
```
需要安装的依赖包jdk1.8、apache-ant-1.10.1、svn1.8、maven3.3.9、git2.9.2

1、jdk1.8安装
```
#cd /usr/local/src/
#wget http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz?AuthParam=1501510958_03d6d6330996ea7e90de6e98f2bec4b0
#mv jdk-8u144-linux-x64.tar.gz\?AuthParam\=1501510958_03d6d6330996ea7e90de6e98f2bec4b0 jdk-8u144-linux-x64.tar.gz
#tar zxvf jdk-8u144-linux-x64.tar.gz -C /var/lib/java
```
添加环境变量
```
#vim /etc/profile
JAVA_HOME=/var/lib/java/jdk1.8.0_144
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar
export PATH JAVA_HOME CLASSPATH
#source /etc/profile
```
查看java版本
```
#java -version
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)
```
2、安装 Ant
从http://ant.apache.org 上下载tar.gz版ant,本次是从原来的服务器上面拷贝，apache-ant-1.10.1，
```
# pwd
/var/lib/apache-ant-1.10.1
```
3、svn客户端安装
由于项目中使用到svn，需要在机器上安装svn客户端。注意：svn版本必须1.7或1.7以上，由于yum安装是1.6，所以需要手动指定svn 的yum源，如果之前有安装请先卸载
```
添加svn1.8yum源
#yum remove subversion
#yum clean
#vim /etc/yum.repos.d/wandisco-svn.repo
[WandiscoSVN]
name=Wandisco SVN Repo
baseurl=http://opensource.wandisco.com/centos/$releasever/svn-1.8/RPMS/$basearch/
enabled=1
gpgcheck=0
```
```
#yum install subversion
#svn --version
svn，版本 1.8.18 (r1800620)
   编译于 Jul  5 2017，14:01:30 在 x86_64-unknown-linux-gnu

Copyright (C) 2017 The Apache Software Foundation.
This software consists of contributions made by many people;
see the NOTICE file for more information.
Subversion is open source software, see http://subversion.apache.org/
```
4、maven安装

版本maven3.3.9 注意：不要安装maven3.5版本，不然后期打包会有报错

```
 #cd /usr/local/src/
 #wget https://archive.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
 #tar zxvf apache-maven-3.3.9-bin.tar.gz -C /usr/local/
 #cd /usr/local/
 # mv apache-maven-3.3.9 maven3
```
添加环境变量
```
#vim /etc/profile

export MAVEN_HOME=/usr/local/maven3
export PATH=${MAVEN_HOME}/bin:${PATH}
#source /etc/profile
```
查看maven版本
```
#mvn -version
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: /usr/local/maven3
Java version: 1.8.0_144, vendor: Oracle Corporation
Java home: /var/lib/java/jdk1.8.0_144/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-642.el6.x86_64", arch: "amd64", family: "unix"
```
5、git客户端安装
```
#yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
#yum install  gcc perl-ExtUtils-MakeMaker autoconf 
#cd /usr/local/src/
#wget https://github.com/git/git/archive/v2.9.2.tar.gz
#tar zxvf v2.9.2.tar.gz
# cd git-2.9.2/
# make configure
#./configure --prefix=/usr/local/git --with-iconv=/usr/local/libiconv
#make && make insatll
#ln -s /usr/local/git/bin/git /usr/bin/git
#git --version
git version 2.9.2
```
二、jenkins 2.7.1 安装

jenkins安装方式有三种方式具体不在这详解，本次使用的是在官网下载war包放在tomcat;
```
#cd /usr/local/src
下载tomcat、jenkins包
# wget http://apache.fayea.com/tomcat/tomcat-8/v8.5.16/bin/apache-tomcat-8.5.16.tar.gz
# wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war
```
创建jenkins用户
```
#useradd jenkins
#passwd jenkins
```
安装Tomcat8.5
```
#tar -zxvf apache-tomcat-8.5.16.tar.gz -C /usr/local/
#cd /usr/local/
# mv apache-tomcat-8.5.16 tomcat8
```
将jenkins.war放tomcat webapps目录下
```
#cp /usr/local/src/jenkins.war /usr/local/tomcat8/webapps/
```
在/etc/profile/文件中指定Jenkins home目录
```
#vim /etc/profile
export JENKINS_HOME=/var/lib/jenkins
```
修改tomcat属主属组
```
#chown -R jenkins:jenkins /usr/local/tomcat8
```
切换用户启动Tomcat
```
#su jenkins
$/usr/local/tomcat8/bin/startup.sh
```
浏览器访问http：//ip:8000/jenkins
从Jenkins初始化启动日志上可以看到初始化密码存放在
/var/lib/jenkins/secrets/initialAdminPassword

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-dd31b93ce32915ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-38f9bc27e8665ce6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-933711474eb3e4ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-ac80fe5129c6e0c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-f0cd74cfbede5bbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-6a9745032daa0836.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
关闭Tomcat
```
$ ps -ef | grep jenkins
jenkins   6540     1  1 11:49 ?        00:06:24 /var/lib/java/jdk1.8.0_144/bin/java -Djava.util.logging.config.file=/usr/local/tomcat8/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -classpath /usr/local/tomcat8/bin/bootstrap.jar:/usr/local/tomcat8/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat8 -Dcatalina.home=/usr/local/tomcat8 -Djava.io.tmpdir=/usr/local/tomcat8/temp org.apache.catalina.startup.Bootstrap start
root      7751  7730  0 21:40 pts/0    00:00:00 grep jenkins
$kill -9 6540
```
将原Jenkins home目录拷贝到现Jenkins的home目录/var/lib/jenkins,
将现/var/lib/jenkins 下的文件移走或删除
原来的jenkins是有yum安装默认jenkins也在/var/lib 下，如有调整可以查看/etc/sysconfig/jenkins文件
```
打包原jenkins的home目录并scp到新jenkins上
#tar zcvf jenkins.tar.gz jenkins
#scp -P22 jenkins.tar.gz root@ip（新机器）:/var/lib
```  
新机器解压
```
#tar zxvf jenkins.tar.gz
切换用户启动tomcat
#su jenkins
$/usr/local/tomcat8/bin/startup.sh
```
打开浏览器测试http://ip:8080/jenkis
输入账号密码，直接为原jenkins集成crowd的账号登陆

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-16098d81f6408ca5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击系统设置----系统设置，里面的选项的home路径改成和安装指定的路径一致，应用保存即可

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-91703b59c49c41f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
更改git路径
系统设置----Global Tool Configuration  删除原来的，添加现环境的路径

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-ba729d089a60d263.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


 打包遇到的问题总结：
1、NoSuchFieldError:DEFAULT_GLOBAL_SETTINGS_FILE 问题
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-0ada05062e3050ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解决办法：因为maven3.5与jenkins会产生bug，所以将maven3.5更换为3.3.9
2、打包提示error=13,权限不够，
思路1：以为是权限问题，使用root启动tomcat测试 结果图2报错，实际上这个路径是存在的
思路2：图一有提示svn，可能是客户端未安装svn照成，测试后成立

![图1.png](http://upload-images.jianshu.io/upload_images/2032456-fe54c833cb911f65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图2.png](http://upload-images.jianshu.io/upload_images/2032456-80c00c5aebeaafc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3、启动jenkins报错

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-1a00a2dc22038d50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
照成原因：使用root启动tomcat
解决办法：更改/var/lib/jenkins 权限,使用jenkins重启tomcat解决
```
#chown -R jenkins:jenkins /var/lib/jenkins
```
4、The svn command failed

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-7442022e029e6f88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解决办法：卸载低于1.7版本的svn客户端，安装svn1.7或者更高
5、[ERROR]svn:E230001

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-c3d11ef52765c162.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
造成原因：svn 地址 是https造成主机不信任
解决办法：信任该主机，更新svn
```
#cd /var/lib/jenkins/workspace/srv_message_sender/msgsender
```
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2032456-83bbe7dc13318e1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
6、git安装报错/bin/sh: line 1: asciidoc: command not found
```
#wget http://sourceforge.net/projects/asciidoc/files/asciidoc/8.6.9/asciidoc-8.6.9.tar.gz
解压 ./confluen   make&&make install
```
附：
jenkins更改主目录三种方式：http://www.cnblogs.com/yangxia-test/p/4367999.html
