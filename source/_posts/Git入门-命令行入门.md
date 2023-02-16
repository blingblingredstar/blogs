---
title: '[Git入门]命令行入门'
date: 2019-08-25 10:25:30
categories: 学习笔记
tags: [饥人谷, 命令行]
---
## 文件的查看操作

| 英文      | 缩写命令 | 英文      | 缩写命令 |
| --------- | -------- | --------- | -------- |
| file      |          | link      | ln       |
| make      | mk       | find      | find     |
| move      | mv       | echo      | echo     |
| remove    | rm       | touch     | touch    |
| copy      | cp       | change    | cd - c   |
| list      | ls       | directory | cd - d   |
| recursive |          | force     |          |

### 增删改查

+ 程序员的宿命
  + 首先学习文件增删改查
  + 学习DOM增删改查
  + 学习HTTP增删改查
<!-- more -->
### 查：查看文件或目录

+ 查看文件绝对路径
  + `pwd`
+ 查看当前目录内容
  + `ls`
+ 查看指定目录内容
  + `ls $PATH`
+ 查看文件内容
  + `cat $FILE_PATH` 文件内容全部输出到bash
  + `head $FILE_PATH` 文件内容部分输出到bash（默认前十行）可以加 `-n NUMBER`指定行数，`tail`也一样
  + `tail $FILE_PATH` 文件内容部分输出到bash（默认后十行）
  + `less $FILE_PATH` 进入类似vim的编辑模式，从头查看文件内容

## 文件的增加操作

### 增：创建文件

+ 创建1.txt
  + `touch 1.txt`
  + `echo hi > 1.txt` 用`hi`覆盖文件内容
  + `echo hi again >> 1.txt` 在文件后追加`hi again`
  + `echo -e "1\n2" > 1.txt` 用`1`换行`2`替换文件内容
+ 创建目录
  + `mkdir $DIR_NAME`
  + `mkdir -p $DIR_NAME` 可以创建套嵌目录文件夹
+ 同时创建多个文件
  + `touch 1.txt 2.txt`
+ 同时创建多个目录
  + `mkdir -p ./a/b/c ./a/d/c`
+ 复制文件
  + `cp 1.txt 2.txt`
+ 复制目录
  + `cp -r ./a ./b`

## 文件的其他操作

### 删：删除文件

+ 删除1.txt
  + `rm 1.txt`
+ 删除目录./a
  + `rm -r ./a`
+ 删除有内容的目录./a
  + 同上，或加`-f`强制删除

### 改：修改文件或mul

+ 修改文件内容
  + `code $FILE_PATH` code可替换为vim，open
+ 追加文件内容
  + `echo '111' >> 1.txt`
+ 清空文件
  + `echo '' > 1.txt` 使用空字符串替换文件内容
+ 移动文件/目录
  + `mv 1.txt ./a` 使用mv命令可以移动文件
+ 重命名文件/目录
  + `mv 1.txt 2.txt` 使用mv命令可以移动文件，后接文件名可以重命名文件或者替换文件
+ 修改文件最后更新时间
  + 使用`ls -l`查看更新时间，使用`touch $FILE_NAME`来更新文件更新时间

### 查看手册

+ 使用`man $OPREATION`来查看操作命令的手册（man: menu）
+ 使用`$OPREATION --help`来查看帮助
+ `yarn global add tldr` 全局安装tldr(too long didn't read) 使用`tldr $OPREATION` 来获取帮助信息

## 脚本文件

### 命令组合

+ 命令执行状态
  + 命令执行完毕后会返回一个返回值，0代表成功，1或者其他数字代表失败，在命令结束后使用`echo $?`来获取返回值
+ `&&` 命令
  + 一条命令执行完毕后接另一条命令
+ `;` 操作
  + 不论上个命令是否成功，都执行下一个命令
+ 把命令编辑为文件
  + 创建一个文件，后缀名任意
  + 添加执行权限
    + `chmod +x $FILE_PATH`
  + 将命令添加到文件中
  + `sh $FILE_PATH`执行
  + 在文件头添加shebang，直接使用文件路径执行
  + 将文件添加到PATH，直接使用文件名执行
    + [mac添加PATH方法](https://xiedaimala.com/tasks/bdeeede8-0b3c-4915-9d9a-4fe039dead37/text_tutorials/9d173da0-b87a-463f-bd43-7837ebedbd3f)
