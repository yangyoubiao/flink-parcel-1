##声明
本parcel是fork下 https://github.com/gaozhangmin/flink-parcel 来修改，感谢作者的贡献

## 导读
CDH除了能够管理自生所提供的一些大数据相关服务外，还允许将第三方服务添加到CDH集群（托管在CDH上）。你需要做的就是按照一定的规则流程制作相关程序包，最后发布到CDH上。虽然过程并不困难，但是手动操作尤其是一些关键配置容易出错，往往导致最终服务无法正常在CDH上安装运行。

本文就是指导大家如何打包自己的服务，发布到CDH上，并且由CDH控制服务的运行、监控服务的基本运行状态。

## 相关介绍  
### 名词介绍
**(1)parcel**:   以".parcel"结尾的压缩文件。parcel包内共两个目录，其中lib包含了服务组件，meta包含一个重要的描述性文件parcel.json，这个文件记录了服务的信息，如版本、所属用户、适用的CDH平台版本等。

**命名规则必须如下**：

文件名称格式为三段，第一段是包名，第二段是版本号，第三段是运行平台。

例如：FLINK-1.9.0-bin-scala_2.12-el7.parcel

**包名**：FLINK

**版本号**：1.9.0-bin-scala_2.12

**运行环境**：el7

el6是代表centos6系统，centos7则用el7表示

==**ps**==:    
parcel必须包置于/opt/cloudera/parcel-repo/目录下才可以被CDH发布程序时识别到。

**(2)csd**：csd文件是一个jar包，它记录了服务在CDH上的管理规则里面包含三个文件目录，images、descriptor、scripts,分别对应。如服务在CDH页面上显示的图标、依赖的服务、暴露的端口、启动规则等。

==**ps**==:  
csd的jar包必须置于/opt/cloudera/csd/目录才可以在添加集群服务时被识别到。




## flink-parcel制作过程

以CDH6.1、FLINK1.9.0为例

(1)**下载制作包**

```
git clone https://github.com/pecanNBU/flink-parcel.git
```
(2)**修改配置文件**　flink-parcel.properties


```
#flink 下载地址
FLINK_URL=http://mirrors.tuna.tsinghua.edu.cn/apache/flink/flink-1.9.0/flink-1.9.0-bin-scala_2.12.tgz

#flink版本号
FLINK_VERSION=1.9.0

#扩展版本号
EXTENS_VERSION=BIN-SCALA_2.12

#操作系统版本，以centos为例
OS_VERSION=7

#CDH 小版本
CDH_MIN_FULL=5.2
CDH_MAX_FULL=6.2

#CDH大版本
CDH_MIN=5
CDH_MAX=6
```



(2)**生成parcel文件**  

```
./build.sh  parcel
```
 
(3)**生成csd文件** 

- on yarn 版本

```
./build.sh  csd_on_yarn
```


- standalone版本

```
./build.sh  csd_standalone
```

## CDH 中安装flink服务
此处假设你已经安装好CDH集群

(1) 将上面生成的parcel文件copy至 cloudera/parcel-repo子目录下  

(2) 将上述生成的jar文件copy至cloudera /parcel-repo子目录下  

(3) 在CDH中添加flink的parcel包：　　

打开CDH管理界面->集群->检查parcel包->flink->分配->激活

(4) 点击CDH所管理的集群添加服务，在列表中找到flink，按提示添加启动并运行。

## 说明：
(1) 在如果集群开启了安全，需要配置security.kerberos.login.keytab和security.kerberos.login.principal两个参数才能正正常启动。如未启动kerberos,则在CDH中添加FLINK服务时请清空这两个参数的内容

(2) If you plan to use Apache Flink together with Apache Hadoop (run Flink on YARN, connect to HDFS, connect to HBase, or use some Hadoop-based file system connector) then select the download that bundles the matching Hadoop version, download the optional pre-bundled Hadoop that matches your version and place it in the lib folder of Flink, or export your HADOOP_CLASSPATH（来自flink官网）


## 相关参考：　　

[Cloudera Manager Extensions](https://github.com/cloudera/cm_csds)

[csd参考模板](git@github.com:cloudera/cm_csds.git)

[FLINK官方下载地址](https://archive.apache.org/dist/flink/)

[CDH添加第三方服务的方法](https://blog.csdn.net/tony_328427685/article/details/86514385)

      
