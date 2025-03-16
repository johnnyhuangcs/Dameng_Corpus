

# GO 语言

本章节主要介绍 GO 语言应用开发常见问题，为用户提供 GO 语言应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [go 语言连接达梦报错：unrecognized relocation (0x2a) in section \`.text](https://eco.dameng.com/document/dm/zh-cn/faq/faq-go.html#go%20%E8%AF%AD%E8%A8%80%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%8A%A5%E9%94%99%EF%BC%9Aunrecognized%20relocation%20\(0x2a\)%20in%20section%20%60.text)



## 正文

## go 语言连接达梦报错：unrecognized relocation (0x2a) in section \`.text

【问题描述】

centos 环境下 go 语言连接达梦报错：" unrecognized relocation (0x2a) in section \`.text"

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230331140844IO1K1TJ87CSFWRTYU0)

【问题分析】

在 centos7 中操作系统的 GNU 的 Binutils 版本一般是 2.23 版本，该版本较低无法满足需求，需要将 GNU 的 Binutils 版本升级到 2.26 版本及以上版本可以解决该问题。可使用以下命令来查看当前 GNU 的 Binutils 版本：

```
[root@localhost go_code]# ld -v
GNU ld version 2.23.52.0.1-30.el7 20130226
```

【问题解决】

将 GNU 的 Binutils 版本升级到 2.26 版本及以上版本，具体步骤如下：

  1. 下载 binutils 包；



参考地址：https://ftp.gnu.org/gnu/binutils/

  2. 解压 binutils 包；



```
[root@localhost go_code]# tar -zxvf binutils-2.26.tar.gz
```

  3. 编译运行；



```
[root@localhost go_code]# cd binutils-2.26
[root@localhost binutils-2.26]# ./configure --prefix=/usr/local/binutils
[root@localhost binutils-2.26]# make
[root@localhost binutils-2.26]# make install
```

  4. 配置环境变量；



```
[root@localhost ~]# vi ~/.bash_profile
## 添加binutils的目录
export PATH=/usr/local/binutils/bin:$PATH
[root@localhost ~]# source ~/.bash_profile
```

  5. 查看 Binutils 版本是否升级成功。



```
[root@s11g binutils-2.26]# ld -v
GNU ld (GNU Binutils) 2.26.20160125
```

升级成功。
