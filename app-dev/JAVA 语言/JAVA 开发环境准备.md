

# JAVA 开发环境准备

## 一、达梦数据库安装

请参考 [数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

## 二、安装 Java 开发工具包-JDK

  1. 双击安装包进行安装，安装步骤简单，点击【下一步】安装即可。以安装目录 \`D:\java\jdk1.8.0_111 \` 为例，安装完成如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221215091843A84LRPOZG028POA00E)

  2. 安装成功后需要配置 JDK 环境变量，即将 JDK 安装路径的 bin 路径复制到环境变量 path 里，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221215091902L4JZV26M679UT0N2L8)

  3. 环境变量配置成功后，可使用 win+R 打开命令提示符，输入 java -version 命令，可以正常出现 JDK 版本号，即环境变量配置成功，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212150919190SQMDLVT40XAHCKWSV)

> **注意**
>
> JDK 安装完成后需配置环境变量，环境变量配置不正确会导致项目启动报错。



因环境变量配置错误导致的常见错误代码如下：

```
CopyUnable to find a javac compiler;
Perhaps JAVA_HOME does not point to the JDK;
```

## 三、安装集成开发环境

正确配置 Java 环境变量后，eclipse/idea 可正常打开。
Idea 下载链接：[https://www.jetbrains.com/idea/download/#section=windows](https://www.jetbrains.com/idea/download/#section=windows)
Eclipse 下载链接：[https://www.eclipse.org/downloads](https://www.eclipse.org/downloads/)
