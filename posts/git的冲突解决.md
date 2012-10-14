---
title:
date: '2012-10-07'
description:
categories:
tags: [git]
---
<!--
$ git --version
git version 1.7.9.6 (Apple Git-31.1)-->

在多人同时开发的时候,遇到冲突是常有的事情.下面这段命令行就模拟了一个简单的冲突情况,把它复制到终端中运行一下

```
mkdir git_CONFLICT
cd git_CONFLICT
				
mkdir remote
cd remote
git init --bare 

cd ..
mkdir working1
cd working1
git init 
git remote add origin ../remote/

echo Hello World! > test
git add test
git commit -m 'Initial commit'
git push origin master

cd ..
mkdir working2
cd working2
git init
git remote add origin ../remote/
git pull origin master

echo v2 >> test
git commit -am 'v2'
git push origin master

cd ../working1
echo v3 >> test
git commit -am 'v3'
git pull origin master
```

执行最后一句之后,由于working1和working2都编辑了test文件,git无法自动合并,终端会输出以下的信息

```
Auto-merging test
CONFLICT (content): Merge conflict in test
Automatic merge failed; fix conflicts and then commit the result.
```

运行``cat test``命令查看文件内容,输出如下

```
Hello World!
<<<<<<< HEAD
v3
=======
v2
>>>>>>> 9b9d0ab7cb47b8a93db5b5e179393c2140062342
```

对于简单文本文件的冲突,把HEAD之类的冲突标记去掉然后commit就可以了,不过如果这个文件是二进制的,或者是由机器生成的标记语言(比如xcode的xib),要通过编辑来解决冲突的难度就很大,只能直接用自己或别人的版本来覆盖,或者撤销掉pull操作.下面就介绍处理这种情况的git命令.


运行下面的命令,可以查看test文件staged的object状态

```
git ls-files -s test
```

输出如下:

```
100644 980a0d5f19a64b4b30a87d4206aade58726b60e3 1	test
100644 9ac7858602b1beba6bc551996b884334d828076d 2	test
100644 2546106ec460f4788795776e08a2553849003aa2 3	test
```

可以看到冲突的文件会分成三个版本,可以用``git show``命令来查看不同版本的内容

```
git show :1:test  # 查看工作目录中的版本
git show :2:test  # 查看本地branch中的版本
git show :3:test  # 查看远程branch中的版本
```

当两个文件无法合并,而别人对该文件的改动比你少,可以使用``git checkout --ours 冲突文件的路径``选择本地的版本覆盖工作目录中的文件,push上去让别人pull下来再重做吧

```
git checkout --ours test
git add -u
git commit
git push origin master
```

如果觉得自己的改动少,还是自己重做算了,把命令换成
```
git checkout --theirs
```
就能用别人的版本来覆盖到工作目录

这两条命令在stage(暂存)之前都是有效的,stage之后就无效了

<!--git checkout --ours test
cat test
git checkout --theirs test
cat test

git add -u
git checkout --ours test
cat test
git checkout --theirs test
cat test

git reset HEAD test
git checkout --ours test
cat test
git checkout --theirs test
cat test
-->

```
$ git checkout --ours test
$ cat test
Hello World!
v3
$ git checkout --theirs test
$ cat test
Hello World!
v2
$ 
$ git add -u
$ git checkout --ours test
$ cat test
Hello World!
v2
$ git checkout --theirs test
$ cat test
Hello World!
v2
$ git ls-files -s test
100644 2546106ec460f4788795776e08a2553849003aa2 0	test

```

***

如果觉得手头上的麻烦事很多,没有时间去考虑解决冲突的问题,可以执行
``
git reset --hard HEAD
``
或者
```
git checkout -f
```
来撤销merge操作.

要是想重新生成冲突后的文件也只需要执行这两条命令之一.然后``git pull``就可以了.
##平时没事千万不要敲这两条命令,会撤销之前全部未提交的改动

