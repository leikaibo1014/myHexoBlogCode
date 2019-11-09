---
title: Linux常用命令
date: 2018-05-01 10:20:32
tags: [Linux]
categories: Linux与Shell编程
toc: true
---

# Linux常用命令

<!-- more -->

## 文件处理命令

    mkdir 创建目录  
    mkdir -p  可以递归创建  
    pwd 显示当前目录  
    cd ..  回到上一级目录  
    rmdir  删除空目录  
    cp -rp  源文件或者是目录  目标目录----复制文件  
    touch 创建空文件  
    cat  查看文件  
    more  分页显示文件内容 -- 按空格或者是f可以一页一页往下翻，按回车一行一行往下翻，按q退出 ，按b返回上一屏  
     less  分页显示内容 , 可以上下翻页。而且输入/  可以进行搜索，搜索完可以进行标记  
    ln -s 源文件  目标文件  为文件创建连接，连接类型分为软连接和硬链接

## 文件搜索命令

find   搜索范围 匹配条件否    
例如  find /etc -name init 在etc文件夹下搜索init名称的文件


## 文件压缩和解压缩命令

gzip 文件 就可以对该文件进行压缩    
gzip只能压缩文件不能压缩文件夹，而且是不保留源文件的  

打包文件夹：使用  tar -cvf 【压缩后文件名】【目录文件名】  
.tar.gz 一般是先打包在压缩后的文件名  

我们可以直接使用命令 【】tar -zcf 123.tar.gz 123 直接对文件夹123进行打包并进行压缩  

解压缩：-x 解包 -v显示详细信息 -f 指定解压文件  -z解压缩
例如; **tar -zxvf 123.tar.gz**

**命令详解**

**tar**    
-c: 建立压缩档案  
-x：解压  
-t：查看内容  
-r：向压缩归档文件末尾追加文件  
-u：更新原压缩包中的文件   
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

-z：有gzip属性的  
-j：有bz2属性的  
-Z：有compress属性的  
-v：显示所有过程  
-O：将文件解开到标准输出  
下面的参数-f是必须的  
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。  
	`tar -cf all.tar *.jpg  `

这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。

	tar -rf all.tar *.gif
这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。

	tar -uf all.tar logo.gif
这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。

	tar -tf all.tar
这条命令是列出all.tar包中所有文件，-t是列出文件的意思

	tar -xf all.tar
这条命令是解出all.tar包中所有文件，-t是解开的意思  

### 压缩
tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg   

tar -czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz  

 tar -cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2  

tar -cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z  

rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux  

### 解压 
tar -xvf file.tar //解压 tar包  
tar -xzvf file.tar.gz //解压tar.gz  
tar -xjvf file.tar.bz2   //解压 tar.bz2  
tar -xZvf file.tar.Z   //解压tar.Z  
unrar e file.rar //解压rar  
unzip file.zip //解压zip  

**总结**  
1、*.tar 用 tar -xvf 解压  
2、*.gz 用 gzip -d或者gunzip 解压   
3、*.tar.gz和*.tgz 用 tar -xzf 解压  
4、*.bz2 用 bzip2 -d或者用bunzip2 解压  
5、*.tar.bz2用tar -xjf 解压  
6、*.Z 用 uncompress 解压  
7、*.tar.Z 用tar -xZf 解压  
8、*.rar 用 unrar e解压  
9、*.zip 用 unzip 解压  




