# BIMS中AAA模块的部署

## 1. 基础镜像的制作
> BIMS中的AAA模块需要的tomcat版本为7.0.94，JDK为1.7.0_80，这里已经制作好了一个基础环境，步骤如下

1. 基于mycentos6.9:v1做为最基础的镜像，启动新的容器
2. 将对应的tomcat版本以及JDK版本解压缩到容器的/usr/local目录下
   - 容器中tomcat的目录为/usr/local/apache-tomcat-7.0.94
   - 容器中jdk目录为/usr/local/java/jdk1.7.0_80
   - 容器中tomcat已经指定了这个jdk版本
3. 为了部署aaa更方便，已经建立了aaa的标准目录
   - 容器中aaa的应用目录为/usr/bestv/apps/aaa
   - 容器中aaa的配置文件目录为/usr/bestv/configs/aaa/conf
   - 容器中aaa的日志目录为/usr/bestv/logs/aaa
   - 容器中aaa的发布文件aaa.xml已经建立好，路径为/usr/local/apache-tomcat-7.0.94/conf/Catalina/localhost，aaa.xml内容为
        ```
        <Context path="/aaa" docBase="/usr/bestv/apps/aaa" workDir="/usr/bestv/apps/aaa/work" ></Context>
        ```
   - tomcat中/usr/local/apache-tomcat-7.0.94/bin/catalina.sh已经指定了配置文件的路径
        ```
        export JAVA_HOME=/usr/local/java/jdk1.7.0_80
        export JRE_HOME=/usr/local/java/jdk1.7.0_80/jre
        JAVA_OPTS="-Dconfig_dir=/usr/bestv/configs/aaa/conf"
        ```
   - 以上几个aaa的目录，都是与现网bims的aaa模块中的部署目录是保持一致的
4. 至此aaa的基础镜像已经制作好，我们push到docker hub中

## 2. 如何使用
1. 使用以下命令pull aaa的基础镜像

```
docker pull 111904/mytomcat:v3
```

2. 在宿主机建立aaa需要的应用目录、配置文件目录、日志目录，比如以上海移动bims aaa为例，我们在本地建立一个目录叫做shyd_bims_aaa，在此目录下也同样建立三个目录apps、conf、logs

   - 宿主机中aaa的应用目录为shyd_bims_aaa/apps/
   - 宿主机中aaa的配置文件目录为shyd_bims_aaa/conf
   - 宿主机中aaa的日志目录为shyd_bims_aaa/logs

![](https://gitee.com/jinming_hu/myblogs/raw/master/pic/20210129151033.png)

   - 将aaa的压缩包解压到宿主机的shyd_bims_aaa/apps/目录下，如下所示

```
[root@iZuf6c271nqm25qoqom0t9Z shyd_bims_aaa]# cd apps/
[root@iZuf6c271nqm25qoqom0t9Z apps]# ll
total 52
-rw-r--r-- 1 root root 3281 Dec  2 16:24 auth.jsp
-rw-r--r-- 1 root root 3360 Dec  2 16:24 cardOrder.jsp
-rw-r--r-- 1 root root  907 Dec  2 16:24 codeConvert.jsp
-rw-r--r-- 1 root root  803 Dec  2 16:24 localEPGAuth.jsp
drwxr-xr-x 2 root root 4096 Dec  2 16:24 META-INF
-rw-r--r-- 1 root root 3529 Dec  2 16:24 order.jsp
-rw-r--r-- 1 root root 1201 Dec  2 16:24 orderQuery.jsp
-rw-r--r-- 1 root root  701 Dec  2 16:24 queryProduct.jsp
-rw-r--r-- 1 root root  568 Dec  2 16:24 test.jsp
-rw-r--r-- 1 root root 2199 Dec  2 16:24 userToken.jsp
-rw-r--r-- 1 root root 1279 Dec  2 16:24 version.jsp
drwxr-xr-x 5 root root 4096 Dec  2 16:24 WEB-INF
drwxr-xr-x 3 root root 4096 Jan 29 14:29 work
[root@iZuf6c271nqm25qoqom0t9Z apps]# 
```

   - 将aaa的配置文件放到宿主机的shyd_bims_aaa/conf目录下，如下所示

```
[root@iZuf6c271nqm25qoqom0t9Z shyd_bims_aaa]# cd conf/
[root@iZuf6c271nqm25qoqom0t9Z conf]# ll
total 32
-rw-r--r-- 1 root root  987 Apr 17  2020 cache.properties
-rw-r--r-- 1 root root 4172 Dec  2 17:00 config.properties
-rw-r--r-- 1 root root 5023 Apr 17  2020 iptv_messages.properties
-rw-r--r-- 1 root root 1431 Apr 17  2020 jdbc.properties
-rw-r--r-- 1 root root 5791 Jan 26 17:27 log4j.properties
[root@iZuf6c271nqm25qoqom0t9Z conf]# 
```

   - 这里有一点需要注意的是，就是配置文件中的log4j.properties中的日志路径目前为/usr/bestv/logs/aaa，这个是容器中的路径，这里其实只要配置为容器中一个存在的路径即可

3. 启动容器，这里需要映射代码和配置文件的目录，进入shyd_bims_aaa的目录
   - -e TZ="Asia/Shanghai"，代表设置tomcat的时区为上海
   - -v映射目录，这里的$(pwd)就是当前宿主机的目录，这里分别映射代码、配置文件、日志的三个目录
```
[root@iZuf6c271nqm25qoqom0t9Z conf]# docker run -itd -e TZ="Asia/Shanghai" -p 8080:8080 -v $(pwd)/apps:/usr/bestv/apps/aaa -v $(pwd)/conf:/usr/bestv/configs/aaa/conf/ -v $(pwd)/logs/:/usr/bestv/logs/aaa mytomcat:v3
```

4. 容器启动后，应该能看到如下所示的信息

```
[root@iZuf6c271nqm25qoqom0t9Z conf]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
f35e62010c7c        mytomcat:v3         "/bin/bash"         43 minutes ago      Up 43 minutes       0.0.0.0:8080->8080/tcp   lucid_shirley
[root@iZuf6c271nqm25qoqom0t9Z conf]# 
```

5. 启动tomcat，执行如下的命令，即可看到tomcat已经启动了

```
[root@iZuf6c271nqm25qoqom0t9Z conf]# docker exec -it f3 /bin/bash
[root@f35e62010c7c /]# ./usr/local/apache-tomcat-7.0.94/bin/startup.sh 
Using CATALINA_BASE:   /usr/local/apache-tomcat-7.0.94
Using CATALINA_HOME:   /usr/local/apache-tomcat-7.0.94
Using CATALINA_TMPDIR: /usr/local/apache-tomcat-7.0.94/temp
Using JRE_HOME:        /usr/local/java/jdk1.7.0_80/jre
Using CLASSPATH:       /usr/local/apache-tomcat-7.0.94/bin/bootstrap.jar:/usr/local/apache-tomcat-7.0.94/bin/tomcat-juli.jar
Tomcat started.
[root@f35e62010c7c /]# 
```

6. 访问aaa的版本信息的地址，即可看到已经部署成功了。
![](https://gitee.com/jinming_hu/myblogs/raw/master/pic/20210129153428.png)

7. 在宿主机的logs目录下即可看到请求aaa的日志了

```
[root@iZuf6c271nqm25qoqom0t9Z shyd_bims_aaa]# cd logs/
[root@iZuf6c271nqm25qoqom0t9Z logs]# ll
total 16
-rw-r--r-- 1 root root 1271 Jan 29 14:34 aaa.log
drwxr-xr-x 2 root root 4096 Jan 29 14:45 apps
-rw-r--r-- 1 root root    0 Jan 29 14:29 auth_difference.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 auth.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 cache.log
drwxr-xr-x 2 root root 4096 Jan 29 14:45 conf
-rw-r--r-- 1 root root    0 Jan 29 14:29 login.log
drwxr-xr-x 2 root root 4096 Jan 29 14:45 logs
-rw-r--r-- 1 root root    0 Jan 29 14:29 orderLog.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 ossInvoke.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 ossOrder.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 ppvLog.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 proxy.log
-rw-r--r-- 1 root root    0 Jan 29 14:29 unorderLog.log
[root@iZuf6c271nqm25qoqom0t9Z logs]# 
```

## 3. 缺点
1. 这里必须先使用docker run来启动容器，但此时并没有启动容器中的tomcat，这里就多了一步操作，所以如果更新代码时，需要先使用docker stop关闭容器，再更新代码，然后再使用docker start启动容器，再启动里面的tomcat，这里不是很方便，需要再研究下
2. 查看tomcat的启动日志不方便，这里需要研究下

## 4. 优点
1. 通过这种方式，可以在一台机器上部署很多个aaa，比如我再创建一个上海联通的aaa，只要建立一个shlt_bims_aaa的目录，将代码、配置文件放进去即可

```
[root@iZuf6c271nqm25qoqom0t9Z docker]# mkdir shlt_bims_aaa
[root@iZuf6c271nqm25qoqom0t9Z docker]# ll
drwxr-xr-x 2 root root      4096 Jan 29 15:42 shlt_bims_aaa
drwxr-xr-x 5 root root      4096 Jan 29 12:01 shyd_bims_aaa
[root@iZuf6c271nqm25qoqom0t9Z docker]# 
```

2. 我们目前大部分的bims的服务aaa、bims、pboss，都是放在10.215.29.151上的，我们需要维护不同项目的文件夹，也需要去维护里面的配置文件，还有每启动一个tomcat，都需要去维护tomcat的三个端口，以防端口占用，如果使用容器的方式，对宿主机只需要一个端口即可

```
[root@iZuf656fw7axs5msw6k1i7Z bestv]# ll
total 72
drwxr-xr-x  3 root root 4096 Apr 20  2020 aaa
drwxr-xr-x  5 root root 4096 Jul 17  2020 bims
drwxr-xr-x  7 root root 4096 Jul 16  2020 fjdx
drwxr-xr-x  7 root root 4096 Jul 16  2020 fjlt
drwxr-xr-x  7 root root 4096 Nov 19 17:29 gsyd
drwxr-xr-x  8 root root 4096 Sep 10 10:00 gsyd_remove_oracle
drwxr-xr-x  6 root root 4096 Dec 24 09:46 hndx
-rw-r--r--  1 root root 4160 Dec 25 13:34 port.txt
-rw-r--r--  1 root root 2002 Jul 10  2020 port.txt.bak.20200710
drwxr-xr-x  5 root root 4096 Jan  5 14:02 sh_content_service_data_collector
drwxr-xr-x  8 root root 4096 Jan 11 14:41 shdx
drwxr-xr-x 10 root root 4096 Nov 19 09:40 shlt
drwxr-xr-x  9 root root 4096 Sep  2 16:15 shyd
drwxr-xr-x  7 root root 4096 Apr 17  2020 sxdx
drwxr-xr-x  9 root root 4096 Nov 13 14:11 sxyd
drwxr-xr-x  7 root root 4096 Apr 24  2020 tools
drwxr-xr-x  8 root root 4096 Jul  8  2020 xjdx
```

这里维护端口信息，需要同时维护很多个

```
[root@iZuf656fw7axs5msw6k1i7Z bestv]# cat port.txt
这里记录不同项目的已占用的端口情况，以免新加入的tomcat启动报错，每加入进来一个tomcat都要在这里将端口情况记录下

查看端口是否被占用：lsof -i:端口
部署多个tomcat主要修改三个端口：
HTTP端口：http
远程停服务端口：shutdown
AJP端口：ajp

上海电信项目shdx，tomcat路径在/usr/bestv/shdx/tools
————————————————————————————
aaa：
start:5001
shutdown:5002
ajp:5003
————————————————————————————
bims:
start:5004
shutdown:5005
ajp:5006
————————————————————————————
pboss:
start:5007
shutdown:5008
ajp:5009

上海移动项目shyd，tomcat路径在/usr/bestv/shyd/tools
————————————————————————————
aaa：
start:9001
shutdown:9002
ajp:9003
————————————————————————————
bims:
start:9004
shutdown:9005
ajp:9006
————————————————————————————
pboss:
start:9007
shutdown:9008
ajp:9009
```