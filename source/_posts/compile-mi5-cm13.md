---
title: 编译CM 13.0 （小米5 gemini）
url: 196.html
id: 196
categories:
  - Linux
date: 2016-11-22 14:06:21
tags:
---

根据cm官方文档所说来编译，首先要搭建环境

我使用的是Ubuntu 16首先需要安装编译环境

  
```
$ sudo apt-get install bison build-essential curl flex git gnupg gperf libesd0-dev liblz4-tool libncurses5-dev libsdl1.2-dev libwxgtk3.0-dev libxml2 libxml2-utils lzop maven openjdk-8-jdk pngcrush schedtool squashfs-tools xsltproc zip zlib1g-dev
```
根据官方提示Ubuntu 16以上要使用 libwxgtk3.0-dev 和 openjdk-8-jdk，当然，如果你的电脑安装了较新的Sun/Oracle Java就不用安装openjdk了

  

  

然后获取repo工具

  
```
$ cd ~
$ mkdir bin
$ curl //storage.lug.ustc.edu.cn/git-repo-downloads/repo > ~/bin/repo
```
  

这一步你需要翻墙才能获取repo工具

之后把 ~/bin/目录加入PATH中， 如果是比较新的Ubuntu，bin已经在PATH环境变量了，当然，如果你图省事，直接把repo丢到/usr/bin也是可以的

 然后对cm源码目录初始化，为下载代码做准备

  
```
$ cd ~/android/system/
$ repo init -u https://github.com/CyanogenMod/android.git -b cm-13.0
```
设置米5的依赖

  
```
$ mkdir .repo/local_manifests
$ vim  .repo/local\_manifests/local\_manifests.xml
```
把一下内容写入local_manifests.xml

  

  
```
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <project name="CyanogenMod/android\_device\_xiaomi_gemini" path="device/xiaomi/gemini" remote="github" />
  <project name="CyanogenMod/android\_device\_qcom_common" path="device/qcom/common" remote="github" />
  <project name="CyanogenMod/android\_kernel\_xiaomi_msm8996" path="kernel/xiaomi/msm8996" remote="github" />
  <project name="CyanogenMod/android\_vendor\_nxp-nfc\_opensource\_frameworks" path="vendor/nxp-nfc/opensource/frameworks" remote="github" />
  <project name="CyanogenMod/android\_vendor\_nxp-nfc\_opensource\_libnfc-nci" path="vendor/nxp-nfc/opensource/libnfc-nci" remote="github" />
  <project name="CyanogenMod/android\_vendor\_nxp-nfc\_opensource\_Nfc" path="vendor/nxp-nfc/opensource/Nfc" remote="github" />
</manifest>
```
这些依赖均使用了官方CM代码

  

  

然后开始下载源码吧，在~/android/sysytem/目录执行

  
```
$ repo sync
```
强烈建议运行repo sync时不要加参数，因为repo本来就有默认参数，默认 -j 4 -c，-j 4的目的是同时进行4个下载任务，-c参数是之下载cm-13.0分支，而不是下载整个CM包含历史版本的源码，下载建议翻墙

博主下载了一夜才下载完成，大概下载了20多GB内容

  

下载完成后进行手机代码适配

运行以下命令

  
```
$ source build/envsetup.sh
$ breakfast gemini
```
  

下一步提取手机文件

把手机插上电脑，电脑端配置好adb

运行下面这些进行提取文件，如果少了这一步，编译的时候会报错

  
```
$ cd device/xiaomi/gemini
$ ./extract-files.sh
```
等待完成即可

  

  

最后进入编译阶段

编译之前建议开启CCACHE，加快编译速度，CCACHE会保存编译过的内容，如果后面碰到重复编译，回直接从ccache中提取，减少编译时间

开启方法:

  
```
$ export USE_CCACHE=1
```
  

分配CCACHE空间

  
```
$ croot
$ prebuilts/misc/linux-x86/ccache/ccache -M 50G
```
  

  

开始编译：

  
```
$ brunch gemini
```
编译比较慢，而且电脑比较卡，编译的时候可以做点其他事情，博主的电脑16G内存，编译了2个小时左右

  

  

博主在编译的时候碰到一下问题：

  
```
\[ERROR\] Failed to execute goal org.apache.maven.plugins:maven-dependency-plugin:2.10:get (default-cli) on project standalone-pom: Couldn't download artifact: Could not transfer artifact org.cyanogenmod:gello:apk:40 from/to central (https://maven.cyanogenmod.org/artifactory/gello_prebuilds): sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
\[ERROR\] org.cyanogenmod:gello:apk:40
\[ERROR\] 
\[ERROR\] from the specified remote repositories:
\[ERROR\] central (https://repo.maven.apache.org/maven2, releases=true, snapshots=false),
\[ERROR\] central (https://maven.cyanogenmod.org/artifactory/gello_prebuilds, releases=true, snapshots=true)
\[ERROR\] -> \[Help 1\]
\[ERROR\] 
\[ERROR\] To see the full stack trace of the errors, re-run Maven with the -e switch.
\[ERROR\] Re-run Maven using the -X switch to enable full debug logging.
\[ERROR\] 
\[ERROR\] For more information about the errors and possible solutions, please read the following articles:
\[ERROR\] \[Help 1\] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
```
这是由于缺少SSL证书导致

首先用浏览器浏览[https://maven.cyanogenmod.org/artifactory/gello_prebuilds](https://maven.cyanogenmod.org/artifactory/gello_prebuilds)

然后使用浏览器把这个网站的证书导出到example.der文件，在这个文件的所在目录执行 

  
```
$ sudo keytool -import -alias example -keystore  $JAVA_HOME/jre/lib/security/cacerts -file example.der
```
提示输入密钥库口令时，输入changeit，这里一定要是changeit，否则无法导入，之后会提示导入成功

  

使用SSLPoke工具可进行查询是否成功（下载 [SSLPoke](https://confluence.atlassian.com/kb/files/779355358/779355357/1/1441897666313/SSLPoke.class)），运行

  
```
$ java SSLPoke maven.cyanogenmod.org 443
```
提示Successfully connected即为成功
